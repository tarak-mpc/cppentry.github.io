---
layout:     post
title:      [Hbase]HBASE SHELL常用命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1>HBASE SHELL常用命令</h1>

<p>标签: <code>HBase Shell</code></p>

<hr><h3>1. 进入hbase shell console</h3>

<p>$HBASE_HOME/bin/hbase shell<br>
如果有kerberos认证，需要事先使用相应的keytab进行一下认证（使用kinit命令），认证成功之后再使用hbase shell进入可以使用whoami命令可查看当前用户.<br><code>hbase(main)&gt; whoami</code></p>

<hr><h3>2. 表的管理</h3>

<p>1）查看有哪些表<br><code>hbase(main)&gt; list</code></p>

<p>2）创建表<br><strong>语法：</strong> <code>create &lt;table&gt;, {NAME =&gt; &lt;family&gt;, VERSIONS =&gt; &lt;VERSIONS&gt;}</code></p>

<p><strong>例如：</strong>创建表t1，有两个family name：f1，f2，且版本数均为2<br><code>hbase(main)&gt; create 't1',{NAME =&gt; 'f1', VERSIONS =&gt; 2},{NAME =&gt; 'f2', VERSIONS =&gt; 2}</code></p>

<p>3）删除表<br>
分两步：1. disable，2. drop<br>
例如：删除表t1<br><code>hbase(main)&gt; disable 't1'</code><br><code>hbase(main)&gt; drop 't1'</code></p>

<p>4）查看表的结构<br><strong>语法：</strong><code>describe &lt;table&gt;</code></p>

<p><strong>例如：</strong>查看表t1的结构<br><code>hbase(main)&gt; describe 't1'</code></p>

<p>5）修改表结构<br><strong>修改表结构必须先disable</strong></p>

<p><strong>语法：</strong>alter 't1', {NAME =&gt; 'f1'}, {NAME =&gt; 'f2', METHOD =&gt; 'delete'}</p>

<p><strong>例如：修改表test1的cf的TTL为180天</strong><br><code>hbase(main)&gt; disable 'test1'</code><br><code>hbase(main)&gt; alter 'test1',{NAME=&gt;'body',TTL=&gt;'15552000'},{NAME=&gt;'meta', TTL=&gt;'15552000'}</code><br><code>hbase(main)&gt; enable 'test1'</code></p>

<hr><h3>3. 权限管理</h3>

<p>1）分配权限<br><strong>语法:</strong> <code>grant &lt;user&gt; &lt;permissions&gt; &lt;table&gt; &lt;column family&gt; &lt;column qualifier&gt;</code>, 参数后面用逗号分隔.<br><strong>权限用五个字母表示：</strong> "RWXCA".<br>
**READ('R'), WRITE('W'), EXEC('X'), CREATE('C'), ADMIN('A')</p>

<p><strong>例如:</strong><br>
给用户‘test'分配对表t1有读写的权限.<br><code>hbase(main)&gt; grant 'test','RW','t1'</code></p>

<p>2）查看权限<br><strong>语法:</strong> <code>user_permission &lt;table&gt;</code></p>

<p><strong>例如:</strong> 查看表t1的权限列表.<br><code>hbase(main)&gt; user_permission 't1'</code></p>

<p>3）收回权限<br><strong>语法:</strong> <code>revoke &lt;user&gt; &lt;table&gt; &lt;column family&gt; &lt;column qualifier&gt;</code></p>

<p><strong>例如:</strong> 收回test用户在表t1上的权限<br><code>hbase(main)&gt; revoke 'test','t1'</code></p>

<hr><h3>4. 表数据的增删改查</h3>

<p>1）添加数据<br><strong>语法:</strong> <code>put &lt;table&gt;,&lt;rowkey&gt;,&lt;family:column&gt;,&lt;value&gt;,&lt;timestamp&gt;</code></p>

<p><strong>例如:</strong> 给表t1的添加一行记录：rowkey是rowkey001，family** name：f1，column name：col1，value：value01，timestamp：系统默认<br><code>hbase(main)&gt; put 't1','rowkey001','f1:col1','value01'</code></p>

<p>2）查询数据<br>
a）查询某行记录<br><strong>语法:</strong> <code>get &lt;table&gt;,&lt;rowkey&gt;,[&lt;family:column&gt;,....]</code></p>

<p><strong>例如:</strong> 查询表t1，rowkey001中的f1下的col1的值**<br><code>hbase(main)&gt; get 't1','rowkey001', 'f1:col1'</code><br>
or<br><code>hbase(main)&gt; get 't1','rowkey001', {COLUMN=&gt;'f1:col1'}</code></p>

<p><strong>查询表t1，rowke002中的f1下的所有列值</strong><br><code>hbase(main)&gt; get 't1','rowkey001'</code></p>

<p>b）扫描表<br><strong>语法:</strong> <code>scan &lt;table&gt;, {COLUMNS =&gt; [ &lt;family:column&gt;,.... ], LIMIT =&gt; num}</code><br><strong>另外，还可以添加STARTROW、TIMERANGE和FITLER等高级功能</strong></p>

