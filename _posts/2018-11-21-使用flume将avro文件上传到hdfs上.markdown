---
layout:     post
title:      使用flume将avro文件上传到hdfs上
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/strongyoung88/article/details/54407011				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="使用flume将avro文件上传到hdfs上">使用flume将avro文件上传到hdfs上</h1>

<p>场景介绍：把一个文件夹下的avro文件上传到hdfs上。source使用的是<code>spooldir</code>，sink使用的是<code>hdfs</code>。</p>

<h1 id="配置-flumeconf">配置 flume.conf</h1>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor"># memory channel called ch1 on agent1</span>
agent1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.ch</span>1<span class="hljs-preprocessor">.type</span> = memory

<span class="hljs-preprocessor"># source</span>
agent1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.spooldir</span>-source1<span class="hljs-preprocessor">.channels</span> = ch1
agent1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.spooldir</span>-source1<span class="hljs-preprocessor">.type</span> = spooldir
agent1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.spooldir</span>-source1<span class="hljs-preprocessor">.spoolDir</span>=/home/yang/data/avro/
agent1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.spooldir</span>-source1<span class="hljs-preprocessor">.basenameHeader</span> = true
agent1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.spooldir</span>-source1<span class="hljs-preprocessor">.deserializer</span> = AVRO
agent1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.spooldir</span>-source1<span class="hljs-preprocessor">.deserializer</span><span class="hljs-preprocessor">.schemaType</span> = LITERAL

<span class="hljs-preprocessor"># sink</span>
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.hdfs</span>-sink1<span class="hljs-preprocessor">.channel</span> = ch1
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.hdfs</span>-sink1<span class="hljs-preprocessor">.type</span> = hdfs

agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.hdfs</span>-sink1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.path</span> = hdfs://node1:<span class="hljs-number">8020</span>/user/yang/test
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.hdfs</span>-sink1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.fileType</span> = DataStream
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.hdfs</span>-sink1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.fileSuffix</span> = <span class="hljs-preprocessor">.avro</span>
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.hdfs</span>-sink1<span class="hljs-preprocessor">.serializer</span> =  org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.serialization</span><span class="hljs-preprocessor">.AvroEventSerializer</span>$Builder
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.hdfs</span>-sink1<span class="hljs-preprocessor">.serializer</span><span class="hljs-preprocessor">.compressionCodec</span> = snappy

agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.hdfs</span>-sink1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.filePrefix</span> = %{basename}
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.hdfs</span>-sink1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollSize</span> = <span class="hljs-number">0</span>
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.hdfs</span>-sink1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollCount</span> = <span class="hljs-number">0</span>

<span class="hljs-preprocessor"># Finally, now that we've defined all of our components, tell</span>
<span class="hljs-preprocessor"># agent1 which ones we want to activate.</span>
agent1<span class="hljs-preprocessor">.channels</span> = ch1
agent1<span class="hljs-preprocessor">.sources</span> = spooldir-source1
agent1<span class="hljs-preprocessor">.sinks</span> = hdfs-sink1</code></pre>

<p><em>注意</em>：上面的配置有几个需要特别注意一下。</p>

<ul>
<li>源（source)部分：</li>
</ul>

<pre class="prettyprint"><code class=" hljs avrasm">agent1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.spooldir</span>-source1<span class="hljs-preprocessor">.deserializer</span> = AVRO
agent1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.spooldir</span>-source1<span class="hljs-preprocessor">.deserializer</span><span class="hljs-preprocessor">.schemaType</span> = LITERAL</code></pre>

<p>deserializer默认的是<code>LINE</code>,如果不设置avro，会报异常，因为我们这里的文件是avro文件。</p>

<p>而deserializer.schemaType默认的是HASH，如果不设为LITERAL，会报以下异常：<code>process failed org.apache.flume.FlumeException: Could not find schema for event</code></p>

<ul>
<li>sink部分：</li>
</ul>



<pre class="prettyprint"><code class=" hljs avrasm">agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.hdfs</span>-sink1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.fileType</span> = DataStream
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.hdfs</span>-sink1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.fileSuffix</span> = <span class="hljs-preprocessor">.avro</span>
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.hdfs</span>-sink1<span class="hljs-preprocessor">.serializer</span> =  org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.serialization</span><span class="hljs-preprocessor">.AvroEventSerializer</span>$Builder
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.hdfs</span>-sink1<span class="hljs-preprocessor">.serializer</span><span class="hljs-preprocessor">.compressionCodec</span> = snappy</code></pre>

<p><code>hdfs.fileType</code>默认的是<code>SequenceFile</code>，如果使用这个文件类型，数据传输到hdfs之后，会出现无法正常解析avro文件的情况，如异常<code>Not a avro data file</code>；<code>hdfs.fileSuffix</code>是指示在文件后面加上后缀名，注意，文件后缀名的那个点（.）不能省略掉，为什么要加后缀名呢？因为在许多情况下，比如使用spark进行读取avro文件的时候，它会先判断文件后缀名，如果不是<code>.avro</code>结尾的文件，它会认为这不是一个avro文件，然后会抛出异常。</p>

<p>特别要注意的是，<code>serializer</code>的前面没有<code>hdfs</code>，而且这个序列化类不是flume自带的，需要自己去下载源代码并打包，这个源码在<a href="https://github.com/cloudera/cdk" rel="nofollow">github</a>项目上，<code>AvroEventSerializer$Builder</code>在项目的<a href="https://github.com/cloudera/cdk/blob/master/cdk-flume-avro-event-serializer/src/main/java/org/apache/flume/serialization/AvroEventSerializer.java" rel="nofollow">位置</a>。我们可以使用git clone项目下来，然后切换到目录<code>cdk-flume-avro-event-serializer</code>下，然后<code>mvn package</code>，再把生成的jar包（在target目录下）拷贝到flume的<code>lib</code>目录下。</p>

<p>参考文献：</p>

<p>[1] <a href="http://flume.apache.org/FlumeUserGuide.html" rel="nofollow">http://flume.apache.org/FlumeUserGuide.html</a></p>

<p>[2] <a href="http://stackoverflow.com/questions/21617025/flume-directory-to-avro-avro-to-hdfs-not-valid-avro-after-transfer?rq=1" rel="nofollow">http://stackoverflow.com/questions/21617025/flume-directory-to-avro-avro-to-hdfs-not-valid-avro-after-transfer?rq=1</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>