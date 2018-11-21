---
layout:     post
title:      Hadoop分布式集群搭建 (一 Centos7 环境准备)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经允许，不得转载					https://blog.csdn.net/Louislip/article/details/53057634				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="机器准备">机器准备</h2>

<ul>
<li>Centos7 </li>
<li>Vmware10</li>
<li>jdk</li>
</ul>



<h3 id="安装和配置ip">安装和配置IP</h3>

<table>
<thead>
<tr>
  <th>主机名称</th>
  <th>用户</th>
  <th>密码</th>
  <th>ip</th>
</tr>
</thead>
<tbody><tr>
  <td>master</td>
  <td>master</td>
  <td>master</td>
  <td>192.168.1.122</td>
</tr>
<tr>
  <td>slave1</td>
  <td>hadoop</td>
  <td>hadoop</td>
  <td>192.168.1.123</td>
</tr>
<tr>
  <td>slave2</td>
  <td>hadoop</td>
  <td>hadoop</td>
  <td>192.168.1.124</td>
</tr>
<tr>
  <td>slave3</td>
  <td>hadoop</td>
  <td>hadoop</td>
  <td>192.168.1.125</td>
</tr>
</tbody></table>


<p>在每台主机添加用户 hadoop 密码 hadoop</p>

<blockquote>
  <p>使用命令 useradd添加用户， passwd为用户hadoop设置密码</p>
</blockquote>



<pre class="prettyprint"><code class=" hljs coffeescript">[root<span class="hljs-property">@slave1</span> ~]<span class="hljs-comment"># useradd hadoop</span>
[root<span class="hljs-property">@slave1</span> ~]<span class="hljs-comment"># passwd hadoop</span>
Changing password <span class="hljs-keyword">for</span> user hadoop.
New <span class="hljs-attribute">password</span>: 
BAD <span class="hljs-attribute">PASSWORD</span>: The password <span class="hljs-keyword">is</span> shorter than <span class="hljs-number">8</span> characters
Retype <span class="hljs-keyword">new</span> <span class="hljs-attribute">password</span>: 
<span class="hljs-attribute">passwd</span>: all authentication tokens updated successfully.
[root<span class="hljs-property">@slave1</span> ~]<span class="hljs-comment"># </span>
</code></pre>



<h3 id="关闭防火墙">关闭防火墙</h3>

<p>关闭防火墙是必须的,centos7默认是不用iptables</p>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-built_in">sudo</span> systemctl stop firewalld.service &amp;&amp; <span class="hljs-built_in">sudo</span>  systemctl disable firewalld.service</code></pre>



<h3 id="配置hosts文件">配置hosts文件</h3>

<p>注意： <strong><em>==sudo==</em></strong> 命名为使用超级管理员用户操作 相当于 root 用户</p>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@localhost</span> ~]<span class="hljs-variable">$ </span>sudo vim /etc/hosts
</code></pre>

<p>问题：若无法使用sudo命令？</p>



<pre class="prettyprint"><code class=" hljs applescript">hadoop <span class="hljs-keyword">is</span> <span class="hljs-keyword">not</span> <span class="hljs-keyword">in</span> <span class="hljs-keyword">the</span> sudoers <span class="hljs-type">file</span>.  This incident will be reported.
</code></pre>

<p>需要在 /etc/sudoers文件中添加配置</p>



<pre class="prettyprint"><code class=" hljs vhdl">## Allow root <span class="hljs-keyword">to</span> run any commands anywhere 
root    <span class="hljs-keyword">ALL</span>=(<span class="hljs-keyword">ALL</span>)   <span class="hljs-keyword">ALL</span>
hadoop <span class="hljs-keyword">ALL</span>=(<span class="hljs-keyword">ALL</span>) <span class="hljs-keyword">ALL</span>
</code></pre>

<p>hosts文件中添加内容为(其他主机同样添加)：</p>



<pre class="prettyprint"><code class=" hljs ">192.168.1.122 master
192.168.1.123 slave1
192.168.1.124 slave2
192.168.1.125 slave3
</code></pre>



