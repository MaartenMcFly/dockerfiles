FROM fluxable/nodeandmeteor:latest

ARG ssh_prv_key
ARG ssh_pub_key

MAINTAINER Maarten Smeets, m_mcfly@hotmail.com

#
RUN mkdir /home/fic

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
RUN git clone --branch "master" --depth 50 git@bitbucket.org:fwalraven123/fic.kosmos.git /home/fic/src

# Remove ssh files
RUN rm -rf /root/.ssh

# Build the app
# Throwing away .meteor/platforms and recreating it to get rid of the Android build
RUN cd /home/fic/src && \
    rm .meteor/platforms && \
    echo "browser" > .meteor/platforms && \
    echo "server" >> .meteor/platforms && \
    source $NVM_DIR/nvm.sh && \
    npm update && \
    meteor --allow-superuser build ../build --directory && \
    cd /home/fic/build/bundle/programs/server && \
    npm install

  # Install the version of Node.js we need.
  # && cd /home/fic/fic/build/bundle \
  # && bash -c 'curl "https://nodejs.org/dist/$(<.node_version.txt)/node-$(<.node_version.txt)-linux-x64.tar.gz" > /home/fic/fic/build/required-node-linux-x64.tar.gz' \
  # && cd /usr/local && tar --strip-components 1 -xzf /home/fic/fic/build/required-node-linux-x64.tar.gz \
  # && rm /home/fic/fic/build/required-node-linux-x64.tar.gz \

  # Build the NPM packages needed for build
  # && cd /home/fic/fic/build/bundle/programs/server \
  # && npm install \

  # Get rid of Meteor. We're done with it.
  #&& rm /usr/local/bin/meteor \
  #&& rm -rf ~/.meteor \

  #no longer need curl
  #&& apt-get --purge autoremove curl -y

# RUN npm install -g forever

EXPOSE 80
ENV PORT 80

# cCMD ["forever", "--minUptime", "1000", "--spinSleepTime", "1000", "fic/build/bundle/main.js"]
