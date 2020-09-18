---
description: isito 1.7 升级
---

# istio update

## install istio

### 下载

```text
master $ curl -L https://istio.io/downloadIstio | ISTIO_VERSION=1.6.8 TARGET_ARCH=x86_64 sh -
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   102  100   102    0     0    229      0 --:--:-- --:--:-- --:--:--   229
100  3888  100  3888    0     0   7593      0 --:--:-- --:--:-- --:--:--  7593
Downloading istio-1.6.8 from https://github.com/istio/istio/releases/download/1.6.8/istio-1.6.8-linux.tar.gz ...Failed.

Trying with TARGET_ARCH. Downloading istio-1.6.8 from https://github.com/istio/istio/releases/download/1.6.8/istio-1.6.8-linux-amd64.tar.gz ...

Istio 1.6.8 Download Complete!

Istio has been successfully downloaded into the istio-1.6.8 folder on your system.

Next Steps:
See https://istio.io/latest/docs/setup/install/ to add Istio to your Kubernetes cluster.

To configure the istioctl client tool for your workstation,
add the /root/bb/istio-1.6.8/bin directory to your environment path variable with:
         export PATH="$PATH:/root/bb/istio-1.6.8/bin"

Begin the Istio pre-installation verification check by running:
         istioctl verify-install

Need more information? Visit https://istio.io/latest/docs/setup/install/
```

安装前

```text
master $ kubectl get services -A
NAMESPACE     NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)                  AGE
default       kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP                  11s
kube-system   kube-dns     ClusterIP   10.96.0.10   <none>        53/UDP,53/TCP,9153/TCP   9s
```



```text
controlplane $ kubectl get pods -A
NAMESPACE     NAME                                      READY   STATUS    RESTARTS   AGE
kube-system   coredns-66bff467f8-gwdhc                  1/1     Running   0          38m
kube-system   coredns-66bff467f8-svxcc                  1/1     Running   0          38m
kube-system   etcd-controlplane                         1/1     Running   0          38m
kube-system   katacoda-cloud-provider-58f89f7d9-256gm   1/1     Running   13         38m
kube-system   kube-apiserver-controlplane               1/1     Running   0          38m
kube-system   kube-controller-manager-controlplane      1/1     Running   0          38m
kube-system   kube-flannel-ds-amd64-22xw6               1/1     Running   0          38m
kube-system   kube-flannel-ds-amd64-9jmqk               1/1     Running   0          38m
kube-system   kube-keepalived-vip-g7cq8                 1/1     Running   0          38m
kube-system   kube-proxy-llzzr                          1/1     Running   0          38m
kube-system   kube-proxy-rv64z                          1/1     Running   0          38m
kube-system   kube-scheduler-controlplane               1/1     Running   0          38m
controlplane $ kubectl get pods -A |  wc -l
13
```



master 节点

```text
controlplane $ docker ps -a
CONTAINER ID        IMAGE                    COMMAND                  CREATED             STATUS                      PORTS               NAMES
07ac084625c1        67da37a9a360             "/coredns -conf /etc…"   40 minutes ago      Up 40 minutes                                   k8s_coredns_coredns-66bff467f8-gwdhc_kube-system_903568a8-c67e-4e4d-ab82-315727316526_0
3e1715af23bc        67da37a9a360             "/coredns -conf /etc…"   40 minutes ago      Up 40 minutes                                   k8s_coredns_coredns-66bff467f8-svxcc_kube-system_55baa58d-2e44-4837-b0a6-3fb3f9670e22_0
c7db4a4abd93        k8s.gcr.io/pause:3.2     "/pause"                 40 minutes ago      Up 40 minutes                                   k8s_POD_coredns-66bff467f8-gwdhc_kube-system_903568a8-c67e-4e4d-ab82-315727316526_0
245e89e39a78        k8s.gcr.io/pause:3.2     "/pause"                 40 minutes ago      Up 40 minutes                                   k8s_POD_coredns-66bff467f8-svxcc_kube-system_55baa58d-2e44-4837-b0a6-3fb3f9670e22_0
7ab57ce69a89        4e9f801d2217             "/opt/bin/flanneld -…"   40 minutes ago      Up 40 minutes                                   k8s_kube-flannel_kube-flannel-ds-amd64-22xw6_kube-system_8081248d-0a3b-44e4-8e05-ce98756f3f0e_0
da7a53d35589        quay.io/coreos/flannel   "cp -f /etc/kube-fla…"   40 minutes ago      Exited (0) 40 minutes ago                       k8s_install-cni_kube-flannel-ds-amd64-22xw6_kube-system_8081248d-0a3b-44e4-8e05-ce98756f3f0e_0
939b12699f57        43940c34f24f             "/usr/local/bin/kube…"   40 minutes ago      Up 40 minutes                                   k8s_kube-proxy_kube-proxy-llzzr_kube-system_89a0fbe2-433d-4925-a57f-b2983c54359b_0
b1ec9ad0d68a        k8s.gcr.io/pause:3.2     "/pause"                 40 minutes ago      Up 40 minutes                                   k8s_POD_kube-flannel-ds-amd64-22xw6_kube-system_8081248d-0a3b-44e4-8e05-ce98756f3f0e_0
205bc1e1446c        k8s.gcr.io/pause:3.2     "/pause"                 40 minutes ago      Up 40 minutes                                   k8s_POD_kube-proxy-llzzr_kube-system_89a0fbe2-433d-4925-a57f-b2983c54359b_0
73fc033093ea        d3e55153f52f             "kube-controller-man…"   41 minutes ago      Up 41 minutes                                   k8s_kube-controller-manager_kube-controller-manager-controlplane_kube-system_f9b9c6969be80756638e9cf4927b5881_0
3b79dc48ddd5        303ce5db0e90             "etcd --advertise-cl…"   41 minutes ago      Up 41 minutes                                   k8s_etcd_etcd-controlplane_kube-system_545c20d277880b8dbc03d412e9b94d73_0
e6ac4ba42416        74060cea7f70             "kube-apiserver --ad…"   41 minutes ago      Up 41 minutes                                   k8s_kube-apiserver_kube-apiserver-controlplane_kube-system_883959a65da4d1a9f762c7c7d99c9283_0
bdf6d37c2692        a31f78c7c8ce             "kube-scheduler --au…"   41 minutes ago      Up 41 minutes                                   k8s_kube-scheduler_kube-scheduler-controlplane_kube-system_5795d0c442cb997ff93c49feeb9f6386_0
1256283313cc        k8s.gcr.io/pause:3.2     "/pause"                 41 minutes ago      Up 41 minutes                                   k8s_POD_kube-scheduler-controlplane_kube-system_5795d0c442cb997ff93c49feeb9f6386_0
6f041c3c52f9        k8s.gcr.io/pause:3.2     "/pause"                 41 minutes ago      Up 41 minutes                                   k8s_POD_kube-controller-manager-controlplane_kube-system_f9b9c6969be80756638e9cf4927b5881_0
181991942de6        k8s.gcr.io/pause:3.2     "/pause"                 41 minutes ago      Up 41 minutes                                   k8s_POD_kube-apiserver-controlplane_kube-system_883959a65da4d1a9f762c7c7d99c9283_0
a1d9780e0035        k8s.gcr.io/pause:3.2     "/pause"                 41 minutes ago      Up 41 minutes                                   k8s_POD_etcd-controlplane_kube-system_545c20d277880b8dbc03d412e9b94d73_0
controlplane $ docker ps -a  | wc -l
18
```

node 节点

```text
node01 $ docker ps -a
CONTAINER ID        IMAGE                                          COMMAND                  CREATED              STATUS                          PORTS               NAMES
ecf30086acd6        74188596f8cb                                   "katacoda-cloud-prov…"   About a minute ago   Up About a minute                                   k8s_katacoda-cloud-provider_katacoda-cloud-provider-58f89f7d9-256gm_kube-system_f11bd165-0dee-4194-b815-0e381879e6d1_13
4d2335bad660        74188596f8cb                                   "katacoda-cloud-prov…"   2 minutes ago        Exited (2) About a minute ago                       k8s_katacoda-cloud-provider_katacoda-cloud-provider-58f89f7d9-256gm_kube-system_f11bd165-0dee-4194-b815-0e381879e6d1_12
86146876f80b        k8s.gcr.io/pause:3.2                           "/pause"                 38 minutes ago       Up 38 minutes                                       k8s_POD_katacoda-cloud-provider-58f89f7d9-256gm_kube-system_f11bd165-0dee-4194-b815-0e381879e6d1_0
195b168a9069        gcr.io/google_containers/kube-keepalived-vip   "/kube-keepalived-vi…"   38 minutes ago       Up 38 minutes                                       k8s_kube-keepalived-vip_kube-keepalived-vip-g7cq8_kube-system_74b78a91-c1d6-4c2a-87bf-2191ea48578b_0
5df438115230        k8s.gcr.io/pause:3.2                           "/pause"                 39 minutes ago       Up 39 minutes                                       k8s_POD_kube-keepalived-vip-g7cq8_kube-system_74b78a91-c1d6-4c2a-87bf-2191ea48578b_0
a3b9d07ebf55        4e9f801d2217                                   "/opt/bin/flanneld -…"   39 minutes ago       Up 39 minutes                                       k8s_kube-flannel_kube-flannel-ds-amd64-9jmqk_kube-system_58ffb540-80f8-4217-8be3-ecd825797bf2_0
f8ffbed7a962        quay.io/coreos/flannel                         "cp -f /etc/kube-fla…"   39 minutes ago       Exited (0) 39 minutes ago                           k8s_install-cni_kube-flannel-ds-amd64-9jmqk_kube-system_58ffb540-80f8-4217-8be3-ecd825797bf2_0
a8bd0e1b99b4        k8s.gcr.io/kube-proxy                          "/usr/local/bin/kube…"   39 minutes ago       Up 39 minutes                                       k8s_kube-proxy_kube-proxy-rv64z_kube-system_2f816bd3-142f-423c-83ab-87b0dd05a649_0
08d610cd84f9        k8s.gcr.io/pause:3.2                           "/pause"                 39 minutes ago       Up 39 minutes                                       k8s_POD_kube-flannel-ds-amd64-9jmqk_kube-system_58ffb540-80f8-4217-8be3-ecd825797bf2_0
f3e7a0a4563b        k8s.gcr.io/pause:3.2                           "/pause"                 39 minutes ago       Up 39 minutes                                       k8s_POD_kube-proxy-rv64z_kube-system_2f816bd3-142f-423c-83ab-87b0dd05a649_0
node01 $ docker ps -a  | wc -l
11
```

### 安装

```text
master $ istioctl install --set profile=demo
Detected that your cluster does not support third party JWT authentication. Falling back to less secure first party JWT. See https://istio.io/docs/ops/best-practices/security/#configure-third-party-service-account-tokens for details.
✔ Istio core installed
✔ Istiod installed
✔ Egress gateways installed
✔ Ingress gateways installed
✔ Addons installed
✔ Installation complete
```

### 安装后

services 

```text
master $ kubectl get services -A
NAMESPACE      NAME                   TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)                 AGE
default        details                ClusterIP      10.108.113.18    <none>        9080/TCP                 26m
default        kubernetes             ClusterIP      10.96.0.1        <none>        443/TCP                 103m
default        productpage            ClusterIP      10.105.111.242   <none>        9080/TCP                 26m
default        ratings                ClusterIP      10.101.225.75    <none>        9080/TCP                 26m
default        reviews                ClusterIP      10.111.89.102    <none>        9080/TCP                 26m
istio-system   istio-egressgateway    ClusterIP      10.105.58.176    <none>        80/TCP,443/TCP,15443/TCP                 29m
istio-system   istio-ingressgateway   LoadBalancer   10.99.172.64     <pending>     15021:30412/TCP,80:31834/TCP,443:32722/TCP,31400:32002/TCP,15443:30322/TCP   29m
istio-system   istiod                 ClusterIP      10.98.76.192     <none>        15010/TCP,15012/TCP,443/TCP,15014/TCP,853/TCP                 30m
kube-system    kube-dns               ClusterIP      10.96.0.10       <none>        53/UDP,53/TCP,9153/TCP                 103m
```

pods

```text
controlplane $ kubectl get pods -A
NAMESPACE      NAME                                      READY   STATUS             RESTARTS   AGE
istio-system   istio-egressgateway-695f5944d8-x7rp8      1/1     Running            0          47s
istio-system   istio-ingressgateway-5c697d4cd7-v7kth     1/1     Running            0          47s
istio-system   istiod-59747cbfdd-wrvwv                   1/1     Running            0          54s
kube-system    coredns-66bff467f8-gwdhc                  1/1     Running            0          43m
kube-system    coredns-66bff467f8-svxcc                  1/1     Running            0          43m
kube-system    etcd-controlplane                         1/1     Running            0          43m
kube-system    katacoda-cloud-provider-58f89f7d9-256gm   0/1     CrashLoopBackOff   13         43m
kube-system    kube-apiserver-controlplane               1/1     Running            0          43m
kube-system    kube-controller-manager-controlplane      1/1     Running            0          43m
kube-system    kube-flannel-ds-amd64-22xw6               1/1     Running            0          43m
kube-system    kube-flannel-ds-amd64-9jmqk               1/1     Running            0          43m
kube-system    kube-keepalived-vip-g7cq8                 1/1     Running            0          42m
kube-system    kube-proxy-llzzr                          1/1     Running            0          43m
kube-system    kube-proxy-rv64z                          1/1     Running            0          43m
kube-system    kube-scheduler-controlplane               1/1     Running            0          43m
controlplane $ kubectl get pods -A | wc -l
16
```

容器

```text
node01 $ docker ps -a | grep "3 min"
4ff91f843668        18ef15b27b3f                                   "/usr/local/bin/pilo…"   3 minutes ago       Up 3 minutes                                    k8s_istio-proxy_istio-egressgateway-695f5944d8-x7rp8_istio-system_4d0dac63-7d4d-48ee-a523-7a00ac025634_0
a9cc1326507a        18ef15b27b3f                                   "/usr/local/bin/pilo…"   3 minutes ago       Up 3 minutes                                    k8s_istio-proxy_istio-ingressgateway-5c697d4cd7-v7kth_istio-system_fa5cce6e-4e8b-444e-a2b9-ba95212c568b_0
d2af5e8ba088        k8s.gcr.io/pause:3.2                           "/pause"                 3 minutes ago       Up 3 minutes                                    k8s_POD_istio-ingressgateway-5c697d4cd7-v7kth_istio-system_fa5cce6e-4e8b-444e-a2b9-ba95212c568b_0
3af0fff6ac01        k8s.gcr.io/pause:3.2                           "/pause"                 3 minutes ago       Up 3 minutes                                    k8s_POD_istio-egressgateway-695f5944d8-x7rp8_istio-system_4d0dac63-7d4d-48ee-a523-7a00ac025634_0
7897501e0481        65fbbed4bcda                                   "/usr/local/bin/pilo…"   3 minutes ago       Up 3 minutes                                    k8s_discovery_istiod-59747cbfdd-wrvwv_istio-system_884e539c-8e6c-49bd-97cf-a4a7ba819a84_0
7bb222f0e98b        k8s.gcr.io/pause:3.2                           "/pause"                 3 minutes ago       Up 3 minutes                                    k8s_POD_istiod-59747cbfdd-wrvwv_istio-system_884e539c-8e6c-49bd-97cf-a4a7ba819a84_0
```

### 设置sidecar inject

```text
master $ kubectl label namespace default istio-injection=enabled
namespace/default labeled
```

### configmap istio-sidecar-injector

