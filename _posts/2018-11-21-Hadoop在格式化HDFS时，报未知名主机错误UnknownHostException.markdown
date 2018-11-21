---
layout:     post
title:      Hadoop在格式化HDFS时，报未知名主机错误UnknownHostException
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p></p><pre><code class="language-html">bin/hdfs namenode -format</code></pre>在执行hdfs格式化时，报一下错误：
<p></p><pre><code class="language-html">java.net.UnknownHostException: localhost.localdomain: localhost.localdomain
        at java.net.InetAddress.getLocalHost(InetAddress.java:1353)  
        at org.apache.hadoop.metrics.MetricsUtil.getHostName(MetricsUtil.java:91)  
        at org.apache.hadoop.metrics.MetricsUtil.createRecord(MetricsUtil.java:80)
</code></pre>
<p>产生此问题的原因：Hadoop在格式化HDFS的是时候，通过hostname指定的主机名  在/etc/hosts文件中映射的时候未找到。</p>
解决办法：
<p>查看当前主机名：#hostname</p>
<p>在 /etc/hosts 中修改：</p>
<p>127.0.0.1                localhost <br>
192.168.140.130          <span style="color:#ff0000;"> localhost </span></p>
<p>或在/etc/sysconfig/network中修改hostname：</p>
<p>NETWORKING=yes<br>
HOSTNAME=<span style="color:#ff0000;">localhost</span><br>
修改完成后，重启网络即可。</p>
<p></p><pre><code class="language-html"># /etc/rc.d/init.d/network restart</code></pre>
<p><br></p>
<p>最后，就可以重新格式化HDFS，启动bin/start-dfs.sh。</p>
<p></p>
[root@localhost hadoop-2.5.0]# jps<br>
5865 DataNode<br>
6104 SecondaryNameNode<br>
6251 Jps<br>
5751 NameNode
<p><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
            </div>
                </div>