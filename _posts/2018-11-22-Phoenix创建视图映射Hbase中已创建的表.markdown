---
layout:     post
title:      Phoenix创建视图映射Hbase中已创建的表
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/daerzei/article/details/80356394				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p></p><div class="toc"><h3>文章目录</h3><ul><ul><li><a href="#_1" rel="nofollow">前言</a></li><li><a href="#HBase_9" rel="nofollow">准备HBase</a></li><li><a href="#PhoenixHBase_52" rel="nofollow">Phoenix创建视图映射HBase已创建的表</a></li></ul></ul></div><p></p>
<h2><a id="_1"></a>前言</h2>
<p>HBase版本：1.2.0-cdh5.10.2<br>
Phoenix版本：phoenix-4.9<br>
虽然HBase的RowKey已经进行了精心+巧妙的设计，<br>
但是对HBase来说不是不够用，于是乎就用Phoenix为HBase创建二级索引来进一步提高查询需求</p>
<p>Phoenix创建视图映射Hbase中已创建的表的时候，各种难产，网上翻了好多资料都没有搞定，这里记录一下</p>
<h2><a id="HBase_9"></a>准备HBase</h2>
<p>登陆HBase Shell</p>
<pre class=" language-shell"><code class="prism  language-shell">hbase shell
</code></pre>
<p><img src="https://img-blog.csdn.net/20180517201856238?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2RhZXJ6ZWk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="Phoenix创建视图映射Hbase中已经存在的表01"></p>
<p>在HBase里创建表，命名为USER：</p>
<pre><code># 创建表：
create 'USER', 'INFO'
</code></pre>
<p>再来点数据</p>
<pre><code>put 'USER', 'bbbZZZ1004', 'INFO:NAME', 'WeiDong'
put 'USER', 'bbbZZZ1004', 'INFO:AGE', '27'
put 'USER', 'bbbZZZ1004', 'INFO:HOME', 'ZhouKou'
put 'USER', 'bbbZZZ1005', 'INFO:NAME', 'XiaoMeng'
put 'USER', 'bbbZZZ1005', 'INFO:AGE', '27'
put 'USER', 'bbbZZZ1005', 'INFO:HOME', 'YuLin'
put 'USER', 'bbbTTT1006', 'INFO:NAME', 'GuiPing'
put 'USER', 'bbbTTT1006', 'INFO:AGE', '39'
put 'USER', 'bbbTTT1006', 'INFO:HOME', 'ZhengZhou'
</code></pre>
<p><strong>注意</strong><br>
这里HBase表名、列簇、列修饰符为什么全部都是大写的呢？<br>
因为在Phoenix里写着方便啊，Phoenix里的表名要与HBase的表名保持一致<br>
Phoenix不管你输入的是大写还是小写都默认把它转成大写的，如果要小写的话必须加上引号，<br>
比如说如果HBase里表名是大写的<br>
我们在Phoenix里写sql是这样的：<code>select * from user</code><br>
如果是小写的话就要加上引号了：<code>select * from "user"</code></p>
<p>看下插入的数据：</p>
<pre><code>scan 'USER'
</code></pre>
<p><img src="https://img-blog.csdn.net/20180517202012498?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2RhZXJ6ZWk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="Phoenix创建视图映射Hbase中已经存在的表02"><br>
我这边刚刚创建这个表，里面的数据比较少，如果你数据量很大的话可千万不能这么来，可以使用下面的命令</p>
<pre><code>scan 'USER', {LIMIT=&gt;10}
</code></pre>
<p>这样就限制最多看10条数据了</p>
<h2><a id="PhoenixHBase_52"></a>Phoenix创建视图映射HBase已创建的表</h2>
<p>切换到Phoenix的根目录下<br>
连接Phoenix</p>
<pre class=" language-shell"><code class="prism  language-shell">bin/sqlline.py cm01,cm02,cm03
</code></pre>
<p>cm01,cm02,cm03这是Zookeeper的主机名<br>
主机名已经映射了IP地址了，如果你没有映射的话就用IP吧<br>
另外，加不加2181的端口号都可以<br>
<img src="https://img-blog.csdn.net/20180517202240829?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2RhZXJ6ZWk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="Phoenix创建视图映射Hbase中已经存在的表03"></p>
<p><strong>创建视图</strong></p>
<pre><code class="language-sql"><code class="prism  language-sql"><span class="token keyword">create</span> <span class="token keyword">view</span> <span class="token keyword">user</span> <span class="token punctuation">(</span>
    pk <span class="token keyword">varchar</span> <span class="token keyword">primary</span> <span class="token keyword">key</span><span class="token punctuation">,</span>
    info<span class="token punctuation">.</span>name <span class="token keyword">varchar</span><span class="token punctuation">,</span>
    info<span class="token punctuation">.</span>age  <span class="token keyword">varchar</span><span class="token punctuation">,</span>
    info<span class="token punctuation">.</span>home <span class="token keyword">varchar</span>
<span class="token punctuation">)</span> <span class="token keyword">as</span> <span class="token keyword">select</span> <span class="token operator">*</span> <span class="token keyword">from</span> <span class="token keyword">user</span><span class="token punctuation">;</span>
</code></code></pre>
<p>一开始的时候一直没有加后面的<code>as select * from user</code>，一直创建不成功，后来无意之间把它加上就好了，看来Phoenix与传统的数据库还是有些不一样啊</p>
<p>查看是否创建成功</p>
<pre><code class="language-sql"><code class="prism  language-sql"><span class="token operator">!</span><span class="token keyword">tables</span>
</code></code></pre>
<p><img src="https://img-blog.csdn.net/20180517202334489?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2RhZXJ6ZWk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="Phoenix创建视图映射Hbase中已经存在的表04"></p>
<p>再看下表结构</p>
<pre><code class="language-sql"><code class="prism  language-sql"><span class="token operator">!</span><span class="token keyword">describe</span> <span class="token keyword">user</span>
</code></code></pre>
<p><img src="https://img-blog.csdn.net/20180517202428540?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2RhZXJ6ZWk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="Phoenix创建视图映射Hbase中已经存在的表05"></p>
<p>OK，现在可以查下数据了</p>
<pre><code class="language-sql"><code class="prism  language-sql"><span class="token keyword">select</span> <span class="token operator">*</span> <span class="token keyword">from</span> <span class="token keyword">user</span><span class="token punctuation">;</span>
</code></code></pre>
<p><img src="https://img-blog.csdn.net/20180517202451519?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2RhZXJ6ZWk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="Phoenix创建视图映射Hbase中已经存在的表06"><br>
OK，数据出来了，</p>
<p>最后咱再在HBase里插入点数据看看Phoenix的视图能否实时更新<br>
在HBase里插点数据</p>
<pre><code>put 'USER', 'cccTTT1007', 'INFO:NAME', 'YongFa'
put 'USER', 'cccTTT1007', 'INFO:AGE', '27'
put 'USER', 'cccTTT1007', 'INFO:HOME', 'LuYi'
put 'USER', 'cccTTT1008', 'INFO:NAME', 'LuiYa'
put 'USER', 'cccTTT1008', 'INFO:AGE', '27'
put 'USER', 'cccTTT1008', 'INFO:HOME', 'ZhengZhou'
put 'USER', 'cccSSS1009', 'INFO:NAME', 'KeMeng'
put 'USER', 'cccSSS1009', 'INFO:AGE', '27'
put 'USER', 'cccSSS1009', 'INFO:HOME', 'NanYang'
</code></pre>
<p>到Phoenix再查下</p>
<pre><code class="language-sql"><code class="prism  language-sql"><span class="token keyword">select</span> <span class="token operator">*</span> <span class="token keyword">from</span> <span class="token keyword">user</span><span class="token punctuation">;</span>
</code></code></pre>
<p><img src="https://img-blog.csdn.net/20180517202555980?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2RhZXJ6ZWk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="Phoenix创建视图映射Hbase中已经存在的表07"><br>
是实时更新的</p>
<p>至此完成，有用就点个赞吧</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>