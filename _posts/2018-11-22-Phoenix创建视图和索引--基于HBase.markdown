---
layout:     post
title:      Phoenix创建视图和索引--基于HBase
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1>一、HBase shell命令</h1>

<h2>1. 进入hbase shell</h2>

<pre class="has">
<code class="hljs"># Step1：进入hbase的安装路径的bin目录
cd /home/gulfmoon/apps/hbase-1.2.4/bin

# Step2：启动hbase shell
hbase shell</code></pre>

<p>启动成功后显示的信息：</p>

<p><img alt="" class="has" height="131" src="https://img-blog.csdn.net/2018081510054323?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE4MTcyMTc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1200"></p>

<h2>2. 查看hbase中所有的表</h2>

<pre class="has">
<code class="hljs">!list</code></pre>

<p><img alt="" class="has" height="74" src="https://img-blog.csdn.net/20180815100741718?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE4MTcyMTc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="867"></p>

<h2>3. help大法</h2>

<p><img alt="" class="has" height="335" src="https://img-blog.csdn.net/20180815100909772?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE4MTcyMTc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1200"></p>

<p> </p>

<h1>二、Phoenix创建视图和索引</h1>

<h2>1. 启动phoenix客户端</h2>

<pre class="has">
<code class="hljs"># Step1：进入phoenix安装路径的bin目录下
cd /home/gulfmoon/apps/apache-phoenix-4.14.0-HBase-1.2-bin/bin

# Step2: 启动phoenix
./start-sqlline.sh </code></pre>

<p>启动成功的界面如下：</p>

<p><img alt="" class="has" height="270" src="https://img-blog.csdn.net/20180815101330545?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE4MTcyMTc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1200"></p>

<h2>2. 查看hbase所有表和phoenix视图和索引清单</h2>

<pre class="has">
<code class="hljs">!tables</code></pre>

<p><img alt="" class="has" height="267" src="https://img-blog.csdn.net/20180815101650895?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE4MTcyMTc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1200"></p>

<h2>3. help大法</h2>

<p><img alt="" class="has" height="540" src="https://img-blog.csdn.net/20180815101744627?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE4MTcyMTc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1200"></p>

<h2>4. 创建视图</h2>

<pre class="has">
<code class="hljs"># Delete phoenix view
DROP VIEW IF EXISTS VIEW_TEST CASCADE;

# Create phoenix view
CREATE VIEW VIEW_TEST
(
   ROWKEY   VARCHAR PRIMARY KEY,
   "F1".TEST_ID UNSIGNED_LONG -- F1 is column family
) AS SELECT * FROM VIEW_TEST ;</code></pre>

<h2>5. 创建索引</h2>

<pre class="has">
<code class="hljs"># Delete index
DROP INDEX IF EXISTS TEST_ID_IDX ON VIEW_TEST;

# Create index
CREATE INDEX TEST_ID_IDX  ON VIEW_TEST(TEST_ID) INCLUDE( ... );</code></pre>

<p>最后，可以使用第2步中介绍的查看所有视图和索引的命令，检查是否创建成功。</p>

<p>Just enjoy it!</p>

<p> </p>

<p> </p>            </div>
                </div>