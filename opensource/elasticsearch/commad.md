### elasticsearch 이상 상태시 복구 방법
	• 상태 확인 방법
		○ curl -v http://localhost:9200/_cluster/health?pretty
	• 샤드 전체 확인
		○ curl -v http://localhost:9200/_cluster/health/?level=shards
	• 해당 샤드중 상태가 red인 부분 삭제 진행
		○ 삭제 된 부분은 인덱스 재생성됨 데이터 삭제는 아님
		○ curl -X DELETE http://localhost:9200/샤드명
	• 재구동 수행
		○ 로그를 상새히 보면 삭제 필요시 로그에 기재 된다
