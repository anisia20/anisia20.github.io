### 구성
	• 구성 환경 cluster 3, kafka 3
	• docker-compose.yml
    
```yaml
version: '2.1'
# 주키퍼와 카프카 서비스를 설정한다
services:
  # 3개의 노드로 된 주키퍼 클러스터를 구성한다.
  zoo1:
    image: zookeeper:3.4.9
    hostname: zoo1
    # 클라이언트 포트 2181를 bind 한다. 
    ports:
      - "2181:2181"
    # 주키퍼는 서버는 2개의 포트를 가진다. 
    # 2888은 서버 노드끼리 통신을 하기 위해서 사용한다.
    # 3888은 리더 선출을 위해서 사용한다.
    environment:
        ZOO_MY_ID: 1
        ZOO_PORT: 2181
        ZOO_SERVERS: server.1=zoo1:2888:3888 server.2=zoo2:2888:3888 server.3=zoo3:2888:3888
    volumes:
      - ./kafka-multiple/zoo1/data:/data
      - ./kafka-multiple/zoo1/datalog:/datalog
zoo2:
    image: zookeeper:3.4.9
    hostname: zoo2
    ports:
      - "2182:2182"
    environment:
        ZOO_MY_ID: 2
        ZOO_PORT: 2182
        ZOO_SERVERS: server.1=zoo1:2888:3888 server.2=zoo2:2888:3888 server.3=zoo3:2888:3888
    volumes:
      - ./kafka-multiple/zoo2/data:/data
      - ./kafka-multiple/zoo2/datalog:/datalog
zoo3:
    image: zookeeper:3.4.9
    hostname: zoo3
    ports:
      - "2183:2183"
    environment:
        ZOO_MY_ID: 3
        ZOO_PORT: 2183
        ZOO_SERVERS: server.1=zoo1:2888:3888 server.2=zoo2:2888:3888 server.3=zoo3:2888:3888
    volumes:
      - ./kafka-multiple/zoo3/data:/data
      - ./kafka-multiple/zoo3/datalog:/datalog
# 3개의 카프카 브로커를 구성한다.
  # Advertised listeners와 주키퍼 노드 포트를 등록한다.
  # 4개의 파티션을 구성한다.
  kafka1:
    image: confluentinc/cp-kafka:5.2.1
    hostname: kafka1
    ports:
      - "9092:9092"
    environment:
      KAFKA_ADVERTISED_LISTENERS: LISTENER_DOCKER_INTERNAL://kafka1:19092,LISTENER_DOCKER_EXTERNAL://${DOCKER_HOST_IP:-127.0.0.1}:9092
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: LISTENER_DOCKER_INTERNAL:PLAINTEXT,LISTENER_DOCKER_EXTERNAL:PLAINTEXT
      KAFKA_INTER_BROKER_LISTENER_NAME: LISTENER_DOCKER_INTERNAL
      KAFKA_ZOOKEEPER_CONNECT: "zoo1:2181,zoo2:2182,zoo3:2183"
      KAFKA_BROKER_ID: 1
      KAFKA_LOG4J_LOGGERS: "kafka.controller=INFO,kafka.producer.async.DefaultEventHandler=INFO,state.change.logger=INFO"
    volumes:
      - ./kafka-multiple/kafka1/data:/var/lib/kafka/data
    depends_on:
      - zoo1
      - zoo2
      - zoo3
kafka2:
    image: confluentinc/cp-kafka:5.2.1
    hostname: kafka2
    ports:
      - "9093:9093"
    environment:
      KAFKA_ADVERTISED_LISTENERS: LISTENER_DOCKER_INTERNAL://kafka2:19093,LISTENER_DOCKER_EXTERNAL://${DOCKER_HOST_IP:-127.0.0.1}:9093
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: LISTENER_DOCKER_INTERNAL:PLAINTEXT,LISTENER_DOCKER_EXTERNAL:PLAINTEXT
      KAFKA_INTER_BROKER_LISTENER_NAME: LISTENER_DOCKER_INTERNAL
      KAFKA_ZOOKEEPER_CONNECT: "zoo1:2181,zoo2:2182,zoo3:2183"
      KAFKA_BROKER_ID: 2
      KAFKA_LOG4J_LOGGERS: "kafka.controller=INFO,kafka.producer.async.DefaultEventHandler=INFO,state.change.logger=INFO"
    volumes:
      - ./kafka-multiple/kafka2/data:/var/lib/kafka/data
    depends_on:
      - zoo1
      - zoo2
      - zoo3
kafka3:
    image: confluentinc/cp-kafka:5.2.1
    hostname: kafka3
    ports:
      - "9094:9094"
    environment:
      KAFKA_ADVERTISED_LISTENERS: LISTENER_DOCKER_INTERNAL://kafka3:19094,LISTENER_DOCKER_EXTERNAL://${DOCKER_HOST_IP:-127.0.0.1}:9094
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: LISTENER_DOCKER_INTERNAL:PLAINTEXT,LISTENER_DOCKER_EXTERNAL:PLAINTEXT
      KAFKA_INTER_BROKER_LISTENER_NAME: LISTENER_DOCKER_INTERNAL
      KAFKA_ZOOKEEPER_CONNECT: "zoo1:2181,zoo2:2182,zoo3:2183"
      KAFKA_BROKER_ID: 3
      KAFKA_NUM_PARTITIONS: 4
      KAFKA_LOG4J_LOGGERS: "kafka.controller=INFO,kafka.producer.async.DefaultEventHandler=INFO,state.change.logger=INFO"
    volumes:
      - ./kafka-multiple/kafka3/data:/var/lib/kafka/data
    depends_on:
      - zoo1
      - zoo2
      - zoo3
kafka-schema-registry:
    image: confluentinc/cp-schema-registry:5.2.1
    hostname: kafka-schema-registry
    ports:
      - "8081:8081"
    environment:
      SCHEMA_REGISTRY_KAFKASTORE_BOOTSTRAP_SERVERS: PLAINTEXT://kafka1:19092
      SCHEMA_REGISTRY_HOST_NAME: kafka-schema-registry
      SCHEMA_REGISTRY_LISTENERS: http://0.0.0.0:8081
    depends_on:
      - zoo1
      - zoo2
      - zoo3
      - kafka1
      - kafka2
      - kafka3
schema-registry-ui:
    image: landoop/schema-registry-ui:0.9.4
    hostname: kafka-schema-registry-ui
    ports:
      - "8001:8000"
    environment:
      SCHEMAREGISTRY_URL: http://kafka-schema-registry:8081/
      PROXY: "true"
    depends_on:
      - kafka-schema-registry
kafka-rest-proxy:
    image: confluentinc/cp-kafka-rest:5.2.1
    hostname: kafka-rest-proxy
    ports:
      - "8082:8082"
    environment:
      # KAFKA_REST_ZOOKEEPER_CONNECT: zoo1:2181
      KAFKA_REST_LISTENERS: http://0.0.0.0:8082/
      KAFKA_REST_SCHEMA_REGISTRY_URL: http://kafka-schema-registry:8081/
      KAFKA_REST_HOST_NAME: kafka-rest-proxy
      KAFKA_REST_BOOTSTRAP_SERVERS: PLAINTEXT://kafka1:19092
    depends_on:
      - zoo1
      - zoo2
      - zoo3
      - kafka1
      - kafka2
      - kafka3
      - kafka-schema-registry
kafka-topics-ui:
    image: landoop/kafka-topics-ui:0.9.4
    hostname: kafka-topics-ui
    ports:
      - "8000:8000"
    environment:
      KAFKA_REST_PROXY_URL: "http://kafka-rest-proxy:8082/"
      PROXY: "true"
    depends_on:
      - zoo1
      - zoo2
      - zoo3
      - kafka1
      - kafka2
      - kafka3
      - kafka-schema-registry
      - kafka-rest-proxy
kafka-connect:
    image: confluentinc/cp-kafka-connect:5.2.1
    hostname: kafka-connect
    ports:
      - kafka-schema-registry
      - kafka-rest-proxy
kafka-connect:
    image: confluentinc/cp-kafka-connect:5.2.1
    hostname: kafka-connect
    ports:
      - kafka-schema-registry
      - kafka-rest-proxy
kafka-connect:
    image: confluentinc/cp-kafka-connect:5.2.1
    hostname: kafka-connect
    ports:
      - "8083:8083"
    environment:
      CONNECT_BOOTSTRAP_SERVERS: "kafka1:19092"
      CONNECT_REST_PORT: 8083
      CONNECT_GROUP_ID: compose-connect-group
      CONNECT_CONFIG_STORAGE_TOPIC: docker-connect-configs
      CONNECT_OFFSET_STORAGE_TOPIC: docker-connect-offsets
      CONNECT_STATUS_STORAGE_TOPIC: docker-connect-status
      CONNECT_KEY_CONVERTER: io.confluent.connect.avro.AvroConverter
      CONNECT_KEY_CONVERTER_SCHEMA_REGISTRY_URL: 'http://kafka-schema-registry:8081'
      CONNECT_VALUE_CONVERTER: io.confluent.connect.avro.AvroConverter
      CONNECT_VALUE_CONVERTER_SCHEMA_REGISTRY_URL: 'http://kafka-schema-registry:8081'
      CONNECT_INTERNAL_KEY_CONVERTER: "org.apache.kafka.connect.json.JsonConverter"
      CONNECT_INTERNAL_VALUE_CONVERTER: "org.apache.kafka.connect.json.JsonConverter"
      CONNECT_REST_ADVERTISED_HOST_NAME: "kafka-connect"
      CONNECT_LOG4J_ROOT_LOGLEVEL: "INFO"
      CONNECT_LOG4J_LOGGERS: "org.apache.kafka.connect.runtime.rest=WARN,org.reflections=ERROR"
      CONNECT_CONFIG_STORAGE_REPLICATION_FACTOR: "1"
      CONNECT_OFFSET_STORAGE_REPLICATION_FACTOR: "1"
      CONNECT_STATUS_STORAGE_REPLICATION_FACTOR: "1"
      CONNECT_PLUGIN_PATH: '/usr/share/java,/etc/kafka-connect/jars'
    volumes:
      - ./connectors:/etc/kafka-connect/jars/
    depends_on:
      - zoo1
      - zoo2
      - zoo3
      - kafka1
      - kafka2
      - kafka3
      - kafka-schema-registry
      - kafka-rest-proxy
kafka-connect-ui:
    image: landoop/kafka-connect-ui:0.9.4
    hostname: kafka-connect-ui
    ports:
      - "8003:8000"
    environment:
      CONNECT_URL: "http://kafka-connect:8083/"
      PROXY: "true"
    depends_on:
      - kafka-connect
ksql-server:
    image: confluentinc/cp-ksql-server:5.2.1
    hostname: ksql-server
    ports:
      - "8088:8088"
    environment:
      KSQL_BOOTSTRAP_SERVERS: PLAINTEXT://kafka1:19092
      KSQL_LISTENERS: http://0.0.0.0:8088/
      KSQL_KSQL_SERVICE_ID: ksql-server_
    depends_on:
      - zoo1
      - zoo2
      - zoo3
      - kafka1
      - kafka2
      - kafka3
zoonavigator-web:
    image: elkozmon/zoonavigator-web:0.5.1
    ports:
     - "8004:8000"
    environment:
      API_HOST: "zoonavigator-api"
      API_PORT: 9000
    links:
     - zoonavigator-api
    depends_on:
     - zoonavigator-api
zoonavigator-api:
    image: elkozmon/zoonavigator-api:0.5.1
    environment:
      SERVER_HTTP_PORT: 9000
    depends_on:
      - zoo1
      - zoo2
      - zoo3
networks:
  mysqlnetwork:
      driver: bridge   
spring boot 세팅
	• application.yml
kafka:
  bootstrap:
    servers: pcip:9092,pcip:9093,pcip:9094
```