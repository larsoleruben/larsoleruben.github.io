## AZ cli

### List of your subscrptions
```bash
az account list --output table
```
### Set current suscription
```bash
az account set --subscription "My Subscription"
```
### Create container instance group from yaml file
```bash
az container create --resource-group My-Resource-Group --file my-deploy-file.yaml
```
### Get logs from the container
```bash
container logs --resource-group My-Resource-Group --name My-Container-Group-name --container-name My-Container-Name
```
### Log into a container from then az cli
```bash
az container exec --resource-group My-Resource-Group --name My-Container-Group-name --container-name My-Container-Nam --exec-command "/bin/bash"
```
### Show container in a Container group
```bash
az container show --resource-group My-Resource-Group --name My-Container-Group-name --output table
```

### List resource groups in a subscription
```bash
az group list [--subscription]
              [--tag]
```

### Get access to with kubectl to cluster in azure
```bash
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```
