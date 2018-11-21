---
layout:     post
title:      Hadoop Federation + HA 搭建（三） – wordCount测试
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/Wpixel/article/details/79194811				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h4 id="一-hadoop环境搭建完毕现在可以对hdfs进行操作">一. hadoop环境搭建完毕，现在可以对hdfs进行操作</h4>

<p>1 .上传文件到hdfs文件系统</p>

<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-comment">#创建一个hive文件夹</span>
[root<span class="hljs-variable">@wpixel01</span> www]<span class="hljs-comment"># hadoop fs -mkdir /hive</span>
<span class="hljs-comment">#将当前目录的文件上传到hdfs文件系统中的hive目录下</span>
[root<span class="hljs-variable">@wpixel01</span> www]<span class="hljs-comment"># hadoop fs -put hadoop-2.8.1.tar.gz /hive</span>
<span class="hljs-comment">#ls查看hdfs文件系统文件列表</span>
[root<span class="hljs-variable">@wpixel01</span> www]<span class="hljs-comment"># hadoop fs -ls /hive                     </span>
<span class="hljs-constant">Found</span> <span class="hljs-number">1</span> items
-rw-r--r--   <span class="hljs-number">3</span> root supergroup   <span class="hljs-number">35042811</span> <span class="hljs-number">2018</span>-<span class="hljs-number">01</span>-<span class="hljs-number">28</span> <span class="hljs-number">15</span><span class="hljs-symbol">:</span><span class="hljs-number">55</span> /hive/zookeeper-<span class="hljs-number">3.4</span>.<span class="hljs-number">10</span>.tar.gz
<span class="hljs-comment">#这里要主要：</span>
<span class="hljs-comment">#如果这里的hive文件夹之前没有创建，那么上传文件到hdfs文件系统的时候，会将文件名变成hive</span>
<span class="hljs-comment">#例如：hive文件夹下没有创建test文件夹，会将上传的文件重命名</span>
[root<span class="hljs-variable">@wpixel01</span> www]<span class="hljs-comment"># hadoop fs -put hadoop-2.8.1.tar.gz /hive/test</span>
<span class="hljs-comment">#这个test就是一个文件，可以从第一列的“-“看出</span>
[root<span class="hljs-variable">@wpixel01</span> www]<span class="hljs-comment"># hadoop fs -ls /hive</span>
<span class="hljs-constant">Found</span> <span class="hljs-number">2</span> items
-rw-r--r--   <span class="hljs-number">3</span> root supergroup  <span class="hljs-number">424555111</span> <span class="hljs-number">2018</span>-<span class="hljs-number">01</span>-<span class="hljs-number">28</span> <span class="hljs-number">16</span><span class="hljs-symbol">:</span><span class="hljs-number">53</span> /hive/test
-rw-r--r--   <span class="hljs-number">3</span> root supergroup   <span class="hljs-number">35042811</span> <span class="hljs-number">2018</span>-<span class="hljs-number">01</span>-<span class="hljs-number">28</span> <span class="hljs-number">15</span><span class="hljs-symbol">:</span><span class="hljs-number">55</span> /hive/zookeeper-<span class="hljs-number">3.4</span>.<span class="hljs-number">10</span>.tar.gz</code></pre>

<p>通过web页面查看 <br>
<img src="https://img-blog.csdn.net/20180129135934648?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvV3BpeGVs/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
2 .上传文件到指定的hdfs文件系统集群（ns1集群和ns2集群）</p>

<pre class="prettyprint"><code class=" hljs ruleslanguage"><span class="hljs-array">#hadoop fs </span>-put [文件名] hdfs:<span class="hljs-comment">//[集群]/[目录或文件名]</span>
[root@wpixel01 www]<span class="hljs-array"># hadoop fs </span>-put hadoop-<span class="hljs-number">2.8</span><span class="hljs-number">.1</span>.tar.gz hdfs:<span class="hljs-comment">//ns1/hbase</span>
[root@wpixel01 www]<span class="hljs-array"># hadoop fs </span>-put hadoop-<span class="hljs-number">2.8</span><span class="hljs-number">.1</span>.tar.gz hdfs:<span class="hljs-comment">//ns2/hbase</span></code></pre>

