RADIUS是Remote Access Dial In User Service的缩写. RADIUS是一个AAA解决方案的一部分, 由Livingston企业发布到Merit Neowrk在1991年. Merit Network是一个非盈利互联网提供商, 需要一种创新的方式来管理通过他们的网络dial-in access to various Points-Of-Presence (POPs).

Livingston企业提供的解决方案有一个中心用户存储用来authentication. 这个可以被许多RAS(Radio Access Station)(dial-in)服务器使用. Authorization和accounting也可以在AAA满足的条件下完成. 另外一个Livingston解决方案的关键方面是包括代理来支持扩展.

RADIUS协议随后在1997年作为RFC发布, 一些修改被提交, 并且现在我们有了RFC2865涵盖了RADIUS协议, 还有RFC2866涵盖了RADIUS accounting. 也有许多额外的RFC涵盖某些RADIUS方面的增强. 让RFC工作从任何个人或供应商到在他们的设备或软件上实现RADIUS协议. 这个导致RADIUS的广泛采用来处理在TCP/IP网络上的AAA. 你将会发现术语RADIUS既表示RADIUS协议也表示完整的RADIUS C/S 系统. 含义应该根据上下文可以明确.

支持RADIUS协议和标准成为NAS供应商的事实标准需求. RADIUS在广泛的地方被使用, 从拥有百万级用户的蜂窝移动网络提供商到小的WISP创业公司提供local neighborhood with Internet 到实现使用802.1x来围栏他们的网路的NAC(Network Access Control)企业网络. RADIUS在所有这些地方和更多的地方可以被发现.

ISP和网络管理员们应该熟悉RADIUS, 由于他被许多控制访问TCP/IP网络的设备使用. 下面是一些例子:

* 一个带有VPN服务器的防火墙可以使用RADIUS.
* 带有WPA-2企业加密的Wi-Fi AP包含RADIUS.
* 当Alice通过DSL连接一个现有的电信设施时, 电信设备将会使用RADIUS协议来联系Isaac的RADIUS服务器为了决定她是否可以通过DSL(代理)获得网络访问权限.

下一节将会总结在RFC2865上指定的RADIUS协议.

## RADIUS协议[RFC2865]

这一节在技术层面探索发布在RFC2865上的RADIUS协议. RADIUS accounting是排除在外的. 这个是发布在RFC2866并且有个关于他自己的章节会详细探索.

RADIUS协议是一个C/S协议, 利用UDP来通信. 使用UDP而不是TCP暗示通信不是严格on state. 在客户端和服务器端的一个典型的数据流有一个来自客户端的单一请求, 紧接着一个来自服务器端的单一回复构成. 这让RADIUS成为一个非常轻量级的协议并且对在低网速环境下的效率有帮助.

在客户端和服务器端之间可以建立成功的通信之前, 每一端必须定义一个shared secret. 这个是用来authenticate客户端.

一个NAS作为一个RADIUS客户端. 因此当你读到关于一个RADIUS客户端, 他表示一个NAS.

