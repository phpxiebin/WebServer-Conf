# WebServer-Conf
各Web服务器前后端分离转发配置教程

### 一、Apache版本配置文件
* 以下配置需开启mod_proxy、mod_proxy_http扩展

> httpd-vhosts.conf文件示例如下：
    
    # 后端站点虚拟机
    <VirtualHost *:80>
        DocumentRoot "后端代码地址"
        ServerName 后端域名
        ErrorLog "logs/后端域名-error_log"
        CustomLog "logs/后端域名-access_log" common
        <Directory "后端代码地址">
            Options Indexes FollowSymLinks
            AllowOverride all
            Order deny,allow
            Allow from all
        </Directory>
    </VirtualHost>

    # 前端站点虚拟机
    <VirtualHost *:80>
        DocumentRoot "前端代码地址"
        ServerName 前端域名
        ErrorLog "logs/前端域名-error_log"
        CustomLog "logs/前端域名-access_log" common
        <Directory "前端代码地址">
            Options Indexes FollowSymLinks
            AllowOverride all
            Order deny,allow
            Allow from all
        </Directory>

        # 转发到后端 (下面举个栗子)
        ProxyPass  /auth  后端域名/auth
    </VirtualHost>

### 二、Nginx版本配置文件
> nginx.conf文件示例如下：
 
    server {
        listen 80;
        server_name 前端域名; 

        # 前端配置
        location / {
            root 前端项目路径;
            # 默认缺省文件
            index index.html index.htm;
        }
        # 转发到后端 (下面举个栗子)
        location /auth {
            proxy_pass         后端域名/auth;
            proxy_set_header   Host             $host;
            proxy_set_header   X-Real-IP        $remote_addr; 
            proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
        }
    }
