개발툴 : vscode
```command

go env | grep GOPATH
GOPATH="/Users/mz01-josm/go"
mkdir -p $GOPATH/src/k8s.io
cd $GOPATH/src/k8s.io
git clone https://github.com/kubernetes/kubernetes
cd kubernetes
```
make # makefile 위치
vscode

add workspace : /Users/mz01-josm/go

구조

cmd : 명령어 소스 위치 ( 각 명령어 트랙킹 가능 )

vendor : 각 라이브러리 존재 위치

빌드 방법

ex ) make kubectl