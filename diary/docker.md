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

