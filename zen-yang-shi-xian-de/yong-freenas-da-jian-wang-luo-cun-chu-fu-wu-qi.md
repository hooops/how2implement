# 用FreeNAS搭建网络存储服务器

## 简介

* FreeNAS是一套免费的NAS服务器，它能将一部普通PC变成网络存储服务器。
* 有Web可视化操作界面
* 能提供的服务
* * FTP
  * iSCSI
  * NFS
  * Rsync
  * SMB
  * SSH
  * TFTP
  * 动态DNS

## 下载并安装FreeNAS11的镜像

* [http://www.freenas.org/download-freenas-release/](http://www.freenas.org/download-freenas-release/)
* 安装（内存推荐8G，多块硬盘）

## 访问Web界面

![](../.gitbook/assets/2%20%281%29.PNG)

![](../.gitbook/assets/1.PNG)

## 卷的类型

* **Stripe:** 至少需要一个磁盘
* **Mirror:** 至少需要两个磁盘
* **RAIDZ1:** 至少需要三个磁盘
* **RAIDZ2:** 至少需要四个磁盘
* **RAIDZ3:** 至少需要五个磁盘
* **log device:** 至少需要一个专用设备，建议使用快速，低延迟，电源保护的SSD
* **cache device:** 至少需要一个专用设备，建议使用SSD



