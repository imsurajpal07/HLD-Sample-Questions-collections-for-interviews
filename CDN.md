| Problem                   | Expected Discussion                                                             |
| ------------------------- | ------------------------------------------------------------------------------- |
| Design a CDN for Netflix  | Streaming optimization, edge cache hierarchy, adaptive bitrate, Anycast routing |
| Design a global image CDN | Compression, image resizing at edge, cache invalidation, tag-based purging      |
| Design CDN metrics system | PoP → Kafka → TSDB (Prometheus/ClickHouse), global dashboard, alerting          |
| Design CDN purge system   | Pub/Sub design, consistency, ordering, scalability                              |
