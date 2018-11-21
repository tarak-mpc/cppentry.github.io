---
layout:     post
title:      Hive集成HBase详解
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div id="post_detail" style="font-family:Verdana, Arial, Helvetica, sans-serif;line-height:18px;">
<div id="topics" style="min-height:200px;overflow:hidden;">
<div class="post">
<div class="postBody" style="line-height:1.5;font-size:13px;">
<div id="cnblogs_post_body">
<div><span style="font-size:18px;line-height:1.5;">Hive提供了与HBase的整合，使得能够在HBase表上使用HQL语句进行查询 插入操作以及进行Join和Union等复杂查询</span></div>
<div> </div>
<div>
<div><span><span style="line-height:1.5;font-size:18pt;">应用场景</span></span></div>
<div><span style="line-height:1.5;font-size:18px;"><span>1. 将ETL操作的数据存入HBase</span></span></div>
<div><img src="http://images2015.cnblogs.com/blog/776259/201607/776259-20160725161314981-225114891.png" alt="" style="border:0px;"></div>
<div><span><span style="line-height:1.5;font-size:18px;">2. HBase作为Hive的数据源</span></span></div>
<div><img src="http://images2015.cnblogs.com/blog/776259/201607/776259-20160725161331716-275437269.jpg" alt="" style="border:0px;"></div>
<div><span><span style="line-height:1.5;font-size:18px;">3. 构建低延时的数据仓库</span></span></div>
<div> </div>
</div>
<div><img src="http://images2015.cnblogs.com/blog/776259/201607/776259-20160725161339997-358332375.jpg" alt="" style="border:0px;"></div>
<div>
<div>
<div><span><span style="line-height:1.5;font-size:18pt;">使用</span></span></div>
<div><span style="line-height:1.5;font-size:14pt;">1.<span>从Hive中创建HBase表</span></span></div>
<div>
<ul><li style="list-style:disc;"><span><span style="line-height:1.5;font-size:18px;">使用HQL语句创建一个指向HBase的Hive表</span></span></li></ul></div>
<div>
<div class="cnblogs_code" style="border:1px solid rgb(204,204,204);overflow:auto;font-family:'Courier New' !important;font-size:12px !important;background-color:rgb(245,245,245);">
<pre style="font-family:'Courier New' !important;"><span style="line-height:1.5;color:rgb(0,0,255);">CREATE</span> <span style="line-height:1.5;color:rgb(0,0,255);">TABLE</span> hbase_table_1(<span style="line-height:1.5;color:rgb(0,0,255);">key</span> <span style="line-height:1.5;color:rgb(0,0,255);">int</span>, value string) <span style="line-height:1.5;color:rgb(128,128,128);">//</span><span style="line-height:1.5;">Hive中的表名hbase_table_1
STORED </span><span style="line-height:1.5;color:rgb(0,0,255);">BY</span> <span style="line-height:1.5;color:rgb(255,0,0);">'</span><span style="line-height:1.5;color:rgb(255,0,0);">org.apache.hadoop.hive.hbase.HBaseStorageHandler</span><span style="line-height:1.5;color:rgb(255,0,0);">'</span>  <span style="line-height:1.5;color:rgb(128,128,128);">//</span><span style="line-height:1.5;">指定存储处理器
</span><span style="line-height:1.5;color:rgb(0,0,255);">WITH</span> SERDEPROPERTIES ("hbase.columns.mapping" <span style="line-height:1.5;color:rgb(128,128,128);">=</span> ":<span style="line-height:1.5;color:rgb(0,0,255);">key</span>,cf1:val") <span style="line-height:1.5;color:rgb(128,128,128);">//</span><span style="line-height:1.5;">声明列族，列名
TBLPROPERTIES ("hbase.</span><span style="line-height:1.5;color:rgb(0,0,255);">table</span>.name" <span style="line-height:1.5;color:rgb(128,128,128);">=</span> "xyz", "hbase.mapred.output.outputtable" <span style="line-height:1.5;color:rgb(128,128,128);">=</span><span style="line-height:1.5;"> "xyz");  
</span><span style="line-height:1.5;color:rgb(128,128,128);">//</span>hbase.<span style="line-height:1.5;color:rgb(0,0,255);">table</span><span style="line-height:1.5;">.name声明HBase表名，为可选属性默认与Hive的表名相同，
</span><span style="line-height:1.5;color:rgb(128,128,128);">//</span>hbase.mapred.output.outputtable指定插入数据时写入的表，如果以后需要往该表插入数据就需要指定该值</pre>
</div>
</div>
<div>
<ul><li style="list-style:disc;"><span style="line-height:1.5;font-size:18px;"><span>通过HBase shell可以查看刚刚创建的HBase表的属性</span></span></li></ul></div>
<div>
<div class="cnblogs_code" style="border:1px solid rgb(204,204,204);overflow:auto;font-family:'Courier New' !important;font-size:12px !important;background-color:rgb(245,245,245);">
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy" style="line-height:1.5;"><a title="复制代码" style="color:rgb(7,93,179);text-decoration:underline;border:none !important;"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码" style="border:none !important;"></a></span></div>
<pre style="font-family:'Courier New' !important;"><span style="line-height:1.5;">$ hbase shell
HBase Shell; enter 'help</span><span style="line-height:1.5;color:rgb(0,0,255);">&lt;</span><span style="line-height:1.5;color:rgb(128,0,0);">RETURN</span><span style="line-height:1.5;color:rgb(0,0,255);">&gt;</span><span style="line-height:1.5;">' for list of supported commands.
Version: 0.20.3, r902334, Mon Jan 25 13:13:08 PST 2010
hbase(main):001:0&gt; list
xyz                                                                                                           
1 row(s) in 0.0530 seconds
hbase(main):002:0&gt; describe "xyz"
DESCRIPTION                                                           ENABLED                               
  {NAME =&gt; 'xyz', FAMILIES =&gt; [{NAME =&gt; 'cf1', COMPRESSION =&gt; 'NONE', VE true                                  
  RSIONS =&gt; '3', TTL =&gt; '2147483647', BLOCKSIZE =&gt; '65536', IN_MEMORY =&gt;                                       
  'false', BLOCKCACHE =&gt; 'true'}]}                                                                            
