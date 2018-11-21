---
layout:     post
title:      Hadoop 实战之Streaming（四）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<span style="font-family:'DejaVu Serif', serif;font-size:14px;">Hadoop</span><span style="font-size:14px;">本身是<span style="font-family:'DejaVu Serif', serif;">Java</span>写的，所以，给<span style="font-family:'DejaVu Serif', serif;">hadoop</span>写<span style="font-family:'DejaVu Serif', serif;">mapreduce</span>，人们会自然地想到<span style="font-family:'DejaVu Serif', serif;">java</span></span>
<p class="article_content" align="left" style="background:#FFFFFF;"><span style="font-size:14px;color:#333333;"></span></p>
<p>但<span style="font-family:'DejaVu Serif', serif;">hadoop</span>里面有个<span style="font-family:'DejaVu Serif', serif;">contrib</span>叫做<span style="font-family:'DejaVu Serif', serif;">hadoop streaming</span>，这是一个小工具，为<span style="font-family:'DejaVu Serif', serif;">hadoop</span>提供<span style="font-family:'DejaVu Serif', serif;">streaming</span>支持，使得任何支持标准<span style="font-family:'DejaVu Serif', serif;">IO
 (stdin, stdout)</span>的可执行程序都能成为<span style="font-family:'DejaVu Serif', serif;">hadoop</span>的<span style="font-family:'DejaVu Serif', serif;">mapper</span>或者<span style="font-family:'DejaVu Serif', serif;">reducer</span></p>
<p>例如：<span style="font-family:'DejaVu Serif', serif;">hadoop jar hadoop-streaming.jar -input SOME_INPUT_DIR_OR_FILE -output SOME_OUTPUT_DIR -mapper /bin/cat -reducer /usr/bin/wc</span></p>
<p>在这个例子里，就使用了<span style="font-family:'DejaVu Serif', serif;">Unix/Linux</span>自带的<span style="font-family:'DejaVu Serif', serif;">cat</span>和<span style="font-family:'DejaVu Serif', serif;">wc</span>工具来作为<span style="font-family:'DejaVu Serif', serif;">mapper / reducer</span>，是不是很神奇？</p>
<p>如果你习惯了使用一些动态语言，用动态语言来写<span style="font-family:'DejaVu Serif', serif;">mapreduce</span>吧，跟之前的编程没有任何不同，<span style="font-family:'DejaVu Serif', serif;">hadoop</span>只是运行它的一个框架，下面我演示一下用<span style="font-family:'DejaVu Serif', serif;">PHP</span>来实现数据集采样RandomSample的<span style="font-family:'DejaVu Serif', serif;">mapreduce</span>。</p>
<p class="article_content" align="left" style="background:#FFFFFF;"><span style="font-weight:bold;"><span style="color:#333333;">环境：Vmware 8.0 和ubuntu11.04</span></span></p>
<p class="article_content" align="left" style="background:#FFFFFF;"><span style="font-weight:bold;"><span style="color:#000000;"><span style="color:#333333;">Hadoop 实战之Streaming（四）---通过脚本使用Streaming</span></span></span><strong></strong></p>
<strong>第一步: 首先在/home/tanglg1987目录下新建一个start.sh脚本文件，每次启动虚拟机都要删除/tmp目录下的全部文件，重新格式化namenode，代码如下：</strong>
<div class="article_content"><pre><code class="language-plain">sudo rm -rf /tmp/*
rm -rf /home/tanglg1987/hadoop-0.20.2/logs
hadoop namenode -format
hadoop datanode -format
start-all.sh
hadoop fs -mkdir input 
hadoop dfsadmin -safemode leave</code></pre></div>
<p class="article_content" style="font-weight:bold;"><span style="color:#000000;">第二步：<span style="background-color:#FFFFFF;">给start.sh增加执行权限并启动hadoop伪分布式集群，</span><span style="background-color:#FFFFFF;">代码如下:</span></span></p>
<div class="article_content"><pre><code class="language-plain">chmod 777 /home/tanglg1987/start.sh
./start.sh </code></pre></div>
<p class="article_content"><span style="color:#000000;"><strong>运行过程如下：</strong></span></p>
<p class="article_content"><strong><img alt="" src="http://img.my.csdn.net/uploads/201210/20/1350691937_1978.png"></strong></p>
<p class="article_content" style="font-weight:bold;">第三步：<strong>上传本地文件到hdfs</strong></p>
<span style="color:#000000;"></span>
<p class="article_content">在专利局<a href="http://data.nber.org/patents/" rel="nofollow"><span style="font-family:'Times New Roman';">http://data.nber.org/patents/</span></a>网站下载专利数据</p>
<p class="article_content" style="font-weight:bold;"><a href="http://data.nber.org/patents/apat63_99.zip" rel="nofollow">http://data.nber.org/patents/apat63_99.zip</a></p>
<pre><code class="language-plain">hadoop fs -put /home/tanglg1987/apat63_99.txt input</code></pre>
<p class="article_content" style="font-weight:bold;">第四步：新建一个RandomSample.php的Php文件</p>
<pre><code class="language-php">&lt;?php
while (!feof(STDIN)) {
    $line = fgets(STDIN);
    if (mt_rand(1,100) &lt;= $argv[1]) {
        echo $line;
    }
}</code></pre>
<p></p>
<p class="article_content" style="font-weight:bold;">第五步：安装php开发环境</p>
<p class="article_content" style="font-weight:bold;"></p>
<pre><code class="language-plain">sudo apt-get install php5 php5-gd php5-cli</code></pre>
<p></p>
<p class="article_content"><span style="color:#000000;"><span style="font-family:'宋体';">测试一下：</span></span></p>
<strong><span style="font-family:'宋体';"></span></strong><pre><code class="language-plain">php -version</code></pre>
<p class="article_content"><img src="http://img.my.csdn.net/uploads/201210/23/1350953754_2040.png" alt=""></p>
<p class="article_content"><span style="color:#000000;"><strong>第六步：编写一个名为：list-4-4.sh的shell脚本</strong></span></p>
<p class="article_content"><span style="color:#000000;"><strong></strong></span></p>
<pre><code class="language-plain">$HADOOP_HOME/bin/hadoop  jar $HADOOP_HOME/hadoop-0.20.2-streaming.jar -input input/apat63_99.txt -output output -file	/home/tanglg1987/test/streaming/RandomSample.php	-mapper 'php RandomSample.php 10' D mapred.reduce.tasks=1 </code></pre>
<p></p>
<p class="article_content" style="font-weight:bold;"><span style="color:#000000;">第七步：<span style="background-color:#FFFFFF;">给list-4-4.sh增加执行权限并启动脚本</span>，代码如下：</span></p>
<pre><code class="language-plain">chmod 777 /home/tanglg1987/list-4-4.sh
./list-4-4.sh</code></pre>
<p class="article_content"><span style="color:#000000;"><strong>第<span style="font-weight:bold;">八</span>步：运行过程如下：</strong></span></p>
<span style="color:#000000;"><strong></strong></span>
<p><img alt="" src="http://img.my.csdn.net/uploads/201210/20/1350745543_2220.png"><br></p>
<p class="article_content"><span style="color:#000000;"><strong>第<span style="font-weight:bold;">九</span>步：查看结果集，运行结果如下：</strong></span></p>
<p class="article_content" style="font-weight:bold;"></p>
<div class="article_content"><img alt="" src="http://img.my.csdn.net/uploads/201210/20/1350745507_6233.png"><br></div>
            </div>
                </div>