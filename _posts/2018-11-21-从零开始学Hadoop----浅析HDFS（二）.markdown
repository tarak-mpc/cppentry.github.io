---
layout:     post
title:      从零开始学Hadoop----浅析HDFS（二）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u010168160/article/details/51351670				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<blockquote>
  <p>      上一篇，我们简单介绍了一下Hadoop的核心之一HDFS，对其中的一些概念有了一定的了解。今天我们来看看HDFS的一些原理解析。</p>
</blockquote>



<h1 id="二原理解析">二、原理解析</h1>



<h2 id="1架构">1、架构</h2>

<p><img src="https://img-blog.csdn.net/20160508131450332" alt="这里写图片描述" title=""></p>

<blockquote>
  <p>      HDFS是一个主/从（Mater/Slave）体系结构，从最终用户的角度来看，它就像传统的文件系统一样，可以通过目录路径对文件执行CRUD（Create、Read、Update和Delete）操作。但由于分布式存储的性质，HDFS集群拥有一个NameNode和一些DataNode。</p>
  
  <p>      NameNode管理文件系统的元数据，DataNode存储实际的数据。客户端通过同NameNode和DataNodes的交互访问文件系统。客户端联系NameNode以获取文件的元数据，而真正的文件I/O操作是直接和DataNode进行交互的。</p>
</blockquote>



<h2 id="2管理员更新日志">2、管理员更新日志</h2>

<p><img src="https://img-blog.csdn.net/20160509125515931" alt="这里写图片描述" title=""> <br>
 - <strong>CheckPoint的时间点</strong></p>



<pre class="prettyprint"><code class=" hljs avrasm">fs<span class="hljs-preprocessor">.checkpoint</span><span class="hljs-preprocessor">.period</span> 指定两次checkpoint的最大时间间隔，默认<span class="hljs-number">3600</span>秒。 </code></pre>



<pre class="prettyprint"><code class=" hljs avrasm">fs<span class="hljs-preprocessor">.checkpoint</span><span class="hljs-preprocessor">.size</span>    规定edits文件的最大值，一旦超过这个值则强制checkpoint，不管是否到达最大时间间隔。默认大小是<span class="hljs-number">64</span>M。</code></pre>

<ul>
<li><strong>流程解析</strong></li>
</ul>



<pre class="prettyprint"><code class=" hljs cs"><span class="hljs-number">1</span>、每隔两个小时，或者edits文件超过最大值时，SecondaryNameNode通知NameNode切换edits，
此时在NameNode中生成一个名为edits.<span class="hljs-keyword">new</span>的新edits，在fsimage被替换之前，所有的操作都会写到edits.<span class="hljs-keyword">new</span>中。</code></pre>



<pre class="prettyprint"><code class=" hljs ">2、SecondaryNameNode通过Http协议复制edits和fsimage到SecondaryNameNode中</code></pre>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-number">3</span>、SecondaryNameNode将fsimage导入内存，用edits中的操作，生成新的fsimage<span class="hljs-preprocessor">.ckpt</span>文件</code></pre>



<pre class="prettyprint"><code class=" hljs livecodeserver"><span class="hljs-number">4</span>、SecondaryNameNode将新的fsimage复制，并通过<span class="hljs-keyword">http</span> Post传给NameNode</code></pre>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-number">5</span>、NameNode将接收到的新的fsimage<span class="hljs-preprocessor">.ckpt</span>替换掉之前的fsimage,同时将新的edits<span class="hljs-preprocessor">.new</span>重命名为edits，将之前的替换掉</code></pre>



<h2 id="3读文件">3、读文件</h2>

<p><img src="https://img-blog.csdn.net/20160509124704812" alt="这里写图片描述" title=""></p>



<pre class="prettyprint"><code class=" hljs fsharp">初始化FileSystem，然后客户端(client)用FileSystem的<span class="hljs-keyword">open</span>()函数打开文件</code></pre>



<pre class="prettyprint"><code class=" hljs ">FileSystem用RPC调用元数据节点，得到文件的数据块信息，对于每一个数据块，元数据节点返回保存数据块的数据节点的地址。</code></pre>



