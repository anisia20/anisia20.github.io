#### 서비스 배포
    secret : registry 참조

```yaml
apiVersion: v1
kind: Service
metadata:
  name: api-sample-service
  labels:
    app: api-sample
spec:
  selector:
    app: api-sample
  ports:
    - port: 38382
      protocol: TCP
  type: LoadBalancer
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: api-sample-ac
  labels:
    app: api-sample
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api-sample-deployment
  labels:
    app: api-sample
spec:
  replicas: 1
  selector:
    matchLabels:
      app: api-sample
  template:
    metadata:
      labels:
        app: api-sample
    spec:
      serviceAccountName: api-sample-ac
      containers:
      - name: api-sample
        image: 192.168.59.102:35000/api-sample:latest
        ports:
        - containerPort: 38382
        resources:
          limits:
            cpu: 500m
          requests:
            cpu: 500m
      imagePullSecrets:
      - name: regcred
      nodeSelector:
        nodeType: worker1
```