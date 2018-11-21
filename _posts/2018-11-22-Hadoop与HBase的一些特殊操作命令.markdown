---
layout:     post
title:      Hadoop与HBase的一些特殊操作命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>Hadoop<span style="font-family:'宋体';">与</span><span style="font-family:'Times New Roman';">HBase</span><span style="font-family:'宋体';">的一些特殊操作命令</span></p>
<p> </p>
<p>1.将<span style="font-family:'Times New Roman';">hbase</span><span style="font-family:'宋体';">数据库中的表导出到</span><span style="font-family:'Times New Roman';">hdfs</span><span style="font-family:'宋体';">中的命令</span><span style="font-family:'Times New Roman';">:</span></p>
<p><span></span>Hbase org.apache.hadoop.hbase.mapreduce.Export &lt;tablename&gt; &lt;output dir&gt; [&lt;versions&gt; [&lt;starttime&gt; [&lt;endtime&gt;]]]</p>
<p>2.加载导出的数据回到<span style="font-family:'Times New Roman';">HBase</span><span style="font-family:'宋体';">的命令：</span></p>
<p><span></span>Hbase org.apache.hadoop.hbase.mapreduce.Import &lt;tablename&gt; &lt;inputdir&gt;</p>
<p>注意：导入时表必须存在，导出的路径可以使本地系统也可以是hdfs,如果导出到本地系统，建议使用:file:///</p>
<p>3.completebulkload <span style="font-family:'宋体';">实用工具可以将产生的存储文件移动到</span><span style="font-family:'Times New Roman';">HBase</span><span style="font-family:'宋体';">表。该工具经常和</span><a href="http://abloz.com/hbase/book.html#importtsv" rel="nofollow">Section <span style="font-family:'宋体';">“</span><span style="font-family:'Times New Roman';">ImportTsv</span><span style="font-family:'宋体';">”</span></a> 的输出联合使用。</p>
<p>两种方法调用该工具，带显式类名或通过驱动：</p>
<p><span></span>$ bin/hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles  &lt;hdfs://storefileoutput&gt; &lt;tablename&gt;</p>
<p> </p>
<p>.. <span style="font-family:'宋体';">通过驱动</span><span style="font-family:'Times New Roman';">..</span></p>
<p><span></span>HADOOP_CLASSPATH=`${HBASE_HOME}/bin/hbase classpath` ${HADOOP_HOME}/bin/hadoop jar ${HBASE_HOME}/hbase-VERSION.jar completebulkload &lt;hdfs://storefileoutput&gt; &lt;tablename&gt;</p>
<p> </p>
<p>4.RowCounter <span style="font-family:'宋体';">实时工具可以统计表的行数。可以用来确认</span><span style="font-family:'Times New Roman';">HBase</span><span style="font-family:'宋体';">可以读取表的所有分块</span></p>
<p><span></span>Hbase org.apache.hadoop.hbase.mapreduce.RowCounter &lt;tablename&gt; [&lt;column1&gt; &lt;column2&gt; . . . ]</p>
<p>5.合并</p>
<p><span></span>Hbase org.apache.hbase.util.Merge &lt;tablename&gt; &lt;region1&gt; &lt;region2&gt;</p>
<p>Note:Merge must run be done when the cluster is down</p>
<p>6.节点下线</p>
<p><span></span>Hbase-daemon.sh stop regionserver</p>
<p>在下线节点前要停止<span style="font-family:'Times New Roman';">Load Balancer</span></p>
<p>要下线一台<span style="font-family:'Times New Roman';">RegionServer</span><span style="font-family:'宋体';">可以这样做</span><span style="font-family:'Times New Roman';">,</span><span style="font-family:'宋体';">当执行</span><span style="font-family:'Times New Roman';">graceful_stop</span><span style="font-family:'宋体';">脚本的时候，要将</span><span style="font-family:'Times New Roman';">Region Load Balancer</span><span style="font-family:'宋体';">关掉（否则</span><span style="font-family:'Times New Roman';">balancer</span><span style="font-family:'宋体';">和下线脚本会在</span><span style="font-family:'Times New Roman';">region</span><span style="font-family:'宋体';">部署的问题上存在冲突）</span></p>
<p>graceful_stop.sh HOSTNAME</p>
<p>7.关闭和开启<span style="font-family:'Times New Roman';">load balancer</span></p>
<p>Hbase&gt; balance_switch false //<span style="font-family:'宋体';">关闭</span></p>
<p>Hbase&gt; balance_switch true //<span style="font-family:'宋体';">开启</span></p>
<p>8.依次重启</p>
<p>你还可以让这个脚本重启一个<span style="font-family:'Times New Roman';">RegionServer,</span><span style="font-family:'宋体';">不改变上面的</span><span style="font-family:'Times New Roman';">Region</span><span style="font-family:'宋体';">的位置。要想保留数据的位置，你可以依次重启</span><span style="font-family:'Times New Roman';">(Rolling Restart),</span><span style="font-family:'宋体';">就像这样</span><span style="font-family:'Times New Roman';">:</span></p>
<p><span></span>$ for i in `cat conf/regionservers|sort`; do ./bin/graceful_stop.sh --restart --reload --debug $i; done &amp;&gt; /tmp/log.txt &amp;</p>
<p>           </p>
<p>Tail /tmp/log.txt<span style="font-family:'宋体';">来看脚本的运行过程</span><span style="font-family:'Times New Roman';">.</span><span style="font-family:'宋体';">上面的脚本只对</span><span style="font-family:'Times New Roman';">RegionServer</span><span style="font-family:'宋体';">进行操作。要确认</span><span style="font-family:'Times New Roman';">load balancer</span><span style="font-family:'宋体';">已经关掉。还需要在之前更新</span><span style="font-family:'Times New Roman';">master</span><span style="font-family:'宋体';">。下面是一段依次重启的伪脚本</span><span style="font-family:'Times New Roman';">,</span><span style="font-family:'宋体';">你可以借鉴它：</span></p>
<p>确认你的版本，保证配置已经<span style="font-family:'Times New Roman';">rsync</span><span style="font-family:'宋体';">到整个集群中。如果版本是</span><span style="font-family:'Times New Roman';">0.90.2</span><span style="font-family:'宋体';">，需要打上</span><span style="font-family:'Times New Roman';">HBASE-3744 </span><span style="font-family:'宋体';">和 </span><span style="font-family:'Times New Roman';">HBASE-3756</span><span style="font-family:'宋体';">两个补丁。</span></p>
<p>运行<span style="font-family:'Times New Roman';">hbck</span><span style="font-family:'宋体';">确保你的集群是一致的</span></p>
<p>$ ./bin/hbase hbck</p>
<p>当发现不一致的时候，可以修复他。</p>
<p>重启<span style="font-family:'Times New Roman';">Master:</span></p>
<p>$ ./bin/hbase-daemon.sh stop master; ./bin/hbase-daemon.sh start master</p>
<p>关闭<span style="font-family:'Times New Roman';">region balancer:</span></p>
<p>$ echo "balance_switch false" | ./bin/hbase</p>
<p>在每个<span style="font-family:'Times New Roman';">RegionServer</span><span style="font-family:'宋体';">上运行</span><span style="font-family:'Times New Roman';">graceful_stop.sh</span><span style="font-family:'宋体';">：</span></p>
<p>$ for i in `cat conf/regionservers|sort`; do ./bin/graceful_stop.sh --restart --reload --debug $i; done &amp;&gt; /tmp/log.txt &amp;</p>
<p>           </p>
<p>如果你在<span style="font-family:'Times New Roman';">RegionServer</span><span style="font-family:'宋体';">还开起来</span><span style="font-family:'Times New Roman';">thrift</span><span style="font-family:'宋体';">和</span><span style="font-family:'Times New Roman';">rest server</span><span style="font-family:'宋体';">。还需要加上</span><span style="font-family:'Times New Roman';">--thrift or --rest </span><span style="font-family:'宋体';">选项 </span><span style="font-family:'Times New Roman';">(</span><span style="font-family:'宋体';">参见 </span><span style="font-family:'Times New Roman';">graceful_stop.sh </span><span style="font-family:'宋体';">脚本的用法</span><span style="font-family:'Times New Roman';">).</span></p>
<p>再次重启<span style="font-family:'Times New Roman';">Master.</span><span style="font-family:'宋体';">这会把已经死亡的</span><span style="font-family:'Times New Roman';">server</span><span style="font-family:'宋体';">列表清空，重新开启</span><span style="font-family:'Times New Roman';">balancer.</span></p>
<p>运行 <span style="font-family:'Times New Roman';">hbck </span><span style="font-family:'宋体';">保证集群是一直的</span></p>
            </div>
                </div>