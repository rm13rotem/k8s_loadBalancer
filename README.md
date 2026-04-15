# k8s_loadBalancer
balance between deployments
two deployments and a service load balancer between them
to check we need to run 

```
 for i in {1..100} ; do curl http://<YOUR_IP>/ |grep img ; done |sort |uniq -c
```

Chat AI response : 
YAMLs:
blue-deploy.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: blue-deployment
  labels:
    app: blue
    container: green-blue
spec:
  replicas: 2
  selector:
    matchLabels:
      app: blue
      container: green-blue
  template:
    metadata:
      labels:
        app: blue
        container: green-blue
    spec:
      containers:
      - name: blue-container
        image: supergiantkir/animals:hare
        ports:
        - containerPort: 80

green-deploy.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: green-deployment
  labels:
    app: green
    container: green-blue
spec:
  replicas: 1
  selector:
    matchLabels:
      app: green
      container: green-blue
  template:
    metadata:
      labels:
        app: green
        container: green-blue
    spec:
      containers:
      - name: green-container
        image: supergiantkir/animals:bear
        ports:
        - containerPort: 80

svc.yaml
apiVersion: v1
kind: Service
metadata:
  name: svc-app
  labels:
    container: green-blue
spec:
  selector:
    container: green-blue
  ports:

name: port80 protocol: TCP port: 80 targetPort: 80 type: LoadBalancer
Apply:
```
kubectl apply -f blue-deploy.yaml
kubectl apply -f green-deploy.yaml
kubectl apply -f svc.yaml
```
Verify:
```
kubectl get deployments
kubectl get pods -l container=green-blue
kubectl get svc svc-app
kubectl describe svc svc-app
```
Notes:

Ensure the selector labels in each Deployment template match the spec.selector.matchLabels exactly.
If you prefer to keep the existing deployment name, you must delete it first before creating a deployment with a different selector/labels.
