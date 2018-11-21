---
layout:     post
title:      Hive--HiveQL：数据定义、数据操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/Xw_Classmate/article/details/49680811				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1><span style="font-size:18px;">2 数据定义</span></h1>
<h2><span style="font-size:18px;">2.1 Hive中的数据库（database）</span></h2>
<p><span style="font-size:18px;">    Hive中的数据库的概念本质上仅仅是表的一个目录或者命名空间。</span></p>
<p><span style="font-size:18px;"><span style="font-size:18px;">2.1.1 查看所有数据库：show databases;</span></span></p>
<p><span style="font-size:18px;"></span></p>
<pre><code class="language-sql">hive (default)&gt; show databases;
</code></pre><br><p></p>
<p><span style="font-size:18px;">创建数据库：create database [if not exists] &lt;<span style="font-size:18px;">database_name</span>&gt;;</span></p>
<p><br><span style="font-size:18px;"></span></p>
<pre><code class="language-sql">hive (default)&gt; create database if not exists financials;</code></pre><br><p><span style="font-size:18px;">注意：所有和数据库相关的命令中，都可以使用schema这个关键字来替代database。</span><br></p>
<p><img src="https://img-blog.csdn.net/20151106114429085" alt=""></p>
<p></p>
<p><span style="font-size:18px;">       如果数据库非常多，可以使用正则表达式匹配来筛选出需要的数据库名。</span></p>
<p><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20151106114704400" alt=""></span></p>
<p><span style="font-size:18px;">    创建数据库时，数据库所在的目录是配置文件中hive.metastore.warehouse.dir所配置的顶层目录之后，默认是<span style="font-size:18px;">/usr/hive/warehouse</span>，当创建数据库financials是，Hive将会对应创建一个目录/usr/hive/warehouse/financials.db。注意，数据库的文件目录名都是以.db结尾的。</span></p>
<p><span style="font-size:18px;">   可以通过添加location来修改这个默认位置，可以通过comment来为数据库添加一个描述信息，且location必须位于comment之后。</span></p>
<p><span style="font-size:18px;"></span></p>
<pre><code class="language-sql">hive (default)&gt; create database first_database
              &gt; comment 'Holds all my data tables'
              &gt; location '/mydirection/mydatabase';</code></pre><br><span style="font-size:18px;">2.1.2 查看数据库的信息</span>：<span style="font-size:18px;">describle database</span> &lt;<span style="font-size:18px;">database_name</span>&gt;;
<p></p>
<p><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20151106150940244" alt=""><br></span></p>
<p><span style="font-size:18px;">可以使用with dbproperties (key1=value1,key2=value2,...)来给数据库添加一些和其相关的键值对信息，并且必须位于location之后。</span></p>
<p><span style="font-size:18px;"></span></p>
<pre><code class="language-sql">hive (default)&gt; create database second_database 
              &gt; comment 'Holds all my data ex_tables'
              &gt; location '/mydirection/mydatabase'
              &gt; with dbproperties ('creator'= 'Mark Moneybags', 'date' = '2015-11-6');</code></pre><br><span style="font-size:18px;">可以通过describe database extended &lt;database_name&gt;来显示出这些所有的信息。</span>
