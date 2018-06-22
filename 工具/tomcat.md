# Tomcat

一个 tomcat 容器中可以放置多个网站（虚拟主机），1个网站可以有多个 Web应用，在 tomcat 中一个 `Host` 代表一个网站，一个 `Context` 代表一个 Web 应用。

> 比如 https://www.googlecom
>
> - https：代表协议；
> - sina.com 代表域名，购买的时候一般买的是域名；
> - www.sina.com：代表主机名，一个域名下面可以搭载多台虚拟主机（网站），如：www.google.com 和 mail.google.com。

## server.xml

server.xml 配置的是 Tomcat 的启动信息，在此文件中配置端口、Host、Context。

### 配置 Web 应用

1. 在 `$CATALINA_HOME/conf/server.xml` 文件中添加 ：

   ```xml
   <Host  name="www.test.com" appBase="/home/jerry" autoDeploy="true" unpackWARs="true">
     <Context path="/image" docBase="/home/jerry/litin-image" reloadable="true"></Context>
   </Host>
   ```

   输入 www.test.com:port/image 就可以访问 `/home/jerry/litin-image` 下的文件了。这种配置方式需要重启 tomcat 才能生效。

   如果想要 www.test.com:port 就可以访问，需要配置为 `<Context path="" docBase="/home/jerry/litin-image" reloadable="true"></Context>` 。

   !> 需要把 www.test.com 添加到 hosts 中，映射为本机 ip。

2. 在 `$CATALINA_HOME/conf/Catalina/localhost` 中新建一个 xml 文件，如 image.xml ，然后在文件中添加：

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <Context docBase="/home/jerry/litin-image" reloadable="true" />
   ```

   输入 `localhost:port/image` 就可以访问 `/home/jerry/litin-image` 下的文件了；这种方法不需要重启 tomcat ，所以推荐此方法。如果想通过 `localhost:port` 就能访问，需要新建一个 `ROOT.xml` ，并且删除 `webapps/ROOT` 文件夹。

   ​