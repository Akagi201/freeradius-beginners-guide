预编译的FreeRADIUS包可以通过下面的命令在相应发行版进行安装.

* CentOS: `yum install freeradius2 freeradius2-utils`
* SUSE: `zypper in freeradius-server freeradius-server-utils freeradius-server-doc`
* Ubuntu: `sudo apt-get install freeradius`

## 优点
* 自动解决依赖. 这个包括考虑到未来的安全更新, 跟踪所有可选的包, 确保正确的版本依赖.
* linux distributor的QA测试确保软件正常工作.
* 更新会被linux distributor照顾好.
* 发行版相关的tweak已经执行.

使用预编译包的一个妥协是你不会用到最新的FreeRADIUS包到你的机器上.

## 额外的包
FreeRADIUS是一个功能丰富的软件. 不同的发行版展示他的FreeRADIUS不同地, 通过分散到多个不同的包里.

CentOS和Ubuntu包含某些FreeRADIUS服务器的包作为可选包. 这使得基本的服务器安装包保持瘦的. 安装可选的服务器模块包将会也安装需要的依赖. 这表示, 例如, 当你安装freeradius-mysql包的时候, 所有需要的MySQL库将作为依赖安装.

SUSE通过功能划分他们的包. 你可以发现客户端和服务器端都有他各自的包集合. SUSE也有FreeRADIUS的utilities和文档的包.

## 可得到的包
这一节列出每种发行版的可得到的预编译的FreeRADIUS包. 加粗的名字是推荐作为基本的FreeRADIUS安装的.

### CentOS

包名 | 简短描述
-----|-----------
**freeradius2** | 高度可配置的RADIUS服务器
freeradius2-krb5 | FreeRADIUS的Kerberos 5支持
freeradius2-ldap | FreeRADIUS的LDAP支持
freeradius2-mysql | FreeRADIUS的MySQL支持
freeradius2-perl | FreeRADIUS的Perl支持
freeradius2-postgresql | FreeRADIUS的PostgreSQL支持
freeradius2-python | FreeRADIUS的python支持
freeradius2-unixODBC | FreeRADIUS的Unix ODBC支持
freeradius2-utils | FreeRADIUS的utilities

### SUSE
包名 | 简短描述
-----|---------
freeradius-client | FreeRADIUS的客户端软件
freeradius-client-libs | FreeRADIUS客户端的共享库
**freeradius-server** | Highly configurable RADIUS server
freeradius-server-dialupadmin | Web management for FreeRADIUS
**freeradius-server-doc** | FreeRADIUS的文档
**freeradius-server-libs** | FreeRADIUS的共享库
**freeradius-server-utils** | FreeRADIUS的客户端

注意, SUSE提供的`freeradius-client`包是被软件开发者使用来利用RADIUS来AAA. 客户端程序像`radtest`是包含在`freeradius-server-utils`包中.

### Ubuntu

包名 | 简短描述
**freeradius** | FreeRADIUS的服务器端包
freeradius-dbg | 包含FreeRADIUS包的分离的调试符号
**libfreeradius2** | FreeRADIUS的共享库
freeradius-ldap | FreeRADIUS服务器的LDAP模块
**freeradius-common** | FreeRADIUS的普通文件, 包括字典和man pages
freeradius-iodbc | FreeRADIUS服务器的iODBC模块
freeradius-krb5 | FreeRADIUS服务器的Kerberos模块
**freeradius-utils** | FreeRADIUS的客户端程序, 包括程序像radclient, radtest, smbencrypt, radsniff, 和 radzap
freeradius-postgresql | FreeRADIUS服务器的PostgreSQL模块
freeradius-mysql | FreeRADIUS服务器的MySQL模块
freeradius-dialupadin | web管理插件
libfreeradius-dev | FreeRADIUS的共享开发库

## 特殊的考虑
旧版本的Ubuntu的预编译包没有编译进去SSL库支持. 当安装FreeRADIUS到这些版本的时候, 如果你需要在一些EAP扩展上支持SSL, 你需要自己编译.

SUSE也提供`yast -i`命令来安装软件. 但使用`zypper`替代, 因为当安装软件和依赖时, 他有更好的决策能力.

在CentOS中FreeRADIUS包的名称是`freeradius2`而不是`freeradius`. 这是因为最开始在CentOS 5上支持的FreeRADIUS版本是1.1.3. FreeRADIUS的1.x和2.x之间的配置文件有改变, 所以需要在名称上有所改变.

不是所有的FreeRADIUS模块有他们在Ubuntu或CentOS上匹配的包. 没有匹配的包的模块就简单低包含在FreeRADIUS的主包上.

## 留意防火墙
CentOS和SUSE默认安装一个活跃的防火墙. 请确保UDP端口1812和1813是对外开放的.

### CentOS
有一个程序来配置CentOS上的防火墙, 叫做`system-config-securitylevel-tui`, 需要root来运行. 这个会启动一个基于鼠标的程序. 选择`Customize`选项然后按下`Enter`. `Allow incoming | Other Ports`列表应该包括`1812:udp 1813:udp`. 选择`OK`来返回到主界面, 然后再选择`OK`, 来提交修改.

确认端口是开放的通过下面命令的输出:

```
# iptables -L -n | grep 181*
ACCEPT     udp  --  0.0.0.0/0            0.0.0.0/0           state NEW udp dpt:1812
ACCEPT     udp  --  0.0.0.0/0            0.0.0.0/0           state NEW udp dpt:1813
```

不推荐你用下面的命令来关闭防火墙.

```
# /etc/init.d/iptables save
# /etc/init.d/iptables stop
# /sbin/chkconfig iptables off
```

为了确认是否防火墙是关闭的, 使用下面的命令.

```
# iptables -L -n
Chain INPUT (policy ACCEPT)
target     prot opt source               destination

Chain FORWARD (policy ACCEPT)
target     prot opt source               destination

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination
```

## SUSE
在SUSE上配置防火墙可能有点a catch-22 situation由于默认的防火墙是如此安全, 你甚至不能用SSH进入系统! 进入SLES服务器然后开始`YaST`. 选择`Security and Users | Firewall`. 选择左边的`Allowed Services`. 我建议你添加`Secure Shell Server`到`External Zone`. 点击`Advanced`按钮, 然后添加1812, 1813到`UDP Ports`. 点击`OK`. 点击`Next`和`Finish`来提交这些修改.

通过下面的命令来确认端口是开放的.

```
# iptables -L -n | grep 181
ACCEPT     udp  --  0.0.0.0/0        0.0.0.0/0         udp dpt:1812
ACCEPT     udp  --  0.0.0.0/0        0.0.0.0/0         udp dpt:1813
```

不推荐用下面的方法关闭防火墙.

1. 使用`YaST`然后选择`Security and Users | Firewall`.
2. 选择左边的`Start-Up`, 选择右边的`Disable Firewall Automatic Starting`. 也选择`Stop Firewall Now`, 然后按`Enter`键来停止当前运行的防火墙.
3. 点击`Next`和`Finish`来提交修改.

通过下面的命令来确认防火墙现在是关闭的.

```
# iptables -L -n
Chain INPUT (policy ACCEPT)
target     prot opt source               destination

Chain FORWARD (policy ACCEPT)
target     prot opt source               destination

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination
```
