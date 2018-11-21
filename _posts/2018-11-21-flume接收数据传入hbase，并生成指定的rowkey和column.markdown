---
layout:     post
title:      flume接收数据传入hbase，并生成指定的rowkey和column
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>接口源码文章：<a href="https://blogs.apache.org/flume/entry/streaming_data_into_apache_hbase" rel="nofollow">https://blogs.apache.org/flume/entry/streaming_data_into_apache_hbase</a><br>
参考博客：<a href="https://blog.csdn.net/m0_37739193/article/details/72868456" rel="nofollow">https://blog.csdn.net/m0_37739193/article/details/72868456</a></p>
<p>目的：flume从event中取出数据作为hbase的rowkey<br>
使用flume接收数据，再传入hbase中，要求中间数据不落地。<br>
flume使用http source入口，使用sink连接hbase实现数据导入，并且通过channels使flume的内存数据保存到本地磁盘（防止集群出现故障，数据可以备份至本地）<br>
传入数据格式为  http:10.0.0.1_{asdasd}   格式说明（url_数据）</p>
<p>hbase存储的结果为：<br>
<img src="https://img-blog.csdnimg.cn/20181109161509108.png" alt="在这里插入图片描述">rowkey:当前时间_url<br>
value:数据<br>
即要对传入的数据进行切分，将url作为rowkey的一部分，当前时间作为另一部分，数据存储到value中</p>
<p>步骤：<br>
1.重写flume中能指定rowkey的源码（HbaseEventSerializer接口）。再打成jar包<br>
java源码见下面：</p>
<p>2.将制作jar包放入flume的/home/hadoop/apache-flume-1.6.0-cdh5.5.2-bin/lib目录下<br>
<img src="https://img-blog.csdnimg.cn/20181109161950516.png" alt="在这里插入图片描述"><br>
3.flume配置文件</p>
<pre><code>a1.sources = r1
a1.sinks = k1
a1.channels = c1

# Describe/configure the source
a1.sources.r1.type = http
a1.sources.r1.port = 44444
a1.sources.r1.bind = 10.0.0.183

# Describe the sink
a1.sinks.k1.type = hbase
a1.sinks.k1.channel = c1
a1.sinks.k1.table = httpdata
a1.sinks.k1.columnFamily = a
a1.sinks.k1.serializer = com.hbase.Rowkey
a1.sinks.k1.channel = memoryChannel

# Use a channel which buffers events in memory
a1.channels.c1.type = file
a1.channels.c1.checkpointDir = /home/x/oyzm_test/flu-hbase/checkpoint/
a1.channels.c1.useDualCheckpoints = false
a1.channels.c1.dataDirs = /home/x/oyzm_test/flu-hbase/flumedir/
a1.channels.c1.maxFileSize = 2146435071
a1.channels.c1.capacity = 100000
a1.channels.c1.transactionCapacity = 10000

# Bind the source and sink to the channel
a1.sources.r1.channels = c1 
a1.sinks.k1.channel = c1
</code></pre>
<p>4.在hbase中建表  create 'httpdata,‘a’</p>
<p>5.flume启动命令<br>
flume-ng agent -c . -f /mysoftware/flume-1.7.0/conf/hbase_simple.conf -n a1 -Dflume.root.logger=INFO,console</p>
<p>6.flume数据写入命令<br>
curl -X POST -d’[{“body”:“http:10.0.0.1_{asdasd}”}]’  <a href="http://10.0.0.183:44444" rel="nofollow">http://10.0.0.183:44444</a></p>
<p>hbase中数据结果：<br>
20181108104034_http:10.0.0.183    column=a:data, timestamp=1541644834926, value={asdasd}</p>
<p>java源码：</p>
<pre><code>package com.hbase;

import java.text.SimpleDateFormat;
import java.util.ArrayList;
import java.util.Date;
import java.util.List;

import org.apache.flume.Context;  
import org.apache.flume.Event;
import org.apache.flume.conf.ComponentConfiguration;  
import org.apache.flume.sink.hbase.HbaseEventSerializer;  
import org.apache.hadoop.hbase.client.Increment;
import org.apache.hadoop.hbase.client.Put;
import org.apache.hadoop.hbase.client.Row;

public class Rowkey implements HbaseEventSerializer {   
   //列族（不用管）
    private byte[] colFam="cf".getBytes();  
    //获取文件
    private Event currentEvent;  
    
    public void initialize(Event event, byte[] colFam) {  
        //byte[]字节型数组  
        this.currentEvent = event;
        this.colFam = colFam;  
    }  
    public void configure(Context context) {}  
    
    public void configure(ComponentConfiguration conf) {  
    }  
    
    //指定rowkey，单元格修饰名，值
    public List&lt;Row&gt; getActions() {  
         // 切分 currentEvent文件 从中拿到的值
         String eventStr = new String(currentEvent.getBody());
         
         //body格式为：url_value
         String url = eventStr.split("_")[0];
         String value = eventStr.split("_")[1];
         
         //得到系统日期  
		 Date d = new Date();
		 SimpleDateFormat df = new SimpleDateFormat("yyyyMMddHHmmss");
         //rowkey
         byte[] currentRowKey = (df.format(d)+"_"+url).getBytes(); 
         
         //hbase的put操作
         List&lt;Row&gt; puts = new ArrayList&lt;Row&gt;();  
         Put putReq = new Put(currentRowKey);  
         //putReq.addColumn  列族，单元格修饰名（可指定），值
         //putReq:  column=a, data, value={asdasd} 
         putReq.addColumn(colFam,  "data".getBytes(), value.getBytes());  
         puts.add(putReq);                
         return puts;  
    }   
    public List&lt;Increment&gt; getIncrements() {  
        List&lt;Increment&gt; incs = new ArrayList&lt;Increment&gt;();      
        return incs;  
    }  
   //关闭流
    public void close() {  
        colFam = null;  
        currentEvent = null;  
    }  
} 
</code></pre>
<p>pom文件：</p>
<pre><code>  &lt;dependencies&gt;
    &lt;dependency&gt;
      &lt;groupId&gt;junit&lt;/groupId&gt;
      &lt;artifactId&gt;junit&lt;/artifactId&gt;
      &lt;version&gt;4.12&lt;/version&gt;
      &lt;scope&gt;test&lt;/scope&gt;
    &lt;/dependency&gt;
    &lt;dependency&gt;
	    &lt;groupId&gt;org.apache.flume.flume-ng-sinks&lt;/groupId&gt;
		&lt;artifactId&gt;flume-ng-hbase-sink&lt;/artifactId&gt;
		&lt;version&gt;1.7.0&lt;/version&gt;
	&lt;/dependency&gt;
   	&lt;dependency&gt;
		&lt;groupId&gt;org.apache.hbase&lt;/groupId&gt;
		&lt;artifactId&gt;hbase-client&lt;/artifactId&gt;
		&lt;version&gt;1.2.4&lt;/version&gt;
	&lt;/dependency&gt;
	&lt;dependency&gt;
         &lt;groupId&gt;jdk.tools&lt;/groupId&gt;
         &lt;artifactId&gt;jdk.tools&lt;/artifactId&gt;
         &lt;version&gt;1.8&lt;/version&gt;
         &lt;scope&gt;system&lt;/scope&gt;
         &lt;systemPath&gt;${JAVA_HOME}/lib/tools.jar&lt;/systemPath&gt;
	&lt;/dependency&gt;
  &lt;/dependencies&gt;
&lt;/project&gt;
</code></pre>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>