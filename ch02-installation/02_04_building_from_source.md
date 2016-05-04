从源码安装软件是`configure, make, make install`的同义词. 我们将使用发型吧的包管理器来从源码编译新的软件.

下一节是可选的内容如果你已经安装了FreeRADIUS的预编译的包.

## 从源码编译
有时有需要来安装最新版本的软件或者包含一些预编译软件没有支持的模块. 这需要从源码编译软件. 大多数开源软件包是作为一个TAR压缩包发布的. TAR实际上可以指用于创建TAR文件的程序(名称是tape archive的简写)或者合成文件的文件格式. 一个TAR文件也通常是压缩的来减少体积. 文件名将会表示压缩的格式. 一个文件名以`.tgz`或者`.tar.gz`结尾使用`gzip`. 一个文件名以`.tbz`, `.tb2`, 或者`.tar.bz2`结尾使用`bzip2`压缩. 为了编译软件我们解压这个文件, 然后执行命令`configure`, `make`和`make install`在解压的文件夹内.

然而有更好的方式利用发行版的包管理系统来编译软件. 不是所有的TAR文件帮助这个容易实现, 但是FreeRADIUS是这样一个成熟的项目, 允许在任意这3个发行版上从源码编译软件.

## 编译包的优势
下面是一些从源码编译包的优势
* 容易安装, 升级, 卸载或发布软件.
* 容易检查那个版本的软件是安装的.
* 能够看到哪些文件被安装, 哪些发行版相关的修改.

既然你已经确定用这种方式编译包, 我们将会为每个发行版来做他.

## 创建编译环境

不用生产机器来开发是一个好的实践. 这包括从源码编译软件. 创建一个你可以编译包的虚拟机. 测试之后简单低拷贝和安装新的包到一个生产机器.

## CentOS
CentOS是一个RedHat风格的发行版. 他是RHEL的社区版. CentOS不是有RedHat直接赞助像Fedora项目那样. 尽管RedHat的品牌和logo从CentOS移除, 但是它跟RHEL是一样的, 由于相同的源代码用来生成发行版.

CentOS使用RPM包管理器来管理软件. 安装的软件包叫做RPM.

## 编译CentOS RPMs
这一节使用从FreeRADIUS的wiki页面的推荐和指令来创建最新的RPM到CentOS上. <http://wiki.freeradius.org/Red_Hat_FAQ#How_to_build_an_SRPM>

你必须用root用户来执行大多数命令. 一旦完成, 遵循这些步骤.

1. 安装`rpm-build`包, 这个创建的编译包需要的目录结构. `yun install rpm-build`
2. 允许普通用户对这个目录有写和执行权限. `find /usr/src/redhat -type d | xargs chmod a+wx`
3. Fedora上可以得到FreeRADIUS的最新版本. 我们可以使用这个版本来在CentOS上编译RPM. 从下面的URL上下载RPM源码. <https://kojipkgs.fedoraproject.org//packages/freeradius/2.2.5/2.fc19/src/freeradius-2.2.5-2.fc19.src.rpm> RPM的源码包以`.src.rpm`结尾.
4. 安装这个源码包. 例如使用下面的命令. 你的源码包的命名可能不同. `rpm -ihv --nomd5 freeradius-2.2.5-2.fc19.src.rpm`.
5. 使用下面的命令来决定需要的依赖. 这些依赖必须在包边以前被安装. `rpmbuild -ba /usr/src/redhat/SPECS/freeradius.spec`
6. 使用`yum`来安装这些依赖. 在我的系统上时下面这些. `yum install autoconf gdbm-devel libtool libtool-ltdl-devel openssl-devel pam-devel zlib-devel net-snmp-devel net-snmp-utils readline-devel libpcap-devel openldap-devel krb5-devel python-devel mysql-devel postgresql-devel unixODBC-devel`
7. 安装一个编译器. `yum install gcc`
8. 再次运行`rpmbuild`命令. 使用普通用户来做, 使用`tee`来输出到一个文件来为了将来的研究. 这将会启动编译过程, 需要花费一些时间. `rpmbuild -ba /usr/src/redhat/SPECS/freeradius.spec | tee /tmp/freeradius_build_out.txt`.
9. 当它成功完成, 将会有一个指示RPM已经写入. 新安装的RPM将会得到下面这行. `Wrote: /usr/src/redhat/RPMS/i386/freeradius-debuginfo-2.1.10-1.i386.rpm`
10. 安装新编译的RPM. 下面的命令将会导致一个基本的FreeRADIUS安装. `yum --nogpgcheck install /usr/src/redhat/RPMS/i386/freeradius-2.1.10-1.i386.rpm /usr/src/redhat/RPMS/i386/freeradius-utils-2.1.10-1.i386.rpm` 如果你得到消息通知你`No package <path and package name> available`, 确认你指定了正确的版本号.

## 发生了什么?
我们刚刚创建和安装了CentOS上最新的RPM. 下面是一些重点的分解.

## 安装`rpm-build`
`rpm-build`包包含脚本和可执行程序用来编译RPM包. 他包括`rpmbuild`程序, 我们将用来创建RPM.

## RPM包的源码.
我们使用FreeRADIUS wiki上推荐的Fedora的RPM包源码. 这导致一个更加稳定和更好的包工作集合, 已经包含了发行版相关的tweak和修改.

## 包名
注释包名是`freeradius`而不是`freeradius2`. 这是因为所有的在Fedora里面的包已经是2.x.

从FreeRADIUS 1.x版本升级在Fedora系统不像CentOS那样没有问题.

## 更新一个已经存在的安装
如果你已经有FreeRADIUS安装了, 他可能包含一些模块你需要包含在更新的时候.
* 决定FreeRADIUS的版本和当前安装的包通过使用下面的命令. `yun list freeradius\*`
* 当你已经作为预编译包安装了`freeradius2`, `yum`将会抱怨, 当尝试安装新的`freeradius`包, 因为名字冲突. 你可以卸载`freeradius2`包, 通过下面的命令. `yum erase freeradius\*`
* 上面假定你没有做任何配置修改到安装, 你想要保留.

















