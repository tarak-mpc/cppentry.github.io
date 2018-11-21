---
layout:     post
title:      Hadoop学习之HBase的集群环境搭建
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u010113156/article/details/44872059				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p align="center"><span style="font-size:24px;color:#ff0000;">HBase的集群环境搭建</span></p>
<p align="left"><span style="font-size:18px;color:#ff0000;"><a href="http://blog.csdn.net/kisssun0608/article/details/44872027" rel="nofollow">该集成环境是在伪分布搭建的基础上搭建</a></span></p>
<p align="left"><span style="font-size:18px;">1.   修改原来的伪分布hadoop1上的hbase的配置文件</span></p>
<blockquote style="border:none;">
<p align="left"><span style="font-size:14px;"></span></p>
<pre><code class="language-plain">#cd /usr/local/hbase/conf/</code></pre><br>
待修改的文件：hbase-env.sh、hbase-site.xml、regionservers
<p></p>
<p align="left"><span style="font-size:14px;"></span></p>
<pre><code class="language-plain">#vim  hbase-env.sh</code></pre><img src="https://img-blog.csdn.net/20150404121430730?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQva2lzc3N1bjA2MDg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br>
使用搭建的zookeeper集群环境，因此hbase自带的zookeeper设置为false，不启动。
<p></p>
<p align="left"><span style="font-size:14px;"></span></p>
<pre><code class="language-plain">#vim  hbase-site.xml</code></pre>
<p></p>
<p align="left"><span style="font-size:14px;"><img src="https://img-blog.csdn.net/20150404121435254?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQva2lzc3N1bjA2MDg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></span></p>
<br>
将zookeeper集群所在的主机名，全部写进去，用逗号分开。
<p align="left"><span style="font-size:14px;"></span></p>
<pre><code class="language-plain">#vim regionservers</code></pre><br>
添加搭建hbase集群的从节点主机名，每个主机名一行
<p></p>
</blockquote>
<p align="left"><span style="font-size:18px;">2.    复制hadoop1中的hbase文件夹到hadoop2和hadoop3等其他从节点。</span></p>
<blockquote style="border:none;">
<p></p>
<pre><code class="language-plain"># scp -r  hbase  hadoop2:/usr/local/
# scp -r  hbase  hadoop3:/usr/local/</code></pre><br><br><p></p>
</blockquote>
<p align="left"><span style="font-size:18px;">3.    复制环境变量配置文件(profile)到其他从节点</span></p>
<blockquote style="border:none;">
<p></p>
<pre><code class="language-plain"># scp -r /etc/profile  hadoop2:/usr/local/</code></pre>
<p></p>
<p></p>
<pre><code class="language-plain"># scp -r /etc/profile  hadoop3:/usr/local/</code></pre>
<p></p>
</blockquote>
<p><span style="font-size:18px;">4.      启动hbase集群</span></p>
<blockquote style="border:none;">
<p><span style="font-size:14px;">要首先启动hadoop和zookeeper，然后启动hbase</span></p>
<p><span style="font-size:14px;">#start-all.sh  在hadoop1上</span></p>
<p><span style="font-size:14px;"># zkServer.sh start 每台机器上都要启动</span></p>
<p><span style="font-size:14px;">#start-hbase.sh 在hadoop1上</span></p>
</blockquote>
<p></p>
<p> <span> </span>  <img src="https://img-blog.csdn.net/20150404121438998?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQva2lzc3N1bjA2MDg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""></p>
<p><br></p>
<p><img src="https://img-blog.csdn.net/20150404121442805?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQva2lzc3N1bjA2MDg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p></p>
<p align="left"> </p>
<p align="left"> </p>
            </div>
                </div>