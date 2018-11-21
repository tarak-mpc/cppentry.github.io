---
layout:     post
title:      大数据HBase系列之HBase基本操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h2>1.  hbase命令</h2>

<ul><li>版本查看</li>
</ul><blockquote>
<p>hbase version</p>
</blockquote>

<ul><li>连接Zookeeper客户端</li>
</ul><blockquote>
<p>hbase zkcli</p>
</blockquote>

<ul><li>连接HBase客户端</li>
</ul><blockquote>
<p>hbase shell</p>
</blockquote>

<hr><h2>2.  CRUD</h2>

<p><strong>2.1  创建表</strong></p>

<pre class="has">
<code class="language-sql">-- 语法：create '表名','列族名'
create 'student','info'</code></pre>

<p><strong>2.2  显示所有表</strong></p>

<pre class="has">
<code class="language-sql">-- 语法：list 或 list '表名'
list 'student'</code></pre>

<p><strong>2.3  显示表描述</strong></p>

<pre class="has">
<code class="language-sql">-- 语法：describe '表名'
describe 'student'</code></pre>

<p><strong>2.4  插入数据</strong></p>

<pre class="has">
<code class="language-sql">-- 语法：put '表名','行键','列族:列名','值'
put 'student','10001','info:id','Ao001'
put 'student','10001','info:name','Jack'
put 'student','10001','info:sex','man'

put 'student','10002','info:id','Ao002'
put 'student','10002','info:name','Little Blue'
put 'student','10002','info:sex','man'

put 'student','10003','info:id','Ao003'
put 'student','10003','info:name','Laugh laugh'
put 'student','10003','info:sex','woman'</code></pre>

<p><strong>2.5  查询数据</strong></p>

<ul><li>get查询</li>
</ul><pre class="has">
<code class="language-sql">-- 语法：get '表名','行键名'
get 'student','10001'

-- 语法：get '表名','行键名','列族:列名'
get 'student','10002','info:name'</code></pre>

<ul><li>scan查询</li>
</ul><pre class="has">
<code class="language-sql">-- 语法：scan '表名',{COLUMN =&gt; ['列族:列名','列族:列名']}
scan 'student',{COLUMN =&gt; ['info:name','info:sex']}

-- 语法：scan '表名',{STARTROW =&gt;'条件'}
scan 'student',{STARTROW =&gt;'10002'}</code></pre>

<p><strong>2.6  删除数据</strong></p>

<ul><li>delete删除</li>
</ul><pre class="has">
<code class="language-sql">-- 语法：delete '表名','行键','列族:列名'
delete 'student','10002','info:sex'</code></pre>

<ul><li>deleteall删除</li>
</ul><pre class="has">
<code class="language-sql">-- 语法：deleteall '表名','行键'
deleteall 'student','10003'</code></pre>

<h3> </h3>            </div>
                </div>