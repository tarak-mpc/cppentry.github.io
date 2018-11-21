---
layout:     post
title:      spark 访问hive表报错
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-github-gist">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>spark访问hive表： 在spark/conf目录下：引入hive配置文件</p>
<pre><code>ln -sf /soft/hive/conf/hive-site.xml /soft/spark/conf/
</code></pre>
<h1><a id="1_5"></a>错误1：</h1>
<p>Hive Schema version 1.2.0 does not match metastore’s schema version 2.3.0<br>
原因： hive版本不一致， 查看hive在mysql中的注册信息如下<br>
<img src="https://img-blog.csdn.net/20181025175955642?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2V5ZW9mZWFnbGU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"> 解决办法： 降低hive的版本, 修改 SCHEMA_VERSION的值为2.1.1<br>
<img src="https://img-blog.csdn.net/20181025180147468?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2V5ZW9mZWFnbGU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>
<h1><a id="2_10"></a>错误2：</h1>
<p>Exception thrown obtaining schema column information from datastore<br>
org.datanucleus.exceptions.NucleusDataStoreException: Exception thrown obtaining schema column information from datastore…<br>
Caused by: java.sql.SQLException: Column name pattern can not be NULL or empty.<br>
原因： 是mysql驱动版本太高，删除后重新导入低版本的驱动<br>
<img src="https://img-blog.csdn.net/2018102518044616?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2V5ZW9mZWFnbGU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"><br>
<img src="https://img-blog.csdn.net/20181025180523545?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2V5ZW9mZWFnbGU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"><br>
重启spark-shell, 加载hive文件成功！<br>
<img src="https://img-blog.csdn.net/20181025180639203?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2V5ZW9mZWFnbGU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>