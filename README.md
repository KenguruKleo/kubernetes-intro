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

You can use this guide:
https://kubernetes.io/docs/tutorials/kubernetes-basics/


### Check cluster
```
kubectl get nodes
kubectl get pods -n kube-system
```

### Deploy App
```
kubectl create deployment kubernetes-bootcamp --image=gcr.io/google-samples/kubernetes-bootcamp:v1
kubectl get deployments
kubectl get pods
kubectl describe pods
```

Run proxy to expose App `kubectl proxy`
and check proxy is running `curl http://localhost:8001/version`

Save POD_NAME:
```
export POD_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')
echo Name of the Pod: $POD_NAME
```

Send request:

`curl http://localhost:8001/api/v1/namespaces/default/pods/$POD_NAME:8080/proxy/`


See logs: `kubectl logs $POD_NAME`


