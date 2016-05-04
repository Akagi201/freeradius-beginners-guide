FreeRADIUS有许多可执行文件被安装. 当在不同发行版之间移动时, 有小的不同点需要考虑.

一个这样的不同是配置文件的位置. 另一个不同是FreeRADIUS服务器程序的名称的不同. 在Ubuntu(和Debian)上, 他叫做`freeradius`. 在CentOS和SLES, 他叫做`radiusd`. 下表列出了重要的可执行程序, 带有一个简短描述.

名称 | 描述
-----|-----
/usr/sbin/raddebug | radmin的shell脚本wrapper用于调试输出不必在debug模式下运行`radiusd`.
/usr/sbin/radiusd 或 /usr/sbin/freeradius | RADIUS服务器程序
/usr/sbin/radmin | 连接到一个运行的`radiusd` daemon的管理程序
/usr/bin/radclient | 用来发送各种RADIUS包到RADIUS服务器和显示回复
/usr/bin/radconf2xml | 以XML格式显示当然服务器的配置
/usr/bin/radcrypt | 加密或者检查密码以DES或MD5格式
/usr/bin/radeapclient | 发送EAP包给一个RADIUS服务器
/usr/bin/radlast | 系统的上一个命令的前端用于显示从accounting 日志文件的输出
/usr/bin/radsqlrelay | 用于管理记录在一个SQL日志文件的账户细节. 这个文件是`rlm_sql_log`模块创建的
/usr/bin/radtest | 发送`Access-Request`(code 1)包给一个RADIUS服务器, 并且显示回复. `radclient`的前端
/usr/bin/radwho | 从`radutmp`文件显示活跃的连接
/usr/bin/radzap | shell脚本wrapper用于移除在session数据库(file或者SQL)里的欺骗记录(rogue entries)
/usr/bin/smbencrypt | 给定一个明文密码的`nt`密码哈希, 被MS-CHAP需要.

不是所有的列出的命令默认都可以执行. 命令像`radwho`和`radlast`依赖日志文件的存在, 因为他们作为这些命令的输入. 命令像`radmin`和`raddebug`需要一个在服务器上的特殊的设置在他们能够运行之前. 如果不是所有的这些命令都能在新的安装上运行, 不要担心.