<h2 id="配置ssh-免登陆">配置SSH 免登陆</h2>

<p>这里需要对 所有机器上的 ==hadoop@主机== 用户配置ssh免密码登录</p>

<p>在Centos7中已经默认安装ssh服务，ssh-keygen命令无效，说明需要先安装ssh服务,即：</p>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@master</span> /]<span class="hljs-variable">$ </span>sudo yum install openssh-server</code></pre>



<h3 id="以master主机为例">以==master==主机为例.</h3>

<ul>
<li>生成无密码密钥对,在命令中一路回车即可</li>
</ul>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@master</span> /]<span class="hljs-variable">$ </span>ssh-keygen</code></pre>

<ul>
<li>### 将公钥写入公钥库中,这里公钥库使用默认库名 authorized_keys</li>
</ul>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@master</span> /]<span class="hljs-variable">$ </span> cat ~<span class="hljs-regexp">/.ssh/id</span>_rsa.pub &gt;&gt; ~<span class="hljs-regexp">/.ssh/authorized</span>_keys
</code></pre>

<p>查看.ssh中内容</p>



<pre class="prettyprint"><code class=" hljs avrasm">[hadoop@master ~]$ cd ~/<span class="hljs-preprocessor">.ssh</span>
[hadoop@master <span class="hljs-preprocessor">.ssh</span>]$ ls
authorized_keys  id_rsa  id_rsa<span class="hljs-preprocessor">.pub</span>  known_hosts
</code></pre>

<ul>
<li>设置公钥库权限</li>
</ul>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@master</span> /]<span class="hljs-variable">$ </span>chmod <span class="hljs-number">600</span> authorized_keys </code></pre>

<ul>
<li>重启ssh服务 (root权限)</li>
</ul>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@master</span> /]<span class="hljs-variable">$ </span>sudo service sshd restart
</code></pre>

<p><strong>其他主机，即slave1,slave2,slave3，分别执行 ssh-keygen生成密钥对</strong></p>

<ul>
<li>将maser主机中的ssh分别发至 slave1,slave2,slave3主机</li>
</ul>

<p>其中 slave1@slave1 即 xx@yy xx代表用户名，yy代表主机名</p>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@master</span> /]<span class="hljs-variable">$ </span>scp ~<span class="hljs-regexp">/.ssh/</span>* hadoop<span class="hljs-variable">@slave1</span><span class="hljs-symbol">:~/</span>.ssh/
[hadoop<span class="hljs-variable">@master</span> /]<span class="hljs-variable">$ </span>scp ~<span class="hljs-regexp">/.ssh/</span>* hadoop<span class="hljs-variable">@slave2</span><span class="hljs-symbol">:~/</span>.ssh/
[hadoop<span class="hljs-variable">@master</span> /]<span class="hljs-variable">$ </span>scp ~<span class="hljs-regexp">/.ssh/</span>* hadoop<span class="hljs-variable">@slave3</span><span class="hljs-symbol">:~/</span>.ssh/
</code></pre>

<p>重启其他主机的ssh服务</p>

<ul>
<li>测试</li>
</ul>

<p>主机之间第一次访问时，会需要接受ESDSA可以，yes接受即可，再次互相访问时，就就不在需要了。</p>



