- ingress service 를 수정
- 수정 항목 : service.beta.kubernetes.io/aws-load-balancer-ssl-cert
- 예제
```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/aws-load-balancer-backend-protocol: http
    service.beta.kubernetes.io/aws-load-balancer-internal: 0.0.0.0/0
    service.beta.kubernetes.io/aws-load-balancer-ssl-cert: [ssl arn 등록]
....
```