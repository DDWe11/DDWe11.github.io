---
title:Linux_CentOS 环境配置
---

# Linux CentOS 环境配置

> 该环境为计算机科学与技术专业大数据方向教学以及实训环境
>
> 配置过程中可以联系助教,Mac系统的同学可以通过[介绍](/docs/ABOUT/intruction.md)联系我
>
> 该文档以mac os作为讲解装配,使用windows的同学可以使用[上课笔记连接](https://note.youdao.com/s/CHjhLHnx)🔗

## 环境版本

### 1.介绍

> 我使用的配置:2020款 M1chip16GB Macbook Pro 
>
> 系统环境:MacOS Venture 13.2

痛点: 由于绝大部分同学使用的PC环境都是windows,所以老师上课讲解展示时使用的都是windows,然而使用mac的同学往往因为系统环境配置的问题,四处请教但不断碰壁,这也是我做这个分享的初衷,希望可以帮助到大家

版本:为了避免不必要的麻烦我们尽量使用同时期(相同版本)的软件作为自己配置环境

- 虚拟机: VMware Fusion Pro

![](Midea_BD/VMware.jpg)

- Linux平台:Cent OS 7(需下载ISO纯净版)
- 远程shell平台:FinalShell
- JDK版本:jdk-8u162-linux-arm64-vfp-hflt (注意是arm64,这里根据chip的芯片选择)
- Hadoop:Hadoop 2.7.3 tar.gz(注意文件名后缀,Linux下的包)


## 安装虚拟机运行Cent OS

### 1.虚拟机搭建

​		虚拟机（Virtual Machine）指通过软件模拟的具有完整硬件系统功能的、运行在一个完全隔离环境中的完整计算机系统。 在实体计算机中能够完成的工作在虚拟机中都能够实现。 在计算机中创建虚拟机时，需要将实体机的部分硬盘和内存容量作为虚拟机的硬盘和内存容量。每个虚拟机都有独立的CMOS、硬盘和操作系统，可以像使用实体机一样对虚拟机进行操作. 总结：虚拟机具有独立内存、硬盘容量、cup，是一个完整计算机系统，具有优点，如果在使用虚拟机的过程中，出现损坏，或者故障，只需要还原虚拟机设备，就会释放虚拟机资源，重新配置虚拟机，更加方便使用。

![begin1](Midea_BD/begin1.jpg)

![bengin4](Midea_BD/bengin4.jpg)

![begin2](Midea_BD/begin2.jpg)

![begin3](Midea_BD/begin3.jpg)

点击`finish`进入centos安装页面

### 2.CentOS配置

我们选择英文进入

![](Midea_BD/cent1.jpg)

- 这里就是安装主页面,我们一共需要修改三个配置其余全是默认即可(Time,Installation Destination,Root Password)

![cent2](Midea_BD/cent2.jpg)

- time(由于设置中没有北京,我们选择Asia,shanghai)

  ![cent3](Midea_BD/cent3.jpg)

- Installation Destination

  ![cent4](Midea_BD/cent4.jpg)

- Root Password

  ![cent5](Midea_BD/cent5.jpg)

- 配置完成点击`Begin Installtion`进入Linux环境

  - 账号默认是root,密码是刚才设置的(没有显示,凭感觉输入后回车)

  ![cent6](Midea_BD/cent6.jpg)

- **基础配置**
  - 建议分配内存4GB,核心数量为4核心,硬盘空间30GB



### 3.CentOS 网络配置

#### 3.1 检查防火墙并关闭

```shell
//在终端输入这三条命令,关闭防火墙并检查其状态
#停止防火墙
[root@localhost ~]# systemctl stop firewalld
#禁止防火墙随着系统启动而启动
[root@localhost ~]# systemctl disable firewalld
#查看防火墙状态
[root@localhost ~]# systemctl status firewalld
```

![osset1](Midea_BD/osset1.jpg)

#### 3.2 禁用selinux

```shell
#将SELINUX的值设置为disabled
[root@localhost ~]# vi/etc/selinux/config

#将SELINUX的值设置为disabled

#查看是否设置完成
[root@localhost ~]# cat /etc/selinux/config
```

![osset2](Midea_BD/osset2.jpg)

#### 3.3 网络配置

##### 3.3.1 设置网络配置改为NAT,默认NAT

> Virtual Machine -> Network Adapter->NAT

![osset3](Midea_BD/osset3.jpg)

##### 3.3.2 获得固态ip以及VMnet ip和netmask

###### (1) 获得固态ip范围

```shell
打开terminal
~ cd /Library/Preferences/VMware Fusion/vmnet8/  进入vmnet8文件目录
~ vi dhcpd.conf 获得静态ip范围
range  192.168.46.128 -- 192.168.46.254; 在这个区间都可以
```

![osset4](Midea_BD/osset4.JPG)

###### (2) 获得 VMnet8的 ip和 netmask

```shell
通过 nat.conf 获得 VMnet8的 ip和 netmask （该网关待会Centos 7配置时需要用到）
相同路径下打开 nat.conf
ip:192.168.46.2
netmask:255.255.255.0
```

![osset5](Midea_BD/osset5.PNG)

##### 3.3.3 CentOS 7 虚拟网络配置

```shell
//执行命令
//查看这个路径下的网卡配置文件
[root@localhost ~]# cd /etc/sysconfig/network-scripts 
[root@localhost network-scripts]# ls
```

![osset6](Midea_BD/osset6.jpg)

```shell
[root@localhost network-scripts]# vi ifcfg-ensxx(根据自己选择)
//根据刚才查看的数据添加配置
IPADDR=192.168.46.130
NETMASK=255.255.255.0
GATEWAY=192.168.46.2
//DNS配置保持一致
DNS1=114.114.114.114
DNS2=8.8.8.8
```

![osset7](Midea_BD/osset7.jpg)

```
重启网络：
service network restart
systemctl restart network.service
ip addr  ----查询ip地址
```

![osset8](Midea_BD/osset8.jpg)

``` 
配置端口(为后期远程连接shell)
[root@localhost ~]# yum install net-tools -y
使用netstat方式查看端口是否存在：
[root@localhost ~]# netstat -tln | grep 22
```

![osset9](Midea_BD/osset9.jpg)

#### 3.4 连接shell

- 创建文件夹名为Hadoop,创建ssh(linux)连接

![shell](Midea_BD/shell.jpg)

- 添加虚拟机信息
  - 名称:与虚拟机信息一致,可以在   Virtual Machine -> Get Info查看
  - 主机:刚才添加的IPADDR一致

![shell1](Midea_BD/shell1.jpg)

- 点击确认,稍等即可链接到虚拟机

![shell2](Midea_BD/shell2.jpg)

## 下载链接
```
链接: https://pan.baidu.com/s/11IRJl4EQjEM1ZZ7ihfT3Dw?pwd=aspm 提取码: aspm 
--来自百度网盘超级会员v3的分享
```

