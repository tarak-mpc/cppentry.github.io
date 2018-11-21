---
layout:     post
title:      【Hbase】Hbase常用命令总结
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_32616843/article/details/78446855				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:18px;">Hbase常用命令总结：（非常实用）</span></p>
<p><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:14px;">1）进入HBase Shell。</span></p>
<span style="font-size:14px;">[root@master ~]# $HBASE_HOME/bin/hbase shell <br>
2）新建user表。<br>
hbase(main):001:0&gt; create 'user','info'<br>
3）导入user表中的数据。<br>
hbase(main):002:0&gt; put 'user','001','info:name','Tom'<br>
4）查看user表中的数据。<br>
hbase(main):008:0&gt; scan 'user'<br>
ROW                    COLUMN+CELL                                                  <br>
 001                   column=info:age, timestamp=1439433026307, value=22    <br>
5）查看user表的描述。       <br>
hbase(main):009:0&gt; describe 'user'<br>
6）删除user表。<br>
hbase(main):010:0&gt; disable 'user' 先禁用<br>
hbase(main):011:0&gt; drop 'user'<br>
7）查询表<br>
hbase(main):012:0&gt; list<br>
8）帮助<br>
hbase(main):001:0&gt;help<br>
9）数据库状态<br>
hbase(main):024:0&gt;status<br>
10）数据库版本<br>
hbase(main):024:0&gt;version<br>
11）删除列族：alter、disable、enable命令 <br>
hbase(main):004:0&gt;disable 'member' <br>
hbase(main):005:0&gt;alter'member',{NAME=&gt;'member_id',METHOD=&gt;'delete'} （实测不可以先disable表）<br>
hbase(main):008:0&gt; enable 'member' <br>
12）查询一个表是否存在<br>
hbase(main):021:0&gt;exists 'member' <br>
13）判断表是否enable或disable <br>
hbase(main):034:0&gt;is_enabled 'member' <br>
hbase(main):032:0&gt;is_disabled 'member' <br>
14）插入记录 <br>
put'member','scutshuxue','info:age','24' <br>
15）获取一个行健的所有数据 <br>
hbase(main):001:0&gt;get 'member','scutshuxue' <br>
16）获取一个行键，一个列族的所有数据 <br>
hbase(main):002:0&gt;get 'member','scutshuxue','info' <br>
17）获取一个行键，一个列族中一个列的所有数据 <br>
hbase(main):002:0&gt;get 'member','scutshuxue','info:age' <br>
18）更新一条记录 <br>
hbase(main):004:0&gt;put 'member','scutshuxue','info:age' ,'99' <br>
19）通过timestamp来获取数据 <br>
hbase(main):010:0&gt;get 'member','scutshuxue',{COLUMN=&gt;'info:age',TIMESTAMP=&gt;1321586238965} <br>
20）删除指定行键的字段 <br>
hbase(main):016:0&gt;delete 'member','temp','info:age' <br>
21）删除整行或整列 （实测不可以先disable表）<br>
hbase(main):001:0&gt;deleteall 'member','xiaofeng' <br>
hbase(main):001:0&gt;deleteall 'member','101' <br>
22）查询表中有多少行 <br>
hbase(main):019:0&gt;count 'member' <br>
23）清空表 <br>
hbase(main):035:0&gt;truncate 'member' </span>
            </div>
                </div>