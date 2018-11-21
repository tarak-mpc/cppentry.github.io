---
layout:     post
title:      HBase shell详情
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h2><span style="color:#ff0000;">概述</span></h2>
<p>HBase 为用户提供了一个非常方便的使用方式，我们称之为"HBase Shell"。</p>
<p>HBase Shell提供了大多数的HBase命令，通过HBase Shell用户可以方便地创建、删除及修改表，还可以向表中添加数据、列出表中的相关信息等。</p>
<p><strong><span style="color:#ff0000;">备注：写错HBase Shell命令时用键盘上的"Ctrl+Backspace"进行删除，"Backspace" 不起作用，另外结尾不能有任何符号。</span></strong></p>
<p>在启动HBase之后，用户可以通过下面的命令进入HBase Shell之中，命令如下图所示：</p>
<p></p>
<pre><code class="language-java">[root@liaozhongmin5 local]# hbase shell
HBase Shell; enter 'help&lt;RETURN&gt;' for list of supported commands.
Type "exit&lt;RETURN&gt;" to leave the HBase Shell
Version 0.94.7, r1471806, Wed Apr 24 18:44:36 PDT 2013

hbase(main):001:0&gt; </code></pre>具体的HBase Shell命令如下表所示：
<p></p>
<p><span></span><img src="https://img-blog.csdn.net/20150204221007260" alt=""><br><br></p>
<h2><span style="color:#ff0000;">一般操作</span></h2>
<p><strong><span style="color:#ff0000;">1.查看服务器状态</span></strong></p>
<p></p>
<pre><code class="language-java">hbase(main):028:0&gt; status
1 servers, 0 dead, 6.0000 average load

hbase(main):029:0&gt; </code></pre><br><strong><span style="color:#ff0000;">2.查看HBase版本</span></strong>
<p></p>
<p></p>
<pre><code class="language-java">hbase(main):029:0&gt; version
0.94.7, r1471806, Wed Apr 24 18:44:36 PDT 2013

hbase(main):030:0&gt; </code></pre><br><p></p>
<h2><span style="color:#ff0000;">DDL操作</span></h2>
<p></p>
<p><strong><span style="color:#ff0000;">1.create 命令</span></strong></p>
<p><strong><span style="color:#ff0000;">创建一个具有三个列族"member_id"和"address"即"info"的表"member"，其中表名、行和列都要用单引号括起来，并以逗号隔开。</span></strong></p>
<p></p>
<pre><code class="language-java">hbase(main):025:0&gt; create 'member','member_id','address','info'
0 row(s) in 1.1320 seconds

hbase(main):026:0&gt; </code></pre>
<p></p>
<p><strong><span style="color:#ff0000;">注：语句最后不能有分号，加入不小心多写了个分号，则可以使用qui命令退出错误控制台，如下：</span></strong></p>
<p></p>
<pre><code class="language-java">hbase(main):030:0&gt; list;
hbase(main):031:0* 
hbase(main):032:0* qui
TABLE                                                                                                                                                     
member                                                                                                                                                    
membermember_id                                                                                                                                           
scores                                                                                                                                                    
tb_myHbase                                                                                                                                                
users                                                                                                                                                     
5 row(s) in 0.1300 seconds

