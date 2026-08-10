# Grafana NPD — Top 30 Metrics by Ingested Series (post-Adaptive-Metrics)

**Date:** 2026-08-10
**Datasource:** `grafanacloud-ocsnpd-prom` (uid `grafanacloud-prom`)
**Window:** last 1 hour, `count by (__name__)({__name__!=""})` range query at 2m steps. "Current" is the value at the final evaluation step of the window (not each series' last sample — see note below).
**Context:** prefix-based Adaptive Metrics drop rules (e.g. `{"metric": "http_", "match_type": "prefix", "drop": true}`) were applied earlier today. This report reflects ingestion **after** those rules settled.

## Summary

- **Current ingestion: 1,561 active series across 43 metrics** — down from ~380k series / 535 metrics before the drop rules (**~99.6% reduction**).
- The top 30 metrics now cover 99.2% of everything ingested; only 7 metrics have more than 1 series.
- The hour contained one anomaly: a **~6-minute burst (~13:26 UTC)** where ~389k series — including ~158k `http_*` — flowed through, coinciding with the propagation of the final rule edit. It fully subsided; ingestion has been flat at 1,561 series since.

## Top 30 by current ingested series

| # | Metric | Current series | % of total |
|---|--------|---------------:|-----------:|
| 1 | `target_info` | 868 | 55.61% |
| 2 | `erlang_vm_allocators` | 288 | 18.45% |
| 3 | `rest_client_requests_total` | 196 | 12.56% |
| 4 | `up` | 104 | 6.66% |
| 5 | `kubernetes_build_info` | 23 | 1.47% |
| 6 | `machine_memory_bytes` | 23 | 1.47% |
| 7 | `traces_host_info` | 23 | 1.47% |
| 8 | `alloy_build_info` | 1 | 0.06% |
| 9 | `rabbitmq_build_info` | 1 | 0.06% |
| 10 | `rabbitmq_channel_consumers` | 1 | 0.06% |
| 11 | `rabbitmq_channel_get_ack_total` | 1 | 0.06% |
| 12 | `rabbitmq_channel_get_empty_total` | 1 | 0.06% |
| 13 | `rabbitmq_channel_get_total` | 1 | 0.06% |
| 14 | `rabbitmq_channel_messages_acked_total` | 1 | 0.06% |
| 15 | `rabbitmq_channel_messages_confirmed_total` | 1 | 0.06% |
| 16 | `rabbitmq_channel_messages_delivered_ack_total` | 1 | 0.06% |
| 17 | `rabbitmq_channel_messages_delivered_total` | 1 | 0.06% |
| 18 | `rabbitmq_channel_messages_published_total` | 1 | 0.06% |
| 19 | `rabbitmq_channel_messages_redelivered_total` | 1 | 0.06% |
| 20 | `rabbitmq_channel_messages_unconfirmed` | 1 | 0.06% |
| 21 | `rabbitmq_channel_messages_unroutable_dropped_total` | 1 | 0.06% |
| 22 | `rabbitmq_channel_messages_unroutable_returned_total` | 1 | 0.06% |
| 23 | `rabbitmq_channels` | 1 | 0.06% |
| 24 | `rabbitmq_channels_closed_total` | 1 | 0.06% |
| 25 | `rabbitmq_channels_opened_total` | 1 | 0.06% |
| 26 | `rabbitmq_connections` | 1 | 0.06% |
| 27 | `rabbitmq_connections_closed_total` | 1 | 0.06% |
| 28 | `rabbitmq_connections_opened_total` | 1 | 0.06% |
| 29 | `rabbitmq_disk_space_available_bytes` | 1 | 0.06% |
| 30 | `rabbitmq_identity_info` | 1 | 0.06% |

Ranks 8–30 (and the remaining 13 metrics below the cut) are single-series RabbitMQ and Alloy metrics; ordering among them is alphabetical, not meaningful.

## Biggest metrics dropped by the rules

These were the largest metrics seen earlier in the hour (peak series count) that are now at zero:

| Metric | Peak series (pre-drop) |
|--------|-----------------------:|
| `http_server_request_duration_seconds_bucket` | 119,490 |
| `aspnetcore_authentication_authenticate_duration_seconds_bucket` | 12,180 |
| `kestrel_connection_duration_seconds_bucket` | 12,000 |
| `kube_pod_status_reason` | 11,672 |
| `forge_appservice_execution_duration_milliseconds_bucket` | 9,480 |
| `forge_appservice_events_fetching_milliseconds_bucket` | 9,150 |
| `http_server_request_duration_seconds_count` | 7,966 |
| `http_server_request_duration_seconds_sum` | 7,966 |
| `forge_appservice_commits_and_projections_milliseconds_bucket` | 7,545 |
| `http_client_request_duration_seconds_bucket` | 7,545 |

## Observations

1. **`target_info` is now the largest metric (868 series, 55.6%).** This is the OTel resource-attribute info metric — one series per resource, with every resource attribute as a label. If further trimming is wanted, it's the obvious next candidate (drop rule or attribute reduction), though at this scale there is little cost pressure.
2. **`erlang_vm_allocators` (288) and `rest_client_requests_total` (196)** are the only other metrics of any size; everything else is ≤104 series.
3. **Remaining candidates to keep or drop deliberately:** `up`, build-info metrics, and the single-series RabbitMQ set are cheap and generally useful for meta-monitoring — probably worth keeping.
4. **Methodology note:** "Current" is measured at the aligned final step of the range window. Using each series' last available sample instead (as an earlier report did) inflates dropped metrics with stale pre-rule values — that's why the previous report incorrectly showed `http_*` on top.
