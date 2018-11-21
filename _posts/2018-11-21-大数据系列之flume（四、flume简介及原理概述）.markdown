---
layout:     post
title:      大数据系列之flume（四、flume简介及原理概述）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：版权所有，转载、分享请说明转载地址。					https://blog.csdn.net/snail_bing/article/details/81780504				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1 id="%E2%80%8B%E2%80%8B%E2%80%8B%E2%80%8B%E2%80%8B%E2%80%8B%E2%80%8BApache%20Flume"><strong><strong><strong>A</strong></strong><strong><strong>pache </strong></strong><strong><strong>F</strong></strong><strong><strong>lume</strong></strong></strong><strong><strong><strong>概述</strong></strong></strong></h1>

<p><img alt="" class="has" height="94" src="https://img-blog.csdn.net/20180817170738178?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NuYWlsX2Jpbmc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="554"></p>

<p style="text-indent:50px;">Flume是Cloudera提供的一个高可用的，高可靠的，分布式的海量日志采集、聚合和传输的系统，Flume支持在日志系统中定制各类数据发送方，用于收集数据；同时，Flume提供对数据进行简单处理，并写到各种数据接受方（可定制）的能力。目前是Apache的顶级项目。</p>

<p style="text-indent:50px;">当前Flume有两个版本Flume 0.9X版本的统称Flume-og，Flume1.X版本的统称Flume-ng。由于Flume-ng经过重大重构，与Flume-og有很大不同，使用时请注意区分。og（original generation）   ng（next generation）</p>

<h3><strong><strong><strong>可靠性 </strong></strong></strong></h3>

<p style="margin-left:0pt;">当节点出现故障时，日志能够被传送到其他节点上而不会丢失。Flume提供了三种级别的可靠性保障，从强到弱依次分别为：</p>

<p style="margin-left:0pt;">1、end-to-end（收到数据agent首先将event写到磁盘上，当数据传送成功后，再删除；如果数据发送失败，可以重新发送）。</p>

<p>2、Store on failure（这也是Scribe-Facebook开源的日志收集系统-采用的策略，当数据接收方crash（崩溃）时，将数据写到本地，待恢复后，继续发送）。</p>

<p>3、Besteffort（数据发送到接收方后，不会进行确认）。</p>

<h1><strong><strong><strong>flume中的概念、模型和特点</strong></strong></strong></h1>

<h2><strong><strong><strong>flume中的一些重要概念</strong></strong></strong></h2>

<p><strong>1、flume Event：</strong></p>

<p style="margin-left:0pt;">flume 事件，被定义为一个具有有效荷载的字节数据流和可选的字符串属性集。(json格式的字符串，由headers和body两部分组成)</p>

<p><strong>2、flume Agent：</strong></p>

<p style="margin-left:0pt;">flume 代理，是一个进程承载从外部源事件流到下一个目的地的过程。包含source channel和sink。</p>

<p><strong>3、Source</strong></p>

<p style="margin-left:0pt;">数据源，消耗外部传递给他的事件，外部源将数据按照flume Source 能识别的格式将Flume 事件发送给flume Source。</p>

<p><strong>4、Channel</strong></p>

<p style="margin-left:0pt;">数据通道，是一个被动的存储，用来保持事件，直到由一个flume Sink消耗。</p>

<p><strong>5、Sink</strong></p>

<p style="margin-left:0pt;">数据汇聚点，代表外部数据存放位置。发送flume event到指定的外部目标。</p>

<h2><strong><strong><strong>flume流动模型</strong></strong></strong></h2>

<p>​​​​​​​flume流动模型如下图所示：</p>

<p><img alt="" class="has" height="237" src="https://img-blog.csdn.net/20180817171558399?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NuYWlsX2Jpbmc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="654"></p>

<h2>​​​​​​​<strong><strong><strong>flume的特点</strong></strong></strong></h2>

<p><strong>​​​​​​​​​​​​​​1、复杂流动性</strong></p>

<p style="margin-left:0pt;">Flume允许用户进行多级流动到最终目的地，也允许扇出流（一到多）、扇入流(多到一)的、故障转移和失败处理。</p>

<p><strong>​​​​​​​2、可靠性</strong></p>

<p style="margin-left:0pt;">事务性的数据传递，保证了数据的可靠性。(flume一次处理一批数据，只要有任意一条失败了，那么flume会重新处理这批数据)</p>

<p><strong>​​​​​​​3、可恢复性</strong></p>

<p style="margin-left:0pt;">通道可以以内存或文件的方式实现，内存更快，但是不可恢复，而文件虽然比较慢但提供了可恢复性。</p>

<p style="margin-left:0pt;"> </p>

<h1><strong><strong><strong>Source详解 </strong></strong></strong></h1>

<h2><strong><strong><strong>1、Avro Source</strong></strong></strong></h2>

<h3><strong><strong><strong>Avro Source 概述</strong></strong></strong></h3>

<p style="text-indent:50px;">监听AVRO端口来接受来自外部AVRO客户端的事件流。利用Avro Source可以实现多级流动、扇出流、扇入流等效果。另外也可以接受通过flume提供的Avro客户端发送的日志信息。​​​​​​​</p>

<h3><strong><strong><strong> Avro Source属性说明</strong></strong></strong></h3>

