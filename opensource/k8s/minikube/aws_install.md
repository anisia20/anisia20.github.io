### aws ec2 minikube
    Docker 는 설치 되어 있어야 함
    minikube setup
```sh
sudo su -l
curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 && chmod +x minikube && sudo mv minikube /usr/local/bin/
minikube version
./minikube start --vm-driver=none 
yum install -y conntrack #에러시 구동 후 다시 업
```

    ○ flanner 이용 방법
    ○ minikube start --cpus 4 --memory 8096 --disk-size=20g --addons=ingress --vm=true --network-plugin=cni --enable-default-cni --extra-config=kubeadm.pod-network-cidr=10.244.0.0/16

    ○ flanner.yaml
    linux kubemaste 설치 자료 참조 nic 명칭 bridge
