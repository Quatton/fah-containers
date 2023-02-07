# Quick Start for Debian Linux
FROM node:18
LABEL description="Folding@home Debian Linux Container"

RUN apt-get update && apt-get install -y sudo apt-utils

# Add a user w/o password (sudoers)
RUN useradd -ms /bin/bash fah && \
    echo "fah ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers
USER fah
WORKDIR /home/fah

# Prerequisites
RUN sudo apt-get update && sudo apt-get install -y scons \
    git \
    npm \
    build-essential \
    libssl-dev

ARG FAH_VERSION=v8.1.11

# Get the code
RUN git clone --depth 1 https://github.com/cauldrondevelopmentllc/cbang
RUN git clone --depth 1 --branch ${FAH_VERSION} https://github.com/foldingathome/fah-client-bastet
RUN git clone --depth 1 --branch ${FAH_VERSION} https://github.com/foldingathome/fah-web-client-bastet

# Build the Folding@home Client

ENV CBANG_HOME=/home/fah/cbang
RUN scons -C cbang
RUN scons -C fah-client-bastet
RUN scons -C fah-client-bastet package

# Install the package
RUN sudo dpkg -i fah-client-bastet/fah-client_${FAH_VERSION}_amd64.deb \
    > $HOME/fah-install.log \
    || echo "No need to uninstall"

# Start the development web server
RUN cd fah-web-client-bastet && npm install

VOLUME ["/home/fah"]
EXPOSE 3000 4173

# Start the development web server with npm run dev using CMD
CMD cd fah-web-client-bastet && npm run serve --host