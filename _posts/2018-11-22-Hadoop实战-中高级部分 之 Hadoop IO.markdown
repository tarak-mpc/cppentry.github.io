---
layout:     post
title:      Hadoop实战-中高级部分 之 Hadoop IO
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p> </p>
<div style="font-size:14px;">
<div style="font-size:14px;">
<div><a href="http://sishuok.com/forum/blogPost/list/5833.html" rel="nofollow">Hadoop RestFul</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5936.html" rel="nofollow">Hadoop HDFS原理1</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5937.html" rel="nofollow">Hadoop HDFS原理2</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5938.html" rel="nofollow">Hadoop作业调优参数调整及原理</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5939.html" rel="nofollow">Hadoop HA</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5961.html" rel="nofollow">Hadoop MapReduce高级编程</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5963.html" rel="nofollow">Hadoop IO</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5965.html" rel="nofollow">Hadoop MapReduce工作原理</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5966.html" rel="nofollow">Hadoop 管理</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5969.html" rel="nofollow">Hadoop 集群安装</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5968.html" rel="nofollow">Hadoop RPC</a></div>
<div> </div>
</div>
<div style="font-size:14px;"><span class="bold" style="font-weight:bold;">第一部分：数据完整性</span></div>
<div style="font-size:14px;">
<div class="O"><span class="bold" style="font-weight:bold;">数据完整性及其采用的技术</span></div>
<div>
<div class="O">保证数据在传输过程中不损坏 ，常见的保证数据完整性采用的技术</div>
<div class="O1">A.奇偶校验技术</div>
<div class="O1">B.ECC校验纠错技术</div>
<div class="O1">C.CRC-32循环冗余校验技术</div>
<div class="O">
<div class="O">
<span class="bold" style="font-weight:bold;"><span lang="en-us" xml:lang="en-us">HDFS</span><span lang="en-us" xml:lang="en-us">的数据完整性</span></span> <span lang="en-us" xml:lang="en-us">  </span>
</div>
<div class="O">
<div>          HDFS以透明方式校验所有写入它的数据，并在默认设置下，会在读取数据时验证校验和。针对数据的每个io.bytes.per.checksum(默认512字节)字节，都会创建一个单独的校验和。</div>
<div>             数据节点负责在存储数据及其校验和之前验证它们收到的数据。 从客户端和其它数据节点复制过来的数据。客户端写入数据并且将它发送到一个数据节点管线中，在管线的最后一个数据节点验证校验和。</div>
<div>              客户端读取数据节点上的数据时，会验证校验和，将其与数据节点上存储的校验和进行对比。每个数据节点维护一个连续的校验和验证日志，因此它知道每个数据块最后验证的时间。每个数据节点还会在后台线程运行一个DataBlockScanner（数据块检测程序），定期验证存储在数据节点上的所有块，为了防止物理存储介质中位衰减锁造成的数据损坏。</div>
<div>          HDFS通过复制完整的副本来产生一个新的，无错的副本来“治愈”哪些出错的数据块。工作方式：如果客户端读取数据块时检测到错误，抛出Checksum Exception前报告该坏块以及它试图从名称节点中药读取的数据节点。名称节点将这个块标记为损坏的，不会直接复制给客户端或复制该副本到另一个数据 节点。它会从其他副本复制一个新的副本。</div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;" lang="en-us" xml:lang="en-us">本地文件系统</span></div>
<div class="O">
<div>            Hadoop的本地文件系统执行客户端校验。意味着，在写一个名filename的文件时，文件系统的客户端以透明的方式创建一个隐藏.filename.crc。在同一个文件夹下，包含每个文件块的校验和。       </div>
<div> </div>
<div>         数据块大小由io.bytes.per.checksum属性控制，块的大小作为元数据存储在.crc文件中。也可能禁用校验和：底层文件系统原生支持校验和。这里通过 RawLocalFileSystem来替代LocalFileSystem完成。要在一个应用中全局使用，只需要设置fs.file.impl值为 org.apache.hadoop.fs.RawLocalFileSystem来重新map执行文件的URL。或者只想对某些读取禁用校验和校验。例子：</div>
<div>Configuration conf = ...</div>
<div>FileSystem fs = new RawLocalFileSystem();</div>
<div>fs.initialize(null, conf)；</div>
<div>
<div class="O"><span lang="en-us" xml:lang="en-us"><strong>ChecksumFileSystem</strong></span></div>
<div class="O">
<div>LocalFileSystem使用ChecksumFileSystem(校验和文件系统)为自己工作，这个类可以很容易添加校验和功能到其他文件系统中。因为ChecksumFileSystem也包含于文件系统中。</div>
<div>
<div class="O">
<div><span class="bold" style="font-weight:bold;">第二部分：压缩</span></div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;">编码/解码</span></div>
</div>
<div>
<div class="O">
<div>编码/解码器：用以执行压缩解压算法。</div>
<div>•DEFLATE   org.apache.hadoop.io.compress.DefaultCodec</div>
<div>•gzip   org.apache.hadoop.io.compress.GzipCodec</div>
<div>•bzip2  org.apache.hadoop.io.compress.Bzip2Codec</div>
<div>•LZO  com.hadoop.compression.lzo.LzopCodec<em></em>
</div>
<div> </div>
</div>
<div class="O">
<div>•CompressionCodec 对流进行进行压缩与解压缩</div>
<div>•CompressionCodecFactory 方法来推断CompressionCodec</div>
<div> </div>
</div>
 
