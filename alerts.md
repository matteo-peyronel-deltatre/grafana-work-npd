# alerts setup

all services on grafana should have alerts. this document highlight a set of ideas for each service.
these are just idea, the goal is to analyze for each service the idea and evaluate if the alert makes sense or not,
update the specific service then implement

grafana instance should be contacted with the MCP

# forge-backoffice

most critical services together with the rest of forge-* services.
all alerts are to be considered in 5 minutes window

## implementation status (2026-07-18)

Implemented in Grafana Cloud (org 1) via MCP.
- Datasource: `grafanacloud-prom` (grafanacloud-ocsprd-prom)
- Prometheus job: `bp-forge/forge-backoffice`
- Folder: `Forge Backoffice Alerts` (uid `forge-backoffice-alerts`), rule group `forge-backoffice`
- Common labels: `service="forge-backoffice"`, `severity=critical|warning`
- App-metric rules use `no_data_state=OK` (traffic-dependent; no traffic must not fire)

Telemetry note: the application OpenTelemetry metrics for this job were absent
for ~2.75 days as of 2026-07-18 (last data ~2026-07-16) while 16 pods were
running per kube-state-metrics. App-metric rules stay in No Data / normal until
the telemetry pipeline is restored; the availability, memory and restart rules
run off kube-state-metrics and evaluate live.

Notifications: no usable contact point exists yet (only an "Adaptive Traces"
webhook; default policy routes to a null "empty" receiver). Rules are created
and evaluated but will not notify until a contact point + notification policy
are configured. TODO.

Calibration baselines (healthy window ~5 to ~2.75 days before 2026-07-18):
POST 5xx ~0%; POST p95 latency ~0.2-2.5s (spikes to ~9.5s); GET p95 latency
~0.2-1s (rare outlier ~5.4s); GET 5xx ~0% (peak 1.6%); dependency failures ~0%
(spike 6%); thread pool queue & kestrel queued ~0; unhandled exceptions ~0;
memory 24-50% of the 6 GiB/pod limit; 16 available replicas (8 per cluster:
bp-prd-ff, bp-prd-ld).

## alerts (implemented)

- more than 2% of error on POST requests
  → `forge-backoffice: POST 5xx error rate > 2%` (critical, for 5m).
    Scoped to 5xx only; POST 4xx (400/422) are expected client-validation
    responses and excluded to avoid noise.
- p95 latency higher than 5 seconds on POST requests
  → `forge-backoffice: POST p95 latency > 5s` (warning, for 5m).
    `histogram_quantile(0.95, ... http_server_request_duration_seconds_bucket ...)`.
- p95 latency higher than 5 seconds on GET requests
  → `forge-backoffice: GET p95 latency > 5s` (warning, for 5m).
- more than 2% of 5xx errors on GET requests (or overall): errors currently occur almost exclusively on GET, and GET volume is high enough to make a percentage threshold statistically meaningful
  → `forge-backoffice: GET 5xx error rate > 2%` (critical, for 5m).
- service availability / no data: fire when the service stops reporting metrics or the number of running instances drops sharply; baseline is a stable count of instances
  → `forge-backoffice: available replicas dropped (>50%)` (critical, for 5m).
    Implemented on kube-state-metrics (available/desired replicas per cluster
    < 0.5) rather than app-metric absence, because the app OTel pipeline can be
    down while the service is healthy (see telemetry note). Baseline 8/cluster.
- outbound dependency failure rate: ratio of failed http client calls (5xx, timeouts, TLS errors via the error_type label) above ~10%, or ~5% sustained over consecutive windows; catches upstream forge-* issues before they become user-facing
  → `forge-backoffice: outbound dependency failure rate > 10%` (warning, for 5m).
    Failures = `error_type=~"5..|System.Threading.Tasks.TaskCanceledException|secure_connection_error"`.
- thread pool / connection queue saturation: thread pool queue length or kestrel queued connections sustained above a low threshold; normally near zero, so any sustained growth is an early warning of starvation and latency spikes
  → `forge-backoffice: thread pool / connection queue saturation` (warning, for 5m).
    `max(process_runtime_dotnet_thread_pool_queue_length or kestrel_queued_connections) > 10`.
- unhandled exceptions rate: spike in aspnetcore unhandled exceptions (do NOT use the raw .NET runtime exception counter, it also counts handled exceptions and is noisy)
  → `forge-backoffice: unhandled exceptions spike` (warning, for 5m).
    `rate(aspnetcore_diagnostics_exceptions_total[5m]) > 0.1`.
- memory / process health: memory usage approaching container limits, pod restarts or OOMKills; requires kube-state-metrics, to be evaluated jointly with cluster metrics
  → `forge-backoffice: memory usage approaching limit (>85%)` (warning, for 10m):
    working set / memory limit > 85% (limit ~6 GiB/pod, excludes forge-backoffice-ui).
  → `forge-backoffice: pod restarts / crash looping` (warning, for 5m):
    `increase(kube_pod_container_status_restarts_total[15m]) > 2`.

# forge-application-service

all alerts are to be considered in 5 minutes window

alerts:

- command duration higher than 5 seconds
