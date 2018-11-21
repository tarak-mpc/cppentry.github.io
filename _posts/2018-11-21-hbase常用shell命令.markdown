---
layout:     post
title:      hbase常用shell命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/cm_chenmin/article/details/52857837				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>一.启动hbase shell<br></p>
<pre><code class="language-plain">bin/hbase shell  
</code></pre><br>
二.查看服务器状态：<br><pre><code class="language-plain">hbase(main)&gt; status  </code></pre><br>
三.查看hbase版本：<br><pre><code class="language-plain">hbase(main)&gt; version  </code></pre><br>
四.hbase表相关操作：<br>
1.查看所有表：<br><pre><code class="language-plain">hbase(main)&gt; list</code></pre><br>
2.创建表<br><pre><code class="language-plain">base(main)&gt; create 'table1',{NAME=&gt;'cf1',VERSIONS=&gt;2},{NAME=&gt;'cf2',VERSIONS=&gt;2}</code></pre><br>
3.删除表<br><pre><code class="language-plain">base(main)&gt; disable 'table1'
base(main)&gt; drop 'table1'</code></pre><br>
4.查看表结构：<br><pre><code class="language-plain">base(main)&gt; describe 'table1'</code></pre><br>
5.修改表(修改表必须先disable表)：<br><pre><code class="language-plain">base(main)&gt; disable 'table1'
hbase(main)&gt; alter 'table1',{NAME=&gt;'cf1',TTL=&gt;'15552000'},{NAME=&gt;'cf2',TTL=&gt;'15552000'}
base(main)&gt; enable 'table1'</code></pre><br>
6.表数据的增删查改：<br>
添加数据：<br><pre><code class="language-plain">hbase(main)&gt; put 'table1','rowkey001','cf1:col1','value01'</code></pre><br>
7.查询数据:<br><pre><code class="language-plain">hbase(main)&gt; get 'table1','rowkey001','cf1:col1'
hbase(main)&gt; get 'table1','rowkey001', {COLUMN=&gt;'cf1:col1'}</code></pre><br>
8.扫描表:<br><pre><code class="language-plain">hbase(main)&gt; scan 'table1',{COLUMNS=&gt;cf1:col1,LIMIT=&gt;5} #可以添加STARTROW、TIMERANGE和FITLER等高级功能</code></pre><br>
9.查询表中的数据行数:<br>
语法：count &lt;table&gt;, {INTERVAL =&gt; intervalNum, CACHE =&gt; cacheNum}<br><pre><code class="language-plain">hbase(main)&gt; count 'table1',{INTERVAL =&gt; 100, CACHE =&gt; 500}</code></pre><br>
10.删除数据:<br><pre><code class="language-plain">hbase(main)&gt; delete 'table1','rowkey001','cf1:col1'
hbase(main)&gt; truncate 'table1'</code></pre><br>
五.权限管理：<br>
语法 : grant &lt;user&gt; &lt;permissions&gt; &lt;table&gt; &lt;column family&gt; &lt;column qualifier&gt; 参数后面用逗号分隔，权限用五个字母表示： "RWXCA".<br>
分别是：READ('R'), WRITE('W'), EXEC('X'), CREATE('C'), ADMIN('A')<br><pre><code class="language-plain">hbase(main)&gt; grant 'table1','RW','cf1'</code></pre><br>
查看权限：<br><pre><code class="language-plain">hbase(main)&gt; user_permission 'hdfs'</code></pre><br>
收回权限:<br><pre><code class="language-plain">hbase(main)&gt; revoke 'hdfs','table1'</code></pre><br>
六.维护操作：<br>
1.手动major_compact,进行操作前先将balancer关闭，操作完成后再打开balancer:<br><pre><code class="language-plain">hbase(main)&gt; balance_switch false
hbase(main)&gt; major_compact 'table1'
hbase(main)&gt; balance_switch true</code></pre>通常生产环境会关闭自动major_compact(配置文件中hbase.hregion.majorcompaction设为0)，选择一个晚上用户少的时间窗口手工major_compact，如果hbase更新不是太频繁，可以一个星期对所有表做一次major_compact，这个可以在做完一次major_compact后，观看所有的storefile数量，如果storefile数量增加到major_compact后的storefile的近二倍时，可以对所有表做一次major_compact，时间比较长，操作尽量避免高锋期<br><br>
2. flush表<br><pre><code class="language-plain">hbase(main)&gt; flush 'table1'</code></pre>将所有memstore刷新到hdfs，通常如果发现regionserver的内存使用过大，造成该机的regionserver很多线程block，可以执行一下flush操作，这个操作会造成hbase的storefile数量剧增，应尽量避免这个操作，还有一种情况，在hbase进行迁移的时候，如果选择拷贝文件方式，可以先停写入，然后flush所有表，拷贝文件<br><br>
3.balance_switch<br><pre><code class="language-plain">hbase(main)&gt; balance_switch true</code></pre>配置master是否执行平衡各个regionserver的region数量，当我们需要维护或者重启一个regionserver时，会关闭balancer，这样就使得region在regionserver上的分布不均，这个时候需要手工的开启balance<br><br>
4.重启一个regionserver<br><pre><code class="language-plain">bin/graceful_stop.sh --restart --reload --debug nodename</code></pre>这个操作是平滑的重启regionserver进程，对服务不会有影响，他会先将需要重启的regionserver上面的所有region迁移到其它的服务器，然后重启，最后又会将之前的region迁移回来，但我们修改一个配置时，可以用这种方式重启每一台机子，这个命令会关闭balancer，所以最后我们要在hbase shell里面执行一下balance_switch true，对hbase regionserver重启，不要直接kill进程，这样会造成在zookeeper.session.timeout这个时间长的中断，也不要通过bin/hbase-daemon.sh
 stop regionserver去重启，如果运气不太好，-ROOT-或者.META.表在上面的话，所有的请求会全部失败<br><br>
