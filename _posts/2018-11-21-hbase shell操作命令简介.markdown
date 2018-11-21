---
layout:     post
title:      hbase shell操作命令简介
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <div class="iteye-blog-content-contain" style="font-size:14px;">
<p> </p>
<p> </p>
<p>0 hbase本身就是一个数据库，也只有一个数据库，只能在里面创建表， 其语法和MySQL没有任何参考性，</p>
<p>可比较性。</p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p>1 hbase shell操作：</p>
<p> </p>
<p>在hbase/bin 目录下，输入hbase 可以看到hbase所有命令如下：</p>
<pre><code class="language-java">[root@master bin]# pwd
/usr/local/hbase/bin
[root@master bin]# hbase
Usage: hbase &lt;command&gt;
where &lt;command&gt; an option from one of these categories:

DBA TOOLS
  shell            run the HBase shell
  hbck             run the hbase 'fsck' tool
  hlog             write-ahead-log analyzer
  hfile            store file analyzer
  zkcli            run the ZooKeeper shell

PROCESS MANAGEMENT
  master           run an HBase HMaster node
  regionserver     run an HBase HRegionServer node
  zookeeper        run a Zookeeper server
  rest             run an HBase REST server
  thrift           run the HBase Thrift server
  thrift2          run the HBase Thrift2 server
  avro             run an HBase Avro server

PACKAGE MANAGEMENT
  classpath        dump hbase CLASSPATH
  version          print the version

 or
  CLASSNAME        run the class named CLASSNAME
Most commands print help when invoked w/o parameters.</code></pre>
<p> </p>
<p> hbase通用命令（在hbase shell内执行）</p>
<p> </p>
<p>a) 通用命令</p>
<ul><li style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;">
<p style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;"><strong style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-weight:bold;">status:<span class="Apple-converted-space"> </span></strong>提供HBase的状态，例如，服务器的数量。</p>
</li>
<li style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;">
<p style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;"><strong style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-weight:bold;">version:<span class="Apple-converted-space"> </span></strong>提供正在使用HBase版本。</p>
</li>
<li style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;">
<p style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;"><strong style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-weight:bold;">table_help:<span class="Apple-converted-space"> </span></strong>表引用命令提供帮助。</p>
</li>
<li style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;">
<p style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;"><strong style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-weight:bold;">whoami:<span class="Apple-converted-space"> </span></strong>提供有关用户的信息。</p>
</li>
</ul><p> </p>
<p>b) 数据定义语言</p>
<p> </p>
<p>hbase在表的操作命令:</p>
<ul><li style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;">
<strong style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-weight:bold;">create:<span class="Apple-converted-space"> </span></strong>创建一个表。</li>
<li style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;">
<strong style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-weight:bold;">list:<span class="Apple-converted-space"> </span></strong>列出HBase的所有表。</li>
<li style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;">
<strong style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-weight:bold;">disable:<span class="Apple-converted-space"> </span></strong>禁用表。</li>
<li style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;">
<strong style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-weight:bold;">is_disabled:<span class="Apple-converted-space"> </span></strong>验证表是否被禁用。</li>
<li style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;">
<strong style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-weight:bold;">enable:<span class="Apple-converted-space"> </span></strong>启用一个表。</li>
<li style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;">
<strong style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-weight:bold;">is_enabled:<span class="Apple-converted-space"> </span></strong>验证表是否已启用。</li>
<li style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;">
<strong style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-weight:bold;">describe:<span class="Apple-converted-space"> </span></strong>提供了一个表的描述。</li>
<li style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;">
<strong style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-weight:bold;">alter:<span class="Apple-converted-space"> </span></strong>改变一个表。</li>
<li style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;">
<strong style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-weight:bold;">exists:<span class="Apple-converted-space"> </span></strong>验证表是否存在。</li>
<li style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;">
<strong style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-weight:bold;">drop:<span class="Apple-converted-space"> </span></strong>从HBase中删除表。</li>
<li style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;">
<strong style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-weight:bold;">drop_all:<span class="Apple-converted-space"> </span></strong>丢弃在命令中给出匹配“regex”的表。</li>
<li style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;">
<strong style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-weight:bold;">Java Admin API:<span class="Apple-converted-space"> </span></strong>在此之前所有的上述命令，Java提供了一个通过API编程来管理实现DDL功能。在这个org.apache.hadoop.hbase.client包中有HBaseAdmin和HTableDescriptor 这两个重要的类提供DDL功能。</li>
</ul><p> </p>
<ul><li style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;">
<strong style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-weight:bold;">put:<span class="Apple-converted-space"> </span></strong>把指定列在指定的行中单元格的值在一个特定的表。</li>
<li style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;">
<strong style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-weight:bold;">get:<span class="Apple-converted-space"> </span></strong>取行或单元格的内容。</li>
<li style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;">
<strong style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-weight:bold;">delete:<span class="Apple-converted-space"> </span></strong>删除表中的单元格值。</li>
<li style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;">
<strong style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-weight:bold;">deleteall:</strong><span class="Apple-converted-space"> </span>删除给定行的所有单元格。</li>
<li style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;">
<strong style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-weight:bold;">scan:</strong><span class="Apple-converted-space"> </span>扫描并返回表数据。</li>
<li style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;">
<strong style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-weight:bold;">count:<span class="Apple-converted-space"> </span></strong>计数并返回表中的行的数目。</li>
<li style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;">
<strong style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-weight:bold;">truncate:<span class="Apple-converted-space"> </span></strong>禁用，删除和重新创建一个指定的表。</li>
<li style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;">
<strong style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-weight:bold;">Java client API:<span class="Apple-converted-space"> </span></strong>在此之前所有上述命令，Java提供了一个客户端API来实现DML功能，CRUD（创建检索更新删除）操作更多的是通过编程，在org.apache.hadoop.hbase.client包下。 在此包HTable 的 Put和Get是重要的类。</li>
</ul><p> </p>
<p> </p>
<p> 应用案例:</p>
<p> </p>
<pre><code class="language-java">0 执行/bin/hbase shell启动命令行
hbase(main):001:0&gt; 
 