<p>web页面查看 <br>
<img src="https://img-blog.csdn.net/20180129140629922?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvV3BpeGVs/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<h4 id="二-yarn测试-wordcount">二 .Yarn测试 wordCount</h4>



<pre class="prettyprint"><code class=" hljs haml">#jar包在hadoop-2.8.1/share/hadoop/mapreduce/目录下
[root@wpixel01 www]# cd hadoop-2.8.1/share/hadoop/mapreduce/
[root@wpixel01 mapreduce]# ll
total 5084
-<span class="ruby">rw-rw-r--. <span class="hljs-number">1</span> <span class="hljs-number">500</span> <span class="hljs-number">500</span>  <span class="hljs-number">562906</span> <span class="hljs-constant">Jun</span>  <span class="hljs-number">2</span>  <span class="hljs-number">2017</span> hadoop-mapreduce-client-app-<span class="hljs-number">2.8</span>.<span class="hljs-number">1</span>.jar
</span>-<span class="ruby">rw-rw-r--. <span class="hljs-number">1</span> <span class="hljs-number">500</span> <span class="hljs-number">500</span>  <span class="hljs-number">782746</span> <span class="hljs-constant">Jun</span>  <span class="hljs-number">2</span>  <span class="hljs-number">2017</span> hadoop-mapreduce-client-common-<span class="hljs-number">2.8</span>.<span class="hljs-number">1</span>.jar
</span>-<span class="ruby">rw-rw-r--. <span class="hljs-number">1</span> <span class="hljs-number">500</span> <span class="hljs-number">500</span> <span class="hljs-number">1571185</span> <span class="hljs-constant">Jun</span>  <span class="hljs-number">2</span>  <span class="hljs-number">2017</span> hadoop-mapreduce-client-core-<span class="hljs-number">2.8</span>.<span class="hljs-number">1</span>.jar
</span>-<span class="ruby">rw-rw-r--. <span class="hljs-number">1</span> <span class="hljs-number">500</span> <span class="hljs-number">500</span>  <span class="hljs-number">195006</span> <span class="hljs-constant">Jun</span>  <span class="hljs-number">2</span>  <span class="hljs-number">2017</span> hadoop-mapreduce-client-hs-<span class="hljs-number">2.8</span>.<span class="hljs-number">1</span>.jar
</span>-<span class="ruby">rw-rw-r--. <span class="hljs-number">1</span> <span class="hljs-number">500</span> <span class="hljs-number">500</span>   <span class="hljs-number">31539</span> <span class="hljs-constant">Jun</span>  <span class="hljs-number">2</span>  <span class="hljs-number">2017</span> hadoop-mapreduce-client-hs-plugins-<span class="hljs-number">2.8</span>.<span class="hljs-number">1</span>.jar
</span>-<span class="ruby">rw-rw-r--. <span class="hljs-number">1</span> <span class="hljs-number">500</span> <span class="hljs-number">500</span>   <span class="hljs-number">67004</span> <span class="hljs-constant">Jun</span>  <span class="hljs-number">2</span>  <span class="hljs-number">2017</span> hadoop-mapreduce-client-jobclient-<span class="hljs-number">2.8</span>.<span class="hljs-number">1</span>.jar
</span>-<span class="ruby">rw-rw-r--. <span class="hljs-number">1</span> <span class="hljs-number">500</span> <span class="hljs-number">500</span> <span class="hljs-number">1587163</span> <span class="hljs-constant">Jun</span>  <span class="hljs-number">2</span>  <span class="hljs-number">2017</span> hadoop-mapreduce-client-jobclient-<span class="hljs-number">2.8</span>.<span class="hljs-number">1</span>-tests.jar
</span>-<span class="ruby">rw-rw-r--. <span class="hljs-number">1</span> <span class="hljs-number">500</span> <span class="hljs-number">500</span>   <span class="hljs-number">75501</span> <span class="hljs-constant">Jun</span>  <span class="hljs-number">2</span>  <span class="hljs-number">2017</span> hadoop-mapreduce-client-shuffle-<span class="hljs-number">2.8</span>.<span class="hljs-number">1</span>.jar
</span>-<span class="ruby">rw-rw-r--. <span class="hljs-number">1</span> <span class="hljs-number">500</span> <span class="hljs-number">500</span>  <span class="hljs-number">301938</span> <span class="hljs-constant">Jun</span>  <span class="hljs-number">2</span>  <span class="hljs-number">2017</span> hadoop-mapreduce-examples-<span class="hljs-number">2.8</span>.<span class="hljs-number">1</span>.jar
</span>drwxrwxr-x. 2 500 500    4096 Jun  2  2017 jdiff
drwxrwxr-x. 2 500 500    4096 Jun  2  2017 lib
drwxrwxr-x. 2 500 500      30 Jun  2  2017 lib-examples
drwxrwxr-x. 2 500 500    4096 Jun  2  2017 sources</code></pre>

