## Docker 설치 가이드
    $ mkdir /nexus-data
    $ docker run --name nexus -d -p 5000:5000 -p 8081:8081 -v /nexus-data:/nexus-data -u root sonatype/nexus3
    admin 패스워드는 /nexus-data/admin.password 에서 확인 가능합니다.
    $ cat /nexus-data/admin.password
    6f471aea-1d52-4e4b-9988-1714e9bf849d # admin의 패스워드
    admin 비밀번호를 새롭게 입력 합니다.



    톱니바퀴 > Repository > Blob Stores > Create blob store
    docker-hosted 와 docker-hub 2개 생성이 필요합니다.
    Name : docker-hosted
    Name : docker-hub

    톱니바퀴 > Repository > Repositories > Create repository
    docker-hosted 와 docker-hub 2개 생성이 필요합니다.
    docker (hosted) 선택
    Name은 docker-hosted
    http 체크 5000
    Enable Docker V1 API 체크
    Blob store docker-hosted 선택


    docker (proxy) 선택
    Name은 docker-hub
    Enable Docker V1 API 체크
    Remote storage 에 https://registry-1.docker.io 입력
    Use Docker Hub 선택


    톱니바퀴 > Realms > Docker Bearer Token Realm Active로 이동 > Save

    /etc/docker/daemon.json 파일 생성
    ✔️ 기존에 존재하지 않는 파일이면, 새로 만들어야 합니다.
    $ cat /etc/docker/daemon.json
    {
    "insecure-registries" : ["182.252.133.70:5000"]
    }
    docker 데몬 재기동
    $ service docker restart
    nexus 컨테이너 재시작
    $ docker restart nexus

    $ docker login localhost:5000
    Username: admin
    Password:
    WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
    Configure a credential helper to remove this warning. See
    https://docs.docker.com/engine/reference/commandline/login/#credentials-store

    Login Succeeded
    3) docker pull 그리고 사내 Docker 저장소에 push
    docker pull busybox 이미지 (from https://hub.docker.com/_/busybox)

    $ docker pull busybox # hub.docker.com 에서 docker pull
    bdbbaa22dec6: Pull complete
    Digest: sha256:6915be4043561d64e0ab0f8f098dc2ac48e077fe23f488ac24b665166898115a
    Status: Downloaded newer image for busybox:latest
    docker.io/library/busybox:latest
    busybox 이미지 확인

    $ docker images -a
    REPOSITORY TAG IMAGE ID CREATED SIZE
    busybox latest 6d5fcfe5ff17 5 weeks ago 1.22MB
    tag 하기

    $ docker tag 6d5fcfe5ff17 182.252.133.70:5000/busybox:v20200205
    사내 Docker 저장소에 push

    $ docker push 182.252.133.70:5000/busybox:v20200205
    The push refers to repository [182.252.133.70:5000/busybox]
    195be5f8be1d: Pushed
    v20200205: digest: sha256:edafc0a0fb057813850d1ba44014914ca02d671ae247107ca70c94db686e7de6 size: 527
    사내 저장소에 push된 이미지 확인

    1) Nexus 웹으로 접속 http://182.252.133.70:8081
    Browse > docker-hosted

    tag 확인
    사내 저장소에서 pull 받기

    $ docker pull 182.252.133.70:5000/busybox:v20200205
    bdbbaa22dec6: Pull complete
    Digest: sha256:edafc0a0fb057813850d1ba44014914ca02d671ae247107ca70c94db686e7de6
    Status: Downloaded newer image for 182.252.133.70:5000/busybox:v20200205
    182.252.133.70:5000/busybox:v20200205
    $ docker images -a
    REPOSITORY TAG IMAGE ID CREATED SIZE
    182.252.133.70:5000/busybox v20200205 6d5fcfe5ff17 5 weeks ago 1.22MB

    출처 : https://velog.io/@king/private-docker-registry
