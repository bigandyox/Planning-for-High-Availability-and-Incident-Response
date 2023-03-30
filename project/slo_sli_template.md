# API Service

| Category     | SLI | SLO                                                                                                         |
|--------------|-----|-------------------------------------------------------------------------------------------------------------|
| Availability | (total number of successful requests / total number of requests) ≥ 99%    | 99%                                                                                                         |
| Latency      | 90th percentile of requests ≤ 100ms    | 90% of requests below 100ms                                                                                 |
| Error Budget | The number of error requests / total number of requests  | Error budget is defined at 20%. This means that 20% of the requests can fail and still be within the budget |
| Throughput   | successful (2xx) requests over a 1 second period ≥ 5      | 5 RPS indicates the application is functioning                                                              |
