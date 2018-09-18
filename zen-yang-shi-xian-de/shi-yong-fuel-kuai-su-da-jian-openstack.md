# 使用Fuel快速搭建OpenStack

## 下载Fuel镜像

* [https://wiki.openstack.org/wiki/Fuel\#Releases](https://wiki.openstack.org/wiki/Fuel#Releases)

## 下载本地Bootstraps Image

*  MOS9.0   本地源下载：链接：https://pan.baidu.com/s/1gNnHymigLPbyY4q2Y8uRdQ 密码：wbm4
*  MOS9.0 bootstrap下载：链接：https://pan.baidu.com/s/1sDzSOPYGzyhkMyx6G1LJUA 密码：5eoi

## 网络拓扑

![](../.gitbook/assets/1.jpg)

## 工具准备

* VirtualBox
* 官方推荐使用CPU：4核，以及内存：4G以上、10G网卡、500G物理磁盘

## V添加3块虚拟网卡

```text
VirtualBox--全局设置--网络--仅主机（Host-Only）网络（H）-->

Host-Only Ethernet Adapter
IPv4: 10.20.0.10
Mask:255.255.255.0
DHCP: Unable
 
Host-Only Ethernet Adapter#2
IPv4: 172.16.0.1
Mask:255.255.255.0
DHCP: Unable
 
Host-Only Ethernet Adapter#3
IPv4: 192.168.0.1
Mask:255.255.255.0
DHCP: Unable
```

## 创建3台虚拟机

```text
Fuel-master       2C+ 60GB + 3网卡 +2GB 以上内存
Fuel-Controller   1C+120GB + 3网卡 +4GB
Fuel-Compute      1C+120GB + 3网卡 +4GB
```

* 创建Fuel-master

![](../.gitbook/assets/2.jpg)

![](../.gitbook/assets/3.jpg)

![](../.gitbook/assets/4.jpg)

![&#x7F51;&#x5361;1&#x548C;2&#x548C;3&#x914D;&#x7F6E;&#x90FD;&#x4E00;&#x6837;](../.gitbook/assets/5.jpg)

* 创建Fuel-Controller
* 创建Fuel-Compute

```text
Fuel-master       2C+ 60GB + 3网卡 +2GB 以上内存
Fuel-Controller   1C+120GB + 3网卡 +4GB
Fuel-Compute      1C+120GB + 3网卡 +4GB
```

## 加载Fuel镜像并启动Fuel-master

![](../.gitbook/assets/7.jpg)

![](../.gitbook/assets/8.jpg)

![&#x7B49;&#x4E0B;&#x4E0A;&#x4F20;&#x672C;&#x5730;Bootstraps&#x955C;&#x50CF;](../.gitbook/assets/9.jpg)

![&#x4FDD;&#x5B58;&#x5E76;&#x7EE7;&#x7EED;&#x5B89;&#x88C5;](../.gitbook/assets/10.jpg)

![&#x5B89;&#x88C5;&#x6210;&#x529F;&#xFF0C;&#x5927;&#x6982;1&#x4E2A;&#x591A;&#x5C0F;&#x65F6;](../.gitbook/assets/11.jpg)

![&#x767B;&#x5F55;https://10.20.0.2:8443&#x6D4B;&#x8BD5;](../.gitbook/assets/12.jpg)

## 上传本地下载好的Bootstrap镜像

* 上传

```text
使用XFTP软件将本地源文件mirrors和bootstraps传到Fuel-master的/var/www/nailgun, 
具体如下图.也可以自己在PC或者服务器上建立FTP服务器，然后利用wget命令传输文件.
总之不管用什么方法都可以，只要能把文件上传到相应的文件夹内即可.(先把文件备份好)
```

![](../.gitbook/assets/13.jpg)

* 激活镜像： 

```text
（1）fuel-bootstrap list
（2）fuel-bootstrap activated [uuid]
```

![](../.gitbook/assets/14.jpg)

## 启动Fuel-controller和Fuel-compute

* 设置启动顺序为：【网络】启动第一
* 选择：【ubuntu\_bootstrap】启动

## 在Fuel的Web界面配置部署Openstack

![](../.gitbook/assets/15.jpg)

![](../.gitbook/assets/16.jpg)

![](../.gitbook/assets/17.jpg)

![](../.gitbook/assets/18.jpg)

![](../.gitbook/assets/19.jpg)

![](../.gitbook/assets/20.jpg)

* 添加节点：Compute和Controller
* 配置网络，验证网络
* 开始部署

![](../.gitbook/assets/21.jpg)

![](../.gitbook/assets/22.jpg)

![](../.gitbook/assets/23.jpg)

## 登录OpenStack

![](../.gitbook/assets/24.jpg)

  


