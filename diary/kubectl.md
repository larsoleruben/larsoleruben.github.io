# Kubectl!
### Make a secret for your private repository.
This will enable you to use an image in in i.e. Azure, AWS or Google in any other K8 cluster:

```yaml
kubectl create secret docker-registry my-registry  --docker-email="your@email" --docker-username="your registry username" --docker-server="your registry server address" --docker-password="your registry password"
```
### Switch to another context, which is in you ~/.kube/config file
You can find the names in the ~/.kube/config file. 
```
kubectl config use-context my-cluster-name
```
### Create a new secret fropm a file (json or yaml or what not)
```
kubectl create secret generic secret-name --from-file=secrets.json
```
