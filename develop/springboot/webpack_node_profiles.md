### sample
        • 목적 : node 에 springboot profile 분기 와 같은 시스템별 분기 설정 요청
        • 현재 방식
            • gradle build시 webpack NpmTask를 통한 node 동시 빌드 jar 생성
        • 적용 내역
            • .env.staging 파일 생성 및 상수 세팅
            • pakage.json 스크립트 추가
                • "build:staging": "vue-cli-service build --mode staging"
            • build.gradle 내용 변경
                • 빌드 시 변수 전달 받아 분기 처리
    task webpack(type: NpmTask, dependsOn: 'npmInstall') {
        workingDir = file("frontend")
        inputs.file file('frontend/public/bui/js/jqxgrid/jqxcore.js')
        outputs.dir file('frontend/node_modules/jqwidgets-scripts/jqwidgets')
        println System.getProperty('profile')
        if (System.getProperty('profile') == 'staging'){
            args = ['run', 'build:staging']
        } else {
            args = ['run', 'build']
        }
    }
            • pipeline 수정
                • sh script: "./gradlew clean build -x test -Dprofile=staging"

