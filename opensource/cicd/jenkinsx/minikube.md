### minikube	
        • gitlab 설치 (호스트를 로컬 아이피로 세팅)
        • minikube 설치
        • jx 설치
    curl -L https://github.com/jenkins-x/jx-cli/releases/download/v3.1.147/jx-cli-darwin-amd64.tar.gz | tar xzv ; sudo mv jx /usr/local/bin
        • 클러스터 만들기
            ○  minikube start --cpus 4 --memory 8096 --disk-size=20g --addons=ingress --vm=true
            ○   git repostory (github 이용)
            ○ git clone https://github.com/anisia20/jx3-minikube-sample.git 
    cd jx3-minikube-sample
    export DOMAIN="$(minikube ip).nip.io"
    jx gitops requirements edit --domain $DOMAIN
     
        • ngrok 터널링 (minikube와 터널링)
    brew install ngrok
    ngrok http 8080
    ...
    Forwarding                    http://5344219fa2a5.ngrok.io -> http://localhost:8080                                                    
    Forwarding                    https://5344219fa2a5.ngrok.io -> http://localhost:8080                                                   
    ...
    vi charts/jenkins-x/jxboot-helmfile-resources/values.yaml
    ...
    hook: "5344219fa2a5.ngrok.io"
    ...
    git add *git commit -a -m "fix: configurations for local minikube"git push origin master
        • git repository 연결
        • github 이용
            ○ 토큰생성
            ○ https://github.com/settings/tokens/new?scopes=repo,read:user,read:org,user:email,admin:repo_hook,delete_repo,write:packages,read:packages,write:discussion,workflow
        • k8s 적용 
    jx admin operator --username root --token ...
    jx admin operator --username anisia20 --token ...
    jx ns js
    kubectl port-forward svc/hook 8080:80
