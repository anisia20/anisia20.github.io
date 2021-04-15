### HAPROXY ingress
###### command
    kubectl apply -f haproxy-ingress.yaml
    kubectl get svc --namespace=haproxy-controller
    kubectl apply -f 1.ingress.yaml
    kubectl apply -f 2.configmap.yaml
    curl -I -H 'Host: foo.bar' 'http://10.222.16.46:30627'
    HTTP/1.1 200 OK
    content-type: text/plain
    date: Thu, 22 Oct 2020 02:50:15 GMT
    content-length: 135

###### haproxy-ingress.yaml
```yaml
---
apiVersion: v1
kind: Namespace
metadata:
  name: haproxy-controller
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: haproxy-ingress-service-account
  namespace: haproxy-controller
---
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: haproxy-ingress-cluster-role
rules:
- apiGroups:
  - ""
  resources:
  - configmaps
  - endpoints
  - nodes
  - pods
  - services
  - namespaces
  - events
  - serviceaccounts
  verbs:
  - get
  - list
  - watch
- apiGroups:
  - "extensions"
  resources:
  - ingresses
  - ingresses/status
  verbs:
  - get
  - list
  - watch
  - update
- apiGroups:
  - ""
  resources:
  - secrets
  verbs:
  - get
  - list
  - watch
  - create
  - patch
  - update
---
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: haproxy-ingress-cluster-role-binding
  namespace: haproxy-controller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: haproxy-ingress-cluster-role
subjects:
- kind: ServiceAccount
  name: haproxy-ingress-service-account
  namespace: haproxy-controller
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: haproxy
  namespace: haproxy-controller
data:
---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    run: ingress-default-backend
  name: ingress-default-backend
  namespace: haproxy-controller
spec:
  replicas: 1
  selector:
    matchLabels:
      run: ingress-default-backend
  template:
    metadata:
      labels:
        run: ingress-default-backend
    spec:
      containers:
      - name: ingress-default-backend
        image: gcr.io/google_containers/defaultbackend:1.0
        ports:
        - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  labels:
    run: ingress-default-backend
  name: ingress-default-backend
  namespace: haproxy-controller
spec:
  selector:
    run: ingress-default-backend
  ports:
  - name: port-1
    port: 8080
    protocol: TCP
    targetPort: 8080
---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    run: haproxy-ingress
  name: haproxy-ingress
  namespace: haproxy-controller
spec:
  replicas: 1
  selector:
    matchLabels:
      run: haproxy-ingress
  template:
    metadata:
      labels:
        run: haproxy-ingress
    spec:
      serviceAccountName: haproxy-ingress-service-account
      containers:
      - name: haproxy-ingress
        image: haproxytech/kubernetes-ingress
        args:
          - --configmap=haproxy-controller/haproxy
          - --default-backend-service=haproxy-controller/ingress-default-backend
        resources:
          requests:
            cpu: "500m"
            memory: "50Mi"
        livenessProbe:
          httpGet:
            path: /healthz
            port: 1042
        ports:
        - name: http
          containerPort: 80
        - name: https
          containerPort: 443
        - name: stat
          containerPort: 1024
        env:
        - name: TZ
          value: "Etc/UTC"
        - name: POD_NAME
          valueFrom:
            fieldRef:
              fieldPath: metadata.name
        - name: POD_NAMESPACE
          valueFrom:
            fieldRef:
              fieldPath: metadata.namespace
---
apiVersion: v1
kind: Service
metadata:
  labels:
    run: haproxy-ingress
  name: haproxy-ingress
  namespace: haproxy-controller
spec:
  selector:
    run: haproxy-ingress
  type: NodePort
  ports:
  - name: http
    port: 80
    protocol: TCP
    targetPort: 80
  - name: https
    port: 443
    protocol: TCP
    targetPort: 443
  - name: stat
    port: 1024
    protocol: TCP
    targetPort: 1024
	• 1.ingress.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    run: app
  name: app
spec:
  replicas: 2
  selector:
    matchLabels:
      run: app
  template:
    metadata:
      labels:
        run: app
    spec:
      containers:
      - name: app
        image: jmalloc/echo-server
        ports:
        - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  labels:
    run: app
  name: app
  annotations:
    haproxy.org/check: "enabled"
    haproxy.org/forwarded-for: "enabled"
    haproxy.org/load-balance: "roundrobin"
spec:
  selector:
    run: app
  ports:
  - name: port-1
    port: 80
    protocol: TCP
    targetPort: 8080
---
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: web-ingress
  namespace: default
spec:
  rules:
  - host: foo.bar
    http:
      paths:
      - path: /
        backend:
          serviceName: app
          servicePort: 80
	• 2.configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: haproxy-configmap
  namespace: default
data:
  servers-increment: "42"
  ssl-redirect: "OFF"
```