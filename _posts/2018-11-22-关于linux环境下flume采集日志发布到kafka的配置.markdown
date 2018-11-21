---
layout:     post
title:      关于linux环境下flume采集日志发布到kafka的配置
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_36811902/article/details/78899306				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="整体流程"><strong>整体流程</strong></h2>



<div class="flow-chart"><svg height="685.7187538146973" version="1.1" width="397.1750030517578" xmlns="http://www.w3.org/2000/svg" style="overflow: hidden; position: relative; top: -0.0833435px;"><desc>Created with Raphaël 2.1.0</desc><defs><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block"></path><marker id="raphael-marker-endblock33" markerheight="3" markerwidth="3" orient="auto" refx="1.5" refy="1.5"><use xlink:href="#raphael-marker-block" transform="rotate(180 1.5 1.5) scale(0.6,0.6)" stroke-width="1.6667" fill="black" stroke="none"></use></marker></defs><rect x="0" y="0" width="50" height="40" r="20" rx="20" ry="20" fill="#ffffff" stroke="#000000" style="" stroke-width="2" class="flowchart" id="st" transform="matrix(1,0,0,1,162.0875,75.5438)"></rect><text style="text-anchor: start; font: normal normal normal normal 14px sans-serif;" x="10" y="20" text-anchor="start" font='10px "Arial"' stroke="none" fill="#000000" id="stt" class="flowchartt" font-size="14px" font-family="sans-serif" font-weight="normal" transform="matrix(1,0,0,1,162.0875,75.5438)"><tspan dy="6">开始 </tspan></text><rect x="0" y="0" width="140.25" height="40" r="0" rx="0" ry="0" fill="#ffffff" stroke="#000000" style="" stroke-width="2" class="flowchart" id="op" transform="matrix(1,0,0,1,116.9625,241.0875)"></rect><text style="text-anchor: start; font: normal normal normal normal 14px sans-serif;" x="10" y="20" text-anchor="start" font='10px "Arial"' stroke="none" fill="#000000" id="opt" class="flowchartt" font-size="14px" font-family="sans-serif" font-weight="normal" transform="matrix(1,0,0,1,116.9625,241.0875)"><tspan dy="6">配置flume+kafka</tspan></text><path style="font-family: sans-serif; font-weight: normal;" fill="#ffffff" stroke="#000000" d="M91.54375076293945,45.77187538146973L0,91.54375076293945L183.0875015258789,183.0875015258789L366.1750030517578,91.54375076293945L183.0875015258789,0L0,91.54375076293945" stroke-width="2" font-family="sans-serif" font-weight="normal" id="cond" class="flowchart" transform="matrix(1,0,0,1,4,335.0875)"></path><text style="text-anchor: start; font: normal normal normal normal 14px sans-serif;" x="96.54375076293945" y="91.54375076293945" text-anchor="start" font='10px "Arial"' stroke="none" fill="#000000" id="condt" class="flowchartt" font-size="14px" font-family="sans-serif" font-weight="normal" transform="matrix(1,0,0,1,4,335.0875)"><tspan dy="6.000003814697266">kafka是否能接收到flume日志？</tspan><tspan dy="18" x="96.54375076293945"></tspan></text><rect x="0" y="0" width="50" height="40" r="20" rx="20" ry="20" fill="#ffffff" stroke="#000000" style="" stroke-width="2" class="flowchart" id="e" transform="matrix(1,0,0,1,162.0875,643.7188)"></rect><text style="text-anchor: start; font: normal normal normal normal 14px sans-serif;" x="10" y="20" text-anchor="start" font='10px "Arial"' stroke="none" fill="#000000" id="et" class="flowchartt" font-size="14px" font-family="sans-serif" font-weight="normal" transform="matrix(1,0,0,1,162.0875,643.7188)"><tspan dy="6">结束</tspan></text><path style="font-family: sans-serif; font-weight: normal;" fill="none" stroke="#000000" d="M187.0875015258789,115.54375076293945C187.0875015258789,115.54375076293945,187.0875015258789,219.04070285534476,187.0875015258789,238.09263202469867" stroke-width="2" marker-end="url(#raphael-marker-endblock33)" font-family="sans-serif" font-weight="normal"></path><path style="font-family: sans-serif; font-weight: normal;" fill="none" stroke="#000000" d="M187.0875015258789,281.0875015258789C187.0875015258789,281.0875015258789,187.0875015258789,320.74160146713257,187.0875015258789,332.08794061047956" stroke-width="2" marker-end="url(#raphael-marker-endblock33)" font-family="sans-serif" font-weight="normal"></path><path style="font-family: sans-serif; font-weight: normal;" fill="none" stroke="#000000" d="M187.0875015258789,518.1750030517578C187.0875015258789,518.1750030517578,187.0875015258789,621.6719551441631,187.0875015258789,640.723884313517" stroke-width="2" marker-end="url(#raphael-marker-endblock33)" font-family="sans-serif" font-weight="normal"></path><text style="text-anchor: start; font: normal normal normal normal 14px sans-serif;" x="192.0875015258789" y="528.1750030517578" text-anchor="start" font='10px "Arial"' stroke="none" fill="#000000" font-size="14px" font-family="sans-serif" font-weight="normal"><tspan dy="6.0000152587890625">yes</tspan></text><path style="font-family: sans-serif; font-weight: normal;" fill="none" stroke="#000000" d="M370.1750030517578,426.63125228881836C370.1750030517578,426.63125228881836,395.1750030517578,426.63125228881836,395.1750030517578,426.63125228881836C395.1750030517578,426.63125228881836,395.1750030517578,216.0875015258789,395.1750030517578,216.0875015258789C395.1750030517578,216.0875015258789,187.0875015258789,216.0875015258789,187.0875015258789,216.0875015258789C187.0875015258789,216.0875015258789,187.0875015258789,231.46094608306885,187.0875015258789,238.09674930013716" stroke-width="2" marker-end="url(#raphael-marker-endblock33)" font-family="sans-serif" font-weight="normal"></path><text style="text-anchor: start; font: normal normal normal normal 14px sans-serif;" x="375.1750030517578" y="416.63125228881836" text-anchor="start" font='10px "Arial"' stroke="none" fill="#000000" font-size="14px" font-family="sans-serif" font-weight="normal"><tspan dy="5.999996185302734">no</tspan></text></svg></div>

