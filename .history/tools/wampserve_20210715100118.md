# WAMPServe

## 多站点多目录配置

`httpd.conf` 文件修改: `\wamp\bin\apache\apache2.4.9\conf\httpd.conf`
 
查找到 `#Include conf/extra/httpd-vhosts.conf` 去掉 `#`

`httpd-vhosts.conf` 文件修改: 路径 `\wamp\bin\apache\Apache2.4.9\conf\extra\httpd-vhosts.conf` 添加如下代码

`DocumentRoot`：项目路径

`ServerName`：服务器名称，写成域名，方便区分

`Directory`：项目路径，与 `DocumentRoot` 一致

``` bash
<VirtualHost *:80>   
    DocumentRoot "D:/wamp/www/test"   
    ServerName test.cn   
    <Directory "D:/wamp/www/test">
        Options Indexes FollowSymLinks
        AllowOverride All
        Order allow,deny
        Allow from all
    </Directory>
</VirtualHost>
```

添加本地解析: `hosts`文件修改，`Windows\System32\drivers\etc\hosts` 添加如下内容

``` bash
127.0.0.1     test.cn
```