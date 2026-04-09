# eCLM Docker Build Guide

This guide requires [Podman](https://docs.podman.io/en/v4.6.1/index.html). If you're on Debian/Ubuntu, run

```sh
sudo apt update
sudo apt-get install -y podman
```

Otherwise, head over to the [Podman install guide](http://podman.io/docs/installation) and follow the instructions
specific to your system.

## Building the eCLM container

> [!NOTE]
> This section is only relevant for developers who have push access rights to the [HPSCTerrSys DockerHub](https://hub.docker.com/u/hpscterrsys).

1. Build eCLM base image.

```sh
podman build --tag hpscterrsys/eclm:latest --tag hpscterrsys/eclm:0.2 .
```

2. Push eCLM base image to DockerHub.

```sh
podman login --username $USER docker.io
podman push hpscterrsys/eclm:latest
podman push hpscterrsys/eclm:0.2
```

3. Build eCLM dev image.

```sh
podman build --tag hpscterrsys/eclm:latest-dev --tag hpscterrsys/eclm:0.2-dev -f Dockerfile_dev
```

4. Push eCLM dev image to DockerHub.

```sh
podman push hpscterrsys/eclm:latest-dev
podman push hpscterrsys/eclm:0.2-dev
podman logout
```

## Using the container image for local development

First we configure file sharing between the host machine and the container image. Decide which
local folder you'd like to expose to the container image, then save its full path to `WORKDIR`:

```sh
WORKDIR=/home/user/shared_folder
```

Then create a container named `eclm-dev`.

```sh
podman run --name eclm-dev -it -v ${WORKDIR}:/root/$(basename ${WORKDIR}) hpscterrsys/eclm:latest-dev
```

The command above will also start `eclm-dev`. Verify that everything works by running these commands:

```sh
root@e06d13d06436:~# pwd                  # $HOME directory
/root
root@e06d13d06436:~# ls                   # Should see your WORKDIR here
shared_folder
root@e06d13d06436:~# echo $CC $FC $CXX    # Preset compilers in this dev image
mpicc mpif90 mpicxx
```

If the container has been stopped, simply run `podman start -ai eclm-dev` to restart it. `podman run` is only done once, *i.e.* during
container creation. It's also important to know these commands:

- `podman ps -a`: list created containers
- `podman images`: list downloaded images
- `podman rm`: Delete existing container
- `podman rmi`: Delete existing image

The mentioned `podman` commands are sufficient to manage your local `eclm-dev` image. For more information regarding `podman` usage, visit the [Podman tutorial](https://docs.podman.io/en/v4.6.1/Tutorials.html).



