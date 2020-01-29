## Make a secret for your private repository.
This will enable you to use an image in in i.e. Azure, AWS or Google in any other K8 cluster:

```yaml
kubectl create secret docker-registry my-registry  --docker-email="your@email" --docker-username="your registry username" --docker-server="your registry server address" --docker-password="your registry password"
```
