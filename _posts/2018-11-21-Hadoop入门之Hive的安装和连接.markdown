---
layout:     post
title:      Hadoop入门之Hive的安装和连接
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/sqh201030412/article/details/77885031				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div>1.下载hive </div>
<div>   wget http://archive.apache.org/dist/hive/hive-1.2.1/apache-hive-1.2.1-bin.tar.gz</div>
<div><br style="background-color:inherit;"></div>
<div>2.解压hive</div>
<div>   tar -zxvf  apache-hive-1.2.1-bin.tar.gz</div>
<div><br style="background-color:inherit;"></div>
<div>   mv apache-hive-1.2.1-bin.tar.gz  app/hive</div>
<div><br style="background-color:inherit;"></div>
<div>3.安装mysql(如果有可用的mysql可以跳过)</div>
<div>   http://www.cnblogs.com/nzplearnSite/p/5002775.html</div>
<div><br style="background-color:inherit;"></div>
<div>    密码修改:</div>
<div>   grant all on *.* to root@'%' identified by 'gome_search';</div>
<div><br style="background-color:inherit;"></div>
<div>   update user set password=password('gome_search') where user='root' and host='localhost';</div>
<div><br style="background-color:inherit;"></div>
<div>4.配置Hive环境变量</div>
<div>  vim  /etc/profile</div>
<div>    export HIVE_HOME=/home/hadoop/app/hive</div>
<div>
<div style="background-color:inherit;">
<p style="background-color:inherit;"><span lang="en-us" style="background-color:inherit;" xml:lang="en-us">    export PATH=${HIVE_HOME}/bin:$PATH</span></p>
</div>
</div>
<div>  source /etc/profile</div>
<div><br style="background-color:inherit;"></div>
<div>5.修改hive配置文件</div>
<div><br style="background-color:inherit;"></div>
<div>
<div style="background-color:inherit;">
<div style="background-color:inherit;">
<div style="background-color:inherit;">进入配置文件的目录</div>
</div>
<p style="background-color:inherit;"><span lang="en-us" style="background-color:inherit;" xml:lang="en-us">cd  ${HIVE_HOME}/conf/</span></p>
<p style="background-color:inherit;"><span lang="en-us" style="background-color:inherit;" xml:lang="en-us"> </span></p>
<div style="background-color:inherit;">
<div style="background-color:inherit;">修改<span lang="en-us" style="background-color:inherit;" xml:lang="en-us">hive-env.sh</span>文件</div>
</div>
<p style="background-color:inherit;"><span lang="en-us" style="background-color:inherit;" xml:lang="en-us">cp hive-env.sh.template hive-env.sh</span></p>
<p style="background-color:inherit;"><span lang="en-us" style="background-color:inherit;" xml:lang="en-us"> </span></p>
<p style="background-color:inherit;">将以下内容写入到<span lang="en-us" style="background-color:inherit;" xml:lang="en-us">hive-env.sh</span>文件中</p>
<p style="background-color:inherit;"><span lang="en-us" style="background-color:inherit;" xml:lang="en-us">export JAVA_HOME=/export/servers/jdk</span></p>
<p style="background-color:inherit;"><span lang="en-us" style="background-color:inherit;" xml:lang="en-us">export HADOOP_HOME=/export/servers/hadoop</span></p>
<p style="background-color:inherit;"><span lang="en-us" style="background-color:inherit;" xml:lang="en-us">export HIVE_HOME=/export/servers/hive</span></p>
<p style="background-color:inherit;"><span lang="en-us" style="background-color:inherit;" xml:lang="en-us"> </span></p>
<div style="background-color:inherit;">
<div style="background-color:inherit;">修改<span lang="en-us" style="background-color:inherit;" xml:lang="en-us">log4j</span>文件</div>
</div>
<p style="background-color:inherit;"><span lang="en-us" style="background-color:inherit;" xml:lang="en-us">cp hive-log4j.properties.template hive-log4j.properties</span></p>
<p style="background-color:inherit;"><span lang="en-us" style="background-color:inherit;" xml:lang="en-us"> </span></p>
<p style="background-color:inherit;">将<span lang="en-us" style="background-color:inherit;" xml:lang="en-us">EventCounter</span>修改成<span lang="en-us" style="background-color:inherit;" xml:lang="en-us">org.apache.hadoop.log.metrics.EventCounter</span></p>
<p style="background-color:inherit;"><span lang="en-us" style="background-color:inherit;" xml:lang="en-us">#log4j.appender.EventCounter=org.apache.hadoop.hive.shims.HiveEventCounter</span></p>
<p style="background-color:inherit;"><span lang="en-us" style="background-color:inherit;" xml:lang="en-us">log4j.appender.EventCounter=org.apache.hadoop.log.metrics.EventCounter</span></p>
</div>
</div>
<div><br style="background-color:inherit;"></div>
<div>
<div style="background-color:inherit;">
<div style="background-color:inherit;">
<div style="background-color:inherit;">配置远程登录模式</div>
</div>
</div>
</div>
<div><br style="background-color:inherit;"></div>
<div>
<div style="background-color:inherit;">
<p style="background-color:inherit;"><span lang="en-us" style="background-color:inherit;" xml:lang="en-us">touch hive-site.xml</span></p>
</div>
<div style="background-color:inherit;">
<p style="background-color:inherit;">将以下信息写入到<span lang="en-us" style="background-color:inherit;" xml:lang="en-us">hive-site.xml</span>文件中</p>
</div>
<div style="background-color:inherit;">&lt;configuration&gt;</div>
<div style="background-color:inherit;">&lt;property&gt;</div>
<div style="background-color:inherit;">&lt;name&gt;javax.jdo.option.ConnectionURL&lt;/name&gt;</div>
<div style="background-color:inherit;">&lt;value&gt;jdbc:mysql://hadoop08:3306/hivedb?createDatabaseIfNotExist=true&lt;/value&gt;</div>
<div style="background-color:inherit;">&lt;/property&gt;</div>
<div style="background-color:inherit;">&lt;property&gt;</div>
<div style="background-color:inherit;">&lt;name&gt;javax.jdo.option.ConnectionDriverName&lt;/name&gt;</div>
<div style="background-color:inherit;">&lt;value&gt;com.mysql.jdbc.Driver&lt;/value&gt;</div>
<div style="background-color:inherit;">&lt;/property&gt;</div>
<div style="background-color:inherit;">&lt;property&gt;</div>
<div style="background-color:inherit;">&lt;name&gt;javax.jdo.option.ConnectionUserName&lt;/name&gt;</div>
<div style="background-color:inherit;">&lt;value&gt;root&lt;/value&gt;</div>
<div style="background-color:inherit;">&lt;/property&gt;</div>
<div style="background-color:inherit;">&lt;property&gt;</div>
<div style="background-color:inherit;">&lt;name&gt;javax.jdo.option.ConnectionPassword&lt;/name&gt;</div>
<div style="background-color:inherit;">&lt;value&gt;你的密码&lt;/value&gt;</div>
<div style="background-color:inherit;">&lt;/property&gt;</div>
<div style="background-color:inherit;">&lt;/configuration&gt;</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
</div>
<div>将mysql驱动放在hive的lib目录下:</div>
<div>
<div style="background-color:inherit;">mv mysql-connector-java-5.1.32.jar  app/hive/lib/</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
</div>
<div><br style="background-color:inherit;"></div>
<div>6.启动调试hive </div>
<div>  </div>
<div>
<div style="background-color:inherit;">
<p style="text-align:left;background-color:#FFFFFF;"><span style="font-family:'宋体';color:#000000;font-size:12pt;background-color:#FFFFFF;">如果报错</span><span lang="en-us" style="font-family:'微软雅黑', sans-serif;color:#FF0000;background-color:#FFFFFF;" xml:lang="en-us">Terminal
 initialization failed; falling back to unsupported  </span><span style="font-family:'微软雅黑', sans-serif;color:#000000;background-color:#FFFFFF;">将<span lang="en-us" style="background-color:inherit;" xml:lang="en-us">/hive/lib<span> </span></span>里面的<span lang="en-us" style="background-color:inherit;" xml:lang="en-us">jline2.12</span>替换了<span lang="en-us" style="background-color:inherit;" xml:lang="en-us">hadoop<span> </span></span>中<span lang="en-us" style="background-color:inherit;" xml:lang="en-us">/hadoop-2.6.1/share/hadoop/yarn/lib/jline-0.09*.jar</span></span></p>
