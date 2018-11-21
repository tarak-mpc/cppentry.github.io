---
layout:     post
title:      如何在spark中删除hdfs的某个文件夹
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><br></p>
<p><br></p>
<p>直接上代码</p>
<p></p>
<pre><code class="language-java">val output = new Path("hdfs://master:9000/output/");
    val hdfs = org.apache.hadoop.fs.FileSystem.get(
      new java.net.URI("hdfs://master:9000"), new org.apache.hadoop.conf.Configuration())

    // 删除输出目录
    if (hdfs.exists(output)) hdfs.delete(output, true)</code></pre><br><br><p></p>
            </div>
                </div>