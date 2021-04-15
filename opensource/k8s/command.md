- 현재 pod 상태 조회
    - kubectl get pods

* 특정 pod delete
    - kubectl delete pods rdeploy-947c958b5-95p7f 
 
* pod 중지 (replicas = 0)
    - kubectl scale --replicas=0 deployment/deploy
* pod 복원 (replicas = 2)
    - kubectl scale --replicas=2 deployment/deploy
- hpa
    * kubectl autoscale deployment deployment --cpu-percent=60 --min=1 --max=2 
    * kubectl delete horizontalpodautoscaler deployment
    * kubectl get hpa 

    * kubectl autoscale deployment deployment --cpu-percent=60 --min=1 --max=2 
    * kubectl delete horizontalpodautoscaler deployment 