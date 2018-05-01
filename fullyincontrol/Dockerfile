FROM dockermcfly/nodeandmeteor:latest

MAINTAINER Maarten Smeets, m_mcfly@hotmail.com

# Both should be passed a --build-arg. Use cat of ssh key files.
ARG repository
ARG ssh_prv_key
ARG ssh_pub_key

# Create mountpoint & temp dir
RUN mkdir /fic && mkdir /tmp/fic

WORKDIR /fic

# Do basic updates
RUN apt-get update -q && \
    apt-get install git ssh -y

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
RUN git clone --branch "master" --depth 50 $repository /tmp/fic

# Remove ssh files
RUN rm -rf /root/.ssh

# Build the app
# Throwing away .meteor/platforms and recreating it to get rid of the Android build
# The whole app ends up in /fic
RUN cd /tmp/fic && \
    rm .meteor/platforms && \
    echo "browser" > .meteor/platforms && \
    echo "server" >> .meteor/platforms && \
    source $NVM_DIR/nvm.sh && \
    meteor npm install && \
    meteor npm install --save angular-messages https://modules.docxtemplater.com/packages/v2/f_walraven_fullyincontrol/f129ee11-1288-4824-7728-37218379d15f/html-module-3.6.6.tar.gz && \
    # Ugly hack to get the version of css-star-rating that seems to work...
    meteor npm install --save css-star-rating@1.1.3 && \
    meteor --allow-superuser build --directory build && \
    cd build/bundle/programs/server && \
    npm install && \
    cd /fic && \
    cp -R /tmp/fic/build/bundle/* . && \
    rm -rf /tmp/fic

RUN apt-get remove --purge git ssh curl bzip2 python g++ build-essential -y

EXPOSE 3000
ENV PORT 3000

CMD ["node", "main.js"]
# CMD ["forever", "meteor --allow-superuser"]
