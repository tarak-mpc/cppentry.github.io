---
layout:     post
title:      hbase--Hbase 建表基本命令总结
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>来源：http://blog.csdn.net/kky2010_110/article/details/12616137</p>
<p><br></p>
<p></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">访问hbase,以及操作hbase，命令不用使用分号<br>
hbase shell 进入hbase</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">list 查看表<br>
hbase shell -d</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">hbase(main):024:0&gt; scan '.META.'</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">=============小例子===================================================</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">1. 创建一个表member<br>
hbase(main):025:0&gt; create 'member','m_id','address','info'</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">2.list #查看所有表</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">3.查看表描述<br>
hbase(main):028:0&gt; describe 'member'</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">4. scan浏览表中数据<br>
scan 'member'</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">5.<br>
删除一个列族m_id：先将表disable（如果表中之前有数据的话，会把数据清空）<br>
hbase(main):030:0&gt; disable 'member'</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">hbase(main):033:0&gt; is_enabled 'member'</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">hbase(main):034:0&gt; alter 'member',{NAME=&gt;'m_id',METHOD=&gt;'delete'}</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">最后enable，否则不能进行其他操作<br>
hbase(main):038:0&gt; enable 'member'</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;"><br>
6.插入几条记录</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">put'member','scutshuxue','info:age','24'</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">put'member','scutshuxue','info:birthday','1987-06-17'</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">put'member','scutshuxue','info:company','alibaba'</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">put'member','scutshuxue','address:contry','china' </p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">put'member','scutshuxue','address:province','zhejiang' </p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">put'member','scutshuxue','address:city','hangzhou' </p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">scan 'member'</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;"><br>
7.获取数据<br>
获取一个id的所有数据<br>
hbase(main):001:0&gt;get 'member','scutshuxue'</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">获取一个id，一个列族的所有数据<br>
hbase(main):002:0&gt;get 'member','scutshuxue','info'</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">获取一个id，一个列族中一个列的所有数据<br>
hbase(main):002:0&gt;get 'member','scutshuxue','info:age' </p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">通过timestamp来获取两个版本的数据<br>
hbase(main):010:0&gt;get 'member','scutshuxue',{COLUMN=&gt;'info:age',TIMESTAMP=&gt;1321586238965}</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;"><br>
8.  更新一条记录</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">将scutshuxue的年龄改成99<br>
hbase(main):004:0&gt;put 'member','scutshuxue','info:age' ,'99'</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">10.<br>
查询表中有多少行：<br>
hbase(main):019:0&gt;count 'member'  </p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">11.<br>
删除整行<br>
hbase(main):001:0&gt;deleteall 'member','xiaofeng'</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">12.<br>
删除id为xiaofeng的值的‘info:age’字段<br>
hbase(main):016:0&gt;delete 'member','temp','info:age'</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">13.<br>
给‘xiaofeng’这个id增加'info:go'字段，并使用counter实现递增<br>
hbase(main):041:0&gt; incr 'member','xiaofeng','info:go'<br>
连续执行incr以上，COUNTER VALUE 的值会递增，通过get_counter</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;"><br>
获取当前count的值<br>
hbase(main):069:0&gt;get_counter 'member','xiaofeng','info:age' </p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">hbase(main):058:0&gt;get 'member','xiaofeng','info:go'  </p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">获取当前count的值<br>
hbase(main):069:0&gt;get_counter 'member','xiaofeng','info:age' </p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;"><br>
=============================相关删除=========================</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">1.<br>
删除一个列族：先将表disable（如果表中之前有数据的话，会把数据清空）<br>
hbase(main):030:0&gt; disable 'member'</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">hbase(main):033:0&gt; is_enabled 'member'</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">hbase(main):034:0&gt; alter 'member',{NAME=&gt;'m_id',METHOD=&gt;'delete'}</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">最后enable，否则不能进行其他操作<br>
hbase(main):038:0&gt; enable 'member'</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">2.<br>
删除整行<br>
hbase(main):001:0&gt;deleteall 'scores','xiaofeng'</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">3.<br>
将整张表清空：<br>
hbase(main):035:0&gt;truncate 'scores'<br>
hbase是先将掉disable掉，然后drop掉后重建表来实现truncate的功能</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">4.<br>
drop一个表（先disable，在drop）<br>
hbase(main):029:0&gt;disable 'scores'<br>
hbase(main):029:0&gt;drop 'scores'<br>
=====================基本操作===========================================</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">1.<br>
查询表是否存在<br>
hbase(main):021:0&gt;exists 'scores'</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">2.<br>
判断表是否enable<br>
hbase(main):034:0&gt;is_enabled 'scores'<br>
                                                                                       <br>
3.<br>
判断表是否disable<br>
hbase(main):032:0&gt;is_disabled 'scores'</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">4.<br>
列出所有的表<br>
hbase(main):028:0&gt;list</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">5.<br>
创建表member<br>
hbase(main):025:0&gt; create 'member','m_id','address','info'</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">6.<br>
查看表描述<br>
hbase(main):028:0&gt; describe 'member'</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">=================查询操作================================================<br>
1.scan '表名' #查看所有数据</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">2.get是根据主键查看一列<br>
get '表名','主键'[，'列簇名'][，'列簇名：列名']</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">=============================================================<br>
过滤查询表的内容<br>
1.FirstKeyOnlyFilter() 得到每一行的列族info的第一个列<br>
hbase(main):002:0&gt; scan 'student',{COLUMNS=&gt;'info',FILTER=&gt;"(FirstKeyOnlyFilter())"}</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">2.查看列簇是info，LIMIT是显示的行数，STARTROW是开始行对应的是主键<br>
hbase(main):008:0&gt; scan 'student',{COLUMNS=&gt;['info'],LIMIT=&gt;4,STARTROW=&gt;'200977100710'}</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">3.查看列簇是info，name<br>
hbase(main):007:0&gt; scan 'student',{COLUMNS=&gt;['info','name'],LIMIT=&gt;4,STARTROW=&gt;'200977100710'}</p>
<br>            </div>
                </div>