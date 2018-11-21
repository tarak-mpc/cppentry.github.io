---
layout:     post
title:      基于Solr的HBase实时查询方案
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/scgaliguodong123_/article/details/46822773				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h3 id="实时查询方案"><strong>实时查询方案</strong></h3>

<p>HBase+Solr+HBase-Indexer <br>
1、HBase提供海量数据存储 <br>
2、solr提供索引构建与查询 <br>
3、HBase indexer提供自动化索引构建（从HBase到Solr） <br>
<img src="https://img-blog.csdn.net/20150710205610754" alt="" title=""></p>

<h3 id="hbase-indexer"><strong>HBase Indexer</strong></h3>

<p><a href="https://github.com/NGDATA/hbase-indexer" rel="nofollow">https://github.com/NGDATA/hbase-indexer</a></p>

<p><strong>教程</strong> <br>
<a href="https://github.com/NGDATA/hbase-indexer/wiki/Tutorial" rel="nofollow">https://github.com/NGDATA/hbase-indexer/wiki/Tutorial</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>