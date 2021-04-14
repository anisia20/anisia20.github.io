# Redis springboot

- Redis Command 참조 : http://redisgate.kr/redis/introduction/redis_intro.php

- java example
---
```yaml

redis:
  mode: standalone  # standalone or sentinels or cluster
  master: mymaster
  db_num: 1  # local only (spring.profile=local)
  password :
  standalone:
      host: 127.0.0.1
      port: 6379
  sentinels:
      host: 127.0.0.1, 127.0.0.2
      port: 6379, 6371
  cluster:
      host: 127.0.0.1
      port: 7000, 7001, 8001
  timeout:
      cmdSec : 10  #sec   

```


```java
// set, get
Dto indata = new Dto();
indata.setKey("key");
indata.setData("data");
redisCmd.set("COMMON", indata);
Dto obj = (Dto) redisCmd.get("COMMON");
// list push, pop
indata.setKey("key");
indata.setData("data");
redisCmd.push("QUEUE_NAME",indata);
obj = redisCmd.pop("QUEUE_NAME");
// hash set, get
indata.setKey("key");
indata.setData("data");
redisCmd.hset("HASH_NAME","FIELD_NAME",indata);
obj = redisCmd.hget("HASH_NAME","FIELD_NAME");
// zset set, get
int score = 1;
indata.setKey("key");
indata.setData("data");
redisCmd.zPut("ZSET_NAME",indata,score);
obj = redisCmd.zRange("ZSET_NAME","*문자열*");
```

```java
@Configuration
public class RedisConfigForLettuce {
@Value("${redis.mode}")
	private String mode;
@Value("${redis.master}")
	private String master;
	
	@Value("${redis.db_num}")
	private int dbNum;
	
	@Value("${redis.password}")
	private String pwd;
@Value("${redis.standalone.host}")
	private String sdHost;
@Value("${redis.standalone.port}")
	private int sdPort;
@Value("${redis.sentinels.host}")
	private String[] stHost;
@Value("${redis.sentinels.port}")
	private int[] stPort;
	
	@Value("${redis.cluster.host}")
	private String[] ctHost;
@Value("${redis.cluster.port}")
	private int[] ctPort;
@Value("${redis.timeout.cmdSec}")
	private int cmdSec;
	
	@Value("${spring.profiles.active}")
	private String activeProfile;
	
	@Primary
	@Bean("connectionFactory")
	public RedisConnectionFactory connectionFactory() {
		
		LettuceClientConfiguration clientConfig = LettuceClientConfiguration.builder()
				.commandTimeout(Duration.ofSeconds(10))
				.shutdownTimeout(Duration.ofSeconds(10))
				.clientOptions(
						ClientOptions.builder().autoReconnect(true).pingBeforeActivateConnection(true)
						.timeoutOptions(TimeoutOptions.enabled(Duration.ofSeconds(cmdSec))).build())
				.build();
if (ObjectUtils.isEmpty(mode) || "standalone".equals(mode)) {
			RedisStandaloneConfiguration redisConf = new RedisStandaloneConfiguration();
			redisConf.setHostName(sdHost);
			redisConf.setPort(sdPort);
			redisConf.setDatabase(getDbNo());
			if (ObjectUtils.isEmpty(pwd) == false) redisConf.setPassword(RedisPassword.of(pwd));
			
			return new LettuceConnectionFactory(redisConf, clientConfig);
} else if ("sentinels".equals(mode)) {
			RedisSentinelConfiguration sentinelConfig = new RedisSentinelConfiguration();
			sentinelConfig.setMaster(master);
			sentinelConfig.setDatabase(getDbNo());
			if (EctUtils.isNullOrEmpty(pwd) == false) sentinelConfig.setPassword(RedisPassword.of(pwd));
			
			RedisNode node = null;
			for (int i=0; i < ctHost.length; i++) {
				node = new RedisNode(stHost[i], stPort[i]);
				sentinelConfig.addSentinel(node);
			}
			
			return new LettuceConnectionFactory(sentinelConfig);
} else {
			RedisClusterConfiguration clusterConfig = new RedisClusterConfiguration();
			if (ObjectUtils.isEmpty(pwd) == false) clusterConfig.setPassword(RedisPassword.of(pwd));
			
			RedisNode node = null;
			for (int i=0; i < ctHost.length; i++) {
				node = new RedisNode(ctHost[i], ctPort[i]);
				clusterConfig.addClusterNode(node);
			}
return new LettuceConnectionFactory(clusterConfig);
		}
	}
	
	public int getDbNo() {
		if ("local".equals(activeProfile)) {
			if (ObjectUtils.isEmpty(dbNum)) {
				return 2;
			} else {
				return dbNum;
			}
			
		} else if ("dev".equals(activeProfile)) {
			return 1;
		}
		return 0;
	}

@Primary
	@Bean("redisTemplate")
	public RedisTemplate<String, Object> redisTemplate(@Qualifier("connectionFactory") RedisConnectionFactory factory) {
ObjectMapper om = new ObjectMapper();
		om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL, As.PROPERTY);
		om.configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, false); 
Jackson2JsonRedisSerializer<Object> redisSerializer = new Jackson2JsonRedisSerializer<Object>(Object.class);
		redisSerializer.setObjectMapper(om);
				
        RedisTemplate<String, Object> template = new RedisTemplate<String, Object>();
        template.setKeySerializer(new StringRedisSerializer());
        template.setHashKeySerializer(new StringRedisSerializer());
template.setValueSerializer(redisSerializer);
        template.setHashValueSerializer(redisSerializer);
template.setConnectionFactory(factory);
        template.getConnectionFactory().getConnection();
		template.afterPropertiesSet();
		return template;
	}
}
```

