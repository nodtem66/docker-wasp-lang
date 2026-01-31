ARG NODE_VERSION=25
ARG WASP_ALPINE_IMAGE=nodtem66/wasp-lang:0.20-alpine

FROM ${WASP_ALPINE_IMAGE} as wasp_image
FROM node:${NODE_VERSION}-alpine

COPY --from=wasp_image /usr/local/bin/wasp* /usr/local/bin/
COPY --from=wasp_image /usr/local/share/wasp-lang/ /usr/local/share/wasp-lang/

# Install openssl package needed by Wasp
RUN apk update && apk add --no-cache openssl