 ## lifecycle
    graceful을 위한 고려
    라이프사이클 추가로 사전 lb 기능 해제 추가
    테스트 결과 강제 종료와 같은 종료 진행
```yaml
...    
    lifecycle:
      preStop:
        exec:
          command: [ "/bin/sh", "-c", "touch /tmp/lbdown ; sleep 30"]   
```