<hr>



<h2 id="flume配置">flume配置</h2>

<p>flume下载地址<a href="https://pan.baidu.com/s/1slNuhad" rel="nofollow">https://pan.baidu.com/s/1slNuhad</a> 提取码:q81v <br>
<strong>1.配置flume路径(配置环境变量)</strong></p>



<pre class="prettyprint"><code class=" hljs bash">$ <span class="hljs-built_in">cd</span> /xx/flume （这里进入flume所在文件夹）
$ vi .bash_profile（这里若没有该文件，会自动创建）

 <span class="hljs-keyword">export</span> FLUME_HOME=/xx(flume所在文件夹路径)/apache-flume-<span class="hljs-number">1.6</span>.<span class="hljs-number">0</span>-cdh5.<span class="hljs-number">8.4</span>-bin
 <span class="hljs-keyword">export</span> PATH=<span class="hljs-variable">$PATH</span>:<span class="hljs-variable">$FLUME_HOME</span>/bin

$ <span class="hljs-built_in">source</span> .bash_profile
</code></pre>

<p><strong>2.配置flume-env.sh文件</strong></p>



<pre class="prettyprint"><code class=" hljs lasso">配置java路径
$ cd /xx/apache<span class="hljs-attribute">-flume</span><span class="hljs-subst">-</span><span class="hljs-number">1.6</span><span class="hljs-number">.0</span><span class="hljs-attribute">-cdh5</span><span class="hljs-number">.8</span><span class="hljs-number">.4</span><span class="hljs-attribute">-bin</span>/conf
$ vi flume<span class="hljs-attribute">-env</span><span class="hljs-built_in">.</span>sh

export JAVA_HOME<span class="hljs-subst">=</span>/xx/jdk1<span class="hljs-number">.8</span><span class="hljs-number">.0</span>_121<span class="hljs-built_in">.</span>jdk<span class="hljs-subst">/</span>
export HADOOP_HOME<span class="hljs-subst">=</span>/xx/apache<span class="hljs-attribute">-flume</span><span class="hljs-subst">-</span><span class="hljs-number">1.6</span><span class="hljs-number">.0</span><span class="hljs-attribute">-cdh5</span><span class="hljs-number">.8</span><span class="hljs-number">.4</span><span class="hljs-attribute">-bin</span></code></pre>

<blockquote>
  <p>关于jdk配置两种方式： <br>
  1.引用环境中安装好的jdk路径 <br>
   2.直接上传一个新的jdk,解压。直接配置该jdk路径 <br>
   （关于jdk配置，网上也能找到。我也会在另外一篇blog中写出）</p>
</blockquote>

<p><strong>3.配置 xx.conf 文件</strong></p>



