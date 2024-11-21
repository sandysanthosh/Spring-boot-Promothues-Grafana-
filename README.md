Prometheus and Grafana Interview Questions with Answers

Prometheus Questions

1. What is Prometheus, and why is it used?
Prometheus is an open-source monitoring and alerting toolkit designed for reliability and scalability. It collects metrics from configured targets at regular intervals, stores them in a time-series database, and provides a query language (PromQL) for data analysis. It's widely used for monitoring system performance, tracking application health, and creating alerts.


---

2. Explain the architecture of Prometheus.
Prometheus architecture includes the following components:

Prometheus Server: Scrapes metrics from targets and stores them in a time-series database.

Exporters: Collect metrics from applications or systems (e.g., Node Exporter for OS-level metrics).

PromQL: Query language to analyze metrics.

Alertmanager: Sends alerts based on predefined rules.

Pushgateway: Handles metrics pushed from short-lived jobs.



---

3. What is the difference between pull-based and push-based monitoring?

Pull-based: Prometheus actively scrapes metrics from endpoints. This method ensures better control over data collection.

Push-based: Metrics are pushed to the monitoring system (e.g., using Pushgateway). This is used for batch jobs or ephemeral services.



---

4. How do you configure Prometheus to monitor a Spring Boot application?

Add Micrometer dependencies to your Spring Boot application:

<dependency>
  <groupId>io.micrometer</groupId>
  <artifactId>micrometer-registry-prometheus</artifactId>
</dependency>

Expose metrics at the /actuator/prometheus endpoint by enabling Spring Actuator:

management.endpoints.web.exposure.include: prometheus
management.metrics.export.prometheus.enabled: true

Update Prometheus configuration to scrape metrics:

scrape_configs:
  - job_name: 'spring-boot-app'
    static_configs:
      - targets: ['localhost:8080']



---

5. What are the different types of metrics in Prometheus?

Counter: Tracks a value that only increases (e.g., request count).

Gauge: Tracks values that go up and down (e.g., memory usage).

Histogram: Measures the frequency of values in buckets (e.g., response times).

Summary: Provides quantiles over a sliding time window.



---

6. How do you expose custom metrics in a Spring Boot application?
Use the Micrometer API to create custom metrics:

``` 
@RestController
public class MetricsController {
    private final Counter requestCounter;

    public MetricsController(MeterRegistry meterRegistry) {
        this.requestCounter = meterRegistry.counter("custom_request_count");
    }

    @GetMapping("/custom")
    public String customEndpoint() {
        requestCounter.increment();
        return "Custom metrics exposed!";
    }
}
```

---

7. What is PromQL? Write a query to calculate the average response time of an API.
PromQL (Prometheus Query Language) is used to query and aggregate metrics.
Example query:

rate(http_server_requests_seconds_sum[5m]) / rate(http_server_requests_seconds_count[5m])

This calculates the average response time over a 5-minute window.


---

Grafana Questions

1. What is Grafana, and how does it work with Prometheus?
Grafana is a data visualization tool used to create dashboards and analyze metrics. When integrated with Prometheus, Grafana queries the Prometheus database using PromQL to display real-time or historical metrics.


---

2. How do you set up Grafana to visualize Spring Boot metrics?

Install Grafana and add Prometheus as a data source.

Configure the Prometheus URL in Grafana (e.g., http://localhost:9090).

Create a new dashboard and add panels to visualize metrics like CPU usage, memory, or custom metrics from /metrics.



---

3. How do you configure alerts in Grafana?

Go to the Alerts section in Grafana.

Define a condition for your query (e.g., "CPU usage > 80%").

Set the evaluation interval and notification channel (e.g., email, Slack).

Grafana will send alerts based on the defined rules.



---

4. How do you create a panel in Grafana for monitoring request counts?

Add a new panel to the dashboard.

Use the following PromQL query to monitor request counts:

sum(rate(http_server_requests_total[1m]))

Configure visualization settings (e.g., bar chart, line graph).



---

5. How can you compare the performance of multiple services in Grafana?

Use a multi-dimensional PromQL query with labels:

sum(rate(http_server_requests_seconds_sum{service=~"service1|service2"}[5m])) by (service)

Group metrics by service and display them on the same panel for comparison.


Let me know if you'd like deeper insights into any of these!

