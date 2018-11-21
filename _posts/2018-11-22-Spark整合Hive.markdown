---
layout:     post
title:      Spark整合Hive
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="line-height:27.2px;color:rgb(63,63,63);font-family:'microsoft yahei';font-size:16px;">
安装hive <br>
解压hive到指定的目录中，一般解压在/usr/local/hive</p>
<pre class="prettyprint" style="font-family:'Source Code Pro', monospace;font-size:14px;line-height:23.8px;color:rgb(51,51,51);"><code class="hljs avrasm has-numbering" style="font-family:'Source Code Pro', monospace;font-size:12.6px;color:inherit;display:block;background:transparent;">tar -zxvf hive-xxx<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.tar</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.gz</span></code></pre><ul class="pre-numbering" style="list-style:none;line-height:23.8px;width:50px;border-right-width:1px;border-right-style:solid;border-right-color:rgb(221,221,221);text-align:right;background-color:rgb(238,238,238);"><li style="list-style:none;color:rgb(153,153,153);">1</li></ul><p style="line-height:27.2px;color:rgb(63,63,63);font-family:'microsoft yahei';font-size:16px;">
配置系统环境 <br><img src="https://img-blog.csdn.net/20171023162815680?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2h1a3VuMTIz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title="" style="border:0px;vertical-align:middle;"></p>
<p style="line-height:27.2px;color:rgb(63,63,63);font-family:'microsoft yahei';font-size:16px;">
配置hive-env.sh的环境 <br>
配置hadoop的家目录，配置hive的家目录，已及配置hive配置文件的目录</p>
<p style="line-height:27.2px;color:rgb(63,63,63);font-family:'microsoft yahei';font-size:16px;">
<img src="https://img-blog.csdn.net/20171023162843807?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2h1a3VuMTIz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title="" style="border:0px;vertical-align:middle;"></p>
<p style="line-height:27.2px;color:rgb(63,63,63);font-family:'microsoft yahei';font-size:16px;">
配置hive-site.xml</p>
<pre class="prettyprint" style="font-family:'Source Code Pro', monospace;font-size:14px;line-height:23.8px;color:rgb(51,51,51);"><code class="hljs xml has-numbering" style="font-family:'Source Code Pro', monospace;font-size:12.6px;color:inherit;display:block;background:transparent;"><span class="hljs-tag" style="color:rgb(0,102,102);">&lt;<span class="hljs-title" style="color:rgb(0,0,136);">property</span>&gt;</span>
    <span class="hljs-tag" style="color:rgb(0,102,102);">&lt;<span class="hljs-title" style="color:rgb(0,0,136);">name</span>&gt;</span>hive.metastore.local<span class="hljs-tag" style="color:rgb(0,102,102);">&lt;/<span class="hljs-title" style="color:rgb(0,0,136);">name</span>&gt;</span>
    <span class="hljs-tag" style="color:rgb(0,102,102);">&lt;<span class="hljs-title" style="color:rgb(0,0,136);">value</span>&gt;</span>true<span class="hljs-tag" style="color:rgb(0,102,102);">&lt;/<span class="hljs-title" style="color:rgb(0,0,136);">value</span>&gt;</span>
    <span class="hljs-tag" style="color:rgb(0,102,102);">&lt;<span class="hljs-title" style="color:rgb(0,0,136);">description</span>/&gt;</span>
  <span class="hljs-tag" style="color:rgb(0,102,102);">&lt;/<span class="hljs-title" style="color:rgb(0,0,136);">property</span>&gt;</span>

  <span class="hljs-comment" style="color:rgb(136,0,0);">&lt;!--配置mysql--&gt;</span>
  <span class="hljs-tag" style="color:rgb(0,102,102);">&lt;<span class="hljs-title" style="color:rgb(0,0,136);">property</span>&gt;</span>
    <span class="hljs-tag" style="color:rgb(0,102,102);">&lt;<span class="hljs-title" style="color:rgb(0,0,136);">name</span>&gt;</span>javax.jdo.option.ConnectionURL<span class="hljs-tag" style="color:rgb(0,102,102);">&lt;/<span class="hljs-title" style="color:rgb(0,0,136);">name</span>&gt;</span>
    <span class="hljs-tag" style="color:rgb(0,102,102);">&lt;<span class="hljs-title" style="color:rgb(0,0,136);">value</span>&gt;</span>jdbc:mysql://192.168.1.3:3306/hive<span class="hljs-tag" style="color:rgb(0,102,102);">&lt;/<span class="hljs-title" style="color:rgb(0,0,136);">value</span>&gt;</span>
    <span class="hljs-tag" style="color:rgb(0,102,102);">&lt;<span class="hljs-title" style="color:rgb(0,0,136);">description</span>/&gt;</span>
  <span class="hljs-tag" style="color:rgb(0,102,102);">&lt;/<span class="hljs-title" style="color:rgb(0,0,136);">property</span>&gt;</span>

  <span class="hljs-tag" style="color:rgb(0,102,102);">&lt;<span class="hljs-title" style="color:rgb(0,0,136);">property</span>&gt;</span>
    <span class="hljs-tag" style="color:rgb(0,102,102);">&lt;<span class="hljs-title" style="color:rgb(0,0,136);">name</span>&gt;</span>javax.jdo.option.ConnectionDriverName<span class="hljs-tag" style="color:rgb(0,102,102);">&lt;/<span class="hljs-title" style="color:rgb(0,0,136);">name</span>&gt;</span>
    <span class="hljs-tag" style="color:rgb(0,102,102);">&lt;<span class="hljs-title" style="color:rgb(0,0,136);">value</span>&gt;</span>com.mysql.jdbc.Driver<span class="hljs-tag" style="color:rgb(0,102,102);">&lt;/<span class="hljs-title" style="color:rgb(0,0,136);">value</span>&gt;</span>
    <span class="hljs-tag" style="color:rgb(0,102,102);">&lt;<span class="hljs-title" style="color:rgb(0,0,136);">description</span>/&gt;</span>
  <span class="hljs-tag" style="color:rgb(0,102,102);">&lt;/<span class="hljs-title" style="color:rgb(0,0,136);">property</span>&gt;</span>

  <span class="hljs-tag" style="color:rgb(0,102,102);">&lt;<span class="hljs-title" style="color:rgb(0,0,136);">property</span>&gt;</span>
    <span class="hljs-tag" style="color:rgb(0,102,102);">&lt;<span class="hljs-title" style="color:rgb(0,0,136);">name</span>&gt;</span>javax.jdo.option.ConnectionUserName<span class="hljs-tag" style="color:rgb(0,102,102);">&lt;/<span class="hljs-title" style="color:rgb(0,0,136);">name</span>&gt;</span>
    <span class="hljs-tag" style="color:rgb(0,102,102);">&lt;<span class="hljs-title" style="color:rgb(0,0,136);">value</span>&gt;</span>root<span class="hljs-tag" style="color:rgb(0,102,102);">&lt;/<span class="hljs-title" style="color:rgb(0,0,136);">value</span>&gt;</span>
    <span class="hljs-tag" style="color:rgb(0,102,102);">&lt;<span class="hljs-title" style="color:rgb(0,0,136);">description</span>/&gt;</span>
  <span class="hljs-tag" style="color:rgb(0,102,102);">&lt;/<span class="hljs-title" style="color:rgb(0,0,136);">property</span>&gt;</span>

   <span class="hljs-tag" style="color:rgb(0,102,102);">&lt;<span class="hljs-title" style="color:rgb(0,0,136);">property</span>&gt;</span>
    <span class="hljs-tag" style="color:rgb(0,102,102);">&lt;<span class="hljs-title" style="color:rgb(0,0,136);">name</span>&gt;</span>javax.jdo.option.ConnectionPassword<span class="hljs-tag" style="color:rgb(0,102,102);">&lt;/<span class="hljs-title" style="color:rgb(0,0,136);">name</span>&gt;</span>
    <span class="hljs-tag" style="color:rgb(0,102,102);">&lt;<span class="hljs-title" style="color:rgb(0,0,136);">value</span>&gt;</span>mysql<span class="hljs-tag" style="color:rgb(0,102,102);">&lt;/<span class="hljs-title" style="color:rgb(0,0,136);">value</span>&gt;</span>
    <span class="hljs-tag" style="color:rgb(0,102,102);">&lt;<span class="hljs-title" style="color:rgb(0,0,136);">description</span>/&gt;</span>
  <span class="hljs-tag" style="color:rgb(0,102,102);">&lt;/<span class="hljs-title" style="color:rgb(0,0,136);">property</span>&gt;</span></code></pre><ul class="pre-numbering" style="list-style:none;line-height:23.8px;width:50px;border-right-width:1px;border-right-style:solid;border-right-color:rgb(221,221,221);text-align:right;background-color:rgb(238,238,238);"><li style="list-style:none;color:rgb(153,153,153);">1</li><li style="list-style:none;color:rgb(153,153,153);">2</li><li style="list-style:none;color:rgb(153,153,153);">3</li><li style="list-style:none;color:rgb(153,153,153);">4</li><li style="list-style:none;color:rgb(153,153,153);">5</li><li style="list-style:none;color:rgb(153,153,153);">6</li><li style="list-style:none;color:rgb(153,153,153);">7</li><li style="list-style:none;color:rgb(153,153,153);">8</li><li style="list-style:none;color:rgb(153,153,153);">9</li><li style="list-style:none;color:rgb(153,153,153);">10</li><li style="list-style:none;color:rgb(153,153,153);">11</li><li style="list-style:none;color:rgb(153,153,153);">12</li><li style="list-style:none;color:rgb(153,153,153);">13</li><li style="list-style:none;color:rgb(153,153,153);">14</li><li style="list-style:none;color:rgb(153,153,153);">15</li><li style="list-style:none;color:rgb(153,153,153);">16</li><li style="list-style:none;color:rgb(153,153,153);">17</li><li style="list-style:none;color:rgb(153,153,153);">18</li><li style="list-style:none;color:rgb(153,153,153);">19</li><li style="list-style:none;color:rgb(153,153,153);">20</li><li style="list-style:none;color:rgb(153,153,153);">21</li><li style="list-style:none;color:rgb(153,153,153);">22</li><li style="list-style:none;color:rgb(153,153,153);">23</li><li style="list-style:none;color:rgb(153,153,153);">24</li><li style="list-style:none;color:rgb(153,153,153);">25</li><li style="list-style:none;color:rgb(153,153,153);">26</li><li style="list-style:none;color:rgb(153,153,153);">27</li><li style="list-style:none;color:rgb(153,153,153);">28</li><li style="list-style:none;color:rgb(153,153,153);">29</li><li style="list-style:none;color:rgb(153,153,153);">30</li></ul><p style="line-height:27.2px;color:rgb(63,63,63);font-family:'microsoft yahei';font-size:16px;">
