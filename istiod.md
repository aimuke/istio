# istiod

## kubectl get service istiod

```text
master $ kubectl get service istiod -n istio-system -o yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"v1","kind":"Service","metadata":{"annotations":{},"labels":{"app":"istiod","install.operator.istio.io/owning-resource":"installed-state","install.operator.istio.io/owning-resource-namespace":"istio-system","istio":"pilot","istio.io/rev":"default","operator.istio.io/component":"Pilot","operator.istio.io/managed":"Reconcile","operator.istio.io/version":"1.7.3","release":"istio"},"name":"istiod","namespace":"istio-system"},"spec":{"ports":[{"name":"grpc-xds","port":15010},{"name":"https-dns","port":15012},{"name":"https-webhook","port":443,"targetPort":15017},{"name":"http-monitoring","port":15014},{"name":"dns-tls","port":853,"protocol":"TCP","targetPort":15053}],"selector":{"app":"istiod","istio":"pilot"}}}
  creationTimestamp: "2020-10-28T06:11:38Z"
  labels:
    app: istiod
    install.operator.istio.io/owning-resource: installed-state
    install.operator.istio.io/owning-resource-namespace: istio-system
    istio: pilot
    istio.io/rev: default
    operator.istio.io/component: Pilot
    operator.istio.io/managed: Reconcile
    operator.istio.io/version: 1.7.3
    release: istio
  managedFields:
  - apiVersion: v1
    fieldsType: FieldsV1
    fieldsV1:
      f:metadata:
        f:annotations:
          .: {}
          f:kubectl.kubernetes.io/last-applied-configuration: {}
        f:labels:
          .: {}
          f:app: {}
          f:install.operator.istio.io/owning-resource: {}
          f:install.operator.istio.io/owning-resource-namespace: {}
          f:istio: {}
          f:istio.io/rev: {}
          f:operator.istio.io/component: {}
          f:operator.istio.io/managed: {}
          f:operator.istio.io/version: {}
          f:release: {}
      f:spec:
        f:ports:
          .: {}
          k:{"port":443,"protocol":"TCP"}:
            .: {}
            f:name: {}
            f:port: {}
            f:protocol: {}
            f:targetPort: {}
          k:{"port":853,"protocol":"TCP"}:
            .: {}
            f:name: {}
            f:port: {}
            f:protocol: {}
            f:targetPort: {}
          k:{"port":15010,"protocol":"TCP"}:
            .: {}
            f:name: {}
            f:port: {}
            f:protocol: {}
            f:targetPort: {}
          k:{"port":15012,"protocol":"TCP"}:
            .: {}
            f:name: {}
            f:port: {}
            f:protocol: {}
            f:targetPort: {}
          k:{"port":15014,"protocol":"TCP"}:
            .: {}
            f:name: {}
            f:port: {}
            f:protocol: {}
            f:targetPort: {}
        f:selector:
          .: {}
          f:app: {}
          f:istio: {}
        f:sessionAffinity: {}
        f:type: {}
    manager: istioctl
    operation: Update
    time: "2020-10-28T06:11:38Z"
  name: istiod
  namespace: istio-system
  resourceVersion: "928"
  selfLink: /api/v1/namespaces/istio-system/services/istiod
  uid: 30f168a1-6780-4528-b7c7-9d669589e007
spec:
  clusterIP: 10.99.250.53
  ports:
  - name: grpc-xds
    port: 15010
    protocol: TCP
    targetPort: 15010
  - name: https-dns
    port: 15012
    protocol: TCP
    targetPort: 15012
  - name: https-webhook
    port: 443
    protocol: TCP
    targetPort: 15017
  - name: http-monitoring
    port: 15014
    protocol: TCP
    targetPort: 15014
  - name: dns-tls
    port: 853
    protocol: TCP
    targetPort: 15053
  selector:
    app: istiod
    istio: pilot
  sessionAffinity: None
  type: ClusterIP
status:
  loadBalancer: {}
```

## deployment