```text
controlplane $ kubectl get configmap istio-sidecar-injector -n istio-system -o yaml
apiVersion: v1
data:
  config: |-
    policy: enabled
    alwaysInjectSelector:
      []
    neverInjectSelector:
      []
    injectedAnnotations:

    template: |
      rewriteAppHTTPProbe: {{ valueOrDefault .Values.sidecarInjectorWebhook.rewriteAppHTTPProbe false }}
      initContainers:
      {{ if ne (annotation .ObjectMeta `sidecar.istio.io/interceptionMode` .ProxyConfig.InterceptionMode) `NONE` }}
      {{ if .Values.istio_cni.enabled -}}
      - name: istio-validation
      {{ else -}}
      - name: istio-init
      {{ end -}}
      {{- if contains "/" .Values.global.proxy_init.image }}
        image: "{{ .Values.global.proxy_init.image }}"
      {{- else }}
        image: "{{ .Values.global.hub }}/{{ .Values.global.proxy_init.image }}:{{ .Values.global.tag }}"
      {{- end }}
        args:
        - istio-iptables
        - "-p"
        - 15001
        - "-z"
        - "15006"
        - "-u"
        - 1337
        - "-m"
        - "{{ annotation .ObjectMeta `sidecar.istio.io/interceptionMode` .ProxyConfig.InterceptionMode }}"
        - "-i"
        - "{{ annotation .ObjectMeta `traffic.sidecar.istio.io/includeOutboundIPRanges` .Values.global.proxy.includeIPRanges }}"
        - "-x"
        - "{{ annotation .ObjectMeta `traffic.sidecar.istio.io/excludeOutboundIPRanges` .Values.global.proxy.excludeIPRanges }}"
        - "-b"
        - "{{ annotation .ObjectMeta `traffic.sidecar.istio.io/includeInboundPorts` `*` }}"
        - "-d"
      {{- if excludeInboundPort (annotation .ObjectMeta `status.sidecar.istio.io/port` .Values.global.proxy.statusPort) (annotation .ObjectMeta`traffic.sidecar.istio.io/excludeInboundPorts` .Values.global.proxy.excludeInboundPorts) }}
        - "15090,15021,{{ excludeInboundPort (annotation .ObjectMeta `status.sidecar.istio.io/port` .Values.global.proxy.statusPort) (annotation .ObjectMeta `traffic.sidecar.istio.io/excludeInboundPorts` .Values.global.proxy.excludeInboundPorts) }}"
      {{- else }}
        - "15090,15021"
      {{- end }}
        {{ if or (isset .ObjectMeta.Annotations `traffic.sidecar.istio.io/includeOutboundPorts`) (ne (valueOrDefault .Values.global.proxy.includeOutboundPorts "") "") -}}
        - "-q"
        - "{{ annotation .ObjectMeta `traffic.sidecar.istio.io/includeOutboundPorts` .Values.global.proxy.includeOutboundPorts }}"
        {{ end -}}
        {{ if or (isset .ObjectMeta.Annotations `traffic.sidecar.istio.io/excludeOutboundPorts`) (ne (valueOrDefault .Values.global.proxy.excludeOutboundPorts "") "") -}}
        - "-o"
        - "{{ annotation .ObjectMeta `traffic.sidecar.istio.io/excludeOutboundPorts` .Values.global.proxy.excludeOutboundPorts }}"
        {{ end -}}
        {{ if (isset .ObjectMeta.Annotations `traffic.sidecar.istio.io/kubevirtInterfaces`) -}}
        - "-k"
        - "{{ index .ObjectMeta.Annotations `traffic.sidecar.istio.io/kubevirtInterfaces` }}"
        {{ end -}}
        {{ if .Values.istio_cni.enabled -}}
        - "--run-validation"
        - "--skip-rule-apply"
        {{ end -}}
        imagePullPolicy: "{{ valueOrDefault .Values.global.imagePullPolicy `Always` }}"
      {{- if .ProxyConfig.ProxyMetadata }}
        env:
        {{- range $key, $value := .ProxyConfig.ProxyMetadata }}
        - name: {{ $key }}
          value: "{{ $value }}"
        {{- end }}
      {{- end }}
      {{- if .Values.global.proxy_init.resources }}
        resources:
          {{ toYaml .Values.global.proxy_init.resources | indent 4 }}
      {{- else }}
        resources: {}
      {{- end }}
        securityContext:
          allowPrivilegeEscalation: {{ .Values.global.proxy.privileged }}
          privileged: {{ .Values.global.proxy.privileged }}
          capabilities:
        {{- if not .Values.istio_cni.enabled }}
            add:
            - NET_ADMIN
            - NET_RAW
        {{- end }}
            drop:
            - ALL
        {{- if not .Values.istio_cni.enabled }}
          readOnlyRootFilesystem: false
          runAsGroup: 0
          runAsNonRoot: false
          runAsUser: 0
        {{- else }}
          readOnlyRootFilesystem: true
          runAsGroup: 1337
          runAsUser: 1337
          runAsNonRoot: true
        {{- end }}
        restartPolicy: Always
      {{ end -}}
      {{- if eq .Values.global.proxy.enableCoreDump true }}
      - name: enable-core-dump
        args:
        - -c
        - sysctl -w kernel.core_pattern=/var/lib/istio/data/core.proxy && ulimit -c unlimited
        command:
          - /bin/sh
      {{- if contains "/" .Values.global.proxy_init.image }}
        image: "{{ .Values.global.proxy_init.image }}"
      {{- else }}
        image: "{{ .Values.global.hub }}/{{ .Values.global.proxy_init.image }}:{{ .Values.global.tag }}"
      {{- end }}
        imagePullPolicy: "{{ valueOrDefault .Values.global.imagePullPolicy `Always` }}"
        resources: {}
        securityContext:
          allowPrivilegeEscalation: true
          capabilities:
            add:
            - SYS_ADMIN
            drop:
            - ALL
          privileged: true
          readOnlyRootFilesystem: false
          runAsGroup: 0
          runAsNonRoot: false
          runAsUser: 0
      {{ end }}
      containers:
      - name: istio-proxy
      {{- if contains "/" (annotation .ObjectMeta `sidecar.istio.io/proxyImage` .Values.global.proxy.image) }}
        image: "{{ annotation .ObjectMeta `sidecar.istio.io/proxyImage` .Values.global.proxy.image }}"
      {{- else }}
        image: "{{ .Values.global.hub }}/{{ .Values.global.proxy.image }}:{{ .Values.global.tag }}"
      {{- end }}
        ports:
        - containerPort: 15090
          protocol: TCP
          name: http-envoy-prom
        args:
        - proxy
        - sidecar
        - --domain
        - $(POD_NAMESPACE).svc.{{ .Values.global.proxy.clusterDomain }}
        - --serviceCluster
        {{ if ne "" (index .ObjectMeta.Labels "app") -}}
        - "{{ index .ObjectMeta.Labels `app` }}.$(POD_NAMESPACE)"
        {{ else -}}
        - "{{ valueOrDefault .DeploymentMeta.Name `istio-proxy` }}.{{ valueOrDefault .DeploymentMeta.Namespace `default` }}"
        {{ end -}}
        - --proxyLogLevel={{ annotation .ObjectMeta `sidecar.istio.io/logLevel` .Values.global.proxy.logLevel}}
        - --proxyComponentLogLevel={{ annotation .ObjectMeta `sidecar.istio.io/componentLogLevel` .Values.global.proxy.componentLogLevel}}
      {{- if .Values.global.sts.servicePort }}
        - --stsPort={{ .Values.global.sts.servicePort }}
      {{- end }}
      {{- if .Values.global.trustDomain }}
        - --trust-domain={{ .Values.global.trustDomain }}
      {{- end }}
      {{- if .Values.global.logAsJson }}
        - --log_as_json
      {{- end }}
      {{- if gt .ProxyConfig.Concurrency.GetValue 0 }}
        - --concurrency
        - "{{ .ProxyConfig.Concurrency.GetValue }}"
      {{- end -}}
      {{- if .Values.global.proxy.lifecycle }}
        lifecycle:
          {{ toYaml .Values.global.proxy.lifecycle | indent 4 }}
      {{- else if .Values.global.proxy.holdApplicationUntilProxyStarts}}
        lifecycle:
          postStart:
            exec:
              command:
              - pilot-agent
              - wait
      {{- end }}
        env:
        - name: JWT_POLICY
          value: {{ .Values.global.jwtPolicy }}
        - name: PILOT_CERT_PROVIDER
          value: {{ .Values.global.pilotCertProvider }}
        - name: CA_ADDR
        {{- if .Values.global.caAddress }}
          value: {{ .Values.global.caAddress }}
        {{- else }}
          value: istiod{{- if not (eq .Values.revision "") }}-{{ .Values.revision }}{{- end }}.{{ .Values.global.istioNamespace }}.svc:15012
        {{- end }}
        - name: POD_NAME
          valueFrom:
            fieldRef:
              fieldPath: metadata.name
        - name: POD_NAMESPACE
          valueFrom:
            fieldRef:
              fieldPath: metadata.namespace
        - name: INSTANCE_IP
          valueFrom:
            fieldRef:
              fieldPath: status.podIP
        - name: SERVICE_ACCOUNT
          valueFrom:
            fieldRef:
              fieldPath: spec.serviceAccountName
        - name: HOST_IP
          valueFrom:
            fieldRef:
              fieldPath: status.hostIP
        - name: CANONICAL_SERVICE
          valueFrom:
            fieldRef:
              fieldPath: metadata.labels['service.istio.io/canonical-name']
        - name: CANONICAL_REVISION
          valueFrom:
            fieldRef:
              fieldPath: metadata.labels['service.istio.io/canonical-revision']
        - name: PROXY_CONFIG
          value: |
                 {{ protoToJSON .ProxyConfig }}
        - name: ISTIO_META_POD_PORTS
          value: |-
            [
            {{- $first := true }}
            {{- range $index1, $c := .Spec.Containers }}
              {{- range $index2, $p := $c.Ports }}
                {{- if (structToJSON $p) }}
                {{if not $first}},{{end}}{{ structToJSON $p }}
                {{- $first = false }}
                {{- end }}
              {{- end}}
            {{- end}}
            ]
        - name: ISTIO_META_APP_CONTAINERS
          value: "{{- range $index, $container := .Spec.Containers }}{{- if ne $index 0}},{{- end}}{{ $container.Name }}{{- end}}"
        - name: ISTIO_META_CLUSTER_ID
          value: "{{ valueOrDefault .Values.global.multiCluster.clusterName `Kubernetes` }}"
        - name: ISTIO_META_INTERCEPTION_MODE
          value: "{{ or (index .ObjectMeta.Annotations `sidecar.istio.io/interceptionMode`) .ProxyConfig.InterceptionMode.String }}"
        {{- if .Values.global.network }}
        - name: ISTIO_META_NETWORK
          value: "{{ .Values.global.network }}"
        {{- end }}
        {{ if .ObjectMeta.Annotations }}
        - name: ISTIO_METAJSON_ANNOTATIONS
          value: |
                 {{ toJSON .ObjectMeta.Annotations }}
        {{ end }}
        {{- if .DeploymentMeta.Name }}
        - name: ISTIO_META_WORKLOAD_NAME
          value: {{ .DeploymentMeta.Name }}
        {{ end }}
        {{- if and .TypeMeta.APIVersion .DeploymentMeta.Name }}
        - name: ISTIO_META_OWNER
          value: kubernetes://apis/{{ .TypeMeta.APIVersion }}/namespaces/{{ valueOrDefault .DeploymentMeta.Namespace `default` }}/{{ toLower .TypeMeta.Kind}}s/{{ .DeploymentMeta.Name }}
        {{- end}}
        {{- if (isset .ObjectMeta.Annotations `sidecar.istio.io/bootstrapOverride`) }}
        - name: ISTIO_BOOTSTRAP_OVERRIDE
          value: "/etc/istio/custom-bootstrap/custom_bootstrap.json"
        {{- end }}
        {{- if .Values.global.meshID }}
        - name: ISTIO_META_MESH_ID
          value: "{{ .Values.global.meshID }}"
        {{- else if .Values.global.trustDomain }}
        - name: ISTIO_META_MESH_ID
          value: "{{ .Values.global.trustDomain }}"
        {{- end }}
        {{- if and (eq .Values.global.proxy.tracer "datadog") (isset .ObjectMeta.Annotations `apm.datadoghq.com/env`) }}
        {{- range $key, $value := fromJSON (index .ObjectMeta.Annotations `apm.datadoghq.com/env`) }}
        - name: {{ $key }}
          value: "{{ $value }}"
        {{- end }}
        {{- end }}
        {{- range $key, $value := .ProxyConfig.ProxyMetadata }}
        - name: {{ $key }}
          value: "{{ $value }}"
        {{- end }}
        imagePullPolicy: "{{ valueOrDefault .Values.global.imagePullPolicy `Always` }}"
        {{ if ne (annotation .ObjectMeta `status.sidecar.istio.io/port` .Values.global.proxy.statusPort) `0` }}
        readinessProbe:
          httpGet:
            path: /healthz/ready
            port: 15021
          initialDelaySeconds: {{ annotation .ObjectMeta `readiness.status.sidecar.istio.io/initialDelaySeconds` .Values.global.proxy.readinessInitialDelaySeconds }}
          periodSeconds: {{ annotation .ObjectMeta `readiness.status.sidecar.istio.io/periodSeconds` .Values.global.proxy.readinessPeriodSeconds }}
          failureThreshold: {{ annotation .ObjectMeta `readiness.status.sidecar.istio.io/failureThreshold` .Values.global.proxy.readinessFailureThreshold }}
        {{ end -}}
        securityContext:
          allowPrivilegeEscalation: {{ .Values.global.proxy.privileged }}
          capabilities:
            {{ if or (eq (annotation .ObjectMeta `sidecar.istio.io/interceptionMode` .ProxyConfig.InterceptionMode) `TPROXY`) (eq (annotation .ObjectMeta `sidecar.istio.io/capNetBindService` .Values.global.proxy.capNetBindService) `true`) -}}
            add:
            {{ if eq (annotation .ObjectMeta `sidecar.istio.io/interceptionMode` .ProxyConfig.InterceptionMode) `TPROXY` -}}
            - NET_ADMIN
            {{- end }}
            {{ if eq (annotation .ObjectMeta `sidecar.istio.io/capNetBindService` .Values.global.proxy.capNetBindService) `true` -}}
            - NET_BIND_SERVICE
            {{- end }}
            {{- end }}
            drop:
            - ALL
          privileged: {{ .Values.global.proxy.privileged }}
          readOnlyRootFilesystem: {{ not .Values.global.proxy.enableCoreDump }}
          runAsGroup: 1337
          fsGroup: 1337
          {{ if or (eq (annotation .ObjectMeta `sidecar.istio.io/interceptionMode` .ProxyConfig.InterceptionMode) `TPROXY`) (eq (annotation .ObjectMeta `sidecar.istio.io/capNetBindService` .Values.global.proxy.capNetBindService) `true`) -}}
          runAsNonRoot: false
          runAsUser: 0
          {{- else -}}
          runAsNonRoot: true
          runAsUser: 1337
          {{- end }}
        resources:
      {{- if or (isset .ObjectMeta.Annotations `sidecar.istio.io/proxyCPU`) (isset .ObjectMeta.Annotations `sidecar.istio.io/proxyMemory`) (isset .ObjectMeta.Annotations `sidecar.istio.io/proxyCPULimit`) (isset .ObjectMeta.Annotations `sidecar.istio.io/proxyMemoryLimit`) }}
        {{- if or (isset .ObjectMeta.Annotations `sidecar.istio.io/proxyCPU`) (isset .ObjectMeta.Annotations `sidecar.istio.io/proxyMemory`) }}
          requests:
            {{ if (isset .ObjectMeta.Annotations `sidecar.istio.io/proxyCPU`) -}}
            cpu: "{{ index .ObjectMeta.Annotations `sidecar.istio.io/proxyCPU` }}"
            {{ end }}
            {{ if (isset .ObjectMeta.Annotations `sidecar.istio.io/proxyMemory`) -}}
            memory: "{{ index .ObjectMeta.Annotations `sidecar.istio.io/proxyMemory` }}"
            {{ end }}
        {{- end }}
        {{- if or (isset .ObjectMeta.Annotations `sidecar.istio.io/proxyCPULimit`) (isset .ObjectMeta.Annotations `sidecar.istio.io/proxyMemoryLimit`) }}
          limits:
            {{ if (isset .ObjectMeta.Annotations `sidecar.istio.io/proxyCPULimit`) -}}
            cpu: "{{ index .ObjectMeta.Annotations `sidecar.istio.io/proxyCPULimit` }}"
            {{ end }}
            {{ if (isset .ObjectMeta.Annotations `sidecar.istio.io/proxyMemoryLimit`) -}}
            memory: "{{ index .ObjectMeta.Annotations `sidecar.istio.io/proxyMemoryLimit` }}"
            {{ end }}
        {{- end }}
      {{- else }}
        {{- if .Values.global.proxy.resources }}
          {{ toYaml .Values.global.proxy.resources | indent 4 }}
        {{- end }}
      {{- end }}
        volumeMounts:
        {{- if eq .Values.global.pilotCertProvider "istiod" }}
        - mountPath: /var/run/secrets/istio
          name: istiod-ca-cert
        {{- end }}
        - mountPath: /var/lib/istio/data
          name: istio-data
        {{ if (isset .ObjectMeta.Annotations `sidecar.istio.io/bootstrapOverride`) }}
        - mountPath: /etc/istio/custom-bootstrap
          name: custom-bootstrap-volume
        {{- end }}
        # SDS channel between istioagent and Envoy
        - mountPath: /etc/istio/proxy
          name: istio-envoy
        {{- if eq .Values.global.jwtPolicy "third-party-jwt" }}
        - mountPath: /var/run/secrets/tokens
          name: istio-token
        {{- end }}
        {{- if .Values.global.mountMtlsCerts }}
        # Use the key and cert mounted to /etc/certs/ for the in-cluster mTLS communications.
        - mountPath: /etc/certs/
          name: istio-certs
          readOnly: true
        {{- end }}
        - name: istio-podinfo
          mountPath: /etc/istio/pod
         {{- if and (eq .Values.global.proxy.tracer "lightstep") .ProxyConfig.GetTracing.GetTlsSettings }}
        - mountPath: {{ directory .ProxyConfig.GetTracing.GetTlsSettings.GetCaCertificates }}
          name: lightstep-certs
          readOnly: true
        {{- end }}
          {{- if isset .ObjectMeta.Annotations `sidecar.istio.io/userVolumeMount` }}
          {{ range $index, $value := fromJSON (index .ObjectMeta.Annotations `sidecar.istio.io/userVolumeMount`) }}
        - name: "{{  $index }}"
          {{ toYaml $value | indent 4 }}
          {{ end }}
          {{- end }}
      {{- if .ProxyConfig.ProxyMetadata.ISTIO_META_DNS_CAPTURE }}
      dnsConfig:
        options:
        - name: "ndots"
          value: "4"
      {{- end }}
      volumes:
      {{- if (isset .ObjectMeta.Annotations `sidecar.istio.io/bootstrapOverride`) }}
      - name: custom-bootstrap-volume
        configMap:
          name: {{ annotation .ObjectMeta `sidecar.istio.io/bootstrapOverride` "" }}
      {{- end }}
      # SDS channel between istioagent and Envoy
      - emptyDir:
          medium: Memory
        name: istio-envoy
      - name: istio-data
        emptyDir: {}
      - name: istio-podinfo
        downwardAPI:
          items:
            - path: "labels"
              fieldRef:
                fieldPath: metadata.labels
            - path: "annotations"
              fieldRef:
                fieldPath: metadata.annotations
      {{- if eq .Values.global.jwtPolicy "third-party-jwt" }}
      - name: istio-token
        projected:
          sources:
          - serviceAccountToken:
              path: istio-token
              expirationSeconds: 43200
              audience: {{ .Values.global.sds.token.aud }}
      {{- end }}
      {{- if eq .Values.global.pilotCertProvider "istiod" }}
      - name: istiod-ca-cert
        configMap:
          name: istio-ca-root-cert
      {{- end }}
      {{- if .Values.global.mountMtlsCerts }}
      # Use the key and cert mounted to /etc/certs/ for the in-cluster mTLS communications.
      - name: istio-certs
        secret:
          optional: true
          {{ if eq .Spec.ServiceAccountName "" }}
          secretName: istio.default
          {{ else -}}
          secretName: {{  printf "istio.%s" .Spec.ServiceAccountName }}
          {{  end -}}
      {{- end }}
        {{- if isset .ObjectMeta.Annotations `sidecar.istio.io/userVolume` }}
        {{range $index, $value := fromJSON (index .ObjectMeta.Annotations `sidecar.istio.io/userVolume`) }}
      - name: "{{ $index }}"
        {{ toYaml $value | indent 2 }}
        {{ end }}
        {{ end }}
      {{- if and (eq .Values.global.proxy.tracer "lightstep") .ProxyConfig.GetTracing.GetTlsSettings }}
      - name: lightstep-certs
        secret:
          optional: true
          secretName: lightstep.cacert
      {{- end }}
      {{- if .Values.global.podDNSSearchNamespaces }}
      dnsConfig:
        searches:
          {{- range .Values.global.podDNSSearchNamespaces }}
          - {{ render . }}
          {{- end }}
      {{- end }}
      podRedirectAnnot:
      {{- if and (.Values.istio_cni.enabled) (not .Values.istio_cni.chained) }}
      {{ if isset .ObjectMeta.Annotations `k8s.v1.cni.cncf.io/networks` }}
        k8s.v1.cni.cncf.io/networks: "{{ index .ObjectMeta.Annotations `k8s.v1.cni.cncf.io/networks`}}, istio-cni"
      {{- else }}
        k8s.v1.cni.cncf.io/networks: "istio-cni"
      {{- end }}
      {{- end }}
        sidecar.istio.io/interceptionMode: "{{ annotation .ObjectMeta `sidecar.istio.io/interceptionMode` .ProxyConfig.InterceptionMode }}"
        traffic.sidecar.istio.io/includeOutboundIPRanges: "{{ annotation .ObjectMeta `traffic.sidecar.istio.io/includeOutboundIPRanges` .Values.global.proxy.includeIPRanges }}"
        traffic.sidecar.istio.io/excludeOutboundIPRanges: "{{ annotation .ObjectMeta `traffic.sidecar.istio.io/excludeOutboundIPRanges` .Values.global.proxy.excludeIPRanges }}"
        traffic.sidecar.istio.io/includeInboundPorts: "{{ annotation .ObjectMeta `traffic.sidecar.istio.io/includeInboundPorts` (includeInboundPorts .Spec.Containers) }}"
        traffic.sidecar.istio.io/excludeInboundPorts: "{{ excludeInboundPort (annotation .ObjectMeta `status.sidecar.istio.io/port` .Values.global.proxy.statusPort) (annotation .ObjectMeta `traffic.sidecar.istio.io/excludeInboundPorts` .Values.global.proxy.excludeInboundPorts) }}"
      {{ if or (isset .ObjectMeta.Annotations `traffic.sidecar.istio.io/includeOutboundPorts`) (ne (valueOrDefault .Values.global.proxy.includeOutboundPorts "") "") }}
        traffic.sidecar.istio.io/includeOutboundPorts: "{{ annotation .ObjectMeta `traffic.sidecar.istio.io/includeOutboundPorts` .Values.global.proxy.includeOutboundPorts }}"
      {{- end }}
      {{ if or (isset .ObjectMeta.Annotations `traffic.sidecar.istio.io/excludeOutboundPorts`) (ne .Values.global.proxy.excludeOutboundPorts "") }}
        traffic.sidecar.istio.io/excludeOutboundPorts: "{{ annotation .ObjectMeta `traffic.sidecar.istio.io/excludeOutboundPorts` .Values.global.proxy.excludeOutboundPorts }}"
      {{- end }}
        traffic.sidecar.istio.io/kubevirtInterfaces: "{{ index .ObjectMeta.Annotations `traffic.sidecar.istio.io/kubevirtInterfaces` }}"
      {{- if .Values.global.imagePullSecrets }}
      imagePullSecrets:
        {{- range .Values.global.imagePullSecrets }}
        - name: {{ . }}
        {{- end }}
      {{- end }}
  values: |-
    {
      "global": {
        "arch": {
          "amd64": 2,
          "ppc64le": 2,
          "s390x": 2
        },
        "caAddress": "",
        "centralIstiod": false,
        "configValidation": true,
        "controlPlaneSecurityEnabled": true,
        "createRemoteSvcEndpoints": false,
        "defaultNodeSelector": {},
        "defaultPodDisruptionBudget": {
          "enabled": true
        },
        "defaultResources": {
          "requests": {
            "cpu": "10m"
          }
        },
        "enableHelmTest": false,
        "enabled": true,
        "hub": "docker.io/istio",
        "imagePullPolicy": "",
        "imagePullSecrets": [],
        "istioNamespace": "istio-system",
        "istiod": {
          "enableAnalysis": false
        },
        "jwtPolicy": "first-party-jwt",
        "logAsJson": false,
        "logging": {
          "level": "default:info"
        },
        "meshExpansion": {
          "enabled": false,
          "useILB": false
        },
        "meshID": "",
        "meshNetworks": {},
        "mountMtlsCerts": false,
        "multiCluster": {
          "clusterName": "",
          "enabled": false
        },
        "namespace": "istio-system",
        "network": "",
        "omitSidecarInjectorConfigMap": false,
        "oneNamespace": false,
        "operatorManageWebhooks": false,
        "pilotCertProvider": "istiod",
        "policyNamespace": "istio-system",
        "priorityClassName": "",
        "proxy": {
          "autoInject": "enabled",
          "clusterDomain": "cluster.local",
          "componentLogLevel": "misc:error",
          "enableCoreDump": false,
          "excludeIPRanges": "",
          "excludeInboundPorts": "",
          "excludeOutboundPorts": "",
          "holdApplicationUntilProxyStarts": false,
          "image": "proxyv2",
          "includeIPRanges": "*",
          "logLevel": "warning",
          "privileged": false,
          "readinessFailureThreshold": 30,
          "readinessInitialDelaySeconds": 1,
          "readinessPeriodSeconds": 2,
          "resources": {
            "limits": {
              "cpu": "2000m",
              "memory": "1024Mi"
            },
            "requests": {
              "cpu": "10m",
              "memory": "40Mi"
            }
          },
          "statusPort": 15020,
          "tracer": "zipkin"
        },
        "proxy_init": {
          "image": "proxyv2",
          "resources": {
            "limits": {
              "cpu": "2000m",
              "memory": "1024Mi"
            },
            "requests": {
              "cpu": "10m",
              "memory": "10Mi"
            }
          }
        },
        "remotePilotAddress": "",
        "remotePolicyAddress": "",
        "remoteTelemetryAddress": "",
        "sds": {
          "token": {
            "aud": "istio-ca"
          }
        },
        "sts": {
          "servicePort": 0
        },
        "tag": "1.7.0",
        "telemetryNamespace": "istio-system",
        "tracer": {
          "datadog": {
            "address": "$(HOST_IP):8126"
          },
          "lightstep": {
            "accessToken": "",
            "address": ""
          },
          "stackdriver": {
            "debug": false,
            "maxNumberOfAnnotations": 200,
            "maxNumberOfAttributes": 200,
            "maxNumberOfMessageEvents": 200
          },
          "zipkin": {
            "address": ""
          }
        },
        "trustDomain": "cluster.local",
        "useMCP": false
      },
      "istio_cni": {
        "enabled": false
      },
      "revision": "",
      "sidecarInjectorWebhook": {
        "alwaysInjectSelector": [],
        "enableNamespacesByDefault": false,
        "injectLabel": "istio-injection",
        "injectedAnnotations": {},
        "neverInjectSelector": [],
        "objectSelector": {
          "autoInject": true,
          "enabled": false
        },
        "rewriteAppHTTPProbe": true
      }
    }
kind: ConfigMap
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"v1","data":{"config":"policy: enabled\nalwaysInjectSelector:\n  []\nneverInjectSelector:\n  []\ninjectedAnnotations:\n\ntemplate: |\n  rewriteAppHTTPProbe: {{ valueOrDefault .Values.sidecarInjectorWebhook.rewriteAppHTTPProbe false }}\n  initContainers:\n  {{ if ne (annotation .ObjectMeta `sidecar.istio.io/interceptionMode` .ProxyConfig.InterceptionMode) `NONE` }}\n  {{ if .Values.istio_cni.enabled -}}\n  - name: istio-validation\n  {{ else -}}\n  - name: istio-init\n  {{ end -}}\n  {{- if contains \"/\" .Values.global.proxy_init.image }}\n    image: \"{{ .Values.global.proxy_init.image }}\"\n  {{- else }}\n    image: \"{{ .Values.global.hub }}/{{ .Values.global.proxy_init.image }}:{{ .Values.global.tag }}\"\n  {{- end }}\n    args:\n    - istio-iptables\n    - \"-p\"\n    - 15001\n    - \"-z\"\n    - \"15006\"\n    - \"-u\"\n    -1337\n    - \"-m\"\n    - \"{{ annotation .ObjectMeta `sidecar.istio.io/interceptionMode` .ProxyConfig.InterceptionMode }}\"\n    - \"-i\"\n- \"{{ annotation .ObjectMeta `traffic.sidecar.istio.io/includeOutboundIPRanges` .Values.global.proxy.includeIPRanges }}\"\n    - \"-x\"\n    -\"{{ annotation .ObjectMeta `traffic.sidecar.istio.io/excludeOutboundIPRanges` .Values.global.proxy.excludeIPRanges }}\"\n    - \"-b\"\n    - \"{{ annotation .ObjectMeta `traffic.sidecar.istio.io/includeInboundPorts` `*` }}\"\n    - \"-d\"\n  {{- if excludeInboundPort (annotation .ObjectMeta `status.sidecar.istio.io/port` .Values.global.proxy.statusPort) (annotation .ObjectMeta `traffic.sidecar.istio.io/excludeInboundPorts` .Values.global.proxy.excludeInboundPorts) }}\n    - \"15090,15021,{{ excludeInboundPort (annotation .ObjectMeta `status.sidecar.istio.io/port` .Values.global.proxy.statusPort) (annotation .ObjectMeta `traffic.sidecar.istio.io/excludeInboundPorts` .Values.global.proxy.excludeInboundPorts) }}\"\n  {{- else }}\n    - \"15090,15021\"\n  {{- end }}\n    {{ if or (isset .ObjectMeta.Annotations `traffic.sidecar.istio.io/includeOutboundPorts`) (ne (valueOrDefault .Values.global.proxy.includeOutboundPorts \"\") \"\") -}}\n    - \"-q\"\n    - \"{{ annotation .ObjectMeta `traffic.sidecar.istio.io/includeOutboundPorts` .Values.global.proxy.includeOutboundPorts }}\"\n    {{ end -}}\n    {{ if or (isset .ObjectMeta.Annotations `traffic.sidecar.istio.io/excludeOutboundPorts`) (ne (valueOrDefault .Values.global.proxy.excludeOutboundPorts \"\") \"\") -}}\n    - \"-o\"\n - \"{{ annotation .ObjectMeta `traffic.sidecar.istio.io/excludeOutboundPorts` .Values.global.proxy.excludeOutboundPorts }}\"\n    {{ end -}}\n   {{ if (isset .ObjectMeta.Annotations `traffic.sidecar.istio.io/kubevirtInterfaces`) -}}\n    - \"-k\"\n    - \"{{ index .ObjectMeta.Annotations `traffic.sidecar.istio.io/kubevirtInterfaces` }}\"\n    {{ end -}}\n    {{ if .Values.istio_cni.enabled -}}\n    - \"--run-validation\"\n- \"--skip-rule-apply\"\n    {{ end -}}\n    imagePullPolicy: \"{{ valueOrDefault .Values.global.imagePullPolicy `Always` }}\"\n  {{- if .ProxyConfig.ProxyMetadata }}\n    env:\n    {{- range $key, $value := .ProxyConfig.ProxyMetadata }}\n    - name: {{ $key }}\n      value: \"{{ $value}}\"\n    {{- end }}\n  {{- end }}\n  {{- if .Values.global.proxy_init.resources }}\n    resources:\n      {{ toYaml .Values.global.proxy_init.resources | indent 4 }}\n  {{- else }}\n    resources: {}\n  {{- end }}\n    securityContext:\n      allowPrivilegeEscalation: {{ .Values.global.proxy.privileged }}\n      privileged: {{ .Values.global.proxy.privileged }}\n      capabilities:\n    {{- if not .Values.istio_cni.enabled }}\n        add:\n        - NET_ADMIN\n        - NET_RAW\n    {{- end }}\n        drop:\n        - ALL\n    {{- if not .Values.istio_cni.enabled }}\n      readOnlyRootFilesystem: false\n      runAsGroup: 0\n      runAsNonRoot: false\n      runAsUser: 0\n    {{- else }}\n      readOnlyRootFilesystem: true\n      runAsGroup: 1337\n      runAsUser: 1337\n      runAsNonRoot: true\n    {{- end }}\n    restartPolicy: Always\n  {{ end -}}\n  {{- if eq .Values.global.proxy.enableCoreDump true }}\n  - name: enable-core-dump\n    args:\n    - -c\n    - sysctl -w kernel.core_pattern=/var/lib/istio/data/core.proxy \u0026\u0026 ulimit -c unlimited\n    command:\n      - /bin/sh\n  {{- if contains \"/\" .Values.global.proxy_init.image }}\n    image: \"{{ .Values.global.proxy_init.image }}\"\n  {{- else }}\n    image: \"{{ .Values.global.hub }}/{{ .Values.global.proxy_init.image }}:{{ .Values.global.tag }}\"\n  {{- end }}\n    imagePullPolicy: \"{{ valueOrDefault .Values.global.imagePullPolicy `Always` }}\"\n resources: {}\n    securityContext:\n      allowPrivilegeEscalation: true\n      capabilities:\n        add:\n        - SYS_ADMIN\n        drop:\n        - ALL\n      privileged: true\n      readOnlyRootFilesystem: false\n      runAsGroup: 0\n      runAsNonRoot: false\n      runAsUser:0\n  {{ end }}\n  containers:\n  - name: istio-proxy\n  {{- if contains \"/\" (annotation .ObjectMeta `sidecar.istio.io/proxyImage` .Values.global.proxy.image) }}\n    image: \"{{ annotation .ObjectMeta `sidecar.istio.io/proxyImage` .Values.global.proxy.image }}\"\n  {{- else }}\n    image: \"{{ .Values.global.hub }}/{{ .Values.global.proxy.image }}:{{ .Values.global.tag }}\"\n  {{- end }}\n    ports:\n    - containerPort: 15090\n      protocol: TCP\n      name: http-envoy-prom\n    args:\n    - proxy\n    - sidecar\n    - --domain\n    - $(POD_NAMESPACE).svc.{{ .Values.global.proxy.clusterDomain }}\n    - --serviceCluster\n    {{ if ne \"\" (index .ObjectMeta.Labels \"app\") -}}\n    - \"{{ index .ObjectMeta.Labels `app` }}.$(POD_NAMESPACE)\"\n    {{ else -}}\n    - \"{{ valueOrDefault .DeploymentMeta.Name `istio-proxy` }}.{{ valueOrDefault .DeploymentMeta.Namespace `default` }}\"\n    {{ end -}}\n    - --proxyLogLevel={{ annotation .ObjectMeta `sidecar.istio.io/logLevel` .Values.global.proxy.logLevel}}\n    - --proxyComponentLogLevel={{ annotation .ObjectMeta `sidecar.istio.io/componentLogLevel` .Values.global.proxy.componentLogLevel}}\n  {{- if .Values.global.sts.servicePort }}\n    - --stsPort={{ .Values.global.sts.servicePort }}\n  {{- end }}\n  {{- if .Values.global.trustDomain }}\n    - --trust-domain={{ .Values.global.trustDomain }}\n  {{- end }}\n  {{- if .Values.global.logAsJson }}\n    - --log_as_json\n  {{- end }}\n  {{- if gt .ProxyConfig.Concurrency.GetValue 0 }}\n    - --concurrency\n    - \"{{ .ProxyConfig.Concurrency.GetValue }}\"\n  {{- end -}}\n  {{- if .Values.global.proxy.lifecycle }}\n    lifecycle:\n      {{ toYaml .Values.global.proxy.lifecycle | indent 4 }}\n  {{- else if .Values.global.proxy.holdApplicationUntilProxyStarts}}\n    lifecycle:\n      postStart:\n        exec:\n          command:\n          - pilot-agent\n          - wait\n  {{- end }}\n    env:\n    - name: JWT_POLICY\n      value: {{ .Values.global.jwtPolicy }}\n    - name: PILOT_CERT_PROVIDER\n      value: {{ .Values.global.pilotCertProvider }}\n    - name: CA_ADDR\n    {{- if .Values.global.caAddress }}\n      value: {{ .Values.global.caAddress }}\n    {{- else }}\n      value: istiod{{- if not (eq .Values.revision \"\") }}-{{ .Values.revision }}{{- end }}.{{ .Values.global.istioNamespace }}.svc:15012\n    {{- end }}\n    - name: POD_NAME\n      valueFrom:\n        fieldRef:\n          fieldPath: metadata.name\n   - name: POD_NAMESPACE\n      valueFrom:\n        fieldRef:\n          fieldPath: metadata.namespace\n    - name: INSTANCE_IP\n      valueFrom:\n        fieldRef:\n          fieldPath: status.podIP\n    - name: SERVICE_ACCOUNT\n      valueFrom:\n        fieldRef:\n          fieldPath:spec.serviceAccountName\n    - name: HOST_IP\n      valueFrom:\n        fieldRef:\n          fieldPath: status.hostIP\n    - name: CANONICAL_SERVICE\n      valueFrom:\n        fieldRef:\n          fieldPath: metadata.labels['service.istio.io/canonical-name']\n    - name: CANONICAL_REVISION\n      valueFrom:\n        fieldRef:\n          fieldPath: metadata.labels['service.istio.io/canonical-revision']\n    - name: PROXY_CONFIG\n      value: |\n             {{ protoToJSON .ProxyConfig }}\n    - name: ISTIO_META_POD_PORTS\n      value: |-\n        [\n        {{- $first := true }}\n        {{- range $index1, $c := .Spec.Containers }}\n          {{- range $index2, $p := $c.Ports }}\n            {{- if (structToJSON $p) }}\n            {{if not $first}},{{end}}{{ structToJSON $p }}\n            {{- $first = false }}\n            {{- end }}\n          {{- end}}\n        {{- end}}\n        ]\n    - name: ISTIO_META_APP_CONTAINERS\n      value: \"{{- range $index, $container := .Spec.Containers }}{{-if ne $index 0}},{{- end}}{{ $container.Name }}{{- end}}\"\n    - name: ISTIO_META_CLUSTER_ID\n      value: \"{{ valueOrDefault .Values.global.multiCluster.clusterName `Kubernetes` }}\"\n    - name: ISTIO_META_INTERCEPTION_MODE\n      value: \"{{ or (index .ObjectMeta.Annotations `sidecar.istio.io/interceptionMode`) .ProxyConfig.InterceptionMode.String }}\"\n    {{- if .Values.global.network }}\n    - name: ISTIO_META_NETWORK\n     value: \"{{ .Values.global.network }}\"\n    {{- end }}\n    {{ if .ObjectMeta.Annotations }}\n    - name: ISTIO_METAJSON_ANNOTATIONS\n  value: |\n             {{ toJSON .ObjectMeta.Annotations }}\n    {{ end }}\n    {{- if .DeploymentMeta.Name }}\n    - name: ISTIO_META_WORKLOAD_NAME\n      value: {{ .DeploymentMeta.Name }}\n    {{ end }}\n    {{- if and .TypeMeta.APIVersion .DeploymentMeta.Name }}\n    - name: ISTIO_META_OWNER\n      value: kubernetes://apis/{{ .TypeMeta.APIVersion }}/namespaces/{{ valueOrDefault .DeploymentMeta.Namespace `default` }}/{{ toLower .TypeMeta.Kind}}s/{{ .DeploymentMeta.Name }}\n    {{- end}}\n    {{- if (isset .ObjectMeta.Annotations `sidecar.istio.io/bootstrapOverride`) }}\n    - name: ISTIO_BOOTSTRAP_OVERRIDE\n      value: \"/etc/istio/custom-bootstrap/custom_bootstrap.json\"\n    {{- end }}\n    {{- if .Values.global.meshID }}\n    - name: ISTIO_META_MESH_ID\n      value: \"{{ .Values.global.meshID }}\"\n    {{- else if .Values.global.trustDomain }}\n    - name: ISTIO_META_MESH_ID\n      value: \"{{ .Values.global.trustDomain }}\"\n    {{- end }}\n    {{- if and (eq .Values.global.proxy.tracer \"datadog\") (isset .ObjectMeta.Annotations `apm.datadoghq.com/env`) }}\n    {{- range $key, $value := fromJSON (index .ObjectMeta.Annotations `apm.datadoghq.com/env`) }}\n    - name: {{ $key }}\n      value: \"{{ $value }}\"\n    {{- end }}\n    {{- end }}\n    {{- range $key, $value := .ProxyConfig.ProxyMetadata }}\n    - name: {{ $key }}\n      value: \"{{ $value }}\"\n    {{- end }}\n    imagePullPolicy: \"{{ valueOrDefault .Values.global.imagePullPolicy `Always` }}\"\n    {{ if ne (annotation .ObjectMeta `status.sidecar.istio.io/port` .Values.global.proxy.statusPort) `0` }}\n    readinessProbe:\n      httpGet:\n        path: /healthz/ready\n        port: 15021\n      initialDelaySeconds: {{ annotation .ObjectMeta `readiness.status.sidecar.istio.io/initialDelaySeconds` .Values.global.proxy.readinessInitialDelaySeconds }}\n      periodSeconds: {{ annotation .ObjectMeta `readiness.status.sidecar.istio.io/periodSeconds` .Values.global.proxy.readinessPeriodSeconds }}\n      failureThreshold: {{ annotation .ObjectMeta `readiness.status.sidecar.istio.io/failureThreshold` .Values.global.proxy.readinessFailureThreshold }}\n    {{ end -}}\n    securityContext:\n      allowPrivilegeEscalation: {{ .Values.global.proxy.privileged }}\n      capabilities:\n        {{ if or (eq (annotation .ObjectMeta `sidecar.istio.io/interceptionMode` .ProxyConfig.InterceptionMode) `TPROXY`) (eq (annotation .ObjectMeta `sidecar.istio.io/capNetBindService` .Values.global.proxy.capNetBindService) `true`) -}}\n        add:\n        {{ if eq (annotation .ObjectMeta `sidecar.istio.io/interceptionMode` .ProxyConfig.InterceptionMode) `TPROXY` -}}\n        - NET_ADMIN\n        {{- end }}\n        {{ if eq (annotation .ObjectMeta`sidecar.istio.io/capNetBindService` .Values.global.proxy.capNetBindService) `true` -}}\n        - NET_BIND_SERVICE\n        {{- end }}\n {{- end }}\n        drop:\n        - ALL\n      privileged: {{ .Values.global.proxy.privileged }}\n      readOnlyRootFilesystem: {{ not .Values.global.proxy.enableCoreDump }}\n      runAsGroup: 1337\n      fsGroup: 1337\n      {{ if or (eq (annotation .ObjectMeta `sidecar.istio.io/interceptionMode` .ProxyConfig.InterceptionMode) `TPROXY`) (eq (annotation .ObjectMeta `sidecar.istio.io/capNetBindService` .Values.global.proxy.capNetBindService) `true`) -}}\n      runAsNonRoot: false\n      runAsUser: 0\n      {{- else -}}\n      runAsNonRoot: true\n      runAsUser: 1337\n      {{- end }}\n    resources:\n  {{- if or (isset .ObjectMeta.Annotations `sidecar.istio.io/proxyCPU`) (isset .ObjectMeta.Annotations `sidecar.istio.io/proxyMemory`) (isset .ObjectMeta.Annotations `sidecar.istio.io/proxyCPULimit`) (isset .ObjectMeta.Annotations `sidecar.istio.io/proxyMemoryLimit`) }}\n    {{- if or (isset .ObjectMeta.Annotations `sidecar.istio.io/proxyCPU`) (isset .ObjectMeta.Annotations `sidecar.istio.io/proxyMemory`) }}\n      requests:\n        {{ if (isset .ObjectMeta.Annotations `sidecar.istio.io/proxyCPU`) -}}\n        cpu: \"{{ index .ObjectMeta.Annotations `sidecar.istio.io/proxyCPU` }}\"\n        {{ end }}\n        {{ if (isset .ObjectMeta.Annotations `sidecar.istio.io/proxyMemory`) -}}\n        memory: \"{{ index .ObjectMeta.Annotations `sidecar.istio.io/proxyMemory` }}\"\n        {{ end }}\n    {{- end }}\n    {{- if or (isset .ObjectMeta.Annotations `sidecar.istio.io/proxyCPULimit`) (isset .ObjectMeta.Annotations `sidecar.istio.io/proxyMemoryLimit`) }}\n      limits:\n        {{ if (isset .ObjectMeta.Annotations `sidecar.istio.io/proxyCPULimit`) -}}\n        cpu: \"{{ index .ObjectMeta.Annotations `sidecar.istio.io/proxyCPULimit` }}\"\n        {{ end }}\n        {{ if (isset .ObjectMeta.Annotations `sidecar.istio.io/proxyMemoryLimit`) -}}\n    memory: \"{{ index .ObjectMeta.Annotations `sidecar.istio.io/proxyMemoryLimit` }}\"\n        {{ end }}\n    {{- end }}\n  {{- else }}\n    {{- if .Values.global.proxy.resources }}\n      {{ toYaml .Values.global.proxy.resources | indent 4 }}\n    {{- end }}\n  {{- end }}\n    volumeMounts:\n    {{- if eq .Values.global.pilotCertProvider \"istiod\" }}\n    - mountPath: /var/run/secrets/istio\n      name: istiod-ca-cert\n    {{- end }}\n    - mountPath: /var/lib/istio/data\n      name: istio-data\n    {{ if (isset .ObjectMeta.Annotations `sidecar.istio.io/bootstrapOverride`) }}\n    - mountPath: /etc/istio/custom-bootstrap\n      name: custom-bootstrap-volume\n    {{- end }}\n    # SDS channel between istioagent and Envoy\n    - mountPath: /etc/istio/proxy\n      name: istio-envoy\n    {{- if eq .Values.global.jwtPolicy \"third-party-jwt\" }}\n    -mountPath: /var/run/secrets/tokens\n      name: istio-token\n    {{- end }}\n    {{- if .Values.global.mountMtlsCerts }}\n    # Use the key andcert mounted to /etc/certs/ for the in-cluster mTLS communications.\n    - mountPath: /etc/certs/\n      name: istio-certs\n      readOnly: true\n    {{- end }}\n    - name: istio-podinfo\n      mountPath: /etc/istio/pod\n     {{- if and (eq .Values.global.proxy.tracer \"lightstep\") .ProxyConfig.GetTracing.GetTlsSettings }}\n    - mountPath: {{ directory .ProxyConfig.GetTracing.GetTlsSettings.GetCaCertificates }}\n      name: lightstep-certs\n      readOnly: true\n    {{- end }}\n      {{- if isset .ObjectMeta.Annotations `sidecar.istio.io/userVolumeMount` }}\n      {{ range $index, $value := fromJSON (index .ObjectMeta.Annotations `sidecar.istio.io/userVolumeMount`) }}\n    - name: \"{{  $index }}\"\n      {{ toYaml $value | indent 4 }}\n      {{ end }}\n      {{- end }}\n  {{- if .ProxyConfig.ProxyMetadata.ISTIO_META_DNS_CAPTURE }}\n  dnsConfig:\n  options:\n    - name: \"ndots\"\n      value: \"4\"\n  {{- end }}\n  volumes:\n  {{- if (isset .ObjectMeta.Annotations `sidecar.istio.io/bootstrapOverride`) }}\n  - name: custom-bootstrap-volume\n    configMap:\n      name: {{ annotation .ObjectMeta `sidecar.istio.io/bootstrapOverride` \"\" }}\n  {{- end }}\n  # SDS channel between istioagent and Envoy\n  - emptyDir:\n      medium: Memory\n    name: istio-envoy\n  - name: istio-data\n    emptyDir: {}\n  - name: istio-podinfo\n    downwardAPI:\n      items:\n        - path: \"labels\"\n          fieldRef:\nfieldPath: metadata.labels\n        - path: \"annotations\"\n          fieldRef:\n            fieldPath: metadata.annotations\n  {{- if eq .Values.global.jwtPolicy \"third-party-jwt\" }}\n  - name: istio-token\n    projected:\n      sources:\n      - serviceAccountToken:\n          path: istio-token\n          expirationSeconds: 43200\n          audience: {{ .Values.global.sds.token.aud }}\n  {{- end }}\n  {{- if eq .Values.global.pilotCertProvider \"istiod\" }}\n  - name: istiod-ca-cert\n    configMap:\n      name: istio-ca-root-cert\n  {{- end }}\n  {{- if .Values.global.mountMtlsCerts }}\n  # Use the key and cert mounted to /etc/certs/ for the in-cluster mTLS communications.\n  - name: istio-certs\n    secret:\n      optional: true\n      {{ if eq .Spec.ServiceAccountName \"\" }}\n      secretName: istio.default\n      {{ else -}}\n      secretName: {{  printf \"istio.%s\" .Spec.ServiceAccountName }}\n      {{  end -}}\n  {{- end }}\n    {{- if isset .ObjectMeta.Annotations `sidecar.istio.io/userVolume` }}\n    {{range $index, $value := fromJSON (index .ObjectMeta.Annotations `sidecar.istio.io/userVolume`) }}\n  - name: \"{{ $index }}\"\n    {{ toYaml $value | indent 2 }}\n    {{ end }}\n    {{ end }}\n  {{- if and (eq .Values.global.proxy.tracer \"lightstep\") .ProxyConfig.GetTracing.GetTlsSettings }}\n  - name: lightstep-certs\n    secret:\n      optional: true\n      secretName: lightstep.cacert\n  {{- end }}\n  {{- if .Values.global.podDNSSearchNamespaces }}\n  dnsConfig:\n    searches:\n      {{- range .Values.global.podDNSSearchNamespaces }}\n- {{ render . }}\n      {{- end }}\n  {{- end }}\n  podRedirectAnnot:\n  {{- if and (.Values.istio_cni.enabled) (not .Values.istio_cni.chained)}}\n  {{ if isset .ObjectMeta.Annotations `k8s.v1.cni.cncf.io/networks` }}\n    k8s.v1.cni.cncf.io/networks: \"{{ index .ObjectMeta.Annotations`k8s.v1.cni.cncf.io/networks`}}, istio-cni\"\n  {{- else }}\n    k8s.v1.cni.cncf.io/networks: \"istio-cni\"\n  {{- end }}\n  {{- end }}\n    sidecar.istio.io/interceptionMode: \"{{ annotation .ObjectMeta `sidecar.istio.io/interceptionMode` .ProxyConfig.InterceptionMode }}\"\n    traffic.sidecar.istio.io/includeOutboundIPRanges: \"{{ annotation .ObjectMeta `traffic.sidecar.istio.io/includeOutboundIPRanges` .Values.global.proxy.includeIPRanges }}\"\n    traffic.sidecar.istio.io/excludeOutboundIPRanges: \"{{ annotation .ObjectMeta `traffic.sidecar.istio.io/excludeOutboundIPRanges` .Values.global.proxy.excludeIPRanges }}\"\n    traffic.sidecar.istio.io/includeInboundPorts: \"{{ annotation .ObjectMeta `traffic.sidecar.istio.io/includeInboundPorts` (includeInboundPorts .Spec.Containers) }}\"\n    traffic.sidecar.istio.io/excludeInboundPorts: \"{{ excludeInboundPort (annotation .ObjectMeta `status.sidecar.istio.io/port` .Values.global.proxy.statusPort) (annotation .ObjectMeta `traffic.sidecar.istio.io/excludeInboundPorts` .Values.global.proxy.excludeInboundPorts) }}\"\n  {{ if or (isset .ObjectMeta.Annotations `traffic.sidecar.istio.io/includeOutboundPorts`) (ne (valueOrDefault .Values.global.proxy.includeOutboundPorts \"\") \"\") }}\n    traffic.sidecar.istio.io/includeOutboundPorts: \"{{ annotation .ObjectMeta `traffic.sidecar.istio.io/includeOutboundPorts` .Values.global.proxy.includeOutboundPorts }}\"\n  {{- end }}\n  {{ if or (isset .ObjectMeta.Annotations `traffic.sidecar.istio.io/excludeOutboundPorts`) (ne .Values.global.proxy.excludeOutboundPorts \"\") }}\n    traffic.sidecar.istio.io/excludeOutboundPorts: \"{{ annotation .ObjectMeta `traffic.sidecar.istio.io/excludeOutboundPorts` .Values.global.proxy.excludeOutboundPorts }}\"\n  {{- end }}\n    traffic.sidecar.istio.io/kubevirtInterfaces: \"{{ index .ObjectMeta.Annotations `traffic.sidecar.istio.io/kubevirtInterfaces` }}\"\n  {{- if .Values.global.imagePullSecrets }}\n  imagePullSecrets:\n    {{- range .Values.global.imagePullSecrets }}\n    - name: {{ . }}\n    {{- end }}\n  {{- end }}","values":"{\n  \"global\": {\n    \"arch\": {\n      \"amd64\": 2,\n      \"ppc64le\": 2,\n      \"s390x\": 2\n    },\n    \"caAddress\": \"\",\n    \"centralIstiod\": false,\n    \"configValidation\": true,\n    \"controlPlaneSecurityEnabled\": true,\n    \"createRemoteSvcEndpoints\": false,\n    \"defaultNodeSelector\": {},\n    \"defaultPodDisruptionBudget\": {\n  \"enabled\": true\n    },\n    \"defaultResources\": {\n      \"requests\": {\n        \"cpu\": \"10m\"\n      }\n    },\n    \"enableHelmTest\": false,\n    \"enabled\": true,\n    \"hub\": \"docker.io/istio\",\n    \"imagePullPolicy\": \"\",\n    \"imagePullSecrets\": [],\n    \"istioNamespace\": \"istio-system\",\n    \"istiod\": {\n      \"enableAnalysis\": false\n    },\n    \"jwtPolicy\": \"first-party-jwt\",\n    \"logAsJson\": false,\n    \"logging\": {\n      \"level\": \"default:info\"\n    },\n    \"meshExpansion\": {\n      \"enabled\": false,\n      \"useILB\": false\n    },\n    \"meshID\": \"\",\n    \"meshNetworks\": {},\n    \"mountMtlsCerts\": false,\n    \"multiCluster\": {\n      \"clusterName\": \"\",\n      \"enabled\": false\n    },\n    \"namespace\": \"istio-system\",\n    \"network\": \"\",\n    \"omitSidecarInjectorConfigMap\": false,\n    \"oneNamespace\": false,\n    \"operatorManageWebhooks\": false,\n    \"pilotCertProvider\": \"istiod\",\n    \"policyNamespace\": \"istio-system\",\n    \"priorityClassName\": \"\",\n    \"proxy\": {\n      \"autoInject\": \"enabled\",\n      \"clusterDomain\": \"cluster.local\",\n      \"componentLogLevel\": \"misc:error\",\n      \"enableCoreDump\": false,\n      \"excludeIPRanges\": \"\",\n      \"excludeInboundPorts\": \"\",\n      \"excludeOutboundPorts\": \"\",\n      \"holdApplicationUntilProxyStarts\": false,\n      \"image\": \"proxyv2\",\n   \"includeIPRanges\": \"*\",\n      \"logLevel\": \"warning\",\n      \"privileged\": false,\n      \"readinessFailureThreshold\": 30,\n\"readinessInitialDelaySeconds\": 1,\n      \"readinessPeriodSeconds\": 2,\n      \"resources\": {\n        \"limits\": {\n          \"cpu\": \"2000m\",\n          \"memory\": \"1024Mi\"\n        },\n        \"requests\": {\n          \"cpu\": \"10m\",\n          \"memory\": \"40Mi\"\n      }\n      },\n      \"statusPort\": 15020,\n      \"tracer\": \"zipkin\"\n    },\n    \"proxy_init\": {\n      \"image\": \"proxyv2\",\n   \"resources\": {\n        \"limits\": {\n          \"cpu\": \"2000m\",\n          \"memory\": \"1024Mi\"\n        },\n        \"requests\": {\n          \"cpu\": \"10m\",\n          \"memory\": \"10Mi\"\n        }\n      }\n    },\n    \"remotePilotAddress\": \"\",\n    \"remotePolicyAddress\": \"\",\n    \"remoteTelemetryAddress\": \"\",\n    \"sds\": {\n      \"token\": {\n        \"aud\": \"istio-ca\"\n      }\n    },\n \"sts\": {\n      \"servicePort\": 0\n    },\n    \"tag\": \"1.7.0\",\n    \"telemetryNamespace\": \"istio-system\",\n    \"tracer\": {\n\"datadog\": {\n        \"address\": \"$(HOST_IP):8126\"\n      },\n      \"lightstep\": {\n        \"accessToken\": \"\",\n        \"address\": \"\"\n      },\n      \"stackdriver\": {\n        \"debug\": false,\n        \"maxNumberOfAnnotations\": 200,\n        \"maxNumberOfAttributes\": 200,\n        \"maxNumberOfMessageEvents\": 200\n      },\n      \"zipkin\": {\n        \"address\": \"\"\n      }\n    },\n    \"trustDomain\": \"cluster.local\",\n    \"useMCP\": false\n  },\n  \"istio_cni\": {\n    \"enabled\": false\n  },\n  \"revision\": \"\",\n  \"sidecarInjectorWebhook\": {\n    \"alwaysInjectSelector\": [],\n    \"enableNamespacesByDefault\": false,\n    \"injectLabel\": \"istio-injection\",\n    \"injectedAnnotations\": {},\n    \"neverInjectSelector\": [],\n    \"objectSelector\": {\n      \"autoInject\": true,\n      \"enabled\": false\n  },\n    \"rewriteAppHTTPProbe\": true\n  }\n}"},"kind":"ConfigMap","metadata":{"annotations":{},"labels":{"install.operator.istio.io/owning-resource":"installed-state","install.operator.istio.io/owning-resource-namespace":"istio-system","istio.io/rev":"default","operator.istio.io/component":"Pilot","operator.istio.io/managed":"Reconcile","operator.istio.io/version":"1.7.0","release":"istio"},"name":"istio-sidecar-injector","namespace":"istio-system"}}
  creationTimestamp: "2020-09-03T01:52:13Z"
  labels:
    install.operator.istio.io/owning-resource: installed-state
    install.operator.istio.io/owning-resource-namespace: istio-system
    istio.io/rev: default
    operator.istio.io/component: Pilot
    operator.istio.io/managed: Reconcile
    operator.istio.io/version: 1.7.0
    release: istio
  managedFields:
  - apiVersion: v1
    fieldsType: FieldsV1
    fieldsV1:
      f:data:
        .: {}
        f:config: {}
        f:values: {}
      f:metadata:
        f:annotations:
          .: {}
          f:kubectl.kubernetes.io/last-applied-configuration: {}
        f:labels:
          .: {}
          f:install.operator.istio.io/owning-resource: {}
          f:install.operator.istio.io/owning-resource-namespace: {}
          f:istio.io/rev: {}
          f:operator.istio.io/component: {}
          f:operator.istio.io/managed: {}
          f:operator.istio.io/version: {}
          f:release: {}
    manager: istioctl
    operation: Update
    time: "2020-09-03T01:52:13Z"
  name: istio-sidecar-injector
  namespace: istio-system
  resourceVersion: "4632"
  selfLink: /api/v1/namespaces/istio-system/configmaps/istio-sidecar-injector
  uid: 4c380cdd-dd1b-4f8f-80f3-ce83fd003b00
```



