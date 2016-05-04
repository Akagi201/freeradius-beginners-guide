这一章是引言和我们要将的基础. 作为讨论的重点, 确定知道下面的事实.

名称 | 代表 | 简称
-----|------------|-------------------
AAA | Authentication, Authorization, Accounting | 对于访问和使用的合适控制的必要的三个组件
NAS | Network Access Server | 例如一个控制网络访问设备, 一个VPN服务器, 作为一个RADIUS客户端.
AVP | Attribute Value Pair | 在RADIUS包中一个3域的组件来包含一个特殊的域和他的数据
VSA | Vendor-Specific Attributes | 由指定vendor管理的扩展的AVP

* AAA是一个安全架构模型.
* RADIUS是AAA的一种特定实现.
* FreeRADIUS是RADIUS的实用应用.
* 因此我们有AAA -> RADIUS -> FreeRADIUS
* RADIUS是关于中心控制和NAS vendor支持的事实标准.
* RADIUS是一个 C/S 协议. 他使用UDP, 监听在1812端口用于authentication, 在1813端口用于accounting请求.
* RADIUS数据包有code域, 指定RADIUS包的类型.
* RADIUS数据包有0或者更多的AVPs, 包含用在RADIUS中的数据.
* FreeRADIUS实现了RADIUS协议, 还有在RFC中的许多扩展.
* FreeRADIUS是一个非常流行的, 广泛使用的, 非常灵活的RADIUS服务器.

这一章是FreeRADIUS的开始. 主要课程从下一章开始, 我们将安装和开始使用FreeRADIUS.

## 课堂作业 - RADIUS知识
1. 解释NAS device术语.
2. 什么是一个session的起点和终点?
3. RADIUS使用哪种协议和端口?
4. RADIUS客户端和服务器端需要成功的通信?
5. 当authenticate一个用户时, RADIUS客户端会发送什么?
6. 谁初始化一个Disconnect Request包并且谁接受他?
7. 命名AVP的三个组件?
8. Alice使用用户名`alice@freeradius.org`连接到一个网络. Alice术语的realm的名称是什么?



