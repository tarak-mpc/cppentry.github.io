---
layout:     post
title:      使用java的Api访问Hdfs的实例
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/mapbar_front/article/details/83594427				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-light">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>hadoop的hdfs的基本操作，除了使用shell脚本的方式，也可以使用JavaApi进行操作。<br>
hdfs shell 命令：</p>
<p>1，创建hdfs文件的创建<br>
hdfs dfs -mkdir -p /home/test</p>
<p>2，上传本地文件到hdfs上<br>
hdfs dfs -put a.txt /home/test</p>
<p>3，查看上传的文件大小<br>
hdfs dfs -du /home/test</p>
<p>4，删除hdfs目录<br>
hdfs dfs -rm -r /home<br>
其中，-r表示递归删除子目录</p>
<p>5，拷贝文件到相应目录下<br>
hdfs dfs moveFromLocal readme.txt /home/test</p>
<p>6，文件一致性检查命令 fsck<br>
Hadoop fsck /home/test —files -blocks -locations -racks</p>
<p>7，distcp分布式文件复制命令<br>
Hadoop distcp … …</p>
<p>hdfs的Java Api的使用：</p>
<p>首先，需要我们引入hdfs的第三方依赖包。</p>
<pre><code>package org.training.hadoop.hdfs;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;
</code></pre>
<p>比如我们需要创建一个/tmp/test的目录：</p>
<pre><code>String path1 = "hdfs://hadoop:9000/tmp/mkdirs-test"
Configuration conf = new Configuration();
Path myPath = new Path(path);
fs = myPath.getFileSystem(conf);

fs.mkdirs(myPath);
</code></pre>
<p>其中，fs的mkdirs就是用来创建一个hdfs上的文件。</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>