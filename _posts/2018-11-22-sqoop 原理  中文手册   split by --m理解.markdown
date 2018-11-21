---
layout:     post
title:      sqoop 原理  中文手册   split by --m理解
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h3 class="tc-card-title" style="margin:8px 0px 6px; padding:0px; font-size:24px; line-height:36px; font-weight:400; color:rgb(51,51,51)">
MySQL里的数据）通过Sqoop Import HDFS 里 和 通过Sqoop Export HDFS 里的数据到（MySQL）（五）</h3>
<div class="tc-card-imagetext-content" id="tc-highlight" style="margin:0px; padding:0px; font-size:14px">
<div class="tc-card-imagetext-text" style="margin:0.2rem 0px; padding:0px; color:rgb(51,51,51); line-height:30px; font-size:18px">
<div style="margin:0px; padding:0px; font-size:16px; color:rgb(0,0,0); text-overflow:clip; clear:none; line-height:30px!important">
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
　　下面我们结合 HDFS，介绍 Sqoop 从关系型数据库的导入和导出</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">一、MySQL里的数据通过Sqoop import HDFS</span></span><br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
　　它的功能是<span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">将数据从关系型数据库导入
 HDFS 中</span></span>，其流程图如下所示。</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=6c2040c7ffb1700501d855cd8414ed83&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201611%2F855959%2D20161130093710990%2D563518867%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
　　我们来分析一下 Sqoop 数据导入流程，首先用户输入一个<span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important"> Sqoop
 import</span></span></span> 命令，Sqoop 会从关系型数据库中获取元数据信息，<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
比如要操作数据库表的 schema是什么样子，这个表有哪些字段，这些字段都是什么数据类型等。<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
　　它获取这些信息之后，会<span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">将输入命令转化为<span style="margin:0px 4px; padding:0px; color:rgb(255,0,0); text-overflow:clip; clear:none; line-height:30px!important">基于
 Map</span> 的 MapReduce作业</span></span>。<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
这样 MapReduce作业中有很多 Map 任务，每个 Map 任务从数据库中读取一片数据，这样多个 Map 任务实现并发的拷贝，把整个数据快速的拷贝到 HDFS 上。</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<span style='color:rgb(69,69,69); font-family:"PingFang SC","Microsoft YaHei",SimHei,Arial,SimSun; font-size:16px'>场景：</span><span style='color:rgb(69,69,69); font-family:"PingFang SC","Microsoft YaHei",SimHei,Arial,SimSun; font-size:16px'> </span><br style='color:rgb(69,69,69); font-family:"PingFang SC","Microsoft YaHei",SimHei,Arial,SimSun; font-size:16px'>
<br style='color:rgb(69,69,69); font-family:"PingFang SC","Microsoft YaHei",SimHei,Arial,SimSun; font-size:16px'>
<span style='color:rgb(69,69,69); font-family:"PingFang SC","Microsoft YaHei",SimHei,Arial,SimSun; font-size:16px'>sqoop import --connect jdbc:postgresql://...../..... --username ....  --query "select * from retail_tb_order_qiulp_test where status = 'TRADE_FINISHED'
 or status = 'TRADE_CLOSED' or status = 'TRADE_CLOSED_BY_TAOBAO' and \$CONDITIONS"  --hive-import -m 6 --hive-table custom_analyse_db.retail_tb_order_main --hive-overwrite --hive-partition-key part --hive-partition-value qiulp --target-dir /user/qiulp/test1
 --split-by tid </span><br style='color:rgb(69,69,69); font-family:"PingFang SC","Microsoft YaHei",SimHei,Arial,SimSun; font-size:16px'>
<span style='color:rgb(69,69,69); font-family:"PingFang SC","Microsoft YaHei",SimHei,Arial,SimSun; font-size:16px'>注意红色字体部分， retail_tb_order_qiulp_test表原有1000条数据，导入hive后成了6000条，是map的倍数关系。 </span><br style='color:rgb(69,69,69); font-family:"PingFang SC","Microsoft YaHei",SimHei,Arial,SimSun; font-size:16px'>
<span style='color:rgb(69,69,69); font-family:"PingFang SC","Microsoft YaHei",SimHei,Arial,SimSun; font-size:16px'>如下图，一条数据在hive中有6条相同的数据： </span><br style='color:rgb(69,69,69); font-family:"PingFang SC","Microsoft YaHei",SimHei,Arial,SimSun; font-size:16px'>
<br style='color:rgb(69,69,69); font-family:"PingFang SC","Microsoft YaHei",SimHei,Arial,SimSun; font-size:16px'>
<span style='color:rgb(69,69,69); font-family:"PingFang SC","Microsoft YaHei",SimHei,Arial,SimSun; font-size:16px'>理解</span><span style='color:rgb(69,69,69); font-family:"PingFang SC","Microsoft YaHei",SimHei,Arial,SimSun; font-size:16px'> </span><br style='color:rgb(69,69,69); font-family:"PingFang SC","Microsoft YaHei",SimHei,Arial,SimSun; font-size:16px'>
<span style='color:rgb(69,69,69); font-family:"PingFang SC","Microsoft YaHei",SimHei,Arial,SimSun; font-size:16px'>1.split-by 根据不同的参数类型有不同的切分方法，如int型，Sqoop会取最大和最小split-by字段值，然后根据传入的num-mappers来 确定划分几个区域。比如select max(split_by),min(split-by) from得到的max(split-by)和min(split-by)分别为1000和1，而num-mappers（-m）为2的话，则会分成两个区域
 (1,500)和(501-1000),同时也会分成2个sql给2个map去进行导入操作，分别为select XXX from table where split-by&gt;=1 and split-by&lt;500和select XXX from table where split-by&gt;=501 and split-by&lt;=1000.最后每个map各自获取各自SQL中的数据进行导入工作。 </span><br style='color:rgb(69,69,69); font-family:"PingFang SC","Microsoft YaHei",SimHei,Arial,SimSun; font-size:16px'>
<br style='color:rgb(69,69,69); font-family:"PingFang SC","Microsoft YaHei",SimHei,Arial,SimSun; font-size:16px'>
<span style='color:rgb(69,69,69); font-family:"PingFang SC","Microsoft YaHei",SimHei,Arial,SimSun; font-size:16px'>2.当split-by不是int型时出现如上场景中的问题。目前想到的解决办法是：将-m 设置称1，split-by不设置，即只有一个map运行，缺点是不能并行map录入数据。（注意，当-m 设置的值大于1时，split-by必须设置字段） </span><br style='color:rgb(69,69,69); font-family:"PingFang SC","Microsoft YaHei",SimHei,Arial,SimSun; font-size:16px'>
<br style='color:rgb(69,69,69); font-family:"PingFang SC","Microsoft YaHei",SimHei,Arial,SimSun; font-size:16px'>
<span style='color:rgb(69,69,69); font-family:"PingFang SC","Microsoft YaHei",SimHei,Arial,SimSun; font-size:16px'>3.split-by即便是int型，若不是连续有规律递增的话，各个map分配的数据是不均衡的，可能会有些map很忙，有些map几乎没有数据处理的情况</span><br>
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<span style='color:rgb(69,69,69); font-family:"PingFang SC","Microsoft YaHei",SimHei,Arial,SimSun; font-size:16px'>-----------------------------------------------------------------------------------------------------------------------</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<h3 style="margin:4px; padding:0px; font-size:16px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important">Sqoop Import HDFS（带着官网）</span></h3>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important"><img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=058b298797993d36cc35e0207604912a&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317151646213%2D1060525675%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></span></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=eb0d90ca6e9addc6743d072441793c9b&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317151657010%2D1167891294%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=bcf95b782e986ffdc3bb5d7a1d03f372&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317151904651%2D555620603%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=f55ce12765725d4531ff16562ebc2f8c&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317151923151%2D1044515795%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
　　具体，自己去尝试做吧！</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
 　　在这之前，先启动hadoop集群,sbin/start-all.sh。这里不多赘述。</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
　　同时，开启MySQL数据库。这里，不多赘述。</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=56e572aacfea4316786f412725077bf3&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317154035995%2D1848706261%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
　　同时，因为后续的sqoop运行啊，会产生一些日志等，我这里先新建一个目录，用来专门存放它。在哪个目录下运行后续的sqoop操作，就在哪个目录下新建就好。（因为，已经配置了环境变量，在任何路径下都是可以运行的）</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=1d09b783fc581723f3687e36b6f34ba5&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317153344073%2D678125301%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">[hadoop@djt002 sqoop]$ pwd
</span>/usr/local/<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">sqoop
[hadoop@djt002 sqoop]$ ll
total </span>4<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">
drwxr</span>-xr-x. 9 hadoop hadoop 4096 Apr 27  2015 sqoop-1.4.6<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">
[hadoop@djt002 sqoop]$ mkdir sqoopRunCreate
[hadoop@djt002 sqoop]$ ll
total </span>8<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">
drwxr</span>-xr-x. 9 hadoop hadoop 4096 Apr 27  2015 sqoop-1.4.6<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">
drwxrwxr</span>-x. 2 hadoop hadoop 4096 Mar 17 23:33<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> sqoopRunCreate
[hadoop@djt002 sqoop]$ cd sqoopRunCreate</span>/<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">
[hadoop@djt002 sqoopRunCreate]$ pwd
</span><span style="margin:0px 4px; padding:0px; float:none; white-space:pre; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(255,0,0); text-overflow:clip; clear:none; line-height:30px!important">/usr/local/sqoop/</span></span><span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(255,0,0); text-overflow:clip; clear:none; line-height:30px!important">sqoopRunCreate</span></span>
[hadoop@djt002 sqoopRunCreate]$</span></pre>
</div>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
 　　比如，以后我就在这个目录下运行操作sqoop，<span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">/usr/local/sqoop/sqoopRunCreate。</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important"> Sqoop Import 应用场景——密码访问</span></span></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
 　　（1）明码访问</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=be0bfd9137cef3fd18b7cb7e1eb8f4d9&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317154135166%2D996598484%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important">[hadoop@djt002 sqoopRunCreate]$ <span style="margin:0px 4px; padding:0px; float:none; white-space:pre; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">sqoop list-databases \
</span></span>&gt;<span style="margin:0px 4px; padding:0px; float:none; white-space:pre; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important"> --connect jdbc:mysql://192.168.80.200/ \</span></span>
&gt;<span style="margin:0px 4px; padding:0px; float:none; white-space:pre; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important"> --username hive \
</span></span>&gt; <span style="margin:0px 4px; padding:0px; float:none; white-space:pre; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">--password hive</span></span></pre>
</div>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=9e6a57c7b40e7cbc092a5eaf2c4dae59&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317154302698%2D538589942%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
 　　（2）交互式密码访问</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=45adc1b27ce3130ff377d1c2717c8dbb&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317154512807%2D1842108799%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important">[hadoop@djt002 sqoopRunCreate]$ sqoop list-<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">databases \
</span>&gt; --connect jdbc:mysql:<span style="margin:0px 4px; padding:0px; color:rgb(0,128,0); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">//</span><span style="margin:0px 4px; padding:0px; color:rgb(0,128,0); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">192.168.80.200/ \</span>
&gt; --<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">username hive \
</span>&gt; <span style="margin:0px 4px; padding:0px; float:none; white-space:pre; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">-</span></span><span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">P</span></span>

Enter password: （输入hive）</span></pre>
</div>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
　　（3）文件授权密码访问</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
　　因为，官网上是这么给的，在家目录，且需赋予400权限。所以</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=e735a32b922431b0a4424760fcebe0c2&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317160306291%2D92846813%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=f357b52b3e0eb0dd224bf464cbf65fbd&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317160748698%2D1573164105%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important">[hadoop@djt002 ~<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">]$ pwd
</span>/home/<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">hadoop
[hadoop@djt002 </span>~]$<span style="margin:0px 4px; padding:0px; float:none; white-space:pre; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important"> echo -n "hive" &gt;</span></span><span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important"> .password</span></span>
[hadoop@djt002 </span>~]$ ls -<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">a
.            .bash_history  .cache   djt        flume    .gnote           .gvfs            .local          .nautilus  .pulse         Videos       .xsession</span>-<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">errors
..           .bash_logout   .config  Documents  .gconf   .gnupg           .hivehistory     .mozilla        <span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(255,0,0); text-overflow:clip; clear:none; line-height:30px!important">.password</span></span>  .pulse</span>-cookie  .vim         .xsession-<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">errors.old
.abrt        .bash_profile  .dbus    Downloads  .gconfd  .gstreamer</span>-0.10<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">  .ICEauthority    Music           Pictures   .ssh           .viminfo
anagram.jar  .bashrc        Desktop  .esd_auth  .gnome2  .gtk</span>-<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">bookmarks   .imsettings.log  .mysql_history  Public     Templates      .Xauthority
[hadoop@djt002 </span>~<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">]$ <span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">more .password</span><span style="margin:0px 4px; padding:0px; color:rgb(255,0,0); text-overflow:clip; clear:none; line-height:30px!important">hive</span></span>
[hadoop@djt002 </span>~]$ </pre>
</div>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=67a2eb0c20ab379bde5bdea3fc8d23b1&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317161036463%2D12227862%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important">[hadoop@djt002 ~]$ <span style="margin:0px 4px; padding:0px; float:none; white-space:pre; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">chmod 400 .password</span></span></pre>
</div>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=20c9936ba4a2bcd4c9a93d8b1513e741&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317162124588%2D1002194798%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important">[hadoop@djt002 sqoopRunCreate]$ <span style="margin:0px 4px; padding:0px; float:none; white-space:pre; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">sqoop list-databases \
</span></span>&gt; <span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">--connect jdbc:mysql://192.168.80.200/ \</span></span>
&gt; <span style="margin:0px 4px; padding:0px; float:none; white-space:pre; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">--username hive \
</span></span>&gt; <span style="margin:0px 4px; padding:0px; float:none; white-space:pre; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">-</span><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">-password-file /home/hadoop/</span></span><span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">.password</span></span>


java.io.IOException: The provided password file /home/hadoop/.password does not exist!
</span></pre>
</div>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=adb3c3efd4adfffebc94cb454823483d&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317162459182%2D1569907363%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=a398436303dd251d85c8b61e87d54964&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317162535338%2D164746296%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=89cfd41d4021ad1b6fbd56fac40ba95a&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317162923120%2D40283844%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=40d9862877c1e7563432f4ee3706d041&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317162938354%2D1379820390%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important">[hadoop@djt002 local]$ $HADOOP_HOME/bin/hadoop dfs -put /home/hadoop/.password /user/<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">hadoop



[hadoop@djt002 local]$ $HADOOP_HOME</span>/bin/hadoop dfs -chmod 400 /user/hadoop/.password</pre>
</div>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=5c69738a141d8b4bec51f3354f66f228&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317163047635%2D1871960059%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important">[hadoop@djt002 ~<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">]$ rm .password 
rm: remove write</span>-<span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">protected</span> regular file `.password'? y</pre>
</div>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=fb121bb992eba909cab8879f8ba9dcbc&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317163232057%2D44827794%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important">[hadoop@djt002 sqoopRunCreate]$ sqoop list-<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">databases \
</span>&gt; --connect jdbc:mysql:<span style="margin:0px 4px; padding:0px; color:rgb(0,128,0); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">//</span><span style="margin:0px 4px; padding:0px; color:rgb(0,128,0); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">192.168.80.200/ \</span>
&gt; --<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">username hive \
</span>&gt; --password-file <span style="margin:0px 4px; padding:0px; float:none; white-space:pre; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">/user/hadoop/.password</span></span></pre>
</div>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important"> Sqoop Import 应用场景——导入全表</span></span></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
　　（1）不指定目录 （则默认是在/user/hadoop/下）</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=bc54a8668e42e94ce48c6b1ac6734a79&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317163727166%2D470860338%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
　　我这里啊，给大家尝试另一个软件。（为什么，要这样带大家使用，是为了你们的多适应和多自学能力）（别嫌麻烦！）</p>
<h1 style="margin:4px; padding:0px; font-size:16px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<a target="_blank" href="http://www.cnblogs.com/zlslch/p/5971271.html" rel="nofollow" style="margin:0px 4px; padding:0px; text-decoration:none; color:rgb(0,0,238); float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">SQLyog之MySQL客户端的下载、安装和使用</a></h1>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=0ab4aeb1c6d2e9333b80bb378edb0980&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317163758432%2D1927047739%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=9d7e6c5771b377389c60a68ff871c5ec&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317163842823%2D1863849188%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=7d00b01fb3289f0ff07db86aabfe9abb&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317163954745%2D2073350737%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=f1dc6ca5531bc9ee2687921af2bda4da&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317164026338%2D1485723800%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
 　　这里，我们选择在hive这个数据库里，创建新的表，命名为</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=7fa00012bae8eee25748a470ce11c0be&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317164421245%2D1217974438%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=82f418ab8693bcf4605dc61d0e54af65&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317170458104%2D1317033458%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=7046bb75cbbf7e0edd68493357408f19&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317170605416%2D1686280964%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
 　　如果，面对 SQLyog不能正常显示中文数据的情况：在SQLyog下输入SET character_set_results = gb2312（或 gbk），执行，重新启动SQLyog，显示应该也可以看到你所插入的中文数据了。</p>
