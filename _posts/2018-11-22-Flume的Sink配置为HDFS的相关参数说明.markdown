---
layout:     post
title:      Flume的Sink配置为HDFS的相关参数说明
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>Flume中的HDFS Sink应该是非常常用的，其中的配置参数也比较多，在这里记录备忘一下。</p>

<ul><li><strong>channel</strong></li>
	<li><strong>type</strong></li>
</ul><p>hdfs</p>

<ul><li><strong>path</strong></li>
</ul><p>写入hdfs的路径，需要包含文件系统标识，比如：hdfs://namenode/flume/webdata/</p>

<p>可以使用flume提供的日期及%{host}表达式。</p>

<ul><li>filePrefix</li>
</ul><p>默认值：FlumeData</p>

<p>写入hdfs的文件名前缀，可以使用flume提供的日期及%{host}表达式。</p>

<ul><li>fileSuffix</li>
</ul><p>写入hdfs的文件名后缀，比如：.lzo .log等。</p>

<ul><li>inUsePrefix</li>
</ul><p>临时文件的文件名前缀，hdfs sink会先往目标目录中写临时文件，再根据相关规则重命名成最终目标文件；</p>

<ul><li>inUseSuffix</li>
</ul><p>默认值：.tmp</p>

<p>临时文件的文件名后缀。</p>

<ul><li>rollInterval</li>
</ul><p>默认值：30</p>

<p>hdfs sink间隔多长将临时文件滚动成最终目标文件，单位：秒；</p>

<p>如果设置成0，则表示不根据时间来滚动文件；</p>

<p><strong>注：滚动（roll</strong><strong>）指的是，hdfs sink</strong><strong>将临时文件重命名成最终目标文件，并新打开一个临时文件来写入数据；</strong></p>

<ul><li>rollSize</li>
</ul><p>默认值：1024</p>

<p>当临时文件达到该大小（单位：bytes）时，滚动成目标文件；</p>

<p>如果设置成0，则表示不根据临时文件大小来滚动文件；</p>

<ul><li>rollCount</li>
</ul><p>默认值：10</p>

<p>当events数据达到该数量时候，将临时文件滚动成目标文件；</p>

<p>如果设置成0，则表示不根据events数据来滚动文件；</p>

<ul><li>idleTimeout</li>
</ul><p>默认值：0<br>
当目前被打开的临时文件在该参数指定的时间（秒）内，没有任何数据写入，则将该临时文件关闭并重命名成目标文件；</p>

<ul><li>batchSize</li>
</ul><p>默认值：100</p>

<p>每个批次刷新到HDFS上的events数量；</p>

<ul><li>codeC</li>
</ul><p>文件压缩格式，包括：gzip, bzip2, lzo, lzop, snappy</p>

<ul><li>fileType</li>
</ul><p>默认值：SequenceFile</p>

<p>文件格式，包括：SequenceFile, DataStream,CompressedStream</p>

<p>当使用DataStream时候，文件不会被压缩，不需要设置hdfs.codeC;</p>

<p>当使用CompressedStream时候，必须设置一个正确的hdfs.codeC值；</p>

<ul><li>maxOpenFiles</li>
</ul><p>默认值：5000</p>

<p>最大允许打开的HDFS文件数，当打开的文件数达到该值，最早打开的文件将会被关闭；</p>

<ul><li>minBlockReplicas</li>
</ul><p>默认值：HDFS副本数</p>

<p>写入HDFS文件块的最小副本数。</p>

<p>该参数会影响文件的滚动配置，一般将该参数配置成1，才可以按照配置正确滚动文件。</p>

<p>待研究。</p>

<ul><li>writeFormat</li>
</ul><p>写sequence文件的格式。包含：Text, Writable（默认）</p>

<ul><li>callTimeout</li>
</ul><p>默认值：10000</p>

<p>       执行HDFS操作的超时时间（单位：毫秒）；</p>

<ul><li>threadsPoolSize</li>
</ul><p>默认值：10</p>

<p>hdfs sink启动的操作HDFS的线程数。</p>

<ul><li>rollTimerPoolSize</li>
</ul><p>默认值：1</p>

<p>hdfs sink启动的根据时间滚动文件的线程数。</p>

