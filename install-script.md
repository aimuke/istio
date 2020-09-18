# install script

## rm

```text
comp=${1}
echo comp is ${comp}

docker stop ${comp}
docker rm ${comp}
```

## etcd

```text
docker run -d --name etcd \
-p 4001:4001 \
-p 2380:2380 \
-p 2379:2379 \
-e 'SERVICE_IGNORE=1' \
k8s.gcr.io/etcd:3.4.3-0 \
/usr/local/bin/etcd -advertise-client-urls=http://0.0.0.0:2379 -listen-client-urls=http://0.0.0.0:2379

```

## apiserver

```text
docker run -d --name apiserver \
-p 8080:8080 \
-e 'SERVICE_IGNORE=1' \
k8s.gcr.io/kube-apiserver:v1.18.0 \
kube-apiserver --etcd-servers http://10.234.88.111:2379 --service-cluster-ip-range 10.99.0.0/16 --insecure-port 8080 -v 2 --insecure-bind-address  0.0.0.0

```

## consul

```text
docker run -d --name consul -p 8500:8500 -p 8400:8400 -p 8502:8502 -e'CONSUL_LOCAL_CONFIG={"skip_leave_on_interrupt": true}' consul agent -server -bootstrap-expect=1  -client 0.0.0.0 -ui
```

### data

```text
{
    "Node": "default-external",
    "Address": "127.0.0.1",
    "NodeMeta": {
        "external": "true",
        "ns": "default"
    },
    "Service": {
        "ID": "winery_null_default_198.150.0.1_1102",
        "Service": "winery",
        "Tags": [
            "\"ns\":{\"namespace\":\"default\"}",
            "\"update_time\":\"2020-08-28T09:31:59.873+0800\""
        ],
        "Address": "198.150.0.1",
        "Meta": {
            "enable_mutil_version": "true",
            "protocol": "http"
        },
        "Port": 1102
    }
}
```

### insert script

```text
curl \
    --request PUT \
    --data @data.json \
    http://127.0.0.1:8500/v1/catalog/register
```

## istiod

### docker

```text
echo create namespace istio-system
./kubectl delete namespaces istio-system --kubeconfig ./conf/kubeconfig
./kubectl create namespace istio-system --kubeconfig ./conf/kubeconfig


####################################################
#
# start pilot docker
#
####################################################

echo docker run...
docker run -d --name pilot \
-p 15012:15012 \
-p 9093:8080 \
-v /home/ubuntu/update/conf:/etc/istio/config \
-e 'SERVICE_IGNORE=1' \
-e 'ENABLE_CA_SERVER=false' \
istio/pilot:1.7.0 \
discovery \
--monitoringAddr=:15014 \
--log_output_level=default:info \
--domain cluster.local \
--trust-domain=cluster.local \
--keepaliveMaxServerConnectionAge 30m \
--kubeconfig /etc/istio/config/kubeconfig \
--meshConfig /etc/istio/config/mesh \
--networksConfig /etc/istio/config/meshNetworks



####################################################
#
# docker logs
#
####################################################
docker logs pilot

```

### mesh

```text
accessLogFile: /dev/stdout
defaultConfig:
  discoveryAddress: istiod.istio-system.svc:15012
  proxyMetadata:
    DNS_AGENT: ""
  tracing:
    zipkin:
      address: zipkin.istio-system:9411
disableMixerHttpReports: true
enablePrometheusMerge: true
rootNamespace: istio-system
trustDomain: cluster.local
configSources:
- address: 10.234.88.111:15098

```

### kubeconfig

```text
apiVersion: v1
clusters:
- cluster:
    server: http://10.234.88.111:8080
  name: istio
contexts:
- context:
    cluster: istio
    user: ""
  name: istio
current-context: istio

```



