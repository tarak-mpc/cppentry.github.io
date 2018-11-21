---
layout:     post
title:      hadoop搭建hive web  界面
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>ubuntu 14.04      hadoop-2.6.0.tar.gz       apache-hive-2.1.1-bin.tar.gzapache-hive-2.1.1-bin.tar.gz</p>
<p><br></p>
<p>前提是搭建好jdk，hadoop，和hive平台。才能继续搭建hive web ui界面</p>
<p>搭建hive web的界面需要两个包  一个是jdk的tools.jar   一个是hive的hive-hwi-*.war 包</p>
<p>稍后会提供下载链接。但是<span style="color:rgb(51,51,51);font-family:arial, tahoma, 'Microsoft Yahei', '宋体', sans-serif;"><span style="font-size:10px;">授人以鱼不如授人以渔。</span></span></p>
<p><span style="color:rgb(51,51,51);font-family:arial, tahoma, 'Microsoft Yahei', '宋体', sans-serif;"><span style="font-size:10px;"><br></span></span></p>
<p><span style="color:rgb(51,51,51);font-family:arial, tahoma, 'Microsoft Yahei', '宋体', sans-serif;"><span style="font-size:10px;">--------------------------------------------------------------------------------</span></span></p>
<p><span style="color:rgb(51,51,51);font-family:arial, tahoma, 'Microsoft Yahei', '宋体', sans-serif;"><span style="font-size:10px;">tools.jar在ubuntu里面安装的jdk-》 lib目录下有次包</span></span></p>
<p><span style="color:rgb(51,51,51);font-family:arial, tahoma, 'Microsoft Yahei', '宋体', sans-serif;"><span style="font-size:10px;"><br></span></span></p>
<p><span style="color:rgb(51,51,51);font-family:arial, tahoma, 'Microsoft Yahei', '宋体', sans-serif;"><span style="font-size:10px;">hive-hwi-*.war 需要下载官网下载 apache-hive-2.1.1-src.tar.gz 资源包  <br></span></span></p>
<p><span style="color:rgb(51,51,51);font-family:arial, tahoma, 'Microsoft Yahei', '宋体', sans-serif;"><span style="font-size:10px;"><a href="http://mirror.bit.edu.cn/apache/hive/hive-2.1.1/" rel="nofollow">hive src资源包</a><br></span></span></p>
<p><span style="color:rgb(51,51,51);font-family:arial, tahoma, 'Microsoft Yahei', '宋体', sans-serif;">可通过wget  下载</span></p>
<p><span style="color:rgb(51,51,51);font-family:arial, tahoma, 'Microsoft Yahei', '宋体', sans-serif;">-----------------------------------------------------------------------</span></p>
<p><span style="font-family:arial, tahoma, 'Microsoft Yahei', '宋体', sans-serif;color:#333333;">下载完之后是这个文件    apache-hive-2.1.1-src.tar.gz</span></p>
<p><span style="color:rgb(51,51,51);font-family:arial, tahoma, 'Microsoft Yahei', '宋体', sans-serif;">下载之后通过tar -zxvf解压</span></p>
<p><span style="color:rgb(51,51,51);font-family:arial, tahoma, 'Microsoft Yahei', '宋体', sans-serif;">产生apache-hive-2.1.1-src 这个文件</span></p>
<p><span style="color:rgb(51,51,51);font-family:arial, tahoma, 'Microsoft Yahei', '宋体', sans-serif;"><br></span></p>
<p><span style="color:rgb(51,51,51);font-family:arial, tahoma, 'Microsoft Yahei', '宋体', sans-serif;">进入这个文件 </span></p>
<p><span style="color:rgb(51,51,51);font-family:arial, tahoma, 'Microsoft Yahei', '宋体', sans-serif;">cd apache-hive-2.1.1-src/hwi/web/<br></span></p>
<p><span style="color:rgb(51,51,51);font-family:arial, tahoma, 'Microsoft Yahei', '宋体', sans-serif;"><br></span></p>
<p><span style="color:rgb(51,51,51);font-family:arial, tahoma, 'Microsoft Yahei', '宋体', sans-serif;"><br></span></p>
<p><span style="color:rgb(51,51,51);font-family:arial, tahoma, 'Microsoft Yahei', '宋体', sans-serif;"><br></span></p>
<p><span style="color:rgb(51,51,51);font-family:arial, tahoma, 'Microsoft Yahei', '宋体', sans-serif;">执行打包命令</span></p>
<p><span style="color:rgb(51,51,51);font-family:arial, tahoma, 'Microsoft Yahei', '宋体', sans-serif;"><br></span></p>
<p><span style="color:rgb(51,51,51);font-family:arial, tahoma, 'Microsoft Yahei', '宋体', sans-serif;"><br></span></p>
<p><span style="font-family:arial, tahoma, 'Microsoft Yahei', '宋体', sans-serif;"></span></p><pre><code class="language-plain">jar -cvf hive-hwi-2.1.0.war *</code></pre><br><br><p><span style="color:rgb(51,51,51);font-family:arial, tahoma, 'Microsoft Yahei', '宋体', sans-serif;"><span style="font-family:'Courier New', sans-serif;background-color:rgb(245,245,245);"><br></span></span></p>
<p><span style="color:rgb(51,51,51);font-family:arial, tahoma, 'Microsoft Yahei', '宋体', sans-serif;"><span style="font-family:'Courier New', sans-serif;background-color:rgb(245,245,245);">会产生一个hive-hwi-2.1.0.war 文件</span></span></p>
<p><span style="color:rgb(51,51,51);font-family:arial, tahoma, 'Microsoft Yahei', '宋体', sans-serif;"><span style="font-family:'Courier New', sans-serif;background-color:rgb(245,245,245);"><br></span></span></p>
<p><span style="color:rgb(51,51,51);font-family:arial, tahoma, 'Microsoft Yahei', '宋体', sans-serif;"><span style="font-family:'Courier New', sans-serif;background-color:rgb(245,245,245);"><br></span></span></p>
<p><span style="color:rgb(51,51,51);font-family:arial, tahoma, 'Microsoft Yahei', '宋体', sans-serif;"><span style="font-family:'Courier New', sans-serif;background-color:rgb(245,245,245);">------------------------------------</span></span></p>
<p><span style="color:rgb(51,51,51);font-family:arial, tahoma, 'Microsoft Yahei', '宋体', sans-serif;"><span style="font-family:'Courier New', sans-serif;background-color:rgb(245,245,245);"><br></span></span></p>
<p><span style="color:rgb(51,51,51);font-family:arial, tahoma, 'Microsoft Yahei', '宋体', sans-serif;"><span style="font-family:'Courier New', sans-serif;background-color:rgb(245,245,245);">将<span style="color:rgb(51,51,51);font-family:'Courier New', sans-serif;background-color:rgb(245,245,245);">hive-hwi-2.1.0.war和tools.jar文件复制到</span></span></span></p>
<p><span style="color:rgb(51,51,51);font-family:arial, tahoma, 'Microsoft Yahei', '宋体', sans-serif;"><span style="font-family:'Courier New', sans-serif;background-color:rgb(245,245,245);"><span style="color:rgb(51,51,51);font-family:'Courier New', sans-serif;background-color:rgb(245,245,245);">hive的lib目录下</span></span></span></p>
<p><span style="color:rgb(51,51,51);font-family:arial, tahoma, 'Microsoft Yahei', '宋体', sans-serif;"><span style="font-family:'Courier New', sans-serif;background-color:rgb(245,245,245);"><span style="color:rgb(51,51,51);font-family:'Courier New', sans-serif;background-color:rgb(245,245,245);"><br></span></span></span></p>
<p><span style="color:rgb(51,51,51);font-family:arial, tahoma, 'Microsoft Yahei', '宋体', sans-serif;"><span style="font-family:'Courier New', sans-serif;background-color:rgb(245,245,245);"><span style="color:rgb(51,51,51);font-family:'Courier New', sans-serif;background-color:rgb(245,245,245);">---------------------------------------</span></span></span></p>
<p><span style="color:rgb(51,51,51);font-family:arial, tahoma, 'Microsoft Yahei', '宋体', sans-serif;"><span style="font-family:'Courier New', sans-serif;background-color:rgb(245,245,245);"><span style="color:rgb(51,51,51);font-family:'Courier New', sans-serif;background-color:rgb(245,245,245);">vim或者gedit
 修改hive -》 conf 下的hive-site.xml文件</span></span></span></p>
