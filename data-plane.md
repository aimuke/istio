---
description: 数据面应用相关信息
---

# data plane

应用相关数据

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