<p></p>
<p><span style="font-size:18px;"></span></p>
<pre><code class="language-sql">hive (default)&gt; describe database extended second_database;</code></pre><br><img src="https://img-blog.csdn.net/20151106153526915" alt=""><br><p></p>
<p><br></p>
<p><span style="font-size:18px;">2.1.3 使用数据库：use &lt;database_name&gt;;</span></p>
<p><br><span style="font-size:18px;"></span></p>
<pre><code class="language-sql">hive (default)&gt; use financials;
</code></pre><br><img src="https://img-blog.csdn.net/20151106153751397" alt=""><p></p>
<p><br></p>
<p><span style="font-size:18px;">2.1.4 删除数据库：drop database [if exists] financiasl;</span></p>
<p><br><span style="font-size:18px;"></span></p>
<pre><code class="language-sql">hive (financials)&gt; drop database financials;
</code></pre><br><img src="https://img-blog.csdn.net/20151106153927752" alt=""><p></p>
<p><br></p>
<p><span style="font-size:18px;">    默认情况下，Hive不支持删除含有表的数据库。要么先删除数据库中的所有表，然后再删除该数据库；要么在删除命了的最后面加上关键字cascade。</span></p>
<p><br><span style="font-size:18px;"></span></p>
<pre><code class="language-sql">hive (default)&gt; drop database first_database cascade;</code></pre><br><img src="https://img-blog.csdn.net/20151106154417889" alt=""><p></p>
<p><br></p>
<p><span style="font-size:18px;">2.1.5 修改数据库的dbproperties的键值对属性：alter database &lt;database_name&gt; set dbproperties ('edited-by' = 'Oner');</span></p>
<p><span style="font-size:18px;">    数据库的其他元数据信息都是不可更改的，包括数据库名和数据库所在的目录。<br></span></p>
<h2><span style="font-size:18px;">2.2 Hive中的表</span></h2>
<h3><span style="font-size:18px;">2.2.1  管理表（内部表）<br></span></h3>
<p><span style="font-size:18px;">1 创建表</span></p>
<p><span style="font-size:18px;">    create table语句遵从SQL语法惯例，但是Hive的这个语句中具有的扩展功能，使其具有更广泛的灵活性。</span></p>
<p><br><span style="font-size:18px;"></span></p>
<pre><code class="language-sql">hive (default)&gt; create table if not exists mydb.employees (
              &gt; name string comment 'Employee name',
              &gt; salary float comment 'Employee salary', 
              &gt; subordinates array&lt;string&gt; comment 'Names of subordinates',
              &gt; deductions map&lt;string,float&gt; comment 'Keys are deductions name,value are percentages',
              &gt; address struct&lt;street:string, city:string, state:string, zip:int&gt; comment 'Home address')
              &gt; comment 'Description of the table'
              &gt; location '/usr/hive/warehouse/mydb.db/employees'
              &gt; tblproperties ('creator'='me', 'created_at'='2015-11-6');</code></pre><br><img src="https://img-blog.csdn.net/20151106202225953" alt=""><p></p>
<p><span style="font-size:18px;">    如果当前用户所处的数据库并非目标数据库，建表的时候可以在表名前面增加数据库名来指定，也就是本例中的mydb；用户可以在字段类型后为每个字段增加一个注释，也可以为这个表添加一个注释；tblproperties的主要作用是安键-值对的格式为表添加额外的文档说明。</span></p>
<p><span style="font-size:18px;">2 使用表：<span style="font-size:18px;">use &lt;table_name&gt;;</span></span></p>
<p><br><span style="font-size:18px;"></span></p>
<pre><code class="language-sql">hive (default)&gt; use default;</code></pre><br><img src="https://img-blog.csdn.net/20151106203506285" alt=""><br><p></p>
<p><span style="font-size:18px;">3 列举数据库下的表：show tables [in &lt;<span style="font-size:18px;">table_name</span>&gt;];</span></p>
<p><span style="font-size:18px;">列举当前数据库下的表：<br></span></p>
<p><span style="font-size:18px;"></span></p>
<pre><code class="language-sql">hive (default)&gt; show tables;</code></pre>列举制定数据库下的表：<br><pre><code class="language-sql">hive (default)&gt; show tables in mydb;</code></pre><img src="https://img-blog.csdn.net/20151106203907469" alt=""><br><br><p></p>
<p><span style="font-size:18px;">使用正则表达式过滤出所需要的表名：</span></p>
<p><br><span style="font-size:18px;"></span></p>
<pre><code class="language-sql">hive (default)&gt; show tables in mydb 'emp.*';</code></pre><br><img src="https://img-blog.csdn.net/20151107135021180" alt=""><p></p>
<p><br></p>
<p><span style="font-size:18px;">注意Hive并非支持所有正则表达式功能。</span></p>
<p><span style="font-size:18px;">4 查看表的详细信息：describe [extended] [database_name].&lt;table_name&gt;;</span></p>
<p><span style="font-size:18px;"><span style="font-size:18px;">或者describe [formatted] [database_name].&lt;table_name&gt;;</span></span></p>
<p><span style="font-size:18px;"><br><span style="font-size:18px;"></span></span></p>
<pre><code class="language-sql">hive (default)&gt; describe mydb.employees;</code></pre><br><img src="https://img-blog.csdn.net/20151107135755650" alt=""><p></p>
<p><span style="font-size:18px;"><span style="font-size:18px;">如果当前所处的工作数据库就是mydb，可以不加mydb.这个前缀。</span></span></p>
<p><span style="font-size:18px;"><span style="font-size:18px;">加上extended或者formatted可以显示更详细的信息，使用formatted显示的结果可读性更好，所有formatted用的更多</span></span></p>
<p><span style="font-size:18px;"><span style="font-size:18px;"></span></span></p>
<pre><code class="language-sql">hive (default)&gt; describe formatted mydb.employees;
</code></pre><br>
如果只想查看某一列的信息，只需要在表名后面添加列的字段名即可。
<p></p>
<p><span style="font-size:18px;"><span style="font-size:18px;"></span></span></p>
<pre><code class="language-sql">hive (mydb)&gt; describe employees.salary;</code></pre><br><br><p></p>
<p><span style="font-size:18px;"><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20151107141137797" alt=""><br></span></span></p>
<p><span style="font-size:18px;"><br></span></p>
<h3><span style="font-size:18px;">2.2.2 外部表</span></h3>
<p><span style="font-size:18px;">     之前创建的表都是管理表(内部表)，在删除一个管理表时，会删除该表的元数据，也会删除表中的数据，如果想要在删除表后，只删除元数据信息，而不删除表中内容，可以考虑使用外部表。</span></p>
<p><span style="font-size:18px;">    创建外部表</span></p>
<p><br><span style="font-size:18px;"></span></p>
<pre><code class="language-sql">hive (default)&gt; create external table if not exists stocks ( 
              &gt; trade string,
              &gt; symbol string, 
              &gt; ymd string,
              &gt; price_open float,
              &gt; price_high float,
              &gt; price_low float,
              &gt; price_close float,
              &gt; volume int, 
              &gt; price_adj_close float) 
              &gt; row format delimited fields terminated by ','
              &gt; location '/data/stocks';</code></pre><br>
    关键字external表示这个表示外部的，而后面的location...子句表示外部表指向的数据在哪里。<br><p></p>
