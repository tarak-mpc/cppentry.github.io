---
layout:     post
title:      HBase常用shell命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：有不正确的地方，还望各位指正指导，Thanks！					https://blog.csdn.net/a544258023/article/details/79616753				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p><strong><span style="font-family:SimSun;">1. Group name: general</span></strong></p><p><span style="font-size:12px;">        1) status: 查看hbase中master状态</span></p><pre><code class="language-plain">hbase(main):004:0&gt; status
1 active master, 0 backup masters, 1 servers, 0 dead, 27.0000 average load</code></pre><p><span style="font-size:12px;">        2) version: 查看hbase版本<br></span></p><pre><code class="language-plain">hbase(main):005:0&gt; version
1.3.0, re359c76e8d9fd0d67396456f92bcbad9ecd7a710, Tue Jan  3 05:31:38 MSK 2017</code></pre><p><span style="font-size:12px;">        3) whoami: 查看当前用户<br></span></p><pre><code class="language-plain">hbase(main):006:0&gt; whoami
llh (auth:SIMPLE)
    groups: llh, adm, cdrom, sudo, dip, plugdev, lpadmin, sambashare</code></pre><p><span style="font-family:SimSun;"><strong>2. Group name: namespace</strong></span></p><p><span style="font-size:12px;font-family:SimSun;">        1)  </span><span style="font-size:12px;font-family:SimSun;">create_namespace: 创建命名空间</span></p><pre><code class="language-plain">hbase(main):010:0&gt; create_namespace 'test'
0 row(s) in 1.0160 seconds</code></pre><p><span style="font-family:SimSun;"><span style="font-size:12px;">        2) drop_namespace: 删除命名空间<br></span></span></p><pre><code class="language-plain">hbase(main):011:0&gt; drop_namespace 'test'
0 row(s) in 0.8820 seconds</code></pre><p><span style="font-family:SimSun;"><span style="font-size:12px;">        3) list_namespace: 显示所有命名空间<br></span></span></p><pre><code class="language-plain">hbase(main):012:0&gt; list_namespace
NAMESPACE                                                                       
default                                                                         
hbase                                                                           
2 row(s) in 0.0080 seconds</code></pre><p><span style="font-family:SimSun;"><span style="font-size:12px;">        4) describe_namespace: 显示单个命名空间的详细信息<br></span></span></p><pre><code class="language-plain">hbase(main):013:0&gt; describe_namespace 'hbase'
DESCRIPTION                                                                     
{NAME =&gt; 'hbase'}                                                               
1 row(s) in 0.0060 seconds</code></pre><p><span style="font-family:SimSun;"><strong>3. Group name: ddl</strong></span></p><p><span style="font-size:12px;font-family:SimSun;">        1) create: 创建单个表</span></p><pre><code class="language-plain">hbase(main):002:0&gt; create 'tb','cf1'
0 row(s) in 1.3180 seconds

=&gt; Hbase::Table - tb
hbase(main):005:0&gt; create 'test:tb1','cf1'
0 row(s) in 1.2320 seconds

=&gt; Hbase::Table - test:tb1</code></pre><p><span style="font-size:12px;font-family:SimSun;">        2) list: 显示所有表<br></span></p><pre><code class="language-plain">hbase(main):009:0&gt; list
TABLE                                                                                                                                                                                                                     
test:tb1                                                                        
tb                                                                    
2 row(s) in 0.0420 seconds

=&gt; ["tb", "test:tb1"]</code></pre><p><span style="font-size:12px;font-family:SimSun;">        3) describe: 显示单个表的详细信息<br></span></p><pre><code class="language-plain">hbase(main):010:0&gt; describe 'test:tb1'
Table test:tb1 is ENABLED                                                       
test:tb1                                                                        
COLUMN FAMILIES DESCRIPTION                                                     
{NAME =&gt; 'cf1', BLOOMFILTER =&gt; 'ROW', VERSIONS =&gt; '1', IN_MEMORY =&gt; 'false', KEE
P_DELETED_CELLS =&gt; 'FALSE', DATA_BLOCK_ENCODING =&gt; 'NONE', TTL =&gt; 'FOREVER', COM
PRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', BLOCKCACHE =&gt; 'true', BLOCKSIZE =&gt; '655
36', REPLICATION_SCOPE =&gt; '0'}                                                  
1 row(s) in 0.1440 seconds</code></pre><p><span style="font-size:12px;font-family:SimSun;">        4) disable: 关闭单个表<br></span></p><pre><code class="language-plain">hbase(main):011:0&gt; disable 'test:tb1'
0 row(s) in 2.2720 seconds</code></pre><p><span style="font-size:12px;font-family:SimSun;">        5) enable: 开启单个表 <br></span></p><pre><code class="language-plain">hbase(main):012:0&gt; enable 'test:tb1'
0 row(s) in 1.2620 seconds</code></pre><p><span style="font-size:12px;font-family:SimSun;">        6) drop: 删除单个表<br></span></p><pre><code class="language-plain">hbase(main):014:0&gt; disable 'tb'
0 row(s) in 2.2450 seconds

