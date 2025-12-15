FROM yohimik/cs-web-server:latest AS server

USER root

RUN dpkg --add-architecture i386
RUN apt update && apt upgrade -y && apt -y --no-install-recommends install aptitude

RUN apt install -y --no-install-recommends \
    ca-certificates curl patchelf

ENV PKG_CONFIG_PATH=/usr/lib/i386-linux-gnu/pkgconfig

WORKDIR /xashds

ARG METAMOD_FILENAME="metamod.so"
ARG METAMOD_URL="https://github.com/mittorn/metamod-p/releases/download/1/metamod.so"
ARG METAMOD_SHA256="276a7714c1c01bd6669e6835424e64e3b7755153aee24ea0a9144af357c2b634"

ARG AMXMODX_VERSION="1.9.0-git5294"
ARG AMXMODX_FILENAME="amxmodx-$AMXMODX_VERSION-base-linux.tar.gz"
ARG AMXMODX_SHA256="b9467a63aa92fc22330c06817d9059c4462abc3ecb50d39538dda21c8f27bd58"
ARG AMXMODX_URL="https://www.amxmodx.org/amxxdrop/1.9/$AMXMODX_FILENAME"

RUN mkdir -p cstrike/addons/metamod/dlls \
    && touch cstrike/addons/metamod/plugins.ini

RUN curl -sLO "$METAMOD_URL" \
    && echo "$METAMOD_SHA256  $METAMOD_FILENAME" | sha256sum -c - \
    && mv "$METAMOD_FILENAME" cstrike/addons/metamod/dlls/

RUN curl -sLO "$AMXMODX_URL" \
    && echo "$AMXMODX_SHA256  $AMXMODX_FILENAME" | sha256sum -c - \
    && tar -C cstrike/ -zxvf "$AMXMODX_FILENAME" \
    && echo 'linux addons/amxmodx/dlls/amxmodx_mm_i386.so' >> cstrike/addons/metamod/plugins.ini \
    && rm -f "$AMXMODX_FILENAME" \
    && patchelf --clear-execstack cstrike/addons/amxmodx/dlls/amxmodx_mm_i386.so

RUN sed -i 's/dlls\/cs\.so/addons\/metamod\/dlls\/metamod.so/g' cstrike/liblist.gam
RUN cat cstrike/mapcycle.txt >> cstrike/addons/amxmodx/configs/maps.ini

USER xashds