<h3><span style="font-size:18px;">2.2.3 分区表</span></h3>
<p><span style="font-size:18px;">1 创建分区表</span></p>
<p><span style="font-size:18px;">1）创建内部分区表<br></span></p>
<p><span style="font-size:18px;">创建一个employees表，先按照country在按照state进行分区。</span></p>
<p><span style="font-size:18px;"></span></p>
<pre><code class="language-sql">hive (mydb)&gt; create table employees (
           &gt; name string,
           &gt; salary float,
           &gt; subordinates array&lt;string&gt;,
           &gt; deductions map&lt;string,float&gt;,
           &gt; address struct&lt;street:string, city:string, state:string,zip:int&gt;)
           &gt; comment 'Description of the table'
           &gt; partitioned by (country string, state string)
           &gt; location '/user/hive/warehouse/mydb.db/employees';</code></pre><br><img src="https://img-blog.csdn.net/20151107165501327" alt=""><br><p></p>
<p><br></p>
<p><span style="font-size:18px;">2）创建外部分区表</span></p>
<p><span style="font-size:18px;"></span></p>
<pre><code class="language-sql">hive (mydb)&gt; create external table if not exists logs_message (
           &gt; hms int,
           &gt; severity string,
           &gt; server string,
           &gt; process_id int,
           &gt; message string)
           &gt; partitioned by (year int, month int, day int)
           &gt; row format delimited fields terminated by '\t';
</code></pre><br><img src="https://img-blog.csdn.net/20151107172634876" alt=""><br><p></p>
<p><span style="font-size:18px;">分区表改变了Hive对数据存储的组织方式。<br></span></p>
<p><span style="font-size:18px;">2 显示分区表</span></p>
<p><span style="font-size:18px;">show partitions &lt;table_name&gt;;</span></p>
<p><br><span style="font-size:18px;"></span></p>
<pre><code class="language-sql">hive (mydb)&gt; show partitions employees;</code></pre><br><img src="https://img-blog.csdn.net/20151107171208707" alt=""><p></p>
<p><br></p>
<h3><span style="font-size:18px;">2.2.4 修改表</span></h3>
<p><span style="font-size:18px;">    大多数的表的属性可以通过alter table语句来进行修改，这种操作会修改元数据，但不会修改数据本身。</span></p>
<p><span style="font-size:18px;">1 表重命名</span></p>
<p><span style="font-size:18px;">alter table &lt;table_name&gt; renamt to l&lt;table_newname&gt;</span>;</p>
<p><span style="font-size:18px;">2  增加、修改和删除表分区</span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;">增加分区：alter table &lt;table_name&gt; add partition (<span style="font-size:18px;"><span style="font-size:18px;">partcol=value</span></span>) [location ...];<br></span></p>
<p><span style="font-size:18px;"></span></p>
<pre><code class="language-sql">hive (mydb)&gt; alter table logs_message add
           &gt; partition (year=2015, month=1, day=1) location '/logs/2015/01/01'
           &gt; partition (year=2015, month=1, day=2) location '/logs/2015/01/02';</code></pre>修改分区：alter table &lt;table_name&gt; partition (...) set location ‘...’;
