# linux常用软件

## JDK

1. 官网下载 jdk 压缩包，解压的自定义文件夹

2. 设置环境变量 `sudo vim ~/.bashrc`

   ```bash
   # set oracle-jdk1.8.0_162 enviroment
   export JAVA_HOME=/home/jerry/java/jdk1.8.0_162
   export JRE_HOME=${JAVA_HOME}/jre
   export PATH=${JAVA_HOME}/bin:$PATH
   ```

3. 让环境变量立即生效 `source ~/.bashrc`

## Tomcat

tomcat 交由 systemd 管理：

1. 新建 `tomcat用户` 以及 `tomcat组` ，并把 `tomcat文件夹 ` 的 所有者 设置为 tomcat用户和组；

2. 在 tomcat 目录的 bin 文件夹下新建 `setenv.sh` 文件，并添加：

   ```bash
   CATALINA_PID="$CATALINA_BASE/bin/catalina.pid"
   ```

3. 在`/etc/systemd/system/` 下新建 `tomcat.service` ,并添加：

   ```bash
   [Unit]
   Description=Tomcat
   After=network.target

   [Service]
   Type=forking

   Environment=JAVA_HOME=/home/jerry/java/jdk1.8.0_162/jre
   Environment=CATALINA_PID=/home/jerry/java/apache-tomcat-8.5.30/tomcat.pid
   Environment=CATALINA_HOME=/home/jerry/java/apache-tomcat-8.5.30
   Environment=CATALINA_BASE=/home/jerry/java/apache-tomcat-8.5.30
   Environment='CATALINA_OPTS=-Xms128M -Xmx256M -server -XX:+UseParallelGC'
   Environment='JAVA_OPTS=-Djava.awt.headless=true -Djava.security.egd=file:/dev/./

   ExecStart=/home/jerry/java/apache-tomcat-8.5.30/bin/startup.sh
   ExecStop=/home/jerry/java/apache-tomcat-8.5.30/bin/shutdown.sh

   User=tomcat
   Group=tomcat

   [Install]
   WantedBy=multi-user.target
   ```

## mysql

`sudo apt install mysql-server` 安装 mysql 时没有要求输入 `root` 密码，可以先用其他账号登录，然后再修改 root 密码。

```bash
# 这个文件中保存的有系统默认给我们分配的用户名和密码，先利用这个账户登录
cat /etc/mysql/debian.cnf

# 修改root密码（mysql-5.7版本）
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'newPassword';
```

### mycli

mycli 是 mysql 的命令行工具，具有语法高亮和自动补全功能。`sudo apt install mycli`，可以在`~/myclirc`修改配色。

## Xcompmg

安装 Xcompmg 可以解决 Lxde/Lubuntu 环境下黑框的问题。

## monitor

