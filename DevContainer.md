# Wasp-lang Dev Container

A Dev Container is a fully-featured development environment that runs inside a Docker (or Podman) container.
Unlike a production container, which only includes the bare essentials to run your app, a Dev Container 
includes your entire environment: compilers, linters, your specific version of Node.js, and even your IDE
extensions.

Instead of installing Wasp, Node, and various databases directly on your Windows, Mac, or Linux machine,
you define them in a configuration file (`.devcontainer/devcontainer.json`). When you open your project,
your IDE remotely steps into this container, providing a seamless experience that feels local but is
completely isolated.

## Quick steps for Windows 11

1) Install [Podman desktop](https://podman-desktop.io/) on Windows. The normal guide uses Docker, but I prefer Podman because of its new idea of deamonless containerization. It is more slim and cleaner, right?
2) Open Podman Desktop and set up the podman machine with rootless mode.
3) Follow [this tutorial](https://podman.io/docs) to check if everything goes correct. For example, after run this command on terminal, it will connect the remote port 80 to the host port 8080. You should see the default webpage through http://localhost:8080.
```sh
podman run -d -p 8080:80/tcp docker.io/library/httpd
```
4) Install [VS Code](https://code.visualstudio.com/) and [Dev Containers extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers). VS Code team also has a good document at https://code.visualstudio.com/docs/devcontainers/containers.
5) Open VS Code. Press F1 > Dev Containers: Settings. Change Docker path (`dev.containers.dockerPath`) to `podman`

6) Open your soure code folder. Press F1 > Dev Containers: New Dev Container...

7) Choose your environment. After that, you can see the configuration file at `.devcontainer/devcontainer.json`. You may modify this file later if you would like to change or add something.

8) To start using Dev Container, Press F1 > Dev Containers: Open Folder in Container. VS Code will start building the image. If something goes wrong, check the log in the panel. Good luck!