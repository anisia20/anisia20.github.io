### 1. NewRelic APM in Kubernetes 설치
    java 프로세스 라인에 -javaagent:/opt/newrelic/newrelic.jar 내용이 포함
    맞다면 정상 구동 된 상태인데 방화벽 통신확인이 필요

    • 방화벽 확인
    • IP Range: 162.247.240.0/22, Port: 443, Domain: *.newrelic.com 으로 통신이 되는지 확인
    • 로그 확인
    • /opt/newrelic/logs/newrelic_agent.log 경로에서 에러로그 확인 
### 2. APM 데이터 보관기간
    데이터 보유기간은 아래와 같은 글로벌 정책으로 정해져 있으며, 추가적인 연장이나 변경은 불가능

| | |
|-|-|
Component|	Pro
Metric timeslice data|	90 days
Key metrics	|forever
Distributed tracing and logs in context	|8 days
Other trace data *	|7 days
Event data **	|8 days

### agent java
    1. 아래 명령어로 Java agent를 다운받습니다.
    • curl -O https://download.newrelic.com/newrelic/java-agent/newrelic-agent/current/newrelic-java.zip
    2. newrelic-java.zip 파일을 APM 설치대상 서버로 업로드 합니다.

    3. newrelic-java.zip 파일을 압축해제 합니다.
    • ~\# mkdir /opt/newrelic
    • ~]# unzip newrelic-java.zip -d /opt/newrelic
    4. /opt/newrelic/newrelic.yml 파일을 수정합니다.
    • 16 Line - license_key: [key]
    • 32 Line - app_name: Application 용도(영문표기)
    5. 환경변수에 $JAVA_OPTS 추가합니다.
    • export JAVA_OPTS="$JAVA_OPTS -javaagent:/opt/newrelic/newrelic.jar"
    6. Application을 재시작합니다.참고: https://docs.newrelic.com/docs/agents/java-agent/installation/install-java-agent (상세 가이드문서)
