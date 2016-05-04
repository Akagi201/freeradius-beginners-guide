在一些安装上(SLES), FreeRADIUS客户端程序不能读取字典文件. 这只有当你用一个普通用户运行时才会发生. 访问`dictionary`文件是需要的对于客户端来解析许多RADIUS AVPs到数字.

当这个问题存在时, 你将会的到下面的信息:
```
radclient: dict_init: Couldn't open dictionary "/etc/raddb/dictionary": Permission denied
```

通过使用下面命令来修复该问题.
```
# chmod o+xr /etc/raddb
# chomod o+r /etc/raddb/dictionary
```

在这之后一个普通用户应该可以运行FreeRADIUS客户端程序没有任何权限问题.
