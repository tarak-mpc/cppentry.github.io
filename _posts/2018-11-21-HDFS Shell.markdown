---
layout:     post
title:      HDFS Shell
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h3 id="基本格式">基本格式</h3>



<pre class="prettyprint"><code class=" hljs xml">hadoop fs -cmd <span class="hljs-tag">&lt;<span class="hljs-title">args</span>&gt;</span></code></pre>



<h4 id="ls">ls</h4>



<pre class="prettyprint"><code class=" hljs lasso">hadoop fs <span class="hljs-attribute">-ls</span> <span class="hljs-subst">/</span>
hadoop fs <span class="hljs-attribute">-ls</span> <span class="hljs-attribute">-R</span> <span class="hljs-subst">/</span> 列出所有目录</code></pre>



<h4 id="put">put</h4>



<pre class="prettyprint"><code class=" hljs r">--hdfs file 的父目录必须存在,否则不执行
hadoop fs -put &lt;local file&gt; &lt;hdfs file&gt;

--hdfs dir 必须存在
hadoop fs -put &lt;local file or dir &gt; <span class="hljs-keyword">...</span> &lt;hdfs dir&gt;

--hdfs file不能存在,从键盘读入Ctrl+D结束
hadoop fs -put - &lt;hdfs file&gt;

hadoop fs -moveFromLocal  &lt; local src &gt; <span class="hljs-keyword">...</span> &lt; hdfs dst &gt;
hadoop fs -copyFromLocal  &lt; local src &gt; <span class="hljs-keyword">...</span> &lt; hdfs dst &gt;</code></pre>

<h4 id="get">get</h4>



<pre class="prettyprint"><code class=" hljs r">hadoop fs -get &lt; hdfs file &gt; &lt; local file or dir&gt;
hadoop fs -get &lt; hdfs file or dir &gt; <span class="hljs-keyword">...</span> &lt; local  dir &gt;
hadoop fs -copyToLocal &lt; local src &gt; <span class="hljs-keyword">...</span> &lt; hdfs dst &gt;</code></pre>



<h4 id="rm">rm</h4>



<pre class="prettyprint"><code class=" hljs r">hadoop fs -rm &lt; hdfs file &gt; <span class="hljs-keyword">...</span>
hadoop fs -rm -r &lt; hdfs dir&gt;<span class="hljs-keyword">...</span></code></pre>



<h4 id="mkdir">mkdir</h4>



<pre class="prettyprint"><code class=" hljs lasso">hadoop fs <span class="hljs-attribute">-mkdir</span> <span class="hljs-attribute">-p</span> <span class="hljs-subst">&lt;</span> hdfs path<span class="hljs-subst">&gt;</span></code></pre>



<h4 id="getmerge">getmerge</h4>



<pre class="prettyprint"><code class=" hljs lasso"><span class="hljs-subst">--</span>将hdfs指定目录下所有文件排序后合并到<span class="hljs-built_in">local</span>指定的文件中，文件不存在时会自动创建，文件存在时会覆盖里面的内容
hadoop fs <span class="hljs-attribute">-getmerge</span> <span class="hljs-subst">&lt;</span> hdfs dir <span class="hljs-subst">&gt;</span>  <span class="hljs-subst">&lt;</span> <span class="hljs-built_in">local</span> file <span class="hljs-subst">&gt;</span>

<span class="hljs-subst">--</span>加上nl后，合并到<span class="hljs-built_in">local</span> file中的hdfs文件之间会空出一行
hadoop fs <span class="hljs-attribute">-getmerge</span> <span class="hljs-attribute">-nl</span>  <span class="hljs-subst">&lt;</span> hdfs dir <span class="hljs-subst">&gt;</span>  <span class="hljs-subst">&lt;</span> <span class="hljs-built_in">local</span> file <span class="hljs-subst">&gt;</span></code></pre>



<h4 id="cp">cp</h4>



<pre class="prettyprint"><code class=" hljs xml">hadoop fs -cp  <span class="hljs-tag">&lt; <span class="hljs-attribute">hdfs</span> <span class="hljs-attribute">file</span> &gt;</span>  <span class="hljs-tag">&lt; <span class="hljs-attribute">hdfs</span> <span class="hljs-attribute">file</span> &gt;</span></code></pre>



<h4 id="mv">mv</h4>



<pre class="prettyprint"><code class=" hljs r">hadoop fs -mv &lt; hdfs file &gt;  &lt; hdfs file &gt;
hadoop fs -mv  &lt; hdfs file or dir &gt;<span class="hljs-keyword">...</span>  &lt; hdfs dir &gt;</code></pre>



