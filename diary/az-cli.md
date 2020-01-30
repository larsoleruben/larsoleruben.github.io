## AZ cli

### List of your subscrptions
```
az account list --output table
```
### Set current suscription
```
account set --subscription "My Subscription"
```
### Create container instance group from yaml file
```
az container create --resource-group My-Resource-Group --file my-deploy-file.yaml
```
### Get logs from the container
```
container logs --resource-group My-Resource-Group --name My-Container-Group-name --container-name My-Container-Name
```
### Log into a container from then az cli
```
az container exec --resource-group My-Resource-Group --name My-Container-Group-name --container-name My-Container-Nam --exec-command "/bin/bash"
```
### Show container in a Container group
```
az container show --resource-group My-Resource-Group --name My-Container-Group-name --output table
```
