# 用Mastodon搭建自己的Twitter

## 简介

* Mastodon是一款‘去中心化’的开源社交软件，类似Twitter和新浪微博
* 可以建立独立节点，也可以跨节点交流

## 机器环境

* CentOS7
* Docker 18.06.1-ce
* Docker-compose 1.22
* Git
* Nginx

## 下载项目代码

```text
cd /home/
git clone https://github.com/tootsuite/mastodon.git live
cd live
chown -R 991:991 public
```

## 编辑 docker-compose.yml

```text
vi docker-compose.yml

# 去掉redis和postgresql的持久化的注释，使其可以持久化数据
```

## 拉取镜像

```text
docker pull tootsuite/mastodon
```

## 配置域名/数据库/邮件系统/管理员账户

```text
docker-compose run --rm web bundle exec rake mastodon:setup

# domain：填自己的域名，这里是192.168.88.104
# 不要使用localhost发邮件，用mailgun的STMP服务
# 记得选保存配置选yes
# 开始初始化数据库选选yes
# 预编译选yes
# 初始化管理员账户选yes
```

## 配置Nginx

* `vi /etc/nginx/conf.d/mysite.conf`
* 只需修改root为代码的目录即可，其它地方都不用改

```text
map $http_upgrade $connection_upgrade {
  default upgrade;
  ''      close;
}

server {
  listen 80;
  listen [::]:80;
  server_name example.com;
  root /home/live/public;
  # Useful for Let's Encrypt
  location /.well-known/acme-challenge/ { allow all; }
  location / { return 301 https://$host$request_uri; }
}

server {
  listen 443 ssl http2;
  listen [::]:443 ssl http2;
  server_name example.com;

  ssl_protocols TLSv1.2;
  ssl_ciphers HIGH:!MEDIUM:!LOW:!aNULL:!NULL:!SHA;
  ssl_prefer_server_ciphers on;
  ssl_session_cache shared:SSL:10m;

  ssl_certificate     /etc/letsencrypt/live/example.com/fullchain.pem;
  ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;

  keepalive_timeout    70;
  sendfile             on;
  client_max_body_size 80m;

  root /home/live/public;

  gzip on;
  gzip_disable "msie6";
  gzip_vary on;
  gzip_proxied any;
  gzip_comp_level 6;
  gzip_buffers 16 8k;
  gzip_http_version 1.1;
  gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

  add_header Strict-Transport-Security "max-age=31536000";

  location / {
    try_files $uri @proxy;
  }

  location ~ ^/(emoji|packs|system/accounts/avatars|system/media_attachments/files) {
    add_header Cache-Control "public, max-age=31536000, immutable";
    try_files $uri @proxy;
  }
  
  location /sw.js {
    add_header Cache-Control "public, max-age=0";
    try_files $uri @proxy;
  }

  location @proxy {
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto https;
    proxy_set_header Proxy "";
    proxy_pass_header Server;

    proxy_pass http://127.0.0.1:3000;
    proxy_buffering off;
    proxy_redirect off;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection $connection_upgrade;

    tcp_nodelay on;
  }

  location /api/v1/streaming {
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto https;
    proxy_set_header Proxy "";

    proxy_pass http://127.0.0.1:4000;
    proxy_buffering off;
    proxy_redirect off;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection $connection_upgrade;

    tcp_nodelay on;
  }

  error_page 500 501 502 503 504 /500.html;
}
```

* 启动Nginx：`nginx  -c  /etc/nginx/nginx.conf`

## 启动Mastodon

```text
cd /home/live
docker-compose up -d
```

* 启动后访问：https://192.168.88.104   \(注意这里为之前设置的域名\)

## 常见问题

* 不能上传图片：
* * 必须保证/live/public/system的所属组是991
  * ```
    chown -R 991:991 /home/live/public/system
    ```

## 效果预览

![](../.gitbook/assets/1%20%281%29.PNG)