<h1 style="margin:4px; padding:0px; font-size:16px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<a target="_blank" href="http://www.cnblogs.com/zlslch/p/6567641.html" rel="nofollow" style="margin:0px 4px; padding:0px; text-decoration:none; color:rgb(0,0,238); float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">SQLyog软件里无法插入中文（即由默认的latin1改成UTF8编码格式）</a></h1>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=903b7dd32c9fca296da3d30da550b3be&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317194103416%2D1337812991%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
　　注意，我的数据表是djt-user。我这里改名啦！</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=7befa6ddf8214d0fa862a0b04672f1e3&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317194959120%2D594050557%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important">[hadoop@djt002 sqoopRunCreate]$ <span style="margin:0px 4px; padding:0px; float:none; white-space:pre; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">sqoop import \
</span></span>&gt; <span style="margin:0px 4px; padding:0px; float:none; white-space:pre; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">--connect jdbc:mysql://192.168.80.200/hive \</span></span>
&gt; <span style="margin:0px 4px; padding:0px; float:none; white-space:pre; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">--username hive \
</span></span>&gt; <span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">--password-file /user/hadoop/.password \
</span></span>&gt; <span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">--table djt-user</span></span></pre>
</div>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=572b61d937dc65a22626cb73bf23acde&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317195253463%2D1901919778%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=063ff2c3edaabccf0d6e1bd1fb3770c6&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317195317729%2D1433161927%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=c2127eeca0748a08bd01ce05999bab27&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317195329713%2D1494989071%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=2f6cdf930016f2f348fe7a5e7c0c6cc0&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317195445526%2D1065239176%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=8408e0e80fb60e76bf02e16bafe8ce2b&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317195613026%2D1067734310%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=a342c6108f9a73cca9a13536e82083b8&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317195747245%2D1261181150%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=c583c3856fae82af0d5effac7c934f09&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317195939620%2D1938654478%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=a53a87fef144a872fc0bf157d2c75138&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317200013854%2D1280524807%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=30fd5437701509af9b61335f604a2997&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317200237823%2D1808587684%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=41ba0b4943d0e1bc12e45f111832533e&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317200345135%2D1809729816%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important">SET character_set_database=<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">utf8;
SET character_set_server</span>=<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">utf8;
SHOW VARIABLES LIKE </span>'character%'; </pre>
</div>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=b49a08579bd8d2cbb7735cc4bf3988df&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317200428635%2D1172285145%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=14e81b92c240c2382525f5ba47c06e0d&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317200950166%2D770936517%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=f0aeb295cb7810a59ec2eedfb3d2e03a&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317201147401%2D1401706302%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=a7912d6d862f0030aefb26555e97efaf&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317201403260%2D495666053%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=2d32a0ab18ef42a36f2d29b29514a82c&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317201500151%2D1523296263%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=19c947462da944c31c00c67a6de98fdb&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317201639401%2D1241540328%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important">[hadoop@djt002 ~]$ $HADOOP_HOME/bin/hadoop fs -rmr /user/hadoop/djt-user</pre>
</div>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=6f371b67aa55a6492947e9ca7921e897&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317201715026%2D2029749844%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important">[hadoop@djt002 sqoopRunCreate]$ sqoop <span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">import</span> --connect jdbc:mysql:<span style="margin:0px 4px; padding:0px; color:rgb(0,128,0); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">//</span><span style="margin:0px 4px; padding:0px; color:rgb(0,128,0); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">192.168.80.200/hive --username hive --password-file /user/hadoop/.password --table djt-user</span></pre>
</div>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
[hadoop@djt002 sqoopRunCreate]$ sqoop import --connect jdbc:mysql://192.168.80.200/hive --username hive --password-file /user/hadoop/.password --table djt-user<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Warning: /usr/local/sqoop/sqoop-1.4.6/../hcatalog does not exist! HCatalog jobs will fail.<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Please set $HCAT_HOME to the root of your HCatalog installation.<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Warning: /usr/local/sqoop/sqoop-1.4.6/../accumulo does not exist! Accumulo imports will fail.<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Please set $ACCUMULO_HOME to the root of your Accumulo installation.<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Warning: /usr/local/sqoop/sqoop-1.4.6/../zookeeper does not exist! Accumulo imports will fail.<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Please set $ZOOKEEPER_HOME to the root of your Zookeeper installation.<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:17:10 INFO sqoop.Sqoop: Running Sqoop version: 1.4.6<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
SLF4J: Class path contains multiple SLF4J bindings.<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
SLF4J: Found binding in [jar:file:/usr/local/hadoop/hadoop-2.6.0/share/hadoop/common/lib/slf4j-log4j12-1.7.5.jar!/org/slf4j/impl/StaticLoggerBinder.class]<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
SLF4J: Found binding in [jar:file:/usr/local/hbase/hbase-1.2.3/lib/slf4j-log4j12-1.7.5.jar!/org/slf4j/impl/StaticLoggerBinder.class]<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
SLF4J: Actual binding is of type [org.slf4j.impl.Log4jLoggerFactory]<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:17:14 INFO manager.MySQLManager: Preparing to use a MySQL streaming resultset.<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:17:14 INFO tool.CodeGenTool: Beginning code generation<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:17:15 INFO manager.SqlManager: Executing SQL statement: SELECT t.* FROM `djt-user` AS t LIMIT 1<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:17:15 INFO manager.SqlManager: Executing SQL statement: SELECT t.* FROM `djt-user` AS t LIMIT 1<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:17:15 INFO orm.CompilationManager: HADOOP_MAPRED_HOME is /usr/local/hadoop/hadoop-2.6.0<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Note: /tmp/sqoop-hadoop/compile/38104c9fe28c7f43fdb42c26826dbf91/djt_user.java uses or overrides a deprecated API.<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Note: Recompile with -Xlint:deprecation for details.<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:17:21 INFO orm.CompilationManager: Writing jar file: /tmp/sqoop-hadoop/compile/38104c9fe28c7f43fdb42c26826dbf91/djt-user.jar<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:17:21 WARN manager.MySQLManager: It looks like you are importing from mysql.<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:17:21 WARN manager.MySQLManager: This transfer can be faster! Use the --direct<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:17:21 WARN manager.MySQLManager: option to exercise a MySQL-specific fast path.<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:17:21 INFO manager.MySQLManager: Setting zero DATETIME behavior to convertToNull (mysql)<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:17:21 INFO mapreduce.ImportJobBase: Beginning import of djt-user<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:17:21 INFO Configuration.deprecation: mapred.jar is deprecated. Instead, use mapreduce.job.jar<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:17:21 INFO Configuration.deprecation: mapred.map.tasks is deprecated. Instead, use mapreduce.job.maps<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:17:22 INFO client.RMProxy: Connecting to ResourceManager at /0.0.0.0:8032</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
17/03/18 04:17:30 INFO db.DBInputFormat: Using read commited transaction isolation<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:17:30 INFO db.DataDrivenDBInputFormat: BoundingValsQuery: SELECT MIN(`id`), MAX(`id`) FROM `djt-user`<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:17:31 INFO mapreduce.JobSubmitter: number of splits:3<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:17:32 INFO mapreduce.JobSubmitter: Submitting tokens for job: job_1489767532299_0002<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:17:33 INFO impl.YarnClientImpl: Submitted application application_1489767532299_0002<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:17:33 INFO mapreduce.Job: The url to track the job: http://djt002:8088/proxy/application_1489767532299_0002/<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:17:33 INFO mapreduce.Job: Running job: job_1489767532299_0002<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:18:03 INFO mapreduce.Job: Job job_1489767532299_0002 running in uber mode : false<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:18:03 INFO mapreduce.Job: map 0% reduce 0%</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
17/03/18 04:19:09 INFO mapreduce.Job: map 67% reduce 0%<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:19:12 INFO mapreduce.Job: map 100% reduce 0%<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:19:13 INFO mapreduce.Job: Job job_1489767532299_0002 completed successfully<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:19:13 INFO mapreduce.Job: Counters: 30<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
File System Counters<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
FILE: Number of bytes read=0<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
FILE: Number of bytes written=370638<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
FILE: Number of read operations=0<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
FILE: Number of large read operations=0<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
FILE: Number of write operations=0<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
HDFS: Number of bytes read=295<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
HDFS: Number of bytes written=105<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
HDFS: Number of read operations=12<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
HDFS: Number of large read operations=0<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
HDFS: Number of write operations=6<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Job Counters <br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Launched map tasks=3<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Other local map tasks=3<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Total time spent by all maps in occupied slots (ms)=174022<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Total time spent by all reduces in occupied slots (ms)=0<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Total time spent by all map tasks (ms)=174022<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Total vcore-seconds taken by all map tasks=174022<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Total megabyte-seconds taken by all map tasks=178198528<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Map-Reduce Framework<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Map input records=3<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Map output records=3<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Input split bytes=295<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Spilled Records=0<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Failed Shuffles=0<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Merged Map outputs=0<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
GC time elapsed (ms)=5172<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
CPU time spent (ms)=9510</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
Physical memory (bytes) snapshot=362741760<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Virtual memory (bytes) snapshot=2535641088<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Total committed heap usage (bytes)=181862400<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
File Input Format Counters <br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Bytes Read=0<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
File Output Format Counters <br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Bytes Written=105<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:19:13 INFO mapreduce.ImportJobBase: Transferred 105 bytes in 111.9157 seconds (0.9382 bytes/sec)<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:19:13 INFO mapreduce.ImportJobBase: Retrieved 3 records.<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
[hadoop@djt002 sqoopRunCreate]$</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=0190cee48ac77aa9c385a31544fb3932&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317202029463%2D2112966837%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=e7b48494bffec975a09c04a00932f940&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317202111041%2D499302194%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=e608f7d925d097792c8575c3d0949e3f&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317202125135%2D1450561021%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=97c0b79bf32403f4f01d1b22b3e6ce72&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317202222776%2D87625310%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=14bcb753cbe47ecd21c5f18d5fe45e85&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317202550041%2D1485470653%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important">[hadoop@djt002 ~]$ $HADOOP_HOME/bin/hadoop fs -cat /user/hadoop/djt-user/part-m-*
1,王菲,female,36<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">,歌手
</span>2,谢霆锋,male,30<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">,歌手
</span>3,周杰伦,male,33<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">,导演
[hadoop@djt002 </span>~]$ </pre>
</div>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important">　总结</span></span></p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin-top:0px; margin-bottom:0px; padding:0px; font-size:18px!important; line-height:30px!important"></pre>
<pre style="margin-right:4px; margin-left:4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">不指定目录  

sqoop import<span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important"> \
--<span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important">connect 'jdbc:mysql://192.168.128.200/hive<span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important"> \
--<span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important">username hive \
--password-file /user/hadoop/<span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important">.password \
--table djt_user</span></span></span></span></span></span><br style="margin:0px 4px; padding:0px; float:none; white-space:pre; text-overflow:clip; clear:none; display:inline; line-height:30px!important"></pre>
<pre style="margin-right:4px; margin-left:4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"><br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">不指定目录    （推荐这种）

sqoop </span><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">import</span><span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>--<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">connect </span>'jdbc:mysql://192.168.128.200/hive?useUnicode=true&amp;characterEncoding=utf-8'<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>--<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">username hive \
</span>--password-file /user/hadoop/<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">.password \
</span>--table djt_user</pre>
</div>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
 　　　　即，<span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">djt_user是在MySQL里</span></span>，通过Sqoop工具，导入到<span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">HDFS里，这里是在/user/hadoop/djt_user</span></span>。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
（2）指定目录</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
　　任意可以指定的。</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=b5a1bc121cae8c1a2e5087aec3273a57&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317203039995%2D1855560756%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=30003d547a0037e60124697ae223fd55&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317203344745%2D658172514%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important">[hadoop@djt002 ~]$ $HADOOP_HOME/bin/hadoop fs -cat /sqoop/test/djt_user/part-m-*
1,王菲,female,36<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">,歌手
</span>2,谢霆锋,male,30<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">,歌手
</span>3,周杰伦,male,33<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">,导演
[hadoop@djt002 </span>~]$ </pre>
</div>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
 　　这里，<span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">为统一标准和规范化</span></span>，用数据表djt_user。</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=740f4cfa88b0996c7fa0dc5b100012e5&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317203439979%2D308484914%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=a3d7087c5b76cf1a9d2c02d5048c05cc&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317203458979%2D729033056%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=70baff54da78a561db679296d06b053d&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317203517073%2D908787561%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
 　　（3）目录已存在</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=6f14c54d554dd56a327b904a3a4cc9f4&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317204403620%2D1174277263%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important">[hadoop@djt002 sqoopRunCreate]$ sqoop <span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">import</span><span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>&gt; --connect jdbc:mysql:<span style="margin:0px 4px; padding:0px; color:rgb(0,128,0); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">//</span><span style="margin:0px 4px; padding:0px; color:rgb(0,128,0); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">192.168.80.200/hive \</span>
&gt; --<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">username hive \
</span>&gt; --password-file /user/hadoop/<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">.password \
</span>&gt; --<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">table djt_user \
</span>&gt; --target-dir /sqoop/test/<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">djt_user \
</span>&gt; --delete-target-dir</pre>
</div>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
Warning: /usr/local/sqoop/sqoop-1.4.6/../hcatalog does not exist! HCatalog jobs will fail.<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Please set $HCAT_HOME to the root of your HCatalog installation.<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Warning: /usr/local/sqoop/sqoop-1.4.6/../accumulo does not exist! Accumulo imports will fail.<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Please set $ACCUMULO_HOME to the root of your Accumulo installation.<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Warning: /usr/local/sqoop/sqoop-1.4.6/../zookeeper does not exist! Accumulo imports will fail.<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Please set $ZOOKEEPER_HOME to the root of your Zookeeper installation.<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:43:40 INFO sqoop.Sqoop: Running Sqoop version: 1.4.6<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
SLF4J: Class path contains multiple SLF4J bindings.<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
SLF4J: Found binding in [jar:file:/usr/local/hadoop/hadoop-2.6.0/share/hadoop/common/lib/slf4j-log4j12-1.7.5.jar!/org/slf4j/impl/StaticLoggerBinder.class]<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
SLF4J: Found binding in [jar:file:/usr/local/hbase/hbase-1.2.3/lib/slf4j-log4j12-1.7.5.jar!/org/slf4j/impl/StaticLoggerBinder.class]<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
SLF4J: Actual binding is of type [org.slf4j.impl.Log4jLoggerFactory]<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:43:45 INFO manager.MySQLManager: Preparing to use a MySQL streaming resultset.<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:43:45 INFO tool.CodeGenTool: Beginning code generation<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:43:46 INFO manager.SqlManager: Executing SQL statement: SELECT t.* FROM `djt_user` AS t LIMIT 1<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:43:46 INFO manager.SqlManager: Executing SQL statement: SELECT t.* FROM `djt_user` AS t LIMIT 1<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:43:46 INFO orm.CompilationManager: HADOOP_MAPRED_HOME is /usr/local/hadoop/hadoop-2.6.0<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Note: /tmp/sqoop-hadoop/compile/1fae17dd362476d95608e216756efa34/djt_user.java uses or overrides a deprecated API.<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Note: Recompile with -Xlint:deprecation for details.<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:43:52 INFO orm.CompilationManager: Writing jar file: /tmp/sqoop-hadoop/compile/1fae17dd362476d95608e216756efa34/djt_user.jar<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:43:52 INFO tool.ImportTool: Destination directory /sqoop/test/djt_user deleted.<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:43:52 WARN manager.MySQLManager: It looks like you are importing from mysql.<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:43:52 WARN manager.MySQLManager: This transfer can be faster! Use the --direct<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:43:52 WARN manager.MySQLManager: option to exercise a MySQL-specific fast path.</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
17/03/18 04:43:52 INFO manager.MySQLManager: Setting zero DATETIME behavior to convertToNull (mysql)<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:43:52 INFO mapreduce.ImportJobBase: Beginning import of djt_user<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:43:52 INFO Configuration.deprecation: mapred.jar is deprecated. Instead, use mapreduce.job.jar<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:43:52 INFO Configuration.deprecation: mapred.map.tasks is deprecated. Instead, use mapreduce.job.maps<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:43:53 INFO client.RMProxy: Connecting to ResourceManager at /0.0.0.0:8032<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:44:02 INFO db.DBInputFormat: Using read commited transaction isolation<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:44:02 INFO db.DataDrivenDBInputFormat: BoundingValsQuery: SELECT MIN(`id`), MAX(`id`) FROM `djt_user`<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:44:02 INFO mapreduce.JobSubmitter: number of splits:3<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:44:03 INFO mapreduce.JobSubmitter: Submitting tokens for job: job_1489767532299_0005<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:44:03 INFO impl.YarnClientImpl: Submitted application application_1489767532299_0005<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:44:03 INFO mapreduce.Job: The url to track the job: http://djt002:8088/proxy/application_1489767532299_0005/<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:44:03 INFO mapreduce.Job: Running job: job_1489767532299_0005<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:44:23 INFO mapreduce.Job: Job job_1489767532299_0005 running in uber mode : false<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:44:23 INFO mapreduce.Job: map 0% reduce 0%</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
17/03/18 04:45:21 INFO mapreduce.Job: map 67% reduce 0%<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:45:23 INFO mapreduce.Job: map 100% reduce 0%<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:45:23 INFO mapreduce.Job: Job job_1489767532299_0005 completed successfully<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:45:24 INFO mapreduce.Job: Counters: 30<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
File System Counters<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
FILE: Number of bytes read=0<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
FILE: Number of bytes written=370635<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
FILE: Number of read operations=0<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
FILE: Number of large read operations=0<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
FILE: Number of write operations=0<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
HDFS: Number of bytes read=295<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
HDFS: Number of bytes written=80<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
HDFS: Number of read operations=12<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
HDFS: Number of large read operations=0<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
HDFS: Number of write operations=6<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Job Counters <br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Launched map tasks=3<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Other local map tasks=3<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Total time spent by all maps in occupied slots (ms)=163316<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Total time spent by all reduces in occupied slots (ms)=0<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Total time spent by all map tasks (ms)=163316<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Total vcore-seconds taken by all map tasks=163316<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Total megabyte-seconds taken by all map tasks=167235584<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Map-Reduce Framework<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Map input records=3<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Map output records=3<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Input split bytes=295<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Spilled Records=0<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Failed Shuffles=0<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Merged Map outputs=0<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
GC time elapsed (ms)=3240<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
CPU time spent (ms)=8480</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
Physical memory (bytes) snapshot=356696064<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Virtual memory (bytes) snapshot=2535596032<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Total committed heap usage (bytes)=181862400<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
File Input Format Counters <br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Bytes Read=0<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
File Output Format Counters <br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Bytes Written=80<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:45:24 INFO mapreduce.ImportJobBase: Transferred 80 bytes in 91.6189 seconds (0.8732 bytes/sec)<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 04:45:24 INFO mapreduce.ImportJobBase: Retrieved 3 records.<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
[hadoop@djt002 sqoopRunCreate]$</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=6d8ac07afdaeb6de8bdd9bc5a195c344&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317204634901%2D62973029%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=b783c36880be140acd8cee034f28d75b&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317204641870%2D1064599307%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important"></span></span></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important"> Sqoop Import 应用场景——控制并行度</span></span></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
　　（1）控制并行度</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
　　　　默认是4个，当然我这里数据量小，指定1个就行了。</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=1517a17e4220cea74858ab114c9ec675&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317205133291%2D1609446237%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important">[hadoop@djt002 ~]$ $HADOOP_HOME/bin/hadoop fs -rm  /sqoop/test/djt_user/part-m-*</pre>
</div>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=5c3b473c799206b3817a0061f38f6939&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317214533151%2D2143735353%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important">[hadoop@djt002 sqoopRunCreate]$ sqoop <span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">import</span><span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>&gt; --connect 'jdbc:mysql://192.168.80.200/hive?useUnicode=true&amp;characterEncoding=utf-8'<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>&gt; --<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">username hive \
</span>&gt; --password-file /user/hadoop/<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">.password \
</span>&gt; --<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">table djt_user \
</span>&gt; --target-dir /sqoop/test/<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">djt_user \
</span>&gt; --delete-target-<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">dir \
</span>&gt; -m 1</pre>
</div>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
 　　在这里，可能会遇到这个问题。</p>