1 row(s) in 0.0220 seconds<br>
hbase(main):003:0&gt; scan "xyz"
ROW                          COLUMN+CELL                                                                      
0 row(s) in 0.0060 seconds</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy" style="line-height:1.5;"><a title="复制代码" style="color:rgb(7,93,179);text-decoration:underline;border:none !important;"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码" style="border:none !important;"></a></span></div>
</div>
</div>
<div>
<ul><li style="list-style:disc;"><span style="line-height:1.5;font-size:18px;"><span>使用HQL向HBase表中插入数据</span></span></li></ul></div>
<div>
<div class="cnblogs_code" style="border:1px solid rgb(204,204,204);overflow:auto;font-family:'Courier New' !important;font-size:12px !important;background-color:rgb(245,245,245);">
<pre style="font-family:'Courier New' !important;"><span style="line-height:1.5;color:rgb(0,0,255);">INSERT</span> OVERWRITE <span style="line-height:1.5;color:rgb(0,0,255);">TABLE</span> hbase_table_1 <span style="line-height:1.5;color:rgb(0,0,255);">SELECT</span> <span style="line-height:1.5;color:rgb(128,128,128);">*</span> <span style="line-height:1.5;color:rgb(0,0,255);">FROM</span> pokes <span style="line-height:1.5;color:rgb(0,0,255);">WHERE</span> foo<span style="line-height:1.5;color:rgb(128,128,128);">=</span><span style="line-height:1.5;color:rgb(128,0,0);"><strong>98</strong></span>;</pre>
</div>
</div>
<div>
<ul><li style="list-style:disc;"><span style="line-height:1.5;font-size:18px;"><span>在HBase端查看插入的数据</span></span></li></ul></div>
<div>
<div class="cnblogs_code" style="border:1px solid rgb(204,204,204);overflow:auto;font-family:'Courier New' !important;font-size:12px !important;background-color:rgb(245,245,245);">
<pre style="font-family:'Courier New' !important;">hbase(main):<span style="line-height:1.5;color:rgb(128,0,0);"><strong>009</strong></span>:<span style="line-height:1.5;color:rgb(128,0,0);"><strong>0</strong></span><span style="line-height:1.5;color:rgb(128,128,128);">&gt;</span><span style="line-height:1.5;"> scan "xyz"
ROW                          </span><span style="line-height:1.5;color:rgb(0,0,255);">COLUMN</span><span style="line-height:1.5;color:rgb(128,128,128);">+</span><span style="line-height:1.5;">CELL                                                                      
 </span><span style="line-height:1.5;color:rgb(128,0,0);"><strong>98</strong></span>                          <span style="line-height:1.5;color:rgb(0,0,255);">column</span><span style="line-height:1.5;color:rgb(128,128,128);">=</span>cf1:val, <span style="line-height:1.5;color:rgb(0,0,255);">timestamp</span><span style="line-height:1.5;color:rgb(128,128,128);">=</span><span style="line-height:1.5;color:rgb(128,0,0);"><strong>1267737987733</strong></span>, value<span style="line-height:1.5;color:rgb(128,128,128);">=</span><span style="line-height:1.5;">val_98                            
