执行`radiusd -X`(或者`freeradius -X`在Ubuntu), 用root用户, 将会启动FreeRADIUS以debug模式, 并且会给出提示, 如果有任何问题.

使用`Ctrl + C`来停止服务器程序.

如果你得到一个错误信息关于1812端口已经被使用, FreeRADIUS已经运行. 你可以通过启动脚本关闭然后再运行来解决.

我们也需要确保FreeRADIUS在重启后能够启动带有其余的服务. 不幸的是每个发行版在这里工作不同. 下表可以被用做一个向导来管理FreeRADIUS的启动.

这些脚本的每一个可以被列出的参数之一调用.

发行版 | 启动脚本
-------|---------------------------------------
CentOS | `/etc/init.d/radiusd start/stop/restart`
SLES | `/etc/init.d/freeradius start/stop/restart`
Ubuntu | `/etc/init.d/freeradius start/stop/restart`

发行版 | 使能和禁用
-------|-------------------------------------
CentOS | `/sbin/chkconfig radiusd on/off`
SLES   | `/sbin/chkconfig -a freeradius` 和 `/sbin/chkconfig -d freeradius`
Ubuntu | `update-rc.d freeradius defaults` 和 `update-rc.d -f freeradius remove`

确保FreeRADIUS在运行: `pidof freeradius` 或者 `ps aux | grep radius`

检查FreeRADIUS在使用哪个网络接口和UDP端口: `netstat -unap | grep radius`


