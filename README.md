[![Build Status](https://travis-ci.org/ICKelin/gtun.svg?branch=master)](https://travis-ci.org/ICKelin/gtun)
[doc](./README-EN.md)
### gtun是什么
gtun是一个加速器，包含有加速器的客户端和服务端的实现，同时gtun为所有客户端虚拟出一个局域网，客户端与客户端之间通过局域网ip即可进行访问。

### 限制

gtun自身具备一定的穿墙功能，在法律允许的范围内使用gtun.

gtun自身使用了tun/tap虚拟网卡技术，tun/tap在各个系统对实现有些许差异，因此在使用gtun时需要考虑各个系统的限制：

| 操作系统 | tun | tap | ip加速 | 虚拟局域网 |
|:-------:|:----:| :--: |:--:|:--:|
| Linux   |  是  |  否  | 是 | 是 |
| Mac OS  |  是  |  否  | 是 | 是 |
| Windows |  否  |  是  | 是 | 否 |

### 如何使用gtun
**下载源码与依赖**
``` shell
go get github.com/ICKelin/glog
go get github.com/songgao/water
go get github.com/ICKelin/gtun
```
**编译**

```
./makefile.sh
```
在bin目录下会生成gtun的服务端二进制文件gtun_srv，gtun_srv大部分情况下都是部署在云服务器上，所以之编译了Linux版本，除了gtun_srv之外，还会生成各个系统的gtun_cli文件。

**gtun_srv部署**
需要将gtun_srv部署到云服务器当中，gtun_srv，同时需要开启系统的ipv4转发功能

vi /etc/sysctl.conf
```
net.ipv4.ip_forward
```

给iptables添加规则做SNAT

```
iptables -t nat -I POSTROUTING -j MASQUERADE
```

启动gtun_srv
```

./gtun_srv -h

Usage of ./gtun_srv:
  -g string
    	local tun device ip (default "192.168.253.1")
  -h	print usage
  -k string
    	client authorize key (default "gtun_authorize")
  -l string
    	local listen address (default ":9621")
  -n string
    	nameservers for gtun_cli
  -r string
    	router rules url
  -t	tap device
```

-r 参数指定加速的ip文件列表。

**gtun_cli部署**

```
./gtun_linux_arm_0.0.2 -h
Usage of ./gtun_linux_arm_0.0.2:
  -debug
    	debug mode
  -key string
    	client authorize key (default "gtun_authorize")
  -s string
    	srv address (default "")
  -tap
    	tap mode

```

ping 192.168.253.1测试连通性。

### tips
gtun_srv与gtun_cli运行起来之后，虚拟局域网就已经存在了，任何使用同一个gtun_srv的gtun_cli在知道对方ip地址的前提下，均能进行通信。

**tips0**
    gtun的分流依赖路由表，可以手动加入路由表实现分流,gtun_srv的 -r 基本形同虚设，
    **这不是个bug，这是个福利**

**tips1**
    可以将gtun部署在树莓派上，树莓派使用hostapd之类的软件释放AP，通过连接wifi即可使用gtun

**tips2**
    树莓派的Wi-Fi性能差？可以尝试在树莓派同级网络接入路由器，在路由器配置页面将下一跳网关指向树莓派，同样能够使用树莓派上的ip加速功能。

**tips3**
    在虚拟局域网搭建一些自己的应用，比如说用树莓派搭建网盘，局限是需要使用客户端接入网络，这一问题将考虑在下一版本中解决，通过公网ip+端口访问内网树莓派。


### TODO
- 二层设备虚拟局域网
- IPV6支持
- 公网映射内网地址


### thanks
[songgao/water](https://github.com/songgao/water)

### more
[tun/tap vpn](https://github.com/ICKelin/article/issues/9)

any [issues](https://github.com/ICKelin/gtun/issues/new) are welcome