1.创建表 create : create 表名 列簇名称1,列簇名称2...
hbase(main):002:0&gt; create 'stu','base','more'

创建表时候制定列族的高级特性 ：
hbase(main):002:0&gt; create 'stu', {NAME =&gt; 't',VERSION=1}
  

2.检测表是否存在 exists 或者 list
hbase(main):002:0&gt; exists 'stu'
Table stu does exist                                                            
0 row(s) in 0.1000 seconds



显示所有表list：
hbase(main):003:0&gt; list
TABLE                                                                           
stu                                                                             
1 row(s) in 0.0840 seconds

=&gt; ["stu"]


3.插入记录 put
put 表名  行健  列簇:列名  列值
下面表示向hdfs文件bas插入两条记录

hbase(main):004:0&gt; put 'stu','1','base:id','1'
0 row(s) in 0.4080 seconds

hbase(main):005:0&gt; put 'stu','1','more:sex','man'     可以看到，不同列簇可以拥有相同的 row key
0 row(s) in 0.4560 seconds

hbase(main):006:0&gt; put 'stu','1','base:name','zm'
0 row(s) in 0.5030 seconds

hbase(main):007:0&gt; put 'stu','1','base:age','30'
0 row(s) in 0.0240 seconds


4.查询一条记录  get 表名 行健 
get 'stu','1'  将行健对应的列按行展示
hbase(main):008:0&gt; get 'stu','1'
COLUMN                CELL                                                      
 base:age             timestamp=1426159948234, value=30                         
 base:id              timestamp=1426159671374, value=1                          
 base:name            timestamp=1426159926391, value=zm                         
 more:sex             timestamp=1426159691563, value=man                        
4 row(s) in 0.2220 seconds


或者查询行健下的具体列
hbase(main):009:0&gt; get 'stu','1','base:name'
COLUMN                CELL                                                      
 base:name            timestamp=1426159926391, value=zm                         
