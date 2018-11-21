---
layout:     post
title:      flume使用（三）：实时log4j日志通过flume输出到MySql数据库
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：					https://blog.csdn.net/maoyuanming0806/article/details/79380826				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p><span class="content">                                    <a href="http://blog.csdn.net/maoyuanming0806/article/details/79371123" rel="nofollow">flume使用（一）：入门demo</a>                                                                    <br></span></p><p><span class="content">                                    <a href="http://blog.csdn.net/maoyuanming0806/article/details/79376600" rel="nofollow">flume使用（二）：采集远程日志数据到MySql数据库</a></span></p><p><br></p><p>本文在【flume使用（二）：采集远程日志数据到MySql数据库】一文基础之上进行测试操作。本文使用到的：</p><p>flume版本、jdk版本、mysql、数据库表、javaBean、自定义的mysqlSink、以及多agent配置均同【flume使用（二）：采集远程日志数据到MySql数据库】</p><h1>一、需求说明<br></h1><p>采集远程系统上log4j实时生成的日志到本机上进行数据库持久化。</p><h1>二、设计<br></h1><p>数据流：</p><p>（1）<span style="background-color:rgb(255,255,102);">log4j</span>（配置文件配置flume输出）</p><p><span style="background-color:rgb(255,153,102);">----[通过avro协议传输]-------&gt;</span>（2）<span style="background-color:rgb(255,255,153);">一个agent</span>（source:avro; channel:memory; sink:avro）</p><p><span style="background-color:rgb(255,204,153);">----[通过avro协议传输]------&gt;</span>（3）<span style="background-color:rgb(255,255,153);">一个agent</span>（source:avro; channel:memory; sink:自定义mysqlSink）</p><p><span style="background-color:rgb(255,204,153);">---[mysqlSink进行jdbc持久化]-----&gt;</span>（4）<span style="background-color:rgb(255,255,153);">mysql数据库 <br></span></p><p><span style="background-color:rgb(255,255,153);"><span style="background-color:rgb(255,255,255);"><br></span></span></p><p><span style="background-color:rgb(255,255,153);"><span style="background-color:rgb(255,255,255);">本测试为了方便一些：</span></span></p><p><span style="background-color:rgb(255,255,153);"><span style="background-color:rgb(255,255,255);">（1）是在windows上直接eclipse执行；（可以打包让运行在linux机器202上）</span></span></p><p><span style="background-color:rgb(255,255,153);"><span style="background-color:rgb(255,255,255);">（2）是linux机器202；</span></span></p><p><span style="background-color:rgb(255,255,153);"><span style="background-color:rgb(255,255,255);">（3）是linux机器201；</span></span></p><p><span style="background-color:rgb(255,255,153);"><span style="background-color:rgb(255,255,255);">（4）数据库在linux机器201上</span></span></p><p><span style="background-color:rgb(255,255,153);"><span style="background-color:rgb(255,255,255);"><br></span></span></p><h1><span style="background-color:rgb(255,255,153);"><span style="background-color:rgb(255,255,255);">三、实施<br></span></span></h1><h2>1.编写实时输出log4j日志程序</h2><p>（1）新建maven测试项目：pom.xml</p><pre><code class="language-plain">&lt;project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"&gt;
  &lt;modelVersion&gt;4.0.0&lt;/modelVersion&gt;
  &lt;groupId&gt;yichao.mym&lt;/groupId&gt;
  &lt;artifactId&gt;flume-log4j&lt;/artifactId&gt;
  &lt;version&gt;0.0.1-SNAPSHOT&lt;/version&gt;
  
  &lt;dependencies&gt;
		&lt;dependency&gt;
			&lt;groupId&gt;log4j&lt;/groupId&gt;
			&lt;artifactId&gt;log4j&lt;/artifactId&gt;
			&lt;version&gt;1.2.16&lt;/version&gt;
		&lt;/dependency&gt;
	     &lt;dependency&gt;
	        &lt;groupId&gt;org.apache.flume&lt;/groupId&gt;
	        &lt;artifactId&gt;flume-ng-core&lt;/artifactId&gt;
	        &lt;version&gt;1.7.0&lt;/version&gt;
	    &lt;/dependency&gt;
		&lt;!-- https://mvnrepository.com/artifact/org.apache.flume.flume-ng-clients/flume-ng-log4jappender --&gt;
		&lt;dependency&gt;
		    &lt;groupId&gt;org.apache.flume.flume-ng-clients&lt;/groupId&gt;
		    &lt;artifactId&gt;flume-ng-log4jappender&lt;/artifactId&gt;
		    &lt;version&gt;1.7.0&lt;/version&gt;
		&lt;/dependency&gt;

	&lt;/dependencies&gt;
  
