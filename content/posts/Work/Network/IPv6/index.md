---
title: "IPV6"
date: 2022-11-17T10:24:01+09:00
menu:
  sidebar:
    name: IPV6
    identifier: IPV6
    parent: Network
    weight: 120
    
---
### 原因

老家有一台多年的网件R7000，服役至今，之前刷了梅林386.2，最近看到跟新了很多`IPV6`方面的支持，随即升级了梅林386.4。路由器是在老家使用，不需要软件中心，也就没折腾384和380版本，386版本想上软件中心，估计是够呛了，不过有其他办方法可以安装entware，不在本文讨论范围内，有时间再折腾。本文重点讲一讲`IPV6`+阿里动态域名的实现方法。

首先，梅林外网`IPV6`访问路由器需要解决3个问题。

    1. 梅林Web页面不支持IPV6访问，需要找到方法来搞定。
    2. 使用脚本把WAN口的IPV6地址更新到阿里云解析。
    3. 设置定期执行脚本。

 \

### 问题

问题1：困扰了我很久，这也是我迟迟没有折腾梅林`IPV6`访问的原因，近几天偶然在恩山论坛上看到k3梅林`IPV6`远程管理方法的帖子，正好解决了这个痛点。肉松直接转载到了我的博客中。 \
见上一篇[文章](https://www.rousongs.com/1441.html)。解决方案形成两行代码。
```
nohup /usr/sbin/socat TCP6-LISTEN:9999,reuseaddr,fork TCP4:192.168.1.1:80 >/dev/null 2>&1 &
ip6tables -I INPUT -i ppp0 -p tcp -m tcp --dport 9999 -j ACCEPT
```
 \
问题2，很早之前折腾过linux和windows下的ipv6阿里云动态域名，脚本可以直接拿来用。 \
参见[linux系统下配置阿里DDNS（IPv6）](https://www.rousongs.com/1293.html) \
具体代码这里就不贴了。

问题3，linux计划任务问题，很好解决。 \
参见[OpenWrt使用crontab执行计划任务](https://www.rousongs.com/1262.html) \
可以形成一行命令，意为每60分钟执行一次/jffs/scripts/aliddns.sh
```
cru aliddns "*/60 * * * * /jffs/scripts/aliddns.sh"
```
 \
然后我们可以把问题123中形成的脚本合成一个脚本文件，命名为ddns-start。问题2中的脚本单独形成一个文件，名称随意，为了区分这里命名为aliddns.sh。（后续解释原因）

### 测试

博主经过反复设置与测试，将整个过程简化整理为三个步骤，如下：
一、找到【高级设置-`IPV6`】，打开路由器中`IPV6`相关的设置，如图。

{{< img src="images/2471299693.png" >}}

 \
 重新拨号号后系统记录-IPV6中查看是否获取IPV6，如图。

{{< img src="images/830659089.png" >}}

 \
如果显示ipv6信息，可以继续后面的设置。如果没有说明没有ipv6地址，无法适用与本文。

二、在/jffs/scripts/写入相关脚本 \
说明：经过测试，梅林自带的ddns功能中xx最后一个选项costom为自定义选项，需要用户自行在/jffs/scripts/目录下自行建立脚本文件， \
文件名为 ddns-start，路由器开机和重启时，会执行这一脚本。所以我们把只需要开机执行一次的命令放入ddns-start中，开机执行一次， \
在管理页面设置ddns时ddns-start文件执行是否成功无从得知，但是重启时肯定是执行成功的。 \
打开路由器的ssh功能，使用winscp把ddns-start文件和aliddns.sh放入/jffs/scripts/目录中，并从属性中设置运行权限， \
或简单粗暴给777权限也可。

三、设置路由器
1、调整路由器jffs分区相关设置
找到【系统管理-系统设置-Persistent JFFS2 partition】

{{< img src="images/962656654.png" >}}

【Format JFFS partition at next boot】保持否  
【Enable JFFS custom scripts and configs】选择是

2、设置 ddns 和 https  
找到【外部网络 (WAN)-DDNS】，如图。

{{< img src="images/903738698.png" >}}

*   启用 DDNS 客户端选择是；
*   服务器选择 Custom，意为自定义。
*   主机名称就写域名吧。
*   HTTPS/SSL 证书根据自己需要，可以上传，也可以选择无。
*   最后保存本页设置，点击保存后，可能页面一直在转，不管他，直接使用浏览器的刷新按钮刷新页面，进行下一步。

### 结果

四、重启路由器  
注: 重启路由器前在阿里云解析添加相应的 AAAA 记录。  
耐心等待路由器重启完毕，查看阿里云解析和路由器 ipv6 地址是否一致，一致则解析成功；ssh 登录输入命令`cru l`查看计划任务，显示有 aliddns 的计划任务则计划任务设置成功。

到此解析搞定，传理论上适用于大多数梅林，后续路由器的玩法方面可以发挥的空间就很大了，大家自由发挥。

本文中省略了大量 ssh 终端测试命令操作、winscp 文件操作和域名证书方面的操作，对小白用户可能不太友好，旨在提供一个解决思路，欢迎大家留言讨论交流。

注∶硬件网件 R7000，软件梅林 386.4(XWRT-VORTEX 版本，附链接 [http://xvtx.ru/xwrt/)](https://rousongs.com/go/aHR0cDovL3h2dHgucnUveHdydC8p)