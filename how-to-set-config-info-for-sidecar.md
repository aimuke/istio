# sidecar 如何获取配置信息

## 原则

sidecar中一共有两个配置对象：

* meshconfig： 由于设置全局的配置信息，所有envoy实例共用
* proxyconfig：具体的某个envoy 的配置信息， 由meshconfig中的 defaultConfig + 具体实例的配置信息生成

## 来源

sidecar 配置信息主要包含以下来源：

* 默认配置信息
* 配置文件 默认位置为 /etc/istio/config/mesh
* 环境变量  PROXY\_CONFIG
* pod annotation 信息  固定为 /etc/istio/pod/annotations, 取annotation的 proxy.istio.io/config 字段
* pilot-agent 启动参数

先由前四项生成全局的 meshconfig，在由 meshConfig 中的defaultProxy 和 pilot-agent启动参数一起生成当前代理的具体配置信息 proxyConfig

## 规则

1. 首先使用默认值
2. 如果配置文件存在 /etc/istio/config/mesh，则使用配置文件覆盖
3. 如果环境变量存在 PROXY\_CONFIG，使用配置文件覆盖
4. 如果annotation 信息存在，使用其进行覆盖
5. 使用启动参数进行当前代理的设置

代码注释如下

```text
// getMeshConfig gets the mesh config to use for proxy configuration
// 1. First we take the default config
// 2. Then we apply any settings from file (this comes from gateway mounting configmap)
// 3. Then we apply settings from environment variable (this comes from sidecar injection sticking meshconfig here)
// 4. Then we apply overrides from annotation (this comes from annotation on gateway, passed through downward API)
//
// Merging is done by replacement. Any fields present in the overlay will replace those existing fields, while
// untouched fields will remain untouched. This means lists will be replaced, not appended to, for example.
```



