# Day 11/40 - Multi Container Pod Kubernetes - Sidecar vs Init Container

## Check out the video below for Day11 👇

[![Day11/40 - Multi Container Pod Kubernetes - Sidecar vs Init Container](https://img.youtube.com/vi/yRiFq1ykBxc/sddefault.jpg)](https://youtu.be/yRiFq1ykBxc)


## Sample YAML used in the demo

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app.kubernetes.io/name: MyApp
spec:
  containers:
  - name: myapp-container
    image: busybox:1.28
    env:
    - name: FIRSTNAME
      value: "Piyush"
    command: ['sh', '-c', 'echo The app is running! && sleep 3600']
  initContainers:
  - name: init-myservice
    image: busybox:1.28
    command: ['sh', '-c'] #command to run
    args: ['until nslookup myservice.default.svc.cluster.local; do echo waiting for myservice; sleep 2; done']
  - name: init-mydb
    image: busybox:1.28
    command: ['sh', '-c']
    args: ['until nslookup mydb.default.svc.cluster.local; do echo waiting for mydb; sleep 2; done']
```


```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app.kubernetes.io/name: MyApp
spec:
  containers:
  - name: myapp-container
    image: busybox:1.28
    env:
    command: ['sh', '-c', 'echo The app is running! && sleep 3600']
  initContainers:
  - name: init-myservice
    image: busybox:1.28
    command: ['sh', '-c'] # command to run
    args: # arguments to the command
      - > # multi-line string
        until nslookup myservice.default.svc.cluster.local; do
         echo waiting for myservice;
         sleep 2;
        done;
```

- k expose deploy demo --name myservice --port 80 -n demo
- kubectl expose deploy nginx-deploy --name myservice --port=80
Creates a Service
The kubectl expose command creates a new Service object in Kubernetes

In your case, the Service will be named myservice because of --name myservice.

Links the Service to the Deployment

The Service is automatically associated with the Deployment nginx-deploy.

It does this by using the labels on the Pods created by that Deployment.

The Service will select those Pods and route traffic to them.

Exposes the Deployment on Port 80

The Service listens on port 80 (--port=80).

By default, it forwards traffic to the same port on the Pods (targetPort=80 unless you specify otherwise).

This means any request to the Service on port 80 will be sent to the Pods of nginx-deploy.

- kubectl -n demo exec myapp-pod -it -- printenv
- kubectl -n demo exec myapp-pod -it -- sh
- If your pod has a main container named, say, app-container, you’d run:

kubectl -n demo exec myapp-pod -c app-container -it -- sh
✅ Bottom line: You can only exec into running containers. Init containers finish and exit, so you can’t exec into them after startup. Use the actual running container name from the pod spec instead.

- k get pods -w  => watch it with current status. 
