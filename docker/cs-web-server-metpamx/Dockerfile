FROM debian:trixie-slim AS addons

RUN dpkg --add-architecture i386
RUN apt update && apt upgrade -y && apt -y --no-install-recommends install aptitude

RUN apt install -y --no-install-recommends \
    ca-certificates curl patchelf

ENV PKG_CONFIG_PATH=/usr/lib/i386-linux-gnu/pkgconfig

WORKDIR /xash

ARG METAMOD_FILENAME="metamod.so"
ARG METAMOD_URL="https://github.com/mittorn/metamod-p/releases/download/1/metamod.so"
ARG METAMOD_SHA256="276a7714c1c01bd6669e6835424e64e3b7755153aee24ea0a9144af357c2b634"

ARG AMXMODX_VERSION="1.9.0-git5302-xash-git2"
ARG AMXMODX_FILENAME="amxmodx-$AMXMODX_VERSION-base-linux-gcc.tar.gz"
ARG AMXMODX_FILENAME_CSTRIKE="amxmodx-$AMXMODX_VERSION-cstrike-linux-gcc.tar.gz"
ARG AMXMODX_SHA256="75109e227a14434574e1d3aa5faab62bf8ead51ae02823208fde46d6e3434037"
ARG AMXMODX_SHA256_CSTRIKE="9c0005134498249eea7d3627fa161a0c0c47c80a2ecdb7c45be99d152c85c76c"
ARG AMXMODX_URL_BASE="https://github.com/ludufre/amxmodx/releases/download/v$AMXMODX_VERSION"

RUN mkdir -p /opt/xash/xashds/cstrike/addons/metamod/dlls \
    && touch /opt/xash/xashds/cstrike/addons/metamod/plugins.ini

RUN curl -sLO "$METAMOD_URL" \
    && echo "$METAMOD_SHA256  $METAMOD_FILENAME" | sha256sum -c - \
    && mv "$METAMOD_FILENAME" /opt/xash/xashds/cstrike/addons/metamod/dlls/

RUN curl -sLO "$AMXMODX_URL_BASE/$AMXMODX_FILENAME" \
    && echo "$AMXMODX_SHA256  $AMXMODX_FILENAME" | sha256sum -c - \
    && tar -C /opt/xash/xashds/cstrike/ -zxvf "$AMXMODX_FILENAME" \
    && echo 'linux addons/amxmodx/dlls/amxmodx_mm_i386.so' >> /opt/xash/xashds/cstrike/addons/metamod/plugins.ini \
    && rm -f "$AMXMODX_FILENAME" \
    && patchelf --clear-execstack /opt/xash/xashds/cstrike/addons/amxmodx/dlls/amxmodx_mm_i386.so

RUN curl -sLO "$AMXMODX_URL_BASE/$AMXMODX_FILENAME_CSTRIKE" \
    && echo "$AMXMODX_SHA256_CSTRIKE  $AMXMODX_FILENAME_CSTRIKE" | sha256sum -c - \
    && tar -C /opt/xash/xashds/cstrike/ -zxvf "$AMXMODX_FILENAME_CSTRIKE" \
    && rm -f "$AMXMODX_FILENAME_CSTRIKE"

FROM yohimik/cs-web-server:latest AS server

COPY --from=addons "/opt/xash/xashds/cstrike" cstrike
USER root
RUN sed -i 's/dlls\/cs\.so/addons\/metamod\/dlls\/metamod.so/g' cstrike/liblist.gam
RUN cat cstrike/mapcycle.txt >> cstrike/addons/amxmodx/configs/maps.ini
USER xashds

WORKDIR /xashds
