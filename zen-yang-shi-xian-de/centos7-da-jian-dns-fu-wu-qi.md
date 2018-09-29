# CentOS7搭建DNS服务器

## \# 原理

* 使用开源的Bind服务来配置一个DNS服务器

## 机器环境

* CentOS7

## 安装Bind服务

*  `yum -y install bind*`

## 配置/etc/named.conf

* 修改如下

```text
options {
        listen-on port 53 { any; };   
        listen-on-v6 port 53 { ::1; };
        directory       "/var/named";
        dump-file       "/var/named/data/cache_dump.db";
        statistics-file "/var/named/data/named_stats.txt";
        memstatistics-file "/var/named/data/named_mem_stats.txt";
        allow-query     { any; };

        /*
         - If you are building an AUTHORITATIVE DNS server, do NOT enable recursion.
         - If you are building a RECURSIVE (caching) DNS server, you need to enable
           recursion.
         - If your recursive DNS server has a public IP address, you MUST enable access
           control to limit queries to your legitimate users. Failing to do so will
           cause your server to become part of large scale DNS amplification
           attacks. Implementing BCP38 within your network would greatly
           reduce such attack surface
        */
        recursion yes;

        dnssec-enable yes;
        dnssec-validation yes;

        /* Path to ISC DLV key */
        bindkeys-file "/etc/named.iscdlv.key";

        managed-keys-directory "/var/named/dynamic";

        pid-file "/run/named/named.pid";
        session-keyfile "/run/named/session.key";
};

logging {
        channel default_debug {
                file "data/named.run";
                severity dynamic;
        };
};

zone "." IN {
        type hint;
        file "named.ca";
};

include "/etc/named.rfc1912.zones";
include "/etc/named.root.key";
```

## 修改/etc/named.rfc1912.zones添加DNS正反向解析规则

```text
// 正向解析
zone "ns2250225.com" IN {
        type master;
        file "ns2250225.com.zone";
        allow-update { none; };
};

// 反向解析
zone "14.168.192.in-addr.arpa" IN {
        type master;
        file "192.168.14.zone";
        allow-update { none; };
};
```

## 创建对应的配置文件

* 创建/var/named/ns2250225.com.zone

```text
$TTL 3600
@     IN SOA ns.ns2250225.com. admin.ns2250225.com. (4 3600 3600 7200 7200)
      IN NS ns
ns    IN A  192.168.14.250
www   IN A  192.168.14.251
mail  IN A  192.168.14.252
```

* 创建/var/named/192.168.14.zone

```text
$TTL 3600
@       IN      SOA     ns.ns2250225.com. admin.ns2250225.com. (4 3600 3600 7200 7200)
        IN      NS      ns.ns2250225.com.
250     IN      PTR     ns.ns2250225.com.
251     IN      PTR     www.ns2250225.com.
252     IN      PTR     mail.ns2250225.com.
```

* 修改文件权限：`chmod 777 ns2250225.com.zone  192.168.14.zone`

## 重启Bind服务

```text
systemctl restart named.service
```

## 测试正向解析

```text
nslookup www.ns2250225.com 192.168.14.250
```

![](../.gitbook/assets/1%20%283%29.png)

## 测试反向解析

```text
nslookup 192.168.14.251 192.168.14.250
```

![](../.gitbook/assets/2%20%282%29.png)

## 附录

### **SOA记录**

```text
domain.com.  IN SOA ns1.domain.com. admin.domain.com. (
                                        12083   ; serial number
                                        3h      ; refresh interval
                                        30m     ; retry interval
                                        3w      ; exiry period
                                        1h      ; negative TTL
)
```



* domain.com.: 此处为区域的root所在，负责指定该区域文件指向domain.com域名。一般来讲，大家会在实际文件中看到这部分被@所取代，其属于占位符、指代此前$ORIGIN变量设定的内容。
* IN SOA: 这里的“IN”部分代表着互联网（多数记录中都包含这部分）。而SOA代表这是一条起始授权记录。
* ns1.domain.com.: 此处定义该域名的第一主命名服务器。命名服务器可为主服务器或者从服务器，而且如果动态DNS在配置中要求一台服务器必须为“第一主服务器”，那么就是在这里进行体现。如果大家还没有配置动态DNS，则其默认要求服务器为主命名服务器。
* admin.domain.com.: 这里为此区域的管理员邮箱地址。其中的“@”被替换为“.”。如果该邮箱地址原本就使用“.”，则使用“\”替换“.”（your.name@domain.com转换为your\name.domain.com）。
* 12083: 此处为该区域文件的序列号。每当编辑一个区域文件时，大家都必须增加该数字以实现正确广播。从服务器会检查主服务器的区域序列号以确定后者的数字更大。如果数字确实更大，则请求该新区域文件; 如果数字并非更大，则继续使用原始文件。
* 3h: 此处为该区域的刷新间隔，负责告知从服务器等待多久才对主服务器的区域文件变更进行一次检查。
* 30m: 此处为该区域的征订间隔。如果从服务器在刷新时无法接入主服务器，则其会等待这里设定的时长并再次重试。
* 3w: 此处为到期期限。如果一台从命名服务器在此期间始终无法接入主服务器，则其不再作为该区域的授权响应源。
* 1h: 此处为命名服务器在无法从文件内找到所请求域名时，缓存一条命名错误的时长。

**A与AAAA记录**

```text
这两类记录都负责将一台主机映射至一个IP地址
其中的“A”记录用于将一台主机映射至一个IPv4 IP地址
而“AAAA”记录则用于将主机映射至IPv6 IP地址

如：
    ns1.domain.com.     IN  A       111.222.111.222
```

**CNAME记录**

```text
CNAME记录负责为我们的服务器的规范名称定义一条别名
举例来说，我们可以使用一条A命名记录定义“server1”主机，而后将“www”作为该主机的别名：

server1     IN  A       111.111.111.111
www         IN  CNAME   server1
```

**MX记录**

```text
MX记录用于定义该域名所使用的邮件交换方式，其能够帮助我们的邮件信息能够正确抵达邮件服务器

MX记录一般指向由A或者AAAA记录定义的主机，而非由CNAME定义的主机
因此，假设我们有两台邮件服务器。其记录内容应如下所示：

        IN  MX  10  mail1.domain.com.
        IN  MX  50  mail2.domain.com.
mail1   IN  A       111.111.111.111
mail2   IN  A       222.222.222.222

在本示例中，“mail1”主机为首选邮件交换服务器
```

**NS记录**

```text
此记录类型用于定义该区域所使用的命名服务器

与MX记录类似，NS记录包含三条全区域参数，因此其同样不需要填写主机。一般来讲，其内容应如下所示：

    IN  NS     ns1.domain.com.
    IN  NS     ns2.domain.com.
```

**PTR记录**

```text
PTR记录用于将一个名称关联至一个IP地址, PTR记录属于A或者AAA记录的倒数

下面来看111.222.333.444的PTR记录示例：

444.333.222.111.in-addr.arpa.   33692   IN  PTR host.example.com.
```

