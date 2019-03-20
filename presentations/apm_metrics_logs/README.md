## Canvas: A Single (and Stunning) Pane of Glass for Logs, Metrics, and APM

This example is from the webinar [Canvas: A Single (and Stunning) Pane of Glass for Logs, Metrics, and APM](https://www.elastic.co/webinars/canvas-a-single-and-stunning-pane-of-glass-for-logs-metrics-and-apm)

![screenshot](/images/apm_metrics_logs.png)

---
### APM Service Count Metric
![screenshot](/images/apm_services_metric.png)

#### SQL
```sql
SELECT COUNT(DISTINCT context.service.name) as services,
AVG(transaction.duration.us) as duration, COUNT(DISTINCT transaction.id) as transactions FROM "apm*"
WHERE "@timestamp" > NOW() - INTERVAL 1 DAY
AND "@timestamp" <= NOW()
```
#### Expression
```
filters
| essql
  query="SELECT COUNT(DISTINCT context.service.name) as services,
AVG(transaction.duration.us) as duration, COUNT(DISTINCT transaction.id) as transactions FROM \"apm*\"
WHERE \"@timestamp\" > NOW() - INTERVAL 1 DAY
AND \"@timestamp\" <= NOW()"
| math "services"
| metric "Services"
  metricFont={font family="'Open Sans', Helvetica, Arial, sans-serif" size=48 align="center" color="#FFFFFF" weight="normal" underline=false italic=false}
  labelFont={font family="'Open Sans', Helvetica, Arial, sans-serif" size=14 align="center" color="#FFFFFF" weight="normal" underline=false italic=false}
| render
```
---
### APM Transaction Count Metric
![screenshot](/images/apm_transactions_metric.png)

#### SQL
```sql
SELECT COUNT(DISTINCT context.service.name) as services,
AVG(transaction.duration.us) as duration, COUNT(DISTINCT transaction.id) as transactions FROM "apm*"
WHERE "@timestamp" > NOW() - INTERVAL 1 DAY
AND "@timestamp" <= NOW()
```
#### Expression
```
filters
| essql
  query="SELECT COUNT(DISTINCT context.service.name) as services,
AVG(transaction.duration.us) as duration, COUNT(DISTINCT transaction.id) as transactions FROM \"apm*\"
WHERE \"@timestamp\" > NOW() - INTERVAL 1 DAY
AND \"@timestamp\" <= NOW()"
| math "transactions"
| formatNumber "0a"
| metric "Transactions"
  metricFont={font family="'Open Sans', Helvetica, Arial, sans-serif" size=48 align="center" color="#FFFFFF" weight="normal" underline=false italic=false}
  labelFont={font family="'Open Sans', Helvetica, Arial, sans-serif" size=14 align="center" color="#FFFFFF" weight="normal" underline=false italic=false}
| render
```
---
### APM Average Transaction Duration Metric
![screenshot](/images/apm_duration_metric.png)

#### SQL
```sql
SELECT COUNT(DISTINCT context.service.name) as services,
AVG(transaction.duration.us) as duration, COUNT(DISTINCT transaction.id) as transactions FROM "apm*"
WHERE "@timestamp" > NOW() - INTERVAL 1 DAY
AND "@timestamp" <= NOW()
```
#### Expression
```
filters
| essql
  query="SELECT COUNT(DISTINCT context.service.name) as services,
AVG(transaction.duration.us) as duration, COUNT(DISTINCT transaction.id) as transactions FROM \"apm*\"
WHERE \"@timestamp\" > NOW() - INTERVAL 1 DAY
AND \"@timestamp\" <= NOW()"
| math "duration / 1000"
| formatNumber "0a"
| metric "Avg Duration (sec)"
  metricFont={font family="'Open Sans', Helvetica, Arial, sans-serif" size=48 align="center" color="#FFFFFF" weight="normal" underline=false italic=false}
  labelFont={font family="'Open Sans', Helvetica, Arial, sans-serif" size=14 align="center" color="#FFFFFF" weight="normal" underline=false italic=false}
| render
```

---
### APM Error Count Metric
![screenshot](/images/apm_errors_metric.png)

#### SQL
```sql
SELECT COUNT(*) total
FROM "apm*"
WHERE QUERY('processor.event: error')
AND "@timestamp" > NOW() - INTERVAL 1 DAY
AND "@timestamp" <= NOW()
```
#### Expression
```
filters
| essql
  query="SELECT COUNT(*) total
FROM \"apm*\"
WHERE QUERY('processor.event: error')
AND \"@timestamp\" > NOW() - INTERVAL 1 DAY
AND \"@timestamp\" <= NOW()"
| math "total"
| formatNumber "0.00a"
| metric "Errors"
  metricFont={font family="'Open Sans', Helvetica, Arial, sans-serif" size=48 align="center" color="#FFFFFF" weight="normal" underline=false italic=false}
  labelFont={font family="'Open Sans', Helvetica, Arial, sans-serif" size=14 align="center" color="#FFFFFF" weight="normal" underline=false italic=false}
| render
```
---
### APM Services Error Bar Chart
![screenshot](/images/apm_services_error_count.png)

#### SQL
```sql
SELECT context.service.name as service, COUNT(*) total
FROM "apm*"
WHERE QUERY('processor.event: error')
AND "@timestamp" > NOW() - INTERVAL 1 DAY
AND "@timestamp" <= NOW()
GROUP BY service
ORDER BY total desc
```
#### Expression
```
filters
| essql
  query="SELECT context.service.name as service, COUNT(*) total
FROM \"apm*\"
WHERE QUERY('processor.event: error')
AND \"@timestamp\" > NOW() - INTERVAL 1 DAY
AND \"@timestamp\" <= NOW()
GROUP BY service
ORDER BY total desc"
| pointseries x="total" y="service" color="total"
| plot defaultStyle={seriesStyle bars=0.75 horizontalBars=true} legend=false palette={palette "#7ECAE3" "#003A4D" gradient=true} xaxis=false
| render
```
---
### APM Services Error Count Over Time
![screenshot](/images/apm_services_error_count_over_time.png)

#### SQL
```sql
SELECT HISTOGRAM("@timestamp", INTERVAL 1 MINUTE) minute, context.service.name service, COUNT(*) total
FROM "apm*"
WHERE QUERY('processor.event: error')
AND "@timestamp" > NOW() - INTERVAL 1 DAY
AND "@timestamp" <= NOW()
GROUP BY minute, service
ORDER BY minute
```
#### Expression
```
filters
| essql
  query="SELECT HISTOGRAM(\"@timestamp\", INTERVAL 1 MINUTE) minute, context.service.name service, COUNT(*) total
FROM \"apm*\"
WHERE QUERY('processor.event: error')
AND \"@timestamp\" > NOW() - INTERVAL 1 DAY
AND \"@timestamp\" <= NOW()
GROUP BY minute, service
ORDER BY minute"
| pointseries x="minute" y="total" color="service"
| plot defaultStyle={seriesStyle lines=1 fill=1} legend=false yaxis=false palette={palette "#7ECAE3" "#003A4D" gradient=true}
| render
```
---
### APM Most Recent Error
![screenshot](/images/apm_services_most_recent_error.png)

#### SQL
```sql
SELECT HISTOGRAM("@timestamp", INTERVAL 1 MINUTE) minute, context.service.name service, COUNT(*) total
FROM "apm*"
WHERE QUERY('processor.event: error')
AND "@timestamp" > NOW() - INTERVAL 1 DAY
AND "@timestamp" <= NOW()
GROUP BY minute, service
ORDER BY minute
```
#### Expression
```
filters
| essql
  query="SELECT HISTOGRAM(\"@timestamp\", INTERVAL 1 MINUTE) minute, context.service.name service, COUNT(*) total
FROM \"apm*\"
WHERE QUERY('processor.event: error')
AND \"@timestamp\" > NOW() - INTERVAL 1 DAY
AND \"@timestamp\" <= NOW()
GROUP BY minute, service
ORDER BY minute"
| pointseries x="minute" y="total" color="service"
| plot defaultStyle={seriesStyle lines=1 fill=1} legend=false yaxis=false palette={palette "#7ECAE3" "#003A4D" gradient=true}
| render
```
---
### Infra Host Health Image
![screenshot](/images/infra_host_image.png)

#### SQL
```sql
SELECT MAX('system.load.1') as max_load
FROM "metricbeat*"
WHERE beat.hostname = 'gke-staging-demo-elastic-default-pool-25a0fc90-5kv2'
AND metricset.name = 'load'
AND "@timestamp" > NOW() - INTERVAL 1 DAY
AND "@timestamp" <= NOW()
```
#### Expression
```
image mode="contain"
  dataurl={asset {filters | essql query="SELECT MAX('system.load.1') as max_load
FROM \"metricbeat*\"
WHERE beat.hostname = 'gke-staging-demo-elastic-default-pool-25a0fc90-5kv2'
AND metricset.name = 'load'
AND \"@timestamp\" > NOW() - INTERVAL 1 DAY
AND \"@timestamp\" <= NOW()" | getCell "max_load" | if {lt 100} then="asset-638ca6e2-0317-4a59-a1d3-0b1f578febc6" else="asset-45c0d84e-7c77-4fd2-b162-dd85016cc3d6"}}
| render
```

---
### Infra Host Health Markdown
![screenshot](/images/infra_host_markdown.png)

#### SQL
```sql
SELECT MAX('system.load.1') as max_load
FROM "metricbeat*"
WHERE beat.hostname = 'gke-staging-demo-elastic-default-pool-25a0fc90-5kv2'
AND metricset.name = 'load'
AND "@timestamp" > NOW() - INTERVAL 1 DAY
AND "@timestamp" <= NOW()
```
#### Expression
```
filters
| essql
  query="SELECT MAX('system.load.1') as max_load
FROM \"metricbeat*\"
WHERE beat.hostname = 'gke-staging-demo-elastic-default-pool-25a0fc90-5kv2'
AND metricset.name = 'load'
AND \"@timestamp\" > NOW() - INTERVAL 1 DAY
AND \"@timestamp\" <= NOW()"
| getCell "max_load"
| if {lt 100}
  then={markdown "gke-staging-demo-elastic-default-pool-25a0fc90-5kv2" font={font family="'Open Sans', Helvetica, Arial, sans-serif" size=18 align="left" color="#000000" weight="normal" underline=false italic=false}}
  else={markdown "gke-staging-demo-elastic-default-pool-25a0fc90-5kv2" font={font family="'Open Sans', Helvetica, Arial, sans-serif" size=18 align="left" color="#dd0a73" weight="bold" underline=false italic=false}}
| render
```
---
### Infra Kubernetes Pods Markdown
![screenshot](/images/infra_pods_markdown.png)

#### SQL
```sql
SELECT COUNT(DISTINCT kubernetes.container.id) as count
FROM "metricbeat-*"
WHERE kubernetes.container.status.phase = 'running'
AND "@timestamp" > NOW() - INTERVAL 1 DAY
AND "@timestamp" <= NOW()
```
#### Expression
```
filters
| essql
  query="SELECT COUNT(DISTINCT kubernetes.container.id) as count
FROM \"metricbeat-*\"
WHERE kubernetes.container.status.phase = 'running'
AND \"@timestamp\" > NOW() - INTERVAL 1 DAY
AND \"@timestamp\" <= NOW()"
| markdown "**" {getCell "count"} "** Kubernetes pods running"
  font={font family="'Open Sans', Helvetica, Arial, sans-serif" size=24 align="left" color="#000000" weight="normal" underline=false italic=false}
| render
```
---
### Infra Kubernetes Pods Markdown
![screenshot](/images/infra_pods_image_repeat.png)

#### SQL
```sql
SELECT COUNT(DISTINCT kubernetes.container.id)/10 as count
FROM "metricbeat-*"
WHERE kubernetes.container.status.phase = 'running'
AND "@timestamp" > NOW() - INTERVAL 1 DAY
AND "@timestamp" <= NOW()
```
#### Expression
```
filters
| essql
  query="SELECT COUNT(DISTINCT kubernetes.container.id)/10 as count
FROM \"metricbeat-*\"
WHERE kubernetes.container.status.phase = 'running'
AND \"@timestamp\" > NOW() - INTERVAL 1 DAY
AND \"@timestamp\" <= NOW()"
| math "count"
| repeatImage image={asset "asset-b24781f0-75d3-4e7e-880a-496a3658a017"} size=40 max=13
| render
```
---
### Uptime Services Monitored Markdown
![screenshot](/images/uptime_markdown_1.png)
![screenshot](/images/uptime_markdown_2.png)

#### SQL
```sql
SELECT "monitor.host" host,  MAX("monitor.duration.us") as duration
FROM "heartbeat*"
WHERE "@timestamp" > NOW() - INTERVAL 1 DAY
AND "@timestamp" <= NOW()
AND host IS NOT NULL
GROUP BY host
ORDER BY duration DESC
```
#### Expression
```
filters
| essql
  query="SELECT \"monitor.host\" host,  MAX(\"monitor.duration.us\") as duration
FROM \"heartbeat*\"
WHERE \"@timestamp\" > NOW() - INTERVAL 1 DAY
AND \"@timestamp\" <= NOW()
AND host IS NOT NULL
GROUP BY host
ORDER BY duration DESC"
| if {math "max(duration)/1000000" | gt 0.1}
  then={markdown "**" {getCell "host"} "** is sooo slow @ **" {getCell "duration" | math "value/1000000" | formatNumber "0.00a"} " seconds**" font={font family="'Open Sans', Helvetica, Arial, sans-serif" size=24 align="left" color="#dd0a73" weight="normal" underline=false italic=false}}
  else={markdown "Monitoring **" {math "count(host)"} "** services for uptime" font={font family="'Open Sans', Helvetica, Arial, sans-serif" size=24 align="left" color="#000000" weight="normal" underline=false italic=false}}
| render
```
---
### Uptime Services Monitored Histogram
![screenshot](/images/uptime_histogram.png)

#### SQL
```sql
SELECT "monitor.host" host, MAX("monitor.duration.us") as duration
FROM "heartbeat*"
WHERE "@timestamp" > NOW() - INTERVAL 1 DAY
AND "@timestamp" <= NOW()
AND host IS NOT NULL
GROUP BY host
ORDER BY duration desc
```
#### Expression
```
filters
| essql
  query="SELECT \"monitor.host\" host, MAX(\"monitor.duration.us\") as duration
FROM \"heartbeat*\"
WHERE \"@timestamp\" > NOW() - INTERVAL 1 DAY
AND \"@timestamp\" <= NOW()
AND host IS NOT NULL
GROUP BY host
ORDER BY duration desc"
| pointseries x="host" y="duration" color="host"
| plot defaultStyle={seriesStyle bars=0.75} legend=false palette={palette "#C5FAF4" "#0F6259" gradient=true}
  font={font size=14 family="'Open Sans', Helvetica, Arial, sans-serif" color="#000000" align="left"} yaxis=false
| render
```
---
### Filebeat Most Recent Log
![screenshot](/images/filebeat_log_markdown.png)

#### SQL
```sql
SELECT message
FROM "filebeat-*"
ORDER BY "@timestamp" DESC
LIMIT 1
```
#### CSS
```css
.canvasRenderEl {
    background: #f4f4f4;
    border: 1px solid #ddd;
    border-left: 3px solid #4cbce4;
    color: #666;
    page-break-inside: avoid;
    font-family: monospace;
    font-size: 15px;
    line-height: 1.6;
    margin-bottom: 1.6em;
    max-width: 100%;
    overflow: auto;
    padding: 1em 1.5em;
    display: block;
    word-wrap: break-word;
}
```

#### Expression
```
filters
| essql query="SELECT message
FROM \"filebeat-*\"
ORDER BY \"@timestamp\" DESC
LIMIT 1"
| markdown {getCell "message"}
| render
  css=".canvasRenderEl {
    background: #f4f4f4;
    border: 1px solid #ddd;
    border-left: 3px solid #4cbce4;
    color: #666;
    page-break-inside: avoid;
    font-family: monospace;
    font-size: 15px;
    line-height: 1.6;
    margin-bottom: 1.6em;
    max-width: 100%;
    overflow: auto;
    padding: 1em 1.5em;
    display: block;
    word-wrap: break-word;
}"
```