<p></p>
<p><span style="font-size:18px;"></span></p>
<pre><code class="language-sql">hive (mydb)&gt; alter table logs_message                                             
           &gt; partition (year=2015, month=1, day=2) set location 'H01://out/2015/01/02';</code></pre>删除分区：alter table <span style="font-size:18px;">&lt;table_name&gt; </span>
drop [if exists] partition (...);
<p></p>
<p><span style="font-size:18px;">2.2.5  修改列信息</span></p>
<p><span style="font-size:18px;">alter table &lt;<span style="font-size:18px;">table_name</span>&gt; change [column] col_old_name col_new_name column_type [comment col_comment] [first|after column_name];</span></p>
<p><span style="font-size:18px;"></span></p>
<pre><code class="language-sql">hive (mydb)&gt; alter table logs_message
           &gt; change column hms hours_minutes_seconds int
           &gt; comment 'The hours,minutes,and seconds part of the timestamp'
           &gt; first;</code></pre><br>
其中，column和comment子句是可选的，first|after column_name是用来指定修改后该列的位置。
<p></p>
<p><span style="font-size:18px;">2.2.6 增加列</span></p>
<p><span style="font-size:18px;">alter table &lt;table_name&gt; add columns (new_column column_type<span style="font-size:18px;">[partition (partcol1=val1, partcol2=val2 ...)]</span>);</span></p>
<p><span style="font-size:18px;"></span></p>
<pre><code class="language-sql">hive (mydb)&gt; alter table logs_message add columns (     
           &gt; app_name string comment 'Application name',       
           &gt; session_name int comment 'The current session id');
</code></pre><br>
2.2.7  删除/替换列<br><p></p>
<p><span style="font-size:18px;"><span style="font-size:18px;">alter table &lt;table_name&gt; replace columns (new_column column_type<span style="font-size:18px;">[partition (partcol1=val1, partcol2=val2 ...)]</span>);</span></span></p>
<p><span style="font-size:18px;"></span></p>
<pre><code class="language-sql">hive (mydb)&gt; alter table logs_message replace columns (
           &gt; hours_mins_secs int comment 'hour,minute,seconds from timestamp',
           &gt; severity string comment 'The message severity',
           &gt; message string comment 'The rest of the message');</code></pre><br>
2.2.8  修改表属性
<p></p>
<p><span style="font-size:18px;">alter table &lt;table_name&gt; set tblproperties ('key'='value');<br></span></p>
<p><span style="font-size:18px;"></span></p>
<pre><code class="language-sql">hive (mydb)&gt; alter table logs_message set tblproperties (                                  
           &gt; 'notes' = 'The process id is no longer captured, this column is always NULL');</code></pre>注意:无法删除表属性<br><br><p></p>
<p><span style="font-size:18px;">2.2.9  修改存储属性</span></p>
<p><span style="font-size:18px;">alter table &lt;<span style="font-size:18px;">table_name</span>&gt;<span style="font-size:18px;">[partition (partcol1=val1, partcol2=val2 ...)]</span> set fileformat &lt;<span style="font-size:18px;">fileformat_name</span>&gt;;<br></span></p>
<p><span style="font-size:18px;"></span></p>
<pre><code class="language-sql">hive (mydb)&gt; alter table logs_message             
           &gt; partition (year=2015, month=1, day=2)
           &gt; set fileformat sequencefile;</code></pre><br>
