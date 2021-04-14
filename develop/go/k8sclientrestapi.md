
gin framework 사용

https://github.com/anisia20/k8s-client.git - Connect to preview 

getpods local TEST

http://localhost:3000/api/getpods

```command


go mod init getpods
go get -u
go get k8s.io/client-go@v0.19.0
go run cmd/main.go
결과


 ```