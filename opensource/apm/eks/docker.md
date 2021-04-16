#### dockr-compose
    vm.max_map_count 65530 에러
    명령어 수행 sudo sysctl -w vm.max_map_count=262144


```yaml
version: '2.2'

services:

  fluentd:
    build: ./
    container_name: log-fluentd
    volumes:
      - ./conf:/fluentd/etc
      - ./conf/s3log:/var/log/fluentd/s3
    environment:
      - TZ=Asia/Seoul
    logging:
    driver: "json-file"
    options:
      max-size: "2048m"
    links:
      - "elasticsearch"
    ports:
      - "24224:24224"

  elasticsearch:
    image: elasticsearch:6.8.5
    container_name: log-elasticsearch
    environment:
      - TZ=Asia/Seoul
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
      nofile:
        soft: 65536
        hard: 65536
    volumes:
      - ./elasticsearch/data:/usr/share/elasticsearch/data
    mem_limit: 2g
    cap_add:
      - IPC_LOCK
    expose:
      - 9200
    ports:
      - "9200:9200"
      - "9300:9300"

  kibana:
    image: kibana:6.8.5
    container_name: log-kubana
    environment:
      - TZ=Asia/Seoul
      - ELASTICSEARCH_HOSTS="http://elasticsearch:9200"
    links:
      - "elasticsearch"
    ports:
      - "5601:5601"
```
