---
layout:     post
title:      flume数据传输到kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/hyneria_hope/article/details/77839956				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="flume-简单介绍">flume 简单介绍</h1>

<blockquote>
  <p>当你看到这篇文章时，应该对flume有一个大概了解但是为照顾刚入门的同学所以还是会说下flume，刚开始使用flume时不需要理解太多里面的东西，只需要理解下面的图就可以使用flume把日志数据传入kafka中，下图中的hdfs只是有代表性的sink而以，我在实际使用中sink是kafka <br>
  <img src="http://upload-images.jianshu.io/upload_images/4441862-a336ee658504eb34.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240" alt="这里写图片描述" title=""></p>
</blockquote>



<h1 id="flume安装">flume安装</h1>



<h2 id="flume环境准备">flume环境准备</h2>

<ul>
<li>centos 6.5</li>
<li>JDK 1.7+ </li>
</ul>



<h2 id="flume下载安装">flume下载安装</h2>

<ul>
<li>flume 1.7 <a href="http://www.apache.org/dyn/closer.lua/flume/1.7.0/apache-flume-1.7.0-bin.tar.gz" rel="nofollow">下载链接</a></li>
<li>安装flume <br>


<blockquote>
  1.tar -zxvf apache-flume-1.7.0-bin.tar.gz <br>
  2.mv apache-flume-1.7.0-bin flume <br>
  3.cp conf/flume-conf.properties.template conf/flume-conf.properties # flume-conf.properties 配置source,channel,sink等信息 <br>
  4.cp conf/flume-env.sh.template conf/flume-env.sh # flume-env.sh配置agent启动项及JAVA环境变量等</blockquote></li>
  </ul>
  

<h2 id="flume配置">flume配置</h2>

<ul>
<li>配置flume-conf.properties</li>
</ul>



<pre class="prettyprint"><code class=" hljs avrasm">agent<span class="hljs-preprocessor">.sources</span>=<span class="hljs-built_in">r1</span>
agent<span class="hljs-preprocessor">.sinks</span>=k1
agent<span class="hljs-preprocessor">.channels</span>=c1

agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.type</span>=exec
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.command</span>=tail -F /data/logs/access<span class="hljs-preprocessor">.log</span>
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.restart</span>=true
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.batchSize</span>=<span class="hljs-number">1000</span>
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.batchTimeout</span>=<span class="hljs-number">3000</span>
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.channels</span>=c1

agent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.type</span>=memory
agent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.capacity</span>=<span class="hljs-number">102400</span>
agent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.transactionCapacity</span>=<span class="hljs-number">1000</span>

agent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.byteCapacity</span>=<span class="hljs-number">134217728</span>
agent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.byteCapacityBufferPercentage</span>=<span class="hljs-number">80</span>

agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.channel</span>=c1
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.type</span>=org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.KafkaSink</span>
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.topic</span>=xxxxx-kafka
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.bootstrap</span><span class="hljs-preprocessor">.servers</span>=<span class="hljs-built_in">x</span><span class="hljs-preprocessor">.x</span><span class="hljs-preprocessor">.x</span><span class="hljs-preprocessor">.x</span>:<span class="hljs-number">9092</span>,<span class="hljs-built_in">x</span><span class="hljs-preprocessor">.x</span><span class="hljs-preprocessor">.x</span><span class="hljs-preprocessor">.x</span>:<span class="hljs-number">9092</span>
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.serializer</span><span class="hljs-preprocessor">.class</span>=kafka<span class="hljs-preprocessor">.serializer</span><span class="hljs-preprocessor">.StringEncoder</span>
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.flumeBatchSize</span>=<span class="hljs-number">1000</span>
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.useFlumeEventFormat</span>=true</code></pre>

<p>命令规则是 r1-&gt;source k1-&gt;sink c1-&gt;channels agent名称在你启动时-n的参数值</p>

<ul>
<li>配置flume-env.sh</li>
</ul>



<pre class="prettyprint"><code class=" hljs ruby">export <span class="hljs-constant">JAVA_HOME</span>=<span class="hljs-regexp">/data/java</span><span class="hljs-regexp">/jdk1.8.0_102/</span></code></pre>

<p>我这边只配置JAVA_HOME，还有agent启动的一些JMX选项没有加，这些可以根据自己的需要添加 <br>
* 启动flume-agent</p>

<blockquote>
  <p>启动flume-agent <br>
  ./bin/flume-ng agent -c conf -f conf/flume-conf.properties -n agent -Dflume.root.logger=INFO,console <br>
  -c 配置文件目录 -f 指定flume配置文件 -n flume客户端名称 Dflume启动时将INFO级别的LOG打印在控制台</p>
</blockquote>


<h1 id="总结">总结</h1>

<blockquote>
  <p>1.flume中可以自己定义source,sink，你可以根据自己的需要去做修改或者重新 <a href="https://github.com/apache/flume" rel="nofollow">git地址</a>，从github中pull代码如你只是修改某模块代码就只需要把之前的jar删除，把编译好的jar丢上去就可以，其他的玩法可以看官方文档 <br>
  2.在使用中memory channels当agent被杀时数据会丢失不会恢复 <br>
  3.flume在日聚合中是非常灵活的，可以组成各种玩法如我从某tcp端口拿数据传入别的flume agent中等 <br>
  4.建议看一篇官方文档</p>
</blockquote>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>