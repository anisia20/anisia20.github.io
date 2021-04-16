## 사전 준비사항
	○ IAM accecekey 발급 (S3 권한)
## 결과화면
## 소스
	○ application.yaml
```yaml
aws:
  access-key: '123'
  secret-key: '123'
  s3:
    bucket: 'mv-bucket'
```
	○ build.gradle
```
	implementation platform('software.amazon.awssdk:bom:2.15.33')
	implementation 'software.amazon.awssdk:kinesis'
	implementation 'software.amazon.awssdk:s3'
```    
	○ AwsConfig.java
```java    
@Configuration
public class AWSConfig {
    /** aws SDK access 키 */
    @Value("${aws.access-key}")
    private String accessKey;
    /** aws SDK secret 키 */
    @Value("${aws.secret-key}")
    private String secretKey;

/**************************************************************
     * S3 client 객체 생성 config
     *
     * @return AmazonS3 아마존 S3 객체
     **************************************************************/
    // v1.xx
//    @Bean
//    public AmazonS3 amazonS3() {
//        return AmazonS3ClientBuilder.standard()
//                .withRegion(this.region)
//                .withCredentials(new AWSStaticCredentialsProvider(
//                        new BasicAWSCredentials(this.accessKey, this.secretKey)))
//                .build();
//    }
    
    @Bean
    public S3Client amazonS3() {
    	AwsSessionCredentials awsCreds = AwsSessionCredentials.create(
    			accessKey,
    			secretKey,
    			"");
    	
    	return S3Client.builder().region(Region.AP_NORTHEAST_2).credentialsProvider(StaticCredentialsProvider.create(awsCreds))
    			.build();
    }
    
    

}
```
    • fileupload.html
```html
<script src="js/vue/vue.js"></script>
<script src="js/vue/vue-friendly-iframe.js"></script>
<script src="js/vue/axios.min.js"></script>

<body></body>
<div align="center" id="app">
  <div>
    <div class="container">
    <label>파일 테스트</label>
        <form @submit.prevent="onSubmit">
            <div class="form-group">
                <input type="file" @change="onFileUpload">
            </div>
            <div class="form-group">
                <button class="btn btn-primary btn-block btn-lg">전송</button>
            </div>
        </form>
    </div>    
  </div>
</div>

<script>
const app = new Vue({
	  el:'#app',
	  data() {
	      return {
	         FILE: null,
	         name: ''
	      };
	    },
	    methods: {
	        onFileUpload (event) {
	          this.FILE = event.target.files[0]
	        },
	        onSubmit() {
	          // upload file
	          const formData = new FormData()
	          formData.append('avatar', this.FILE, this.FILE.name)
	          formData.append('name', this.name)
	          axios.post('/api/fileupload', formData, {
	          }).then((res) => {
	            console.log(res)
	          })
	        }  
	    }
})

</script>
```
    • ProductController.java
```java
	@PostMapping("/fileupload")
	public ResponseEntity fileupload(
			HttpServletRequest request, HttpServletResponse response
			) {
		MultipartHttpServletRequest multiRequest = (MultipartHttpServletRequest) request;
		Map<String, MultipartFile> files = multiRequest.getFileMap();
		
		int n_res = productService.file2S3(files);
		ResponseEntity result = null;
		if( n_res < 0) result = new ResponseEntity(HttpStatus.BAD_REQUEST);
		
		return result;
		
	}
```
    • ProductService.java
```java
	public int file2S3(Map<String, MultipartFile> files) {
		Iterator itr = files.values().iterator();
		MultipartFile file = (MultipartFile) itr.next();
		PutObjectResponse rslt = null;
		try { 
			rslt = s3Cli.putObject(PutObjectRequest.builder().bucket(bucket).key(file.getOriginalFilename()).build(), RequestBody.fromBytes(file.getBytes()));
		} catch (Exception e) {
			log.warn("fail file send D:{}",file.getOriginalFilename());
			return -1;
		}
		
		log.debug("send sucess, S={}, M={}",rslt.sdkHttpResponse().statusCode(),rslt.sdkHttpResponse().statusText());
		return 0;
	}
```