---
layout:     post
title:      unknown protocol: hdfs
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载,如需转载请在明显处标明出处！					https://blog.csdn.net/qq_36291682/article/details/72026858				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>URL默认不支持hdfs </p>
<p></p>
<p>import org.apache.hadoop.fs.FsUrlStreamHandlerFactory;</p>
<p><br></p>
<p>URL.setURLStreamHandlerFactory(new FsUrlStreamHandlerFactory());  然后再使用<br></p>
<p>URL url2=new URL("hdfs://192.168.66.8:9000"); 就可以了<br></p>
            </div>
                </div>