<pre class="prettyprint"><code class=" hljs bash">FileSystem返回FSDataInputStream给客户端，用来读取数据，客户端调用stream的<span class="hljs-built_in">read</span>()函数开始读取数据。</code></pre>



<pre class="prettyprint"><code class=" hljs haskell"><span class="hljs-type">DFSInputStream</span>连接保存此文件第一个数据块的最近的数据节点，<span class="hljs-typedef"><span class="hljs-keyword">data</span>从数据节点读到客户端<span class="hljs-container">(<span class="hljs-title">client</span>)</span></span></code></pre>



<pre class="prettyprint"><code class=" hljs ">当此数据块读取完毕时，DFSInputStream关闭和此数据节点的连接，然后连接此文件下一个数据块的最近的数据节点。</code></pre>



<pre class="prettyprint"><code class=" hljs livecodeserver">当客户端读取完毕数据的时候，调用FSDataInputStream的<span class="hljs-built_in">close</span>函数。
在读取数据的过程中，如果客户端在与数据节点通信出现错误，则尝试连接包含此数据块的下一个数据节点。</code></pre>



<pre class="prettyprint"><code class=" hljs ">失败的数据节点将被记录，以后不再连接。</code></pre>



<h2 id="4写文件">4、写文件</h2>

<p><img src="https://img-blog.csdn.net/20160509124831438" alt="这里写图片描述" title=""></p>



<pre class="prettyprint"><code class=" hljs sql">初始化FileSystem，客户端调用<span class="hljs-operator"><span class="hljs-keyword">create</span>()来创建文件</span></code></pre>



<pre class="prettyprint"><code class=" hljs ">FileSystem用RPC调用元数据节点，在文件系统的命名空间中创建一个新的文件，元数据节点首先确定文件原来不存在，
并且客户端有创建文件的权限，然后创建新文件。</code></pre>



<pre class="prettyprint"><code class=" hljs ">FileSystem返回DFSOutputStream，客户端用于写数据，客户端开始写入数据。</code></pre>



<pre class="prettyprint"><code class=" hljs lasso">DFSOutputStream将数据分成块，写入<span class="hljs-built_in">data</span> <span class="hljs-built_in">queue</span>。<span class="hljs-built_in">data</span> <span class="hljs-built_in">queue</span>由<span class="hljs-built_in">Data</span> Streamer读取，
并通知元数据节点分配数据节点，用来存储数据块(每块默认复制<span class="hljs-number">3</span>块)。
分配的数据节点放在一个pipeline里。<span class="hljs-built_in">Data</span> Streamer将数据块写入pipeline中的第一个数据节点。
第一个数据节点将数据块发送给第二个数据节点。第二个数据节点将数据发送给第三个数据节点。</code></pre>



<pre class="prettyprint"><code class=" hljs lasso">DFSOutputStream为发出去的数据块保存了ack <span class="hljs-built_in">queue</span>，等待pipeline中的数据节点告知数据已经写入成功。</code></pre>



<pre class="prettyprint"><code class=" hljs livecodeserver">当客户端结束写入数据，则调用stream的<span class="hljs-built_in">close</span>函数。
此操作将所有的数据块写入pipeline中的数据节点，并等待ack queue返回成功。
最后通知元数据节点写入完毕。</code></pre>



<pre class="prettyprint"><code class=" hljs lasso">如果数据节点在写入的过程中失败，关闭pipeline，将ack <span class="hljs-built_in">queue</span>中的数据块放入<span class="hljs-built_in">data</span> <span class="hljs-built_in">queue</span>的开始，
当前的数据块在已经写入的数据节点中被元数据节点赋予新的标示，则错误节点重启后能够察觉其数据块是过时的，会被删除。
失败的数据节点从pipeline中移除，另外的数据块则写入pipeline中的另外两个数据节点。
元数据节点则被通知此数据块是复制块数不足，将来会再创建第三份备份。</code></pre>



<h2 id="总结">总结：</h2>

<blockquote>
  <p>      今天我们接着上次介绍了一下HDFS的概念之后，我们对HDFS的架构，内部构造以及运行原理做了一些解析，下次我们将在实战中熟练使用HDFS做一些例子。</p>
</blockquote>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>