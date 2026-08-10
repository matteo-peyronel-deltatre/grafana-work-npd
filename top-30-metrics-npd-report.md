# Grafana NPD — Top 30 Metrics by Ingested Series

> **⚠️ OUTDATED — reflects pre-Adaptive-Metrics ingestion.** Prefix-based Adaptive Metrics drop rules (e.g. `http_` prefix, `drop: true`) were applied on 2026-08-10, roughly 1–2h before these queries ran. The tables below are dominated by pre-rule data: the range queries picked up each series' *last* sample, most of which came from before the rules (plus a ~6-minute burst of ~389k series during the final rule propagation at ~13:26 UTC). **Actual ingestion after the rules settled: ~1,560 active series total, with `http_*` at zero.** Top remaining metrics: `target_info` (868), `erlang_vm_allocators` (288), `rest_client_requests_total` (196), `up` (104). The rules achieved a ~99.6% series reduction (from ~380k).

**Date:** 2026-08-10
**Datasource:** `grafanacloud-ocsnpd-prom` (uid `grafanacloud-prom`)
**Method:** `count by (__name__)({__name__!=""})` range query over the last 24h (1h step). "Current" is the latest evaluation; "Peak (24h)" is the highest value observed in the window.

## Summary

- **Total active series:** ~247,000 across **420 distinct metrics**
- **Top 30 metrics account for ~185,000 series (74.9% of the total)**
- A single metric, `http_server_request_duration_seconds_bucket`, holds **~115,000 series (46.7% of everything ingested)** — nearly 10× the runner-up.

## Top 30

| # | Metric | Current series | Peak (24h) | % of total |
|---|--------|---------------:|-----------:|-----------:|
| 1 | `http_server_request_duration_seconds_bucket` | 115,452 | 121,905 | 46.74% |
| 2 | `kube_pod_status_reason` | 11,672 | 11,688 | 4.73% |
| 3 | `kube_pod_status_phase` | 7,295 | 7,305 | 2.95% |
| 4 | `kube_deployment_status_condition` | 3,966 | 3,966 | 1.61% |
| 5 | `kube_secret_metadata_resource_version` | 3,132 | 3,132 | 1.27% |
| 6 | `kube_pod_container_resource_requests` | 2,968 | 2,972 | 1.20% |
| 7 | `kube_pod_container_resource_limits` | 2,621 | 2,625 | 1.06% |
| 8 | `http_server_active_requests` | 1,929 | 1,947 | 0.78% |
| 9 | `kube_configmap_info` | 1,924 | 1,924 | 0.78% |
| 10 | `kube_configmap_metadata_resource_version` | 1,924 | 1,924 | 0.78% |
| 11 | `node_cpu_seconds_total` | 1,808 | 1,808 | 0.73% |
| 12 | `kube_pod_container_info` | 1,699 | 1,701 | 0.69% |
| 13 | `kube_pod_container_status_restarts_total` | 1,699 | 1,701 | 0.69% |
| 14 | `container_cpu_usage_seconds_total` | 1,636 | 1,639 | 0.66% |
| 15 | `container_memory_cache` | 1,636 | 1,639 | 0.66% |
| 16 | `container_memory_rss` | 1,636 | 1,639 | 0.66% |
| 17 | `container_memory_swap` | 1,636 | 1,639 | 0.66% |
| 18 | `container_memory_usage_bytes` | 1,636 | 1,639 | 0.66% |
| 19 | `container_memory_working_set_bytes` | 1,636 | 1,639 | 0.66% |
| 20 | `node_namespace_pod_container:container_cpu_usage_seconds_total:sum_irate` | 1,636 | 1,638 | 0.66% |
| 21 | `node_namespace_pod_container:container_cpu_usage_seconds_total:sum_rate5m` | 1,636 | 1,638 | 0.66% |
| 22 | `node_namespace_pod_container:container_memory_cache` | 1,636 | 1,639 | 0.66% |
| 23 | `node_namespace_pod_container:container_memory_rss` | 1,636 | 1,639 | 0.66% |
| 24 | `node_namespace_pod_container:container_memory_swap` | 1,636 | 1,639 | 0.66% |
| 25 | `node_namespace_pod_container:container_memory_working_set_bytes` | 1,636 | 1,639 | 0.66% |
| 26 | `container_fs_reads_bytes_total` | 1,460 | 1,463 | 0.59% |
| 27 | `container_fs_reads_total` | 1,460 | 1,463 | 0.59% |
| 28 | `container_fs_writes_bytes_total` | 1,460 | 1,463 | 0.59% |
| 29 | `container_fs_writes_total` | 1,460 | 1,463 | 0.59% |
| 30 | `kube_pod_info` | 1,459 | 1,461 | 0.59% |

## Top 30 — last hour only

Range query over `now-1h` at 5m steps. This view captures **535 distinct metrics and ~375,000 series** — more than the 24h query above surfaced (420 metrics / ~247k series), which appears to have been truncated by a per-query series limit. Treat this hourly view as the more complete picture. Top 30 = ~267,700 series (71.3% of total).

