# 浅谈DNS服务
关于DNS服务器  我们在这里用到的工具是` bind `
#### 下载
```bash
[root@localhost ~]# yum install bind bind-utils
```

要使用 `bind` 工具来提供 DNS 服务  需要在 ` resolv.conf ` 文件内添加一行设定DNS服务器

```bash
nameserver 127.0.0.1
```
 然后重启服务 应用更改

```bash
systemctl restart named
```
#### 简单配置--添加解析
主配置文件` named.conf `在 `/etc` 下
我们在这里添加一个记录 使用权威域名 ` rj.com `

```bash
zone "rj.com" IN {
        type master;
        file "rj.com.zone";
};
```

到 ` /var/named `文件夹下 书写 zone 文件

```bash
# vim /var/named/rj.com.zone
$TTL 1D
@        SOA     dns.rj.com.  root (
                                        0       ; serial
                                        1D      ; refresh
                                        1H      ; retry
                                        1W      ; expire
                                        3H )    ; minimum
@       NS      dns.rj.com.
dns.rj.com.   A       192.168.72.132
www.rj.com.   A       192.168.72.132
ftp.rj.com.   A       192.168.72.132
```

进行测试

``` bash
# host www.rj.com
    www.rj.com has address 192.168.72.132
```