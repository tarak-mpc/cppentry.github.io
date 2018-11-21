---
layout:     post
title:      Hadoop实践（三）---使用HDFS的Java API
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/Wee_Mita/article/details/52750018				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<pre><code>HDFS Java API 可以用于任何Java程序与HDFS交互，该API使我们能够从其他Java程序中利用到存储在HDFS中的数据，也能够使用其他非Hadoop的计算框架处理该数据

为了以编程方式与HDFS进行交互，首先需要得到当前配置文件系统的句柄，实例化一个Configuration对象，并获得一个Hadoop环境中的FileSystem句柄，它将指向当前环境的HDFS NameNode
</code></pre>



<pre class="prettyprint"><code class=" hljs vhdl"><span class="hljs-keyword">Configuration</span> conf = <span class="hljs-keyword">new</span> <span class="hljs-keyword">Configuration</span>();
FileSystem fs = FileSystem.get(conf);</code></pre>

<p>FileSystem.create(filepath);方法会在指定的路径创建一个新的文件，并提供一个到新创建的文件的FSDataOutputStream对象；FSDataOutputStream封装了java.io.DataOutputStream，并允许程序向文件中写入基本Java数据类型；如果该文件存在FileSystem.create()方法会覆盖该文件</p>



<pre class="prettyprint"><code class=" hljs avrasm">Path file = new Path(<span class="hljs-string">"demo.txt"</span>)<span class="hljs-comment">;</span>
FSDataOutputStream outStream = fs<span class="hljs-preprocessor">.create</span>(file)<span class="hljs-comment">;</span>
outStream<span class="hljs-preprocessor">.writeUTF</span>(<span class="hljs-string">"Welcome to HDFS Java API !!!"</span>)<span class="hljs-comment">;</span>
outStream<span class="hljs-preprocessor">.close</span>()<span class="hljs-comment">;</span></code></pre>

<p>在该例子中，该文件将在HDFS中相对于用户的主目录进行创建，产生类似/user/user_name/demo.txt的路径 <br>
FileSyste.open(filePath)打开给定文件的FSDataInputStream，FSDataInputStream封装了java.io.DataInputStream，允许程序从文件中读取剧本Java数据类型</p>



<pre class="prettyprint"><code class=" hljs haskell"><span class="hljs-type">FSDataInputStream</span> inStream = fs.open(file);
<span class="hljs-type">String</span> <span class="hljs-typedef"><span class="hljs-keyword">data</span> = inStream.readUTF<span class="hljs-container">()</span>;</span>
<span class="hljs-type">System</span>.out.println(<span class="hljs-typedef"><span class="hljs-keyword">data</span>);</span>
<span class="hljs-title">inStream</span>.close();</code></pre>



<h2 id="配置文件系统对象">配置文件系统对象</h2>

<p>可以在Hadoop的环境之外使用HDFS的Java API，这样做必须显示配置HDFS的NameNode和端口，以下是几种进行该项配置的方法： <br>
在获得FileSystem对象之前加载Configuration对象的配置文件，需要确保所有的Hadoop和依赖库都添加在类路径中</p>



<pre class="prettyprint"><code class=" hljs vhdl"><span class="hljs-keyword">Configuration</span> conf = <span class="hljs-keyword">new</span> <span class="hljs-keyword">Configuration</span>();
conf.addResource(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">".../hadoop/conf/core-site.xml"</span>));
conf.addResource(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">".../hadoop/conf/hdfs-site.xml"</span>));
FileSystem fs = FileSystem.get(conf);</code></pre>

<p>通过指定NameNode和端口，将NAMENODE_HOSTNAME和PORT替换为HDFS安装的NameNode的主机名和端口</p>



<pre class="prettyprint"><code class=" hljs cs">Configuration conf = <span class="hljs-keyword">new</span> Configuration();
conf.<span class="hljs-keyword">set</span>(<span class="hljs-string">"fs.default.name"</span>,<span class="hljs-string">"hdfs://NAMENODE_HOSTNAME:PORT"</span>);
FileSystem fs = FileSystem.<span class="hljs-keyword">get</span>(conf);</code></pre>

<p><strong>HDFS的文件系统API是一种支持多个文件系统的抽象，如果上述程序无法找到有效的HDFS配置，它将会指向本地文件系统，而不是HDFS，可以使用getUti()函数识别FileSystem对象的当前文件系统，在使用正确的HDFS配置文件的情况下，返回hdfs://your_namenode:port，在使用本地文件系统的情况下，则返回file:///</strong></p>



<pre class="prettyprint"><code class=" hljs avrasm">FileSystem fs = FileSystem<span class="hljs-preprocessor">.get</span>(conf)<span class="hljs-comment">;</span>
fs<span class="hljs-preprocessor">.getUri</span>()<span class="hljs-comment">;</span></code></pre>



<h2 id="获取文件的数据块列表">获取文件的数据块列表</h2>

<p>FileSystem对象的getFileBlockLocations()函数，可以用来获取存储在HDFS中的文件数据块的列表，同时可以获取存储块的主机名和块的偏移量，<strong>如果计划使用Hadoop MapReduce之外的其他框架执行文件数据的任何数据本地化操作，那么这些信息非常有用</strong></p>



<pre class="prettyprint"><code class=" hljs vhdl"><span class="hljs-keyword">Configuration</span> conf = <span class="hljs-keyword">new</span> <span class="hljs-keyword">Configuration</span>();
FileSystem fs = FileSystem.get(conf);
Path <span class="hljs-keyword">file</span> = <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"demo.txt"</span>);
FileStatus fileStatus = fs.getFileStatus(<span class="hljs-keyword">file</span>);
eBlockLocations[]= blocks = fs.getFileBlockLocations(fileStatus,<span class="hljs-number">0</span>,fileStatus.getLen());</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>