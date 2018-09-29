# 搭建团队文档协作平台（OnlyOffice）

## 机器环境

* CentOS7
* Docker

## 搭建OnlyOffice

```text
# 创建网桥
docker network create --driver bridge onlyoffice

# 启动onlyOffice document server
docker run --net onlyoffice --privileged -i -t -d --restart=always \
 --name onlyoffice-document-server \
 -v /app/onlyoffice/DocumentServer/data:/var/www/onlyoffice/Data \
 -v /app/onlyoffice/DocumentServer/logs:/var/log/onlyoffice \
 -v /app/onlyoffice/DocumentServer/lib:/var/lib/onlyoffice \
 onlyoffice/documentserver
 
 # 启动onlyOffice community server
 docker run --net onlyoffice --privileged -i -t -d --restart=always \
  --name onlyoffice-community-server -p 80:80 -p 5222:5222 -p 443:443 \
  -v /app/onlyoffice/CommunityServer/data:/var/www/onlyoffice/Data \
  -v /app/onlyoffice/CommunityServer/mysql:/var/lib/mysql \
  -v /app/onlyoffice/CommunityServer/logs:/var/log/onlyoffice \
  -v /app/onlyoffice/DocumentServer/data:/var/www/onlyoffice/DocumentServerData \
  -e DOCUMENT_SERVER_PORT_80_TCP_ADDR=onlyoffice-document-server \
 onlyoffice/communityserver
```

## 访问Web界面进行操作

* http://server\_ip

![](../.gitbook/assets/1.png)

![](../.gitbook/assets/2%20%284%29.png)

![](../.gitbook/assets/3%20%281%29.png)

![word](../.gitbook/assets/4.png)

![excel](../.gitbook/assets/5.png)

![ppt](../.gitbook/assets/6%20%281%29.png)



