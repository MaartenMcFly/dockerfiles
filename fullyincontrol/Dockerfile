FROM dockermcfly/nodeandmeteor:latest

MAINTAINER Maarten Smeets, m_mcfly@hotmail.com

# Both should be passed a --build-arg. Use cat of ssh key files.
ARG repository
ARG ssh_prv_key
ARG ssh_pub_key

# Create mountpoint and temp dir
RUN mkdir /fic && mkdir /tmp/fic

# Do basic updates
RUN apt-get update -q && \
    apt-get clean && \
    apt-get install curl git ssh bzip2 python g++ build-essential -y

# Make interactive by switching off StrictHostKeyChecking. Is this a security risk?
RUN mkdir -p /root/.ssh && \
    chmod 0700 /root/.ssh && \
    echo "StrictHostKeyChecking no" >> /etc/ssh/ssh_config

# Add the keys and set permissions
RUN echo "$ssh_prv_key" > /root/.ssh/id_rsa && \
    echo "$ssh_pub_key" > /root/.ssh/id_rsa.pub && \
    chmod 600 /root/.ssh/id_rsa && \
    chmod 600 /root/.ssh/id_rsa.pub

# Clone repository
RUN git clone --branch "master" --depth 50 $repository /tmp/fic/src

# Remove ssh files
RUN rm -rf /root/.ssh

# Build the app
# Throwing away .meteor/platforms and recreating it to get rid of the Android build
# Building and then stripping, so the whole app ends up in /fic
RUN cd /tmp/fic/src && \
    rm .meteor/platforms && \
    echo "browser" > .meteor/platforms && \
    echo "server" >> .meteor/platforms && \
    source $NVM_DIR/nvm.sh && \
    npm update && \
    meteor --allow-superuser build ../build --directory && \
    cd /tmp/fic/build/bundle/programs/server && \
    npm install && \
    cp -R /tmp/fic/build/bundle/* /fic && \
    rm -rf /tmp/fic

EXPOSE 80
ENV PORT 80

CMD ["node", "/fic/main.js"]
