#### argocd 설치
    curl -v https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml > install.yaml
    kubectl apply -f install.yaml
    kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "NodePort"}}'
    kubectl apply -f ingress.yaml #하단 참조

#### argocd cmd 설치
    VERSION=$(curl --silent "https://api.github.com/repos/argoproj/argo-cd/releases/latest" | grep '"tag_name"' | sed -E 's/.*"([^"]+)".*/\1/')
    curl -sSL -o /usr/local/bin/argocd https://github.com/argoproj/argo-cd/releases/download/$VERSION/argocd-linux-amd64
    chmod +x /usr/local/bin/argocd

#### 설정
    ARGOCD_SERVER=`kubectl get pods -n argocd -l app.kubernetes.io/name=argocd-server -o name | cut -d'/' -f 2` 

    route53 -> alb 세팅
    argocd login --grpc-web argocd.co.kr
    name : admin
    pass ARGOCD_SERVER 내용

#### 패스워드 변경
    argocd account update-password

#### 클러스터 추가
    kubectl config get-contexts -o name #확인
    argocd cluster add arn:aws:eks:ap-northeast-2:.../main-eks-new --grpc-web --server argocd.co.kr

    #네임스페이스 변경시 인증을위한 작업을 해주어야함
    kubectl create clusterrolebinding default-admin --clusterrole=admin --serviceaccount=prd-cicd:default
    kubectl edit ClusterRoleBinding argocd-application-controller 
    kubectl edit ClusterRoleBinding argocd-server 
    가장 하단 namespace argocd -> prd-cicd로 변경

#### git 추가
    argocd repo add http://bitbucket.co.kr:7990/scm/chic/co_docker.git --grpc-web --username ... --password ... 

#### 참고 자료 --서비스 443으로 안하면 무한 리다이렉트
```yaml
ingress.yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: argocd-ingress-internal
  namespace: argocd
  annotations:
    kubernetes.io/ingress.class: alb
    alb.ingress.kubernetes.io/scheme: internal
    alb.ingress.kubernetes.io/backend-protocol: HTTPS
    alb.ingress.kubernetes.io/healthcheck-path: /login
    alb.ingress.kubernetes.io/subnets: subnet
    alb.ingress.kubernetes.io/certificate-arn: arn:aws:acm
    alb.ingress.kubernetes.io/listen-ports: '[{"HTTPS": 443}, {"HTTP": 80}]'
    alb.ingress.kubernetes.io/actions.ssl-redirect: '{"Type": "redirect", "RedirectConfig": { "Protocol": "HTTPS", "Port": "443", "StatusCode": "HTTP_301"}}'
    #ingress.kubernetes.io/force-ssl-redirect: "true"
  labels:
    app: argocd-ingress
spec:
  rules:
    - http:
        paths:
          - backend:
              serviceName: ssl-redirect
              servicePort: use-annotation
    - host: argocd.co.kr
      http:
        paths:
          - backend:
              serviceName: argocd-server
              servicePort: 443
```