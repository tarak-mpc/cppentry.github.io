---
layout:     post
title:      HDFS Corrupt blocks 解决方法
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/lingbo229/article/details/81128316				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1><a href="http://www.cnblogs.com/admln/p/5822004.html" rel="nofollow" id="cb_post_title_url">查看修复HDFS中损坏的块</a></h1>

<p>检测缺失块</p>

<pre>
<code class="language-html hljs">1 hdfs fsck -list-corruptfileblocks</code></pre>

<pre>
<code class="language-html hljs">1 hdfs fsck / | egrep -v '^\.+$' | grep -v eplica</code></pre>

<p>查看上面某一个文件的情况</p>

<pre>
<code class="language-html hljs">1 hdfs fsck /path/to/corrupt/file -locations -blocks -files</code></pre>

<h1>解决方法</h1>

<ol><li>如果文件不重要，可以直接删除此文件(hdfs fsck -delete)；或删除后重新复制一份到集群中</li>
	<li>如果不能删除，需要从上面命令中找到发生在哪台机器上，然后到此机器上查看日志。</li>
</ol><p> </p>

<h1>常用命令介绍：</h1>

<p>1、查看节点、hdfs、丢失的数据块 命令：hadoop dfsadmin -report </p>

<p>2、查看文件系统的健康状况：hdfs fsck --&lt;path&gt; [-options]</p>

<p>Usage: DFSck &lt;path&gt; [-move | -delete | -openforwrite] [-files [-blocks [-locations | -racks]]]<br>
        &lt;path&gt;             检查这个目录中的文件是否完整</p>

<p>        -move               破损的文件移至/lost+found目录<br>
        -delete             删除破损的文件</p>

<p>        -openforwrite   打印正在打开写操作的文件</p>

<p>        -files                 打印正在check的文件名</p>

<p>        -blocks             打印block报告 （需要和-files参数一起使用）</p>

<p>        -locations         打印每个block的位置信息（需要和-files参数一起使用）</p>

<p>        -racks               打印位置信息的网络拓扑图 （需要和-files参数一起使用）</p>

<p>hadoop  fsck /</p>

<p>用这个命令可以检查整个文件系统的健康状况，但是要注意它不会主动恢复备份缺失的block，这个是由NameNode单独的线程异步处理的。</p>

<p>3、如果hadoop不能自动恢复，则只能删除 corrupted blocks;</p>

<p>     hdfs fsck -delete</p>            </div>
                </div>