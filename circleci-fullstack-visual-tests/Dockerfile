FROM streetteam/circleci-fullstack:master

USER root

# Install deps to download and install xvfb for visual tests
RUN set -xe \
    && apt-get update \
    && apt-get install -y --no-install-recommends \
        ca-certificates bash curl grep \
        xvfb dbus \
    && rm -rf /var/lib/apt/lists/*

RUN mkdir -p /src/visual_tests

FROM node:8.5.0

# fix several issues when running npm as root
RUN npm config set unsafe-perm true \
    && npm install -g backstopjs@3.0.32

WORKDIR /src/visual_tests

# xvfb wrapper
COPY xvfb-run /usr/bin/xvfb-run
