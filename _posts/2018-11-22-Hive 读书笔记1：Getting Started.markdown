---
layout:     post
title:      Hive 读书笔记1：Getting Started
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>bin/hive --help</p>
<p>Usage ./hive &lt;parameters&gt; --service serviceName &lt;service parameters&gt;<br>
Service List: <span style="color:#000099;">cli help hiveserver hwi jar lineage metastore rcfilecat（Hive支持的service）</span><br></p>
<p>对于某个具体的服务，要获取帮助的话，可以：</p>
<p>hive --help --service cli</p>
<p><br></p>
<p>hive中的变量和属性有这样四个名字空间：hivevar（可以读写），hiveconf（可以读写），system（可以读写），env（只读）。</p>
<p>1. --define key=value 选项等价于 --hivevar key=value，表示在hivevar名字空间下定义了这样一个变量。<br></p>
<p>2. 查看某个变量：hive&gt; set env:HOME;</p>
<p>3. 查看全部变量：hive&gt; set;</p>
<p>4. 查看具有某个特征的变量：</p>
<p>hive -S -e "set" | grep encoding</p>
<p>表示获取所以带有encoding的变量。-e后跟带引号的hive指令或者查询，-S去掉多余的输出。</p>
<p><br></p>
<p><span style="color:#000099;">hive执行查询或者指令的办法：</span></p>
<p>1. 用hive进入hive shell；</p>
<p>2. -e选项，例如：</p>
<p>hive -S -e "select * FROM mytable LIMIT 3" &gt; /tmp/myquery<br></p>
<p>3. -f选项带一个文件，例如：</p>
<p>hive -f /path/to/file/withqueries.hql<br></p>
<p>如果已经在hive shell下了，用source命令执行：</p>
<p>hive&gt; source /path/to/file/withqueries.hql;<br></p>
<p><br></p>
<p><span style="color:#000099;">Hive shell下执行Linux指令和Hadoop指令</span></p>
<p>Hive shell下执行Linux shell命令，加!即可（末尾加分号），不需要退出：</p>
<p>hive&gt; ! pwd;<br></p>
<p>Hvie shell下执行hadoop的指令，直接用dfs即可，例如：</p>
<p>dfs -ls / ;<br></p>
<p><br></p>
<p><span style="color:#000099;">Hive的注释</span></p>
<p>加"--"，其后的都被认为是注释。但主要CLI不解析注释。带有注释的文件只能通过这种方式执行：hive -f script_name。</p>
<p><br></p>
<p><span style="color:#000099;">打印查询头</span></p>
<p>需要显示设置：set hive.cli.print.header=true;<br></p>
<p><br></p>
            </div>
                </div>