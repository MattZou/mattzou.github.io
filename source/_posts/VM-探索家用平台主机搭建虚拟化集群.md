---
layout: post
title: 家用平台主机搭建虚拟化集群
author: MattZou
index_img: https://mattblog.oss-cn-beijing.aliyuncs.com/img/index_img/Kvmbanner-logo2_1.png/bg
banner_img: https://mattblog.oss-cn-beijing.aliyuncs.com/img/index_img/kvm-01.png/bg
date: 2021/08/27
updated: 2021/08/29
categories: 虚拟化
tags: KVM
description: 虚拟化小探索
---

# 探坑
最近购置了三台办公用主机，既要兼顾`Windows`平台下办公需求，又要考虑开发`Linux`集群部署，物理机->虚拟化->容器这样的架构从维护和拓展性上都是最佳的选择，虚拟化是一切服务的基础，因此需要找到一个合适的虚拟化方案。

找了一圈，开源，主流，易上手的虚拟化方案主要有两种：物理机`Win`环境，用`wsl`或者`VirtualBox`跑`CentOS`；另一种是物理机`CentOS`，用`KVM`虚拟`Win`以及`Linux`。前一种方案，在单机上尝试过，`wsl/wsl2`各有优劣，一切看微软大爷眼色行事，更加适用于个人或小组研究，扩展性上估计一堆坑，所以绕过，考虑在`Linux`下用`KVM`的[解决方案](https://www.redhat.com/zh/topics/virtualization/what-is-KVM)。

单机上架设多个VM，使用`virt-manager`可以很方便进行管理，对于集群`KVM`管理，常用的是`Proxmox`和`WebVirtMgr`，都比较容易上手。但看着`Proxmox`官网更加炫酷，安装方便，集群维护、VM迁移等功能齐备，而且网上保姆教程一大堆，用`Ventory`还能自动部署，就选它了。

---
> 以上都是动手前的美好设想。

# 掉坑
## 硬件配置
三台配置相同，如下表所示。

| Part 	| Model 	|
|:---:	|:---:	|
| CPU 	| Tiger Lake i7-11700 8c16t 2.5-4.9GHz 	|
| GPU 	| Intel® UHD Graphics 750 	|
| Memory 	| 16*2 3600MHz 	|
| Motherboard 	| B560m 	|
| Ethernet 	| RTL8125B 	|
| SSD 	| M.2 500g 	|
| HDD 	| 5400RPM 2T 	|

装机就开始坑了，CPU风扇方向装反，拆了装装了拆===好歹是开机亮灯没问题。先装了个Win做稳定性测试，FS140风冷压不带k的i7还是没问题，不过反复拆装可能导致一个硅脂没涂好，两台双烤差了5℃，我都怕没撕膜直接怼上了。B560主板支持内存超频，镁光C9BJZ颗粒XMP上3600[轻松容易](https://zhuanlan.zhihu.com/p/369653535)，就是3600再往上，[内存分频](https://www.bilibili.com/video/BV1AM4y157ut)代价太大，就不折腾了。

## Proxmox安装
Proxmox VE最新7.0版本是基于Debian 11，安装方式提供iso直装，批量部署推荐使用Ventory（真是装机神器，iso一放，脚本一配置就可以喝茶去了）。

但是，在这个家用机平台，安装出问题了😭。

7.0镜像启动，进入Boot选择，自检过后，在Debian中走到
`starting a root shell on tty3`时候直接卡住，两眼一抹黑，连左上角卡住光标的机会都没有===

找各种资料后，发现也有这个新平台（z590 + 11700）出[问题](https://forum.proxmox.com/threads/stuck-on-root-shell-on-tty3.86956/)，尝试里面的解决方法
### 修改xorg配置
```
$ chmod 1777 /tmp  
$ apt update
$ apt upgrade
$ Xorg -configure  
$ mv /xorg.conf.new /etc/X11/xorg.conf
$ vim /etc/X11/xorg.conf # update Driver "amdgpu"-> "fbdev" <-be sure to get all instances in the case of a multi-head card which tripped me up initially.
$ startx

```
一番折腾后总算看到安装界面，但是上面只有几个大字
**`No Network Interface Found`**
事情变得微妙起来，新平台出问题，大概率是驱动跟不上。既然是网络问题，于是带上网卡关键词[No Network Interface Found for RTL8125B](https://forum.proxmox.com/threads/no-network-interface-found-for-rtl8125b.72378/)，思路豁然开朗。
其中有人通过一个复杂的网卡驱动安装解决了。

### 安装网卡驱动
```
1) 1 usb with proxmox, 1 usb with driver package
2) Boot into proxmox installer and Select debug mode installation
3) Press ctrl+d once for it to proceed
4) plug in android phone after enabling usb tether on the phone
5) type in ‘ ip addr show ‘ And note down the interface name for the usb tether, mine was enp0s something something
6) type in ‘ ip link set enp0s up’ #replace enp0s with your own interface name
7) type in dhclient enp0s #replace enp0s with your own interface name
8) test connectivity by using ping, ‘ping 1.1.1.1’
9) 'apt update'
10)install pve-header apt install pve-headers-$(uname -r)
11) cd into a directory to make the second usb mount= 'cd /media'
12) ‘mkdir usb’
13) use blkid to find the correct /dev/sd** name for your usb, (mine was sda1)
14) ‘mount /dev/sda1 /media/usb/‘
15) cd /media/usb
16)’ls’ #to find names
17) my tmp folder was restricted and apt couldn't use it, i had to 'chmod 1777 /tmp' to make it open again
18) 'apt install build-essential'
19) extract r8125-9.003.05.tar if not already done so, 'tar -xvf r8125-9.003.05.tar'
20) cd into r8125-9.003.05 folder and execute the script autorun.sh= 'chmod +x autorun.sh' and './autorun.sh'
21) now you can unplug the usb tether, repeat steps 5-8 to check if your Ethernet connection works.
22) now you can do what they ask the ctrl+D again(?) to go back into normal mode and it'll find your network interface and you can proceed normally.
```
其实驱动问题大部分还是kernel问题，查阅发现`Linux kernel`对`RTL8125B`网卡支持在[5.9](https://linuxreviews.org/Realtek_RTL_8125)就已经实现了啊。Debian 11咋也是这之上了，无解。

可能点背，上面那一串步骤执行了，还是没反应。（这个后面再试试，估计是那天太累了🤣）

然后发现一堆人用着这个网卡，Proxmox还是6.4的都搞定了，他们做的就是升级内核到5.11。[[SOLVED] Realtek RTL 8125B](https://forum.proxmox.com/threads/realtek-rtl-8125b.86747/)

当然，终极大杀器就是[Install Proxmox VE on Debian Buster
](https://pve.proxmox.com/wiki/Install_Proxmox_VE_on_Debian_Buster)

### 先安装Debian再装Proxmox
这个后面有空再折腾，估计又是一堆内核，驱动升级。
好在GitHub上有人放出来了基于Debian发行版的[解决方案](https://github.com/tubaxiaosiji/RTL8125-Driver-for-Proxmox-VE5-6-and-debian)，后面有机会抄作业。

# 爬坑
折腾了好久还没成效，本着退一步海阔天空🌏的想法，暂时放弃Proxmox🤷‍♂️，就先装CentOS用着，后面Proxmox折腾明白了再做单机VM向集群迁移也行。

在团队大佬的点醒下，退化到中级玩法（定义来自[此文](https://zhuanlan.zhihu.com/p/49118355)）试试👨‍🚀。

```
那么 KVM + Libvirtd 有几种不同层次的玩法：

初级：在 /etc/libvirtd/qemu 下面用 xml 描述每一台虚拟机的配置，
    然后用 virsh 在命令行管理虚拟机，
    最后用 VNC/SPICE 按照配置好的端口链接过去，模拟终端操作。
中级：使用各种 libvirtd 的前端，比如基于桌面 GUI 的 Virt Manager 给你界面上直接编辑和管理虚拟机，
    桌面版本的 VNC/SPICE 会自动弹出来，像 VmWare 一样操作。
高级：使用基于 Web 的各种 virt manager 进行集群管理，
    比如轻量级的 WebVirtMgr / Kimchi，
    适合小白的 Proxmox VE。基本是用 WebVnc/Web
超级：上重量级的 OpenStack，
    搭配自己基于 libvirt （libvirtd 的客户端库，比如有 python-libvirt 的封装）写的各种自动化脚本。
```
## CentOS KVM配置
实验在CentOS中配置一台WinServer虚拟机。
首先在BIOS中打开虚拟化。
### 安装必要包
CentOS8中自带了QEMU/KVM支持
参考[^1][^2]
```
yum groupinstall "Virtualization Host"
sudo systemctl enable --now libvirtd
sudo yum -y install virt-top libguestfs-tools
```
可以选择以两种直观的可视化界面进行配置。

GUI管理工具
```
sudo yum -y install virt-manager
```

Cockpit Web Console管理工具
```
dnf install cockpit cockpit-machines
systemctl start cockpit.socket
systemctl enable cockpit.socket
systemctl status cockpit.socket
firewall-cmd --add-service=cockpit --permanent
firewall-cmd --reload
```
### 配置桥接网络
为了方便虚拟机外网连通访问，需要架设网桥。此处需要物理网口支持，Wifi网卡配置存在问题。
关于Bridge和NAT模式区别，可参考[Virtual Networking](https://www.virtualbox.org/manual/ch06.html#networkingmodes)和[网络配置Bridge方式、NAT方式](https://www.jianshu.com/p/ed0ce43374e6)

CentOS8 使用nmcli命令进行网络配置

1. 查看当前连接
```
nmcli connection
```
记录ethernet的device 标识

2. 新建名为br0的网桥
```
nmcli connection add type bridge con-name br0 ifname br0 autoconnect yes
```

3. 编辑网桥，设置静态IP及网关
```
nmcli conn modify br0 ipv4.addresses '192.168.0.x/24'
nmcli conn modify br0 ipv4.gateway '192.168.0.1'
nmcli conn modify br0 ipv4.dns '192.168.0.1'
nmcli conn modify br0 ipv4.method manual
```
或者编辑文件
```
vim /etc/sysconfig/network-scripts/ifcfg-br0
```
修改
```
IPADDR=192.168.0.x
PREFIX=24
GATEWAY=192.168.0.1
DNS1=192.168.0.1
```

4. 网桥连接至物理网卡
假设物理网卡 device 标识为 enp3s0
```
nmcli connection add type bridge-slave ifname enp3s0 master br0
```

5. 启动网桥
```
nmcli connection up br0
```

6. 关闭物理网卡
```
nmcli connection down enp3s0
```
再次查看连接状态，或者ping一下百度🫓，确认联网成功。

### Windows镜像安装
合理合法获取ISO文件👮🚔。

使用之前安装的`virt-manager`工具
![](https://phoenixnap.com/kb/wp-content/uploads/2021/04/2020-11-03_13-19-09.png)
或者通过`Cockpit`安装。
![](https://www.tecmint.com/wp-content/uploads/2020/03/Create-New-Virtual-Machine.png)

详细配置参考[10 Easy Steps To Install Windows Server in Linux KVM](https://getlabsdone.com/install-windows-server-on-kvm/)，文章步骤极为清晰。要注意的地方有三个：
1. CPU设置，一定要指定拓扑结构，设置不当会影响性能[Configure the CPU for the KVM windows guest properly.](https://getlabsdone.com/install-windows-server-on-kvm/#Configure-the-CPU-for-the-KVM-windows-guest)。关于这些参数如何设置，可以参考[【进程调度】关于CPU的sockets、dies、cores、threads含义理解](https://www.cnblogs.com/GyForever1004/p/13600183.html)。

2. 硬盘一定选择VirtIO形式，比SATA性能更好，甚至接近物理机水平[No windows harddisk in KVM ?, fix that now.](https://getlabsdone.com/install-windows-server-on-kvm/#No-windows-harddisk-in-kvm)；此处注意的原因是，一旦用SATA模式安装后，再切换回VirtIO模式，Windows会显示`INACCESSIBLE_BOOT_DEVICE`并BSOD，后续切换会很费劲。

3. VirtIO驱动很重要，不能遗漏下载。

### 物理机迁移到KVM
安装好虚拟机之后，需要把原先物理机上的系统原封搬过来。我使用[傲梅轻松备份](https://www.disktool.cn/backup/backup-software.html)工具
1. 在原机器上进行系统备份，将备份导入移动硬盘
2. 在工具里创建PE，选择Windows版，创建过程这会打包VirtIO驱动，生成的ampe.iso通过scp传输到宿主机上
3. 关闭虚拟机，添加硬件，挂载备份硬盘，ampe.iso，VirtIO驱动iso。设置ampe.iso为启动项
4. 进入恢复程序，选择备份，进行全盘恢复
5. 重启虚拟机，卸除挂载，设置主硬盘为引导项
6. 设置宿主机引导时，虚拟机同步启动

### VM迁移
将物理机迁移到KVM上之后，KVM也提供不同物理机之间的VM迁移，相当于集群间虚拟机移动，KVM迁移有冷热两种。由于热迁移需要设置NFS共享盘，还没做测试，目前通过冷迁移完成了一个虚拟机的移动。
首先确保目标宿主机配置好了KVM环境和网络。
#### 冷迁移
关机
1. 通过scp把虚拟机所有qcow2镜像文件传输到目标宿主机
2. 把此机器配置xml文件传输到目标，文件位置在`/etc/libvirt/qemu/`
```
scp -rp /data/win01.qcow2 root@172.168.0.x:/data
scp -rp /etc/libvirt/qemu/cwin01.xml root@172.168.0.x:/etc/libvirt/qemu/
```
3. 目标宿主机切换到xml配置目录
```
virsh define win01.xml 
virsh list --all
```
可以看到虚拟机已经注册
此时启动虚拟机，可能会遇到[KVM上创建虚拟机和权限有关的故障](https://www.pianshen.com/article/65991564217/)。
建议在启动前，配置qcow2存放目录权限
```
chown qemu:qemu /data
```
#### 热迁移
暂未做测试，留坑。参考[^3]

### KVM性能优化
1. 存储主要是磁盘模式（VirtIO）
2. CPU：CPU较新的型号KVM里没有提供对应模板，因此在cpu型号设置中手动输入`host-passthrough`可以向VM公开主机CPU的全部功能，默认设置下11700处理在虚拟机中`cpuz`自动识别为10代服务器处理器，且锁定频率为基频。设置直通后，可以识别为正确型号，且睿频生效[^4]。
3. 显示性能，由于11代处理器集成显卡有较大升级，后期准备研究一下`Intel GVT-g`显卡直通技术，提升虚拟机显示性能，视频可以看起来了📺
4. 显示模式：目前采用QXL模式，默认16M显存，在配置xml中改造为128M，重载配置生效，由于手头都是1080p显示器，不知道是否真能支持4K[^5]。


## KVM管理WebVirtCloud
[WebVirtMgr](http://retspen.github.io/)是之前很很火的的KVM虚拟化Web管理工具，但GitHub上年久失修，并且逐渐迁移到WebVirtCloud，因此考虑部署WebVirtCloud管理服务器用来对集群虚拟机进行集中管理。

### 简介
[WebVirtCloud](https://github.com/retspen/webvirtcloud)基于Python 3.x & Django 3.2 LTS构建。

特性包括：
```
QEMU/KVM Hypervisor Management
QEMU/KVM Instance Management - Create, Delete, Update
Hypervisor & Instance web based stats
Manage Multiple QEMU/KVM Hypervisor
Manage Hypervisor Datastore pools
Manage Hypervisor Networks
Instance Console Access with Browsers
Libvirt API based web management UI
User Based Authorization and Authentication
User can add SSH public key to root in Instance (Tested only Ubuntu)
User can change root password in Instance (Tested only Ubuntu)
Supports cloud-init datasource interface
```

### 部署
#### 创建WebVirtCloud服务器
由于WebVirtCloud需要配置默认Nginx配置，且没有提供Docker部署方式，因此考虑单开一台虚拟机作为管理服务器。创建CentOS8-GUI虚拟机一台。选择GUI版，并附加安装虚拟机组件，一些WebVirtCloud的依赖包附带安装了，配置的时候比较省事。

#### WebVirtCloud快速安装模式
1. 进入快速安装
网好
```
wget https://raw.githubusercontent.com/retspen/webvirtcloud/master/install.sh
chmod 744 install.sh
# run with sudo or root user
./install.sh
```
网不好，[下载](https://github.com/retspen/webvirtcloud/archive/refs/heads/master.zip)包，解压，然后
```
chmod 744 install.sh
# run with sudo or root user
./install.sh
```
2. 快速安装模式里配置端口，建议默认，后期可以配置端口转发[^6]。
以下是默认配置和需要确认的部分
```
WEBVIRTCLOUD

  Welcome to Webvirtcloud Installer for CentOS, Fedora, Debian and Ubuntu!

  The installer has detected centos version 8.
  Q. Do you want to configure fqdn for Nginx? (y/n) y

  Q. What is the FQDN of your server? ({serverip_or_hostname}):
     Setting to {serverip_or_hostname}

  Q. Do you want to change NOVNC service port number?(Default: 6080)
     Setting novnc service port 6080

  Q. Do you want to change NOVNC public port number for reverse proxy(e.g: 80 or 443)?(Default: 6080)
      Setting novnc public port 6080

  Q. Do you want to change NOVNC host listen ip?(Default: 0.0.0.0)
      Setting novnc host ip 0.0.0.0
```
确认后会自动执行安装，如果没有错误，会出现
```
***Open http://{hostname} to login to webvirtcloud.***

* Cleaning up...
* Finished!
```

### 配置
#### 添加Webvirtcloud服务器对KVM宿主机的ssh访问
1. 在Webvirtcloud服务器端，生成ssh key
```
su - nginx -s /bin/bash
ssh-keygen -t rsa -P ''
ls -lh /var/lib/nginx/.ssh/
```
2. 传输到KVM宿主机
```
ssh-copy-id root@compute1
```
3. 测试nginx用户对KVM宿主机的访问连接
```
virsh --connect qemu+ssh://root@compute1/system list --all
```
如果可以看到虚拟机列表，则表明配置成功。
每次新增KVM宿主机节点，按以上步骤实现一遍即可。

#### 访问Web Dashboard
在http://{serverip_or_hostname}可进入管理登录页面，默认admin/admin。进入后可以修改密码等配置。

在计算节点（Computer）面板，添加ssh连接到宿主机。FQDN/IP,Login等项目按照刚才配置的ssh访问项目填写即可。
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/IT/KVM/hosts.png)
添加后状态显示成功即可，返回实例（Instance）面板对宿主机下挂虚拟机进行管理。
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/IT/KVM/grouped.png)
实例详情
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/IT/KVM/instance.png)


## 内网穿透Frp
服务器配置好，外网如果要访问，且没有固定IP（其实是💴不够），需要内网穿透方案。应急可以使用花生壳的免费版，但流量速度有限。刚好有台阿里云ECS，可以作为跳板，进行内网穿透。
选用Frp方案进行配置。
### Server端
阿里云服务器作为Server
1. 获取Frp
```
wget https://github.com/fatedier/frp/releases/download/v0.32.1/frp_0.32.1_linux_amd64.tar.gz
tar -zxvf  frp_0.32.1_linux_amd64.tar.gz
```
2. 编辑`frps.ini`文件
配置server和client通讯端口，这里设置为7000
配置frp控制面板，可以监控端口状态
``` ini
[common]
bind_port = 7000

#frp 控制面板
dashboard_port = 7500

# dashboard 用户名密码可选，默认都为 admin
dashboard_user = admin
dashboard_pwd = admin
```

3. 配置服务自启动
```
vi /etc/systemd/system/frps.service
```
内容为
``` ini
[Unit]
Description=frps
After=network.target

[Service]
ExecStart=/root/frp_0.32.1_linux_amd64/frps -c /root/frp_0.32.1_linux_amd64/frps.ini 

[Install]
WantedBy=multi-user.target
```
```
# 启动测试
systemctl start frps.service
# 查看启动状态
systemctl status frps.service
# 开机自启
systemctl enable frps.service
```

4. 配置之后，需要在ECS控制台规则里放行通讯端口以及需要映射出去的端口。


### Client端
在需要使用Frp的机器上进行配置
1. 下载Frp
2. 编辑`frpc.ini`
``` ini
[common]
server_addr = X.X.X.X # 填自己服务器端的IP地址或者域名
server_port = 7000 # 服务器端口，跟server端对应

[ssh]  #在一个server体系下，此处名称不能冲突
type = tcp
local_ip = 127.0.0.1 
local_port = 22
remote_port = 6000 
```
1. windows下设置为系统服务，保证自启动
用winsw将frp注册为系统服务
下载：https://github.com/kohsuke/winsw/releases
改名为winsw.exe，放入frp目录
在此目录下，新建`utf8`编码的xml，命名`winsw.xml`,内容为
``` xml
<service>
    <id>frp</id>
    <name>frp这里是服务的名称</name>
    <description>这里是服务的介绍</description>
    <executable>frpc</executable>
    <arguments>-c frpc.ini</arguments>
    <onfailure action="restart" delay="60 sec"/>
    <onfailure action="restart" delay="120 sec"/>
    <logmode>reset</logmode>
</service>
```
在目录里cmd运行
``` cmd
winsw install
winsw start

# winsw stop
# winsw uninstall
```
在系统服务中就可以看到frp服务了


### 更多
更多高级设置可参考[文档](https://gofrp.org/docs/)。其实主要就是在两端配置frps和frpc配置文件。



# 感想
折腾一路下来，最深刻的感受就是：遇事不决就重启。
kill->restart->reboot->🤡

家用平台折腾了一圈，发现虚拟化最好的解决方案：花钱让专业的人买专业的服务器给你弄💴💴💴

当然，自己折腾乐呵乐呵就行，要不容易上头👨‍🦲


# Acknowledgements 
   <a href="https://gitee.com/hejiang" class="card-body hover-with-bg" target="_blank" rel="noopener">
     <div class="card-content">
      <div class="link-avatar my-auto">
        <img src="https://portrait.gitee.com/uploads/avatars/user/180/541417_hejiang_1579424599.png!avatar200" alt=""
             onerror="this.onerror=null; this.srcset=null; this.src=''"/>
      </div>
       <div class="link-text">
         <div class="link-title">hejiang</div>
         <div class="link-intro"></div>
       </div>
     </div>
   </a>


# Reference

[^1]:[How to Install KVM on CentOS 8](https://phoenixnap.com/kb/install-kvm-centos)
[^2]:[How to Install KVM on CentOS/RHEL 8](https://www.tecmint.com/install-kvm-in-centos-8/)
[^3]:[KVM虚拟机迁移（冷迁移、热迁移）](https://www.wanhebin.com/devops/kvm/893.html)
[^4]:[在virt-manager中使用CPU型号主机直通](https://blog.csdn.net/allway2/article/details/102940533)
[^5]:[qemu+spice的QXL配置](https://www.icode9.com/content-4-960089.html)
[^6]:[Install WebVirtCloud KVM Management on CentOS 8 / Stream 8](https://computingforgeeks.com/install-webvirtcloud-kvm-management-on-centos/)