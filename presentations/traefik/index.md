<section data-state="no-title-footer">
![traefik](images/traefik.png)
## Traefik: a modern Loadbalancer/Reverse Proxy 
### devops meetup franken
Bjoern Pohl, Senior Systems Engineer @Paessler
---

## What is Traefik ?

Traefik is a<br> 
Cloud/Microservice-enabled 
reverse proxy / loadbalancer

Note: Talk about loadbalancer and stuff
---

## Another one? 
We already have HAProxy, Apache, Nginx...

---

# Yes, but:
* As long as you need a static lb/rp, all of them are fine.
* If you need dynamic reconfiguration at runtime you will find yourself messing around with something like
  * consul-template for consul-driven microservice architectures
  * etcd/confd ...
  * your own tinkered bash/python/whatever scripts...
* some of the above solutions are not even able to activate substantial configuration changes at runtime.
Note: Apache and Haproxy do not allow dynamic configuration, at least not that easy.
---
## Features:
* Let's Encrypt built-in
* Single Go Binary! (But official Docker Image available, too)
* Backends built-in for:
  * Docker (both plain docker and swarm mode)
  * K8S
  * Mesos/Marathon
  * nearly all relevant K-V Stores (etcd, consul,..)
  * Amazon ECS, DynamoDB
  * REST Api
  * tiny web frontend
  * ...and of course still a file-based config
---
## Traefik to the rescue!
![architecture](images/architecture.png)
(image shamelessly stolen from traefik.io)

---
## Usecases
---

## Frontend for your VPS

* your dockerized Blog
  * automatic routing available, we'll see that later.
* some static configs for various Webinterfaces (mysqladmin, squirrelmail, whatever..)

---

## Ingress controller for K8S

* name-, path-based routing available
* further customisations using annotations
* traefik itself also lives in k8s.

---

## Fullstack microservice infrastructure

* traefik usually living on dedicated hosts
* clustering is possible
* configuration usually controlled by k-v store (consul, ...)




## Config
* Entrypoint
  * Ports to listen on
  * Certificates
  * Redirects
---
* Frontend
  * Routes incoming Request to backend
    * depending on url, path, headers, regexp, ....
  * modify requests (i.e. coolapp.somedomain.com -> somedomain.com/coolapp)
  * filters (http headers)
---
* Backend
  * Backend Servers (1-N, Loadbalancing..)
  * Backend Services (Docker, Kubernetes, ...) 

---
#### simple config with docker backend
```toml
 #/etc/traefik.toml
 #generic stuff
 logLevel = "DEBUG"
 #entrypoints
 [entryPoints]
   [entryPoints.http]
         address = ":80"
         [entryPoints.http.redirect]
                 entryPoint = "https"
   [entryPoints.https]
         address = ":443"
         [entryPoints.https.tls]
 defaultEntryPoints = ["http", "https"]
 #Lets Encrypt
 [acme]
 email = "john.doe@somedomain.com"
 storage = "/srv/traefik/acme.json"
 entryPoint = "https"
 OnHostRule = true
 [[acme.domains]]
   main = "www.somedomain.com"
 #Web UI
 [web]
 address = ":8080"
 ReadOnly = true
  [web.statistics]
    RecentErrors = 10
    [web.auth.basic]
      users = ["john:$apr1$ktosecure4u!1TP8lp71"]
 
 #docker backend
 [docker]
 endpoint = "unix:///var/run/docker.sock"
 domain = "somedomain.com"
 watch = true
 exposedbydefault = true #whoa! 
```
---

# Demo Time!

---

#Thanks!

## https://github.com/mcslow/reveal-traefik



