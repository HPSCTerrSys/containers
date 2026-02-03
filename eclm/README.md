## Manual build steps

1. Build Docker image

```sh
IMG_VERSION="0.2"
podman build --tag hpscterrsys/eclm:latest --tag hpscterrsys/eclm:$IMG_VERSION .
```

2. Commit image to DockerHub

```sh
podman login docker.io
podman push hpscterrsys/eclm:latest
podman push hpscterrsys/eclm:$IMG_VERSION
podman logout
```