<p style="margin-left:0pt;">!channels –  </p>

<p style="margin-left:0pt;">!type – 类型名称，"AVRO"</p>

<p style="margin-left:0pt;">!bind – 需要监听的主机名或IP</p>

<p style="margin-left:0pt;">!port – 要监听的端口</p>

<p style="margin-left:0pt;">threads – 工作线程最大线程数</p>

<p style="margin-left:0pt;">selector.type    </p>

<p style="margin-left:0pt;">selector.*    </p>

<p style="margin-left:0pt;">interceptors – 空格分隔的拦截器列表</p>

<p style="margin-left:0pt;">interceptors.*    </p>

<p style="margin-left:0pt;">compression-type none 压缩类型，可以是“none”或“default”，这个值必须和AvroSource的压缩格式匹配</p>

<p style="margin-left:0pt;">ssl false 是否启用ssl加密，如果启用还需要配置一个“keystore”和一个“keystore-password”。</p>

<p style="margin-left:0pt;">keystore – 为SSL提供的java密钥文件所在路径。</p>

<p style="margin-left:0pt;">keystore-password – 为SSL提供的java密钥文件 密码。</p>

<p style="margin-left:0pt;">keystore-type JKS 密钥库类型可以是“JKS”或“PKCS12”。</p>

<p style="margin-left:0pt;">exclude-protocols SSLv3 空格分隔开的列表，用来指定在SSL / TLS协议中排除。SSLv3将总是被排除除了所指定的协议。</p>

<p style="margin-left:0pt;">ipFilter false 如果需要为netty开启ip过滤，将此项设置为true</p>

<p style="margin-left:0pt;">ipFilterRules – 定义netty的ip过滤设置表达式规则</p>

<h2>​​​​​​​2、<strong><strong><strong>Exec Source</strong></strong></strong><strong><strong><strong> </strong></strong></strong></h2>

<h3><strong><strong><strong>Exec Source概述</strong></strong></strong></h3>

<p style="margin-left:0pt;">可以将命令产生的输出作为源。</p>

<h3>​​​​​​​<strong><strong><strong> Exec Source属性说明</strong></strong></strong></h3>

<p style="margin-left:0pt;">!channels –  </p>

<p style="margin-left:0pt;">!type – 类型名称，需要是"exec"</p>

<p style="margin-left:0pt;">!command – 要执行的命令</p>

<p style="margin-left:0pt;">shell – A shell invocation used to run the command. e.g. /bin/sh -c. Required only for commands relying on shell features like wildcards, back ticks, pipes etc.</p>

<p style="margin-left:0pt;">restartThrottle 10000 毫秒为单位的时间，用来声明等待多久后尝试重试命令</p>

<p style="margin-left:0pt;">restart false 如果cmd挂了，是否重启cmd</p>

<p style="margin-left:0pt;">logStdErr false 无论是否是标准错误都该被记录</p>

<p style="margin-left:0pt;">batchSize 20 同时发送到通道中的最大行数</p>

<p style="margin-left:0pt;">batchTimeout 3000 如果缓冲区没有满，经过多长时间发送 数据</p>

<p style="margin-left:0pt;">selector.type 复制还是多路复用</p>

<p style="margin-left:0pt;">selector.*   Depends on the selector.type value</p>

<p style="margin-left:0pt;">interceptors – 空格分隔的拦截器列表</p>

<p style="margin-left:0pt;">interceptors.*</p>

<h2><strong><strong><strong>3、Spooling Directory Source</strong></strong></strong></h2>

<h3>​​​​​​​<strong><strong><strong>Spooling Directory Source概述</strong></strong></strong></h3>

<p style="text-indent:50px;">这个Source允许你将将要收集的数据放置到"自动搜集"目录中。这个Source将监视该目录，并将解析新文件的出现。事件处理逻辑是可插拔的，当一个文件被完全读入通道，它会被重命名或可选的直接删除。</p>

<p style="text-indent:50px;">要注意的是，放置到自动搜集目录下的文件不能修改，如果修改，则flume会报错。另外，也不能产生重名的文件，如果有重名的文件被放置进来，则flume会报错。</p>

<h3>​​​​​​​<strong><strong><strong>Spooling Directory Source属性说明</strong></strong></strong></h3>

<p style="margin-left:0pt;">!channels –  </p>

<p style="margin-left:0pt;">!type – 类型，需要指定为"spooldir"</p>

<p style="margin-left:0pt;">!spoolDir – 读取文件的路径，即"搜集目录"</p>

<p style="margin-left:0pt;">fileSuffix .COMPLETED 对处理完成的文件追加的后缀</p>

<p style="margin-left:0pt;">deletePolicy never 处理完成后是否删除文件，需是"never"或"immediate"</p>

<p style="margin-left:0pt;">fileHeader false 是否添加一个存储的绝对路径名的头文件.</p>

<p style="margin-left:0pt;">fileHeaderKey file Header key to use when appending absolute path filename to event header.</p>

<p style="margin-left:0pt;">basenameHeader false Whether to add a header storing the basename of the file.</p>

<p style="margin-left:0pt;">basenameHeaderKey basename Header Key to use when appending basename of file to event header.</p>

<p style="margin-left:0pt;">ignorePattern ^$ 正则表达式指定哪些文件需要忽略</p>

