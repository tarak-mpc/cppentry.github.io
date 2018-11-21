---
layout:     post
title:      一步一步跟我学习hadoop(3)----hadoop命令手册
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div>
<p>上节我们学习了hadoop的eclipse插件安装和wordcount程序的运行，本篇对hadoop命令进行一个系统的了解</p>
<p>hadoop的命令通过HADOOP_HOME\bin\hadoop命令触发，我们可以在命令行执行</p>
<pre><code class="language-plain">hadoop --help
</code></pre>
<p>来提示用户的命令输入。</p>
<p>hadoop命令分为两大类：用户命令和管理命令，以下是hadoop命令的详细解读</p>
<p>本篇博客是摘自官网<a href="http://hadoop.apache.org/docs/r1.0.4/cn/commands_manual.html" rel="nofollow">http://hadoop.apache.org/docs/r1.0.4/cn/commands_manual.html</a></p>
<p><br>
 对应的word文档下载地址为<a href="http://download.csdn.net/detail/wuyinggui10000/8890981" rel="nofollow">http://download.csdn.net/detail/wuyinggui10000/8890981</a></p>
<h1 align="left"><a name="_Toc290487829">0 </a>概述：</h1>
<p align="left">         所有的Hadoop命令均由bin/hadoop脚本引发。不指定参数运行hadoop脚本会打印所有命令的描述。</p>
<p align="left">The general command line syntax is:</p>
<p align="left">hadoop [--config confdir][COMMAND][GENERIC_OPTIONS][COMMAND_OPTIONS]</p>
<p align="left">命令选项描述：</p>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p align="left">命令选项</p>
</td>
<td valign="top">
<p align="left">描述</p>
</td>
</tr><tr><td valign="top">
<p align="left">--config confdir</p>
</td>
<td valign="top">
<p align="left">覆盖缺省配置目录。缺省是${HADOOP_HOME}/conf</p>
</td>
</tr><tr><td valign="top">
<p align="left">GENERIC_OPTIONS</p>
</td>
<td valign="top">
<p align="left">多个命令都支持的通用选项。</p>
</td>
</tr><tr><td valign="top">
<p align="left">COMMAND</p>
</td>
<td valign="top">
<p align="left">各种各样的命令被分为用户命令和管理命令两组。</p>
</td>
</tr><tr><td valign="top">
<p align="left">Hadoop</p>
</td>
<td valign="top">
<p align="left">有一个选项解析框架用于解析一般的选项和运行类。</p>
</td>
</tr></tbody></table><h1 align="left"><a name="_Toc290487830">1 </a>常规选项：</h1>
<p align="left">下面的选项被dfsadmin，fs，fsck，和job支持。应用程序要实现Tool接口来支持常规选项。</p>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p align="left">GENERIC_OPTION</p>
</td>
<td valign="top">
<p align="left">Description</p>
</td>
</tr><tr><td valign="top">
<p align="left">-conf&lt;configuration file&gt;</p>
</td>
<td valign="top">
<p align="left">指定应用程序的配置文件</p>
</td>
</tr><tr><td valign="top">
<p align="left">-D &lt;property=value&gt;</p>
</td>
<td valign="top">
<p align="left">为指定property指定值value</p>
</td>
</tr><tr><td valign="top">
<p align="left">-fs &lt;local|namenode:port&gt;</p>
</td>
<td valign="top">
<p align="left">指定namenode</p>
</td>
</tr><tr><td valign="top">
<p align="left">-jt &lt;local|jobtracker:port&gt;</p>
</td>
<td valign="top">
<p align="left">指定job tracker，只适用于job</p>
</td>
</tr><tr><td valign="top">
<p align="left">-files &lt;逗号分隔的文件列表&gt;</p>
</td>
<td valign="top">
<p align="left">指定要拷贝到map reduce集群的文件的逗号分隔的列表，只是用与job</p>
</td>
</tr><tr><td valign="top">
<p align="left">-libjars &lt;逗号分隔的jar列表&gt;</p>
</td>
<td valign="top">
<p align="left">指定要包含到classpath中的jar文件的逗号分隔的列表。 只适用于<a href="http://www.hadoop.org.cn/document/cn/r0.18.2/commands_manual.html#job" rel="nofollow"><span><span style="font-family:'Times New Roman';">job</span></span></a></p>
</td>
</tr><tr><td valign="top">
<p align="left">-archives &lt;逗号分隔的archive列表&gt;</p>
</td>
<td valign="top">
<p align="left">指定要被解压到计算节点上的档案文件的逗号分割的列表。 只适用于<a href="http://www.hadoop.org.cn/document/cn/r0.18.2/commands_manual.html#job" rel="nofollow"><span><span style="font-family:'Times New Roman';">job</span></span></a>。</p>
</td>
</tr></tbody></table><p align="left"></p>
<h1 align="left"><a name="_Toc290487831">2 </a>用户命令：</h1>
<p align="left">Hadoop集群用户的常用命令</p>
<h2 align="left"><a name="_Toc290487832"><span style="color:#000000;">2.1 archive</span></a></h2>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p align="left">用法编号</p>
</td>
<td valign="top">
<p align="left">命令样式</p>
</td>
<td valign="top">
<p align="left">功能</p>
</td>
</tr><tr><td valign="top">
<p align="left">1</p>
</td>
<td valign="top">
<p align="left">hadoop archive –archiveName &lt;src&gt;* &lt;dest&gt;</p>
</td>
<td valign="top">
<p align="left">创建一个hadoop档案文件</p>
</td>
</tr></tbody></table><p align="left"></p>
<p align="left">用法1：</p>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p align="left">命令选项</p>
</td>
<td valign="top">
<p align="left">描述</p>
</td>
</tr><tr><td valign="top">
<p align="left">-archiveName</p>
</td>
<td valign="top">
<p align="left">要创建的档案的名字</p>
</td>
</tr><tr><td valign="top">
<p align="left">src</p>
</td>
<td valign="top">
<p align="left">文件系统的路径名，和通常含正则表达的一样</p>
</td>
</tr><tr><td valign="top">
<p align="left">Dest</p>
</td>
<td valign="top">
<p align="left">保存档案文件的目录目标</p>
</td>
</tr></tbody></table><h2 align="left"><a name="_Toc290487833"><span style="color:#000000;">2.2 distcp</span></a></h2>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p align="left">用法编号</p>
</td>
<td valign="top">
<p align="left">命令样式</p>
</td>
<td valign="top">
<p align="left">功能</p>
</td>
</tr><tr><td valign="top">
<p align="left">2</p>
</td>
<td valign="top">
<p align="left">hadoop distcp &lt;srcurl&gt;  &lt;desturl&gt;</p>
</td>
<td valign="top">
<p align="left">递归地拷贝文件或目录</p>
</td>
</tr></tbody></table><p align="left">用法2：</p>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p align="left"><a name="OLE_LINK6"></a><a name="OLE_LINK5">命令选项</a></p>
</td>
<td valign="top">
<p align="left">描述</p>
</td>
</tr><tr><td valign="top">
<p align="left">srcurl</p>
</td>
<td valign="top">
<p align="left">文件或目录的源url</p>
</td>
</tr><tr><td valign="top">
<p align="left">desturl</p>
</td>
<td valign="top">
<p align="left">文件或目录的目标url</p>
</td>
</tr></tbody></table><p align="left"></p>
<h2 align="left"><a name="_Toc290487834"><span style="color:#000000;">2.3 fs</span></a></h2>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p align="left">用法编号</p>
</td>
<td valign="top">
<p align="left">命令样式</p>
</td>
<td valign="top">
<p align="left">功能</p>
</td>
</tr><tr><td valign="top">
<p align="left">3</p>
</td>
<td valign="top">
<p align="left">hadoop fs [GENERIC_OPTIONS] [COMMAND_OPTIONS]</p>
</td>
<td valign="top">
<p align="left">运行一个常规的文件系统客户端</p>
</td>
</tr></tbody></table><p align="left"><strong></strong></p>
<h2 align="left"><a name="_Toc290487835"><span style="color:#000000;">2.4 fsck</span></a></h2>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p align="left">用法编号</p>
</td>
<td valign="top">
<p align="left">命令样式</p>
</td>
<td valign="top">
<p align="left">功能</p>
</td>
</tr><tr><td valign="top">
<p align="left">4</p>
</td>
<td valign="top">
<p align="left"><span style="color:#000000;">hadoop fsck [</span><a href="http://www.hadoop.org.cn/document/cn/r0.18.2/commands_manual.html#%E5%B8%B8%E8%A7%84%E9%80%89%E9%A1%B9" rel="nofollow"><span style="color:#000000;">GENERIC_OPTIONS</span></a><span style="color:#000000;">]
 &lt;path&gt; [-move | -delete | -openforwrite] [-files [-blocks [-locations | -racks]]]</span></p>