</span><span style="line-height:1.5;color:rgb(128,0,0);"><strong>1</strong></span> row(s) <span style="line-height:1.5;color:rgb(128,128,128);">in</span> <span style="line-height:1.5;color:rgb(128,0,0);"><strong>0.0110</strong></span> seconds</pre>
</div>
</div>
<div> </div>
<div><span style="line-height:1.5;font-size:14pt;"><span>2.从Hive中映射HBase</span></span></div>
<div>
<ul><li style="list-style:disc;"><span><span style="line-height:1.5;font-size:18px;">创建一个指向已经存在的HBase表的Hive表</span></span></li></ul></div>
<div>
<div class="cnblogs_code" style="border:1px solid rgb(204,204,204);overflow:auto;font-family:'Courier New' !important;font-size:12px !important;background-color:rgb(245,245,245);">
<pre style="font-family:'Courier New' !important;"><span style="line-height:1.5;color:rgb(0,0,255);">CREATE</span> EXTERNAL <span style="line-height:1.5;color:rgb(0,0,255);">TABLE</span> hbase_table_2(<span style="line-height:1.5;color:rgb(0,0,255);">key</span> <span style="line-height:1.5;color:rgb(0,0,255);">int</span><span style="line-height:1.5;">, value string) 
STORED </span><span style="line-height:1.5;color:rgb(0,0,255);">BY</span> <span style="line-height:1.5;color:rgb(255,0,0);">'</span><span style="line-height:1.5;color:rgb(255,0,0);">org.apache.hadoop.hive.hbase.HBaseStorageHandler</span><span style="line-height:1.5;color:rgb(255,0,0);">'</span>
<span style="line-height:1.5;color:rgb(0,0,255);">WITH</span> SERDEPROPERTIES ("hbase.columns.mapping" <span style="line-height:1.5;color:rgb(128,128,128);">=</span><span style="line-height:1.5;"> "cf1:val")
TBLPROPERTIES("hbase.</span><span style="line-height:1.5;color:rgb(0,0,255);">table</span>.name" <span style="line-height:1.5;color:rgb(128,128,128);">=</span> "some_existing_table", "hbase.mapred.output.outputtable" <span style="line-height:1.5;color:rgb(128,128,128);">=</span> "some_existing_table");</pre>
</div>
</div>
<div><span style="line-height:1.5;color:rgb(255,0,0);">该Hive表一个外部表，所以删除该表并不会删除HBase表中的数据</span></div>
<div><span style="line-height:1.5;color:rgb(255,0,0);"><span><span style="line-height:1.5;font-size:14pt;">注意</span></span></span></div>
<ol><li style="list-style:decimal;">建表或映射表的时候如果没有指定:key则第一个列默认就是行键</li><li style="list-style:decimal;">HBase对应的Hive表中没有时间戳概念，默认返回的就是最新版本的值</li><li style="list-style:decimal;">由于HBase中没有数据类型信息，所以在存储数据的时候都转化为String类型</li></ol><div><span style="line-height:1.5;font-size:14pt;"><span>3.多列及多列族的映射</span></span></div>
<div>如下表：value1和value2来自列族a对应的b c列，value3来自列族d对应的列</div>
<div>
<div class="cnblogs_code" style="border:1px solid rgb(204,204,204);overflow:auto;font-family:'Courier New' !important;font-size:12px !important;background-color:rgb(245,245,245);">
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy" style="line-height:1.5;"><a title="复制代码" style="color:rgb(7,93,179);text-decoration:underline;border:none !important;"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码" style="border:none !important;"></a></span></div>
<pre style="font-family:'Courier New' !important;"><span style="line-height:1.5;color:rgb(0,0,255);">CREATE</span> <span style="line-height:1.5;color:rgb(0,0,255);">TABLE</span> hbase_table_1(<span style="line-height:1.5;color:rgb(0,0,255);">key</span> <span style="line-height:1.5;color:rgb(0,0,255);">int</span>, value1 string, value2 <span style="line-height:1.5;color:rgb(0,0,255);">int</span>, value3 <span style="line-height:1.5;color:rgb(0,0,255);">int</span><span style="line-height:1.5;">) 
STORED </span><span style="line-height:1.5;color:rgb(0,0,255);">BY</span> <span style="line-height:1.5;color:rgb(255,0,0);">'</span><span style="line-height:1.5;color:rgb(255,0,0);">org.apache.hadoop.hive.hbase.HBaseStorageHandler</span><span style="line-height:1.5;color:rgb(255,0,0);">'</span>
<span style="line-height:1.5;color:rgb(0,0,255);">WITH</span><span style="line-height:1.5;"> SERDEPROPERTIES (
"hbase.columns.mapping" </span><span style="line-height:1.5;color:rgb(128,128,128);">=</span> ":<span style="line-height:1.5;color:rgb(0,0,255);">key</span><span style="line-height:1.5;">,a:b,a:c,d:e"
);
</span><span style="line-height:1.5;color:rgb(0,0,255);">INSERT</span> OVERWRITE <span style="line-height:1.5;color:rgb(0,0,255);">TABLE</span> hbase_table_1 <span style="line-height:1.5;color:rgb(0,0,255);">SELECT</span> foo, bar, foo<span style="line-height:1.5;color:rgb(128,128,128);">+</span><span style="line-height:1.5;color:rgb(128,0,0);"><strong>1</strong></span>, foo<span style="line-height:1.5;color:rgb(128,128,128);">+</span><span style="line-height:1.5;color:rgb(128,0,0);"><strong>2</strong></span> 
<span style="line-height:1.5;color:rgb(0,0,255);">FROM</span> pokes <span style="line-height:1.5;color:rgb(0,0,255);">WHERE</span> foo<span style="line-height:1.5;color:rgb(128,128,128);">=</span><span style="line-height:1.5;color:rgb(128,0,0);"><strong>98</strong></span> <span style="line-height:1.5;color:rgb(128,128,128);">OR</span> foo<span style="line-height:1.5;color:rgb(128,128,128);">=</span><span style="line-height:1.5;color:rgb(128,0,0);"><strong>100</strong></span>;</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy" style="line-height:1.5;"><a title="复制代码" style="color:rgb(7,93,179);text-decoration:underline;border:none !important;"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码" style="border:none !important;"></a></span></div>
</div>
</div>
<div> </div>
<div><span><span style="line-height:1.5;font-size:18px;">4.Hive Map类型在HBase中的映射规则</span></span></div>
<div>如下表:通过Hive的Map数据类型映射HBase表，这样每行都可以有不同的列组合，列名与map中的key对应，列值与map中的value对应</div>
<div>
<div class="cnblogs_code" style="border:1px solid rgb(204,204,204);overflow:auto;font-family:'Courier New' !important;font-size:12px !important;background-color:rgb(245,245,245);">
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy" style="line-height:1.5;"><a title="复制代码" style="color:rgb(7,93,179);text-decoration:underline;border:none !important;"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码" style="border:none !important;"></a></span></div>
<pre style="font-family:'Courier New' !important;"><span style="line-height:1.5;color:rgb(0,0,255);">CREATE</span> <span style="line-height:1.5;color:rgb(0,0,255);">TABLE</span> hbase_table_1(value map<span style="line-height:1.5;color:rgb(128,128,128);">&lt;</span>string,<span style="line-height:1.5;color:rgb(0,0,255);">int</span><span style="line-height:1.5;color:rgb(128,128,128);">&gt;</span>, row_key <span style="line-height:1.5;color:rgb(0,0,255);">int</span><span style="line-height:1.5;">) 
STORED </span><span style="line-height:1.5;color:rgb(0,0,255);">BY</span> <span style="line-height:1.5;color:rgb(255,0,0);">'</span><span style="line-height:1.5;color:rgb(255,0,0);">org.apache.hadoop.hive.hbase.HBaseStorageHandler</span><span style="line-height:1.5;color:rgb(255,0,0);">'</span>
<span style="line-height:1.5;color:rgb(0,0,255);">WITH</span><span style="line-height:1.5;"> SERDEPROPERTIES (
"hbase.columns.mapping" </span><span style="line-height:1.5;color:rgb(128,128,128);">=</span> "cf:,:<span style="line-height:1.5;color:rgb(0,0,255);">key</span><span style="line-height:1.5;">"
);
</span><span style="line-height:1.5;color:rgb(0,0,255);">INSERT</span> OVERWRITE <span style="line-height:1.5;color:rgb(0,0,255);">TABLE</span> hbase_table_1 <span style="line-height:1.5;color:rgb(0,0,255);">SELECT</span> map(bar, foo), foo <span style="line-height:1.5;color:rgb(0,0,255);">FROM</span><span style="line-height:1.5;"> pokes 
</span><span style="line-height:1.5;color:rgb(0,0,255);">WHERE</span> foo<span style="line-height:1.5;color:rgb(128,128,128);">=</span><span style="line-height:1.5;color:rgb(128,0,0);"><strong>98</strong></span> <span style="line-height:1.5;color:rgb(128,128,128);">OR</span> foo<span style="line-height:1.5;color:rgb(128,128,128);">=</span><span style="line-height:1.5;color:rgb(128,0,0);"><strong>100</strong></span>;</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy" style="line-height:1.5;"><a title="复制代码" style="color:rgb(7,93,179);text-decoration:underline;border:none !important;"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码" style="border:none !important;"></a></span></div>
</div>
</div>
<div>cf为列族，其列名对应map中的bar,列值对应map中的foo</div>
<div>
<ul><li style="list-style:disc;"><span style="line-height:1.5;font-size:18px;"><span>在HBase下查看数据</span></span></li></ul></div>
<div>
<div class="cnblogs_code" style="border:1px solid rgb(204,204,204);overflow:auto;font-family:'Courier New' !important;font-size:12px !important;background-color:rgb(245,245,245);">
<pre style="font-family:'Courier New' !important;">hbase(main):<span style="line-height:1.5;color:rgb(128,0,0);"><strong>012</strong></span>:<span style="line-height:1.5;color:rgb(128,0,0);"><strong>0</strong></span><span style="line-height:1.5;color:rgb(128,128,128);">&gt;</span><span style="line-height:1.5;"> scan "hbase_table_1"
ROW                          </span><span style="line-height:1.5;color:rgb(0,0,255);">COLUMN</span><span style="line-height:1.5;color:rgb(128,128,128);">+</span><span style="line-height:1.5;">CELL                                                                      
 </span><span style="line-height:1.5;color:rgb(128,0,0);"><strong>100</strong></span>                         <span style="line-height:1.5;color:rgb(0,0,255);">column</span><span style="line-height:1.5;color:rgb(128,128,128);">=</span>cf:val_100, <span style="line-height:1.5;color:rgb(0,0,255);">timestamp</span><span style="line-height:1.5;color:rgb(128,128,128);">=</span><span style="line-height:1.5;color:rgb(128,0,0);"><strong>1267739509194</strong></span>, value<span style="line-height:1.5;color:rgb(128,128,128);">=</span><span style="line-height:1.5;color:rgb(128,0,0);"><strong>100</strong></span>                            
 <span style="line-height:1.5;color:rgb(128,0,0);"><strong>98</strong></span>                          <span style="line-height:1.5;color:rgb(0,0,255);">column</span><span style="line-height:1.5;color:rgb(128,128,128);">=</span>cf:val_98, <span style="line-height:1.5;color:rgb(0,0,255);">timestamp</span><span style="line-height:1.5;color:rgb(128,128,128);">=</span><span style="line-height:1.5;color:rgb(128,0,0);"><strong>1267739509194</strong></span>, value<span style="line-height:1.5;color:rgb(128,128,128);">=</span><span style="line-height:1.5;color:rgb(128,0,0);"><strong>98</strong></span>                              
