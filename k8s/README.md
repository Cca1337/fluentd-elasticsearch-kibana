## We need a Kubernetes cluster

Lets create a Kubernetes cluster to play with using [minikube](https://minikube.sigs.k8s.io/docs/start/)

```
minikube start
```

## Fluentd Docker

Use manifests from the official fluentd [github repo](https://github.com/fluent/fluentd-kubernetes-daemonset)<br/>

You may want to build your own image if you want to install plugins like `fluentd` elasticsearch plugin <br/>

Let's build our docker image:
*  To build and run Docker images inside the minikube environment


```
eval $(minikube docker-env)

```
* build an image

```
#note: use your own tag!
docker build . -t cca1337/fluentd-demo

```

## Fluentd Namespace

Let's create a `fluentd` namespace: <br/>

```
kubectl create ns fluentd

```
## Fluentd Configmapuentd 

We have 4 files in our `fluentd-configmap.yaml` :
* fluent.conf: Our main config which includes all other configurations

* pods-fluent.conf: `tail` config that sources all pod logs on the `kubernetes` host in the cloud. <br/>
  Note: When running K8s in the cloud, logs may go into JSON format.
* file-fluent.conf: `match` config to capture all logs and write it to file for testing log collection </br>
  Note: This is great to test if collection of logs works
* elastic-fluent.conf: `match` config that captures all logs and sends it to `elasticseach`

Let's deploy our `configmap`:

```
kubectl apply -f ./fluentd-configmap.yaml

```

## Fluentd Daemonset

Let's deploy our `daemonset`:

```
kubectl apply -f ./fluentd-rbac.yaml 
kubectl apply -f ./fluentd.yaml
kubectl -n fluentd get pods

```

Let's deploy our example app that writes logs to `stdout`

```
kubectl apply -f ./counter.yaml
kubectl get pods

```

## ElasticSearch and Kibana

```
kubectl create ns elastic-kibana

# deploy elastic search
kubectl -n elastic-kibana apply -f ./elastic/elastic-demo.yaml
kubectl -n elastic-kibana get pods

# deploy kibana
kubectl -n elastic-kibana apply -f ./elastic/kibana-demo.yaml
kubectl -n elastic-kibana get pods
```

## Kibana

```
kubectl -n elastic-kibana port-forward svc/kibana 5601
```