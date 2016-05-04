FreeRADIUS应该被尽可能低的权限运行在生产环境中. 一个正常的安装创建一个专用的用户和组为了这个目的.

在CentOS和SLES上, 用户和组叫做`radiusd`. 在Ubuntu上, 用户和组叫做`freerad`. 只有特殊的配置才需要root权限.