<p style="text-align:left;background-color:#FFFFFF;"><span style="font-family:'微软雅黑', sans-serif;color:#000000;background-color:#FFFFFF;"><span lang="en-us" style="background-color:inherit;" xml:lang="en-us"><br></span></span></p>
<p style="text-align:left;background-color:#FFFFFF;"><span style="font-family:'微软雅黑', sans-serif;color:#000000;background-color:#FFFFFF;"><span lang="en-us" style="background-color:inherit;" xml:lang="en-us"></span></span></p>
<div>本地shell连接:</div>
<div><br style="background-color:inherit;"></div>
<div>  hive</div>
<img src="https://img-blog.csdn.net/20170907181356609?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3FoMjAxMDMwNDEy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br><p></p>
<p style="text-align:left;background-color:#FFFFFF;"><span style="font-family:'微软雅黑', sans-serif;color:#000000;background-color:#FFFFFF;"><span lang="en-us" style="background-color:inherit;" xml:lang="en-us"><br></span></span></p>
<p style="text-align:left;background-color:#FFFFFF;"><span style="font-family:'微软雅黑', sans-serif;color:#000000;background-color:#FFFFFF;"><span lang="en-us" style="background-color:inherit;" xml:lang="en-us"></span></span></p>
<div>远程连接hive:</div>
<div><br style="background-color:inherit;"></div>
<div>
<div style="background-color:inherit;">
<p style="background-color:inherit;"><span style="font-family:'宋体';background-color:inherit;">启动方式</span></p>
<p style="background-color:inherit;"><span style="font-family:'宋体';background-color:inherit;">启动为前台：</span><span lang="en-us" style="background-color:inherit;" xml:lang="en-us">bin/hiveserver2</span></p>
<p style="background-color:inherit;"><span style="font-family:'宋体';background-color:inherit;">启动为后台: </span><span style="line-height:1.5;background-color:inherit;">nohup </span><span style="line-height:1.5;background-color:inherit;">/home/hadoop/app/hive/bin/hiveserver2</span><span style="line-height:1.5;background-color:inherit;"> &gt; </span><span style="line-height:1.5;background-color:inherit;">/home/hadoop/log/hiveserver.log</span><span style="line-height:1.5;background-color:inherit;"> 2&gt;&amp;1
 &amp;</span></p>