<h1 style="margin:4px; padding:0px; font-size:16px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<a target="_blank" href="http://www.cnblogs.com/zlslch/p/6568403.html" rel="nofollow" style="margin:0px 4px; padding:0px; text-decoration:none; color:rgb(0,0,238); float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">Sqoop异常解决ERROR
 tool.ImportTool: Encountered IOException running import job: java.io.IOException: No columns to generate for ClassWriter问题</a></h1>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=46667a41ef423379154b2bcb8cf41c6e&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317214937120%2D1311813870%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=14b6249a2acbb4408b8cb1ff63e4ca75&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317215108838%2D776994484%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important">[hadoop@djt002 ~]$ $HADOOP_HOME/bin/hadoop fs -cat /sqoop/test/djt_user/part-m-*</pre>
</div>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important">Sqoop Import 应用场景——控制字段分隔符</span></span></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
　　　　（1）控制字段分隔符</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
 　　注意，默认的控制分段分隔符是逗号，我们这里自定义。</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=c6d7bb15df401356719ae86b115719a1&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317215700229%2D2089355861%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important">[hadoop@djt002 sqoopRunCreate]$ sqoop <span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">import</span><span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>&gt; --connect 'jdbc:mysql://192.168.80.200/hive?useUnicode=true&amp;characterEncoding=utf-8'<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>&gt; --<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">username hive \
</span>&gt; --password-file /user/hadoop/<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">.password \
</span>&gt; --<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">table djt_user \
</span>&gt; --target-dir /sqoop/test/<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">djt_user \
</span>&gt; --delete-target-<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">dir \
</span>&gt; -m 1<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>&gt; --fields-terminated-by "@"</pre>
</div>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
　　这里，<span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">djt_user是在MySQL里</span></span>，通过Sqoop工具，导入到<span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">HDFS里，是在/sqoop/test/djt_user。</span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=af07c4dcfb44df9041228fbf900368a3&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317215824338%2D386173046%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important">[hadoop@djt002 ~]$ $HADOOP_HOME/bin/hadoop fs -cat /sqoop/test/djt_user/part-m-*
1@王菲@female@36<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">@歌手
</span>2@谢霆锋@male@30<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">@歌手
</span>3@周杰伦@male@33<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">@导演
[hadoop@djt002 </span>~]$ </pre>
</div>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
　　（2）手动增量导入</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=d3aebc2247916671ded7ef53ca8031dc&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317220239338%2D679598221%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
            我们加入，4、5和6。</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=ce6f8580a2e9758ed78478afcf0397ce&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317220448916%2D1932980517%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important">[hadoop@djt002 sqoopRunCreate]$ sqoop <span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">import</span><span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>&gt; --connect 'jdbc:mysql://192.168.80.200/hive?useUnicode=true&amp;characterEncoding=utf-8'<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>&gt; --<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">username hive \
</span>&gt; --password-file /user/hadoop/<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">.password \
</span>&gt; --<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">table djt_user \
</span>&gt; --target-dir /sqoop/test/<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">djt_user \
</span>&gt; -m 1<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>&gt; --fields-terminated-by "@"<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>&gt; --<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">append \
</span>&gt; --check-column 'id'<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>&gt; --<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">incremental append \
</span>&gt; --last-value 3</pre>
</div>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
　　这里，<span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">djt_user是在MySQL里</span></span>，通过Sqoop工具，导入到<span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">HDFS里，是在/sqoop/test/djt_user</span>。</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=24af736c0ec1d6a62a5636241230864c&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317220542541%2D1633732505%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important">[hadoop@djt002 ~]$ $HADOOP_HOME/bin/hadoop fs -cat /sqoop/test/djt_user/part-m-*
1@王菲@female@36<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">@歌手
</span>2@谢霆锋@male@30<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">@歌手
</span>3@周杰伦@male@33<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">@导演
</span>4@王力宏@male@40<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">@演员
</span>5@张三@male@39<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">@无业游民
</span>6@李四@female@18<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">@学生
[hadoop@djt002 </span>~]$ </pre>
</div>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
    　　（3）自动增量导入</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=cb0b037f8023b1f3effa11e596b1352f&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317221230995%2D1130081137%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=d8fff533d92557d10ba4a0cde48380ed&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317224729541%2D1242132200%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">[hadoop@djt002 sqoopRunCreate]$ sqoop job \
</span>&gt; --<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">create job_import_djt_user \
</span>&gt; -- <span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">import</span><span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>&gt; --connect 'jdbc:mysql://192.168.80.200/hive?useUnicode=true&amp;characterEncoding=utf-8'<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>&gt; --<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">username hive \
</span>&gt; --password-file /user/hadoop/<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">.password \
</span>&gt; --<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">table djt_user \
</span>&gt; --target-dir /sqoop/test/<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">djt_user \
</span>&gt; -m 1<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>&gt; --fields-terminated-by "@"<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>&gt; --<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">append \
</span>&gt; --check-column 'id'<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>&gt; --<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">incremental append \
</span>&gt; --last-value 6<br style="margin:0px 4px; padding:0px; float:none; white-space:pre; text-overflow:clip; clear:none; display:inline; line-height:30px!important">[hadoop@djt002 sqoopRunCreate]$ sqoop job --exec job_import_djt_user</pre>
</div>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
 　　这里，<span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">djt_user是在MySQL里</span></span>，通过Sqoop工具，导入到<span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">HDFS里，是在/sqoop/test/djt_user</span>。</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">删除某个job</span></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=94dc172ef33050b45f96f8d4e0d77ded&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317224300995%2D1845994775%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important"> [hadoop@djt002 sqoopRunCreate]$ sqoop job --delete job_import_djt_user</pre>
</div>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">查看当前可用的job</span></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
 [hadoop@djt002 sqoopRunCreate]$ sqoop job --list</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=c8a77de7ef1cd5ee50ce00465269e502&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317225031995%2D1725922359%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">查看某个具体job的信息</span></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
 [hadoop@djt002 sqoopRunCreate]$  sqoop job --show job_import_djt_user</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=9810e5c3b2fd199b802c732d5d963ae9&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170317225223166%2D17449009%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
[hadoop@djt002 sqoopRunCreate]$ sqoop job --show<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Warning: /usr/local/sqoop/sqoop-1.4.6/../hcatalog does not exist! HCatalog jobs will fail.<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Please set $HCAT_HOME to the root of your HCatalog installation.<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Warning: /usr/local/sqoop/sqoop-1.4.6/../accumulo does not exist! Accumulo imports will fail.<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Please set $ACCUMULO_HOME to the root of your Accumulo installation.<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Warning: /usr/local/sqoop/sqoop-1.4.6/../zookeeper does not exist! Accumulo imports will fail.<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Please set $ZOOKEEPER_HOME to the root of your Zookeeper installation.<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 06:50:58 INFO sqoop.Sqoop: Running Sqoop version: 1.4.6<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Missing argument for option: show<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
[hadoop@djt002 sqoopRunCreate]$ clear<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
[hadoop@djt002 sqoopRunCreate]$ sqoop job --show job_import_djt_user<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Warning: /usr/local/sqoop/sqoop-1.4.6/../hcatalog does not exist! HCatalog jobs will fail.<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Please set $HCAT_HOME to the root of your HCatalog installation.<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Warning: /usr/local/sqoop/sqoop-1.4.6/../accumulo does not exist! Accumulo imports will fail.<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Please set $ACCUMULO_HOME to the root of your Accumulo installation.<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Warning: /usr/local/sqoop/sqoop-1.4.6/../zookeeper does not exist! Accumulo imports will fail.<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Please set $ZOOKEEPER_HOME to the root of your Zookeeper installation.<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
17/03/18 06:51:47 INFO sqoop.Sqoop: Running Sqoop version: 1.4.6<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
SLF4J: Class path contains multiple SLF4J bindings.<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
SLF4J: Found binding in [jar:file:/usr/local/hadoop/hadoop-2.6.0/share/hadoop/common/lib/slf4j-log4j12-1.7.5.jar!/org/slf4j/impl/StaticLoggerBinder.class]<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
SLF4J: Found binding in [jar:file:/usr/local/hbase/hbase-1.2.3/lib/slf4j-log4j12-1.7.5.jar!/org/slf4j/impl/StaticLoggerBinder.class]<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
SLF4J: Actual binding is of type [org.slf4j.impl.Log4jLoggerFactory]<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Job: job_import_djt_user<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Tool: import<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Options:<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
----------------------------<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
verbose = false<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
incremental.last.value = 10<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
db.connect.string = jdbc:mysql://192.168.80.200/hive?useUnicode=true&amp;characterEncoding=utf-8<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
codegen.output.delimiters.escape = 0<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
codegen.output.delimiters.enclose.required = false</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
codegen.input.delimiters.field = 0<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
hbase.create.table = false<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
hdfs.append.dir = true<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
db.table = djt_user<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
codegen.input.delimiters.escape = 0<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
import.fetch.size = null<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
accumulo.create.table = false<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
codegen.input.delimiters.enclose.required = false<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
db.username = hive<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
reset.onemapper = false<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
codegen.output.delimiters.record = 10<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
import.max.inline.lob.size = 16777216<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
hbase.bulk.load.enabled = false<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
hcatalog.create.table = false<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
db.clear.staging.table = false<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
incremental.col = id<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
codegen.input.delimiters.record = 0<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
db.password.file = /user/hadoop/.password<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
enable.compression = false<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
hive.overwrite.table = false<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
hive.import = false<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
codegen.input.delimiters.enclose = 0<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
accumulo.batch.size = 10240000<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
hive.drop.delims = false<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
codegen.output.delimiters.enclose = 0<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
hdfs.delete-target.dir = false</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
codegen.output.dir = .<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
codegen.auto.compile.dir = true<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
relaxed.isolation = false<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
mapreduce.num.mappers = 1<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
accumulo.max.latency = 5000<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
import.direct.split.size = 0<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
codegen.output.delimiters.field = 64<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
export.new.update = UpdateOnly<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
incremental.mode = AppendRows<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
hdfs.file.format = TextFile<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
codegen.compile.dir = /tmp/sqoop-hadoop/compile/d81bf23cb3eb8eb11e7064a16df0b92b<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
direct.import = false<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
hdfs.target.dir = /sqoop/test/djt_user<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
hive.fail.table.exists = false<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
db.batch = false<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
[hadoop@djt002 sqoopRunCreate]$</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important">Sqoop Import 应用场景——启动压缩</span></span></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
　　 启动压缩</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
 　　默认是gzip压缩，具体去看官网</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=80ab8d039568c7ab811a5bd2747acda7&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318100604370%2D28645877%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important">[hadoop@djt002 sqoopRunCreate]$ sqoop <span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">import</span><span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>&gt; --connect 'jdbc:mysql://192.168.80.200/hive?useUnicode=true&amp;characterEncoding=utf-8'<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>&gt; --<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">username hive \
</span>&gt; --password-file /user/hadoop/<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">.password \
</span>&gt; -<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">table djt_user \
</span>&gt; --target-dir /sqoop/test/<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">djt_user \
</span>&gt; --delete-target-<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">dir \
</span>&gt; -m 1<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>&gt; --fields-terminated-by "@"<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>&gt; -z</pre>
</div>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
 　　这里，<span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">djt_user是在MySQL里</span></span>，通过Sqoop工具，导入到<span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">HDFS里，是在/sqoop/test/djt_user</span>。</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=308588afaf49ff7230555361516b4ddd&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318100708354%2D1549750068%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=04f2017bac17956b3693e6993e9715aa&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318100822448%2D322725849%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important">[hadoop@djt002 ~]$ $HADOOP_HOME/bin/hadoop fs -cat /sqoop/test/djt_user/part-m-*</pre>
</div>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important">Sqoop Import 应用场景——导入空值处理</span></span></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
　　（1）导入空值处理</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=5fd83abfcfd9a723c9a424183d3c30de&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318100947198%2D294554899%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
 　　先，不加空值处理，看是怎样的。</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=8e4647457912b203147b8bf2e3aa7a09&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318101253713%2D585199366%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important">[hadoop@djt002 sqoopRunCreate]$ sqoop <span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">import</span><span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>&gt; --connect 'jdbc:mysql://192.168.80.200/hive?useUnicode=true&amp;characterEncoding=utf-8'<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>&gt; --<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">username hive \
</span>&gt; --password-file /user/hadoop/<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">.password \
</span>&gt; -<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">table djt_user \
</span>&gt; --target-dir /sqoop/test/<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">djt_user \
</span>&gt; --delete-target-<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">dir \
</span>&gt; -m 1<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>&gt; --fields-terminated-by "@"
&gt; </pre>
</div>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
 　　这里，<span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">djt_user是在MySQL里</span></span>，通过Sqoop工具，导入到<span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">HDFS里，是在/sqoop/test/djt_user</span>。</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=c64c76d9cae82c8110df4ee581192d27&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318101342026%2D76139679%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important">[hadoop@djt002 ~]$ $HADOOP_HOME/bin/hadoop fs -cat /sqoop/test/djt_user/part-m-*</pre>
</div>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
 　　所以，一般需要对null进行转换，即需对空值进行处理。比如年龄那列，要么给他假如是18岁定死，要么就是0等。</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=c080372e7e1690810f5ad3f2e52f2d9d&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318101739323%2D352826193%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important">[hadoop@djt002 sqoopRunCreate]$ sqoop <span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">import</span><span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>&gt; --connect 'jdbc:mysql://192.168.80.200/hive?useUnicode=true&amp;characterEncoding=utf-8'<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>&gt; --<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">username hive \
</span>&gt; --password-file /user/hadoop/<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">.password \
</span>&gt; -<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">table djt_user \
</span>&gt; --target-dir /sqoop/test/<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">djt_user \
</span>&gt; --delete-target-<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">dir \
</span>&gt; -m 1<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>&gt; --fields-terminated-by "@"<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>&gt; --<span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">null</span>-non-string "###"<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>&gt; --<span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">null</span>-string "###"</pre>
</div>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
　　这里，<span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">djt_user是在MySQL里</span></span>，通过Sqoop工具，导入到<span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">HDFS里，是在/sqoop/test/djt_user</span>。</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
 　　我这里，将空值null转换成###，这个，大家可以根据自己的需要，可以转换成其它的，不多赘述。自行去举一反三。</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=b1428c362574d47d61c4fe360848221a&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318101911041%2D257399661%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important">[hadoop@djt002 ~]$ $HADOOP_HOME/bin/hadoop fs -cat /sqoop/test/djt_user/part-m-*</pre>
</div>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
 　　下面呢，这个场景，比如，如下，我不需全部的字段导出，非空值的某部分字段呢，该如何操作啊?</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">Sqoop Import 应用场景——导入部分数据</span></span></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
