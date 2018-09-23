# Ceph集群的搭建与运维

## Ceph的存储过程

> 文件-&gt;obj-&gt;pool-&gt;pg-&gt;osd-&gt;disk

* 文件被分片成对象
* 对象存放于特定的pool
* pool由多个pg组成
* pg对应多个osd
* osd直接对应disk

## 机器环境

* 4台centos7机器
* 默认最小集群是一个mon节点，两个osd节点

```text
admin             10.37.129.10
ceph-node1        10.37.129.11
ceph-node2        10.37.129.12
ceph-node3        10.37.129.13
```

## 设置ssh免密码登陆

* 各节点修改/etc/hosts文件

```text
admin             10.37.129.10
ceph-node1        10.37.129.11
ceph-node2        10.37.129.12
ceph-node3        10.37.129.13
```

* 在admin节点生成密钥对并派发

```text
# 在admin节点

ssh-keygen -t rsa 

ssh-copy-id  root@ceph-node1
ssh-copy-id  root@ceph-node2
ssh-copy-id  root@ceph-node3
```

## 各节点环境配置

* 各节点关闭防火墙

```text
sudo systemctl stop firewalld.service     #停止 firewall
sudo systemctl disable firewalld.service  #禁止 firewall 开机启动
```

* 各节点关闭selinux

```text
vi /etc/selinux/config
SELINUX=disabled
reboot
```

* 各节点安装时间同步工具

```text
yum install chrony -y 
systemctl restart  chronyd
systemctl enable  chronyd
chronyc source -v （查看时间是否同步，*表示同步完成）
```

* 各节点配置yum源

```text
vi  /etc/yum.repo.d/ceph.repo

[Ceph]
name=Ceph packages for $basearch
baseurl=http://mirrors.163.com/ceph/rpm-kraken/el7/$basearch
enabled=1
priority=1
gpgcheck=1
gpgkey=https://download.ceph.com/keys/release.asc

[Ceph-noarch]
name=Ceph noarch packages
baseurl=http://mirrors.163.com/ceph/rpm-kraken/el7/noarch
enabled=1
priority=1
gpgcheck=1
gpgkey=https://download.ceph.com/keys/release.asc

[ceph-source]
name=Ceph source packages
baseurl=http://mirrors.163.com/ceph/rpm-kraken/el7/SRPMS
enabled=0
priority=1
gpgcheck=1
gpgkey=https://download.ceph.com/keys/release.asc
```

## 在admin节点部署

* 在admin节点安装部署工具

```text
yum -y install ceph-deploy 
```

* 创建部署文件目录

```text
mkdir -p /etc/ceph
cd /etc/ceph
```

* 创建一个集群（创建mon节点）

```text
ceph-deploy new ceph-node1 ceph-node2  （也就是定义2各mon节点）
```

* 配置ceph.conf

```text
vi ceph.conf
加入：
public network =10.37.129.0/24
```

* 开始部署

```text
ceph-deploy install admin ceph-node1 ceph-node2  ceph-node3
```

* 初始化 mon节点 并收集所有的秘钥

```text
ceph-deploy mon create-initial
```

* 创建osd

```text
ceph-deploy osd  create ceph-node1:/dev/sdb   ceph-node2:/dev/sdb
```

* 使用ceph-deploy把配置文件和admin秘钥推送到管理节点和ceph节点

```text
ceph-deploy admin admin ceph-node1 ceph-node2 ceph-node3
```

* 给每一个节点的keyring 增加 r 权限（各节点执行）

```text
chmod +r /ect/ceph/ceph.client.admin.keyring 
```

* 检查集群状况

```text
ceph health
```

## 新增一个osd节点和mon节点

* 在admin节点操作

```text
1、新增一个osd ceph-node3 
ceph-deploy osd create ceph-node3:/dev/sdb

2、在ceph-node1增加一个元数据的角色 
ceph-deploy mds create ceph-node1 

3、增加一个新的mon在ceph-node3 上面
ceph-deploy mon add ceph-node3
```

## 误删osd恢复流程

* 记录osd状态：ceph osd tree

```text
ID WEIGHT  TYPE NAME           UP/DOWN REWEIGHT PRIMARY-AFFINITY 
-1 0.01469 root default                                          
-2 0.00490     host ceph-node1                                   
 0 0.00490         osd.0            up  1.00000          1.00000 
-3 0.00490     host ceph-node2                                   
 1 0.00490         osd.1            up  1.00000          1.00000 
-4 0.00490     host ceph-node3                                   
 2 0.00490         osd.2            up  1.00000          1.00000 
```

* 模拟删除osd.2

```text
ceph osd out osd.2
systemctl stop ceph-osd@2 (osd id)
ceph osd crush remove osd.2
ceph auth del osd.2
ceph osd rm  osd.2
```

* 在删除的osd节点上进行恢复操作

```text
进入到其挂载的目录 例如
cd /var/lib/ceph/osd/ceph-1

more fsid
ceph osd create  uuid
ceph auth add osd.2 osd 'allow *' mon 'allow rwx' -i /var/lib/ceph/osd/ceph-1/keyring
ceph osd crush add 2 osd.2(osd id） 0.0049 (权重） host=ceph-node3
ceph osd in osd.2
systemctl start  ceph-osd@2
```