</div>
</div>
<div>连接:</div>
<div>
<div style="background-color:inherit;">
<p style="background-color:inherit;"><span lang="en-us" style="background-color:inherit;" xml:lang="en-us">hive/bin/beeline<span style="background-color:inherit;"> <span> </span></span></span><span style="font-family:'宋体';background-color:inherit;">回车，进入</span><span lang="en-us" style="background-color:inherit;" xml:lang="en-us">beeline</span><span style="font-family:'宋体';background-color:inherit;">的命令界面</span></p>
<p style="background-color:inherit;"><span lang="en-us" style="background-color:inherit;" xml:lang="en-us">beeline&gt; !connect jdbc:hive2://hadoop6:10000</span></p>
</div>
</div>
<img src="https://img-blog.csdn.net/20170907181401049?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3FoMjAxMDMwNDEy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br><p></p>
<p style="text-align:left;background-color:#FFFFFF;"><span style="font-family:'微软雅黑', sans-serif;color:#000000;background-color:#FFFFFF;"><span lang="en-us" style="background-color:inherit;" xml:lang="en-us"><br></span></span></p>
<p style="text-align:left;background-color:#FFFFFF;"><span style="font-family:'微软雅黑', sans-serif;color:#000000;background-color:#FFFFFF;"><span lang="en-us" style="background-color:inherit;" xml:lang="en-us"><br></span></span></p>
<p style="text-align:left;background-color:#FFFFFF;"><span style="font-family:'微软雅黑', sans-serif;color:#000000;background-color:#FFFFFF;"><span lang="en-us" style="background-color:inherit;" xml:lang="en-us"><br></span></span></p>
</div>
</div>
            </div>
                </div>