<p><strong>例如:</strong> 扫描表t1的前5条数据<br><code>hbase(main)&gt; scan 't1',{LIMIT=&gt;5}</code></p>

<p>c）查询表中的数据行数<br><strong>语法:</strong> <code>count &lt;table&gt;, {INTERVAL =&gt; intervalNum, CACHE =&gt; cacheNum}</code><br><strong>INTERVAL设置多少行显示一次及对应的rowkey，默认1000；CACHE每次去取的缓存区大小，默认是10，调整该参数可提高查询速度</strong></p>

<p><strong>例如:</strong> 查询表t1中的行数，每100条显示一次，缓存区为500**<br><code>hbase(main)&gt; count 't1', {INTERVAL =&gt; 100, CACHE =&gt; 500}</code></p>

<p>3）删除数据<br>
a )删除行中的某个列值<br><strong>语法:</strong> <code>delete &lt;table&gt;, &lt;rowkey&gt;, &lt;family:column&gt; , &lt;timestamp&gt;</code>,必须指定列名.</p>

<p><strong>例如:</strong> 删除表t1，rowkey001中的f1:col1的数据<br><code>hbase(main)&gt; delete 't1','rowkey001','f1:col1'</code><br>
注：将删除改行f1:col1列所有版本的数据</p>

<p>b )删除行<br><strong>语法:</strong> <code>deleteall &lt;table&gt;, &lt;rowkey&gt;, &lt;family:column&gt; , &lt;timestamp&gt;</code>，可以不指定列名，删除整行数据.</p>

<p><strong>例如:</strong> 删除表t1，rowk001的数据<br><code>hbase(main)&gt; deleteall 't1','rowkey001'</code></p>

<p>c）删除表中的所有数据<br><strong>语法:</strong> <code>truncate &lt;table&gt;</code></p>

<p><strong>删除流程:</strong> <code>disable table -&gt; drop table -&gt; create table</code></p>

<p><strong>例如:</strong> 删除表t1的所有数据<br><code>hbase(main)&gt; truncate 't1'</code></p>

<hr><h3>5. Region管理</h3>

<p>1）移动region<br><strong>语法:</strong> <code>move 'encodeRegionName', 'ServerName'</code>,<br><strong>encodeRegionName指的regioName后面的编码，ServerName指的是master-status的Region Servers列表</strong></p>

<p><strong>例如：</strong><br><code>hbase(main)&gt;move '4343995a58be8e5bbc739af1e91cd72d', 'db-41.xxx.xxx.org,60020,1390274516739'</code></p>

<p>2）开启/关闭region<br><strong>语法:</strong> <code>balance_switch true|false</code></p>

<p><strong>例如：</strong><code>hbase(main)&gt; balance_switch</code></p>

<p>3）手动split<br><strong>语法:</strong> <code>split 'regionName', 'splitKey'</code></p>

<p>4）手动触发major compaction<br><strong>语法：</strong><br><strong>Compact all regions in a table:</strong><br><code>hbase&gt; major_compact 't1'</code><br><strong>Compact an entire region:</strong><br><code>hbase&gt; major_compact 'r1'</code><br><strong>Compact a single column family within a region:</strong><br><code>hbase&gt; major_compact 'r1', 'c1'</code><br><strong>Compact a single column family within a table:</strong><br><code>hbase&gt; major_compact 't1', 'c1'</code></p>

<hr><h3>6. 配置管理及节点重启</h3>

<p>1）修改hdfs配置<br>
hdfs配置位置：/etc/hadoop/conf<br><strong>同步hdfs配置</strong></p>

<pre class="has">
<code>cat /home/hadoop/slaves|xargs -i -t scp /etc/hadoop/conf/hdfs-site.xml hadoop@{}:/etc/hadoop/conf/hdfs-site.xml
</code></pre>

<p><strong>关闭：</strong></p>

<pre class="has">
<code>cat /home/hadoop/slaves|xargs -i -t ssh hadoop@{} "sudo /home/hadoop/cdh4/hadoop-2.0.0-cdh4.2.1/sbin/hadoop-daemon.sh --config /etc/hadoop/conf stop datanode"`
</code></pre>

<p><strong>启动：</strong></p>

<pre class="has">
<code>cat /home/hadoop/slaves|xargs -i -t ssh hadoop@{} "sudo /home/hadoop/cdh4/hadoop-2.0.0-cdh4.2.1/sbin/hadoop-daemon.sh --config /etc/hadoop/conf start datanode"
</code></pre>

<p>2）修改hbase配置<br>
hbase配置位置：/etc/hbase/conf<br><strong>同步hbase配置</strong></p>

<pre class="has">
<code>cat /home/hadoop/hbase/conf/regionservers|xargs -i -t scp /home/hadoop/hbase/conf/hbase-site.xml hadoop@{}:/home/hadoop/hbase/conf/hbase-site.xml
</code></pre>

<p><strong>graceful重启</strong></p>

<pre class="has">
<code>cd ~/hbase
bin/graceful_stop.sh --restart --reload --debug inspurXXX.xxx.xxx.org</code></pre>

<p> </p>            </div>
                </div>