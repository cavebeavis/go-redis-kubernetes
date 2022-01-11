# Kubernetes Stuff

This is based upon the work here: https://www.callicoder.com/deploy-multi-container-go-redis-app-kubernetes/

&

https://github.com/callicoder/go-redis-kubernetes

## Install

For interacting with the Azure Kubernetes Service ('AKS'), you will need `kubectl` installed locally and then you will need your specific kubernetes ('k8s') config file with your cluster secrets and context. To locally test the k8s deployments and services, you will need a k8s cluster like `minikube` and possibly additional tools like `krew`.

<br></br>

**`kubectl`**

This is *required*.

Install instructions for Linux <span style="font-size:2em;">:sunglasses:</span>: https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/

Install instructions for mac/OS <span style="font-size:2em;">:weary:</span>: https://kubernetes.io/docs/tasks/tools/install-kubectl-macos/

Install instructions for Windows <span style="font-size:2em;">:sob:</span>: https://kubernetes.io/docs/tasks/tools/install-kubectl-windows/

<br></br>

To install your AKS context, follow these directions: https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/ 

<br></br>

**`krew`**

This is *optional* but will allow you to download plugins like `whoami` which does kewl stuff like:

```
$ kubectl whoami
your-context-name
```

**NOTE:** *be careful which plugins you use as they are community open-sourced and have not been specifically checked out from a security standpoint.*

Install instructions: https://krew.sigs.k8s.io/docs/user-guide/setup/install/

To install `whoami`:

```
$ kubectl krew install whoami
```


<br></br>

**`minikube`**

This is *required* for local testing.

Install and startup instructions: https://minikube.sigs.k8s.io/docs/start/ 


<br></br>

**`kompose`**

This is *optional* to create k8s service and deployment yaml files from `docker-compose` files.

Install instructions: https://kompose.io/installation/ 

**NOTE:** *You will need to set the `docker-compose` files version from `version: '3.9'` to `version: '3'` and also any `gateway:` lines will need to be commented out in the `networks:` section.*

<br></br>

## Deployment

```
$ docker build -t go-redis-kubernetes .
$ docker tag go-redis-kubernetes cavebeavis/go-redis-app:1.0.0
$ docker login
$ docker push cavebeavis/go-redis-app:1.0.0

$ kubectl apply -f deployments/go-redis-app.yml
$ kubectl get pods -A

$ kubectl delete -f deployments/go-redis-app.yml

```