## istioD

### configmap istio

```text
controlplane $ kubectl get configmap  istio -n istio-system -o yaml
apiVersion: v1
data:
  mesh: |-
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
  meshNetworks: 'networks: {}'
kind: ConfigMap
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"v1","data":{"mesh":"accessLogFile: /dev/stdout\ndefaultConfig:\n  discoveryAddress: istiod.istio-system.svc:15012\n  proxyMetadata:\n    DNS_AGENT: \"\"\n  tracing:\n    zipkin:\n      address: zipkin.istio-system:9411\ndisableMixerHttpReports: true\nenablePrometheusMerge: true\nrootNamespace: istio-system\ntrustDomain: cluster.local","meshNetworks":"networks: {}"},"kind":"ConfigMap","metadata":{"annotations":{},"labels":{"install.operator.istio.io/owning-resource":"installed-state","install.operator.istio.io/owning-resource-namespace":"istio-system","istio.io/rev":"default","operator.istio.io/component":"Pilot","operator.istio.io/managed":"Reconcile","operator.istio.io/version":"1.7.0","release":"istio"},"name":"istio","namespace":"istio-system"}}
  creationTimestamp: "2020-09-03T01:52:13Z"
  labels:
    install.operator.istio.io/owning-resource: installed-state
    install.operator.istio.io/owning-resource-namespace: istio-system
    istio.io/rev: default
    operator.istio.io/component: Pilot
    operator.istio.io/managed: Reconcile
    operator.istio.io/version: 1.7.0
    release: istio
  managedFields:
  - apiVersion: v1
    fieldsType: FieldsV1
    fieldsV1:
      f:data:
        .: {}
        f:mesh: {}
        f:meshNetworks: {}
      f:metadata:
        f:annotations:
          .: {}
          f:kubectl.kubernetes.io/last-applied-configuration: {}
        f:labels:
          .: {}
          f:install.operator.istio.io/owning-resource: {}
          f:install.operator.istio.io/owning-resource-namespace: {}
          f:istio.io/rev: {}
          f:operator.istio.io/component: {}
          f:operator.istio.io/managed: {}
          f:operator.istio.io/version: {}
          f:release: {}
    manager: istioctl
    operation: Update
    time: "2020-09-03T01:52:13Z"
  name: istio
  namespace: istio-system
  resourceVersion: "4631"
  selfLink: /api/v1/namespaces/istio-system/configmaps/istio
  uid: 2ca45bdb-9a02-4af7-9747-318728a0f51c
```

