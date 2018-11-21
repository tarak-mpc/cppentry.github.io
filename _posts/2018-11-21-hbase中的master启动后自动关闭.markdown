---
layout:     post
title:      hbase中的master启动后自动关闭
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，转载请在明显处注明来源					https://blog.csdn.net/strugglm/article/details/81067385				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p><strong>问题描述:</strong>  <br>
      原本非ha下,hbase正常启动。切换为hadoop-ha后,hbase启动无法启动master。</p>

<hr>

<p><strong>分析：</strong> <br>
   hbase的数据存放在hdfs中，元数据存放在hdfs和zookeeper中。如果重新格式化hdfs则原来的hbase数据消失。但是元数据依旧存放在zookeeper中。此时启动hbase，zookeeper中有关hbase的元数据与hdfs进行验证时，发现异常，导致关闭master，无法进行数据存储。</p>

<p><strong>hbase数据存放位置，如下：</strong> <br>
<img src="https://img-blog.csdn.net/2018071616310869?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N0cnVnZ2xt/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p><strong>解决方案：</strong> <br>
   重新格式化hdfs后，将zookeeper中有关hbase的数据删除。然后启动新的hdfs，再启动hbase后会重新注册信息到hdfs。</p>

<p><img src="https://img-blog.csdn.net/20180716163124231?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N0cnVnZ2xt/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>