　　（1）使用–columns </p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
　　即，指定某个或某些字段导入</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=eb1f37be66743bd7c74b01c97499bb1d&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318102204182%2D774492321%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
 　　比如，我这里，指定只导入id和name，当然，你可以去指定更多，我这里只是个参考和带入门的引子实例罢了。</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=a761d8162a88f7c2f3def2c5dd1666f2&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318102857901%2D1493254228%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important">[hadoop@djt002 sqoopRunCreate]$ sqoop <span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">import</span><span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>&gt; --connect 'jdbc:mysql://192.168.80.200/hive?useUnicode=true&amp;characterEncoding=utf-8'<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>&gt; --<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">username hive \
</span>&gt; --password-file /user/hadoop/<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">.password \
</span>&gt; -<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">table djt_user \
</span>&gt; <span style="margin:0px 4px; padding:0px; float:none; white-space:pre; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">--columns id,name \
</span></span>&gt; --target-dir /sqoop/test/<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">djt_user \
</span>&gt; --delete-target-<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">dir \
</span>&gt; -m 1<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>&gt; --fields-terminated-by "@"<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>&gt; --<span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">null</span>-non-string "###"<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>&gt; --<span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">null</span>-string "###"</pre>
</div>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
　　这里，<span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">djt_user是在MySQL里</span></span>，通过Sqoop工具，导入到<span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">HDFS里，是在/sqoop/test/djt_user</span>。</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=fda77b4ed91b734d50b5bcd72a8521dc&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318102957307%2D1598946798%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important">[hadoop@djt002 ~]$ $HADOOP_HOME/bin/hadoop fs -cat /sqoop/test/djt_user/part-m-*
1<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">@王菲
</span>2<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">@谢霆锋
</span>3<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">@周杰伦
</span>4<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">@王力宏
</span>5<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">@张三
</span>6<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">@李四
</span>7<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">@王五
</span>8<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">@王六
</span>9<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">@小王
</span>10<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">@小林
[hadoop@djt002 </span>~]$ </pre>
</div>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
　　（2）使用–where</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
 　　刚是导入指定的字段，也可以用筛选来导入达到目的。</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
　　比如，我这里，只想导入sex=female的。</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=b84e571dc330cf1a54c0b85dd4e81aa5&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318103406166%2D1568093501%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=b933bf4f38b482479c8e8fef01cd6f3f&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318103242182%2D1901781537%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important">[hadoop@djt002 sqoopRunCreate]$ sqoop <span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">import</span><span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>&gt; --connect 'jdbc:mysql://192.168.80.200/hive?useUnicode=true&amp;characterEncoding=utf-8'<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>&gt; --<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">username hive \
</span>&gt; --password-file /user/hadoop/<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">.password \
</span>&gt; --<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">table djt_user \
</span>&gt; <span style="margin:0px 4px; padding:0px; float:none; white-space:pre; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">--where "sex='female'" \
</span></span>&gt; --target-dir /sqoop/test/<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">djt_user \
</span>&gt; --delete-target-<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">dir \
</span>&gt; -m 1<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>&gt; --fields-terminated-by "@"<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>&gt; --<span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">null</span>-non-string "###"<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>&gt; --<span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">null</span>-string "###"</pre>
</div>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
 　　这里，<span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">djt_user是在MySQL里</span></span>，通过Sqoop工具，导入到<span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">HDFS里，是在/sqoop/test/djt_user</span>。</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=b9e3409dc0523a89bc232c885ba17b14&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318103450979%2D64903011%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important">[hadoop@djt002 ~]$ $HADOOP_HOME/bin/hadoop fs -cat /sqoop/test/djt_user/part-m-*
1@王菲@female@36<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">@歌手
</span>6@李四@female@18<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">@学生
</span>9@小王@female@24<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">@hadoop运维
</span>10@小林@female@30<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">@###
[hadoop@djt002 </span>~]$ </pre>
</div>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
 　　（3）使用–query</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
 　　比如，导入比较复杂更实用。</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=80d395e076e94f27d0727a6b9bab8824&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318103754088%2D1502659131%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=e0d5907f0edbc32174dc01aaaca8053c&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318104140932%2D467632306%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=651c7736bbb4148f300d5ea261a26240&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318104457370%2D252605012%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important">[hadoop@djt002 ~]$ $HADOOP_HOME/bin/hadoop fs -cat /sqoop/test/djt_user/part-m-*
2@谢霆锋@male@30<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">@歌手
</span>6@李四@female@18<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">@学生
</span>9@小王@female@24<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">@hadoop运维
</span>10@小林@female@30<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">@###
[hadoop@djt002 </span>~]$ </pre>
</div>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">注意</span></span><br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
　　若，从MySQL数据库导入数据到HDFS里，出现中断情况了怎么办?</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
　　答：好比MapReduce作业丢失一样，有个容错机制。但是这里，我们不用担心，任务中断导致数据重复插入，这个不需担心。</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
　　它这里呢，要么就是全部导入才成功，要么就是一条都导不进不成功。</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
      即，<span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">Sqoop
 Import HDFS 里没有“脏数据”的情况发生</span></span>。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important">MySQL里的数据通过Sqoop
 import HDFS</span>（作为扩展）</span></span></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
　　下面我们看一下 Sqoop 如何使用命令行来导入数据的，其命令行语法如下所示。</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
sqoop import \<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
--connect jdbc:mysql://192.168.80.128:3306/db_hadoop \<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
--username sqoop \<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
--password sqoop \<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
--table user \<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
--target-dir /junior/sqoop/ \ //可选，不指定目录，数据默认导入到/user下<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
--where "sex='female'" \ //可选<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
--as-sequencefile \ //可选，不指定格式，数据格式默认为 Text 文本格式<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
--num-mappers 10 \ //可选，这个数值不宜太大<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
--null-string '\\N' \ //可选 <br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
--null-non-string '\\N' \ //可选</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">--connect</span>：指定 JDBC URL。<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">--username/password</span>：mysql 数据库的用户名。<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">--table</span>：要读取的数据库表。<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">--target-dir</span>：将数据导入到指定的 HDFS 目录下，文件名称如果不指定的话，会默认数据库的表名称。<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">--where</span>：过滤从数据库中要导入的数据。<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">--as-sequencefile</span>：指定数据导入数据格式。<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">--num-mappers</span>：指定 Map 任务的并发度。<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">--null-string，--null-non-string</span>：同时使用可以将数据库中的空字段转化为'\N'，因为数据库中字段为 null，会占用很大的空间。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">下面我们介绍几种 Sqoop 数据导入的特殊应用（作为扩展）</span></span></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">1、</span></span>Sqoop 每次导入数据的时候，不需要把以往的所有数据重新导入
 HDFS，只需要把新增的数据导入 HDFS 即可，下面我们来看看如何导入新增数据。</p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; float:none; white-space:pre; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">sqoop import \
</span><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">--connect jdbc:mysql://192.168.80.128:3306/db_hadoop \</span><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">--username sqoop \
</span><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">--password sqoop \
</span><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">--table user \
</span><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">--incremental append \</span></span><span style="margin:0px 4px; padding:0px; color:rgb(0,128,0); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">//</span><span style="margin:0px 4px; padding:0px; color:rgb(0,128,0); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">代表只导入增量数据</span><span style="margin:0px 4px; padding:0px; float:none; white-space:pre; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">--check-column id \</span></span><span style="margin:0px 4px; padding:0px; color:rgb(0,128,0); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">//</span><span style="margin:0px 4px; padding:0px; color:rgb(0,128,0); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">以主键id作为判断条件</span><span style="margin:0px 4px; padding:0px; float:none; white-space:pre; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">--last-value 999</span></span><span style="margin:0px 4px; padding:0px; color:rgb(0,128,0); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">//</span><span style="margin:0px 4px; padding:0px; color:rgb(0,128,0); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">导入id大于999的新增数据</span></pre>
</div>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
　　上述三个组合使用，可以实现数据的增量导入。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">2、</span></span>Sqoop 数据导入过程中，直接输入明码存在安全隐患，我们可以通过下面两种方式规避这种风险。<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
　　1)-P:sqoop 命令行最后使用 -P，此时提示用户输入密码，而且用户输入的密码是看不见的，起到安全保护作用。密码输入正确后，才会执行 sqoop 命令。</p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important">sqoop <span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">import</span><span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>--connect jdbc:mysql:<span style="margin:0px 4px; padding:0px; color:rgb(0,128,0); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">//</span><span style="margin:0px 4px; padding:0px; color:rgb(0,128,0); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">192.168.80.128:3306/db_hadoop \</span>
--<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">username sqoop \
</span>--<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">table user \
</span>-P</pre>
</div>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
　　2)--password-file：指定一个密码保存文件，读取密码。我们可以将这个文件设置为只有自己可读的文件，防止密码泄露。</p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important">sqoop <span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">import</span><span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>--connect jdbc:mysql:<span style="margin:0px 4px; padding:0px; color:rgb(0,128,0); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">//</span><span style="margin:0px 4px; padding:0px; color:rgb(0,128,0); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">192.168.80.128:3306/db_hadoop \</span>
--<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">username sqoop \
</span>--<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">table user \
</span>--password-file my-sqoop-password</pre>
</div>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">二、通过Sqoop Export <span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important">HDFS里的数据到MySQL</span></span></span><br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
　　它的功能是将数据从 HDFS 导入关系型数据库表中，其流程图如下所示。</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=4f1848711523b43c06e993f323480a2b&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201611%2F855959%2D20161130094401506%2D1657159659%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
　　我们来分析一下 Sqoop 数据导出流程，首先用户输入一个 Sqoop export 命令，它会获取关系型数据库的 schema，<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
建立 Hadoop 字段与数据库表字段的映射关系。 然后会将输入命令转化为基于 Map 的 MapReduce作业，<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
　　这样 MapReduce作业中有很多 Map 任务，它们并行的从 HDFS 读取数据，并将整个数据拷贝到数据库中。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=146f467fc3804503178fda7f87349c70&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318105314276%2D1571435584%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
　　大家，必须要去看官网！</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important"> Sqoop Export 应用场景——直接导出</span></span></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
　　　　直接导出</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
　　请去看我下面的这篇博客，对你有好处。我不多赘述。</p>
<h1 style="margin:4px; padding:0px; font-size:16px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<a target="_blank" href="http://www.cnblogs.com/zlslch/p/6572293.html" rel="nofollow" style="margin:0px 4px; padding:0px; text-decoration:none; color:rgb(0,0,238); float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">SQLyog普通版与SQLyog企业版对比分析</a></h1>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=c9e6fe4f4a8e443cdc2b03dd6d2509b3&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318113211776%2D1906460104%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important">CREATE TABLE djt_user_copy SELECT * FROM djt_user WHERE 1=2;</pre>
</div>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=703abb75d3327a5962024f8803ac6556&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318112202995%2D1758363934%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">[hadoop@djt002 sqoopRunCreate]$ sqoop export \
</span>&gt; --connect 'jdbc:mysql://192.168.80.200/hive?useUnicode=true&amp;characterEncoding=utf-8'<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>&gt; --<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">username hive \
</span>&gt; --password-file /user/hadoop/<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">.password \
</span>&gt; --<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">table djt_user_copy \
</span>&gt; --export-dir /sqoop/test/<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">djt_user \
</span>&gt; --input-fields-terminated-by "@"</pre>
</div>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
 　　这里，<span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">HDFS里，是在/sqoop/test/djt_user</span>，<span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important">通过Sqoop工具，导出到</span><span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">djt_user_copy是在MySQL里</span>。</span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
 　　因为啊，之前，/sqoop/test/djt_user的数据如下</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=5280067242a5efeb3d9b085c1b689443&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318112129854%2D549281554%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=6e46702ecd7140db0573d4ffe04acd5d&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318113524104%2D64023160%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important"> Sqoop Export 应用场景——指定map数</span></span></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
　　指定map数</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
　   Map Task默认是4个</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=cc6df105f04a239e40024336ab0d2261&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318114252010%2D1212759630%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=79b948a81d94d299f7ba709d683f4ae4&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318114310073%2D377143181%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=a46140170d5e6ebfbe9bcb6ead744de6&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318113805260%2D1422008133%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">[hadoop@djt002 sqoopRunCreate]$ sqoop export \
</span>&gt; --connect 'jdbc:mysql://192.168.80.200/hive?useUnicode=true&amp;characterEncoding=utf-8'<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>&gt; --<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">username hive \
</span>&gt; --password-file /user/hadoop/<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">.password \
</span>&gt; --<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">table djt_user_copy \
</span>&gt; --export-dir /sqoop/test/<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">djt_user \
</span>&gt; --input-fields-terminated-by "@"<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>&gt; -m 1</pre>
</div>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
 　　这里，<span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">HDFS里，是在/sqoop/test/djt_user，<span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important">通过Sqoop工具，导出到</span><span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important">djt_user_copy是在MySQL里。</span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=f098338c58ece0882c981882e6946d2e&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318114536666%2D857265087%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important">Sqoop Export 应用场景——插入和更新</span></span></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
　　插入和更新</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=b9150cb020a0613cd9e2e03a3aff8833&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318114623307%2D292949850%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=4f2fb97b51f09fc873e87edb6357098e&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318115518901%2D2093473674%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=5b7a7d42fe7a1afc792133907d2eca05&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318115259510%2D1425214797%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">[hadoop@djt002 sqoopRunCreate]$ sqoop export \
</span>&gt; --connect 'jdbc:mysql://192.168.80.200/hive?useUnicode=true&amp;characterEncoding=utf-8'<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>&gt; --<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">username hive \
</span>&gt; --password-file /user/hadoop/<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">.password \
</span>&gt; --<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">table djt_user_copy \
</span>&gt; --export-dir /sqoop/test/<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">djt_user \
</span>&gt; --input-fields-terminated-by "@"<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>&gt; -m 1<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>&gt; --update-<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">key id \
</span>&gt; --update-mode allowinsert</pre>
</div>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
 　　这里，<span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">HDFS里，是在/sqoop/test/djt_user</span>，<span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important">通过Sqoop工具，导出到</span><span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">djt_user_copy是在MySQL里</span>。</span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=75b8fe0607ccd762bd1db6b4f632b2fe&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318115605604%2D663141806%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important">Sqoop Export 应用场景——事务处理</span></span></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
　　事务处理</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
 　　比如，从HDFS里导出到MySQL。这个时候可能会出现意外情况，如出现中断，则会出现“脏数据”重复情况。</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
则提供了这个事务处理。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
　　　　　　即 <span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">HDFS  -&gt;   先导出到  中间表（成功才可以，后续导出） -&gt; MySQL</span></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
我这里是，     <span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">/sqoop/test/djt_user</span></span><span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">（在HDFS里）
    -&gt;     djt_user_copy_tmp （在MySQL里）  -&gt;    djt_user_copy （在MySQL里） </span></span></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
　　 这里，<span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">HDFS里，是在/sqoop/test/djt_user，<span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important">通过Sqoop工具，导出到</span><span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important">djt_user_copy是在MySQL里。</span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
　　注意这个中间表，需要创建djt_user_copy_tmp</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=29a21579555eb93cae8b2c5bc2eeb25b&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318120340916%2D1205403045%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=50e3b59b63864f784afc49fed9cc829a&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318120512338%2D1059304186%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=f1c5b66711c98df8eb31c9e1dfca9151&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318120201432%2D1495293006%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=141b3d3d43e99021540bf2ebc44c76d1&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318120209604%2D1930035827%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=9af0e05f9471f49fecd2cb774e977066&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318121016588%2D1334524115%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">[hadoop@djt002 sqoopRunCreate]$ sqoop export \
</span>&gt; --connect 'jdbc:mysql://192.168.80.200/hive?useUnicode=true&amp;characterEncoding=utf-8'<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>&gt; --<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">username hive \
</span>&gt; --password-file /user/hadoop/<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">.password \
</span>&gt; --<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">table djt_user_copy \
</span>&gt; --staging-<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">table djt_user_copy_tmp \
</span>&gt; --clear-staging-<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">table \
</span>&gt; --export-dir /sqoop/test/<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">djt_user \
</span>&gt; -input-fields-terminated-by "@"</pre>
</div>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
 　　这里，<span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">HDFS里，是在/sqoop/test/djt_user</span>，<span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important">通过Sqoop工具，先导出到中间表<span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important">djt_user_copy_tmp是在MySQL里</span></span>，再继续导出到</span><span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">djt_user_copy是在MySQL</span>里。</span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
　　因为，此刻HDFS里的</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=c5148e2781c2412699533c269d86eecb&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318121403370%2D1576669605%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=b10b376e115d5ef405837e83eebbf604&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318121416338%2D1623080436%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=922e9f839b85c97d226719eaf01242cd&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318121428729%2D924524380%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">再次做测试</span></span>，假设，我现在，把MySQL里的djt_user数据导入到HDFS里的/sqoop/test/djt_user。</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=b482c212d9f1a2a43ad2634eacc10a3f&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318121514354%2D701981786%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=75e617f0a2a1f95845036df0ca382dcd&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318122546635%2D74477842%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important">[hadoop@djt002 sqoopRunCreate]$ sqoop <span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">import</span> --connect 'jdbc:mysql://192.168.80.200/hive?useUnicode=true&amp;characterEncoding=utf-8' --username hive --password-file /user/hadoop/.password -table djt_user  --target-dir /sqoop/test/djt_user --delete-target-dir -m 1 --fields-terminated-by "@" --<span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">null</span>-non-string "###" --<span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">null</span>-string "###"</pre>
</div>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=4e0bb0f6144a7471742f23d9c8006bff&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318122711448%2D919098539%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important">[hadoop@djt002 ~]$ $HADOOP_HOME/bin/hadoop fs -cat /sqoop/test/djt_user/part-m-*
1@王菲@female@36<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">@歌手
</span>2@谢霆锋@male@30<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">@歌手
</span>3@周杰伦@male@33<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">@导演
</span>4@王力宏@male@40<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">@演员
</span>5@张三@male@39<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">@无业游民
</span>6@李四@female@18<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">@学生
</span>7@王五@male@34<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">@Java开发工程师
</span>8@王六@male@45<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">@hadoop工程师
</span>9@小王@female@24<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">@hadoop运维
</span>10@小林@female@30<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">@###
[hadoop@djt002 </span>~]$ </pre>
</div>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
 　　然后，接着，我们把HDFS里的/sqoop/test/djt_user    导出到  MySQL里的 djt_user_copydjt_user_copy。</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">说白了，就是再次做了一下 Sqoop
 Export 应用场景——事务处理。（自己好好理清思路去）</span></span></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
