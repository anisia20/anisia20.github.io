- 현재 pod 상태 조회
    - kubectl get pods
- 노드에 라벨 추가 
    - kubectl label nodes worker1 nodeType=worker1
    - deployment 에 아래와 같이 추가 가능
    > nodeSelector:
    >   nodeType: worker1
* 특정 pod delete
    - kubectl delete pods rdeploy-947c958b5-95p7f 
 
* pod 중지 (replicas = 0)
    - kubectl scale --replicas=0 deployment/deploy
* pod 복원 (replicas = 2)
    - kubectl scale --replicas=2 deployment/deploy
- hpa
    * kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml # 메트릭 설치
    * # cpu 설정 필수로 필요
    ```yaml
    containers:
        - name: test
          image: test
          resources:
            limits:
              cpu: "1"
            requests:
              cpu: "0.5"
          ports:
            - containerPort: 3000
    ```
    * kubectl autoscale deployment deployment --cpu-percent=60 --min=1 --max=2 
    * kubectl delete horizontalpodautoscaler deployment
    * kubectl get hpa 

    * kubectl autoscale deployment deployment --cpu-percent=60 --min=1 --max=2 
    * kubectl delete horizontalpodautoscaler deployment 
* container 접근
    - kubectl -n kube-system exec -it <pod-in-kube-system> sh
* log
    - kubectl logs -f pod/api-deployment --all-containers 
