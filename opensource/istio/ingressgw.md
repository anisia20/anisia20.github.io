###### ingress exam
```yaml
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
  name: api-gateway
  namespace: service
spec:
  selector:
    istio: ingressgateway # use istio default controller
  servers:
  - port:
      number: 80
      name: http
      protocol: HTTP
    hosts:
    - "*"

```
- 파드 추가 후 VirtualService 만 추가해 주면 된다.
```yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: api-virtual
  namespace: service
spec:
  hosts:
  - "domain.co.kr"
  gateways:
  - api-gateway
  http:
  - match:
    - uri:
        exact: /
    - uri:
        prefix: /
    route:
    - destination:
        host: service
        port:
          number: 8092 
```
