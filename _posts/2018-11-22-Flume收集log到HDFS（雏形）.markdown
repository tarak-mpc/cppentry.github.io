---
layout:     post
title:      Flume收集log到HDFS（雏形）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/lemonZhaoTao/article/details/81150902				</div>
								            <div id="content_views" class="markdown_views prism-github-gist">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>Flume系列文章： <br>
<a href="https://blog.csdn.net/lemonzhaotao/article/details/80560092" rel="nofollow">Flume 概述 &amp; 架构 &amp; 组件介绍</a> <br>
<a href="https://blog.csdn.net/lemonzhaotao/article/details/80640959" rel="nofollow">Flume 使用入门 &amp; 入门Demo</a></p>

<p>在本篇文章中，将继续介绍Flume的使用</p>



<h2 id="flume的使用场景">Flume的使用场景</h2>

<p>监控某个文件，并把新增的内容输出到HDFS(这是离线处理的典型的一个场景) <br>
Agent的技术选型，该如何选择？在这里我们的选择如下：</p>

<ul>
<li>exec source</li>
<li>memory channel</li>
<li>hdfs sink</li>
</ul>



<h2 id="flume收集log到hdfs雏形">Flume收集log到HDFS（雏形）</h2>



<h3 id="agent配置内容解读">Agent配置内容解读</h3>

<pre class="prettyprint"><code class=" hljs avrasm">a1<span class="hljs-preprocessor">.sources</span> = <span class="hljs-built_in">r1</span>
a1<span class="hljs-preprocessor">.sinks</span> = k1.....
a1<span class="hljs-preprocessor">.channels</span> = c1,c2,c3

<span class="hljs-preprocessor"># 官网：http://archive.cloudera.com/cdh5/cdh/5/flume-ng-1.6.0-cdh5.7.0/FlumeUserGuide.html#exec-source</span>
<span class="hljs-preprocessor"># 必填的属性：channels</span>
<span class="hljs-preprocessor">#            type      必须是exec</span>
<span class="hljs-preprocessor">#            command   命令，使用 tail -F 监控/home/hadoop/data/data.log  目前data.log是空的</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.type</span> = exec
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.command</span> = tail -F /home/hadoop/data/data<span class="hljs-preprocessor">.log</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.channels</span> = c1

<span class="hljs-preprocessor"># 使用memory channel</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.type</span> = memory

<span class="hljs-preprocessor"># 官网：http://archive.cloudera.com/cdh5/cdh/5/flume-ng-1.6.0-cdh5.7.0/FlumeUserGuide.html#hdfs-sink</span>
<span class="hljs-preprocessor"># 如果我们自己进行设置的话，使用HDFS Sink，我们需要进行配置的内容有：路径、batch、压缩、存储格式</span>
<span class="hljs-preprocessor"># 必填的属性：channel</span>
<span class="hljs-preprocessor">#            type        必须是hdfs</span>
<span class="hljs-preprocessor">#            hdfs.path</span>
<span class="hljs-preprocessor"># </span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.type</span> = hdfs
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.path</span> = hdfs://hadoop000:<span class="hljs-number">8020</span>/data/flume/tail
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.batchSize</span> = <span class="hljs-number">10</span>             每个批次放<span class="hljs-number">10</span>条数据
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.fileType</span> = DataStream      文件类型；有SequenceFile(默认设置是这种，SequenceFile的内容我们是看不见的，是没有办法直接读取的),
                                                       DataStream(不能使用压缩，因此不能设置codec)
                                                       CompressedStream(需要去设置codec)
                                                      <span class="hljs-number">3</span>种
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.writeFormat</span> = Text         写数据的格式，有Text,Writable <span class="hljs-number">2</span>种类型</code></pre>



<h3 id="conf文件配置">conf文件配置</h3>

<p><code>$FLUME_HOME/conf/exec-memory-hdfs.conf</code>文件编写</p>

<pre class="prettyprint"><code class=" hljs avrasm">a1<span class="hljs-preprocessor">.sources</span> = <span class="hljs-built_in">r1</span>
a1<span class="hljs-preprocessor">.sinks</span> = k1
a1<span class="hljs-preprocessor">.channels</span> = c1

a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.type</span> = exec
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.command</span> = tail -F /home/hadoop/data/data<span class="hljs-preprocessor">.log</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.channels</span> = c1

a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.type</span> = memory

a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.type</span> = hdfs
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.path</span> = hdfs://<span class="hljs-number">192.168</span><span class="hljs-number">.26</span><span class="hljs-number">.131</span>:<span class="hljs-number">8020</span>/data/flume/tail
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.batchSize</span> = <span class="hljs-number">10</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.fileType</span> = DataStream
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.writeFormat</span> = Text
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.channel</span> = c1</code></pre>

<h3 id="运行flume">运行Flume</h3>

<p><strong>启动Flume Agent：</strong></p>

<pre class="prettyprint"><code class=" hljs lasso">$<span class="hljs-subst">&gt;</span>flume<span class="hljs-attribute">-ng</span> agent <span class="hljs-subst">\</span>
  <span class="hljs-subst">--</span>name a1  <span class="hljs-subst">\</span>
  <span class="hljs-subst">--</span>conf <span class="hljs-variable">$FLUME_HOME</span>/conf <span class="hljs-subst">\</span>
  <span class="hljs-subst">--</span>conf<span class="hljs-attribute">-file</span> <span class="hljs-variable">$FLUME_HOME</span>/conf/exec<span class="hljs-attribute">-memory</span><span class="hljs-attribute">-hdfs</span><span class="hljs-built_in">.</span>conf <span class="hljs-subst">\</span>
  <span class="hljs-attribute">-Dflume</span><span class="hljs-built_in">.</span>root<span class="hljs-built_in">.</span>logger<span class="hljs-subst">=</span>INFO,console

