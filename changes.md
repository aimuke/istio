# changes

## 控制面数据面数据同步问题

**问题**

由于同步顺序原因，再sidecar 再接收到请求后才会判断是否需要获取新的 eds（根据cds判断）或 rds（根据lds）判断。如果需要重新获取才则重新发起请求。这样中间可能有一段时间，出现lds中有router，但是rds中没有的情况。或者有cds 但是没有eds 的情况。这时envoy 接收到请求就会出错。

**解决方案**

修改方式为再推送 lds和 cds 的同时，更新服务端监听的rds和eds 数据。根据新的数据重新推送数据到sidecar。



## IP栈判断不准确

**问题**

 istio 代码中判断是否为ipv6 的方式为 遍历ip，存在一个ipv4 就认为是ipv4 环境。

**解决方式**

遍历所有ip，存在任意一个ip为 v6 版本，就认为ipv6 可用。

## 双栈时非默认栈流量被丢弃

**问题**

双栈时，v4和v6的流量都会被转发到 15001或15006 端口。但是15001 和 15006 都只监听在了一个ip栈 上。

假如当前的主用栈为 ipv6，那么15006 只监听在了ipv6 地址上。这时候一个ipv4 地址的请求到来，入向流量被转发到 15006 端口。但是 envoy 没有在 ipv4 的地址上监听15006. 就会导致流量丢失。

出向流量同理。

**解决**

当节点为双栈时，需要设置为监听在 ipv6，同时兼容 ipv4

```text
"name": "virtualInbound",
 "address": {
  "socket_address": {
   "address": "0.0.0.0",
   "port_value": 15006,
   "ipv4_compat": true
  }
 },
```

## 双栈时未注册的非默认栈流量被抛弃

**问题**

双栈环境下，加入pod 中同时有 svr1，svr2，分别监听**不同端口** 8080和8081，其中 svr1 向serviceregistry 进行了注册。但是 svr2 没有进行注册。 所以控制面下发的配置中。virtualInbound 中的 filter 中有 tcp 的paasthrough和 主用栈的passthrough，但是不会有非主用栈的passthrough。同时还有一个 8080 端口的filter。但是没有8081 端口的filter。

这时候如果从外部使用 非主用栈ip 访问 svr2 的端口 8081。 iptable 会将流量导流到 15006 端口，然后依次跟经过filter。由于没有对应的 8081 端口匹配，所以只能走passthrough。但是envoy 中passthrough 配置了 原地址匹配，即分成了 ipv4 的passthrough和ipv6 的passthrough。此时由于没有非主用栈的passthrough。所以该流量会被丢弃。

**解决方式**

在双栈时，同时添加 ipv4的passthrough和 ipv6 的**passthrough**

## 双栈时pilot 域名无法访问

**问题**

前提是 双栈时，如果pilot 只注册了ipv6 的服务地址

这时由于envoy同时有 ipv4和ipv6 的地址，所以生成静态 cluster 时pilot 配置的  "dns\_lookup\_family": "V4\_ONLY", 即根据域名解析时，只向域名服务器获取IPV4 的地址。由于只注册了IPV6的地址，所以域名解析器无法获取对应的IPV4地址。导致域名解析失败。

**解决方式**

在pilot-agent 生成envoy 的静态配置数据的时候，判断是否含有IPV6，如果含有，则应该设置dns解析为同时获取 V4和V6（ **"dns\_lookup\_family": “AUTO”**）.

```text
"dns_lookup_family": "V4_ONLY",  // AUTO
```

## Consul 数据中获取服务命名空间处理

**问题**

从consul 中获取的数据，默认没有命名空间，在istio中统一的都设置为default ，不能满足规则设置的要求。

**修改**

在服务的label中添加命名空间属性，通过解析label 设置服务对应的命名空间





