### h2db
        • gradle 
            compile('org.springframework.boot:spring-boot-starter-jdbc')
            runtime('com.h2database:h2')
        • application.yml
            spring:
            application:
                name: sso
            ansi:
                enabled: DETECT
            mvc:
                view:
                    prefix: /WEB-INF/views/
                    suffix: .jsp
                static-path-pattern: /resources/**
            h2:
                console:
                    enabled: true
                    path: /indb/h2
        필자의 경우 H2 웹 콘솔 연결 시 오류 발생 : "Database "mem:testdb" not found, and IFEXISTS=true, so we cant auto-create it [90146-199] 90146/90146 (Help)"
            • 기본값인 jdbc:h2:mem:testdb에도 오류가 발생
            • 원하는 이름인 jdbc:h2:~/leveldiary에도 오류가 발생
            • 오류 문구에 나오는 IFEXISTS=true를 추가하여도(jdbc:h2:mem:testdb;IFEXISTS=TRUE) 동일한 오류 또는 duplicate IFEXISTS 오류가 발생
            • 오류 원인 : 최근 버전의 경우 보안상의 이유로 웹 콘솔에서 새 데이터베이스를 작성할 수 없다 함. (필자에게 설치된 버전은 h2-1.4.199) 이러한 이유로 h2를 별도로 설치 후 dababase를 직접 생성한 후에 작업하기로 결정 (관련 내용 보기- [새창])
                ○ Mac에서 H2 설치 및 사용
                    § 명령어 : $ brew install h2 
    (참고 : Install h2 on Mac OSX [새창])
                    § 사용 명령어 : $ h2 -web
                    § 콘솔 연결 : http://localhost:8082/ 
                    § 접속한 h2 콘솔에서 JDBC URL에 원하는 이름(예:jdbc:h2:~/leveldiary)으로 접속하면 해당 이름으로 데이터베이스가 생성됨
                    § 연결 끊기
    (연결 끊지 않고 http://localhost:8080/h2-console 연결하면 Database may be already in use: null. Possible solutions: close all other connection(s); use the server mode [90020-199] 90020/90020 오류가 발생)
                    § http://localhost:8080/h2-console에 접속하여 앞서 설정했던 JDBC URL(예:jdbc:h2:~/leveldiary)로 연결
            • resources 하위에 
                ○ schema.sql
                DROP TABLE IF EXISTS TBL_EMPLOYEES;
                
                CREATE TABLE TBL_EMPLOYEES (
                id INT AUTO_INCREMENT  PRIMARY KEY,
                first_name VARCHAR(250) NOT NULL,
                last_name VARCHAR(250) NOT NULL,
                email VARCHAR(250) DEFAULT NULL
                );
                
                ○ data.sql
                INSERT INTO TBL_EMPLOYEES (first_name, last_name, email) VALUES
                ('Lokesh', 'Gupta', 'abc@gmail.com'),
                ('Deja', 'Vu', 'xyz@email.com'),
                ('Caption', 'America', 'cap@marvel.com');
                
            이런식으로 작성해 놓으면 부트 구동시 자동으로 테이블 생성을 한다.
            
            • 자동 설정
            • spring.datasource.url이 모든 Datasource의 url이 된다.
            • 수동 설정 (Java Config)
            • spring.datasource.jdbc-url로 해야 HikariCP가 인식한다.


		
		
			
		

