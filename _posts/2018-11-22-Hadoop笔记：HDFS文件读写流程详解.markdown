---
layout:     post
title:      Hadoop笔记：HDFS文件读写流程详解
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/japson_iot/article/details/80467504				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="大数据笔记hdfs文件读写流程详解">大数据笔记：HDFS文件读写流程详解</h1>

<p>标签： 大数据</p>

<p></p><div class="toc">
<ul>
<li><a href="#%E5%A4%A7%E6%95%B0%E6%8D%AE%E7%AC%94%E8%AE%B0hdfs%E6%96%87%E4%BB%B6%E8%AF%BB%E5%86%99%E6%B5%81%E7%A8%8B%E8%AF%A6%E8%A7%A3" rel="nofollow">大数据笔记：HDFS文件读写流程详解</a><ul>
<li><a href="#%E4%B8%89%E4%B8%AA%E8%A7%92%E8%89%B2" rel="nofollow">三个角色</a></li>
<li><a href="#hdfs%E5%86%99%E6%95%B0%E6%8D%AE" rel="nofollow">HDFS写数据</a></li>
<li><a href="#hdfs%E8%AF%BB%E6%95%B0%E6%8D%AE" rel="nofollow">HDFS读数据</a></li>
<li><a href="#%E4%BC%98%E7%BC%BA%E7%82%B9" rel="nofollow">优缺点</a><ul>
<li><a href="#%E4%BC%98%E7%82%B9" rel="nofollow">优点</a></li>
<li><a href="#%E7%BC%BA%E7%82%B9" rel="nofollow">缺点</a></li>
</ul>
</li>
</ul>
</li>
</ul>
</div>


<hr>

<p>通过一个工作流的形式，展示HDFS读写流程</p>



<h2 id="三个角色">三个角色</h2>

<p>client：客户端，发起读写请求，向HDFS中存或取数据。</p>

<p>NameNode：HDFS的核心，唯一的领导，把控全局所有的请求，干什么事都要想它汇报。</p>

<p>DataNode：可以有很多，负责数据的存储。</p>



<h2 id="hdfs写数据">HDFS写数据</h2>

<ul>
<li><p>小张和client说：“我要写200MB的数据”</p></li>
<li><p>client说：“没问题！你的这些数据将会按照128M大小分隔成block，并且以3副本的形式存储！”</p></li>
<li><p>client开始将数据拆成128M的blocks，余数单独成一个block</p></li>
<li><p>client现在打算要上传第一个block，他对NameNode说：“你好，请帮我写一个128M的block，并有3个副本”</p></li>
<li><p>NameNode想了想：“3副本请求的话，我得找三个DataNode来帮我做。” 然后仔细思考了一会</p></li>
<li><p>NameNode对client说：“哥们！我给你安排了三个DataNode来做这件事，分别是DataNode1、DataNode2、DataNode3，并且我把他们已经按照离你的距离排好序了！”</p></li>
<li><p>client对DataNode1说：“现在我只向你发送数据”。</p></li>
<li><p>DataNode1接收存储数据的同时，将相同的数据发送给DataNode2，DataNode也在接收数据的同时将数据发送给DataNode3。当DataNode3写完时，说明第一个Block写完了。（流水线的方式）</p></li>
<li><p>一旦当前的Block写完了，DataNode们会向NameNode汇报：已经完成工作。NameNode就掌握了信息：Block已经成功地存储3份了。</p></li>
<li><p>Client会对剩下的block重复相同的操作。</p></li>
<li><p>所有Block都写完了，连接应该关闭了。NameNode将元信息整理并处理好。（元信息：哪些信息分别存储在哪些DataNode上了）</p></li>
</ul>



<h2 id="hdfs读数据">HDFS读数据</h2>

<ul>
<li><p>小张对client说：“我想要读数据。” client让小张稍等。</p></li>
<li><p>client对NameNode说：“我给你一个文件名，你把这个文件名的具体信息给我”</p></li>
<li><p>NameNode给了client具体信息，也就是元信息，其中包括：这些信息一共分为多少个block，每个block的每个副本都存储在哪个DataNode上（DataNode排好序）。</p></li>
<li><p>client现在知道了所要读的文件，一共需要下载多少block，也知道了每个block分别在哪个DataNode下载。</p></li>
<li><p>client按顺序地去每个DataNode下载对应的block。如果这个DataNode挂了，或者数据丢失和错误，那么client可以去其他的DataNode获取副本数据。</p></li>
</ul>



<h2 id="优缺点">优缺点</h2>



<h3 id="优点">优点</h3>

<ol>
<li><p>数据冗余多备份、硬件容错</p></li>
<li><p>处理流式的数据访问，即一次写入多次读取</p></li>
<li><p>适合存储大文件</p></li>
<li><p>构建在廉价机器上</p></li>
</ol>



<h3 id="缺点">缺点</h3>

<ol>
<li><p>低延迟的数据访问</p></li>
<li><p>不适合小文件的存储，因为只要小于128M，就是一个元数据，如果小文件很多，那么元数据很大，NameNode有压力</p></li>
</ol>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>