#### minikube nginx ingress
###### command
    minikube start --vm-driver=virtualbox
    minikube addons enable ingress
    kubectl create deployment web --image=gcr.io/google-samples/hello-app:1.0
    kubectl create deployment web2 --image=gcr.io/google-samples/hello-app:2.0
    kubectl expose deployment web --type=NodePort --port=8080
    kubectl expose deployment web2 --port=8080 --type=NodePort
    kubectl apply -f example-ingress.yaml
    kubectl get ingress
#### sysout
    NAME              CLASS    HOSTS              ADDRESS        PORTS   AGE
    example-ingress   <none>   hello-world.info   172.17.0.15    80      38s
#### end
    sudo vi /etc/hosts
#### sysout
    ...
    172.17.0.15 hello-world.info
    ...
#### end
###### example-ingress.yaml
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: example-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  rules:
    - host: hello-world.info
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: web
                port:
                  number: 8080
          - path: /v2
            pathType: Prefix
            backend:
              service:
                name: web2
                port:
                  number: 8080
```