NameError: undefined local variable or method `qui' for #&lt;Object:0x1046270&gt;

hbase(main):033:0&gt; </code></pre><br><br><strong><span style="color:#ff0000;">2.list命令</span></strong>
<p></p>
<p><strong><span style="color:#ff0000;">查看当前HBase中具有哪些表</span></strong></p>
<p></p>
<pre><code class="language-java">hbase(main):026:0&gt; list
TABLE                                                                                                                                                     
member                                                                                                                                                    
scores                                                                                                                                                    
tb_myHbase                                                                                                                                                
users                                                                                                                                                     
4 row(s) in 0.0520 seconds

hbase(main):027:0&gt; </code></pre><br><br><p></p>
<p><strong><span style="color:#ff0000;">3.describe 命令</span></strong></p>
<p><strong><span style="color:#ff0000;">查看表的描述信息。</span></strong></p>
<p></p>
<pre><code class="language-java">hbase(main):028:0&gt; describe 'member'
DESCRIPTION                                                                                         ENABLED                                               
 'member', {NAME =&gt; 'address', DATA_BLOCK_ENCODING =&gt; 'NONE', BLOOMFILTER =&gt; 'NONE', REPLICATION_SC true                                                  
 OPE =&gt; '0', VERSIONS =&gt; '3', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', TTL =&gt; '2147483647', KEEP                                                       
 _DELETED_CELLS =&gt; 'false', BLOCKSIZE =&gt; '65536', IN_MEMORY =&gt; 'false', ENCODE_ON_DISK =&gt; 'true', B                                                       
 LOCKCACHE =&gt; 'true'}, {NAME =&gt; 'info', DATA_BLOCK_ENCODING =&gt; 'NONE', BLOOMFILTER =&gt; 'NONE', REPLI                                                       
 CATION_SCOPE =&gt; '0', VERSIONS =&gt; '3', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', TTL =&gt; '21474836                                                       
 47', KEEP_DELETED_CELLS =&gt; 'false', BLOCKSIZE =&gt; '65536', IN_MEMORY =&gt; 'false', ENCODE_ON_DISK =&gt;                                                        
 'true', BLOCKCACHE =&gt; 'true'}, {NAME =&gt; 'member_id', DATA_BLOCK_ENCODING =&gt; 'NONE', BLOOMFILTER =&gt;                                                       
  'NONE', REPLICATION_SCOPE =&gt; '0', VERSIONS =&gt; '3', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', TT                                                       
 L =&gt; '2147483647', KEEP_DELETED_CELLS =&gt; 'false', BLOCKSIZE =&gt; '65536', IN_MEMORY =&gt; 'false', ENCO                                                       
 DE_ON_DISK =&gt; 'true', BLOCKCACHE =&gt; 'true'}                                                                                                              
1 row(s) in 0.0320 seconds

hbase(main):029:0&gt; </code></pre><br><strong><span style="color:#ff0000;">4.删除一个列族：disable，alter，enable</span></strong>
<p></p>
<p><strong><span style="color:#ff0000;">我们之前建了3个列族，如果觉得member_id这个列族是多余的，要将其删除。</span></strong></p>
<p></p>
<pre><code class="language-java">alter 'member','delete'=&gt;'member_id'</code></pre>
<p></p>
<p>会出现如下错误：</p>
<p></p>
<pre><code class="language-java">ERROR: org.apache.hadoop.hbase.TableNotDisabledException: org.apache.hadoop.hbase.TableNotDisabledException: member</code></pre>解决方案：先disable这张表
<p></p>
<p></p>
<pre><code class="language-java">hbase(main):004:0&gt; disable 'member' 
0 row(s) in 2.0960 seconds

hbase(main):005:0&gt; </code></pre>删除刚才的列族
<p></p>
<p></p>
<pre><code class="language-java">alter 'member','delete'=&gt;'member_id'</code></pre>查看表情况
<p></p>
<p></p>
<pre><code class="language-java">hbase(main):017:0&gt; describe 'member'
DESCRIPTION                                                                                         ENABLED                                               
 'member', {NAME =&gt; 'address', DATA_BLOCK_ENCODING =&gt; 'NONE', BLOOMFILTER =&gt; 'NONE', REPLICATION_SC false                                                 
 OPE =&gt; '0', VERSIONS =&gt; '3', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', TTL =&gt; '2147483647', KEEP                                                       
 _DELETED_CELLS =&gt; 'false', BLOCKSIZE =&gt; '65536', IN_MEMORY =&gt; 'false', ENCODE_ON_DISK =&gt; 'true', B                                                       
 LOCKCACHE =&gt; 'true'}, {NAME =&gt; 'info', DATA_BLOCK_ENCODING =&gt; 'NONE', BLOOMFILTER =&gt; 'NONE', REPLI                                                       
 CATION_SCOPE =&gt; '0', VERSIONS =&gt; '3', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', TTL =&gt; '21474836                                                       
 47', KEEP_DELETED_CELLS =&gt; 'false', BLOCKSIZE =&gt; '65536', IN_MEMORY =&gt; 'false', ENCODE_ON_DISK =&gt;                                                        
 'true', BLOCKCACHE =&gt; 'true'}                                                                                                                            
1 row(s) in 0.0490 seconds</code></pre><strong><span style="color:#ff0000;">我们可以发现member_id字段已经被删除了。</span></strong>
<p></p>
<p><span style="color:#ff0000;"><strong>启用表</strong></span></p>
<p></p>
<pre><code class="language-java">hbase(main):021:0&gt; enable 'member'
0 row(s) in 2.0860 seconds</code></pre><br><br><br><strong><span style="color:#ff0000;">5.删除一个表 disable，drop</span></strong>
<p></p>
<p><strong><span style="color:#ff0000;">先disable表，再drop删除表，然后用list检查</span></strong></p>
<p></p>
<pre><code class="language-java">hbase(main):022:0&gt; disable 'member'
0 row(s) in 2.1140 seconds

hbase(main):023:0&gt; drop 'member'
0 row(s) in 1.1670 seconds

hbase(main):024:0&gt; list
TABLE                                                                                                                                                     
scores                                                                                                                                                    
tb_myHbase                                                                                                                                                
users                                                                                                                                                     
3 row(s) in 0.0250 seconds

hbase(main):025:0&gt; </code></pre><br><strong><span style="color:#ff0000;">6.查询表是否存在</span></strong>
<p></p>
<p></p>
<pre><code class="language-java">hbase(main):026:0&gt; exists 'member'
Table member does not exist                                                                                                                               
0 row(s) in 0.0740 seconds

hbase(main):027:0&gt; </code></pre><br><strong><span style="color:#ff0000;">7.判断表是否enable</span></strong>
<p></p>
<p></p>
<pre><code class="language-java">hbase(main):028:0&gt; is_enable 'users'
NoMethodError: undefined method `is_enable' for #&lt;Object:0x735f45&gt;

