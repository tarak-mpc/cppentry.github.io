---
layout:     post
title:      linux 单机模式HBase (一)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-family:SimSun;">1,HBase 可以3中模式运行: 单机,伪分布式,分布式</span></p>
<p><span class="space" style="font-family:SimSun;color:rgb(79,79,79);text-align:justify;display:inline-block;text-indent:2em;line-height:inherit;"> </span><span style="font-family:SimSun;color:rgb(79,79,79);text-align:justify;">安装之前先确保安装了java环境</span></p>
<p><span class="space" style="font-family:SimSun;text-align:justify;display:inline-block;text-indent:2em;line-height:inherit;"><span> 配置文件默认为单机模式,无需修改hbase配置文件</span></span><span class="space" style="color:rgb(63,63,63);font-family:SimSun;display:inline-block;text-indent:2em;line-height:inherit;"><br></span></p>
<p><span class="space" style="font-family:SimSun;display:inline-block;text-indent:2em;line-height:inherit;"> </span><span style="color:rgb(63,63,63);font-family:SimSun;">默认数据文件存储在/tmp 下边</span></p>
<p></p>
<span style="font-family:SimSun;">2,安装HBase:<br><span></span><span class="space" style="display:inline-block;text-indent:2em;line-height:inherit;"> </span>获取HBase:<br><span></span><span class="space" style="display:inline-block;text-indent:2em;line-height:inherit;"> </span><span class="space" style="display:inline-block;text-indent:2em;line-height:inherit;"> </span>wget
 http://archive.apache.org/dist/hbase/hbase-0.94.27/hbase-0.94.27.tar.gz  <br><span></span><span class="space" style="display:inline-block;text-indent:2em;line-height:inherit;"> </span>解压到指定目录<br><span></span><span class="space" style="display:inline-block;text-indent:2em;line-height:inherit;"> </span><span class="space" style="display:inline-block;text-indent:2em;line-height:inherit;"> </span>mkdir
 /home/hbase -p<br><span></span><span class="space" style="display:inline-block;text-indent:2em;line-height:inherit;"> </span><span class="space" style="display:inline-block;text-indent:2em;line-height:inherit;"> </span>tar
 xvfz hbase-0.94.27.tar.gz<br><span></span><span class="space" style="display:inline-block;text-indent:2em;line-height:inherit;"> </span>配置环境变量<br><span></span><span class="space" style="display:inline-block;text-indent:2em;line-height:inherit;"> </span><span class="space" style="display:inline-block;text-indent:2em;line-height:inherit;"> </span>vi
 .bash_profile<br><span></span><span class="space" style="display:inline-block;text-indent:2em;line-height:inherit;"> </span>添加下边几行<br><span></span><span class="space" style="display:inline-block;text-indent:2em;line-height:inherit;"> </span><span class="space" style="display:inline-block;text-indent:2em;line-height:inherit;"> </span>HBASE_HOME=/home/hbase/hbase-0.94.27<br>
          HBASE_BIN=$HBASE_HOME/bin<br>
          PATH=$PATH:$HBASE_HOME:$HBASE_BIN:./<br><span style="color:rgb(79,79,79);text-align:justify;">          export PATH</span></span>
<p><span style="font-family:SimSun;"><span class="space" style="display:inline-block;text-indent:2em;line-height:inherit;"> </span><span class="space" style="display:inline-block;text-indent:2em;line-height:inherit;"> 执行 source
 .bash_profile 使更改生效</span><span class="space" style="display:inline-block;text-indent:2em;line-height:inherit;"> </span><span class="space" style="display:inline-block;text-indent:2em;line-height:inherit;"> </span></span></p>
<span style="font-family:SimSun;"><span></span><span class="space" style="display:inline-block;text-indent:2em;line-height:inherit;"> </span>直接运行:<br></span>
<p><span style="font-family:SimSun;"><span></span><span class="space" style="display:inline-block;text-indent:2em;line-height:inherit;"> </span>start-hbase.sh<span style="text-indent:2em;"> </span><br><img src="https://img-blog.csdn.net/20180125142959882?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbHZiaWFu/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="" style="text-indent:2em;"></span></p>
<span style="font-family:SimSun;"><span></span>   确认是否启动成功<span></span><br></span>
<p><span style="font-family:SimSun;"><span></span><span class="space" style="display:inline-block;text-indent:2em;line-height:inherit;"> </span>http://192.168.1.36:60010/master-status<span style="text-indent:2em;"> </span><img src="https://img-blog.csdn.net/20180125142900559?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbHZiaWFu/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="" style="text-indent:2em;"><br><span style="text-indent:2em;">停止hbase:</span></span></p>
<p><span style="font-family:SimSun;"><img src="https://img-blog.csdn.net/20180125142807205?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbHZiaWFu/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""></span></p>
<p><br></p>
            </div>
                </div>