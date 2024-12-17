---
layout: page
title: Kubectl
permalink: /diary/kubectl
---

## Scale all deployments in a namespace in one go
```bash
kubectl scale deploy -n <namespace> --replicas=0 --all
```
## Make a secret for your private repository.
This will enable you to use an image in in i.e. Azure, AWS or Google in any other K8 cluster:

```bash
kubectl create secret docker-registry my-registry  --docker-email="your@email" --docker-username="your registry username" --docker-server="your registry server address" --docker-password="your registry password"
```
## Create a new secret fropm a file (json or yaml or what not)
```bash
kubectl create secret generic secret-name --from-file=secrets.json
```
## Decoding a Secret
```bash
kubectl get secret mysecret -o yaml
```
## Decode the password field
```bash
echo 'MWYyZDFlMmU2N2Rm' | base64 --decode
```
## Editing a Secret
```bash
kubectl edit secrets mysecret
```
But beware, you then get the secret 64 encoded, and you will have to decode and encode it before updating.
Fortunatly there is a very good small [python script for that](https://github.com/lbolla/kube-secret-editor), which you can find [here](https://github.com/lbolla/kube-secret-editor)

## Get a list of all contexts
```bash
kubectl config get-contexts
```

## Switch to another context, which is in your ~/.kube/config file
You can find the names in the ~/.kube/config file.
```bash
kubectl config use-context my-context-name
```
## Set the context manually
```bash
kubectl config set-context my-context-name
```


## Autocomplete in bash
```bash
source <(kubectl completion bash) # setup autocomplete in bash into the current shell, bash-completion package should be installed first.
echo "source <(kubectl completion bash)" >> ~/.bashrc # add autocomplete permanently to your bash shell.
```
## Shorthand alias for kubectl
```bash
alias k=kubectl
complete -F __start_kubectl k
```
## Changeing namespace "on the fly", i.e. not permanently
```bash
kubectl --namespace=mynamespace
```
## Setting namespace preference
```bash
kubectl config set-context --current --namespace=<insert-namespace-name-here>
# Validate it
kubectl config view --minify | grep namespace:
```
## Se resources in namespaces
```bash
# In a namespace
kubectl api-resources --namespaced=true

# Not in a namespace
kubectl api-resources --namespaced=false
```

## All config commands, meaning kubectl config .....and then:
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

## Easy way to make a secret yaml file from a secret file
```bash
kubectl create secret generic my-secret-name \
    --from-file=./my-secret-file.json --dry-run -o yaml |
  kubectl apply -f -
```

## Delete a user from a context
```bash
kubectl --kubeconfig=config-demo config unset users.<name>
```
## Rolling restart of a deployment pod.
```bash
kubectl rollout restart deployment/<my-deployment>
```

## Get a proxy on your localhost
```bash
kubectl proxy
```

## Get a terminal in a busybox image
```bash
kubectl run -i --rm  --tty busybox --image=busybox --restart=Never -- sh
```

## Restart a pod in Kubernerest
As of kubernetes 1.15, you can now do a rolling restart of all pods for a deployment, so that you don’t take the service down
```bash
kubectl -n <namepace-name> rollout restart deployment <deployment-name>
```
If you have set the namespace in you config, the namespace is optional

## scaling a replica set
```bash
kubectl scale --replicas=3 rs/foo
```
## Deployments
## For normal deployment:
The dash f "-f" is for "file"
```bash
kubectl apply -f my-yaml-deployment.yaml
```
## Dry run, i.e. check everything is ok
```bash
kubectl apply --dry-run=server my-yaml-deployment.yaml
```

## Delete unused contexts from the config file
```kubectl config``` unset takes a dot-delimited path. You can delete cluster/context/user entries by name. E.g.
```bash
kubectl config unset users.gke_project_zone_name

kubectl config unset contexts.aws_cluster1-kubernetes

kubectl config unset clusters.foobar-baz
```

## Delete configmaps
```bash
# kubectl delete configmap  <configmap-name>  -n  <namespace-name>
$ kubectl delete configmap    my-cofigmap     -n   namespacename
```
Or from yaml
```bash
# kubectl delete -f <file-directory> -n <namespace-name>
$ kubectl delete -f  configmap.yaml  -n  namespacename
```

## Get a shell in a running container
Note: The double dash (--) separates the arguments you want to pass to the command from the kubectl arguments.
```bash
kubectl exec --stdin --tty shell-demo -- /bin/bash
```

## The syntax of Kubectl cp
Kubectl CP is as simple as a typical Linux CP command. ```kubectl cp <source-file-path> <destination-file-path>```

Here is the syntax to copy a file from local to pod
```shell
kubectl cp /<path-to-your-file>/<file-name> <pod-name>:<fully-qualified-file-name> -c <container-name>
```
Here is the syntax to copy a file from the pod to local
```shell
kubectl cp <pod-name>:<fully-qualified-file-name> /<path-to-your-file>/<file-name> -c <container-name>
```  

## Getting logs

### Getting the logs of the previous pod for a deployment
```shell
kubectl logs nginx-7d8b49557c-c2lx9 --previous
```  

### Getting the logs of a specific container inside a pod
````shell
kubectl logs nginx-7d8b49557c-c2lx9 -c nginx
````

### Get the logs from a crashing container during container init causing crashloopbackoff
````shell
kubectl logs you_pod -c  container_name
````

## Some useful Links
[A page with most CLI commands](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)
[Kubernetes documentation home](https://kubernetes.io/docs/home/)
[Kubernetes secrets in genereal](https://kubernetes.io/docs/concepts/configuration/secret/)  

