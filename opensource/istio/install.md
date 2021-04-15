# istio 1.5
    Istio-Pilot은 서비스 검색을 수행하고 Istio 서비스 메시에서 Envoy 사이드카 프록시를 구성합니다.
    Mixer 구성요소인 Istio-Policy와 Istio-Telemetry는 사용 정책을 적용하고 서비스 메시 간에 텔레메트리 데이터를 수집합니다.
    Istio-Ingressgateway는 클러스터 외부의 트래픽에 대한 Ingress 지점을 제공합니다.
    Istio-Citadel은 Istio를 위한 키 및 인증서 관리를 자동화합니다.

1. 기존 Istio 삭제
```sh
helm template install/kubernetes/helm/istio --name istio --namespace istio-system | kubectl delete -f -
kubectl delete namespace istio-system
for i in install/kubernetes/helm/istio-init/files/crd*yaml; do kubectl delete -f $i; done
```
2. 설치
```sh
for i in install/kubernetes/helm/istio-init/files/crd*yaml; do kubectl apply -f $i; done
kubectl apply -f namespace.yaml

helm template \
--name=istio \
--namespace istio-system \
--set tracing.enabled=true \
--set global.mtls.enabled=false \
--set grafana.enabled=true \
--set kiali.enabled=true \
--set servicegraph.enabled=true \
--set gateways.istio-ingressgateway.serviceAnnotations."service\.beta\.kubernetes\.io/aws-load-balancer-internal"="0\.0\.0\.0/0”
install/kubernetes/helm/istio \
> ./istioFex.yaml

kubectl apply -f istioFex.yaml
kubectl label namespace default istio-injection=enabled
kubectl get ns --show-labels
```