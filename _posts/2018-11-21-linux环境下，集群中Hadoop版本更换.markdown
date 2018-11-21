---
layout:     post
title:      linux环境下，集群中Hadoop版本更换
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_21407811/article/details/77950149				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span class="token p" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;"><span class="token strong" style="font-weight:bold;">1.下载新版本的hadoop</span></span></p>
<p><span class="token p" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;"><span class="token url">https://archive.apache.org/dist/hadoop/common/</span></span></p>
<p><span class="token lf" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;"></span><span class="token p" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;">这里有各种版本的hadoop</span></p>
<p><span class="token lf" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;"></span><span class="token p" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;">wget
 .....</span></p>
<p><span class="token lf" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;"></span><span class="token p" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;">tar
 -xzf .....</span></p>
<p><span class="token lf" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;"></span><span class="token p" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;">将新版本的Hadoop解压在</span><span class="token lf" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;"></span><span class="token p" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;">/usr/local/hadoop目录下</span></p>
<p><span class="token p" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;"><br></span></p>
<p><span class="token lf" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;"></span><span class="token p" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;"><span class="token strong" style="font-weight:bold;">2.修改配置</span></span></p>
<p><span class="token p" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;">vi $HOME/.bashrc</span></p>
<p><span class="token lf" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;"></span><span class="token p" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;">export
 HADOOP_HOME</span></p>
<p><span class="token lf" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;"></span><span class="token p" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;">source
 $HOME/.bashrc</span></p>
<p><span class="token lf" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;"></span><span class="token p" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;">vi
 hadoop-env.sh</span></p>
<p><span class="token lf" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;"></span><span class="token p" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;">vi
 core-site.xml</span></p>
<p><span class="token lf" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;"></span><span class="token p" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;">vi
 hdfs-site.xml</span></p>
<p><span class="token lf" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;"></span><span class="token p" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;">vi
 mapred-site.xml</span></p>
<p><span class="token lf" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;"></span><span class="token p" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;">vi
 slaves</span></p>
<p><span class="token p" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;"><br></span></p>
<p><span class="token p" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;"><span class="token strong" style="font-weight:bold;">3.复制到其它节点</span></span></p>
<p><span class="token p" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;">scp -r local<span class="token em" style="font-style:italic;"><span class="token md md-em md-start">_</span>folder
 remote<span class="token md md-em md-close">_</span></span>username@remote<span class="token em" style="font-style:italic;"><span class="token md md-em md-start">_</span>ip:remote<span class="token md md-em md-close">_</span></span>folder</span></p>
<p><span class="token lf" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;"></span><span class="token lf" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;"></span><span class="token p" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;">vi
 $HOME/.bashrc</span></p>
<p><span class="token lf" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;"></span><span class="token p" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;">export
 HADOOP_HOME</span></p>
<p><span class="token lf" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;"></span><span class="token p" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;">source
 $HOME/.bashrc</span></p>
<p><span class="token p" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;"><span class="token strong" style="font-weight:bold;"><span class="token md md-strong"><br></span></span></span></p>
<p><span class="token lf" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;"></span><span class="token p" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;"><span class="token strong" style="font-weight:bold;">4.启动服务</span></span></p>
<p><span class="token p" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;">$ bin/hdfs namenode -format</span></p>
<p><span class="token lf" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;"></span><span class="token p" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;">$
 sbin/start-dfs.sh</span></p>
<p><span class="token lf" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;"></span><span class="token p" style="color:rgb(90,90,90);font-family:'microsoft yahei';font-size:17px;">$
 sbin/start-all.sh</span></p>
<p><span style="font-family:'microsoft yahei';color:#5a5a5a;"><span style="font-size:17px;">JPS</span></span></p>
<p><span style="font-family:'microsoft yahei';color:#5a5a5a;"><span style="font-size:17px;">看到和之前一样的结果就是更换成功了</span></span></p>
            </div>
                </div>