<h4 id="count">count</h4>



<pre class="prettyprint"><code class=" hljs xml">hadoop fs -count <span class="hljs-tag">&lt; <span class="hljs-attribute">hdfs</span> <span class="hljs-attribute">path</span> &gt;</span></code></pre>



<h4 id="du">du</h4>



<pre class="prettyprint"><code class=" hljs haml">-<span class="ruby">-显示hdfs对应路径下每个文件夹和文件的大小
</span>hadoop fs -du &lt; hdsf path&gt; 

-<span class="ruby">-显示hdfs对应路径下所有文件和的大小
</span>hadoop fs -du -s &lt; hdsf path&gt; 

-<span class="ruby">-显示hdfs对应路径下每个文件夹和文件的大小,文件的大小用方便阅读的形式表示，例如用<span class="hljs-number">64</span>M代替<span class="hljs-number">67108864</span>
</span>hadoop fs -du -h &lt; hdsf path&gt; </code></pre>



<h4 id="text">text</h4>



<pre class="prettyprint"><code class=" hljs livecodeserver"><span class="hljs-comment">--将文本文件或某些格式的非文本文件通过文本格式输出</span>
hadoop fs -<span class="hljs-keyword">text</span> &lt; hdsf <span class="hljs-built_in">file</span>&gt;</code></pre>



<h4 id="setrep">setrep</h4>



<pre class="prettyprint"><code class=" hljs lasso"><span class="hljs-subst">--</span>改变一个文件在hdfs中的副本个数，上述命令中数字<span class="hljs-number">3</span>为所设置的副本个数，<span class="hljs-attribute">-R</span>选项可以对一个人目录下的所有目录<span class="hljs-subst">+</span>文件递归执行改变副本个数的操作
hadoop fs <span class="hljs-attribute">-setrep</span> <span class="hljs-attribute">-R</span> <span class="hljs-number">3</span> <span class="hljs-subst">&lt;</span> hdfs path <span class="hljs-subst">&gt;</span></code></pre>



<h4 id="stat">stat</h4>



<pre class="prettyprint"><code class=" hljs perl">--返回对应路径的状态信息
--[<span class="hljs-keyword">format</span>]可选参数有：<span class="hljs-variable">%b</span>（文件大小），<span class="hljs-variable">%o</span>（Block大小），<span class="hljs-variable">%n</span>（文件名），<span class="hljs-variable">%r</span>（副本个数），<span class="hljs-variable">%y</span>（最后一次修改日期和时间）
hadoop fs -<span class="hljs-keyword">stat</span> [<span class="hljs-keyword">format</span>] &lt; hdfs path &gt;</code></pre>



<h4 id="tail">tail</h4>



<pre class="prettyprint"><code class=" hljs xml">hadoop fs -tail <span class="hljs-tag">&lt; <span class="hljs-attribute">hdfs</span> <span class="hljs-attribute">file</span> &gt;</span></code></pre>



<h4 id="archive">archive</h4>



<pre class="prettyprint"><code class=" hljs xml">hadoop archive -archiveName name.har -p <span class="hljs-tag">&lt; <span class="hljs-attribute">hdfs</span> <span class="hljs-attribute">parent</span> <span class="hljs-attribute">dir</span> &gt;</span> <span class="hljs-tag">&lt; <span class="hljs-attribute">src</span> &gt;</span>* <span class="hljs-tag">&lt; <span class="hljs-attribute">hdfs</span> <span class="hljs-attribute">dst</span> &gt;</span></code></pre>



<h4 id="balancer">balancer</h4>



<pre class="prettyprint"><code class=" hljs ">hdfs balancer</code></pre>



<h4 id="dfsadmin">dfsadmin</h4>



<pre class="prettyprint"><code class=" hljs haml">-<span class="ruby">-管理员可以通过dfsadmin管理<span class="hljs-constant">HDFS</span>，用法可以通过上述命令查看 hdfs dfsadmin -report
</span>hdfs dfsadmin -help


-<span class="ruby">-enter：进入安全模式；leave：离开安全模式；get：获知是否开启安全模式； wait：等待离开安全模式
</span>hdfs dfsadmin -safemode &lt; enter | leave | get | wait &gt;</code></pre>



<h4 id="distcp">distcp</h4>

<p>用来在两个HDFS之间拷贝数据</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>