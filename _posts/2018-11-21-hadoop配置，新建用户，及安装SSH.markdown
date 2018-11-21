---
layout:     post
title:      hadoop配置，新建用户，及安装SSH
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>新建用户组</p>
<p></p>
<pre><code class="language-plain">sudo addgroup hadoop</code></pre><br>
创建hadoop用户
<p></p>
<p></p>
<pre><code class="language-plain">sudo adduser -ingroup hadoop hadoop</code></pre><br><p>给予hadoop用户所有权限</p>
<p></p>
<pre><code class="language-plain">sudo gedit /etc/sudoers</code></pre><br>
在打开的文件中，<span style="background-color:rgb(245,245,245);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;">在root   ALL=(ALL:ALL)   ALL下添加hadoop   ALL=(ALL:ALL)  ALL</span>
<p></p>
<p><span style="background-color:rgb(245,245,245);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;"></span></p>
<pre><code class="language-plain">hadoop  ALL=(ALL:ALL) ALL</code></pre>以hadoop用户登录
<p></p>
<p><span style="background-color:rgb(245,245,245);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;"></span></p>
<pre><code class="language-plain">su - hadoop</code></pre><br>
安装SSH
<p></p>
<p><span style="background-color:rgb(245,245,245);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;"></span></p>
<pre><code class="language-plain">sudo apt-get install ssh</code></pre><br>
生成当前用户的密钥文件
<p></p>
<p><span style="background-color:rgb(245,245,245);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;"></span></p>
<pre><code class="language-plain">ssh-keygen -t dsa -P '' -f ~/.ssh/id_dsa</code></pre>
<p><br></p>
<p>把客户端的生成的公钥文件上传到服务器端</p>
<pre><code class="language-plain">~/.ssh/id_dsa.pub</code></pre>在服务器端，把公钥，加入到授权文件中去
<p></p>
<p><span style="background-color:rgb(245,245,245);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;"></span></p>
<pre><code class="language-plain">cat ~/.ssh/id_dsa.pub&gt;&gt;~/.ssh/authorized_keys</code></pre>
<p>在服务器端更改文件夹的权限：</p>
<p></p><pre><code class="language-plain">chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys</code></pre><br><p>如果是centos系统，默认安装了ssh的服务器端，而没有安装客户端，还需执行以下命令</p>
<p></p>
<pre><code class="language-plain">yum install openssh-clients</code></pre><br><p></p>
<p>验证SSH是否安装成功</p>
<p></p>
<p><span style="background-color:rgb(245,245,245);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;"></span></p>
<pre><code class="language-plain">ssh -version</code></pre><br>
输出如下
<p></p>
<p><span style="background-color:rgb(245,245,245);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;"></span></p>
<pre><code class="language-plain">OpenSSH_5.9p1 Debian-5ubuntu1, OpenSSL 1.0.1 14 Mar 2012
Bad escape character 'rsion'.</code></pre><br>
安装成功之后，可使用以下命令进行登录
<p></p>
<p><span style="background-color:rgb(245,245,245);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;"></span></p>
<pre><code class="language-plain">ssh localhost</code></pre><br>
第一次登录会显示是否连接，输入yes就OK。<br><br><p></p>
            </div>
                </div>