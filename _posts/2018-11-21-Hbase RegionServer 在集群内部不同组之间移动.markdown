---
layout:     post
title:      Hbase RegionServer 在集群内部不同组之间移动
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/liaynling/article/details/73478126				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h3 style="font-weight:500;line-height:1.1;color:rgb(76,78,84);">
<span style="font-family:'Source Sans Pro', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:1.25em;"></span></h3>
<span style="font-size:18px;">方法一<br></span><br><br><span style="font-family:SimSun;font-size:14px;">1.先关指定组的balance<br>
#hbase shell进去<br>
关某个分组：<br>
hbase shell&gt;group_set_balance_switch '$group1',false<br>
关全局：<br>
hbase shell&gt;balance_switch false<br><br><br>
2. 移走region<br>
nohup hbase org.apache.hadoop.hbase.util.RegionMover -host $hostname  -group $group1 -sleeptime 1000 -checkratio 999 -threads 5&amp;<br>
或者<br>
hbase org.jruby.Main /home/hadoop/hbase-current/bin/region_mover.rb unload  $hostip:60020<br><br><br>
3.确认机器上的region已经全部移走后，移动机器到新的组<br>
hbase shell&gt;group_move_server  '$hostip:60020','$group2'<br><br><br>
4.恢复balance<br>
hbase shell&gt;group_set_balance_switch '$group1’,true</span><br><br><br><span style="font-size:18px;">方法二<br></span><br><span style="font-family:SimSun;font-size:14px;"><br>
1. 移走region<br>
~/hbase-current/bin/graceful_stop.sh  $hostip<br><br><br>
2.确认机器上的region已经全部移走后，移动机器到group2<br>
hbase shell&gt;group_move_server  '$hostip:60020','$group2'Hbase机器在本集群不同组之间移动</span>
<h3 style="font-weight:500;line-height:1.1;color:rgb(76,78,84);">
</h3>
            </div>
                </div>