hbase(main):015:0&gt; drop 'tb'  
0 row(s) in 1.2630 seconds</code></pre><p><span style="font-size:12px;font-family:SimSun;">        7) alter: 修改表结构<br></span></p><pre><code class="language-plain">hbase(main):016:0&gt; alter 'test:tb1', NAME=&gt;'cf1', TTL=&gt;'3600'       //修改列簇为cf1数据保留两个小时
Updating all regions with the new schema...
1/1 regions updated.
Done.
0 row(s) in 1.9330 seconds

hbase(main):017:0&gt; alter 'test:tb1', NAME=&gt;'cf2', VERSIONS=&gt;5       //改变或添加一个列簇
Updating all regions with the new schema...
1/1 regions updated.
Done.
0 row(s) in 1.8890 seconds

hbase(main):019:0&gt; alter 'test:tb1', NAME=&gt;'cf2', METHOD=&gt;'delete'  //删除一个列簇
Updating all regions with the new schema...
1/1 regions updated.
Done.
0 row(s) in 2.1470 seconds</code></pre><p><span style="font-size:12px;font-family:SimSun;">        8) alter_async: 异步执行alter<br></span></p><p><span style="font-size:12px;font-family:SimSun;">        9) alter_status: 查看alter执行的状态<br></span></p><pre><code class="language-plain">hbase(main):026:0&gt; alter_status 'test:tb1'
1/1 regions updated.
Done.</code></pre><p><span style="font-size:12px;font-family:SimSun;">        10) is_enabled: 验证指定表是否有效<br></span></p><pre><code class="language-plain">hbase(main):020:0&gt; is_enabled 'test:tb1'
true                                                                            
0 row(s) in 0.0150 seconds</code></pre><p><span style="font-size:12px;font-family:SimSun;">        11) exists: 查看指定表是否存在<br></span></p><pre><code class="language-plain">hbase(main):021:0&gt; exists 'test:tb1'
Table test:tb1 does exist                                                       
0 row(s) in 0.0080 seconds</code></pre><p><span style="font-size:12px;font-family:SimSun;">        12) locate_region: 查看region分布情况<br></span></p><pre><code class="language-plain">hbase(main):022:0&gt; locate_region 'test:tb1','cf1'
HOST                  REGION                                                    
 ebi-llh:16020        {ENCODED =&gt; 5f4d2d31db8eaaeb39cfd978b0234054, NAME =&gt; 'tes
                      t:tb1,,1521473386612.5f4d2d31db8eaaeb39cfd978b0234054.', S
                      TARTKEY =&gt; '', ENDKEY =&gt; ''}                              
1 row(s) in 0.0250 seconds</code></pre><p><span style="font-size:12px;font-family:SimSun;">        13) show_filters: 显示所有过滤器(*)<br></span></p><pre><code class="language-plain">hbase(main):023:0&gt; show_filters
DependentColumnFilter                                                           
KeyOnlyFilter                                                                   
ColumnCountGetFilter</code></pre><p><span style="font-family:SimSun;"><strong>4. Group name: dml</strong></span></p><p><span style="font-family:SimSun;"><span style="font-size:12px;">        1) put: 插入或更新数据</span><br></span></p><pre><code class="language-plain">hbase(main):001:0&gt; put 'test:tb1','10001','cf1:name','jack'
0 row(s) in 0.3390 seconds</code></pre><p><span style="font-family:SimSun;"><span style="font-size:12px;">        2) delete: 删除单条数据<br></span></span></p><pre><code class="language-plain">hbase(main):002:0&gt; delete 'test:tb1','10001','cf1:name'
0 row(s) in 0.0240 seconds</code></pre><p><span style="font-family:SimSun;"><span style="font-size:12px;">        3) deleteall: 删除多条数据<br></span></span></p><pre><code class="language-plain">hbase(main):007:0&gt; deleteall 'test:tb1','10001'
0 row(s) in 0.0070 seconds</code></pre><p><span style="font-family:SimSun;"><span style="font-size:12px;">        4) get: 获取数据</span></span></p><pre><code class="language-plain">hbase(main):005:0&gt; get 'test:tb1','10001'
COLUMN                CELL                                                      
 cf1:age              timestamp=1521942283487, value=22                         
 cf1:name             timestamp=1521942276491, value=Jack                       
1 row(s) in 0.0160 seconds

hbase(main):006:0&gt; get 'test:tb1','10001','cf1:age'
COLUMN                CELL                                                      
 cf1:age              timestamp=1521942283487, value=22                         
1 row(s) in 0.0130 seconds

hbase(main):007:0&gt; get 'test:tb1','10001',{TIMERANGE=&gt;[1521942283487,1521942283488]}
COLUMN                CELL                                                      
 cf1:age              timestamp=1521942283487, value=22                         
1 row(s) in 0.0070 seconds

hbase(main):008:0&gt; get 'test:tb1','10001',{COLUMN=&gt;['cf1:name']}                     //等效于cf1:name
COLUMN                CELL                                                      
 cf1:name             timestamp=1521942276491, value=Jack                       
1 row(s) in 0.0070 seconds

