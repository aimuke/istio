---
description: 数据面应用相关信息
---

# data plane

应用相关数据

## netstat 

```bash
node01 $ docker exec -ti  a7223bdca526 /bin/bash
istio-proxy@details-v1-558b8b4b76-6b68x:/$ netstat -anp
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:15021           0.0.0.0:*               LISTEN      12/envoy
tcp        0      0 0.0.0.0:15090           0.0.0.0:*               LISTEN      12/envoy
tcp        0      0 127.0.0.1:15000         0.0.0.0:*               LISTEN      12/envoy
tcp        0      0 0.0.0.0:9080            0.0.0.0:*               LISTEN      -
tcp        0      0 0.0.0.0:15001           0.0.0.0:*               LISTEN      12/envoy
tcp        0      0 0.0.0.0:15006           0.0.0.0:*               LISTEN      12/envoy
tcp        0      0 127.0.0.1:15000         127.0.0.1:52040         ESTABLISHED 12/envoy
tcp        0      0 127.0.0.1:37624         127.0.0.1:15020         ESTABLISHED 12/envoy
tcp        0      0 10.244.1.6:41510        10.103.250.226:15012    ESTABLISHED 1/pilot-agent
tcp        0      0 127.0.0.1:52040         127.0.0.1:15000         ESTABLISHED 1/pilot-agent
tcp        0      0 127.0.0.1:37594         127.0.0.1:15020         ESTABLISHED 12/envoy
tcp        0      0 10.244.1.6:41520        10.103.250.226:15012    ESTABLISHED 12/envoy
tcp6       0      0 :::15020                :::*                    LISTEN      1/pilot-agent
tcp6       0      0 :::9080                 :::*                    LISTEN      -
tcp6       0      0 127.0.0.1:15020         127.0.0.1:37594         ESTABLISHED 1/pilot-agent
tcp6       0      0 127.0.0.1:15020         127.0.0.1:37624         ESTABLISHED 1/pilot-agent
Active UNIX domain sockets (servers and established)
Proto RefCnt Flags       Type       State         I-Node   PID/Program name     Path
unix  2      [ ACC ]     STREAM     LISTENING     72710    1/pilot-agent        ./etc/istio/proxy/SDS
unix  2      [ ]         DGRAM                    72837    12/envoy             @envoy_domain_socket_parent_0@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
unix  2      [ ]         DGRAM                    72836    12/envoy             @envoy_domain_socket_child_0@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
unix  3      [ ]         STREAM     CONNECTED     71339    12/envoy
unix  3      [ ]         STREAM     CONNECTED     72914    1/pilot-agent        ./etc/istio/proxy/SDS
```

## ps -ef 

```text
node01 $ docker exec fd7b63703fc0 ps -ef > ps-pilot.data
node01 $ cat ps-pilot.data
UID        PID  PPID  C STIME TTY          TIME CMD
istio-p+     1     0  0 09:05 ?        00:00:07 /usr/local/bin/pilot-discovery discovery --monitoringAddr=:15014 --log_output_level=default:info --domain cluster.local --trust-domain=cluster.local --keepaliveMaxServerConnectionAge 30m
istio-p+    19     0  0 09:31 ?        00:00:00 ps -ef
```

## pod info

