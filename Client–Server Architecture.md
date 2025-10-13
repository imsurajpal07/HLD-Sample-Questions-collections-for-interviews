# 1 — Client–Server Architecture

### Q1: Explain the Client–Server model. What happens when a client sends a request to a server?

**Ideal answer outline**

* Define client (requester) and server (responder/service provider).
* Mention transport (TCP/IP), DNS resolution, connection establishment, request processing, response, and connection teardown or reuse.
* Mention protocols (HTTP, gRPC) and roles: authentication, validation, business logic, persistence.
  **How to diagram**
* Draw client → network (DNS) → load balancer → server(s) → DB/cache.
* Annotate steps: DNS → TCP/TLS handshake → HTTP request → app logic → DB → response.

---

### Q2: What is the difference between 2-tier, 3-tier, and n-tier architectures?

**Ideal answer outline**

* 2-tier: client ↔ server (DB + app together). Good for small apps.
* 3-tier: presentation (client), application layer (app servers), data layer (DB). Better separation, scalability.
* n-tier: further split (API gateway, auth, microservices, cache, analytics).
  **How to diagram**
* For each: draw layers horizontally; for n-tier add gateways, auth, microservices, message bus, analytics.

---

### Q3: How does client find which server to connect to when multiple servers exist?

**Ideal answer outline**

* DNS + service discovery (Consul/Eureka).
* Load balancers (L4/L7): round robin, least connections, IP hash.
* Health checks and failover.
  **How to diagram**
* Draw DNS → Load Balancer → pool of servers; annotate health-check arrows and service registry interactions.

---

### Q4: What happens behind the scenes when you type `https://example.com`?

**Ideal answer outline**

* DNS lookup, TCP 3-way handshake, TLS handshake, HTTP request/response, browser rendering.
* Caching, cookies, redirects may be involved.
  **How to diagram**
* Linear timeline: DNS → TCP → TLS → HTTP GET → Server processing → Response → Render.

---

### Q5: How would you design a scalable client-server system supporting millions of clients?

**Ideal answer outline**

* Stateless APIs, horizontal scaling, autoscaling groups, LB, CDNs, caching (edge + app), sharding for DB, async processing (queues), monitoring.
  **How to diagram**
* Clients → CDN → LB → stateless app servers (autoscale) → cache layer (Redis) → DB cluster (sharded) → message queue + worker pool.

---

# 2 — Protocols (HTTP, WebSocket, gRPC, etc.)

### Q6: Difference between HTTP and WebSocket?

**Ideal answer outline**

* HTTP: request-response, stateless, short-lived, initiated by client.
* WebSocket: upgrade from HTTP, full-duplex, persistent TCP connection, server push.
* Tradeoffs: overhead, latency, scalability, use cases.
  **How to diagram**
* Side-by-side: HTTP flow per request vs one WebSocket handshake then bidirectional arrows.

---

### Q7: Why is HTTP stateless? How to maintain state?

**Ideal answer outline**

* Server doesn’t persist session by design; each request contains necessary info.
* Maintain state: cookies + session store, JWT tokens (stateless), sticky sessions, distributed cache for session.
  **How to diagram**
* Client sends token/cookie on each request → LB → any server → validate token → serve.

---

### Q8: Difference between stateful and stateless protocols?

**Ideal answer outline**

* Stateful: server keeps context (WebSocket, DB connection). Better for real-time, but harder to scale.
* Stateless: each request independent, easier to scale and cache.
  **How to diagram**
* Show stateless servers interchangeable vs stateful server bound to connection (sticky session).

---

### Q9: How does TCP ensure reliable delivery?

**Ideal answer outline**

* Sequencing, acknowledgments (ACK), retransmission, sliding window, flow control, congestion control (slow start, congestion avoidance).
  **How to diagram**
* Show packets with sequence numbers, ACKs, retransmit on loss; label flow/congestion control components.

---

### Q10: TCP 3-way handshake vs WebSocket handshake

**Ideal answer outline**

* TCP 3-way: SYN, SYN/ACK, ACK → establish TCP. WebSocket handshake: HTTP Upgrade request/101 response (over established TCP). WebSocket uses the TCP connection thereafter.
  **How to diagram**
