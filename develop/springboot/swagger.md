# swagger sample

```	
    gradle
		// swagger
		compile group: 'io.springfox', name: 'springfox-swagger2', version: '2.5.0'
		compile group: 'io.springfox', name: 'springfox-swagger-ui', version: '2.5.0'
```

## swagger config java
```java		
		@Configuration
		@EnableSwagger2
		@ConditionalOnProperty(name = "test", havingValue = "true", matchIfMissing = false)
		public class SwaggerConfig {
		
			@Autowired
			JWTCmd jwtCmd;
			
		    @Bean
		    public Docket api() {
		    	ParameterBuilder aParameterBuilder = new ParameterBuilder();
		        aParameterBuilder.name("Authorization") //헤더 이름
		                .modelRef(new ModelRef("String"))
		                .parameterType("header") 
		                .defaultValue("Bearer "+jwtCmd.getToken(Def.JWT_SECKEY, "agent1", 365 * 24 * 60 * 60 * 1000))
		                .required(true)
		                .build();
		                
		        List<Parameter> aParameters = new ArrayList<>();
		        aParameters.add(aParameterBuilder.build());
		        
		    	Docket api = new Docket(DocumentationType.SWAGGER_2)
		    			.ignoredParameterTypes(RedirectAttributes.class)
		                .select()
		                .apis(RequestHandlerSelectors.any()) // 현재 RequestMapping으로 할당된 모든 URL 리스트를 추출
		                .paths(PathSelectors.ant("/api/**")) // 그중 /api/** 인 URL들만 필터링
		                .build()
		                .apiInfo(apiInfo())
		                .useDefaultResponseMessages(false) // default response description 사용하지 않음
		                .globalOperationParameters(aParameters)
		                ;
		    	
		    	return api;
		    }
		    
		    private ApiInfo apiInfo() {
				return new ApiInfoBuilder()
						.title("SMMS API")
						.description("신모니터링 시스템 Agent API")
						.license("123")
						.version("v1")
						.build();
		    }
		}
	sample
		@ApiOperation(value="송신ON/OFF 변경", notes="Agent 송신 상태를 ON/OFF로 변경한다.", authorizations = { @Authorization(value="jwtToken") })
		@ApiResponses({
			@ApiResponse(code=200, response=ResponseVo.class, message = "송신 상태 변경 요청 결과"),
		})
		@PreAuthorize("hasRole('ADMIN')")
		@PostMapping("/modifySendOnOff")
		public ModelAndView modifySendOnOff(
				HttpServletRequest request, 
				HttpServletResponse response,
				@RequestBody
				@ApiParam(name="ModifySendOnOff", value="ModifySendOnOff")
				ModifySendOnOff data
			) 
		{
			data.setJobId(mResourcesManager.getKeyMaker().nextKey(KeyMaker.TT_CAB));
			log.info("K={} call, parameters={}", data.getJobId(), request.getRequestURI(), request.getQueryString());
			return new ModelAndView("jsonView", mSmmsAgentConfigService.modifyConfigOnOff(data));
		}
		
		@Data
		public class ModifySendOnOff extends CommonVo{
			@ApiModelProperty(value = "송신 Y(ON)/N(OFF)", required = true, example="Y")
			@NotEmpty(message="R_2013")
			@Pattern(regexp= "^Y$|^N$", message="R_2013")
			String isOnOff;
		}
		
		
		if(mResourcesManager.validationDto(data, result) == true)
			return result;
		
		
		public boolean validationDto(CommonVo dto, Result result) {
			boolean isFail = false;
			if (ValidUtil.isValid(dto, result) == false) {
				log.warn("validation check faild. err={}", result);
				isFail = true;
			}
		}
		
		public static boolean isValid(Object source, Result result) { 
			Set<ConstraintViolation<Object>> violations = validator.validate(source);
			for (ConstraintViolation<Object> violation : violations) {
				result.onFail(ResultCode.getResultCd(violation.getMessage()));
				return false;
			}
			return true;
		}
		
		select box 처리
		@ApiParam(name="table", allowableValues = "TYPE1, TYPE2, TYPE3, TYPE4", value="테이블 명")
					@PathVariable("table") String table,
```		
			
