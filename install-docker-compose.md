# install docker-compose

## 依赖环境

install-base.yaml

```text
# GENERATED FILE. Use with Docker-Compose and consul
# TO UPDATE, modify files in install/consul/templates and run install/updateVersion.sh
version: '2'
services:
  etcd:
    image: k8s.gcr.io/etcd:3.4.3-0
    networks:
      istiomesh:
        aliases:
          - etcd
    ports:
      - "4001:4001"
      - "2380:2380"
      - "2379:2379"
    environment:
      - SERVICE_IGNORE=1
    command: ["/usr/local/bin/etcd", "-advertise-client-urls=http://0.0.0.0:2379", "-listen-client-urls=http://0.0.0.0:2379"]

  istio-apiserver:
    image: k8s.gcr.io/kube-apiserver:v1.18.0
    networks:
      istiomesh:
        ipv4_address: 172.28.0.13
        aliases:
          - apiserver
    ports:
      - "8080:8080"
    privileged: true
    environment:
      - SERVICE_IGNORE=1
    command: ["kube-apiserver", "--etcd-servers", "http://etcd:2379", "--service-cluster-ip-range", "10.99.0.0/16", "--insecure-port", "8080", "-v", "2", "--insecure-bind-address", "0.0.0.0"]

  consul:
    image: consul:latest
    networks:
      istiomesh:
        aliases:
          - consul
    ports:
      - "8500:8500"
      - "${DOCKER_GATEWAY}53:8600/udp"
      - "8400:8400"
      - "8502:8502"
    environment:
      - 'CONSUL_LOCAL_CONFIG={"skip_leave_on_interrupt": true}'
    command: ["agent", "-server", "-bootstrap-expect=1", "-client", "0.0.0.0", "-ui"]

  registrator:
    image: gliderlabs/registrator:master
    networks:
      istiomesh:
    volumes:
      - /var/run/docker.sock:/tmp/docker.sock
    command: ["-internal", "-retry-attempts=-1", "consul://consul:8500"]

networks:
  istiomesh:
    ipam:
      driver: default
      config:
        - subnet: 172.28.0.0/16
          gateway: 172.28.0.1

```

##  istio脚本

install.yaml

```text
# GENERATED FILE. Use with Docker-Compose and consul
# TO UPDATE, modify files in install/consul/templates and run install/updateVersion.sh
version: '2'
services:
  pilot:
    image: istio/pilot:1.7.0
    networks:
      istiomesh:
        aliases:
          - istio-pilot
    ports:
      - "15012:15012"
      - "9093:15014"
    environment:
      - ENABLE_CA_SERVER=false
    command: ["discovery", "--monitoringAddr", ":15014", "--log_output_level", "default:info", "--domain", "cluster.local", "--trust-domain", "cluster.local", "--kubeconfig", "/etc/istio/config/kubeconfig", "--keepaliveMaxServerConnectionAge", "30m","--meshConfig", "/etc/istio/config/mesh", "--networksConfig", "/etc/istio/config/meshNetworks" ]
    volumes:
      - ./conf:/etc/istio/config

networks:
  istiomesh:
    external:
      name: base_istiomesh

```

## 安装脚本 up

```text
export DOCKER_GATEWAY=172.28.0.1:

docker-compose -f install-base.yaml -p base ${1} up -d
echo base is ready
echo "============================================================"


sleep 5s

./kubectl create namespace istio-system --kubeconfig ./conf/kubeconfig

docker-compose -f install.yaml ${1} -p istio up -d

```

## 卸载脚本 down

```text
export DOCKER_GATEWAY=172.28.0.1:

docker-compose -f install.yaml -p istio down

sleep 2
echo istio is down
echo "============================================================"

docker-compose -f install-base.yaml -p base down

```



## 安装bookinfo

### yaml

bookinfo.yaml

