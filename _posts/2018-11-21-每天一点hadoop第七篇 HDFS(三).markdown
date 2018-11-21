---
layout:     post
title:      每天一点hadoop第七篇 HDFS(三)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="hdfs-的文件的shell-命令">HDFS 的文件的shell 命令</h1>

<blockquote>
  <p>这是hadoop1.1.2上进行操作的 （2.x的文件命令大小相同） <br>
  启动hadoop <code>./start -all.sh</code> <br>
  使用命令：hadoop fs -help  查看hadoop fs的shell命令 <br>
  hdfs的操作命令与linux的操作命令相差不多 <br>
  HDFS的命令： <br>
  -help [cmd]    //显示命令的帮助信息 <br>
    -ls(r)    //显示当前目录下所有文件 <br>
    -du(s)    //显示目录中所有文件大小 <br>
    -count[-q]    //显示目录中文件数量 <br>
    -mv   //移动多个文件到目标目录 <br>
    -cp   //复制多个文件到目标目录 <br>
    -rm(r)      //删除文件(夹) <br>
    -put     //本地文件复制到hdfs <br>
    -copyFromLocal  //同put <br>
    -moveFromLocal  //从本地文件移动到hdfs <br>
    -get [-ignoreCrc]    //复制文件到本地，可以忽略crc校验 <br>
    -getmerge        //将源目录中的所有文件排序合并到一个文件中 <br>
    -cat   //在终端显示文件内容 <br>
    -text  //在终端显示文件内容 <br>
    -copyToLocal [-ignoreCrc]    //复制到本地 <br>
    -moveToLocal   <br>
    -mkdir    //创建文件夹 <br>
    -touchz   //创建一个空白文件</p>
</blockquote>



<h2 id="hdfs-shell-命令练习">HDFS shell 命令练习</h2>

<blockquote>
  <p>hadoop fs -ls /  查看HDFS根目录 <br>
  hadoop fs -mkdir /test 在根目录创建一个目录test <br>
  hadoop fs -mkdir /test1 在根目录创建一个目录test1 <br>
  hadoop fs -put /test.txt /test　 <br>
  hadoop fs -get /test/test.txt . <br>
  hadoop fs -cp /test/test.txt /test1 <br>
  hadoop fs -rm /test1/test.txt <br>
  hadoop fs -mv /test/test.txt /test1 <br>
  hadoop fs -rmr /test1 <br>
  <img src="https://img-blog.csdn.net/20150420124823886" alt="这里写图片描述" title=""></p>
</blockquote>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>