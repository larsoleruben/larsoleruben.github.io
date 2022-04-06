---
layout: page
title: Docker
permalink: /diary/docker
---

## Build a new docker image from a docker file and tag it for azure
This is for a image that later will go into Azure. If you want it to be google, find the server url there. The version in the end could be anything
```
docker build -t my-registry.azurecr.io/image-name:1.0.0 .
```
## Push your image to some registry repository in azure cloud or elsewhere
WARNING! Using --password via the CLI is insecure. Use --password-stdin.
WARNING! Your password will be stored unencrypted in /home/laoch/.docker/config.json.
Configure a credential helper to remove this warning. [See this](
https://docs.docker.com/engine/reference/commandline/login/#credentials-store)
PS: The namespace (whatever is before the /) must be the same as the registry url, or it will not get pushed. Took me a few hours to figure that out :-)
```bash
docker login registry-url -u username -p password
docker push my-registry.azurecr.io/image-name:1.0.0
```
## Install docker on ubuntu cook book
There is straight forward no nonsence [cookbook](https://docs.docker.com/engine/install/ubuntu/) here

## start a container and go into it's shell
```bash
docker run --rm -it ubuntu /bin/bash
```
## Kill a container that is running
PS: You'll only need the first 3 characters from them ID to kill it.
```bash
docker container ls
# this will reveal all the containers running and their ID's or names
docker container stop container_id or name
```

## Build a container with a tag for some registry in azure.
The domain has to be part of the name

```bash
docker build -t myregname.azurecr.io/cfdmails:1.006 .
```
## Run above created image locally with the appconfig folder mounted (similar to a mounted secret)

You need to have a folder in root with the name kvsecrets and it should contain the appconfig.json file

```bash
 docker run -d \
  --name cfdmails \
  --mount type=bind,source="$(pwd)"/kvsecrets,target=/kvsecrets \
  myregname.azurecr.io/cfdmails:1.006
```

## I want to connect from a container to a service on the host (maybe also running in Docker)
This can be done in several ways, but say you have a database running in docker and your scripts uses "localhost" to access it from another container, it will not work.
Instead of "localhost" you have to use "host.docker.internal" and it will workd again.
See mere [here](https://docs.docker.com/desktop/mac/networking/)

## The nightmare of building dockerfile with "PIP_EXTRA_INDEX_URL"
Ok, here is some really good info. How to read the azure artifacts when building a dockerfile, containing "PIP_EXTRA_INDEX_URL"
This will give the image user the necessary rights to get your artifacts from Azure DevOps.
Remember the passworkd is your Token for artefacts, which you have to get from you account in azure devops.
```docker
ARG USER
ARG PASS
RUN echo "machine your.pkgs.visualstudio.com \
          "    login ${USER} \
          "    password ${PASS}" > /root/.netrc
 RUN chown root ~/.netrc
 RUN chmod 0600 ~/.netrc
```
Or you can do like this with the extra index url is:
```bash
--build-arg PIP_EXTRA_INDEX_URL= https://your_initials:your_secret_token@your_server.pkgs.visualstudio.com/your_project/_packaging/obs-cm/pypi/simple/
```