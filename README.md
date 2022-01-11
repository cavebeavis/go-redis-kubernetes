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

**`helm`**

This is *optional* to pull certain k8s manifests without copying the yaml file.

Install instructions: https://helm.sh/docs/intro/install/

<br></br>

## Deployment

```bash
$ kubectl config use-context {your-context-name -or- minikube (if installed)}


$ docker build -t go-redis-kubernetes .
$ docker tag go-redis-kubernetes {your dockerhub handle if you want to upload the image}/go-redis-app:1.0.0
$ docker login
$ docker push {your dockerhub handle if you want to upload the image}/go-redis-app:1.0.0

$ kubectl apply -f deployments/go-redis-app.yml
$ kubectl get pods -A
# Will display something like:
# NAMESPACE     NAME                               READY   STATUS    RESTARTS      AGE
# default       go-redis-app-5998f585-6r5jh        1/1     Running   2 (16m ago)   16m
# default       go-redis-app-5998f585-jfmzc        1/1     Running   1 (16m ago)   16m
# default       go-redis-app-5998f585-q4c82        1/1     Running   1 (16m ago)   16m
# default       redis-master-86b6654444-ckxvr      1/1     Running   0             16m
# kube-system   coredns-78fcd69978-slk89           1/1     Running   0             22h
# kube-system   etcd-minikube                      1/1     Running   0             22h
# kube-system   kube-apiserver-minikube            1/1     Running   0             22h
# kube-system   kube-controller-manager-minikube   1/1     Running   0             22h
# kube-system   kube-proxy-f29cn                   1/1     Running   0             22h
# kube-system   kube-scheduler-minikube            1/1     Running   0             22h
# kube-system   metrics-server-6467f65c76-g92ck    0/1     Running   0             16h
# kube-system   storage-provisioner                1/1     Running   0             22h

$ minikube service go-redis-app-service --url
# Will display something like:
# http://192.168.49.2:30641
#
# paste that into browser ^^

# Since there are 3 replicas, you will need to specify which pod and container like: 
$ kubectl exec go-redis-app-5998f585-6r5jh -c go-redis-app -i -t -- sh -il
# Will display something like:
# go-redis-app-5998f585-6r5jh:~# ls
# main

# Since there is only 1 up and running, then do not specify the container like:
$ kubectl exec redis-master-86b6654444-ckxvr -i -t -- sh -il
# Will display something like:
# # redis-cli
# 127.0.0.1:6379> KEYS *
# 1) "2022-01-11"
# 127.0.0.1:6379> GET "2022-01-11"
# "The free soul is rare, but you know it when you see it - basically because you feel good, very good, when you are near or with them."
# 127.0.0.1:6379> exit
# # exit


$ kubectl delete -f deployments/go-redis-app.yml
# service "redis-master" deleted
# deployment.apps "redis-master" deleted
# service "go-redis-app-service" deleted
# deployment.apps "go-redis-app" deleted

```
