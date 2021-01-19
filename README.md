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

https://kubernetes.io/ru/docs/reference/kubectl/cheatsheet/

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

Access to container:
```
kubectl exec -ti $POD_NAME bash
cat server.js
curl localhost:8080
exit
```

### Work with YAML

See applied config: `kubectl get pod $POD_NAME -o yaml`

Apply from the file:
```
kubectl delete deployment
kubectl get pods
kubectl apply -f deployment.yaml
kubectl get pods
```

### Services

Expose deployment:
```
kubectl get services
kubectl expose deployment/kubernetes-bootcamp --type="NodePort" --port 8080
kubectl get services

export NODE_PORT=$(kubectl get services/kubernetes-bootcamp -o go-template='{{(index .spec.ports 0).nodePort}}')
echo NODE_PORT=$NODE_PORT

curl $(minikube ip):$NODE_PORT
```

### Scaling

```
kubectl get deployments
kubectl get rs
kubectl scale deployments/kubernetes-bootcamp --replicas=4
kubectl get deployments
kubectl get pods -o wide

curl $(minikube ip):$NODE_PORT

kubectl scale deployments/kubernetes-bootcamp --replicas=2
kubectl get pods -o wide
```

### Updating App

```
kubectl set image deployments/kubernetes-bootcamp kubernetes-bootcamp=jocatalin/kubernetes-bootcamp:v2
kubectl get pods -o wide

curl $(minikube ip):$NODE_PORT
```

#### Set broken image:

```
kubectl set image deployments/kubernetes-bootcamp kubernetes-bootcamp=gcr.io/google-samples/kubernetes-bootcamp:v10
kubectl get deployments
kubectl get pods -o wide

curl $(minikube ip):$NODE_PORT

kubectl describe pods
```

#### Rollout

```
kubectl rollout undo deployments/kubernetes-bootcamp
```


### Ingress

https://kubernetes.io/docs/tasks/access-application-cluster/ingress-minikube/

Get minikube service URL: `minikube service kubernetes-bootcamp --url`

Deploy Ingress
```
kubectl apply -f ingress.yaml
kubectl describe ingress
```

Find host ADDRESS `kubectl get ingress` and add it into the host file: `sudo nano /etc/hosts`

e.g. add this line, instead 172.17.0.15 use ADDRESS:
```
172.17.0.15 hello-world.info
```

#### Verify that the Ingress controller is directing traffic:

```
curl hello-world.info
```

### Ingress with 2 services / deployments

#### Create Second Deployment

Create a v2 Deployment using the following command:

`kubectl create deployment web2 --image=gcr.io/google-samples/hello-app:2.0`

Expose the Deployment:

`kubectl expose deployment web2 --port=8080 --type=NodePort`

#### Edit Ingress

Edit the existing ingress.yaml and add the following lines:

      - path: /v2
        pathType: Prefix
        backend:
          service:
            name: web2
            port:
              number: 8080
Apply the changes:

```
kubectl apply -f ingress.yaml
```

#### Test Your Ingress
Access the 1st version of the Hello World app.

```
curl hello-world.info
```

Access the 2nd version of the Hello World app.

```
curl hello-world.info/v2
```

