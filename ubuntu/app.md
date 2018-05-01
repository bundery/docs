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

### 任务栏系统监视器

[indicator-sysmonitor](https://github.com/fossfreedom/indicator-sysmonitor) 

```shell
sudo add-apt-repository ppa:fossfreedom/indicator-sysmonitor
sudo apt update
sudo apt install indicator-sysmonitor
```

### mycli
mycli 是 mysql 的命令行工具，具有语法高亮和自动补全功能。`sudo apt install mycli`，可以在`~/myclirc`修改配色。

## Xcompmg

安装 Xcompmg 可以解决 Lxde/Lubuntu 环境下黑框的问题。