---
description: 通过docker安装istio，由consul 来做服务注册发现
---

# regist data by consul istio1.7

本文内容主要描述如何基于docker 安装 istio ，并从consul 中获取注册发现数据。

istio 版本为 istio 1.7.0

## 安装依赖

* 系统安装了docker
* docker-compose 
* kubectl 

## 目录结构

```text
[root@k8s2 update]# ll
total 20
drwxr-xr-x 2 root root   43 Sep 18 11:18 bin
drwxr-xr-x 4 root root 4096 Sep 18 12:07 bookinfo
-rwxr-xr-x 1 root root  152 Sep 18 14:08 install.sh
drwxr-xr-x 4 root root  170 Sep 18 14:05 istio
-rwxr-xr-x 1 root root  132 Sep 18 12:03 tail.sh
drwxr-xr-x 2 root root  108 Sep 18 14:10 tmp
-rwxr-xr-x 1 root root  139 Sep 18 14:02 uninstall.sh

```

项目根目录下是目录含义为 ：

* bin 需要使用的二进制文件，docker-compose 和 kubectl
* bookinfo 进行测试的demo 安装和测试脚本
* istio 服务网格安装脚本
* tmp 一些调试的工具脚本
* install.sh uninstall.sh 安装/删除脚本 
* tail.sh  共用脚本，用于在任务执行完后输出一根横线

完整目录

```text
# tree
.
├── bin
│   ├── docker-compose
│   └── kubectl
├── bookinfo
│   ├── bookinfo-down.sh
│   ├── bookinfo-up.sh
│   ├── bookinfo.yaml
│   ├── conf
│   │   └── mesh
│   ├── destination-rule-all.yaml
│   ├── down.sh
│   ├── env.sh
│   ├── proxy
│   │   ├── details-v1
│   │   │   └── envoy-rev0.json
│   │   ├── envoy-rev0.json
│   │   ├── productpage-v1
│   │   │   └── envoy-rev0.json
│   │   ├── ratings-v1
│   │   │   └── envoy-rev0.json
│   │   ├── reviews-v1
│   │   ├── reviews-v2
│   │   └── reviews-v3
│   ├── rule-add.sh
│   ├── rule-delete.sh
│   ├── sidecar-down.sh
│   ├── sidecar-inject.sh
│   ├── sidecar-restart.sh
│   ├── sidecar-up.sh
│   ├── sidecar.yaml
│   ├── up.sh
│   └── virtual-service-review-v3.yaml
├── install.sh
├── istio
│   ├── conf
│   │   ├── kubeconfig
│   │   ├── mesh
│   │   └── meshNetworks
│   ├── configs.yaml
│   ├── crd.sh
│   ├── crd.yaml
│   ├── down.sh
│   ├── env.sh
│   ├── install-base.yaml
│   ├── install.yaml
│   ├── secrets
│   │   └── istio-dns
│   │       ├── cert-chain.pem
│   │       ├── key.pem
│   │       └── self-signed-root.pem
│   └── up.sh
├── tail.sh
├── tmp
│   ├── edsz.sh
│   ├── getenvoyconf.sh
│   ├── log.sh
│   ├── pilot.sh
│   ├── sidecar.sh
│   └── valid.sh
└── uninstall.sh

15 directories, 45 files

```

## 测试指南

在需要的依赖都准备好后，

### 1 安装

```text
./install.sh
```

脚本将同时安装 服务网格 istio 和 demo的bookinfo 用例

### 2 测试

在浏览器中打开 bookinfo访问页面  http://ip:9081/productpage, 能够看到如下页面

![bookinfo](../.gitbook/assets/image.png)

### 3 规则验证

```text
./bookinfo/rule-add.sh
```

该脚本将添加服务网格规则，将所有 reviews 的流量导向 v3 版本

```text
./bookinfo/rule-delete.sh
```

该脚本将删除规则，重新刷新 bookinfo 页面，将看到 reviews 部分显示内容每次刷新都在不同版本间切换。

## 4 卸载

```text
./uninstall.sh
```

该命令将删除所有测试的docker容器

