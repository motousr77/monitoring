## Configure for my case 
### Instantiate prometheus and other services in general executing (./application_name)
\
Alert Rules file (save anywhere and store path to file for prometheus)
~~~yml
# alert_rules.yaml
groups:
- name: memory_cpu_usage_
  rules:
  # Alert Rule for Server Memory Highload
  - alert: OutOfMemory
    expr: node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes * 100 < 40
    for: 1m
    labels:
      severity: warning
    annotations:
      summary: "Out of memory (instance {{ $labels.instance }})"
      description: "Node memory is filling up (< 10% left)\n  VALUE = {{ $value }}\n  LABELS: {{ $labels }}"
  # Alert Rule for Server Node Highload
  - alert: NodeLoad1
    expr: node_load1 > 1.0
    for: 20s
    labels:
      severity: warning
    annotations:
      summary: "Server under high load"
      description: "Server under high load {{ $value}}. Reported by instance {{ $labels.instance }} of job {{ $labels.job }}."
~~~
\
Prometheus YML
~~~sh
./prometheus --config.file="prometheus.yml"
~~~
~~~yml
# prometheus.yml 
global:
  scrape_interval:     15s
  evaluation_interval: 15s

rule_files:
  - alert_rules.yml
  # - "first_rules.yml"
  # - "second_rules.yml"

alerting:
  alertmanagers:
  - static_configs:
    - targets:
      - localhost:9093

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
    - targets: ['localhost:9090']
  - job_name: 'node_exporter'
    static_configs:
    - targets: ['localhost:9100']
~~~
\
Alert Manager YML
~~~sh
./alertmanager --config.file="alertmanager.yml"
~~~
~~~yml
# alertmanager.yml
global:
  slack_api_url: <SECRET_SLACK_API_TOKEN>
route:
  receiver: 'slack-notifications'
  group_by: [alertname, datacenter, app]

receivers:
- name: 'slack-notifications'
  slack_configs:
  - channel: '#test'
    title: "{{ range .Alerts }}{{ .Annotations.summary }}\n{{ end }}"
    text: "http://18.218.59.27:9090/alerts \n{{ .GroupLabels.app }}/{{ .GroupLabels.alertname }}"
~~~
\
May will be edited :)
\
Monitoring deployments in Kubernetes cluster i will be expose later.

#### Collection Alerting Rules https://awesome-prometheus-alerts.grep.to/rules.html