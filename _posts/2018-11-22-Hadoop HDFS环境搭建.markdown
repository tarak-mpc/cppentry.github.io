---
layout:     post
title:      Hadoop HDFS环境搭建
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h4 id="二安装jdkhadoop及配置环境变量" style="font-family:'microsoft yahei';font-weight:300;line-height:1.1;color:rgb(63,63,63);font-size:1.25em;">
<span>二、安装JDK、Hadoop及配置环境变量</span></h4>
<p style="color:rgb(63,63,63);font-family:'microsoft yahei';font-size:15px;">
安装，解压JDK到<code style="font-family:'Source Code Pro', monospace;font-size:13.5px;">/usr/lib/java/</code>路径下，Hadoop到<code style="font-family:'Source Code Pro', monospace;font-size:13.5px;">/usr/local/etc/hadoop/</code>路径下：</p>
<pre class="prettyprint" style="font-family:'Source Code Pro', monospace;font-size:14px;line-height:1.45;color:rgb(51,51,51);background-color:rgba(128,128,128,.05);border:0px solid rgb(136,136,136);"><code class="language-bash hljs has-numbering" style="display:block;background:transparent;color:inherit;font-family:'Source Code Pro', monospace;">    tar zxf ./hadoop-<span class="hljs-number" style="color:rgb(0,102,102);">2.6</span>.*.tar.gz 
    mv ./hadoop-<span class="hljs-number" style="color:rgb(0,102,102);">2.6</span>.* /usr/local/etc/hadoop <span class="hljs-comment" style="color:rgb(136,0,0);"># 将 /usr/local/etc/hadoop作为Hadoop的安装路径</span></code></pre><ul class="pre-numbering" style="width:50px;background-color:rgb(238,238,238);border-right:1px solid rgb(221,221,221);list-style:none;text-align:right;"><li style="list-style:none;margin-left:0px;">1</li><li style="list-style:none;margin-left:0px;">2</li></ul><p style="color:rgb(63,63,63);font-family:'microsoft yahei';font-size:15px;">
解压完成之后，可验证hadoop的可用性：</p>
<pre class="prettyprint" style="font-family:'Source Code Pro', monospace;font-size:14px;line-height:1.45;color:rgb(51,51,51);background-color:rgba(128,128,128,.05);border:0px solid rgb(136,136,136);"><code class="hljs livecodeserver has-numbering" style="display:block;background:transparent;color:inherit;font-family:'Source Code Pro', monospace;">    cd /usr/<span class="hljs-built_in" style="color:rgb(102,0,102);">local</span>/etc/hadoop
    ./bin/hadoop <span class="hljs-built_in" style="color:rgb(102,0,102);">version</span> <span class="hljs-comment" style="color:rgb(136,0,0);"># 查看hadoop的版本信息</span></code></pre><ul class="pre-numbering" style="width:50px;background-color:rgb(238,238,238);border-right:1px solid rgb(221,221,221);list-style:none;text-align:right;"><li style="list-style:none;margin-left:0px;">1</li><li style="list-style:none;margin-left:0px;">2</li></ul><p style="color:rgb(63,63,63);font-family:'microsoft yahei';font-size:15px;">
