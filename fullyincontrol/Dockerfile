FROM fluxable/nodeandmeteor:latest

ARG ssh_prv_key
ARG ssh_pub_key

MAINTAINER Maarten Smeets, m_mcfly@hotmail.com

# Create mountpoint
RUN mkdir /fic

# Do basic updates
RUN apt-get update -q && apt-get clean && apt-get install curl git ssh bzip2 python g++ build-essential -y

# Make interactive by switching off SttictHostKeyChecking. Is this a security risk?
RUN mkdir -p /root/.ssh && \
    chmod 0700 /root/.ssh && \
    echo "StrictHostKeyChecking no" >> /etc/ssh/ssh_config

# Add the keys and set permissions
RUN echo "$ssh_prv_key" > /root/.ssh/id_rsa && \
    echo "$ssh_pub_key" > /root/.ssh/id_rsa.pub && \
    chmod 600 /root/.ssh/id_rsa && \
    chmod 600 /root/.ssh/id_rsa.pub

# Clone repository
RUN git clone --branch "master" --depth 50 git@bitbucket.org:fwalraven123/fic.kosmos.git /fic/src

# Remove ssh files
RUN rm -rf /root/.ssh

# Build the app
# Throwing away .meteor/platforms and recreating it to get rid of the Android build
# Building and then stripping, so the whole app ends up in /fic
RUN cd /fic/src && \
    rm .meteor/platforms && \
    echo "browser" > .meteor/platforms && \
    echo "server" >> .meteor/platforms && \
    source $NVM_DIR/nvm.sh && \
    npm update && \
    meteor --allow-superuser build ../build --directory && \
    cd /fic/build/bundle/programs/server && \
    npm install && \
    rm -rf /fic/src && \
    cp -R /fic/build/bundle/* /fic && \
    rm -rf /fic/build

# RUN npm install -g forever

EXPOSE 80
ENV PORT 80

# cCMD ["forever", "--minUptime", "1000", "--spinSleepTime", "1000", "fic/build/bundle/main.js"]
