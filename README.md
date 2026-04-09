# containers 

This repo contains the Dockerfiles used to build the images hosted at the [HPSCTerrSys DockerHub repository]. 

Run `podman pull <repository>:<tag>` to retrieve the desired image.

## Supported images

| Image | Version |         `podman pull` command          |     Usage guide     |
|:-----:|:-------:|:--------------------------------------:|:-------------------:|
| CLM5  |   0.4   | `podman pull hpscterrsys/clm5:latest`  | [link][clm5-usage]  |
| eCLM  |   0.2   | `podman pull hpscterrsys/eclm:latest`  | [link][eclm-usage]  |
| TSMP2 |   0.2   | `podman pull hpscterrsys/tsmp2:latest` | [link][tsmp2-usage] |

[HPSCTerrSys DockerHub repository]: https://hub.docker.com/u/hpscterrsys
[clm5-usage]: https://hub.docker.com/r/hpscterrsys/clm5
[eclm-usage]: https://hub.docker.com/r/hpscterrsys/eclm
[tsmp2-usage]: https://hub.docker.com/r/hpscterrsys/tsmp2
