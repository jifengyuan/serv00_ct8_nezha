# serv00和ct8主机一键安装哪吒探针和多主机保活

![构建哪吒探针FreeBSD二进制安装包](https://github.com/nezhahq/nezha/raw/master/.github/brand.svg)


![Os][os-shield]
[![Release][release-shield]][release-url]
[![Forks][forks-shield]][forks-url]
[![Stargazers][stars-shield]][stars-url]
[![Issues][issues-shield]][issues-url]
[![Contributors][contributors-shield]][contributors-url]
[![License][license-shield]][license-url]


## 1 背景
基于`serv00`和`ct8`这种配置较低的主机，比较适合用来做探针。目前还没发现能自动安装哪吒探针面板和agent客户端的脚本，以及多主机相互保活、探针进程保活等，所以写了这个项目。

哪吒探针v0版本的效果体验：[https://monitor1.typecodes.us.kg](https://monitor1.typecodes.us.kg) 。

哪吒探针v1版本的效果体验：[https://monitor2.typecodes.us.kg](https://monitor2.typecodes.us.kg) 。


## 2 特点

```
1、【安装简单】：支持一键安装最新v1/v0版本的哪吒探针dashboard或者agent客户端；
2、【自动保活】：弃用PM2，通过自动生成crontab，实现了探针进程监控保活以及主机间相互保活；
3、【自动保活】：当某个主机探针进程掉线时，本机或者其它保活的主机都能自动重新拉起本机探针进程；
4、【外部保活】：对于单台serv00/ct8主机，也可以通过 青龙面板 或者其它云主机对它进行探针进程监控和保活；
5、【扩展性强】：支持保活自定义的进程，只需把任该进程追加到monitor.conf配置文件即可；
6、【通信安全】：多主机之间使用ssh公私钥进行通信保活，不会暴露主机密码；
7、【监控通知】：支持企业微信机器人、企业微信app应用、Telegram、pushPlus等重要域名进行监控和通知；
8、【数据备份】：支持七牛、腾讯云cos、阿里云oss云存储备份哪吒面板数据库。
```


## 2 使用步骤

```
1、下载项目: git clone https://github.com/vfhky/serv00_ct8_nezha.git
2、进入项目: cd serv00_ct8_nezha
3、追加其它保活主机（非必须的操作）: vim config/host.eg
4、开始安装: python3 main.py 。
```


## 3 配置文件说明

在`config`配置目录下面有4个模板文件，其中`host.eg`和`sys.eg`这两个配置文件是需要`【手工配置】`，其它两个文件都不需要修改（系统会自动根据相关逻辑生成对应的`xxx.conf`配置文件）。

#### 3.1 主机配置模板 host.eg

用于填写需要相互保活的主机信息。

1、当你有多台serv00/ct8机器时，通过这个配置实现主机间的相互保活。例如用当前serv00/ct8主机和另外一个s9的serv00机器(用户名是vhub)做相互保活，那么在文件中追加s9的配置即可：

```
# hostname|port|username|password
s9.serv00.com|22|vhub|password
```

2、假如你只有一台serv00/ct8机器，则不需要修改（可以借助青龙面板等外部定时任务来保活）。

#### 3.2 系统配置模板 sys.eg

这个是系统配置文件，可以控制开启企业微信机器人、企业微信app应用、TG、pushPlus、七牛云备份等功能。

#### 3.3 进程监控模板 monitor.eg

用于监控需要保活的进程。当进程（如探针dashboard面板）掉线时，会通过本机或者其它相互保活的主机的crontab自动重新拉起本机的这个进程。

在安装完哪吒dashboard或agent后，系统会自动生成类似以下的配置。当然也可以手工追加任意一个其它进程来实现该进程的监控保活。

```
/home/vfhky/nezha_app/agent|nezha-agent|sh nezha-agent.sh|foreground
/home/vfhky/nezha_app/dashboard|nezha-dashboard|./nezha-dashboard|background
```

#### 3.4 多主机心跳保活模板 heartbeat.eg

用于对其它serv00/ct8机器保活（也包括进程保活等）。当在`host.eg`配置文件中新增了要相互保活的主机，系统会自动生成多主机间相互保活的配置数据（示例如下）：

`s9.serv00.com|22|vhub`


## 4 相关手册

以下是`安装哪吒探针`、`探针进程监控保活`、`多主机保活原理`、`面板sqlite.db备份`等功能的文档，方便大家参考查阅：

#### 4.1 详细安装过程

1、常规手工安装哪吒探针V0版本： 包括如何server00开启应用、TCP端口、申请github的token等等，[《在serv00主机上安装哪吒探针》](https://typecodes.com/linux/server00installnezha.html)

2、一键安装哪吒探针V0版本： [《serv00和ct8主机一键安装哪吒探针和多主机保活》](https://typecodes.com/python/serv00ct8nezha.html)

3、一键安装哪吒探针V1版本： [serv00和ct8主机一键安装哪吒探针V1版本和多主机保活](https://typecodes.com/python/serv00ct8nezhav1.html)

4、升级哪吒探针V1版本开通Github、Gitee的OAuth2登录： [serv00和ct8上的哪吒探针V1开启Github和Gitee登录](https://typecodes.com/python/serv00ct8nezhav1githubgiteelogin.html)

#### 4.2 其它功能

1、使用七牛、腾讯云cos、阿里云oss云存储备份哪吒面板数据库： [《serv00和ct8主机一键安装哪吒探针和多主机保活(五)》](https://typecodes.com/python/serv00ct8nezha5.html)

2、使用青龙面板对单台serv00保活： [《serv00和ct8主机一键安装哪吒探针和多主机保活(三)》](https://typecodes.com/python/serv00ct8nezha3.html)

3、utils.sh 强大的serv00脚本工具： [《serv00和ct8主机一键安装哪吒探针和多主机保活(四)》](https://typecodes.com/python/serv00ct8nezha4.html)


#### 4.3 架构原理

1、哪吒探针dashboard面板FreeBSD二进制安装包构建：[https://github.com/vfhky/nezha-build](https://github.com/vfhky/nezha-build)

2、探针面板安装包：[《github workflow构建哪吒探针FreeBSD安装包》](https://typecodes.com/linux/githubworkflownezhafreebsdserv00.html)

3、架构说明（含保活原理等）： [《serv00和ct8主机一键安装哪吒探针和多主机保活(二)》](https://typecodes.com/python/serv00ct8nezha2.html)

4、修复项目中哪吒面板不显示主机区域的问题： [《serv00和ct8主机一键安装哪吒探针和多主机保活(六)》](https://typecodes.com/python/serv00ct8nezha6.html)


## 5 Stars 趋势

[![Star History Chart](https://api.star-history.com/svg?repos=vfhky/serv00_ct8_nezha&type=Date)](https://star-history.com/#vfhky/serv00_ct8_nezha&Date)


<!-- links -->
[os-shield]: https://img.shields.io/badge/FreeBSD-blue
[release-shield]: https://img.shields.io/github/v/release/vfhky/serv00_ct8_nezha
[release-url]: https://github.com/vfhky/serv00_ct8_nezha/releases
[contributors-shield]: https://img.shields.io/github/contributors/vfhky/serv00_ct8_nezha
[contributors-url]: https://github.com/vfhky/serv00_ct8_nezha/graphs/contributors
[forks-shield]: https://img.shields.io/github/forks/vfhky/serv00_ct8_nezha?style=flat
[forks-url]: https://github.com/vfhky/serv00_ct8_nezha/network/members
[stars-shield]: https://img.shields.io/github/stars/vfhky/serv00_ct8_nezha?style=flat
[stars-url]: https://github.com/vfhky/serv00_ct8_nezha/stargazers
[issues-shield]: https://img.shields.io/github/issues/vfhky/serv00_ct8_nezha
[issues-url]: https://github.com/vfhky/serv00_ct8_nezha/issues
[license-shield]: https://img.shields.io/github/license/vfhky/serv00_ct8_nezha
[license-url]: https://github.com/vfhky/serv00_ct8_nezha/blob/master/LICENSE?color=blue