　即 <span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">HDFS  -&gt;   先导出到  中间表（成功才可以，后续导出） -&gt; MySQL</span></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
我这里是，      <span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important">/sqoop/test/djt_user</span>（在HDFS里）
    -&gt;     djt_user_copy_tmp （在MySQL里）  -&gt;    djt_user_copydjt_user_copy （在MySQL里） </span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=24121ee898b037cd59668654f75a4c60&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318123353307%2D225800598%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=4d6a47e1646f1f3d6de60ca2fc4b1687&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318123041698%2D1731142354%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">[hadoop@djt002 sqoopRunCreate]$ sqoop export \
</span>&gt; --connect 'jdbc:mysql://192.168.80.200/hive?useUnicode=true&amp;characterEncoding=utf-8'<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>&gt; --<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">username hive \
</span>&gt; --password-file /user/hadoop/<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">.password \
</span>&gt; --<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">table djt_user_copy \
</span>&gt; --staging-<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">table djt_user_copy_tmp \
</span>&gt; --clear-staging-<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">table \
</span>&gt; --export-dir /sqoop/test/<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">djt_user \
</span>&gt; -input-fields-terminated-by "@"</pre>
</div>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">这里，<span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">HDFS里，是在/sqoop/test/djt_user</span>，<span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important">通过Sqoop工具，先导出到中间表<span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important">djt_user_copy_tmp是在MySQL里</span></span>，再继续导出到</span><span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">djt_user_copy是在MySQL里</span>。</span></span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
 　　因为，HDFS里的</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=68741320291cc539395fd2d1caab3524&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318123202432%2D1674707464%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=b10b376e115d5ef405837e83eebbf604&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318121416338%2D1623080436%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
 　　得到，</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=a666f1a9b751a587a43a0f8640477e10&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318123702823%2D1711413558%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">Sqoop Export  HDFS 应用场景——字段不对应问题</span></span></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
　　字段不对应问题</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
 　　因为，在Sqoop import时，我们有选择性的导入某个字段或某些字段对吧，那么，同样，对于Sqoop export也是一样！</p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important">[hadoop@djt002 sqoopRunCreate]$ sqoop <span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">import</span> --connect 'jdbc:mysql://192.168.80.200/hive?useUnicode=true&amp;characterEncoding=utf-8' --username hive --password-file /user/hadoop/.password -table djt_user --columns name,sex,age,profile --target-dir /sqoop/test/djt_user --delete-target-dir -m 1 --fields-terminated-by "@" --<span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">null</span>-non-string "###" --<span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">null</span>-string "###"</pre>
</div>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<pre style="margin-right:4px; margin-left:4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important">　　比如，HDFS里（的/sqoop/test/djt_user/）有4列，  数据库里（的djt_user_copy）有5列（因为会多出自增键）。<span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">那么，如何来处理这个棘手问题呢?</span></span></pre>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=50dc5f31513fe48f69f78e3285674e8a&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318124410932%2D1393408953%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=6c42d10bcdfaa7261195f9c7d564588e&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318124451791%2D1770241347%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">这样来处理， </span></span></span></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">　　照样sqoop
 export里也有 -columns name,sex,age,profile \</span></span></span></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important"><img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=0d905753c350591967f29c69c81cd3c7&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318124951604%2D195714814%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></span></span></span></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important"><img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=9e469676165f16861acd42c07a740519&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318124837573%2D628639204%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></span></span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<img src="https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&amp;quality=100&amp;size=w4096&amp;sec=1512694762&amp;di=e551485af44e6fb308f7647469595edb&amp;ref=https%3A%2F%2Fwww%2Ecnblogs%2Ecom%2Fzlslch%2Fp%2F6116476%2Ehtml&amp;src=http%3A%2F%2Fimages2015%2Ecnblogs%2Ecom%2Fblog%2F855959%2F201703%2F855959%2D20170318125218416%2D1081440827%2Epng" alt="" style="margin:0px 4px; padding:0px; height:auto; float:none; text-overflow:clip; max-width:100%; clear:none; display:inline; line-height:30px!important"></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
　　 我的这里，自增键呢?/</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important">通过Sqoop
 Export <span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important">HDFS里的数据到MySQL</span></span>（作为扩展）</span></span></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
    下面我们看一下 Sqoop 如何使用命令行来导出数据的，其命令行语法如下所示。</p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">sqoop export \
</span>--connect jdbc:mysql:<span style="margin:0px 4px; padding:0px; color:rgb(0,128,0); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">//</span><span style="margin:0px 4px; padding:0px; color:rgb(0,128,0); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">192.168.80.128:3306/db_hadoop \</span>
--<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">username sqoop \
</span>--<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">password sqoop \
</span>--<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">table user \
</span>--export-dir user</pre>
</div>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">--connect</span>：指定 JDBC URL。<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">--username/password</span>：mysql 数据库的用户名和密码。<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">--table</span>：要导入的数据库表。<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">--export-dir</span>：数据在 HDFS 上的存放目录。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">下面我们介绍几种 Sqoop 数据导出的特殊应用（作为扩展）</span></span></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">1</span>、Sqoop export 将数据导入数据库，一般情况下是一条一条导入的，这样导入的效率非常低。这时我们可以使用 Sqoop export 的批量导入提高效率，其具体语法如下。</p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; float:none; white-space:pre; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">sqoop export \
--Dsqoop.export.records.per.statement=10 \
--connect jdbc:mysql://192.168.80.128:3306/db_hadoop \
--username sqoop \
--password sqoop \
--table user \
--export-dir user \
--batch</span></span></pre>
</div>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); text-overflow:clip; clear:none; line-height:30px!important">--Dsqoop.export.records.per.statement</span>：指定每次导入10条数据，--batch：指定是批量导入。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">2</span>、在实际应用中还存在这样一个问题，比如导入数据的时候，Map Task 执行失败， <br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
那么该 Map 任务会转移到另外一个节点执行重新运行，这时候之前导入的数据又要重新导入一份，造成数据重复导入。<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
因为 Map Task 没有回滚策略，一旦运行失败，已经导入数据库中的数据就无法恢复。<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Sqoop export 提供了一种机制能保证原子性， 使用--staging-table 选项指定临时导入的表。</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
Sqoop export 导出数据的时候会分为两步：<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
　　第一步，将数据导入数据库中的临时表，如果导入期间 Map Task 失败，会删除临时表数据重新导入；<br style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">
　　第二步，确认所有 Map Task 任务成功后，会将临时表名称为指定的表名称。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">sqoop export \
</span>--connect jdbc:mysql:<span style="margin:0px 4px; padding:0px; color:rgb(0,128,0); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">//</span><span style="margin:0px 4px; padding:0px; color:rgb(0,128,0); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">192.168.80.128:3306/db_hadoop \</span>
--<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">username sqoop \
</span>--<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">password sqoop \
</span>--<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">table user \
</span>--staging-table staging_user</pre>
</div>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">3</span>、在 Sqoop 导出数据过程中，如果我们想更新已有数据，可以采取以下两种方式。</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
        1)通过 --update-key id 更新已有数据。</p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">sqoop export \
</span>--connect jdbc:mysql:<span style="margin:0px 4px; padding:0px; color:rgb(0,128,0); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">//</span><span style="margin:0px 4px; padding:0px; color:rgb(0,128,0); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">192.168.80.128:3306/db_hadoop \</span>
--<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">username sqoop \
</span>--<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">password sqoop \
</span>--<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">table user \
</span>--update-key id</pre>
</div>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
   2)  使用 --update-key id和--update-mode allowinsert 两个选项的情况下，如果数据已经存在，则更新数据，如果数据不存在，则插入新数据记录。</p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">sqoop export \
</span>--connect jdbc:mysql:<span style="margin:0px 4px; padding:0px; color:rgb(0,128,0); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">//</span><span style="margin:0px 4px; padding:0px; color:rgb(0,128,0); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">192.168.80.128.:3306/db_hadoop \</span>
--<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">username sqoop \
</span>--<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">password sqoop \
</span>--<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">table user \
</span>--update-<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">key id \
</span>--update-mode allowinsert</pre>
</div>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">4、</span>如果 HDFS 中的数据量比较大，很多字段并不需要，我们可以使用 --columns 来指定插入某几列数据。</p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">sqoop export \
</span>--connect jdbc:mysql:<span style="margin:0px 4px; padding:0px; color:rgb(0,128,0); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">//</span><span style="margin:0px 4px; padding:0px; color:rgb(0,128,0); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">192.168.80.128:3306/db_hadoop \</span>
--<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">username sqoop \
</span>--<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">password sqoop \
</span>--<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">table user \
</span>--column username,sex</pre>
</div>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">5、</span>当导入的字段数据不存在或者为null的时候，我们使用--input-null-string和--input-null-non-string 来处理。</p>
<div style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<pre style="margin:0px 4px; padding:0px; float:none; white-space:pre-wrap; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">sqoop export \
</span>--connect jdbc:mysql:<span style="margin:0px 4px; padding:0px; color:rgb(0,128,0); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">//</span><span style="margin:0px 4px; padding:0px; color:rgb(0,128,0); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">129.168.80.128:3306/db_hadoop \</span>
--<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">username sqoop \
</span>--<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">password sqoop \
</span>--<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">table user \
</span>--input-<span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">null</span>-string '\\N'<span style="margin:0px 4px; padding:0px; white-space:pre; text-overflow:clip; clear:none; line-height:30px!important"> \
</span>--input-<span style="margin:0px 4px; padding:0px; color:rgb(0,0,255); white-space:pre; text-overflow:clip; clear:none; line-height:30px!important">null</span>-non-string '\\N'</pre>
</div>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-size:18px!important; line-height:30px!important">
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; text-overflow:clip; clear:none; line-height:30px!important"><span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">推荐博客</span></span></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<span style="margin:0px 4px; padding:0px; float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important"><a target="_blank" href="http://sqoop.apache.org/docs/1.4.6/SqoopUserGuide.html" rel="nofollow" style="margin:0px 4px; padding:0px; text-decoration:none; color:rgb(0,0,238); float:none; text-overflow:clip; clear:none; display:inline; line-height:30px!important">http://sqoop.apache.org/docs/1.4.6/SqoopUserGuide.html</a> （sqoop官网文档）</span></p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<br>
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
<br>
</p>
<p style="margin-right:4px; margin-left:4px; padding-top:0px; padding-bottom:0px; float:none; text-overflow:clip; clear:none; line-height:30px!important">
</p>
<h1 style='margin:1.71429rem 0px; font-size:1.5rem; font-family:Arial,"Microsoft YaHei"; font-weight:500; line-height:1.5; color:rgb(68,68,68); padding:0px; border:0px; vertical-align:baseline; clear:both'>
1.     概述</h1>
<p></p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
本文档主要对SQOOP的使用进行了说明，参考内容主要来自于Cloudera SQOOP的官方文档。为了用中文更清楚明白地描述各参数的使用含义，本文档几乎所有参数使用说明都经过了我的实际验证而得到。</p>
<h1 style='margin:1.71429rem 0px; font-size:1.5rem; font-family:Arial,"Microsoft YaHei"; font-weight:500; line-height:1.5; color:rgb(68,68,68); padding:0px; border:0px; vertical-align:baseline; clear:both'>
2.     codegen</h1>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
将关系数据库表映射为一个java文件、java class类、以及相关的jar包，作用主要是两方面：</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
1、  将数据库表映射为一个Java文件，在该Java文件中对应有表的各个字段。</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
2、  生成的Jar和class文件在metastore功能使用时会用到。</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
基础语句：</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
sqoop codegen –connect jdbc:mysql://localhost:3306/hive –username root –password 123456 –table TBLS2</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
 </p>
<table border="1" cellspacing="0" cellpadding="0" style='border-collapse:collapse; border-spacing:0px; max-width:100%; word-break:break-all; border-width:0px 0px 1px; border-style:solid; border-color:rgb(238,238,238) rgb(238,238,238) rgb(237,237,237); font-family:"Open Sans",Helvetica,Arial,sans-serif; margin:0px 0px 1.71429rem; padding:0px; font-size:0.857143rem; vertical-align:baseline; color:rgb(117,117,117); line-height:2; width:624px'>
<tbody style="margin:0px; padding:0px; border:0px; font-size:12px; vertical-align:baseline">
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
参数</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
说明</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–bindir &lt;dir&gt;</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
指定生成的java文件、编译成的class文件及将生成文件打包为JAR的JAR包文件输出路径</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–class-name &lt;name&gt;</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
设定生成的Java文件指定的名称</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–outdir &lt;dir&gt;</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
生成的java文件存放路径</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–package-name&lt;name&gt;</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
包名，如cn.cnnic，则会生成cn和cnnic两级目录，生成的文件（如java文件）就存放在cnnic目录里</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–input-null-non-string&lt;null-str&gt;</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
在生成的java文件中，可以将null字符串设为想要设定的值（比如空字符串’’）</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–input-null-string&lt;null-str&gt;</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
同上，设定时，最好与上面的属性一起设置，且设置同样的值（比如空字符串等等）。</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–map-column-java&lt;arg&gt;</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
数据库字段在生成的java文件中会映射为各种属性，且默认的数据类型与数据库类型保持对应，比如数据库中某字段的类型为bigint，则在Java文件中的数据类型为long型，通过这个属性，可以改变数据库字段在java中映射的数据类型，格式如：–map-column-java DB_ID=String,id=Integer</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–null-non-string&lt;null-str&gt;</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
在生成的java文件中，比如TBL_ID==null?”null”:””，通过这个属性设置可以将null字符串设置为其它值如ddd，TBL_ID==null?”ddd”:””</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–null-string&lt;null-str&gt;</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
同上，使用的时候最好和上面的属性一起用，且设置为相同的值</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–table &lt;table-name&gt;</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
对应关系数据库的表名，生成的java文件中的各属性与该表的各字段一一对应。</td>
</tr>
</tbody>
</table>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
 </p>
<h1 style='margin:1.71429rem 0px; font-size:1.5rem; font-family:Arial,"Microsoft YaHei"; font-weight:500; line-height:1.5; color:rgb(68,68,68); padding:0px; border:0px; vertical-align:baseline; clear:both'>
3.     create-hive-table</h1>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
生成与关系数据库表的表结构对应的HIVE表</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
基础语句：</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
sqoop create-hive-table –connect jdbc:mysql://localhost:3306/hive -username root -password 123456 –table TBLS –hive-table h_tbls2</p>
<table border="1" cellspacing="0" cellpadding="0" style='border-collapse:collapse; border-spacing:0px; max-width:100%; word-break:break-all; border-width:0px 0px 1px; border-style:solid; border-color:rgb(238,238,238) rgb(238,238,238) rgb(237,237,237); font-family:"Open Sans",Helvetica,Arial,sans-serif; margin:0px 0px 1.71429rem; padding:0px; font-size:0.857143rem; vertical-align:baseline; color:rgb(117,117,117); line-height:2; width:624px'>
<tbody style="margin:0px; padding:0px; border:0px; font-size:12px; vertical-align:baseline">
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
参数</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
说明</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–hive-home &lt;dir&gt;</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
Hive的安装目录，可以通过该参数覆盖掉默认的hive目录</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–hive-overwrite</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
覆盖掉在hive表中已经存在的数据</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–create-hive-table</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
默认是false,如果目标表已经存在了，那么创建任务会失败</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–hive-table</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
后面接要创建的hive表</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–table</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
指定关系数据库表名</td>
</tr>
</tbody>
</table>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
 </p>
