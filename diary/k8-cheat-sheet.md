# Kubectl cheat sheet

## Make a secret for your private repository, so you can use the image in the yaml files:

```yaml
kubectl create secret docker-registry my-registry  --docker-email="your@email" --docker-username="your registry username" --docker-server="your registry server address" --docker-password="your registry password"
```
