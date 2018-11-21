---
layout:     post
title:      Hadoop上传文件到hdfs中
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>hadoop常见指令：</p>

<p>hdfs dfs -copyFromLocal /local/data /hdfs/data：将本地文件上传到 hdfs 上（原路径只能是一个文件） <br>
hdfs dfs -put /tmp/ /hdfs/ ：和 copyFromLocal 区别是，put 原路径可以是文件夹等 <br>
hadoop fs -ls / ：查看根目录文件 <br>
hadoop fs -ls /tmp/data：查看/tmp/data目录 <br>
hadoop fs -cat /tmp/a.txt ：查看 a.txt，与 -text 一样 <br>
hadoop fs -mkdir dir：创建目录dir <br>
hadoop fs -rmr dir：删除目录dir</p>

<p>出现 Call From master to localhost:9000 failed on connection exception…..的错误： <br>
原因没有打开权限：修改 /hadoop/etc/hadoop/hdfs.site.xml</p>

<p>找到dfs.permissions属性修改为false（默认为true）OK了 <br>
<img src="https://img-blog.csdn.net/20180826235348750?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDk5MDgxOA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>出现hdfs.DFSClient: DataStreamer Exception的问题： <br>
原因：在进行namenode格式化时多次造成那么spaceID不一致。</p>

<p>1.删除core.site.xml中临时文件指定的目录 <br>
<img src="https://img-blog.csdn.net/20180826235823292?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDk5MDgxOA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>2.重新进行格式化 <br>
hadoop name -formate </p>

<p>3.启动集群  <br>
start-all.sh</p>

<p>4.创造目录 <br>
hadoop fs -mkdir /user <br>
hadoop fs -mkdir /user/root</p>

<p>5上传文件 <br>
hdfs dfs -put movie.csv /user/root/ <br>
<img src="https://img-blog.csdn.net/20180827000147984?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDk5MDgxOA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>