# ssh 内网穿透

> 网上的大部分 ssh 内网穿透文章都写的比较官方，对于初学者的我来说晦涩难懂，在踩了很多坑之后，决心写篇简单易懂的文章介绍下 ssh 内网穿透

## 实际状况

| 机器号  | IP | 用户名 | 备注 |
| ------ | -- | ----- | --- |
| A  | 192.168.1.1 | usr_a | 我的电脑，在局域网中，可以访问 B |
| B  | 8.8.8.8 | usr_b | 代理服务器，在外网中 |

## SSH 参数解释

```bash
-f 后台运行
-C 允许压缩数据
-N 不执行任何命令
-R 将端口绑定到远程服务器，反向代理
-L 将端口绑定到本地客户端，正向代理
```

## 用ssh做正向连接

啥叫正向连接？就是client连上server，然后把server能访问的机器地址和端口（当然也包括server自己）镜像到client的端口上。

```bash
ssh -fCNL [客户端IP或省略]:[客户端端口]:[服务器能访问的IP]:[服务器能访问的IP的端口] [登陆服务器的用户名@服务器IP] -p [服务器ssh服务端口（默认22）]
```

举例说明：
你的本机A，可以ssh到服务器B，将本机A的8080端口映射到服务器B的80端口的http服务，那么可以执行：

```bash
ssh -fCNL 192.168.1.1:8080:8.8.8.8:80 usr_b@8.8.8.8
```

## 用ssh做反向连接

啥叫反向连接？就是client连上server，然后把client能访问的机器地址和端口（也包括client自己）镜像到server的端口上

```bash
ssh -fCNR [服务器IP或省略]:[服务器端口]:[客户端侧能访问的IP]:[客户端侧能访问的IP的端口] [登陆服务器的用户名@服务器IP] -p [服务器ssh服务端口（默认22）]
```

举例说明：
你的本机A，你可以ssh到外网某台服务器B，如果你想让外网所有的能访问服务器B的IP都能访问你的的http服务，那么可以执行：

```bash
ssh -fCNR 8.8.8.8:8080:192.168.1.1:80 usr_b@8.8.8.8
```

## 用ssh做socks代理

假设你内网里某台机器可以上网，但是你不能上网，如果你有ssh到那台机器的权限，那么就可以利用ssh方式建立一个代理socks5，通过代理来上网。

```bash
ssh --fCND [本地IP或省略]:[本地端口] [登陆服务器的用户名@服务器IP] -p [服务器ssh服务端口（默认22）]
```

举例说明：
你的本机A，可以ssh到服务器B，这样你就可以通过服务器B来上网：

```bash
ssh -fCND 192.168.1.1:8080 usr_b@8.8.8.8
```