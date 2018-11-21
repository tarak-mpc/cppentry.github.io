---
layout:     post
title:      python访问Hive配置
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1 id="title-heading" class="pagetitle" style="line-height:2;font-size:24px;font-family:Arial, Helvetica, FreeSans, sans-serif;text-align:left;">
</h1><div class="wiki-content" style="font-size:13px;line-height:17px;color:rgb(51,51,51);font-family:Arial, Helvetica, FreeSans, sans-serif;text-align:left;">
<h3 id="python访问Hive配置-hive有三种启动方式：" style="line-height:normal;font-size:1.4em;color:rgb(0,0,0);">
hive有三种启动方式：</h3>
<ol style="font-size:10pt;line-height:13pt;margin-left:0px;list-style-position:outside;"><li style="font-size:10pt;line-height:13pt;"><span> hive  命令行模式，直接输入/hive/bin/hive的执行程序，或者输入 hive –service cli  </span>用于linux平台命令行查询，查询语句基本跟mysql查询语句类似</li><li style="font-size:10pt;line-height:13pt;">
<p style="font-size:10pt;line-height:13pt;background-color:transparent;">
hive  <a href="http://www.haogongju.net/tag/web" rel="nofollow" title="web" class="external-link" style="text-decoration:none;color:rgb(0,109,175);">web</a><a href="http://www.haogongju.net/tag/%E7%95%8C%E9%9D%A2" rel="nofollow" title="界面" class="external-link" style="text-decoration:none;color:rgb(0,109,175);">界面</a>的启动方式，hive
 –service hwi  用于通过<a href="http://www.haogongju.net/tag/%E6%B5%8F%E8%A7%88%E5%99%A8" rel="nofollow" title="浏览器" class="external-link" style="text-decoration:none;color:rgb(0,109,175);">浏览器</a>来访问hive。可以分离查询的执行，同时执行多个查询；不用本地安装hive</p>
</li><li style="font-size:10pt;line-height:13pt;"><span>hive  远程服务 (端口号10000) 启动方式,</span><span style="color:rgb(0,0,0);">hive以thrift服务的服务器形式运行，允许不同的语言编写客户端进行访问，通过thrift，jdbc，odbc连接器和hive服务器与hive通信，这种方式很适合java、python编程人员通过jdbc接口去访问hive。</span></li></ol><h4 id="python访问Hive配置-这里实现第三种方式，首先要安装thrift" style="line-height:normal;font-size:1.2em;color:rgb(0,0,0);">
这里实现第三种方式，首先要安装thrift</h4>
<p style="font-size:10pt;line-height:13pt;background-color:transparent;">
<span style="color:rgb(0,0,0);"> </span></p>
<div>Thrift是一个跨语言服务部署框架，最初由Facebook于2007年开发，后于2008年进入Apache孵化器(Apache Incubator)。类似于SOAP，COM 和CORBA，Thrift通过定义一个中间定义语言和Thrift代码生成工具，生成指定语言的代码。目前，Thrift</div>
<div>支持C++,Java, Python, PHP, Ruby, Erlang, Perl, Haskell, C#, Cocoa, Smalltalk和OCaml的代码生成。</div>
<div><span style="color:rgb(34,34,34);">在这里我们用的是thrift的另一个应用，</span>不是生成指定语言的代码，而是通过<span style="color:rgb(34,34,34);"><span style="color:rgb(0,0,0);">Thrift Gateway，利用Thrift序列化技术（支持C++，PHP，Python等多种语言）适合其他异构系统调用hive，在我们这用</span>python客户端通过</span>thrift<span style="color:rgb(34,34,34);">调用</span>hive（将hive相关的python语句放与hive服务器）</div>
<p style="font-size:10pt;line-height:13pt;background-color:transparent;">
<span style="color:rgb(0,0,0);">thrift主页：<a href="http://thrift.apache.org/" rel="nofollow" class="external-link" style="color:rgb(0,109,175);">http://thrift.apache.org/</a>，上面有详细的安装说明。</span></p>
<ol style="font-size:10pt;line-height:13pt;margin-left:0px;list-style-position:outside;"><li style="font-size:10pt;line-height:13pt;"><span style="color:rgb(0,0,0);">到thrift主页下载最新版本thrift.<a href="http://thrift.apache.org/download/" rel="nofollow" class="external-link" style="color:rgb(0,109,175);">http://thrift.apache.org/download/</a>   <a href="https://dist.apache.org/repos/dist/release/thrift/0.8.0/thrift-0.8.0.tar.gz" rel="nofollow" class="external-link" style="text-decoration:none;color:rgb(0,109,175);">thrift-0.8.0.tar.gz</a> <br></span><span style="color:rgb(0,0,0);">直接解压</span><br><span style="color:rgb(0,0,0);">tar zxvf <a href="https://dist.apache.org/repos/dist/release/thrift/0.8.0/thrift-0.8.0.tar.gz" rel="nofollow" class="external-link" style="text-decoration:none;color:rgb(0,109,175);">thrift-0.8.0.tar.gz</a> </span></li><li style="font-size:10pt;line-height:13pt;"><span style="color:rgb(0,0,0);">出现错误:This does not look like a tar archive</span>
<div><span style="color:rgb(0,0,0);">这是个BUG </span></div>
<span style="color:rgb(0,0,0);"> </span><span style="color:rgb(0,0,0);">解决办法: </span>
<div><span style="color:rgb(0,0,0);">               gzip -d xxxx.tar.gz （对于.tar.gz文件的处理方式）<br>
               tar -xf xxxx.tar    （对于.tar文件处理方式）</span></div>
