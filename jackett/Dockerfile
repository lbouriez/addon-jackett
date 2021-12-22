ARG BUILD_FROM=ghcr.io/hassio-addons/ubuntu-base/amd64:6.2.1

# hadolint ignore=DL3006
FROM ${BUILD_FROM}

ARG \
    BUILD_ARCH=amd64 \
    JACKETT_RELEASE

ENV XDG_DATA_HOME="/share/jackett" \
    XDG_CONFIG_HOME="/config"

# Environment variables
ENV \
    LANG="C.UTF-8" \
    S6_BEHAVIOUR_IF_STAGE2_FAILS=2 \
    S6_CMD_WAIT_FOR_SERVICES=1

# Set shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# hadolint ignore=DL3008
RUN \
    apt-get update \
    \
    && apt-get install -y --no-install-recommends \
              libicu60 \
              libssl1.0 \
              wget \
              curl \
              nginx \
              libnginx-mod-http-lua \
              luarocks \
    \
    && luarocks install lua-resty-http 0.13-0 \
    && mkdir -p /opt/Jackett \
    && if [ -z "${JACKETT_RELEASE}" ]; then \
              JACKETT_RELEASE=$(curl -sX GET "https://api.github.com/repos/Jackett/Jackett/releases/latest" \
              | jq -r .tag_name); \
        fi \
    && ARCH="${BUILD_ARCH}" \
    && if [ "${BUILD_ARCH}" = "aarch64" ]; then ARCH="ARM64"; fi \
    && if [ "${BUILD_ARCH}" = "amd64" ]; then ARCH="AMDx64"; fi \
    && if [ "${BUILD_ARCH}" = "armv7" ]; then ARCH="ARM32"; fi \
    && curl -o \
    /tmp/jackett.tar.gz -L \
    		"https://github.com/Jackett/Jackett/releases/download/${JACKETT_RELEASE}/Jackett.Binaries.Linux${ARCH}.tar.gz" \
    && tar xf \
    /tmp/jackett.tar.gz -C \
    		/opt/Jackett --strip-components=1 \
    && apt-get clean \
    && rm -rf /tmp/* \
              /var/lib/apt/lists/* \
              /var/tmp/* \
              /etc/nginx

# Copy root filesystem
COPY rootfs /

# CMD [ "/run.sh" ]
