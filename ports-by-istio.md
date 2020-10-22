# 端口占用情况

| Port | Protocol | Used by | Description |
| :--- | :--- | :--- | :--- |
| 15000 | TCP | Envoy | Envoy admin port \(commands/diagnostics\) |
| 15001 | TCP | Envoy | Envoy Outbound |
| 15006 | TCP | Envoy | Envoy Inbound |
| 15008 | TCP | Envoy | Envoy Tunnel port \(Inbound\) |
| 15020 | HTTP | Envoy | Istio agent Prometheus telemetry |
| 15021 | HTTP | Envoy | Health checks |
| 15090 | HTTP | Envoy | Envoy Prometheus telemetry |
| 15010 | GRPC | Istiod | XDS and CA services \(plaintext\) |
| 15012 | GRPC | Istiod | XDS and CA services \(TLS\) |
| 8080 | HTTP | Istiod | Debug interface |
| 443 | HTTPS | Istiod | Webhooks |
| 15014 | HTTP | Mixer, Istiod | Control plane monitoring |
| 15443 | TLS | Ingress and Egress Gateways | SNI |
| 9090 | HTTP | Prometheus | Prometheus |
| 42422 | TCP | Mixer | Telemetry - Prometheus |
| 15004 | HTTP | Mixer, Pilot | Policy/Telemetry - `mTLS` |
| 9091 | HTTP | Mixer | Policy/Telemetry |

