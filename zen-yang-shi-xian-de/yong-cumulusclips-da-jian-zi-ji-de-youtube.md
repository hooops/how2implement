# 用Cumulusclips搭建自己的YouTube

## 简介

* **cumulusclips是一款开源的视频分享软件，类似youtube，支持Web端，手机端访问**

## 机器环境

* CentOS7
* Docker

## 搭建C**umulusclips**

```text
# 启动Mysql
docker run --name cumulusclips-mysql -e MYSQL_ROOT_PASSWORD=mysecretpassword  -e MYSQL_DATABASE=cumulusclipsdb \
-e MYSQL_USER=cumulusclipsuser -e MYSQL_PASSWORD=cumulusclipsdbpasswd -d mysql:5.7

# 启动Cumulusclips
docker run -d -p 8080:80 --link cumulusclips-mysql:db quantumobject/docker-cumulusclips
```

* 访问`http://server_ip:8080`
* 初始化数据库
* * host：填db
  * database：填 cumulusclipsdb
  * database user : 填cumulusclipsuser
  * database password : 填cumulusclipsdbpasswd

## 上传视频

* 注意视频编码为H.264，不然会转码很久
* 上传后，手机端也可以访问