* Two-tier: first TCP three arrows, then HTTP upgrade exchange, then WebSocket open.

---

### Q11: Why not just use HTTP long-polling instead of WebSocket?

**Ideal answer outline**

* Long polling adds overhead: repeated HTTP requests, higher latency and server load. WebSocket is lower overhead for continuous bidirectional traffic. Long polling simpler to implement and safe behind proxies but scales worse for high-frequency updates.
  **How to diagram**
* Show repeated HTTP requests timeline vs single WebSocket line with many messages.

---

### Q12: When prefer gRPC over WebSocket?

**Ideal answer outline**

* gRPC for inter-service RPCs needing performance, schema (protobuf), streaming (server/client/bidirectional) using HTTP/2. WebSocket for browser-based real-time features and arbitrary messaging. gRPC better for typed RPC, WebSocket better for raw message push from servers to browsers.
  **How to diagram**
* Service mesh with gRPC between microservices; WebSocket gateway to client.

---

### Q13: How LB handles persistent WebSocket connections?

**Ideal answer outline**

* Use L4 or L7 with connection affinity (sticky sessions) or route via consistent hashing; use layer that supports long-lived TCP connections. Health checks and connection draining required. Consider conn-track limits.
  **How to diagram**
* LB → persistent connections to server pool; mark sticky cookie or consistent hashing.

---

# 3 — Stateless vs Stateful Connections

### Q14: Define stateless vs stateful for HTTP, WebSocket, DB connections

**Ideal answer outline**

* HTTP stateless, WebSocket stateful (persistent), DB TCP sessions stateful. Discuss memory, scaling, and failover implications.
  **How to diagram**
* Example: stateless REST request served by any server vs WebSocket connection pinned to a server.

---

### Q15: Why stateless better for scalability?

**Ideal answer outline**

* No session affinity, servers interchangeable, easier auto-scale, simple failover, and caching.
  **How to diagram**
* Many identical app servers behind LB handling any request.

---

### Q16: How make stateful protocols scalable?

**Ideal answer outline**

* Session replication (Redis), shared session store, sticky sessions, consistent hashing, connection migration, or move state client side (tokens). Use external pub/sub for broadcasts.
  **How to diagram**
* Show servers writing sessions to Redis, LB distributing connections.

---

### Q17: Examples of stateful services at large scale

**Ideal answer outline**

* Chat servers, multiplayer game servers, SSH/DB connections, media streaming sessions, WebRTC signaling.
  **How to diagram**
* For a chat: clients → WS servers → pub/sub → other WS servers.

---

### Q18: Design login/session system for stateless REST API

**Ideal answer outline**

* Use JWT for stateless auth (short lived + refresh tokens), store blacklist or refresh tokens in DB, secure cookies for browsers, rotate keys and enforce scopes.
  **How to diagram**
* Client ↔ API gateway (validate JWT) ↔ Auth service ↔ Token store (DB/Redis).

---

# 4 — WebSocket Deep Dive

### Q19: How does WebSocket start? Describe HTTP → WebSocket handshake

**Ideal answer outline**

* Client sends HTTP GET with Upgrade: websocket headers and Sec-WebSocket-Key; server responds 101 Switching Protocols with Sec-WebSocket-Accept. Subsequent frames use WebSocket framing.
  **How to diagram**
* Show initial HTTP GET with headers then 101 response; label subsequent bidirectional frame flow.

---

### Q20: Why persistent TCP for WebSocket?

**Ideal answer outline**

* Keeps connection open for low latency and server push; avoids TCP handshakes per message; ordered, reliable delivery.
  **How to diagram**
* Single TCP pipe between client and server with many messages over time.

---

### Q21: How are WebSocket messages framed?

**Ideal answer outline**

* Binary frame format: FIN, opcode, mask, payload length, masking key, payload. Client frames masked. Small overhead per frame.
  **How to diagram**
* Draw frame structure boxes: header, mask, payload.

---

### Q22: What if WebSocket connection breaks? Recovery?

**Ideal answer outline**

* Client exponential backoff reconnect; resume logic using last-seen message ID/offset; server stores undelivered messages; use persistent queues for durability. Heartbeats detect dead peers.
  **How to diagram**
