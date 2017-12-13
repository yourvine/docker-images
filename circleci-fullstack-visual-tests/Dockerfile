FROM circleci-fullstack

USER root

# Install deps to download and install xvfb for visual tests
RUN set -xe \
    && apt-get update \
    && apt-get install -y --no-install-recommends \
        ca-certificates bash curl grep \
        xvfb dbus \
    && rm -rf /var/lib/apt/lists/*

RUN set -xe \
    && curl -fsSL https://dl-ssl.google.com/linux/linux_signing_key.pub | apt-key add - \
    && echo "deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main" > /etc/apt/sources.list.d/google-chrome.list \
    && apt-get update \
    && apt-get install -y google-chrome-stable \
    && rm -rf /var/lib/apt/lists/*

RUN mkdir -p /src/visual_tests
# fix several issues when running npm as root
RUN npm config set unsafe-perm true

COPY package.json /src/visual_tests/package.json

WORKDIR /src/visual_tests

RUN yarn install

# xvfb wrapper
COPY xvfb-run /usr/bin/xvfb-run

USER ciuser
