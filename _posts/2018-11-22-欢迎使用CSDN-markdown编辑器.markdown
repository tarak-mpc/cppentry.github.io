---
layout:     post
title:      欢迎使用CSDN-markdown编辑器
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/sean_cd/article/details/72639991				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="hadoop-hdfs实践攻略">Hadoop HDFS实践攻略</h1>

<p>理论部分：</p>

<ul>
<li><strong>HDFS 基本原理</strong></li>
<li><strong>文件读取、写入机制</strong></li>
<li><strong>元数据管理思路</strong></li>
</ul>

<p>实践部分：</p>

<ul>
<li><strong>安装实践环境</strong></li>
<li><strong>Shell 命令行操作方式</strong></li>
<li><strong>Java API操作方式</strong></li>
</ul>

<hr>



<h2 id="hdfs基本原理">HDFS基本原理</h2>



<h3 id="1-hdfs的解决思路">1. HDFS的解决思路</h3>

<blockquote>
  <p>HDFS(Hadoop Distribute File System)是一个分布式文件系统，是Hadoop的重要成员。</p>
</blockquote>

<p>HDFS是个抽象层，底层依赖很多独立的服务器，对外提供统一的文件管理功能，对于用户来讲，感觉就像在操作一台机器，感受不到HDFS下面的多台服务器。 <br>
<img src="http://s5.51cto.com/wyfs02/M01/96/76/wKioL1kiS0nxVu2mAABP9W0rrXI420.jpg" alt="这里写图片描述" title=""></p>

<p>HDFS首先会把这个文件进行分割，例如分为4块，然后分别放到不同服务器上。 <br>
<img src="http://s2.51cto.com/wyfs02/M02/96/76/wKiom1kiS2SDWg88AABpR5-2tlg330.jpg" alt="这里写图片描述" title=""></p>

<p>这样做有个好处，不怕文件太大，并且读文件的压力不会全部集中在一台服务器上。但如果某台服务器坏了，文件就读不全了。 <br>
HDFS为保证文件可靠性，会把每个文件块进行多个备份： <br>
<img src="http://s4.51cto.com/wyfs02/M01/96/76/wKiom1kiS5DBy94oAABuoeu28Co379.jpg" alt="这里写图片描述" title=""></p>

<p>这样文件的可靠性就大大增强了，即使某个服务器坏了，也可以完整读取文件。 <br>
同时还带来一个很大的好处，就是增加了文件的并发访问能力，比如多个用户读取这个文件时，都要读块1，HDFS可以根据服务器的繁忙程度，选择从那台服务器读块1</p>



<h3 id="1-元数据的管理">1. 元数据的管理</h3>

<p>HDFS中存了哪些文件? <br>
文件被分成了哪些块? <br>
每个块被放在哪台服务器上? <br>
…… <br>
这些都叫做元数据，这些元数据被抽象为一个目录树，记录了这些复杂的对应关系。这些元数据由一个单独的模块进行管理，这个模块叫做NameNode。存放文件块的真实服务器叫做DataNode，所以用户访问HDFS的过程可以理解为： <br>
用户-&gt; HDFS -&gt; NameNode -&gt; DataNode</p>



<h3 id="3-hdfs优点">3. HDFS优点</h3>

<ul>
<li>容量可以线性扩展</li>
<li>有副本机制，存储可靠性高，吞吐量增大</li>
<li>有了NameNode后，用户访问文件只需指定HDFS上的路径</li>
</ul>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>