<pre class="prettyprint"><code class=" hljs avrasm">tier1<span class="hljs-preprocessor">.sources</span> = source_ETE_SERV_SSPS
tier1<span class="hljs-preprocessor">.channels</span> = channel_ETE_SERV_SSPS_kafka
tier1<span class="hljs-preprocessor">.sinks</span> = sink_ETE_SERV_SSPS_kafka
<span class="hljs-preprocessor">#ETE_SERV_SSPS</span>
tier1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>_ETE_SERV_SSPS<span class="hljs-preprocessor">.type</span> = TAILDIR
tier1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>_ETE_SERV_SSPS<span class="hljs-preprocessor">.positionFile</span> = position/taildir_position_ETE_SERV_SSPS<span class="hljs-preprocessor">.json</span>
tier1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>_ETE_SERV_SSPS<span class="hljs-preprocessor">.filegroups</span> = f1
<span class="hljs-preprocessor"># 监控日志文件路径</span>


----------


tier1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>_ETE_SERV_SSPS<span class="hljs-preprocessor">.filegroups</span><span class="hljs-preprocessor">.f</span>1 = /oss/ztracer/.*info*.*log（目标日志文件路径）


----------


tier1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>_ETE_SERV_SSPS<span class="hljs-preprocessor">.idleTimeout</span> = <span class="hljs-number">8000</span>
<span class="hljs-preprocessor">#batchSize一般要大于等于transactionCapacity</span>
tier1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>_ETE_SERV_SSPS<span class="hljs-preprocessor">.batchSize</span> = <span class="hljs-number">2000</span>
tier1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>_ETE_SERV_SSPS<span class="hljs-preprocessor">.channels</span> = channel_ETE_SERV_SSPS_kafka
tier1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.channel</span>_ETE_SERV_SSPS_kafka<span class="hljs-preprocessor">.type</span> = memory
tier1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.channel</span>_ETE_SERV_SSPS_kafka<span class="hljs-preprocessor">.capacity</span> = <span class="hljs-number">100000</span>
tier1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.channel</span>_ETE_SERV_SSPS_kafka<span class="hljs-preprocessor">.transactionCapacity</span> = <span class="hljs-number">2000</span>
tier1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>_ETE_SERV_SSPS_kafka<span class="hljs-preprocessor">.type</span> = org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.KafkaSink</span>
tier1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>_ETE_SERV_SSPS_kafka<span class="hljs-preprocessor">.channel</span> = channel_ETE_SERV_SSPS_kafka


----------


<span class="hljs-preprocessor"># kafka主题（监控日志将被放在kafka该主题下）</span>
tier1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>_ETE_SERV_SSPS_kafka<span class="hljs-preprocessor">.topic</span> = ETE_SERV_SSPS
<span class="hljs-preprocessor"># kafka 连接信息，可配置多个</span>
tier1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>_ETE_SERV_SSPS_kafka<span class="hljs-preprocessor">.brokerList</span> = 
<span class="hljs-label">kafka01:</span><span class="hljs-number">9092</span>,kafka01:<span class="hljs-number">9093</span>,kafka01:<span class="hljs-number">9094</span>,kafka02:<span class="hljs-number">9092</span>,kafka02:<span class="hljs-number">9093</span>,kafka02:<span class="hljs-number">9094</span>


----------


tier1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>_ETE_SERV_SSPS_kafka<span class="hljs-preprocessor">.batchSize</span> = <span class="hljs-number">1000</span></code></pre>

<p><strong>4.启动flume</strong> <br>
./flume-ng agent -n tier1 -c /IBM/flume/apache-flume-1.6.0-cdh5.8.4-bin/conf -f /IBM/flume/apache-flume-1.6.0-cdh5.8.4-bin/conf/xx.conf -Dflume.root.logger=INFO,console <br>
（像我的文件放置在这个目录下，启动即可）</p>

<p>这样，kafka就能接收到我们上传的日志了。 <br>
修改日志文件，kafka能实时接收到增量日志。 <br>
大功告成~</p>

<hr>

<p>[1]linux环境如何查看jdk安装路径: <a href="https://www.cnblogs.com/kerrycode/archive/2015/08/27/4762921.html" rel="nofollow">https://www.cnblogs.com/kerrycode/archive/2015/08/27/4762921.html</a></p>

<p>[2]Linux下搭建kafka环境简易教程【转】：<a href="http://blog.csdn.net/aitcax/article/details/49583351" rel="nofollow">http://blog.csdn.net/aitcax/article/details/49583351</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>