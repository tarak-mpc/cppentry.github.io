---
layout:     post
title:      hadoop eclipse  配置
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:18px;">重装系统后有折腾了好久，才搞定。。感觉还是不靠谱。。先记录下</span></p>
<p><span style="font-size:18px;">hadoop 伪分布式配置：</span></p>
<p><span style="font-size:18px;">1，hadoop版本<br></span></p>
<p><span style="font-size:18px;">hadoop@ubuntu:~/collector_3h/hadoop-0.20.2/bin$ ./hadoop version<br>
Hadoop 0.20.2<br>
Subversion https://svn.apache.org/repos/asf/hadoop/common/branches/branch-0.20 -r 911707<br>
Compiled by chrisdo on Fri Feb 19 08:07:34 UTC 2010<br></span></p>
<p><span style="font-size:18px;">2,配置文件 </span></p>
<p><span style="font-size:18px;"><span style="font-size:24px;"><strong>hdfs-site.xml</strong></span><br></span></p>
<p><span style="font-size:18px;">hadoop@ubuntu:~/collector_3h/hadoop-0.20.2/conf$ cat hdfs-site.xml
<br>
&lt;?xml version="1.0"?&gt;<br>
&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt;<br><br>
&lt;!-- Put site-specific property overrides in this file. --&gt;<br><br>
&lt;configuration&gt;<br>
    &lt;property&gt;<br>
        &lt;name&gt;dfs.replication&lt;/name&gt;<br>
        &lt;value&gt;1&lt;/value&gt;<br>
    &lt;/property&gt;<br><br>
     &lt;property&gt;  <br>
      &lt;name&gt;dfs.permissions&lt;/name&gt;  <br>
      &lt;value&gt;false&lt;/value&gt;  <br>
     &lt;/property&gt;  <br><br>
    &lt;property&gt;<br>
        &lt;name&gt;dfs.name.dir&lt;/name&gt;<br>
        &lt;value&gt;/home/hadoop/collector_3h/hadoop-0.20.2/hdfs/name&lt;/value&gt;<br>
    &lt;/property&gt;<br><br>
    &lt;property&gt;<br>
        &lt;name&gt;dfs.data.dir&lt;/name&gt;<br>
        &lt;value&gt;/home/hadoop/collector_3h/hadoop-0.20.2/hdfs/data&lt;/value&gt;<br>
    &lt;/property&gt;<br>
&lt;/configuration&gt;</span></p>
<p><span style="font-size:18px;">-----------------------</span></p>
<p><span style="font-size:18px;"><span style="font-size:24px;"><strong>mapred-site.xml</strong></span><br></span></p>
<p><span style="font-size:18px;">hadoop@ubuntu:~/collector_3h/hadoop-0.20.2/conf$ cat mapred-site.xml
<br>
&lt;?xml version="1.0"?&gt;<br>
&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt;<br><br>
&lt;!-- Put site-specific property overrides in this file. --&gt;<br><br>
&lt;configuration&gt;<br>
    &lt;property&gt;<br>
        &lt;name&gt;mapred.job.tracker&lt;/name&gt;<br>
        &lt;value&gt;localhost:9001&lt;/value&gt;<br>
    &lt;/property&gt;<br><br><br>
    &lt;property&gt; <br>
    &lt;name&gt;mapred.local.dir&lt;/name&gt; <br>
    &lt;value&gt;/home/hadoop/collector_3h/hadoop-0.20.2/mapred/local&lt;/value&gt; <br>
    &lt;/property&gt;  <br><br>
    &lt;property&gt; <br>
    &lt;name&gt;mapred.system.dir&lt;/name&gt; <br>
    &lt;value&gt;/home/hadoop/collector_3h/hadoop-0.20.2/mapred/system&lt;/value&gt; <br>
    &lt;/property&gt;<br><br>
&lt;/configuration&gt;<br></span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;">-<span style="font-size:24px;"><strong>-------------------------------------------</strong></span></span></p>
<p><span style="font-size:18px;"><span style="font-size:24px;"><strong>core-site.xml</strong></span><br>
hadoop@ubuntu:~/collector_3h/hadoop-0.20.2/conf$ cat core-site.xml <br>
&lt;?xml version="1.0"?&gt;<br>
&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt;<br><br>
&lt;!-- Put site-specific property overrides in this file. --&gt;<br><br>
&lt;configuration&gt;<br>
  &lt;property&gt;<br>
    &lt;name&gt;fs.default.name&lt;/name&gt;<br>
    &lt;value&gt;hdfs://localhost:9000&lt;/value&gt;<br>
  &lt;/property&gt;<br><br>
  &lt;property&gt;  <br>
    &lt;name&gt;hadoop.tmp.dir&lt;/name&gt;  <br>
    &lt;value&gt;/home/hadoop/collector_3h/hadoop-0.20.2/hdfs/tmp&lt;/value&gt;  <br>
  &lt;/property&gt;  <br><br>
&lt;/configuration&gt;</span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;">--------------------------------------</span></p>
<p><span style="font-size:18px;">之后配置eclipse  这个插件真不靠谱</span></p>
<p><span style="font-size:18px;">最终用的3.3.2+hadoop 自带的插件<br><br></span></p>
<br>            </div>
                </div>