+++
categories = ["padavan"]
date = "2018-06-07T18:02:24+08:00"
tags = ["update"]
title = "201806 固件更新记录"
banner = "img/banners/update.jpg"
+++

好了,终于花时间写了个详细版的更新说明。


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
* PSG1218-K2_3.4.3.9-099_20180607-0205

由于时间关系,还有以下型号没有升级:

* A3004NS
* B70
* MZ-R13P
* PSG1218-256M



由于8M闪存容量限制,此次发布的原版硬件的K2固件,把iperf3 移除了.


嗯,我写了个[固件大小校验器](http://files.80x86.io/router/rom/opt/padavan-utils/rom-size-validator/):

你能想象我看到这个结果时的心情(多出了1KB):
![](/img/blog/2018/06/07/k2_size_invalid_2018.06.08_00h17m08s_009_.png)

所以,没办法,把iperf3 移除了:
![](/img/blog/2018/06/07/k2_size_ok_2018.06.08_00h17m26s_010_.png)


-------------------------------------------------------

## changelog

### 0. 引入 SMB2.0 协议, 完美支持win10系统

openwrt 很久以前就支持SMB2.0 协议了,然而padavan是个比较保守一些的系统. 无论是底层类库/驱动的更新,
还是一些软件的更新. 因此,padavan 官方版本里面的samba, 还是很久以前的3.0.x版,没有办法支持 SMB 2.0协议.

此次更新, 所有带USB功能的固件，均已更新samba3到最新版，以更好地支持win10系统。
不需要再去改系统配置启用古老的SMB 1.0协议。

因此,可以说这个固件此这一版开始,也算是"win10  compatible"了.

![](/img/blog/2018/06/07/Windows-10-Compatible.png)


关于win10 在1709版之后默认禁用smb 1.0的说明,详情可参阅m$的支持文档:
[SMBv1 不被安装的 Windows 10 秋季创建者更新和 Windows 服务器、 版本 1709年及更高版本中的默认值](https://support.microsoft.com/zh-cn/help/4034314/smbv1-is-not-installed-by-default-in-windows)


![](/img/blog/2018/06/07/win10_ready.jpg)


科普SMB协议：

```
SMB2.0相对于1.0的主要变化，主要体现在共享的网络文件上读写速度上，
尤其针对高延迟的网络环境，SMB 2.0能提高其安全和性能，
SMB 2.0 协议提供许多通讯增强功能，
包括通过高延迟链接连接到文件共享时更高的性能，
以及通过使用相互身份验证和消息签名获得更好的安全性。
```
关于SMB2的更多信息可以访问此链接获取： http://smb.pconline.com.cn/zxpc/0802/1230849_all.html

### 1. 全部支持HTTPS

![](/img/blog/2018/06/07/ssl_secured.jpg)

对于自签名的ssl证书, 在Mac系统下面,最新的chrome浏览器
已经默认不允许用户点击继续访问了. (windows下面可以点击继续访问)
因此,给路由器设置一个正确的ssl证书显得必要了.

如何申请一个正规ca颁发的证书?方法有很多,我这里提供一种比较方便的方式.


假设你ddns 远程访问路由器绑定域名是example.com , 那么你要申请的域名就是 example.com 

进 https://console.cloud.tencent.com/ssl 点击购买证书,然后选择免费型,如下图:

![](/img/blog/2018/06/07/tencent_ssl_free_2018.06.07_23h28m40s_004_.png)


申请成功后,可以点击下载,会下载到一个zip压缩文件.

解压后,进入  Apache目录 , 用sublime text 或 Emeditor 等打开对应的文件,粘贴进去,点击应用.
文件与padavan文本框的对应关系如下图:
![](/img/blog/2018/06/07/padavan_httpd_ssl_setup_2018.06.07_23h18m29s_003_.png)


这样设置好之后,远程访问路由器就可以看到浏览器地址栏绿了或不再是红色.

当然,对于没有公网的用户来说, 不存在DDNS, 因此,需要设置的SSL证书是frp 或 ngrok 访问时的域名.

如果只是仅仅到这里了,那么这次更新也不算什么新功能了,因为这是padavan自带的.

插件里面,有shellinabox 和 Aria2 同样用到了SSL证书.
其中,shellinabox是强制HTTPS访问的. Aria2为可选.

shellinabox： 默认读取httpd的证书,也就是说,和前面步骤里你设置的证书(httpd的证书),是完全一致的.

在 Aria2 的SSL功能配置设计上面,我做得稍微有些不同.
从理论上来说,我完全也可以做到和shellinabox一样,直接读取httpd的证书.但是这样做是不行的.
因为padavan默认是以nobody启动aria2c 进程的,它没有权限读取admin用户配置的httpd ssl证书.
当然,最简单的一个修改就是,让aria2c也以root权限启动.本着遵循原固件功能设计和代码设计的原则,
这些东西 ,我们尽量不去改.

因此,我单独给aria2设计了ssl证书配置选项. 证书的值,在第一次启用aria2 ssl功能时,会自动copy
httpd的ssl证书.
aria2的证书路径是:/mnt/aria/config/ssl.crt (对应配置界面的Aria2 Certificate) 和 /mnt/aria/config/ssl.key (对应配置界面的Aria2 Certificate Key)

同时,增加了 Rpc Secure 选项开头,用户可自行控制打开和关闭ssl.
Rpc Certificate Domain 则是证书对应的域名.

注意:
aria2 的ssl一旦打开,那么,你通过浏览器打开aria2 web ui时, 填写的rpc地址一定要是和对应的SSL证书匹配的.
不然无法连接.打开控制台你会发现chrome在报怨: 
`Failed to load resource: net::ERR_CERT_COMMON_NAME_INVALID`

所以,ssl功能启用于否,看你的需求.如果你是mac用户,那么很可能需要启用.

![](/img/blog/2018/06/07/ARIA2_SSL_ENABLED_2018.06.08_00h04m43s_008_.png)


### 2. 新增功能 和 优化

![](/img/blog/2018/06/07/power.jpg)

dnsmasq: 增加广告屏蔽hosts选项,可填写一个url,并实现定时自动更新.
如果这里留空,则表示不使用此功能.

一些推荐的hosts列表(同时欢迎各位老司机推荐和共享好用的hosts):
```
https://adaway.org/hosts.txt
https://raw.githubusercontent.com/vokins/yhosts/master/hosts

```

![](/img/blog/2018/06/07/adhosts_2018.06.07_23h56m00s_006_.png)


固件已经内置了自动更新脚本和cron配置,如下图示:
![](/img/blog/2018/06/07/adhosts_cron_2018.06.07_23h58m57s_007_.png)


openvpn: 增加 xor patches
此功能来自github一位朋友提的issue (https://github.com/ihacklog/p4davan-fw/issues/242)

```
### openvpn_xorpatch
# see https://tunnelblick.net/cOpenvpn_xorpatch.html for more information
# Note: The "scramble" option and parameters in the server and client configuration files must match.
#scramble put_your_xor_string_here
```

由于我把transmission升级到最新版了,最新版有个配置默认是启用的,导致打不开web ui.
这个问题此次也一并得以解决了.

默认禁用 rpc-whitelist , 解决访问web ui时提示 `Unauthorized IP Address.` 的问题
另外,transmission编译上面, 用更适合嵌入式设置的mbedtls库替换了原来的openssl.

顶部按钮：去除shellinabox快捷按钮，增加transmission 快捷按钮。 嗯,最近的事,大家都
应该知道了. 115 要把所有数据迁移到阿里云了,同时,115 已经开始限制资源的下载.很多magnet链接
已经不能通过115下载了. 此时, transmission这位老朋友就显得更加重要了.
同时,shellinabox 实际上打开的频率,远没有transmission高, 因此,这也是从使用的角度,对界面进行优化.
虽然我做的功能不多,但凡是做出来的功能,哪怕是一个按钮,也是经过考究的.


### 3. 各种软件包和内裤(类库)更新

![](/img/blog/2018/06/07/update.png)

padavan: 同步更新到官方最新源码（20180531） e6f4533

    主要更新是：Switch from Entware-ng to Entware

关于这一点呢,不需要太过于关注.因为这个升级是平滑的.
padavan里存在这个升级的原因是:
Entware-ng-3x and Entware-ng merged to become Entware
所谓分久必合,合久必分啊.

> Entware-ng-3x and Entware-ng decided to join forces and merge the two projects into a new project called Entware. As a result the two projects Entware-ng-3x and Entware-ng will not be maintained anymore. All software and resources will be available at the new project webpage, including the documentation wiki.
from: https://github.com/Entware/Entware

aria2: 版本更新到最新的1.34.0

libcurl：bump to version 7.60.0

libpcre: bump to version pcre-8.42

libmbedtls: mbedtls-2.9.0

$$-libev: bump to version 3.2.0

libxml2: bump to version 2.9.8

transmission： bump to  transmission-2.94

这些都没啥好说的了.喜欢了解详情的,可以去各自的官网看changelog.

修复若干bug.
