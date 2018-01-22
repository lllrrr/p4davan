+++
title = "FAQ"
description = "Frequently asked questions"
keywords = ["FAQ","How do I","questions","what if"]
+++

这里是常见问题解答,为避免互相伤害(浪费时间),遇到问题时我建议你确保以下FAQ都阅读过后再
去向别人提问.

* @author: 荒野无灯
* @date: 2017-05-12

padavan官方 教程：

- 如何启用Entware: https://bitbucket.org/padavan/rt-n56u/wiki/EN/HowToConfigureEntware
- padavan必备技巧： https://bitbucket.org/padavan/rt-n56u/wiki/EN/CommonTips

## 0.这个固件的默认管理员用户名和密码是什么
`admin\admin`is a default credentials for WebUI and SSH.

`1234567890` is a default password for Wi-Fi.

更多信息，请移步 https://bitbucket.org/padavan/rt-n56u/wiki/EN/FAQ#markdown-header-i-cant-get-webui-access-how-to-reset-it

## 1. adbyby 或 koolproxy的enable 按钮为何是灰色的？

answer: 大哥，adbyby 和 koolproxy 不能同时启用。。。

## 2. 如何通过命令快速切换$$
使用场景：某些免费的$$会定期更换密钥，或者想自己写脚本检测出最快的服务器，然后瞬间切换，而不是用固件默认的主从切换。

yzqianf：
我在编一个SS Server的速度测试脚本,想请教快速切换SS的方法。我原用修改NVRAM中的信息 和 killall ss-redir ,
等watchdog检测到SS故障后重启SS的方法,需等待十到二十秒，太慢了！
我想改用killall ss-redir后，根据新配置运行ss-redir后用iptables(还是ipset?对端口转发命令不熟悉)。
想请教切换到新的SS服务器上。
望能指点！

answer:
对于你的情况，服务器节点应该不是在路由器UI里添加的，你想动态改变，用如下方式即可：

首先，设置将要切换的服务器的信息
加密方法，可查看 `http://192.168.2.1/Advanced_Extensions_SS_Node.asp` HTML 源码获得,
如 rc4-md5 的值是3

```bash
nvram set ss_node_method_x0=3
nvram set ss_node_password_x0=密码
nvram set ss_node_server_port_x0=服务器端口
nvram set ss_node_server_addr_x0=服务器地址
#然后重启$$服务，即会自动使用此服务器
nvram settmp ss_cur_config=0
nvram set shadowsocks_master_config=0
restart_ss
```

## 3. $$ 老是自动重启怎么回事？

answer: 固件的$$服务有一项故障检测功能，当在一分钟之内连续检测到访问指定的站点10次失败，即会重启$$并切换主从配置.
一般来说这个条件还是不是那么容易触发的。但是不排除某些人使用的是免费的$$，稳定性和速度没法保障。
你有两个选择：其一是关闭Status Detection， 其二是，花占钱购买一个好点的$$吧。

## 4. 老毛子有没有定时重启？

answer: 这个问题被人问了无数次。我有点想骂人了。
定时任务在Linux里一般由Cron daemon来负责。padavan是基于Linux的。这个功能并没有省略。
自己进入 http://my.router/Advanced_Services_Content.asp 就可看到。
还有我要多说一句：我一直是反对自动重启这个东西的。没事别重启。
定时任务是用来做任何其它有意义的事情的。
不是给你用来重启的。
如果我不更新固件不折腾padavan系统。我的路由器可以开10年不动。

## 5. 我想问下，老毛子固件怎么查看每个设备的实时流量

answer: 之前也有集成过按IP查看单个设备流量的功能。但是MTK芯片的路由器都有一个问题：
HW NAT打开后会导致现有的流量统计错误。然而，为了使用流量统计而关闭HW NAT是不值得的。
因此，后面我又把这个功能移除了。

---

> In case you haven't found the answer for your question please feel free to contact us, our customer support will be happy to help you.
