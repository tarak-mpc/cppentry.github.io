---
layout:     post
title:      hive安装——mysql作为元数据，使用beeline
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/wild46cat/article/details/78987253				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1>hive安装——mysql作为元数据，使用beeline</h1>
<div><span style="font-size:18px;">本文主要介绍如何安装hive，同时hive用mysql作为数据源，最后会使用beeline的方式而不是hive CLI的方式进行hive操作。</span></div>
<div><span style="font-size:18px;"><br></span></div>
<div><span style="font-size:18px;">好，下面上货。</span></div>
<div><span style="font-size:18px;">1、下载hive，根据文档安装hive</span></div>
<div><span style="font-size:18px;"><a href="https://cwiki.apache.org/confluence/display/Hive/GettingStarted#GettingStarted-RunningHiveCLI" rel="nofollow">官方文档</a></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"><br></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">2、把hive的tar包tar开，然后放在指定目录下，这里我放在/usr/local下。</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">3、修改配置文件。</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"><img src="https://img-blog.csdn.net/20180106105859808?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2lsZDQ2Y2F0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"><br></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">修改为如下内容：</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"><img src="https://img-blog.csdn.net/20180106105919879?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2lsZDQ2Y2F0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"><br></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">4、修改hive-env.sh</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"><img src="https://img-blog.csdn.net/20180106105951300?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2lsZDQ2Y2F0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"><br></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">5、修改系统环境变量。</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"></span>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:18px;">HIVE_HOME=/usr/local/apache-hive-1.2.2-bin</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:18px;">export HIVE_HOME</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:18px;">export PATH=$HIVE_HOME/bin/:$PATH</span></div>
<br></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"></span>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:18px;">6、添加hive-site.xml</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:18px;"></span><pre><code class="language-html">  &lt;property&gt;
    &lt;name&gt;javax.jdo.option.ConnectionURL&lt;/name&gt;
    &lt;value&gt;jdbc:mysql://192.168.1.120:3306/hive?useSSL=false&lt;/value&gt;
    &lt;description&gt;JDBC connect string for a JDBC metastore&lt;/description&gt;
  &lt;/property&gt;
  
  &lt;property&gt;
    &lt;name&gt;javax.jdo.option.ConnectionDriverName&lt;/name&gt;
    &lt;value&gt;com.mysql.jdbc.Driver&lt;/value&gt;
    &lt;description&gt;Driver class name for a JDBC metastore&lt;/description&gt;
  &lt;/property&gt;
  
  &lt;property&gt;
    &lt;name&gt;javax.jdo.option.ConnectionUserName&lt;/name&gt;
    &lt;value&gt;root&lt;/value&gt;
    &lt;description&gt;Username to use against metastore database&lt;/description&gt;
  &lt;/property&gt;
  
  &lt;property&gt;
    &lt;name&gt;javax.jdo.option.ConnectionPassword&lt;/name&gt;
    &lt;value&gt;123456&lt;/value&gt;
    &lt;description&gt;password to use against metastore database&lt;/description&gt;
  &lt;/property&gt;</code></pre><br>
7、<span style="font-size:18px;">在lib下添加mysql的connector jar包</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:18px;"><br></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:18px;">8、</span><span style="font-size:18px;">由于hiveCLI已经被废弃了，所以 我们这里使用的是hive2 beeline的方式</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:18px;"></span>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:18px;">$HIVE_HOME/bin/schematool -dbType mysql -initSchema</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:18px;"><br></span></div>
9、<span style="font-size:18px;">运行结果：</span><span style="font-size:18px;"></span>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20180106110258565?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2lsZDQ2Y2F0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:18px;"><br></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:18px;">运行出现错误：</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:18px;"></span>
<div><img src="https://img-blog.csdn.net/20180106110327212?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2lsZDQ2Y2F0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<div><br></div>
<div>10、重新安装mysql，可以参考如下文章<a href="http://blog.csdn.net/wild46cat/article/details/78957410" rel="nofollow">linux centos7 安装mysql</a>。然后再次初始化 schematool -dbType mysql -initSchema。</div>
<div><img src="https://img-blog.csdn.net/20180106110406778?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2lsZDQ2Y2F0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:18px;">11、使用beeline登录</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:18px;"></span>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:18px;">首先需要后台启动hiveserver2</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:18px;">nohup hive --service hiveserver2</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:18px;">然后通过beeline命令进行登录</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:18px;">beeline -u jdbc:hive2://</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:18px;">或者beeline -u jdbc:hive2://cdh1:10000</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:18px;"><br></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:18px;">12、运行结果如下：</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20180106110538125?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2lsZDQ2Y2F0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<br></div>
</div>
<pre><code class="language-plain">
</code></pre></div>
            </div>
                </div>