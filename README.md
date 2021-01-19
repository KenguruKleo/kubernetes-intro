# kubernetes-intro

### Install minikube

https://minikube.sigs.k8s.io/docs/start/

```
brew install minikube
minikube start --vm=true
minikube addons enable ingress
minikube addons enable metrics-server
minikube addons list
minikube dashboard
```

If you already have kubectl installed, you can now use it to access your shiny new cluster:

`kubectl get pods --all-namespaces`

Alternatively, minikube can download the appropriate version of kubectl, if you don’t mind the double-dashes in the command-line:

`minikube kubectl -- get po -A`

### Install and Set Up kubectl

https://kubernetes.io/docs/tasks/tools/install-kubectl/

```
brew install kubectl
kubectl version --client
```

### Install Lens (optional)

https://k8slens.dev/

## Ready to Play



```
kubectl get pods -n kube-system
```
