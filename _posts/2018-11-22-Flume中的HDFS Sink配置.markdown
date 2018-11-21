---
layout:     post
title:      Flume中的HDFS Sink配置
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u012689336/article/details/52711413				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>Flume中的HDFS Sink配置参数说明</p>
<p><br></p>
<p>type：hdfs</p>
<p><span style="font-size:12px;">path：</span><span style="font-size:12px;">hdfs的路径，需要包含文件系统标识，比如：hdfs://namenode/flume/webdata/</span></p>
<p>filePrefix：默认值：FlumeData，写入hdfs的文件名前缀</p>
<p><span style="font-size:12px;">fileSuffix：</span><span style="font-size:12px;">写入 hdfs 的文件名后缀，比如：.lzo .log等。</span></p>
<p><span style="font-size:12px;">inUsePrefix：</span><span style="font-size:12px;">临时文件的文件名前缀，hdfs sink 会先往目标目录中写临时文件，再根据相关规则重命名成最终目标文件；</span></p>
<p><span style="font-size:12px;">inUseSuffix：</span><span style="font-size:12px;">默认值：.tmp，</span><span style="font-size:12px;">临时文件的文件名后缀。</span></p>
<p><span style="font-size:12px;">rollInterval：默认值：30：hdfs sink 间隔多长将临时文件滚动成最终目标文件，单位：秒；</span></p>
<p><span style="font-size:12px;">                       如果设置成0，则表示不根据时间来滚动文件；</span></p>
<p><span style="font-size:12px;">注：滚动（roll）指的是，hdfs sink 将临时文件重命名成最终目标文件，并新打开一个临时文件来写入数据；</span></p>
<p><span style="font-size:12px;">rollSize：</span><span style="font-size:12px;">默认值：1024：</span><span style="font-size:12px;">当临时文件达到多少（单位：bytes）时，滚动成目标文件；</span><span style="font-size:12px;">如果设置成0，则表示不根据临时文件大小来滚动文件；</span></p>
<p>rollCount：默认值：10：当 events 数据达到该数量时候，将临时文件滚动成目标文件；如果设置成0，则表示不根据events数据来滚动文件；<br></p>
<p>idleTimeout：默认值：0：当目前被打开的临时文件在该参数指定的时间（秒）内，没有任何数据写入，则将该临时文件关闭并重命名成目标文件；<br></p>
<p>batchSize：默认值：100：每个批次刷新到 HDFS 上的 events 数量；<br></p>
<p>codeC：文件压缩格式，包括：gzip, bzip2, lzo, lzop, snappy<br></p>
<p>fileType：默认值：SequenceFile，<span style="font-size:12px;">文件格式，包括：SequenceFile, DataStream,CompressedStream</span></p>
<p><span style="font-size:12px;">                                   当使用DataStream时候，文件不会被压缩，不需要设置hdfs.codeC;</span></p>
<p><span style="font-size:12px;">                                   当使用CompressedStream时候，必须设置一个正确的hdfs.codeC值；</span></p>
<p><span style="font-size:12px;">maxOpenFiles：默认值：5000：最大允许打开的HDFS文件数，当打开的文件数达到该值，最早打开的文件将会被关闭；<br></span></p>
<p><span style="font-size:12px;">minBlockReplicas：默认值：HDFS副本数，写入 HDFS 文件块的最小副本数。</span></p>
<p><span style="font-size:12px;">                                     该参数会影响文件的滚动配置，一般将该参数配置成1，才可以按照配置正确滚动文件。<br></span></p>
<p><span style="font-size:12px;">writeFormat：写 sequence 文件的格式。包含：Text, Writable（默认）</span></p>
<p><span style="font-size:12px;">callTimeout：默认值：10000，执行HDFS操作的超时时间（单位：毫秒）；<br></span></p>
<p><span style="font-size:12px;">threadsPoolSize：默认值：10，hdfs sink 启动的操作HDFS的线程数。<br></span></p>
<p><span style="font-size:12px;">rollTimerPoolSize：默认值：1，hdfs sink 启动的根据时间滚动文件的线程数。<br></span></p>
<p><span style="font-size:12px;">kerberosPrincipal：HDFS安全认证kerberos配置；</span></p>
<p><span style="font-size:12px;">kerberosKeytab：HDFS安全认证kerberos配置；</span></p>
<p><span style="font-size:12px;">proxyUser：代理用户<br></span></p>
<p><br></p>
<p>round：默认值：false，<span style="font-size:12px;">是否启用时间上的”舍弃”；</span></p>
<p><span style="font-size:12px;">roundValue：</span><span style="font-size:12px;">默认值：1，</span><span style="font-size:12px;">时间上进行“舍弃”的值；</span></p>
<p><span style="font-size:12px;">roundUnit：</span><span style="font-size:12px;">默认值：seconds，</span><span style="font-size:12px;">时间上进行”舍弃”的单位，包含：second,minute,hour</span></p>
<p><span style="font-size:12px;"><br></span></p>
<p><span style="font-size:12px;">示例：</span></p>
<p><span style="font-size:12px;">a1.sinks.k1.hdfs.path = /flume/events/%y-%m-%d/%H%M/%S</span></p>
<p><span style="font-size:12px;">a1.sinks.k1.hdfs.round = true</span></p>
<p><span style="font-size:12px;">a1.sinks.k1.hdfs.roundValue = 10</span></p>
<p><span style="font-size:12px;">a1.sinks.k1.hdfs.roundUnit = minute</span></p>
<p><span style="font-size:12px;">当时间为2015-10-16 17:38:59时候，hdfs.path依然会被解析为：/flume/events/20151016/17:30/00</span></p>
<p><span style="font-size:12px;">因为设置的是舍弃10分钟内的时间，因此，该目录每10分钟新生成一个。<br></span></p>
<p><span style="font-size:12px;"><br></span></p>
<p><span style="font-size:12px;">timeZone：默认值：Local Time，时区。<br></span></p>
<p><span style="font-size:12px;">useLocalTimeStamp：默认值：flase，是否使用当地时间。<br></span></p>
<p><span style="font-size:12px;">closeTries：默认值：0，hdfs sink 关闭文件的尝试次数；</span></p>
<p><span style="font-size:12px;">                      如果设置为1，当一次关闭文件失败后，hdfs sink将不会再次尝试关闭文件，这个未关闭的文件将会一直留在那，并且是打开状态。</span></p>
<p><span style="font-size:12px;">                      设置为0，当一次关闭失败后，hdfs sink会继续尝试下一次关闭，直到成功。<br></span></p>
<p><span style="font-size:12px;">retryInterval：默认值：180（秒），hdfs sink 尝试关闭文件的时间间隔，如果设置为0，表示不尝试，相当于于将hdfs.closeTries设置成1.<br></span></p>
<p><span style="font-size:12px;">serializer：默认值：TEXT，序列化类型。<br></span></p>
<p><br></p>
<p><br></p>
<p>比如：</p>
<p>agent1.sinks.sink1.type = hdfs</p>
<p><span style="font-size:13.3333px;">agent1</span>.sinks.sink1.hdfs.path = hdfs://cdh5/tmp/data/%Y%m%d</p>
<p><span style="font-size:13.3333px;">agent1</span>.sinks.sink1.hdfs.filePrefix = log_%Y%m%d_%H</p>
<p><span style="font-size:13.3333px;">agent1</span>.sinks.sink1.hdfs.fileSuffix = .lzo</p>
<p><span style="font-size:13.3333px;">agent1</span>.sinks.sink1.hdfs.useLocalTimeStamp = true</p>
<p><span style="font-size:13.3333px;">agent1</span>.sinks.sink1.hdfs.writeFormat = Text</p>
<p><span style="font-size:13.3333px;">agent1</span>.sinks.sink1.hdfs.fileType = CompressedStream</p>
<p><span style="font-size:13.3333px;">agent1</span>.sinks.sink1.hdfs.rollCount = 0</p>
<p><span style="font-size:13.3333px;">agent1</span>.sinks.sink1.hdfs.rollSize = 0</p>
<p><span style="font-size:13.3333px;">agent1</span>.sinks.sink1.hdfs.rollInterval = 600</p>
<p><span style="font-size:13.3333px;">agent1</span>.sinks.sink1.hdfs.codeC = lzop</p>
<p><span style="font-size:13.3333px;">agent1</span>.sinks.sink1.hdfs.batchSize = 100</p>
<p><span style="font-size:13.3333px;">agent1</span>.sinks.sink1.hdfs.threadsPoolSize = 10</p>
<p><span style="font-size:13.3333px;">agent1</span>.sinks.sink1.hdfs.idleTimeout = 0</p>
<p><span style="font-size:13.3333px;">agent1</span>.sinks.sink1.hdfs.minBlockReplicas = 1</p>
<p><br></p>
<p>上面的配置中，在 HDFS 的 /tmp/data/ 目录下，每天生成一个格式为20151016的目录，<br><br>
目标文件每10分钟生成一个，目标文件格式为：log_20151016_13.1444973768543.lzo<br><br>
目标文件采用lzo压缩。<br></p>
<p><br></p>
<p><br></p>
            </div>
                </div>