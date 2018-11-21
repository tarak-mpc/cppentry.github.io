---
layout:     post
title:      Hadoop实战-中高级一部分 之 Hadoop作业调优参数调整及原理
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div class="c_t">Hadoop<strong>实战</strong>-<strong>中高级</strong>部分 之 Hadoop<strong>作业</strong>调优<strong>参数调整</strong>及<strong>原理</strong><br><p> </p>
<p> </p>
<p>Hadoop<strong>实战</strong>-<strong>中高级</strong>部分 之 Hadoop<strong>作业</strong>调优<strong>参数调整</strong>及<strong>原理</strong></p>
<div>
<div>Hadoop RestFul</div>
<div>Hadoop HDFS<strong>原理</strong>1</div>
<div>Hadoop HDFS<strong>原理</strong>2</div>
<div>Hadoop<strong>作业</strong>调优<strong>参数调整</strong>及<strong>原理</strong></div>
<div>Hadoop HA</div>
<div>Hadoop MapReduce高级编程</div>
<div>Hadoop IO</div>
<div>Hadoop MapReduce工作<strong>原理</strong></div>
<div>Hadoop 管理</div>
<div>Hadoop 集群安装</div>
<div>Hadoop RPC</div>
<div> </div>
</div>
<div></div>
<div>
<div class="O">
<div>•core-site.xml为Hadoop的核心属性文件，<strong>参数</strong>为Hadoop的核心功能，独立于HDFS与MapReduce。</div>
<div>
<div class="O"></div>
<div>
<div class="O">•fs.default.name</div>
<div class="O1">•默认值 file:///</div>
<div class="O1">•说明：设置Hadoop  namenode的hostname及port，预设是Standalone mode,如果是伪分布式文件系统要设置成</div>
<div class="O">     hdfs://localhost:9000，如果使用集群模式则配置为 hdfs://hostname:9000</div>
<div class="O">
<div class="O">•hadoop.tmp.dir</div>
<div class="O1">•默认值/tmp/hadoop-${user.name}</div>
<div class="O1">•会在tmp下根据username生成不同的目录</div>
<div class="O"> 
<div>
<div class="O">•fs.checkpoint.dir</div>
<div class="O1">•默认值${hadoop.tmp.dir}/dfs/namesecondary</div>
<div class="O1">•Sencondary Nam…………………………………………………………………………
<p style="color:#FF0000;font-size:16px;"><strong>本文固定链接：<a href="http://www.verydemo.com/demo_c134_i16010.html" rel="nofollow">http://www.verydemo.com/demo_c134_i16010.html</a></strong></p>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
            </div>
                </div>