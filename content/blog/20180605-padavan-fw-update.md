+++
categories = ["padavan"]
date = "2018-06-05T18:02:24+08:00"
tags = ["update"]
title = "201806 固件更新记录"

+++

先来个简单版的更新记录吧。后续会有更详细的说明。


升级，要注意备份（nvram 和 storage)!!!

-------------------------------------------------------

目前已经更新的型号最新版本有：

* R6800_3.4.3.9-099_20180603-2152
* K2P-512M_3.4.3.9-099_20180603-1620
* K2P-USB_3.4.3.9-099_20180603-1453
* K2P_3.4.3.9-099_20180603-0704
* NEWIFI-D1_3.4.3.9-099_20180605-0419
* NEWIFI-Y1_3.4.3.9-099_20180607
* YH-WR1200JS_3.4.3.9-099_20180607-0049
* YOUKU-L1_3.4.3.9-099_20180606-0107

to be upgrade:

* A3004NS
* B70
* MZ-R13P
* PSG1218-256M
* PSG1218-K2


-------------------------------------------------------

所有带USB功能的固件，均会更新samba3到最新版，全面支持win10系统。
不需要再去改系统配置启用古老的SMB 1.0协议。

科普SMB协议：

```
SMB2.0相对于1.0的主要变化，主要体现在共享的网络文件上读写速度上，
尤其针对高延迟的网络环境，SMB 2.0能提高其安全和性能，
SMB 2.0 协议提供许多通讯增强功能，
包括通过高延迟链接连接到文件共享时更高的性能，
以及通过使用相互身份验证和消息签名获得更好的安全性。
```
关于SMB2的更多信息可以访问此链接获取： http://smb.pconline.com.cn/zxpc/0802/1230849_all.html


## changelog

padavan: 同步更新到官方最新源码（20180531） e6f4533

    主要更新是：Switch from Entware-ng to Entware

aria2: 版本更新到最新的1.34.0

增加ssl支持选项，默认读取httpd的证书

shellinabox： 增加ssl证书支持，默认读取httpd的证书

dnsmasq: add adblock hosts

libcurl：bump to version 7.60.0

libpcre: bump to version pcre-8.42

libmbedtls: mbedtls-2.9.0

$$-libev: bump to version 3.2.0

libxml2: bump to version 2.9.8

openvpn: add xor patches

```
### openvpn_xorpatch
# see https://tunnelblick.net/cOpenvpn_xorpatch.html for more information
# Note: The "scramble" option and parameters in the server and client configuration files must match.
#scramble put_your_xor_string_here
```

setup guide: https://github.com/ihacklog/p4davan-fw/issues/242


transmission： 

bump to  transmission-2.94

默认禁用 rpc-whitelist 解决访问web ui时提示 Unauthorized IP Address. 的问题
从openssl换用mbedtls

顶部按钮：去除shellinabox快捷按钮，增加transmission 快捷按钮。

修复若干bug.