<p style="margin-left:0pt;">trackerDir .flumespool Directory to store metadata related to processing of files. If this path is not an absolute path, then it is interpreted as relative to the spoolDir.</p>

<p style="margin-left:0pt;">consumeOrder 处理文件的策略，oldest, youngest 或 random。</p>

<p style="margin-left:0pt;">maxBackoff 4000 The maximum time (in millis) to wait between consecutive attempts to write to the channel(s) if the channel is full. The source will start at a low backoff and increase it exponentially each time the channel throws a ChannelException, upto the value specified by this parameter.</p>

<p style="margin-left:0pt;">batchSize 100 Granularity at which to batch transfer to the channel</p>

<p style="margin-left:0pt;">inputCharset UTF-8 读取文件时使用的编码。</p>

<p style="margin-left:0pt;">decodeErrorPolicy FAIL 当在输入文件中发现无法处理的字符编码时如何处理。FAIL：抛出一个异常而无法 ​​解析该文件。REPLACE：用“替换字符”字符，通常是Unicode的U + FFFD更换不可解析角色。 忽略：掉落的不可解析的字符序列。</p>

<p style="margin-left:0pt;">deserializer LINE 声明用来将文件解析为事件的解析器。默认一行为一个事件。处理类必须实现EventDeserializer.Builder接口。</p>

<p style="margin-left:0pt;">deserializer.*   Varies per event deserializer.</p>

<p style="margin-left:0pt;">bufferMaxLines – (Obselete) This option is now ignored.</p>

<p style="margin-left:0pt;">bufferMaxLineLength 5000 (Deprecated) Maximum length of a line in the commit buffer. Use deserializer.maxLineLength instead.</p>

<p style="margin-left:0pt;">selector.type replicating replicating or multiplexing</p>

<p style="margin-left:0pt;">selector.*   Depends on the selector.type value</p>

<p style="margin-left:0pt;">interceptors – Space-separated list of interceptors</p>

<p style="margin-left:0pt;">interceptors.*</p>

<h2>​​​​​​​4、<strong><strong><strong>NetCat Source</strong></strong></strong> </h2>

<h3>​​​​​​​<strong><strong><strong>NetCat Source概述</strong></strong></strong></h3>

<p style="margin-left:0pt;">一个NetCat Source用来监听一个指定端口，并将接收到的数据的每一行转换为一个事件。</p>

<h3 style="margin-left:0pt;">​​​​​​​<strong><strong><strong> NetCat Source属性说明</strong></strong></strong></h3>

<p style="margin-left:0pt;">！channels –  </p>

<p style="margin-left:0pt;">！type – 类型名称，需要被设置为"netcat"</p>

<p style="margin-left:0pt;">！bind – 指定要绑定到的ip或主机名。</p>

<p style="margin-left:0pt;">！port – 指定要绑定到的端口号</p>

<p style="margin-left:0pt;">max-line-length 512 单行最大字节数</p>

<p style="margin-left:0pt;">ack-every-event true 对于收到的每一个Event是否响应"OK"</p>

<p style="margin-left:0pt;">selector.type</p>

<p style="margin-left:0pt;">selector.*  </p>

<p style="margin-left:0pt;">interceptors –</p>

<p style="margin-left:0pt;">interceptors.*</p>

<h2>5、​​​​​​​<strong><strong><strong>Sequence Generator Source</strong></strong></strong></h2>

<h3>​​​​​​​<strong><strong><strong>Sequence Generator Source概述</strong></strong></strong></h3>

<p style="margin-left:0pt;">一个简单的序列发生器，不断的产生事件，值是从0开始每次递增1。主要用来进行测试。​​​​​​​</p>

<h3 style="margin-left:0pt;"><strong><strong><strong>Sequence Generator Source属性说明</strong></strong></strong></h3>

<p style="margin-left:0pt;">!channels –  </p>

<p style="margin-left:0pt;">!type – 类型名称，必须为"seq"</p>

<p style="margin-left:0pt;">selector.type  </p>

<p style="margin-left:0pt;">selector.*</p>

<p style="margin-left:0pt;">interceptors –</p>

<p style="margin-left:0pt;">interceptors.*    </p>

<p style="margin-left:0pt;">batchSize</p>

<h2>​​​​​​​6、<strong><strong><strong>HTTP Source</strong></strong></strong><strong><strong><strong> </strong></strong></strong></h2>

<h3><strong><strong><strong>HTTP Source概述</strong></strong></strong></h3>

<p style="text-indent:50px;">HTTP Source接受HTTP的GET和POST请求作为Flume的事件,其中GET方式应该只用于试验。该Source需要提供一个可插拔的"处理器"来将请求转换为事件对象，这个处理器必须实现HTTPSourceHandler接口，该处理器接受一个 HttpServletRequest对象，并返回一个Flume Envent对象集合。</p>

<p style="text-indent:50px;">从一个HTTP请求中得到的事件将在一个事务中提交到通道中。因此允许像文件通道那样对通道提高效率。如果处理器抛出一个异常，Source将会返回一个400的HTTP状态码。如果通道已满，无法再将Event加入Channel，则Source返回503的HTTP状态码，表示暂时不可用。</p>

<h3>​​​​​​​<strong><strong><strong>HTTP Source属性说明</strong></strong></strong></h3>

