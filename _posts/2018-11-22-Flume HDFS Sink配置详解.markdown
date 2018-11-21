---
layout:     post
title:      Flume HDFS Sink配置详解
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <table border="1" cellpadding="2" cellspacing="0"><thead><tr><th>Name</th>
			<th>Default</th>
			<th>Description</th>
		</tr></thead><tbody><tr><td>channel</td>
			<td>–</td>
			<td>
			<p> </p>
			</td>
		</tr><tr><td>type</td>
			<td>–</td>
			<td>组件的名称，必须为：HDFS</td>
		</tr><tr><td>hdfs.path</td>
			<td>–</td>
			<td>HDFS目录路径，例如：hdfs://namenode/flume/webdata/</td>
		</tr><tr><td>hdfs.filePrefix</td>
			<td>FlumeData</td>
			<td>HDFS目录中，由Flume创建的文件前缀。</td>
		</tr><tr><td>hdfs.fileSuffix</td>
			<td>–</td>
			<td>追加到文件的后缀，例如：.txt</td>
		</tr><tr><td>hdfs.inUsePrefix</td>
			<td>–</td>
			<td>文件正在写入时的前缀。</td>
		</tr><tr><td>hdfs.inUseSuffix</td>
			<td><code>.tmp</code></td>
			<td>文件正在写入时的后缀。</td>
		</tr><tr><td>hdfs.rollInterval</td>
			<td>30</td>
			<td>当前写入的文件滚动间隔，默认30秒生成一个新的文件 (0 = 不滚动)</td>
		</tr><tr><td>hdfs.rollSize</td>
			<td>1024</td>
			<td>以文件大小触发文件滚动，单位字节(0 = 不滚动)</td>
		</tr><tr><td>hdfs.rollCount</td>
			<td>10</td>
			<td>
			<pre>

 </pre>

			<pre>

 </pre>

			<pre>

 </pre>

			<pre>

 </pre>

			<pre>

 </pre>

			<pre>

 </pre>

			<pre>

 </pre>

			<pre>
以写入的事件数触发文件滚动。(0 = 不滚动)</pre>
			</td>
		</tr><tr><td>hdfs.idleTimeout</td>
			<td>0</td>
			<td>超时多久以后关闭无效的文件。(0 = 禁用自动关闭的空闲文件)但是还是可能因为网络等多种原因导致，正在写的文件始终没有关闭，从而产生tmp文件</td>
		</tr><tr><td>hdfs.batchSize</td>
			<td>100</td>
			<td>有多少Event后，写到文件才刷新到HDFS。</td>
		</tr><tr><td>hdfs.codeC</td>
			<td>–</td>
			<td>压缩编解码器，可以使用：gzip, bzip2, lzo, lzop, snappy</td>
		</tr><tr><td>hdfs.fileType</td>
			<td>SequenceFile</td>
			<td>文件格式：通常使用<code>SequenceFile（默认）</code>, <code>DataStream</code> 或者 <code>CompressedStream</code><br>
			(1)DataStream不能压缩输出文件，请不用设置hdfs.codeC编码解码器。<br>
			(2)CompressedStream要求设置hdfs.codeC来制定一个有效的编码解码器。</td>
		</tr><tr><td>hdfs.maxOpenFiles</td>
			<td>5000</td>
			<td>HDFS中允许打开文件的数据，如果数量超过了，最老的文件将被关闭。</td>
		</tr><tr><td>hdfs.callTimeout</td>
			<td>10000</td>
			<td>允许HDFS操作的毫秒数，例如：open，write, flush, close。如果很多HFDS操作超时，这个配置应该增大。</td>
		</tr><tr><td>hdfs.threadsPoolSize</td>
			<td>10</td>
			<td>
			<p>每个HDFS sink的HDFS的IO操作线程数（例如：open，write）</p>
			</td>
		</tr><tr><td>hdfs.rollTimerPoolSize</td>
			<td>1</td>
			<td>每个HDFS sink调度定时文件滚动的线程数。</td>
		</tr><tr><td>hdfs.kerberosPrincipal</td>
			<td>–</td>
			<td>安全访问HDFS Kerberos的主用户。</td>
		</tr><tr><td>hdfs.kerberosKeytab</td>
			<td>–</td>
			<td>安全访问HDFS Kerberos keytab</td>
		</tr><tr><td>hdfs.proxyUser</td>
			<td>
			<p> </p>
			</td>
			<td>
			<p> </p>
			</td>
		</tr><tr><td>hdfs.round</td>
			<td>false</td>
			<td>时间戳应该被四舍五入。(如果为true，会影响所有的时间，除了t%)</td>
		</tr><tr><td>hdfs.roundValue</td>
			<td>1</td>
			<td>四舍五入的最高倍数（单位配置在hdfs.roundUnit），但是要小于当前时间。</td>
		</tr><tr><td>hdfs.roundUnit</td>
			<td>second</td>
			<td>四舍五入的单位，包含：<code>second</code>, <code>minute</code> or <code>hour</code>.</td>
		</tr><tr><td>hdfs.timeZone</td>
			<td>Local Time</td>
			<td>时区的名称，主要用来解决目录路径。例如：America/Los_Angeles</td>
		</tr><tr><td>hdfs.useLocalTimeStamp</td>
			<td>false</td>
			<td>使用本地时间替换转义字符。 (而不是event header的时间戳)</td>
		</tr><tr><td>hdfs.closeTries</td>
			<td>0</td>
			<td>在发起一个关闭命令后，HDFS sink必须尝试重命名文件的次数。如果设置为1，重命名失败后，HDFS sink不会再次尝试重命名该文件，这个文件处于打开状态，并且用.tmp作为扩展名。如果为0，Sink会一直尝试重命名，直至重命名成功。如果文件 失败，这个文件可能一直保持打开状态，但是这种情况下数据是完整的。文件将会在Flume下次重启时被关闭。</td>
		</tr><tr><td>hdfs.retryInterval</td>
			<td>180</td>
			<td>在几秒钟之间连续尝试关闭文件。每个关闭请求都会有多个RPC往返Namenode，因此设置的太低可能导致Namenode超负荷，如果设置0或者更小，如果第一次尝试失败的话，该Sink将不会尝试关闭文件。并且把文件打开，或者用“.tmp”作为扩展名。</td>
		</tr><tr><td>serializer</td>
			<td><code>TEXT</code></td>
			<td>可能的选项包括avro_event或继承了EventSerializer.Builder接口的类名。</td>
		</tr><tr><td>serializer.*</td>
			<td>
			<p> </p>
			</td>
			<td>
			<p> </p>
			</td>
		</tr></tbody></table>            </div>
                </div>