### docker env

```text
node01 $ docker exec 2accaba5850a env
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=istiod-59747cbfdd-mzmv9
ISTIOD_ADDR=istiod.istio-system.svc:15012
JWT_POLICY=first-party-jwt
POD_NAME=istiod-59747cbfdd-mzmv9
PILOT_ENABLE_PROTOCOL_SNIFFING_FOR_OUTBOUND=true
INJECTION_WEBHOOK_CONFIG_NAME=istio-sidecar-injector
REVISION=default
PILOT_CERT_PROVIDER=istiod
SERVICE_ACCOUNT=istiod-service-account
CLUSTER_ID=Kubernetes
POD_NAMESPACE=istio-system
PILOT_TRACE_SAMPLING=100
KUBECONFIG=/var/run/secrets/remote/config
PILOT_ENABLE_PROTOCOL_SNIFFING_FOR_INBOUND=true
PILOT_ENABLE_ANALYSIS=false
CENTRAL_ISTIOD=false
KUBERNETES_PORT_443_TCP_ADDR=10.96.0.1
ISTIOD_PORT_15010_TCP_PORT=15010
ISTIOD_PORT_443_TCP_ADDR=10.102.216.247
ISTIOD_PORT_853_TCP_PORT=853
ISTIOD_SERVICE_HOST=10.102.216.247
ISTIOD_SERVICE_PORT_HTTP_MONITORING=15014
ISTIOD_SERVICE_PORT_DNS_TLS=853
ISTIOD_PORT_443_TCP_PROTO=tcp
ISTIOD_PORT_15014_TCP_ADDR=10.102.216.247
ISTIOD_PORT_853_TCP_PROTO=tcp
ISTIOD_PORT_443_TCP=tcp://10.102.216.247:443
ISTIOD_PORT_853_TCP_ADDR=10.102.216.247
KUBERNETES_SERVICE_PORT=443
KUBERNETES_PORT=tcp://10.96.0.1:443
ISTIOD_SERVICE_PORT_HTTPS_DNS=15012
ISTIOD_PORT_15012_TCP=tcp://10.102.216.247:15012
ISTIOD_PORT_15012_TCP_PORT=15012
ISTIOD_PORT_15012_TCP_ADDR=10.102.216.247
ISTIOD_PORT_15014_TCP=tcp://10.102.216.247:15014
ISTIOD_PORT=tcp://10.102.216.247:15010
ISTIOD_PORT_15014_TCP_PROTO=tcp
ISTIOD_PORT_15014_TCP_PORT=15014
KUBERNETES_SERVICE_HOST=10.96.0.1
ISTIOD_PORT_15010_TCP_ADDR=10.102.216.247
ISTIOD_PORT_443_TCP_PORT=443
KUBERNETES_SERVICE_PORT_HTTPS=443
KUBERNETES_PORT_443_TCP_PROTO=tcp
ISTIOD_SERVICE_PORT=15010
ISTIOD_PORT_15010_TCP=tcp://10.102.216.247:15010
ISTIOD_PORT_15010_TCP_PROTO=tcp
KUBERNETES_PORT_443_TCP=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP_PORT=443
ISTIOD_SERVICE_PORT_GRPC_XDS=15010
ISTIOD_SERVICE_PORT_HTTPS_WEBHOOK=443
ISTIOD_PORT_15012_TCP_PROTO=tcp
ISTIOD_PORT_853_TCP=tcp://10.102.216.247:853
DEBIAN_FRONTEND=noninteractive
HOME=/home/istio-proxy
```



### ps -ef 

```text
node01 $ docker exec fd7b63703fc0 ps -ef > ps-pilot.data
node01 $ cat ps-pilot.data
UID        PID  PPID  C STIME TTY          TIME CMD
istio-p+     1     0  0 09:05 ?        00:00:07 /usr/local/bin/pilot-discovery discovery --monitoringAddr=:15014 --log_output_level=default:info --domain cluster.local --trust-domain=cluster.local --keepaliveMaxServerConnectionAge 30m
istio-p+    19     0  0 09:31 ?        00:00:00 ps -ef
```



### docker inspect

```text
[
    {
        "Id": "7897501e0481fd8ac1d7e5900cf0f288b4da88496fa306643c832ab2b5449124",
        "Created": "2020-09-01T08:33:56.484281229Z",
        "Path": "/usr/local/bin/pilot-discovery",
        "Args": [
            "discovery",
            "--monitoringAddr=:15014",
            "--log_output_level=default:info",
            "--domain",
            "cluster.local",
            "--trust-domain=cluster.local",
            "--keepaliveMaxServerConnectionAge",
            "30m"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 29972,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2020-09-01T08:33:57.116850107Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:65fbbed4bcda3d82e648db590e0185e0ee96b3ad73cd7099fce66aaf078aab6d",
        "ResolvConfPath": "/var/lib/docker/containers/7bb222f0e98b43ba7417d0a16f370e622fffe2cb53c5b1744e2204e6055449ab/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/7bb222f0e98b43ba7417d0a16f370e622fffe2cb53c5b1744e2204e6055449ab/hostname",
        "HostsPath": "/var/lib/kubelet/pods/884e539c-8e6c-49bd-97cf-a4a7ba819a84/etc-hosts",
        "LogPath": "/var/lib/docker/containers/7897501e0481fd8ac1d7e5900cf0f288b4da88496fa306643c832ab2b5449124/7897501e0481fd8ac1d7e5900cf0f288b4da88496fa306643c832ab2b5449124-json.log",
        "Name": "/k8s_discovery_istiod-59747cbfdd-wrvwv_istio-system_884e539c-8e6c-49bd-97cf-a4a7ba819a84_0",
        "RestartCount": 0,
        "Driver": "overlay",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "docker-default",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": [
                "/var/lib/kubelet/pods/884e539c-8e6c-49bd-97cf-a4a7ba819a84/volumes/kubernetes.io~configmap/config-volume:/etc/istio/config:ro",
                "/var/lib/kubelet/pods/884e539c-8e6c-49bd-97cf-a4a7ba819a84/volumes/kubernetes.io~empty-dir/local-certs:/var/run/secrets/istio-dns",
                "/var/lib/kubelet/pods/884e539c-8e6c-49bd-97cf-a4a7ba819a84/volumes/kubernetes.io~secret/cacerts:/etc/cacerts:ro",
                "/var/lib/kubelet/pods/884e539c-8e6c-49bd-97cf-a4a7ba819a84/volumes/kubernetes.io~secret/istio-kubeconfig:/var/run/secrets/remote:ro",
                "/var/lib/kubelet/pods/884e539c-8e6c-49bd-97cf-a4a7ba819a84/volumes/kubernetes.io~configmap/inject:/var/lib/istio/inject:ro",
                "/var/lib/kubelet/pods/884e539c-8e6c-49bd-97cf-a4a7ba819a84/volumes/kubernetes.io~secret/istiod-service-account-token-csln4:/var/run/secrets/kubernetes.io/serviceaccount:ro",
                "/var/lib/kubelet/pods/884e539c-8e6c-49bd-97cf-a4a7ba819a84/etc-hosts:/etc/hosts",
                "/var/lib/kubelet/pods/884e539c-8e6c-49bd-97cf-a4a7ba819a84/containers/discovery/4dffa4c0:/dev/termination-log"
            ],
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {
                    "max-size": "100m"
                }
            },
            "NetworkMode": "container:7bb222f0e98b43ba7417d0a16f370e622fffe2cb53c5b1744e2204e6055449ab",
            "PortBindings": null,
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": null,
            "CapDrop": [
                "ALL"
            ],
            "Capabilities": null,
            "Dns": null,
            "DnsOptions": null,
            "DnsSearch": null,
            "ExtraHosts": null,
            "GroupAdd": [
                "1337"
            ],
            "IpcMode": "container:7bb222f0e98b43ba7417d0a16f370e622fffe2cb53c5b1744e2204e6055449ab",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 975,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": [
                "seccomp=unconfined"
            ],
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 10,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "kubepods-burstable-pod884e539c_8e6c_49bd_97cf_a4a7ba819a84.slice",
            "BlkioWeight": 0,
            "BlkioWeightDevice": null,
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 100000,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "KernelMemory": 0,
            "KernelMemoryTCP": 0,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": false,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/asound",
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay/8ad9767497dbfd7e67917e102709369669dfb7d5f7ea5e2e3ab8e59148320508/root",
                "MergedDir": "/var/lib/docker/overlay/7fa2f0935f402a668af404670e8a3135b3c245010598cadb8ccda7348a69ab21/merged",
                "UpperDir": "/var/lib/docker/overlay/7fa2f0935f402a668af404670e8a3135b3c245010598cadb8ccda7348a69ab21/upper",
                "WorkDir": "/var/lib/docker/overlay/7fa2f0935f402a668af404670e8a3135b3c245010598cadb8ccda7348a69ab21/work"
            },
            "Name": "overlay"
        },
        "Mounts": [
            {
                "Type": "bind",
                "Source": "/var/lib/kubelet/pods/884e539c-8e6c-49bd-97cf-a4a7ba819a84/volumes/kubernetes.io~secret/cacerts",
                "Destination": "/etc/cacerts",
                "Mode": "ro",
                "RW": false,
                "Propagation": "rprivate"
            },
            {
                "Type": "bind",
                "Source": "/var/lib/kubelet/pods/884e539c-8e6c-49bd-97cf-a4a7ba819a84/volumes/kubernetes.io~secret/istio-kubeconfig",
                "Destination": "/var/run/secrets/remote",
                "Mode": "ro",
                "RW": false,
                "Propagation": "rprivate"
            },
            {
                "Type": "bind",
                "Source": "/var/lib/kubelet/pods/884e539c-8e6c-49bd-97cf-a4a7ba819a84/volumes/kubernetes.io~configmap/inject",
                "Destination": "/var/lib/istio/inject",
                "Mode": "ro",
                "RW": false,
                "Propagation": "rprivate"
            },
            {
                "Type": "bind",
                "Source": "/var/lib/kubelet/pods/884e539c-8e6c-49bd-97cf-a4a7ba819a84/volumes/kubernetes.io~secret/istiod-service-account-token-csln4",
                "Destination": "/var/run/secrets/kubernetes.io/serviceaccount",
                "Mode": "ro",
                "RW": false,
                "Propagation": "rprivate"
            },
            {
                "Type": "bind",
                "Source": "/var/lib/kubelet/pods/884e539c-8e6c-49bd-97cf-a4a7ba819a84/etc-hosts",
                "Destination": "/etc/hosts",
                "Mode": "",
                "RW": true,
                "Propagation": "rprivate"
            },
            {
                "Type": "bind",
                "Source": "/var/lib/kubelet/pods/884e539c-8e6c-49bd-97cf-a4a7ba819a84/containers/discovery/4dffa4c0",
                "Destination": "/dev/termination-log",
                "Mode": "",
                "RW": true,
                "Propagation": "rprivate"
            },
            {
                "Type": "bind",
                "Source": "/var/lib/kubelet/pods/884e539c-8e6c-49bd-97cf-a4a7ba819a84/volumes/kubernetes.io~configmap/config-volume",
                "Destination": "/etc/istio/config",
                "Mode": "ro",
                "RW": false,
                "Propagation": "rprivate"
            },
            {
                "Type": "bind",
                "Source": "/var/lib/kubelet/pods/884e539c-8e6c-49bd-97cf-a4a7ba819a84/volumes/kubernetes.io~empty-dir/local-certs",
                "Destination": "/var/run/secrets/istio-dns",
                "Mode": "",
                "RW": true,
                "Propagation": "rprivate"
            }
        ],
        "Config": {
            "Hostname": "istiod-59747cbfdd-wrvwv",
            "Domainname": "",
            "User": "1337:1337",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "CLUSTER_ID=Kubernetes",
                "JWT_POLICY=first-party-jwt",
                "POD_NAME=istiod-59747cbfdd-wrvwv",
                "SERVICE_ACCOUNT=istiod-service-account",
                "CENTRAL_ISTIOD=false",
                "POD_NAMESPACE=istio-system",
                "KUBECONFIG=/var/run/secrets/remote/config",
                "PILOT_ENABLE_PROTOCOL_SNIFFING_FOR_OUTBOUND=true",
                "PILOT_TRACE_SAMPLING=100",
                "PILOT_ENABLE_PROTOCOL_SNIFFING_FOR_INBOUND=true",
                "PILOT_ENABLE_ANALYSIS=false",
                "ISTIOD_ADDR=istiod.istio-system.svc:15012",
                "REVISION=default",
                "PILOT_CERT_PROVIDER=istiod",
                "INJECTION_WEBHOOK_CONFIG_NAME=istio-sidecar-injector",
                "ISTIOD_SERVICE_PORT_HTTPS_WEBHOOK=443",
                "ISTIOD_PORT_15010_TCP_PROTO=tcp",
                "ISTIOD_PORT_15012_TCP=tcp://10.108.129.197:15012",
                "ISTIOD_PORT_15014_TCP_PROTO=tcp",
                "ISTIOD_PORT_15014_TCP_ADDR=10.108.129.197",
                "KUBERNETES_PORT_443_TCP_PROTO=tcp",
                "ISTIOD_PORT_15012_TCP_PROTO=tcp",
                "ISTIOD_PORT_15012_TCP_PORT=15012",
                "ISTIOD_PORT_853_TCP_ADDR=10.108.129.197",
                "ISTIOD_PORT=tcp://10.108.129.197:15010",
                "KUBERNETES_PORT_443_TCP_PORT=443",
                "ISTIOD_SERVICE_HOST=10.108.129.197",
                "ISTIOD_SERVICE_PORT=15010",
                "ISTIOD_SERVICE_PORT_DNS_TLS=853",
                "KUBERNETES_PORT_443_TCP=tcp://10.96.0.1:443",
                "ISTIOD_PORT_443_TCP_ADDR=10.108.129.197",
                "ISTIOD_PORT_853_TCP=tcp://10.108.129.197:853",
                "KUBERNETES_PORT_443_TCP_ADDR=10.96.0.1",
                "KUBERNETES_SERVICE_PORT_HTTPS=443",
                "ISTIOD_SERVICE_PORT_GRPC_XDS=15010",
                "ISTIOD_SERVICE_PORT_HTTPS_DNS=15012",
                "ISTIOD_PORT_15010_TCP_PORT=15010",
                "ISTIOD_PORT_15010_TCP_ADDR=10.108.129.197",
                "ISTIOD_PORT_15012_TCP_ADDR=10.108.129.197",
                "ISTIOD_PORT_853_TCP_PORT=853",
                "KUBERNETES_SERVICE_HOST=10.96.0.1",
                "ISTIOD_SERVICE_PORT_HTTP_MONITORING=15014",
                "ISTIOD_PORT_15010_TCP=tcp://10.108.129.197:15010",
                "ISTIOD_PORT_443_TCP_PORT=443",
                "ISTIOD_PORT_853_TCP_PROTO=tcp",
                "KUBERNETES_SERVICE_PORT=443",
                "ISTIOD_PORT_443_TCP=tcp://10.108.129.197:443",
                "ISTIOD_PORT_443_TCP_PROTO=tcp",
                "ISTIOD_PORT_15014_TCP=tcp://10.108.129.197:15014",
                "KUBERNETES_PORT=tcp://10.96.0.1:443",
                "ISTIOD_PORT_15014_TCP_PORT=15014",
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "DEBIAN_FRONTEND=noninteractive"
            ],
            "Cmd": [
                "discovery",
                "--monitoringAddr=:15014",
                "--log_output_level=default:info",
                "--domain",
                "cluster.local",
                "--trust-domain=cluster.local",
                "--keepaliveMaxServerConnectionAge",
                "30m"
            ],
            "Healthcheck": {
                "Test": [
                    "NONE"
                ]
            },
            "Image": "sha256:65fbbed4bcda3d82e648db590e0185e0ee96b3ad73cd7099fce66aaf078aab6d",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": [
                "/usr/local/bin/pilot-discovery"
            ],
            "OnBuild": null,
            "Labels": {
                "annotation.io.kubernetes.container.hash": "ac96bb1c",
                "annotation.io.kubernetes.container.ports": "[{\"containerPort\":8080,\"protocol\":\"TCP\"},{\"containerPort\":15010,\"protocol\":\"TCP\"},{\"containerPort\":15017,\"protocol\":\"TCP\"},{\"containerPort\":15053,\"protocol\":\"TCP\"}]",
                "annotation.io.kubernetes.container.restartCount": "0",
                "annotation.io.kubernetes.container.terminationMessagePath": "/dev/termination-log",
                "annotation.io.kubernetes.container.terminationMessagePolicy": "File",
                "annotation.io.kubernetes.pod.terminationGracePeriod": "30",
                "io.kubernetes.container.logpath": "/var/log/pods/istio-system_istiod-59747cbfdd-wrvwv_884e539c-8e6c-49bd-97cf-a4a7ba819a84/discovery/0.log",
                "io.kubernetes.container.name": "discovery",
                "io.kubernetes.docker.type": "container",
                "io.kubernetes.pod.name": "istiod-59747cbfdd-wrvwv",
                "io.kubernetes.pod.namespace": "istio-system",
                "io.kubernetes.pod.uid": "884e539c-8e6c-49bd-97cf-a4a7ba819a84",
                "io.kubernetes.sandbox.id": "7bb222f0e98b43ba7417d0a16f370e622fffe2cb53c5b1744e2204e6055449ab"
            }
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "",
            "Gateway": "",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "",
            "IPPrefixLen": 0,
            "IPv6Gateway": "",
            "MacAddress": "",
            "Networks": {}
        }
    }
]
```

