---
layout: post
---

# podman构建一个ssh蜜罐

文章中的环境是安装有podman-docker的，所以docker命令实际执行的是podman

## Dockerfile

```
FROM alpine:latest

RUN sed -i -e 's/dl-cdn.alpinelinux.org/mirrors.tuna.tsinghua.edu.cn/' /etc/apk/repositories
RUN apk update && apk add openssh-server && ssh-keygen -A

COPY sshd_config /etc/ssh/sshd_config

CMD /usr/sbin/sshd -D -e
```

sshd\_config 请自行拷贝需要的到Dockerfile所在目录

## 构建

进入Dockfile所在目录

```
docker build -t sshpot .
```

## 运行

先将主机的特权端口调小:

```
sysctl net.ipv4.ip_unprivileged_ports_start=0
```

然后运行:

```
podman run -t --name sshpot --rm -p 22:22/tcp sshpot
```