<h1 style='margin:1.71429rem 0px; font-size:1.5rem; font-family:Arial,"Microsoft YaHei"; font-weight:500; line-height:1.5; color:rgb(68,68,68); padding:0px; border:0px; vertical-align:baseline; clear:both'>
4.     eval</h1>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
可以快速地使用SQL语句对关系数据库进行操作，这可以使得在使用import这种工具进行数据导入的时候，可以预先了解相关的SQL语句是否正确，并能将结果显示在控制台。</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
<span style="margin:0px; padding:0px; border:0px; vertical-align:baseline">查询示例</span>：</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
sqoop eval –connect jdbc:mysql://localhost:3306/hive -username root -password 123456 -query “SELECT * FROM tbls LIMIT 10″</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
<span style="margin:0px; padding:0px; border:0px; vertical-align:baseline">数据插入示例</span>：</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
sqoop eval –connect jdbc:mysql://localhost:3306/hive -username root -password 123456 -e “INSERT INTO TBLS2</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
VALUES(100,1375170308,1,0,’hadoop’,0,1,’guest’,’MANAGED_TABLE’,’abc’,’ddd’)”</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
-e、-query这两个参数经过测试，比如后面分别接查询和插入SQL语句，皆可运行无误，如上。</p>
<h1 style='margin:1.71429rem 0px; font-size:1.5rem; font-family:Arial,"Microsoft YaHei"; font-weight:500; line-height:1.5; color:rgb(68,68,68); padding:0px; border:0px; vertical-align:baseline; clear:both'>
5.     export</h1>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
从hdfs中导数据到关系数据库中</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
sqoop export –connect jdbc:mysql://localhost:3306/hive –username root –password</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
123456  –table TBLS2 –export-dir sqoop/test</p>
<table border="1" cellspacing="0" cellpadding="0" style='border-collapse:collapse; border-spacing:0px; max-width:100%; word-break:break-all; border-width:0px 0px 1px; border-style:solid; border-color:rgb(238,238,238) rgb(238,238,238) rgb(237,237,237); font-family:"Open Sans",Helvetica,Arial,sans-serif; margin:0px 0px 1.71429rem; padding:0px; font-size:0.857143rem; vertical-align:baseline; color:rgb(117,117,117); line-height:2; width:624px'>
<tbody style="margin:0px; padding:0px; border:0px; font-size:12px; vertical-align:baseline">
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="215" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
参数</td>
<td valign="top" width="353" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
说明</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="215" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–direct</td>
<td valign="top" width="353" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
快速模式，利用了数据库的导入工具，如mysql的mysqlimport，可以比jdbc连接的方式更为高效的将数据导入到关系数据库中。</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="215" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–export-dir &lt;dir&gt;</td>
<td valign="top" width="353" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
存放数据的HDFS的源目录</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="215" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
-m,–num-mappers &lt;n&gt;</td>
<td valign="top" width="353" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
启动N个map来并行导入数据，默认是4个，最好不要将数字设置为高于集群的最大Map数</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="215" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–table &lt;table-name&gt;</td>
<td valign="top" width="353" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
要导入到的关系数据库表</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="215" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–update-key &lt;col-name&gt;</td>
<td valign="top" width="353" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
后面接条件列名，通过该参数，可以将关系数据库中已经存在的数据进行更新操作，类似于关系数据库中的update操作</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="215" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–update-mode &lt;mode&gt;</td>
<td valign="top" width="353" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
更新模式，有两个值updateonly和默认的allowinsert，该参数只能是在关系数据表里不存在要导入的记录时才能使用，比如要导入的hdfs中有一条id=1的记录，如果在表里已经有一条记录id=2，那么更新会失败。</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="215" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–input-null-string &lt;null-string&gt;</td>
<td valign="top" width="353" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
可选参数，如果没有指定，则字符串null将被使用</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="215" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–input-null-non-string &lt;null-string&gt;</td>
<td valign="top" width="353" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
可选参数，如果没有指定，则字符串null将被使用</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="215" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–staging-table &lt;staging-table-name&gt;</td>
<td valign="top" width="353" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
该参数是用来保证在数据导入关系数据库表的过程中事务安全性的，因为在导入的过程中可能会有多个事务，那么一个事务失败会影响到其它事务，比如导入的数据会出现错误或出现重复的记录等等情况，那么通过该参数可以避免这种情况。创建一个与导入目标表同样的数据结构，保留该表为空在运行数据导入前，所有事务会将结果先存放在该表中，然后最后由该表通过一次事务将结果写入到目标表中。</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="215" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–clear-staging-table</td>
<td valign="top" width="353" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
如果该staging-table非空，则通过该参数可以在运行导入前清除staging-table里的数据。</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="215" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–batch</td>
<td valign="top" width="353" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
该模式用于执行基本语句（暂时还不太清楚含义）</td>
</tr>
</tbody>
</table>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
 </p>
<h1 style='margin:1.71429rem 0px; font-size:1.5rem; font-family:Arial,"Microsoft YaHei"; font-weight:500; line-height:1.5; color:rgb(68,68,68); padding:0px; border:0px; vertical-align:baseline; clear:both'>
6.     import</h1>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
将数据库表的数据导入到hive中，如果在hive中没有对应的表，则自动生成与数据库表名相同的表。</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
sqoop import –connect jdbc:mysql://localhost:3306/hive –username root –password</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
123456 –table user –split-by id –hive-import</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
–split-by指定数据库表中的主键字段名，在这里为id。</p>
<table border="1" cellspacing="0" cellpadding="0" style='border-collapse:collapse; border-spacing:0px; max-width:100%; word-break:break-all; border-width:0px 0px 1px; border-style:solid; border-color:rgb(238,238,238) rgb(238,238,238) rgb(237,237,237); font-family:"Open Sans",Helvetica,Arial,sans-serif; margin:0px 0px 1.71429rem; padding:0px; font-size:0.857143rem; vertical-align:baseline; color:rgb(117,117,117); line-height:2; width:624px'>
<tbody style="margin:0px; padding:0px; border:0px; font-size:12px; vertical-align:baseline">
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
参数</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
说明</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–append</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
将数据追加到hdfs中已经存在的dataset中。使用该参数，sqoop将把数据先导入到一个临时目录中，然后重新给文件命名到一个正式的目录中，以避免和该目录中已存在的文件重名。</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–as-avrodatafile</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
将数据导入到一个Avro数据文件中</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–as-sequencefile</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
将数据导入到一个sequence文件中</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–as-textfile</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
将数据导入到一个普通文本文件中，生成该文本文件后，可以在hive中通过sql语句查询出结果。</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–boundary-query &lt;statement&gt;</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
边界查询，也就是在导入前先通过SQL查询得到一个结果集，然后导入的数据就是该结果集内的数据，格式如：–boundary-query ‘select id,creationdate from person where id = 3’，表示导入的数据为id=3的记录，或者select min(&lt;split-by&gt;), max(&lt;split-by&gt;) from &lt;table name&gt;，注意查询的字段中不能有数据类型为字符串的字段，否则会报错：java.sql.SQLException: Invalid value
 for
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
getLong()</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
目前问题原因还未知</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
 </p>
</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–columns&lt;col,col,col…&gt;</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
指定要导入的字段值，格式如：–columns id,username</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–direct</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
直接导入模式，使用的是关系数据库自带的导入导出工具。官网上是说这样导入会更快</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–direct-split-size</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
在使用上面direct直接导入的基础上，对导入的流按字节数分块，特别是使用直连模式从PostgreSQL导入数据的时候，可以将一个到达设定大小的文件分为几个独立的文件。</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–inline-lob-limit</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
设定大对象数据类型的最大值</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
-m,–num-mappers</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
启动N个map来并行导入数据，默认是4个，最好不要将数字设置为高于集群的节点数</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–query，-e&lt;statement&gt;</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
从查询结果中导入数据，该参数使用时必须指定–target-dir、–hive-table，在查询语句中一定要有where条件且在where条件中需要包含$CONDITIONS，示例：–query ‘select * from person where $CONDITIONS ‘ –target-dir
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
/user/hive/warehouse/person –hive-table person</p>
</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–split-by&lt;column-name&gt;</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
表的列名，用来切分工作单元，一般后面跟主键ID</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–table &lt;table-name&gt;</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
关系数据库表名，数据从该表中获取</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–target-dir &lt;dir&gt;</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
指定hdfs路径</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–warehouse-dir &lt;dir&gt;</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
与–target-dir不能同时使用，指定数据导入的存放目录，适用于hdfs导入，不适合导入hive目录</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–where</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
从关系数据库导入数据时的查询条件，示例：–where ‘id = 2′</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
-z,–compress</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
压缩参数，默认情况下数据是没被压缩的，通过该参数可以使用gzip压缩算法对数据进行压缩，适用于SequenceFile, text文本文件, 和Avro文件</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–compression-codec</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
Hadoop压缩编码，默认是gzip</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–null-string &lt;null-string&gt;</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
可选参数，如果没有指定，则字符串null将被使用</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–null-non-string&lt;null-string&gt;</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
可选参数，如果没有指定，则字符串null将被使用</td>
</tr>
</tbody>
</table>
<h2 style='font-family:Arial,"Microsoft YaHei"; font-weight:500; line-height:1.6; color:rgb(68,68,68); margin:1.71429rem 0px; font-size:1.28571rem; padding:0px; border:0px; vertical-align:baseline; clear:both'>
增量导入</h2>
<table border="1" cellspacing="0" cellpadding="0" style='border-collapse:collapse; border-spacing:0px; max-width:100%; word-break:break-all; border-width:0px 0px 1px; border-style:solid; border-color:rgb(238,238,238) rgb(238,238,238) rgb(237,237,237); font-family:"Open Sans",Helvetica,Arial,sans-serif; margin:0px 0px 1.71429rem; padding:0px; font-size:0.857143rem; vertical-align:baseline; color:rgb(117,117,117); line-height:2; width:624px'>
<tbody style="margin:0px; padding:0px; border:0px; font-size:12px; vertical-align:baseline">
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
参数</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
说明</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–check-column (col)</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
用来作为判断的列名，如id</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–incremental (mode)</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
append：追加，比如对大于last-value指定的值之后的记录进行追加导入。lastmodified：最后的修改时间，追加last-value指定的日期之后的记录</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–last-value (value)</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
指定自从上次导入后列的最大值（大于该指定的值），也可以自己设定某一值</td>
</tr>
</tbody>
</table>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
对incremental参数，如果是以日期作为追加导入的依据，则使用lastmodified，否则就使用append值。</p>
<h1 style='margin:1.71429rem 0px; font-size:1.5rem; font-family:Arial,"Microsoft YaHei"; font-weight:500; line-height:1.5; color:rgb(68,68,68); padding:0px; border:0px; vertical-align:baseline; clear:both'>
7.     import-all-tables</h1>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
将数据库里的所有表导入到HDFS中，每个表在hdfs中都对应一个独立的目录。</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
sqoop import-all-tables –connect jdbc:mysql://localhost:3306/test</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
sqoop import-all-tables –connect jdbc:mysql://localhost:3306/test –hive-import</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
<span style="margin:0px; padding:0px; border:0px; vertical-align:baseline"> </span></p>
<table border="1" cellspacing="0" cellpadding="0" style='border-collapse:collapse; border-spacing:0px; max-width:100%; word-break:break-all; border-width:0px 0px 1px; border-style:solid; border-color:rgb(238,238,238) rgb(238,238,238) rgb(237,237,237); font-family:"Open Sans",Helvetica,Arial,sans-serif; margin:0px 0px 1.71429rem; padding:0px; font-size:0.857143rem; vertical-align:baseline; color:rgb(117,117,117); line-height:2; width:624px'>
<tbody style="margin:0px; padding:0px; border:0px; font-size:12px; vertical-align:baseline">
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="215" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
参数</td>
<td valign="top" width="353" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
说明</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="215" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–as-avrodatafile</td>
<td valign="top" width="353" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
同import参数</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="215" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–as-sequencefile</td>
<td valign="top" width="353" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
同import参数</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="215" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–as-textfile</td>
<td valign="top" width="353" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
同import参数</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="215" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–direct</td>
<td valign="top" width="353" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
同import参数</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="215" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–direct-split-size &lt;n&gt;</td>
<td valign="top" width="353" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
同import参数</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="215" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–inline-lob-limit &lt;n&gt;</td>
<td valign="top" width="353" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
同import参数</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="215" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
-m,–num-mappers &lt;n&gt;</td>
<td valign="top" width="353" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
同import参数</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="215" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–warehouse-dir &lt;dir&gt;</td>
<td valign="top" width="353" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
同import参数</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="215" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
-z,–compress</td>
<td valign="top" width="353" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
同import参数</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="215" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–compression-codec</td>
<td valign="top" width="353" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
同import参数</td>
</tr>
</tbody>
</table>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
 </p>
<h1 style='margin:1.71429rem 0px; font-size:1.5rem; font-family:Arial,"Microsoft YaHei"; font-weight:500; line-height:1.5; color:rgb(68,68,68); padding:0px; border:0px; vertical-align:baseline; clear:both'>
8.     job</h1>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
用来生成一个sqoop的任务，生成后，该任务并不执行，除非使用命令执行该任务。</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
sqoop job</p>
<table border="1" cellspacing="0" cellpadding="0" style='border-collapse:collapse; border-spacing:0px; max-width:100%; word-break:break-all; border-width:0px 0px 1px; border-style:solid; border-color:rgb(238,238,238) rgb(238,238,238) rgb(237,237,237); font-family:"Open Sans",Helvetica,Arial,sans-serif; margin:0px 0px 1.71429rem; padding:0px; font-size:0.857143rem; vertical-align:baseline; color:rgb(117,117,117); line-height:2; width:624px'>
<tbody style="margin:0px; padding:0px; border:0px; font-size:12px; vertical-align:baseline">
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
参数</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
说明</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–create &lt;job-id&gt;</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
生成一个job，示例如：sqoop job –create myjob  — import –connectjdbc:mysql://localhost:3306/test –table
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
person</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
 </p>
</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–delete &lt;job-id&gt;</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
删除一个jobsqoop job –delete myjob</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–exec &lt;job-id&gt;</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
执行一个jobsqoop job –exec myjob</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–help</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
显示帮助说明</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–list</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
显示所有的jobsqoop job –list</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–meta-connect &lt;jdbc-uri&gt;</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
用来连接metastore服务，示例如：–meta-connect jdbc:hsqldb:hsql://localhost:16000/sqoop</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–show &lt;job-id&gt;</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
显示一个job的各种参数sqoop job –show myjob</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–verbose</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
打印命令运行时的详细信息</td>
</tr>
</tbody>
</table>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
 </p>
<h1 style='margin:1.71429rem 0px; font-size:1.5rem; font-family:Arial,"Microsoft YaHei"; font-weight:500; line-height:1.5; color:rgb(68,68,68); padding:0px; border:0px; vertical-align:baseline; clear:both'>
9.     list-databases</h1>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
打印出关系数据库所有的数据库名</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
sqoop list-databases –connect jdbc:mysql://localhost:3306/ -username root -password 123456</p>
<h1 style='margin:1.71429rem 0px; font-size:1.5rem; font-family:Arial,"Microsoft YaHei"; font-weight:500; line-height:1.5; color:rgb(68,68,68); padding:0px; border:0px; vertical-align:baseline; clear:both'>
10.             list-tables</h1>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
打印出关系数据库某一数据库的所有表名</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
sqoop list-tables –connect jdbc:mysql://localhost:3306/zihou -username root -password 123456</p>
<h1 style='margin:1.71429rem 0px; font-size:1.5rem; font-family:Arial,"Microsoft YaHei"; font-weight:500; line-height:1.5; color:rgb(68,68,68); padding:0px; border:0px; vertical-align:baseline; clear:both'>
11.             merge</h1>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
将HDFS中不同目录下面的数据合在一起，并存放在指定的目录中，示例如：</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
sqoop merge –new-data /test/p1/person –onto /test/p2/person –target-dir /test/merged –jar-file /opt/data/sqoop/person/Person.jar –class-name Person –merge-key id</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
其中，–class-name所指定的class名是对应于Person.jar中的Person类，而Person.jar是通过Codegen生成的</p>
<table border="1" cellspacing="0" cellpadding="0" style='border-collapse:collapse; border-spacing:0px; max-width:100%; word-break:break-all; border-width:0px 0px 1px; border-style:solid; border-color:rgb(238,238,238) rgb(238,238,238) rgb(237,237,237); font-family:"Open Sans",Helvetica,Arial,sans-serif; margin:0px 0px 1.71429rem; padding:0px; font-size:0.857143rem; vertical-align:baseline; color:rgb(117,117,117); line-height:2; width:624px'>
<tbody style="margin:0px; padding:0px; border:0px; font-size:12px; vertical-align:baseline">
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
参数</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
说明</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–new-data &lt;path&gt;</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
Hdfs中存放数据的一个目录，该目录中的数据是希望在合并后能优先保留的，原则上一般是存放越新数据的目录就对应这个参数。</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–onto &lt;path&gt;</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
Hdfs中存放数据的一个目录，该目录中的数据是希望在合并后能被更新数据替换掉的，原则上一般是存放越旧数据的目录就对应这个参数。</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–merge-key &lt;col&gt;</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
合并键，一般是主键ID</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–jar-file &lt;file&gt;</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
合并时引入的jar包，该jar包是通过Codegen工具生成的jar包</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–class-name &lt;class&gt;</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
对应的表名或对象名，该class类是包含在jar包中的。</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–target-dir &lt;path&gt;</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
合并后的数据在HDFS里的存放目录</td>
</tr>
</tbody>
</table>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
 </p>
<h1 style='margin:1.71429rem 0px; font-size:1.5rem; font-family:Arial,"Microsoft YaHei"; font-weight:500; line-height:1.5; color:rgb(68,68,68); padding:0px; border:0px; vertical-align:baseline; clear:both'>
12.             metastore</h1>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
记录sqoop job的元数据信息，如果不启动metastore实例，则默认的元数据存储目录为：~/.sqoop，如果要更改存储目录，可以在配置文件sqoop-site.xml中进行更改。</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
metastore实例启动：sqoop metastore</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
 </p>
