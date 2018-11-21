---
layout:     post
title:      Hbase的基本操作（shell形式）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2><a id="Hbaseshell_0"></a>Hbase的基本操作（shell形式）</h2>
<p>1、进入和离开Hbase<br>
终端输入“hbase shell”<br>
<img src="https://img-blog.csdnimg.cn/20181101164348246.png" alt="在这里插入图片描述"><br>
进入hbase后若出现ERROR: Can’t get master address from ZooKeeper; znode data == null则很可能是需要重启虚拟机<br>
离开输入“exit”<br>
<img src="https://img-blog.csdnimg.cn/20181031110003348.png" alt="在这里插入图片描述"><br>
寻求帮助输入“help”<br>
2、list:列出Hbase的所有的表的信息<br>
<img src="https://img-blog.csdnimg.cn/20181101162119791.png" alt="在这里插入图片描述"><br>
当前状态下是没有任何表的<br>
3、创建表<br>
<em><strong>一定要用NAME，是区分大小写的</strong></em><br>
(1)创建表t1，列族为f1，列族版本号为5</p>
<pre><code>create 't1',{NAME =&gt; 'f1',version =&gt; 5}
</code></pre>
<p><img src="https://img-blog.csdnimg.cn/20181101162541204.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5OTA1OTE3,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"><br>
(2)创建表t2，3个列族分别是f1,f2,f3</p>
<pre><code>create 't2',{NAME =&gt; 'f1'},{NAME =&gt; 'f2'},{NAME =&gt; 'f3'}
</code></pre>
<p><img src="https://img-blog.csdnimg.cn/20181101164527228.png" alt="在这里插入图片描述"><br>
或者使用一下等价的命令</p>
<pre><code>create 't2', 'f1', 'f2', 'f3'
</code></pre>
<p><img src="https://img-blog.csdnimg.cn/20181101164919668.png" alt="在这里插入图片描述"><br>
（3）创建表</p>
<pre><code>create 't4','f1',{NUMREGIONS =&gt; 15,SPLITALGO =&gt; 'HexStringSplit'}
</code></pre>
<p><img src="https://img-blog.csdnimg.cn/20181101171346381.png" alt="在这里插入图片描述"><br>
（4）</p>
<pre><code>create 't5','f1',{SPLITS =&gt; ['10','20','30','40']}
</code></pre>
<p><img src="https://img-blog.csdnimg.cn/20181101171357596.png" alt="在这里插入图片描述"><br>
4、put:向表、行、列指定的单元格添加数据<br>
（1）</p>
<pre><code>put 't1','row1','f1:c1','value',1421822284898
</code></pre>
<p><img src="https://img-blog.csdnimg.cn/20181101171407726.png" alt="在这里插入图片描述"></p>
<p>5、get:通过指定表名、行、列、时间戳、时间范围和版本号来获得相应单元格的值</p>
<pre><code>get 't1','row1','f1:c1','f1:c2'
</code></pre>
<p><img src="https://img-blog.csdnimg.cn/20181105203252643.png" alt="在这里插入图片描述"></p>
<p>6、scan<br>
(1）浏览最新一列</p>
<pre><code>scan 't1'
</code></pre>
<p><img src="https://img-blog.csdnimg.cn/20181105202934737.png" alt="在这里插入图片描述"><br>
（2）浏览同一列的多个时间版本</p>
<pre><code>scan 't1',{VERSIONS =&gt; 3}
</code></pre>
<p><img src="https://img-blog.csdnimg.cn/20181112151555554.png" alt="在这里插入图片描述"></p>
<p>7、count计数</p>
<pre><code>count 't1'
</code></pre>
<p><img src="https://img-blog.csdnimg.cn/20181112151820169.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5OTA1OTE3,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<p>8、truncate清空数据</p>
<pre><code>truncate 't1'
</code></pre>
<p><img src="https://img-blog.csdnimg.cn/20181112151812375.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5OTA1OTE3,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<p>9、disable使表无效，enable使表有效</p>
<p>10、删除列族<br>
<strong>一定要disable一下表，否则无法执行alter操作，若之后还需要用表，要enable一下表</strong></p>
<pre><code>disable 'student'
alter 'student',{NAME =&gt; 'home',METHOD =&gt; 'delete'}
</code></pre>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>