注意，我用的是windows下的mysql，还有，将mysql的驱动jar包放在hive的lib下</p>
<p style="line-height:27.2px;color:rgb(63,63,63);font-family:'microsoft yahei';font-size:16px;">
启动hive，启动之前必须初始化数据库：我初始化的是mysql数据库</p>
<pre class="prettyprint" style="font-family:'Source Code Pro', monospace;font-size:14px;line-height:23.8px;color:rgb(51,51,51);"><code class="hljs brainfuck has-numbering" style="font-family:'Source Code Pro', monospace;font-size:12.6px;color:inherit;display:block;background:transparent;"><span class="hljs-string" style="color:rgb(0,136,0);">.</span><span class="hljs-comment" style="color:rgb(136,0,0);">/schemaTool</span>   <span class="hljs-literal" style="color:rgb(0,102,102);">-</span><span class="hljs-literal" style="color:rgb(0,102,102);">-</span><span class="hljs-comment" style="color:rgb(136,0,0);">initSchema</span>  <span class="hljs-literal" style="color:rgb(0,102,102);">-</span><span class="hljs-literal" style="color:rgb(0,102,102);">-</span><span class="hljs-comment" style="color:rgb(136,0,0);">dbType</span> <span class="hljs-comment" style="color:rgb(136,0,0);">mysql</span></code></pre><ul class="pre-numbering" style="list-style:none;line-height:23.8px;width:50px;border-right-width:1px;border-right-style:solid;border-right-color:rgb(221,221,221);text-align:right;background-color:rgb(238,238,238);"><li style="list-style:none;color:rgb(153,153,153);">1</li></ul><p style="line-height:27.2px;color:rgb(63,63,63);font-family:'microsoft yahei';font-size:16px;">
启动hive</p>
<pre class="prettyprint" style="font-family:'Source Code Pro', monospace;font-size:14px;line-height:23.8px;color:rgb(51,51,51);"><code class="hljs has-numbering" style="font-family:'Source Code Pro', monospace;font-size:12.6px;color:inherit;display:block;background:transparent;">./hive</code></pre><ul class="pre-numbering" style="list-style:none;line-height:23.8px;width:50px;border-right-width:1px;border-right-style:solid;border-right-color:rgb(221,221,221);text-align:right;background-color:rgb(238,238,238);"><li style="list-style:none;color:rgb(153,153,153);">1</li></ul><p style="line-height:27.2px;color:rgb(63,63,63);font-family:'microsoft yahei';font-size:16px;">
<img src="https://img-blog.csdn.net/20171023163117963?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2h1a3VuMTIz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title="" style="border:0px;vertical-align:middle;"></p>
<p style="line-height:27.2px;color:rgb(63,63,63);font-family:'microsoft yahei';font-size:16px;">
HDFS上面也有建好的数据表</p>
<p style="line-height:27.2px;color:rgb(63,63,63);font-family:'microsoft yahei';font-size:16px;">
<img src="https://img-blog.csdn.net/20171023163213060?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2h1a3VuMTIz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title="" style="border:0px;vertical-align:middle;"></p>
<p style="line-height:27.2px;color:rgb(63,63,63);font-family:'microsoft yahei';font-size:16px;">
启动spark，启动之前将mysql的驱动包，加入到spark的lib下</p>
<p style="line-height:27.2px;color:rgb(63,63,63);font-family:'microsoft yahei';font-size:16px;">
<img src="https://img-blog.csdn.net/20171023163250258?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2h1a3VuMTIz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title="" style="border:0px;vertical-align:middle;"></p>
<p style="line-height:27.2px;color:rgb(63,63,63);font-family:'microsoft yahei';font-size:16px;">
启动spark-shell</p>
<p style="line-height:27.2px;color:rgb(63,63,63);font-family:'microsoft yahei';font-size:16px;">
<img src="https://img-blog.csdn.net/20171023163315190?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2h1a3VuMTIz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title="" style="border:0px;vertical-align:middle;"></p>
<p style="line-height:27.2px;color:rgb(63,63,63);font-family:'microsoft yahei';font-size:16px;">
建表：</p>
<p style="line-height:27.2px;color:rgb(63,63,63);font-family:'microsoft yahei';font-size:16px;">
<img src="https://img-blog.csdn.net/20171023163355748?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2h1a3VuMTIz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title="" style="border:0px;vertical-align:middle;"></p>
<p style="line-height:27.2px;color:rgb(63,63,63);font-family:'microsoft yahei';font-size:16px;">
HDFS上面也有spark建好的数据表</p>
<p style="line-height:27.2px;color:rgb(63,63,63);font-family:'microsoft yahei';font-size:16px;">
<img src="https://img-blog.csdn.net/20171023163425132?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2h1a3VuMTIz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title="" style="border:0px;vertical-align:middle;"></p>
<p style="line-height:27.2px;color:rgb(63,63,63);font-family:'microsoft yahei';font-size:16px;">
Spark整合hive完毕</p>
<p style="line-height:27.2px;color:rgb(63,63,63);font-family:'microsoft yahei';font-size:16px;">
宁波注射除皱医院http://www.iyestar.com/wzx/zscz/<br></p>
            </div>
                </div>