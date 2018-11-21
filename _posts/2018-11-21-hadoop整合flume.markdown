---
layout:     post
title:      hadoop整合flume
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/chukun123/article/details/78390709				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p><strong>安装flume</strong> <br>
1、将flume-ng-1.5.0-cdh5.3.6.tar.gz拷贝到spark1的/usr/local目录下。 <br>
2、对flume进行解压缩：</p>



<pre class="prettyprint"><code class=" hljs lasso">tar <span class="hljs-attribute">-zxvf</span> flume<span class="hljs-attribute">-ng</span><span class="hljs-subst">-</span><span class="hljs-number">1.5</span><span class="hljs-number">.0</span><span class="hljs-attribute">-cdh5</span><span class="hljs-number">.3</span><span class="hljs-number">.6</span><span class="hljs-built_in">.</span>tar<span class="hljs-built_in">.</span>gz</code></pre>

<p>3、对flume目录进行重命名：</p>



<pre class="prettyprint"><code class=" hljs lasso">mv apache<span class="hljs-attribute">-flume</span><span class="hljs-subst">-</span><span class="hljs-number">1.5</span><span class="hljs-number">.0</span><span class="hljs-attribute">-cdh5</span><span class="hljs-number">.3</span><span class="hljs-number">.6</span><span class="hljs-attribute">-bin</span> flume</code></pre>

<p>4、配置flume相关的环境变量</p>

<pre class="prettyprint"><code class=" hljs bash">vi ~/.bashrc
<span class="hljs-keyword">export</span> FLUME_HOME=/usr/local/flume
<span class="hljs-keyword">export</span> FLUME_CONF_DIR=<span class="hljs-variable">$FLUME_HOME</span>/conf
<span class="hljs-keyword">export</span> PATH=<span class="hljs-variable">$FLUME_HOME</span>/bin
<span class="hljs-built_in">source</span> ~/.bashrc</code></pre>

<p><strong>修改flume的配置文件</strong></p>



<pre class="prettyprint"><code class=" hljs avrasm">vi /usr/local/flume/conf/flume-conf<span class="hljs-preprocessor">.properties</span>

<span class="hljs-preprocessor">#agent1表示代理名称</span>
agent1<span class="hljs-preprocessor">.sources</span>=source1
agent1<span class="hljs-preprocessor">.sinks</span>=sink1
agent1<span class="hljs-preprocessor">.channels</span>=channel1
<span class="hljs-preprocessor">#配置source1</span>
agent1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>1<span class="hljs-preprocessor">.type</span>=spooldir
agent1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>1<span class="hljs-preprocessor">.spoolDir</span>=/usr/local/logs
agent1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>1<span class="hljs-preprocessor">.channels</span>=channel1
agent1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>1<span class="hljs-preprocessor">.fileHeader</span> = false
agent1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>1<span class="hljs-preprocessor">.interceptors</span> = i1
agent1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>1<span class="hljs-preprocessor">.interceptors</span><span class="hljs-preprocessor">.i</span>1<span class="hljs-preprocessor">.type</span> = timestamp

<span class="hljs-preprocessor">#配置channel1</span>
agent1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.channel</span>1<span class="hljs-preprocessor">.type</span>=file
agent1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.channel</span>1<span class="hljs-preprocessor">.checkpointDir</span>=/usr/local/logs_tmp_cp
agent1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.channel</span>1<span class="hljs-preprocessor">.dataDirs</span>=/usr/local/logs_tmp

<span class="hljs-preprocessor">#配置sink1</span>
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.type</span>=hdfs
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.path</span>=hdfs://spark1:<span class="hljs-number">9000</span>/logs
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.fileType</span>=DataStream
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.writeFormat</span>=TEXT
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollInterval</span>=<span class="hljs-number">1</span>
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.channel</span>=channel1
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.filePrefix</span>=%<span class="hljs-built_in">Y</span>-%m-%d
</code></pre>

<p><strong>启动flume</strong></p>



<pre class="prettyprint"><code class=" hljs lasso">flume<span class="hljs-attribute">-ng</span> agent <span class="hljs-attribute">-n</span> agent1 <span class="hljs-attribute">-c</span> conf <span class="hljs-attribute">-f</span> /usr/<span class="hljs-built_in">local</span>/flume/conf/flume<span class="hljs-attribute">-conf</span><span class="hljs-built_in">.</span>properties <span class="hljs-attribute">-Dflume</span><span class="hljs-built_in">.</span>root<span class="hljs-built_in">.</span>logger<span class="hljs-subst">=</span>DEBUG,console</code></pre>

<p><strong>测试flume</strong></p>

<p>新建本地日志目录:</p>



<pre class="prettyprint"><code class=" hljs perl"><span class="hljs-keyword">mkdir</span> /usr/<span class="hljs-keyword">local</span>/logs</code></pre>

<p>新建hdfs目录:</p>



<pre class="prettyprint"><code class=" hljs perl">hdfs dfs -<span class="hljs-keyword">mkdir</span> /logs</code></pre>

<p>新建一份文件，移动到/usr/local/logs目录下，flume就会自动上传到HDFS的/logs目录中</p>

<p><img src="https://img-blog.csdn.net/20171030103211497?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2h1a3VuMTIz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>至此，hadoop整合flume完毕</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>