FROM ubuntu:lunar
ENV VERSION=0.0.15

# Set locale non-interactively
ARG DEBIAN_FRONTEND=noninteractive
ENV LANG C.UTF-8

RUN apt-get -y update \
    && apt-get install -y --no-install-recommends \
            apt-utils \
            build-essential \
            curl \
            binutils \
            git \
            jq \
            locales \
            tzdata \
            wget \
    && apt-get clean -y \
    && rm -rf /var/lib/apt/lists/*

# Set locale non-interactively
RUN echo "America/Los_Angeles" > /etc/timezone \
    && dpkg-reconfigure -f noninteractive tzdata \
    && sed -i -e 's/# en_US.UTF-8 UTF-8/en_US.UTF-8 UTF-8/' /etc/locale.gen \
    && echo 'LANG="en_US.UTF-8"' > /etc/default/locale \
    && dpkg-reconfigure --frontend=noninteractive locales \
    && update-locale LANG=en_US.UTF-8


#####################################
# requirements install
#####################################
ENV APPLICATION_ROOT=/tmp \
    LANG=en_US.UTF-8 \
    LANGUAGE=en_US:en \
    LC_ALL=en_US.UTF-8 \
    TERM=linux \
    TERMINFO=/etc/terminfo

WORKDIR $APPLICATION_ROOT
ENTRYPOINT ["/bin/bash", "-c", "echo Hello from Kaniko"]