---
layout:     post
title:      hadoop配置ha后，hbase配置的变化
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u014034934/article/details/76571795				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:18px;">     hadoop配置完ha后，hbase的配置有一些变化。</span></p>
<p><span style="font-size:18px;">     hbase-site.xml</span></p>
<p><span style="font-size:18px;"></span></p>
<pre><code class="language-java">&lt;property&gt;
&lt;name&gt;hbase.rootdir&lt;/name&gt;
&lt;value&gt;hdfs://myha/hbase&lt;/value&gt;&lt;!--myha为nameservices的值--&gt;
&lt;/property&gt;</code></pre>     还要把hdfs-site.xml和core-site.xml复制到hbase的conf目录下。不然会报找不到myha的错误。<br><p></p>
<p><span style="font-size:18px;">     hbase的ha配置就是在conf目录下，配置backup-masters文件，里面写备用的master。</span></p>
            </div>
                </div>