[indicator-sysmonitor](https://github.com/fossfreedom/indicator-sysmonitor) 

## shadowsocks

### 客户端

1. 安装最新版 ***libsodium***（用于支持 chacha20 加密）：`sudo apt install libsodium-dev`

2. 安装最新版 shadowSocks：`sudo pip install https://github.com/shadowsocks/shadowsocks/archive/master.zip -U` （pip 可以直接安装 zip 等含 *py* 安装文件的压缩包 ）

3. 在自定义文件夹创建配置文件：`sudo gedit shadowsocks.json` 

   ```json
   {
     "server":"your_server_ip",
     "server_port":***,
     "local_port":1080,
     "password":"your_server_shadowsocks_password",
     "timeout":600,
     "method":"chacha20-ietf-poly1305"
   }
   ```

   正常情况下输入 `sslocal -c **/shadowsocks.json` 就能启动了。

4. 交由 ***systemd*** 管理，`sudo gedit /etc/systemd/system/shadowsocks.service`，创建文件配置以下内容（`which is sslocal`查看 ***sslocal*** 命令的位置）：

   ```ini
   [Unit]
   Description=Shadowsocks Client Service
   After=network.target
   
   [Service]
   Type=simple
   User=nobody
   Group=nogroup
   ExecStart=/usr/local/bin/sslocal -c /home/**/shadowsocks.json
   
   [Install]
   WantedBy=multi-user.target
   ```

   - 开机自启：`systemctl enable shadowsocks.service`
   - 取消开机自启：`systemctl disable shadowsocks.service`
   - 开启：`systemctl start shadowsocks.service`
   - 停止：`systemctl stop shadowsocks.service`

5. 系统设置网络代理设置 *socks5 127.0.0.1 1080* 代理；或者 chrome 浏览器下载 [proxy switchyomega](https://chrome.google.com/webstore/detail/proxy-switchyomega/padekgcemlokbadohgkifijomclgjgif) 扩展设置用 [white list](https://github.com/breakwa11/gfw_whitelist)

#### 命令行使用代理

可以在 bash 和 zsh 配置文件中配置别名，使用前运行命令（只对当前终端有效）：

```bash
#使用代理
alias setproxy="export ALL_PROXY=socks5://127.0.0.1:1080"
#取消使用代理
alias unsetproxy="unset ALL_PROXY"
```

### 服务端

1. 安装  [shadowsocks-libev](https://github.com/shadowsocks/shadowsocks-libev#debian--ubuntu)

2. 配置 */etc/shadowsocks-libev*  

   ```json
   {
     "server":"0.0.0.0",
     "server_port":443,
     "local_port":1080,
     "password":"password",
     "timeout":600,
     "method":"chacha20-ietf-poly1305"
   }
   ```

3. 交由 ***systemd*** 管理

```ini
[Unit]
Description=Shadowsocks-libev Server Service
Documentation=man:shadowsocks-libev(8)
After=network.target

[Service]
Type=simple
EnvironmentFile=/etc/default/shadowsocks-libev
User=nobody
Group=nogroup
LimitNOFILE=32768
ExecStart=/usr/bin/ss-server -c $CONFFILE $DAEMON_ARGS

[Install]
WantedBy=multi-user.target
```

## NextCloud

1. 安装 nginx

2. 官网下载 [nextcloud](https://download.nextcloud.com/server/releases/nextcloud-13.0.1.zip) 安装包，解压到任意文件夹

   ```shell
   wget https://download.nextcloud.com/server/releases/nextcloud-13.0.1.zip #下载
   sudo unzip nextcloud-13.0.1.zip #解压
   sudo mv nextcloud /var/www/nextcloud #移动文件夹
   sudo chown www-data:www-data /var/www/nextcloud/ -R #修改权限为nginx运行时的用户名和组
   ```

3. 安装 mysql ，创建数据库和用户

   ```shell
   create database nextcloud;
   create user nextclouduser@localhost identified by 'password';
   grant all privileges on nextcloud.* to nextclouduser@localhost identified by 'password';
   flush privileges;
   ```

4. 配置 nginx，在 /etc/nginx/nginx.conf  文件内添加

   ```nginx
   server {
     listen 80;
     server_name localhost;
   
     # Add headers to serve security related headers
     add_header X-Content-Type-Options nosniff;
     add_header X-Frame-Options "SAMEORIGIN";
     add_header X-XSS-Protection "1; mode=block";
     add_header X-Robots-Tag none;
     add_header X-Download-Options noopen;
     add_header X-Permitted-Cross-Domain-Policies none;
   
     # Path to the root of your installation
     root /var/www/nextcloud/;
   
     location = /robots.txt {
       allow all;
       log_not_found off;
       access_log off;
     }
   
     # The following 2 rules are only needed for the user_webfinger app.
     # Uncomment it if you're planning to use this app.
     #rewrite ^/.well-known/host-meta /public.php?service=host-meta last;
     #rewrite ^/.well-known/host-meta.json /public.php?service=host-meta-json
     # last;
   
     location = /.well-known/carddav {
       return 301 $scheme://$host/remote.php/dav;
     }
     location = /.well-known/caldav {
       return 301 $scheme://$host/remote.php/dav;
     }
   
     location ~ /.well-known/acme-challenge {
       allow all;
     }
   
     # set max upload size
     client_max_body_size 512M;
     fastcgi_buffers 64 4K;
   
     # Disable gzip to avoid the removal of the ETag header
     gzip off;
   
     # Uncomment if your server is build with the ngx_pagespeed module
     # This module is currently not supported.
     #pagespeed off;
   
     error_page 403 /core/templates/403.php;
     error_page 404 /core/templates/404.php;
   
     location / {
       rewrite ^ /index.php$uri;
     }
   
     location ~ ^/(?:build|tests|config|lib|3rdparty|templates|data)/ {
       deny all;
     }
     location ~ ^/(?:\.|autotest|occ|issue|indie|db_|console) {
       deny all;
     }
   
     location ~ ^/(?:index|remote|public|cron|core/ajax/update|status|ocs/v[12]|updater/.+|ocs-provider/.+|core/templates/40[34])\.php(?:$|/) {
       include fastcgi_params;
       fastcgi_split_path_info ^(.+\.php)(/.*)$;
       fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
       fastcgi_param PATH_INFO $fastcgi_path_info;
       #Avoid sending the security headers twice
       fastcgi_param modHeadersAvailable true;
       fastcgi_param front_controller_active true;
       fastcgi_pass unix:/run/php/php7.0-fpm.sock;
       fastcgi_intercept_errors on;
       fastcgi_request_buffering off;
     }
   
     location ~ ^/(?:updater|ocs-provider)(?:$|/) {
       try_files $uri/ =404;
       index index.php;
     }
   
     # Adding the cache control header for js and css files
     # Make sure it is BELOW the PHP block
     location ~* \.(?:css|js)$ {
       try_files $uri /index.php$uri$is_args$args;
       add_header Cache-Control "public, max-age=7200";
       # Add headers to serve security related headers (It is intended to
       # have those duplicated to the ones above)
       add_header X-Content-Type-Options nosniff;
       add_header X-Frame-Options "SAMEORIGIN";
       add_header X-XSS-Protection "1; mode=block";
       add_header X-Robots-Tag none;
       add_header X-Download-Options noopen;
       add_header X-Permitted-Cross-Domain-Policies none;
       # Optional: Don't log access to assets
       access_log off;
     }
   
     location ~* \.(?:svg|gif|png|html|ttf|woff|ico|jpg|jpeg)$ {
       try_files $uri /index.php$uri$is_args$args;
       # Optional: Don't log access to other assets
       access_log off;
     }
   }
   ```