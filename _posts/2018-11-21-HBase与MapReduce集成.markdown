---
layout:     post
title:      HBase与MapReduce集成
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<strong><span style="font-size:14px;">HBase与MapReduce集成<br><span></span>1、集成的模式<br><span></span>-》从hbase读数据，就是将hbase数据作为map的输入<br><span></span>-》将数据写入hbase，将hbase作为reduce的输出<br><span></span>-》inputformat输入-》key默认类型longwritable+value text类型<br><span></span>-》outputformat输出<br><span></span>-》写入HDFS文件<br><span></span>-》上面两种的结合，从hbase读，再写入hbase，更多的适合数据迁移的场景<br><br><br>
$ /opt/moduels/hadoop-2.5.0/bin/yarn jar lib/hbase-server-0.98.6-hadoop2.jar <br><span></span>2、解决方法<br><span></span>-》将所有hbase的jar包放入Hadoop的运行环境变量中<br><span></span>-》HADOOP_CLASSPATH<br><span></span>-》为了防止jar包之间的冲突，这种做法不推荐<br><span></span>-》将需要的hbase的jar包放入Hadoop的运行环境变量中<br><span></span>-》export HBASE_HOME=/opt/moduels/hbase-0.98.6-hadoop2<br><span></span>-》export HADOOP_CLASSPATH=$HADOOP_CLASSPATH:`$HBASE_HOME/bin/hbase mapredcp`<br><span></span>3、测试rowcounter<br><span></span>/opt/moduels/hadoop-2.5.0/bin/yarn jar lib/hbase-server-0.98.6-hadoop2.jar rowcounter nstest:tb1</span></strong>
            </div>
                </div>