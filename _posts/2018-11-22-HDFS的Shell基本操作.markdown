---
layout:     post
title:      HDFS的Shell基本操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/sl1992/article/details/53409406				</div>
								            <div id="content_views" class="markdown_views prism-atelier-sulphurpool-light">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p></p><div class="toc"><div class="toc">
<ul>
<li><a href="#1hdfs%E7%9A%84%E6%A6%82%E8%BF%B0" rel="nofollow">1.HDFS的概述</a><ul>
<li><a href="#11namenode%E8%B4%9F%E8%B4%A3%E7%AE%A1%E7%90%86%E5%B7%A5%E4%BD%9C" rel="nofollow">1.1NameNode负责管理工作</a></li>
<li><a href="#12datanode%E8%B4%9F%E8%B4%A3%E6%95%B0%E6%8D%AE%E5%AD%98%E5%82%A8%E5%B7%A5%E4%BD%9C" rel="nofollow">1.2DataNode负责数据存储工作</a></li>
</ul>
</li>
<li><a href="#2shell%E5%AF%B9hdfs%E8%BF%9B%E8%A1%8C%E6%93%8D%E4%BD%9C" rel="nofollow">2.Shell对HDFS进行操作</a><ul>
<li><a href="#21-ls-path%E8%A1%A8%E7%A4%BA%E5%AF%B9hdfs%E7%9B%AE%E5%BD%95%E7%9A%84%E6%9F%A5%E7%9C%8B" rel="nofollow">2.1-ls <path>表示对HDFS目录的查看</path></a></li>
<li><a href="#22-mkdir-path%E5%88%9B%E5%BB%BA%E7%9B%AE%E5%BD%95" rel="nofollow">2.2-mkdir <path>创建目录</path></a></li>
<li><a href="#23-put-src-des%E4%BB%8Elinux%E4%B8%8A%E4%BC%A0%E6%96%87%E4%BB%B6%E5%88%B0hdfs" rel="nofollow">2.3-put <src> <des>从Linux上传文件到HDFS</des></src></a></li>
<li><a href="#24-text-path%E6%9F%A5%E7%9C%8B%E6%96%87%E4%BB%B6%E5%86%85%E5%AE%B9" rel="nofollow">2.4-text <path>查看文件内容</path></a></li>
<li><a href="#25-get-src-des%E4%BB%8Ehdfs%E4%B8%8B%E8%BD%BD%E6%96%87%E4%BB%B6%E5%88%B0linux" rel="nofollow">2.5-get <src> <des>从HDFS下载文件到Linux</des></src></a></li>
<li><a href="#26-rm-path%E5%88%A0%E9%99%A4%E7%9B%AE%E5%BD%95" rel="nofollow">2.6-rm <path>删除目录</path></a></li>
<li><a href="#27-appendtofile%E5%90%91%E6%96%87%E4%BB%B6%E8%BF%BD%E5%8A%A0%E6%95%B0%E6%8D%AE" rel="nofollow">2.7-appendToFile向文件追加数据</a></li>
<li><a href="#28-cp-src-des%E5%A4%8D%E5%88%B6" rel="nofollow">2.8-cp <src> <des>复制</des></src></a></li>
<li><a href="#29-mv-src-des%E7%A7%BB%E5%8A%A8%E9%87%8D%E5%91%BD%E5%90%8D" rel="nofollow">2.9-mv <src> <des>移动，重命名</des></src></a></li>
<li><a href="#210-cat-path%E6%9F%A5%E7%9C%8B%E6%96%87%E4%BB%B6%E5%86%85%E5%AE%B9" rel="nofollow">2.10-cat <path>查看文件内容</path></a></li>
<li><a href="#211-setrep-blocknum-path%E4%BF%AE%E6%94%B9%E5%89%AF%E6%9C%AC%E6%95%B0" rel="nofollow">2.11-setrep <blocknum> <path>修改副本数</path></blocknum></a></li>
<li><a href="#212-help-command%E6%9F%A5%E7%9C%8B%E5%91%BD%E4%BB%A4%E7%9A%84%E5%B8%AE%E5%8A%A9%E4%BF%A1%E6%81%AF" rel="nofollow">2.12-help [command]查看命令的帮助信息</a></li>
</ul>
</li>
</ul>
</div>
</div>




<h1 id="1hdfs的概述">1.HDFS的概述</h1>

<p>HDFS是Hadoop的分布式文件系统，提供海量数据的存储。 <br>
HDFS包括NameNode和DataNode。</p>



<h2 id="11namenode负责管理工作">1.1NameNode负责管理工作</h2>