* Sequence: connection drop → client reconnect → fetch missed messages from DB/queue.

---

### Q23: Authentication & authorization in WebSocket?

**Ideal answer outline**

* Provide token (JWT) in handshake (query param or header via subprotocol, cookie), validate at gateway or auth service, enforce per-message authorization on server. Renew tokens with refresh flow.
  **How to diagram**
* Show handshake with token → auth service validation → authorized connection.

---

### Q24: Scaling WebSocket servers horizontally

**Ideal answer outline**

* LB with sticky sessions or connection routing; use Redis/Kafka for cross-server pub/sub; use connection store to lookup which server holds a user; stateless app servers for business logic; shard users by ID.
  **How to diagram**
* LB → WS servers → Redis pub/sub → other WS servers; annotate "user→server mapping" store.

---

### Q25: WebSocket behind proxies/firewalls?

**Ideal answer outline**

* WebSocket uses standard ports and upgrade headers; some proxies need explicit support or will block Upgrade. Use wss over 443 to traverse firewalls. Consider fallbacks (SSE/long polling).
  **How to diagram**
* Client → corporate proxy → LB; indicate need for proxy supporting Upgrade or fallback.

---

### Q26: Compare WebSocket, SSE, HTTP Polling

**Ideal answer outline**

* SSE: server→client unidirectional event stream over HTTP; lighter than WS for one-way updates. Polling: client periodic requests; high overhead. WebSocket: full duplex.
  **How to diagram**
* Three timelines: polling (repeated requests), SSE (one-way stream), WebSocket (two-way).

---

### Q27: Broadcast to millions of clients (live ticker)

**Ideal answer outline**

* Use hierarchical fanout: publishers → Kafka for ingestion → tiered push layer (streaming compute) → edge caches/CDN or specialized pub/sub clusters → push via WebSocket clusters or push proxies. Use delta updates, compression, batching.
  **How to diagram**
* Publisher → Kafka → processing layer (Flink) → fanout clusters → edge nodes → clients.

---

### Q28: Explain ping/pong heartbeats

**Ideal answer outline**

* Heartbeats detect dead sockets/idle connections and keep NAT mappings; server or client sends ping frames; missing pong within timeout triggers reconnect and cleanup.
  **How to diagram**
* Periodic ping arrows between client and server; annotate timeout behavior.

---

# 5 — Real-time System Design Scenarios (Practice Problems)

*(For each problem: question, ideal outline, diagram approach.)*

### Q29: Design a real-time chat system

**Ideal answer outline**

* Use WebSocket for client connections; LB with sticky sessions; WS server cluster; Redis/Kafka for cross-server pub/sub; Cassandra for message store; notification service for offline; message ack, retries, ordering guarantees.
  **How to diagram**
* Use the full chat architecture I previously provided; label flows for send, broadcast, persist, offline.

---

### Q30: Design a live notification system (likes/comments)

**Ideal answer outline**

* Fanout on write vs fanout on read tradeoff; use message queue for fanout, worker pool to push to subscribers, push to devices via push services, and to web clients via WebSocket. Use batching and backpressure.
  **How to diagram**
* Event source → write to queue → fanout workers → per-user message queues → WS servers/push services → clients.

---

### Q31: Design a live stock ticker

**Ideal answer outline**

* High throughput ingestion (market data feeds) → dedupe/normalize → streaming compute (low latency) → publish to partitioned pub/sub → edge push via WebSocket clusters or CDN with websockets support; compress diffs.
  **How to diagram**
* Market feeds → ingest → processing → partitioned pub/sub → websocket clusters → clients.

---

### Q32: Collaborative document editing (Google Docs)

**Ideal answer outline**

* Use WebSocket for low-latency ops; CRDT or OT for conflict resolution; operational transformation server or CRDT merge on clients; persistent log of ops, snapshotting, presence service.
  **How to diagram**
* Clients → WS servers → OT/CRDT service → shared document store; show op logs and snapshot pipeline.

---

### Q33: Multiplayer gaming server

**Ideal answer outline**