3  数据操作
<p></p>
<p><span style="font-size:18px;">3.1 向管理表中装载数据</span></p>
<p><span style="font-size:18px;">load data [local] inpath 'filepath' [overwrite] into table &lt;<span style="font-size:18px;">table_name</span>&gt; [partition (partcol1=val1, partcol2=val2 ...)];<br></span></p>
<p><br><span style="font-size:18px;"></span></p>
<pre><code class="language-sql">hive (mydb)&gt; load data local inpath '${env:HOME}/employees.txt' 
           &gt; overwrite into table employees
           &gt; partition (country='US',state='CA');</code></pre><br><img src="https://img-blog.csdn.net/20151107200530288" alt=""><p></p>
<p><br></p>
<p><span style="font-size:18px;">local：<br></span></p>
<p><span style="font-size:18px;">    指定了local，代表数据源路径是本地文件系统的路径，该数据将会被copy到目标位置；如果未指定local关键字，这个路径代表的是HDFS上的路径，这种情况下，文件会被move到目标位置。</span></p>
<p><span style="font-size:18px;">overwrite：<br></span></p>
<p><span style="font-size:18px;">    指定了overwrite<span style="color:#000000;">目标表（或者分区）中的内容（如果有）会被删除，然后再将 filepath 指向的文件/目录中的内容添加到表/分区中；<span style="color:#000000;">如果目标表（分区）已经有一个文件，并且文件名和 filepath 中的文件名冲突，那么现有的文件会被新文件所替代。</span></span><br></span></p>
<p><span style="font-size:18px;">partition：<br></span></p>
<p><span style="font-size:18px;">    指定了partition，目标分区目录如果不存在的话，系统会自动创建分区目录，然后将数据copy到该目录下；如果目标是非分区表，应省去partition子句。</span></p>
<h2><span style="font-size:18px;">3.2  将查询结果写入Hive表</span></h2>
<h3><span style="font-size:18px;"><span style="font-size:18px;">3.2.</span>1 <span style="font-size:18px;">
基本模式</span><br></span></h3>
<p><span style="font-size:18px;">insert overwrite table &lt;table_name&gt; [partition (partcol1=val1, partcol2=val2 ...)] select_statement from<span style="font-size:18px;"><span style="color:#000000;">from_statement</span></span>;</span></p>
<p><span style="font-size:18px;"></span></p>
<pre><code class="language-sql">hive (mydb)&gt; insert overwrite employees      
           &gt; partition (country='US', state='OR')
           &gt; select * from staged_employees se
           &gt; where se.cnty = 'US' and se.st = 'OR';
</code></pre><br><h3><span style="font-size:18px;">3.2.2 多插入模式</span></h3>
<p></p>
<p><span style="font-size:18px;">from &lt;from_statement&gt;</span></p>
<p><span style="font-size:18px;">insert overwrite table &lt;table_name1&gt; [partition (partcol1=val1, partcol2=val2 ...)] select_statement1</span></p>
<p><span style="font-size:18px;"><span style="font-size:18px;">insert overwrite table &lt;table_name2&gt; [partition (partcol1=val1, partcol2=val2 ...)] select_statement2；</span></span></p>
<h3><span style="font-size:18px;"><span style="font-size:18px;">3.2.</span>3 自动分区模式</span></h3>
<p><span style="font-size:18px;">insert overwrite table &lt;table_name&gt; partition (partcol1[=val1], partcol2[=val2] ...) select_statement form form_statement;</span></p>
<h2><span style="font-size:18px;">3.3  导出数据</span></h2>
<p><span style="font-size:18px;"><span style="font-size:18px;">insert overwrite [local] directory 'directory' select_statement;</span></span></p>
<p><span style="font-size:18px;">导出数据到本地目录：</span></p>
<p><span style="font-size:18px;">insert overwrite local directory '${env:HOME}/data' select * from employees;</span></p>
<p><span style="font-size:18px;">导出数据到HDFS中：</span></p>
<p><span style="font-size:18px;">insert overwrite directory '/user/hive/warehouse/table' select value from employees;</span></p>
<p><span style="font-size:18px;">同一个查询结果可以同时插入到多个表或者多个目录中：</span></p>
<p><span style="font-size:18px;">from employees se<br>
insert overwrite local directory '${env:HOME}/data' select *<br>
insert overwrite directory '/user/hive/warehouse/table' select * where se.cty='US';<br></span></p>
<p><span style="font-size:18px;"><br></span></p>
            </div>
                </div>