<p><span style="color:rgb(51,51,51);font-family:arial, tahoma, 'Microsoft Yahei', '宋体', sans-serif;"><span style="font-family:'Courier New', sans-serif;background-color:rgb(245,245,245);"><span style="color:rgb(51,51,51);font-family:'Courier New', sans-serif;background-color:rgb(245,245,245);"><br></span></span></span></p>
<p><span style="color:rgb(51,51,51);font-family:arial, tahoma, 'Microsoft Yahei', '宋体', sans-serif;"><span style="font-family:'Courier New', sans-serif;background-color:rgb(245,245,245);"><span style="color:rgb(51,51,51);font-family:'Courier New', sans-serif;background-color:rgb(245,245,245);">&lt;property&gt;<br>
    &lt;name&gt;hive.hwi.listen.host&lt;/name&gt;<br>
    &lt;value&gt;0.0.0.0&lt;/value&gt;<br>
    &lt;description&gt;监听的地址&lt;/description&gt;<br>
  &lt;/property&gt;<br>
  &lt;property&gt;<br>
    &lt;name&gt;hive.hwi.listen.port&lt;/name&gt;<br>
    &lt;value&gt;9999&lt;/value&gt;<br>
    &lt;description&gt;监听的端口号&lt;/description&gt;<br>
  &lt;/property&gt;<br>
  &lt;property&gt;<br>
    &lt;name&gt;hive.hwi.war.file&lt;/name&gt;<br>
    &lt;value&gt;lib/hive-hwi-2.1.0.war&lt;/value&gt;<br>
    &lt;description&gt;war包所在的地址&lt;/description&gt;<br>
  &lt;/property&gt;<br></span></span></span></p>