* Choose UDP for low latency state sync where occasional loss is acceptable; use TCP or WebSocket for reliable events; authoritative server model to prevent cheating; tick rate, interest management (area of interest), state reconciliation.
  **How to diagram**
* Clients → authoritative game servers (regionally distributed) → state sync via UDP; use matchmaker service.

---

### Q34: Live dashboard / analytics feed

**Ideal answer outline**

* Telemetry ingestion → stream processing (Kafka + Flink) → materialized view store → push updates to dashboards over WebSocket or fetch via REST; use sampling for extremely high volume.
  **How to diagram**
* Data producers → Kafka → processing → materialized store → WS servers → dashboards.

---

# 6 — Scalability & Reliability in Real-Time Systems

### Q35: Handling millions of concurrent WebSocket connections

**Ideal answer outline**

* Use event-driven servers (epoll), connection multiplexing, horizontal scaling, LB that supports many open connections, offload heavy CPU work to workers, use sharding and connection directory (consistent hashing), offload desktop notifications to separate services.
  **How to diagram**
* LB → many WS servers (annotate epoll/non-blocking I/O) → shared pub/sub.

---

### Q36: What if a WebSocket server fails?

**Ideal answer outline**

* Use connection reestablish with client reconnect logic, replicated state, persistent queues to ensure no message loss, and detection + draining before maintenance. Use health checks and autoscaling.
  **How to diagram**
* Show failure scenario and reconnection steps to another server; show message retrieval from DB/queue.

---

### Q37: Redis Pub/Sub or Kafka to scale WebSocket communication?

**Ideal answer outline**

* Redis pub/sub is simple and low latency for small clusters; Kafka offers persistence, replayability, partitioning for high throughput and durability. For global scale use Kafka + stream processing and regional pub/sub caches.
  **How to diagram**
* Compare: WS servers → Redis cluster (fast) vs WS servers → Kafka (durable) → consumers.

---

### Q38: Ensure message ordering and delivery ACK

**Ideal answer outline**

* Use per-conversation partitioning (shard by chat id), sequence numbers, store offsets, use ACKs and retries, idempotency keys, and at-least-once or exactly-once semantics design (tradeoffs).
  **How to diagram**
* Client sends seq numbers; servers persist with offsets; consumer verifies order before deliver.

---

### Q39: Rate limiting and backpressure control

**Ideal answer outline**

* Implement per-client and global rate limits, token bucket, priority queues, slow consumer detection, drop/queue strategies, and communicate backpressure to producers.
  **How to diagram**
* Show producer → queue → worker; annotate throttling and backpressure signals.

---

### Q40: Offline user handling in chat

**Ideal answer outline**

* Persist messages, store delivery receipts, use notification service (APNs/FCM) for mobile, fetch missed messages at reconnect, mark read/delivered on client ack.
  **How to diagram**
* Client offline → message stored → push notification → client reconnect → sync from DB.

---

# 7 — Security Questions

### Q41: How is authentication handled in WebSocket?

**Ideal answer outline**

* Authenticate at handshake with JWT in query or cookie; validate at gateway; issue short-lived tokens; per-message authorization checks for sensitive ops; rotate keys and revoke tokens via blacklist.
  **How to diagram**
* Handshake includes token → Auth service validation → connection accepted.

---

### Q42: Security concerns with persistent connections

**Ideal answer outline**

* Increased DoS surface (many open sockets), token revocation complexity, session hijacking, stale connections, data exfiltration risk. Mitigate with TLS (wss), rate limits, connection limits, monitoring, and periodic reauthentication.
  **How to diagram**
* Annotate security controls: WSS, rate limiter, WAF, IDS.

---

### Q43: Ensure messages encrypted & tamper-proof

**Ideal answer outline**

* Use TLS (wss), message signing (HMAC), end-to-end encryption (client-side keys) if needed, and secure key management.
  **How to diagram**
* Client ↔ TLS tunnel ↔ server; optional E2E encryption layer between clients.

---

### Q44: Difference between WSS and HTTPS

**Ideal answer outline**

* Both use TLS; WSS is WebSocket over TLS for persistent bidirectional comms; HTTPS is HTTP over TLS for request-response. WSS keeps socket open.
  **How to diagram**
* Show both protocols over TLS but different message patterns.

---

