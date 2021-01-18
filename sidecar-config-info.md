# sidecar 配置信息来源

sidecar 启动的时候的配置信息有几个来源:

* pilot-agent 的启动参数
* 组件pod 的annotation /etc/istio/pod/annotations
* 配置文件 默认为 /etc/istio/config/mesh

组合规则为



