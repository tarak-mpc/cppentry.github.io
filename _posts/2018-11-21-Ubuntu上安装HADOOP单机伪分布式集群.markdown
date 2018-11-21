---
layout:     post
title:      Ubuntu上安装HADOOP单机伪分布式集群
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h2><strong><span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';">1、新建用户和组</span></span></strong></h2>
<blockquote style="border:none;">
<p style="font-size:14px;line-height:25.1875px;">
<span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';">addgroup hadoop</span></span></p>
<p style="font-size:14px;line-height:25.1875px;">
<span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';">adduser --ingroup hadoop hadoop</span></span></p>
<p style="font-size:14px;line-height:25.1875px;">
<span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';">注销root以hadoop用户登录</span></span></p>
</blockquote>
<p style="font-size:14px;line-height:25.1875px;">
<span style="font-family:'Microsoft YaHei';"> </span></p>
<h2><strong><span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';">2、配置SSH</span></span></strong></h2>
<blockquote style="border:none;">
<p style="font-size:14px;line-height:25.1875px;">
<span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';">ssh-keygen -t rsa（密码为空，路径默认）</span></span></p>
<p style="font-size:14px;line-height:25.1875px;">
<span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';">cp .ssh/id_rsa.pub .ssh/authorized_keys</span></span></p>
</blockquote>
<p style="font-size:14px;line-height:25.1875px;">
<span style="font-family:'Microsoft YaHei';"> </span></p>
<h2><strong><span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';">3、准备HADOOP运行环境</span></span></strong></h2>
<blockquote style="border:none;">
<p style="font-size:14px;line-height:25.1875px;">
<span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';">wget <a href="http://mirror.bit.edu.cn/apache/hadoop/common/hadoop-1.1.2/hadoop-1.1.2.tar.gz" rel="nofollow" style="color:rgb(16,138,198);">http://mirror.bit.edu.cn/apache/hadoop/common/hadoop-1.1.2/hadoop-1.1.2.tar.gz</a></span></span></p>
<p style="font-size:14px;line-height:25.1875px;">
<span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';">tar -xzvf hadoop-1.1.2.tar.gz</span></span></p>
<p style="font-size:14px;line-height:25.1875px;">
<span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';">在/home/hadoop/.bashrc 中追加：</span></span></p>
<p style="font-size:14px;line-height:25.1875px;">
<span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';">export  PATH=/home/hadoop/hadoop-1.1.2/bin:$PATH</span></span></p>
<p style="font-size:14px;line-height:25.1875px;">
<span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';">重新登录就生效</span></span></p>
<p style="font-size:14px;line-height:25.1875px;">
<span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';">ssh localhost</span></span></p>
<p style="font-size:14px;line-height:25.1875px;">
<span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';">which hadoop</span></span></p>
<p style="font-size:14px;line-height:25.1875px;">
<span style="font-family:'Microsoft YaHei';"> </span></p>
</blockquote>
<h2><strong><span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';">4、配置HADOOP运行参数</span></span></strong></h2>
<blockquote style="border:none;">
<p style="font-size:14px;line-height:25.1875px;">
<span style="font-family:'Microsoft YaHei';"><span style="font-size:18px;">vi conf/core-site.xml</span><span style="font-size:18px;"> </span></span></p>
<div class="dp-highlighter" style="width:679px;overflow:auto;margin-left:9px;line-height:25.1875px;">
<div class="bar">
<div class="tools" style="font-weight:bold;"><span style="font-family:'Microsoft YaHei';">Xml代码  <a href="" rel="nofollow" title="收藏这段代码" style="color:rgb(16,138,198);"><img class="star" src="http://yangshangchuan.iteye.com/images/icon_star.png" alt="收藏代码" style="border:0px;"></a></span></div>
</div>
</div>
</blockquote>
<div class="dp-highlighter" style="width:679px;overflow:auto;margin-left:9px;line-height:25.1875px;">
<ol start="1" class="dp-xml" style="font-size:1em;line-height:1.4em;border:1px solid rgb(209,215,220);color:rgb(43,145,175);"><li><ol><li style="font-size:1em;border-left-width:1px;border-left-style:solid;border-left-color:rgb(209,215,220);background-color:rgb(250,250,250);line-height:18px;">
<span style="color:#000000;"><span style="font-family:'Microsoft YaHei';"><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&lt;</span><span class="tag-name" style="color:rgb(0,102,153);font-weight:bold;">span</span> <span class="attribute" style="color:#FF0000;">style</span>=<span class="attribute-value" style="color:#0000FF;">"font-size: 18px;"</span><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&gt;</span><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&lt;</span><span class="tag-name" style="color:rgb(0,102,153);font-weight:bold;">property</span><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&gt;</span>  </span></span></li><li style="font-size:1em;border-left-width:1px;border-left-style:solid;border-left-color:rgb(209,215,220);background-color:rgb(250,250,250);line-height:18px;">
<span style="color:#000000;"><span style="font-family:'Microsoft YaHei';"><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&lt;</span><span class="tag-name" style="color:rgb(0,102,153);font-weight:bold;">name</span><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&gt;</span>fs.default.name<span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&lt;/</span><span class="tag-name" style="color:rgb(0,102,153);font-weight:bold;">name</span><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&gt;</span>  </span></span></li><li style="font-size:1em;border-left-width:1px;border-left-style:solid;border-left-color:rgb(209,215,220);background-color:rgb(250,250,250);line-height:18px;">
<span style="color:#000000;"><span style="font-family:'Microsoft YaHei';"><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&lt;</span><span class="tag-name" style="color:rgb(0,102,153);font-weight:bold;">value</span><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&gt;</span>hdfs://localhost:9000<span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&lt;/</span><span class="tag-name" style="color:rgb(0,102,153);font-weight:bold;">value</span><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&gt;</span>  </span></span></li><li style="font-size:1em;border-left-width:1px;border-left-style:solid;border-left-color:rgb(209,215,220);background-color:rgb(250,250,250);line-height:18px;">
<span style="color:#000000;"><span style="font-family:'Microsoft YaHei';"><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&lt;/</span><span class="tag-name" style="color:rgb(0,102,153);font-weight:bold;">property</span><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&gt;</span>  </span></span></li><li style="font-size:1em;border-left-width:1px;border-left-style:solid;border-left-color:rgb(209,215,220);background-color:rgb(250,250,250);line-height:18px;">
<span style="color:#000000;"><span style="font-family:'Microsoft YaHei';"><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&lt;</span><span class="tag-name" style="color:rgb(0,102,153);font-weight:bold;">property</span><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&gt;</span>  </span></span></li><li style="font-size:1em;border-left-width:1px;border-left-style:solid;border-left-color:rgb(209,215,220);background-color:rgb(250,250,250);line-height:18px;">
<span style="color:#000000;"><span style="font-family:'Microsoft YaHei';"><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&lt;</span><span class="tag-name" style="color:rgb(0,102,153);font-weight:bold;">name</span><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&gt;</span>hadoop.tmp.dir<span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&lt;/</span><span class="tag-name" style="color:rgb(0,102,153);font-weight:bold;">name</span><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&gt;</span>  </span></span></li><li style="font-size:1em;border-left-width:1px;border-left-style:solid;border-left-color:rgb(209,215,220);background-color:rgb(250,250,250);line-height:18px;">
<span style="color:#000000;"><span style="font-family:'Microsoft YaHei';"><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&lt;</span><span class="tag-name" style="color:rgb(0,102,153);font-weight:bold;">value</span><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&gt;</span>/home/hadoop/tmp<span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&lt;/</span><span class="tag-name" style="color:rgb(0,102,153);font-weight:bold;">value</span><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&gt;</span>  </span></span></li><li style="font-size:1em;border-left-width:1px;border-left-style:solid;border-left-color:rgb(209,215,220);background-color:rgb(250,250,250);line-height:18px;">
<span style="color:#000000;"><span style="font-family:'Microsoft YaHei';"><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&lt;/</span><span class="tag-name" style="color:rgb(0,102,153);font-weight:bold;">property</span><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&gt;</span>  </span></span></li><li style="font-size:1em;border-left-width:1px;border-left-style:solid;border-left-color:rgb(209,215,220);background-color:rgb(250,250,250);line-height:18px;">
<span style="color:#000000;"><span style="font-family:'Microsoft YaHei';"><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&lt;/</span><span class="tag-name" style="color:rgb(0,102,153);font-weight:bold;">span</span><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&gt;</span>  </span></span></li></ol></li></ol></div>
<blockquote style="border:none;">
<p style="font-size:14px;line-height:25.1875px;">
<span style="font-family:'Microsoft YaHei';"><span style="font-size:18px;"> </span><span style="font-size:18px;">vi conf/hdfs-site.xml</span><span style="font-size:18px;"> </span></span></p>
<div class="dp-highlighter" style="width:679px;overflow:auto;margin-left:9px;line-height:25.1875px;">
<div class="bar">
<div class="tools" style="font-weight:bold;"><span style="font-family:'Microsoft YaHei';">Xml代码  <a href="" rel="nofollow" title="收藏这段代码" style="color:rgb(16,138,198);"><img class="star" src="http://yangshangchuan.iteye.com/images/icon_star.png" alt="收藏代码" style="border:0px;"></a></span></div>
</div>
</div>
</blockquote>
<div class="dp-highlighter" style="width:679px;overflow:auto;margin-left:9px;line-height:25.1875px;">
<ol start="1" class="dp-xml" style="font-size:1em;line-height:1.4em;border:1px solid rgb(209,215,220);color:rgb(43,145,175);"><li><ol><li style="font-size:1em;border-left-width:1px;border-left-style:solid;border-left-color:rgb(209,215,220);background-color:rgb(250,250,250);line-height:18px;">
<span style="color:#000000;"><span style="font-family:'Microsoft YaHei';"><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&lt;</span><span class="tag-name" style="color:rgb(0,102,153);font-weight:bold;">span</span> <span class="attribute" style="color:#FF0000;">style</span>=<span class="attribute-value" style="color:#0000FF;">"font-size: 18px;"</span><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&gt;</span><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&lt;</span><span class="tag-name" style="color:rgb(0,102,153);font-weight:bold;">property</span><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&gt;</span>  </span></span></li><li style="font-size:1em;border-left-width:1px;border-left-style:solid;border-left-color:rgb(209,215,220);background-color:rgb(250,250,250);line-height:18px;">
<span style="color:#000000;"><span style="font-family:'Microsoft YaHei';"> <span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&lt;</span><span class="tag-name" style="color:rgb(0,102,153);font-weight:bold;">name</span><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&gt;</span>dfs.name.dir<span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&lt;/</span><span class="tag-name" style="color:rgb(0,102,153);font-weight:bold;">name</span><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&gt;</span>  </span></span></li><li style="font-size:1em;border-left-width:1px;border-left-style:solid;border-left-color:rgb(209,215,220);background-color:rgb(250,250,250);line-height:18px;">
<span style="color:#000000;"><span style="font-family:'Microsoft YaHei';"> <span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&lt;</span><span class="tag-name" style="color:rgb(0,102,153);font-weight:bold;">value</span><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&gt;</span>/home/hadoop/dfs/filesystem/name<span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&lt;/</span><span class="tag-name" style="color:rgb(0,102,153);font-weight:bold;">value</span><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&gt;</span>  </span></span></li><li style="font-size:1em;border-left-width:1px;border-left-style:solid;border-left-color:rgb(209,215,220);background-color:rgb(250,250,250);line-height:18px;">
<span style="color:#000000;"><span style="font-family:'Microsoft YaHei';"><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&lt;/</span><span class="tag-name" style="color:rgb(0,102,153);font-weight:bold;">property</span><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&gt;</span>  </span></span></li><li style="font-size:1em;border-left-width:1px;border-left-style:solid;border-left-color:rgb(209,215,220);background-color:rgb(250,250,250);line-height:18px;">
<span style="color:#000000;"><span style="font-family:'Microsoft YaHei';"><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&lt;</span><span class="tag-name" style="color:rgb(0,102,153);font-weight:bold;">property</span><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&gt;</span>  </span></span></li><li style="font-size:1em;border-left-width:1px;border-left-style:solid;border-left-color:rgb(209,215,220);background-color:rgb(250,250,250);line-height:18px;">
<span style="color:#000000;"><span style="font-family:'Microsoft YaHei';"> <span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&lt;</span><span class="tag-name" style="color:rgb(0,102,153);font-weight:bold;">name</span><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&gt;</span>dfs.data.dir<span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&lt;/</span><span class="tag-name" style="color:rgb(0,102,153);font-weight:bold;">name</span><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&gt;</span>  </span></span></li><li style="font-size:1em;border-left-width:1px;border-left-style:solid;border-left-color:rgb(209,215,220);background-color:rgb(250,250,250);line-height:18px;">
<span style="color:#000000;"><span style="font-family:'Microsoft YaHei';"> <span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&lt;</span><span class="tag-name" style="color:rgb(0,102,153);font-weight:bold;">value</span><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&gt;</span>/home/hadoop/dfs/filesystem/data<span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&lt;/</span><span class="tag-name" style="color:rgb(0,102,153);font-weight:bold;">value</span><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&gt;</span>  </span></span></li><li style="font-size:1em;border-left-width:1px;border-left-style:solid;border-left-color:rgb(209,215,220);background-color:rgb(250,250,250);line-height:18px;">
<span style="color:#000000;"><span style="font-family:'Microsoft YaHei';"><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&lt;/</span><span class="tag-name" style="color:rgb(0,102,153);font-weight:bold;">property</span><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&gt;</span>  </span></span></li><li style="font-size:1em;border-left-width:1px;border-left-style:solid;border-left-color:rgb(209,215,220);background-color:rgb(250,250,250);line-height:18px;">
<span style="color:#000000;"><span style="font-family:'Microsoft YaHei';"><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&lt;</span><span class="tag-name" style="color:rgb(0,102,153);font-weight:bold;">property</span><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&gt;</span>  </span></span></li><li style="font-size:1em;border-left-width:1px;border-left-style:solid;border-left-color:rgb(209,215,220);background-color:rgb(250,250,250);line-height:18px;">
<span style="color:#000000;"><span style="font-family:'Microsoft YaHei';">  <span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&lt;</span><span class="tag-name" style="color:rgb(0,102,153);font-weight:bold;">name</span><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&gt;</span>dfs.replication<span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&lt;/</span><span class="tag-name" style="color:rgb(0,102,153);font-weight:bold;">name</span><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&gt;</span>  </span></span></li><li style="font-size:1em;border-left-width:1px;border-left-style:solid;border-left-color:rgb(209,215,220);background-color:rgb(250,250,250);line-height:18px;">
<span style="color:#000000;"><span style="font-family:'Microsoft YaHei';"> <span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&lt;</span><span class="tag-name" style="color:rgb(0,102,153);font-weight:bold;">value</span><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&gt;</span>1<span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&lt;/</span><span class="tag-name" style="color:rgb(0,102,153);font-weight:bold;">value</span><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&gt;</span>  </span></span></li><li style="font-size:1em;border-left-width:1px;border-left-style:solid;border-left-color:rgb(209,215,220);background-color:rgb(250,250,250);line-height:18px;">
<span style="color:#000000;"><span style="font-family:'Microsoft YaHei';"><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&lt;/</span><span class="tag-name" style="color:rgb(0,102,153);font-weight:bold;">property</span><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&gt;</span>  </span></span></li><li style="font-size:1em;border-left-width:1px;border-left-style:solid;border-left-color:rgb(209,215,220);background-color:rgb(250,250,250);line-height:18px;">
<span style="color:#000000;"><span style="font-family:'Microsoft YaHei';"><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&lt;/</span><span class="tag-name" style="color:rgb(0,102,153);font-weight:bold;">span</span><span class="tag" style="color:rgb(0,102,153);font-weight:bold;">&gt;</span>  </span></span></li></ol></li></ol></div>
<blockquote style="border:none;">
<p style="font-size:14px;line-height:25.1875px;">
<span style="font-family:'Microsoft YaHei';"><span style="font-size:18px;"> </span><span style="font-size:18px;">vi conf/mapred-site.xml</span></span></p>
<div class="dp-highlighter" style="width:679px;overflow:auto;margin-left:9px;line-height:25.1875px;">
<div class="bar">
<div class="tools" style="font-weight:bold;"><span style="font-family:'Microsoft YaHei';">Java代码  <a href="" rel="nofollow" title="收藏这段代码" style="color:rgb(16,138,198);"><img class="star" src="http://yangshangchuan.iteye.com/images/icon_star.png" alt="收藏代码" style="border:0px;"></a></span></div>
</div>
</div>
</blockquote>
<div class="dp-highlighter" style="width:679px;overflow:auto;margin-left:9px;line-height:25.1875px;">
<ol start="1" class="dp-j" style="font-size:1em;line-height:1.4em;border:1px solid rgb(209,215,220);color:rgb(43,145,175);"><li><ol><li style="font-size:1em;border-left-width:1px;border-left-style:solid;border-left-color:rgb(209,215,220);background-color:rgb(250,250,250);line-height:18px;">
<span style="color:#000000;"><span style="font-family:'Microsoft YaHei';">&lt;span style=<span class="string" style="color:#0000FF;">"font-size: 18px;"</span>&gt;&lt;property&gt;  </span></span></li><li style="font-size:1em;border-left-width:1px;border-left-style:solid;border-left-color:rgb(209,215,220);background-color:rgb(250,250,250);line-height:18px;">
<span style="color:#000000;"><span style="font-family:'Microsoft YaHei';"> &lt;name&gt;mapred.job.tracker&lt;/name&gt;  </span></span></li><li style="font-size:1em;border-left-width:1px;border-left-style:solid;border-left-color:rgb(209,215,220);background-color:rgb(250,250,250);line-height:18px;">
<span style="color:#000000;"><span style="font-family:'Microsoft YaHei';"> &lt;value&gt;localhost:<span class="number" style="color:rgb(192,0,0);">9001</span>&lt;/value&gt;  </span></span></li><li style="font-size:1em;border-left-width:1px;border-left-style:solid;border-left-color:rgb(209,215,220);background-color:rgb(250,250,250);line-height:18px;">
<span style="color:#000000;"><span style="font-family:'Microsoft YaHei';">&lt;/property&gt;  </span></span></li><li style="font-size:1em;border-left-width:1px;border-left-style:solid;border-left-color:rgb(209,215,220);background-color:rgb(250,250,250);line-height:18px;">
<span style="color:#000000;"><span style="font-family:'Microsoft YaHei';">&lt;property&gt;  </span></span></li><li style="font-size:1em;border-left-width:1px;border-left-style:solid;border-left-color:rgb(209,215,220);background-color:rgb(250,250,250);line-height:18px;">
<span style="color:#000000;"><span style="font-family:'Microsoft YaHei';"> &lt;name&gt;mapred.tasktracker.map.tasks.maximum&lt;/name&gt;  </span></span></li><li style="font-size:1em;border-left-width:1px;border-left-style:solid;border-left-color:rgb(209,215,220);background-color:rgb(250,250,250);line-height:18px;">
<span style="color:#000000;"><span style="font-family:'Microsoft YaHei';"> &lt;value&gt;<span class="number" style="color:rgb(192,0,0);">4</span>&lt;/value&gt;  </span></span></li><li style="font-size:1em;border-left-width:1px;border-left-style:solid;border-left-color:rgb(209,215,220);background-color:rgb(250,250,250);line-height:18px;">
<span style="color:#000000;"><span style="font-family:'Microsoft YaHei';">&lt;/property&gt;  </span></span></li><li style="font-size:1em;border-left-width:1px;border-left-style:solid;border-left-color:rgb(209,215,220);background-color:rgb(250,250,250);line-height:18px;">
<span style="color:#000000;"><span style="font-family:'Microsoft YaHei';">&lt;property&gt;  </span></span></li><li style="font-size:1em;border-left-width:1px;border-left-style:solid;border-left-color:rgb(209,215,220);background-color:rgb(250,250,250);line-height:18px;">
<span style="color:#000000;"><span style="font-family:'Microsoft YaHei';"> &lt;name&gt;mapred.tasktracker.reduce.tasks.maximum&lt;/name&gt;  </span></span></li><li style="font-size:1em;border-left-width:1px;border-left-style:solid;border-left-color:rgb(209,215,220);background-color:rgb(250,250,250);line-height:18px;">
<span style="color:#000000;"><span style="font-family:'Microsoft YaHei';"> &lt;value&gt;<span class="number" style="color:rgb(192,0,0);">4</span>&lt;/value&gt;  </span></span></li><li style="font-size:1em;border-left-width:1px;border-left-style:solid;border-left-color:rgb(209,215,220);background-color:rgb(250,250,250);line-height:18px;">
<span style="color:#000000;"><span style="font-family:'Microsoft YaHei';">&lt;/property&gt;  </span></span></li><li style="font-size:1em;border-left-width:1px;border-left-style:solid;border-left-color:rgb(209,215,220);background-color:rgb(250,250,250);line-height:18px;">
<span style="color:#000000;"><span style="font-family:'Microsoft YaHei';">&lt;property&gt;  </span></span></li><li style="font-size:1em;border-left-width:1px;border-left-style:solid;border-left-color:rgb(209,215,220);background-color:rgb(250,250,250);line-height:18px;">
<span style="color:#000000;"><span style="font-family:'Microsoft YaHei';"> &lt;name&gt;mapred.system.dir&lt;/name&gt;  </span></span></li><li style="font-size:1em;border-left-width:1px;border-left-style:solid;border-left-color:rgb(209,215,220);background-color:rgb(250,250,250);line-height:18px;">
<span style="color:#000000;"><span style="font-family:'Microsoft YaHei';"> &lt;value&gt;/home/hadoop/mapreduce/system&lt;/value&gt;  </span></span></li><li style="font-size:1em;border-left-width:1px;border-left-style:solid;border-left-color:rgb(209,215,220);background-color:rgb(250,250,250);line-height:18px;">
<span style="color:#000000;"><span style="font-family:'Microsoft YaHei';">&lt;/property&gt;  </span></span></li><li style="font-size:1em;border-left-width:1px;border-left-style:solid;border-left-color:rgb(209,215,220);background-color:rgb(250,250,250);line-height:18px;">
<span style="color:#000000;"><span style="font-family:'Microsoft YaHei';">&lt;property&gt;  </span></span></li><li style="font-size:1em;border-left-width:1px;border-left-style:solid;border-left-color:rgb(209,215,220);background-color:rgb(250,250,250);line-height:18px;">
<span style="color:#000000;"><span style="font-family:'Microsoft YaHei';"> &lt;name&gt;mapred.local.dir&lt;/name&gt;  </span></span></li><li style="font-size:1em;border-left-width:1px;border-left-style:solid;border-left-color:rgb(209,215,220);background-color:rgb(250,250,250);line-height:18px;">
<span style="color:#000000;"><span style="font-family:'Microsoft YaHei';"> &lt;value&gt;/home/hadoop/mapreduce/local&lt;/value&gt;  </span></span></li><li style="font-size:1em;border-left-width:1px;border-left-style:solid;border-left-color:rgb(209,215,220);background-color:rgb(250,250,250);line-height:18px;">
<span style="color:#000000;"><span style="font-family:'Microsoft YaHei';">&lt;/property&gt;  </span></span></li><li style="font-size:1em;border-left-width:1px;border-left-style:solid;border-left-color:rgb(209,215,220);background-color:rgb(250,250,250);line-height:18px;">
<span style="color:#000000;"><span style="font-family:'Microsoft YaHei';">&lt;/span&gt;  </span></span></li></ol></li></ol></div>
<p style="font-size:14px;line-height:25.1875px;">
<span style="font-family:'Microsoft YaHei';"> </span></p>
<h2><strong><span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';">5、格式化名称节点并启动集群</span></span></strong></h2>
<blockquote style="border:none;">
<p style="font-size:14px;line-height:25.1875px;">
<span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';">hadoop namenode -format</span></span></p>
</blockquote>
<p style="font-size:14px;line-height:25.1875px;">
<span style="font-family:'Microsoft YaHei';"> </span></p>
<h2><strong><span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';">6、启动集群并查看WEB管理界面</span></span></strong></h2>
<blockquote style="border:none;">
<p style="font-size:14px;line-height:25.1875px;">
<span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';">start-all.sh</span></span></p>
<p style="font-size:14px;line-height:25.1875px;">
<span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';">访问<a href="http://localhost:50030/" rel="nofollow" style="color:rgb(16,138,198);">http://localhost:50030</a>可以查看 JobTracker 的运行状态</span></span></p>
<p style="font-size:14px;line-height:25.1875px;">
<span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';">访问<a href="http://localhost:50060/" rel="nofollow" style="color:rgb(16,138,198);">http://localhost:50060</a>可以查看 TaskTracker 的运行状态</span></span></p>
<p style="font-size:14px;line-height:25.1875px;">
<span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';">访问<a href="http://localhost:50070/" rel="nofollow" style="color:rgb(16,138,198);">http://localhost:50070</a>可以查看 NameNode 以及整个分布式文件系统的状态，浏览分布式文件系统中的文件以及 log 等</span></span></p>
</blockquote>
<p style="font-size:14px;line-height:25.1875px;">
<span style="font-family:'Microsoft YaHei';"> </span></p>
<h2><strong><span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';">7、停止集群</span></span></strong></h2>
<blockquote style="border:none;">
<p style="font-size:14px;line-height:25.1875px;">
<span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';">stop-all.sh停止集群</span></span></p>
</blockquote>
            </div>
                </div>