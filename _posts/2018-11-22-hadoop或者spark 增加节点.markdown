---
layout:     post
title:      hadoop或者spark 增加节点
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/lxhandlbb/article/details/53843674				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>增加节点。</p>
<p>复制一个虚拟机。</p>
<p>配置hosts文件，检查是否能ssh通。</p>
<p> </p>
<p><strong>Hadoop:</strong></p>
<div>
<pre></pre><ol style="color:rgb(30,52,123);"><li style="color:rgb(190,190,197);"><code><span style="color:rgb(72,72,76);">scp </span><span style="color:rgb(147,161,161);">/</span><span style="color:rgb(72,72,76);">usr</span><span style="color:rgb(147,161,161);">/</span><span style="color:rgb(72,72,76);">local</span><span style="color:rgb(147,161,161);">/</span><span style="color:rgb(72,72,76);">hadoop</span><span style="color:rgb(147,161,161);">/</span><span style="color:rgb(72,72,76);">hadoop</span><span style="color:rgb(147,161,161);">-</span><span style="color:rgb(25,95,145);">2.6</span><span style="color:rgb(147,161,161);">.</span><span style="color:rgb(25,95,145);">0</span><span style="color:rgb(147,161,161);">/</span><span style="color:rgb(72,72,76);">etc</span><span style="color:rgb(147,161,161);">/</span><span style="color:rgb(72,72,76);">hadoop</span><span style="color:rgb(147,161,161);">/</span><span style="color:rgb(72,72,76);">slaves root@Worker1</span><span style="color:rgb(147,161,161);">:/</span><span style="color:rgb(72,72,76);">usr</span><span style="color:rgb(147,161,161);">/</span><span style="color:rgb(72,72,76);">local</span><span style="color:rgb(147,161,161);">/</span><span style="color:rgb(72,72,76);">hadoop</span><span style="color:rgb(147,161,161);">/</span><span style="color:rgb(72,72,76);">hadoop</span><span style="color:rgb(147,161,161);">-</span><span style="color:rgb(25,95,145);">2.6</span><span style="color:rgb(147,161,161);">.</span><span style="color:rgb(25,95,145);">0</span><span style="color:rgb(147,161,161);">/</span><span style="color:rgb(72,72,76);">etc</span><span style="color:rgb(147,161,161);">/</span><span style="color:rgb(72,72,76);">hadoop</span><span style="color:rgb(147,161,161);">/</span><span style="color:rgb(72,72,76);">slaves</span></code></li></ol>
</div>
<div><br></div>
<div><strong>格式化：</strong></div>
<div><strong><br></strong></div>
<div>
<div>
<pre></pre><ol style="color:rgb(30,52,123);"><li style="color:rgb(190,190,197);"><code><span style="color:rgb(72,72,76);">hadoop namenode </span><span style="color:rgb(147,161,161);">–</span><span style="color:rgb(72,72,76);">format</span><span style="color:rgb(147,161,161);">/</span><span style="color:rgb(72,72,76);">hdfs namenode </span><span style="color:rgb(147,161,161);">-</span><span style="color:rgb(72,72,76);">format</span></code></li></ol>
</div>
<div><br></div>
</div>
<p> </p>
<p><strong>Spark:</strong></p>
<div>
<pre></pre><ol style="color:rgb(30,52,123);"><li style="color:rgb(190,190,197);"><code><span style="color:rgb(147,161,161);">scp /</span><span style="color:rgb(72,72,76);">usr</span><span style="color:rgb(147,161,161);">/</span><span style="color:rgb(72,72,76);">local</span><span style="color:rgb(147,161,161);">/</span><span style="color:rgb(72,72,76);">spark</span><span style="color:rgb(147,161,161);">/</span><span style="color:rgb(72,72,76);">spark</span><span style="color:rgb(147,161,161);">-</span><span style="color:rgb(25,95,145);">1.6</span><span style="color:rgb(147,161,161);">.</span><span style="color:rgb(25,95,145);">0</span><span style="color:rgb(147,161,161);">-</span><span style="color:rgb(72,72,76);">bin</span><span style="color:rgb(147,161,161);">-</span><span style="color:rgb(72,72,76);">hadoop2</span><span style="color:rgb(147,161,161);">.</span><span style="color:rgb(25,95,145);">6</span><span style="color:rgb(147,161,161);">/</span><span style="color:rgb(72,72,76);">conf</span><span style="color:rgb(147,161,161);">/</span><span style="color:rgb(72,72,76);">slaves </span></code><span style="color:rgb(72,72,76);">root@Worker1</span><span style="color:rgb(147,161,161);">:</span><span style="color:rgb(147,161,161);">/</span><span style="color:rgb(72,72,76);">usr</span><span style="color:rgb(147,161,161);">/</span><span style="color:rgb(72,72,76);">local</span><span style="color:rgb(147,161,161);">/</span><span style="color:rgb(72,72,76);">spark</span><span style="color:rgb(147,161,161);">/</span><span style="color:rgb(72,72,76);">spark</span><span style="color:rgb(147,161,161);">-</span><span style="color:rgb(25,95,145);">1.6</span><span style="color:rgb(147,161,161);">.</span><span style="color:rgb(25,95,145);">0</span><span style="color:rgb(147,161,161);">-</span><span style="color:rgb(72,72,76);">bin</span><span style="color:rgb(147,161,161);">-</span><span style="color:rgb(72,72,76);">hadoop2</span><span style="color:rgb(147,161,161);">.</span><span style="color:rgb(25,95,145);">6</span><span style="color:rgb(147,161,161);">/</span><span style="color:rgb(72,72,76);">conf</span><span style="color:rgb(147,161,161);">/</span><span style="color:rgb(72,72,76);">slaves</span></li></ol>
</div>
<div><br></div>
<p><br></p>
<p> </p>
<p>出现问题：</p>
<p>只显示3个DataNode</p>
<p><a href="http://www.cnblogs.com/wanqieddy/archive/2012/08/02/2619928.html" rel="nofollow">http://www.cnblogs.com/wanqieddy/archive/2012/08/02/2619928.html</a></p>
<p> </p>
<p><span style="color:#ff0000;">temp/data/里面的VERSION文件不同。导致有节点未能注册到Hadoop集群manager上！！！</span></p>
<p><strong><span style="color:rgb(57,57,57);">scp </span><span style="color:rgb(57,57,57);">对拷文件夹</span><span style="color:rgb(57,57,57);"> </span><span style="color:rgb(57,57,57);">和</span><span style="color:rgb(57,57,57);"> </span><span style="color:rgb(57,57,57);">文件夹下的所有文件</span><span style="color:rgb(57,57,57);"> </span><span style="color:rgb(57,57,57);">对拷文件并重命名</span></strong></p>
<p><span style="color:rgb(57,57,57);"> </span></p>
<p><span style="color:rgb(57,57,57);">对拷文件夹</span><span style="color:rgb(57,57,57);"> (</span><span style="color:rgb(57,57,57);">包括文件夹本身</span><span style="color:rgb(57,57,57);">)</span></p>
<p><span style="color:rgb(57,57,57);">scp -r   /home/wwwroot/www/charts/util root@192.168.1.65:/home/wwwroot/limesurvey_back/scp</span></p>
<p><span style="color:rgb(57,57,57);">对拷文件夹下所有文件</span><span style="color:rgb(57,57,57);"> (</span><span style="color:rgb(57,57,57);">不包括文件夹本身</span><span style="color:rgb(57,57,57);">)</span></p>
<p><span style="color:rgb(57,57,57);">scp   /home/wwwroot/www/charts/util/* root@192.168.1.65:/home/wwwroot/limesurvey_back/scp</span></p>
<p><span style="color:rgb(57,57,57);"> </span></p>
<p><span style="color:rgb(57,57,57);"> </span><span style="color:rgb(57,57,57);">对拷文件并重命名</span></p>
<p><span style="color:rgb(57,57,57);">scp   /home/wwwroot/www/charts/util/a.txt root@192.168.1.65:/home/wwwroot/limesurvey_back/scp/b.text</span></p>
<p> </p>
<p>修改slaves</p>
<p> </p>
<p>root@Master:~# scp /usr/local/spark/spark-1.6.0-bin-hadoop2.6/conf/slaves root@Worker1:/usr/local/spark/spark-1.6.0-bin-hadoop2.6/conf/slaves</p>
<p>slaves100%  899     0.9KB/s00:00   </p>
<p>root@Master:~# scp /usr/local/spark/spark-1.6.0-bin-hadoop2.6/conf/slaves root@Worker2:/usr/local/spark/spark-1.6.0-bin-hadoop2.6/conf/slaves</p>
<p>slaves100%  899     0.9KB/s00:00   </p>
<p>root@Master:~# scp /usr/local/spark/spark-1.6.0-bin-hadoop2.6/conf/slaves root@Worker3:/usr/local/spark/spark-1.6.0-bin-hadoop2.6/conf/slaves</p>
<p>slaves                                        100%899     0.9KB/s   00:00</p>
<p>root@Master:~# scp /usr/local/spark/spark-1.6.0-bin-hadoop2.6/conf/slaves root@Worker4:/usr/local/spark/spark-1.6.0-bin-hadoop2.6/conf/slaves</p>
<p>slaves</p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p>启动时遇到问题：</p>
<p>full log in /usr/local/spark/spark-1.6.0-bin-hadoop2.6/logs/spark-root-org.apache.spark.deploy.master.Master-1-Master.out</p>
<p> </p>
<p>报错master 内存不足。 <img width="769" height="465" src="http://b865a395.fromwiz.com/share/resources/46c3933a-18eb-4981-8bf4-ba5a4d4289a2/index_files/clip_image0018b172491-a8fb-44bd-bf93-4701f7c15234.png" alt=""></p>
            </div>
                </div>