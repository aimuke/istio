# 端口占用情况

## 端口使用列表

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

## sidecar 中端口

```bash
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:15021           0.0.0.0:*               LISTEN      12/envoy
tcp        0      0 0.0.0.0:15090           0.0.0.0:*               LISTEN      12/envoy
tcp        0      0 127.0.0.1:15000         0.0.0.0:*               LISTEN      12/envoy
tcp        0      0 0.0.0.0:15001           0.0.0.0:*               LISTEN      12/envoy
tcp        0      0 0.0.0.0:15006           0.0.0.0:*               LISTEN      12/envoy
tcp6       0      0 :::15020                :::*                    LISTEN      1/pilot-agent

```

如上为sidecar中使用的所有端口

### envoy

#### 15000

envoy的管理端口，监听在本地ip上，不能从外部进行访问，提供类似 config\_dump， clusters 等服务

#### 15001 15006

15001 接收 iptable 转发过来的出向流量

15006 接收iptable 拦截的入向流量

#### 15021

提供对外的 /healthz/ready 服务，接收到请求后直接转发给 pilot-agent 的 15020 端口进行处理

#### 15090

envoy 上报性能指标端口，提供 /stats/prometheus 服务，上报envoy 中的性能指标。该服务直接将请求转发给 15000 的 /stats/prometheus 服务进行处理。

对外暴露15090 端口的原因应该是 15000 端口不能从外部访问，prometheus 无法获取对应的数据。

### pilot-agent

pilot-agent 只监听了一个端口 15020，该端口监听在 :::15020 端口上，可以从容器或pod 外部进行访问，主要提供了两个功能：

* 接收envoy 转发过来的 /healthz/ready 的访问请求
* 接收prometheus 的数据抓取请求，根据配置是否需要进行合并，先抓取 envoy 中 15090 端口的性能指标数据，在抓取应用的指标数据。根据是否合并来返回给prometheus



