### agent 설정
```yaml
Application.yml
spring:
  application:
    name: start-point
  sleuth:
    sampler:
      probability: 1.0
  zipkin:
    base-url: http://localhost:9411
```

```gradle
Gradle
        springBootVersion = '2.1.6.RELEASE'
…
        implementation("org.springframework.cloud:spring-cloud-starter-zipkin:2.1.4.RELEASE")
        implementation("org.springframework.cloud:spring-cloud-starter-sleuth:2.1.4.RELEASE")
```

### Docker-compose.yaml 서버
```yaml
# View traces at http://127.0.0.1:9411/zipkin
zipkin:
  image: openzipkin/zipkin-slim
  container_name: zipkin
  ports:
   - 9411:9411
  networks:
   - mysqlnetwork
```


