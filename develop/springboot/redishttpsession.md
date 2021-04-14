|저장형식 | 자료형 | 역활|
|-|-|-|
|spring:session:sessions:expires:(session id) |string| 해당 세션의 만료 키로 사용합니다.|
|spring:session:expirations:(expire time)|set|expire time에 삭제될 세션 정보들을 담고있습니다. 해당 time이 되면 저장된 데이터를 조회하여 해당 세션을 모두 삭제합니다.|
|spring:session:sessions:(session id)|hash|세션의 생성 시간, 마지막 세션 조회 시간, 최대 타임아웃 허용 시간과 해당 세션에 저장한 데이터를 저장합니다.|