```text
kubectl get pod ratings-v1-7dc98c7588-gsz9h -o yaml
apiVersion: v1
kind: Pod
metadata:
  annotations:
    prometheus.io/path: /stats/prometheus
    prometheus.io/port: "15020"
    prometheus.io/scrape: "true"
    sidecar.istio.io/status: '{"version":"8e6e902b765af607513b28d284940ee1421e9a0d07698741693b2663c7161c11","initContainers":["istio-init"],"containers":["istio-proxy"],"volumes":["istio-envoy","istio-data","istio-podinfo","istiod-ca-cert"],"imagePullSecrets":null}'
  creationTimestamp: "2021-01-11T08:22:33Z"
  generateName: ratings-v1-7dc98c7588-
  labels:
    app: ratings
    istio.io/rev: default
    pod-template-hash: 7dc98c7588
    security.istio.io/tlsMode: istio
    service.istio.io/canonical-name: ratings
    service.istio.io/canonical-revision: v1
    version: v1
  managedFields:
  - apiVersion: v1
    fieldsType: FieldsV1
    fieldsV1:
      f:metadata:
        f:generateName: {}
        f:labels:
          .: {}
          f:app: {}
          f:pod-template-hash: {}
          f:version: {}
        f:ownerReferences:
          .: {}
          k:{"uid":"9bd14019-43b8-48d1-a9e0-1269e0d8100f"}:
            .: {}
            f:apiVersion: {}
            f:blockOwnerDeletion: {}
            f:controller: {}
            f:kind: {}
            f:name: {}
            f:uid: {}
      f:spec:
        f:containers:
          k:{"name":"ratings"}:
            .: {}
            f:image: {}
            f:imagePullPolicy: {}
            f:name: {}
            f:ports:
              .: {}
              k:{"containerPort":9080,"protocol":"TCP"}:
                .: {}
                f:containerPort: {}
                f:protocol: {}
            f:resources: {}
            f:terminationMessagePath: {}
            f:terminationMessagePolicy: {}
        f:dnsPolicy: {}
        f:enableServiceLinks: {}
        f:restartPolicy: {}
        f:schedulerName: {}
        f:securityContext: {}
        f:serviceAccount: {}
        f:serviceAccountName: {}
        f:terminationGracePeriodSeconds: {}
    manager: kube-controller-manager
    operation: Update
    time: "2021-01-11T08:22:29Z"
  - apiVersion: v1
    fieldsType: FieldsV1
    fieldsV1:
      f:status:
        f:conditions:
          k:{"type":"ContainersReady"}:
            .: {}
            f:lastProbeTime: {}
            f:lastTransitionTime: {}
            f:message: {}
            f:reason: {}
            f:status: {}
            f:type: {}
          k:{"type":"Initialized"}:
            .: {}
            f:lastProbeTime: {}
            f:lastTransitionTime: {}
            f:message: {}
            f:reason: {}
            f:status: {}
            f:type: {}
          k:{"type":"Ready"}:
            .: {}
            f:lastProbeTime: {}
            f:lastTransitionTime: {}
            f:message: {}
            f:reason: {}
            f:status: {}
            f:type: {}
        f:containerStatuses: {}
        f:hostIP: {}
        f:initContainerStatuses: {}
        f:startTime: {}
    manager: kubelet
    operation: Update
    time: "2021-01-11T08:22:49Z"
  name: ratings-v1-7dc98c7588-gsz9h
  namespace: default
  ownerReferences:
  - apiVersion: apps/v1
    blockOwnerDeletion: true
    controller: true
    kind: ReplicaSet
    name: ratings-v1-7dc98c7588
    uid: 9bd14019-43b8-48d1-a9e0-1269e0d8100f
  resourceVersion: "2911"
  selfLink: /api/v1/namespaces/default/pods/ratings-v1-7dc98c7588-gsz9h
  uid: a9aca555-6f87-4c62-9928-05c1883ff6b9
spec:
  containers:
  - image: docker.io/istio/examples-bookinfo-ratings-v1:1.16.2
    imagePullPolicy: IfNotPresent
    name: ratings
    ports:
    - containerPort: 9080
      protocol: TCP
    resources: {}
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: bookinfo-ratings-token-66z9r
      readOnly: true
  - args:
    - proxy
    - sidecar
    - --domain
    - $(POD_NAMESPACE).svc.cluster.local
    - --serviceCluster
    - ratings.$(POD_NAMESPACE)
    - --proxyLogLevel=warning
    - --proxyComponentLogLevel=misc:error
    - --trust-domain=cluster.local
    - --concurrency
    - "2"
    env:
    - name: JWT_POLICY
      value: first-party-jwt
    - name: PILOT_CERT_PROVIDER
      value: istiod
    - name: CA_ADDR
      value: istiod.istio-system.svc:15012
    - name: POD_NAME
      valueFrom:
        fieldRef:
          apiVersion: v1
          fieldPath: metadata.name
    - name: POD_NAMESPACE
      valueFrom:
        fieldRef:
          apiVersion: v1
          fieldPath: metadata.namespace
    - name: INSTANCE_IP
      valueFrom:
        fieldRef:
          apiVersion: v1
          fieldPath: status.podIP
    - name: SERVICE_ACCOUNT
      valueFrom:
        fieldRef:
          apiVersion: v1
          fieldPath: spec.serviceAccountName
    - name: HOST_IP
      valueFrom:
        fieldRef:
          apiVersion: v1
          fieldPath: status.hostIP
    - name: CANONICAL_SERVICE
      valueFrom:
        fieldRef:
          apiVersion: v1
          fieldPath: metadata.labels['service.istio.io/canonical-name']
    - name: CANONICAL_REVISION
      valueFrom:
        fieldRef:
          apiVersion: v1
          fieldPath: metadata.labels['service.istio.io/canonical-revision']
    - name: PROXY_CONFIG
      value: |
        {"proxyMetadata":{"DNS_AGENT":""}}
    - name: ISTIO_META_POD_PORTS
      value: |-
        [
            {"containerPort":9080,"protocol":"TCP"}
        ]
    - name: ISTIO_META_APP_CONTAINERS
      value: ratings
    - name: ISTIO_META_CLUSTER_ID
      value: Kubernetes
    - name: ISTIO_META_INTERCEPTION_MODE
      value: REDIRECT
    - name: ISTIO_META_WORKLOAD_NAME
      value: ratings-v1
    - name: ISTIO_META_OWNER
      value: kubernetes://apis/apps/v1/namespaces/default/deployments/ratings-v1
    - name: ISTIO_META_MESH_ID
      value: cluster.local
    - name: DNS_AGENT
    - name: ISTIO_KUBE_APP_PROBERS
      value: '{}'
    image: docker.io/istio/proxyv2:1.7.1
    imagePullPolicy: Always
    name: istio-proxy
    ports:
    - containerPort: 15090
      name: http-envoy-prom
      protocol: TCP
    readinessProbe:
      failureThreshold: 30
      httpGet:
        path: /healthz/ready
        port: 15021
        scheme: HTTP
      initialDelaySeconds: 1
      periodSeconds: 2
      successThreshold: 1
      timeoutSeconds: 1
    resources:
      limits:
        cpu: "2"
        memory: 1Gi
      requests:
        cpu: 10m
        memory: 40Mi
    securityContext:
      allowPrivilegeEscalation: false
      capabilities:
        drop:
        - ALL
      privileged: false
      readOnlyRootFilesystem: true
      runAsGroup: 1337
      runAsNonRoot: true
      runAsUser: 1337
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /var/run/secrets/istio
      name: istiod-ca-cert
    - mountPath: /var/lib/istio/data
      name: istio-data
    - mountPath: /etc/istio/proxy
      name: istio-envoy
    - mountPath: /etc/istio/pod
      name: istio-podinfo
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: bookinfo-ratings-token-66z9r
      readOnly: true
  dnsPolicy: ClusterFirst
  enableServiceLinks: true
  initContainers:
  - args:
    - istio-iptables
    - -p
    - "15001"
    - -z
    - "15006"
    - -u
    - "1337"
    - -m
    - REDIRECT
    - -i
    - '*'
    - -x
    - ""
    - -b
    - '*'
    - -d
    - 15090,15021,15020
    env:
    - name: DNS_AGENT
    image: docker.io/istio/proxyv2:1.7.1
    imagePullPolicy: Always
    name: istio-init
    resources:
      limits:
        cpu: "2"
        memory: 1Gi
      requests:
        cpu: 10m
        memory: 10Mi
    securityContext:
      allowPrivilegeEscalation: false
      capabilities:
        add:
        - NET_ADMIN
        - NET_RAW
        drop:
        - ALL
      privileged: false
      readOnlyRootFilesystem: false
      runAsGroup: 0
      runAsNonRoot: false
      runAsUser: 0
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: bookinfo-ratings-token-66z9r
      readOnly: true
  nodeName: node01
  priority: 0
  restartPolicy: Always
  schedulerName: default-scheduler
  securityContext:
    fsGroup: 1337
  serviceAccount: bookinfo-ratings
  serviceAccountName: bookinfo-ratings
  terminationGracePeriodSeconds: 30
  tolerations:
  - effect: NoExecute
    key: node.kubernetes.io/not-ready
    operator: Exists
    tolerationSeconds: 300
  - effect: NoExecute
    key: node.kubernetes.io/unreachable
    operator: Exists
    tolerationSeconds: 300
  volumes:
  - name: bookinfo-ratings-token-66z9r
    secret:
      defaultMode: 420
      secretName: bookinfo-ratings-token-66z9r
  - emptyDir:
      medium: Memory
    name: istio-envoy
  - emptyDir: {}
    name: istio-data
  - downwardAPI:
      defaultMode: 420
      items:
      - fieldRef:
          apiVersion: v1
          fieldPath: metadata.labels
        path: labels
      - fieldRef:
          apiVersion: v1
          fieldPath: metadata.annotations
        path: annotations
    name: istio-podinfo
  - configMap:
      defaultMode: 420
      name: istio-ca-root-cert
    name: istiod-ca-cert
status:
  conditions:
  - lastProbeTime: null
    lastTransitionTime: "2021-01-11T08:22:49Z"
    message: 'containers with incomplete status: [istio-init]'
    reason: ContainersNotInitialized
    status: "False"
    type: Initialized
  - lastProbeTime: null
    lastTransitionTime: "2021-01-11T08:22:49Z"
    message: 'containers with unready status: [ratings istio-proxy]'
    reason: ContainersNotReady
    status: "False"
    type: Ready
  - lastProbeTime: null
    lastTransitionTime: "2021-01-11T08:22:49Z"
    message: 'containers with unready status: [ratings istio-proxy]'
    reason: ContainersNotReady
    status: "False"
    type: ContainersReady
  - lastProbeTime: null
    lastTransitionTime: "2021-01-11T08:22:33Z"
    status: "True"
    type: PodScheduled
  containerStatuses:
  - image: docker.io/istio/proxyv2:1.7.1
    imageID: ""
    lastState: {}
    name: istio-proxy
    ready: false
    restartCount: 0
    started: false
    state:
      waiting:
        reason: PodInitializing
  - image: docker.io/istio/examples-bookinfo-ratings-v1:1.16.2
    imageID: ""
    lastState: {}
    name: ratings
    ready: false
    restartCount: 0
    started: false
    state:
      waiting:
        reason: PodInitializing
  hostIP: 172.17.0.18
  initContainerStatuses:
  - image: docker.io/istio/proxyv2:1.7.1
    imageID: ""
    lastState: {}
    name: istio-init
    ready: false
    restartCount: 0
    state:
      waiting:
        reason: PodInitializing
  phase: Pending
  qosClass: Burstable
  startTime: "2021-01-11T08:22:49Z"
```

