---
layout:     post
title:      Hadoop 解除 "Name node is in safe mode"
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
hadoop dfsadmin -safemode leave<br><div class="articalTitle">
<h2 class="titName SG_txta" id="t_46d817650100sudn">Hadoop 解除 "Name node is in safe mode"</h2>
</div>
<div class="articalContent" id="sina_keyword_ad_area2">
<div>
<p>运行hadoop程序时，有时候会报以下错误：<br>
org.apache.hadoop.dfs.SafeModeException: Cannot delete /user/hadoop/input. Name node is in safe mode<br>
这个错误应该还满常见的吧（至少我运行的时候是这样的）<br><br>
那我们来分析下这个错误，从字面上来理解：<br>
Name node is in safe mode<br><br>
说明Hadoop的NameNode处在安全模式下。<br>
那什么是Hadoop的安全模式呢？<br>
在分布式文件系统启动的时候，开始的时候会有安全模式，当分布式文件系统处于安全模式的情况下，文件系统中的内容不允许修改也不允许删除，直到安全模式结束。安全模式主要是为了系统启动的时候检查各个DataNode上数据块的有效性，同时根据策略必要的复制或者删除部分数据块。运行期通过命令也可以进入安全模式。在实践过程中，系统启动的时候去修改和删除文件也会有安全模式不允许修改的出错提示，只需要等待一会儿即可。<br><br>
现在就清楚了，那现在要解决这个问题，我想让Hadoop不处在safe mode 模式下，能不能不用等，直接解决呢？<br>
答案是可以的，只要在Hadoop的目录下输入：<br>
bin/hadoop dfsadmin -safemode leave<br><br>
也就是关闭Hadoop的安全模式，这样问题就解决了。</p>
<p>之前在hadoop执行过程中使用了"ctrl+c"操作<br>
再次使用hadoop时出现<span style="background-color:rgb(255,0,0);">“Name node is in safe mode”</span>提示：<br>
root@v-jiwan-ubuntu-0:~/hadoop/hadoop-0.20.2# bin/hadoop fs -put conf input<br>
put: org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /user/root/input/conf. Name node is in safe mode.</p>
<pre>－－－－－－－－－－－－－－－－－－－－－－－－－－－－－</pre>
最近在測試hadoop，但是沒想到在reduce時卡點，<br>
沒辦法只好Ctrl+c，但是問題也就跟著來了XD<br>
先將hadoop停止後，再啟動hadoop<br>
然後要刪除DFS裡的資料時，<br>
就出現name node is in safe mode，就沒辦法刪除資料啦!<br>
找了好久才找到答案，<br><pre>bin/hadoop dfsadmin -safemode leave
就可以把safemode解除，為了這個問題煩惱了好久Orz
－－－－－－－－－－－－－－－－－－－－－－－－－－－－－
 <strong>safemode模式</strong>
NameNode在启动的时候首先进入安全模式，如果<span style="font-family:'Courier New';"><span>datanode</span>丢失的block达到一定的比例（1-dfs.safemode.threshold.pct），则系统会一直处于安全模式状态即只读状态。
dfs.safemode.threshold.pct（缺省值0.999f）表示HDFS启动的时候，如果DataNode上报的block个数达到了元<span>数据</span>记录的block个数的0.999倍才可以离开安全模式，否则一直是这种只读模式。如果设为1则HDFS永远是处于SafeMode。
下面这行摘录自NameNode启动时的日志（block上报比例1达到了阀值0.9990）
The ratio of reported blocks 1.0000 has reached the threshold 0.9990. Safe mode will be turned off automatically in 18 seconds.
 <strong>hadoop dfsadmin -safemode leave</strong>
有两个方法离开这种安全模式
1. 修改dfs.safemode.threshold.pct为一个比较小的值，缺省是0.999。
2. hadoop dfsadmin -safemode leave命令强制离开
http://bbs.hadoopor.com/viewthread.php?tid=61&amp;extra=page=1
－－－－－－－－－－－－－－－－－－－－－－－－－－－－－
Safe mode is exited when the minimal replication condition is reached, plus an extension
time of 30 seconds. The minimal replication condition is when 99.9% of the blocks in
the whole filesystem meet their minimum replication level (which defaults to one, and
is set by dfs.replication.min).
安全模式的退出前提 - 整个文件系统中的99.9%（默认是99.9%，可以通过dfs.safemode.threshold.pct设置）的Blocks达到最小备份级别(默认是1，可以通过dfs.replication.min设置)。
dfs.safemode.threshold.pct    float    0.999 
The proportion of blocks in the system that must meet the minimum
replication level defined by dfs.rep lication.min before the namenode
will exit safe mode. </span><span style="color:#ff0000;">Setting
this value to                                               
Setting this value to more than 1 means the namenode never exits safe
mode.
</span>－－－－－－－－－－－－－－－－－－－－－－－－－－－－－
用户可以通过dfsadmin -safemode value   来操作安全模式，参数value的说明如下：
enter - 进入安全模式
leave - 强制NameNode离开安全模式
get -   返回安全模式是否开启的信息
wait - 等待，一直到安全模式结束。</pre>
</div>
</div>
            </div>
                </div>