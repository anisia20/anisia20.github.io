### 파드 헬스 체크
    • livenessProbe: 컨테이너가 동작 중인지 여부를 나타낸다. 
    • readinessProbe: 컨테이너가 요청을 처리할 준비가 되었는지 여부를 나타낸다. 
    • startupProbe: 컨테이너 내의 애플리케이션이 시작되었는지를 나타낸다.
       • 발생 현상 
        ○ autoscale 2 → 40 증가시 워커 노드 증가, 어플리케이션 늘어나는 시간 지연 더 필요함
        ○ autoscale시 몇몇 파드 재기동 현상, 시간이 지연 되나 결국 정상화 됨
    • 조치 사항
        ○ livenessProbe initialDelaySeconds 180 으로 변경
            § alb 연결 체크는 60초 이후 수행
            § 비정상 체크는 180초 이후 수행
    • 참고 자료
        ○ IBM 가이드 참조 
            § https://cloud.ibm.com/docs/cloud-native?topic=cloud-native-healthcheck&locale=ko
            § 다시 시작 주기를 피하려면, livenessProbe.initialDelaySeconds 매개변수를 서비스가 초기화되는 데 걸리는 시간보다 반드시 더 길게 설정하십시오. 그런 다음 readinessProbe.initialDelaySeconds 속성에 대해 더 짧은 값을 사용하여 요청이 준비되는 즉시 서비스로 라우팅할 수 있습니다.
            § 예제 구성은 다음 예제와 유사할 수 있습니다(경로 및 포트 값 참조).


```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api-product-deployment
  namespace: service
  labels:
    app: api-product
spec:
  replicas: 1
  selector:
    matchLabels:
      app: api-product
  template:
    metadata:
      labels:
        app: api-product
    spec:
      serviceAccountName: api-product-ac
      containers:
      - name: api-product
        image: ...api_product:1523
        ports:
        - containerPort: 8093
        resources:
         limits:
           cpu: 1000m
         requests:
           cpu: 500m
        livenessProbe:
          httpGet:
            path: /product/v1/check
            port: 8093
          initialDelaySeconds: 180
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /product/v1/check
            port: 8093
          initialDelaySeconds: 60
          periodSeconds: 5
      nodeSelector:
        nodegroup-type: api1

```

 
