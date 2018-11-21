---
layout:     post
title:      HDFS Exlorer初体验
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1 align="left"><span style="font-size:24px;">简介</span></h1>
<p align="left"><span style="font-size:14px;">     HDFS默认通过网页<a href="http://master:50070" rel="nofollow">http://master:50070</a>访问，该网站只有下载功能，其交互性一般。</span></p>
<p align="left"><span style="font-size:14px;">    <a href="http://bigdata.red-gate.com/hdfs-explorer.html" rel="nofollow">
 </a><a href="http://bigdata.red-gate.com/hdfs-explorer.html" rel="nofollow">HDFS Explorer</a>的设计初衷是让你可以更好的访问HDFS上的文件，它提供了一个类似于Windows7文件浏览器的用户界面。现在虽然只是beta版，但是对HDFS的访问还是很方便的。</span></p>
<div align="left">
<table cellspacing="1" cellpadding="1" align="center" border="0"><tbody><tr><td width="370"><span style="font-size:14px;"> 更好的方式访问HDFS集群</span>
<ul><li>
<div align="left"><span style="font-size:14px;">Windows7文件管理器相同的交互体验</span></div>
</li><li>
<div align="left"><span style="font-size:14px;">能够管理多个HDFS集群</span></div>
</li><li>
<div align="left"><span style="font-size:14px;">和Windows文件管理器之间无缝的复杂和粘贴操作</span></div>
</li><li>
<div align="left"><span style="font-size:14px;">快速下载Pig和Hive查询结果</span></div>
</li></ul>
 </td>
<td width="370" align="center">      
<p align="center"><img alt="" src="https://img-blog.csdn.net/20140226115350781?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvamlld3V5b3U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" width="320" height="200"></p>
</td>
</tr></tbody></table></div>
<p align="left"> </p>
<div align="left">
<table cellspacing="1" cellpadding="1" align="center" border="0"><tbody><tr><td width="370">
<p align="center"><span style="font-size:14px;"><img alt="" src="https://img-blog.csdn.net/20140226120243109?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvamlld3V5b3U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast"></span></p>
         </td>
<td width="370">
<p>   </p>
<p align="left"><span style="font-size:14px;">HDFS访问设计</span></p>
<ul><li>
<div align="left"><span style="font-size:14px;">几秒内添加HDFS服务器</span></div>
</li><li>
<div align="left"><span style="font-size:14px;">管理多个HDFS集群</span></div>
</li><li>
<div align="left"><span style="font-size:14px;">编辑HDFS文件的权限</span></div>
</li><li>
<div align="left"><span style="font-size:14px;">查询文件</span></div>
</li></ul><p>   </p>
</td>
</tr></tbody></table></div>
<h1> <span style="font-size:24px;">安装</span></h1>
<h2 align="left"><span style="font-size:18px;">HDFS Explorer安装</span></h2>
<p align="left"><span style="font-size:14px;">   </span><span style="font-size:14px;"> 安装前提条件</span></p>
<ul><li>
<div align="left"><span style="font-size:14px;">NET Framework 4.5</span></div>
</li><li>
<div align="left"><span style="font-size:14px;">系统要求：Windows vista、7、8、8.1或者Windows Server 2008R2 及后面的版本</span></div>
</li></ul><p align="left"><span style="font-size:14px;">    安装过程</span></p>
<ol><li>
<div align="left"><span style="font-size:14px;">下载<a href="http://bigdatainstallers.azurewebsites.net/files/HDFS%20Explorer/beta/1/HDFS%20Explorer%20-%20beta.application" rel="nofollow">HDFS Explorer</a></span></div>
</li><li>
<div align="left"><span style="font-size:14px;">双击“HDFS Explorer - beta.application”进行安装。</span></div>
</li><li>
<div align="left"><span style="font-size:14px;">安装完成后，输入Email即可使用</span></div>
</li><li>
<div align="left"><span style="font-size:14px;">添加集群</span>
<p align="left"><span style="font-size:14px;">       右击HDFS Explorer左下角的HDFS Connections，选择“Add Connection”，出现界面“Add an HDFS connection”，输入集群IP和Hadoop用户即可。</span></p>
<p align="center"><span style="font-size:14px;"></span></p>
<p align="center"><img alt="" src="https://img-blog.csdn.net/20140226153930046?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvamlld3V5b3U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast"><br></p>
<p align="center"><span style="font-size:14px;">点击“Add Connection”</span></p>
<div style="text-align:center;">      <img alt="" src="https://img-blog.csdn.net/20140226120705484?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvamlld3V5b3U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast"></div>
<p align="center"><span style="font-size:14px;">输入HDFS集群的IP和Hadoop的用户</span></p>
</div>
</li><li>
<div>
<p align="left"><span style="font-size:14px;">完毕后，你就可以像Windows文件管理器一样访问HDFS了。</span></p>
</div>
</li></ol><h2><strong><span style="font-size:18px;">配置HDFS服务器</span></strong></h2>
<ol><li>
<div align="left"><span style="font-size:14px;">配置WebHDFS</span>
<p align="left"><span style="font-size:14px;">     HDFS Explorer通过WebHDFS访问HDFS，必须配置HDFS支持webhdfs</span><span style="font-size:14px;">。在hdfs-site.xmle文件中添加如下内容：</span></p>
<div>
<table width="60000" style="background-color:rgb(217,217,217);"><tbody><tr><td><span style="font-size:14px;">&lt;property&gt;<br></span><span style="font-family:Calibri;font-size:14px;">    &lt;name&gt;dfs.webhdfs.enabled&lt;/name&gt;<br>
    &lt;value&gt;true&lt;/value&gt;<br>
&lt;/property&gt;</span></td>
</tr></tbody></table></div>
</div>
</li><li>
<div align="left"><span style="font-size:14px;">离开safemode</span>
<div>
<table width="60000" style="background-color:rgb(217,217,217);"><tbody><tr><td><span style="font-size:14px;">#$HADOOP_HOME/bin/hadoop dfsadmin -safemode leave</span></td>
</tr></tbody></table></div>
</div>
</li></ol><h1 align="left"><span style="font-size:24px;">体验</span></h1>
<p align="left"><span style="font-size:14px;">     其体验效果和宣传的一样。HDFS Explorer支持文件拖拽上传和下载，也可以直接双击打开文件，如图中的word文档，直接双击即可打开。但是文件上传速度较慢，体验了几次，最快达到1.5MB/s。</span></p>
<p align="left"><span style="font-size:14px;">     总的来说，使用起来还是很方便的，现在只是beta版，相信其后续版本会更加的好用。</span></p>
<p align="left"><span style="font-size:14px;"><img alt="" src="https://img-blog.csdn.net/20140226154121843?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvamlld3V5b3U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast"><br></span></p>
            </div>
                </div>