1 row(s) in 0.0570 seconds

5.查询所有记录
scan 'stu'

hbase(main):015:0&gt; scan  'stu'
ROW                   COLUMN+CELL                                               
 1                    column=base:age, timestamp=1426159948234, value=30        
 1                    column=base:id, timestamp=1426159671374, value=1          
 1                    column=base:name, timestamp=1426159926391, value=zm       
 1                    column=more:sex, timestamp=1426159691563, value=man       
 2                    column=base:age, timestamp=1426160220320, value=27        
 2                    column=base:id, timestamp=1426160248465, value=2          
 2                    column=base:name, timestamp=1426160205334, value=xin      
2 row(s) in 0.0500 seconds


使用scan查询某个列的所有数据:

hbase(main):004:0&gt; scan 'stu',{COLUMNS=&gt;'base:name'}

ROW                      COLUMN+CELL

 1                       column=base:name, timestamp=1426159926391, value=zm
 2                       column=base:name, timestamp=1426160205334, value=xin


6.删除一条记录
hbase(main):019:0&gt; deleteall 'stu','2'
0 row(s) in 0.0290 seconds

hbase(main):020:0&gt; get 'stu','2'
COLUMN                CELL                                                      
0 row(s) in 0.0240 seconds


7.删除一个列
hbase(main):016:0&gt; delete 'stu','1','more:sex'
0 row(s) in 0.3190 seconds

hbase(main):017:0&gt; get 'stu','1'
COLUMN                CELL                                                      
 base:age             timestamp=1426159948234, value=30                         
 base:id              timestamp=1426159671374, value=1                          
 base:name            timestamp=1426159926391, value=zm                         
3 row(s) in 0.0420 seconds


8.修改列值
hbase(main):021:0&gt; put 'stu','1','base:age','31'
0 row(s) in 0.0150 seconds

hbase(main):022:0&gt; get 'stu','1'
COLUMN                CELL                                                      
 base:age             timestamp=1426160505433, value=31     将最新的结果查出来                      
 base:id              timestamp=1426159671374, value=1                          
 base:name            timestamp=1426159926391, value=zm                         
3 row(s) in 0.0610 seconds


9.删除表
hbase(main):023:0&gt; disable 'stu'
0 row(s) in 11.4010 seconds

