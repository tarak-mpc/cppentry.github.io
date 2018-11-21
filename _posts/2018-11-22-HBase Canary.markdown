---
layout:     post
title:      HBase Canary
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<span style="font-family:'FangSong_GB2312';font-size:18px;">    HBase  Canary 用于检测HBase 系统的状态。它对指定表的每一个region 抓取一行，来探测失败或者延迟。<br></span>
<pre class="programlisting">    hbase org.apache.hadoop.hbase.tool.Canary -help</pre>
   
<pre class="programlisting">Usage: bin/hbase org.apache.hadoop.hbase.tool.Canary [opts] [table1 [table2]...] | [regionserver1 [regionserver2]..]
 where [opts] are:
   -help          Show this help and exit.
   -regionserver  replace the table argument to regionserver,
      which means to enable regionserver mode
   -daemon        Continuous check at defined intervals.
   -interval &lt;N&gt;  Interval between checks (sec)
   -e             Use region/regionserver as regular expression
      which means the region/regionserver is regular expression pattern
   -f &lt;B&gt;         stop whole program if first error occurs, default is true
   -t &lt;N&gt;         timeout for a check, default is 600000 (milisecs)</pre>
<p><span style="font-family:'FangSong_GB2312';font-size:18px;">（1）检测每一个表的每一个region的每一个列簇。</span></p>
<p><span style="font-family:'FangSong_GB2312';font-size:18px;">    hbase  </span><span style="font-size:18px;"><span style="font-family:'FangSong_GB2312';"></span></span>org.apache.hadoop.hbase.tool.Canary<br><span style="font-family:'FangSong_GB2312';font-size:18px;"></span></p>
<p><span style="font-family:'FangSong_GB2312';font-size:18px;">（2）检测指定表的每一个region的每一个列簇，表之间用空格分开</span></p>
<p><span style="font-family:'FangSong_GB2312';font-size:18px;">    </span><span style="font-size:18px;"><span style="font-family:'FangSong_GB2312';"></span></span>hbase orghapache.hadoop.hbase.tool.Canary test-01 test-02</p>
<p><span style="font-family:'FangSong_GB2312';font-size:18px;">（3）检测regionserver <br></span></p>
<p><span style="font-family:'FangSong_GB2312';font-size:18px;">  <span style="font-family:'FangSong_GB2312';font-size:18px;">
   </span>hbase orghapache.hadoop.hbase.tool.Canary  -regionserver</span></p>
<p><span style="font-family:'FangSong_GB2312';font-size:18px;">（4）正则表达式检测</span></p>
<p><span style="font-family:'FangSong_GB2312';font-size:18px;">   <span style="font-family:'FangSong_GB2312';font-size:18px;">
<span style="font-family:'FangSong_GB2312';font-size:18px;">   </span>hbase orghapache.hadoop.hbase.tool.Canary  -e test-0[1-2]</span></span></p>
<p><span style="font-family:'FangSong_GB2312';font-size:18px;"> (5)以daemon模式运行</span></p>
<p><font face="FangSong_GB2312" size="4">    <span style="font-family:'FangSong_GB2312';font-size:18px;">
<span style="font-family:'FangSong_GB2312';font-size:18px;"><span style="font-family:'FangSong_GB2312';font-size:18px;"> 
</span>hbase orghapache.hadoop.hbase.tool.Canary</span></span> -daemon <br></font></p>
<p><span style="font-family:'FangSong_GB2312';font-size:18px;">      时间间隔为50000毫秒，出现错误不会停止<br></span></p>
<pre class="programlisting">      hbase orghapache.hadoop.hbase.tool.Canary -daemon -interval 50000 -f false</pre>
（6）指定超时
<p></p>
<p><font face="FangSong_GB2312" size="4">   <font face="FangSong_GB2312" size="4">
<span style="font-family:'FangSong_GB2312';font-size:18px;"><span style="font-family:'FangSong_GB2312';font-size:18px;"><span style="font-family:'FangSong_GB2312';font-size:18px;"> 
</span>hbase orghapache.hadoop.hbase.tool.Canary</span></span> -daemon  </font>-t 6000000<br></font></p>
            </div>
                </div>