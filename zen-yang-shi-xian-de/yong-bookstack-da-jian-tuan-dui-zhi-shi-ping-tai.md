# 用bookStack搭建团队知识平台

## 简介

* bookStack是一款开源的知识储备分享平台，类似wiki

## 机器环境

* CentOS7

## 安装Docker和Compose

* 安装Docker

```text
yum install -y docker git
```

* 安装docker-compose

```text
sudo curl -L "https://github.com/docker/compose/releases/download/1.22.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version
```

## 克隆并运行项目

```text
git clone https://github.com/solidnerd/docker-bookstack.git
cd docker-bookstack
docker-compose up
```

## 访问Web界面

* `http://server_ip:8080`

![](../.gitbook/assets/1%20%281%29.PNG)

![](../.gitbook/assets/2%20%281%29.PNG)