### docker logs 

```text
node01 $ docker logs 3fd588cce3f7
2020-09-03T07:13:09.522028Z     info    FLAG: --appNamespace=""
2020-09-03T07:13:09.522062Z     info    FLAG: --caCertFile=""
2020-09-03T07:13:09.522066Z     info    FLAG: --clusterID="Kubernetes"
2020-09-03T07:13:09.522069Z     info    FLAG: --clusterRegistriesNamespace="istio-system"
2020-09-03T07:13:09.522071Z     info    FLAG: --configDir=""
2020-09-03T07:13:09.522073Z     info    FLAG: --consulserverURL=""
2020-09-03T07:13:09.522076Z     info    FLAG: --ctrlz_address="localhost"
2020-09-03T07:13:09.522080Z     info    FLAG: --ctrlz_port="9876"
2020-09-03T07:13:09.522082Z     info    FLAG: --domain="cluster.local"
2020-09-03T07:13:09.522084Z     info    FLAG: --grpcAddr=":15010"
2020-09-03T07:13:09.522088Z     info    FLAG: --help="false"
2020-09-03T07:13:09.522090Z     info    FLAG: --httpAddr=":8080"
2020-09-03T07:13:09.522092Z     info    FLAG: --httpsAddr=":15017"
2020-09-03T07:13:09.522096Z     info    FLAG: --keepaliveInterval="30s"
2020-09-03T07:13:09.522105Z     info    FLAG: --keepaliveMaxServerConnectionAge="30m0s"
2020-09-03T07:13:09.522108Z     info    FLAG: --keepaliveTimeout="10s"
2020-09-03T07:13:09.522110Z     info    FLAG: --kubeconfig=""
2020-09-03T07:13:09.522112Z     info    FLAG: --log_as_json="false"
2020-09-03T07:13:09.522114Z     info    FLAG: --log_caller=""
2020-09-03T07:13:09.522117Z     info    FLAG: --log_output_level="default:info"
2020-09-03T07:13:09.522119Z     info    FLAG: --log_rotate=""
2020-09-03T07:13:09.522122Z     info    FLAG: --log_rotate_max_age="30"
2020-09-03T07:13:09.522124Z     info    FLAG: --log_rotate_max_backups="1000"
2020-09-03T07:13:09.522126Z     info    FLAG: --log_rotate_max_size="104857600"
2020-09-03T07:13:09.522129Z     info    FLAG: --log_stacktrace_level="default:none"
2020-09-03T07:13:09.522136Z     info    FLAG: --log_target="[stdout]"
2020-09-03T07:13:09.522139Z     info    FLAG: --mcpInitialConnWindowSize="1048576"
2020-09-03T07:13:09.522141Z     info    FLAG: --mcpInitialWindowSize="1048576"
2020-09-03T07:13:09.522144Z     info    FLAG: --mcpMaxMsgSize="4194304"
2020-09-03T07:13:09.522146Z     info    FLAG: --meshConfig="./etc/istio/config/mesh"
2020-09-03T07:13:09.522148Z     info    FLAG: --monitoringAddr=":15014"
2020-09-03T07:13:09.522151Z     info    FLAG: --namespace="istio-system"
2020-09-03T07:13:09.522153Z     info    FLAG: --networksConfig="/etc/istio/config/meshNetworks"
2020-09-03T07:13:09.522158Z     info    FLAG: --plugins="[authn,authz,health,mixer]"
2020-09-03T07:13:09.522160Z     info    FLAG: --profile="true"
2020-09-03T07:13:09.522164Z     info    FLAG: --registries="[Kubernetes]"
2020-09-03T07:13:09.522167Z     info    FLAG: --resync="1m0s"
2020-09-03T07:13:09.522169Z     info    FLAG: --secureGRPCAddr=":15012"
2020-09-03T07:13:09.522171Z     info    FLAG: --tlsCertFile=""
2020-09-03T07:13:09.522173Z     info    FLAG: --tlsKeyFile=""
2020-09-03T07:13:09.522175Z     info    FLAG: --trust-domain="cluster.local"
2020-09-03T07:13:09.533658Z     info    initializing mesh configuration
2020-09-03T07:13:09.535463Z     info    mesh configuration: {
    "disablePolicyChecks": true,
    "disableMixerHttpReports": true,
    "proxyListenPort": 15001,
    "connectTimeout": "10s",
    "protocolDetectionTimeout": "5s",
    "ingressClass": "istio",
    "ingressService": "istio-ingressgateway",
    "ingressControllerMode": "STRICT",
    "enableTracing": true,
    "accessLogFile": "/dev/stdout",
    "defaultConfig": {
        "configPath": "./etc/istio/proxy",
        "binaryPath": "/usr/local/bin/envoy",
        "serviceCluster": "istio-proxy",
        "drainDuration": "45s",
        "parentShutdownDuration": "60s",
        "discoveryAddress": "istiod.istio-system.svc:15012",
        "proxyAdminPort": 15000,
        "controlPlaneAuthPolicy": "MUTUAL_TLS",
        "statNameLength": 189,
        "concurrency": 2,
        "tracing": {
            "zipkin": {
                "address": "zipkin.istio-system:9411"
            }
        },
        "envoyAccessLogService": {

        },
        "envoyMetricsService": {

        },
        "proxyMetadata": {
            "DNS_AGENT": ""
        },
        "statusPort": 15020,
        "terminationDrainDuration": "5s"
    },
    "outboundTrafficPolicy": {
        "mode": "ALLOW_ANY"
    },
    "sdsUdsPath": "unix:./etc/istio/proxy/SDS",
    "enableAutoMtls": true,
    "trustDomain": "cluster.local",
    "trustDomainAliases": [
    ],
    "defaultServiceExportTo": [
        "*"
    ],
    "defaultVirtualServiceExportTo": [
        "*"
    ],
    "defaultDestinationRuleExportTo": [
        "*"
    ],
    "rootNamespace": "istio-system",
    "localityLbSetting": {
        "enabled": true
    },
    "dnsRefreshRate": "5s",
    "reportBatchMaxEntries": 100,
    "reportBatchMaxTime": "1s",
    "certificates": [
    ],
    "thriftConfig": {

    },
    "serviceSettings": [
    ],
    "enablePrometheusMerge": true
}
2020-09-03T07:13:09.535504Z     info    version: 1.7.0-2022348138e47498c4b54995b4cb5a1656817c4e-Clean
2020-09-03T07:13:09.535911Z     info    flags:
2020-09-03T07:13:09.536026Z     warn    Config not found: /var/run/secrets/remote/config
2020-09-03T07:13:09.537729Z     info    initializing mesh networks
2020-09-03T07:13:09.537881Z     info    mesh networks configuration: {
   "networks": {
   }
}
2020-09-03T07:13:09.538020Z     info    initializing mesh handlers
2020-09-03T07:13:09.538045Z     info    initializing controllers
2020-09-03T07:13:09.538050Z     info    No certificates specified, skipping K8S DNS certificate controller
2020-09-03T07:13:09.599226Z     warn    kube    Skipping CRD networking.x-k8s.io/v1alpha1/GatewayClass as it is not present
2020-09-03T07:13:09.599334Z     warn    kube    Skipping CRD networking.x-k8s.io/v1alpha1/Gateway as it is not present
2020-09-03T07:13:09.599353Z     warn    kube    Skipping CRD networking.x-k8s.io/v1alpha1/HTTPRoute as it is not present
2020-09-03T07:13:09.599469Z     warn    kube    Skipping CRD networking.x-k8s.io/v1alpha1/TcpRoute as it is not present
2020-09-03T07:13:09.599603Z     warn    kube    Skipping CRD networking.x-k8s.io/v1alpha1/TrafficSplit as it is not present
2020-09-03T07:13:09.599806Z     info    Ingress controller watching namespaces ""
2020-09-03T07:13:09.611637Z     warn    Config Store &{0xc00155b170 cluster.local 0xc000f9ddd0 [] [] 0xc00122ccf0 0xc00122d320 0xc001432b40 0xc0013cbc20} cannot track distribution in aggregate: this SetLedger operation is not supported by kube ingress controller
2020-09-03T07:13:09.611908Z     info    Adding Kubernetes registry adapter
2020-09-03T07:13:09.612041Z     info    Initializing Kubernetes service registry "Kubernetes"
2020-09-03T07:13:09.612272Z     info    JWT policy is first-party-jwt
2020-09-03T07:13:09.612319Z     info    creating CA and initializing public key
2020-09-03T07:13:09.612441Z     info    Use self-signed certificate as the CA certificate
2020-09-03T07:13:09.654550Z     info    pkica   Failed to get secret (error: secrets "istio-ca-secret" not found), will create one
2020-09-03T07:13:09.809812Z     info    pkica   Using self-generated public key: -----BEGIN CERTIFICATE-----
MIIC3TCCAcWgAwIBAgIQX9+/hv0hgH1or3fh4OD6WjANBgkqhkiG9w0BAQsFADAY
MRYwFAYDVQQKEw1jbHVzdGVyLmxvY2FsMB4XDTIwMDkwMzA3MTMwOVoXDTMwMDkw
MTA3MTMwOVowGDEWMBQGA1UEChMNY2x1c3Rlci5sb2NhbDCCASIwDQYJKoZIhvcN
AQEBBQADggEPADCCAQoCggEBAMbVT8EDiO83CIzAJlwBsbiDle5sLQzr9hyZZ1fc
RtwHEEFeIgqAGngzR328tMPcUGr+VQ0AWPPArliJgogHwFGOlZeJ0cExqDxAsh0f
7cdCkfLX/Aomoten8cyXB/tYnuH+CGdKaSNLpnYsnwijMsmVXCdR3e8fGhkk1EgZ
13rJGK6ZkliAoa6PlkmK771eFCuBsfz7l8CVwUHnF/RSV5CC+3BZNDD9fWI9maLR
VMWxnXXEVM6S85A8yg3U/wlc9IV5qwGVyfRfQ/bmU43iNR19ZjJl7WtCZIBX1qNw
yAmX3efg/rY0NdngXC5zUSxzLDZQn5+dLFIs1UglPp36P8UCAwEAAaMjMCEwDgYD
VR0PAQH/BAQDAgIEMA8GA1UdEwEB/wQFMAMBAf8wDQYJKoZIhvcNAQELBQADggEB
AIDnfiFRBaizj4rJ+HTbyoS8wQl3Fbd0lPNn40ZMJLKbvOPsmDBej5z0FJ881p9f
OKNhl2Gv7RIUFZKIRfsJXvFJSMTb3CJcdnvG6xrtYf3f7NGsMTxU1tjRhdc0iF6B
6JCigpXWJy5jMOnXa7N9F445ywFQfLWXkucW3YJHjN9/ey3Gu+DQpWHWYI1tPFZo
oQsILz7WDVP+xh24XSv/cvrnUXyAfqh+7L5x5rRU/0k/hh0Em3lTsxYluL1f5hpc
0e/eRtpqO0+4In2W765RYdUvOdStk6ymSk2EBQ2lEnkhgejcvlynlc3LwvJKzEhn
JTUQFhx0iCowePpaDc4foVQ=
-----END CERTIFICATE-----

2020-09-03T07:13:09.810091Z     info    rootcertrotator Set up back off time 29m47s to start rotator.
2020-09-03T07:13:09.810209Z     info    rootcertrotator Jitter is enabled, wait 29m47s before starting root cert rotator.
2020-09-03T07:13:09.810297Z     info    initializing Istiod DNS certificates host: istiod.istio-system.svc, custom host:
2020-09-03T07:13:09.810358Z     info    Generating istiod-signed cert for [istiod.istio-system.svc istiod-remote.istio-system.svc istio-pilot.istio-system.svc]
2020-09-03T07:13:10.676614Z     info    No plugged-in cert at etc/cacerts/ca-key.pem; self-signed cert is used
2020-09-03T07:13:10.676971Z     info    DNS certificates created in ./var/run/secrets/istio-dns
2020-09-03T07:13:10.677291Z     info    adding watcher for certificate var/run/secrets/istio-dns/cert-chain.pem
2020-09-03T07:13:10.677568Z     info    adding watcher for certificate var/run/secrets/istio-dns/key.pem
2020-09-03T07:13:10.677766Z     info    spiffe  Added 1 certs to trust domain cluster.local in peer cert verifier
2020-09-03T07:13:10.677782Z     info    initializing secure discovery service
2020-09-03T07:13:10.678082Z     info    initializing secure webhook server for istiod webhooks
2020-09-03T07:13:10.678147Z     info    initializing sidecar injector
2020-09-03T07:13:10.679516Z     info    initializing config validator
2020-09-03T07:13:10.679604Z     info    initializing Istiod admin server
2020-09-03T07:13:10.679987Z     info    initializing registry event handlers
2020-09-03T07:13:10.680092Z     info    starting discovery service
2020-09-03T07:13:10.680181Z     info    initializing Kubernetes cluster registry
2020-09-03T07:13:10.680281Z     info    Setting up event handlers
2020-09-03T07:13:10.680327Z     info    initializing DNS server
2020-09-03T07:13:10.682580Z     info    Staring Istiod Server with primary cluster Kubernetes
2020-09-03T07:13:10.696100Z     info    Starting Secrets controller
2020-09-03T07:13:10.696197Z     info    Waiting for informer caches to sync
2020-09-03T07:13:10.696349Z     info    ControlZ available at 127.0.0.1:9876
2020-09-03T07:13:10.696582Z     info    status  Starting status follower controller
2020-09-03T07:13:10.696636Z     info    attempting to acquire leader lease  istio-system/istio-leader...
2020-09-03T07:13:10.697005Z     info    kube    Starting Pilot K8S CRD controller
2020-09-03T07:13:10.698223Z     info    kube    controller "config.istio.io/v1alpha2/HTTPAPISpecBinding" is syncing...
2020-09-03T07:13:10.712273Z     info    successfully acquired lease istio-system/istio-leader
2020-09-03T07:13:10.713088Z     info    ads     Starting ADS server
2020-09-03T07:13:10.713201Z     info    Started DNS-TLS[::]:15053
2020-09-03T07:13:10.713309Z     info    Started DNS :15053
2020-09-03T07:13:10.713349Z     info    staring CA
2020-09-03T07:13:10.713453Z     info    serverca        added client certificate authenticator
2020-09-03T07:13:10.713524Z     info    serverca        added K8s JWT authenticator
2020-09-03T07:13:10.713878Z     info    Istiod CA has started
2020-09-03T07:13:10.714140Z     info    attempting to acquire leader lease  istio-system/istio-validation-controller-election...
2020-09-03T07:13:10.714634Z     info    attempting to acquire leader lease  istio-system/istio-namespace-controller-election...
2020-09-03T07:13:10.748693Z     info    successfully acquired lease istio-system/istio-namespace-controller-election
2020-09-03T07:13:10.748986Z     info    Starting namespace controller
2020-09-03T07:13:10.749839Z     info    successfully acquired lease istio-system/istio-validation-controller-election
2020-09-03T07:13:10.750399Z     info    Starting validation controller
2020-09-03T07:13:10.797406Z     info    Handle EDS endpoint: skip updating, service kube-scheduler/kube-system has not been populated
2020-09-03T07:13:10.797659Z     info    ads     Full push, new service kube-dns.kube-system.svc.cluster.local
2020-09-03T07:13:10.797705Z     info    Handle EDS endpoint: skip updating, service cloud-controller-manager/kube-system has not been populated
2020-09-03T07:13:10.797853Z     info    ads     Full push, new service kubernetes.default.svc.cluster.local
2020-09-03T07:13:10.797915Z     info    Handle EDS endpoint: skip updating, service kube-controller-manager/kube-system has not been populated
2020-09-03T07:13:10.798088Z     info    Handle EDS endpoint: skip updating, service kube-controller-manager/kube-system has not been populated
2020-09-03T07:13:10.798109Z     info    Handle EDS endpoint: skip updating, service kube-scheduler/kube-system has not been populated
2020-09-03T07:13:10.798143Z     info    Handle EDS endpoint: skip updating, service cloud-controller-manager/kube-system has not been populated
2020-09-03T07:13:10.812957Z     info    Starting ingress controller
2020-09-03T07:13:10.813022Z     warn    Missing ingress, skip status updates
2020-09-03T07:13:10.814193Z     info    All caches have been synced up, marking server ready
2020-09-03T07:13:10.814235Z     info    starting secure gRPC discovery service at [::]:15012
2020-09-03T07:13:10.814492Z     info    starting webhook service at [::]:8080
2020-09-03T07:13:10.814771Z     info    starting gRPC discovery service at [::]:15010
2020-09-03T07:13:10.814837Z     info    starting Http service at [::]:8080
2020-09-03T07:13:10.845782Z     info    ads     Full push, new service istiod.istio-system.svc.cluster.local
2020-09-03T07:13:10.849375Z     info    Namespace controller started
2020-09-03T07:13:10.946167Z     info    ads     Push debounce stable[1] 21: 100.140894ms since last change, 148.738086ms since last push, full=true
2020-09-03T07:13:10.953153Z     info    validationController    Reconcile(enter): add event (v1, Kind=Endpoints) istio-system/istiod
2020-09-03T07:13:10.953526Z     info    ads     XDS: Pushing:2020-09-03T07:13:10Z/0 Services:3 ConnectedEndpoints:0
2020-09-03T07:13:11.114898Z     info    http: TLS handshake error from 172.17.0.8:57777: remote error: tls: bad certificate
2020-09-03T07:13:11.120170Z     info    validationController    Not ready to switch validation to fail-closed: dummy invalid config not rejected
2020-09-03T07:13:11.207741Z     info    validationController    Successfully updated validatingwebhookconfiguration istiod-istio-system (failurePolicy=Ignore,resourceVersion=2450)
2020-09-03T07:13:11.208024Z     info    validationController    Reconcile(enter): update event (v1, Kind=Endpoints) istio-system/istiod
2020-09-03T07:13:11.219185Z     info    validationServer        configuration is invalid: gateway must have at least one server
2020-09-03T07:13:11.222526Z     info    validationController    Endpoint successfully rejected invalid config. Switching to fail-close.
2020-09-03T07:13:11.317491Z     info    validationController    Successfully updated validatingwebhookconfiguration istiod-istio-system (failurePolicy=Fail,resourceVersion=2451)
2020-09-03T07:13:11.317965Z     info    validationController    Reconcile(enter): add event (admissionregistration.k8s.io/v1beta1, Kind=ValidatingWebhookConfiguration) istiod-istio-system
2020-09-03T07:13:11.511781Z     error   validationController    Failed to update validatingwebhookconfiguration istiod-istio-system (failurePolicy=Fail, resourceVersion=2450): Operation cannot be fulfilled on validatingwebhookconfigurations.admissionregistration.k8s.io "istiod-istio-system": the object has been modified; please apply your changes to the latest version and try again
2020-09-03T07:13:11.511971Z     error   Operation cannot be fulfilled on validatingwebhookconfigurations.admissionregistration.k8s.io "istiod-istio-system": the object has been modified; please apply your changes to the latest version and try again
2020-09-03T07:13:11.511993Z     info    validationController    Reconcile(enter): initial request to kickstart reconciliation
2020-09-03T07:13:11.513020Z     info    validationController    validatingwebhookconfiguration istiod-istio-system (failurePolicy=Fail, resourceVersion=2451) is up-to-date. No change required.
2020-09-03T07:13:11.513043Z     info    validationController    Reconcile(enter): update event (admissionregistration.k8s.io/v1beta1, Kind=ValidatingWebhookConfiguration) istiod-istio-system
2020-09-03T07:13:11.513717Z     info    validationController    validatingwebhookconfiguration istiod-istio-system (failurePolicy=Fail, resourceVersion=2451) is up-to-date. No change required.
2020-09-03T07:13:11.513756Z     info    validationController    Reconcile(enter): update event (admissionregistration.k8s.io/v1beta1, Kind=ValidatingWebhookConfiguration) istiod-istio-system
2020-09-03T07:13:11.514359Z     info    validationController    validatingwebhookconfiguration istiod-istio-system (failurePolicy=Fail, resourceVersion=2451) is up-to-date. No change required.
2020-09-03T07:13:11.514397Z     info    validationController    Reconcile(enter): add event (admissionregistration.k8s.io/v1beta1, Kind=ValidatingWebhookConfiguration) istiod-istio-system
2020-09-03T07:13:11.514983Z     info    validationController    validatingwebhookconfiguration istiod-istio-system (failurePolicy=Fail, resourceVersion=2451) is up-to-date. No change required.
2020-09-03T07:13:11.876765Z     info    ads     Push debounce stable[2] 4: 100.101475ms since last change, 159.2595ms since last push, full=true
2020-09-03T07:13:11.877015Z     info    ads     XDS: Pushing:2020-09-03T07:13:11Z/1 Services:5 ConnectedEndpoints:0
2020-09-03T07:13:12.120546Z     info    validationController    Reconcile(enter): retry dry-run creation of invalid config
2020-09-03T07:13:12.121379Z     info    validationController    validatingwebhookconfiguration istiod-istio-system (failurePolicy=Fail, resourceVersion=2451) is up-to-date. No change required.
2020-09-03T07:13:20.714822Z     info    ads     Push Status: {}
2020-09-03T07:13:21.760521Z     info    ads     Push debounce stable[3] 2: 102.81669ms since last change, 114.995831ms since last push, full=false
2020-09-03T07:13:21.760759Z     info    ads     XDS:EDSInc Pushing:2020-09-03T07:13:11Z/1 Services:map[istio-egressgateway.istio-system.svc.cluster.local:{} istio-ingressgateway.istio-system.svc.cluster.local:{}] ConnectedEndpoints:0
2020-09-03T07:13:22.323856Z     info    ads     ADS:CDS: REQ router~10.244.1.4~istio-ingressgateway-5c697d4cd7-qbdfh.istio-system~istio-system.svc.cluster.local-1 version:
2020-09-03T07:13:22.325738Z     info    ads     CDS: PUSH for node:istio-ingressgateway-5c697d4cd7-qbdfh.istio-system clusters:33 services:5 version:2020-09-03T07:13:11Z/1
2020-09-03T07:13:22.355550Z     info    ads     EDS: PUSH for node:istio-ingressgateway-5c697d4cd7-qbdfh.istio-system clusters:32 endpoints:20 empty:16
2020-09-03T07:13:22.366573Z     info    ads     ADS:CDS: REQ router~10.244.1.5~istio-egressgateway-695f5944d8-hl7x5.istio-system~istio-system.svc.cluster.local-2 version:
2020-09-03T07:13:22.367380Z     info    ads     CDS: PUSH for node:istio-egressgateway-695f5944d8-hl7x5.istio-system clusters:33 services:5 version:2020-09-03T07:13:11Z/1
2020-09-03T07:13:22.372346Z     info    ads     LDS: PUSH for node:istio-ingressgateway-5c697d4cd7-qbdfh.istio-system listeners:0
2020-09-03T07:13:22.387666Z     info    ads     EDS: PUSH for node:istio-egressgateway-695f5944d8-hl7x5.istio-system clusters:32 endpoints:20 empty:16
2020-09-03T07:13:22.402671Z     info    ads     LDS: PUSH for node:istio-egressgateway-695f5944d8-hl7x5.istio-system listeners:0
2020-09-03T07:13:23.859988Z     info    ads     Full push, new service istio-egressgateway.istio-system.svc.cluster.local
2020-09-03T07:13:23.960215Z     info    ads     Push debounce stable[4] 1: 100.138834ms since last change, 100.138627ms since last push, full=true
2020-09-03T07:13:23.960691Z     info    ads     XDS: Pushing:2020-09-03T07:13:23Z/2 Services:5 ConnectedEndpoints:2
2020-09-03T07:13:23.960818Z     info    ads     Pushing router~10.244.1.5~istio-egressgateway-695f5944d8-hl7x5.istio-system~istio-system.svc.cluster.local-2
2020-09-03T07:13:23.960924Z     info    ads     Pushing router~10.244.1.4~istio-ingressgateway-5c697d4cd7-qbdfh.istio-system~istio-system.svc.cluster.local-1
2020-09-03T07:13:23.961556Z     info    ads     CDS: PUSH for node:istio-egressgateway-695f5944d8-hl7x5.istio-system clusters:33 services:5 version:2020-09-03T07:13:23Z/2
2020-09-03T07:13:23.961772Z     info    ads     CDS: PUSH for node:istio-ingressgateway-5c697d4cd7-qbdfh.istio-system clusters:33 services:5 version:2020-09-03T07:13:23Z/2
2020-09-03T07:13:23.961862Z     info    ads     EDS: PUSH for node:istio-egressgateway-695f5944d8-hl7x5.istio-system clusters:32 endpoints:26 empty:10
2020-09-03T07:13:23.961920Z     info    ads     LDS: PUSH for node:istio-egressgateway-695f5944d8-hl7x5.istio-system listeners:0
2020-09-03T07:13:23.962126Z     info    ads     EDS: PUSH for node:istio-ingressgateway-5c697d4cd7-qbdfh.istio-system clusters:32 endpoints:26 empty:10
2020-09-03T07:13:23.962388Z     info    ads     LDS: PUSH for node:istio-ingressgateway-5c697d4cd7-qbdfh.istio-system listeners:0
2020-09-03T07:13:24.195018Z     info    ads     Full push, new service istio-ingressgateway.istio-system.svc.cluster.local
2020-09-03T07:13:24.295486Z     info    ads     Push debounce stable[5] 1: 100.142436ms since last change, 100.142218ms since last push, full=true
2020-09-03T07:13:24.295900Z     info    ads     XDS: Pushing:2020-09-03T07:13:24Z/3 Services:5 ConnectedEndpoints:2
2020-09-03T07:13:24.296165Z     info    ads     Pushing router~10.244.1.5~istio-egressgateway-695f5944d8-hl7x5.istio-system~istio-system.svc.cluster.local-2
2020-09-03T07:13:24.296209Z     info    ads     Pushing router~10.244.1.4~istio-ingressgateway-5c697d4cd7-qbdfh.istio-system~istio-system.svc.cluster.local-1
2020-09-03T07:13:24.297230Z     info    ads     CDS: PUSH for node:istio-egressgateway-695f5944d8-hl7x5.istio-system clusters:33 services:5 version:2020-09-03T07:13:24Z/3
2020-09-03T07:13:24.298031Z     info    ads     EDS: PUSH for node:istio-egressgateway-695f5944d8-hl7x5.istio-system clusters:32 endpoints:36 empty:0
2020-09-03T07:13:24.298120Z     info    ads     LDS: PUSH for node:istio-egressgateway-695f5944d8-hl7x5.istio-system listeners:0
2020-09-03T07:13:24.297336Z     info    ads     CDS: PUSH for node:istio-ingressgateway-5c697d4cd7-qbdfh.istio-system clusters:33 services:5 version:2020-09-03T07:13:24Z/3
2020-09-03T07:13:24.301117Z     info    ads     EDS: PUSH for node:istio-ingressgateway-5c697d4cd7-qbdfh.istio-system clusters:32 endpoints:36 empty:0
2020-09-03T07:13:24.301309Z     info    ads     LDS: PUSH for node:istio-ingressgateway-5c697d4cd7-qbdfh.istio-system listeners:0
2020-09-03T07:13:30.714710Z     info    ads     Push Status: {}
```

