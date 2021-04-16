### aplication yml 외부적용시 구동 스크립트
    -Dspring.config.location=/apps/conf/


    # 구동 jvm 옵션
    -Dspring.profiles.active=prod -Dmybaits.mapper=/test/*.xml
    내부 : applicationContext.getResources("classpath:mapper/**/*.xml")
    외부파일 : applicationContext.getResources("file://"+System.getProperty("mybaits.mapper")

