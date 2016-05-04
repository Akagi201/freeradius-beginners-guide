SUSE也利用RPM包管理器来软件管理就像RedHat发行版一样. 尽管都使用RPM作为基础的软件管理, SLES使用`zypper`来管理软件包仓库, CentOS使用`yum`.

SLES是一个硬核的企业发行版. 尽可能使用预编译包, 但是如果你感到勇敢, 想要你的手变脏, 这一节为你准备.

在SLES上编译FreeRADIUS的RPM包, 要比CentOS或者Ubuntu徐璈更多的努力.

## 添加一个OpenSUSE仓库
从源码编译FreeRADIUS需要许多开发库. 标准的SLES仓库不需要全部包含他们他们, 甚至当使用的SDK DVD是SLES的一部分. 我们因此需要添加OpenSUSE仓库. 这将会满足所有的开发库需求.

1. 通过下面的URL定位离你最近的OpenSUSE镜像: <http://mirrors.opensuse.org>
2. 使用root用户开始`YAST`.
3. 选择`Software | Software Repositories`并且然后选择`Add`来添加到一个仓库.
4. 添加在开始时你选择的OpenSUSE镜像. 对于我的OpenSUSE11.3最近的URL是: <ftp://opensuse.mirror.ac.za/opensuse/distribution/11.3/repo/oss/suse>
5. 你可能要被接受GnuPG密钥的导入对于新的仓库. 选择`Import`来继续.

这个设置编译FreeRADIUS RPM的阶段. 现在遵循下面的步骤:

1. 从官网下载最新的`.tar.bz2`格式的FreeRADIUS源码.
2. SLES包括一个目录结构专门用于编译RPM. 位于`/usr/src/packages`. 拷贝原始的`bz2`源文件到SOURCES目录. 替换x和y为对应的版本. `cp freeradius-server-2.x.y.tar.bz2 /usr/src/packages/SOURCES/`.
3. 从TAR文件解压SUSE RPM .spec文件, 替换x,y为对应版本.
```
# cd /usr/src/packages/SOURCES/
# tar -xvjf freeradius-server-2.x.y.tar.bz2 freeradius-server-2.x.y/suse/freeradius.spec
```
4. 拷贝下面的文件到`/usr/src/packages/SPECS`目录:
```
# cp freeradius-server-2.x.y/suse/freeradius.spec ../SPECS/
```
5. 编辑`spec`文件的下面的行, 修改`%{fillup_and_insserv -s freeradius START_RADIUSD}`为`%{fillup_and_insserv freeradius}`.
6. 运行下面的命令来决定依赖. `rpmbuild -ba /usr/src/packages/SPECS/freeradius.spec`
7. 这个将会列出必要的开发包来安装, 为了FreeRADIUS RPM被编译. 在你的系统上的列表可能不同. 下面是我的系统上的结果(用`zypper`安装他们)
```
zypper in db-devel e2fsprogs-devel gcc-c++ gdbm-devel gettext-devel glibc-devel ncurses-devel openldap2-devel openssl-devel pam-devel postgresql-devel python-devel unixODBC-devel zlib-devel apache2-devel cyrus-sasl-devel krb5-devel libapr1-devel libmysqlclient-devel
```
8. 再次运行`rpmbuild`命令. 如果所有的依赖都满足, RPM的编译将会开始. 把结果tee到一个文件用于将来的调查.
```
rpmbuild -ba /usr/src/packages/SPECS/freeradius.spec | tee /tmp/build_out.txt
```
9. 当编译完成, RPM位于`/usr/src/packages/RPMS/<architecture>/`目录.
10. 使用下面命令安装新的FreeRADIUS包.
```
# cd /usr/src/packages/RPMS/<architecture>/
# zypper in freeradius-server-2.x.y-0.i586.rpm freeradius-server-libs-2.x.y-0.i586.rpm freeradius-server-utils-2.x.y-0.i586.rpm
```
11. 注意默认FreeRADIUS将会已用户`radiusd`运行. 这个用户是在安装FreeRADIUS过程中创建的. 给这个用户`certs`目录的权限. 否则会安装失败.
```
chown -R radiusd. /etc/raddb/certs
```
12. 通过`radiusd -X`来确认`radiused`可以正确启动.
13. `Ctrl+C`将会停止正在运行的FreeRADIUS. 你现在可以使用下面的启动脚本来启动FreeRADIUS
```
/etc/init.d/freeradius start
```

## 发生了什么?
我们完成了在SLES上编译和安装FreeRADIUS. 尽管不像CentOS那样优雅, 仍然没有许多问题.

## zypper还是yast -i
使用`zypper`来安装需要的依赖. 使用`yast -i`来安装需要的`gettext-devel`包将会返回一个错误. `zypper`知道`gettext-tools`包将会满足依赖.

使用`yast -i`来安装依赖也导致从OpenSUSE仓库不需要的更新. 你会被警告.

## 手动tweak
有一些tweak我们必须手动执行, 为了获取在SLES上最新的FreeRADIUS.
* 添加OpenSUSE仓库作为一个开发包源.
* 从OpenSUSE安装各种开发包.
* 编辑`freeradius.spec`来卸载旧的宏.
* 修改包含证书的目录的权限.




