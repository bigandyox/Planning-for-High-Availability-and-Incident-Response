# API Service

| Category     | SLI | SLO                                                                                                         |
|--------------|-----|-------------------------------------------------------------------------------------------------------------|
| Availability | sum (rate(apiserver_request_total{job="apiserver",code!~"5.."}[5m]))/sum (rate(apiserver_request_total{job="apiserver"}[5m]))    | 99% of requests resulting in a successful response over the last 5 minutes                                  |
| Latency      | histogram_quantile(0.90,
sum(rate(apiserver_request_duration_seconds_bucket{job="apiserver"}[5m])) by (le, verb)) | 90% of requests below 100ms over the last 5 minutes                                                         |
| Error Budget | 1 - ((1 - (sum(increase(apiserver_request_total{job="apiserver", code="200"}[7d])) by (verb)) / sum(increase(apiserver_request_total{job="apiserver"}[7d])) by (verb)) / (1 - .80)) | Error budget no higher than 20% over the last 7 days  |
| Throughput   | sum(rate(apiserver_request_total{job="apiserver",code=~"2.."}[5m])) | 5 requests per second (rps) indicates the application is functioning                                        |
