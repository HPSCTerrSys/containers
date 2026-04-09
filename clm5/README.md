## Manual build steps

1. Build Docker image

```sh
$ IMG_VERSION="0.4"
$ docker build --tag hpscterrsys/clm5:latest --tag hpscterrsys/clm5:$IMG_VERSION .
```

2. Commit image to DockerHub

```sh
$ docker login
$ docker push hpscterrsys/clm5:latest
$ docker push hpscterrsys/clm5:$IMG_VERSION
$ docker logout
```