<span style="line-height:1.5;color:rgb(128,0,0);"><strong>2</strong></span> row(s) <span style="line-height:1.5;color:rgb(128,128,128);">in</span> <span style="line-height:1.5;color:rgb(128,0,0);"><strong>0.0080</strong></span> seconds</pre>
</div>
</div>
</div>
<div>
<div>
<ul><li style="list-style:disc;"><span style="line-height:1.5;font-size:18px;"><span>在Hive下查看数据</span></span></li></ul></div>
<div>
<div class="cnblogs_code" style="border:1px solid rgb(204,204,204);overflow:auto;font-family:'Courier New' !important;font-size:12px !important;background-color:rgb(245,245,245);">
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy" style="line-height:1.5;"><a title="复制代码" style="color:rgb(7,93,179);text-decoration:underline;border:none !important;"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码" style="border:none !important;"></a></span></div>
<pre style="font-family:'Courier New' !important;">hive<span style="line-height:1.5;color:rgb(128,128,128);">&gt;</span> <span style="line-height:1.5;color:rgb(0,0,255);">select</span> <span style="line-height:1.5;color:rgb(128,128,128);">*</span> <span style="line-height:1.5;color:rgb(0,0,255);">from</span><span style="line-height:1.5;"> hbase_table_1;
Total MapReduce jobs </span><span style="line-height:1.5;color:rgb(128,128,128);">=</span> <span style="line-height:1.5;color:rgb(128,0,0);"><strong>1</strong></span><span style="line-height:1.5;">
Launching Job </span><span style="line-height:1.5;color:rgb(128,0,0);"><strong>1</strong></span> out <span style="line-height:1.5;color:rgb(0,0,255);">of</span> <span style="line-height:1.5;color:rgb(128,0,0);"><strong>1</strong></span><span style="line-height:1.5;">
...
OK
{"val_100":</span><span style="line-height:1.5;color:rgb(128,0,0);"><strong>100</strong></span>}    <span style="line-height:1.5;color:rgb(128,0,0);"><strong>100</strong></span><span style="line-height:1.5;">
{"val_98":</span><span style="line-height:1.5;color:rgb(128,0,0);"><strong>98</strong></span>}    <span style="line-height:1.5;color:rgb(128,0,0);"><strong>98</strong></span><span style="line-height:1.5;">
Time taken: </span><span style="line-height:1.5;color:rgb(128,0,0);"><strong>3.808</strong></span> seconds</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy" style="line-height:1.5;"><a title="复制代码" style="color:rgb(7,93,179);text-decoration:underline;border:none !important;"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码" style="border:none !important;"></a></span></div>
</div>
</div>
<div><span style="line-height:1.5;color:rgb(255,0,0);">注意:由于map中的key是作为HBase的列名使用的，所以map中的key类型必须为String类型</span></div>
<div> </div>
<div><span style="line-height:1.5;font-size:18px;"><span>以下映射语句都会报错</span></span></div>
<div><span>1.</span></div>
<div>
<div class="cnblogs_code" style="border:1px solid rgb(204,204,204);overflow:auto;font-family:'Courier New' !important;font-size:12px !important;background-color:rgb(245,245,245);">
<pre style="font-family:'Courier New' !important;"><span style="line-height:1.5;color:rgb(0,0,255);">CREATE</span> <span style="line-height:1.5;color:rgb(0,0,255);">TABLE</span> hbase_table_1(<span style="line-height:1.5;color:rgb(0,0,255);">key</span> <span style="line-height:1.5;color:rgb(0,0,255);">int</span>, value map<span style="line-height:1.5;color:rgb(128,128,128);">&lt;</span><span style="line-height:1.5;color:rgb(0,0,255);">int</span>,<span style="line-height:1.5;color:rgb(0,0,255);">int</span><span style="line-height:1.5;color:rgb(128,128,128);">&gt;</span><span style="line-height:1.5;">) 
STORED </span><span style="line-height:1.5;color:rgb(0,0,255);">BY</span> <span style="line-height:1.5;color:rgb(255,0,0);">'</span><span style="line-height:1.5;color:rgb(255,0,0);">org.apache.hadoop.hive.hbase.HBaseStorageHandler</span><span style="line-height:1.5;color:rgb(255,0,0);">'</span>
<span style="line-height:1.5;color:rgb(0,0,255);">WITH</span><span style="line-height:1.5;"> SERDEPROPERTIES (
"hbase.columns.mapping" </span><span style="line-height:1.5;color:rgb(128,128,128);">=</span> ":<span style="line-height:1.5;color:rgb(0,0,255);">key</span><span style="line-height:1.5;">,cf:"
);</span></pre>
</div>
</div>
<div>原因：map中的key必须是String</div>
<div> </div>
<div><span>2.</span></div>
<div>
<div class="cnblogs_code" style="border:1px solid rgb(204,204,204);overflow:auto;font-family:'Courier New' !important;font-size:12px !important;background-color:rgb(245,245,245);">
<pre style="font-family:'Courier New' !important;"><span style="line-height:1.5;color:rgb(0,0,255);">CREATE</span> <span style="line-height:1.5;color:rgb(0,0,255);">TABLE</span> hbase_table_1(<span style="line-height:1.5;color:rgb(0,0,255);">key</span> <span style="line-height:1.5;color:rgb(0,0,255);">int</span><span style="line-height:1.5;">, value string) 
STORED </span><span style="line-height:1.5;color:rgb(0,0,255);">BY</span> <span style="line-height:1.5;color:rgb(255,0,0);">'</span><span style="line-height:1.5;color:rgb(255,0,0);">org.apache.hadoop.hive.hbase.HBaseStorageHandler</span><span style="line-height:1.5;color:rgb(255,0,0);">'</span>
<span style="line-height:1.5;color:rgb(0,0,255);">WITH</span><span style="line-height:1.5;"> SERDEPROPERTIES (
"hbase.columns.mapping" </span><span style="line-height:1.5;color:rgb(128,128,128);">=</span> ":<span style="line-height:1.5;color:rgb(0,0,255);">key</span><span style="line-height:1.5;">,cf:"
);</span></pre>
</div>
</div>
<div>原因：当hbase.columns.mapping中的列族后面为空时(形如cf:),说明在Hive中其对应的数据类型为map，而这条语句中对应的是String所以报错</div>
<div> </div>
<div><span style="line-height:1.5;font-size:18pt;"><span>5.Hive还支持简单的复合行键</span></span></div>
<div>如下：创建一张指向HBase的Hive表，行键有两个字段，字段之间使用~分隔</div>
<div>
<div class="cnblogs_code" style="border:1px solid rgb(204,204,204);overflow:auto;font-family:'Courier New' !important;font-size:12px !important;background-color:rgb(245,245,245);">
<pre style="font-family:'Courier New' !important;"><span style="line-height:1.5;color:rgb(0,0,255);">CREATE</span> EXTERNAL <span style="line-height:1.5;color:rgb(0,0,255);">TABLE</span> delimited_example(<span style="line-height:1.5;color:rgb(0,0,255);">key</span> struct<span style="line-height:1.5;color:rgb(128,128,128);">&lt;</span>f1:string, f2:string<span style="line-height:1.5;color:rgb(128,128,128);">&gt;</span><span style="line-height:1.5;">, value string) 
ROW FORMAT DELIMITED 
COLLECTION ITEMS TERMINATED </span><span style="line-height:1.5;color:rgb(0,0,255);">BY</span> <span style="line-height:1.5;color:rgb(255,0,0);">'</span><span style="line-height:1.5;color:rgb(255,0,0);">~</span><span style="line-height:1.5;color:rgb(255,0,0);">'</span><span style="line-height:1.5;"> 
STORED </span><span style="line-height:1.5;color:rgb(0,0,255);">BY</span> <span style="line-height:1.5;color:rgb(255,0,0);">'</span><span style="line-height:1.5;color:rgb(255,0,0);">org.apache.hadoop.hive.hbase.HBaseStorageHandler</span><span style="line-height:1.5;color:rgb(255,0,0);">'</span> 
<span style="line-height:1.5;color:rgb(0,0,255);">WITH</span><span style="line-height:1.5;"> SERDEPROPERTIES (
  </span><span style="line-height:1.5;color:rgb(255,0,0);">'</span><span style="line-height:1.5;color:rgb(255,0,0);">hbase.columns.mapping</span><span style="line-height:1.5;color:rgb(255,0,0);">'</span><span style="line-height:1.5;color:rgb(128,128,128);">=</span><span style="line-height:1.5;color:rgb(255,0,0);">'</span><span style="line-height:1.5;color:rgb(255,0,0);">:key,f:c1</span><span style="line-height:1.5;color:rgb(255,0,0);">'</span>);</pre>
</div>
</div>
<pre></pre>
</div>
<div><span><span style="line-height:1.5;font-size:18pt;">6.使用Hive集成HBase表的需注意</span></span></div>
<ol><li style="list-style:decimal;">对HBase表进行预分区，增大其MapReduce作业的并行度</li><li style="list-style:decimal;">合理的设计rowkey使其尽可能的分布在预先分区好的Region上</li><li style="list-style:decimal;">通过set hbase.client.scanner.caching设置合理的扫描缓存</li></ol><p style="line-height:1.5;">
 </p>
<p style="line-height:1.5;">
参考资料：</p>
<p style="line-height:1.5;">
<a href="https://cwiki.apache.org/confluence/display/Hive/HBaseIntegration" rel="nofollow" style="color:rgb(7,93,179);"><span style="line-height:1.5;font-size:15px;">Hive HBase Integration</span></a></p>
<p style="line-height:1.5;">
 </p>
<div> </div>
 </div>
<div> </div>
</div>
</div>
</div>
</div>
</div>
            </div>
                </div>