<pre class="prettyprint"><code class=" hljs applescript">[hadoop@slave3 ~]$ ssh master
The authenticity <span class="hljs-keyword">of</span> host 'master (<span class="hljs-number">192.168</span><span class="hljs-number">.1</span><span class="hljs-number">.122</span>)' can't be established.
ECDSA key fingerprint <span class="hljs-keyword">is</span> <span class="hljs-number">3</span>b:<span class="hljs-number">14</span>:<span class="hljs-number">33</span>:d5:<span class="hljs-number">5</span>a:c4:<span class="hljs-number">92</span>:c5:<span class="hljs-number">62</span>:<span class="hljs-number">65</span>:<span class="hljs-number">49</span>:<span class="hljs-number">3</span>d:<span class="hljs-number">4</span>f:<span class="hljs-number">90</span>:<span class="hljs-number">07</span>:<span class="hljs-number">16.</span>
Are you sure you want <span class="hljs-keyword">to</span> <span class="hljs-keyword">continue</span> connecting (yes/no)? yes
Warning: Permanently added 'master,<span class="hljs-number">192.168</span><span class="hljs-number">.1</span><span class="hljs-number">.122</span>' (ECDSA) <span class="hljs-keyword">to</span> <span class="hljs-keyword">the</span> <span class="hljs-type">list</span> <span class="hljs-keyword">of</span> known hosts.
Last login: Sun Nov  <span class="hljs-number">6</span> <span class="hljs-number">06</span>:<span class="hljs-number">03</span>:<span class="hljs-number">47</span> <span class="hljs-number">2016</span> <span class="hljs-keyword">from</span> <span class="hljs-number">192.168</span><span class="hljs-number">.1</span><span class="hljs-number">.105</span>
[hadoop@master ~]$ ssh slave1
Last login: Sun Nov  <span class="hljs-number">6</span> <span class="hljs-number">06</span>:<span class="hljs-number">05</span>:<span class="hljs-number">12</span> <span class="hljs-number">2016</span> <span class="hljs-keyword">from</span> <span class="hljs-number">192.168</span><span class="hljs-number">.1</span><span class="hljs-number">.105</span>
[hadoop@slave1 ~]$ ssh slave2
Last login: Sun Nov  <span class="hljs-number">6</span> <span class="hljs-number">06</span>:<span class="hljs-number">11</span>:<span class="hljs-number">48</span> <span class="hljs-number">2016</span> <span class="hljs-keyword">from</span> <span class="hljs-number">192.168</span><span class="hljs-number">.1</span><span class="hljs-number">.105</span>
[hadoop@slave2 ~]$ ssh slave3
</code></pre>

<p>maser,slave1,slave2,slave3 四台主机之间都可以无密码访问，即是ssh设置成功了！</p>



<h2 id="配置jdk">配置JDK</h2>

<p>这里对==master==主机进行配置 将jdk等软件上传至/opt/目录：</p>

<ul>
<li>使用<em>超级管理员</em>用户解压 jdk</li>
</ul>



<pre class="prettyprint"><code class=" hljs lasso">sudo tar <span class="hljs-attribute">-zxvf</span> jdk<span class="hljs-subst">-</span><span class="hljs-number">7</span>u67<span class="hljs-attribute">-linux</span><span class="hljs-attribute">-x64</span><span class="hljs-built_in">.</span>tar<span class="hljs-built_in">.</span>gz </code></pre>

<p>配置jdk环境变量 /etc/profile，并查看是否成功</p>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-keyword">export</span> JAVA_HOME=/opt/jdk1.<span class="hljs-number">7.0</span>_67/
<span class="hljs-keyword">export</span> JRE_HOME=/opt/jdk1.<span class="hljs-number">7.0</span>_67/jre
<span class="hljs-keyword">export</span> CLASSPATH=.:<span class="hljs-variable">$CLASSPATH</span>:<span class="hljs-variable">$JAVA_HOME</span>/lib:<span class="hljs-variable">$JRE_HOME</span>/lib
<span class="hljs-keyword">export</span> PATH=<span class="hljs-variable">$PATH</span>:<span class="hljs-variable">$JAVA_HOME</span>/bin:<span class="hljs-variable">$JRE_HOME</span>/bin
</code></pre>

<p>注意，上面配置中，最后一行，$PATH放在前面不生效时，也可以这么写：</p>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-keyword">export</span> PATH=<span class="hljs-variable">$JAVA_HOME</span>/bin:<span class="hljs-variable">$JRE_HOME</span>/bin：<span class="hljs-variable">$PATH</span>
</code></pre>

<p>重新加载</p>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-built_in">source</span> /etc/profile</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>