## envoy-rev0.json

```text
istio-proxy@details-v1-558b8b4b76-6b68x:/$ cat /etc/istio/proxy/envoy-rev0.json
{
  "node": {
    "id": "sidecar~10.244.1.6~details-v1-558b8b4b76-6b68x.default~default.svc.cluster.local",
    "cluster": "details.default",
    "locality": {
    },
    "metadata": {"APP_CONTAINERS":"details","CLUSTER_ID":"Kubernetes","EXCHANGE_KEYS":"NAME,NAMESPACE,INSTANCE_IPS,LABELS,OWNER,PLATFORM_METADATA,WORKLOAD_NAME,MESH_ID,SERVICE_ACCOUNT,CLUSTER_ID","INSTANCE_IPS":"10.244.1.6","INTERCEPTION_MODE":"REDIRECT","ISTIO_PROXY_SHA":"istio-proxy:262253d9d066f8ef7ed82fd175c28b8f95acbec0","ISTIO_VERSION":"1.7.1","LABELS":{"app":"details","istio.io/rev":"default","pod-template-hash":"558b8b4b76","security.istio.io/tlsMode":"istio","service.istio.io/canonical-name":"details","service.istio.io/canonical-revision":"v1","version":"v1"},"MESH_ID":"cluster.local","NAME":"details-v1-558b8b4b76-6b68x","NAMESPACE":"default","OWNER":"kubernetes://apis/apps/v1/namespaces/default/deployments/details-v1","POD_PORTS":"[{\"containerPort\":9080,\"protocol\":\"TCP\"}]","PROXY_CONFIG":{"binaryPath":"/usr/local/bin/envoy","concurrency":2,"configPath":"./etc/istio/proxy","controlPlaneAuthPolicy":"MUTUAL_TLS","discoveryAddress":"istiod.istio-system.svc:15012","drainDuration":"45s","envoyAccessLogService":{},"envoyMetricsService":{},"parentShutdownDuration":"60s","proxyAdminPort":15000,"proxyMetadata":{"DNS_AGENT":""},"serviceCluster":"details.default","statNameLength":189,"statusPort":15020,"terminationDrainDuration":"5s","tracing":{"zipkin":{"address":"zipkin.istio-system:9411"}}},"SDS":"true","SERVICE_ACCOUNT":"bookinfo-details","WORKLOAD_NAME":"details-v1"}
  },
  "layered_runtime": {
      "layers": [
          {
              "name": "deprecation",
              "static_layer": {
                  "envoy.deprecated_features:envoy.config.listener.v3.Listener.hidden_envoy_deprecated_use_original_dst": true
              }
          },
          {
              "name": "admin",
              "admin_layer": {}
          }
      ]
  },
  "stats_config": {
    "use_all_default_tags": false,
    "stats_tags": [
      {
        "tag_name": "cluster_name",
        "regex": "^cluster\\.((.+?(\\..+?\\.svc\\.cluster\\.local)?)\\.)"
      },
      {
        "tag_name": "tcp_prefix",
        "regex": "^tcp\\.((.*?)\\.)\\w+?$"
      },
      {
        "regex": "(response_code=\\.=(.+?);\\.;)|_rq(_(\\.d{3}))$",
        "tag_name": "response_code"
      },
      {
        "tag_name": "response_code_class",
        "regex": "_rq(_(\\dxx))$"
      },
      {
        "tag_name": "http_conn_manager_listener_prefix",
        "regex": "^listener(?=\\.).*?\\.http\\.(((?:[_.[:digit:]]*|[_\\[\\]aAbBcCdDeEfF[:digit:]]*))\\.)"
      },
      {
        "tag_name": "http_conn_manager_prefix",
        "regex": "^http\\.(((?:[_.[:digit:]]*|[_\\[\\]aAbBcCdDeEfF[:digit:]]*))\\.)"
      },
      {
        "tag_name": "listener_address",
        "regex": "^listener\\.(((?:[_.[:digit:]]*|[_\\[\\]aAbBcCdDeEfF[:digit:]]*))\\.)"
      },
      {
        "tag_name": "mongo_prefix",
        "regex": "^mongo\\.(.+?)\\.(collection|cmd|cx_|op_|delays_|decoding_)(.*?)$"
      },
      {
        "regex": "(reporter=\\.=(.*?);\\.;)",
        "tag_name": "reporter"
      },
      {
        "regex": "(source_namespace=\\.=(.*?);\\.;)",
        "tag_name": "source_namespace"
      },
      {
        "regex": "(source_workload=\\.=(.*?);\\.;)",
        "tag_name": "source_workload"
      },
      {
        "regex": "(source_workload_namespace=\\.=(.*?);\\.;)",
        "tag_name": "source_workload_namespace"
      },
      {
        "regex": "(source_principal=\\.=(.*?);\\.;)",
        "tag_name": "source_principal"
      },
      {
        "regex": "(source_app=\\.=(.*?);\\.;)",
        "tag_name": "source_app"
      },
      {
        "regex": "(source_version=\\.=(.*?);\\.;)",
        "tag_name": "source_version"
      },
      {
        "regex": "(source_cluster=\\.=(.*?);\\.;)",
        "tag_name": "source_cluster"
      },
      {
        "regex": "(destination_namespace=\\.=(.*?);\\.;)",
        "tag_name": "destination_namespace"
      },
      {
        "regex": "(destination_workload=\\.=(.*?);\\.;)",
        "tag_name": "destination_workload"
      },
      {
        "regex": "(destination_workload_namespace=\\.=(.*?);\\.;)",
        "tag_name": "destination_workload_namespace"
      },
      {
        "regex": "(destination_principal=\\.=(.*?);\\.;)",
        "tag_name": "destination_principal"
      },
      {
        "regex": "(destination_app=\\.=(.*?);\\.;)",
        "tag_name": "destination_app"
      },
      {
        "regex": "(destination_version=\\.=(.*?);\\.;)",
        "tag_name": "destination_version"
      },
      {
        "regex": "(destination_service=\\.=(.*?);\\.;)",
        "tag_name": "destination_service"
      },
      {
        "regex": "(destination_service_name=\\.=(.*?);\\.;)",
        "tag_name": "destination_service_name"
      },
      {
        "regex": "(destination_service_namespace=\\.=(.*?);\\.;)",
        "tag_name": "destination_service_namespace"
      },
      {
        "regex": "(destination_port=\\.=(.*?);\\.;)",
        "tag_name": "destination_port"
      },
      {
        "regex": "(destination_cluster=\\.=(.*?);\\.;)",
        "tag_name": "destination_cluster"
      },
      {
        "regex": "(request_protocol=\\.=(.*?);\\.;)",
        "tag_name": "request_protocol"
      },
      {
        "regex": "(request_operation=\\.=(.*?);\\.;)",
        "tag_name": "request_operation"
      },
      {
        "regex": "(request_host=\\.=(.*?);\\.;)",
        "tag_name": "request_host"
      },
      {
        "regex": "(response_flags=\\.=(.*?);\\.;)",
        "tag_name": "response_flags"
      },
      {
        "regex": "(grpc_response_status=\\.=(.*?);\\.;)",
        "tag_name": "grpc_response_status"
      },
      {
        "regex": "(connection_security_policy=\\.=(.*?);\\.;)",
        "tag_name": "connection_security_policy"
      },
      {
        "regex": "(permissive_response_code=\\.=(.*?);\\.;)",
        "tag_name": "permissive_response_code"
      },
      {
        "regex": "(permissive_response_policyid=\\.=(.*?);\\.;)",
        "tag_name": "permissive_response_policyid"
      },
      {
        "regex": "(source_canonical_service=\\.=(.*?);\\.;)",
        "tag_name": "source_canonical_service"
      },
      {
        "regex": "(destination_canonical_service=\\.=(.*?);\\.;)",
        "tag_name": "destination_canonical_service"
      },
      {
        "regex": "(source_canonical_revision=\\.=(.*?);\\.;)",
        "tag_name": "source_canonical_revision"
      },
      {
        "regex": "(destination_canonical_revision=\\.=(.*?);\\.;)",
        "tag_name": "destination_canonical_revision"
      },
      {
        "regex": "(cache\\.(.+?)\\.)",
        "tag_name": "cache"
      },
      {
        "regex": "(component\\.(.+?)\\.)",
        "tag_name": "component"
      },
      {
        "regex": "(tag\\.(.+?);\\.)",
        "tag_name": "tag"
      },
      {
        "regex": "(wasm_filter\\.(.+?)\\.)",
        "tag_name": "wasm_filter"
      }
    ],
    "stats_matcher": {
      "inclusion_list": {
        "patterns": [
          {
          "prefix": "reporter="
          },
          {
          "prefix": "cluster_manager"
          },
          {
          "prefix": "listener_manager"
          },
          {
          "prefix": "http_mixer_filter"
          },
          {
          "prefix": "tcp_mixer_filter"
          },
          {
          "prefix": "server"
          },
          {
          "prefix": "cluster.xds-grpc"
          },
          {
          "prefix": "wasm"
          },
          {
          "prefix": "component"
          }
        ]
      }
    }
  },
  "admin": {
    "access_log_path": "/dev/null",
    "profile_path": "/var/lib/istio/data/envoy.prof",
    "address": {
      "socket_address": {
        "address": "127.0.0.1",
        "port_value": 15000
      }
    }
  },
  "dynamic_resources": {
    "lds_config": {
      "ads": {},
      "resource_api_version": "V3"
    },
    "cds_config": {
      "ads": {},
      "resource_api_version": "V3"
    },
    "ads_config": {
      "api_type": "GRPC",
      "transport_api_version": "V3",
      "grpc_services": [
        {
          "envoy_grpc": {
            "cluster_name": "xds-grpc"
          }
        }
      ]
    }
  },
  "static_resources": {
    "clusters": [
      {
        "name": "prometheus_stats",
        "type": "STATIC",
        "connect_timeout": "0.250s",
        "lb_policy": "ROUND_ROBIN",
        "load_assignment": {
          "cluster_name": "prometheus_stats",
          "endpoints": [{
            "lb_endpoints": [{
              "endpoint": {
                "address":{
                  "socket_address": {
                    "protocol": "TCP",
                    "address": "127.0.0.1",
                    "port_value": 15000
                  }
                }
              }
            }]
          }]
        }
      },
      {
        "name": "agent",
        "type": "STATIC",
        "connect_timeout": "0.250s",
        "lb_policy": "ROUND_ROBIN",
        "load_assignment": {
          "cluster_name": "prometheus_stats",
          "endpoints": [{
            "lb_endpoints": [{
              "endpoint": {
                "address":{
                  "socket_address": {
                    "protocol": "TCP",
                    "address": "127.0.0.1",
                    "port_value": 15020
                  }
                }
              }
            }]
          }]
        }
      },
      {
        "name": "sds-grpc",
        "type": "STATIC",
        "http2_protocol_options": {},
        "connect_timeout": "1s",
        "lb_policy": "ROUND_ROBIN",
        "load_assignment": {
          "cluster_name": "sds-grpc",
          "endpoints": [{
            "lb_endpoints": [{
              "endpoint": {
                "address":{
                  "pipe": {
                    "path": "./etc/istio/proxy/SDS"
                  }
                }
              }
            }]
          }]
        }
      },
      {
        "name": "xds-grpc",
        "type": "STRICT_DNS",
        "respect_dns_ttl": true,
        "dns_lookup_family": "V4_ONLY",
        "connect_timeout": "1s",
        "lb_policy": "ROUND_ROBIN",
        "transport_socket": {
          "name": "envoy.transport_sockets.tls",
          "typed_config": {
            "@type": "type.googleapis.com/envoy.extensions.transport_sockets.tls.v3.UpstreamTlsContext",
            "sni": "istiod.istio-system.svc",
            "common_tls_context": {
              "alpn_protocols": [
                "h2"
              ],
              "tls_certificate_sds_secret_configs": [
                {
                  "name": "default",
                  "sds_config": {
                    "resource_api_version": "V3",
                    "initial_fetch_timeout": "0s",
                    "api_config_source": {
                      "api_type": "GRPC",
                      "transport_api_version": "V3",
                      "grpc_services": [
                        {
                          "envoy_grpc": { "cluster_name": "sds-grpc" }
                        }
                      ]
                    }
                  }
                }
              ],
              "validation_context": {
                "trusted_ca": {
                  "filename": "./var/run/secrets/istio/root-cert.pem"
                },
                "match_subject_alt_names": [{"exact":"istiod.istio-system.svc"}]
              }
            }
          }
        },
        "load_assignment": {
          "cluster_name": "xds-grpc",
          "endpoints": [{
            "lb_endpoints": [{
              "endpoint": {
                "address":{
                  "socket_address": {"address": "istiod.istio-system.svc", "port_value": 15012}
                }
              }
            }]
          }]
        },
        "circuit_breakers": {
          "thresholds": [
            {
              "priority": "DEFAULT",
              "max_connections": 100000,
              "max_pending_requests": 100000,
              "max_requests": 100000
            },
            {
              "priority": "HIGH",
              "max_connections": 100000,
              "max_pending_requests": 100000,
              "max_requests": 100000
            }
          ]
        },
        "upstream_connection_options": {
          "tcp_keepalive": {
            "keepalive_time": 300
          }
        },
        "max_requests_per_connection": 1,
        "http2_protocol_options": { }
      }

      ,
      {
        "name": "zipkin",
        "type": "STRICT_DNS",
        "respect_dns_ttl": true,
        "dns_lookup_family": "V4_ONLY",
        "connect_timeout": "1s",
        "lb_policy": "ROUND_ROBIN",
        "load_assignment": {
          "cluster_name": "zipkin",
          "endpoints": [{
            "lb_endpoints": [{
              "endpoint": {
                "address":{
                  "socket_address": {"address": "zipkin.istio-system", "port_value": 9411}
                }
              }
            }]
          }]
        }
      }


    ],
    "listeners":[
      {
        "address": {
          "socket_address": {
            "protocol": "TCP",
            "address": "0.0.0.0",
            "port_value": 15090
          }
        },
        "filter_chains": [
          {
            "filters": [
              {
                "name": "envoy.http_connection_manager",
                "typed_config": {
                  "@type": "type.googleapis.com/envoy.extensions.filters.network.http_connection_manager.v3.HttpConnectionManager",
                  "codec_type": "AUTO",
                  "stat_prefix": "stats",
                  "route_config": {
                    "virtual_hosts": [
                      {
                        "name": "backend",
                        "domains": [
                          "*"
                        ],
                        "routes": [
                          {
                            "match": {
                              "prefix": "/stats/prometheus"
                            },
                            "route": {
                              "cluster": "prometheus_stats"
                            }
                          }
                        ]
                      }
                    ]
                  },
                  "http_filters": [{
                    "name": "envoy.router",
                    "typed_config": {
                      "@type": "type.googleapis.com/envoy.extensions.filters.http.router.v3.Router"
                    }
                  }]
                }
              }
            ]
          }
        ]
      },
      {
        "address": {
          "socket_address": {
            "protocol": "TCP",
            "address": "0.0.0.0",
            "port_value": 15021
          }
        },
        "filter_chains": [
          {
            "filters": [
              {
                "name": "envoy.http_connection_manager",
                "typed_config": {
                  "@type": "type.googleapis.com/envoy.extensions.filters.network.http_connection_manager.v3.HttpConnectionManager",
                  "codec_type": "AUTO",
                  "stat_prefix": "agent",
                  "route_config": {
                    "virtual_hosts": [
                      {
                        "name": "backend",
                        "domains": [
                          "*"
                        ],
                        "routes": [
                          {
                            "match": {
                              "prefix": "/healthz/ready"
                            },
                            "route": {
                              "cluster": "agent"
                            }
                          }
                        ]
                      }
                    ]
                  },
                  "http_filters": [{
                    "name": "envoy.router",
                    "typed_config": {
                      "@type": "type.googleapis.com/envoy.extensions.filters.http.router.v3.Router"
                    }
                  }]
                }
              }
            ]
          }
        ]
      }
    ]
  }
  ,
  "tracing": {
    "http": {
      "name": "envoy.zipkin",
      "typed_config": {
        "@type": "type.googleapis.com/envoy.config.trace.v3.ZipkinConfig",
        "collector_cluster": "zipkin",
        "collector_endpoint": "/api/v2/spans",
        "collector_endpoint_version": "HTTP_JSON",
        "trace_id_128bit": true,
        "shared_span_context": false
      }
    }
  }


}
```