<p style="margin-left:0pt;">！type   类型，必须为"HTTP"</p>

<p style="margin-left:0pt;">！port – 监听的端口</p>

<p style="margin-left:0pt;">bind 0.0.0.0 监听的主机名或ip</p>

<p style="margin-left:0pt;">handler   org.apache.flume.source.http.JSONHandler处理器类，需要实现HTTPSourceHandler接口</p>

<p style="margin-left:0pt;">handler.* – 处理器的配置参数</p>

<p style="margin-left:0pt;">selector.type</p>

<p style="margin-left:0pt;">selector.*  </p>

<p style="margin-left:0pt;">interceptors –</p>

<p style="margin-left:0pt;">interceptors.*    </p>

<p style="margin-left:0pt;">enableSSL false 是否开启SSL,如果需要设置为true。注意，HTTP不支持SSLv3。</p>

<p style="margin-left:0pt;">excludeProtocols SSLv3 空格分隔的要排除的SSL/TLS协议。SSLv3总是被排除的。</p>

<p style="margin-left:0pt;">keystore   密钥库文件所在位置。</p>

<p style="margin-left:0pt;">keystorePassword Keystore 密钥库密码</p>

<h2>7、​​​​​​​<strong><strong><strong>Custom source</strong></strong></strong></h2>

<h3><strong><strong><strong>Custom source概述</strong></strong></strong></h3>

<p style="text-indent:50px;">自定义源是自己实现源接口得到的，自定义源的类和其依赖包必须在开始时就放置到Flume的类加载目录下。</p>

<h3>​​​​​​​<strong><strong><strong>Custom source属性说明</strong></strong></strong></h3>

<p style="margin-left:0pt;">！channels –  </p>

<p style="margin-left:0pt;">！type – 类型，必须设置为自己的自定义处理类的全路径名</p>

<p style="margin-left:0pt;">selector.type</p>

<p style="margin-left:0pt;">elector.*</p>

<p style="margin-left:0pt;">interceptors –</p>

<p style="margin-left:0pt;">interceptors.*</p>

<p style="margin-left:0pt;"> </p>

<h1><strong><strong><strong>Channel详解</strong></strong></strong></h1>

<h2>1、​​​​​​​<strong><strong><strong>Memory Channel</strong></strong></strong></h2>

<h3><strong><strong><strong>Memory Channel概述</strong></strong></strong></h3>

<p style="text-indent:50px;">Memory Channel，内存通道；事件将被存储在内存中的具有指定大小的队列中。非常适合那些需要高吞吐量但是失败时会丢失数据的场景下。</p>

<h3>​​​​​​​<strong><strong><strong>Memory Channel属性说明</strong></strong></strong></h3>

<p style="margin-left:0pt;">!type – 类型，必须是“memory”</p>

<p style="margin-left:0pt;">capacity 100 事件存储在信道中的最大数量</p>

<p style="margin-left:0pt;">transactionCapacity 100 每个事务中的最大事件数</p>

<p style="margin-left:0pt;">keep-alive 3 添加或删除操作的超时时间</p>

<p style="margin-left:0pt;">byteCapacityBufferPercentage 20 Defines the percent of buffer between byteCapacity and the estimated total size of all events in the channel, to account for data in headers. See below.</p>

<p style="margin-left:0pt;">byteCapacity see description Maximum total bytes of memory allowed as a sum of all events in this channel. The implementation only counts the Event body, which is the reason for providing the byteCapacityBufferPercentage configuration parameter as well. Defaults to a computed value equal to 80% of the maximum memory available to the JVM (i.e. 80% of the -Xmx value passed on the command line). Note that if you have multiple memory channels on a single JVM, and they happen to hold the same physical events (i.e. if you are using a replicating channel selector from a single source) then those event sizes may be double-counted for channel byteCapacity purposes. Setting this value to 0 will cause this value to fall back to a hard internal limit of about 200 GB.</p>

<h2 style="margin-left:0pt;">2、​​​​​​​<strong><strong><strong>JDBC Channe</strong></strong></strong></h2>

<h3 style="margin-left:0pt;"><strong><strong><strong>l</strong></strong></strong><strong><strong><strong>JDBC Channel概述</strong></strong></strong></h3>

<p style="text-indent:50px;">事件被持久存储在可靠的数据库中。目前支持嵌入式的Derby数据库。如果可恢复性非常的重要可以使用这种方式。</p>

<h2>​​​​​​​3、<strong><strong><strong>File Channel</strong></strong></strong></h2>

<h3><strong><strong><strong>File Channel概述</strong></strong></strong></h3>

<p style="text-indent:50px;">性能会比较低下，但是即使程序出错数据不会丢失。</p>

<h3>​​​​​​​<strong><strong><strong>File Channel属性说明</strong></strong></strong></h3>

<p style="margin-left:0pt;">!type – 类型，必须是“file”</p>

<p style="margin-left:0pt;">checkpointDir ~/.flume/file-channel/checkpoint 检查点文件存放的位置</p>

<p style="margin-left:0pt;">useDualCheckpoints false Backup the checkpoint. If this is set to true, backupCheckpointDir must be set</p>

<p style="margin-left:0pt;">backupCheckpointDir – The directory where the checkpoint is backed up to. This directory must not be the same as the data directories or the checkpoint directory</p>

