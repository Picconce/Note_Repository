# 浅谈HTTP服务
---

根据 Archwiki 的描述，Apache 配置文件位于 `/etc/httpd/conf` 里，其中包含其他配置文件  对于简单的设置 默认的配置文件完全足够
###### 安装和简单配配置
```bash
# blog➜ ~ ᐅ   yum install httpd -y
```
现在就可以使用默认的配置进行访问了
    # blog➜  ~  ᐅ  curl 192.168.72.130
` curl ` 后面跟服务器 IP 地址

要想在同网络内其他的机器上访问。需要调整防火墙，我们直接
  关闭

```bash
# blog➜  ~  ᐅ  systemctl stop firewalld
# blog➜  ~  ᐅ  systemctl disable firewalld
```
在配置主目录中 存在以下文件

```bash
# blog➜  httpd  ᐅ  ll
total 0
drwxr-xr-x. 2 root root  37 May 18 02:34 conf
drwxr-xr-x. 2 root root  82 May 18 02:28 conf.d
drwxr-xr-x. 2 root root 146 May 18 02:28 conf.modules.d
lrwxrwxrwx. 1 root root  19 May 18 02:28 logs -> ../../var/log/httpd
lrwxrwxrwx. 1 root root  29 May 18 02:28 modules -> ../../usr/lib64/httpd/modules
lrwxrwxrwx. 1 root root  10 May 18 02:28 run -> /run/httpd
```
其中 ` logs ` 和 ` modules ` 文件夹是链接方式提供

查看文件内生效内容

    ```bash
# blog➜  conf  ᐅ  cat httpd.conf |grep -v ‘^#’
    ```

更改默认页面内容  在 ` /var/www/html `文件夹内新建 ` index.html `
使用命令检测语法错误

```bash
[~] httpd -t                                                                                          
Syntax OK
```
---
###### 关于HTTPS访问

安装`mod_ssl`

```bash
# blog➜ yum install mod_ssl -y
```

创建一个私钥并自己签名认证

```bash
 # blog➜ /etc/httpd/conf # openssl req -new  -x509 -nodes -newkey  rsa:4096 -keyout server.key -out server.crt -days 1095
```

修改配置文件  取消 `DocumentRoot`一行的注释

    ```bash
 # blog➜ vim /etc/httpd/conf.d/ssl.cof
    ```

重启服务  访问测试

---

###### 关于虚拟主机
对于 Apache 而言 主机有两类 ( 中心主机和虚拟主机 ) ，不能同时使用 如果要定义虚拟主机 那就要为现存的主机创建一个虚拟主机

1. 中心主机 ( ` Mainhost ` ) 未配置虚拟主机时 例如上文写道简单配置结束
2. 虚拟主机  ( ` Vhost ` ) 如果配置完了中心主机，同时也想配置虚拟主机，那么中心主机配置成虚拟主机

在进行虚拟主机设置的时候 可以选择使用配置文件的方式  放置在 ` /etc/httpd/conf.d ` 文件夹下
书写规范如下


```bash
  [~] cat /etc/httpd/conf.d/virthost.conf             
  <VirtualHost * :80>
  	ServerAdmin codezhujr@gmail.com
  	DocumentRoot /data/web_data
  	ServerName www.rj.com
  	<Directory "/data/web_data">
  		Options None
  		AllowOverride None
  		Require all granted
  	</Directory>
  </VirtualHost>
```

`ServerAdmin`  项 - - - - 写网站管理员的电子邮件地址，在错误页面会展示给用户

`DocumentRoot`  项 - - - - 网站的主目录
事实上  完整的配置应该包括`ErrorLog`、 `CustomLog`、`<Directory>`等项

ArchWiki给出的基础的配置如下：

```bash
<VirtualHost * :80>
    ServerAdmin webmaster@domainname1.dom
    DocumentRoot "/home/user/http/domainname1.dom"
    ServerName domainname1.dom
    ServerAlias domainname1.dom
    ErrorLog "/var/log/httpd/domainname1.dom-error_log"
    CustomLog "/var/log/httpd/domainname1.dom-access_log" common

    <Directory "/home/user/http/domainname1.dom">
        Require all granted
    </Directory>
</VirtualHost>
```
