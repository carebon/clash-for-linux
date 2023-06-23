[TOC]

# 项目介绍

此项目是通过使用开源项目[clash](https://github.com/Dreamacro/clash)作为核心程序，再结合脚本实现简单的代理功能。

主要是为了解决我们在服务器上下载GitHub等一些国外资源速度慢的问题。

<br>

# 使用须知

- 运行本项目建议使用root用户，或者使用 sudo 提权。
- 使用过程中如遇到问题，请优先查已有的 [issues](https://github.com/wanhebin/clash-for-linux/issues)。
- 在进行issues提交前，请替换提交内容中是敏感信息（例如：订阅地址）。
- 本项目是基于 [clash](https://github.com/Dreamacro/clash) 、[yacd](https://github.com/haishanh/yacd) 进行的配置整合，关于clash、yacd的详细配置请去原项目查看。
- 此项目不提供任何订阅信息，请自行准备Clash订阅地址。
- 运行前请手动更改`.env`文件中的`CLASH_URL`变量值，否则无法正常运行。
- 当前在RHEL系列和Debian系列Linux系统中测试过，其他系列可能需要适当修改脚本。
- 支持 x86_64/aarch64 平台

<br>

# 使用教程

## 下载项目

下载项目

```bash
$ git clone https://github.com/wanhebin/clash-for-linux.git
```

进入到项目目录，编辑`.env`文件，修改变量`CLASH_URL`的值。

```bash
$ cd clash-for-linux
$ vim .env
```

> **注意：** `.env` 文件中的变量 `CLASH_SECRET` 为自定义 Clash Secret，值为空时，脚本将自动生成随机字符串。

<br>

## 启动程序

直接运行脚本文件`start.sh`

- 进入项目目录

```bash
$ cd clash-for-linux
```

- 运行启动脚本

```bash
$ sudo bash start.sh

正在检测订阅地址...
Clash订阅地址可访问！                                      [  OK  ]

正在下载Clash配置文件...
配置文件config.yaml下载成功！                              [  OK  ]

正在启动Clash服务...
服务启动成功！                                             [  OK  ]

Clash Dashboard 访问地址：http://<ip>:9090/ui
Secret：xxxxxxxxxxxxx

请执行以下命令加载环境变量: source /etc/profile.d/clash.sh

请执行以下命令开启系统代理: proxy_on

若要临时关闭系统代理，请执行: proxy_off

```

```bash
$ source /etc/profile.d/clash.sh
$ proxy_on
```

- 检查服务端口

```bash
$ netstat -tln | grep -E '9090|789.'
tcp        0      0 127.0.0.1:9090          0.0.0.0:*               LISTEN     
tcp6       0      0 :::7890                 :::*                    LISTEN     
tcp6       0      0 :::7891                 :::*                    LISTEN     
tcp6       0      0 :::7892                 :::*                    LISTEN
```

- 检查环境变量

```bash
$ env | grep -E 'http_proxy|https_proxy'
http_proxy=http://127.0.0.1:7890
https_proxy=http://127.0.0.1:7890
```

以上步鄹如果正常，说明服务clash程序启动成功，现在就可以体验高速下载github资源了。

<br>

## 重启程序

如果需要对Clash配置进行修改，请修改 `conf/config.yaml` 文件。然后运行 `restart.sh` 脚本进行重启。

> **注意：**
> 重启脚本 `restart.sh` 不会更新订阅信息。

<br>

## 停止程序

- 进入项目目录

```bash
$ cd clash-for-linux
```

- 关闭服务

```bash
$ sudo bash shutdown.sh

服务关闭成功，请执行以下命令关闭系统代理：proxy_off

```

```bash
$ proxy_off
```

然后检查程序端口、进程以及环境变量`http_proxy|https_proxy`，若都没则说明服务正常关闭。


<br>

## Clash Dashboard

- 访问 Clash Dashboard

通过浏览器访问 `start.sh` 执行成功后输出的地址，例如：http://192.168.0.1:9090/ui

- 登录管理界面

在`API Base URL`一栏中输入：http://\<ip\>:9090 ，在`Secret(optional)`一栏中输入启动成功后输出的Secret。

点击Add并选择刚刚输入的管理界面地址，之后便可在浏览器上进行一些配置。

- 更多教程

此 Clash Dashboard 使用的是[yacd](https://github.com/haishanh/yacd)项目，详细使用方法请移步到yacd上查询。


<br>

# 常见问题

1. 部分Linux系统默认的 shell `/bin/sh` 被更改为 `dash`，运行脚本会出现报错（报错内容一般会有 `-en [ OK ]`）。建议使用 `bash xxx.sh` 运行脚本。

2. 部分用户在UI界面找不到代理节点，基本上是因为厂商提供的clash配置文件是经过base64编码的，且配置文件格式不符合clash配置标准。

   目前此项目已集成自动识别和转换clash配置文件的功能。如果依然无法使用，则需要通过自建或者第三方平台（不推荐，有泄露风险）对订阅地址转换。
   
3. 程序日志中出现`error: unsupported rule type RULE-SET`报错，解决方法查看官方[WIKI](https://github.com/Dreamacro/clash/wiki/FAQ#error-unsupported-rule-type-rule-set)


我的补充

​
因为在服务器上下数据集实在太慢了，就想代理一下，折腾了两天 记录一下

主要是参考GitHub - wanhebin/clash-for-linux: Linux 端使用 Clash 作为代理工具

git没装的提前装一下，下载安装包
```bash
git clone https://github.com/wanhebin/clash-for-linux.git
```
进入项目，编辑.env
```bash
$ cd clash-for-linux
$ vim .env
```
注意： .env 文件中的变量 CLASH_SECRET 为自定义 Clash Secret，值为空时，脚本将自动生成随机字符串。
```bash
# Clash 订阅地址
export CLASH_URL='https://XXXXXXXX.com/XXXXX'把这个修改成订阅地址
export CLASH_SECRET=''#不动的话会默认生成
```
启动程序，直接运行start.sh
```bash
$ cd clash-for-linux
$ sudo bash start.sh
```
会出现
```bash
正在检测订阅地址...
Clash订阅地址可访问！                                      [  OK  ]

正在下载Clash配置文件...
配置文件config.yaml下载成功！                              [  OK  ]

正在启动Clash服务...
服务启动成功！                                             [  OK  ]

Clash Dashboard 访问地址：http://<ip>:9090/ui
Secret：xxxxxxxxxxxxx

请执行以下命令加载环境变量: source /etc/profile.d/clash.sh

请执行以下命令开启系统代理: proxy_on

若要临时关闭系统代理，请执行: proxy_off
```
然后第一个是设置环境变量（不确定）source命令是一个内置的shell命令,用于从当前shell会话中的文件读取和执行命令。source命令通常用于保留、更改当前shell中的环境变量。

   第二个是启动
```bash
$ source /etc/profile.d/clash.sh
$ proxy_on
```
通过下面的检查服务端口   会出现
```bash
$ netstat -tln | grep -E '9090|789.'
tcp        0      0 127.0.0.1:9090          0.0.0.0:*               LISTEN     
tcp6       0      0 :::7890                 :::*                    LISTEN     
tcp6       0      0 :::7891                 :::*                    LISTEN     
tcp6       0      0 :::7892                 :::*                    LISTEN
```
↑这个是作者的  ↓这个是我的
```bash
$ netstat -tln | grep -E '9090|789.'
tcp6       0      0 :::9090                 :::*                    LISTEN
tcp6       0      0 :::7892                 :::*                    LISTEN
tcp6       0      0 :::7891                 :::*                    LISTEN
tcp6       0      0 :::7890                 :::*                    LISTEN
```
应该都是对的

检查环境变量
```bash
$ env | grep -E 'http_proxy|https_proxy'
```
会输出
```bash
http_proxy=http://127.0.0.1:7890
https_proxy=http://127.0.0.1:7890
```
第一次我的没出来，我就没管 果然不能用  手动设置一下是
```bash
export http_proxy=http://127.0.0.1:7890
export https_proxy=http://127.0.0.1:7890
```
 验证方法是

wget https://www.google.com.hk/
会出现
```bash
 wget www.google.com
 ```
--2023-06-23 10:45:13--  http://www.google.com/
正在连接 127.0.0.1:7890... 已连接。
已发出 Proxy 请求，正在等待回应... 200 OK
长度： 未指定 [text/html]
正在保存至: “index.html.1”

index.html.1                [ <=>                            ]  17.84K  --.-KB/s    用时 0.04s

2023-06-23 10:45:14 (418 KB/s) - “index.html.1” 已保存 [18273]

如果需要对Clash配置进行修改，请修改 conf/config.yaml 文件。然后运行 restart.sh 脚本进行重启。注意： 重启脚本 restart.sh 不会更新订阅信息

停止程序
进入项目目录
```bash
cd clash-for-linux
```
关闭服务
```bash
sudo bash shutdown.sh
```
服务关闭成功后  请执行以下命令关闭系统代理：
```bash
proxy_off
```
然后检查程序端口、进程以及环境变量
```bash
http_proxy
https_proxy
```
若都没则说明服务正常关闭。
```bash
Clash Dashboard
```
这个带ui的没弄出来 不知道啥原因

而且还有一个问题 浏览器出不去  命令行好像可以  还继续研究下

浏览器的问题解决了 参考linux环境使用clash实现网络代理访问外网 - 程序员大本营

需要设置一下网络代理

![1687490202058](https://github.com/carebon/clash-for-linux/assets/40285558/4d28f03a-33bd-4893-8552-178ddfebac41)


​