<table border="1" cellspacing="0" cellpadding="0" style='border-collapse:collapse; border-spacing:0px; max-width:100%; word-break:break-all; border-width:0px 0px 1px; border-style:solid; border-color:rgb(238,238,238) rgb(238,238,238) rgb(237,237,237); font-family:"Open Sans",Helvetica,Arial,sans-serif; margin:0px 0px 1.71429rem; padding:0px; font-size:0.857143rem; vertical-align:baseline; color:rgb(117,117,117); line-height:2; width:624px'>
<tbody style="margin:0px; padding:0px; border:0px; font-size:12px; vertical-align:baseline">
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
参数</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
说明</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–shutdown</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
关闭一个运行的metastore实例</td>
</tr>
</tbody>
</table>
<h1 style='margin:1.71429rem 0px; font-size:1.5rem; font-family:Arial,"Microsoft YaHei"; font-weight:500; line-height:1.5; color:rgb(68,68,68); padding:0px; border:0px; vertical-align:baseline; clear:both'>
13.             version</h1>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
显示sqoop版本信息</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
语句：sqoop version</p>
<h1 style='margin:1.71429rem 0px; font-size:1.5rem; font-family:Arial,"Microsoft YaHei"; font-weight:500; line-height:1.5; color:rgb(68,68,68); padding:0px; border:0px; vertical-align:baseline; clear:both'>
14.             help</h1>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
打印sqoop帮助信息</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
语句：sqoop help</p>
<h1 style='margin:1.71429rem 0px; font-size:1.5rem; font-family:Arial,"Microsoft YaHei"; font-weight:500; line-height:1.5; color:rgb(68,68,68); padding:0px; border:0px; vertical-align:baseline; clear:both'>
15.             公共参数</h1>
<h2 style='font-family:Arial,"Microsoft YaHei"; font-weight:500; line-height:1.6; color:rgb(68,68,68); margin:1.71429rem 0px; font-size:1.28571rem; padding:0px; border:0px; vertical-align:baseline; clear:both'>
Hive参数</h2>
<table border="1" cellspacing="0" cellpadding="0" style='border-collapse:collapse; border-spacing:0px; max-width:100%; word-break:break-all; border-width:0px 0px 1px; border-style:solid; border-color:rgb(238,238,238) rgb(238,238,238) rgb(237,237,237); font-family:"Open Sans",Helvetica,Arial,sans-serif; margin:0px 0px 1.71429rem; padding:0px; font-size:0.857143rem; vertical-align:baseline; color:rgb(117,117,117); line-height:2; width:624px'>
<tbody style="margin:0px; padding:0px; border:0px; font-size:12px; vertical-align:baseline">
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
参数</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
说明</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–hive-delims-replacement &lt;arg&gt;</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
用自定义的字符串替换掉数据中的\n, \r, and \01等字符</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–hive-drop-import-delims</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
在导入数据到hive中时，去掉数据中\n,\r和\01这样的字符</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–map-column-hive &lt;arg&gt;</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
生成hive表时，可以更改生成字段的数据类型，格式如：–map-column-hiveTBL_ID=String,LAST_ACCESS_TIME=string</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–hive-partition-key</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
创建分区，后面直接跟分区名即可，创建完毕后，通过describe 表名可以看到分区名，默认为string型</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–hive-partition-value&lt;v&gt;</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
该值是在导入数据到hive中时，与–hive-partition-key设定的key对应的value值。</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–hive-home &lt;dir&gt;</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
Hive的安装目录，可以通过该参数覆盖掉默认的hive目录</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–hive-import</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
将数据从关系数据库中导入到hive表中</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–hive-overwrite</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
覆盖掉在hive表中已经存在的数据</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–create-hive-table</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
默认是false,如果目标表已经存在了，那么创建任务会失败</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–hive-table</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
后面接要创建的hive表</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–table</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
指定关系数据库表名</td>
</tr>
</tbody>
</table>
<h2 style='font-family:Arial,"Microsoft YaHei"; font-weight:500; line-height:1.6; color:rgb(68,68,68); margin:1.71429rem 0px; font-size:1.28571rem; padding:0px; border:0px; vertical-align:baseline; clear:both'>
数据库连接参数</h2>
<table border="1" cellspacing="0" cellpadding="0" style='border-collapse:collapse; border-spacing:0px; max-width:100%; word-break:break-all; border-width:0px 0px 1px; border-style:solid; border-color:rgb(238,238,238) rgb(238,238,238) rgb(237,237,237); font-family:"Open Sans",Helvetica,Arial,sans-serif; margin:0px 0px 1.71429rem; padding:0px; font-size:0.857143rem; vertical-align:baseline; color:rgb(117,117,117); line-height:2; width:624px'>
<tbody style="margin:0px; padding:0px; border:0px; font-size:12px; vertical-align:baseline">
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="234" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
参数</td>
<td valign="top" width="334" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
说明</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="234" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–connect &lt;jdbc-uri&gt;</td>
<td valign="top" width="334" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
Jdcb连接url，示例如：–connect jdbc:mysql://localhost:3306/hive</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="234" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–connection-manager &lt;class-name&gt;</td>
<td valign="top" width="334" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
指定要使用的连接管理类</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="234" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–driver &lt;class-name&gt;</td>
<td valign="top" width="334" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
数据库驱动类</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="234" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–hadoop-home &lt;dir&gt;</td>
<td valign="top" width="334" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
Hadoop根目录</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="234" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–help</td>
<td valign="top" width="334" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
打印帮助信息</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="234" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
-P</td>
<td valign="top" width="334" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
从控制端读取密码</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="234" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–password &lt;password&gt;</td>
<td valign="top" width="334" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
Jdbc url中的数据库连接密码</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="234" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–username &lt;username&gt;</td>
<td valign="top" width="334" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
Jdbc url中的数据库连接用户名</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="234" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–verbose</td>
<td valign="top" width="334" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
在控制台打印出详细信息</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="234" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–connection-param-file &lt;filename&gt;</td>
<td valign="top" width="334" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
一个记录着数据库连接参数的文件</td>
</tr>
</tbody>
</table>
<h2 style='font-family:Arial,"Microsoft YaHei"; font-weight:500; line-height:1.6; color:rgb(68,68,68); margin:1.71429rem 0px; font-size:1.28571rem; padding:0px; border:0px; vertical-align:baseline; clear:both'>
文件输出参数</h2>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
用于import场景。</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
示例如：</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
sqoop import –connect jdbc:mysql://localhost:3306/test –username root –P –table person –split-by id –check-column id –incremental append  –last-value 1 –enclosed-by ‘\”‘</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
–escaped-by \# –fields-terminated-by .</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
 </p>
<table border="1" cellspacing="0" cellpadding="0" style='border-collapse:collapse; border-spacing:0px; max-width:100%; word-break:break-all; border-width:0px 0px 1px; border-style:solid; border-color:rgb(238,238,238) rgb(238,238,238) rgb(237,237,237); font-family:"Open Sans",Helvetica,Arial,sans-serif; margin:0px 0px 1.71429rem; padding:0px; font-size:0.857143rem; vertical-align:baseline; color:rgb(117,117,117); line-height:2; width:624px'>
<tbody style="margin:0px; padding:0px; border:0px; font-size:12px; vertical-align:baseline">
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
参数</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
说明</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–enclosed-by &lt;char&gt;</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
给字段值前后加上指定的字符，比如双引号，示例：–enclosed-by ‘\”‘，显示例子：”3″,”jimsss”,”dd@dd.com”</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–escaped-by &lt;char&gt;</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
给双引号作转义处理，如字段值为”测试”，经过–escaped-by \\处理后，在hdfs中的显示值为：\”测试\”，对单引号无效</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–fields-terminated-by &lt;char&gt;</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
设定每个字段是以什么符号作为结束的，默认是逗号，也可以改为其它符号，如句号.，示例如：–fields-terminated-by.</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–lines-terminated-by &lt;char&gt;</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
设定每条记录行之间的分隔符，默认是换行，但也可以设定自己所需要的字符串，示例如：–lines-terminated-by ‘#’ 以#号分隔</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–mysql-delimiters</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
Mysql默认的分隔符设置，字段之间以,隔开，行之间以换行\n隔开，默认转义符号是\，字段值以单引号’包含起来。</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="206" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–optionally-enclosed-by &lt;char&gt;</td>
<td valign="top" width="362" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
enclosed-by是强制给每个字段值前后都加上指定的符号，而–optionally-enclosed-by只是给带有双引号或单引号的字段值加上指定的符号，故叫可选的。示例如：–optionally-enclosed-by ‘$’
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
显示结果：</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
$”hehe”,测试$</p>
</td>
</tr>
</tbody>
</table>
<h2 style='font-family:Arial,"Microsoft YaHei"; font-weight:500; line-height:1.6; color:rgb(68,68,68); margin:1.71429rem 0px; font-size:1.28571rem; padding:0px; border:0px; vertical-align:baseline; clear:both'>
文件输入参数</h2>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
对数据格式的解析，用于export场景，与文件输出参数相对应。</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
示例如：</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
sqoop export –connect jdbc:mysql://localhost:3306/test –username root –password</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
123456  –table person2 –export-dir /user/hadoop/person –staging-table person3</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
–clear-staging-table –input-fields-terminated-by ‘,’</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
在hdfs中存在某一格式的数据，在将这样的数据导入到关系数据库中时，必须要按照该格式来解析出相应的字段值，比如在hdfs中有这样格式的数据：</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
3,jimsss,dd@dd.com,1,2013-08-07 16:00:48.0,”hehe”,测试</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
上面的各字段是以逗号分隔的，那么在解析时，必须要以逗号来解析出各字段值，如：</p>
<p style='margin-top:0px; margin-bottom:1.71429rem; padding-top:0px; padding-bottom:0px; font-family:Arial,"Microsoft YaHei"; font-size:0.7rem; color:rgb(51,51,51); line-height:1.71429; word-break:break-all; border:0px; vertical-align:baseline'>
–input-fields-terminated-by ‘,’</p>
<table border="1" cellspacing="0" cellpadding="0" style='border-collapse:collapse; border-spacing:0px; max-width:100%; word-break:break-all; border-width:0px 0px 1px; border-style:solid; border-color:rgb(238,238,238) rgb(238,238,238) rgb(237,237,237); font-family:"Open Sans",Helvetica,Arial,sans-serif; margin:0px 0px 1.71429rem; padding:0px; font-size:0.857143rem; vertical-align:baseline; color:rgb(117,117,117); line-height:2; width:624px'>
<tbody style="margin:0px; padding:0px; border:0px; font-size:12px; vertical-align:baseline">
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="234" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
参数</td>
<td valign="top" width="334" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
说明</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="234" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–input-enclosed-by &lt;char&gt;</td>
<td valign="top" width="334" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
对字段值前后有指定的字符，比如双引号的值进行解析：–input-enclosed-by ‘\”‘，数据例子：”3″,”jimsss”,”dd@dd.com”</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="234" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–input-escaped-by &lt;char&gt;</td>
<td valign="top" width="334" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
对含有转义双引号的字段值作转义处理，如字段值为\”测试\”，经过–input-escaped-by \\处理后，解析得到的值为：”测试”，对单引号无效。</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="234" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–input-fields-terminated-by &lt;char&gt;</td>
<td valign="top" width="334" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
以字段间的分隔符来解析得到各字段值，示例如：– input-fields-terminated-by,</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="234" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–input-lines-terminated-by &lt;char&gt;</td>
<td valign="top" width="334" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
以每条记录行之间的分隔符来解析得到字段值，示例如：–input-lines-terminated-by ‘#’ 以#号分隔</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; vertical-align:baseline">
<td valign="top" width="234" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
–input-optionally-enclosed-by &lt;char&gt;</td>
<td valign="top" width="334" style="border-width:1px 0px 0px; border-style:solid; border-color:rgb(237,237,237) rgb(238,238,238) rgb(238,238,238); margin:0px; padding:6px 10px 6px 0px; vertical-align:baseline">
与–input-enclosed-by功能相似，与–input-enclosed-by的区别参见输出参数中对–optionally-enclosed-by的描述</td>
</tr>
</tbody>
</table>
<div style='margin:0px; padding:0px; color:rgb(51,51,51); font-family:PingFangSC,"helvetica neue","hiragino sans gb",arial,"microsoft yahei ui","microsoft yahei",simsun,sans-serif; font-size:16px'>
这些内容是从sqoop的官网整理出来的，是1.4.3版本的Document，如果有错误，希望大家指正。</div>
<div style='margin:0px; padding:0px; color:rgb(51,51,51); font-family:PingFangSC,"helvetica neue","hiragino sans gb",arial,"microsoft yahei ui","microsoft yahei",simsun,sans-serif; font-size:16px'>
<span style="font-weight:700">1.使用sqoop导入数据</span></div>
<div style='margin:0px; padding:0px; color:rgb(51,51,51); font-family:PingFangSC,"helvetica neue","hiragino sans gb",arial,"microsoft yahei ui","microsoft yahei",simsun,sans-serif; font-size:16px'>
<pre class=" language-none" style='margin-top:0.5em; margin-bottom:0.5em; padding:1em; word-wrap:normal; overflow:auto; font-size:13px; line-height:1.5; font-family:Consolas,Monaco,"Andale Mono","Ubuntu Mono",monospace; word-break:normal; color:rgb(248,248,242); border:none; direction:ltr; word-spacing:normal; background:rgb(39,40,34)'><code class=" language-none" style='margin:0px; padding:0px; font-size:undefined; font-family:Consolas,Monaco,"Andale Mono","Ubuntu Mono",monospace; background-color:transparent; direction:ltr; word-spacing:normal; word-break:normal; word-wrap:normal; line-height:1.5'>sqoop import --connect jdbc:mysql://localhost/db --username foo --table TEST</code></pre>
<span style="font-weight:700">2.账号密码</span></div>
<div style='margin:0px; padding:0px; color:rgb(51,51,51); font-family:PingFangSC,"helvetica neue","hiragino sans gb",arial,"microsoft yahei ui","microsoft yahei",simsun,sans-serif; font-size:16px'>
<pre class=" language-none" style='margin-top:0.5em; margin-bottom:0.5em; padding:1em; word-wrap:normal; overflow:auto; font-size:13px; line-height:1.5; font-family:Consolas,Monaco,"Andale Mono","Ubuntu Mono",monospace; word-break:normal; color:rgb(248,248,242); border:none; direction:ltr; word-spacing:normal; background:rgb(39,40,34)'><code class=" language-none" style='margin:0px; padding:0px; font-size:undefined; font-family:Consolas,Monaco,"Andale Mono","Ubuntu Mono",monospace; background-color:transparent; direction:ltr; word-spacing:normal; word-break:normal; word-wrap:normal; line-height:1.5'>sqoop import --connect jdbc:mysql://database.example.com/employees \
    --username aaron --password 12345</code></pre>
<span style="font-weight:700">3.驱动</span></div>
<div style='margin:0px; padding:0px; color:rgb(51,51,51); font-family:PingFangSC,"helvetica neue","hiragino sans gb",arial,"microsoft yahei ui","microsoft yahei",simsun,sans-serif; font-size:16px'>
<pre class=" language-none" style='margin-top:0.5em; margin-bottom:0.5em; padding:1em; word-wrap:normal; overflow:auto; font-size:13px; line-height:1.5; font-family:Consolas,Monaco,"Andale Mono","Ubuntu Mono",monospace; word-break:normal; color:rgb(248,248,242); border:none; direction:ltr; word-spacing:normal; background:rgb(39,40,34)'><code class=" language-none" style='margin:0px; padding:0px; font-size:undefined; font-family:Consolas,Monaco,"Andale Mono","Ubuntu Mono",monospace; background-color:transparent; direction:ltr; word-spacing:normal; word-break:normal; word-wrap:normal; line-height:1.5'>sqoop import --driver com.microsoft.jdbc.sqlserver.SQLServerDriver \
    --connect &lt;connect-string&gt; ...</code></pre>
<span style="font-weight:700">4.写sql语句导入的方式</span><br style="">
<pre class=" language-none" style='margin-top:0.5em; margin-bottom:0.5em; padding:1em; word-wrap:normal; overflow:auto; font-size:13px; line-height:1.5; font-family:Consolas,Monaco,"Andale Mono","Ubuntu Mono",monospace; word-break:normal; color:rgb(248,248,242); border:none; direction:ltr; word-spacing:normal; background:rgb(39,40,34)'><code class=" language-none" style='margin:0px; padding:0px; font-size:undefined; font-family:Consolas,Monaco,"Andale Mono","Ubuntu Mono",monospace; background-color:transparent; direction:ltr; word-spacing:normal; word-break:normal; word-wrap:normal; line-height:1.5'> sqoop import \
  --query 'SELECT a.*, b.* FROM a JOIN b on (a.id == b.id) WHERE $CONDITIONS' \
  --split-by a.id --target-dir /user/foo/joinresults</code></pre>
如果是顺序导入的话，可以只开一个线程</div>
<div style='margin:0px; padding:0px; color:rgb(51,51,51); font-family:PingFangSC,"helvetica neue","hiragino sans gb",arial,"microsoft yahei ui","microsoft yahei",simsun,sans-serif; font-size:16px'>
<pre class=" language-none" style='margin-top:0.5em; margin-bottom:0.5em; padding:1em; word-wrap:normal; overflow:auto; font-size:13px; line-height:1.5; font-family:Consolas,Monaco,"Andale Mono","Ubuntu Mono",monospace; word-break:normal; color:rgb(248,248,242); border:none; direction:ltr; word-spacing:normal; background:rgb(39,40,34)'><code class=" language-none" style='margin:0px; padding:0px; font-size:undefined; font-family:Consolas,Monaco,"Andale Mono","Ubuntu Mono",monospace; background-color:transparent; direction:ltr; word-spacing:normal; word-break:normal; word-wrap:normal; line-height:1.5'>sqoop import \
  --query 'SELECT a.*, b.* FROM a JOIN b on (a.id == b.id) WHERE $CONDITIONS' \
  -m 1 --target-dir /user/foo/joinresults</code></pre>
