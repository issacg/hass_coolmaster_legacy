FROM ubuntu:20.04

WORKDIR /workspaces

# Default ENV
ENV LANG C.UTF-8
ENV DEBIAN_FRONTEND noninteractive

# Set shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# Use the mirror protocol for a fast mirror
RUN sed -i -e 's/http:\/\/archive\.ubuntu\.com\/ubuntu\//mirror:\/\/mirrors\.ubuntu\.com\/mirrors\.txt/' /etc/apt/sources.list

# Install docker
# https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/
RUN apt-get update && apt-get install -y --no-install-recommends \
        apt-transport-https \
        ca-certificates \
        curl \
        dbus \
        software-properties-common \
        gpg-agent \
        git \
        jq \
        socat \
        sudo \
    && curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add - \
    && add-apt-repository "deb https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" \
    && apt-get update && apt-get install -y --no-install-recommends \
        docker-ce \
        docker-ce-cli \
        containerd.io
# This is a development container.  Don't bother to clean up apt cache, this way we have it handy later

# Install bashio
RUN curl -J -L -o /tmp/bashio.tar.gz \
        "https://github.com/hassio-addons/bashio/archive/v0.9.0.tar.gz" \
    && mkdir /tmp/bashio \
    && tar zxvf \
        /tmp/bashio.tar.gz \
        --strip 1 -C /tmp/bashio \
    \
    && mv /tmp/bashio/lib /usr/lib/bashio \
    && ln -s /usr/lib/bashio/bashio /usr/bin/bashio \
    && rm -fr /tmp/bashio*

COPY .devcontainer/start_hassio.sh /usr/local/bin/start_hassio.sh

# Install dependencies for the add-on development below.  For example, if you're running Node.js,
# you may want something like the following...
RUN apt-get update && apt-get install -y python3.7 python3-pip \
    && python3.7 -m pip install --upgrade pip \
    && pip install pylint black

# Generate a machine-id for this container
RUN rm /etc/machine-id && dbus-uuidgen --ensure=/etc/machine-id

ENV DEBIAN_FRONTEND=dialog