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


Here are additional Prometheus and Grafana interview questions that are relevant for your Spring Boot expertise:


---

Prometheus Advanced Questions

1. How do you secure Prometheus endpoints in a Spring Boot application?

Protect the /actuator/prometheus endpoint using Spring Security or IP whitelisting.

Example configuration in application.yml:

management.endpoints.web.exposure.include: prometheus
spring.security.user.name: admin
spring.security.user.password: adminpass
management.endpoint.health.roles: ADMIN


2. What is the purpose of a Prometheus Pushgateway? When should you use it?
The Pushgateway is used to collect metrics from short-lived or batch jobs that cannot expose metrics to Prometheus directly.
Example: A batch job completes and pushes metrics using a script:

echo "batch_job_duration_seconds 30" | curl --data-binary @- http://localhost:9091/metrics/job/batch_job

3. Explain how Prometheus handles high-cardinality data.

Prometheus uses labels to store multi-dimensional data. However, excessive unique labels (high cardinality) can lead to performance issues and increased storage.

To optimize:

Use fewer unique label values.

Avoid dynamic label values like UUIDs or timestamps.



4. What is rate() in PromQL, and how is it different from irate()?

rate(): Computes the average rate of change over a specified time range (e.g., rate(metric[5m])).

irate(): Computes the instantaneous rate of change based on the last two data points. It's useful for high-resolution monitoring.


5. How do you handle Prometheus scaling issues in large deployments?

Use federation to divide data collection across multiple Prometheus instances.

Store long-term metrics in external storage like Thanos or Cortex for scalability and durability.



---

Grafana Advanced Questions

1. How can you create templated dashboards in Grafana?

Use variables (e.g., $service) to create dynamic dashboards.

Define a variable for services in the dashboard settings:

label_values(http_server_requests_seconds_count, service)

Use $service in panel queries:

rate(http_server_requests_seconds_count{service="$service"}[5m])


2. How do you set up Grafana to display multiple environments (e.g., Dev, QA, Prod)?

Add environment-specific Prometheus data sources (e.g., Prometheus_Dev, Prometheus_Prod).

Use variables to switch between environments dynamically:

label_values(http_server_requests_seconds_count, environment)


3. What are Grafana annotations, and how do you use them?

Annotations mark specific events on dashboards (e.g., deployments, incidents).

Add annotations via the Grafana UI or integrate with tools like Jenkins to automatically push annotations during deployments.


4. How do you visualize latency percentiles (e.g., 95th percentile) in Grafana?

Use the histogram_quantile() function in PromQL:

histogram_quantile(0.95, sum(rate(http_request_duration_seconds_bucket[5m])) by (le))

This calculates the 95th percentile latency over a 5-minute window.


5. Explain Grafana provisioning. How does it automate dashboard setup?

Grafana allows provisioning of dashboards, data sources, and alerts using YAML or JSON files.

Example data source provisioning:

apiVersion: 1
datasources:
  - name: Prometheus
    type: prometheus
    url: http://prometheus:9090
    access: proxy



---

Combined Prometheus and Grafana Scenarios

1. How do you monitor the health of a Spring Boot microservice ecosystem using Prometheus and Grafana?

Set up metrics for individual services:

Service availability: up{service="service_name"}

Error rate: rate(http_server_requests_seconds_count{status=~"5.."}[5m])

Latency: histogram_quantile(0.95, rate(http_server_requests_seconds_bucket[5m]))


Visualize these metrics in Grafana using service-based dashboards.


2. How do you handle alert deduplication in Prometheus and Grafana?

Use Prometheus' Alertmanager to deduplicate alerts based on labels like service or instance.

Example grouping rule in Alertmanager:

group_by: ['service', 'alertname']


3. How can you create alerts for custom Spring Boot metrics?

Expose a custom metric (e.g., failed login attempts):

@RestController
public class LoginController {
    private final Counter failedLogins;
    public LoginController(MeterRegistry registry) {
        failedLogins = registry.counter("login_failed_count");
    }
    // Increment failedLogins counter on login failure
}

Write an alert rule in Prometheus:

alert: HighLoginFailures
expr: rate(login_failed_count[5m]) > 10
for: 2m
labels:
  severity: critical



---

Behavioral and Troubleshooting Questions

1. How would you debug a missing metric in Prometheus?

Verify the /metrics endpoint of the application.

Check Prometheus scrape logs for errors.

Ensure the scrape configuration is correct and the target is reachable.

Look for high-cardinality labels causing metric drop-off.


2. How do you optimize a slow PromQL query in Grafana?

Use fewer label matchers to reduce the data scanned.

Aggregate at the server level instead of querying individual instances.

Use rate() instead of irate() for larger datasets.


3. How do you ensure alerts are actionable?

Define clear alert descriptions and runbooks.

Set thresholds based on historical trends to reduce noise.

Categorize alerts by severity (e.g., Warning vs. Critical).



---

Let me know if you'd like more detailed scenarios or further clarifications!


