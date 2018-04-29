FROM bitnami/minideb:jessie

MAINTAINER Maarten Smeets, m_mcfly@hotmail.com

# replace shell with bash so we can source files
RUN rm /bin/sh && ln -s /bin/bash /bin/sh

# nvm environment variables
ENV NVM_DIR /usr/local/nvm
ENV NODE_VERSION 8.8.1
ENV PATH $PATH:$NVM_DIR/versions/node/v$NODE_VERSION/bin

# Do basic updates
RUN apt-get update -q && \
    apt-get clean && \
    apt-get install curl bzip2 python g++ build-essential -y

# Install node
# https://github.com/creationix/nvm#install-script
RUN curl --silent -o- https://raw.githubusercontent.com/creationix/nvm/v0.31.2/install.sh | bash

# install node and npm
RUN source $NVM_DIR/nvm.sh && \
    nvm install $NODE_VERSION && \
    nvm use $NODE_VERSION && \
    node -v && \
    npm install

# Install Meteor
RUN curl -o meteor_install_script.sh https://install.meteor.com/ && \
    chmod +x meteor_install_script.sh && \
    ./meteor_install_script.sh && \
    export PATH=$PATH:~/.meteor/ && \
    meteor --allow-superuser --version
