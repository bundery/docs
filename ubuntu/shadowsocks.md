## 客户端

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
   WantedBy=multi-user.targetet
   ```

   - 开机自启：`systemctl enable shadowsocks.service`
   - 取消开机自启：`systemctl disable shadowsocks.service`
   - 开启：`systemctl start shadowsocks.service`
   - 停止：`systemctl stop shadowsocks.service`

5. 系统设置网络代理设置 *socks5 127.0.0.1 1080* 代理；或者 chrome 浏览器下载 [proxy switchyomega](https://chrome.google.com/webstore/detail/proxy-switchyomega/padekgcemlokbadohgkifijomclgjgif) 扩展设置用 [white list](https://github.com/breakwa11/gfw_whitelist)

## 服务端

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

