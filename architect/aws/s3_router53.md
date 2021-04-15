# S3 생성 및 처리
## S3 생성
    • 버킷 만들기 default 생성 public 환경으 별도 액세스 권한 필요
    • aws cli iam 액세스, 엑세스 비밀키 세팅
    • aws s3 cp 파일위치 s3://test
    • domain 구성 : 새벽 1시 scouter log backup

## Router53
### domain.co.kr/파일명
    • 도메인 :domain.co.kr
    • 타입 : S3 웹 사이트 엔드포인트에 대한 별칭
    • 리전 : 아시아태평양 서울
    • 엔드포인트 : s3-website.ap-northeast-2.amazonaws.com.
### S3
#### 버킷 정책
##### • 전체 허용
    {
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "1",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::domain.co.kr/*"
        }
    ]
    }
##### 아이피 제한
    bucket.s3.amazonaws.com
    https://db.s3.ap-northeast-2.amazonaws.com/20200924.tar

    {
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowIPs",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::awss3buckettest/*",
            "Condition": {
                "IpAddress": {
                    "aws:SourceIP": "192.168.0.0/24"
                },
                "NotIpAddress": {
                    "aws:SourceIP": "192.168.0.100/32"
                }
            }
        }
    ]
    }
    CORS
    ```
    <?xml version="1.0" encoding="UTF-8"?>
    <CORSConfiguration xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
    <CORSRule>
    <AllowedOrigin>*</AllowedOrigin>
    <AllowedOrigin>http://domain.co.kr</AllowedOrigin>
    <AllowedMethod>POST</AllowedMethod>
    <AllowedMethod>GET</AllowedMethod>
    <AllowedMethod>PUT</AllowedMethod>
    <AllowedMethod>DELETE</AllowedMethod>
    <AllowedMethod>HEAD</AllowedMethod>
    <AllowedHeader>*</AllowedHeader>
    </CORSRule>
    </CORSConfiguration>

    ```

    aws_access_key_id=[IAM 참조]
    aws_secret_access_key=[IAM 참조]
    aws s3 cp --recursive / data s3://domain.co.kr/ --acl public-read
