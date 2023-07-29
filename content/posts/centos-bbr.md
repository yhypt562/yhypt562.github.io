---
title: "CentOS7安装新版内核和开启BBR加速"
date: 2023-07-27T08:34:16+08:00
draft: false
keywords:
- centos7
- bbr
description: "CentOS7安装新版内核和开启BBR加速"
---


## 设置安装内核

查看当前[服务器](https://cloud.tencent.com/product/cvm?from=20065&from_column=20065)的内核版本。

```sh
uname -sr
```

uname命令用于打印当前系统相关信息（内核版本号、硬件架构、主机名称和操作系统类型等）。

```sh
-a或--all：显示全部的信息；
-m或--machine：显示电脑类型；
-n或-nodename：显示在网络上的主机名称；
-r或--release：显示操作系统的发行编号；
-s或--sysname：显示操作系统名称；
-v：显示操作系统的版本；
-p或--processor：输出处理器类型或"unknown"；
-i或--hardware-platform：输出硬件平台或"unknown"；
-o或--operating-system：输出操作系统名称；
--help：显示帮助；
--version：显示版本信息。
```

BBR内核要求是4.9+，通常来说你通过上面这个命令出来的内核版本是在3.几。接下来启用 ELRepo 仓库

```sh
rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org
rpm -Uvh https://www.elrepo.org/elrepo-release-7.0-3.el7.elrepo.noarch.rpm
```

然后安装新版的稳定版内核

```
yum --enablerepo=elrepo-kernel install kernel-ml -y
```

安装完毕后使用下面的命令查看是否安装成功。

```
rpm -qa | grep kernel
```

我的显示如下：

```
kernel-3.10.0-862.14.4.el7.x86_64
kernel-ml-5.3.8-1.el7.elrepo.x86_64
kernel-3.10.0-1062.4.1.el7.x86_64
kernel-headers-3.10.0-1062.4.1.el7.x86_64
kernel-3.10.0-957.5.1.el7.x86_64
kernel-3.10.0-1062.1.2.el7.x86_64
kernel-tools-3.10.0-1062.4.1.el7.x86_64
kernel-tools-libs-3.10.0-1062.4.1.el7.x86_64
kernel-3.10.0-957.1.3.el7.x86_64
```

里面kernel-ml-5.3.8-1.el7.elrepo.x86_64就是安装的新版版本内核（你看到这篇教程的时候可能内核版本有变化，随机应变）

接下来需要设置系统启动顺序，使用下面的命令。

```
sudo egrep ^menuentry /etc/grub2.cfg | cut -f 2 -d \'
```

我的显示如下：

```
CentOS Linux (5.3.8-1.el7.elrepo.x86_64) 7 (Core)
CentOS Linux (3.10.0-1062.4.1.el7.x86_64) 7 (Core)
CentOS Linux (3.10.0-1062.1.2.el7.x86_64) 7 (Core)
CentOS Linux (3.10.0-957.5.1.el7.x86_64) 7 (Core)
CentOS Linux (3.10.0-957.1.3.el7.x86_64) 7 (Core)
CentOS Linux (3.10.0-862.14.4.el7.x86_64) 7 (Core)
CentOS Linux (0-rescue-618ca2de6e204efbb013b592564ef36a) 7 (Core)
```

排在第一的就是CentOS Linux (5.3.8-1.el7.elrepo.x86_64) 7 (Core)，从第一行为0依次数，0、1、2、3这样，看你的新内核是第几。 然后就输入下面的命令（命令例子为第1行）

```
sudo grub2-set-default 0
```

接下来重启服务器

```
reboot
```

再次查看内核版本

```
uname -r
```

内核版本显示为4.9以上，本文更新的时候新版版本是5.3.8，就证明安装成功了。

重建内核配置

```
grub2-mkconfig -o /boot/grub2/grub.cfg
```

重启系统验证，没问题就OK了。



## 在CentOS7新内核上开启BBR

要在新安装好的CentOS7上面启用新内核，只需要复制下面的代码执行就可以了。

```
echo 'net.core.default_qdisc=fq' | sudo tee -a /etc/sysctl.conf
echo 'net.ipv4.tcp_congestion_control=bbr' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

然后输入下面的命令查看是否开启BBR成功

```
sudo sysctl net.ipv4.tcp_available_congestion_control
```

成功的话应该是下面这种输出

```javascript
net.ipv4.tcp_available_congestion_control = bbr cubic reno
```

继续验证

```
sudo sysctl -n net.ipv4.tcp_congestion_control
```

输出应该是

```
bbr
```

最后看内核模块是否加载

```
lsmod | grep bbr
```

输出应该是类似下面这种

```
tcp_bbr 16384 0
```