hbase(main):009:0&gt; get 'test:tb1','10001',{FILTER=&gt;"ValueFilter(=,'substring:22')"}  //获取值包含22的数据
COLUMN                CELL                                                      
 cf1:age              timestamp=1521942283487, value=22                         
1 row(s) in 0.0430 seconds

hbase(main):010:0&gt; get 'test:tb1','10001',{FILTER=&gt;"ValueFilter(=,'binary:22')"}    //获取值等于22的数据

COLUMN                CELL                                                      
 cf1:age              timestamp=1521942283487, value=22                         
1 row(s) in 0.0160 seconds</code></pre><p><span style="font-family:SimSun;"><span style="font-size:12px;">        5) scan 扫描获取数据<br></span></span></p><pre><code class="language-plain">hbase(main):017:0&gt; scan 'test:tb1'
ROW                   COLUMN+CELL                                               
 10001                column=cf1:age, timestamp=1521942283487, value=22         
 10001                column=cf1:name, timestamp=1521942276491, value=Jack      
1 row(s) in 0.0160 seconds

hbase(main):018:0&gt; scan 'test:tb1',{COLUMNS=&gt;'cf1:age'}
ROW                   COLUMN+CELL                                               
 10001                column=cf1:age, timestamp=1521942283487, value=22         
1 row(s) in 0.0130 seconds

hbase(main):019:0&gt; scan 'test:tb1',{COLUMNS=&gt;'cf1:age', STARTROW=&gt;'2', LIMIT=&gt;1}   //获取列为age,rowkey以100开始的1条数据
ROW                   COLUMN+CELL                                               
0 row(s) in 0.0090 seconds

hbase(main):021:0&gt; scan 'test:tb1',{TIMERANGE=&gt;[1521942276491,1521942276492]}     //获取插入时间戳范围为[ts1,ts2]的数据
ROW                   COLUMN+CELL                                               
 10001                column=cf1:name, timestamp=1521942276491, value=Jack      
1 row(s) in 0.0110 seconds

hbase(main):023:0&gt; scan 'test:tb1',{FILTER=&gt;"PrefixFilter('100')"}                //获取rowkey包含100的数据
ROW                   COLUMN+CELL                                               
 10001                column=cf1:age, timestamp=1521942283487, value=22         
 10001                column=cf1:name, timestamp=1521942276491, value=Jack      
1 row(s) in 0.0100 seconds

hbase(main):024:0&gt; scan 'test:tb1',{FILTER=&gt;"ValueFilter(=,'substring:22')"}     //获取值包含22的数据
ROW                   COLUMN+CELL                                               
 10001                column=cf1:age, timestamp=1521942283487, value=22         
1 row(s) in 0.0090 seconds</code></pre><p><span style="font-family:SimSun;"><span style="font-size:12px;">        6) count: 统计表总数（慎重使用）<br></span></span></p><pre><code class="language-plain">hbase(main):027:0&gt; count 'test:tb1', INTERVAL=&gt;10, CACHE=&gt;1000   
1 row(s) in 0.0270 seconds

=&gt; 1</code></pre><p><span style="font-family:SimSun;"><span style="font-size:12px;">        7) append: 追加，假如该列不存在添加新列，存在将值追加到最后<br></span></span></p><pre><code class="language-plain">hbase(main):028:0&gt; append 'test:tb1','10001','cf1:name','_back'  //追加，假如该列不存在添加新列，存在将值追加到最后
0 row(s) in 0.0250 seconds

hbase(main):029:0&gt; get 'test:tb1','10001','cf1:name'
COLUMN                CELL                                                      
 cf1:name             timestamp=1521943322296, value=Jack_back                  
1 row(s) in 0.0080 seconds</code></pre><p><span style="font-family:SimSun;"><span style="font-size:12px;">        8) truncate: 清空表(disable--&gt;drop--&gt;create)<br></span></span></p><pre><code class="language-plain">hbase(main):030:0&gt; truncate 'test:tb1'                         
Truncating 'test:tb1' table (it may take a while):
 - Disabling table...
 - Truncating table...
0 row(s) in 4.1460 seconds</code></pre><p><span style="font-family:SimSun;"><span style="font-size:12px;">        9) truncate_preserve: 清空表但保留分区</span></span></p><pre><code class="language-plain">hbase(main):031:0&gt; truncate_preserve 'test:tb1'
Truncating 'test:tb1' table (it may take a while):
 - Disabling table...
 - Truncating table...
0 row(s) in 3.3780 seconds</code></pre><p><span style="font-family:SimSun;"><span style="font-size:12px;">        10) *incr: 增加指定表行或列的值(不会用)<br></span></span></p><p><span style="font-family:SimSun;"><span style="font-size:12px;">        11) *get_counter: 统计指定的行数(不会用)<br></span></span></p><p><span style="font-family:SimSun;"><strong>5. Group name: tools</strong></span></p><p><span style="font-family:SimSun;"><span style="font-size:12px;">        1) flush: 刷新数据</span><br></span></p><pre><code class="language-plain">hbase(main):028:0&gt; flush 'test:tb1'
0 row(s) in 0.2550 seconds</code></pre>            </div>
                </div>