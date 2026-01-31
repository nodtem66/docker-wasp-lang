# Docker Wasp-lang

The unofficial Wasp-lang docker image, designed for minimal alpine linux

## What is Wasp-lang?

Wasp (Web Application Specification) is a high-level language that generate whole source of
your web application, including front-end, back-end, and deployment. Wasp uses React,
Node.js, and Prisma to build your app in a day and deploy it with a single CLI command!

Wasp is the engine that drives [OpenSaaS](https://opensaas.sh/) enabling you to rapid-prototype any SaaS app.
You can build and sell as fast as the current tech can provide.

See: https://wasp.sh/

## Motivation for creating Docker Wasp-lang

Developing an app using **Wasp** is straightforward: run a few commands to scaffold a project, then customize the `.wasp` file to bring your ideas to life. But suppose you find a fantastic NPM library that adds a powerful AI feature to your app. You install it, only to realize later that the library was actually **malicious**.

Supply-chain attacks are becoming increasingly sophisticated and frequent. How can we develop web applications without contantly fearing for our host system's integrity?

_The answer is Dev Containers_

By using [Dev containers](https://containers.dev/), you can strictly isolate your development environment from your host machine. If something goes wrong, you simply delete the container and rebuild it. While this might not be the ideal solution for low-level hardware or native desktop development, it provides a clean, sandboxed, and reproducible environment for modern webapp development.

_Why Wasp-lang container?_

It bridges the gap between Wasp's ease of use and the safety from containerization. It offers:

- **Isolation:** A clean seperation between your host OS and the Wasp development environment
- **Security:** A safe playground to test new libraries without risking your primary system.
- **Consistency:** A "works on my machine" experience for every contributor.

The trade-off:

- **Resource usage:** Running containers requires more system overhead (CPU/memory/disk space) than native development.

> _My setup:_  
> Currently, I use this Wasp-lang container with Podman (rootless mode), VS Code, and
> the Dev Containers extension on Windows 11. The performance is decent, but more
> importantly I have peace of mind. Security concerns no longer limit my creativity.
> Please check [the guide for setting up Dev Container](./DevContainer.md).

## Image Variants

### `wasp-lang:<version>-node<node_version>-alpine`

Ready-to-use images that contains:

- wasp-cli
- node
- npm
- yarn

They are based on the popular [Alpine Linux project](https://alpinelinux.org),
available in [the `alpine` official image](https://hub.docker.com/_/alpine).
Alpine Linux is much smaller than most distribution base images (~10MB),
and thus leads to much slimmer images in general.

The Dockerfiles are at `/wasp-*/node`.
The `nodejs` version can be customized by the argument `NODE_VERSION`. For example:

```sh
docker build -t wasp-lang:0.20-node25-alpine --build-arg NODE_VERSION="25" .
```

### `wasp-lang:<wasp_version>-alpine`

Unusable images with only Wasp static binary (no `nodejs` or `npm` installed), serving as the bare-minimum image before copying to `nodejs` images. See: `/wasp-*/alpine`.

```sh
docker build -t wasp-lang:0.20-alpine --build-arg WASP_VERSION="0.20.1" .
```

### `ghc-static:<version>-alpine`

The internal image for building wasp. It is an Alpine-based Docker image that contains:

- GHC
- GHCUP
- cabal
- nodejs
- npm
- yarn

It is for building static Wasp-cli executables against `musl`. The Dockerfile is at `/ghc-static/`.
The GHC version can be customized by the argument `GHC_VERSION`. For example:

```sh
docker build -t ghc-static:9.6.7-alpine --build-arg GHC_VERSION="9.6.7" .
```

See: https://github.com/fossas/haskell-static-alpine

## License

[Node License information](https://github.com/nodejs/node/blob/master/LICENSE) |
[Wasp License information](https://github.com/wasp-lang/wasp/blob/main/LICENSE)