<p style="margin-left:0pt;">dataDirs ~/.flume/file-channel/data 逗号分隔的目录列表，用以存放日志文件。使用单独的磁盘上的多个目录可以提高文件通道效率。</p>

<p style="margin-left:0pt;">transactionCapacity 10000 The maximum size of transaction supported by the channel</p>

<p style="margin-left:0pt;">checkpointInterval 30000 Amount of time (in millis) between checkpoints</p>

<p style="margin-left:0pt;">maxFileSize 2146435071 一个日志文件的最大尺寸</p>

<p style="margin-left:0pt;">minimumRequiredSpace 524288000 Minimum Required free space (in bytes). To avoid data corruption, File Channel stops accepting take/put requests when free space drops below this value</p>

<p style="margin-left:0pt;">capacity 1000000 Maximum capacity of the channel</p>

<p style="margin-left:0pt;">keep-alive 3 Amount of time (in sec) to wait for a put operation</p>

<p style="margin-left:0pt;">use-log-replay-v1 false Expert: Use old replay logic</p>

<p style="margin-left:0pt;">use-fast-replay false Expert: Replay without using queue</p>

<p style="margin-left:0pt;">checkpointOnClose true Controls if a checkpoint is created when the channel is closed. Creating a checkpoint on close speeds up subsequent startup of the file channel by avoiding replay.</p>

<p style="margin-left:0pt;">encryption.activeKey – Key name used to encrypt new data</p>

<p style="margin-left:0pt;">encryption.cipherProvider – Cipher provider type, supported types: AESCTRNOPADDING</p>

<p style="margin-left:0pt;">encryption.keyProvider – Key provider type, supported types: JCEKSFILE</p>

<p style="margin-left:0pt;">encryption.keyProvider.keyStoreFile – Path to the keystore file</p>

<p style="margin-left:0pt;">encrpytion.keyProvider.keyStorePasswordFile – Path to the keystore password file</p>

<p style="margin-left:0pt;">encryption.keyProvider.keys – List of all keys (e.g. history of the activeKey setting)</p>

<p style="margin-left:0pt;">encyption.keyProvider.keys.*.passwordFile – Path to the optional key password file</p>

<h2>3、​​​​​​​<strong><strong><strong>Spillable Memory Channel</strong></strong></strong></h2>

<h3><strong><strong><strong>Spillable Memory Channel概述</strong></strong></strong></h3>

<p style="text-indent:50px;">Spillable Memory Channel：内存溢出通道。事件被存储在内存队列和磁盘中，内存队列作为主存储，而磁盘作为溢出内容的存储。内存存储通过embedded File channel来进行管理，当内存队列已满时，后续的事件将被存储在文件通道中，这个通道适用于正常操作期间适用内存通道已期实现高效吞吐，而在高峰期间适用文件通道实现高耐受性。通过降低吞吐效率提高系统可耐受性。如果Agent崩溃，则只有存储在文件系统中的事件可以被恢复；此通道处于试验阶段，不建议在生产环境中使用。</p>

<h3>​​​​​​​<strong><strong><strong>Spillable Memory Channel属性说明</strong></strong></strong></h3>

<p style="margin-left:0pt;">!type – 类型，必须是"SPILLABLEMEMORY"</p>

<p style="margin-left:0pt;">memoryCapacity 10000 内存中存储事件的最大值，如果想要禁用内存缓冲区将此值设置为0。</p>

<p style="margin-left:0pt;">overflowCapacity 100000000 可以存储在磁盘中的事件数量最大值。设置为0可以禁用磁盘存储。</p>

<p style="margin-left:0pt;">overflowTimeout 3 在内存填充磁盘溢出之前等待的秒数。</p>

<p style="margin-left:0pt;">byteCapacityBufferPercentage 20 Defines the percent of buffer between byteCapacity and the estimated total size of all events in the channel, to account for data in headers. See below.</p>

<p style="margin-left:0pt;">byteCapacity see description Maximum bytes of memory allowed as a sum of all events in the memory queue. The implementation only counts the Event body, which is the reason for providing the byteCapacityBufferPercentage configuration parameter as well. Defaults to a computed value equal to 80% of the maximum memory available to the JVM (i.e. 80% of the -Xmx value passed on the command line). Note that if you have multiple memory channels on a single JVM, and they happen to hold the same physical events (i.e. if you are using a replicating channel selector from a single source) then those event sizes may be double-counted for channel byteCapacity purposes. Setting this value to 0 will cause this value to fall back to a hard internal limit of about 200 GB.</p>

<p style="margin-left:0pt;">avgEventSize 500 Estimated average size of events, in bytes, going into the channel</p>

<p style="margin-left:0pt;">&lt;file channel properties&gt; see file channel Any file channel property with the exception of ‘keep-alive’ and ‘capacity’ can be used. The keep-alive of file channel is managed by Spillable Memory Channel. Use ‘overflowCapacity’ to set the File channel’s capacity.</p>

<h2 style="margin-left:0pt;">​​​​​​​4、<strong><strong><strong>自定义通道</strong></strong></strong></h2>

<h3 style="margin-left:0pt;"><strong><strong><strong>自定义通道概述</strong></strong></strong></h3>

