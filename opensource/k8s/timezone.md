### Sample
```yaml
apiVersion: v1
kind: Deployment
...
spec:
  containers:
    ...
    volumeMounts:
    - name: tz-config
      mountPath: /etc/localtime
  volumes:
    - name: tz-config
      hostPath:
        path: /usr/share/zoneinfo/Asia/Seoul
```
