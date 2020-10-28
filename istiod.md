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