hbase(main):025:0&gt; drop 'stu'
0 row(s) in 0.8830 seconds
</code></pre>
<p> </p>
<p><br> <strong>alter :  删除表的所有列族</strong></p>
<p> </p>
<pre><code class="language-java">hbase shell 删除列族
hbase(main):002:0&gt; alter 'stu','delete' =&gt; 'more'   这样就将stu表列族more和下面数据删除掉
Updating all regions with the new schema...
1/1 regions updated.
Done.
0 row(s) in 1.1700 seconds</code></pre>
<p> </p>
<p><strong> 清空表数据</strong>:  truncate  table</p>
<p> </p>
<p> </p>
<p> hbase 表权限:    这几个在工作中暂时没有用到过</p>
<p>grant</p>
<p>我们可以从RWXCA组，其中给予零个或多个特权给用户</p>
<ul><li style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;">R - 代表读取权限</li>
<li style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;">W - 代表写权限</li>
<li style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;">X - 代表执行权限</li>
<li style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;">C - 代表创建权限</li>
<li style="font-size:16px;font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;">A - 代表管理权限</li>
</ul><p>下面给出是为用户“Tutorialspoint'授予所有权限的例子。</p>
<pre class="prettyprint notranslate prettyprinted"><span class="pln" style="font-size:14px;font-family:'Courier New', Courier, monospace;color:#000000;">hbase</span><span class="pun" style="font-size:14px;font-family:'Courier New', Courier, monospace;color:#666600;">(</span><span class="pln" style="font-size:14px;font-family:'Courier New', Courier, monospace;color:#000000;">main</span><span class="pun" style="font-size:14px;font-family:'Courier New', Courier, monospace;color:#666600;">):</span><span class="lit" style="font-size:14px;font-family:'Courier New', Courier, monospace;color:#006666;">018</span><span class="pun" style="font-size:14px;font-family:'Courier New', Courier, monospace;color:#666600;">:</span><span class="lit" style="font-size:14px;font-family:'Courier New', Courier, monospace;color:#006666;">0</span><span class="pun" style="font-size:14px;font-family:'Courier New', Courier, monospace;color:#666600;">&gt;</span><span class="pln" style="font-size:14px;font-family:'Courier New', Courier, monospace;color:#000000;"> grant </span><span class="str" style="font-size:14px;font-family:'Courier New', Courier, monospace;color:#008800;">'Tutorialspoint'</span><span class="pun" style="font-size:14px;font-family:'Courier New', Courier, monospace;color:#666600;">,</span><span class="str" style="font-size:14px;font-family:'Courier New', Courier, monospace;color:#008800;">'RWXCA'</span></pre>
</div>
<p> </p>
<p>revoke</p>
<p>下面的代码撤消名为“Tutorialspoint”用户的所有权限。</p>
<pre class="prettyprint notranslate prettyprinted"><span class="pln" style="font-size:14px;font-family:'Courier New', Courier, monospace;color:#000000;">hbase</span><span class="pun" style="font-size:14px;font-family:'Courier New', Courier, monospace;color:#666600;">(</span><span class="pln" style="font-size:14px;font-family:'Courier New', Courier, monospace;color:#000000;">main</span><span class="pun" style="font-size:14px;font-family:'Courier New', Courier, monospace;color:#666600;">):</span><span class="lit" style="font-size:14px;font-family:'Courier New', Courier, monospace;color:#006666;">006</span><span class="pun" style="font-size:14px;font-family:'Courier New', Courier, monospace;color:#666600;">:</span><span class="lit" style="font-size:14px;font-family:'Courier New', Courier, monospace;color:#006666;">0</span><span class="pun" style="font-size:14px;font-family:'Courier New', Courier, monospace;color:#666600;">&gt;</span><span class="pln" style="font-size:14px;font-family:'Courier New', Courier, monospace;color:#000000;"> revoke </span><span class="str" style="font-size:14px;font-family:'Courier New', Courier, monospace;color:#008800;">'Tutorialspoint'</span></pre>
<p> </p>
<p><span>user_permission:  <span>列出特定表的所有权限</span></span></p>
<p>下面的代码列出了“emp”表的所有用户权限。</p>
<pre class="prettyprint notranslate prettyprinted"><span class="pln" style="font-size:14px;font-family:'Courier New', Courier, monospace;color:#000000;">hbase</span><span class="pun" style="font-size:14px;font-family:'Courier New', Courier, monospace;color:#666600;">(</span><span class="pln" style="font-size:14px;font-family:'Courier New', Courier, monospace;color:#000000;">main</span><span class="pun" style="font-size:14px;font-family:'Courier New', Courier, monospace;color:#666600;">):</span><span class="lit" style="font-size:14px;font-family:'Courier New', Courier, monospace;color:#006666;">013</span><span class="pun" style="font-size:14px;font-family:'Courier New', Courier, monospace;color:#666600;">:</span><span class="lit" style="font-size:14px;font-family:'Courier New', Courier, monospace;color:#006666;">0</span><span class="pun" style="font-size:14px;font-family:'Courier New', Courier, monospace;color:#666600;">&gt;</span><span class="pln" style="font-size:14px;font-family:'Courier New', Courier, monospace;color:#000000;"> user_permission </span><span class="str" style="font-size:14px;font-family:'Courier New', Courier, monospace;color:#008800;">'emp'</span></pre>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p><span style="color:#ff0000;"><strong>hbase表清空分区数据，但是保留分区：</strong></span></p>
<p> </p>
<pre><code class="language-java">truncate_preserve 'youtable'</code></pre>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>            </div>
                </div>