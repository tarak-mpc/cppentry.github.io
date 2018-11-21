---
layout:     post
title:      安装Hbase Thrift C++ Client
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div>
<div>
<table bgcolor="#D4DDE5" border="0" width="100%"><tbody><tr><td>
<h1>安装HBase Thrift For C++ Client</h1>
<strong>Reference:</strong> <a href="https://github.com/ypf412/hbase-thrift" rel="nofollow">
https://github.com/ypf412/hbase-thrift</a></td>
</tr></tbody></table></div>
<div>
<div style="background-color:rgb(255,255,255);"><br></div>
<h1 style="background-color:rgb(255,255,255);"><span style="font-size:14px;">简介</span></h1>
<div style="background-color:rgb(255,255,255);">This is a common C++ client that accesses HBase cluster through HBase ThriftServer. It based on HBase thrift, but not the new thrift interface in HBase 0.94+ version.<br><div>
<div><strong><br></strong>
<h1><strong><span style="font-size:14px;">测试安装环境</span></strong></h1>
</div>
<div><span style="font-size:18px;">  </span><span style="font-size:12px;">CentOS 6.3 64bit</span></div>
</div>
<br></div>
<div style="background-color:rgb(255,255,255);"><br></div>
<h1 style="background-color:rgb(255,255,255);"><strong><span style="font-size:14px;">前期准备</span></strong></h1>
<h2 style="background-color:rgb(255,255,255);"><span style="font-size:14px;">安装Hbase</span></h2>
<div style="background-color:rgb(255,255,255);"> [详略]</div>
<div style="background-color:rgb(255,255,255);"><br></div>
<h2 style="background-color:rgb(255,255,255);"><strong><span style="font-family:SimSun;font-size:14px;">安装Thrift</span></strong></h2>
<div style="background-color:rgb(255,255,255);">   (1) 下载，解压和安装<a href="https://dist.apache.org/repos/dist/release/thrift/0.8.0/thrift-0.8.0.tar.gz" rel="nofollow">thrift-0.8.0</a></div>
<pre><code class="language-plain"> # wget https://dist.apache.org/repos/dist/release/thrift/0.8.0/thrift-0.8.0.tar.gz
 # tar zxvf thrift-0.8.0.tar.gz
 # sudo yum -y install automake libtool flex bison pkgconfig gcc-c++ boost-devel libevent-devel zlib-devel python-devel ruby-devel
 # cd thrift-0.8.0
 # ./configure
 # make
 # sudo make install</code></pre></div>
<div>
<pre>-------------------------------------------------
</pre><div><pre style="font-size:12px;">【注】 在configure时若出错："Error: libcrypto required."
</pre><pre style="font-size:12px;">OpenStack解决：
</pre><pre style="font-size:12px;">- CentOS/Fedora/RHEL平台      </pre><pre><code class="language-plain"># yum -y install  openssl-devel </code></pre><br><pre style="font-size:12px;">- Debian/Ubuntu平台：     </pre><pre style="font-size:12px;"></pre><pre><code class="language-plain"># apt-get install libssl-dev</code></pre><pre>-------------------------------------------------</pre></div> 
</div>
<blockquote></blockquote>
<div style="background-color:rgb(255,255,255);">    (2) 使用thrift编译生成HBase cpp模块: </div>
<div style="background-color:rgb(255,255,255);">
<pre></pre><pre><code class="language-plain"># thrift --gen cpp [hbase-root]/src/main/resources/org/apache/hadoop/hbase/thrift/Hbase.thrift</code></pre>
<pre>   <span style="font-family:'微软雅黑';">过程没有输出信息，检验结果查看生成的文件：</span>         </pre><pre><code class="language-plain"># ls [hbase-root]src/main/resources/org/apache/hadoop/hbase/thrift/gen-cpp</code></pre>
<pre>            <span style="color:#1C3387;">Hbase_constants.cpp  Hbase.cpp  Hbase_server.skeleton.cpp  Hbase_types.h
</span></pre>
<pre><span style="color:#1C3387;">            Hbase_constants.h    Hbase.h    Hbase_types.cpp

</span></pre>
<h2><strong><span style="font-family:SimSun;font-size:14px;">启动与关闭thrift服务</span></strong></h2>
<pre><span style="font-family:'微软雅黑';">        开启ThriftServer服务：
</span></pre><pre><code class="language-plain"> # $HBASE_HOME/bin/hbase-daenom.sh start thrift</code></pre>              <span style="color:#333399;">starting thrift, logging to /opt/hbase-0.94.10/logs/hbase-root-thrift-node03.rsclouds.cn.out</span>
</div>
</div>
<div>
<div style="background-color:rgb(255,255,255);">
<pre>      <span style="font-family:'微软雅黑';">Thrift Server默认端口号是9090，搜索端口进程，可以检查启动情况：</span>    </pre><pre><code class="language-plain"># lsof -i:9090</code></pre><span style="color:#000099;">        <span style="font-size:10px;">      COMMAND  PID USER   FD   TYPE  DEVICE SIZE/OFF NODE NAME<br>                java    6712 root  134u  IPv6 2295851      0t0  TCP *:websm (LISTEN</span></span>
<pre>      停止ThriftServer:
</pre><pre><code class="language-plain"># $HBASE_HOME/bin/hbase-daemon.sh stop thrift</code></pre><br><br><h2><strong><span style="font-size:14px;">更多资料</span></strong></h2><div><pre><span style="font-size:12px;"><a href="http://wiki.apache.org/thrift/ThriftUsageC%2B%2B" rel="nofollow">ThriftUsageC++</a></span></pre><pre><span style="font-size:12px;"><a href="http://wiki.apache.org/thrift/ThriftRequirements" rel="nofollow">ThriftRequirements</a></span></pre><pre><span style="font-size:12px;"><a href="http://wiki.apache.org/thrift/GettingCentOS5Packages" rel="nofollow">Installing the required packages on CentOS 5</a></span></pre></div><br><br>
<pre>     
</pre>
<pre></pre>
<pre></pre>
</div>
</div>
<br><div></div>
<br>            </div>
                </div>