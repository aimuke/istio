# install docker for centos

## 设置阿里的代理



## 设置yum 代理

在/etc/yum.conf后面添加以下内容：  
1. 如果代理不需要用户名密码认证：  
proxy=http://代理服务器IP地址:端口号  
例如：  
proxy=http://192.168.1.1:8080  
2. 如果需要认证

proxy=http://代理服务器IP地址:端口号  
proxy\_username=代理服务器用户名  
proxy\_password=代理服务器密码  
例如：

proxy=http://192.168.1.1:8080  
proxy\_username=twingao  
proxy\_password=123456

## docker 启动失败

```text
[root@ALY-HKC-PRO-001 ~]# systemctl restart docker
Job for docker.service failed because the control process exited with error code. See "systemctl status docker.service" and "journalctl -xe" for details.

```

修改

第一次

```text
systemctl status docker.service -l
● docker.service - Docker Application Container Engine
   Loaded: loaded (/usr/lib/systemd/system/docker.service; disabled; vendor preset: disabled)
  Drop-In: /etc/systemd/system/docker.service.d
           └─http-proxy.conf
   Active: failed (Result: start-limit) since Fri 2020-09-04 14:42:39 CST; 1min 36s ago
     Docs: https://docs.docker.com
  Process: 14077 ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock (code=exited, status=1/FAILURE)
 Main PID: 14077 (code=exited, status=1/FAILURE)

Sep 04 14:42:37 k8s2.novalocal systemd[1]: docker.service failed.
Sep 04 14:42:39 k8s2.novalocal systemd[1]: docker.service holdoff time over, scheduling restart.
Sep 04 14:42:39 k8s2.novalocal systemd[1]: Stopped Docker Application Container Engine.
Sep 04 14:42:39 k8s2.novalocal systemd[1]: start request repeated too quickly for docker.service
Sep 04 14:42:39 k8s2.novalocal systemd[1]: Failed to start Docker Application Container Engine.
Sep 04 14:42:39 k8s2.novalocal systemd[1]: Unit docker.service entered failed state.
Sep 04 14:42:39 k8s2.novalocal systemd[1]: docker.service failed.
Sep 04 14:43:30 k8s2.novalocal systemd[1]: start request repeated too quickly for docker.service
Sep 04 14:43:30 k8s2.novalocal systemd[1]: Failed to start Docker Application Container Engine.
Sep 04 14:43:30 k8s2.novalocal systemd[1]: docker.service failed.
```

rm /etc/systemd/system/docker.service.d/http-proxy.conf

第二次

有一个帖子说需要删除 /var/run/ 下面的 docker.sock 和 docker.pid

rm -rf /var/run/docker.sock

第三次

{% embed url="https://segmentfault.com/q/1010000002392472" %}

如果是配置了国内镜像，并且镜像文件为 /etc/docker/daemon.json ，则修改文件后缀为.conf 即可正常启动

mv /etc/docker/daemon.json /etc/docker/daemon.conf



## 为 Docker 设置代理

因为众所周知的原因，Docker在国内的使用举步维艰。于是，很多组织在国内提供了`mirror`或者叫`加速器`。  
甚至在1.13的release note中提到微软提供了官方的中国镜像，然后我并没有找到怎么启用，找到了再写。

使用这些镜像或者加速器，拉取各种官方镜像是ok了，自有的镜像也可以放在国内的`registry`。  
但是官方镜像只是沧海一粟，大量的组织或个人的镜像都在`docker hub`，这一部分并没有被镜像同步。  
于是，你还是需要一个代理。

本文假设：

* 你已经有一个http代理了
* Linux发行版的服务管理器使用的是systemd
* 本文写于 Version 17.03.0-ce , 在 Docker 1.13 和 17.03 上是可以的，不排除将来有所改变

顺带说一句，Windows版的在 Settings 的图形界面上直接可以设置代理。

### 关于systemd

很多人可能对systemd还不熟悉，但主流发行版已经全都切换成systemd了，还是很有必要了解一下。

```text
# 重启docker
$ sudo systemctl restart docker
# 对应的旧的命令，其实现在还是支持，效果和上一句一样。
$ sudo service docker restart
# 设置开机启动
$ sudo systemctl enable docker
```

`systemd`是由文件夹`/lib/systemd/system`中的`docker.service`文件定义的。  
我们随便搜索一下systemd教程，就知道怎么样自己编写一个service文件了。  
于是你可能跃跃欲试，把这个文件改一改，代理加进去就好了嘛。

等等，不要着急，如果你自己在做一个自己的服务，当然是要自己直接写这个文件了。但是，我们的docker是从官方源安装的。  
这意味着你现在改了这个文件虽然会生效，但是docker一升级，这个文件又被覆盖了呢。针对这个问题，systemd当然也有解决方案。

你其实只需要创造一个叫 `<something>.conf` 的配置文件，名字随便起，放在  
`/etc/systemd/system/docker.service.d` 目录。你就覆盖了默认的启动配置，并且它会作为你的用户配置一直存在。

### HTTP proxy

好了，现在我们可以开始加代理配置了。

1. 默认情况下这个配置文件夹并不存在，我们要创建它。

   ```text
   $ mkdir -p /etc/systemd/system/docker.service.d
   ```

2. 创建一个文件 `/etc/systemd/system/docker.service.d/http-proxy.conf`  
   包含 `HTTP_PROXY` 环境变量:

   ```text
   [Service]
   Environment="HTTP_PROXY=http://proxy.example.com:80/"
   ```

3. 如果有局域网或者国内的registry，我们还需要使用 `NO_PROXY` 变量声明一下，比如你可以能国内的daocloud.io放有镜像:

   ```text
   [Service]
   Environment="HTTP_PROXY=http://proxy.example.com:80/" "NO_PROXY=localhost,127.0.0.1,daocloud.io"
   ```

4. 刷新systemd配置:

   ```text
   $ sudo systemctl daemon-reload
   ```

5. 用系统命令验证环境变量加上去没:

   ```text
   $ systemctl show --property=Environment docker
   Environment=HTTP_PROXY=http://proxy.example.com:80/
   ```

6. 万事俱备，重启docker，在外面的世界遨游吧:

   ```text
   $ sudo systemctl restart docker
   ```

ps. 本文只是对官方文档的翻译和简化，希望大家还是学会活用google，检索官方文档，比看博客更有时效性。

