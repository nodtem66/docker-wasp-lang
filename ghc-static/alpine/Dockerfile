
# Haskell static build environment based on Alpine Linux
# https://github.com/fossas/haskell-static-alpine
# These should match the corresponding versions from the wasp repo:
# https://github.com/wasp-lang/wasp/blob/main/waspc/cabal.project
ARG GHC_VERSION="9.6.7"
ARG NODE_IMAGE="node:22-alpine"
ARG CABAL_VERSION="3.12.1.0"

FROM ${NODE_IMAGE}

# Install deps for:
# - ghc/cabal: curl gcc g++ gmp-dev ncurses-dev libffi-dev make xz tar perl zlib-dev
# - static builds: zlib-static ncurses-static gmp-static
# - random haskell libraries with cbits (basement): binutils-gold
# - to use embedded binaries in development (ie themis): libc6-compat
RUN apk --no-cache add binutils-gold curl gcc g++ gmp-dev gmp-static ncurses-dev ncurses-static libffi-dev make xz xz-static tar perl zlib-dev zlib-static bash libc6-compat

ENV PATH="/root/.cabal/bin:/root/.ghcup/bin:$PATH"

# There aren't official builds of ghcup that are built against musl for aarch64.
# So the following two steps do the install manually on that platform.
# They manually do the same steps that ghcup would.
# ghcup should have support for this soon: https://github.com/haskell/ghcup-hs/issues/1012#issuecomment-2294829976
# When that happens, you should be able to remove the 'else' branches and have the install work for all platforms.
RUN export BOOTSTRAP_HASKELL_NONINTERACTIVE=1 && \
    export BOOTSTRAP_HASKELL_MINIMAL=1 && \
    curl --proto '=https' --tlsv1.2 -sSf https://get-ghcup.haskell.org | sh

# Install GHC
RUN ghcup install ghc $GHC_VERSION && \
    ghcup set ghc $GHC_VERSION && \
    rm -rf "$HOME/.ghcup/share/doc"

# Install cabal
RUN ghcup install cabal ${CABAL_VERSION}

# Sets USER environment to overcome issue, as described in:
# https://github.com/cachix/install-nix-action/issues/122
ENV USER=guest