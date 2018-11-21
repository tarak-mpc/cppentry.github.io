---
layout:     post
title:      Spark调优之Tuning Spark(Part 2)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u011564172/article/details/73739752				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="概述">概述</h1>

<p>翻译Spark官方调优指南<a href="http://spark.apache.org/docs/latest/tuning.html" rel="nofollow">Tuning Spark</a>剩余部分<strong>Other Considerations</strong>。</p>



<h3 id="level-of-parallelism">Level of Parallelism</h3>

<p>并行度不够，会导致集群的资源利用的不充分，并行程度由task数决定，task数和最后一个transformation的Partition数一致，因此，调整并行度就是调整Partition数。</p>

<p>创建RDD时可以指定Partition数量，默认由<strong>spark.default.parallelism</strong>参数决定(YARN模式下为所有executor的core数总和)，能够触发Shuffle的transformation可以通过参数的方式修改Partition数，推荐每个core运行2-3 task，更多内容请参考我的博客<a href="http://blog.csdn.net/u011564172/article/details/53611109" rel="nofollow">Spark RDD之Partition</a>，<a href="http://blog.csdn.net/u011564172/article/details/73521804" rel="nofollow">Spark调优之Cloudera博客(Part 2)</a>中关于<strong>Parallelism</strong>部分的介绍。</p>



<h3 id="memory-usage-of-reduce-tasks">Memory Usage of Reduce Tasks</h3>

<p>OOM是Spark开发中最常见的问题，Spark中也有溢写机制，内存不足时写磁盘，导致OOM的原因可以分为以下 两方面</p>

<ul>
<li>非Spark原因导致，可以概括为JVM内存模型中某个部分内存不足导致OOM(常见如Perm区)，这一类问题是所有运行在JVM之上的程序都会遇到的，更详细的内容请参考<a href="https://yq.aliyun.com/articles/40402" rel="nofollow">深入理解Java虚拟机：OutOfMemory实战</a>。</li>
<li>Spark运行机制导致，由于Spark溢写机制的存在，仅仅是内存放不下数据并不会导致OOM，典型的场景是，当Shuffle中涉及到<strong>group</strong>、<strong>aggregation</strong>操作，需要把对应数据全部加载到内存，如果此时内存大小不足会触发OOM。</li>
</ul>

<p>针对OOM问题，解决的思路如下</p>

<ul>
<li>如果使用的资源管理器是YARN，通过Web UI查看日志确定内存使用情况，根据日志做出对应调整，参考<a href="http://caiguangguang.blog.51cto.com/1652935/1407424" rel="nofollow">yarn oom问题一例</a>的思路。</li>
<li>通过日志没有思路的话，需要输出OOM时的堆栈信息，spark-submit提交任务时增加如下参数<pre>--conf "spark.executor.extraJavaOptions= -XX:+HeapDumpOnOutOfMemoryError" <br>
</pre>使用eclipse插件<a href="http://www.eclipse.org/mat/" rel="nofollow">Memory Analyzer (MAT)</a>分析。</li>
<li>此外，增加Partition数是最简单，也是优先考虑尝试的方式。</li>
</ul>



<h3 id="broadcasting-large-variables">Broadcasting Large Variables</h3>

<p>如果transformation中使用了较大的对象(大于20KB)，<a href="http://spark.apache.org/docs/latest/programming-guide.html#broadcast-variables" rel="nofollow">Broadcast</a>功能能够降低最终生成task的大小，因为每个task内部不再保存这个变量，而是通过driver直接发送给executor，参考<a href="https://stackoverflow.com/questions/26884871/advantage-of-broadcast-variables" rel="nofollow">Advantage of Broadcast Variables <br>
</a>，当Partition较多时，效果越发明显。</p>



<h3 id="data-locality">Data Locality</h3>

<p><strong>就近执行</strong>对Spark性能有较大影响，计算跟着数据走，两者在一个JVM或节点上是最快的，否则需要网络传输数据，有以下几个级别</p>

<table>
    <tbody>
        <tr>
            <td>PROCESS_LOCAL</td>
            <td>数据和task在一个JVM，最好的情况，通常是Local模式下</td>
        </tr>
        <tr>
            <td>NODE_LOCAL</td>
            <td>数据和task在一个节点，比PROCESS_LOCAL差一些</td>
        </tr>
        <tr>
            <td>NO_PREF</td>
            <td>从任何地方访问数据速度相同，没有locality偏好</td>
        </tr>
        <tr>
            <td>RACK_LOCAL</td>
            <td>数据和task在相同机架不同节点</td>
        </tr>
        <tr>
            <td>ANY</td>
            <td>数据和task在不同机架</td>
        </tr>
    </tbody>
</table>

<p>通过Spark Web UI查看Data Locality，如下 <br>
<img src="https://img-blog.csdn.net/20170705144409437?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTU2NDE3Mg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="" title=""></p>

<p>Spark根据就近执行原则选择较近的节点执行task，但较近的节点CPU可能处于忙碌状态，暂时无法执行task，此时Spark等待一段时间(由spark.locality参数确定，默认3s)，等待结束后依然如此，task发送到其他executor执行。</p>

<p>参考： <br>
<a href="https://yq.aliyun.com/articles/40402" rel="nofollow">深入理解Java虚拟机：OutOfMemory实战</a> <br>
<a href="http://spark.apache.org/docs/latest/tuning.html#memory-usage-of-reduce-tasks" rel="nofollow">Tuning Spark</a> <br>
<a href="http://caiguangguang.blog.51cto.com/1652935/1407424" rel="nofollow">yarn oom问题一例</a> <br>
<a href="https://stackoverflow.com/questions/26884871/advantage-of-broadcast-variables" rel="nofollow">Advantage of Broadcast Variables <br>
</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>