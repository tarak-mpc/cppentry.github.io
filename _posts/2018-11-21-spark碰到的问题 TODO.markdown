---
layout:     post
title:      spark碰到的问题 TODO
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载					https://blog.csdn.net/T1DMzks/article/details/73929155				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h4 id="javaiofilenotfoundexception-file-does-not-exist">java.io.FileNotFoundException: File does not exist</h4>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-label">java.io.FileNotFoundException:</span> File does not exist: hdfs://master:<span class="hljs-number">9000</span>/user/hmaster/<span class="hljs-preprocessor">.sparkStaging</span>/application_1498791665418_0041/spark-assembly-<span class="hljs-number">1.4</span><span class="hljs-number">.0</span>-hadoop2<span class="hljs-number">.6</span><span class="hljs-number">.0</span><span class="hljs-preprocessor">.jar</span>
        at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.DistributedFileSystem</span>$22<span class="hljs-preprocessor">.doCall</span>(DistributedFileSystem<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">1309</span>)
        at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.DistributedFileSystem</span>$22<span class="hljs-preprocessor">.doCall</span>(DistributedFileSystem<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">1301</span>)
        at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.fs</span><span class="hljs-preprocessor">.FileSystemLinkResolver</span><span class="hljs-preprocessor">.resolve</span>(FileSystemLinkResolver<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">81</span>)
        at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.DistributedFileSystem</span><span class="hljs-preprocessor">.getFileStatus</span>(DistributedFileSystem<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">1301</span>)
        at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.yarn</span><span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.FSDownload</span><span class="hljs-preprocessor">.copy</span>(FSDownload<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">253</span>)
        at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.yarn</span><span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.FSDownload</span><span class="hljs-preprocessor">.access</span>$000(FSDownload<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">63</span>)
        at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.yarn</span><span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.FSDownload</span>$2<span class="hljs-preprocessor">.run</span>(FSDownload<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">361</span>)
        at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.yarn</span><span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.FSDownload</span>$2<span class="hljs-preprocessor">.run</span>(FSDownload<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">359</span>)
        at java<span class="hljs-preprocessor">.security</span><span class="hljs-preprocessor">.AccessController</span><span class="hljs-preprocessor">.doPrivileged</span>(Native Method)
        at javax<span class="hljs-preprocessor">.security</span><span class="hljs-preprocessor">.auth</span><span class="hljs-preprocessor">.Subject</span><span class="hljs-preprocessor">.doAs</span>(Subject<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">415</span>)
        at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.security</span><span class="hljs-preprocessor">.UserGroupInformation</span><span class="hljs-preprocessor">.doAs</span>(UserGroupInformation<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">1657</span>)
        at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.yarn</span><span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.FSDownload</span><span class="hljs-preprocessor">.call</span>(FSDownload<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">358</span>)
        at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.yarn</span><span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.FSDownload</span><span class="hljs-preprocessor">.call</span>(FSDownload<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">62</span>)
        at java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.concurrent</span><span class="hljs-preprocessor">.FutureTask</span><span class="hljs-preprocessor">.run</span>(FutureTask<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">262</span>)
        at java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.concurrent</span><span class="hljs-preprocessor">.Executors</span>$RunnableAdapter<span class="hljs-preprocessor">.call</span>(Executors<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">471</span>)
        at java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.concurrent</span><span class="hljs-preprocessor">.FutureTask</span><span class="hljs-preprocessor">.run</span>(FutureTask<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">262</span>)
        at java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.concurrent</span><span class="hljs-preprocessor">.ThreadPoolExecutor</span><span class="hljs-preprocessor">.runWorker</span>(ThreadPoolExecutor<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">1145</span>)
        at java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.concurrent</span><span class="hljs-preprocessor">.ThreadPoolExecutor</span>$Worker<span class="hljs-preprocessor">.run</span>(ThreadPoolExecutor<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">615</span>)
        at java<span class="hljs-preprocessor">.lang</span><span class="hljs-preprocessor">.Thread</span><span class="hljs-preprocessor">.run</span>(Thread<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">745</span>)

Failing this attempt. Failing the application.
         ApplicationMaster host: N/A
         ApplicationMaster RPC port: -<span class="hljs-number">1</span>
         queue: root<span class="hljs-preprocessor">.hmaster</span>
         start time: <span class="hljs-number">1499311838058</span>
         final status: FAILED
         tracking URL: http://master:<span class="hljs-number">8088</span>/cluster/app/application_1498791665418_0041
         user: hmaster
Exception <span class="hljs-keyword">in</span> thread <span class="hljs-string">"main"</span> org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.SparkException</span>: Application application_1498791665418_0041 finished with failed status
        at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.deploy</span><span class="hljs-preprocessor">.yarn</span><span class="hljs-preprocessor">.Client</span><span class="hljs-preprocessor">.run</span>(Client<span class="hljs-preprocessor">.scala</span>:<span class="hljs-number">841</span>)
        at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.deploy</span><span class="hljs-preprocessor">.yarn</span><span class="hljs-preprocessor">.Client</span>$<span class="hljs-preprocessor">.main</span>(Client<span class="hljs-preprocessor">.scala</span>:<span class="hljs-number">867</span>)
        at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.deploy</span><span class="hljs-preprocessor">.yarn</span><span class="hljs-preprocessor">.Client</span><span class="hljs-preprocessor">.main</span>(Client<span class="hljs-preprocessor">.scala</span>)
        at sun<span class="hljs-preprocessor">.reflect</span><span class="hljs-preprocessor">.NativeMethodAccessorImpl</span><span class="hljs-preprocessor">.invoke</span>0(Native Method)
        at sun<span class="hljs-preprocessor">.reflect</span><span class="hljs-preprocessor">.NativeMethodAccessorImpl</span><span class="hljs-preprocessor">.invoke</span>(NativeMethodAccessorImpl<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">57</span>)
        at sun<span class="hljs-preprocessor">.reflect</span><span class="hljs-preprocessor">.DelegatingMethodAccessorImpl</span><span class="hljs-preprocessor">.invoke</span>(DelegatingMethodAccessorImpl<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">43</span>)
        at java<span class="hljs-preprocessor">.lang</span><span class="hljs-preprocessor">.reflect</span><span class="hljs-preprocessor">.Method</span><span class="hljs-preprocessor">.invoke</span>(Method<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">606</span>)
        at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.deploy</span><span class="hljs-preprocessor">.SparkSubmit</span>$<span class="hljs-preprocessor">.org</span>$apache$spark$deploy$SparkSubmit$$runMain(SparkSubmit<span class="hljs-preprocessor">.scala</span>:<span class="hljs-number">664</span>)
        at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.deploy</span><span class="hljs-preprocessor">.SparkSubmit</span>$<span class="hljs-preprocessor">.doRunMain</span>$1(SparkSubmit<span class="hljs-preprocessor">.scala</span>:<span class="hljs-number">169</span>)
        at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.deploy</span><span class="hljs-preprocessor">.SparkSubmit</span>$<span class="hljs-preprocessor">.submit</span>(SparkSubmit<span class="hljs-preprocessor">.scala</span>:<span class="hljs-number">192</span>)
        at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.deploy</span><span class="hljs-preprocessor">.SparkSubmit</span>$<span class="hljs-preprocessor">.main</span>(SparkSubmit<span class="hljs-preprocessor">.scala</span>:<span class="hljs-number">111</span>)
        at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.deploy</span><span class="hljs-preprocessor">.SparkSubmit</span><span class="hljs-preprocessor">.main</span>(SparkSubmit<span class="hljs-preprocessor">.scala</span>)</code></pre>

<p>原因： 目前主要碰到这两种 <br>
1. 代码中本地测试setMaster(“local”)，集群中运行应该去掉这个  <br>
2. 编译打包用的jdk版本和集群上配置不一样</p>

<h4 id="spark-yarn-模式下-一直-运行不报错">spark yarn 模式下 一直 运行不报错</h4>

<h4 id="spark-yarn模式下打开节点很少">spark yarn模式下，打开节点很少</h4>

<h4 id="spark-standalone-模式下不报错停在某个statge额某个任务">spark standalone 模式下，不报错，停在某个statge额某个任务</h4>

<p>个人目前觉得standalone模式在一些复杂的计算中不太使用，之前测试一份数据，计算比较复杂，standalone为了追求速度，所有节点火力全开，导致cpu阻塞</p>

<h4 id="不断增加">不断增加</h4>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>