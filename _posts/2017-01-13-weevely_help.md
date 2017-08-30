---
layout: post
title:  "Weevely webshell工具使用文档"
date:   2017-01-13 10:02:01 +0800
categories: Webshell
tag: Webshell源码学习
---

* content
{:toc}



## 1.&emsp;简介 ##
### 1.1&emsp;前言 ###
&emsp;&emsp;&emsp;weevely 是一款使用python编写的webshell工具（集webshell生成和连接于一身），采用C/S模式构建，可以算作是linux下的一款菜刀替代工具（限于PHP），具有很好的隐蔽性，在Linux下具有：<br>

* 服务器配置审计
* 后门放置
* 暴力破解
* 文件管理
* 资源搜索
* 网络代理
* 命令执行
* 系统信息收集
* 端口扫描等功能

### 1.2&emsp;测试环境 ###
&emsp;&emsp;&emsp;测试系统：Linux kali 4.6.0-kali1-amd64<br>
&emsp;&emsp;&emsp;测试环境：kali自带Apache+php+MySQL环境<br>
&emsp;&emsp;&emsp;weevely工具版本：weevely 3.2.0

![weevely]({{ '/styles/images/weevely/weevely_version.png' | prepend: site.baseurl  }})

## 2.&emsp;weevely工具操作 ##
### 2.1&emsp;生成后门 ###
&emsp;&emsp;&emsp;生成php后门：通过命令行输入命令：<code><font color=red>weevely generate hello /var/www/html/testformd.php</font></code>生成一个路径为/var/www/html下的文件名为testformd.php的php文件，其中hello为后门连接的密码。

![weevely]({{ '/styles/images/weevely/weevely_make.png' | prepend: site.baseurl  }})

### 2.2&emsp;连接后门 ###
&emsp;&emsp;&emsp;通过已知的连接密码去连接php后门文件：在命令行中输入命令<code><font color=red>weevely http://192.168.182.137/backdoor.php hello</font></code>进行远程连接后门文件。其中http://192.168.182.137/backdoor.php为你想要连接的php后门文件路径，hello为连接密码。

![weevely]({{ '/styles/images/weevely/weevely_connect.png' | prepend: site.baseurl  }})

当命令行页面显示为截图所示时即为连接页面，但并没有去验证密码的正确性，所以当出现这个页面时并不能代表你成功连接到后门。当你在当前命令行中输入任意命令就会触发源代码中的密码验证机制。来进一步去判断连接密码的正确性。（**具体验证机制说明将在下文单独说明**）如果输入任意命令时出现以下画面代表后门连接成功:

![weevely]({{ '/styles/images/weevely/connect_success.png' | prepend: site.baseurl  }})

出现错误提示时代表连接失败：

![weevely]({{ '/styles/images/weevely/connect_failed.png' | prepend: site.baseurl  }})

### 2.2&emsp;后门操作 ###
&emsp;&emsp;&emsp;连接后门成功后，在命令行中按两次Tab键可查看weevely的可利用模块：

![weevely]({{ '/styles/images/weevely/weevely_function.png' | prepend: site.baseurl  }})

模块功能说明如下图所示：

![weevely]({{ '/styles/images/weevely/function_help.png' | prepend: site.baseurl  }})
