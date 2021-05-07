## aurora db mysql

### 이슈 
    파드 -> db cluster slave 간 hikariCP pool 이 분산이 안되고 하나의 인스턴트만 바라보는 현상

### 분석내용
    mysql 컨넥터에는 클러스터와 최적화된 기능이 없다.
    mariadb 컨넥터를 이용해야 클러스터 기능을 이용할 수 있다.
    

## 개선 사항
    gradle
    runtimeOnly 'org.mariadb.jdbc:mariadb-java-client:2.3.0'

    yaml
    data-source:
      driver-class-name: org.mariadb.jdbc.Driver
      jdbc-url: jdbc:mysql:aurora://main-db.cluster-ro-123123.ap-northeast-2.rds.amazonaws.com:3306/database?serverTimezone=UTC&characterEncoding=utf8&autoReconnect=true
      type: com.zaxxer.hikari.HikariDataSource
      maximum-pool-size: 5
      read-only: true
      ....