### Q45: Prevent unauthorized broadcasting

**Ideal answer outline**

* Authorize publish operations per topic, check ACLs at server or broker, use signed requests, and validate user access for each broadcast.
  **How to diagram**
* Show ACL check on publish path before pushing to pub/sub.

---

# 8 — Code & Low-Level Understanding

### Q46: Implement a simple WebSocket server in Node/Java — what to explain?

**Ideal answer outline**

* Show handshake, WebSocket frame handling, parsing masked frames, ping/pong, connection lifecycle, message handlers and scaling considerations (clustering).
  **How to diagram**
* Code flow box: accept socket → upgrade → parse frames → handle messages → write frames.

---

### Q47: Client WebSocket API — how it looks

**Ideal answer outline**

* Explain `new WebSocket(url)`, `onopen`, `onmessage`, `onclose`, `send()`, reconnect logic, and error handling.
  **How to diagram**
* Small client block with event handlers connected to WS server.

---

### Q48: What happens when server pushes data to client?

**Ideal answer outline**

* Server frames a message, writes to socket buffer, OS sends TCP segments, client receives frame, unmask/parses and `onmessage` invoked. Consider TCP flow control and backpressure.
  **How to diagram**
* Server send → network stack → client socket → application callback.

---

### Q49: Implement retry logic for reconnecting WebSockets

**Ideal answer outline**

* Exponential backoff with jitter, cap max attempts, detect network online/offline, exponential backoff reset on success, and resume logic with last offset.
  **How to diagram**
* Client reconnect flow with backoff timers and state sync step after reconnect.

---

# 9 — Advanced (SDE-3 / Architect) Questions

### Q50: Testing a system with thousands of concurrent WS connections

**Ideal answer outline**

* Use distributed load generators, containerized clients (k6, Gatling), synthetic traffic patterns, resource monitoring, chaos testing, and realistic scenarios including reconnects and message payloads.
  **How to diagram**
* Test harness cluster → LB → WS cluster, annotated metrics collection.

---

### Q51: Scaling Redis Pub/Sub when it’s a bottleneck

**Ideal answer outline**

* Shard channels, use Redis Cluster, introduce Kafka for durable fanout, add intermediate fanout services, or use specialized pub/sub (NATS, Pulsar). Offload heavy broadcasting to edge.
  **How to diagram**
* Pub/sub tier with shards or Kafka replacing Redis and workers consuming/republishing.

---

### Q52: Multi-region deployments with real-time sync

**Ideal answer outline**

* Local region handling with eventual consistency; global state via geo-replicated data stores or CRDTs; use region proxies and message replication; route clients to nearest region; reconcile conflicts asynchronously.
  **How to diagram**
* Two regions each with WS clusters + regional DB + inter-region replication links; show client region affinity.

---

### Q53: Trade-off between consistency and latency

**Ideal answer outline**

* Strong consistency increases latency (synchronous replication); eventual consistency reduces latency but can show stale data. Choose per requirement (e.g., money transfers → strong consistency; chat messages → eventual acceptable).
  **How to diagram**
* Show synchronous vs asynchronous replication timelines and their latency impacts.

---

### Q54: Optimize bandwidth for high-frequency updates

**Ideal answer outline**

* Send diffs (delta encoding), compress messages (gzip/snappy), batch updates, throttle rates, sample updates for low priority clients, binary format (protobuf) instead of JSON.
  **How to diagram**
* Sender compress/encode → network → client decode/merge.

---

# 10 — Quickfire Basics (Rapid answers to common short Qs)

For each quick Q, state short ideal answer and sketch minimal diagram if asked:

* Is HTTP persistent? — No, stateless per request (keep-alive exists but still request-response).
* WebSocket uses TCP? — Yes.
* Can WebSocket work without HTTP? — No, it upgrades from HTTP handshake.
* Does WebSocket support request-response? — Yes, via message semantics on top of full duplex.
* Why is REST stateless? — Requests carry all context; server stores no client context.
* What’s WSS? — WebSocket over TLS (secure).
* How to reconnect lost sockets? — Client side exponential backoff + resume from last offset.
* Difference push vs polling? — Push = server initiates; polling = client periodically asks.

---

