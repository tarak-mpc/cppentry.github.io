---
layout:     post
title:      phoenix创建hbase的映射表
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1>phoenix创建hbase的映射表</h1>

<p>首先在hbase中创建表方法如下：</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180824112804224?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3MDUwOTkz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p>往表里面添加数据：</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180824112815458?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3MDUwOTkz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p>hbase查询数据：</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180824112824509?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3MDUwOTkz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p> </p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/2018082411283449?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3MDUwOTkz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p>在phoenix创建hbase的映射表：</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180824112847452?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3MDUwOTkz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p> </p>

<table><tbody><tr><td>
			<p><span style="color:#f33b45;">注意</span>：在phoenix创建hbase映射表时，如果hbase的表是小写，一定要在创建phoenix映射表时小写表名加上引号，phoenix映射表的字段不管hbase的字段是大小写都要加上引号，否则会出现如下报错：</p>

			<p><span style="color:#f33b45;">Error: ERROR 601 (42P00): Syntax error. Encountered "ROW" at line 1, column 26. (state=42P00,code=601)</span></p>
			</td>
		</tr></tbody></table><p>查看phoenix表的详细信息：</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180824112855968?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3MDUwOTkz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p>查询数据：</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180824112909452?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3MDUwOTkz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p> </p>

<p> </p>            </div>
                </div>