<div class="O"><span class="bold" style="font-weight:bold;">Hadoop支持的压缩形式</span></div>
</div>
</div>
<table style="width:516px;" dir="ltr" border="0" cellspacing="0" cellpadding="0"><tbody><tr><td style="font-size:14px;" width="87" height="43"><strong>压缩格式</strong></td>
<td style="font-size:14px;" width="87" height="43"><strong>工具</strong></td>
<td style="font-size:14px;" width="87" height="43"><strong>算法</strong></td>
<td style="font-size:14px;" width="87" height="43">  <strong>文件扩展名</strong>
</td>
<td style="font-size:14px;" width="87" height="43">  <strong>多文件</strong>
</td>
<td style="font-size:14px;" width="87" height="43"><strong>可分割性</strong></td>
</tr><tr><td style="font-size:14px;" width="87" height="43"> DEFLATE</td>
<td style="font-size:14px;" width="87" height="43"> 无</td>
<td style="font-size:14px;" width="87" height="43"> DEFLATE</td>
<td style="font-size:14px;" width="87" height="43"> .deflate</td>
<td style="font-size:14px;" width="87" height="43"> 不</td>
<td style="font-size:14px;" width="87" height="43"> 不</td>
</tr><tr><td style="font-size:14px;" width="87" height="43"> gzip</td>
<td style="font-size:14px;" width="87" height="43"> gzip</td>
<td style="font-size:14px;" width="87" height="43"> DEFLATE</td>
<td style="font-size:14px;" width="87" height="43"> .gz</td>
<td style="font-size:14px;" width="87" height="43"> 不</td>
<td style="font-size:14px;" width="87" height="43"> 不</td>
</tr><tr><td style="font-size:14px;" width="87" height="43"> bzip2</td>
<td style="font-size:14px;" width="87" height="43"> bzip2</td>
<td style="font-size:14px;" width="87" height="43"> bzip2</td>
<td style="font-size:14px;" width="87" height="43"> .bz2</td>
<td style="font-size:14px;" width="87" height="43"> 不</td>
<td style="font-size:14px;" width="87" height="43"> 是</td>
</tr><tr><td style="font-size:14px;" width="87" height="43"> LZO</td>
<td style="font-size:14px;" width="87" height="43"> lzop</td>
<td style="font-size:14px;" width="87" height="43"> LZO</td>
<td style="font-size:14px;" width="87" height="43"> .lzo</td>
<td style="font-size:14px;" width="87" height="43"> 不</td>
<td style="font-size:14px;" width="87" height="43"> 不</td>
</tr></tbody></table></div>
<div>
<div class="O">
<div>•属性名:</div>
<div>      io.compression.codecs</div>
<div>      默认值： org.apache.hadoop.io.compress.DefaultCodec,org.apache.hadoop.io.compress.GzipCodec,org.apache.hadoop.io.ompress.Bzip2Codec</div>
<div>•本地库</div>
<div>
<table style="width:370px;" dir="ltr" border="0" cellspacing="0" cellpadding="0"><tbody><tr><td style="font-size:14px;" width="124" height="23"><strong>压缩格式</strong></td>
<td style="font-size:14px;" width="124" height="23">
<strong>Java</strong> <strong>实现</strong>
</td>
<td style="font-size:14px;" width="124" height="23"><strong>本地实现</strong></td>
</tr><tr><td style="font-size:14px;" width="124" height="23">DEFLATE</td>
<td style="font-size:14px;" width="124" height="23">是</td>
<td style="font-size:14px;" width="124" height="23">是</td>
</tr><tr><td style="font-size:14px;" width="124" height="34">Gzip</td>
<td style="font-size:14px;" width="124" height="34">是</td>
<td style="font-size:14px;" width="124" height="34">是</td>
</tr><tr><td style="font-size:14px;" width="124" height="40">Bzip2</td>
<td style="font-size:14px;" width="124" height="40">是</td>
<td style="font-size:14px;" width="124" height="40">
<div>否</div>
<div> </div>
</td>
</tr><tr><td style="font-size:14px;" width="124" height="23">LZO</td>
<td style="font-size:14px;" width="124" height="23">否</td>
<td style="font-size:14px;" width="124" height="23">是</td>
</tr></tbody></table></div>
<div> </div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;">压缩与输入分割</span></div>
<div>
<div class="O">•前提：</div>
<div class="O">          在考虑如何压缩那些将由MapReduce处理的数据时，考虑压缩格式是否支持分割是很重要的。</div>
<div class="O">• 案例</div>
<div class="O1">•假设，一个文件时一个gzip格式的压缩文件，压缩后的大小为1GB。HDFS将其分为16块。然而针对每一块在进行分块是不可以的，因为gzip合适的文件不支持分割（分块）机制，所以读取他的MapReduce不分割文件，造成了只有Map读取16块文件的情况。导致运行时间变长。</div>
<div class="O">
<div>
<div class="O">•应该选择哪种压缩形式</div>
<div class="O1">•总体原则，还要经过测试，才可以决定。</div>
<div class="O1">•经验：大文件选择支持分割的压缩形式</div>
<div class="O"> </div>
</div>
 