$<span class="hljs-subst">&gt;</span>echo aaa <span class="hljs-subst">&gt;&gt;</span> <span class="hljs-built_in">data</span><span class="hljs-built_in">.</span><span class="hljs-keyword">log</span>
$<span class="hljs-subst">&gt;</span>echo aaa <span class="hljs-subst">&gt;&gt;</span> <span class="hljs-built_in">data</span><span class="hljs-built_in">.</span><span class="hljs-keyword">log</span>
$<span class="hljs-subst">&gt;</span>echo aaa <span class="hljs-subst">&gt;&gt;</span> <span class="hljs-built_in">data</span><span class="hljs-built_in">.</span><span class="hljs-keyword">log</span>
$<span class="hljs-subst">&gt;</span>echo aaa <span class="hljs-subst">&gt;&gt;</span> <span class="hljs-built_in">data</span><span class="hljs-built_in">.</span><span class="hljs-keyword">log</span>
$<span class="hljs-subst">&gt;</span>echo aaa <span class="hljs-subst">&gt;&gt;</span> <span class="hljs-built_in">data</span><span class="hljs-built_in">.</span><span class="hljs-keyword">log</span>
$<span class="hljs-subst">&gt;</span>echo aaa <span class="hljs-subst">&gt;&gt;</span> <span class="hljs-built_in">data</span><span class="hljs-built_in">.</span><span class="hljs-keyword">log</span>
$<span class="hljs-subst">&gt;</span>echo aaa <span class="hljs-subst">&gt;&gt;</span> <span class="hljs-built_in">data</span><span class="hljs-built_in">.</span><span class="hljs-keyword">log</span>
<span class="hljs-attribute">...</span>
不断向/home/hadoop/<span class="hljs-built_in">data</span>/<span class="hljs-built_in">data</span><span class="hljs-built_in">.</span><span class="hljs-keyword">log</span>追加内容</code></pre>

<p><strong>启动Agent的那端，所显示的log信息：</strong> <br>
<code>Serializer = TEXT, UseRawLocalFileSystem = false</code> <br>
<code>Creating hdfs://192.168.26.131:8020/data/flume/tail/FlumeData.1517894275500.tmp</code> <br>
创建文件，前缀是FlumeData，中间是一个时间戳，后缀是.tmp(因为还在写) <br>
<strong>注意：</strong></p>

<ul>
<li><p>之所以前缀是FlumeData，是因为hdfs.filePrefix属性的默认值就是FlumeData(参照HDFS Sink的配置文件，官网可以查阅到) <br>
在生产上，就相当于业务线的名称(结合线下若泽所说的项目来想)</p></li>
<li><p>之所后缀是.tmp，是因为hdfs.inUseSuffix属性的默认值是.tmp(参照HDFS Sink的配置文件，官网可以查阅到)</p></li>
</ul>

<p><code>Closing hdfs://192.168.26.131:8020/data/flume/tail/FlumeData.1517894275500.tmp  <br>
Renaming hdfs://192.168.26.131:8020/data/flume/tail/FlumeData.1517894275500.tmp to <br>
 hdfs://192.168.26.131:8020/data/flume/tail/FlumeData.1517894275500</code> <br>
写完之后，做了一个Rename的操作，将带tmp的文件，给Rename成不带tmp文件了，没有后缀了，标志着真正的写完了</p>

<p><strong>收集到HDFS之后，最终会在HDFS目录/data/flume/tail上所产生了2个文件：</strong></p>

<pre class="prettyprint"><code class=" hljs lasso">Permission  Owner   <span class="hljs-keyword">Group</span>       Size    Last Modified                   Replication     Block Size  Name
<span class="hljs-attribute">-rw</span><span class="hljs-attribute">-r</span><span class="hljs-subst">--</span>r<span class="hljs-subst">--</span>  hadoop  supergroup  <span class="hljs-number">40</span> B    Tue Feb <span class="hljs-number">06</span> <span class="hljs-number">13</span>:<span class="hljs-number">18</span>:<span class="hljs-number">01</span> <span class="hljs-subst">+</span><span class="hljs-number">0800</span> <span class="hljs-number">2018</span>  <span class="hljs-number">1</span>               <span class="hljs-number">128</span> MB      FlumeData<span class="hljs-number">.1517894275500</span>
<span class="hljs-attribute">-rw</span><span class="hljs-attribute">-r</span><span class="hljs-subst">--</span>r<span class="hljs-subst">--</span>  hadoop  supergroup  <span class="hljs-number">16</span> B    Tue Feb <span class="hljs-number">06</span> <span class="hljs-number">13</span>:<span class="hljs-number">18</span>:<span class="hljs-number">31</span> <span class="hljs-subst">+</span><span class="hljs-number">0800</span> <span class="hljs-number">2018</span>  <span class="hljs-number">1</span>               <span class="hljs-number">128</span> MB      FlumeData<span class="hljs-number">.1517894275501</span></code></pre>

<p><strong>潜在的问题：</strong> <br>
Block Size都是128MB，而Flume收集过来的文件分别只有40B与16B，这就产生了1个问题： <br>
Flume收集过来的文件过小，这点必然是不行的；同时，这点会造成集群的Block的数量扛不住，对此需要采取相应的措施去规避，这点将在下篇文章中介绍 <br>
这里，分享一篇关于Block计算的相关issue：<a href="https://issues.apache.org/jira/browse/HADOOP-1687" rel="nofollow">hadoop-1687</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>