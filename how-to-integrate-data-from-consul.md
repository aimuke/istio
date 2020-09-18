# how to integrate data from consul

istiod 启动参数 

```text
discoveryCmd.PersistentFlags().StringVar(&serverArgs.MeshConfigFile, "meshConfig", "./etc/istio/config/mesh",
		"File name for Istio mesh configuration. If not specified, a default mesh will be used.")
```

/etc/istio/config/mesh

```text
node01 $ docker exec de5eb27b2d81 cat /etc/istio/config/mesh
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
```

meshconfig 配置信息

[https://istio.io/latest/docs/reference/config/istio.mesh.v1alpha1/](https://istio.io/latest/docs/reference/config/istio.mesh.v1alpha1/)

