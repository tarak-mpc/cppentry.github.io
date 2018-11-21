---
layout:     post
title:      How to force STORE (overwrite) to HDFS in Pig?
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>问题：</p>
<p> Pig Grunt 中，fs -rmr /apps/hive/warehouse/tmp_test 可以正常运行</p>
<p>直接运行pig -param_file/home/hive/sample.params /home/hive/preprocess.pig ，如果已经有/apps/hive/warehouse/tmp_test 文件存在，会报错；</p>
<p></p>
<pre style="border:0px;font-size:13px;overflow:auto;font-family:Consolas, Menlo, Monaco, 'Lucida Console', 'Liberation Mono', 'DejaVu Sans Mono', 'Bitstream Vera Sans Mono', 'Courier New', monospace, sans-serif;background-color:rgb(239,240,241);color:rgb(36,39,41);"><code style="border:0px;font-family:Consolas, Menlo, Monaco, 'Lucida Console', 'Liberation Mono', 'DejaVu Sans Mono', 'Bitstream Vera Sans Mono', 'Courier New', monospace, sans-serif;">2012-06-19 19:22:49,680 [main] ERROR org.apache.pig.tools.grunt.Grunt - ERROR 6000: Output Location Validation Failed for: 'hdfs://[server]/user/[user]/foo/bar More info to follow:
Output directory hdfs://[server]/user/[user]/foo/bar already exists</code></pre>
<p></p>
<p>解决方案：</p>
<p><a href="http://stackoverflow.com/questions/11110403/how-to-force-store-overwrite-to-hdfs-in-pig" rel="nofollow">http://stackoverflow.com/questions/11110403/how-to-force-store-overwrite-to-hdfs-in-pig</a></p>
<p></p>
<pre style="border:0px;font-size:13px;overflow:auto;font-family:Consolas, Menlo, Monaco, 'Lucida Console', 'Liberation Mono', 'DejaVu Sans Mono', 'Bitstream Vera Sans Mono', 'Courier New', monospace, sans-serif;background-color:rgb(239,240,241);color:rgb(36,39,41);"><code style="border:0px;font-family:Consolas, Menlo, Monaco, 'Lucida Console', 'Liberation Mono', 'DejaVu Sans Mono', 'Bitstream Vera Sans Mono', 'Courier New', monospace, sans-serif;">rmf foo/bar</code></pre>
<br><p></p>
            </div>
                </div>