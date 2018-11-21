---
layout:     post
title:      Hive关联HBase
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span></span><span style="font-size:18px;">在Hbase中创建的表数据，怎么在hive中查询呢， 下面我们做一下hive对hbase的关联。</span></p>
<p><span style="font-size:18px;"><span></span>1.先后启动hdfs和hbase和hive</span></p>
<p><span style="font-size:18px;"><span></span>#start-all.sh</span></p>
<p><span style="font-size:18px;"><span></span>#start-hbase.sh</span></p>
<p><span style="font-size:18px;"><span></span>#hive -hiveconf hbase.zookeeper.quorum=Master.Hadoop, Slave1.Hadoop, Slave2.Hadoop</span></p>
<p><span><span style="font-size:18px;"></span></span></p>
<p><span style="font-size:18px;"><span></span>2.在hbase中创建一个表</span></p>
<p><span style="font-size:18px;"><span></span>hbase&gt;create 't_logs', 'time', 'terminalId', 'contents'</span></p>
<p><span style="font-size:18px;"><span></span>hbase&gt;put 't_logs', '1', 'time:1', '2013-05-15'</span></p>
<p><span style="font-size:18px;"><span></span>hbase&gt;put 't_logs', '1', 'terminalId:1', 'w000001'</span></p>
<p><span style="font-size:18px;"><span></span>hbase&gt;put 't_logs', '1', 'contents:1', 'http://www.hotye.com'</span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;"><span></span>hbase&gt;put 't_logs', '2', 'time:1', '2013-05-16'</span></p>
<p><span style="font-size:18px;"><span><span style="font-size:18px;">hbase&gt;put 't_logs', '2', 'terminalId:1', 'w00002'</span></span></span></p>
<p><span style="font-size:18px;"><span><span style="font-size:18px;"><span><span style="font-size:18px;">hbase&gt;put 't_logs', '2', 'contents:1', 'http://www.baidu.com'</span></span></span></span></span></p>
<p><span style="font-size:18px;"><span><span style="font-size:18px;"><span><span style="font-size:18px;"><span></span></span></span></span></span></span></p>
<p><span style="font-size:18px;"><span><span style="font-size:18px;"><span><span></span>3</span>.在hive中关联hbase中的表</span></span></span></p>
<p><span style="font-size:18px;"><span><span style="font-size:18px;"><span></span>hive&gt;create external table t_h_logs(key string, time string, terminalId string, contents string)</span></span></span></p>
<p><span style="font-size:18px;"><span><span style="font-size:18px;"><span></span>stored by 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'</span></span></span></p>
<p><span style="font-size:18px;"><span><span style="font-size:18px;"><span></span>with serdeproperties("hbase.columns.mapping"=":key, time:1, terminalId:1, contents:1")</span></span></span></p>
<p><span><span style="font-size:18px;"><span></span>tblproperties("hbase.table.name"="t_logs");</span></span></p>
<p><br></p>
<p><span><span style="font-size:18px;"><span></span>然后再在hive中查询数据</span></span></p>
<p><span><span style="font-size:18px;"><span></span>hive&gt;select * from t_h_logs;</span></span></p>
<p><span><span style="font-size:18px;"><span></span>就会显示在hbase中插入的数据。</span></span></p>
<p><br></p>
<p><span></span></p>
            </div>
                </div>