<p>本身含有很多元数据信息，存储在fsimage文件中；还含有file与block的对应关系。 <br>
在运行的时候，这些信息全部放在内存中。 <br>
只有在运行的时候，才有的映射关系——block与datanode的映射关系，不存硬盘。</p>



<h2 id="12datanode负责数据存储工作">1.2DataNode负责数据存储工作</h2>

<p>block是hdfs进行数据存储的基本单位，默认64MB。 <br>
并不意味着文件一定是64MB，如果文件小于64MB，那么该block占用实际大小的硬盘空间。 <br>
假设有1个100MB和1个20MB文件，那么产生3个block，大小分别是64MB、36MB、20MB。 <br>
replication是hdfs为了数据安全而做的数据冗余备份，默认是3份。备份的单位是按照block进行备份。</p>



<h1 id="2shell对hdfs进行操作">2.Shell对HDFS进行操作</h1>

<p>对HDFS操作的命令格式是<code>hadoop fs</code>或<code>hdfs dfs</code>。命令格式为<code>hadoop fs -ls hdfs://cyyun:9000/</code>，也可以使用<code>hadoop fs -ls /</code>，<code>/</code>表示HDFS根目录，是<code>hdfs://cyyun:9000/</code>的简写。</p>



<h2 id="21-ls-path表示对hdfs目录的查看">2.1<code>-ls &lt;path&gt;</code>表示对HDFS目录的查看</h2>

<p><code>hadoop fs -ls /</code>显示根目录下信息 <br>
<code>-ls -d &lt;path&gt;</code> 列出目录 <br>
<code>-ls –h &lt;path&gt;</code>显示更易读的大小 <br>
<code>-ls –R &lt;path&gt;</code>递归查看</p>

<h2 id="22-mkdir-path创建目录">2.2<code>-mkdir &lt;path&gt;</code>创建目录</h2>

<p><code>hadoop fs -mkdir /test</code>创建一个test目录 <br>
<code>-mkdir -p &lt;path&gt;</code>递归创建</p>



<h2 id="23-put-src-des从linux上传文件到hdfs">2.3<code>-put &lt;src&gt; &lt;des&gt;</code>从Linux上传文件到HDFS</h2>

<p><code>hadoop fs -put /root/Documents/text1.txt /</code>将Linux目录下的text1.txt文件上传到HDFS根目录下 <br>
<code>-put -p</code>可选，保留原文件的一些特性 <br>
<code>-put -f</code>可选，直接覆盖</p>



<h2 id="24-text-path查看文件内容">2.4<code>-text &lt;path&gt;</code>查看文件内容</h2>

<p><code>hadoop fs -text /text1.txt</code></p>

<h2 id="25-get-src-des从hdfs下载文件到linux">2.5<code>-get &lt;src&gt; &lt;des&gt;</code>从HDFS下载文件到Linux</h2>

<p><code>hadoop fs -get /text1.txt /root</code></p>



<h2 id="26-rm-path删除目录">2.6<code>-rm &lt;path&gt;</code>删除目录</h2>

<p><code>-rm -R</code>递归删除 <br>
<code>hadoop fs -rm -R /test</code></p>

<h2 id="27-appendtofile向文件追加数据">2.7<code>-appendToFile</code>向文件追加数据</h2>

<p><code>hadoop fs -appendToFile /root/1.txt /input/test1.txt</code>将Linux上的文件追加到HDFS上的文件中 <br>
<code>-appendToFile -</code> <code>-</code>代表命令行输入以Ctrl+C结束</p>



<h2 id="28-cp-src-des复制">2.8<code>-cp &lt;src&gt; &lt;des&gt;</code>复制</h2>

<p><code>hadoop fs -cp /input/test1.txt /count</code></p>



<h2 id="29-mv-src-des移动重命名">2.9<code>-mv &lt;src&gt; &lt;des&gt;</code>移动，重命名</h2>

<p><code>hadoop fs /input/test1.txt /input/test2.txt</code>重命名</p>



<h2 id="210-cat-path查看文件内容">2.10<code>-cat &lt;path&gt;</code>查看文件内容</h2>

<p><code>hadoop fs -cat /text1.txt</code></p>

<h2 id="211-setrep-blocknum-path修改副本数">2.11<code>-setrep &lt;blockNum&gt; &lt;path&gt;</code>修改副本数</h2>

<p><code>hadoop fs -setrep 3 /input/test1.txt</code>修改副本数为3个 <br>
<code>-setrep -w</code> 表示等待操作执行完毕才退出 <br>
<code>-setrep -R</code>如果是目录，那么会递归设置该目录下的所有文件的副本数</p>



<h2 id="212-help-command查看命令的帮助信息">2.12<code>-help [command]</code>查看命令的帮助信息</h2>

<p><code>hadoop fs -help ls</code>查看ls的一些操作信息</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>