```java
@Configuration
public class RedisCmd {
	
	@Autowired
	@Qualifier("redisTemplateCmd")
	private RedisTemplate<String, Object> template;
public RedisConnectionFactory getConnectionFactory() {
		return template.getConnectionFactory();
	}
public RedisConnection getConnection() {
		try {
			return template.getConnectionFactory().getConnection();
		} catch(Exception e) {
			log.error("getConnection error. e={}", e.getMessage());
			return null;
		}
	}
	
	public String genKey(String redisKeyType, String subKey) {
		return redisKeyType.replaceAll("\\{sub\\}", subKey);
	}
public boolean set(String key, Object obj) {
		return set(key, obj, -1);
	}
public boolean set(String key, Object obj, long expire) {
		boolean b = true;
		try {
			template.opsForValue().set(key, obj);
			if (expire > 0) setKeyExpire(key, expire);
		} catch (Exception e) {
			b = false;
			log.error(e.toString());
		}
		return b;
	}
public Object get(String key) {
		Object r = null;
		try {
			r = template.opsForValue().get(key);
			r = nvl(r);
		} catch (Exception e) {
			r = null;
			log.error(e.toString());
		}
		return r;
	}
public boolean hput(String key, String field, Object obj) {
		boolean b = true;
		try {
			template.opsForHash().put(key, field, obj);
		} catch (Exception e) {
			log.error(e.toString());
			b = false;
		}
		return b;
	}
public boolean hput(String key, String field, Object obj, long expire) {
		boolean b = true;
		try {
			template.opsForHash().put(key, field, obj);
			if (expire > 0) setKeyExpire(key, expire);
		} catch (Exception e) {
			log.error(e.toString());
			b = false;
		}
		return b;
	}
public Object hget(String key, String field) {
		Object r = null;
		try {
			r = template.opsForHash().get(key, field);
		} catch (Exception e) {
			r = null;
			log.error(e.toString());
		}
		return r;
	}
	
	/**
	 * Hash 키의 필드/값 목록 조회
	 * @param key
	 * @return
	 */
	public Map<Object, Object> hgetAll(String key) {
		Map<Object, Object> r = null;
		try {
			r = template.opsForHash().entries(key);
		} catch (Exception e) {
			r = null;
			log.error(e.toString());
		}
		return r;
	}
/**
	 * Hash 키의 필드목록 조회
	 * @param key
	 * @return
	 */
	public Set<Object> hgetAllField(String key) {
		Set<Object> r = null;
		try {
			r = template.opsForHash().keys(key);
		} catch (Exception e) {
			r = null;
			log.error(e.toString());
		}
		return r;
	}
public boolean hdelete(String key, String field) {
boolean b = true;
		try {
			template.opsForHash().delete(key, field);
		} catch (Exception e) {
			b = false;
			log.error(e.toString());
		}
		return b;
	}
	
	public boolean delete(String key) {
boolean b = true;
		try {
			template.delete(key);
		} catch (Exception e) {
			b = false;
			log.error(e.toString());
		}
		return b;
	}
public long push(String key, Object obj) {
		return lPush(key, obj, 0);
	}
	
	public long push(String key, Object obj, long expire) {
		return lPush(key, obj, expire);
	}
	
	public long lPush(String key, Object obj, long expire) {
		long r = 0;
		try {
			r = template.opsForList().leftPush(key, obj);
			if (expire > 0) setKeyExpire(key, expire);
		} catch (Exception e) {
			r = -1;
			log.error(e.toString());
		}
return r;
	}
public long ldelete(String key, Object obj) {
		long r = 0;
		try {
			r = template.opsForList().remove(key, 1, obj);
		} catch (Exception e) {
			r = -1;
			log.error(e.toString());
		}
return r;
	}
	
	public long rPush(String key, Object obj) {
		long r = 0;
		try {
			r = template.opsForList().rightPush(key, obj);
		} catch (Exception e) {
			r = -1;
			log.error(e.toString());
		}
		return r;
	}
public long rPush(String key, Object obj, long expire) {
		long r = 0;
		try {
			r = template.opsForList().rightPush(key, obj);
			if (expire > 0) setKeyExpire(key, expire);
		} catch (Exception e) {
			r = -1;
			log.error(e.toString());
		}
		return r;
	}
	
	public Object pop(String key) {
		return rPop(key);
	}
	
	public Object index(String key, int index) {
		Object r = null;
		try {
			r = template.opsForList().index(key, index);
		} catch (Exception e) {
			r = null;
			log.error(e.toString());
		}
		return r;
	}
public Object rPop(String key) {
		Object r = null;
		try {
			r = template.opsForList().rightPop(key);
		} catch (Exception e) {
			r = null;
			log.error(e.toString());
		}
		return r;
	}
public Object lPop(String key) {
		Object r = null;
		try {
			r = template.opsForList().leftPop(key);
		} catch (Exception e) {
			r = null;
			log.error(e.toString());
		}
		return r;
	}
public Object rPop(String key, int blockTimeOut) {
		Object r = null;
		try {
			r = template.opsForList().rightPop(key, blockTimeOut, TimeUnit.SECONDS);
			r = nvl(r);
		} catch (Exception e) {
			r = null;
			log.error(e.toString());
		}
		return r;
	}
public List<Object> rangeList(String key, int start, int end) {
		List<Object> r = null;
		try {
			r = template.opsForList().range(key, start, end);
		} catch (Exception e) {
			r = null;
			log.error(e.toString());
		}
		return r;
	}
public long getKeyExpire(String key) {
long sec = 0;
		try {
			sec = template.getExpire(key, TimeUnit.SECONDS);
		} catch (Exception e) {
			sec = -1l;
			log.error(e.toString());
		}
		return sec;
	}
public boolean setKeyExpire(String key, long expireSec) {
		boolean b = true;
		try {
			template.expire(key, expireSec, TimeUnit.SECONDS);
		} catch (Exception e) {
			b = false;
			log.error(e.toString());
		}
		return b;
	}
public boolean setKeyPersist(String key) {
		boolean b = true;
		try {
			template.persist(key);
		} catch (Exception e) {
			b = false;
			log.error(e.toString());
		}
		return b;
	}
public boolean hasKey(String key) {
		boolean b = true;
		try {
			b = template.hasKey(key);
		} catch (Exception e) {
			b = false;
			log.error(e.toString());
		}
		return b;
	}
public boolean hasField(String key, String field) {
		boolean b = true;
		try {
			b = template.opsForHash().hasKey(key, field);
		} catch (Exception e) {
			b = false;
			log.error(e.toString());
		}
		return b;
	}
public Long incField(String key, String field, long increment) {
long l = 0l;
		try {
			l = template.opsForHash().increment(key, field, increment);
		} catch (Exception e) {
			l = -1;
			log.error(e.toString());
		}
		return l;
	}
public Long incValue(String key) {
		Long l = 0l;
		try {
			l = template.opsForValue().increment(key);
			if (l == null) {
				log.error("increment value is null. K={}", key);
				l = -1L;
			}
		} catch (Exception e) {
			l = -1L;
			log.error(e.toString());
		}
		return l;
	}
public long hsize(String key) {
		long l = 0L;
		try {
			l = template.opsForHash().size(key);
		} catch (Exception e) {
			l = -1;
			log.error(e.toString());
		}
		return l;
	}
public long qsize(String redisKeyType) {
		long l = 0L;
		try {
			l = template.opsForList().size(redisKeyType);
		} catch (Exception e) {
			l = -1;
			log.error("qsize error. RK={}, err={}", redisKeyType, e.getMessage());
		}
		return l;
	}
	
	public long size(String redisKey) {
		long l = 0L;
		try {
			DataType dType = template.type(redisKey);
			if(dType.equals(DataType.HASH)) {
				l = template.opsForHash().size(redisKey);
			}else if(dType.equals(DataType.LIST)) {
				l = template.opsForList().size(redisKey);
			}else if(dType.equals(DataType.SET)) {
				l = template.opsForSet().size(redisKey);
			}else if(dType.equals(DataType.ZSET)) {
				l = template.opsForZSet().size(redisKey);
			}
		} catch (Exception e) {
			l = -1;
			log.error(e.toString());
		}
		return l;
	}
public List<String> getKeyList( String pattern ) {
		List<String> rslt = new ArrayList<String>();
		try {
			Set<String> keys = template.keys(pattern);
			if (keys == null) return null;
			
			rslt.addAll(keys);
		} catch (Exception e) {
			log.error(e.toString());
		}
		return rslt;
	}
	
	public List<String> getKeyList( String pattern, Integer n_res ) {
		List<String> rslt = new ArrayList<String>();
		try {
			Set<String> keys = template.keys(pattern);
			if (keys == null) {
				n_res = -2;
				return null;
			}
rslt.addAll(keys);
		} catch (Exception e) {
			log.error(e.toString());
			n_res = -3;
		}
		return rslt;
	}
	
	public HashMap<String, Object> getValueList( String key, String pattern ) {
		HashMap<String, Object> result = new HashMap<>();
		Cursor<Entry<Object, Object>> rslt = null;
		try {
			ScanOptions options = ScanOptions.scanOptions().match(pattern).build();
			rslt = template.opsForHash().scan(key, options);
			if (rslt == null) return null;
while(rslt.hasNext()) {
				Entry<Object, Object> bytes = rslt.next();
				result.put((String) bytes.getKey(), bytes.getValue());
			}
} catch (Exception e) {
			log.error(e.toString());
		} finally {
			if (rslt != null) try { rslt.close(); } catch (Exception e) {}
		}
		return result;
	}
public HashMap<String, Object> getFieldList( String key, String pattern ) {
		HashMap<String, Object> result = new HashMap<>();
		Cursor<Entry<Object, Object>> rslt = null;
		try {
			ScanOptions options = ScanOptions.scanOptions().match(pattern).build();
			rslt = template.opsForHash().scan(key, options);
			if (rslt == null) return null;
while(rslt.hasNext()) {
				Entry<Object, Object> bytes = rslt.next();
				result.put((String) bytes.getKey(), bytes.getKey());
			}
} catch (Exception e) {
			log.error(e.toString());
		} finally {
			if (rslt != null) try { rslt.close(); } catch (Exception e) {}
		}
		return result;
	}
Object nvl(Object s) {
		return s == null ? "" : s;
	}
public int checkRedis() {
        try {
        	String key = "RedisCmdHealthcheck";
            Object chk = template.expire(key, 10, TimeUnit.MILLISECONDS);
            return 1;
        } catch (Exception e){ 
        	log.error(e.getMessage()); 
            return -1;
        }
	}
public Set<Object> zRange(String key, double minScore, double maxScore, long offset, long count) {
		try {
			Set<Object> lst = template.opsForZSet().rangeByScore(key, minScore, maxScore, offset, count);
			return lst;
		} catch (Exception e){ 
        	log.error(e.getMessage()); 
            return null;
        }
	}
	
	public Set<Object> zRange(String key, double minScore, double maxScore) {
		try {
			Set<Object> lst = template.opsForZSet().rangeByScore(key, minScore, maxScore);
			return lst;
		} catch (Exception e){ 
			log.error(e.getMessage()); 
			return null;
		}
	}
	
	public List<Object> zRange(String key, String pattern) {
		Cursor<TypedTuple<Object>> rslt = null;
		try {
			ScanOptions options = ScanOptions.scanOptions().match(pattern).build();
			rslt = template.opsForZSet().scan(key, options);
			if (rslt == null) return null;
List<Object> lst = new ArrayList<>();
			while(rslt.hasNext()) {
				TypedTuple<Object> bytes = rslt.next();
				Object obj = bytes.getValue();
				lst.add(obj);
			}
if (lst.size() == 0) return null;
			
			return lst;
		} catch (Exception e){ 
			log.error(e.getMessage()); 
			return null;
		} finally {
			if (rslt != null) { try { rslt.close(); } catch(Exception e) {} }
		}
	}
	
	public Long zRank(String key, Object obj) {
		Long r = null;
		try {
			r = template.opsForZSet().rank(key, obj);
		} catch (Exception e) {
			r = null;
			log.error(e.toString());
		}
		return r;
	}
	
	public long zPut(String key, Object value, double score) {
		try {
			boolean l_res = template.opsForZSet().add(key, value, score);
			return 1;
		} catch (Exception e){ 
        	log.error(e.getMessage()); 
            return -1;
        }
	}
	
	public long zScore(String key, Object value) {
		try {
			Double l_res = template.opsForZSet().score(key, value);
			if (l_res == null) return -1;
			
			return l_res.longValue();
		} catch (Exception e){ 
        	log.error(e.getMessage()); 
            return -2;
        }
	}
	
	public long zRemoveScore(String key, double minScore, double maxScore) {
		try {
			long l_res = template.opsForZSet().removeRangeByScore(key, minScore, maxScore);
			return l_res;
		} catch (Exception e){ 
        	log.error(e.getMessage()); 
            return -1;
        }
	}
	
	public long zRemoveObj(String key, Object...objLst) {
		try {
			long l_res = template.opsForZSet().remove(key, objLst);
			return l_res;
		} catch (Exception e){ 
        	log.error(e.getMessage()); 
            return -1;
        }
	}
	
	public long zSize(String key) {
		try {
			long l_res = template.opsForZSet().size(key);
			return l_res;
		} catch (Exception e){ 
        	log.error(e.getMessage()); 
            return -1;
        }
	}

```