<span>若在此处，会出现类似以下的错误信息，则很有可能是该安装包有问题。</span></p>
<pre class="prettyprint" style="font-family:'Source Code Pro', monospace;font-size:14px;line-height:1.45;color:rgb(51,51,51);background-color:rgba(128,128,128,.05);border:0px solid rgb(136,136,136);"><code class="hljs avrasm has-numbering" style="display:block;background:transparent;color:inherit;font-family:'Source Code Pro', monospace;">    Error: Could not find <span class="hljs-keyword" style="color:rgb(0,0,136);">or</span> load main class org<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.apache</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.hadoop</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.util</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.VersionInfo</span></code></pre><ul class="pre-numbering" style="width:50px;background-color:rgb(238,238,238);border-right:1px solid rgb(221,221,221);list-style:none;text-align:right;"><li style="list-style:none;margin-left:0px;">1</li></ul><p style="color:rgb(63,63,63);font-family:'microsoft yahei';font-size:15px;">
<span style="font-size:16px;font-family:Verdana, Arial, Helvetica, sans-serif;">第三步：配置系统环境</span></p>
<p style="color:rgb(63,63,63);font-family:'microsoft yahei';font-size:15px;">
</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;">
这一步我们需要告诉Linux，我们装的东西在哪里，装了哪些命令</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;">
配置系统环境变量（vim /etc/bash.bashrc）</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;">
在文件末尾追加：</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;">
<span class="hljs-keyword" style="font-family:'Source Code Pro', monospace;font-size:14px;color:rgb(0,0,136);">export</span><span style="color:rgb(51,51,51);font-family:'Source Code Pro', monospace;font-size:14px;"> HADOOP_HOME=/usr/local/etc/hadoop</span><span class="hljs-keyword" style="font-family:'Source Code Pro', monospace;font-size:14px;color:rgb(0,0,136);">export</span><span style="color:rgb(51,51,51);font-family:'Source Code Pro', monospace;font-size:14px;">
 JAVA_HOME=/usr/lib/java/jdk1.</span><span class="hljs-number" style="font-family:'Source Code Pro', monospace;font-size:14px;color:rgb(0,102,102);">8.0</span><span style="color:rgb(51,51,51);font-family:'Source Code Pro', monospace;font-size:14px;">_45</span><span class="hljs-keyword" style="font-family:'Source Code Pro', monospace;font-size:14px;color:rgb(0,0,136);">export</span><span style="color:rgb(51,51,51);font-family:'Source Code Pro', monospace;font-size:14px;">
 JRE_HOME=</span><span class="hljs-variable" style="font-family:'Source Code Pro', monospace;font-size:14px;color:rgb(102,0,102);">${JAVA_HOME}</span><span style="color:rgb(51,51,51);font-family:'Source Code Pro', monospace;font-size:14px;">/jre</span><span class="hljs-keyword" style="font-family:'Source Code Pro', monospace;font-size:14px;color:rgb(0,0,136);">export</span><span style="color:rgb(51,51,51);font-family:'Source Code Pro', monospace;font-size:14px;">
 CLASSPATH=.:</span><span class="hljs-variable" style="font-family:'Source Code Pro', monospace;font-size:14px;color:rgb(102,0,102);">${JAVA_HOME}</span><span style="color:rgb(51,51,51);font-family:'Source Code Pro', monospace;font-size:14px;">/lib:</span><span class="hljs-variable" style="font-family:'Source Code Pro', monospace;font-size:14px;color:rgb(102,0,102);">${JRE_HOME}</span><span style="color:rgb(51,51,51);font-family:'Source Code Pro', monospace;font-size:14px;">/lib</span><span class="hljs-keyword" style="font-family:'Source Code Pro', monospace;font-size:14px;color:rgb(0,0,136);">export</span><span style="color:rgb(51,51,51);font-family:'Source Code Pro', monospace;font-size:14px;">
 PATH=</span><span class="hljs-variable" style="font-family:'Source Code Pro', monospace;font-size:14px;color:rgb(102,0,102);">$PATH</span><span style="color:rgb(51,51,51);font-family:'Source Code Pro', monospace;font-size:14px;">:</span><span class="hljs-variable" style="font-family:'Source Code Pro', monospace;font-size:14px;color:rgb(102,0,102);">${JAVA_HOME}</span><span style="color:rgb(51,51,51);font-family:'Source Code Pro', monospace;font-size:14px;">/bin:</span><span class="hljs-variable" style="font-family:'Source Code Pro', monospace;font-size:14px;color:rgb(102,0,102);">${JRE_HOME}</span><span style="color:rgb(51,51,51);font-family:'Source Code Pro', monospace;font-size:14px;">/bin:</span><span class="hljs-variable" style="font-family:'Source Code Pro', monospace;font-size:14px;color:rgb(102,0,102);">${HADOOP_HOME}</span><span style="color:rgb(51,51,51);font-family:'Source Code Pro', monospace;font-size:14px;">/bin:</span><span class="hljs-variable" style="font-family:'Source Code Pro', monospace;font-size:14px;color:rgb(102,0,102);">${HADOOP_HOME}</span><span style="color:rgb(51,51,51);font-family:'Source Code Pro', monospace;font-size:14px;">/sbin</span></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;">
</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;">
完成后，激活一下配置。执行命令：source /etc/bash.bashrc</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;">
验证jdk是否完成配置：java -version</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;">
<img src="http://images2017.cnblogs.com/blog/1177372/201708/1177372-20170811103209589-49192730.png" alt="" style="border:0px;"></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;">
没有问题！</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;">
另外两台机器，重复上述（也可以后期拷贝到另外两台机器上。见仁见智！）</p>
<br><p style="font-family:Verdana, Arial, Helvetica, sans-serif;">
</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;">
<span style="font-size:16px;"><span>第三步：配置SSH秘钥</span></span></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;">
我们使用的shj账号，配置的目的使得三台虚拟机能够通过该账号相互进行通信。（对root账号，不可行）</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;">
<span>chap1：生成ssh密钥对</span>（私钥和公钥）</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;">
  mkdir -p ~/.ssh（如果没有.ssh则生成一个，ubuntu的好像是没有的）</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;">
  ssh-keygen -t rsa（生成ssh密钥对，放在~/.ssh目录下）</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;">
　将生成的key_id.pub写入文件(authorized_keys）中，确保文件名没有问题。</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;">
<span>chap2：进行本机的免密登录：ssh localhost</span></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;">
<img src="http://images2017.cnblogs.com/blog/1177372/201708/1177372-20170810193600245-585657771.png" alt="" style="border:0px;"></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;">
如果报错，要求输入密码，则有问题（一般<span><span style="color:rgb(255,0,0);">重启</span></span>一下就可以了，如果还有问题，则可能是你的家目录和私钥的权限不对，导致系统不允许免密登录）</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;">
<br></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;">
</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;">
<span>chap3：</span></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;">
</p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">在ip为192.168.0.3主机终端中输入</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">sudo gedit  /etc/hosts</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">在最后键入</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">192.168.0.2 master</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;">
<span style="background-color:rgb(255,255,255);"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;">192.168.0.3 node</span><br></span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">保存退出。并在终端中键入</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">scp ~/.ssh/id_rsa.pub wuyi@192.168.0.2:/home/wuyi/</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">这里可能会让输入wuyi@192.168.0.2的主机密码，输入就可以了。</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">在ip为192.168.0.2的主机终端中键入</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">cat ~/id_rsa.pub &gt;&gt; ～/.ssh/authorized_keys</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">建立信任列表。然后回到192.168.0.3的主机上，终端键入</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">ssh hadoop@192.168.0.2</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">如果有输入密码的提示，直接输入ip为192.168.0.2的主机密码就可以了。实际上，如果你键入</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">ssh 192.168.0.2</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;">
<span style="background-color:rgb(255,255,255);"><br></span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;">
同理，在192.168.0.3机器上执行相同的操作</p>
<br>            </div>
                </div>