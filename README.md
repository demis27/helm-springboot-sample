# How to run this basic image on kubernetes/helm

## Create and push the image
1. Build the docker image

```
mvn clean install dockerfile:build
```

2. Tag the docker image for the Talend registry

```
docker tag talend/docker-spring-boot:<version> <registry>/talend/docker-spring-boot:<version>
```

Example :

```
docker tag talend/docker-spring-boot:1.0.0-SNAPSHOT registry.datapwn.com/talend/docker-spring-boot:1.0.0-SNAPSHOT
```

3. Push the image to the Talend registry

```
docker login <registry>
# enter your credentials
docker push <registry>/talend/docker-spring-boot:<version>
```

Example :

```
docker login registry.datapwn.com
# enter your credentials
docker push registry.datapwn.com/talend/docker-spring-boot:1.0.0-SNAPSHOT
```

## Use Kubernetes/helm

1. Checkout the Helm project

```
git checkout https://github.com/Talend/helm-charts.git
cd helm-charts
git checkout skermabon/mbo
```

2. Install Kubernetes, minikube, helm

* Kubernetes -> https://kubernetes.io/docs/tasks/tools/install-kubectl/
* Minikube -> https://kubernetes.io/docs/tasks/tools/install-minikube/
* Helm -> https://github.com/kubernetes/helm

3. Start minikube

```
minikube start
```

4. Create your secret

```
kubectl create secret docker-registry talend-docker-registry --docker-server=registry.datapwn.com --docker-username=<you user name> --docker-password="<your password>" --docker-email=<your email>
```

5. Install the chart

```
helm install example-without-dependency --name example
```

6. Verify all is ok

```
kubectl get deployment

NAME             DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
example-simple   1         1         1            0           2s

kubectl get services

NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
example-talend   ClusterIP   10.109.8.221    <none>        8080/TCP         1m
hello-minikube   NodePort    10.104.40.245   <none>        8080:30107/TCP   76d
kubernetes       ClusterIP   10.96.0.1       <none>        443/TCP          76d

kubectl get pods

NAME                              READY     STATUS    RESTARTS   AGE
example-simple-66cf4bf97f-w8ll6   1/1       Running   0          1m
```

## Test your application

1. Launch a proxy to your application

In another terminal

```
kubectl proxy
```

2. Get the name of your pod

```
kubectl get pods

NAME                              READY     STATUS    RESTARTS   AGE
example-simple-66cf4bf97f-w8ll6   1/1       Running   0          7m
```

The pod name is 'example-simple-66cf4bf97f-w8ll6'.

3. Call your service

```
curl http://127.0.0.1:8001/api/v1/namespaces/default/pods/<pod-name>/proxy/

Hello Docker World
```

## Remove your chart

```
helm delete --purge example
```

