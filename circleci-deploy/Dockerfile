FROM bitnami/minideb:unstable

RUN apt-get update && apt-get install -y --no-install-recommends \
        apt-utils software-properties-common curl gnupg2 apt-transport-https unzip zip jq && \
    curl -L https://cli-assets.heroku.com/apt/release.key | apt-key add - && \
    add-apt-repository "deb https://cli-assets.heroku.com/branches/stable/apt ./" && \
    apt-get update && apt-get install -y --no-install-recommends git openssh-client build-essential checkinstall \
    heroku postgresql-client make gcc sudo && \
    # python3.7 related dependencies
    apt-get install -y --no-install-recommends python2.7-minimal libreadline-dev \
    libncursesw5-dev libssl-dev sqlite3 libsqlite3-dev tk-dev libgdbm-dev libc6-dev \
    libbz2-dev libffi-dev zlib1g-dev wget gettext libgettextpo-dev libgnutls28-dev && \
    # download python3.7 and install
    wget https://www.python.org/ftp/python/3.7.9/Python-3.7.9.tgz -P /usr/src && \
    tar xzf /usr/src/Python-3.7.9.tgz -C /usr/src/ && \
    /usr/src/Python-3.7.9/configure --enable-optimizations && \
    make altinstall && \

    # Install terraform
    git clone https://github.com/tfutils/tfenv.git /usr/src/.tfenv && \
    ln -s /usr/src/.tfenv/bin/* /usr/local/bin && \
    tfenv install 1.0.0 && \
    tfenv use 1.0.0 && \


    # install pip
    # Removing lsb_release as it causes problem when installing pip
    # see: https://github.com/pypa/pip/issues/4924
    rm -rf /usr/bin/lsb_release 2>dev/null && \
    python3.7 -m pip install --upgrade pip && \
    apt-get remove -y --purge software-properties-common gnupg2 apt-transport-https && \
    apt-get clean -y && \
    apt-get autoremove --purge -y && \
    rm -rf /var/lib/apt/lists/* /var/tmp/* /tmp/*

# Ensure python3.7 is default
RUN update-alternatives --install /usr/bin/python3 python3 /usr/local/bin/python3.7 10 && \
    update-alternatives --install /usr/bin/python python /usr/local/bin/python3.7 10
    
RUN pip install --upgrade pip requests setuptools

# Install nave for NodeJS versions
RUN curl -o /usr/bin/nave https://raw.githubusercontent.com/isaacs/nave/master/nave.sh && \
    chmod +x /usr/bin/nave

# Install Sentry-cli, for FE sourcemap upload
RUN curl -sL https://sentry.io/get-cli/ | bash
    
# Restrict further actions to the non-root 'ciuser' user, but with access to sudo
RUN useradd -ms /bin/bash ciuser && echo "ciuser ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers

# Install aws cli session manager
RUN curl "https://s3.amazonaws.com/session-manager-downloads/plugin/latest/ubuntu_64bit/session-manager-plugin.deb" \
    -o "session-manager-plugin.deb" && dpkg -i session-manager-plugin.deb

# Set correct locale and ensure locale is available in any interactive/non-interactive shell
COPY locale /etc/default/locale
ENV LC_ALL C.UTF-8
ENV LANG C.UTF-8
ENV LANGUAGE C.UTF-8

USER ciuser
WORKDIR /home/ciuser

# heroku plugins installation
RUN heroku plugins:install heroku-repo && \
    heroku plugins:install heroku-releases-retry
