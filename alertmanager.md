Alertmanager examples
https://prometheus.io/docs/alerting/notification_examples/

POST request by curl
~~~sh
curl -X POST --data-urlencode "payload={\"channel\": \"#test\", \"username\": \"webhookbot\", \"text\": \"This is posted to #test and comes from a bot named webhookbot.\", \"icon_emoji\": \":imp:\"}" https://hooks.slack.com/services/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
~~~

JSON payload
~~~json
{
  "channel": "#test",
  "username": "webhookbot",
  "text": "This is posted to #test and comes from a bot named webhookbot. POSTman", 
  "icon_emoji": ":hushed:"
}
~~~

Rules
~~~yml
# Node memory is filling up (< 10% left) [ less then 40% in my case]
- alert: OutOfMemory
  expr: node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes * 100 < 40
  for: 5m
  labels:
    severity: warning
  annotations:
    summary: "Out of memory (instance {{ $labels.instance }})"
    description: "Node memory is filling up (< 10% left)\n  VALUE = {{ $value }}\n  LABELS: {{ $labels }}"
~~~

Configure for my case
~~~yml
global:
  slack_api_url: 'https://hooks.slack.com/services/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'

route:
  receiver: 'slack-notifications'
  group_by: [alertname, datacenter, app]

receivers:
- name: 'slack-notifications'
  slack_configs:
  - channel: '#test'
    title: "{{ range .Alerts }}{{ .Annotations.summary }}\n{{ end }}"
    text: "https://internal.myorg.net/wiki/alerts/{{ .GroupLabels.app }}/{{ .GroupLabels.alertname }}"

# Alert
rules:
- alert: OutOfMemory
  expr: node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes * 100 < 40
  for: 1m
  labels:
    severity: warning
  # Prometheus templates apply here in the annotation and label fields of the alert.
  annotations:
    summary: "Out of memory (instance {{ $labels.instance }})"
    description: "Node memory is filling up (< 10% left)\n  VALUE = {{ $value }}\n  LABELS: {{ $labels }}"
~~~
\
Start Alertmanaget application
~~~sh
./alertmanager --config.file="alertmanager.yml"
~~~

Collection Alerting Rules https://awesome-prometheus-alerts.grep.to/rules.html

Configure | YAML Definition
~~~yml
global:
  slack_api_url: '<slack_webhook_url>'

route:
  receiver: 'slack-notifications'
  group_by: [alertname, datacenter, app]

receivers:
- name: 'slack-notifications'
  slack_configs:
  - channel: '#alerts'
    text: 'https://internal.myorg.net/wiki/alerts/{{ .GroupLabels.app }}/{{ .GroupLabels.alertname }}'
---
# Alert
groups:
- name: Instances
  rules:
  - alert: InstanceDown
    expr: up == 0
    for: 5m
    labels:
      severity: page
    # Prometheus templates apply here in the annotation and label fields of the alert.
    annotations:
      description: '{{ $labels.instance }} of job {{ $labels.job }} has been down for more than 5 minutes.'
      summary: 'Instance {{ $labels.instance }} down'
---
# Receiver
- name: 'team-x'
  slack_configs:
  - channel: '#alerts'
    # Alertmanager templates apply here.
    text: "<!channel> \nsummary: {{ .CommonAnnotations.summary }}\ndescription: {{ .CommonAnnotations.description }}"
---
# Ranging over all received Alerts
- name: 'default-receiver'
  slack_configs:
  - channel: '#alerts'
    title: "{{ range .Alerts }}{{ .Annotations.summary }}\n{{ end }}"
    text: "{{ range .Alerts }}{{ .Annotations.description }}\n{{ end }}"
~~~

Default YAML
~~~yml
global:
  resolve_timeout: 5m

route:
  group_by: ['alertname']
  group_wait: 10s
  group_interval: 10s
  repeat_interval: 1h
  receiver: 'web.hook'
receivers:
- name: 'web.hook'
  webhook_configs:
  - url: 'http://127.0.0.1:5001/'
inhibit_rules:
  - source_match:
      severity: 'critical'
    target_match:
      severity: 'warning'
    equal: ['alertname', 'dev', 'instance']
~                                           
~~~
Webhook URL
https://hooks.slack.com/services/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

https://github.com/samber/awesome-prometheus-alerts