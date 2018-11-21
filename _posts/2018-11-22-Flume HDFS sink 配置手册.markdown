---
layout:     post
title:      Flume HDFS sink 配置手册
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="flume-hdfs-sink-配置手册">Flume HDFS sink 配置手册</h1>

<hr>

<p>Flume默认没有引入HDFS的相关jar包，如果有需要使用HDFS作为sink，需要自行添加相关jar。</p>



<h3 id="pluginsd目录">plugins.d目录：</h3>

<p>Flume可使用plugins.d框架部署自定义代码。Flume提供了一种简单的部署自定义代码的方式：plugins.d框架。 <br>
plugins.d目录是自动添加到Flume环境变量的，因此不需要明确添加到环境变量中。 <br>
如果FLUME_HOME目录下没有plugins.d目录，可以新建一个。对于每个自定义组件，一般在plugins.d目录下创建一个新的子目录（名字无关紧要，自己定），在这个新的子目录下，需要有3个目录（lib , libext , native）。</p>



<h3 id="hdfs依赖jar包">hdfs依赖jar包：</h3>

<p>将HDFS做为Flume的sink需要依赖以下jar。  </p>

<blockquote>
  <p>commons-configuration-1.6.jar <br>
  commons-io-2.4.jar <br>
  hadoop-annotations.jar <br>
  hadoop-auth.jar <br>
  hadoop-common.jar <br>
  hadoop-hdfs.jar <br>
  htrace-core-3.1.0-incubating.jar  </p>
</blockquote>

<p>将依赖的jar包， 放入plugins.d目录下新建的sink目录下的lib目录中。</p>



<h3 id="hdfs配置文件">hdfs配置文件：</h3>

<p>在hadoop平台中复制如下文件至conf/目录。  </p>

<blockquote>
  <p>core-site.xml <br>
  hdfs-site.xml</p>
</blockquote>



<h3 id="sink-配置">sink 配置：</h3>

<p>参考例子：</p>

<blockquote>
  <p>agent1.sinks.k1.channel = ch1 <br>
  agent1.sinks.k1.type = hdfs <br>
  agent1.sinks.k1.hdfs.path = hdfs://172.17.8.61:8020/ues/ <br>
  agent1.sinks.k1.hdfs.fileType = DataStream <br>
  agent1.sinks.k1.hdfs.filePrefix = mureport_%Y%m%d <br>
  agent1.sinks.k1.hdfs.fileSuffix = .txt <br>
  agent1.sinks.k1.hdfs.useLocalTimeStamp = true <br>
  agent1.sinks.k1.hdfs.writeFormat = Text <br>
  agent1.sinks.k1.hdfs.rollCount = 0 <br>
  agent1.sinks.k1.hdfs.rollSize = 0 <br>
  agent1.sinks.k1.hdfs.rollInterval = 600 <br>
  agent1.sinks.k1.hdfs.batchSize = 100 <br>
  agent1.sinks.k1.hdfs.threadsPoolSize = 10 <br>
  agent1.sinks.k1.hdfs.idleTimeout = 0 <br>
  agent1.sinks.k1.hdfs.minBlockReplicas = 1  </p>
</blockquote>



<h3 id="参数说明">参数说明：</h3>



<h4 id="channel">channel</h4>



<h4 id="type-hdfs">type hdfs</h4>



<h4 id="path">path</h4>

<p>写入hdfs的路径，需要包含文件系统标识，比如：hdfs://namenode/flume/webdata/</p>

<p>可以使用flume提供的日期及%{host}表达式。</p>



<h4 id="fileprefix">filePrefix</h4>

<p>默认值：FlumeData</p>

<p>写入hdfs的文件名前缀，可以使用flume提供的日期及%{host}表达式。</p>



<h4 id="filesuffix">fileSuffix</h4>

<p>写入hdfs的文件名后缀，比如：.lzo .log等。</p>



<h4 id="inuseprefix">inUsePrefix</h4>

<p>临时文件的文件名前缀，hdfs sink会先往目标目录中写临时文件，再根据相关规则重命名成最终目标文件；</p>



<h4 id="inusesuffix">inUseSuffix</h4>

<p>默认值：.tmp</p>

<p>临时文件的文件名后缀。</p>



<h4 id="rollinterval">rollInterval</h4>

<p>默认值：30</p>

<p>hdfs sink间隔多长将临时文件滚动成最终目标文件，单位：秒；</p>

<p>如果设置成0，则表示不根据时间来滚动文件；</p>

<p>注：滚动（roll）指的是，hdfs sink将临时文件重命名成最终目标文件，并新打开一个临时文件来写入数据；</p>



<h4 id="rollsize">rollSize</h4>

<p>默认值：1024</p>

<p>当临时文件达到该大小（单位：bytes）时，滚动成目标文件；</p>

<p>如果设置成0，则表示不根据临时文件大小来滚动文件；</p>



<h4 id="rollcount">rollCount</h4>

<p>默认值：10</p>