```text
master $ kubectl get deployments istiod  -n istio-system -o yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    deployment.kubernetes.io/revision: "1"
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"labels":{"app":"istiod","install.operator.istio.io/owning-resource":"installed-state","install.operator.istio.io/owning-resource-namespace":"istio-system","istio":"pilot","istio.io/rev":"default","operator.istio.io/component":"Pilot","operator.istio.io/managed":"Reconcile","operator.istio.io/version":"1.7.3","release":"istio"},"name":"istiod","namespace":"istio-system"},"spec":{"replicas":1,"selector":{"matchLabels":{"istio":"pilot"}},"strategy":{"rollingUpdate":{"maxSurge":"100%","maxUnavailable":"25%"}},"template":{"metadata":{"annotations":{"prometheus.io/port":"15014","prometheus.io/scrape":"true","sidecar.istio.io/inject":"false"},"labels":{"app":"istiod","istio":"pilot","istio.io/rev":"default"}},"spec":{"containers":[{"args":["discovery","--monitoringAddr=:15014","--log_output_level=default:info","--domain","cluster.local","--trust-domain=cluster.local","--keepaliveMaxServerConnectionAge","30m"],"env":[{"name":"REVISION","value":"default"},{"name":"JWT_POLICY","value":"first-party-jwt"},{"name":"PILOT_CERT_PROVIDER","value":"istiod"},{"name":"POD_NAME","valueFrom":{"fieldRef":{"apiVersion":"v1","fieldPath":"metadata.name"}}},{"name":"POD_NAMESPACE","valueFrom":{"fieldRef":{"apiVersion":"v1","fieldPath":"metadata.namespace"}}},{"name":"SERVICE_ACCOUNT","valueFrom":{"fieldRef":{"apiVersion":"v1","fieldPath":"spec.serviceAccountName"}}},{"name":"KUBECONFIG","value":"/var/run/secrets/remote/config"},{"name":"PILOT_TRACE_SAMPLING","value":"100"},{"name":"PILOT_ENABLE_PROTOCOL_SNIFFING_FOR_OUTBOUND","value":"true"},{"name":"PILOT_ENABLE_PROTOCOL_SNIFFING_FOR_INBOUND","value":"true"},{"name":"INJECTION_WEBHOOK_CONFIG_NAME","value":"istio-sidecar-injector"},{"name":"ISTIOD_ADDR","value":"istiod.istio-system.svc:15012"},{"name":"PILOT_ENABLE_ANALYSIS","value":"false"},{"name":"CLUSTER_ID","value":"Kubernetes"},{"name":"CENTRAL_ISTIOD","value":"false"}],"image":"docker.io/istio/pilot:1.7.3","name":"discovery","ports":[{"containerPort":8080},{"containerPort":15010},{"containerPort":15017},{"containerPort":15053}],"readinessProbe":{"httpGet":{"path":"/ready","port":8080},"initialDelaySeconds":1,"periodSeconds":3,"timeoutSeconds":5},"resources":{"requests":{"cpu":"10m","memory":"100Mi"}},"securityContext":{"capabilities":{"drop":["ALL"]},"runAsGroup":1337,"runAsNonRoot":true,"runAsUser":1337},"volumeMounts":[{"mountPath":"/etc/istio/config","name":"config-volume"},{"mountPath":"/var/run/secrets/istio-dns","name":"local-certs"},{"mountPath":"/etc/cacerts","name":"cacerts","readOnly":true},{"mountPath":"/var/run/secrets/remote","name":"istio-kubeconfig","readOnly":true},{"mountPath":"/var/lib/istio/inject","name":"inject","readOnly":true}]}],"securityContext":{"fsGroup":1337},"serviceAccountName":"istiod-service-account","volumes":[{"emptyDir":{"medium":"Memory"},"name":"local-certs"},{"name":"cacerts","secret":{"optional":true,"secretName":"cacerts"}},{"name":"istio-kubeconfig","secret":{"optional":true,"secretName":"istio-kubeconfig"}},{"configMap":{"name":"istio-sidecar-injector"},"name":"inject"},{"configMap":{"name":"istio"},"name":"config-volume"}]}}}}
  creationTimestamp: "2020-10-28T06:11:38Z"
  generation: 1
  labels:
    app: istiod
    install.operator.istio.io/owning-resource: installed-state
    install.operator.istio.io/owning-resource-namespace: istio-system
    istio: pilot
    istio.io/rev: default
    operator.istio.io/component: Pilot
    operator.istio.io/managed: Reconcile
    operator.istio.io/version: 1.7.3
    release: istio
  managedFields:
  - apiVersion: apps/v1
    fieldsType: FieldsV1
    fieldsV1:
      f:metadata:
        f:annotations:
          .: {}
          f:kubectl.kubernetes.io/last-applied-configuration: {}
        f:labels:
          .: {}
          f:app: {}
          f:install.operator.istio.io/owning-resource: {}
          f:install.operator.istio.io/owning-resource-namespace: {}
          f:istio: {}
          f:istio.io/rev: {}
          f:operator.istio.io/component: {}
          f:operator.istio.io/managed: {}
          f:operator.istio.io/version: {}
          f:release: {}
      f:spec:
        f:progressDeadlineSeconds: {}
        f:replicas: {}
        f:revisionHistoryLimit: {}
        f:selector:
          f:matchLabels:
            .: {}
            f:istio: {}
        f:strategy:
          f:rollingUpdate:
            .: {}
            f:maxSurge: {}
            f:maxUnavailable: {}
          f:type: {}
        f:template:
          f:metadata:
            f:annotations:
              .: {}
              f:prometheus.io/port: {}
              f:prometheus.io/scrape: {}
              f:sidecar.istio.io/inject: {}
            f:labels:
              .: {}
              f:app: {}
              f:istio: {}
              f:istio.io/rev: {}
          f:spec:
            f:containers:
              k:{"name":"discovery"}:
                .: {}
                f:args: {}
                f:env:
                  .: {}
                  k:{"name":"CENTRAL_ISTIOD"}:
                    .: {}
                    f:name: {}
                    f:value: {}
                  k:{"name":"CLUSTER_ID"}:
                    .: {}
                    f:name: {}
                    f:value: {}
                  k:{"name":"INJECTION_WEBHOOK_CONFIG_NAME"}:
                    .: {}
                    f:name: {}
                    f:value: {}
                  k:{"name":"ISTIOD_ADDR"}:
                    .: {}
                    f:name: {}
                    f:value: {}
                  k:{"name":"JWT_POLICY"}:
                    .: {}
                    f:name: {}
                    f:value: {}
                  k:{"name":"KUBECONFIG"}:
                    .: {}
                    f:name: {}
                    f:value: {}
                  k:{"name":"PILOT_CERT_PROVIDER"}:
                    .: {}
                    f:name: {}
                    f:value: {}
                  k:{"name":"PILOT_ENABLE_ANALYSIS"}:
                    .: {}
                    f:name: {}
                    f:value: {}
                  k:{"name":"PILOT_ENABLE_PROTOCOL_SNIFFING_FOR_INBOUND"}:
                    .: {}
                    f:name: {}
                    f:value: {}
                  k:{"name":"PILOT_ENABLE_PROTOCOL_SNIFFING_FOR_OUTBOUND"}:
                    .: {}
                    f:name: {}
                    f:value: {}
                  k:{"name":"PILOT_TRACE_SAMPLING"}:
                    .: {}
                    f:name: {}
                    f:value: {}
                  k:{"name":"POD_NAME"}:
                    .: {}
                    f:name: {}
                    f:valueFrom:
                      .: {}
                      f:fieldRef:
                        .: {}
                        f:apiVersion: {}
                        f:fieldPath: {}
                  k:{"name":"POD_NAMESPACE"}:
                    .: {}
                    f:name: {}
                    f:valueFrom:
                      .: {}
                      f:fieldRef:
                        .: {}
                        f:apiVersion: {}
                        f:fieldPath: {}
                  k:{"name":"REVISION"}:
                    .: {}
                    f:name: {}
                    f:value: {}
                  k:{"name":"SERVICE_ACCOUNT"}:
                    .: {}
                    f:name: {}
                    f:valueFrom:
                      .: {}
                      f:fieldRef:
                        .: {}
                        f:apiVersion: {}
                        f:fieldPath: {}
                f:image: {}
                f:imagePullPolicy: {}
                f:name: {}
                f:ports:
                  .: {}
                  k:{"containerPort":8080,"protocol":"TCP"}:
                    .: {}
                    f:containerPort: {}
                    f:protocol: {}
                  k:{"containerPort":15010,"protocol":"TCP"}:
                    .: {}
                    f:containerPort: {}
                    f:protocol: {}
                  k:{"containerPort":15017,"protocol":"TCP"}:
                    .: {}
                    f:containerPort: {}
                    f:protocol: {}
                  k:{"containerPort":15053,"protocol":"TCP"}:
                    .: {}
                    f:containerPort: {}
                    f:protocol: {}
                f:readinessProbe:
                  .: {}
                  f:failureThreshold: {}
                  f:httpGet:
                    .: {}
                    f:path: {}
                    f:port: {}
                    f:scheme: {}
                  f:initialDelaySeconds: {}
                  f:periodSeconds: {}
                  f:successThreshold: {}
                  f:timeoutSeconds: {}
                f:resources:
                  .: {}
                  f:requests:
                    .: {}
                    f:cpu: {}
                    f:memory: {}
                f:securityContext:
                  .: {}
                  f:capabilities:
                    .: {}
                    f:drop: {}
                  f:runAsGroup: {}
                  f:runAsNonRoot: {}
                  f:runAsUser: {}
                f:terminationMessagePath: {}
                f:terminationMessagePolicy: {}
                f:volumeMounts:
                  .: {}
                  k:{"mountPath":"/etc/cacerts"}:
                    .: {}
                    f:mountPath: {}
                    f:name: {}
                    f:readOnly: {}
                  k:{"mountPath":"/etc/istio/config"}:
                    .: {}
                    f:mountPath: {}
                    f:name: {}
                  k:{"mountPath":"/var/lib/istio/inject"}:
                    .: {}
                    f:mountPath: {}
                    f:name: {}
                    f:readOnly: {}
                  k:{"mountPath":"/var/run/secrets/istio-dns"}:
                    .: {}
                    f:mountPath: {}
                    f:name: {}
                  k:{"mountPath":"/var/run/secrets/remote"}:
                    .: {}
                    f:mountPath: {}
                    f:name: {}
                    f:readOnly: {}
            f:dnsPolicy: {}
            f:restartPolicy: {}
            f:schedulerName: {}
            f:securityContext:
              .: {}
              f:fsGroup: {}
            f:serviceAccount: {}
            f:serviceAccountName: {}
            f:terminationGracePeriodSeconds: {}
            f:volumes:
              .: {}
              k:{"name":"cacerts"}:
                .: {}
                f:name: {}
                f:secret:
                  .: {}
                  f:defaultMode: {}
                  f:optional: {}
                  f:secretName: {}
              k:{"name":"config-volume"}:
                .: {}
                f:configMap:
                  .: {}
                  f:defaultMode: {}
                  f:name: {}
                f:name: {}
              k:{"name":"inject"}:
                .: {}
                f:configMap:
                  .: {}
                  f:defaultMode: {}
                  f:name: {}
                f:name: {}
              k:{"name":"istio-kubeconfig"}:
                .: {}
                f:name: {}
                f:secret:
                  .: {}
                  f:defaultMode: {}
                  f:optional: {}
                  f:secretName: {}
              k:{"name":"local-certs"}:
                .: {}
                f:emptyDir:
                  .: {}
                  f:medium: {}
                f:name: {}
    manager: istioctl
    operation: Update
    time: "2020-10-28T06:11:38Z"
  - apiVersion: apps/v1
    fieldsType: FieldsV1
    fieldsV1:
      f:metadata:
        f:annotations:
          f:deployment.kubernetes.io/revision: {}
      f:status:
        f:availableReplicas: {}
        f:conditions:
          .: {}
          k:{"type":"Available"}:
            .: {}
            f:lastTransitionTime: {}
            f:lastUpdateTime: {}
            f:message: {}
            f:reason: {}
            f:status: {}
            f:type: {}
          k:{"type":"Progressing"}:
            .: {}
            f:lastTransitionTime: {}
            f:lastUpdateTime: {}
            f:message: {}
            f:reason: {}
            f:status: {}
            f:type: {}
        f:observedGeneration: {}
        f:readyReplicas: {}
        f:replicas: {}
        f:updatedReplicas: {}
    manager: kube-controller-manager
    operation: Update
    time: "2020-10-28T06:11:51Z"
  name: istiod
  namespace: istio-system
  resourceVersion: "992"
  selfLink: /apis/apps/v1/namespaces/istio-system/deployments/istiod
  uid: 3806aa30-26d6-4964-9e33-0c4a568139ce
spec:
  progressDeadlineSeconds: 600
  replicas: 1
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      istio: pilot
  strategy:
    rollingUpdate:
      maxSurge: 100%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      annotations:
        prometheus.io/port: "15014"
        prometheus.io/scrape: "true"
        sidecar.istio.io/inject: "false"
      creationTimestamp: null
      labels:
        app: istiod
        istio: pilot
        istio.io/rev: default
    spec:
      containers:
      - args:
        - discovery
        - --monitoringAddr=:15014
        - --log_output_level=default:info
        - --domain
        - cluster.local
        - --trust-domain=cluster.local
        - --keepaliveMaxServerConnectionAge
        - 30m
        env:
        - name: REVISION
          value: default
        - name: JWT_POLICY
          value: first-party-jwt
        - name: PILOT_CERT_PROVIDER
          value: istiod
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
        - name: SERVICE_ACCOUNT
          valueFrom:
            fieldRef:
              apiVersion: v1
              fieldPath: spec.serviceAccountName
        - name: KUBECONFIG
          value: /var/run/secrets/remote/config
        - name: PILOT_TRACE_SAMPLING
          value: "100"
        - name: PILOT_ENABLE_PROTOCOL_SNIFFING_FOR_OUTBOUND
          value: "true"
        - name: PILOT_ENABLE_PROTOCOL_SNIFFING_FOR_INBOUND
          value: "true"
        - name: INJECTION_WEBHOOK_CONFIG_NAME
          value: istio-sidecar-injector
        - name: ISTIOD_ADDR
          value: istiod.istio-system.svc:15012
        - name: PILOT_ENABLE_ANALYSIS
          value: "false"
        - name: CLUSTER_ID
          value: Kubernetes
        - name: CENTRAL_ISTIOD
          value: "false"
        image: docker.io/istio/pilot:1.7.3
        imagePullPolicy: IfNotPresent
        name: discovery
        ports:
        - containerPort: 8080
          protocol: TCP
        - containerPort: 15010
          protocol: TCP
        - containerPort: 15017
          protocol: TCP
        - containerPort: 15053
          protocol: TCP
        readinessProbe:
          failureThreshold: 3
          httpGet:
            path: /ready
            port: 8080
            scheme: HTTP
          initialDelaySeconds: 1
          periodSeconds: 3
          successThreshold: 1
          timeoutSeconds: 5
        resources:
          requests:
            cpu: 10m
            memory: 100Mi
        securityContext:
          capabilities:
            drop:
            - ALL
          runAsGroup: 1337
          runAsNonRoot: true
          runAsUser: 1337
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
        volumeMounts:
        - mountPath: /etc/istio/config
          name: config-volume
        - mountPath: /var/run/secrets/istio-dns
          name: local-certs
        - mountPath: /etc/cacerts
          name: cacerts
          readOnly: true
        - mountPath: /var/run/secrets/remote
          name: istio-kubeconfig
          readOnly: true
        - mountPath: /var/lib/istio/inject
          name: inject
          readOnly: true
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext:
        fsGroup: 1337
      serviceAccount: istiod-service-account
      serviceAccountName: istiod-service-account
      terminationGracePeriodSeconds: 30
      volumes:
      - emptyDir:
          medium: Memory
        name: local-certs
      - name: cacerts
        secret:
          defaultMode: 420
          optional: true
          secretName: cacerts
      - name: istio-kubeconfig
        secret:
          defaultMode: 420
          optional: true
          secretName: istio-kubeconfig
      - configMap:
          defaultMode: 420
          name: istio-sidecar-injector
        name: inject
      - configMap:
          defaultMode: 420
          name: istio
        name: config-volume
status:
  availableReplicas: 1
  conditions:
  - lastTransitionTime: "2020-10-28T06:11:51Z"
    lastUpdateTime: "2020-10-28T06:11:51Z"
    message: Deployment has minimum availability.
    reason: MinimumReplicasAvailable
    status: "True"
    type: Available
  - lastTransitionTime: "2020-10-28T06:11:38Z"
    lastUpdateTime: "2020-10-28T06:11:51Z"
    message: ReplicaSet "istiod-7556f7fddf" has successfully progressed.
    reason: NewReplicaSetAvailable
    status: "True"
    type: Progressing
  observedGeneration: 1
  readyReplicas: 1
  replicas: 1
  updatedReplicas: 1
```

