### secret 설정
kubectl create secret generic regcred \
    --from-file=.dockerconfigjson=/home/josm/.docker/config.json \
    --type=kubernetes.io/dockerconfigjson
