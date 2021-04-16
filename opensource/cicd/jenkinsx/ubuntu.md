### ubuntu
    github 사용시
    mac 에서 설치
    brew install ngrok

    jx
    복제 생성 https://github.com/jx3-gitops-repositories/jx3-kubernetes/generate

    jx-requirements.yml
    apiVersion: core.jenkins-x.io/v4beta1
    kind: Requirements
    spec:
    autoUpdate:
        enabled: false
        schedule: ""
    cluster:
        chartRepository: http://bucketrepo.jx.svc.cluster.local/bucketrepo/charts
        clusterName: kind
        devEnvApprovers:
        - root
        environmentGitOwner: root
        gitKind: gitlab
        gitName: gitlab
        gitServer: https://gitlab.com
        provider: kubernetes
        registry: docker.io
    environments:
    - key: dev
        owner: josm
        repository: jx-kube
    - key: staging
    - key: production
    ingress:
        domain: 192.168.59.103
        externalDNS: false
        kind: ingress
        namespaceSubDomain: -jx.


    git push
    git add *
    git commit -a -m "fix: added domain"
    git push origin master

    jx 설치
    curl -L https://github.com/jenkins-x/jx-cli/releases/download/v3.1.147/jx-cli-linux-amd64.tar.gz | tar xzv ; sudo mv jx /usr/local/bin
    sudo jx upgrade cli
    sudo jx upgrade plugins

    • git repository 연결
        • github 이용
            ○ 토큰생성
            ○ https://github.com/settings/tokens/new?scopes=repo,read:user,read:org,user:email,admin:repo_hook,delete_repo,write:packages,read:packages,write:discussion,workflow
        • gitlab 이용
            ○ access token 생성

    • k8s 적용
        • github: jx admin operator --username anisia20 --token ...
        • gitlab: jx admin operator --username josm --token ...
        jx admin operator --token ...
        • jx ns jx
        git pull

    k get ing

    mac, gitlab 서버에서 
    sudo vi /etc/hosts
    ...
    192.168.59.103 hook-jx.192.168.59.103
    192.168.59.103 bucketrepo-jx.192.168.59.103
    192.168.59.103 bucketrepo-jx.192.168.59.103
    ...

    test
    curl -v http://hook-jx.192.168.59.105/hook/hook 

    //github 이용시
    ngrok http hook-jx.192.168.59.105

    k edit ingress hook
    spec:
    rules:
    - host: 27278f2c7d76.ngrok.io
        http:

    config-root/namespaces/jx/jxboot-helmfile-resources/hook-ingress.yaml
    내용도 변경

    //////


    인증 만료시  $HOME/git/credentials 토큰을 바꿔 주면 됨

    초기화
    k delete ns jjx x-git-operator jx-production jx-staging
    rm -rf ~/.jx3

    샘플생성
    jx create quickstart --username josm --git-kind gitlab
    기존 프로젝트 복제
    jx import --git-server http://192.168.59.211 --git-kind gitlab --name sb-api-sample

    secret 추가
    jx secret verify

    jx get activities
    jx admin log
    jx get build log

    kubectl get secret regcred -o jsonpath='{.data}'

    jx gitops upgrade

    참고 도메인 변경시 자동 변경 되는 것들
    config-root/namespaces/jx/jx-build-controller/jx-build-controller-deploy.yaml 
    config-root/namespaces/jx/jxboot-helmfile-resources/dev-environment.yaml
    config-root/namespaces/jx/jxboot-helmfile-resources/dev-sourcerepository.yaml 
    config-root/namespaces/jx/jxboot-helmfile-resources/tekton-git-secret.yaml 
    config-root/namespaces/jx/lighthouse/lighthouse-foghorn-deploy.yaml 
    config-root/namespaces/jx/lighthouse/lighthouse-keeper-deploy.yaml 
    config-root/namespaces/jx/lighthouse/lighthouse-webhooks-deploy.yaml 
    helmfiles/jx/jx-values.yaml 
    helmfiles/nginx/jx-values.yaml 
    helmfiles/tekton-pipelines/jx-values.yaml  
