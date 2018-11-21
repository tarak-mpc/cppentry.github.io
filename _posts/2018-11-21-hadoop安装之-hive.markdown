---
layout:     post
title:      hadoop安装之-hive
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章,未经博主允许不得转载。					https://blog.csdn.net/Stubborn_Cow/article/details/49658497				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>一、安装准备</p>
<p>1， 下载hive包 <a href="http://mirrors.hust.edu.cn/apache/" rel="nofollow" style="background-color:inherit;line-height:1.5;">
http://mirrors.hust.edu.cn/apache/</a> <br>
2，  mysql环境，字符集使用latin1，使用其他字符集会造成hive使用中发生错误。<br>
        创建hive使用的数据和，hive用户<span style="font-family:verdana, arial, helvetica, sans-serif;background-color:rgb(250,250,250);"><br>
mysql&gt; create database hive;</span><br></p>
<div style="background-color:inherit;"><span style="font-family:verdana, arial, helvetica, sans-serif;background-color:rgb(250,250,250);"><span style="font-family:verdana, arial, helvetica, sans-serif;background-color:rgb(250,250,250);">mysql&gt;</span>grant all on hive.* to hive@'%'  identified by 'hive';<br style="background-color:inherit;"></span></div>
<div style="background-color:inherit;"><span style="font-family:verdana, arial, helvetica, sans-serif;background-color:rgb(250,250,250);"><span style="font-family:verdana, arial, helvetica, sans-serif;background-color:rgb(250,250,250);">mysql&gt;</span>grant all on hive.* to hive@'localhost'  identified by 'hive';<br style="background-color:inherit;"></span></div>
<div style="background-color:inherit;"><span style="font-family:verdana, arial, helvetica, sans-serif;background-color:rgb(250,250,250);"><span style="font-family:verdana, arial, helvetica, sans-serif;background-color:rgb(250,250,250);">mysql&gt;</span>flush privileges;<span style="background-color:inherit;"><br></span></span></div>
<br><p></p>
<p>二、安装hive</p>
<p>1，解压安装包(如在home下解压)<br>
cd  ~<br>
tar -zxvf apache-hive-1.1.0-bin.tar.gz<br>
2，建立软连接<br>
ln -s apache-hive-1.1.0-bin hive<br>
3，添加环境变量<br>
vi  .bash_profile<br>
导入下面的环境变量<br>
export HIVE_HOME=/home/hive<br>
export PATH=$PATH:$HIVE_HOME/bin<br><br>
使其有效<br>
source .bash_profile<br><br>
4，cp hive/conf/hive-default.xml.template hive/conf/hive-site.xml<br>
编辑hive-site.xml<br>
主要修改以下参数<br>
&lt;property&gt; <br>
   &lt;name&gt;javax.jdo.option.ConnectionURL &lt;/name&gt; <br>
   &lt;value&gt;jdbc:mysql://localhost:3306/hive &lt;/value&gt; <br>
&lt;/property&gt; <br>
 <br>
&lt;property&gt; <br>
   &lt;name&gt;javax.jdo.option.ConnectionDriverName &lt;/name&gt; <br>
   &lt;value&gt;com.mysql.jdbc.Driver &lt;/value&gt; <br>
&lt;/property&gt;<br><br>
&lt;property&gt; <br>
   &lt;name&gt;javax.jdo.option.ConnectionPassword &lt;/name&gt; <br>
   &lt;value&gt;hive &lt;/value&gt; <br>
&lt;/property&gt; <br>
 <br>
&lt;property&gt; <br>
   &lt;name&gt;hive.hwi.listen.port &lt;/name&gt; <br>
   &lt;value&gt;9999 &lt;/value&gt; <br>
   &lt;description&gt;This is the port the Hive Web Interface will listen on &lt;/descript ion&gt;<br>
&lt;/property&gt; <br><br>
&lt;property&gt; <br>
   &lt;name&gt;datanucleus.autoCreateSchema &lt;/name&gt; <br>
   &lt;value&gt;true&lt;/value&gt; <br>
&lt;/property&gt; <br>
 <br>
&lt;property&gt; <br>
   &lt;name&gt;datanucleus.fixedDatastore &lt;/name&gt; <br>
   &lt;value&gt;false&lt;/value&gt; <br>
&lt;/property&gt; <br>
&lt;/property&gt; <br><br>
  &lt;property&gt;<br>
    &lt;name&gt;javax.jdo.option.ConnectionUserName&lt;/name&gt;<br>
    &lt;value&gt;hive&lt;/value&gt;<br>
    &lt;description&gt;Username to use against metastore database&lt;/description&gt;<br>
  &lt;/property&gt;<br><br>
  &lt;property&gt;<br>
    &lt;name&gt;hive.exec.local.scratchdir&lt;/name&gt;<br>
    &lt;value&gt;/home/hive/iotmp&lt;/value&gt;<br>
    &lt;description&gt;Local scratch space for Hive jobs&lt;/description&gt;<br>
  &lt;/property&gt;<br>
  &lt;property&gt;<br>
    &lt;name&gt;hive.downloaded.resources.dir&lt;/name&gt;<br>
    &lt;value&gt;/home/hive/iotmp&lt;/value&gt;<br>
    &lt;description&gt;Temporary local directory for added resources in the remote file system.&lt;/description&gt;<br>
  &lt;/property&gt;<br>
  &lt;property&gt;<br>
    &lt;name&gt;hive.querylog.location&lt;/name&gt;<br>
    &lt;value&gt;/home/hdpsrc/hive/iotmp&lt;/value&gt;<br>
    &lt;description&gt;Location of Hive run time structured log file&lt;/description&gt;<br>
  &lt;/property&gt;<br><br>
5,拷贝mysql-connector-java-5.1.6-bin.jar 到hive 的lib下面（有没有'bin'无所谓）<br>
mv /home/hdpsrc/Desktop/mysql-connector-java-5.1.6-bin.jar /home/hive/lib/<br><br>
6，把jline-2.12.jar拷贝到hadoop相应的目录下，替代jline-0.9.94.jar，否则启动会报错<br>
cp /home/hdpsrc/hive/lib/jline-2.12.jar /home/hdpsrc/hadoop-2.6.0/share/hadoop/yarn/lib/<br>
mv /home/hdpsrc/hadoop-2.6.0/share/hadoop/yarn/lib/jline-0.9.94.jar /home/hadoop-2.6.0/share/hadoop/yarn/lib/jline-0.9.94.jar.bak /<br><br>
7，创建hive临时文件夹<br>
mkdir /home/hive/iotmp<br><br><br>
三，启动测试hive<br>
启动hadoop后，执行hive命令<br>
hive<br><br>
测试输入 show database;<br>
hive&gt; show databases;<br>
OK<br>
default<br>
Time taken: 0.907 seconds, Fetched: 1 row(s)</p>
<p><br></p>
<p>四，常见错误</p>
<p>1，创建表提示执行错误</p>
<p><img src="https://img-blog.csdn.net/20151106093034398?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p>     解决办法：将创建的hive数据库字符集更改为latin1<br></p>
            </div>
                </div>