hbase(main):029:0&gt; is_enabled 'users'
true                                                                                                                                                      
0 row(s) in 0.0060 seconds

hbase(main):030:0&gt; disable 'users'
0 row(s) in 2.0880 seconds

hbase(main):031:0&gt; is_enabled 'users'
false                                                                                                                                                     
0 row(s) in 0.0040 seconds

hbase(main):032:0&gt; </code></pre><strong><span style="color:#ff0000;">注：另外还有一个命令与他对应即判断是否disable：is_disabled '表名'</span></strong>
<p></p>
<p><br><br></p>
<h2><span style="color:#ff0000;">DML操作</span></h2>
<p><strong><span style="color:#ff0000;">1.插入数据</span></strong></p>
<p></p>
<pre><code class="language-java">put 'member','scutshuxue','info:age','24'
put 'member','scutshuxue','info:birthday','1987-06-17
put 'member','scutshuxue','info:company','alibaba'
put 'member','scutshuxue','address:contry','china'
put 'member','scutshuxue','address:province','zhejiang'
put 'member','scutshuxue','address:city','hangzhou'
put 'member','xiaofeng','info:birthday','1987-4-17'
put 'member','xiaofeng','info:favorite','movie'
put 'member','xiaofeng','info:company','alibaba'
put 'member','xiaofeng','address:contry','china'
put 'member','xiaofeng','address:province','guangdong'
put 'member','xiaofeng','address:city','jieyang'
put 'member','xiaofeng','address:town','xianqiao'</code></pre><strong><span style="color:#ff0000;">注：插入语句后面一定不要有空格。</span></strong>
<p></p>
<p><br><strong><span style="color:#ff0000;">2.获取一条数据</span></strong></p>
<p></p>
<pre><code class="language-java">hbase(main):012:0&gt; get 'member','xiaofeng'
COLUMN                                  CELL                                                                                                              
 address:city                           timestamp=1423056496524, value=jieyang                                                                            
 address:contry                         timestamp=1423056496475, value=china                                                                              
 address:province                       timestamp=1423056496496, value=guangdong                                                                          
 address:town                           timestamp=1423056499535, value=xianqiao                                                                           
 info:birthday                          timestamp=1423056496348, value=1987-4-17                                                                          
 info:company                           timestamp=1423056496447, value=alibaba                                                                            
 info:favorite                          timestamp=1423056496386, value=movie                                                                              
7 row(s) in 0.0780 seconds

hbase(main):013:0&gt; </code></pre><br><strong><span style="color:#ff0000;">3.获取一条记录中某个列族的信息</span></strong>
<p></p>
<p></p>
<pre><code class="language-java">hbase(main):013:0&gt; get 'member','xiaofeng','info'
COLUMN                                  CELL                                                                                                              
 info:birthday                          timestamp=1423056496348, value=1987-4-17                                                                          
 info:company                           timestamp=1423056496447, value=alibaba                                                                            
 info:favorite                          timestamp=1423056496386, value=movie                                                                              
3 row(s) in 0.0160 seconds

