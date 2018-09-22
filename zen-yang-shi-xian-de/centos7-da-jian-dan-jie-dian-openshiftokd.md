# Centos7搭建单节点OpenShift\(OKD\)

## 机器环境

* CPU:2   MEM:6G   DISK:100G
* CentOS7

## 安装Docker\(&gt;1.22\)

* `yum install docker`

## 配置Docker

* vi  /etc/docker/daemon.json

```text
{
   "insecure-registries": [
     "172.30.0.0/16"
   ]
}
```

## 重启Docker

```text
$ sudo systemctl daemon-reload
$ sudo systemctl restart docker
```

## 配置网络

*  `sysctl net.ipv4.ip_forward=1`
* docker network inspect -f "{{range .IPAM.Config }}{{ .Subnet }}{{end}}" bridge

## 配置防火墙开放端口（**切勿关闭防火墙**）

```text
firewall-cmd --permanent --new-zone dockerc
firewall-cmd --permanent --zone dockerc --add-source 172.17.0.0/16
firewall-cmd --permanent --zone dockerc --add-port 8443/tcp
firewall-cmd --permanent --zone dockerc --add-port 53/udp
firewall-cmd --permanent --zone dockerc --add-port 8053/udp

firewall-cmd --permanent --zone=public --add-port=8443/tcp 

firewall-cmd --reload
```

## 下载安装工具OC

* [https://www.okd.io/download.html\#oc-platforms](https://www.okd.io/download.html#oc-platforms)
* 这里下载的是： [v3.10.0-dd10d17-linux-64bit.tar.gz](https://github.com/openshift/origin/releases/download/v3.10.0/openshift-origin-server-v3.10.0-dd10d17-linux-64bit.tar.gz)
* 上传，解压
* 把oc文件放到/usr/local/bin/里面：`cp oc /usr/local/bin/`

## 开始安装

* ```
  oc cluster up --public-hostname=192.168.88.104
  ```
* `--public-hostname是服务器的IP，用来供外网访问`

## 安装完成

```text
OpenShift server started.

The server is accessible via web console at:
    https://192.168.88.104:8443

You are logged in as:
    User:     developer
    Password: <any value>

To login as administrator:
    oc login -u system:admin
```

* 外网访问web界面：

  ```text
  https://192.168.88.104:8443
  ```



