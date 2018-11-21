---
layout:     post
title:      Hadoop伪分布式安装二(MapReduce+Yarn)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载					https://blog.csdn.net/yu0_zhang0/article/details/78841365				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p><strong>上一篇我们已经介绍了HDFS的部署，接着我们进行Hadoop伪分布式MapReduce+Yarn的部署</strong></p>



<h4 id="1yarn部署">1.Yarn部署</h4>

<ul>
<li>修改mapred-site.xml 文件</li>
</ul>



<pre class="prettyprint"><code class=" hljs xml">[hadoop@zydatahadoop001 hadoop]$ cd        /opt/software/hadoop/etc/hadoop/
[hadoop@zydatahadoop001 hadoop]$ cp mapred-site.xml.template mapred-site.xml
[hadoop@zydatahadoop001 hadoop]$ vi mapred-site.xml

添加下面的内容在<span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>中
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>mapreduce.framework.name<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>yarn<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
</code></pre>

<ul>
<li>修改yarn-site.xml文件</li>
</ul>



<pre class="prettyprint"><code class=" hljs xml">[hadoop@zydatahadoop001 hadoop]$ vi yarn-site.xml

添加下面的内容在<span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>中
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.nodemanager.aux-services<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>mapreduce_shuffle<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span></code></pre>

<ul>
<li>开启服务</li>
</ul>



<pre class="prettyprint"><code class=" hljs vhdl">[hadoop@zydatahadoop001 hadoop]$ cd /opt/software/hadoop
[hadoop@zydatahadoop001 hadoop]$ sbin/start-yarn.sh
starting yarn daemons
starting resourcemanager, logging <span class="hljs-keyword">to</span> /opt/software/hadoop-<span class="hljs-number">2.8</span><span class="hljs-number">.1</span>/logs/yarn-hadoop-resourcemanager-zydatahadoop001.<span class="hljs-keyword">out</span>
zydatahadoop001: starting nodemanager, logging <span class="hljs-keyword">to</span> /opt/software/hadoop-<span class="hljs-number">2.8</span><span class="hljs-number">.1</span>/logs/yarn-hadoop-nodemanager-zydatahadoop001.<span class="hljs-keyword">out</span>
[hadoop@zydatahadoop001 hadoop]$ jps
<span class="hljs-number">24439</span> ResourceManager
<span class="hljs-number">24840</span> Jps
<span class="hljs-number">24073</span> SecondaryNameNode
<span class="hljs-number">24539</span> NodeManager
<span class="hljs-number">23788</span> NameNode

查看<span class="hljs-number">8088</span>端口
[hadoop@zydatahadoop001 hadoop]$ netstat -nlp|grep <span class="hljs-number">8088</span>
(<span class="hljs-keyword">Not</span> <span class="hljs-keyword">all</span> processes could be identified, non-owned <span class="hljs-keyword">process</span> info
 will <span class="hljs-keyword">not</span> be shown, you would have <span class="hljs-keyword">to</span> be root <span class="hljs-keyword">to</span> see it <span class="hljs-keyword">all</span>.)
tcp        <span class="hljs-number">0</span>      <span class="hljs-number">0</span> :::<span class="hljs-number">8088</span>                     :::*                        LISTEN      <span class="hljs-number">24439</span>/java   </code></pre>

<p><strong>web界面进行查看: http://自己的ip:8088/</strong></p>



<h4 id="2mr-job测试">2.MR Job测试</h4>

<p>MapReduce: java代码写的，map(映射)函数和reduce(归约)函数</p>

<p><strong><em>这里可能出现错误：</em></strong></p>



<pre class="prettyprint"><code class=" hljs avrasm">WARN hdfs<span class="hljs-preprocessor">.DataStreamer</span>: DataStreamer Exception
org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.ipc</span><span class="hljs-preprocessor">.RemoteException</span>(java<span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.IOException</span>):</code></pre>

<p>解决办法<a href="http://blog.csdn.net/yu0_zhang0/article/details/78841623" rel="nofollow">参考这篇博客</a></p>

<ul>
<li>计算圆周率</li>
</ul>



<pre class="prettyprint"><code class=" hljs coffeescript">[hadoop<span class="hljs-property">@zydatahadoop001</span> hadoop]$ bin/hadoop jar ./share/hadoop/mapreduce/hadoop-mapreduce-examples-<span class="hljs-number">2.8</span><span class="hljs-number">.1</span>.jar pi <span class="hljs-number">5</span> <span class="hljs-number">10</span>

计算结果（选取的参数问题出现误差）：
Job Finished <span class="hljs-keyword">in</span> <span class="hljs-number">749.765</span> seconds
Estimated value <span class="hljs-keyword">of</span> Pi <span class="hljs-keyword">is</span> <span class="hljs-number">3.28000000000000000000</span></code></pre>



<h4 id="3停止yarn">3.停止Yarn</h4>



<pre class="prettyprint"><code class=" hljs vbnet">[hadoop@zydatahadoop001 hadoop]$ sbin/<span class="hljs-keyword">stop</span>-yarn.sh 
stopping yarn daemons
stopping resourcemanager
zydatahadoop001: stopping nodemanager
zydatahadoop001: nodemanager did <span class="hljs-keyword">not</span> <span class="hljs-keyword">stop</span> gracefully after <span class="hljs-number">5</span> seconds: killing <span class="hljs-keyword">with</span> kill -<span class="hljs-number">9</span>
no proxyserver <span class="hljs-keyword">to</span> <span class="hljs-keyword">stop</span></code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>