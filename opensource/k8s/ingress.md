AWS Router53 ALB SSL
1. 인증서 확인
	• AWS 인증 검색
	• 도메인 인증서 arn 확인
2. ALB 세팅
	• arn 세팅, subnet 세팅
	• command
kubectl apply -f test-web-ingress-internal.yaml
kubectl describe Ingress test-web-ingress-internal -n chicor-service
	• yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: test-web-ingress-internal
  namespace: service
  annotations:
    kubernetes.io/ingress.class: alb
    alb.ingress.kubernetes.io/scheme: internal
    alb.ingress.kubernetes.io/subnets: subnet
    alb.ingress.kubernetes.io/certificate-arn: arn:aws:acm:ap-northeast-2:
    alb.ingress.kubernetes.io/listen-ports: '[{"HTTPS": 443}, {"HTTP": 80}]'
  labels:
    app: web
spec:
  rules:
    - http:
        paths:
          - path: /*
            backend:
              serviceName: web-service
              servicePort: 8084
3. Router53 세팅
	• Application/Classic Load Balancer에 대한 별칭
	• 대상은 자동 검색 목록중 선택