<p>上传需要统计的文件wordCount.txt</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor">#文件内容</span>
[root@wpixel01 www]<span class="hljs-preprocessor"># cat wordCount.txt </span>
hadoop contos windows
contos zojirushi zare nike
adidas hadoop my windows google
google baidu zojirushi
<span class="hljs-preprocessor">#上传到ns1集群的/txt目录</span>
[root@wpixel01 www]<span class="hljs-preprocessor"># hadoop fs -put wordCount.txt hdfs://ns1/txt/wordCount</span>
<span class="hljs-preprocessor">#运行测试程序</span>
[root@wpixel01 mapreduce]<span class="hljs-preprocessor"># hadoop jar hadoop-mapreduce-examples-2.8.1.jar wordcount /txt/wordCount /count</span>
<span class="hljs-preprocessor">#这里是yarn计算的基本信息</span>
<span class="hljs-number">18</span>/<span class="hljs-number">01</span>/<span class="hljs-number">28</span> <span class="hljs-number">17</span>:<span class="hljs-number">43</span>:<span class="hljs-number">28</span> INFO input<span class="hljs-preprocessor">.FileInputFormat</span>: Total input files to process : <span class="hljs-number">1</span>
<span class="hljs-number">18</span>/<span class="hljs-number">01</span>/<span class="hljs-number">28</span> <span class="hljs-number">17</span>:<span class="hljs-number">43</span>:<span class="hljs-number">28</span> INFO mapreduce<span class="hljs-preprocessor">.JobSubmitter</span>: number of splits:<span class="hljs-number">1</span>
<span class="hljs-number">18</span>/<span class="hljs-number">01</span>/<span class="hljs-number">28</span> <span class="hljs-number">17</span>:<span class="hljs-number">43</span>:<span class="hljs-number">29</span> INFO mapreduce<span class="hljs-preprocessor">.JobSubmitter</span>: Submitting tokens for job: job_1517156134219_0001
<span class="hljs-number">18</span>/<span class="hljs-number">01</span>/<span class="hljs-number">28</span> <span class="hljs-number">17</span>:<span class="hljs-number">43</span>:<span class="hljs-number">31</span> INFO impl<span class="hljs-preprocessor">.YarnClientImpl</span>: Submitted application application_1517156134219_0001
<span class="hljs-number">18</span>/<span class="hljs-number">01</span>/<span class="hljs-number">28</span> <span class="hljs-number">17</span>:<span class="hljs-number">43</span>:<span class="hljs-number">31</span> INFO mapreduce<span class="hljs-preprocessor">.Job</span>: The url to track the job: http://wpixel03:<span class="hljs-number">8088</span>/proxy/application_1517156134219_0001/
<span class="hljs-number">18</span>/<span class="hljs-number">01</span>/<span class="hljs-number">28</span> <span class="hljs-number">17</span>:<span class="hljs-number">43</span>:<span class="hljs-number">31</span> INFO mapreduce<span class="hljs-preprocessor">.Job</span>: Running job: job_1517156134219_0001
<span class="hljs-number">18</span>/<span class="hljs-number">01</span>/<span class="hljs-number">28</span> <span class="hljs-number">17</span>:<span class="hljs-number">44</span>:<span class="hljs-number">35</span> INFO mapreduce<span class="hljs-preprocessor">.Job</span>: Job job_1517156134219_0001 running <span class="hljs-keyword">in</span> uber mode : false
<span class="hljs-number">18</span>/<span class="hljs-number">01</span>/<span class="hljs-number">28</span> <span class="hljs-number">17</span>:<span class="hljs-number">44</span>:<span class="hljs-number">35</span> INFO mapreduce<span class="hljs-preprocessor">.Job</span>:  map <span class="hljs-number">0</span>% reduce <span class="hljs-number">0</span>%
<span class="hljs-number">18</span>/<span class="hljs-number">01</span>/<span class="hljs-number">28</span> <span class="hljs-number">17</span>:<span class="hljs-number">44</span>:<span class="hljs-number">50</span> INFO mapreduce<span class="hljs-preprocessor">.Job</span>:  map <span class="hljs-number">100</span>% reduce <span class="hljs-number">0</span>%
<span class="hljs-number">18</span>/<span class="hljs-number">01</span>/<span class="hljs-number">28</span> <span class="hljs-number">17</span>:<span class="hljs-number">45</span>:<span class="hljs-number">01</span> INFO mapreduce<span class="hljs-preprocessor">.Job</span>:  map <span class="hljs-number">100</span>% reduce <span class="hljs-number">100</span>%
<span class="hljs-number">18</span>/<span class="hljs-number">01</span>/<span class="hljs-number">28</span> <span class="hljs-number">17</span>:<span class="hljs-number">45</span>:<span class="hljs-number">03</span> INFO mapreduce<span class="hljs-preprocessor">.Job</span>: Job job_1517156134219_0001 completed successfully</code></pre>

