---
layout:     post
title:      Hadoop实战（6）_搭建Apache Hadoop的Eclipse开发环境
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/padluo/article/details/78391329				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>系列目录：</p>

<p><a href="http://blog.csdn.net/padluo/article/details/78236793" rel="nofollow" target="_blank">Hadoop实战（1）_阿里云搭建Hadoop2.x的伪分布式环境</a></p>

<p><a href="http://blog.csdn.net/padluo/article/details/78322222" rel="nofollow" target="_blank">Hadoop实战（2）_虚拟机搭建Hadoop的全分布模式</a></p>

<p><a href="http://blog.csdn.net/padluo/article/details/78334715" rel="nofollow" target="_blank">Hadoop实战（3）_虚拟机搭建CDH的全分布模式</a></p>

<p><a href="http://blog.csdn.net/padluo/article/details/78357977" rel="nofollow" target="_blank">Hadoop实战（4）_Hadoop的集群管理和资源分配</a></p>

<p><a href="http://blog.csdn.net/padluo/article/details/78384445" rel="nofollow" target="_blank">Hadoop实战（5）_Hadoop的运维经验</a></p>



<h1 id="hadoop插件的安装配置">Hadoop插件的安装配置</h1>

<p>1、<code>hadoop-eclipse-plugin-2.4.1.jar</code>插件放到Eclipse的plugins目录下。</p>

<p>2、Window-Preferences-Hadoop Map/Reduce选项，指定Hadoop的Home目录(Windows上模拟的Hadoop安装目录，与集群版本一致)。</p>

<p>3、打开Map/Reduce的视图，Window-Perspective-Open Perspective-Other-Map/Reduce。</p>

<p>4、Map/Reduce Locations，连接到HDFS，New Hadoop location，Location name(ApacheHadoop)</p>

<table>
<thead>
<tr>
  <th>Map/Reduce(V2) Master</th>
  <th>DFS Master</th>
</tr>
</thead>
<tbody><tr>
  <td>Host:hadoopmaster</td>
  <td>Host:hadoopmaster</td>
</tr>
<tr>
  <td>Port:9001</td>
  <td>Port:9000</td>
</tr>
</tbody></table>


<p>5、验证，Project Explorer-DFS Locations-MyHadoop。</p>



<h1 id="mapreduce验证">Map/Reduce验证</h1>

<p>New-Project-Map/Reduce Project，Project Name(WordCount)，由于之前已指定Hadoop的Home目录，自动把相关的jar包包含进来了。</p>

<p>测试数据准备，</p>



<pre class="prettyprint"><code class=" hljs haskell">[root@hadoopmaster <span class="hljs-typedef"><span class="hljs-keyword">data</span>]# hdfs dfs -cat /input/<span class="hljs-keyword">data</span>.txt</span>
<span class="hljs-type">I</span> love <span class="hljs-type">Beijing</span>
<span class="hljs-type">I</span> love <span class="hljs-type">China</span>
<span class="hljs-type">Beijing</span> is the capital <span class="hljs-keyword">of</span> <span class="hljs-type">China</span></code></pre>

<p>把官方的WordCount程序拷贝到src目录，右键Run as-Run Configurations-Java Application，指定Arguments输入输出参数，Run。</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-label">hdfs:</span>//hadoopmaster:<span class="hljs-number">9000</span>/input/data<span class="hljs-preprocessor">.txt</span>
<span class="hljs-label">hdfs:</span>//hadoopmaster:<span class="hljs-number">9000</span>/output</code></pre>

<p><strong>NullPointerException异常</strong>，把<code>%HADOOP_HOME%\bin</code>的hadoop.dll拷贝到<code>C:\Windows\System32</code>。指定<code>HADOOP_HOME</code>环境变量，把<code>HADOOP_HOME</code>下的bin目录加到系统的<code>PATH</code>路径中。重启Eclipse。</p>

<p><strong>org.apache.hadoop.io.nativeio.NativeIO$Windows.access</strong>，权限不满足Linux的要求，修改源代码NativeIO.java里面的内部类Windows的方法access，<code>retrue true</code>，不进行权限检查。</p>

<p>Run一下，到HDFS的目录下，查看是否在output目录下生成结果。</p>



<pre class="prettyprint"><code class=" hljs applescript">Beijing <span class="hljs-number">2</span>
China   <span class="hljs-number">2</span>
I   <span class="hljs-number">2</span>
capital <span class="hljs-number">1</span>
<span class="hljs-keyword">is</span>  <span class="hljs-number">1</span>
love    <span class="hljs-number">2</span>
<span class="hljs-keyword">of</span>  <span class="hljs-number">1</span>
<span class="hljs-keyword">the</span> <span class="hljs-number">1</span></code></pre>

<hr>

<p>微信公众号「数据分析」，分享数据科学家的自我修养，既然遇见，不如一起成长。</p>



<h2 id="title"><img src="https://img-blog.csdn.net/20171030112330760?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcGFkbHVv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="数据分析" title=""></h2>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>