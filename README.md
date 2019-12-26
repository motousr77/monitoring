## Monitoring Linux System by Prometheus & Grafana
\
Prometheus download section https://prometheus.io/download/ \
Many useful application locate in it.
~~~sh
wget https://github.com/prometheus/prometheus/releases/download/v2.15.1/prometheus-2.15.1.linux-amd64.tar.gz
tar -zxf prometheus-2.15.1.linux-amd64.tar.gz
cd prometheus-2.15.1.linux-amd64 && ./prometheus
~~~
\
Prometheus configuration save in prometheus.yml \
Default web panel: localhost:9090 | See more in the Status section \
Metrics you can view in section Status -> Targets: http://localhost:9090/metrics 
Also you can customize graph like this:
~~~
promhttp_metric_handler_requests_total{code="200"}
rate(promhttp_metric_handler_requests_total{code="200"}[1m])
~~~
\
Also you can install some of metric exporter like node_exporter
~~~sh
wget https://github.com/prometheus/node_exporter/releases/download/v0.18.1/node_exporter-0.18.1.linux-amd64.tar.gz
tar zxf node_exporter-0.18.1.linux-amd64.tar.gz
cd node_exporter-0.18.1.linux-amd64 && ./node_exporter
# listen metrics at http://localhost:9100/metrics
~~~
\
Then we cat edit prometheus.yml file
~~~yml
global:
  scrape_interval:     15s
  evaluation_interval: 15s
scrape_configs:
  - job_name: 'prometheus'
    static_configs:
    - targets: ['localhost:9090']
  - job_name: 'node_exporter'
    static_configs:
    - targets: ['localhost:9100']
~~~
Then you can do queries
~~~query
node_memory_MemFree_bytes
node_filesystem_free_bytes{mountpoint="/"}
~~~
\
Also we can configure cloud server and fetch metrics remote from it. \
Then we cat edit prometheus.yml file
~~~yml
... ...
scrape_configs:
  ... ...
  - job_name: 'node_exporter'
    static_configs:
    - targets: ['localhost:9100', '<remote_ip>:9100']
~~~
\
Grafana download section https://grafana.com/grafana/download
~~~sh
wget https://dl.grafana.com/oss/release/grafana-6.5.2.linux-amd64.tar.gz
tar -zxf grafana-6.5.2.linux-amd64.tar.gz
cd grafana-6.5.2 && bin/grafana-server
~~~
