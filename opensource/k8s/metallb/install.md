1. MetalLB를 위한 네임스페이스를 생성합니다.
(kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.9.3/manifests/namespace.yaml)
 
2. MetalLB Components를 생성합니다.
(kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.9.3/manifests/metallb.yaml)
 
3. Secret을 생성합니다.
(kubectl create secret generic -n metallb-system memberlist --from-literal=secretkey="$(openssl rand -base64 128)")
 
4. 라우팅 처리를 위한 ConfigMap을 생성합니다.
본인의 호스트아이피 - +10으로
 
apiVersion: v1
kind: ConfigMap
metadata:
  namespace: metallb-system
  name: config
data:
  config: |
    address-pools:
    - name: default
      protocol: layer2
      addresses:
      - 192.168.72.102-192.168.72.103
 
5. 기존과 마찬가지로 nginx를 Deploy한 다음 LoadBalancer 타입으로 노출시킨 결과 이전과는 다르게 nginx Service 오브젝트의 External IP가 할당된 것을 확인할 수 있습니다.
 