<ul><li>kerberosPrincipal</li>
</ul><p>HDFS安全认证kerberos配置；</p>

<ul><li>kerberosKeytab</li>
</ul><p>HDFS安全认证kerberos配置；</p>

<ul><li>proxyUser</li>
</ul><p>代理用户</p>

<ul><li>round</li>
</ul><p>默认值：false</p>

<p>是否启用时间上的”舍弃”，这里的”舍弃”，类似于”四舍五入”，后面再介绍。如果启用，则会影响除了%t的其他所有时间表达式；</p>

<ul><li>roundValue</li>
</ul><p>默认值：1</p>

<p>时间上进行“舍弃”的值；</p>

<ul><li>roundUnit</li>
</ul><p>默认值：seconds</p>

<p>时间上进行”舍弃”的单位，包含：second,minute,hour</p>

<p> </p>

<p>示例：</p>

<p>a1.sinks.k1.hdfs.path = /flume/events/%y-%m-%d/%H%M/%S</p>

<p>a1.sinks.k1.hdfs.round = true</p>

<p>a1.sinks.k1.hdfs.roundValue = 10</p>

<p>a1.sinks.k1.hdfs.roundUnit = minute</p>

<p>当时间为2015-10-16 17:38:59时候，hdfs.path依然会被解析为：</p>

<p>/flume/events/20151016/17:30/00</p>

<p>因为设置的是舍弃10分钟内的时间，因此，该目录每10分钟新生成一个。</p>

<ul><li>timeZone</li>
</ul><p>默认值：Local Time</p>

<p>时区。</p>

<ul><li>useLocalTimeStamp</li>
</ul><p>默认值：flase</p>

<p>是否使用当地时间。</p>

<ul><li>closeTries</li>
</ul><p>默认值：0</p>

<p>hdfs sink关闭文件的尝试次数；</p>

<p>如果设置为1，当一次关闭文件失败后，hdfs sink将不会再次尝试关闭文件，这个未关闭的文件将会一直留在那，并且是打开状态。</p>

<p>设置为0，当一次关闭失败后，hdfs sink会继续尝试下一次关闭，直到成功。</p>

<ul><li>retryInterval</li>
</ul><p>默认值：180（秒）</p>

<p>hdfs sink尝试关闭文件的时间间隔，如果设置为0，表示不尝试，相当于于将hdfs.closeTries设置成1.</p>

<ul><li>serializer</li>
</ul><p>默认值：TEXT</p>

<p>序列化类型。其他还有：avro_event或者是实现了EventSerializer.Builder的类名。</p>

<p> </p>

<p>下面的配置中，在HDFS的/tmp/lxw1234/目录下，每天生成一个格式为20151016的目录，</p>

<p>目标文件每5分钟生成一个，文件名格式为：log_20151016_13.1444973768543.lzo</p>

<p>目标文件采用lzo压缩。</p>

<pre class="has">
<code>agent_lxw1234.sinks.sink1.type = hdfs
agent_lxw1234.sinks.sink1.hdfs.path = hdfs://cdh5/tmp/lxw1234/%Y%m%d
agent_lxw1234.sinks.sink1.hdfs.filePrefix = log_%Y%m%d_%H
agent_lxw1234.sinks.sink1.hdfs.fileSuffix = .lzo
agent_lxw1234.sinks.sink1.hdfs.useLocalTimeStamp = true
agent_lxw1234.sinks.sink1.hdfs.writeFormat = Text
agent_lxw1234.sinks.sink1.hdfs.fileType = CompressedStream
agent_lxw1234.sinks.sink1.hdfs.rollCount = 0
agent_lxw1234.sinks.sink1.hdfs.rollSize = 0
agent_lxw1234.sinks.sink1.hdfs.rollInterval = 600
agent_lxw1234.sinks.sink1.hdfs.codeC = lzop
agent_lxw1234.sinks.sink1.hdfs.batchSize = 100
agent_lxw1234.sinks.sink1.hdfs.threadsPoolSize = 10
agent_lxw1234.sinks.sink1.hdfs.idleTimeout = 0
agent_lxw1234.sinks.sink1.hdfs.minBlockReplicas = 1</code></pre>

<p> </p>            </div>
                </div>