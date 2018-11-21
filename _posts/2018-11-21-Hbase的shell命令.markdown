---
layout:     post
title:      Hbase的shell命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_25371579/article/details/50900607				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="hbase的shell命令">HBase的shell命令</h1>



<h2 id="1查询服务器状态">1、查询服务器状态</h2>

<pre><code>hbase(main):002:0&gt; status
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-15/25987750.jpg" alt="" title=""> </p>



<h2 id="查看版本">、查看版本</h2>

<pre><code>hbase(main):008:0&gt; version
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-15/25428742.jpg" alt="" title=""> </p>



<h2 id="2创建表">2、创建表</h2>

<pre><code>hbase(main):003:0&gt; create 'bdclass' , 'bdinfo' , 'bdother'
hbase(main):006:0&gt; create 'score','grade','course'
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-15/24421376.jpg" alt="" title=""> <br>
<img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-15/17824909.jpg" alt="" title=""></p>



<h2 id="3查看当前hbase中哪些表list">3、查看当前HBase中哪些表list</h2>

<pre><code>hbase(main):001:0&gt; list
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-15/88161536.jpg" alt="" title=""> </p>



<h2 id="4查看表的详细描述">4、查看表的详细描述</h2>

<pre><code>hbase(main):005:0&gt; describe 'bdclass'
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-15/38372480.jpg" alt="" title=""> </p>



<h2 id="5添加记录">5、添加记录</h2>

<pre><code>hbase(main):007:0&gt; put 'bdclass','rowkey1','bdinfo:name','hadoop'   

hbase(main):008:0&gt; put 'bdclass','rowkey1','bdinfo:age','30'    

hbase(main):009:0&gt; put 'bdclass','rowkey1','bdinfo:addr','apache'   

hbase(main):010:0&gt; put 'bdclass','rowkey1','bdinfo:name','hadoop1'
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-15/27798994.jpg" alt="" title=""> </p>



<h2 id="6扫描全表查看所有记录">6、扫描全表，查看所有记录</h2>

<pre><code>hbase(main):011:0&gt; scan 'bdclass'
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-15/13259145.jpg" alt="" title=""> </p>



<h2 id="7查看记录">7、查看记录</h2>

<pre><code>hbase(main):012:0&gt; get 'bdclass' , 'rowkey1' , 'bdinfo'
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-15/7805717.jpg" alt="" title=""> </p>



<h2 id="8查看记录">8、查看记录</h2>

<pre><code>hbase(main):013:0&gt; get 'bdclass' , 'rowkey1' , 'bdinfo:name'
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-15/92819240.jpg" alt="" title=""></p>



<h2 id="查看某个表某个列中所有数据">查看某个表某个列中所有数据</h2>

<pre><code>hbase(main):009:0&gt; scan 'bdclass',{FILTER=&gt; "PrefixFilter('rowkey1')",COLUMN =&gt; 'bdinfo:name' ,VERSIONS =&gt;10} 
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-15/37149970.jpg" alt="" title=""></p>

<pre><code>hbase(main):011:0&gt; scan 'bdclass',{FILTER=&gt; "PrefixFilter('rowkey1')",COLUMN =&gt; 'bdinfo:addr' ,VERSIONS =&gt;10}
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-15/71763981.jpg" alt="" title=""></p>



<h2 id="9查看表中的记录总数">9、查看表中的记录总数</h2>

<pre><code>hbase(main):015:0&gt; count 'bdclass' 
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-15/77689144.jpg" alt="" title=""> <br>
<img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-15/367483.jpg" alt="" title=""></p>



<h2 id="10清空表中数据">10、清空表中数据</h2>

<pre><code>hbase(main):012:0&gt; disable 'bdclass'
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-15/68738737.jpg" alt="" title=""></p>



<h2 id="11删除表">11、删除表</h2>

<pre><code>hbase(main):013:0&gt; drop 'bdclass'
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-15/43161790.jpg" alt="" title=""></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>