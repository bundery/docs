# ubuntu app

## 有道词典命令行

[有道词典](https://github.com/longcw/youdao)

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


## eclipse
下载 eclipse 压缩包，解压之后在`eclipse.ini`的`-vm`下行添加java的路径`.../jdk1.8.0_144/jre/bin/java`。

自动提示：在 java/editor/content assist 里添加`.(abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ_`；然后把修改好的jar包`org.eclipse.jface.text_3.12.0.v20170523-1043.jar`替换到 eclipse/plugins 里。

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

### 任务栏系统监视器

[indicator-sysmonitor](https://github.com/fossfreedom/indicator-sysmonitor) 

```shell
sudo add-apt-repository ppa:fossfreedom/indicator-sysmonitor
sudo apt update
sudo apt install indicator-sysmonitor
```

## 