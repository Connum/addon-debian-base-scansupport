ARG BUILD_FROM=ghcr.io/hassio-addons/debian-base:latest
# hadolint ignore=DL3006
FROM ${BUILD_FROM}

# Environment variables
ENV \
    TERM="xterm-256color"

# Set shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# Install base system
ARG BUILD_ARCH=amd64
ARG BASHIO_VERSION="v0.16.3"
ARG S6_OVERLAY_VERSION="3.2.0.2"
ARG TEMPIO_VERSION="2024.11.2"
# hadolint ignore=DL3008
RUN \
    apt-get update \
    \
    && apt-get install -y --no-install-recommends \
        sane \
        sane-utils \
        dbus \
        scanbd \
        git \
        sed \
    # sane-scan-pdf dependencies
        bash \
        netpbm \
        ghostscript \
        poppler-utils \
        units \
        imagemagick \
        unpaper \
        util-linux \
        tesseract-ocr \
        tesseract-ocr-all \
        parallel \
        bc \
    # sane-scan-pdf
    && git clone --branch master https://github.com/rocketraman/sane-scan-pdf.git /opt/sane-scan-pdf && \
        chmod a+x /opt/sane-scan-pdf/scan /opt/sane-scan-pdf/scan_perpage && \
        ln -s /opt/sane-scan-pdf/scan /usr/local/bin/sane-scan-pdf \
    # patch sane-scan-pdf script
    && sed -i 's|\${XDG_DATA_HOME:-\$HOME/.local/share}|/config|g' /opt/sane-scan-pdf/scan \
    && sed -i 's|"\$DIR/scan_perpage"|/opt/sane-scan-pdf/scan_perpage|g' /opt/sane-scan-pdf/scan \
    # add root user to scanner group
    && usermod -a -G scanner root \
    # cleanup
    && mkdir -p /var/run/dbus/ \
    && apt-get purge -y --auto-remove \
        xz-utils \
    # cleanup
    && apt-get remove --purge -y \
        git \
    && apt-get autoremove -y \
    && apt-get clean \
    && rm -fr \
        /tmp/* \
        /var/{cache,log}/* \
        /var/lib/apt/lists/* \
    # run setup script
    chmod a+x /setup.sh \
    && . /setup.sh \
    && rm /setup.sh

# Copy root filesystem
#COPY rootfs /

# Build arugments
ARG BUILD_DATE
ARG BUILD_REF
ARG BUILD_VERSION
ARG BUILD_REPOSITORY

# Labels
LABEL \
    io.hass.name="Addon Debian base for ${BUILD_ARCH} with added scanning support" \
    io.hass.description="Home Assistant Community Add-on: ${BUILD_ARCH} Debian base image with added scanning support" \
    io.hass.arch="${BUILD_ARCH}" \
    io.hass.type="base" \
    io.hass.version=${BUILD_VERSION} \
    io.hass.base.version=${BUILD_VERSION} \
    io.hass.base.name="debian" \
    io.hass.base.image="hassioaddons/debian-base" \
    maintainer="Franck Nijhof <frenck@addons.community>" \
    org.opencontainers.image.title="Addon Debian base for ${BUILD_ARCH} with added scanning support" \
    org.opencontainers.image.description="Home Assistant Community Add-on: ${BUILD_ARCH} Debian base image with added scanning support" \
    org.opencontainers.image.vendor="Home Assistant Community Add-ons" \
    org.opencontainers.image.authors="authors of the base image with added scanning support by Sarah Groß" \
    org.opencontainers.image.licenses="MIT" \
    org.opencontainers.image.url="https://addons.community" \
    org.opencontainers.image.source="https://github.com/${BUILD_REPOSITORY}" \
    org.opencontainers.image.documentation="https://github.com/${BUILD_REPOSITORY}/blob/main/README.md" \
    org.opencontainers.image.created=${BUILD_DATE} \
    org.opencontainers.image.revision=${BUILD_REF} \
    org.opencontainers.image.version=${BUILD_VERSION}
