###
# Bitcoin Full Node + Lightning
#
# Version 0.0.1
#
# Created by:
# Níckolas Goline
# http://github.com/ngoline
# nickolas.goline+github@gmail.com
###

# We will use Ubuntu 16.04 to run our node
FROM ubuntu:16.04

# Create a group and a user
RUN groupadd -r bitcoin && useradd -r -m -g bitcoin bitcoin

# Set environment variables
ENV BITCOIN_DATA /dev/vol1/mainnet
ENV LIGHTNINGD_DATA /dev/vol1/lightning
ENV LIGHTNINGD_RPC_PORT 9835
ENV LIGHTNINGD_VERSION master

# Create the folders needed and link them
RUN mkdir /dev/vol1 \
	&& mkdir /dev/vol1/mainnet \
    && chown -R bitcoin:bitcoin "$BITCOIN_DATA" \
    && ln -sfn "$BITCOIN_DATA" /home/bitcoin/.bitcoin \
    && chown -h bitcoin:bitcoin /home/bitcoin/.bitcoin
RUN mkdir /dev/vol1 \
	&& mkdir /dev/vol1/lightning \
    && chown -R bitcoin:bitcoin "$LIGHTNINGD_DATA" \
    && ln -sfn "$LIGHTNINGD_DATA" /root/.lightning \
    && chown -h bitcoin:bitcoin /root/.lightning

# Add a volume to this machine
VOLUME '/dev/vol1/mainnet'
VOLUME '/dev/vol1/lightning'

# Add bitcoin repository
RUN apt-get update
RUN apt-get install -y --no-install-recommends apt-utils
RUN apt-get install -y --no-install-recommends software-properties-common
RUN add-apt-repository ppa:bitcoin/bitcoin

# Install bitcoind
RUN apt-get update
RUN apt-get install -y --no-install-recommends bitcoind

# Install other tools 
RUN apt-get install -y --no-install-recommends autoconf automake build-essential git libtool libgmp-dev \
	libsqlite3-dev python python3 net-tools zlib1g-dev

# Set bitcoind to run on start-up
#RUN echo "" >>! /var/spool/crontab/root
#RUN crontab -l | { cat; echo "@reboot bitcoind -daemon"; } | crontab -

# Set the working dir
WORKDIR /home/bitcoin/

# Clone c lightning from github, configure, make
RUN git clone https://github.com/ElementsProject/lightning.git \
    && cd lightning \
    && ./configure \
    && make -j3 DEVELOPER=0 \
    && cp lightningd/lightning* cli/lightning-cli /usr/bin/



# Copy bitcoin.conf
ADD mainnet/. "$BITCOIN_DATA"

# Start bitcoin server
RUN bitcoind -daemon

# EXPOSE rpc port for the node to allow outside container access
EXPOSE 8332 8333 18332 18333

# Drop user into bash shell by default
CMD ["/bin/bash"]