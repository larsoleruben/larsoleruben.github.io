---
layout: page
title: Kubectl
permalink: /diary/kubectl
---
### Make a secret for your private repository.
This will enable you to use an image in in i.e. Azure, AWS or Google in any other K8 cluster:

```bash
kubectl create secret docker-registry my-registry  --docker-email="your@email" --docker-username="your registry username" --docker-server="your registry server address" --docker-password="your registry password"
```
### Create a new secret fropm a file (json or yaml or what not)
```bash
kubectl create secret generic secret-name --from-file=secrets.json
```
### Decoding a Secret
```bash
kubectl get secret mysecret -o yaml
```
### Decode the password field
```bash
echo 'MWYyZDFlMmU2N2Rm' | base64 --decode
```
### Editing a Secret
```bash
kubectl edit secrets mysecret
```
But beware, you then get the secret 64 encoded, and you will have to decode and encode it before updating.
Fortunatly there is a very good small [python script for that](https://github.com/lbolla/kube-secret-editor), which you can find [here](https://github.com/lbolla/kube-secret-editor)
### Switch to another context, which is in your ~/.kube/config file
You can find the names in the ~/.kube/config file.
```bash
kubectl config use-context my-cluster-name
```

### Autocomplete in bash
```bash
source <(kubectl completion bash) # setup autocomplete in bash into the current shell, bash-completion package should be installed first.
echo "source <(kubectl completion bash)" >> ~/.bashrc # add autocomplete permanently to your bash shell.
```
### Shorthand alias for kubectl
```bash
alias k=kubectl
complete -F __start_kubectl k
```
### Changeing namespace "on the fly", i.e. not permanently
```bash
kubectl --namespace=mynamespace
```
### Setting namespace preference
```bash
kubectl config set-context --current --namespace=<insert-namespace-name-here>
# Validate it
kubectl config view --minify | grep namespace:
```
### Se resources in namespaces
```bash
# In a namespace
kubectl api-resources --namespaced=true

# Not in a namespace
kubectl api-resources --namespaced=false
```

### All config commands, meaning kubectl config .....and then:
```bash
current-context Displays the current-context
delete-cluster  Delete the specified cluster from the kubeconfig
delete-context  Delete the specified context from the kubeconfig
get-clusters    Display clusters defined in the kubeconfig
get-contexts    Describe one or many contexts
rename-context  Renames a context from the kubeconfig file.
set             Sets an individual value in a kubeconfig file
set-cluster     Sets a cluster entry in kubeconfig
set-context     Sets a context entry in kubeconfig
set-credentials Sets a user entry in kubeconfig
unset           Unsets an individual value in a kubeconfig file
use-context     Sets the current-context in a kubeconfig file
view            Display merged kubeconfig settings or a specified kubeconfig file
```

### Easy way to make a secret yaml file from a secret file
```bash
kubectl create secret generic my-secret-name \
    --from-file=./my-secret-file.json --dry-run -o yaml |
  kubectl apply -f -
```

### Delete a user from a context
```bash
kubectl --kubeconfig=config-demo config unset users.<name>
```
### Rolling restart of a deployment pod.
```bash
kubectl rollout restart deployment/<my-deployment>
```

### Get a proxy on your localhost
```bash
kubectl proxy
```

### Get a terminal in a busybox image
```bash
kubectl run -i --rm  --tty busybox --image=busybox --restart=Never -- sh
```

### Get the logs from a crashing container during container init causing crashloopbackoff
```
kubectl logs you_pod -c  container_name
```

### Restart a pod in Kubernerest
As of kubernetes 1.15, you can now do a rolling restart of all pods for a deployment, so that you don’t take the service down
```bash
kubectl -n <namepace-name> rollout restart deployment <deployment-name>
```
If you have set the namespace in you config, the namespace is optional

### scaling a replica set
```bash
kubectl scale --replicas=3 rs/foo
```
## Links
[A page with most CLI commands](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)
[Kubernetes documentation home](https://kubernetes.io/docs/home/)
[Kubernetes secrets in genereal](https://kubernetes.io/docs/concepts/configuration/secret/)