hbase(main):014:0&gt; </code></pre><br><strong><span style="color:#ff0000;">4.获取一条记录的某个列族中的某个列的信息</span></strong>
<p></p>
<p></p>
<pre><code class="language-java">hbase(main):015:0&gt; get 'member','xiaofeng','info:company'
COLUMN                                  CELL                                                                                                              
 info:company                           timestamp=1423056496447, value=alibaba                                                                            
1 row(s) in 0.0130 seconds

hbase(main):016:0&gt; </code></pre><br><br><strong><span style="color:#ff0000;">5.更新一条记录</span></strong>
<p></p>
<p><strong><span style="color:#ff0000;">把xiaofeng的年龄改为25岁</span></strong></p>
<p></p>
<pre><code class="language-java">hbase(main):001:0&gt; get 'member','xiaofeng','info:age'
COLUMN                                  CELL                                                                                                              
 info:age                               timestamp=1423057077614, value=23                                                                                 
1 row(s) in 0.7080 seconds

hbase(main):002:0&gt; put 'member','xiaofeng','info:age','25'
0 row(s) in 0.0180 seconds

hbase(main):003:0&gt; get 'member','xiaofeng','info:age'
COLUMN                                  CELL                                                                                                              
 info:age                               timestamp=1423057248381, value=25                                                                                 
1 row(s) in 0.0110 seconds

