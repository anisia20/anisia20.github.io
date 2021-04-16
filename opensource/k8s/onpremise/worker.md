kubeadm join 192.168.56.102:6443 --token uz2e14.y2087yqhc7890cj4     --discovery-token-ca-cert-hash sha256:...

#토큰
$ kubeadm token list
#hash
$ openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | openssl rsa -pubin -outform der 2>/dev/null | openssl dgst -sha256 -hex | sed 's/^.* //'
#토큰 24시간 만료
$ kubeadm token create

kubeadm reset : 초기화