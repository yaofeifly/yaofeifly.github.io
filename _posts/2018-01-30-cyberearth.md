---
layout: post
title:  "赛博地球杯线下赛WEB_RCE Write_up"
date:   2018-01-30 10:02:01 +0800
categories: CTF
tag: CTF Write-up
---

* content
{:toc}

这次参与赛博地球杯线下赛出题，看到巨佬们强还是强啊，先膜一下，然后献上自己的出题思路，萌新出题，轻喷 ！！！

### 第一步、登录

查看网站发现网站无法登录，并且根据robots.txt能发现flag.php和hack.php

![cyberearth]({{ '/styles/images/cyberearth/1.png' | prepend: site.baseurl  }})

不过可以注意到cookie中存在isLogin=0，设置cookie，isLogin=1，发现成功登录

![cyberearth]({{ '/styles/images/cyberearth/2.png' | prepend: site.baseurl  }})

### 第二步、文件包含

成功登录，发现一个管理页面`admin/admin.php?file=index&ext=php`，推测存在文件包含，可以发现存在过滤，不过可以绕过，访问

`admin/admin.php?file=....//....//....//....//....//....//etc/passwd&ext=`

可以成功包含文件

![cyberearth]({{ '/styles/images/cyberearth/3.png' | prepend: site.baseurl  }})

不过尝试flag.php会发现是文件包含，不能显示源代码，伪协议也无法使用

![cyberearth]({{ '/styles/images/cyberearth/4.png' | prepend: site.baseurl  }})

### 第三步、审计nginx配置

不过可以读取一些配置文件，比如读取nginx配置文件

`admin/admin.php?file=....//....//....//....//....//....//etc/nginx/sites-enabled/default&ext=`

可以发现配置文件中存在问题

![cyberearth]({{ '/styles/images/cyberearth/5.png' | prepend: site.baseurl  }})

存在一个location是/web-img，使用alias指向了/images/，不过location的后面没有/，alias的后面有斜杠，存在隐患

访问/web-img../，发现列出了根目录

![cyberearth]({{ '/styles/images/cyberearth/6.png' | prepend: site.baseurl  }})

由于这个location不解析php，

访问`/web-img../usr/share/nginx/html/flag.php`获得flag的重要信息

![cyberearth]({{ '/styles/images/cyberearth/7.png' | prepend: site.baseurl  }})

flag.php中提示我们flag存在于FLAGFLAGFLAGFLAG.pcapng中，我们尝试访问`/web-img../usr/share/nginx/html/FLAGFLAGFLAGFLAG.pcapng`发现403 forbidden，无法获取FLAGFLAGFLAGFLAG.pcapng。这里我们有两种方式获取FLAGFLAGFLAGFLAG.pcapng。

一种是通过文件包含对FLAGFLAGFLAGFLAG.pcapng源码数据包进行分析:

访问：`/admin/admin.php?file=....//FLAGFLAGFLAGFLAG.pcapng&ext=`得到数据包源码

![cyberearth]({{ '/styles/images/cyberearth/18.png' | prepend: site.baseurl  }})

第二种是利用题目中给的hack.php的webshell进行连接，下载数据包进行查看。这里主要说明第二种方式：

### 第四步、根据hack.php进行webshell连接RCE

最初robots.txt还提示了我们一个hack.php,我们根据上面数据包分析也可以发现hack.php就是利用weevely生成的webshell，

通过最前的方法，下载hack.php，查看hack.php，我们可以看到webshell加了混淆和替换，将这些混淆替换掉，就可以看到webshell的源码了，

![cyberearth]({{ '/styles/images/cyberearth/12.png' | prepend: site.baseurl  }})

![cyberearth]({{ '/styles/images/cyberearth/13.png' | prepend: site.baseurl  }})

利用weevely连接webshell，但是我们发现源码中并没有给我们weevely连接的密码，但是源码中有两个变量`$kh="42f7"`和`$kf="e9ac"`

审计weevely源码，我们会知道weevely生成的webshell进行连接时密码的验证方式是将我们输入的密码进行md5加密然后取md5加密后的字符串后8位，即就是我们源码中的`42f7和e9ac`

![cyberearth]({{ '/styles/images/cyberearth/14.png' | prepend: site.baseurl  }})

所以在我们不知道连接密码的时候，我们可以直接修改weevely源码，强行让他的`share_key`等于我们的webshell的`42f7e9ac`然后不论我们输入什么密码都可以直接连接webshell

![cyberearth]({{ '/styles/images/cyberearth/15.png' | prepend: site.baseurl  }})

成功RCE

![cyberearth]({{ '/styles/images/cyberearth/17.png' | prepend: site.baseurl  }})

### 第五步、数据包分析

成功连接shell，利用weevely的file_download命令下载FLAGFLAGFLAGFLAG.pcapng数据包。

查看数据包，跟踪tcp流，我们可以看出这是weevely的数据包，题目提示，flag在数据包中，所以我们需要解密这个webshell。

![cyberearth]({{ '/styles/images/cyberearth/8.png' | prepend: site.baseurl  }})



### 第六步、weevely源码分析

从github中下载weevely3并分析weevely3的源码，我们可以知道weevely执行命令操作的加密方式

![cyberearth]({{ '/styles/images/cyberearth/9.png' | prepend: site.baseurl  }})

根据这个加密函数，编写自己的解密方法，直接解密得到flag：

![cyberearth]({{ '/styles/images/cyberearth/10.png' | prepend: site.baseurl  }})

![cyberearth]({{ '/styles/images/cyberearth/11.png' | prepend: site.baseurl  }})





