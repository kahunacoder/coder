FROM debian:stretch-slim

# Set arguments and environment variables.
ARG CODER_VERSION=1.1156-vsc1.33.1
ENV CODER_VERSION="${CODER_VERSION}"
ENV PATH=/home/coder/.local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

# Install dependencies.
RUN apt-get update && apt-get install -y apt-utils net-tools git locales dumb-init curl wget unzip zsh && \
    apt -y autoremove

# Set locale.
COPY ./.docker/coder/config/environment /etc/environment
COPY ./.docker/coder/config/locale.gen /etc/locale.gen
COPY ./.docker/coder/config/locale.conf /etc/locale.conf
RUN locale-gen en_US.UTF-8

# Create coder-user and install fixuid.
RUN addgroup --gid 1000 coder && \
    adduser --uid 1000 --ingroup coder --home /home/coder --shell /bin/zsh --disabled-password --gecos "" coder && \
    USER=coder && \
    GROUP=coder && \
    mkdir -p /home/coder/.local/bin && \
    chown -R coder:coder /home/coder/.local && \
    curl -SsL https://github.com/boxboat/fixuid/releases/download/v0.4/fixuid-0.4-linux-amd64.tar.gz | tar -C /home/coder/.local/bin -xzf - && \
    chown root:root /home/coder/.local/bin/fixuid && \
    chmod 4755 /home/coder/.local/bin/fixuid && \
    mkdir -p /etc/fixuid && \
    chown -R coder:coder /etc/fixuid
COPY ./.docker/coder/config/config.yml /etc/fixuid/config.yml

# Run as coder for the rest of the build.
USER coder

# Install the visual code-server.
RUN cd /home/coder && \
    curl -SsL https://github.com/cdr/code-server/releases/download/$CODER_VERSION/code-server$CODER_VERSION-linux-x64.tar.gz | tar -C /home/coder -xzf - && \
    chmod +x /home/coder/code-server$CODER_VERSION-linux-x64/code-server && \
    cp -r /home/coder/code-server$CODER_VERSION-linux-x64/code-server /home/coder/.local/bin && \
    rm -rf /home/coder/code-server$CODER_VERSION-linux-x64 /home/coder/code-server$CODER_VERSION-linux-x64.tar.gz

# Create the code-server directory to ensure coder is the owner of it.
RUN mkdir -p /home/coder/project/code-server && \
    chown -R coder:coder /home/coder/project

# Configure settings and extensions.
RUN mkdir -p /home/coder/.local/share/code-server/Backups && \
    mkdir -p /home/coder/.local/share/code-server/extensions && \
    mkdir -p /home/coder/.local/share/code-server/User
COPY ./.docker/coder/config/settings.json /home/coder/.local/share/code-server/User/settings.json

# Configure oh-my-zsh
RUN wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | zsh || true
COPY ./.docker/coder/config/zshrc /home/coder/.zshrc

WORKDIR /home/coder/project/code-server
EXPOSE 8443
VOLUME [ "/home/coder/project/code-server" ]

ENTRYPOINT ["dumb-init", "fixuid", "code-server", "--allow-http", "--no-auth"]