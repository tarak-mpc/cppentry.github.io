---
layout:     post
title:      Flume使用namespace操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：尊重原创，转载请标明"本文转自:https://blog.csdn.net/high2011"					https://blog.csdn.net/high2011/article/details/70153668				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h4><span style="font-weight:normal;"><span style="font-size:18px;"> 参考：</span></span></h4>
<h4><span style="font-weight:normal;"><span style="font-size:18px;">(1)官方文档：<a href="http://flume.apache.org/FlumeUserGuide.html" rel="nofollow">http://flume.apache.org/FlumeUserGuide.html</a></span></span></h4>
<div><span><span style="font-size:18px;">(2)中文文档：<a href="http://download.csdn.net/download/high2011/9812414" rel="nofollow">http://download.csdn.net/download/high2011/9812414</a></span></span></div>
<h4><span style="font-size:24px;"><span>1</span><span>、</span>举例</span></h4>
<p>(1)编辑flume-conf.properties文件</p>
<p>内容如下：</p>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p># example.conf: A single-node Flume configuration</p>
<p> </p>
<p># Name the components on this agent</p>
<p>a1.sources = r1</p>
<p>a1.sinks = k1</p>
<p>a1.channels = c1</p>
<p> </p>
<p># Describe/configure the source</p>
<p>a1.sources.r1.type = exec</p>
<p>a1.sources.r1.command= tail -F /home/jifei_yang/testflume/data_dir/taildata/tailflume.txt</p>
<p> </p>
<p># Describe the sink</p>
<p>a1.sinks.k1.type = hdfs</p>
<p>#hdfs sink间隔多长将临时文件滚动成最终目标文件，单位：秒；</p>
<p>a1.sinks.k1.hdfs.rollInterval= 60</p>
<p>a1.sinks.k1.hdfs.rollSize= 0</p>
<p>a1.sinks.k1.hdfs.rollCount= 0</p>
<p>a1.sinks.k1.hdfs.path= hdfs://<span style="background-color:rgb(255,102,102);">nameservice1test</span>/user/piqiang_wei/testflume/%Y-%m-%d-%H  </p>
<p>a1.sinks.k1.hdfs.useLocalTimeStamp= true</p>
<p>a1.sinks.k1.hdfs.fileType= DataStream</p>
<p>#当目前被打开的临时文件在该参数指定的时间（秒）内，没有任何数据写入，</p>
<p>#则将该临时文件关闭并重命名成目标文件；</p>
<p>a1.sinks.k1.hdfs.idleTimeout= 30</p>
<p>a1.sinks.k1.hdfs.kerberosPrincipal= log2hadoop</p>
<p>a1.sinks.k1.hdfs.kerberosKeytab= /home/jifei_yang/testflume/conf/log2hadoop_bgp_apple.keytab</p>
<p> </p>
<p> </p>
<p># Use a channel which buffers events in memory</p>
<p>a1.channels.c1.type = memory</p>
<p>a1.channels.c1.capacity = 1000</p>
<p>a1.channels.c1.transactionCapacity = 100</p>
<p> </p>
<p># Bind the source and sink to the channel</p>
<p>a1.sources.r1.channels = c1</p>
<p>a1.sinks.k1.channel = c1</p>
</td>
</tr></tbody></table><p> <span style="font-size:24px;">2、注意</span></p>
<h6><span style="font-size:14px;">(1)、红色是需要在原来的基础上修改的地方</span></h6>
<p>将原来的ip:8020-----》<span style="color:#FF0000;">nameservice1test(</span><span style="color:#FF0000;">命名空间</span><span style="color:#FF0000;">)</span></p>
<h6><span style="font-size:14px;">(2)、提交命令</span></h6>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p>flume-ng agent  <span style="color:#FF0000;">--conf  /home/jifei_yang/testflume/configs/piqiang_wei</span>  --conf-file  /home/jifei_yang/testflume/my_properties/flume-conf.properties --name a1    -Dflume.root.logger=DEBUG,console</p>
</td>
</tr></tbody></table><p> </p>
<p>红色部分是新添加的配置目录，需要创建！</p>
<p><br></p>
<p><strong> <span style="font-size:14px;">(3)、在目录下添加xml文件</span></strong></p>
<p><span style="color:#FF0000;">目录：</span><span style="color:#FF0000;">/home/jifei_yang/testflume/configs/piqiang_wei</span></p>
<p><span style="color:#FF0000;">文件：</span></p>
<table border="1" cellspacing="0" cellpadding="0" width="633"><tbody><tr><td valign="top">
<p></p>
<p> <img src="https://img-blog.csdn.net/20170413090647919?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaGlnaDIwMTE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p> </p>
<p></p>
</td>
</tr></tbody></table><p> </p>
<p>这些文件是在namenode所在机器的目录(/etc/Hadoop/conf)下获取！</p>
<p><br></p>
<p>注：测试通过，可以按需修改使用！</p>
<p><br></p>
<p><br></p>
            </div>
                </div>