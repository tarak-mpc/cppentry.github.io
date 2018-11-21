---
layout:     post
title:      Hbase、Hive、Impala数据同步简单示例
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1><strong>Hbase +hive+impala数据同步简单示例</strong></h1>

<p>首先先在hbase创建表</p>

<p><strong>hbase</strong></p>

<p>hbase shell</p>

<p>list</p>

<p>scan 'TEST_INFO'</p>

<p>create 'TEST_INFO','d'</p>

<p>put ‘TEST_INFO’,’001’,’fields’,’VALUE’,</p>

<p>scan 'TEST_INFO'</p>

<h1><a name="t1"></a><strong>Hive</strong></h1>

<p>hive创建外部表</p>

<p> </p>

<p>show databases;</p>

<p>show tables;</p>

<p>CREATE EXTERNAL TABLE TEST_INFO(</p>

<p> id string,field12 string,field13 string,field14 string, field15 string,field16 string, field17 string, field18 string, field19 string,field20 string,field21 string,field22 string,field23 string,field24 string,field25 string, field26 string,field27 string,field28 string, field29 string,field30 string, field31 string, field32 string,field33 string,field34 string,field35 string)</p>

<p>ROW FORMAT SERDE 'org.apache.hadoop.hive.hbase.HBaseSerDe'</p>

<p>STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'</p>

<p>WITH SERDEPROPERTIES ('serialization.format'='\t','hbase.columns.mapping'=':key, d:field12,d:field13,d:field14, d:field15, d:field16,d:field17, d:field18,d:field19, d:field20,d:field21, d:field22,d:field23, d:field24,d:field25, d:field26,d:field27, d:field28,d:field29, d:field30,d:field31, d:field32,d:field33, d:field34, d:field35','field.delim'='\t')</p>

<p>TBLPROPERTIES('hbase.table.name'='TEST_INFO');</p>

<p> </p>

<p>hive&gt; CREATE EXTERNAL TABLE TEST_INFO(</p>

<p>    &gt;  id string,field12 string,field13 string,field14 string, field15 string,field16 string, field17 string, field18 string, field19 string,field20 string,field21 string,field22 string,field23 string,field24 string,field25 string, field26 string,field27 string,field28 string, field29 string,field30 string, field31 string, field32 string,field33 string,field34 string,field35 string)</p>

<p>    &gt; ROW FORMAT SERDE 'org.apache.hadoop.hive.hbase.HBaseSerDe'</p>

<p>    &gt; STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'</p>

<p>    &gt; WITH SERDEPROPERTIES ('serialization.format'='\t','hbase.columns.mapping'=':key, d:field12,d:field13,d:field14, d:field15, d:field16,d:field17, d:field18,d:field19, d:field20,d:field21, d:field22,d:field23, d:field24,d:field25, d:field26,d:field27, d:field28,d:field29, d:field30,d:field31, d:field32,d:field33, d:field34, d:field35','field.delim'='\t')</p>

<p>    &gt; TBLPROPERTIES('hbase.table.name'='TEST_INFO');</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180414211646981?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1dZcGVyc2lzdA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p>hive&gt; desc test_info;</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180414211737342?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1dZcGVyc2lzdA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p>hive&gt;select * from test_info;</p>

<h1><a name="t2"></a><strong>impala</strong></h1>

<p>impala-shell</p>

<p>#同步hive 到ipmala</p>

<p>INVALIDATE METADATA; </p>

<p>[cdh002:21000] &gt; desc test_info;</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180414211812106?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1dZcGVyc2lzdA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p> #查看到映射HBase中表的结构：</p>

<p>[cdh002:21000] &gt; select * from test_info;</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180414211824348?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1dZcGVyc2lzdA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>            </div>
                </div>