### intall guide
    Aws 
    sudo curl -L https://github.com/docker/compose/releases/download/1.25.0\
    -rc2/docker-compose-`uname -s`-`uname -m` -o \
    /usr/local/bin/docker-compose
    sudo chmod +x /usr/local/bin/docker-compose

    /etc/ssh/sshd_config
    Port 10022
    systemctl restart sshd

    Dockerfile
    FROM gitlab/gitlab-ce:latest

    RUN echo "192.168.59.103 hook-jx.192.168.59.103" >> /etc/hosts #webhook

    docker-compose.yaml
    #########################################
```yaml
    version: '3'

    services:
    gitlab:
        build:
        context: .
        dockerfile: Dockerfile
        container_name : gitlab
        #image: 'gitlab/gitlab-ce:latest'
        restart: always
        hostname: '192.168.59.211'
        environment:
        GITLAB_OMNIBUS_CONFIG: |
            external_url 'http://192.168.59.211'
            #gitlab_rails['gitlab_shell_ssh_port'] = 2224
        ports:
        - '80:80'
        - '443:443'
        - '2244:2244'
    # extra_hosts:
        #    - "gitlab.my.com:192.168.59.211"
        volumes:
        - '/srv/gitlab/config:/etc/gitlab'
        - '/srv/gitlab/logs:/var/log/gitlab'
        - '/srv/gitlab/data:/var/opt/gitlab'
        network_mode: host
    #########################################
```

    docker-compose up -d
    docker-compose logs -f -t --tail="all"


    github 에서 토큰 발급으로 이관가능
    import project



    password 변경
    docker-compose exec gitlab bash
    gitlab-rails console -e production

    user = User.where( id: 1).first
    user.password = '...'
    user.password_confirmation = '...'
    user.save
    exit