##  安装bookinfo

```text
master $ kubectl apply -f samples/bookinfo/platform/kube/bookinfo.yaml
service/details created
serviceaccount/bookinfo-details created
deployment.apps/details-v1 created
service/ratings created
serviceaccount/bookinfo-ratings created
deployment.apps/ratings-v1 created
service/reviews created
serviceaccount/bookinfo-reviews created
deployment.apps/reviews-v1 created
deployment.apps/reviews-v2 created
deployment.apps/reviews-v3 created
service/productpage created
serviceaccount/bookinfo-productpage created
deployment.apps/productpage-v1 created
```

安装bookinfo 后服务信息

```text
master $ kubectl get services
NAME          TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
details       ClusterIP   10.105.100.41    <none>        9080/TCP   75s
kubernetes    ClusterIP   10.96.0.1        <none>        443/TCP    61m
productpage   ClusterIP   10.102.170.95    <none>        9080/TCP   72s
ratings       ClusterIP   10.101.239.232   <none>        9080/TCP   74s
reviews       ClusterIP   10.111.13.193    <none>        9080/TCP   74s
master $
```

pod信息

```text
master $ kubectl get pods
NAME                              READY   STATUS    RESTARTS   AGE
details-v1-558b8b4b76-ftc5s       2/2     Running   0          2m7s
productpage-v1-6987489c74-c8qnz   1/2     Running   0          2m4s
ratings-v1-7dc98c7588-xldfc       2/2     Running   0          2m7s
reviews-v1-7f99cc4496-5kvtp       1/2     Running   0          2m5s
reviews-v2-7d79d5bd5d-prdjf       2/2     Running   0          2m6s
reviews-v3-7dbcdcbc56-vzlv8       2/2     Running   0          2m6s
```

所有pod 都ready需要等待一会儿

```text
master $ kubectl get pods
NAME                              READY   STATUS    RESTARTS   AGE
details-v1-558b8b4b76-ftc5s       2/2     Running   0          3m14s
productpage-v1-6987489c74-c8qnz   2/2     Running   0          3m11s
ratings-v1-7dc98c7588-xldfc       2/2     Running   0          3m14s
reviews-v1-7f99cc4496-5kvtp       2/2     Running   0          3m12s
reviews-v2-7d79d5bd5d-prdjf       2/2     Running   0          3m13s
reviews-v3-7dbcdcbc56-vzlv8       2/2     Running   0          3m13s
```

### 校验服务是否运行正常

```text
master $ kubectl exec -it $(kubectl get pod -l app=ratings -o jsonpath='{.items[0].metadata.name}') -c ratings -- curl productpage:9080/productpage | grep -o "<title>.*</title>"
<title>Simple Bookstore App</title>
                                   master $
```

## sidecar 配置

### docker inspect

```text
node01 $ docker inspect f066206956ae
[
    {
        "Id": "f066206956ae10a49feda3dc2833ec16bca941b549fd410e6179662906f4de7e",
        "Created": "2020-09-01T09:11:09.324791126Z",
        "Path": "/usr/local/bin/pilot-agent",
        "Args": [
            "proxy",
            "sidecar",
            "--domain",
            "default.svc.cluster.local",
            "--serviceCluster",
            "details.default",
            "--proxyLogLevel=warning",
            "--proxyComponentLogLevel=misc:error",
            "--trust-domain=cluster.local",
            "--concurrency",
            "2"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 12629,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2020-09-01T09:11:10.110048283Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:18ef15b27b3fbd5a23d00237bf403c06e7c0ac8049e45c78c4e5d5a954b37c45",
        "ResolvConfPath": "/var/lib/docker/containers/57e9b8905441d5cb8d7a5e7eb9e9c385c512f34e0c61c3584dddb7793e83845b/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/57e9b8905441d5cb8d7a5e7eb9e9c385c512f34e0c61c3584dddb7793e83845b/hostname",
        "HostsPath": "/var/lib/kubelet/pods/5d39a5e1-ab3e-44f7-af58-c2fa534defb0/etc-hosts",
        "LogPath": "/var/lib/docker/containers/f066206956ae10a49feda3dc2833ec16bca941b549fd410e6179662906f4de7e/f066206956ae10a49feda3dc2833ec16bca941b549fd410e6179662906f4de7e-json.log",
        "Name": "/k8s_istio-proxy_details-v1-558b8b4b76-twbwm_default_5d39a5e1-ab3e-44f7-af58-c2fa534defb0_0",
        "RestartCount": 0,
        "Driver": "overlay",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "docker-default",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": [
                "/var/lib/kubelet/pods/5d39a5e1-ab3e-44f7-af58-c2fa534defb0/volumes/kubernetes.io~configmap/istiod-ca-cert:/var/run/secrets/istio:ro",
                "/var/lib/kubelet/pods/5d39a5e1-ab3e-44f7-af58-c2fa534defb0/volumes/kubernetes.io~empty-dir/istio-data:/var/lib/istio/data",
                "/var/lib/kubelet/pods/5d39a5e1-ab3e-44f7-af58-c2fa534defb0/volumes/kubernetes.io~empty-dir/istio-envoy:/etc/istio/proxy",
                "/var/lib/kubelet/pods/5d39a5e1-ab3e-44f7-af58-c2fa534defb0/volumes/kubernetes.io~downward-api/istio-podinfo:/etc/istio/pod:ro",
                "/var/lib/kubelet/pods/5d39a5e1-ab3e-44f7-af58-c2fa534defb0/volumes/kubernetes.io~secret/bookinfo-details-token-4wwmq:/var/run/secrets/kubernetes.io/serviceaccount:ro",
                "/var/lib/kubelet/pods/5d39a5e1-ab3e-44f7-af58-c2fa534defb0/etc-hosts:/etc/hosts",
                "/var/lib/kubelet/pods/5d39a5e1-ab3e-44f7-af58-c2fa534defb0/containers/istio-proxy/95db8595:/dev/termination-log"
            ],
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {
                    "max-size": "100m"
                }
            },
            "NetworkMode": "container:57e9b8905441d5cb8d7a5e7eb9e9c385c512f34e0c61c3584dddb7793e83845b",
            "PortBindings": null,
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": null,
            "CapDrop": [
                "ALL"
            ],
            "Capabilities": null,
            "Dns": null,
            "DnsOptions": null,
            "DnsSearch": null,
            "ExtraHosts": null,
            "GroupAdd": [
                "1337"
            ],
            "IpcMode": "container:57e9b8905441d5cb8d7a5e7eb9e9c385c512f34e0c61c3584dddb7793e83845b",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 990,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": true,
            "SecurityOpt": [
                "no-new-privileges",
                "seccomp=unconfined"
            ],
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 10,
            "Memory": 1073741824,
            "NanoCpus": 0,
            "CgroupParent": "kubepods-burstable-pod5d39a5e1_ab3e_44f7_af58_c2fa534defb0.slice",
            "BlkioWeight": 0,
            "BlkioWeightDevice": null,
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 100000,
            "CpuQuota": 200000,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "KernelMemory": 0,
            "KernelMemoryTCP": 0,
            "MemoryReservation": 0,
            "MemorySwap": -1,
            "MemorySwappiness": null,
            "OomKillDisable": false,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/asound",
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay/5725ea92c7151d1b8665c1864a25a21d5092621709480ec6e3da1ed5ae52a8a4/root",
                "MergedDir": "/var/lib/docker/overlay/531ac45161789bcd533ead6f42cf0d8808f7e25da2293cec58f302707857ee79/merged",
                "UpperDir": "/var/lib/docker/overlay/531ac45161789bcd533ead6f42cf0d8808f7e25da2293cec58f302707857ee79/upper",
                "WorkDir": "/var/lib/docker/overlay/531ac45161789bcd533ead6f42cf0d8808f7e25da2293cec58f302707857ee79/work"
            },
            "Name": "overlay"
        },
        "Mounts": [
            {
                "Type": "bind",
                "Source": "/var/lib/kubelet/pods/5d39a5e1-ab3e-44f7-af58-c2fa534defb0/etc-hosts",
                "Destination": "/etc/hosts",
                "Mode": "",
                "RW": true,
                "Propagation": "rprivate"
            },
            {
                "Type": "bind",
                "Source": "/var/lib/kubelet/pods/5d39a5e1-ab3e-44f7-af58-c2fa534defb0/containers/istio-proxy/95db8595",
                "Destination": "/dev/termination-log",
                "Mode": "",
                "RW": true,
                "Propagation": "rprivate"
            },
            {
                "Type": "bind",
                "Source": "/var/lib/kubelet/pods/5d39a5e1-ab3e-44f7-af58-c2fa534defb0/volumes/kubernetes.io~configmap/istiod-ca-cert",
                "Destination": "/var/run/secrets/istio",
                "Mode": "ro",
                "RW": false,
                "Propagation": "rprivate"
            },
            {
                "Type": "bind",
                "Source": "/var/lib/kubelet/pods/5d39a5e1-ab3e-44f7-af58-c2fa534defb0/volumes/kubernetes.io~empty-dir/istio-data",
                "Destination": "/var/lib/istio/data",
                "Mode": "",
                "RW": true,
                "Propagation": "rprivate"
            },
            {
                "Type": "bind",
                "Source": "/var/lib/kubelet/pods/5d39a5e1-ab3e-44f7-af58-c2fa534defb0/volumes/kubernetes.io~empty-dir/istio-envoy",
                "Destination": "/etc/istio/proxy",
                "Mode": "",
                "RW": true,
                "Propagation": "rprivate"
            },
            {
                "Type": "bind",
                "Source": "/var/lib/kubelet/pods/5d39a5e1-ab3e-44f7-af58-c2fa534defb0/volumes/kubernetes.io~downward-api/istio-podinfo",
                "Destination": "/etc/istio/pod",
                "Mode": "ro",
                "RW": false,
                "Propagation": "rprivate"
            },
            {
                "Type": "bind",
                "Source": "/var/lib/kubelet/pods/5d39a5e1-ab3e-44f7-af58-c2fa534defb0/volumes/kubernetes.io~secret/bookinfo-details-token-4wwmq",
                "Destination": "/var/run/secrets/kubernetes.io/serviceaccount",
                "Mode": "ro",
                "RW": false,
                "Propagation": "rprivate"
            }
        ],
        "Config": {
            "Hostname": "details-v1-558b8b4b76-twbwm",
            "Domainname": "",
            "User": "1337:1337",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "ISTIO_META_CLUSTER_ID=Kubernetes",
                "CA_ADDR=istiod.istio-system.svc:15012",
                "POD_NAMESPACE=default",
                "ISTIO_META_APP_CONTAINERS=details",
                "ISTIO_META_OWNER=kubernetes://apis/apps/v1/namespaces/default/deployments/details-v1",
                "ISTIO_META_MESH_ID=cluster.local",
                "DNS_AGENT=",
                "ISTIO_KUBE_APP_PROBERS={}",
                "JWT_POLICY=first-party-jwt",
                "PILOT_CERT_PROVIDER=istiod",
                "INSTANCE_IP=10.244.1.10",
                "HOST_IP=172.17.0.37",
                "PROXY_CONFIG={\"proxyMetadata\":{\"DNS_AGENT\":\"\"}}\n",
                "ISTIO_META_INTERCEPTION_MODE=REDIRECT",
                "ISTIO_META_WORKLOAD_NAME=details-v1",
                "POD_NAME=details-v1-558b8b4b76-twbwm",
                "SERVICE_ACCOUNT=bookinfo-details",
                "CANONICAL_SERVICE=details",
                "CANONICAL_REVISION=v1",
                "ISTIO_META_POD_PORTS=[\n    {\"containerPort\":9080,\"protocol\":\"TCP\"}\n]",
                "KUBERNETES_SERVICE_HOST=10.96.0.1",
                "KUBERNETES_SERVICE_PORT=443",
                "KUBERNETES_SERVICE_PORT_HTTPS=443",
                "KUBERNETES_PORT=tcp://10.96.0.1:443",
                "DETAILS_SERVICE_HOST=10.108.113.18",
                "DETAILS_PORT_9080_TCP=tcp://10.108.113.18:9080",
                "KUBERNETES_PORT_443_TCP_ADDR=10.96.0.1",
                "RATINGS_PORT_9080_TCP=tcp://10.101.225.75:9080",
                "REVIEWS_PORT_9080_TCP_PROTO=tcp",
                "REVIEWS_PORT_9080_TCP_PORT=9080",
                "DETAILS_PORT=tcp://10.108.113.18:9080",
                "DETAILS_PORT_9080_TCP_ADDR=10.108.113.18",
                "PRODUCTPAGE_PORT_9080_TCP_PROTO=tcp",
                "KUBERNETES_PORT_443_TCP=tcp://10.96.0.1:443",
                "RATINGS_SERVICE_HOST=10.101.225.75",
                "RATINGS_SERVICE_PORT_HTTP=9080",
                "RATINGS_PORT_9080_TCP_PROTO=tcp",
                "DETAILS_SERVICE_PORT=9080",
                "DETAILS_SERVICE_PORT_HTTP=9080",
                "PRODUCTPAGE_PORT=tcp://10.105.111.242:9080",
                "KUBERNETES_PORT_443_TCP_PROTO=tcp",
                "RATINGS_SERVICE_PORT=9080",
                "DETAILS_PORT_9080_TCP_PROTO=tcp",
                "PRODUCTPAGE_PORT_9080_TCP=tcp://10.105.111.242:9080",
                "PRODUCTPAGE_PORT_9080_TCP_ADDR=10.105.111.242",
                "RATINGS_PORT_9080_TCP_PORT=9080",
                "REVIEWS_PORT_9080_TCP=tcp://10.111.89.102:9080",
                "PRODUCTPAGE_SERVICE_HOST=10.105.111.242",
                "RATINGS_PORT=tcp://10.101.225.75:9080",
                "RATINGS_PORT_9080_TCP_ADDR=10.101.225.75",
                "REVIEWS_SERVICE_PORT=9080",
                "REVIEWS_SERVICE_PORT_HTTP=9080",
                "PRODUCTPAGE_SERVICE_PORT=9080",
                "PRODUCTPAGE_SERVICE_PORT_HTTP=9080",
                "PRODUCTPAGE_PORT_9080_TCP_PORT=9080",
                "DETAILS_PORT_9080_TCP_PORT=9080",
                "KUBERNETES_PORT_443_TCP_PORT=443",
                "REVIEWS_SERVICE_HOST=10.111.89.102",
                "REVIEWS_PORT=tcp://10.111.89.102:9080",
                "REVIEWS_PORT_9080_TCP_ADDR=10.111.89.102",
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "DEBIAN_FRONTEND=noninteractive",
                "ISTIO_META_ISTIO_PROXY_SHA=istio-proxy:f642a7fd07d0a99944a6e3529566e7985829839c",
                "ISTIO_META_ISTIO_VERSION=1.7.0"
            ],
            "Cmd": [
                "proxy",
                "sidecar",
                "--domain",
                "default.svc.cluster.local",
                "--serviceCluster",
                "details.default",
                "--proxyLogLevel=warning",
                "--proxyComponentLogLevel=misc:error",
                "--trust-domain=cluster.local",
                "--concurrency",
                "2"
            ],
            "Healthcheck": {
                "Test": [
                    "NONE"
                ]
            },
            "Image": "istio/proxyv2@sha256:c1f1b45a4162509f86aa82d0148aef55824454e7204f27f23dddc9d7f4ae7cd1",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": [
                "/usr/local/bin/pilot-agent"
            ],
            "OnBuild": null,
            "Labels": {
                "annotation.io.kubernetes.container.hash": "3a7e0dbb",
                "annotation.io.kubernetes.container.ports": "[{\"name\":\"http-envoy-prom\",\"containerPort\":15090,\"protocol\":\"TCP\"}]",
                "annotation.io.kubernetes.container.restartCount": "0",
                "annotation.io.kubernetes.container.terminationMessagePath": "/dev/termination-log",
                "annotation.io.kubernetes.container.terminationMessagePolicy": "File",
                "annotation.io.kubernetes.pod.terminationGracePeriod": "30",
                "io.kubernetes.container.logpath": "/var/log/pods/default_details-v1-558b8b4b76-twbwm_5d39a5e1-ab3e-44f7-af58-c2fa534defb0/istio-proxy/0.log",
                "io.kubernetes.container.name": "istio-proxy",
                "io.kubernetes.docker.type": "container",
                "io.kubernetes.pod.name": "details-v1-558b8b4b76-twbwm",
                "io.kubernetes.pod.namespace": "default",
                "io.kubernetes.pod.uid": "5d39a5e1-ab3e-44f7-af58-c2fa534defb0",
                "io.kubernetes.sandbox.id": "57e9b8905441d5cb8d7a5e7eb9e9c385c512f34e0c61c3584dddb7793e83845b"
            }
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "",
            "Gateway": "",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "",
            "IPPrefixLen": 0,
            "IPv6Gateway": "",
            "MacAddress": "",
            "Networks": {}
        }
    }
]
```

### docker env

