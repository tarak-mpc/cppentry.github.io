---
layout:     post
title:      hadoop-config
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>hdfs-site.xml</p>
<p>&lt;?xml version="1.0"?&gt;</p>
&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt;<br>
&lt;!-- Put site-specific property overrides in this file. --&gt;<br>
&lt;configuration&gt;  <br>
  &lt;property&gt;  <br>
    &lt;name&gt;dfs.name.dir&lt;/name&gt;  <br>
    &lt;value&gt;/usr/local/hadoop/datalog1,/usr/local/hadoop/datalog2&lt;/value&gt;  <br>
  &lt;/property&gt;  <br>
  &lt;property&gt;  <br>
    &lt;name&gt;dfs.data.dir&lt;/name&gt;  <br>
    &lt;value&gt;/usr/local/hadoop/data1,/usr/local/hadoop/data2&lt;/value&gt;  <br>
  &lt;/property&gt;  <br>
  &lt;property&gt;  <br>
    &lt;name&gt;dfs.replication&lt;/name&gt;  <br>
    &lt;value&gt;1&lt;/value&gt;  <br>
  &lt;/property&gt;  <br>
  &lt;property&gt;  <br>
    &lt;name&gt;dfs.permissions&lt;/name&gt;  <br>
    &lt;value&gt;false&lt;/value&gt;  <br>
  &lt;/property&gt;  <br><p>&lt;/configuration&gt;</p>
<p>==========================================================================</p>
<p>core-site.xml</p>
<p>&lt;?xml version="1.0"?&gt;<br>
&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt;<br>
&lt;configuration&gt;<br>
&lt;property&gt;<br>
  &lt;name&gt;fs.default.name&lt;/name&gt;<br>
  &lt;value&gt;hdfs://172.10.10.144:9000&lt;/value&gt;<br>
&lt;/property&gt;<br>
&lt;/configuration&gt;<br></p>
<p>==========================================================================<br></p>
<p>mapred-site.xml</p>
<p>&lt;?xml version="1.0"?&gt;<br>
&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt;<br>
&lt;configuration&gt;<br>
&lt;property&gt;<br>
  &lt;name&gt;mapred.job.tracker&lt;/name&gt;<br>
  &lt;value&gt;172.10.10.144:9001&lt;/value&gt;<br>
&lt;/property&gt;<br>
&lt;/configuration&gt;<br></p>
<p>==========================================================================<br></p>
<p>hbase-site.xml</p>
<p>&lt;?xml version="1.0"?&gt;<br>
&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt;<br>
&lt;configuration&gt;<br>
&lt;property&gt;<br>
    &lt;name&gt;hbase.rootdir&lt;/name&gt;<br>
    &lt;value&gt;hdfs://172.10.10.144:9000/hbase&lt;/value&gt;<br>
&lt;/property&gt;<br>
&lt;property&gt;<br>
    &lt;name&gt;dfs.replication&lt;/name&gt;<br>
    &lt;value&gt;3&lt;/value&gt;<br>
&lt;/property&gt;<br>
&lt;/configuration&gt;</p>
<p><br></p>
            </div>
                </div>