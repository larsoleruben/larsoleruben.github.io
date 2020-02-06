# Kubectl!
### Make a secret for your private repository.
This will enable you to use an image in in i.e. Azure, AWS or Google in any other K8 cluster:

```bash
kubectl create secret docker-registry my-registry  --docker-email="your@email" --docker-username="your registry username" --docker-server="your registry server address" --docker-password="your registry password"
```
### Switch to another context, which is in your ~/.kube/config file
You can find the names in the ~/.kube/config file. 
```bash
kubectl config use-context my-cluster-name
```
### Create a new secret fropm a file (json or yaml or what not)
```bash
kubectl create secret generic secret-name --from-file=secrets.json
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
