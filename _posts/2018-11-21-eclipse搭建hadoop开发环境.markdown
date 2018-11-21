---
layout:     post
title:      eclipse搭建hadoop开发环境
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>1.版本</p>
<p><span></span>hadoop：apache0.20.0</p>
<p><span></span>eclipse：3.3.0</p>
<p>2.插件</p>
<p><span></span>hadoop自带，contrib/eclipse_plugin</p>
<p>3.步骤</p>
<p><span></span>* 将hadoop自带插件拷贝至eclipse plugins目录</p>
<p><span></span>* 启动eclipse，window--&gt;preference--&gt; hadoop map/reduce 指定本地hadoop目录</p>
<p>* window--&gt;show view --&gt;other ,增加mapreduce view</p>
<p><span></span>* 重启eclipse，修改hadoop.job.ugi参数，默认为“Drwho，Drwho“，修改为远端hadoop安装用户"xx,xx"</p>
<p><span></span>参数查找过程：</p>
<p><img src="http://hi.csdn.net/attachment/201110/28/0_131976807046gI.gif" alt=""></p>
<p><br></p>
<p><img src="http://hi.csdn.net/attachment/201110/28/0_13197681559w3Q.gif" alt=""><br></p>
<p><img src="http://hi.csdn.net/attachment/201110/28/0_1319768171APxz.gif" alt=""><br></p>
<p>4 注意</p>
<p>*  一开始可能没有hadoop.job.ugi参数，此时需要重启eclipse</p>
<p><span></span>* hadoop的配置文件（core/hdfs/mapred-site.xml，masters，slaves尽量使用ip而非机器名）</p>
<p><span></span>* 本地windows机器host文件，指定hadoop安装机器的ip和机器名</p>
            </div>
                </div>