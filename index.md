# Archi, DEV, log
    개인적인 기록 보관소

## ArchiTect
<details>
<summary> 상세보기
<div markdown="1">

![](20230613155822.png) ![](20230613155912.png)

</div>
</summary>
<div markdown="1">

- ### Cqrs
    - [ordershop](architect/cqrs/redislabs_odershop.md)
- ### aws
    - [router53 join s3](architect/aws/s3_router53.md)
    - [eks autoscale](architect/aws/autoscale.md)
    - [terraform sample](https://github.com/anisia20/terraform-sample)
    - [AGIC](https://aws.amazon.com/ko/blogs/opensource/kubernetes-ingress-aws-alb-ingress-controller/)

</div>
</details>


## Develop
<details>
<summary> 상세보기
<div markdown="1">

![](20230613162542.png) ![](20230613155529.png) ![](20230613155457.png) ![](20230613155621.png) ![](20230613155703.png) ![](20230613155744.png) ![](20230613155953.png) ![](image.png)

</div>
</summary>

<div markdown="1">

- ### Quarkus
    - [Quarkus](https://velog.io/@anisia20?tag=Quarkus)
- ### redis
    - [develop guide java](develop/redis/devguide.md)
- ### springboot
    - [AWS aurora](develop/springboot/aws_aurora_db.md)
    - [Webflux Swagger3](develop/springboot/webflux_swagger3.md)
    - [Redis Http Session](develop/springboot/redishttpsession.md)
    - [csv to redis sample](https://github.com/anisia20/staticstic-csv-redis)
    - [aws s3](develop/springboot/aws_s3.md)
    - [aws mediaconvert](develop/springboot/aws_mediaconvert.md)
    - [extanal yaml](develop/springboot/extanal_yaml.md)
    - [Sb2 RFC 3986](develop/springboot/sbrfc.md)
    - [h2db](develop/springboot/h2db.md)
    - [Websecurity, 302, 200 JWTtoken](https://octoperf.com/blog/2018/03/08/securing-rest-api-spring-security/) webcecurity 는 기본이 302가 리턴됨, rest는 200 관련되서 세팅 사항을 참조
    - [x-forward-for](develop/springboot/xforwardfor.md)
    - [exclude webcecurity ](develop/springboot/exclud_websec.md)
    - [lombok validate](develop/springboot/lombok_validate.md)
    - [swagger](develop/springboot/swagger.md)
    - [frontend profiles](develop/springboot/webpack_node_profiles.md)
    - [kafka](develop/springboot/kafka.md)
    - [Array sort 알고리즘](develop/springboot/array_sort.md)
- ### groovy
    - [ngrinder sample](develop/groovy/ngrinder.md)

- ### python
    - [setting](develop/python/setting.md)
    - [cralwing](develop/python/cralwing.md)

- ### go
    - [개발환경](develop/go/setting.md)
    - [k8s api](develop/go/k8sapi.md)
    - [k8s client api](develop/go/k8sclientrestapi.md)

- ### git
    - [gitlab_install](develop/git/gitlab_install.md)

- ### php
    - [wordpress short code](develop/php/wordpress_function.md)

</div>
</details>

## OpenSource
<details>
<summary> 상세보기
<div markdown="1">

![](20230613160119.png) ![](20230613161145.png) ![](20230613161207.png) ![](20230613161256.png) ![](20230613161347.png) ![](20230613161425.png) ![](20230613161501.png) ![](20230613161523.png) ![](20230613161631.png) ![](20230613161708.png) ![](20230613161737.png) ![](20230613161835.png) ![](20230613161905.png) ![](20230613161940.png) ![](20230613162000.png) ![](20230613162029.png) ![](20230613162051.png) ![](20230613162733.png)

</div>
</summary>

<div markdown="1">

- ### K8S
    - [command](opensource/k8s/command.md)
    - [alb ingress](opensource/k8s/ingress.md)
    - [timezone](opensource/k8s/timezone.md)
    - [api client](opensource/k8s/apiclient.md)
    - [probe](opensource/k8s/probe.md)
    - [lifecycle](opensource/k8s/lifecycle.md)
    - #### onpremise
        - [vbox](opensource/k8s/onpremise/vbox.md)    
        - [install](opensource/k8s/onpremise/install.md)
        - [master](opensource/k8s/onpremise/master.md)
        - [worker](opensource/k8s/onpremise/worker.md)
        - [registry](opensource/k8s/onpremise/registry.md)
        - [deploy](opensource/k8s/onpremise/deploy.md)
    - #### ingress    
        - [contour_ingress](opensource/k8s/ingress/contour_ingress.md)  
        - [haproxy_ingress](opensource/k8s/ingress/haproxy_ingress.md) 
        - [nginx_ingress](opensource/k8s/ingress/nginx_ingress.md) 
    - #### istio
        - [install](opensource/k8s/istio/install.md)
        - [ssl](opensource/k8s/istio/ssl.md)
        - [ingressgw](opensource/k8s/istio/ingressgw.md)
        - [service](opensource/k8s/istio/service.md)
    - #### metallb
        on-premise 환경에 lb 역활
        - [install](opensource/k8s/metallb/install.md) 
    
    - #### minikbe
        - [aws install](opensource/k8s/minikube/aws_install.md) 
    - #### registry
        - [nexus3](opensource/registry/nexus3.md)
    - #### rocketchat
        - [docker](opensource/rocketchat/docker.md)
- ### cicd
    - #### argocd
        - [aws_install](opensource/cicd/argocd/aws_install.md)
    - #### jenkinsx
        - [minikube](opensource/cicd/jenkinsx/minikube.md)
        - [ubuntu](opensource/cicd/jenkinsx/ubuntu.md)
        - [command](opensource/cicd/jenkinsx/command.md)

- ### apm
    - #### newrelic
        - [agent](opensource/apm/newrelic/agent.md)
        - [sql](opensource/apm/newrelic/sql.md)
    - #### efk
        - [docker](opensource/apm/efk/docker.md)
        - [duplicate_s3](opensource/apm/efk/duplicate_s3.md)
        - [eks migrate](opensource/apm/efk/eks_migrate.md)
        - [fluentd custom](opensource/apm/efk/fluentd_custom.md)
    - #### skywalking
        - [build](opensource/apm/skywalking/build.md)
        - [agent](opensource/apm/skywalking/agent.md)
    - #### pinpoint
        - [exam](opensource/apm/pinpoint/server_agent.md)
    - #### zipkin
        - [guide](opensource/apm/zipkin/guide.md)

- ### elasticsearch
    - [command](opensource/elasticsearch/command.md)

- ### kafka
    - [docker compose cluster](opensource/kafka/docker.md)

- ### docker
    - [docker api for mac](opensource/docker/docker_api_mac.md)

- ### nGrinder
    - [nGrinder](https://velog.io/@anisia20?tag=nGrinder)

- ### keycloak
    - [keycloak](opensource/keycloak/keycloak.md)
    - [keycloak 데이터 추가](opensource/keycloak/keycloak_add_attrivutes.md)

- ### hasura
    - [hasura](opensource/hasura/hasura.md)

- ### jeager
    - [jeager](opensource/jeager/jeager.md)
    

</div>
</details>

## Tools
<details>
<summary> 상세보기
<div markdown="1">

![](20230613162149.png)

</div>
</summary>
<div markdown="1">

- ### helm
    - [helm](tools/helm.md)

</div>
</details>