<div style="margin:0px; padding:0px">如果where语句中有要用单引号的，就像这样子写就可以啦"SELECT * FROM x WHERE a='foo' AND \$CONDITIONS"</div>
<div style="margin:0px; padding:0px"><span style="font-weight:700">5.  1.4.3版本的sqoop不支持复杂的sql语句，不支持or语句</span></div>
<div style="margin:0px; padding:0px"><span style="font-weight:700">6. --split-by &lt;column-name&gt;</span><br style="">
</div>
<div style="margin:0px; padding:0px">默认是主键，假设有100行数据，它会执行那个SELECT * FROM sometable WHERE id &gt;= lo AND id &lt; hi, with (lo, hi)  会分为4次导入（0,250），（250,500），(500,750),(750,1001)</div>
<div style="margin:0px; padding:0px">如果这个字段不能达到实际的划分区域的效果，可以用别的字段。如果没有索引列或者是组合主键的表，需要手动设置一个划分列。</div>
<div style="margin:0px; padding:0px"><span style="font-weight:700">7. --direct 是为了利用某些数据库本身提供的快速导入导出数据的工具</span>，比如mysql的mysqldump性能比jdbc更好，但是不知大对象的列，使用的时候，那些快速导入的工具的客户端必须的shell脚本的目录下。</div>
<div style="margin:0px; padding:0px"><span style="font-weight:700">8.导入数据到hdfs目录，这个命令会把数据写到/shared/foo/ 目录。</span></div>
<div style="margin:0px; padding:0px">
<pre class=" language-none" style='margin-top:0.5em; margin-bottom:0.5em; padding:1em; word-wrap:normal; overflow:auto; font-size:13px; line-height:1.5; font-family:Consolas,Monaco,"Andale Mono","Ubuntu Mono",monospace; word-break:normal; color:rgb(248,248,242); border:none; direction:ltr; word-spacing:normal; background:rgb(39,40,34)'><code class=" language-none" style='margin:0px; padding:0px; font-size:undefined; font-family:Consolas,Monaco,"Andale Mono","Ubuntu Mono",monospace; background-color:transparent; direction:ltr; word-spacing:normal; word-break:normal; word-wrap:normal; line-height:1.5'> sqoop import --connnect &lt;connect-str&gt; --table foo --warehouse-dir /shared \</code></pre>
或者<br style="">
<pre class=" language-none" style='margin-top:0.5em; margin-bottom:0.5em; padding:1em; word-wrap:normal; overflow:auto; font-size:13px; line-height:1.5; font-family:Consolas,Monaco,"Andale Mono","Ubuntu Mono",monospace; word-break:normal; color:rgb(248,248,242); border:none; direction:ltr; word-spacing:normal; background:rgb(39,40,34)'><code class=" language-none" style='margin:0px; padding:0px; font-size:undefined; font-family:Consolas,Monaco,"Andale Mono","Ubuntu Mono",monospace; background-color:transparent; direction:ltr; word-spacing:normal; word-break:normal; word-wrap:normal; line-height:1.5'> sqoop import --connnect &lt;connect-str&gt; --table foo --target-dir /dest \</code></pre>
<span style="font-weight:700">9.传递参数给快速导入的工具，使用--开头，下面这句命令传递给mysql默认的字符集是latin1。</span></div>
<div style="margin:0px; padding:0px">
<pre class=" language-none" style='margin-top:0.5em; margin-bottom:0.5em; padding:1em; word-wrap:normal; overflow:auto; font-size:13px; line-height:1.5; font-family:Consolas,Monaco,"Andale Mono","Ubuntu Mono",monospace; word-break:normal; color:rgb(248,248,242); border:none; direction:ltr; word-spacing:normal; background:rgb(39,40,34)'><code class=" language-none" style='margin:0px; padding:0px; font-size:undefined; font-family:Consolas,Monaco,"Andale Mono","Ubuntu Mono",monospace; background-color:transparent; direction:ltr; word-spacing:normal; word-break:normal; word-wrap:normal; line-height:1.5'>sqoop import --connect jdbc:mysql://server.foo.com/db --table bar \
    --direct -- --default-character-set=latin1</code></pre>
<div style="margin:0px; padding:0px"><span style="font-weight:700">10.转换为对象</span></div>
<div style="margin:0px; padding:0px">　　--map-column-java &lt;mapping&gt;  转换为java数据类型</div>
<div style="margin:0px; padding:0px">　　--map-column-hive &lt;mapping&gt;  转转为hive数据类型</div>
<div style="margin:0px; padding:0px"><span style="font-weight:700">11.增加导入</span><br style="">
</div>
<div style="margin:0px; padding:0px">　　--check-column (col)  Specifies the column to be examined when determining which rows to import.</div>
<div style="margin:0px; padding:0px">　　--incremental (mode)  Specifies how Sqoop determines which rows are new. Legal values for mode include append and lastmodified.</div>
<div style="margin:0px; padding:0px">　　--last-value (value)  Specifies the maximum value of the check column from the previous import.</div>
<div style="margin:0px; padding:0px">增加导入支持两种模式append和lastmodified，用--incremental来指定。<br style="">
</div>
<div style="margin:0px; padding:0px"><span style="font-weight:700">12.导入大对象</span>，比如BLOB和CLOB列时需要特殊处理，小于16MB的大对象可以和别的数据一起存储，超过这个值就存储在_lobs的子目录当中。</div>
<div style="margin:0px; padding:0px">它们采用的是为大对象做过优化的存储格式，最大能存储2^63字节的数据，我们可以用--inline-lob-limit参数来指定每个lob文件最大的限制是多少。如果设置为0，则大对象使用外部存储。</div>
<div style="margin:0px; padding:0px"><span style="font-weight:700">13.分隔符、转移字符</span></div>
<div style="margin:0px; padding:0px">下面的这句话<br style="">
</div>
<div style="margin:0px; padding:0px">　　Some string, with a comma.</div>
<div style="margin:0px; padding:0px">　　Another "string with quotes"</div>
<div style="margin:0px; padding:0px">使用这句命令导入$ sqoop import --fields-terminated-by , --escaped-by \\ --enclosed-by '\"' ...</div>
<div style="margin:0px; padding:0px">会有下面这个结果</div>
<div style="margin:0px; padding:0px">　　"Some string, with a comma.","1","2","3"...</div>
<div style="margin:0px; padding:0px">　　"Another \"string with quotes\"","4","5","6"...</div>
<div style="margin:0px; padding:0px">使用这句命令导入$ sqoop import --optionally-enclosed-by '\"' (the rest as above)...</div>
<div style="margin:0px; padding:0px">　　"Some string, with a comma.",1,2,3...</div>
<div style="margin:0px; padding:0px">　　"Another \"string with quotes\"",4,5,6...</div>
<div style="margin:0px; padding:0px"><span style="font-weight:700">14.hive导入参数</span></div>
<div style="margin:0px; padding:0px">　　--hive-home &lt;dir&gt;  重写$HIVE_HOME</div>
<div style="margin:0px; padding:0px">　　--hive-import          插入数据到hive当中，使用hive的默认分隔符</div>
<div style="margin:0px; padding:0px">　　--hive-overwrite  重写插入</div>
<div style="margin:0px; padding:0px">　　--create-hive-table  建表，如果表已经存在，该操作会报错！</div>
<div style="margin:0px; padding:0px">　　--hive-table &lt;table-name&gt;  设置到hive当中的表名</div>
<div style="margin:0px; padding:0px">　　--hive-drop-import-delims  导入到hive时删除 \n, \r, and \01 </div>
<div style="margin:0px; padding:0px">　　--hive-delims-replacement  导入到hive时用自定义的字符替换掉 \n, \r, and \01 </div>
<div style="margin:0px; padding:0px">　　--hive-partition-key          hive分区的key</div>
<div style="margin:0px; padding:0px">　　--hive-partition-value &lt;v&gt;  hive分区的值</div>
<div style="margin:0px; padding:0px">　　--map-column-hive &lt;map&gt;          类型匹配，sql类型对应到hive类型</div>
<div style="margin:0px; padding:0px"><span style="font-weight:700">15.hive空值处理</span></div>
<div style="margin:0px; padding:0px">sqoop会自动把NULL转换为null处理，但是hive中默认是把\N来表示null，因为预先处理不会生效的。我们需要使用 --null-string 和 --null-non-string来处理空值 把\N转为\\N。</div>
<pre class=" language-none" style='margin-top:0.5em; margin-bottom:0.5em; padding:1em; word-wrap:normal; overflow:auto; font-size:13px; line-height:1.5; font-family:Consolas,Monaco,"Andale Mono","Ubuntu Mono",monospace; word-break:normal; color:rgb(248,248,242); border:none; direction:ltr; word-spacing:normal; background:rgb(39,40,34)'><code class=" language-none" style='margin:0px; padding:0px; font-size:undefined; font-family:Consolas,Monaco,"Andale Mono","Ubuntu Mono",monospace; background-color:transparent; direction:ltr; word-spacing:normal; word-break:normal; word-wrap:normal; line-height:1.5'>sqoop import  ... --null-string '\\N' --null-non-string '\\N'</code></pre>
<div style="margin:0px; padding:0px"><span style="font-weight:700">16.导入数据到hbase</span></div>
<div style="margin:0px; padding:0px">导入的时候加上--hbase-table，它就会把内容导入到hbase当中，默认是用主键作为split列。也可以用--hbase-row-key来指定，列族用--column-family来指定,它不支持--direct。如果不想手动建表或者列族，就用--hbase-create-table参数。</div>
<div style="margin:0px; padding:0px"><span style="font-weight:700">17.代码生成参数</span>，没看懂</div>
<div style="margin:0px; padding:0px">　　--bindir &lt;dir&gt;  Output directory for compiled objects</div>
<div style="margin:0px; padding:0px">　　--class-name &lt;name&gt;  Sets the generated class name. This overrides --package-name. When combined with --jar-file, sets the input class.</div>
<div style="margin:0px; padding:0px">　　--jar-file &lt;file&gt;  Disable code generation; use specified jar</div>
<div style="margin:0px; padding:0px">　　--outdir &lt;dir&gt;  Output directory for generated code</div>
<div style="margin:0px; padding:0px">　　--package-name &lt;name&gt;  Put auto-generated classes in this package</div>
<div style="margin:0px; padding:0px">　　--map-column-java &lt;m&gt;  Override default mapping from SQL type to Java type for configured columns.</div>
<div style="margin:0px; padding:0px"><span style="font-weight:700">18.通过配置文件conf/sqoop-site.xml来配置常用参数</span></div>
<pre class=" language-none" style='margin-top:0.5em; margin-bottom:0.5em; padding:1em; word-wrap:normal; overflow:auto; font-size:13px; line-height:1.5; font-family:Consolas,Monaco,"Andale Mono","Ubuntu Mono",monospace; word-break:normal; color:rgb(248,248,242); border:none; direction:ltr; word-spacing:normal; background:rgb(39,40,34)'><code class=" language-none" style='margin:0px; padding:0px; font-size:undefined; font-family:Consolas,Monaco,"Andale Mono","Ubuntu Mono",monospace; background-color:transparent; direction:ltr; word-spacing:normal; word-break:normal; word-wrap:normal; line-height:1.5'>&lt;property&gt;
    &lt;name&gt;property.name&lt;/name&gt;
    &lt;value&gt;property.value&lt;/value&gt;
 &lt;/property&gt;</code></pre>
如果不在这里面配置的话，就需要像这样写命令</div>
<div style="margin:0px; padding:0px">
<pre class=" language-none" style='margin-top:0.5em; margin-bottom:0.5em; padding:1em; word-wrap:normal; overflow:auto; font-size:13px; line-height:1.5; font-family:Consolas,Monaco,"Andale Mono","Ubuntu Mono",monospace; word-break:normal; color:rgb(248,248,242); border:none; direction:ltr; word-spacing:normal; background:rgb(39,40,34)'><code class=" language-none" style='margin:0px; padding:0px; font-size:undefined; font-family:Consolas,Monaco,"Andale Mono","Ubuntu Mono",monospace; background-color:transparent; direction:ltr; word-spacing:normal; word-break:normal; word-wrap:normal; line-height:1.5'> sqoop import -D property.name=property.value ...</code></pre>
<div style="margin:0px; padding:0px"><span style="font-weight:700">19.两个特别的参数</span> </div>
<div style="margin:0px; padding:0px">sqoop.bigdecimal.format.string  大decimal是否保存为string，如果保存为string就是 0.0000007,否则则为1E7。sqoop.hbase.add.row.key          是否把作为rowkey的列也加到行数据当中，默认是false的。</div>
<div style="margin:0px; padding:0px"><span style="font-weight:700">20.例子</span></div>
<pre class=" language-none" style='margin-top:0.5em; margin-bottom:0.5em; padding:1em; word-wrap:normal; overflow:auto; font-size:13px; line-height:1.5; font-family:Consolas,Monaco,"Andale Mono","Ubuntu Mono",monospace; word-break:normal; color:rgb(248,248,242); border:none; direction:ltr; word-spacing:normal; background:rgb(39,40,34)'><code class=" language-none" style='margin:0px; padding:0px; font-size:undefined; font-family:Consolas,Monaco,"Andale Mono","Ubuntu Mono",monospace; background-color:transparent; direction:ltr; word-spacing:normal; word-break:normal; word-wrap:normal; line-height:1.5'>#指定列
$ sqoop import --connect jdbc:mysql://db.foo.com/corp --table EMPLOYEES \
    --columns "employee_id,first_name,last_name,job_title"
#使用8个线程
$ sqoop import --connect jdbc:mysql://db.foo.com/corp --table EMPLOYEES \
    -m 8
#快速模式
$ sqoop import --connect jdbc:mysql://db.foo.com/corp --table EMPLOYEES \
    --direct
#使用sequencefile作为存储方式
$ sqoop import --connect jdbc:mysql://db.foo.com/corp --table EMPLOYEES \
    --class-name com.foocorp.Employee --as-sequencefile
#分隔符
$ sqoop import --connect jdbc:mysql://db.foo.com/corp --table EMPLOYEES \
    --fields-terminated-by '\t' --lines-terminated-by '\n' \
    --optionally-enclosed-by '\"'
#导入到hive
$ sqoop import --connect jdbc:mysql://db.foo.com/corp --table EMPLOYEES \
    --hive-import
#条件过滤
$ sqoop import --connect jdbc:mysql://db.foo.com/corp --table EMPLOYEES \
    --where "start_date &gt; '2010-01-01'"
#用dept_id作为分个字段
$ sqoop import --connect jdbc:mysql://db.foo.com/corp --table EMPLOYEES \
    --split-by dept_id
#追加导入
$ sqoop import --connect jdbc:mysql://db.foo.com/somedb --table sometable \
    --where "id &gt; 100000" --target-dir /incremental_dataset --append</code></pre>
<div style="margin:0px; padding:0px"><span style="font-weight:700">21.导入所有的表sqoop-import-all-tables</span></div>
<div style="margin:0px; padding:0px">每个表都要有主键，不能使用where条件过滤</div>
<div style="margin:0px; padding:0px">
<pre class=" language-none" style='margin-top:0.5em; margin-bottom:0.5em; padding:1em; word-wrap:normal; overflow:auto; font-size:13px; line-height:1.5; font-family:Consolas,Monaco,"Andale Mono","Ubuntu Mono",monospace; word-break:normal; color:rgb(248,248,242); border:none; direction:ltr; word-spacing:normal; background:rgb(39,40,34)'><code class=" language-none" style='margin:0px; padding:0px; font-size:undefined; font-family:Consolas,Monaco,"Andale Mono","Ubuntu Mono",monospace; background-color:transparent; direction:ltr; word-spacing:normal; word-break:normal; word-wrap:normal; line-height:1.5'> sqoop import-all-tables --connect jdbc:mysql://db.foo.com/corp</code></pre>
<div style="margin:0px; padding:0px"><span style="font-weight:700">22.export</span></div>
<div style="margin:0px; padding:0px">我们采用sqoop-export插入数据的时候，如果数据已经存在了，插入会失败。<br style="">
</div>
<div style="margin:0px; padding:0px">如果我们使用--update-key，它会认为每个数据都是更新，比如我们使用下面这条语句：</div>
<div style="margin:0px; padding:0px">
<pre class=" language-none" style='margin-top:0.5em; margin-bottom:0.5em; padding:1em; word-wrap:normal; overflow:auto; font-size:13px; line-height:1.5; font-family:Consolas,Monaco,"Andale Mono","Ubuntu Mono",monospace; word-break:normal; color:rgb(248,248,242); border:none; direction:ltr; word-spacing:normal; background:rgb(39,40,34)'><code class=" language-none" style='margin:0px; padding:0px; font-size:undefined; font-family:Consolas,Monaco,"Andale Mono","Ubuntu Mono",monospace; background-color:transparent; direction:ltr; word-spacing:normal; word-break:normal; word-wrap:normal; line-height:1.5'>sqoop-export --table foo --update-key id --export-dir /path/to/data --connect …
　　UPDATE foo SET msg='this is a test', bar=42 WHERE id=0;
　　UPDATE foo SET msg='some more data', bar=100 WHERE id=1;
　　...</code></pre>
<div style="margin:0px; padding:0px">这样即使找不到它也不会报错。</div>
<div style="margin:0px; padding:0px"><span style="font-weight:700">23.如果存在就更新，不存在就插入</span><br style="">
</div>
<div style="margin:0px; padding:0px">加上这个参数就可以啦--update-mode allowinsert。</div>
<div style="margin:0px; padding:0px"><span style="font-weight:700">24.事务的处理</span><br style="">
</div>
<div style="margin:0px; padding:0px">它会一次statement插入100条数据，然后每100个statement提交一次，所以一次就会提交10000条数据。</div>
<div style="margin:0px; padding:0px"><span style="font-weight:700">25.例子</span></div>
<pre class=" language-none" style='margin-top:0.5em; margin-bottom:0.5em; padding:1em; word-wrap:normal; overflow:auto; font-size:13px; line-height:1.5; font-family:Consolas,Monaco,"Andale Mono","Ubuntu Mono",monospace; word-break:normal; color:rgb(248,248,242); border:none; direction:ltr; word-spacing:normal; background:rgb(39,40,34)'><code class=" language-none" style='margin:0px; padding:0px; font-size:undefined; font-family:Consolas,Monaco,"Andale Mono","Ubuntu Mono",monospace; background-color:transparent; direction:ltr; word-spacing:normal; word-break:normal; word-wrap:normal; line-height:1.5'>$ sqoop export --connect jdbc:mysql://db.example.com/foo --table bar  \
    --export-dir /results/bar_data

$ sqoop export --connect jdbc:mysql://db.example.com/foo --table bar  \
    --export-dir /results/bar_data --validate

$ sqoop export --connect jdbc:mysql://db.example.com/foo --call barproc \
    --export-dir /results/bar_data</code></pre>
</div>
</div>
</div>
</div>
<br>
</div>
</div>
</div>
</div>
            </div>
                </div>