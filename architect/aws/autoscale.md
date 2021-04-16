## Cluster Autoscaler Overview
    Cluster Autoscaler 는 다음 이상의 2가지 조건 중 하나 이상 충족될 때 클러스터의 크기를 자동으로 조절하는 도구이다.
        1. 리소스 부족으로 인해 클러스터에서 실행되지 않은 Pod 가 있을 때 ex) Pending 상태 일때)
        2. 클러스터에 장기간 사용되지 않은 노드가 있고 그러한 노드들의 포드를 기존의 다른 노드에 배치할 수 있을 때
    AWS 에서의 Cluster Autoscaler 는 지정된 Auto Scaling Group 내의 Worker node 를 조정하여 클러스터에서 Deployment 로 실행된다.

```sh
kubectl apply -f https://raw.githubusercontent.com/kubernetes/autoscaler/master/cluster-autoscaler/cloudprovider/aws/examples/cluster-autoscaler-autodiscover.yaml

kubectl annotate serviceaccount cluster-autoscaler \
  -n kube-system \
  eks.amazonaws.com/role-arn=arn:aws:iam::<AWS_ACCOUNT_ID>:role/<AmazonEKSClusterAutoscalerRole>
### find aws console nodegroup arn

kubectl patch deployment cluster-autoscaler \ -n kube-system \ -p '{"spec":{"template":{"metadata":{"annotations":{"cluster-autoscaler.kubernetes.io/safe-to-evict": "false"}}}}}'

kubectl -n kube-system edit deployment.apps/cluster-autoscaler

Edit the cluster-autoscaler container command to replace <YOUR CLUSTER NAME> (including <>) with your cluster's name, and add the following options.
```