<p style="text-indent:50px;">自定义通道需要自己实现Channel接口。自定义Channle类及其依赖类必须在Flume启动前放置到类加载的目录下。</p>

<h3>​​​​​​​<strong><strong><strong>自定义通道属性说明</strong></strong></strong></h3>

<p style="margin-left:0pt;">type - 自己实现的Channle类的全路径名称</p>

<p style="margin-left:0pt;"> </p>

<h1 style="margin-left:0pt;"><strong><strong><strong>Sink详解</strong></strong></strong></h1>

<h2>​​​​​​​1、<strong><strong><strong>Logger Sink</strong></strong></strong>​​​​​​​</h2>

<h3><strong><strong><strong>Logger Sink概述</strong></strong></strong></h3>

<p style="margin-left:0pt;">记录INFO级别的日志，通常用于调试。</p>

<h3>​​​​​​​<strong><strong><strong>Logger Sink属性说明</strong></strong></strong></h3>

<p style="margin-left:0pt;">!channel –</p>

<p style="margin-left:0pt;">!type – The component type name, needs to be logger</p>

<p style="margin-left:0pt;">maxBytesToLog 16 Maximum number of bytes of the Event body to log</p>

<p style="margin-left:0pt;">要求必须在 --conf 参数指定的目录下有 log4j的配置文件，也可以通过-Dflume.root.logger=INFO,console在命令启动时手动指定log4j参数</p>

<h2>2、​​​​​​​<strong><strong><strong>File Roll Sink</strong></strong></strong></h2>

<h3><strong><strong><strong>File Roll Sink概述</strong></strong></strong></h3>

<p style="margin-left:0pt;">在本地文件系统中存储事件，每隔指定时长生成文件保存这段时间内收集到的日志信息。</p>

<h3>​​​​​​​<strong><strong><strong>File Roll Sink属性说明</strong></strong></strong></h3>

<p style="margin-left:0pt;">!channel –  </p>

<p style="margin-left:0pt;">!type – 类型，必须是"file_roll"</p>

<p style="margin-left:0pt;">!sink.directory – 文件被存储的目录</p>

<p style="margin-left:0pt;">sink.rollInterval 30 滚动文件每隔30秒（应该是每隔30秒钟单独切割数据到一个文件的意思）。如果设置为0，则禁止滚动，从而导致所有数据被写入到一个文件中。</p>

<p style="margin-left:0pt;">sink.serializer TEXT Other possible options include avro_event or the FQCN of an implementation of EventSerializer.Builder interface.</p>

<p style="margin-left:0pt;">batchSize 100</p>

<h2>​​​​​​​3、<strong><strong><strong>Avro Sink</strong></strong></strong></h2>

<h3><strong><strong><strong>Avro Sink概述</strong></strong></strong></h3>

<p style="margin-left:0pt;">Avro Sink是实现多级流动、扇出流(1到多) 和 扇入流(多到1) 的基础。</p>

<h3>​​​​​​​<strong><strong><strong>Avro Sink属性说明</strong></strong></strong></h3>

<p style="margin-left:0pt;">!channel –  </p>

<p style="margin-left:0pt;">!type – The component type name, needs to be avro.</p>

<p style="margin-left:0pt;">!hostname – The hostname or IP address to bind to.</p>

<p style="margin-left:0pt;">!port – The port # to listen on.</p>

<p style="margin-left:0pt;">batch-size 100 number of event to batch together for send.</p>

<p style="margin-left:0pt;">connect-timeout 20000 Amount of time (ms) to allow for the first (handshake) request.</p>

<p style="margin-left:0pt;">request-timeout 20000 Amount of time (ms) to allow for requests after the first.</p>

<p style="margin-left:0pt;">reset-connection-interval none Amount of time (s) before the connection to the next hop is reset. This will force the Avro Sink to reconnect to the next hop. This will allow the sink to connect to hosts behind a hardware load-balancer when news hosts are added without having to restart the agent.</p>

<p style="margin-left:0pt;">compression-type none This can be “none” or “deflate”. The compression-type must match the compression-type of matching AvroSource</p>

<p style="margin-left:0pt;">compression-level 6 The level of compression to compress event. 0 = no compression and 1-9 is compression. The higher the number the more compression</p>

<p style="margin-left:0pt;">ssl false Set to true to enable SSL for this AvroSink. When configuring SSL, you can optionally set a “truststore”, “truststore-password”, “truststore-type”, and specify whether to “trust-all-certs”.</p>

<p style="margin-left:0pt;">trust-all-certs false If this is set to true, SSL server certificates for remote servers (Avro Sources) will not be checked. This should NOT be used in production because it makes it easier for an attacker to execute a man-in-the-middle attack and “listen in” on the encrypted connection.</p>

<p style="margin-left:0pt;">truststore – The path to a custom Java truststore file. Flume uses the certificate authority information in this file to determine whether the remote Avro Source’s SSL authentication credentials should be trusted. If not specified, the default Java JSSE certificate authority files (typically “jssecacerts” or “cacerts” in the Oracle JRE) will be used.</p>

<p style="margin-left:0pt;">truststore-password – The password for the specified truststore.</p>

<p style="margin-left:0pt;">truststore-type JKS The type of the Java truststore. This can be “JKS” or other supported Java truststore type.</p>

