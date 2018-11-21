---
layout:     post
title:      hbase系列-hbase导入导出
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创，转载请声明出处。					https://blog.csdn.net/weixin_41279060/article/details/78865558				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;"><strong>1、创建test1表</strong></span></div>
<div style="line-height:1.75;font-size:14px;"># hbase shell</div>
<div style="line-height:1.75;font-size:14px;">hbase(main):004:0&gt; create 'test1', 'cf'</div>
<div style="line-height:1.75;font-size:14px;">0 row(s) in 1.2450 seconds</div>
<div style="line-height:1.75;font-size:14px;">=&gt; Hbase::Table - test1</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;"><strong>2、查看创建好的表</strong></span></div>
<div style="line-height:1.75;font-size:14px;">hbase(main):005:0&gt; list</div>
<div style="line-height:1.75;font-size:14px;">test1</div>
<div style="line-height:1.75;font-size:14px;">1 row(s) in 0.0160 seconds</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;"><strong>3、往test1表添加数据</strong></span></div>
<div style="line-height:1.75;font-size:14px;">hbase(main):006:0&gt; put 'test1','1','cf:name','名字'</div>
<div style="line-height:1.75;font-size:14px;">0 row(s) in 0.0730 seconds</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;"><strong>4、查看数据</strong></span></div>
<div style="line-height:1.75;font-size:14px;">hbase(main):008:0&gt; scan "test1"</div>
<div style="line-height:1.75;font-size:14px;">ROW COLUMN+CELL
</div>
<div style="line-height:1.75;font-size:14px;">1 column=cf:name, timestamp=1513841265980, value=\xE5\x90\x8D\xE5\xAD\x97
</div>
<div style="line-height:1.75;font-size:14px;">1 row(s) in 0.0130 seconds</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;"><strong>5、创建另一个空表test2</strong></span></div>
<div style="line-height:1.75;font-size:14px;">hbase(main):009:0&gt; create 'test2','cf'</div>
<div style="line-height:1.75;font-size:14px;">0 row(s) in 1.2280 seconds</div>
<div style="line-height:1.75;font-size:14px;">=&gt; Hbase::Table - test2</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;"><strong>6、导出表test1的数据</strong></span></div>
<div style="line-height:1.75;font-size:14px;">先切换hdfs用户</div>
<div style="line-height:1.75;font-size:14px;"># su hdfs</div>
<div style="line-height:1.75;font-size:14px;"><span style="color:rgb(223,64,42);"><strong>导出数据</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="color:rgb(223,64,42);"><strong># hbase org.apache.hadoop.hbase.mapreduce.Driver export test1 /test1</strong></span></div>
<div style="line-height:1.75;font-size:14px;">这时候会执行mapreduce，导出数据到hdfs文件目录</div>
<div style="line-height:1.75;font-size:14px;">查看hdfs文件目录，数据也导出来了</div>
<div style="line-height:1.75;font-size:14px;"># hadoop fs -ls /</div>
<div style="line-height:1.75;font-size:14px;">Found 4 items</div>
<div style="line-height:1.75;font-size:14px;">drwxr-xr-x - hbase hbase 0 2017-12-21 14:20 /hbase</div>
<div style="line-height:1.75;font-size:14px;">drwxr-xr-x - hdfs supergroup 0 2017-12-21 16:46 /test1</div>
<div style="line-height:1.75;font-size:14px;">drwxrwxrwt - hdfs supergroup 0 2017-12-20 17:42 /tmp</div>
<div style="line-height:1.75;font-size:14px;">drwxr-xr-x - hdfs supergroup 0 2017-12-21 11:53 /user</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;">如果想导出数据到本地文件系统，有些博客说加前缀 file:///，也就是</div>
<div style="line-height:1.75;font-size:14px;"># hbase org.apache.hadoop.hbase.mapreduce.Driver export test1 file:///test1</div>
<div style="line-height:1.75;font-size:14px;">但测试之后，首先是文件目录的权限问题，文件目录权限好了，导出时也报错_temporary/1不存在</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;"><strong>7、导入数据到test2</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="color:rgb(223,64,42);"><strong>hbase org.apache.hadoop.hbase.mapreduce.Driver import test3 hdfs://192.168.100.60:8022/test1</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="color:rgb(223,64,42);"><strong>备注：hdfs的端口为</strong></span><span style="color:rgb(223,64,42);background-color:rgb(248,248,248);"><strong>NameNode 服务 RPC 端口dfs.namenode.servicerpc-address</strong></span></div>
            </div>
                </div>