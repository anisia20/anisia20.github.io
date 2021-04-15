#### sample yaml
```yaml
apiVersion: v1
kind: Service
metadata:
  name: zipkin-service
  namespace: service
  labels:
    app: zipkin
spec:
  selector:
    app: zipkin
  ports:
    - port: 9411
      protocol: TCP
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: zipkin-ac
  namespace: service
  labels:
    app: zipkin
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: zipkin-deployment
  namespace: service
  labels:
    app: zipkin
spec:
  replicas: 1
  selector:
    matchLabels:
      app: zipkin
  template:
    metadata:
      labels:
        app: zipkin
    spec:
      serviceAccountName: zipkin-ac
      containers:
      - name: chicor-zipkin
        image: openzipkin/zipkin-slim:latest
        #command: ["/bin/bash","-c"]
        #args: ["start.sh"]
        ports:
        - containerPort: 9411
      nodeSelector:
        nodegroup-type: istioDataFront
```