---
layout:     post
title:      Hadoop用户配置ssh免密登录
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：					https://blog.csdn.net/wangkun_j/article/details/80351277				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p><strong>一般生产环境 Hadoop组件都是由hadoop用户来启动，首先需要配置hadoop用户ssh免密登录</strong></p>



<h5 id="1创建hadoop用户">1.创建Hadoop用户</h5>



<pre class="prettyprint"><code class="language-shell hljs coffeescript">[root<span class="hljs-property">@hadoop000</span> ~]<span class="hljs-comment"># useradd hadoop</span>
[root<span class="hljs-property">@hadoop000</span> ~]<span class="hljs-comment"># id hadoop</span>
uid=<span class="hljs-number">1102</span>(hadoop) gid=<span class="hljs-number">1102</span>(hadoop) groups=<span class="hljs-number">1102</span>(hadoop)
[root<span class="hljs-property">@hadoop000</span> ~]<span class="hljs-comment"># passwd hadoop</span>
Changing password <span class="hljs-keyword">for</span> user hadoop.
New <span class="hljs-attribute">password</span>: 
BAD <span class="hljs-attribute">PASSWORD</span>: it <span class="hljs-keyword">is</span> too simplistic/systematic
BAD <span class="hljs-attribute">PASSWORD</span>: <span class="hljs-keyword">is</span> too simple
Retype <span class="hljs-keyword">new</span> <span class="hljs-attribute">password</span>: 
<span class="hljs-attribute">passwd</span>: all authentication tokens updated successfully.
<span class="hljs-comment">#给hadoop sudo权限</span>
[root<span class="hljs-property">@hadoop000</span> ~]<span class="hljs-comment"># vi /etc/sudoers</span>
hadoop ALL=(root) <span class="hljs-attribute">NOPASSWD</span>:ALL</code></pre>



<h5 id="2切换到hadoop用户-配置免密登录">2.切换到hadoop用户 配置免密登录</h5>



<pre class="prettyprint"><code class="language-shell hljs applescript">[root@hadoop000 ~]<span class="hljs-comment"># su - hadoop</span>
<span class="hljs-comment">#确保有ssh服务</span>
[hadoop@hadoop000 ~]$ sudo service sshd status
openssh-daemon (pid  <span class="hljs-number">24823</span>) <span class="hljs-keyword">is</span> <span class="hljs-property">running</span>...
[hadoop@hadoop000 ~]$ ssh-keygen
Generating public/private rsa key pair.
Enter <span class="hljs-type">file</span> <span class="hljs-keyword">in</span> which <span class="hljs-keyword">to</span> save <span class="hljs-keyword">the</span> key (/home/hadoop/.ssh/id_rsa): 
Created directory '/home/hadoop/.ssh'.
Enter passphrase (empty <span class="hljs-keyword">for</span> no passphrase): 
Enter same passphrase again: 
Your identification has been saved <span class="hljs-keyword">in</span> /home/hadoop/.ssh/id_rsa.
Your public key has been saved <span class="hljs-keyword">in</span> /home/hadoop/.ssh/id_rsa.pub.
The key fingerprint <span class="hljs-keyword">is</span>:
bd:<span class="hljs-number">75</span>:<span class="hljs-number">87</span>:ca:<span class="hljs-number">44</span>:<span class="hljs-number">2</span>d:<span class="hljs-number">1</span>f:de:b7:<span class="hljs-number">5</span>e:a0:<span class="hljs-number">96</span>:ac:b3:<span class="hljs-number">7</span>e:<span class="hljs-number">01</span> hadoop@hadoop000
The key's randomart image <span class="hljs-keyword">is</span>:
+<span class="hljs-comment">--[ RSA 2048]----+</span>
|                 |
|             .   |
|            o o  |
|         .E. + + |
|        S ..o * +|
|           *.= oo|
|          . B. ..|
|          .o. . .|
|         .++   . |
+<span class="hljs-comment">-----------------+</span>
[hadoop@hadoop000 ~]$ cd .ssh
[hadoop@hadoop000 .ssh]$ ll
total <span class="hljs-number">8</span>
-rw<span class="hljs-comment">------- 1 hadoop hadoop 1675 May 17 14:59 id_rsa</span>
-rw-r<span class="hljs-comment">--r-- 1 hadoop hadoop  395 May 17 14:59 id_rsa.pub</span>
[hadoop@hadoop000 .ssh]$ cat id_rsa.pub &gt;&gt; authorized_keys
[hadoop@hadoop000 .ssh]$ ll
total <span class="hljs-number">12</span>
-rw-rw-r<span class="hljs-comment">-- 1 hadoop hadoop  395 May 17 14:59 authorized_keys</span>
-rw<span class="hljs-comment">------- 1 hadoop hadoop 1675 May 17 14:59 id_rsa</span>
-rw-r<span class="hljs-comment">--r-- 1 hadoop hadoop  395 May 17 14:59 id_rsa.pub</span>
<span class="hljs-comment">#authorized_keys权限必须为600</span>
[hadoop@hadoop000 .ssh]$ chmod <span class="hljs-number">600</span> authorized_keys
[hadoop@hadoop000 .ssh]$ ssh hadoop000 <span class="hljs-type">date</span>
The authenticity <span class="hljs-keyword">of</span> host 'hadoop000 (<span class="hljs-number">192.168</span><span class="hljs-number">.6</span><span class="hljs-number">.217</span>)' can't be established.
RSA key fingerprint <span class="hljs-keyword">is</span> <span class="hljs-number">25</span>:d9:d6:<span class="hljs-number">7</span>e:fa:e0:a7:<span class="hljs-number">92</span>:<span class="hljs-number">7</span>c:<span class="hljs-number">06</span>:<span class="hljs-number">76</span>:<span class="hljs-number">5</span>c:<span class="hljs-number">2</span>b:<span class="hljs-number">19</span>:bf:a3.
Are you sure you want <span class="hljs-keyword">to</span> <span class="hljs-keyword">continue</span> connecting (yes/no)? yes
Warning: Permanently added 'hadoop000,<span class="hljs-number">192.168</span><span class="hljs-number">.6</span><span class="hljs-number">.217</span>' (RSA) <span class="hljs-keyword">to</span> <span class="hljs-keyword">the</span> <span class="hljs-type">list</span> <span class="hljs-keyword">of</span> known hosts.
Thu May <span class="hljs-number">17</span> <span class="hljs-number">15</span>:<span class="hljs-number">00</span>:<span class="hljs-number">28</span> CST <span class="hljs-number">2018</span>
[hadoop@hadoop000 .ssh]$ ssh hadoop000 <span class="hljs-type">date</span>
Thu May <span class="hljs-number">17</span> <span class="hljs-number">15</span>:<span class="hljs-number">00</span>:<span class="hljs-number">30</span> CST <span class="hljs-number">2018</span></code></pre>



<h5 id="3总结">3.总结</h5>

<p>hadoop用户配置ssh免密登录 authorized_keys权限必须改为600</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>