<p>当events数据达到该数量时候，将临时文件滚动成目标文件；</p>

<p>如果设置成0，则表示不根据events数据来滚动文件；</p>



<h4 id="idletimeout">idleTimeout</h4>

<p>默认值：0 <br>
当目前被打开的临时文件在该参数指定的时间（秒）内，没有任何数据写入，则将该临时文件关闭并重命名成目标文件；</p>



<h4 id="batchsize">batchSize</h4>

<p>默认值：100</p>

<p>每个批次刷新到HDFS上的events数量；</p>



<h4 id="codec">codeC</h4>

<p>文件压缩格式，包括：gzip, bzip2, lzo, lzop, snappy</p>



<h4 id="filetype">fileType</h4>

<p>默认值：SequenceFile</p>

<p>文件格式，包括：SequenceFile, DataStream,CompressedStream</p>

<p>当使用DataStream时候，文件不会被压缩，不需要设置hdfs.codeC;</p>

<p>当使用CompressedStream时候，必须设置一个正确的hdfs.codeC值；</p>



<h4 id="maxopenfiles">maxOpenFiles</h4>

<p>默认值：5000</p>

<p>最大允许打开的HDFS文件数，当打开的文件数达到该值，最早打开的文件将会被关闭；</p>



<h4 id="minblockreplicas">minBlockReplicas</h4>

<p>默认值：HDFS副本数</p>

<p>写入HDFS文件块的最小副本数。</p>

<p>该参数会影响文件的滚动配置，一般将该参数配置成1，才可以按照配置正确滚动文件。</p>

<p>待研究。</p>



<h4 id="writeformat">writeFormat</h4>

<p>写sequence文件的格式。包含：Text, Writable（默认）</p>



<h4 id="calltimeout">callTimeout</h4>

<p>默认值：10000</p>

<p>执行HDFS操作的超时时间（单位：毫秒）；</p>



<h4 id="threadspoolsize">threadsPoolSize</h4>

<p>默认值：10</p>

<p>hdfs sink启动的操作HDFS的线程数。</p>



<h4 id="rolltimerpoolsize">rollTimerPoolSize</h4>

<p>默认值：1</p>

<p>hdfs sink启动的根据时间滚动文件的线程数。</p>



<h4 id="kerberosprincipal">kerberosPrincipal</h4>

<p>HDFS安全认证kerberos配置；</p>



<h4 id="kerberoskeytab">kerberosKeytab</h4>

<p>HDFS安全认证kerberos配置；</p>



<h4 id="proxyuser">proxyUser</h4>

<p>代理用户</p>



<h4 id="round">round</h4>

<p>默认值：false</p>

<p>是否启用时间上的”舍弃”，这里的”舍弃”，类似于”四舍五入”，后面再介绍。如果启用，则会影响除了%t的其他所有时间表达式；</p>



<h4 id="roundvalue">roundValue</h4>

<p>默认值：1</p>

<p>时间上进行“舍弃”的值；</p>



<h4 id="roundunit">roundUnit</h4>

<p>默认值：seconds</p>

<p>时间上进行”舍弃”的单位，包含：second,minute,hour</p>

<blockquote>
  <p>示例：</p>
  
  <p>a1.sinks.k1.hdfs.path = /flume/events/%y-%m-%d/%H%M/%S</p>
  
  <p>a1.sinks.k1.hdfs.round = true</p>
  
  <p>a1.sinks.k1.hdfs.roundValue = 10</p>
  
  <p>a1.sinks.k1.hdfs.roundUnit = minute</p>
  
  <p>当时间为2015-10-16 17:38:59时候，hdfs.path依然会被解析为：</p>
  
  <p>/flume/events/20151016/17:30/00</p>
  
  <p>因为设置的是舍弃10分钟内的时间，因此，该目录每10分钟新生成一个。</p>
</blockquote>



<h4 id="timezone">timeZone</h4>

<p>默认值：Local Time</p>

<p>时区。</p>



<h4 id="uselocaltimestamp">useLocalTimeStamp</h4>

<p>默认值：flase</p>

<p>是否使用当地时间。</p>



<h4 id="closetries">closeTries</h4>

<p>默认值：0</p>

<p>hdfs sink关闭文件的尝试次数；</p>

<p>如果设置为1，当一次关闭文件失败后，hdfs sink将不会再次尝试关闭文件，这个未关闭的文件将会一直留在那，并且是打开状态。</p>

<p>设置为0，当一次关闭失败后，hdfs sink会继续尝试下一次关闭，直到成功。</p>



<h4 id="retryinterval">retryInterval</h4>

<p>默认值：180（秒）</p>

<p>hdfs sink尝试关闭文件的时间间隔，如果设置为0，表示不尝试，相当于于将hdfs.closeTries设置成1.</p>



<h4 id="serializer">serializer</h4>

<p>默认值：TEXT</p>

<p>序列化类型。其他还有：avro_event或者是实现了EventSerializer.Builder的类名。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>