5.regionserver关闭下线<br><pre><code class="language-plain">bin/graceful_stop.sh --stop  nodename</code></pre>和上面一样，系统会在关闭之前迁移所有region，然后stop进程，同样最后我们要手工balance_switch true，开启master的region均衡<br><br>
6.检查region是否正常以及修复：<br>
检查,有时集群正在启动或region正在做split操作，会造成数据不一致<br><pre><code class="language-plain">bin/hbase hbck  
bin/hbase hbck -details 
bin/hbase hbck 'table1' 
bin/hbase hbck -fix  #修复</code></pre><br>
7.手动merge region:<br>
找到需要合并的region的encoded name,<br><pre><code class="language-plain">hbase(main)&gt; merge_region ‘region1’,’region2’</code></pre><br>
8.手动分配region:<br>
如果发现台regionServer资源占用特别高，可以检查这台regionserver上的region是否存在过多比较大的region,通过hbase shell将部分比较大的region分配给其他不是很忙的regions server:<br>
move ‘regionId’,’serverName’<br>
例：<br><pre><code class="language-plain">hbase(main)&gt; move ‘54fca23d09a595bd3496cd0c9d6cae85‘,‘vmcnod05,60020,1390211132297‘</code></pre><br>
七.hbase的迁移<br>
1.copytable方式<br><pre><code class="language-plain">bin/hbase org.apache.hadoop.hbase.mapreduce.CopyTable --peer.adr=zookeeper1,zookeeper2,zookeeper3:/hbase 'table1'</code></pre>这个操作需要添加hbase目录里的conf/mapred-site.xml，可以复制hadoop的过来<br><br>
2.Export/Import<br><pre><code class="language-plain">bin/hbase org.apache.hadoop.hbase.mapreduce.Export table1 /user/testtable [versions] [starttime] [stoptime]
bin/hbase org.apache.hadoop.hbase.mapreduce.Import table1 /user/testtable</code></pre>export支持多版本，比copytable更实用一些<br><br>
3.直接拷贝hdfs对应的文件<br>
首先拷贝hdfs文件，如:<br><pre><code class="language-plain">bin/hadoop distcp hdfs://srcnamenode:9000/hbase/testtable/ hdfs://distnamenode:9000/hbase/testtable/</code></pre>然后在目的hbase上执行:<br><pre><code class="language-plain">bin/hbase org.jruby.Main bin/add_table.rb /hbase/testtable</code></pre>生成meta信息后，重启hbase.<br>
这个操作是简单的方式，操作之前可以关闭hbase的写入，执行flush所有表（上面有介绍）,再distcp拷贝，如果hadoop版本不一致，可以用hftp接口的方式，我推荐使用这种方式，成本低<br><p><br></p>
<p>本文参考：http://www.mamicode.com/info-detail-359862.html</p>
            </div>
                </div>