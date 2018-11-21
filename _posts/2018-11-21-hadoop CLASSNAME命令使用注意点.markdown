---
layout:     post
title:      hadoop CLASSNAME命令使用注意点
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                Hadoop中可是使用hadoop CLASSNAME命令。这个CLASSNAME就是你写好的类名。hadoop CLASSNAME命令类似于java classname。<br>使用hadoop CLASSNAM之前，你需要设置HADOOP_CLASSPATH.<br><pre><code class="language-java"><br>export  HADOOP_CLASSPATH=/home/hadoop/jardir/*.jar:/home/hadoop/workspace/hdfstest/bin/<br></code></pre><br>其中/home/hadoop/jardir/包含了我所有的hadoop的jar包。<br>/home/hadoop/workspace/hdfstest/bin/就是我的开发class的所在目录，我使用eclipse写java开发，由于eclipse有自动编译的功能，写好之后，就可以直接在命令行运行hadoop CLASSNAME的命令：<br><pre><code class="language-java"><br>hadoop FileSystemDoubleCat hdfs://Hadoop:8020/xstartup<br></code></pre><br><br>你同样可以将你的工程打成runable jar包（将所有的jar包打包）。<br>然后运行hadoop jar jar包名 类型 参数1 。<br>每一次都要打成jar包，这对于测试来说极不方便的。。。<br><br><br>原创，转载请注明出处。            </div>
                </div>