---
title: '单位内ssh privoxy chrome SwitchyOmega翻墙'
layout: post
tags:
    - ssh
    - privoxy
    - chrome
    - Switchy Omega
---
#### 世界那么大，我想去看看
单位里面被墙的很厉害，很多网站不能访问。即使用了运维的pac也只是能访问google和github这些基本所需的网站。所谓**世界那么大，我想去看看。**因此一定得翻个墙才行。本文将要介绍的就是`ssh -D`这款利器--*那道墙对你来说，从此透明。*

#### 基本工具
1. 公司Linux开发机，有ssh，网络访问基本没有被限制。
2. privoxy，一种辅助翻墙工具。
3. chrome浏览器+Switchy Omega扩展

#### 搭梯子
- 在开发机上执行`ssh -qTfnN -D 0.0.0.0:7070 liaotao@localhost`，输入密码，这样开发机就可以作为一台代理服务器用了。
  - q表示该命令进入安静模式
  - T是指该命令不占用shell
  - N是指该命令不执行远程命令
  - f是指该命令在后台运行
  - D是该命令重要参数，他的后面跟着socks5服务器的地址与端口
  
利用加密隧道传输加密数据，可以越过单位墙和GFW。

- 安装、配置privoxy
  - `yum -y install privoxy`
  - `vi /etc/privoxy/config`编辑config文件。修改`listen-address  127.0.0.1:8118`为`listen-address  0.0.0.0:8118`任何机器都能访问。
  - 添加`forward-socks5t    /              0.0.0.0:7070 .`,意思是所有站点通过服务器上的7070端口代理走。保存文件。
  - `service privoxy start`,启动privoxy。
 
- 配置chrome的Switchy Omega扩展
  - 选项->新建情景模式->创建
  - 如下图，代理服务器为开发机IP地址，8118为privoxy监听端口号。

---
<img src='{{ site.atom-baseurl }}/media/img/CC0B82AE-9016-48B4-A72F-75BCB2231206.png' width='80%' alt='Switchy Omega扩展代理配置' />

配置好后在地址栏中输入<http://p.p>正常显示则表示梯子已经搭好了哦。：-）


