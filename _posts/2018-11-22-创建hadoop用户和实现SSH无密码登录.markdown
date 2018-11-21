---
layout:     post
title:      创建hadoop用户和实现SSH无密码登录
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><strong><span style="font-size:14px;">1. 创建hadoop用户</span></strong></p>
<p><span style="font-size:14px;">    输入命令：Sudo addgroup hadoop  (创建hadoop用户组)</span></p>
<p><span style="font-size:14px;">    Sudo adduser --ingroup hadoop hadoop1 (添加用户hadoop到hadoop组) </span></p>
<p><span style="font-size:14px;">   设置密码为：hadoop<br><br>
   给hadoop用户赋权限</span></p>
<p><span style="font-size:14px;">  sudo gedit /etc/sudoers</span></p>
<p><span style="font-size:14px;"><br>
  在root   ALL=(ALL:ALL)   ALL下添加</span></p>
<p><span style="font-size:14px;">  hadoop   ALL=(ALL:ALL)  ALL</span></p>
<p><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:14px;"><strong>2.实现SSH无密码登陆</strong></span></p>
<p><br></p>
<p><span style="font-size:14px;">（1） 首先设置namenode的ssh为无需密码的、自动登录。<br><br><br><span></span>切换到hadoop用户( 保证用户hadoop可以无需密码登录，因为我们后面安装的hadoop属主是hadoop用户。)<br><br><br><span></span>$ su hadoop<br><br><br><span></span>cd /home/hadoop<br><br><br><span></span>$ ssh-keygen -t rsa<br><span></span>然后一直按回车<br><span></span>完成后，在home跟目录下会产生隐藏文件夹.ssh<br><span></span>$ cd .ssh<br><br><br><span></span>之后ls 查看文件<br><br><br><span></span>cp id_rsa.pub  authorized_keys<br><br><br><span></span>测试：<br><br><br><span></span>$ssh localhost<br><br><br><span></span>或者：<br><br><br><span></span>$ ssh master.hadoop                                      <br>
   <span> </span>第一次ssh会有提示信息：<br><br><br><span></span>The authenticity of host ‘node1 (10.64.56.76)’ can’t be established.<br><span></span>RSA key fingerprint is 03:e0:30:cb:6e:13:a8:70:c9:7e:cf:ff:33:2a:67:30.<br><span></span>Are you sure you want to continue connecting (yes/no)?<br><br><br><span></span>输入 yes 来继续。这会把该服务器添加到你的已知主机的列表中<br><br><br><span></span>发现链接成功，并且无需密码。<br><br><br><br><br><span></span>(2) 复制authorized_keys到slave1.hadoop 和slave2.hadoop 上<br><br><span></span>为了保证<span style="font-size:14px;">master.hadoop </span>可以无需密码自动登录到<span style="font-size:14px;">slave1.hadoop </span>和<span style="font-size:14px;">slave2.hadoop </span>，先在<span style="font-size:14px;">slave1.hadoop </span>和<span style="font-size:14px;">slave2.hadoop </span>上执行<br><br><br><span></span>$ su hadoop<br><br><br><span></span>cd /home/hadoop<br><br><br><span></span>$ ssh-keygen -t rsa<br><span></span>一路按回车.<br><span></span>然后回到<span style="font-size:14px;">master.hadoop </span>，复制authorized_keys到<span style="font-size:14px;">slave1.hadoop </span> 和<span style="font-size:14px;">slave1.hadoop </span> <br><span></span>[hadoop@hadoop .ssh]$ scp authorized_keys   <span style="font-size:14px;">
slave1.hadoop </span>:/home/hadoop/.ssh/<br><br><span></span>[hadoop@hadoop .ssh]$ scp authorized_keys   <span style="font-size:14px;">
slave2.hadoop </span>:/home/hadoop/.ssh/<br><br><br><span></span>这里会提示输入密码,输入hadoop账号密码就可以了。<br><span></span>改动 authorized_keys 文件的许可权限<br><span></span>[hadoop@hadoop .ssh]$chmod 644 authorized_keys<br><br><br><span></span>测试：ssh <span style="font-size:14px;">slave1.hadoop </span>或者ssh<span style="font-size:14px;">slave2.hadoop </span>（第一次需要输入yes）。<br><span></span>如果不须要输入密码则配置成功，如果还须要请检查上面的配置能不能正确。<br></span></p>
<p><span style="font-size:14px;">       <img src="https://img-blog.csdn.net/20130926110313062?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGlwZW5nc2h1YWkwNTA3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p><span style="font-size:14px;">      上图说明，已经无需密码登录了slave1.hadoop</span></p>
<p><span style="font-size:14px;">       <img src="https://img-blog.csdn.net/20130926110313062?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGlwZW5nc2h1YWkwNTA3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p><span style="font-size:14px;">      <span style="font-size:14px;">上图说明，已经无需密码登录了slave2.hadoop</span></span></p>
<p><span style="font-size:14px;"><span style="font-size:14px;"><span style="color:#ff0000;"><strong>说明 ：上面两个图中，ssh登录后，均出现欢迎信息和帮助信息网址，这会浪费登录时间，如何关闭显示这些信息呢，请参见文章《<span style="font-family:'Microsoft YaHei';font-size:20px;line-height:30px;"> </span></strong></span></span></span></p>
<h3 style="display:inline;font-weight:normal;font-size:20px;line-height:30px;font-family:'Microsoft YaHei';vertical-align:middle;">
<span class="link_title"><a href="http://blog.csdn.net/cadgj/article/details/10562367" rel="nofollow" style="color:rgb(102,102,102);text-decoration:none;">修改 Ubuntu SSH 登录后的欢迎信息</a></span></h3>
》
<p></p>
<p><span style="font-size:14px;"> </span></p>
            </div>
                </div>