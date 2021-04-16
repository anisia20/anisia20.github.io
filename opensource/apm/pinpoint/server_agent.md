### SERVER
    git clone https://github.com/pinpoint-apm/pinpoint-docker.git
    docker-compose pull && docker-compose up -d
    
    cat .env
    http://localhost:8079

### AGENT
    • https://github.com/pinpoint-apm/pinpoint/releases/tag/v2.2.2 
    • pinpoint-agent-2.2.2.tar.gz
    • 압축 풀고 어플리케이션에 디렉토리 통 복사
    -javaagent:pinpoint/pinpoint-bootstrap-2.2.2.jar -Dpinpoint.agentId=member1 -Dpinpoint.applicationName=member -Dspring.profiles.active=local