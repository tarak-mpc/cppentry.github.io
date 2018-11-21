---
layout:     post
title:      Pentaho Kettle Loading Data into HBase
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：					https://blog.csdn.net/snj1314/article/details/79472450				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="环境准备">环境准备</h2>

<pre><code>Pentaho Kettle : 4.3.0~5.1.0
    插件：pdi-ce-5.1.0.0-752中的pentaho-big-data-plugin
    插件配置 : cdh50
Hadoop : hadoop-2.7.4
HBase : hbase-1.2.6
</code></pre>

<h2 id="创建hbase表">创建HBase表</h2>

<p>1、打开HBase Shell </p>



<pre class="prettyprint"><code class=" hljs vala"><span class="hljs-preprocessor"># hbase shell</span></code></pre>

<p>2、在HBase中创建表</p>



<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">create</span> <span class="hljs-string">'weblogs'</span>, <span class="hljs-string">'pageviews'</span></span></code></pre>

<p>3、在HBase中查询表 </p>



<pre class="prettyprint"><code class=" hljs mathematica">scan <span class="hljs-string">'weblogs'</span>, <span class="hljs-list">{LIMIT =&gt; 10}</span></code></pre>

<p>4、关闭HBase Shell </p>



<pre class="prettyprint"><code class=" hljs mel"><span class="hljs-keyword">quit</span></code></pre>



<h2 id="创建transformation加载数据到hbase">创建Transformation加载数据到HBase</h2>

<p>1、启动Ketttle，并新建一个Transformation <br>
2、添加【CSV输入】插件 <br>
<img src="https://img-blog.csdn.net/20180307154252439?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc25qMTMxNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
3、配置【CSV输入】插件，如图所示： <br>
<img src="https://img-blog.csdn.net/20180307154321386?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc25qMTMxNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
4、添加【HBase输出】插件 <br>
<img src="https://img-blog.csdn.net/20180307154343480?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc25qMTMxNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
5、连接【CSV输入】【HBase输出】插件，如图所示 <br>
<img src="https://img-blog.csdn.net/20180307154423182?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc25qMTMxNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
6、配置【HBase输出】插件</p>

<p><strong>Configure connection 基本配置</strong></p>

<pre><code>a、Zookeeper host(s)： HBase Zookeeper Hosts 用“,”分隔的列表；
b、Zookeeper port： Zookeeper hosts 端口。HBase默认为 '2181'；
</code></pre>

<p>如图所示： <br>
<img src="https://img-blog.csdn.net/20180307154458892?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc25qMTMxNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
<strong>Create/Edit mappings 配置</strong></p>

<pre><code>a、HBase table name：点击【Get table names】选择 'weblogs'；
b、Mapping name：输入'pageviews'；
c、点击 'Get incoming fields' 按钮；
d、修改Alias为key行的配置信息，修改Key为Y，修改'Column family' 与'Column name'为空，修改Type为'String'；
e、点击 'Save mapping' 按钮；
f、Mapping name：输入'pageviews'；
</code></pre>

<p>如图所示： <br>
<img src="https://img-blog.csdn.net/20180307154642215?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc25qMTMxNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
<strong>完成Configure connection 最终配置</strong></p>

<pre><code>a、HBase table name：点击'Get table names'选择 'weblogs'；
b、Mapping name：点击'Get mappings for the specified table'选择 'pageviews'；
</code></pre>

<p>如图所示： <br>
<img src="https://img-blog.csdn.net/20180307154701998?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc25qMTMxNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
7、保存转换</p>

<p>8、运行转换</p>



<h2 id="查看hbase">查看HBase</h2>

<p>1、打开HBase Shell </p>



<pre class="prettyprint"><code class=" hljs vala"><span class="hljs-preprocessor"># hbase shell</span></code></pre>

<p>2、在HBase中查询表</p>



<pre class="prettyprint"><code class=" hljs mathematica">scan <span class="hljs-string">'weblogs'</span>, <span class="hljs-list">{LIMIT =&gt; 10}</span></code></pre>

<p>3、关闭HBase Shell </p>



<pre class="prettyprint"><code class=" hljs mel"><span class="hljs-keyword">quit</span></code></pre>

<p>本文附件地址： <br>
    链接: <a href="https://pan.baidu.com/s/1-1l9YyIKHT68W3iwDqbsMg" rel="nofollow">https://pan.baidu.com/s/1-1l9YyIKHT68W3iwDqbsMg</a> 密码: 9p5h</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>