hbase(main):004:0&gt; </code></pre><br><strong><span style="color:#ff0000;">6.根据TimeStamp来获取记录</span></strong>
<p></p>
<p></p>
<pre><code class="language-java">hbase(main):007:0&gt; get 'member','xiaofeng',{column=&gt;'info:age',timestamp=&gt;1423057248381}
NameError: undefined local variable or method `column' for #&lt;Object:0x1046270&gt;

hbase(main):008:0&gt; get 'member','xiaofeng',{COLUMN=&gt;'info:age',TIMESTAMP=&gt;1423057248381}
COLUMN                                  CELL                                                                                                              
 info:age                               timestamp=1423057248381, value=25                                                                                 
1 row(s) in 0.0070 seconds</code></pre><strong><span style="color:#ff0000;">注：要注意大小写，另外TIMESTAMP后面的时间戳不用单引号。</span></strong>
<p></p>
<p><strong><span style="color:#ff0000;">7.根据版本号获取记录</span></strong></p>
<p></p>
<pre><code class="language-java">hbase(main):025:0&gt; get 'member','xiaofeng',{COLUMN=&gt;'info:company',VERSION=&gt;3}
COLUMN                                  CELL                                                                                                              
 info:company                           timestamp=1423056496447, value=alibaba                                                                            
1 row(s) in 0.0100 seconds

hbase(main):026:0&gt; </code></pre><br><br><br><p></p>
<p><strong><span style="color:#ff0000;">8.全表扫描</span></strong></p>
<p></p>
<pre><code class="language-java">hbase(main):009:0&gt; scan 'member'
ROW                                     COLUMN+CELL                                                                                                       
 lavimer                                column=address:city, timestamp=1423056054519, value=ganzhou                                                       
 lavimer                                column=info:name, timestamp=1423056214559, value=liaozhongmin                                                     
 scutshuxue                             column=address:city, timestamp=1423056496301, value=hangzhou                                                      
 scutshuxue                             column=address:contry, timestamp=1423056399271, value=china                                                       
 scutshuxue                             column=address:province, timestamp=1423056496250, value=zhejiang                                                  
 scutshuxue                             column=info:age, timestamp=1423056337733, value=24                                                                
 scutshuxue                             column=info:birthday, timestamp=1423056367576, value=1987-06-17                                                   
 scutshuxue                             column=info:company, timestamp=1423056370340, value=alibaba                                                       
 xiaofeng                               column=address:city, timestamp=1423056496524, value=jieyang                                                       
 xiaofeng                               column=address:contry, timestamp=1423056496475, value=china                                                       
 xiaofeng                               column=address:province, timestamp=1423056496496, value=guangdong                                                 
 xiaofeng                               column=address:town, timestamp=1423056499535, value=xianqiao                                                      
 xiaofeng                               column=info:age, timestamp=1423057248381, value=25                                                                
 xiaofeng                               column=info:birthday, timestamp=1423056496348, value=1987-4-17                                                    
 xiaofeng                               column=info:company, timestamp=1423056496447, value=alibaba                                                       
 xiaofeng                               column=info:favorite, timestamp=1423056496386, value=movie                                                        
3 row(s) in 0.1090 seconds

hbase(main):010:0&gt; </code></pre><br><strong><span style="color:#ff0000;">9.删除某条记录中某个列族的某个列</span></strong>
<p></p>
<p><strong><span style="color:#ff0000;">删除xiaofeng年龄这一列。</span></strong></p>
<p></p>
<pre><code class="language-java">hbase(main):010:0&gt; delete 'member','xiaofeng','info:age'
0 row(s) in 0.0060 seconds

hbase(main):011:0&gt; get 'member','xiaofeng','info:age'
COLUMN                                  CELL                                                                                                              
0 row(s) in 0.0330 seconds

hbase(main):012:0&gt; get 'member','xiaofeng','info'
COLUMN                                  CELL                                                                                                              
 info:birthday                          timestamp=1423056496348, value=1987-4-17                                                                          
 info:company                           timestamp=1423056496447, value=alibaba                                                                            
 info:favorite                          timestamp=1423056496386, value=movie                                                                              
3 row(s) in 0.0470 seconds

hbase(main):013:0&gt; </code></pre><br><br><p></p>
<p><strong><span style="color:#ff0000;">10.删除整行</span></strong></p>
<p></p>
<pre><code class="language-java">hbase(main):013:0&gt; deleteall 'member','scutshuxue'
0 row(s) in 0.0080 seconds

hbase(main):014:0&gt; scan 'member'
ROW                                     COLUMN+CELL                                                                                                       
 lavimer                                column=address:city, timestamp=1423056054519, value=ganzhou                                                       
 lavimer                                column=info:name, timestamp=1423056214559, value=liaozhongmin                                                     
 xiaofeng                               column=address:city, timestamp=1423056496524, value=jieyang                                                       
 xiaofeng                               column=address:contry, timestamp=1423056496475, value=china                                                       
 xiaofeng                               column=address:province, timestamp=1423056496496, value=guangdong                                                 
 xiaofeng                               column=address:town, timestamp=1423056499535, value=xianqiao                                                      
 xiaofeng                               column=info:birthday, timestamp=1423056496348, value=1987-4-17                                                    
 xiaofeng                               column=info:company, timestamp=1423056496447, value=alibaba                                                       
 xiaofeng                               column=info:favorite, timestamp=1423056496386, value=movie                                                        
2 row(s) in 0.0430 seconds

hbase(main):015:0&gt; </code></pre><strong><span style="color:#ff0000;"><br>
11.查询表中有多少行</span></strong>
<p></p>
<p></p>
<pre><code class="language-java">hbase(main):015:0&gt; count 'member'
2 row(s) in 0.2170 seconds

hbase(main):016:0&gt; </code></pre><br><strong><span style="color:#ff0000;">12.给某条记录的某个列族增加一列并使用counter实现递增</span></strong>
<p></p>
<p></p>
<pre><code class="language-java">hbase(main):018:0&gt; incr 'member','xiaofeng','info:age'
COUNTER VALUE = 1

hbase(main):019:0&gt; get 'member','xiaofeng','info:age'
COLUMN                                  CELL                                                                                                              
 info:age                               timestamp=1423058331353, value=\x00\x00\x00\x00\x00\x00\x00\x01                                                   
1 row(s) in 0.0100 seconds

hbase(main):021:0&gt; incr 'member','xiaofeng','info:age'
COUNTER VALUE = 2

hbase(main):022:0&gt; </code></pre><br><strong><span style="color:#ff0000;">13.获取当前count的值</span></strong>
<p></p>
<p></p>
<pre><code class="language-java">hbase(main):022:0&gt; get_counter 'member','xiaofeng','info:age'
COUNTER VALUE = 2

hbase(main):023:0&gt; </code></pre>
<p></p>
<p><br></p>
<strong><span style="color:#ff0000;">14.清空整张表</span></strong>
<p></p>
<pre><code class="language-java">hbase(main):026:0&gt; truncate 'member'
Truncating 'member' table (it may take a while):
 - Disabling table...
 - Dropping table...
 - Creating table...
0 row(s) in 5.5960 seconds

hbase(main):027:0&gt; scan 'member'
ROW                                     COLUMN+CELL                                                                                                       
0 row(s) in 0.0150 seconds

hbase(main):028:0&gt; </code></pre><br><p></p>
            </div>
                </div>