<p style="margin-left:0pt;">exclude-protocols SSLv3 Space-separated list of SSL/TLS protocols to exclude. SSLv3 will always be excluded in addition to the protocols specified.</p>

<p style="margin-left:0pt;">maxIoWorkers 2 * the number of available processors in the machine The maximum number of I/O w</p>

<h2>​​​​​​​4、<strong><strong><strong>HDFS Sink</strong></strong></strong></h2>

<h3><strong><strong><strong>HDFS Sink概述</strong></strong></strong></h3>

<p style="text-indent:50px;">HDFS Sink将事件写入到Hadoop分布式文件系统HDFS中，目前支持创建文本文件和序列化文件。并且对这两种格式都支持压缩。这些文件可以按照指定的时间或数据量或事件的数量为基础进行分卷。它还通过类似时间戳或机器属性对数据进行 buckets/partitions 操作。 HDFS的目录路径可以包含将要由HDFS替换格式的转移序列用以生成存储事件的目录/文件名。</p>

<p style="text-indent:50px;">使用HDFS Sink要求hadoop必须已经安装好，以便Flume可以通过hadoop提供的jar包与HDFS进行通信。注意，此版本hadoop必须支持sync()调用。</p>

<h3>​​​​​​​<strong><strong><strong>HDFS Sink属性说明</strong></strong></strong></h3>

<p style="margin-left:0pt;">!channel –  </p>

<p style="margin-left:0pt;">!type – 类型名称，必须是“HDFS”</p>

