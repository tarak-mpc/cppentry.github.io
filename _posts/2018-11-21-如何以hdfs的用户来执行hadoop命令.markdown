---
layout:     post
title:      如何以hdfs的用户来执行hadoop命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/kwu_ganymede/article/details/50673302				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>当hadoop集群中加上权限管理后，即使是linux的root用户也不能拥有最高权限了，因为hdfs的管理员是hdfs，那么问题来了，怎么在命令行调用hdfs的命令呢？</p>
<p><br></p>
<p>开启检查 HDFS 权限<br>
dfs.permissions<br></p>
<p>设置为true</p>
<p><br></p>
<p>创建目录</p>
<p></p>
<pre><code class="language-plain">su hdfs -c "hadoop fs -mkdir /spark-log"</code></pre><br><br><p></p>
<p>分配权限</p>
<p></p>
<pre><code class="language-plain">su hdfs -l -c "hadoop fs -chown -R root:root /spark-log"</code></pre>
<p></p>
<p><br></p>
查看目录
<p></p>
<pre><code class="language-plain">su hdfs -l -c "hadoop fs -ls /"</code></pre><br><br><p></p>
<p>说明：</p>
<p>假设用户名为username，需要运行command程序，命令如下：<br>
su username -l -c "comman"<br><br><br>
其中“-l”表示以登录方式执行，这就意味着username的环境变量会被初始化，这在很多情况下是必须的。<br></p>
            </div>
                </div>