</td>
<td valign="top">
<p align="left">运行HDFS文件系统检查工具</p>
</td>
</tr></tbody></table><p align="left">用法4：</p>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p align="left"><a name="OLE_LINK2"></a><a name="OLE_LINK1">命令选项</a></p>
</td>
<td valign="top">
<p align="left">描述</p>
</td>
</tr><tr><td valign="top">
<p align="left">&lt;path&gt;</p>
</td>
<td valign="top">
<p align="left">检查的起始目录</p>
</td>
</tr><tr><td valign="top">
<p align="left">-move</p>
</td>
<td valign="top">
<p align="left">移动受损文件到/lost+found</p>
</td>
</tr><tr><td valign="top">
<p align="left">-delete</p>
</td>
<td valign="top">
<p align="left">删除受损文件</p>
</td>
</tr><tr><td valign="top">
<p align="left">-openforwrite</p>
</td>
<td valign="top">
<p align="left">打印出写打开的文件</p>
</td>
</tr><tr><td valign="top">
<p align="left">-files</p>
</td>
<td valign="top">
<p align="left">打印出正被检查的文件</p>
</td>
</tr><tr><td valign="top">
<p align="left">-blocks</p>
</td>
<td valign="top">
<p align="left">打印出块信息报告</p>
</td>
</tr><tr><td valign="top">
<p align="left">-locations</p>
</td>
<td valign="top">
<p align="left">打印出每个块的位置信息</p>
</td>
</tr><tr><td valign="top">
<p align="left">-racks</p>
</td>
<td valign="top">
<p align="left">打印出data-node的网络拓扑结构</p>
</td>
</tr></tbody></table><h2 align="left"><a name="_Toc290487836"><span style="color:#000000;">2.5 jar</span></a></h2>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p align="left"><a name="OLE_LINK4"></a><a name="OLE_LINK3">用法编号</a></p>
</td>
<td valign="top">
<p align="left">命令样式</p>
</td>
<td valign="top">
<p align="left">功能</p>
</td>
</tr><tr><td valign="top">
<p align="left">5</p>
</td>
<td valign="top">
<p align="left"><span style="color:#000000;">hadoop jar &lt;jar&gt; [mainClass] args</span></p>
</td>
<td valign="top">
<p align="left"><span style="color:#000000;">运行</span><span style="color:#000000;">jar</span><span style="color:#000000;">文件</span></p>
</td>
</tr></tbody></table><p align="left"><span style="color:#000000;">用户可以把他们的</span><span style="color:#000000;">Map Reduce</span><span style="color:#000000;">代码捆绑到</span><span style="color:#000000;">jar</span><span style="color:#000000;">文件中，使用这个命令执行</span></p>
<h2 align="left"><a name="_Toc290487837">2.6 job</a></h2>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p align="left">用法编号</p>
</td>
<td valign="top">
<p align="left">命令样式</p>
</td>
<td valign="top">
<p align="left">功能</p>
</td>
</tr><tr><td valign="top">
<p align="left">6</p>
</td>
<td valign="top">
<p align="left"><span style="color:#000000;">hadoop job [</span><a href="http://www.hadoop.org.cn/document/cn/r0.18.2/commands_manual.html#%E5%B8%B8%E8%A7%84%E9%80%89%E9%A1%B9" rel="nofollow"><span style="color:#3366cc;">GENERIC_OPTIONS</span></a><span style="color:#000000;">]
 [-submit &lt;job-file&gt;] | [-status &lt;job-id&gt;] | [-counter &lt;job-id&gt; &lt;group-name&gt; &lt;counter-name&gt;] | [-kill &lt;job-id&gt;] | [-events &lt;job-id&gt; &lt;from-event-#&gt; &lt;#-of-events&gt;] | [-history [all] &lt;jobOutputDir&gt;] | [-list [all]] | [-kill-task &lt;task-id&gt;] | [-fail-task &lt;task-id&gt;]</span></p>
</td>
<td valign="top">
<p align="left"><span style="color:#000000;">运行</span><span style="color:#000000;">jar</span><span style="color:#000000;">文件</span></p>
</td>
</tr></tbody></table><p align="left"><strong>用法6</strong></p>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p align="left">命令选项</p>
</td>
<td valign="top">
<p align="left">描述</p>
</td>
</tr><tr><td valign="top">
<p align="left">-submit &lt;job-file&gt;</p>
</td>
<td valign="top">
<p align="left">提交作业</p>
</td>
</tr><tr><td valign="top">
<p align="left">-status &lt;job-id&gt;</p>
</td>
<td valign="top">
<p align="left">打印map和reduce完成百分比和所有计数器。</p>
</td>
</tr><tr><td valign="top">
<p align="left">-counter &lt;job-id&gt; &lt;group-name&gt; &lt;counter-name&gt;</p>
</td>
<td valign="top">
<p align="left">打印计数器的值。</p>
</td>
</tr><tr><td valign="top">
<p align="left">-kill &lt;job-id&gt;</p>
</td>
<td valign="top">
<p align="left">杀死指定作业。</p>
</td>
</tr><tr><td valign="top">
<p align="left">-events &lt;job-id&gt; &lt;from-event-#&gt; &lt;#-of-events&gt;</p>
</td>
<td valign="top">
<p align="left">打印给定范围内jobtracker接收到的事件细节。</p>
</td>
</tr><tr><td valign="top">
<p align="left">-history [all] &lt;jobOutputDir&gt;</p>
</td>
<td valign="top">
<p align="left">-history &lt;jobOutputDir&gt; 打印作业的细节、失败及被杀死原因的细节。更多的关于一个作业的细节比如成功的任务，做过的任务尝试等信息可以通过指定[all]选项查看。</p>
</td>
</tr><tr><td valign="top">
<p align="left">-list [all]</p>
</td>
<td valign="top">
<p align="left">-list all显示所有作业。-list只显示将要完成的作业。</p>
</td>
</tr><tr><td valign="top">
<p align="left">-kill-task &lt;task-id&gt;</p>
</td>
<td valign="top">
<p align="left">杀死任务。被杀死的任务不会不利于失败尝试。</p>
</td>
</tr><tr><td valign="top">
<p align="left">-fail-task &lt;task-id&gt;</p>
</td>
<td valign="top">
<p align="left">使任务失败。被失败的任务会对失败尝试不利。</p>
</td>
</tr></tbody></table><h2 align="left"><a name="_Toc290487838"><span style="color:#000000;">2.7 pipes</span></a></h2>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p align="left">用法编号</p>
</td>
<td valign="top">
<p align="left">命令样式</p>
</td>
<td valign="top">
<p align="left">功能</p>
</td>
</tr><tr><td valign="top">
<p align="left">7</p>
</td>
<td valign="top">
<p align="left"><span style="color:#000000;">hadoop pipes [-conf &lt;path&gt;] [-jobconf &lt;key=value&gt;, &lt;key=value&gt;, ...] [-input &lt;path&gt;] [-output &lt;path&gt;] [-jar &lt;jar file&gt;] [-inputformat &lt;class&gt;] [-map &lt;class&gt;] [-partitioner &lt;class&gt;] [-reduce &lt;class&gt;] [-writer &lt;class&gt;]
 [-program &lt;executable&gt;] [-reduces &lt;num&gt;]</span></p>
</td>
<td valign="top">
<p align="left"><span style="color:#000000;">运行</span><span style="color:#000000;">pipes</span><span style="color:#000000;">作业</span></p>
</td>
</tr></tbody></table><p align="left"><strong>用法7</strong></p>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p align="left">命令选项</p>
</td>
<td valign="top">
<p align="left">描述</p>
</td>
</tr><tr><td valign="top">
<p align="left">-conf &lt;path&gt;</p>
</td>
<td valign="top">
<p align="left">作业的配置</p>
</td>
</tr><tr><td valign="top">
<p align="left">-jobconf &lt;key=value&gt;, &lt;key=value&gt;, ...</p>
</td>
<td valign="top">
<p align="left">增加/覆盖作业的配置项</p>
</td>
</tr><tr><td valign="top">
<p align="left">-input &lt;path&gt;</p>
</td>
<td valign="top">
<p align="left">输入目录</p>
</td>
</tr><tr><td valign="top">
<p align="left">-output &lt;path&gt;</p>
</td>
<td valign="top">
<p align="left">输出目录</p>
</td>
</tr><tr><td valign="top">
<p align="left">-jar &lt;jar file&gt;</p>
</td>
<td valign="top">
<p align="left">Jar文件名</p>
</td>
</tr><tr><td valign="top">
<p align="left">-inputformat &lt;class&gt;</p>
</td>
<td valign="top">
<p align="left">InputFormat类</p>
</td>
</tr><tr><td valign="top">
<p align="left">-map &lt;class&gt;</p>
</td>
<td valign="top">
<p align="left">Java Map类</p>
</td>
</tr><tr><td valign="top">
<p align="left">-partitioner &lt;class&gt;</p>
</td>
<td valign="top">
<p align="left">Java Partitioner</p>
</td>
</tr><tr><td valign="top">
<p align="left">-reduce &lt;class&gt;</p>
</td>
<td valign="top">
<p align="left">Java Reduce类</p>
</td>
</tr><tr><td valign="top">
<p align="left">-writer &lt;class&gt;</p>
</td>
<td valign="top">
<p align="left">Java RecordWriter</p>
</td>
</tr><tr><td valign="top">
<p align="left">-program &lt;executable&gt;</p>
</td>
<td valign="top">
<p align="left">可执行程序的URI</p>
</td>
</tr><tr><td valign="top">
<p align="left">-reduces &lt;num&gt;</p>
</td>
<td valign="top">
<p align="left">reduce个数</p>
</td>
</tr></tbody></table><h2 align="left"><a name="_Toc290487839"><span style="color:#000000;">2.8 version</span></a></h2>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p>用法编号</p>
</td>
<td valign="top">
<p align="left">命令样式</p>
</td>
<td valign="top">
<p align="left">功能</p>
</td>
</tr><tr><td valign="top">
<p align="left">8</p>
</td>
<td valign="top">
<p align="left"><span style="color:#000000;">hadoop version</span></p>
</td>
<td valign="top">
<p align="left"><span style="color:#000000;">打印版本信息</span></p>
</td>
</tr></tbody></table><h2 align="left"><a name="_Toc290487840"><span style="color:#000000;">2.9 CLASSNAME</span></a></h2>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p align="left">用法编号</p>
</td>
<td valign="top">
<p align="left">命令样式</p>
</td>
<td valign="top">
<p align="left">功能</p>
</td>
</tr><tr><td valign="top">
<p align="left">9</p>
</td>
<td valign="top">
<p><span style="color:#000000;">hadoop CLASSNAME</span></p>
</td>
<td valign="top">
<p align="left"><span style="color:#000000;">hadoop</span><span style="color:#000000;">脚本可用于调调用任何类</span></p>
</td>
</tr></tbody></table><h1 align="left"><a name="_Toc290487841"><span style="color:#000000;">3</span></a><span style="color:#000000;">管理命令</span></h1>
<p><span style="color:#000000;">hadoop</span><span style="color:#000000;">集群管理员常用的命令。</span></p>
<h2 align="left"><a name="_Toc290487842"><span style="color:#000000;">3.1 balancer</span></a></h2>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p align="left">用法编号</p>
</td>
<td valign="top">
<p align="left">命令样式</p>
</td>
<td valign="top">
<p align="left">功能</p>
</td>
</tr><tr><td valign="top">
<p align="left">10</p>
</td>
<td valign="top">
<p><span style="color:#000000;">hadoop balancer [-threshold &lt;threshold&gt;]</span></p>
</td>
<td valign="top">
<p align="left"><span style="color:#000000;">运行集群平衡工具</span></p>
</td>
</tr></tbody></table><p><span style="color:#000000;">管理员可以简单的按</span><span style="color:#000000;">Ctrl-C</span><span style="color:#000000;">来停止平衡过程。</span></p>
<p><span style="color:#000000;">用法</span><span style="color:#000000;">10</span><span style="color:#000000;">：</span></p>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p align="left">命令选项</p>
</td>
<td valign="top">
<p align="left">描述</p>
</td>
</tr><tr><td>
<p align="left">-threshold &lt;threshold&gt;</p>
</td>
<td>
<p align="left">磁盘容量的百分比。这会覆盖缺省的阀值。</p>
</td>
</tr></tbody></table><h2 align="left"><a name="_Toc290487843"><span style="color:#000000;">3.2 daemonlog</span></a></h2>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p align="left">用法编号</p>
</td>
<td valign="top">
<p align="left">命令样式</p>
</td>
<td valign="top">
<p align="left">功能</p>
</td>
</tr><tr><td valign="top">
<p align="left">11</p>
</td>
<td valign="top">
<p><span style="color:#000000;">hadoop daemonlog -getlevel &lt;host:port&gt; &lt;name&gt;</span></p>
</td>
<td valign="top">
<p><span style="color:#000000;">获取或设置每个守护进程的日志级别</span></p>
</td>
</tr><tr><td valign="top">
<p align="left">12</p>
</td>
<td valign="top">
<p><span style="color:#000000;">hadoop daemonlog -setlevel &lt;host:port&gt; &lt;name&gt; &lt;level&gt;</span></p>
</td>
<td valign="top">
<p align="left"><span style="color:#000000;">同上</span></p>
</td>
</tr></tbody></table><p><span style="color:#000000;">用法</span><span style="color:#000000;">11,</span><span style="color:#000000;">，</span><span style="color:#000000;">12</span><span style="color:#000000;">：</span></p>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p><span style="color:#000000;">命令选项</span></p>
</td>
<td valign="top">
<p><span style="color:#000000;">描述</span></p>
</td>
</tr><tr><td>
<p align="left">-getlevel &lt;host:port&gt; &lt;name&gt;</p>
</td>
<td>
<p align="left">打印运行在&lt;host:port&gt;的守护进程的日志级别。这个命令内部会连接http://&lt;host:port&gt;/logLevel?log=&lt;name&gt;</p>
</td>
</tr><tr><td>
<p align="left">-setlevel &lt;host:port&gt; &lt;name&gt; &lt;level&gt;</p>
</td>
<td>
<p align="left">设置运行在&lt;host:port&gt;的守护进程的日志级别。这个命令内部会连接http://&lt;host:port&gt;/logLevel?log=&lt;name&gt;</p>
</td>
</tr></tbody></table><h2 align="left"><a name="_Toc290487844"><span style="color:#000000;">3.3 datanode</span></a></h2>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p align="left">用法编号</p>
</td>
<td valign="top">
<p align="left">命令样式</p>
</td>
<td valign="top">
<p align="left">功能</p>
</td>
</tr><tr><td valign="top">
<p align="left">13</p>
</td>
<td valign="top">
<p><span style="color:#000000;">hadoop datanode [-rollback]</span></p>
</td>
<td valign="top">
<p><span style="color:#000000;">运行一个</span><span style="color:#000000;">HDFS</span><span style="color:#000000;">的</span><span style="color:#000000;">datanode</span><span style="color:#000000;">。</span></p>
</td>
</tr></tbody></table><table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p><span style="color:#000000;">命令选项</span></p>
</td>
<td valign="top">
<p><span style="color:#000000;">描述</span></p>
</td>
</tr><tr><td>
<p align="left">-rollback</p>
</td>
<td>
<p align="left">将datanode回滚到前一个版本。这需要在停止datanode，分发老的hadoop版本之后使用。</p>
</td>
</tr></tbody></table><h2 align="left"><a name="_Toc290487845"><span style="color:#000000;">3.4 dfsadmin</span></a></h2>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p align="left">用法编号</p>
</td>
<td valign="top">
<p align="left">命令样式</p>
</td>
<td valign="top">
<p align="left">功能</p>
</td>
</tr><tr><td valign="top">
<p align="left">14</p>
</td>
<td valign="top">
<p><span style="color:#000000;">hadoop dfsadmin [</span><a href="http://www.hadoop.org.cn/document/cn/r0.18.2/commands_manual.html#%E5%B8%B8%E8%A7%84%E9%80%89%E9%A1%B9" rel="nofollow"><span style="color:#000000;"><span style="font-family:'Times New Roman';">GENERIC_OPTIONS</span></span></a><span style="color:#000000;">]
 [-report] [-safemode enter | leave | get | wait] [-refreshNodes] [-finalizeUpgrade] [-upgradeProgress status | details | force] [-metasave filename] [-setQuota &lt;quota&gt; &lt;dirname&gt;...&lt;dirname&gt;] [-clrQuota &lt;dirname&gt;...&lt;dirname&gt;] [-help [cmd]]</span></p>
</td>
<td valign="top">
<p><span style="color:#000000;">运行一个</span><span style="color:#000000;">HDFS</span><span style="color:#000000;">的</span><span style="color:#000000;">dfsadmin</span><span style="color:#000000;">客户端</span></p>
</td>
</tr></tbody></table><table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top" width="131" colspan="3">
<p><span style="color:#000000;">命令选项</span></p>
</td>
<td valign="top">
<p><span style="color:#000000;">描述</span></p>
</td>
</tr><tr><td width="131" colspan="3">
<p align="left">- report</p>
</td>
<td>
<p align="left">报告文件系统的基本信息和统计信息</p>
</td>
</tr><tr><td width="131" colspan="3">
<p align="left">-safemode enter| leave| get | wait</p>
</td>
<td>
<p align="left">安全模式维护命令。安全模式是Namenode的一个状态，这种状态下，Namenode<br>
1. 不接受对名字空间的更改(只读)<br>
2. 不复制或删除块<br>
Namenode会在启动时自动进入安全模式，当配置的块最小百分比数满足最小的副本数条件时，会自动离开安全模式。安全模式可以手动进入，但是这样的话也必须手动关闭安全模式。</p>
</td>
</tr><tr><td width="131" colspan="3">
<p align="left">-refreshNodes</p>
</td>
<td>
<p align="left">重新读取hosts和exclude文件，更新允许连到Namenode的或那些需要退出或入编的Datanode的集合。</p>
</td>
</tr><tr><td width="131" colspan="3">
<p align="left">-finalizeUpgrade</p>
</td>
<td>
<p align="left">终结HDFS的升级操作。Datanode删除前一个版本的工作目录，之后Namenode也这样做。这个操作完结整个升级过程。</p>
</td>
</tr><tr><td width="131" colspan="3">
<p align="left">-upgradeProgress status|details | force</p>
</td>
<td>
<p align="left">请求当前系统的升级状态，状态的细节，或者强制升级操作进行。</p>
</td>
</tr><tr><td width="131" colspan="3">
<p align="left">-metasave filename</p>
</td>
<td>
<p align="left">保存Namenode的主要数据结构到hadoop.log.dir属性指定的目录下的&lt;filename&gt;文件。对于下面的每一项，&lt;filename&gt;中都会一行内容与之对应<br>
1. Namenode收到的Datanode的心跳信号<br>
2. 等待被复制的块<br>
3. 正在被复制的块<br>
4. 等待被删除的块</p>
</td>
</tr><tr><td width="131" colspan="3">
<p align="left">-setQuota &lt;quota&gt; &lt;dirname&gt;...&lt;dirname&gt;</p>
</td>
<td>
<p align="left">为每个目录 &lt;dirname&gt;设定配额&lt;quota&gt;。目录配额是一个长整型整数，强制限定了目录树下的名字个数。<br>
命令会在这个目录上工作良好，以下情况会报错：<br>
1. N不是一个正整数，或者<br>
2. 用户不是管理员，或者<br>
3. 这个目录不存在或是文件，或者<br>
4. 目录会马上超出新设定的配额。</p>
</td>
</tr><tr><td width="131" colspan="3">
<p align="left">-clrQuota &lt;dirname&gt;...&lt;dirname&gt;</p>
</td>
<td>
<p align="left">为每一个目录&lt;dirname&gt;清除配额设定。<br>
命令会在这个目录上工作良好，以下情况会报错：<br>
1. 这个目录不存在或是文件，或者<br>
2. 用户不是管理员。<br>
如果目录原来没有配额不会报错。</p>
</td>
</tr><tr><td width="131" colspan="3">
<p align="left">-help [cmd]</p>
</td>
<td>
<p align="left">显示给定命令的帮助信息，如果没有给定命令，则显示所有命令的帮助信息。</p>
</td>
</tr><tr><td>
<p align="left"></p>
</td>
<td>
<p align="left"></p>
</td>
</tr></tbody></table><h2 align="left"><a name="_Toc290487846"><span style="color:#000000;">3.5 jobtracker</span></a></h2>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p>用法编号</p>
</td>
<td valign="top">
<p align="left">命令样式</p>
</td>
<td valign="top">
<p align="left">功能</p>
</td>
</tr><tr><td valign="top">
<p align="left">15</p>
</td>
<td valign="top">
<p><span style="color:#000000;">hadoop jobtracker</span></p>
</td>
<td valign="top">
<p><span style="color:#000000;">运行</span><span style="color:#000000;">MapReduce job Tracker</span><span style="color:#000000;">节点</span></p>
</td>
</tr></tbody></table><h2 align="left"><a name="_Toc290487847"><span style="color:#000000;">3.6 namenode</span></a></h2>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p align="left">用法编号</p>
</td>
<td valign="top">
<p align="left">命令样式</p>
</td>
<td valign="top">
<p align="left">功能</p>
</td>
</tr><tr><td valign="top">
<p align="left">16</p>
</td>
<td valign="top">
<p><span style="color:#000000;">hadoop namenode [-format] | [-upgrade] | [-rollback] | [-finalize] | [-importCheckpoint]</span></p>
</td>
<td valign="top">
<p><span style="color:#000000;">运行</span><span style="color:#000000;">namenode</span><span style="color:#000000;">。有关升级，回滚，升级终结的更多信息请参考</span><span style="color:#000000;"><a href="http://www.hadoop.org.cn/document/cn/r0.18.2/hdfs_user_guide.html#%E5%8D%87%E7%BA%A7%E5%92%8C%E5%9B%9E%E6%BB%9A" rel="nofollow"><span style="color:#000000;">升级和回滚</span></a></span></p>
</td>
</tr></tbody></table><table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p><span style="color:#000000;">命令选项</span></p>
</td>
<td valign="top">
<p><span style="color:#000000;">描述</span></p>
</td>
</tr><tr><td>
<p align="left">-format</p>
</td>
<td>
<p align="left">格式化namenode。它启动namenode，格式化namenode，之后关闭namenode。</p>
</td>
</tr><tr><td>
<p align="left">-upgrade</p>
</td>
<td>
<p align="left">分发新版本的hadoop后，namenode应以upgrade选项启动。</p>
</td>
</tr><tr><td>
<p align="left">-rollback</p>
</td>
<td>
<p align="left">将namenode回滚到前一版本。这个选项要在停止集群，分发老的hadoop版本后使用。</p>
</td>
</tr><tr><td>
<p align="left">-finalize</p>
</td>
<td>
<p align="left">finalize会删除文件系统的前一状态。最近的升级会被持久化，rollback选项将再不可用，升级终结操作之后，它会停掉namenode。</p>
</td>
</tr><tr><td>
<p align="left">-importCheckpoint</p>
</td>
<td>
<p align="left">从检查点目录装载镜像并保存到当前检查点目录，检查点目录由fs.checkpoint.dir指定。</p>
</td>
</tr></tbody></table><h2 align="left"><a name="_Toc290487848"><span style="color:#000000;">3.7 secondarynamenode</span></a></h2>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p align="left">用法编号</p>
</td>
<td valign="top">
<p align="left">命令样式</p>
</td>
<td valign="top">
<p align="left">功能</p>
</td>
</tr><tr><td valign="top">
<p align="left">17</p>
</td>
<td valign="top">
<p><span style="color:#000000;">hadoop secondarynamenode [-checkpoint [force]] | [-geteditsize]</span></p>
</td>
<td valign="top">
<p><span style="color:#000000;">运行</span><span style="color:#000000;">HDFS</span><span style="color:#000000;">的</span><span style="color:#000000;">secondary namenode</span><span style="color:#000000;">。</span></p>
</td>
</tr></tbody></table><table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p><span style="color:#000000;">命令选项</span></p>
</td>
<td valign="top">
<p><span style="color:#000000;">描述</span></p>
</td>
</tr><tr><td>
<p align="left">-checkpoint [force]</p>
</td>
<td>
<p align="left">如果EditLog的大小 &gt;= fs.checkpoint.size，启动Secondary namenode的检查点过程。 如果使用了-force，将不考虑EditLog的大小。</p>
</td>
</tr><tr><td>
<p align="left">-geteditsize</p>
</td>
<td>
<p align="left">打印EditLog大小。</p>
</td>
</tr></tbody></table><h2 align="left"><a name="_Toc290487849"><span style="color:#000000;">3.8 tasktracker</span></a></h2>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p align="left">用法编号</p>
</td>
<td valign="top">
<p align="left">命令样式</p>
</td>
<td valign="top">
<p align="left">功能</p>
</td>
</tr><tr><td valign="top">
<p align="left">18</p>
</td>
<td valign="top">
<p><span style="color:#000000;">hadoop tasktracker</span></p>
</td>
<td valign="top">
<p><span style="color:#000000;">运行</span><span style="color:#000000;">MapReduce</span><span style="color:#000000;">的</span><span style="color:#000000;">task Tracker</span><span style="color:#000000;">节点</span></p>
</td>
</tr></tbody></table><p></p>
</div>
<p> </p>
            </div>
                </div>