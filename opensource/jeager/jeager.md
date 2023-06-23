## Jeager docker compose
```yaml
version: '3.7'
services:
  jaeger:
    image: jaegertracing/all-in-one:latest
    ports:
      - "16686:16686"
      - "14268:14268"
    environment:
      - COLLECTOR_OTLP_ENABLED=true
      - LOG_LEVEL=debug
    networks:
      - jaeger-example
  hotrod:
    image: jaegertracing/example-hotrod:latest
    # To run the latest trunk build, find the tag at Docker Hub and use the line below
    # https://hub.docker.com/r/jaegertracing/example-hotrod-snapshot/tags
    #image: jaegertracing/example-hotrod-snapshot:0ab8f2fcb12ff0d10830c1ee3bb52b745522db6c
    ports:
      - "8080:8080"
    command: ["all"]
    environment:
      - OTEL_EXPORTER_JAEGER_ENDPOINT=http://jaeger:14268/api/traces
    networks:
      - jaeger-example
    depends_on:
      - jaeger

networks:
  jaeger-example:
```

## SPM Sample
```yaml
version: "3.5"
services:
  jaeger:
    networks:
      - backend
    image: jaegertracing/all-in-one:${JAEGER_IMAGE_TAG}
    volumes:
      - "./jaeger-ui.json:/etc/jaeger/jaeger-ui.json"
    command: --query.ui-config /etc/jaeger/jaeger-ui.json
    environment:
      - METRICS_STORAGE_TYPE=prometheus
      - PROMETHEUS_SERVER_URL=http://prometheus:9090
      - LOG_LEVEL=debug
      - PROMETHEUS_QUERY_SUPPORT_SPANMETRICS_CONNECTOR=${PROMETHEUS_QUERY_SUPPORT_SPANMETRICS_CONNECTOR}
      - PROMETHEUS_QUERY_NAMESPACE=${PROMETHEUS_QUERY_NAMESPACE}
      - PROMETHEUS_QUERY_DURATION_UNIT=${PROMETHEUS_QUERY_DURATION_UNIT}
    ports:
      - "14268:14268"
      - "16686:16686"
  otel_collector:
    networks:
      - backend
    image: otel/opentelemetry-collector-contrib:${OTEL_IMAGE_TAG}
    volumes:
      - ${OTEL_CONFIG_SRC}:/etc/otelcol/otel-collector-config.yml
    command: --config /etc/otelcol/otel-collector-config.yml
    ports:
      - "4317:4317"
    depends_on:
      - jaeger
  microsim:
    networks:
      - backend
    image: yurishkuro/microsim:0.2.0
    command: "-j http://otel_collector:14278/api/traces -d 24h -s 500ms"
    depends_on:
      - otel_collector
  prometheus:
    networks:
      - backend
    image: prom/prometheus:latest
    volumes:
      - "./prometheus.yml:/etc/prometheus/prometheus.yml"
    ports:
      - "9090:9090"
  grafana:
    networks:
      - backend
    image: grafana/grafana:latest
    volumes:
      - ./grafana.ini:/etc/grafana/grafana.ini
      - ./datasource.yml:/etc/grafana/provisioning/datasources/datasource.yaml
    environment:
      - GF_AUTH_ANONYMOUS_ENABLED=true
      - GF_AUTH_ANONYMOUS_ORG_ROLE=Admin
      - GF_AUTH_DISABLE_LOGIN_FORM=true
    ports:
      - 3000:3000

networks:
  backend:
```