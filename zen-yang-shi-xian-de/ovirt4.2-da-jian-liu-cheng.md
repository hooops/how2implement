# Ovirt4.2搭建流程

## 简介

> Ovirt是红帽子公司开源的虚拟机管理平台，类似ESXI，Proxmox，Openstack

## 主机规划

* 2台 CentOS7 机器

```text
192.168.88.101 ovirt-engine
192.168.88.102 ovirt-node1
```

## 安装Ovirt-engine（管理界面）

* 在`192.168.88.101 ovirt-engine 上操作`

```text
yum install http://resources.ovirt.org/pub/yum-repo/ovirt-release42.rpm
yum update
reboot
yum -y install ovirt-engine（半个小时左右）

engine-setup（全部选默认）

vi /etc/ovirt-engine/engine.conf.d/xxx-sso.conf
添加：
SSO_CALLBACK_PREFIX_CHECK=false

service ovirt-engine restart （重启服务）
```

## 登陆Web界面添加主机节点

* 访问：https://`192.168.88.101`
* 添加主机：点击【计算】-&gt;【主机】-&gt;【新建】
* 填写 IP 和 密码，点击确定等待自动安装节点主机