| # | Metric | Current series | Peak (1h) | % of total |
|---|--------|---------------:|----------:|-----------:|
| 1 | `http_server_request_duration_seconds_bucket` | 119,490 | 119,490 | 31.81% |
| 2 | `aspnetcore_authentication_authenticate_duration_seconds_bucket` | 12,180 | 12,180 | 3.24% |
| 3 | `kestrel_connection_duration_seconds_bucket` | 11,970 | 12,000 | 3.19% |
| 4 | `kube_pod_status_reason` | 11,672 | 11,672 | 3.11% |
| 5 | `forge_appservice_execution_duration_milliseconds_bucket` | 9,480 | 9,480 | 2.52% |
| 6 | `forge_appservice_events_fetching_milliseconds_bucket` | 9,150 | 9,150 | 2.44% |
| 7 | `http_server_request_duration_seconds_count` | 7,966 | 7,966 | 2.12% |
| 8 | `http_server_request_duration_seconds_sum` | 7,966 | 7,966 | 2.12% |
| 9 | `forge_appservice_commits_and_projections_milliseconds_bucket` | 7,545 | 7,545 | 2.01% |
| 10 | `http_client_request_duration_seconds_bucket` | 7,545 | 7,545 | 2.01% |
| 11 | `kube_pod_status_phase` | 7,295 | 7,295 | 1.94% |
| 12 | `aspnetcore_routing_match_attempts_total` | 5,560 | 5,560 | 1.48% |
| 13 | `http_client_duration_milliseconds_bucket` | 5,168 | 5,168 | 1.38% |
| 14 | `forge_appservice_aggregate_hydration_milliseconds_bucket` | 4,905 | 4,905 | 1.31% |
| 15 | `http_server_duration_milliseconds_bucket` | 4,832 | 4,832 | 1.29% |
| 16 | `v8js_gc_duration_seconds_bucket` | 4,095 | 4,095 | 1.09% |
| 17 | `kube_deployment_status_condition` | 3,966 | 3,966 | 1.06% |
| 18 | `kube_secret_metadata_resource_version` | 3,132 | 3,132 | 0.83% |
| 19 | `kube_pod_container_resource_requests` | 2,968 | 2,968 | 0.79% |
| 20 | `kube_pod_container_resource_limits` | 2,621 | 2,621 | 0.70% |
| 21 | `forge_messagingbus_command_duration_milliseconds_bucket` | 1,980 | 1,980 | 0.53% |
| 22 | `forge_messagingbus_command_queue_duration_milliseconds_bucket` | 1,980 | 1,980 | 0.53% |
| 23 | `http_server_active_requests` | 1,929 | 1,929 | 0.51% |
| 24 | `kube_configmap_info` | 1,924 | 1,924 | 0.51% |
| 25 | `kube_configmap_metadata_resource_version` | 1,924 | 1,924 | 0.51% |
| 26 | `node_cpu_seconds_total` | 1,808 | 1,808 | 0.48% |
| 27 | `kube_pod_container_info` | 1,699 | 1,699 | 0.45% |
| 28 | `kube_pod_container_status_restarts_total` | 1,699 | 1,699 | 0.45% |
| 29 | `container_cpu_usage_seconds_total` | 1,637 | 1,637 | 0.44% |
| 30 | `container_memory_cache` | 1,637 | 1,637 | 0.44% |

Key differences vs the 24h view: the hourly query surfaces a large block of **application-level OTel histograms** that the 24h query missed — ASP.NET Core (`aspnetcore_*`, `kestrel_*`), the `forge_appservice_*` / `forge_messagingbus_*` histograms, and `http_client_*` — together roughly 80k series. Histogram `_bucket` metrics make up 12 of the top 16 entries.

## Observations

1. **`http_server_request_duration_seconds_bucket` dominates (46.7%).** This is an OpenTelemetry HTTP server histogram. Its cardinality is the product of buckets × routes × methods × status codes × pods, so it explodes easily. If cost or ingest limits become a concern, this is by far the highest-leverage target — options include dropping high-cardinality labels (e.g. per-pod or per-route), reducing bucket count, or converting to a native/exponential histogram.
2. **kube-state-metrics is the next block** (`kube_pod_status_reason`, `kube_pod_status_phase`, `kube_deployment_status_condition`, secret/configmap metadata metrics — ~30k series combined). `kube_pod_status_reason` and `kube_pod_status_phase` emit one series per pod per reason/phase, mostly zeros; allowlisting only needed reasons/phases is a common trim.
3. **cAdvisor container metrics + their recording rules are duplicated.** Entries 14–25 show each `container_*` memory/CPU metric alongside a `node_namespace_pod_container:` recording rule of the same size — the raw series and the rule output are both being stored.
4. **Series counts are stable over the 24h window** (peak ≈ current for nearly every metric), so this snapshot is representative of steady-state ingestion.
