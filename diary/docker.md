## Docker

### Build a new docker image from a docker file
This is for a image that later will go into Azure. If you want it to be google, find the server url there. The version in the end could be anything
```
docker build -t my-registry.azurecr.io/image-name:1.0.0 .
```
### Push your image to some registry repository in cloud or elsewhere
WARNING! Using --password via the CLI is insecure. Use --password-stdin.  
WARNING! Your password will be stored unencrypted in /home/laoch/.docker/config.json.  
Configure a credential helper to remove this warning. See 
https://docs.docker.com/engine/reference/commandline/login/#credentials-store
```
docker login registry-url -u username -p password
docker push my-registry.azurecr.io/image-name:1.0.0
```
