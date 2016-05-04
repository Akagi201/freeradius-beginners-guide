Ubuntu是基于Debian Linux, 使用dpkg包挂利器来管理软件像deb.

`apt`程序是用来管理dpkg仓库, 跟`zypper`和`yum`使用RPM是一样的.

下面的步骤将会展示如何安装deb.
1. 安装`dpkg-dev`包. 这个包提供开发工具(包括`dpkg-source`), 用来unpack, 编译, 和上传Debian源码包.
`sudo apt-get install dpkg-dev`
如果这个包没有安装, 检查被`apt`程序使用的源列表. 这个仓库的源定义在`/etc/apt/sources.list`文件, 并且通过`apt-get update`命令更新.
2. 安装所有`freeradius`包需要的开发库
```
sudo apt-get build-dep freeradius
```
如果你得到一个信息: `Unable to find a source package for freeradius`, 可能因为仓库源(deb-src)没有包含在`/etc/apt/sources.list`文件.
3. 确保`fakeroot`程序被安装.
```
sudo apt-get install fakeroot
```
4. 确保`ssl-cert`包被安装. 不能安装这个包将会导致编译EAP模块出现问题.
```
sudo apt-get install ssl-cert
```
5. 下载最新版本的FreeRADIUS源码.
```
wget ftp://ftp.freeradius.org/pub/freeradius/freeradius-server-2.x.y.tar.gz
```
6. 解压FreeRADIUS的源码TAR文件.
```
tar -xzvf freeradius-server-2.x.y.tar.gz
```
7. 切换到解压目录.
```
cd freeradius-server-2.x.y
```
8. 在当前目录创建一个`fakeroot`环境, 并且使用已经包含在`debian`子目录的控制文件来编译包. 编译过程将会花费一些时间并且依赖计算机能性能. 你可以监控这个命令的结果,是否有fatal错误, 尽管他应该能够工作.
```
fakeroot dpkg-buildpackage -b -uc
```
9. 这将会创建所有需要的deb并且把他们放到`freeradius-server-2-x.y`目录外.
10. 通过下面的命令安装新的deb.
```
cd ../
sudo dpkg -i freeradius_2.x.y+git_i386.deb freeradius-common_2.x.y+git_all.deb freeradius-utils_2.x.y+git_i386.deb libfreeradius2_2.x.y+git_i386.deb
```

## 发生了什么?
我们刚刚创建和安装了最新的FreeRADIUS包到Ubuntu. 下面是一些分解的重点.

### 安装dpkg-dev
`dpkg-dev`包安装编译deb的需要的所有工具. 这包括`autoconfig`和`make`工具, 还有一个编译器. 也包括`dpkg-buildpackage`陈天旭, 我们将会使用来创建deb.

### 使用build-dep
`apt-get`是接下来使用的命令来指导他做什么. 当我们安装包时, 使用`install`命令, `build-dep`命令用来安装所有一个指定源码包的依赖.

我们没有使用随着ubuntu一起的源码包, 而是使用来自FreeRADIUS最新的源码包. 这表示他不是一个十分安全的方法, 尤其当最新的源码包含开发库跟ubuntu默认装的版本不一致的时候. 然而你将会被通知在编译过程中, 可以最后安装这个依赖的库.

### fakeroot
这个命令允许我们作为一个普通用户编译deb, 并且是推荐的方式. `Fakeroot`跟着一个命令. 这个`fakeroot`调用的命令将会认为他是通过root用户有root权限对于文件操作. 在我们的例子中, 我们使用`dpkg-buildpackage`命令.

### dpkg-buildpackage
这个命令在FreeRADIUS源码目录运行. 他将会利用`debian`目录的指定信息来编译源码和术后创建各种FreeRADIUS包. `-b`选项是对于只有二进制的编译, 而`-uc`选项是跳过一个`gpg`密钥的签名.

### 安装debs
我们安装基础的包. 当我们需要额外的二模考时, 他们可以作为一个包来安装. 需要的依赖可以通过使用`apt`命令来安装.

## 对于那些守旧派的人
尽管我们鼓励这样编译包, 我们没有阻止你使用`configure, make, make install`模式. 如果你运行`./configure --help`你会看到一个选项列表. 你可以用来tweak FreeRADIUS的编译过程. 你可以例如指定某个目录(--bindir=/usr/bin), 使能或禁用某个特性(--enable-developer), 或者包括某个包(--with-openssl)
