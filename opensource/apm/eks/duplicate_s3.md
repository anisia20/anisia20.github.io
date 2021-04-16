### s3 동시 백업

```dockerfile
# fluentd/Dockerfile
#FROM fluent/fluentd:v0.14-debian
FROM fluent/fluentd
USER root
RUN ["gem", "install", "fluent-plugin-elasticsearch", "--no-rdoc", "--no-ri", "--version", "2.9.0"]
RUN ["gem", "install", "fluent-plugin-s3", "--no-rdoc", "--no-ri", "--version", "1.3.0"]
```

```conf
<source>
  @type forward
  port 24224
  bind 0.0.0.0
</source>
<match *.**>
  @type copy
  <store>
    @type elasticsearch
    host 10.222.16.46
    port 9200
    logstash_format true
    logstash_prefix fluentd
    logstash_dateformat %Y%m%d
    include_tag_key true
    type_name access_log
    tag_key @log_name
    flush_interval 1s
  </store>
  <store>
    type s3
    aws_key_id asdf
    aws_sec_key asdf
    s3_bucket domain.co.kr
    s3_region ap-northeast-2
    path logs/%Y/%m/%d/${tag}/
    s3_object_key_format %{path}%{time_slice}_%{index}.%{file_extension}
    <buffer tag,time>
      @type file
      path /var/log/fluentd/s3
      timekey 3600 # 1 hour partition
      timekey_wait 10m
    </buffer>
    <format>
      @type json
    </format>
    store_as json
  </store> 
  <store>
    @type stdout
  </store>
</match>

```