<p><span style="color:rgb(51,51,51);font-family:arial, tahoma, 'Microsoft Yahei', '宋体', sans-serif;"><br></span></p>
<p><span style="color:rgb(51,51,51);font-family:arial, tahoma, 'Microsoft Yahei', '宋体', sans-serif;"><span style="font-size:10px;">---------------------------------------------------------------------------------</span></span></p>
<p><span style="color:rgb(51,51,51);font-family:arial, tahoma, 'Microsoft Yahei', '宋体', sans-serif;"><span style="font-size:10px;"><br></span></span></p>
<p><span style="color:rgb(51,51,51);font-family:arial, tahoma, 'Microsoft Yahei', '宋体', sans-serif;"><span style="font-size:10px;">配置到此基本结束</span></span></p>
<p><span style="color:rgb(51,51,51);font-family:arial, tahoma, 'Microsoft Yahei', '宋体', sans-serif;"><span style="font-size:10px;"><br></span></span></p>
<p><span style="font-family:arial, tahoma, 'Microsoft Yahei', '宋体', sans-serif;font-size:10px;color:#333333;">终端执行                </span></p><pre><code class="language-plain">    hive --service hwi</code></pre><br><p><span style="font-family:arial, tahoma, 'Microsoft Yahei', '宋体', sans-serif;font-size:10px;"><span style="color:#ff0000;"><span class="hljs-comment" style="font-family:'Courier New', sans-serif;"><br></span></span></span></p>
<p><span style="font-family:'Courier New', sans-serif;font-size:10px;"><span>在浏览器输入 http://ip:9999/hwi/  这个ip可能是localhost，也可能是你自己设置的</span></span></p>
<p><span style="font-family:'Courier New', sans-serif;font-size:10px;"><span><br></span></span></p>
<p><span style="font-family:'Courier New', sans-serif;font-size:10px;"><span>这里有个坑，刷新浏览器可能会报错，再刷新可能错误还要改变。需要不停的刷新几次可能才会有正确界面</span></span></p>
<p><span style="font-family:'Courier New', sans-serif;font-size:10px;"><span><br></span></span></p>
<p><span style="font-family:'Courier New', sans-serif;font-size:10px;"><span>---------------------------------</span></span></p>
<p><span style="font-family:'Courier New', sans-serif;font-size:10px;"><span><br></span></span></p>
<p><span style="font-family:'Courier New', sans-serif;font-size:10px;"><span>tools和<span style="color:rgb(51,51,51);font-family:'Courier New', sans-serif;background-color:rgb(245,245,245);">hive-hwi-2.1.0.war下载地址</span></span></span></p>
<p><span style="color:rgb(51,51,51);font-family:arial, tahoma, 'Microsoft Yahei', '宋体', sans-serif;"><span style="font-size:10px;"><br></span></span></p>
<p><span style="color:rgb(51,51,51);font-family:arial, tahoma, 'Microsoft Yahei', '宋体', sans-serif;"><span style="font-size:10px;"><br></span></span></p>
<p><a href="http://download.csdn.net/download/fangyuandoit/10167284" rel="nofollow">资源地址</a><br></p>
<p><br></p>
            </div>
                </div>