# ShadowsocksR 多用户版安装教程 #

以下命令均以root用户执行，或sudo方式执行

### 基本库安装 ###
centos： 
```
yum install python-setuptools && easy_install pip
yum install git
```
ubuntu/debian： 
```
apt-get install python-pip
apt-get install git
```

### 获取源代码 ###
`git clone -b manyuser https://github.com/breakwa11/shadowsocks.git`

执行完毕后此目录会新建一个shadowsocks目录，其中根目录的是多用户版（即数据库版），子目录中的是单用户版。

根目录即 ./shadowsocks

子目录即 ./shadowsocks/shadowsocks 


### 服务端配置 ###
shadowsocks目录内，把apiconfig.py复制为userapiconfig.py后，对userapiconfig.py里以上内容进行相应修改： 
```
API_INTERFACE = 'mudbjson' //修改接口类型
```

文件config.json复制一份到user-config.json，然后编辑： 
```
"method":"aes-256-cfb",                   //修改成您要的加密方式的名称
"protocol": "auth_sha1_compatible",       //修改成您要的协议插件名称
"obfs": "tls1.0_session_auth_compatible", //修改成您要的混淆插件名称
```

### 服务端运行与停止 ###

增加脚本可执行权限 

`chmod +x *.sh`

后台运行（无log，ssh窗口关闭后也继续运行） 

`./run.sh`

后台运行（输出log，ssh窗口关闭后也继续运行） 

`./logrun.sh`

后台运行时查看运行情况 

`./tail.sh`

停止运行 

`./stop.sh`

注：通过脚本运行默认日志会保存在根目录的ssserver.log，可手动查看。

### 更新源代码 ###
如果代码有更新可用本命令更新代码

进入shadowsocks目录 

`cd shadowsocks` 

执行 

`git pull` 

成功后重启ss服务

### 其它异常 ###
如果你的服务端python版本在2.6以下，那么必须更新python到2.6.x或2.7.x版本

如果运行一段时间后，你发现服务器无法连接，同时ssh连上去后，发现进程不存在，那么可能是达到了系统的最大连接数 

如果是ubuntu/centos均可修改`/etc/sysctl.conf`

找到`fs.file-max`这一行，修改其值为1024000，并保存退出。然后执行`sysctl -p`使其生效

打开文件`/etc/security/limits.conf`

添加两行： 
```
*               soft    nofile           512000
*               hard    nofile          1024000
```
保存后，重启操作系统生效

针对ubuntu系统，你还需要额外的在运行前使用ulimit命令设置最大文件数，可使用附带的运行脚本。  
如果使用supervisor进程守护，需要修改文件`/etc/default/supervisor`，添加一行：  
`ulimit -n 512000`  
再启动你的服务