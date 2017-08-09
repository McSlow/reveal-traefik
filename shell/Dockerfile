FROM garland/butterfly:latest
MAINTAINER Bjoern Pohl <bjoern@datenwalze.de> 

# install additional tools
RUN perl -pi -e 's/httpredir/ftp.de/' /etc/apt/sources.list \
  && apt-get update \
  && apt-get install -y git vim-nox curl sudo python-pip \
  && apt-get clean \
  && pip install libsass

ADD john-shell /usr/local/bin/john-shell

# dont run as root
RUN groupadd -r john && useradd -m -g john john
USER john

# customize user environment here
RUN curl -sL https://raw.githubusercontent.com/benningm/vimrc/master/install.sh | bash

USER root
CMD [ "butterfly.server.py", "--unsecure", "--host=0.0.0.0", "--port=57575", "--shell=/usr/local/bin/john-shell" ]
ENTRYPOINT []

