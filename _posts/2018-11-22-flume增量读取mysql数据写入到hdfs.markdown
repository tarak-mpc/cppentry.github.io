---
layout:     post
title:      flume增量读取mysql数据写入到hdfs
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u010916338/article/details/82694566				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>一,下载flume-ng-sql-source-1.4.1.jar,放入flume的lib目录下<br>
链接：<a href="https://pan.baidu.com/s/1krRxL3vi1PcTqVN_pF3Pug" rel="nofollow">https://pan.baidu.com/s/1krRxL3vi1PcTqVN_pF3Pug</a> 密码：fkod<br>
二,将mysql驱动jar包放入flume的lib目录下(mysql5.5)<br>
链接：<a href="https://pan.baidu.com/s/1sZv2b70mbBqcr4mrBLAbTQ" rel="nofollow">https://pan.baidu.com/s/1sZv2b70mbBqcr4mrBLAbTQ</a> 密码：cbxj<br>
<img src="https://img-blog.csdn.net/20180918144910350?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTA5MTYzMzg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"><br>
三,flume配置文件<br>
注意事项:<br>
1,flume1.7以后才支持拦截器Interceptor<br>
2,flume如何匹配空格,需要加括号<br>
3,hdfs落地时地址要用命名空间代替<br>
4,a1.sources.sqlSource.delimiter.entry=|  突然失效,原因未知</p>
<pre><code>#声明source,channel,sink
a1.sources=sqlSource
a1.channels=c1
a1.sinks=s1

#声明source类型
a1.sources.sqlSource.type=org.keedio.flume.source.SQLSource
a1.sources.sqlSource.hibernate.connection.url=jdbc:mysql://192.168.35.202:3306/nagios
a1.sources.sqlSource.hibernate.connection.user=root
a1.sources.sqlSource.hibernate.connection.password=root123
#这个参数很重要，默认false,如果设为false就不会自动查询
a1.sources.sqlSource.hibernate.connection.autocommit=true
#声明mysql的hibernate方言
a1.sources.sqlSource.hibernate.dialect=org.hibernate.dialect.MySQL5Dialect
#声明mysql驱动
a1.sources.sqlSource.hibernate.connection.driver_class=com.mysql.jdbc.Driver
#查询间隔，单位毫秒
a1.sources.sqlSource.run.query.delay=10000
#声明保存flume状态的文件夹位置
a1.sources.sqlSource.status.file.path=/usr/soft/apache-flume-1.5.2-bin/log
a1.sources.sqlSource.status.file.name=sqlSouce.status
#声明从第一条数据开始查询
a1.sources.sqlSouce.start.from=0
#sql语句自定义,但是要注意:增量只能针对id字段即主键列,经测试系统默认如此.
#而且必须要将主键查询出来,因为如果不查询主键,flume无法记录上一次查询的位置.
#$@$表示增量列上一次查询的值，记录在status文件中
a1.sources.sqlSource.custom.query=
SELECT nsc.servicecheck_id,  ns.display_name, nh.alias, nsc.state, nsc.start_time, nsc.end_time, nsc.output, nsc.perfdata 
from nagios_servicechecks as nsc 
LEFT JOIN nagios_services as ns ON nsc.service_object_id = ns.service_object_id 
LEFT JOIN nagios_hosts as nh ON ns.host_object_id = nh.host_object_id 
WHERE ns.display_name = '901_CPU_load' 
AND nsc.servicecheck_id &gt; $@$ ORDER BY nsc.servicecheck_id ASC

#设置分批参数
a1.sources.sqlSource.batch.size=1000
a1.sources.sqlSource.max.rows=1000

#设置数据查询出来后用什么分隔符隔开,存储时也用此分隔符
#此处一开始有效,后来没有效果,未及测试原因,此处功能可以用拦截器进行替换.
#a1.sources.sqlSource.delimiter.entry=|

#设置c3p0连接池参数
a1.sources.sqlSource.hibernate.connection.provider_class = org.hibernate.connection.C3P0ConnectionProvider
a1.sources.sqlSource.hibernate.c3p0.min_size=1
a1.sources.sqlSource.hibernate.c3p0.max_size=10


#配置拦截器(替换)
a1.sources.sqlSource.interceptors=i1 i2 i3 i4 i5
a1.sources.sqlSource.interceptors.i1.type=search_replace
a1.sources.sqlSource.interceptors.i1.searchPattern="
a1.sources.sqlSource.interceptors.i1.replaceString=

a1.sources.sqlSource.interceptors.i2.type=search_replace
a1.sources.sqlSource.interceptors.i2.searchPattern=OK - CPU used=
a1.sources.sqlSource.interceptors.i2.replaceString=

a1.sources.sqlSource.interceptors.i3.type=search_replace
#注意:此处有空格需要匹配,因为直接写空格无法直接匹配,外层需要加上小括号
a1.sources.sqlSource.interceptors.i3.searchPattern=( idle=)
a1.sources.sqlSource.interceptors.i3.replaceString=

a1.sources.sqlSource.interceptors.i4.type=search_replace
a1.sources.sqlSource.interceptors.i4.searchPattern=,
a1.sources.sqlSource.interceptors.i4.replaceString=|

a1.sources.sqlSource.interceptors.i5.type=search_replace
a1.sources.sqlSource.interceptors.i5.searchPattern=(% )
a1.sources.sqlSource.interceptors.i5.replaceString=%


#设置通道为内存模式
a1.channels.c1.type=memory
a1.channels.c1.capacity=10000
a1.channels.c1.transactionCapacity=10000
a1.channels.c1.byteCapacityBufferPercentage=20
a1.channels.c1.byteCapacity=800000


a1.sinks.s1.type=HDFS
#ns为namenode的命名空间,两个作用,一个是防止集群坍塌,另一个是改参数只能作用在active的namenode节点上
a1.sinks.s1.hdfs.path=hdfs://ns/nagios/901_CPU_load
a1.sinks.s1.hdfs.fileType=DataStream
a1.sinks.s1.hdfs.writeFormat=Text
#设置滚动时间,每隔多少时间生成一个文件.如果设置成0,则禁止滚动,可以使所有数据被写到一个文件中.
a1.sinks.s1.hdfs.rollInterval=0
#设置文件存储数据多大的时候生成下一个文件,建议设置成128M和块大小相同
a1.sinks.s1.hdfs.rollSize=134217728
#设置文件多少行时,滚动生成下一个文件,设置成0时禁止滚动
a1.sinks.s1.hdfs.rollCount=0



#连接source,channel,sink
a1.sources.sqlSource.channels=c1
a1.sinks.s1.channel=c1

</code></pre>
<p><img src="https://img-blog.csdn.net/20180918144927222?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTA5MTYzMzg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>