# This builder should match the one used in ghc-static image
ARG GHC_BUILDER_IMAGE="nodtem66/ghc-static:9.6.7-alpine"
# This is the runtime image that Wasp will run in the end
ARG BASE_IMAGE="alpine:3.23"

# Stage 1. Build Wasp binary and package it with its data files
FROM ${GHC_BUILDER_IMAGE} AS ghc_builder

ARG WASP_VERSION="0.20.1"
ENV BUILD_STATIC=1
ENV LC_ALL=C.UTF-8

WORKDIR /home

# Download and unpack Wasp source code
RUN wget "https://github.com/wasp-lang/wasp/archive/refs/tags/v${WASP_VERSION}.tar.gz" && \
    tar -xzf "v${WASP_VERSION}.tar.gz" && \
    rm "v${WASP_VERSION}.tar.gz"

# Build Wasp
# Note: run script often changes. In Wasp-0.20, it does not build frontend/backend packages.
# But it does in the future versions. I decided to build it manually to make sure
# that the built Wasp binary has everything it needs.
RUN cd /home/wasp-${WASP_VERSION}/waspc && chmod a+x run && \
    cabal update && \
    cabal build wasp-cli --enable-executable-static --enable-executable-stripping \
    --disable-tests --disable-coverage --disable-benchmarks

# Build NPM packages (frontend/backend) used by Wasp if ./run build did not do it
RUN for pkg in /home/wasp-${WASP_VERSION}/waspc/data/packages/*/package.json; do \
    pkg_dir=$(dirname "$pkg") && \
    cd "$pkg_dir" && \
    npm install && \
    npm run build && \
    mkdir -p ../tmp && \
    mv dist ../tmp/ && \
    mv package*.json ../tmp/ && \
    rm -rf * && \
    mv ../tmp/* . && \
    rm -rf ../tmp; \
    done

# In the new version of Wasp, ./tools/build_libs.ts is used to build Wasp libs
RUN if [ -f /home/wasp-${WASP_VERSION}/waspc/tools/build_libs.ts ]; then \
    node --experimental-strip-types /home/wasp-${WASP_VERSION}/waspc/tools/build_libs.ts; \
    fi

ENV WASP_DATA_DIR="/usr/local/share/wasp-lang/${WASP_VERSION}"
ENV WASP_BIN_DIR="/usr/local/bin"
ENV WASP_BIN="/usr/local/bin/wasp"

# Package Wasp into a tar.gz file
RUN cd /home/wasp-${WASP_VERSION}/waspc && \
    ./tools/make_binary_package.sh wasp.tar.gz && \
    tar -xzf wasp.tar.gz && rm wasp.tar.gz && \
    mv ./wasp-bin ${WASP_BIN_DIR}/wasp-bin && \
    chmod a+x ${WASP_BIN_DIR}/wasp-bin && \
    mkdir -p ${WASP_DATA_DIR}/ && \
    mv ./data ${WASP_DATA_DIR} && \
    printf '#!/bin/sh\nwaspc_datadir=%s/data exec %s/wasp-bin "$@"\n' "$WASP_DATA_DIR" "$WASP_BIN_DIR" \
    > $WASP_BIN && \
    chmod a+x $WASP_BIN

# Stage 2. Create the final Wasp image using Node runtime image
FROM ${BASE_IMAGE}

WORKDIR /home

COPY --from=ghc_builder /usr/local/bin/wasp* /usr/local/bin/
COPY --from=ghc_builder /usr/local/share/wasp-lang /usr/local/share/wasp-lang

RUN apk update && apk add --no-cache openssl && \
    addgroup -g 1001 wasp && \
    adduser -u 1001 -G wasp -s /bin/sh -D wasp

ENV USER=wasp