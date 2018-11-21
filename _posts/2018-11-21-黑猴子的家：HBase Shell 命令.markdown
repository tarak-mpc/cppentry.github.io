---
layout:     post
title:      黑猴子的家：HBase Shell 命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_28652401/article/details/83507934				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
    <div class="show-content-free">
            <h4>1、基本操作</h4>
<p><strong>1）进入HBase客户端命令行</strong></p>
<pre><code>[victor@hadoop102 hbase]$ bin/hbase shell
</code></pre>
<p><strong>2）查看帮助命令</strong></p>
<pre><code>hbase(main)&gt; help
</code></pre>
<p><strong>3）查看当前数据库中有哪些表</strong></p>
<pre><code>hbase(main)&gt; list
</code></pre>
<h4>2、表的操作</h4>
<p><strong>1）创建表</strong></p>
<pre><code>hbase(main)&gt; create 'student','info'
</code></pre>
<p>创建多个列族<br>
hbase&gt; create 't1', {NAME =&gt; 'f1'}, {NAME =&gt; 'f2'}, {NAME =&gt; 'f3'}</p>
<p><strong>2）插入数据到表</strong></p>
<pre><code>hbase(main) &gt; put 'student','1001','info:name','Thomas'
hbase(main) &gt; put 'student','1001','info:sex','male'
hbase(main) &gt; put 'student','1001','info:age','18'
hbase(main) &gt; put 'student','1002','info:name','Janna'
hbase(main) &gt; put 'student','1002','info:sex','female'
hbase(main) &gt; put 'student','1002','info:age','20'
</code></pre>
<p><strong>3）扫描查看表数据</strong></p>
<pre><code>hbase(main) &gt; scan 'student'
hbase(main) &gt; scan 'student',{STARTROW =&gt; '1001', STOPROW  =&gt; '1001'}
hbase(main) &gt; scan 'student',{STARTROW =&gt; '1001'}
hbase(main) &gt; scan 'student',{STARTROW =&gt; '2018-03-02', STOPROW =&gt; '2018-03-28'}
hbase(main)  &gt; scan ' student',{LIMIT =&gt; 5}
</code></pre>
<p><strong>4）查看表结构</strong></p>
<pre><code>hbase(main):012:0&gt; describe ‘student’
</code></pre>
<p><strong>5）更新指定字段的数据</strong></p>
<pre><code>hbase(main) &gt; put 'student','1001','info:name','Nick'
hbase(main) &gt; put 'student','1001','info:age','100'
</code></pre>
<p><strong>6）查看“指定行”或“指定列族:列”的数据</strong></p>
<pre><code>hbase(main) &gt; get 'student','1001'
hbase(main) &gt; get 'student','1001','info:name'
</code></pre>
<p><strong>7）删除数据</strong></p>
<p>删除某rowkey的全部数据</p>
<pre><code>hbase(main) &gt; deleteall 'student','1001'
</code></pre>
<p>删除某rowkey的某一列数据</p>
<pre><code>hbase(main) &gt; delete 'student','1002','info:sex'
</code></pre>
<p><strong>8）清空表数据</strong></p>
<pre><code>hbase(main) &gt; truncate 'student'
</code></pre>
<p>尖叫提示：清空表的操作顺序为先disable，然后再truncating。</p>
<p><strong>9）删除表</strong></p>
<p>首先需要先让该表为disable状态</p>
<pre><code>hbase(main) &gt; disable 'student'
</code></pre>
<p>然后才能drop这个表</p>
<pre><code>hbase(main) &gt; drop 'student'
</code></pre>
<p>尖叫提示：如果直接drop表，会报错：Drop the named table. Table must first be disabled<br>
ERROR: Table student is enabled. Disable it first.</p>
<p><strong>10）统计表数据行数</strong></p>
<pre><code>hbase(main) &gt; count 'student'
</code></pre>
<p><strong>11）变更表信息</strong><br>
将info列族中的数据存放3个版本</p>
<pre><code>hbase(main) &gt; alter 'student',{NAME=&gt;'info',VERSIONS=&gt;3}
</code></pre>
<p>为当前表增加列族</p>
<pre><code>hbase&gt; alter 'hbase_book', NAME =&gt; 'CF2', VERSIONS =&gt; 2
</code></pre>
<p>为当前表删除列族</p>
<pre><code>hbase&gt; alter 'hbase_book', 'delete' =&gt; ’CF2’
</code></pre>
<p><strong>12）显示服务器状态</strong></p>
<pre><code>hbase&gt; status 'node1'
</code></pre>
<p><strong>13）显示HBase当前用户</strong></p>
<pre><code>hbase&gt; whoami
</code></pre>
<p><strong>14）显示当前所有的表</strong></p>
<pre><code>hbase&gt; list
</code></pre>
<p><strong>15）exist</strong><br>
检查表是否存在，适用于表量特别多的情况</p>
<pre><code>hbase&gt; exist 'hbase_book'
</code></pre>
<p><strong>16）is_enabled/is_disabled</strong><br>
检查表是否启用或禁用</p>
<pre><code>hbase&gt; is_enabled 'hbase_book'
hbase&gt; is_disabled 'hbase_book'
</code></pre>
<p><strong>17）disable</strong><br>
禁用一张表</p>
<pre><code>hbase&gt; disable 'hbase_book'
</code></pre>

          </div>
              </div>
                </div>