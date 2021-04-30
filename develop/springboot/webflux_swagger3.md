## webflux swagger 3
    webflux 는 swagger 3 버전 부터 지원 하여 재구성
    http://localhost:8080/swagger-ui/ 로 베이스 경로 변경됨


### 필요 자원
    스프링 시큐리티 추가 필요
    .pathMatchers("/swagger-ui/**").permitAll() // 산출물 경로
    .pathMatchers("favicon.ico").permitAll() // 산출물 경로
    .pathMatchers("/swagger-resources/**").permitAll() // 산출물 경로
    .pathMatchers("/v2/api-docs").permitAll() // 산출물 경로
    .pathMatchers("/webjars/**").permitAll() // 산출물 경로

### gradle
```gradle
    compile group: 'org.springframework.plugin', name: 'spring-plugin-core', version: '2.0.0.RELEASE'
    implementation "io.springfox:springfox-boot-starter:3.0.0"
    compile group: 'io.springfox', name: 'springfox-swagger2', version: '3.0.0'
    compile group: 'io.springfox', name: 'springfox-spring-webflux', version: '3.0.0'
    compile group: 'io.springfox', name: 'springfox-swagger-ui', version: '3.0.0'
```

### java
```java
@Configuration
@EnableSwagger2
public class SwaggerConfig {

    @Autowired
    JwtCmd jwtCmd;


    @Bean
    public Docket api() {


        Docket api = new Docket(DocumentationType.SWAGGER_2)
                .select()
                .apis(RequestHandlerSelectors.any()) // 현재 RequestMapping으로 할당된 모든 URL 리스트를 추출
                .paths(PathSelectors.ant("/v1/**")) // 그중 /api/** 인 URL들만 필터링
                .build()
                .apiInfo(apiInfo())
                .useDefaultResponseMessages(false) // default response description 사용하지 않음
                ;


        return api;
    }

    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title("MEMBER")
                .description("MEMBER API")
                .license("glegend")
                .version("v1")
                .build();
    }
```

```java
@Log4j2
@Configuration
public class WebFluxConfig implements WebFluxConfigurer {

    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.
                addResourceHandler("/swagger-ui/**")
                .addResourceLocations("classpath:/META-INF/resources/webjars/springfox-swagger-ui/")
                .resourceChain(false);
    }

}
```