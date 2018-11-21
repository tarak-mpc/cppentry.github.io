---
layout:     post
title:      联邦HDFS（HDFS Federation）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/Lin_wj1995/article/details/78933783				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<blockquote>
  <p>对于联邦HDFS的一些概念，我这里先不进行介绍；下面直接记录我对理解联邦HDFS时产生的疑惑以及疑惑的答案。</p>
</blockquote>

<h2 id="问题产生">问题产生：</h2>

<p>首先在我的理论知识中，联邦HDFS，就是有多个active的namenode，不同的namenode掌管hdfs中不同路径下文件的数据，互相隔离，互不影响。</p>

<p>这里理论的论证就是，在CDH页面配置联邦HDFS时，需要指定装载点，即这个nameservice负责管理哪个目录下的数据。这让我肯定了我上面的理论知识，但是</p>

<p>在查看<a href="http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/Federation.html" rel="nofollow" target="_blank">官网联邦HDFS介绍</a>时，发现配置中没有对不同的nameservice指定装载点，然后我查找了一些博客，发现他们配置中也没有指定各自的装载点，但是namenode却能够启动成功，也就是说没有指定明确的装载点没问题，是正确的，这让我对我上面建立的理论知识产生了冲击。</p>

<p>于是产生了如下问题：</p>

<ul>
<li>因为不同的nameservice负责管理的名称空间不同，但是官网中的配置没有去指定哪个nameservice负责哪个名称空间，那么他们是如何去分配的？</li>
</ul>

<h2 id="解决过程">解决过程：</h2>

<p>通过翻阅《Hadoop权威指南》，里面有一句话，“每个namenode管理文件系统命名空间中的一部分。例如，一个namenode可能管理/user目录下的所有文件，而另一个namenode可能管理/share目录下的所有文件”</p>

<p>发现之前的观点是正确的，但是官网又是怎么回事？那请求数据时，如何判断是在哪个nameservice中？还是代码指定，手动指定？？</p>

<p>于是我接着看《Hadoop权威指南》，发现有viewFs这个东东，于是去<a href="http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/ViewFs.html" rel="nofollow">官网</a>看一下是什么东东来的，我发现，这不就是我之前所有疑惑的答案吗？</p>

<p>附上viewFs介绍的原话：</p>

<blockquote>
  <p>The View File System (ViewFs) provides a way to manage multiple Hadoop file system namespaces (or namespace volumes). It is particularly useful for clusters having multiple namenodes, and hence multiple namespaces, in HDFS Federation. ViewFs is analogous to client side mount tables in some Unix/Linux systems. ViewFs can be used to create personalized namespace views and also per-cluster common views.</p>
</blockquote>

<p>附上viewFs的结构图：</p>

<p><img src="https://img-blog.csdn.net/20171229173614202?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvTGluX3dqMTk5NQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>也就是说viewFs会帮我们统筹所有的namenode，并且有一个数据表将文件路径映射到namenode！</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>