```text
# Copyright 2017 Istio Authors
#
#   Licensed under the Apache License, Version 2.0 (the "License");
#   you may not use this file except in compliance with the License.
#   You may obtain a copy of the License at
#
#       http://www.apache.org/licenses/LICENSE-2.0
#
#   Unless required by applicable law or agreed to in writing, software
#   distributed under the License is distributed on an "AS IS" BASIS,
#   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
#   See the License for the specific language governing permissions and
#   limitations under the License.

############################################################################
version: '2'
services:
  details-v1:
    image: istio/examples-bookinfo-details-v1:1.8.0
    networks:
      istiomesh:
    dns:
      - 172.28.0.1
      - 8.8.8.8
    dns_search:
        - service.consul
    environment:
      - SERVICE_NAME=details
      - SERVICE_TAGS=version|v1
      - SERVICE_PROTOCOL=http
    expose:
      - "9080"

  ratings-v1:
    image: istio/examples-bookinfo-ratings-v1:1.8.0
    networks:
      istiomesh:
    dns:
      - 172.28.0.1
      - 8.8.8.8
    dns_search:
        - service.consul
    environment:
      - SERVICE_NAME=ratings
      - SERVICE_TAGS=version|v1
      - SERVICE_PROTOCOL=http
    expose:
      - "9080"

  reviews-v1:
    image: istio/examples-bookinfo-reviews-v1:1.8.0
    networks:
      istiomesh:
    dns:
      - 172.28.0.1
      - 8.8.8.8
    dns_search:
        - service.consul
    environment:
      - SERVICE_9080_NAME=reviews
      - SERVICE_TAGS=version|v1
      - SERVICE_PROTOCOL=http
      - SERVICE_9443_IGNORE=1
    expose:
      - "9080"

  reviews-v2:
    image: istio/examples-bookinfo-reviews-v2:1.8.0
    networks:
      istiomesh:
    dns:
      - 172.28.0.1
      - 8.8.8.8
    dns_search:
        - service.consul
    environment:
      - SERVICE_9080_NAME=reviews
      - SERVICE_TAGS=version|v2
      - SERVICE_PROTOCOL=http
      - SERVICE_9443_IGNORE=1
    expose:
      - "9080"

  reviews-v3:
    image: istio/examples-bookinfo-reviews-v3:1.8.0
    networks:
      istiomesh:
    dns:
      - 172.28.0.1
      - 8.8.8.8
    dns_search:
        - service.consul
    environment:
      - SERVICE_9080_NAME=reviews
      - SERVICE_TAGS=version|v3
      - SERVICE_PROTOCOL=http
      - SERVICE_9443_IGNORE=1
    expose:
      - "9080"

  productpage-v1:
    image: istio/examples-bookinfo-productpage-v1:1.8.0
    networks:
      istiomesh:
        ipv4_address: 172.28.0.14
    dns:
      - 172.28.0.1
      - 8.8.8.8
    dns_search:
        - service.consul
    environment:
      - SERVICE_NAME=productpage
      - SERVICE_TAGS=version|v1
      - SERVICE_PROTOCOL=http
    ports:
      - "9081:9080"
    expose:
      - "9080"
networks:
  istiomesh:
    external:
      name: base_istiomesh


```

### script

```text
docker-compose -p consul -f bookinfo.yaml up -d
```

### test

```text
export GATEWAY_URL=localhost:9081
curl -o /dev/null -s -w "%{http_code}\n" http://${GATEWAY_URL}/productpage
```

## 安装sidecar

### inject yaml

```text
$ cat inject-sidecar.yaml
filename=bookinfo.sidecar.yaml

createYaml() {
service=${1}
cat>>"${filename}"<<EOF
  ${service}-init:
    image: istio/proxyv2:1.7.1
    cap_add:
      - NET_ADMIN
      - NET_RAW
    network_mode: "container:consul_${service}_1"
    command:
      - istio-iptables
      - -p
      - "15001"
      - -u
      - "1337"
      - -z
      - "15006"
      - -m
      - REDIRECT
      - -i
      - "*"
      - -x
      - ""
      - -b
      - "*"
      - -d
      - 15090,15021,15020
  ${service}-sidecar:
    image: istio/proxyv2:1.7.1
    network_mode: "container:consul_${service}_1"
    command:
      - proxy
      - sidecar
      - --domain
      - default.svc.cluster.local
      - --serviceCluster
      - ${service}
      - --proxyLogLevel=debug
      - --proxyComponentLogLevel=misc:error
      - --trust-domain=cluster.local
      - --concurrency
      - "1"

EOF

}

cat>${filename}<<EOF
version: '2'
services:
EOF

createYaml "details-v1"
createYaml "ratings-v1"
createYaml "productpage-v1"
createYaml "reviews-v1"
createYaml "reviews-v2"
createYaml "reviews-v3"

cat ${filename}

```

### 安装

```text
$ cat sidecar-up.sh
docker-compose -f bookinfo.sidecar.yaml -p consul up -d
```

### 卸载

```text
# cat sidecar-down.sh
docker-compose -f bookinfo.sidecar.yaml -p consul down
```