<div class="O"><span class="bold" style="font-weight:bold;">在<span lang="en-us" xml:lang="en-us">MR </span>中使用压缩</span></div>
</div>
<div class="O">
<div class="O">•前提：</div>
<div class="O">         如果文件是压缩过的，那么在被MapReduce读取时，它们会被解压，根据文件的扩展名来选择应该使用拿一种压缩解码器。</div>
<div class="O">•使用：</div>
<div class="O1">•压缩MapReduce的作业输出，在作业配置中将 mapred.output.compress属性设置为true,将mapred.output.compression.codec属性设置为自己需要使用的压缩解码/编码器的类名。</div>
<div class="O1">•通过gunzip –c file来查看结果。</div>
</div>
</div>
</div>
</div>
</div>
<div> 
<div>
<div class="O2">Ø代码示例</div>
<div class="O2">conf.setBoolean(“mapred.output.compress’,true)</div>
<div class="O2">Conf.setClass(“mapred.output.compression.codec”,GizpCodec.class,</div>
<div class="O2">CompressionCodec.class);</div>
<div class="O"> </div>
<div class="O">
<div class="O">•Map作业输出结果的压缩</div>
<div class="O1">•使用原因</div>
<div class="O1">          因为Map作业的中间结果会输出到本地，并在网络上传递。所以压缩能获得更好性能，因为传播的数据减少了。</div>
<div class="O1">•Map输出压缩属性</div>
<div class="O2">•mapred.compress.map.output</div>
<div class="O2">•mapred.map.output</div>
<div class="O2">•compression.codec</div>
<div class="O1">•代码示例</div>
<div class="O2">•conf.setCompressMapOutput</div>
<div class="O2">•conf.setMapOutputCompressorClass(GzipCodec.classs)</div>
<div class="O2">
<br><div class="O"><span class="bold" style="font-weight:bold;">第三部分：序列化</span></div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;">什么是Hadoop的序列化</span></div>
<div>
<div class="O">•序列化（serialization）</div>
<div class="O">          序列化指的是将结构化对象转为字节流以便于通过网络进行传输或写入持久存储的过程。反序列化指的是将字节流转为一系列结构化对象的过程。</div>
<div class="O">          序列化用于：进程间通信与持久存储。</div>
<div class="O">    RPC序列化建议的特性</div>
<div class="O1">1.紧凑(Compact)即方便网络传输,充分利用存储空间</div>
<div class="O1">2.快速（Fast)即序列化及反序列化性能要好</div>
<div class="O1">3.扩展性(Extensible)即协议有变化，可以支持新的需求</div>
<div class="O1">4.互操作性（Interoperable）即客户端及服务器端不依赖语言的实现</div>
<div class="O">  Hadoop使用Writables,满足紧凑、快速，不满足扩展能及互操作性</div>
<div class="O">
<div class="O">
<div>•Hadoop的序列化不是java的序列化，Hadoop自己实现了自己的序列化机制。格式Writables。</div>
<div>•Hadoop中定义了两个序列化相关的接口：Writable接口和Comparable接口，这两个接口可以合成一个接口WritableComparable.</div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;"><span lang="en-us" xml:lang="en-us">Writable</span>接口</span></div>
<div class="O">
<div>
<span lang="en-us" xml:lang="en-us">Writable</span> 接口</div>
<div>
<span lang="en-us" xml:lang="en-us">Writable</span> 接口定义了两个方法：</div>
<div>（1）一个用于将其状态写入二进制格式的 <span lang="en-us" xml:lang="en-us">DataOutput</span> 流；</div>
<div>（2）另一个用于从二进制格式的 <span lang="en-us" xml:lang="en-us">DataInput</span> 流读取其状态；<br></div>
<div>我们可以使用 <span lang="en-us" xml:lang="en-us">set()</span> 函数来创建和设置 <span lang="en-us" xml:lang="en-us">Writable</span> 的值：</div>
<div><span lang="en-us" xml:lang="en-us">IntWritable wirtable = new IntWritable();</span></div>
<div><span lang="en-us" xml:lang="en-us">writable.set(163);</span></div>
<div>同样我们也可以使用构造函数：</div>
<div><span lang="en-us" xml:lang="en-us">IntWritable writable = new IntWritable(163);</span></div>
<div>
<div class="O">
<div><span lang="en-us" xml:lang="en-us">package org.apache.hadoop.io;</span></div>
<div><span lang="en-us" xml:lang="en-us">import java.io.DataOutput;</span></div>
<div><span lang="en-us" xml:lang="en-us">import java.io.DataInput;</span></div>
<div><span lang="en-us" xml:lang="en-us">import java.io.IOException;</span></div>
<div><span lang="en-us" xml:lang="en-us">public interface Writable { </span></div>
<div><span lang="en-us" xml:lang="en-us">  void write(DataOutput out) throws IOException; </span></div>
<div><span lang="en-us" xml:lang="en-us">  void readFields(DataInput in) throws IOException;}</span></div>
<div> </div>
<div>
<div class="O">
<div>
<span lang="en-us" xml:lang="en-us">Writable</span> 接口</div>
<div>
<span lang="en-us" xml:lang="en-us">Writable</span> 接口定义了两个方法：</div>
<div>（1）一个用于将其状态写入二进制格式的 <span lang="en-us" xml:lang="en-us">DataOutput</span> 流；</div>
<div>（2）另一个用于从二进制格式的 <span lang="en-us" xml:lang="en-us">DataInput</span> 流读取其状态；<br></div>
<div>我们可以使用 <span lang="en-us" xml:lang="en-us">set()</span> 函数来创建和设置 <span lang="en-us" xml:lang="en-us">Writable</span> 的值：</div>
<div><span lang="en-us" xml:lang="en-us">IntWritable wirtable = new IntWritable();</span></div>
<div><span lang="en-us" xml:lang="en-us">writable.set(163);</span></div>
<div>同样我们也可以使用构造函数：</div>
<div><span lang="en-us" xml:lang="en-us">IntWritable writable = new IntWritable(163);</span></div>
<div> </div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;"><span lang="en-us" xml:lang="en-us">WritableComparable </span>与<span lang="en-us" xml:lang="en-us"> comparator</span></span></div>
<div class="O">
<div class="O">
<span lang="en-us" xml:lang="en-us">         IntWritable</span> 实现了 <span lang="en-us" xml:lang="en-us">WritableComparable </span>接口 ，后者是 <span lang="en-us" xml:lang="en-us">Writable</span> 与<span lang="en-us" xml:lang="en-us">java.lang.Comprable</span> 接口的子接口</div>
<div class="O"> </div>
<div class="O">
<span lang="en-us" xml:lang="en-us">    </span><span lang="en-us" xml:lang="en-us">package org.apache.hadoop.io;</span>
</div>
<div class="O"><span lang="en-us" xml:lang="en-us">     public interface       WritableComparable&lt;T&gt; extends Writable,Comparable&lt;T&gt; {}</span></div>
<div class="O"> </div>
<div class="O"> </div>
<div class="O">
<span lang="en-us" xml:lang="en-us">        </span><span lang="en-us" xml:lang="en-us">Hadoop</span> 优化比对，不需要反序列化即可比较。</div>
<div class="O1">
<span lang="en-us" xml:lang="en-us"><strong>package</strong></span> <span lang="en-us" xml:lang="en-us"> </span> <span lang="en-us" xml:lang="en-us">org.apache.hadoop.io;</span> <span lang="en-us" xml:lang="en-us">  </span>
</div>
<div class="O1">
<span lang="en-us" xml:lang="en-us"><strong>import</strong></span> <span lang="en-us" xml:lang="en-us"> </span> <span lang="en-us" xml:lang="en-us">java.util.Comparator;</span> <span lang="en-us" xml:lang="en-us">  </span>
</div>
<div class="O1">
<span lang="en-us" xml:lang="en-us"><strong>public</strong></span> <span lang="en-us" xml:lang="en-us"> </span> <span lang="en-us" xml:lang="en-us"><strong>interface</strong></span> <span lang="en-us" xml:lang="en-us"> RawComparator&lt;T&gt; </span> <span lang="en-us" xml:lang="en-us"><strong>extends</strong></span> <span lang="en-us" xml:lang="en-us"> </span> <span lang="en-us" xml:lang="en-us">Comparator&lt;T&gt;</span> <span lang="en-us" xml:lang="en-us"> </span> <span lang="en-us" xml:lang="en-us">{</span> <span lang="en-us" xml:lang="en-us">  </span>
</div>
<div class="O1">
<span lang="en-us" xml:lang="en-us"><strong>    public</strong></span> <span lang="en-us" xml:lang="en-us"> </span> <span lang="en-us" xml:lang="en-us"><strong>int</strong></span> <span lang="en-us" xml:lang="en-us"> compare(</span> <span lang="en-us" xml:lang="en-us"><strong>byte</strong></span> <span lang="en-us" xml:lang="en-us">[] b1, </span> <span lang="en-us" xml:lang="en-us"><strong>int</strong></span> <span lang="en-us" xml:lang="en-us"> s1, </span> <span lang="en-us" xml:lang="en-us"><strong>int</strong></span> <span lang="en-us" xml:lang="en-us"> l1, </span> <span lang="en-us" xml:lang="en-us"><strong>byte</strong></span> <span lang="en-us" xml:lang="en-us">[] b2, </span> <span lang="en-us" xml:lang="en-us"><strong>int</strong></span><span lang="en-us" xml:lang="en-us"> s2, </span> <span lang="en-us" xml:lang="en-us"><strong>int</strong></span> <span lang="en-us" xml:lang="en-us"> </span> <span lang="en-us" xml:lang="en-us">l2);</span> <span lang="en-us" xml:lang="en-us">  </span>
</div>
<div class="O1">
<span lang="en-us" xml:lang="en-us">}</span> <span lang="en-us" xml:lang="en-us">  </span>
</div>
<div class="O"> </div>
<div class="O">
<div>
<div class="O">
<span lang="en-us" xml:lang="en-us">         WritableComparator </span>是一个 <span lang="en-us" xml:lang="en-us">RawComparator</span> 通用的实现 ，为<span lang="en-us" xml:lang="en-us">WritableComparable classes. </span>
</div>
<div class="O1"><span lang="en-us" xml:lang="en-us">  它做了两件事</span></div>
<div class="O1"> 1.实现了 <span lang="en-us" xml:lang="en-us">compare()</span> 方法(返序列化）</div>
<div class="O1"> 2.它充当的是 <span lang="en-us" xml:lang="en-us">RawComparator</span> 的工厂类</div>
<div class="O"> </div>
<div class="O"> </div>
</div>
<div class="O"><span class="bold" style="font-weight:bold;" lang="en-us" xml:lang="en-us">Hadoop自带的序列化接口（Writable类）</span></div>
<div class="O">
<div>
<span lang="en-us" xml:lang="en-us">Hadoop</span> 自带的序列化接口</div>
<div>实现了 <span lang="en-us" xml:lang="en-us">WritableComparable</span> 接口的类：</div>
<div>基础： <span lang="en-us" xml:lang="en-us">BooleanWritable | ByteWritable</span>
</div>
<div>数字： <span lang="en-us" xml:lang="en-us">IntWritable | VIntWritable | FloatWritable | LongWritable |</span><span lang="en-us" xml:lang="en-us">VLongWritable | DoubleWritable</span>
</div>
<div>高级： <span lang="en-us" xml:lang="en-us">NullWritable | Text | BytesWritable | MDSHash | ObjectWritable |</span><span lang="en-us" xml:lang="en-us">GenericWritable</span>
</div>
<div>仅实现了 <span lang="en-us" xml:lang="en-us">Writable</span> 接口的类：</div>
<div>数组： <span lang="en-us" xml:lang="en-us">ArrayWritable | TwoDArrayWritable</span>
</div>
<div>映射： <span lang="en-us" xml:lang="en-us">AbstractMapWritable | MapWritable | SortedMapWritable</span>
</div>
<div>
<div class="O">•Text</div>
<div class="O">        Text是UTF-8的Writable。可以将它理解为一种与java.lang.String 相类似的Writable。Text类代替了UTF-8类。</div>
<div class="O">         Text是可变的，其值可以通过调用set()方法来改变。最大存储是2GB。</div>
<div class="O">•NullWritable</div>
<div class="O1">NullWritable是一种特殊的Writable类型，因为它的序列化的长度是零。可以做占位符。</div>
<div class="O">•BytesWritable <br> BytesWritable 是一个二进制的数据数组封装。它的序列化格式是一个int字段.</div>
<div class="O1">BytesWritable是可变的，其值可以通过调用set()方法来改变。</div>
<div class="O1"> 
<div>
<div class="O">•ObjectWriable</div>
<div class="O1">ObjectWriable 适用于字段可以使用多种类型时。</div>
<div class="O">•Writable集合</div>
<div class="O">     一共 四种：</div>
<div class="O">            ArrayWritable和TwoDArrayWritable是针对数组与二维数组</div>
<div class="O">            MapWritable和SortededMapWritable 针对是Map与SortMap</div>
<div class="O1">
<div class="O"><span class="bold" style="font-weight:bold;">自定义Writable</span></div>
<div class="O">
<div>•实现WritableComparable</div>
<div>•实现</div>
<div>     /** * 将对象转换为字节流并写入到输出流out中 */</div>
<div>      write()</div>
<div>      /** * 从输入流in 中读取字节流并反序列化为对象 */</div>
<div>       readFields(),</div>
<div>      /** * 将this对像与对象O比较*/</div>
<div>      compareTo()方法。</div>
</div>
</div>
</div>
</div>
</div>
<div><span lang="en-us" xml:lang="en-us"><br></span></div>
<div>
<div class="O">
<div><span class="bold" style="font-weight:bold;">第四部分：基于文件的数据结构</span></div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;"><span lang="en-us" xml:lang="en-us">SequenceFile</span>类</span></div>
<div>
<div class="O">          SequeceFile是Hadoop API提供的一种二进制文件支持。这种二进制文件直接将&lt;key, value&gt;对序列化到文件中。</div>
<div class="O">         Key是任意的Writable，Value是任意的Writable</div>
<div class="O"> </div>
<div class="O">         这种文件格式 有以下好处：</div>
<div class="O1">A.支持压缩，且可定制为基于Record或Block压缩（Block级压缩性能较优）</div>
<div class="O1">B.本地化任务支持：因为文件可以被切分，因此MapReduce任务时数据的本地化情况应该是非常好的。</div>
<div class="O1">C.难度低：因为是Hadoop框架提供的API，业务逻辑侧的修改比较简单。</div>
<div class="O1"> 
<div class="O"><span class="bold" style="font-weight:bold;">写<span lang="en-us" xml:lang="en-us">SequenceFile</span></span></div>
</div>
<div class="O">
<div class="O">
<div>步骤：</div>
<div>
<span lang="en-us" xml:lang="en-us">      </span><span lang="en-us" xml:lang="en-us">1.</span> 设置 <span lang="en-us" xml:lang="en-us">Configuration</span>
</div>
<div>
<span lang="en-us" xml:lang="en-us">      2. </span>获取 <span lang="en-us" xml:lang="en-us">File System</span>
</div>
<div>
<span lang="en-us" xml:lang="en-us">      3. </span>设置文件输出路径</div>
<div>
<span lang="en-us" xml:lang="en-us">      4. </span><span lang="en-us" xml:lang="en-us">SequenceFile.createWriter </span>创建 <span lang="en-us" xml:lang="en-us">SequenceFile.Writer</span> 然后写入</div>
<div>
<span lang="en-us" xml:lang="en-us">      5.</span> 调用 <span lang="en-us" xml:lang="en-us">SequenceFile.Writer</span> <span lang="en-us" xml:lang="en-us">.append </span>追加写入</div>
<div>
<span lang="en-us" xml:lang="en-us">      6.</span> 关闭流</div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;">读<span lang="en-us" xml:lang="en-us">SequenceFile</span></span></div>
</div>
<div>
<div class="O">
<div>步骤：</div>
<div>
<span lang="en-us" xml:lang="en-us">      </span><span lang="en-us" xml:lang="en-us">1.</span> 设置 <span lang="en-us" xml:lang="en-us">Configuration</span>
</div>
<div>
<span lang="en-us" xml:lang="en-us">      2. </span>获取 <span lang="en-us" xml:lang="en-us">File System</span>
</div>
<div>
<span lang="en-us" xml:lang="en-us">      3. </span>设置文件输出路径</div>
<div>
<span lang="en-us" xml:lang="en-us">      4.</span> <span lang="en-us" xml:lang="en-us">SequenceFile.Reader </span>创建读取类 <span lang="en-us" xml:lang="en-us">SequenceFile.Reader</span>
</div>
<div>
<span lang="en-us" xml:lang="en-us">      5. </span>拿到 <span lang="en-us" xml:lang="en-us">Key</span> 与 <span lang="en-us" xml:lang="en-us">Value</span> 的 <span lang="en-us" xml:lang="en-us">class</span>
</div>
<div>
<span lang="en-us" xml:lang="en-us">      6. </span>读取</div>
<div> </div>
<div> 
<div class="O"><span class="bold" style="font-weight:bold;">在<span lang="en-us" xml:lang="en-us">SequenceFile</span>使用压缩</span></div>
</div>
<div>
<div class="O">
<div>写：压缩分为Record和Block两种。</div>
<div>读时自动解压。</div>
<div>步骤：</div>
<div>      增加如下代码</div>
<div>     SequenceFile.createWriter(fs, conf, path, key.getClass(), value.getClass(), SequenceFile.CompressionType.RECORD, new Bzip2Codec())</div>
<div> </div>
</div>
 </div>
</div>
 
<div class="O"><span class="bold" style="font-weight:bold;" lang="en-us" xml:lang="en-us">MapFile</span></div>
</div>
<div>
<div class="O">
<div>
<span lang="en-us" xml:lang="en-us">           MapFile</span> <span lang="en-us" xml:lang="en-us">是经过排序的带索引的</span> <span lang="en-us" xml:lang="en-us">SequenceFile</span> <span lang="en-us" xml:lang="en-us">，可以根据键值进行查找</span> <span lang="en-us" xml:lang="en-us">.</span>
</div>
<div>      由两部分组成，分别是 <span lang="en-us" xml:lang="en-us">data</span> 和 <span lang="en-us" xml:lang="en-us">index</span> 。 <span lang="en-us" xml:lang="en-us">index</span> 作为文件的数据索引，主要记录了每个 <span lang="en-us" xml:lang="en-us">Record</span> 的 <span lang="en-us" xml:lang="en-us">key</span> 值，以及该 <span lang="en-us" xml:lang="en-us">Record</span> 在文件中的偏移位置。在 <span lang="en-us" xml:lang="en-us">MapFile</span> 被访问的时候 <span lang="en-us" xml:lang="en-us">,</span> 索引 文件会被加载到内存，通过索引映射关系可迅速定位到指定<span lang="en-us" xml:lang="en-us">Record</span> 所在文件位置， 因此，相对 <span lang="en-us" xml:lang="en-us">SequenceFile</span> 而言， <span lang="en-us" xml:lang="en-us">MapFile</span> 的检索效率是高效的，缺点是会消耗一部分 内存来存储 <span lang="en-us" xml:lang="en-us">index</span> 数据 <span lang="en-us" xml:lang="en-us">.</span>
</div>
<div> </div>
<div> 
<div class="O"><span class="bold" style="font-weight:bold;">读<span lang="en-us" xml:lang="en-us">MapFile</span></span></div>
</div>
</div>
<div class="O">
<div>步骤：</div>
<div>
<span lang="en-us" xml:lang="en-us">      </span><span lang="en-us" xml:lang="en-us">1.</span> 设置 <span lang="en-us" xml:lang="en-us">Configuration</span>
</div>
<div>
<span lang="en-us" xml:lang="en-us">      2. </span>获取 <span lang="en-us" xml:lang="en-us">File System</span>
</div>
<div>
<span lang="en-us" xml:lang="en-us">      3. </span>设置文件输出路径</div>
<div>
<span lang="en-us" xml:lang="en-us">      4. MapFile</span> <span lang="en-us" xml:lang="en-us">Reader </span>创建读取类 <span lang="en-us" xml:lang="en-us">. MapFile</span> <span lang="en-us" xml:lang="en-us">Reader</span>
</div>
<div>
<span lang="en-us" xml:lang="en-us">      5. </span>拿到 <span lang="en-us" xml:lang="en-us">Key</span> 与 <span lang="en-us" xml:lang="en-us">Value</span> 的 <span lang="en-us" xml:lang="en-us">class</span>
</div>
<div>
<span lang="en-us" xml:lang="en-us">      6. </span>读取</div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;">写<span lang="en-us" xml:lang="en-us">MapFile</span></span></div>
</div>
<div>
<div class="O">
<div>步骤：</div>
<div>
<span lang="en-us" xml:lang="en-us">      </span><span lang="en-us" xml:lang="en-us">1.</span> 设置 <span lang="en-us" xml:lang="en-us">Configuration</span>
</div>
<div>
<span lang="en-us" xml:lang="en-us">      2. </span>获取 <span lang="en-us" xml:lang="en-us">File System</span>
</div>
<div>
<span lang="en-us" xml:lang="en-us">      3. </span>设置文件输出路径</div>
<div>
<span lang="en-us" xml:lang="en-us">      4. </span><span lang="en-us" xml:lang="en-us">MapFile</span> <span lang="en-us" xml:lang="en-us">.createWriter </span>创建 <span lang="en-us" xml:lang="en-us">MapFile</span> <span lang="en-us" xml:lang="en-us">.createWriter</span> 然后写入</div>
<div>
<span lang="en-us" xml:lang="en-us">      5.</span> 调用 <span lang="en-us" xml:lang="en-us">MapFile</span> <span lang="en-us" xml:lang="en-us">.Writer</span> <span lang="en-us" xml:lang="en-us">.append </span>追加写入</div>
<div>
<span lang="en-us" xml:lang="en-us">      6.</span> 关闭流</div>
<div> </div>
<div> </div>
</div>
  转载请注明出处【  <a href="http://sishuok.com/forum/blogPost/list/5963.html" rel="nofollow">http://sishuok.com/forum/blogPost/list/5963.html</a>】</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
<p> </p>            </div>
                </div>