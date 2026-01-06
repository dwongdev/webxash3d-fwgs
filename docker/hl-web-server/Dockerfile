FROM --platform=linux/amd64 node:22-alpine AS wasm

RUN npm install -g pnpm

WORKDIR /client

COPY docker/hl-web-server/wasm/package.json package.json
COPY docker/hl-web-server/wasm/pnpm-lock.yaml pnpm-lock.yaml
RUN pnpm install --frozen-lockfile

FROM yohimik/cs-web-server:latest

USER root
RUN rm -rf /xashds/cstrike
RUN rm -rf /xashds/public/cstrike
USER xashds

COPY --from=wasm /client/node_modules/hlsdk-portable/dist/valve/ ./public/valve
COPY --from=wasm /client/node_modules/xash3d-fwgs/dist/valve/ ./public/valve
COPY --from=wasm /client/node_modules/xash3d-fwgs/dist/libmenu.wasm ./public/valve/libmenu.wasm

# Engine configuration
ENV GAME_DIR="valve"
ENV ENGINE_ARGS="-windowed"
ENV ENGINE_CONSOLE=""

# Library paths
ENV CLIENT_WASM_PATH="valve/cl_dlls/client_emscripten_wasm32.wasm"
ENV SERVER_WASM_PATH="valve/dlls/hl_emscripten_wasm32.wasm"
ENV MENU_WASM_PATH="valve/libmenu.wasm"
ENV EXTRAS_PATH="valve/extras.pk3"
ENV SERVER_LIB="dlls/hl_emscripten_wasm32.so"

# Start server
ENTRYPOINT ["./xash", "+ip", "0.0.0.0", "-port", "27015"]

# Default start parameters
CMD ["+map crossfire", "+maxplayers", "16"]