```text
node01 $ docker exec 2630eb5af727 env
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=details-v1-558b8b4b76-t5ptk
SERVICE_ACCOUNT=bookinfo-details
PROXY_CONFIG={"proxyMetadata":{"DNS_AGENT":""}}

ISTIO_META_CLUSTER_ID=Kubernetes
ISTIO_META_MESH_ID=cluster.local
POD_NAMESPACE=default
CA_ADDR=istiod.istio-system.svc:15012
INSTANCE_IP=10.244.1.7
ISTIO_META_WORKLOAD_NAME=details-v1
ISTIO_META_OWNER=kubernetes://apis/apps/v1/namespaces/default/deployments/details-v1
DNS_AGENT=
JWT_POLICY=first-party-jwt
ISTIO_META_POD_PORTS=[
    {"containerPort":9080,"protocol":"TCP"}
]
ISTIO_META_INTERCEPTION_MODE=REDIRECT
POD_NAME=details-v1-558b8b4b76-t5ptk
HOST_IP=172.17.0.9
CANONICAL_SERVICE=details
CANONICAL_REVISION=v1
ISTIO_META_APP_CONTAINERS=details
ISTIO_KUBE_APP_PROBERS={}
PILOT_CERT_PROVIDER=istiod
PRODUCTPAGE_SERVICE_HOST=10.105.104.164
PRODUCTPAGE_PORT_9080_TCP_PORT=9080
DETAILS_PORT_9080_TCP=tcp://10.102.165.52:9080
DETAILS_PORT_9080_TCP_PORT=9080
KUBERNETES_SERVICE_PORT=443
KUBERNETES_PORT_443_TCP_ADDR=10.96.0.1
DETAILS_PORT=tcp://10.102.165.52:9080
RATINGS_PORT=tcp://10.105.58.32:9080
REVIEWS_PORT_9080_TCP_PROTO=tcp
KUBERNETES_PORT_443_TCP_PROTO=tcp
PRODUCTPAGE_SERVICE_PORT=9080
DETAILS_PORT_9080_TCP_ADDR=10.102.165.52
REVIEWS_PORT=tcp://10.102.135.229:9080
KUBERNETES_PORT=tcp://10.96.0.1:443
PRODUCTPAGE_PORT_9080_TCP=tcp://10.105.104.164:9080
DETAILS_PORT_9080_TCP_PROTO=tcp
RATINGS_PORT_9080_TCP=tcp://10.105.58.32:9080
RATINGS_PORT_9080_TCP_PROTO=tcp
PRODUCTPAGE_PORT=tcp://10.105.104.164:9080
DETAILS_SERVICE_PORT_HTTP=9080
RATINGS_PORT_9080_TCP_ADDR=10.105.58.32
REVIEWS_SERVICE_PORT_HTTP=9080
REVIEWS_PORT_9080_TCP_PORT=9080
PRODUCTPAGE_PORT_9080_TCP_PROTO=tcp
PRODUCTPAGE_PORT_9080_TCP_ADDR=10.105.104.164
RATINGS_SERVICE_HOST=10.105.58.32
RATINGS_SERVICE_PORT=9080
REVIEWS_SERVICE_HOST=10.102.135.229
KUBERNETES_PORT_443_TCP=tcp://10.96.0.1:443
PRODUCTPAGE_SERVICE_PORT_HTTP=9080
DETAILS_SERVICE_PORT=9080
RATINGS_SERVICE_PORT_HTTP=9080
RATINGS_PORT_9080_TCP_PORT=9080
REVIEWS_SERVICE_PORT=9080
REVIEWS_PORT_9080_TCP=tcp://10.102.135.229:9080
REVIEWS_PORT_9080_TCP_ADDR=10.102.135.229
KUBERNETES_SERVICE_PORT_HTTPS=443
DETAILS_SERVICE_HOST=10.102.165.52
KUBERNETES_SERVICE_HOST=10.96.0.1
KUBERNETES_PORT_443_TCP_PORT=443
DEBIAN_FRONTEND=noninteractive
ISTIO_META_ISTIO_PROXY_SHA=istio-proxy:f642a7fd07d0a99944a6e3529566e7985829839c
ISTIO_META_ISTIO_VERSION=1.7.0
HOME=/home/istio-proxy
```

### 

### sidecar-init 参数

```text
node01 $ docker inspect 4421956e0144
[
    {
        "Id": "4421956e0144d71c95d0c20a61380a457fe786a4cca2da965ba9f98b29b8c010",
        "Created": "2020-09-01T09:09:12.786344637Z",
        "Path": "/usr/local/bin/pilot-agent",
        "Args": [
            "istio-iptables",
            "-p",
            "15001",
            "-z",
            "15006",
            "-u",
            "1337",
            "-m",
            "REDIRECT",
            "-i",
            "*",
            "-x",
            "",
            "-b",
            "*",
            "-d",
            "15090,15021,15020"
        ],
        "State": {
            "Status": "exited",
            "Running": false,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 0,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2020-09-01T09:09:13.254565892Z",
            "FinishedAt": "2020-09-01T09:09:13.590305056Z"
        },
        "Image": "sha256:18ef15b27b3fbd5a23d00237bf403c06e7c0ac8049e45c78c4e5d5a954b37c45",
        "ResolvConfPath": "/var/lib/docker/containers/57e9b8905441d5cb8d7a5e7eb9e9c385c512f34e0c61c3584dddb7793e83845b/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/57e9b8905441d5cb8d7a5e7eb9e9c385c512f34e0c61c3584dddb7793e83845b/hostname",
        "HostsPath": "/var/lib/kubelet/pods/5d39a5e1-ab3e-44f7-af58-c2fa534defb0/etc-hosts",
        "LogPath": "/var/lib/docker/containers/4421956e0144d71c95d0c20a61380a457fe786a4cca2da965ba9f98b29b8c010/4421956e0144d71c95d0c20a61380a457fe786a4cca2da965ba9f98b29b8c010-json.log",
        "Name": "/k8s_istio-init_details-v1-558b8b4b76-twbwm_default_5d39a5e1-ab3e-44f7-af58-c2fa534defb0_0",
        "RestartCount": 0,
        "Driver": "overlay",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "docker-default",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": [
                "/var/lib/kubelet/pods/5d39a5e1-ab3e-44f7-af58-c2fa534defb0/volumes/kubernetes.io~secret/bookinfo-details-token-4wwmq:/var/run/secrets/kubernetes.io/serviceaccount:ro",
                "/var/lib/kubelet/pods/5d39a5e1-ab3e-44f7-af58-c2fa534defb0/etc-hosts:/etc/hosts",
                "/var/lib/kubelet/pods/5d39a5e1-ab3e-44f7-af58-c2fa534defb0/containers/istio-init/7f6f33e7:/dev/termination-log"
            ],
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {
                    "max-size": "100m"
                }
            },
            "NetworkMode": "container:57e9b8905441d5cb8d7a5e7eb9e9c385c512f34e0c61c3584dddb7793e83845b",
            "PortBindings": null,
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": [
                "NET_ADMIN",
                "NET_RAW"
            ],
            "CapDrop": [
                "ALL"
            ],
            "Capabilities": null,
            "Dns": null,
            "DnsOptions": null,
            "DnsSearch": null,
            "ExtraHosts": null,
            "GroupAdd": [
                "1337"
            ],
            "IpcMode": "container:57e9b8905441d5cb8d7a5e7eb9e9c385c512f34e0c61c3584dddb7793e83845b",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 998,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": [
                "no-new-privileges",
                "seccomp=unconfined"
            ],
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 10,
            "Memory": 1073741824,
            "NanoCpus": 0,
            "CgroupParent": "kubepods-burstable-pod5d39a5e1_ab3e_44f7_af58_c2fa534defb0.slice",
            "BlkioWeight": 0,
            "BlkioWeightDevice": null,
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 100000,
            "CpuQuota": 200000,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "KernelMemory": 0,
            "KernelMemoryTCP": 0,
            "MemoryReservation": 0,
            "MemorySwap": -1,
            "MemorySwappiness": null,
            "OomKillDisable": false,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/asound",
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay/5725ea92c7151d1b8665c1864a25a21d5092621709480ec6e3da1ed5ae52a8a4/root",
                "MergedDir": "/var/lib/docker/overlay/0409f537740171d14176aa2572405859a5c672534b10bf6fef0e18cef4248b45/merged",
                "UpperDir": "/var/lib/docker/overlay/0409f537740171d14176aa2572405859a5c672534b10bf6fef0e18cef4248b45/upper",
                "WorkDir": "/var/lib/docker/overlay/0409f537740171d14176aa2572405859a5c672534b10bf6fef0e18cef4248b45/work"
            },
            "Name": "overlay"
        },
        "Mounts": [
            {
                "Type": "bind",
                "Source": "/var/lib/kubelet/pods/5d39a5e1-ab3e-44f7-af58-c2fa534defb0/volumes/kubernetes.io~secret/bookinfo-details-token-4wwmq",
                "Destination": "/var/run/secrets/kubernetes.io/serviceaccount",
                "Mode": "ro",
                "RW": false,
                "Propagation": "rprivate"
            },
            {
                "Type": "bind",
                "Source": "/var/lib/kubelet/pods/5d39a5e1-ab3e-44f7-af58-c2fa534defb0/etc-hosts",
                "Destination": "/etc/hosts",
                "Mode": "",
                "RW": true,
                "Propagation": "rprivate"
            },
            {
                "Type": "bind",
                "Source": "/var/lib/kubelet/pods/5d39a5e1-ab3e-44f7-af58-c2fa534defb0/containers/istio-init/7f6f33e7",
                "Destination": "/dev/termination-log",
                "Mode": "",
                "RW": true,
                "Propagation": "rprivate"
            }
        ],
        "Config": {
            "Hostname": "details-v1-558b8b4b76-twbwm",
            "Domainname": "",
            "User": "0:0",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "DNS_AGENT=",
                "DETAILS_PORT=tcp://10.108.113.18:9080",
                "RATINGS_SERVICE_HOST=10.101.225.75",
                "REVIEWS_PORT_9080_TCP_PROTO=tcp",
                "KUBERNETES_SERVICE_PORT_HTTPS=443",
                "KUBERNETES_SERVICE_PORT=443",
                "KUBERNETES_PORT_443_TCP_PROTO=tcp",
                "DETAILS_PORT_9080_TCP_PORT=9080",
                "RATINGS_SERVICE_PORT=9080",
                "RATINGS_PORT_9080_TCP_ADDR=10.101.225.75",
                "REVIEWS_PORT_9080_TCP_ADDR=10.111.89.102",
                "KUBERNETES_PORT_443_TCP=tcp://10.96.0.1:443",
                "KUBERNETES_PORT_443_TCP_ADDR=10.96.0.1",
                "DETAILS_SERVICE_PORT=9080",
                "DETAILS_PORT_9080_TCP=tcp://10.108.113.18:9080",
                "PRODUCTPAGE_SERVICE_PORT_HTTP=9080",
                "PRODUCTPAGE_PORT_9080_TCP=tcp://10.105.111.242:9080",
                "REVIEWS_PORT=tcp://10.111.89.102:9080",
                "KUBERNETES_SERVICE_HOST=10.96.0.1",
                "KUBERNETES_PORT_443_TCP_PORT=443",
                "PRODUCTPAGE_PORT_9080_TCP_PROTO=tcp",
                "RATINGS_PORT_9080_TCP=tcp://10.101.225.75:9080",
                "DETAILS_SERVICE_PORT_HTTP=9080",
                "PRODUCTPAGE_SERVICE_HOST=10.105.111.242",
                "REVIEWS_PORT_9080_TCP_PORT=9080",
                "DETAILS_PORT_9080_TCP_PROTO=tcp",
                "DETAILS_PORT_9080_TCP_ADDR=10.108.113.18",
                "PRODUCTPAGE_SERVICE_PORT=9080",
                "RATINGS_SERVICE_PORT_HTTP=9080",
                "RATINGS_PORT=tcp://10.101.225.75:9080",
                "RATINGS_PORT_9080_TCP_PORT=9080",
                "REVIEWS_PORT_9080_TCP=tcp://10.111.89.102:9080",
                "KUBERNETES_PORT=tcp://10.96.0.1:443",
                "DETAILS_SERVICE_HOST=10.108.113.18",
                "PRODUCTPAGE_PORT_9080_TCP_ADDR=10.105.111.242",
                "RATINGS_PORT_9080_TCP_PROTO=tcp",
                "REVIEWS_SERVICE_HOST=10.111.89.102",
                "REVIEWS_SERVICE_PORT_HTTP=9080",
                "REVIEWS_SERVICE_PORT=9080",
                "PRODUCTPAGE_PORT=tcp://10.105.111.242:9080",
                "PRODUCTPAGE_PORT_9080_TCP_PORT=9080",
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "DEBIAN_FRONTEND=noninteractive",
                "ISTIO_META_ISTIO_PROXY_SHA=istio-proxy:f642a7fd07d0a99944a6e3529566e7985829839c",
                "ISTIO_META_ISTIO_VERSION=1.7.0"
            ],
            "Cmd": [
                "istio-iptables",
                "-p",
                "15001",
                "-z",
                "15006",
                "-u",
                "1337",
                "-m",
                "REDIRECT",
                "-i",
                "*",
                "-x",
                "",
                "-b",
                "*",
                "-d",
                "15090,15021,15020"
            ],
            "Healthcheck": {
                "Test": [
                    "NONE"
                ]
            },
            "Image": "istio/proxyv2@sha256:c1f1b45a4162509f86aa82d0148aef55824454e7204f27f23dddc9d7f4ae7cd1",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": [
                "/usr/local/bin/pilot-agent"
            ],
            "OnBuild": null,
            "Labels": {
                "annotation.io.kubernetes.container.hash": "8d7d0774",
                "annotation.io.kubernetes.container.restartCount": "0",
                "annotation.io.kubernetes.container.terminationMessagePath": "/dev/termination-log",
                "annotation.io.kubernetes.container.terminationMessagePolicy": "File",
                "annotation.io.kubernetes.pod.terminationGracePeriod": "30",
                "io.kubernetes.container.logpath": "/var/log/pods/default_details-v1-558b8b4b76-twbwm_5d39a5e1-ab3e-44f7-af58-c2fa534defb0/istio-init/0.log",
                "io.kubernetes.container.name": "istio-init",
                "io.kubernetes.docker.type": "container",
                "io.kubernetes.pod.name": "details-v1-558b8b4b76-twbwm",
                "io.kubernetes.pod.namespace": "default",
                "io.kubernetes.pod.uid": "5d39a5e1-ab3e-44f7-af58-c2fa534defb0",
                "io.kubernetes.sandbox.id": "57e9b8905441d5cb8d7a5e7eb9e9c385c512f34e0c61c3584dddb7793e83845b"
            }
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "",
            "Gateway": "",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "",
            "IPPrefixLen": 0,
            "IPv6Gateway": "",
            "MacAddress": "",
            "Networks": {}
        }
    }
]
```

### envoy-rev0.json

```text
$ cat /etc/istio/proxy/envoy-rev0.json
{
  "node": {
    "id": "sidecar~10.244.1.10~details-v1-558b8b4b76-twbwm.default~default.svc.cluster.local",
    "cluster": "details.default",
    "locality": {
    },
    "metadata": {"APP_CONTAINERS":"details","CLUSTER_ID":"Kubernetes","EXCHANGE_KEYS":"NAME,NAMESPACE,INSTANCE_IPS,LABELS,OWNER,PLATFORM_METADATA,WORKLOAD_NAME,MESH_ID,SERVICE_ACCOUNT,CLUSTER_ID","INSTANCE_IPS":"10.244.1.10","INTERCEPTION_MODE":"REDIRECT","ISTIO_PROXY_SHA":"istio-proxy:f642a7fd07d0a99944a6e3529566e7985829839c","ISTIO_VERSION":"1.7.0","LABELS":{"app":"details","istio.io/rev":"default","pod-template-hash":"558b8b4b76","security.istio.io/tlsMode":"istio","service.istio.io/canonical-name":"details","service.istio.io/canonical-revision":"v1","version":"v1"},"MESH_ID":"cluster.local","NAME":"details-v1-558b8b4b76-twbwm","NAMESPACE":"default","OWNER":"kubernetes://apis/apps/v1/namespaces/default/deployments/details-v1","POD_PORTS":"[{\"containerPort\":9080,\"protocol\":\"TCP\"}]","PROXY_CONFIG":{"binaryPath":"/usr/local/bin/envoy","concurrency":2,"configPath":"./etc/istio/proxy","controlPlaneAuthPolicy":"MUTUAL_TLS","discoveryAddress":"istiod.istio-system.svc:15012","drainDuration":"45s","envoyAccessLogService":{},"envoyMetricsService":{},"parentShutdownDuration":"60s","proxyAdminPort":15000,"proxyMetadata":{"DNS_AGENT":""},"serviceCluster":"details.default","statNameLength":189,"statusPort":15020,"terminationDrainDuration":"5s","tracing":{"zipkin":{"address":"zipkin.istio-system:9411"}}},"SDS":"true","SERVICE_ACCOUNT":"bookinfo-details","WORKLOAD_NAME":"details-v1"}
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

### ps -ef

```text
node01 $ docker exec f066206956ae ps -ef > ps.data
node01 $ cat ps.data
UID        PID  PPID  C STIME TTY          TIME CMD
istio-p+     1     0  0 09:11 ?        00:00:01 /usr/local/bin/pilot-agent proxy sidecar --domain default.svc.cluster.local --serviceCluster details.default --proxyLogLevel=warning --proxyComponentLogLevel=misc:error --trust-domain=cluster.local --concurrency 2
istio-p+    12     1  0 09:11 ?        00:00:02 /usr/local/bin/envoy -c etc/istio/proxy/envoy-rev0.json --restart-epoch 0 --drain-time-s 45 --parent-shutdown-time-s 60 --service-cluster details.default --service-node sidecar~10.244.1.10~details-v1-558b8b4b76-twbwm.default~default.svc.cluster.local --local-address-ip-version v4 --log-format-prefix-with-location 0 --log-format %Y-%m-%dT%T.%fZ.%l.envoy %n.%v -l warning --component-log-level misc:error --concurrency 2
istio-p+    42     0  0 09:28 ?        00:00:00 ps -ef
```

## docker logs

```text
docker logs  ee0579f2df3f
2020-09-16T09:09:18.826936Z     info    FLAG: --concurrency="2"
2020-09-16T09:09:18.826981Z     info    FLAG: --disableInternalTelemetry="false"
2020-09-16T09:09:18.826988Z     info    FLAG: --domain="default.svc.cluster.local"
2020-09-16T09:09:18.826991Z     info    FLAG: --help="false"
2020-09-16T09:09:18.826994Z     info    FLAG: --id=""
2020-09-16T09:09:18.826997Z     info    FLAG: --ip=""
2020-09-16T09:09:18.827000Z     info    FLAG: --log_as_json="false"
2020-09-16T09:09:18.828823Z     info    FLAG: --log_caller=""
2020-09-16T09:09:18.829001Z     info    FLAG: --log_output_level="default:info"
2020-09-16T09:09:18.829135Z     info    FLAG: --log_rotate=""
2020-09-16T09:09:18.829258Z     info    FLAG: --log_rotate_max_age="30"
2020-09-16T09:09:18.829275Z     info    FLAG: --log_rotate_max_backups="1000"
2020-09-16T09:09:18.829281Z     info    FLAG: --log_rotate_max_size="104857600"
2020-09-16T09:09:18.829379Z     info    FLAG: --log_stacktrace_level="default:none"
2020-09-16T09:09:18.829526Z     info    FLAG: --log_target="[stdout]"
2020-09-16T09:09:18.829544Z     info    FLAG: --meshConfig="./etc/istio/config/mesh"
2020-09-16T09:09:18.829549Z     info    FLAG: --mixerIdentity=""
2020-09-16T09:09:18.829685Z     info    FLAG: --outlierLogPath=""
2020-09-16T09:09:18.829700Z     info    FLAG: --proxyComponentLogLevel="misc:error"
2020-09-16T09:09:18.829705Z     info    FLAG: --proxyLogLevel="warning"
2020-09-16T09:09:18.829708Z     info    FLAG: --serviceCluster="details.default"
2020-09-16T09:09:18.829907Z     info    FLAG: --serviceregistry="Kubernetes"
2020-09-16T09:09:18.829913Z     info    FLAG: --stsPort="0"
2020-09-16T09:09:18.829916Z     info    FLAG: --templateFile=""
2020-09-16T09:09:18.829920Z     info    FLAG: --tokenManagerPlugin="GoogleTokenExchange"
2020-09-16T09:09:18.829924Z     info    FLAG: --trust-domain="cluster.local"
2020-09-16T09:09:18.830110Z     info    Version 1.7.1-4e26c697ce460dc8d3b25b25818fb0163ca16394-Clean
2020-09-16T09:09:18.830442Z     info    Obtained private IP [10.244.2.6]
2020-09-16T09:09:18.830710Z     info    Apply proxy config from env {"proxyMetadata":{"DNS_AGENT":""}}

2020-09-16T09:09:18.832170Z     info    Effective config: binaryPath: /usr/local/bin/envoy
concurrency: 2
configPath: ./etc/istio/proxy
controlPlaneAuthPolicy: MUTUAL_TLS
discoveryAddress: istiod.istio-system.svc:15012
drainDuration: 45s
envoyAccessLogService: {}
envoyMetricsService: {}
parentShutdownDuration: 60s
proxyAdminPort: 15000
proxyMetadata:
  DNS_AGENT: ""
serviceCluster: details.default
statNameLength: 189
statusPort: 15020
terminationDrainDuration: 5s
tracing:
  zipkin:
    address: zipkin.istio-system:9411

2020-09-16T09:09:18.832445Z     info    Proxy role: &model.Proxy{Type:"sidecar", IPAddresses:[]string{"10.244.2.6"}, ID:"details-v1-558b8b4b76-sdnjx.default", Locality:(*envoy_config_core_v3.Locality)(nil), DNSDomain:"default.svc.cluster.local", ConfigNamespace:"", Metadata:(*model.NodeMetadata)(nil), SidecarScope:(*model.SidecarScope)(nil), PrevSidecarScope:(*model.SidecarScope)(nil), MergedGateway:(*model.MergedGateway)(nil), ServiceInstances:[]*model.ServiceInstance(nil), IstioVersion:(*model.IstioVersion)(nil), ipv6Support:false, ipv4Support:false, GlobalUnicastIP:"", XdsResourceGenerator:model.XdsResourceGenerator(nil), Active:map[string]*model.WatchedResource(nil), ActiveExperimental:map[string]*model.WatchedResource(nil), RequestedTypes:struct { CDS string; EDS string; RDS string; LDS string }{CDS:"", EDS:"", RDS:"", LDS:""}}
2020-09-16T09:09:18.832478Z     info    JWT policy is first-party-jwt
2020-09-16T09:09:18.832529Z     info    PilotSAN []string{"istiod.istio-system.svc"}
2020-09-16T09:09:18.832537Z     info    MixerSAN []string{"spiffe://cluster.local/ns/istio-system/sa/istio-mixer-service-account"}
2020-09-16T09:09:18.832711Z     info    sa.serverOptions.CAEndpoint == istiod.istio-system.svc:15012
2020-09-16T09:09:18.832851Z     info    Using user-configured CA istiod.istio-system.svc:15012
2020-09-16T09:09:18.832864Z     info    istiod uses self-issued certificate
2020-09-16T09:09:18.833141Z     info    the CA cert of istiod is: -----BEGIN CERTIFICATE-----
MIIC3TCCAcWgAwIBAgIQFaFtMzOEgkLiFyM8bUyWdzANBgkqhkiG9w0BAQsFADAY
MRYwFAYDVQQKEw1jbHVzdGVyLmxvY2FsMB4XDTIwMDkxNjA5MDM1OFoXDTMwMDkx
NDA5MDM1OFowGDEWMBQGA1UEChMNY2x1c3Rlci5sb2NhbDCCASIwDQYJKoZIhvcN
AQEBBQADggEPADCCAQoCggEBANRMgN+wPrbKdOjTi0s8ZtrYZDkTFVc/tEZQzqNF
+e8JJBSaMM1bxq5Rdo9dUrmaPWOkcy5eAg0yx2EmYCqANqueC5NQROptlq1fNs4J
tHvxga1bk2Onw8PAW96QvH3EDeNjAD2OKJWfLinT35PjcDOtrp/Hday2HApQtwPM
37UJgbpu+k6yLSEqbOJlInJdxc/hMI87DSSP2uOlNs5q7zb/GWRIVqF1tLbOPmx+
rajPF6xQYg8xOYgLuHO8V3LlsE+6qxw9DN3tVMC2JsmahLAo+0vgue3Edv2wigeW
e1CIbb5WvZngZ6fx2uQhaFnKEkcWrdUen07WwGF0aYvaL78CAwEAAaMjMCEwDgYD
VR0PAQH/BAQDAgIEMA8GA1UdEwEB/wQFMAMBAf8wDQYJKoZIhvcNAQELBQADggEB
AJHPd60kQ5o/VTz/lCnxPYEY5ywcbQpUI1847BBDSWD412jCWmT+GyUaqmuQs8Sq
IIFwsAZVjxR0utmJkWEm07uHeEw722rOttZsTDrchQQe7LKReiIB/sUROiTAmTox
kLYFTEB/KOzDz558JGtXtUkPBJ1XKW55YYBg+vaLKKpgcNILzQY6neqYTihEapdF
sPWISXtIpbGdgR46kUVD53aqG8zHLknNYqB5l0r8oJkY7LoRh9xtrpFZPgs5G3s/
f4pZzhvOruyqHn7/XhRY1ytvVSzXZG1TDNpYjV8n6E3xpV1NThDzaYTyf8umz7La
+r/YWxrLu1aXtKiplVjIqTs=
-----END CERTIFICATE-----