</li><li style="font-size:10pt;line-height:13pt;"><span style="color:rgb(0,0,0);"> </span>
<div><span style="color:rgb(0,0,0);">系统必备包和工具安装，按照 </span><a href="http://thrift.apache.org/docs/install/ubuntu/" rel="nofollow" class="external-link" style="color:rgb(0,109,175);">http://thrift.apache.org/docs/install/ubuntu/</a> 上所说，安装必备工具和包</div>
<span style="color:rgb(0,0,0);"> </span>
<div>$:sudo apt-get install libboost-dev libboost-test-dev libboost-program-options-dev libevent-dev automake libtool flex bison pkg-config g++ libssl-dev </div>
<div>python必备包安装：</div>
<div>$:python-dev python-twisted</div>
</li><li style="font-size:10pt;line-height:13pt;"><span style="color:rgb(0,0,0);">安装thrift <a href="http://thrift.apache.org/docs/BuildingFromSource/" rel="nofollow" class="external-link" style="color:rgb(0,109,175);">http://thrift.apache.org/docs/BuildingFromSource/</a></span><br><pre style="overflow:visible;">直接运行</pre>
<pre style="overflow:visible;">$:./configure</pre>
<pre style="overflow:visible;">不用运行</pre>
<pre style="overflow:visible;"><code>./bootstrap.sh </code></pre>
<pre style="overflow:visible;"><code>$:make &amp; make install</code></pre>
</li></ol><p style="font-size:10pt;line-height:13pt;background-color:transparent;">
<span style="color:rgb(0,0,0);">thrift安装完毕。</span></p>
<h3 id="python访问Hive配置- thrift的lib包加入python的lib下" style="line-height:normal;font-size:1.4em;color:rgb(0,0,0);">
 thrift的lib包加入python的lib下</h3>
<p style="font-size:10pt;line-height:13pt;background-color:transparent;">
 </p>
<div class="codeheads">
<p style="font-size:10pt;line-height:13pt;background-color:transparent;">
运行<br>
$： python  <br>
&gt;&gt;&gt;from hive_service import<br>
 &gt;&gt;&gt;ThritHiveImportError: cannot import name ThritHive<br><span style="color:rgb(0,0,0);">查看之后，才发现在执行了第（1）步后，python2.7/site-packages中并没有相应模块，这样当然是不可能通过编译的，所以到刚才解压后的根目录，将build/lib中的所有模块都拷贝到python2.7/site-packages中，如下：</span>有两种方式：</p>
<ol style="font-size:10pt;line-height:13pt;margin-left:0px;list-style-position:outside;"><li style="font-size:10pt;line-height:13pt;">$:cd  .../thrift-0.8.0/lib/py/build/lib.linux-x86_64-2.7<br>
cp -r thrift/   /usr/lib/python2.7/site-packages/</li><li style="font-size:10pt;line-height:13pt;">python插件方式<br><img class="confluence-embedded-image" src="http://wiki.yunat.com/download/attachments/13402342/thrift_python.png?version=1&amp;modificationDate=1346743979428" alt="" style="border:1px solid transparent;"></li></ol></div>
<p style="font-size:10pt;line-height:13pt;background-color:transparent;">
 </p>
<h3 id="python访问Hive配置-eclipse下python写hive程序设置" style="line-height:normal;font-size:1.4em;color:rgb(0,0,0);">
eclipse下python写hive程序设置</h3>
<p style="font-size:10pt;line-height:13pt;background-color:transparent;">
在菜单项windonw－&gt;preferences下</p>
<p style="font-size:10pt;line-height:13pt;background-color:transparent;">
 <img class="confluence-embedded-image" width="500" src="http://wiki.yunat.com/download/attachments/13402342/eclipse_python_hive.png?version=1&amp;modificationDate=1346744359440" alt="" style="border:1px solid transparent;"></p>
<p style="font-size:10pt;line-height:13pt;background-color:transparent;">
 </p>
<p style="font-size:10pt;line-height:13pt;background-color:transparent;">
<img class="confluence-embedded-image" width="500" src="http://wiki.yunat.com/download/attachments/13402342/eclispe_python_hive.png?version=1&amp;modificationDate=1346744649583" alt="" style="border:1px solid transparent;"></p>
<p style="font-size:10pt;line-height:13pt;background-color:transparent;">
添加thrift for python 和 thrift for hive</p>
<p style="font-size:10pt;line-height:13pt;background-color:transparent;">
<span style="color:rgb(0,0,0);">oK</span></p>
<div><span style="color:rgb(0,0,0);"><br></span></div>
</div>
<br>
            </div>
                </div>