<p>yarn的运行可以在8088端口看到，正在运行时state状态为RUNNING <br>
<img src="https://img-blog.csdn.net/20180129144641295?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvV3BpeGVs/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
查看yarn计算的结果</p>



<pre class="prettyprint"><code class=" hljs perl">[root<span class="hljs-variable">@wpixel01</span> www]<span class="hljs-comment"># hadoop fs -ls /count/ </span>
Found <span class="hljs-number">2</span> items
-rw-r--r--   <span class="hljs-number">3</span> root supergroup          <span class="hljs-number">0</span> <span class="hljs-number">2018</span>-<span class="hljs-number">01</span>-<span class="hljs-number">28</span> <span class="hljs-number">17</span>:<span class="hljs-number">45</span> /count/_SUCCESS 这是程序计算的状态，表示计算成功
-rw-r--r--   <span class="hljs-number">3</span> root supergroup         <span class="hljs-number">85</span> <span class="hljs-number">2018</span>-<span class="hljs-number">01</span>-<span class="hljs-number">28</span> <span class="hljs-number">17</span>:<span class="hljs-number">45</span> /count/part-r-<span class="hljs-number">00000</span> 真正的结果在这个文件里
[root<span class="hljs-variable">@wpixel01</span> www]<span class="hljs-comment"># hadoop fs -cat /count/part-r-00000 </span>
<span class="hljs-comment">#单词     在文本中出现的次数</span>
adidas          <span class="hljs-number">1</span>
baidu           <span class="hljs-number">1</span>
contos          <span class="hljs-number">2</span>
google          <span class="hljs-number">2</span>
hadoop          <span class="hljs-number">2</span>
<span class="hljs-keyword">my</span>              <span class="hljs-number">1</span>
nike            <span class="hljs-number">1</span>
windows         <span class="hljs-number">2</span>
zare            <span class="hljs-number">1</span>
zojirushi       <span class="hljs-number">2</span></code></pre>



<h4 id="总结">总结</h4>

<p>Hadoop2中Hadoop Federation、HA、Yarn完全分布式配置已经搭建成功了。 <br>
以后会发布整个架构图和解析 <br>
电脑跑四台虚拟机也不容易啊，8G内存还是有点吃力的，那么你成功了么。……(￣∇￣)</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>