2020-09-16T09:09:18.974265Z     info    sds     SDS gRPC server for workload UDS starts, listening on "./etc/istio/proxy/SDS"

2020-09-16T09:09:18.974464Z     info    sds     Start SDS grpc server
2020-09-16T09:09:18.974724Z     info    Starting proxy agent
2020-09-16T09:09:18.974942Z     info    Opening status port 15020

2020-09-16T09:09:18.975067Z     info    Received new config, creating new Envoy epoch 0
2020-09-16T09:09:18.975604Z     info    Epoch 0 starting
2020-09-16T09:09:18.993874Z     info    Envoy command: [-c etc/istio/proxy/envoy-rev0.json --restart-epoch 0 --drain-time-s 45 --parent-shutdown-time-s 60 --service-cluster details.default --service-node sidecar~10.244.2.6~details-v1-558b8b4b76-sdnjx.default~default.svc.cluster.local --local-address-ip-version v4 --log-format-prefix-with-location 0 --log-format %Y-%m-%dT%T.%fZ   %l      envoy %n        %v -l warning --component-log-level misc:error --concurrency 2]
2020-09-16T09:09:19.051647Z     warning envoy runtime   Unable to use runtime singleton for feature envoy.reloadable_features.activate_fds_next_event_loop
2020-09-16T09:09:19.105107Z     warning envoy config    StreamAggregatedResources gRPC config stream closed: 14, no healthy upstream
2020-09-16T09:09:19.105142Z     warning envoy config    Unable to establish new stream
2020-09-16T09:09:19.154810Z     info    sds     resource:default new connection
2020-09-16T09:09:19.155345Z     info    sds     Skipping waiting for gateway secret
2020-09-16T09:09:19.407400Z     info    cache   Root cert has changed, start rotating root cert for SDS clients
2020-09-16T09:09:19.407429Z     info    cache   GenerateSecret default
2020-09-16T09:09:19.407875Z     info    sds     resource:default pushed key/cert pair to proxy
2020-09-16T09:09:19.409906Z     warning envoy main      there is no configured limit to the number of allowed active connections. Set a limit via the runtime key overload.global_downstream_max_connections
2020-09-16T09:09:19.680390Z     info    sds     resource:ROOTCA new connection
2020-09-16T09:09:19.680460Z     info    sds     Skipping waiting for gateway secret
2020-09-16T09:09:19.680490Z     info    cache   Loaded root cert from certificate ROOTCA
2020-09-16T09:09:19.680628Z     info    sds     resource:ROOTCA pushed root cert to proxy
2020-09-16T09:09:19.771241Z     warning envoy filter    mTLS PERMISSIVE mode is used, connection can be either plaintext or TLS, and client cert can be omitted. Please consider to upgrade to mTLS STRICT mode for more secure configuration that only allows TLS connection with client cert. See https://istio.io/docs/tasks/security/mtls-migration/
2020-09-16T09:09:19.772879Z     warning envoy filter    mTLS PERMISSIVE mode is used, connection can be either plaintext or TLS, and client cert can be omitted. Please consider to upgrade to mTLS STRICT mode for more secure configuration that only allows TLS connection with client cert. See https://istio.io/docs/tasks/security/mtls-migration/
2020-09-16T09:09:20.737091Z     info    Envoy proxy is ready
```

## netstat -anp 

```text
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:15021           0.0.0.0:*               LISTEN      12/envoy
tcp        0      0 0.0.0.0:15090           0.0.0.0:*               LISTEN      12/envoy
tcp        0      0 127.0.0.1:15000         0.0.0.0:*               LISTEN      12/envoy
tcp        0      0 0.0.0.0:9080            0.0.0.0:*               LISTEN      -
tcp        0      0 0.0.0.0:15001           0.0.0.0:*               LISTEN      12/envoy
tcp        0      0 0.0.0.0:15006           0.0.0.0:*               LISTEN      12/envoy
tcp        0      0 127.0.0.1:41636         127.0.0.1:15020         ESTABLISHED 12/envoy
tcp        0      0 127.0.0.1:49348         127.0.0.1:15000         ESTABLISHED 1/pilot-agent
tcp        0      0 10.244.2.6:46180        10.98.183.171:15012     ESTABLISHED 1/pilot-agent
tcp        0      0 127.0.0.1:41666         127.0.0.1:15020         ESTABLISHED 12/envoy
tcp        0      0 10.244.2.6:46188        10.98.183.171:15012     ESTABLISHED 12/envoy
tcp        0      0 127.0.0.1:15000         127.0.0.1:49348         ESTABLISHED 12/envoy
tcp6       0      0 :::15020                :::*                    LISTEN      1/pilot-agent
tcp6       0      0 :::9080                 :::*                    LISTEN      -
tcp6       0      0 127.0.0.1:15020         127.0.0.1:41636         ESTABLISHED 1/pilot-agent
tcp6       0      0 127.0.0.1:15020         127.0.0.1:41666         ESTABLISHED 1/pilot-agent
Active UNIX domain sockets (servers and established)
Proto RefCnt Flags       Type       State         I-Node   PID/Program name     Path
unix  2      [ ACC ]     STREAM     LISTENING     170189   1/pilot-agent        ./etc/istio/proxy/SDS
unix  2      [ ]         DGRAM                    170216   12/envoy             @envoy_domain_socket_parent_0@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
unix  2      [ ]         DGRAM                    170215   12/envoy             @envoy_domain_socket_child_0@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
unix  3      [ ]         STREAM     CONNECTED     170246   12/envoy
unix  3      [ ]         STREAM     CONNECTED     169614   1/pilot-agent        ./etc/istio/proxy/SDS
```

## sidecar init

```text
[
    {
        "Id": "03c062d3183e442ea937deec54983aef17f3ce99d48e0bbb9ea4f3bf40d62b6e",
        "Created": "2020-09-11T07:11:05.411477115Z",
        "Path": "/usr/local/bin/pilot-agent",
        "Args": [
            "istio-iptables",
            "-p",
            "15001",
            "-z",
            "15006",
            "-u",
            "1337",
            "-m",
            "REDIRECT",
            "-i",
            "*",
            "-x",
            "",
            "-b",
            "*",
            "-d",
            "15090,15021,15020"
        ],
        "State": {
            "Status": "exited",
            "Running": false,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 0,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2020-09-11T07:11:05.699657253Z",
            "FinishedAt": "2020-09-11T07:11:06.024278085Z"
        },
        "Image": "sha256:cf428a5eb58cd03d3dd4b9fc48273dff5a8ba4e2724bc537edf95295299469dd",
        "ResolvConfPath": "/var/lib/docker/containers/6454f9c612d3fdb09ca5171e36132fac70aa5f20c86c5969d44ebecb8204d494/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/6454f9c612d3fdb09ca5171e36132fac70aa5f20c86c5969d44ebecb8204d494/hostname",
        "HostsPath": "/var/lib/kubelet/pods/578678d8-c689-4aaa-9d9a-399703b4ac43/etc-hosts",
        "LogPath": "/var/lib/docker/containers/03c062d3183e442ea937deec54983aef17f3ce99d48e0bbb9ea4f3bf40d62b6e/03c062d3183e442ea937deec54983aef17f3ce99d48e0bbb9ea4f3bf40d62b6e-json.log",
        "Name": "/k8s_istio-init_details-v1-558b8b4b76-7s49v_default_578678d8-c689-4aaa-9d9a-399703b4ac43_0",
        "RestartCount": 0,
        "Driver": "overlay",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "docker-default",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": [
                "/var/lib/kubelet/pods/578678d8-c689-4aaa-9d9a-399703b4ac43/volumes/kubernetes.io~secret/bookinfo-details-token-srtlr:/var/run/secrets/kubernetes.io/serviceaccount:ro",
                "/var/lib/kubelet/pods/578678d8-c689-4aaa-9d9a-399703b4ac43/etc-hosts:/etc/hosts",
                "/var/lib/kubelet/pods/578678d8-c689-4aaa-9d9a-399703b4ac43/containers/istio-init/4135ddc1:/dev/termination-log"
            ],
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {
                    "max-size": "100m"
                }
            },
            "NetworkMode": "container:6454f9c612d3fdb09ca5171e36132fac70aa5f20c86c5969d44ebecb8204d494",
            "PortBindings": null,
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": [
                "NET_ADMIN",
                "NET_RAW"
            ],
            "CapDrop": [
                "ALL"
            ],
            "Capabilities": null,
            "Dns": null,
            "DnsOptions": null,
            "DnsSearch": null,
            "ExtraHosts": null,
            "GroupAdd": [
                "1337"
            ],
            "IpcMode": "container:6454f9c612d3fdb09ca5171e36132fac70aa5f20c86c5969d44ebecb8204d494",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 998,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": [
                "no-new-privileges",
                "seccomp=unconfined"
            ],
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 10,
            "Memory": 1073741824,
            "NanoCpus": 0,
            "CgroupParent": "kubepods-burstable-pod578678d8_c689_4aaa_9d9a_399703b4ac43.slice",
            "BlkioWeight": 0,
            "BlkioWeightDevice": null,
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 100000,
            "CpuQuota": 200000,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "KernelMemory": 0,
            "KernelMemoryTCP": 0,
            "MemoryReservation": 0,
            "MemorySwap": -1,
            "MemorySwappiness": null,
            "OomKillDisable": false,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/asound",
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay/c90e5e8a4716966b20b0313933e1e4e65e45a01c55ab4008c82618f3c2630c4f/root",
                "MergedDir": "/var/lib/docker/overlay/21a7838219885471708ec5a0e05eda7cf6bc510acd306fca9f60eb62457f0348/merged",
                "UpperDir": "/var/lib/docker/overlay/21a7838219885471708ec5a0e05eda7cf6bc510acd306fca9f60eb62457f0348/upper",
                "WorkDir": "/var/lib/docker/overlay/21a7838219885471708ec5a0e05eda7cf6bc510acd306fca9f60eb62457f0348/work"
            },
            "Name": "overlay"
        },
        "Mounts": [
            {
                "Type": "bind",
                "Source": "/var/lib/kubelet/pods/578678d8-c689-4aaa-9d9a-399703b4ac43/etc-hosts",
                "Destination": "/etc/hosts",
                "Mode": "",
                "RW": true,
                "Propagation": "rprivate"
            },
            {
                "Type": "bind",
                "Source": "/var/lib/kubelet/pods/578678d8-c689-4aaa-9d9a-399703b4ac43/containers/istio-init/4135ddc1",
                "Destination": "/dev/termination-log",
                "Mode": "",
                "RW": true,
                "Propagation": "rprivate"
            },
            {
                "Type": "bind",
                "Source": "/var/lib/kubelet/pods/578678d8-c689-4aaa-9d9a-399703b4ac43/volumes/kubernetes.io~secret/bookinfo-details-token-srtlr",
                "Destination": "/var/run/secrets/kubernetes.io/serviceaccount",
                "Mode": "ro",
                "RW": false,
                "Propagation": "rprivate"
            }
        ],
        "Config": {
            "Hostname": "details-v1-558b8b4b76-7s49v",
            "Domainname": "",
            "User": "0:0",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "DNS_AGENT=",
                "KUBERNETES_PORT_443_TCP_PORT=443",
                "REVIEWS_PORT=tcp://10.108.168.143:9080",
                "RATINGS_SERVICE_HOST=10.110.16.230",
                "RATINGS_SERVICE_PORT=9080",
                "RATINGS_PORT=tcp://10.110.16.230:9080",
                "KUBERNETES_PORT=tcp://10.96.0.1:443",
                "RATINGS_PORT_9080_TCP=tcp://10.110.16.230:9080",
                "RATINGS_PORT_9080_TCP_PROTO=tcp",
                "KUBERNETES_SERVICE_PORT_HTTPS=443",
                "DETAILS_SERVICE_HOST=10.110.121.187",
                "DETAILS_PORT_9080_TCP_PROTO=tcp",
                "DETAILS_PORT_9080_TCP_ADDR=10.110.121.187",
                "REVIEWS_PORT_9080_TCP_PROTO=tcp",
                "REVIEWS_PORT_9080_TCP_ADDR=10.108.168.143",
                "PRODUCTPAGE_PORT_9080_TCP=tcp://10.108.135.12:9080",
                "KUBERNETES_PORT_443_TCP_ADDR=10.96.0.1",
                "DETAILS_PORT=tcp://10.110.121.187:9080",
                "DETAILS_SERVICE_PORT=9080",
                "DETAILS_SERVICE_PORT_HTTP=9080",
                "DETAILS_PORT_9080_TCP_PORT=9080",
                "REVIEWS_PORT_9080_TCP_PORT=9080",
                "PRODUCTPAGE_SERVICE_PORT=9080",
                "PRODUCTPAGE_PORT=tcp://10.108.135.12:9080",
                "PRODUCTPAGE_PORT_9080_TCP_PROTO=tcp",
                "KUBERNETES_PORT_443_TCP=tcp://10.96.0.1:443",
                "RATINGS_PORT_9080_TCP_PORT=9080",
                "KUBERNETES_PORT_443_TCP_PROTO=tcp",
                "DETAILS_PORT_9080_TCP=tcp://10.110.121.187:9080",
                "REVIEWS_SERVICE_PORT=9080",
                "REVIEWS_SERVICE_PORT_HTTP=9080",
                "REVIEWS_PORT_9080_TCP=tcp://10.108.168.143:9080",
                "PRODUCTPAGE_PORT_9080_TCP_PORT=9080",
                "RATINGS_PORT_9080_TCP_ADDR=10.110.16.230",
                "PRODUCTPAGE_SERVICE_HOST=10.108.135.12",
                "PRODUCTPAGE_SERVICE_PORT_HTTP=9080",
                "KUBERNETES_SERVICE_PORT=443",
                "REVIEWS_SERVICE_HOST=10.108.168.143",
                "PRODUCTPAGE_PORT_9080_TCP_ADDR=10.108.135.12",
                "RATINGS_SERVICE_PORT_HTTP=9080",
                "KUBERNETES_SERVICE_HOST=10.96.0.1",
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "DEBIAN_FRONTEND=noninteractive",
                "ISTIO_META_ISTIO_PROXY_SHA=istio-proxy:262253d9d066f8ef7ed82fd175c28b8f95acbec0",
                "ISTIO_META_ISTIO_VERSION=1.7.1"
            ],
            "Cmd": [
                "istio-iptables",
                "-p",
                "15001",
                "-z",
                "15006",
                "-u",
                "1337",
                "-m",
                "REDIRECT",
                "-i",
                "*",
                "-x",
                "",
                "-b",
                "*",
                "-d",
                "15090,15021,15020"
            ],
            "Healthcheck": {
                "Test": [
                    "NONE"
                ]
            },
            "Image": "istio/proxyv2@sha256:4b6f682755956a957fd81a60ef246db79dae747278ec240752feb2c13135f322",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": [
                "/usr/local/bin/pilot-agent"
            ],
            "OnBuild": null,
            "Labels": {
                "annotation.io.kubernetes.container.hash": "6dce9177",
                "annotation.io.kubernetes.container.restartCount": "0",
                "annotation.io.kubernetes.container.terminationMessagePath": "/dev/termination-log",
                "annotation.io.kubernetes.container.terminationMessagePolicy": "File",
                "annotation.io.kubernetes.pod.terminationGracePeriod": "30",
                "io.kubernetes.container.logpath": "/var/log/pods/default_details-v1-558b8b4b76-7s49v_578678d8-c689-4aaa-9d9a-399703b4ac43/istio-init/0.log",
                "io.kubernetes.container.name": "istio-init",
                "io.kubernetes.docker.type": "container",
                "io.kubernetes.pod.name": "details-v1-558b8b4b76-7s49v",
                "io.kubernetes.pod.namespace": "default",
                "io.kubernetes.pod.uid": "578678d8-c689-4aaa-9d9a-399703b4ac43",
                "io.kubernetes.sandbox.id": "6454f9c612d3fdb09ca5171e36132fac70aa5f20c86c5969d44ebecb8204d494"
            }
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "",
            "Gateway": "",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "",
            "IPPrefixLen": 0,
            "IPv6Gateway": "",
            "MacAddress": "",
            "Networks": {}
        }
    }
]
```

## 卸载ISTIO

### 删除组件

```text
controlplane $ kubectl delete -f samples/addons
Error from server (NotFound): error when deleting "samples/addons/grafana.yaml": serviceaccounts "grafana" not found
Error from server (NotFound): error when deleting "samples/addons/grafana.yaml": configmaps "grafana" not found
Error from server (NotFound): error when deleting "samples/addons/grafana.yaml": services "grafana" not found
Error from server (NotFound): error when deleting "samples/addons/grafana.yaml": deployments.apps "grafana" not found
Error from server (NotFound): error when deleting "samples/addons/grafana.yaml": configmaps "istio-grafana-dashboards" not found
Error from server (NotFound): error when deleting "samples/addons/grafana.yaml": configmaps "istio-services-grafana-dashboards" not found
Error from server (NotFound): error when deleting "samples/addons/jaeger.yaml": deployments.apps "jaeger" not found
Error from server (NotFound): error when deleting "samples/addons/jaeger.yaml": services "tracing" not found
Error from server (NotFound): error when deleting "samples/addons/jaeger.yaml": services "zipkin" not found
Error from server (NotFound): error when deleting "samples/addons/kiali.yaml": customresourcedefinitions.apiextensions.k8s.io "monitoringdashboards.monitoring.kiali.io" not found
Error from server (NotFound): error when deleting "samples/addons/kiali.yaml": serviceaccounts "kiali" not found
Error from server (NotFound): error when deleting "samples/addons/kiali.yaml": configmaps "kiali" not found
Error from server (NotFound): error when deleting "samples/addons/kiali.yaml": clusterroles.rbac.authorization.k8s.io "kiali-viewer" not found
Error from server (NotFound): error when deleting "samples/addons/kiali.yaml": clusterroles.rbac.authorization.k8s.io "kiali" not found
Error from server (NotFound): error when deleting "samples/addons/kiali.yaml": clusterrolebindings.rbac.authorization.k8s.io "kiali" not found
Error from server (NotFound): error when deleting "samples/addons/kiali.yaml": services "kiali" not found
Error from server (NotFound): error when deleting "samples/addons/kiali.yaml": deployments.apps "kiali" not found
Error from server (NotFound): error when deleting "samples/addons/prometheus.yaml": serviceaccounts "prometheus" not found
Error from server (NotFound): error when deleting "samples/addons/prometheus.yaml": configmaps "prometheus" not found
Error from server (NotFound): error when deleting "samples/addons/prometheus.yaml": clusterroles.rbac.authorization.k8s.io "prometheus" not found
Error from server (NotFound): error when deleting "samples/addons/prometheus.yaml": clusterrolebindings.rbac.authorization.k8s.io "prometheus" not found
Error from server (NotFound): error when deleting "samples/addons/prometheus.yaml": services "prometheus" not found
Error from server (NotFound): error when deleting "samples/addons/prometheus.yaml": deployments.apps "prometheus" not found
unable to recognize "samples/addons/kiali.yaml": no matches for kind "MonitoringDashboard" in version "monitoring.kiali.io/v1alpha1"

```

### 删除配置信息

```text
controlplane $ istioctl manifest generate --set profile=demo | kubectl delete --ignore-not-found=true -f -
customresourcedefinition.apiextensions.k8s.io "adapters.config.istio.io" deleted
customresourcedefinition.apiextensions.k8s.io "attributemanifests.config.istio.io" deleted
customresourcedefinition.apiextensions.k8s.io "authorizationpolicies.security.istio.io" deleted
customresourcedefinition.apiextensions.k8s.io "destinationrules.networking.istio.io" deleted
customresourcedefinition.apiextensions.k8s.io "envoyfilters.networking.istio.io" deleted
customresourcedefinition.apiextensions.k8s.io "gateways.networking.istio.io" deleted
customresourcedefinition.apiextensions.k8s.io "handlers.config.istio.io" deleted
customresourcedefinition.apiextensions.k8s.io "httpapispecbindings.config.istio.io" deleted
customresourcedefinition.apiextensions.k8s.io "httpapispecs.config.istio.io" deleted
customresourcedefinition.apiextensions.k8s.io "instances.config.istio.io" deleted
customresourcedefinition.apiextensions.k8s.io "istiooperators.install.istio.io" deleted
customresourcedefinition.apiextensions.k8s.io "peerauthentications.security.istio.io" deleted
customresourcedefinition.apiextensions.k8s.io "quotaspecbindings.config.istio.io" deleted
customresourcedefinition.apiextensions.k8s.io "quotaspecs.config.istio.io" deleted
customresourcedefinition.apiextensions.k8s.io "requestauthentications.security.istio.io" deleted
customresourcedefinition.apiextensions.k8s.io "rules.config.istio.io" deleted
customresourcedefinition.apiextensions.k8s.io "serviceentries.networking.istio.io" deleted
customresourcedefinition.apiextensions.k8s.io "sidecars.networking.istio.io" deleted
customresourcedefinition.apiextensions.k8s.io "templates.config.istio.io" deleted
customresourcedefinition.apiextensions.k8s.io "virtualservices.networking.istio.io" deleted
customresourcedefinition.apiextensions.k8s.io "workloadentries.networking.istio.io" deleted
serviceaccount "istio-egressgateway-service-account" deleted
serviceaccount "istio-ingressgateway-service-account" deleted
serviceaccount "istio-reader-service-account" deleted
serviceaccount "istiod-service-account" deleted
clusterrole.rbac.authorization.k8s.io "istio-reader-istio-system" deleted
clusterrole.rbac.authorization.k8s.io "istiod-istio-system" deleted
clusterrolebinding.rbac.authorization.k8s.io "istio-reader-istio-system" deleted
clusterrolebinding.rbac.authorization.k8s.io "istiod-pilot-istio-system" deleted
validatingwebhookconfiguration.admissionregistration.k8s.io "istiod-istio-system" deleted
configmap "istio" deleted
configmap "istio-sidecar-injector" deleted
mutatingwebhookconfiguration.admissionregistration.k8s.io "istio-sidecar-injector" deleted
deployment.apps "istio-egressgateway" deleted
deployment.apps "istio-ingressgateway" deleted
deployment.apps "istiod" deleted
poddisruptionbudget.policy "istio-egressgateway" deleted
poddisruptionbudget.policy "istio-ingressgateway" deleted
poddisruptionbudget.policy "istiod" deleted
role.rbac.authorization.k8s.io "istio-egressgateway-sds" deleted
role.rbac.authorization.k8s.io "istio-ingressgateway-sds" deleted
role.rbac.authorization.k8s.io "istiod-istio-system" deleted
rolebinding.rbac.authorization.k8s.io "istio-egressgateway-sds" deleted
rolebinding.rbac.authorization.k8s.io "istio-ingressgateway-sds" deleted
rolebinding.rbac.authorization.k8s.io "istiod-istio-system" deleted
service "istio-egressgateway" deleted
service "istio-ingressgateway" deleted
service "istiod" deleted
```

### 删除 命名空间

```text
controlplane $ kubectl delete namespace istio-system
namespace "istio-system" deleted
```



