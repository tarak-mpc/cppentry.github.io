---
layout:     post
title:      hadoop-quickstart-命令行方式打包运行wordcount
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div>在hadoop环境配置完成后，运行第一个例子程序：<br>
1，创建文件</div>
<div>#echo "hello world" &gt;&gt; test1.txt</div>
<div>#echo "hello hadoop" &gt;&gt; test2.txt<br><br>
2，上传文件到hdfs<br></div>
<div>#cd ../hadoop</div>
<div>#bin/hadoop dfs -put ../input in </div>
<div>#bin/hadoop dfs -ls ./in/*</div>
<div><br>
3，,进入/home/hadoop/src/examples/org/apache/hadoop/examples</div>
<div><br>
4，创建test目录  <br>
#mkdir test<br><br>
5,编译</div>
<div>#javac -classpath /home/hadoop/hadoop-core-1.2.1.jar:/home/hadoop/lib/commons-cli-1.2.jar -d test WordCount.java </div>
<div><br>
6,把class文件打包成jar</div>
<div>jar -cvf wordcount.jar -C test/ .</div>
<div><br>
7,运行</div>
<div>#/home/hadoop/bin/hadoop jar wordcount.jar org.apache.hadoop.examples.WordCount input test</div>
<div><br style="background-color:inherit;">
8,查看结果</div>
<div>bin/hadoop dfsadmin -report</div>
            </div>
                </div>