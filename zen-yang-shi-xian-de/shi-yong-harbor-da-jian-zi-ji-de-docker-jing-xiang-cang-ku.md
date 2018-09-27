# 使用Harbor搭建自己的docker镜像仓库

## 机器环境

* CentOS7

## 安装docker和docker-compose

* 安装docker：`yum install docker`
* 安装docker-compose：

```text
curl -L https://github.com/docker/compose/releases/download/1.13.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

* 修改docker配置

```text
$ vim /etc/docker/daemon.json 
# 添加该行，ip为服务器ip
{ "insecure-registries":["10.68.7.20"] }   
```

* 重启docker服务：`systemctl restart docker`

## 安装Harbor

```text
# 下载压缩包
wget https://github.com/vmware/harbor/releases/download/v1.1.2/harbor-online-installer-v1.1.2.tgz
tar xvf harbor-online-installer-v1.1.2.tgz
cd harbor

# 编辑配置文件
vim harbor.cfg 

# hostname设置访问地址，可以使用ip、域名，不可以设置为127.0.0.1或localhost。
hostname = 10.68.7.20

# 访问协议，默认是http，也可以设置https，如果设置https，则nginx ssl需要设置on
ui_url_protocol = http

# mysql数据库root用户默认密码root123，实际使用时修改下
db_password = root123

max_job_workers = 3 
customize_crt = on
ssl_cert = /data/cert/server.crt
ssl_cert_key = /data/cert/server.key
secretkey_path = /data
admiral_url = NA

# 邮件设置，发送重置密码邮件时使用
email_identity = 
email_server = smtp.mydomain.com
email_server_port = 25
email_username = sample_admin@mydomain.com
email_password = abc
email_from = admin <sample_admin@mydomain.com>
email_ssl = false

# 启动Harbor后，管理员UI登录的密码，默认是Harbor12345
harbor_admin_password = Harbor12345

# 认证方式，这里支持多种认证方式，如LADP、本次存储、数据库认证。默认是db_auth，mysql数据库认证
auth_mode = db_auth

# LDAP认证时配置项
#ldap_url = ldaps://ldap.mydomain.com
#ldap_searchdn = uid=searchuser,ou=people,dc=mydomain,dc=com
#ldap_search_pwd = password
#ldap_basedn = ou=people,dc=mydomain,dc=com
#ldap_filter = (objectClass=person)
#ldap_uid = uid 
#ldap_scope = 3 
#ldap_timeout = 5

# 是否开启自注册
self_registration = on

# Token有效时间，默认30分钟
token_expiration = 30

# 用户创建项目权限控制，默认是everyone（所有人），也可以设置为adminonly（只能管理员）
project_creation_restriction = everyone

verify_remote_cert = on
```

## 启动Harbor

* ```
  ./install.sh
  ```

```text
✔ ----Harbor has been installed and started successfully.----

Now you should be able to visit the admin portal at http://10.68.7.20	. 
For more details, please visit https://github.com/vmware/harbor .
```

## 上传自己的镜像

```text
# 下载镜像
docker pull nginx

# 修改镜像的名字
docker tag nginx 10.68.7.20/library/nginx:latest

# 登录
docker login 10.68.7.20
Username (admin): admin
Password: 
Login Succeeded

# 推送镜像到Harbor仓库
docker push 10.68.7.20/library/nginx 
```