<p style="margin-left:0pt;">!hdfs.path – HDFS 目录路径 (eg hdfs://namenode/flume/webdata/)</p>

<p style="margin-left:0pt;">hdfs.filePrefix FlumeData Flume在目录下创建文件的名称前缀</p>

<p style="margin-left:0pt;">hdfs.fileSuffix – 追加到文件的名称后缀 (eg .avro - 注: 日期时间不会自动添加)</p>

<p style="margin-left:0pt;">hdfs.inUsePrefix – Flume正在处理的文件所加的前缀</p>

<p style="margin-left:0pt;">hdfs.inUseSuffix .tmp Flume正在处理的文件所加的后缀</p>

<p style="margin-left:0pt;">hdfs.rollInterval 30 Number of seconds to wait before rolling current file (0 = never roll based on time interval)</p>

<p style="margin-left:0pt;">hdfs.rollSize 1024 File size to trigger roll, in bytes (0: never roll based on file size)</p>

<p style="margin-left:0pt;">hdfs.rollCount 10 Number of events written to file before it rolled (0 = never roll based on number of events)</p>

<p style="margin-left:0pt;">hdfs.idleTimeout 0 Timeout after which inactive files get closed (0 = disable automatic closing of idle files)</p>

<p style="margin-left:0pt;">hdfs.batchSize 100 number of events written to file before it is flushed to HDFS</p>

<p style="margin-left:0pt;">hdfs.codeC – Compression codec. one of following : gzip, bzip2, lzo, lzop, snappy</p>

<p style="margin-left:0pt;">hdfs.fileType SequenceFile File format: currently SequenceFile, DataStream or CompressedStream (1)DataStream will not compress output file and please don’t set codeC (2)CompressedStream requires set hdfs.codeC with an available codeC</p>

<p style="margin-left:0pt;">hdfs.maxOpenFiles 5000 Allow only this number of open files. If this number is exceeded, the oldest file is closed.</p>

<p style="margin-left:0pt;">hdfs.minBlockReplicas – Specify minimum number of replicas per HDFS block. If not specified, it comes from the default Hadoop config in the classpath.</p>

<p style="margin-left:0pt;">hdfs.writeFormat – Format for sequence file records. One of “Text” or “Writable” (the default).</p>

<p style="margin-left:0pt;">hdfs.callTimeout 10000 Number of milliseconds allowed for HDFS operations, such as open, write, flush, close. This number should be increased if many HDFS timeout operations are occurring.</p>

<p style="margin-left:0pt;">hdfs.threadsPoolSize 10 Number of threads per HDFS sink for HDFS IO ops (open, write, etc.)</p>

<p style="margin-left:0pt;">hdfs.rollTimerPoolSize 1 Number of threads per HDFS sink for scheduling timed file rolling</p>

<p style="margin-left:0pt;">hdfs.kerberosPrincipal – Kerberos user principal for accessing secure HDFS</p>

<p style="margin-left:0pt;">hdfs.kerberosKeytab – Kerberos keytab for accessing secure HDFS</p>

<p style="margin-left:0pt;">hdfs.proxyUser    </p>

<p style="margin-left:0pt;">hdfs.round false 时间戳是否向下取整（如果是true，会影响所有基于时间的转移序列，除了%T）</p>

<p style="margin-left:0pt;">hdfs.roundValue 1 舍值的边界值</p>

<p style="margin-left:0pt;">hdfs.roundUnit 向下舍值的单位 -  second, minute , hour</p>

<p style="margin-left:0pt;">hdfs.timeZone Local Time Name of the timezone that should be used for resolving the directory path, e.g. America/Los_Angeles.</p>

<p style="margin-left:0pt;">hdfs.useLocalTimeStamp false Use the local time (instead of the timestamp from the event header) while replacing the escape sequences.</p>

<p style="margin-left:0pt;">hdfs.closeTries 0 Number of times the sink must try renaming a file, after initiating a close attempt. If set to 1, this sink will not re-try a failed rename (due to, for example, NameNode or DataNode failure), and may leave the file in an open state with a .tmp extension. If set to 0, the sink will try to rename the file until the file is eventually renamed (there is no limit on the number of times it would try). The file may still remain open if the close call fails but the data will be intact and in this case, the file will be closed only after a Flume restart.</p>

<p style="margin-left:0pt;">hdfs.retryInterval 180 Time in seconds between consecutive attempts to close a file. Each close call costs multiple RPC round-trips to the Namenode, so setting this too low can cause a lot of load on the name node. If set to 0 or less, the sink will not attempt to close the file if the first attempt fails, and may leave the file open or with a ”.tmp” extension.</p>

<p style="margin-left:0pt;">serializer TEXT Other possible options include avro_event or the fully-qualified class name of an implementation of the EventSerializer.Builder interface.</p>

<h2>​​​​​​​5、<strong><strong><strong>Hive Sink</strong></strong></strong></h2>

<h3><strong><strong><strong>Hive Sink概述</strong></strong></strong></h3>

<p style="text-indent:50px;">这个Sink可以将含分隔符的文本或JSON数据事件直接导入Hive的表或分区。事件Event是使用Hive transactions编写的，当一组Event被提交到Hive中，它们立即可以通过Hive被查询出来。Flume要写入数据的分区即可以预先创建好，也可以在缺失时由Flume来创建。Flume收到的数据字段将映射到Hive表的列上。此功能是一个预览功能，不推荐在生产环境下使用。</p>

<h3>​​​​​​​<strong><strong><strong>Hive Sink属性说明</strong></strong></strong></h3>

<p style="margin-left:0pt;">！channel –  </p>

<p style="margin-left:0pt;">！type – 类型，必须设置为“hive”</p>

<p style="margin-left:0pt;">！hive.metastore – Hive metastore URI (eg thrift://a.b.com:9083 )</p>

<p style="margin-left:0pt;">！hive.database – hive库名称</p>

<p style="margin-left:0pt;">！hive.table – hive表名称</p>

<p style="margin-left:0pt;">hive.partition – 逗号分开的分区值确定写入分区的列表。可以包含转义序列。</p>

<p style="margin-left:0pt;">hive.txnsPerBatchAsk 100 Hive grants a batch of transactions instead of single transactions to streaming clients like Flume. This setting configures the number of desired transactions per Transaction Batch. Data from all transactions in a single batch end up in a single file. Flume will write a maximum of batchSize events in each transaction in the batch. This setting in conjunction with batchSize provides control over the size of each file. Note that eventually Hive will transparently compact these files into larger files.</p>

<p style="margin-left:0pt;">heartBeatInterval 240 (In seconds) Interval between consecutive heartbeats sent to Hive to keep unused transactions from expiring. Set this value to 0 to disable heartbeats.</p>

<p style="margin-left:0pt;">autoCreatePartitions true Flume will automatically create the necessary Hive partitions to stream to</p>

<p style="margin-left:0pt;">batchSize 15000 Max number of events written to Hive in a single Hive transaction</p>

<p style="margin-left:0pt;">maxOpenConnections 500 Allow only this number of open connections. If this number is exceeded, the least recently used connection is closed.</p>

<p style="margin-left:0pt;">callTimeout 10000 (In milliseconds) Timeout for Hive &amp; HDFS I/O operations, such as openTxn, write, commit, abort.</p>

<p style="margin-left:0pt;">serializer   Serializer is responsible for parsing out field from the event and mapping them to columns in the hive table. Choice of serializer depends upon the format of the data in the event. Supported serializers: DELIMITED and JSON</p>

<p style="margin-left:0pt;">roundUnit minute The unit of the round down value - second, minute or hour.</p>

<p style="margin-left:0pt;">roundValue 1 Rounded down to the highest multiple of this (in the unit configured using hive.roundUnit), less than current time</p>

<p style="margin-left:0pt;">timeZone Local Time Name of the timezone that should be used for resolving the escape sequences in partition, e.g. America/Los_Angeles.</p>

<p style="margin-left:0pt;">useLocalTimeStamp false Use the local time (instead of the timestamp from the event header) while replacing the escape sequences.</p>

<h2>​​​​​​​6、<strong><strong><strong>Custom Sink</strong></strong></strong></h2>

<h3><strong><strong><strong>Custom Sink概述</strong></strong></strong></h3>

<p style="text-indent:50px;">自定义接收器，是自己实现的接收器接口Sink来实现的。自定义接收器的类及其依赖类须在Flume启动前放置到Flume类加载目录下。</p>

<h3>​​​​​​​<strong><strong><strong>Custom Sink属性说明</strong></strong></strong></h3>

<p style="margin-left:0pt;">type – 类型，需要指定为自己实现的Sink类的全路径名。</p>

<p style="text-indent:50px;">以上就是flume的概述及原理简单介绍，后续还会更新flume实战篇，结合HDFS和HIVE实现数据离线分析。如有疑问和建议欢迎留言。</p>

<hr><p style="margin-left:0pt;">版权所有，转载请说明转载地址。</p>

<p> </p>            </div>
                </div>