&lt;/project&gt;</code></pre><p><br></p><p>（2）编写log4j.properties</p><pre><code class="language-plain">log4j.rootLogger=INFO, stdout, flume
log4j.logger.per.flume=INFO

### flume ###
log4j.appender.flume=org.apache.flume.clients.log4jappender.Log4jAppender
log4j.appender.flume.layout=org.apache.log4j.PatternLayout
log4j.appender.flume.Hostname=192.168.216.202
log4j.appender.flume.Port=44444

### stdout ###
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.Threshold=INFO
log4j.appender.stdout.Target=System.out
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss} %c{1} [%p] %m%n</code></pre><br><p>（3）编写日志打印程序</p><pre><code class="language-plain">package yichao.mym.base.bean;

import org.apache.log4j.Logger;

public class WriteLog {
    protected static final Logger logger = Logger.getLogger(WriteLog.class);

    public static void main(String[] args) throws Exception {
    	int name = 0;
        while (true) {
            logger.info("log4j-flume-name-"+name+","+(name++));
            Thread.sleep(1000);
        }
    }
}</code></pre><p>注意这里打印的数据必须要能让mysqlSink.java能够解析。由于log4j输出数据格式问题，导致原本根据“,”拆分的字符不能正确解析，原因是输出的数据如： [ log4j-flume-name-1668,1668   ] 。这条数据前后都有间隔，导致第二个字段"1688  "不能被转换成整型。</p><p>解决方式：修改解析方式：mysqlSink.java中在string转int类型时，先对string进行去除空格处理</p><p>处理前：person.setAge(Integer.parseInt(content.substring(content.indexOf(",")+1)));<br></p><p>处理后：person.setAge(Integer.parseInt(content.substring(content.indexOf(",")+1).trim()));</p><p>目前文章flume使用（二）已经做了修正</p><p><br></p><h2>2.编写采集log4j实时传输过来数据的flume的配置文件</h2><p>此配置文件放到linux机器202中，而原linux机器201的conf配置文件不变仍然使用：avro-mysql.conf</p><p>文件名：log4jAvro-avro.conf</p><pre><code class="language-plain">agent1.sources=source1  
agent1.channels=channel1  
agent1.sinks=mysqlSink  
  
# describe/configure source1  
agent1.sources.source1.type=avro
agent1.sources.source1.bind=192.168.216.202
agent1.sources.source1.port=44444
agent1.sources.source1.channels=channel1
  
# use a channel which buffers events in memory  
# type:memory or file is to temporary to save buffer data which is sink using  
agent1.channels.channel1.type=memory  
agent1.channels.channel1.capacity=5000  
agent1.channels.channel1.transactionCapacity=1000  

agent1.sinks.mysqlSink.type=avro
agent1.sinks.mysqlSink.channel=channel1
agent1.sinks.mysqlSink.hostname=192.168.216.201
agent1.sinks.mysqlSink.port=4545</code></pre><p>配置文件同样放在linux机器202的flume目录中的bin中</p><p><br></p><h1>四、测试</h1>都进入flume中的bin目录下执行命令<p></p><p>1.启动linux机器201</p><pre><code class="language-plain">./flume-ng agent -c ../conf -f ../conf/avro-mysql.conf -n agent1 -Dflume.root.logger=INFO,console</code></pre><p>2.启动linux机器202（注意启动的配置文件）</p><pre><code class="language-plain">./flume-ng agent -c ../conf -f ../conf/log4jAvro-avro.conf -n agent1 -Dflume.root.logger=INFO,console</code></pre><p>3.在windows上执行编写好的实时输出log4j的程序</p><p>此时刷新mysql数据库表，即可看到数据正源源不断的插入进来：</p><p><img src="https://img-blog.csdn.net/20180226205304985?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbWFveXVhbm1pbmcwODA2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></p><p><br></p><p></p><p></p><h1 class="csdn_top"><br></h1><br><p><span style="background-color:rgb(255,255,153);"><span style="background-color:rgb(255,255,255);"></span><br></span></p><p><span style="background-color:rgb(255,255,153);"><br></span></p><p><span style="background-color:rgb(255,255,153);"><br></span></p><p></p><p></p><p><span class="content"></span></p>            </div>
                </div>