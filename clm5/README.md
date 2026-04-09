## Manual build steps

1. Build Docker image

```sh
IMG_VERSION="0.5"
podman build --tag hpscterrsys/clm5:latest --tag hpscterrsys/clm5:$IMG_VERSION .
```

2. Commit image to DockerHub

```sh
podman login docker.io
podman push hpscterrsys/eclm:latest
podman push hpscterrsys/eclm:$IMG_VERSION
podman logout
```