![nas-radius](http://akagi201.qiniudn.com/nas-radius.png)

RADIUS包有一个指定的格式, 定义在RFC里面. 在一个RADIUS包中, 两个关键的组件是:

* code: 表示包的类型.
* attribute: 携带RADIUS使用的必要数据.

让我们来研究一下RADIUS数据报的组成.

## 数据包

了解一个RADIUS数据包的格式将会大大帮助理解RADIUS协议. 让我们更细致的查看RADIUS数据包. 我们将会看一个简单的authentication请求. 一个客户端发送一个Access-Request包到服务器. 服务器返回一个Access-Accept包来表示成功.

显示在这里的RADIUS数据包只是一个UDP数据包的payload. UDP和IP协议的讨论已经超出这本书的范围了.

屏幕截图是捕捉在RADIUS客户端和服务器端之间的流量得到的. 这里的屏幕截图是一个简单的Authentication请求发送到一个RADIUS服务器的结果.

下面的截图显示来自RADIUS客户端的Access-Request包.

![radius-request](http://akagi201.qiniudn.com/radius-request.png)

下面的截图显示RADIUS服务器返回给这个请求一个Access-Accept数据包.

![radius-respond](http://akagi201.qiniudn.com/radius-respond.png)

让我们来研究一下这些数据包.

## Code
每一个数据包由一个code来识别. 这个域是一个字节. 这个code的值决定这个包的某些特性和需求. 下面的表格可以被用作一个索引来列出当前RADIUS数据包的定义的code.

RADIUS code(decimal) | Packet type    | Sent by
---------------------|----------------|---------
1                    | Access-Request | NAS
2                    | Access-Accept  | RADIUS server
3                    | Access-Reject  | RADIUS server
4                    | Accounting-Request | NAS
5                    | Accounting-Response | RADIUS server
11                   | Access-Challenge | RADIUS server
12                   | Status-Server(Experimental) |
13                   | Status-Client(Experimental) |
255                  | Reserved |

当跟RADIUS工作时, 了解这些code是有利的.

## Identifier
每个包的第二个字节包含一个唯一的标识符. 他是由客户端生成的, 并且用做辅助来匹配请求和回复. RADIUS数据包是通过无连接的UDP传输的. 这样需要RADIUS来实现他自己的算法来提交从客户端的重试请求. 当一个客户端重发一个请求给服务器, 数据包的identifier将会保持不变. 服务器将会回复请求通过匹配在回复数据包中的identifier.

## Length
这个是数据包的第3, 第4个byte. 他表示一直到哪里在数据包中是有用的数据. 在这个边界之外的字节被认为是填充和被默默地忽略.

## Authenticator

这个域占用2个byte, 根据包是来自客户端还是服务器端有不同的格式. 他也依赖包的类型, 例如, Access-Request 或者 Accounting-Request. 如果他是一个请求, 这个域作为一个Redquest Authenticator. 如果他是一个回复, 这个域作为一个Response Authenticator.

一个Request Authenticator的值一个不会重复的随机数. Response Authenticator的值是在回复包的许多域的MD5 哈希值, 带有客户端和服务器端之间的共享密钥(shared secret).

如果请求包含User-Password attribute, 那么这个attribute的值会被加密. 这个加密的值通常是把共享的密钥(shared secret)结合authenticator生成MD5哈希值, 然后与用户的密码进行异或生成的. 这就是为什么共享密钥(shared secret)要在客户端和服务端保持一致, 为了解密用户的密码.

## Attributes
RADIUS包的其余部分包含0或者更多attribute, 作为AVP(Attribute Value Pairs). 这些AVPs的结尾是由包的length域指定的.

## Conclusion
RADIUS包是通过UDP传输. code域表示RADIUS包的类型. attribute是用来提供指定的信息用于authentication, authorization 和 accounting. 例如为了authenticate一个用户, User-Name和User-Password AVPs将会和一些其他的attribute一起被包含在Access-Request包中.

## AVPs
AVPs是RADIUS协议的苦力. AVPs可以被分类为check或者reply attribute. check attribute是从客户端发到服务器端. reply attribute是从服务器端发到客户端.

attribute 作为信息的载体在客户端和服务器端之间服务. 他们被客户端使用来提供他们自身的信息和连接到他上的用户. 他们也被使用当服务器端回复客户端时. 客户端然后可以使用这个回复来控制用户的连接基于在服务器端回复接收到的AVPs.

下一节将会描述一个AVP的格式.

## Type
AVP的第一个byte是type域. 这个域的数值是和一个attribute名称联系在一起的, 所以我们人类也可以理解. 这些attribute和数值的关系是由IANA(http://www.iana.org)控制的. attribute的名称通常是有描述性的, 例如 User-Name(1), User-Password(2), 或者 NAS-IP-Address(4).

RADIUS也允许扩展协议. attribute类型26(叫做Vendor-Specific)允许这样做. Vendor-Specific的attribute的值可以反过来包含由一个vendor管理的Vendor Specific Attributes(VSAs).

## Length
length域是AVP的第二个byte. 这个与RADIUS包中的length一个含义, 用来表示AVP的长度. 这个方法允许AVPs带有不同大小的值由于length域将会标记AVP的结尾.

## Value
AVP的value可以在大小上不同. 这个value域可以是0或者更多byte. 这个value域可以包含一下数据类型中的一个: test, string, address, integer或者time.

Text和string可以多达253byte. Address, integer和time是4个byte大小.

如果我们取出一个请求包中的NAS-IP-Address AVP, 我们会看到长度是6 byte. 一个byte表示类型, 一个byte表示长度, 然后4个byte表示IP地址, 总共6byte.

下一节将会讨论Vendor-Specific Attribute, 是标准的AVPs的扩展.

## Vendor-Specific Attributes(VSAs)
VSAs允许vendor来定义他们自己的attribute. attribute定义的格式基本上是普通的AVPs带有额外的一个vendor域名. VSAs是通过AVP类型26发送. 这表示VSAs是AVPs的一个扩展, 并且包含在AVPs之内.

这让RADIUS非常灵活, 并且允许一个vendor来创建扩展来自定义他们的RADIUS实现. 例如CoovaChilli有一个VSA attribute叫做ChilliSpot-Max-Total-Octets. 当CoovaChilli客户端从RADIUS服务器接收到这个attribute的回复时, 他使用这个值来限制通过captive portal的数据.

NAS将会默默地忽略任何他不支持的VSAs. 一些vendors发布了他们的VSAs, 但是这不是需要的. 其他仅仅列出他们在网站或者文档里. 这个然后可以被用来决定他们设备实现RADIUS的能力.

## Proxying 和 realms

RADIUS协议允许扩展. Proxying允许一个RADIUS服务器来作为一个客户端连接到另一个RADIUS服务器. 这个最终形成一个链条.

在proxying上的讨论也包含realms. Realms是名字用来组织用户和形成用户名的一部分. 一个用户名区分于realm name带有一个指定的分隔符. realm name可以是用户名加上前缀或者后缀. 今天的流行的标准使用玉米作为后缀, 并且用@分割, 例如:`alice@freeradius.org`. 这个然而只是一个约定. realm可以是任何名字, 分隔符也可以是任何符号. windows用户通常用一个前缀指定域名, 用`\`作为分隔符, 例如: `my_domain\alice`.

当RADIUS服务器接收到一个请求带有用户名包含一个realm时, 他决定是否处理这个请求或者转发这个请求给另一个RADIUS服务器被设计用来处理这个指定的realm. 这将会需要第二台RADIUS服务器应该有转发RADIUS服务器作为一个客户端定义, 并且他们也有一个共享的密钥(shared secret).

## RADIUS服务器端
RADIUS协议是基于C/S架构. RADIUS服务器将会监听UDP端口1812和1813. 1812端口是用于authentication. 这将会包含Access-Request, Access-Accept, Access-Reject, 和Access-Challenge包. 1813端口用于accounting. 这个将会包含Accounting-Request和Accounting-Response包.

一个客户端和服务器需要一个共享的密钥(shared secret)为了加密和解密在RADIUS包中的某些域.

## RADIUS客户端
RADIUS客户端通常提供访问TCP/IP网络. 客户端作为一个RADIUS服务器和一个想要网络权限的用户/设备之间的中间人.

RADIUS的proxying功能也允许一个RADIUS服务器成为另一台RADIUS服务器的客户端, 将会最终形成一个链条.

来自RADIUS服务器的反馈不仅仅决定是否一个用户允许访问网络(authentication), 也可以引导客户端来强加某些限制在用户上(authorization). 限制的例子有在session上的一个时间限制或者限制连接速度.

是否强加推荐的对于用户sessiion的调整取决于客户端. 域名RADIUS协议的无状态的本质, 对于RADIUS服务器没有方法来指导是否客户单在执行推荐的限制.

Accounting定义在一个单独的RFC. 下一节将会总结在RFC2866里面的RADIUS accounting.

## RADIUS accounting [RFC2866]

这一节探索RADIUS协议的accounting功能. accounting是一种方法来跟踪资源的使用和通常用于计费.

## Operation
RADIUS accounting服务器运行在1813端口. 当一个用户的session开始, NAS发送一个Accounting-Request包给RADIUS服务器. 这个包必须包含某些AVPs. 他是在成功authentication之后发送的第一个包. 服务器端将会确定接收通过发送一个匹配的Accounting-Response包.

在整个session, NAS可以发送可选的update报告关于一个指定用户的时间和数据使用. 当用户的session结束, NAS通知服务器. 这个将关闭在用户的session期间的accounting细节.

RADIUS客户端的功能为未来做准备比如服务器下线. NAS将会然后依赖他是如何配置的, 重试或者联系另一个RADIUS服务器.

当一个RADIUS服务器作为一个转发代理到另一个RADIUS服务器时, 他将会作为accounting数据的中继. 他也可能在转发前本地记录accounting数据.

## 包格式
Accounting包括RADIUS code 4(Accounting-Request)和code 5(Accounting-Response)包. Accounting包像authentication包一样使用相同的RADIUS协议. 一个独特的accounting包的特性是User-Password attribute不在请求中发送.

看下面的Wireshark输出, 显示一个典型的accounting 交易. 他从一个来自客户端的Accounting-Request开始.

![accounting-request](http://akagi201.qiniudn.com/accounting-request.png)

服务器然后回复给客户端一个Accounting-Response.

![accounting-response](http://akagi201.qiniudn.com/accounting-response.png)

Accounting-Request包也需要包含某些AVPs. 让我们看一下在accounting中使用的重要的AVPs.

## Acct-Status-Type [Type40]
这个包表示用户或者NAS的状态. 一个NAS可能发送临时的更新关于某个session的使用. 为了做这个, NAS设置类型为Interim-Update. 这个允许我们来近似实时地跟踪使用趋势.

RADIUS服务器不会在一个NAS上检查. 如果一个NAS已经通知RADIUS服务器关于一个新连接的用户(status类型Start)并且然后NAS完全关闭, RADIUS服务器上的记录将会仍然指示那个用户连接在NAS上, 而事实上没有. 这些记录被称为欺骗条目(rogue entry). 为了减少欺骗条目, 最好让NAS发送一个Accounting-Off紧接着一个Accounting-On包, 仅仅在boot-up之后, 并且也发送一个Accounting-Off包在关闭之前. 这个动作将会导致RADIUS关闭所有打开的记录对于任何连接到指定NAS的用户允许一个干净的启动.

欺骗条目尤其称为问题当你限制一个用户可以拥有的session的时候. 如果限制一个用户session的组件利用包含欺骗条目的数据, 那么计算将会不准确.

下面的数字到类型表格可以被用来索引可能的状态值.

Decimal value | Status type
--------------|-------------
1             | Start
2             | Stop
3             | Interim-Update
7             | Accounting-On
8             | Accounting-Off
9-14          | Reserved for tunnel accounting
15            | Reserved for failed

尽管Acct-Status-Type AVP不是必须的, 他总是被包含.

## Acct-Input-Octets [Type42]
这个表示在session期间接收到的byte并且带有Acct-Status-Type有一个值Interim-Update或者Stop.

注意值的限制. 4个byte限制他到4294967296. 大多数现代RADIUS实现已经满足这个需要.

## Acct-Output-Octets [Type43]
这个表示在session期间发送的byte, 并且带有Acct-Status-Type, 值为Interim-Update或者Stop.

注意值的限制. 4个byte限制他为4294967296. 大多数现代RADIUS实现已经满足这个需要.

## Acct-Session-Id [Type44]
对于所有的Accounting-Request包的规定动作, 这个是一个唯一的值用来匹配Start, Interim 和Stop记录. 所有的一个session的Start, Interm, 和Stop记录应该有对于Acct-Session-Id相同的值.

## Acct-Session-Time [Type46]
这个名字是自解释的. 以秒为时间表示session的长度, 并且带有Acct-Status-Type, 值为Interim-Update 或者Stop.

## Acct-Terminate-Cause [Type49]
这个伴随着一个Acct-Status-Type AVP, 他的值设置为Stop. 这个AVP的值为十进制数. 他与Acct-Status-Type的行为类似, 用一个指定的数值对应解析一个终止原因.

## Conclusion
这个带给我们RADIUS accounting的结束. 下一节我们将看某些RFC, 添加功能和增强给RFC2865和RFC2866的RADIUS定义.

## RADIUS扩展
在初始的RFC定义了通常的RADIUS和RADIUS accounting, 许多扩展被建议来扩展RADIUS的使用或者提高一些弱点.

也有一个改进的RADIUS协议叫做Diameter(一个文字游戏--两倍好于RADIUS). 然后Diameter的接受非常缓慢, RADIUS仍然保持事实的标准对于可预见的未来. 一个主要的原因可能是事实上许多Diameter的增强已经被许多RADIUS的扩展实现. 例如, RadSec协议通过TCP和TLS传输RADIUS协议. 这个让RADIUS再漫游环境更容易扩展.

尽管有许多, 我们将只研究两个可能被使用的扩展.

## Dynamic Authorization extension [RFC5176]
这个扩展帮助创建从RADIUS服务器到NAS的反馈回环. 这个实际上交换了客户端和服务器端之间的角色. RADIUS服务器成为NAS的一个客户端.

动态authorization允许RADIUS服务器来通知NAS关于已经做的改变给在NAS上一个用户的现有session. 有2个这种扩展的流行应用.

## Disconnect-Message [DM]
也叫做一个POD(Packet Of Disconnect), 这个用来断开一个现有用户的session. RADIUS服务器发送断开连接请求, 并且NAS必须回复无论断开是否成功.

## Change-of-Authorizaion Message [CoA]
这个消息提供数据来改变一个先有用户session的authorization. 例如, 我们现在可以动态改变每个session的带宽限制. 这让我们可以当互联网连接下降时可以增加每个session的带宽. 反之, 也可以.

MikroTik RouterOS包括这个功能在一些使用RADIUS的服务上.

下表列出包含的RADIUS包的code和名称

RADIUS code(decimal) | Packet type | Sent by
---------------------|-------------|---------
40                   | Disconnect-Request | RADIUS server
41                   | Disconnect-ACK | NAS
42                   | Disconnect-NAK | NAS
43                   | CoA-Request | RADIUS server
44                   | CoA-ACK     | NAS
45                   | CoA-NAK     | NAS

## RADIUS support for EAP [RFC3579]
EAP代表Extensible Authentication Protocol. 他大多用于以太网交换机和Wi-Fi Access Point的安全.

EAP支持一个外部authorization服务器的使用. RADIUS可以是这样的一个服务器. EAP将会使用RADIUS协议来wrap EAP数据在AVP内部为了authenticate一个连接.

这本书有个专门的章节关于EAP, 由于他是Wi-Fi企业安全的如此一个重要的部分.

在下一节, 将会研究FreeRADIUS项目. FreeRADIUS是一种RADIUS协议的实现, 并且他的许多扩展包括2个在这里提到的.

