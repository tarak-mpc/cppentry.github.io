---
layout:     post
title:      hbase shell 命令一
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<pre><span style="font-family:Arial;font-size:12px;background-color:rgb(255,255,255);"></span></pre><p>创建表</p><p>hbase(main):007:0&gt; create 'users','user_id','address','info'
0 row(s) in 0.4600 seconds</p><p>=&gt; Hbase::Table - users</p><p> </p><p>列出全部表
hbase(main):008:0&gt; list
TABLE                                                                           
users                                                                           
1 row(s) in 0.0290 seconds</p><p>=&gt; ["users"]
</p><p> </p><p>得到表的描述
hbase(main):009:0&gt; describe 'users'</p><p> </p><p>删除表
hbase(main):011:0&gt; disable'users'
0 row(s) in 1.3970 seconds</p><p>hbase(main):012:0&gt; drop 'users'
0 row(s) in 0.2200 seconds</p><p> </p><p>添加记录
put 'users','xiaoming','info:age','24'
0 row(s) in 0.0300 seconds</p><p>hbase(main):020:0&gt; put 'users','xiaoming','info:birthday','1987-06-17'
0 row(s) in 0.0140 seconds</p><p>hbase(main):021:0&gt; put 'users','xiaoming','info:company','alibaba'
0 row(s) in 0.0200 seconds</p><p> </p><p>取得一个rowkey的所有数据
hbase(main):036:0&gt; get 'users', 'xiaoming'
COLUMN                CELL                                                      
 adress:city          timestamp=1394939832037, value=jinan                      
 adress:contry        timestamp=1394939760861, value=china                      
 adress:province      timestamp=1394939799221, value=shandong                   
 info:age             timestamp=1394939511010, value=24                         
 info:birthday        timestamp=1394939561569, value=1987-06-17                 
 info:company         timestamp=1394939601488, value=alibaba                    
 6 row(s) in 0.0980 seconds</p><p>
取得一个rowkey，一个列族中的所有数据
hbase(main):038:0&gt; get 'users','xiaoming','info'
COLUMN                CELL                                                      
 info:age             timestamp=1394939511010, value=24                         
 info:birthday        timestamp=1394939561569, value=1987-06-17                 
 info:company         timestamp=1394939601488, value=alibaba                    
3 row(s) in 0.0140 seconds</p><p>
取得一个rowkey，一个列族中一个列的所有数据
hbase(main):037:0&gt; get 'users','xiaoming','info:age'
COLUMN                CELL                                                      
 info:age             timestamp=1394939511010, value=24                         
1 row(s) in 0.0210 seconds</p><p>
根据时间戳获取单元格的数据
hbase(main):017:0&gt; get 'users','xiaoming',{COLUMN=&gt;'info:age',TIMESTAMP=&gt;1394941032519}
COLUMN                CELL                                                      
 info:age             timestamp=1394941032519, value=25                         
1 row(s) in 0.0250 seconds</p><p> </p><p>全表扫描
scan user</p><p>
删除小明的info：age字段
hbase(main):022:0&gt; delete 'users','xiaoming','info:age'
0 row(s) in 0.1470 seconds</p><p>hbase(main):023:0&gt; get 'users','xiaoming'
COLUMN                CELL                                                      
 adress:city          timestamp=1394939832037, value=jinan                      
 adress:contry        timestamp=1394939760861, value=china                      
 adress:province      timestamp=1394939799221, value=shandong                   
 info:birthday        timestamp=1394939561569, value=1987-06-17                 
 info:company         timestamp=1394939601488, value=alibaba                    
5 row(s) in 0.0200 seconds</p><p>
删除整行
deleteall 'users','xiaoming'</p><p>统计行数
count 'users'</p><p>清空表
truncate 'users'
</p>
            </div>
                </div>