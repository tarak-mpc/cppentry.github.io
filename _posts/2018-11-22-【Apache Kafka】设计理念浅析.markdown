---
layout:     post
title:      【Apache Kafka】设计理念浅析
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:25px;color:rgb(51,51,51);">
本文将从以下两个方面去尝试讲解Kafka的设计理念：</p>
<ul style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:13px;line-height:18px;"><li style="line-height:18px;list-style-type:disc;">
<p style="font-size:14px;line-height:25px;">Kafka设计背景及原因</p>
</li><li style="line-height:18px;list-style-type:disc;">
<p style="font-size:14px;line-height:25px;">Kafka的设计特色</p>
</li></ul><h2 id="kafka" style="color:rgb(51,51,51);font-size:24px;line-height:36px;border-bottom-width:1px;border-bottom-style:dotted;border-bottom-color:rgb(170,170,170);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;">
Kafka设计背景及原因</h2>
<p style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:25px;color:rgb(51,51,51);">
Kafka最初被LinkedIn设计来处理活动流数据(activity stream data)和系统处理数据(operaitonal data)。活动流数据是指像page view、用户搜索关键词等等通过用户操作产生的数据，它的常见场景有时间线(time line)即新鲜事提醒、用户浏览量 搜索量排名等等。系统处理数据是服务器性能相关的数据，如CPU、负载、用户请求数等，它的应用场景多数是为后台服务，如在安全方面，可以监控到恶意攻击服务器的用户，从而做出相应措施，还有监控服务器性能，在其出现问题时即时报警等。</p>
<p style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:25px;color:rgb(51,51,51);">
这两种数据都属于日志数据的范畴。常见的日志系统，如scribe等都是将这些数据收集起来，然后再通过线下批处理，如hadoop集群等，获取所需的结果。线下处理的频率一般不会太高，比如一个小时甚至一天一次，这是不适合做实时应用的，如timeline这种应用。现有的消息队列系统非常适合这种实时性要求高的场景，但是由于它们都是在内存中维护消息队列，所以处理数据的大小就受到了限制。看到这里，相信聪明的读者已经知晓了Kafka出现的原因，就是要将上面讲的这两种场景整合到一个系统中，即offline的大数据分析和online的实时数据分析都可以通过该系统实现。</p>
<p style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:25px;color:rgb(51,51,51);">
Kafka在设计上保留了消息队列的常用操作，而这也使得在其诞生后被越来越广泛的作为一个消息队列使用，而不仅仅局限于处理上面提到的两种数据。</p>
<h2 id="kafka-1" style="color:rgb(51,51,51);font-size:24px;line-height:36px;border-bottom-width:1px;border-bottom-style:dotted;border-bottom-color:rgb(170,170,170);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;">
Kafka的设计特色</h2>
<p style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:25px;color:rgb(51,51,51);">
Kafka在设计上有以下几个特色：</p>
<ul style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:13px;line-height:18px;"><li style="line-height:18px;list-style-type:disc;">消息数据通过磁盘线性存取</li><li style="line-height:18px;list-style-type:disc;">强调吞吐率</li><li style="line-height:18px;list-style-type:disc;">消费状态由消费者自己维护</li><li style="line-height:18px;list-style-type:disc;">分布式</li></ul><h3 id="section" style="color:rgb(102,102,102);line-height:27px;font-size:18px;font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;">
消息数据通过磁盘线性存取</h3>
<p style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:25px;color:rgb(51,51,51);">
该特性应该是Kafka最让惊叹的特性。在我们的认识中，硬盘较内存的数据处理速度(读写)是慢很多的，所以基本所有设计数据处理的程序都是尽量使用内存。<span class="goog_qs-tidbit goog_qs-tidbit-0" style="background-color:inherit;color:inherit;text-decoration:inherit;display:inline !important;">然而Kafka的设计者在经过一番调研测试后，大胆地采用了全硬盘存取消息数据的方案，他们的主要依据是：</span></p>
<ul style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:13px;line-height:18px;"><li style="line-height:18px;list-style-type:disc;"><span class="goog_qs-tidbit goog_qs-tidbit-0" style="background-color:inherit;color:inherit;text-decoration:inherit;display:inline !important;">硬盘在线性读写的情况下性能优异。</span>
<ul style="list-style:disc;"><li style="line-height:18px;list-style-type:disc;">有测试表明在一个6 7200rpm 的SATA RAID-5 阵列上，线性写可以达到300MB/Sec，而随机写只有50KB/Sec。线性读写之所以可以有这么优异的表现与文件系统是分不开的，因为对写操作，操作系统一般会进行缓冲，而对于读操作，操作系统会进行预抓取的缓冲操作，这会极大地提高读取效率。又由于这一层缓存操作是在OS级的，也就意味着即便Kafka挂掉了重启，缓存也不会失效。</li></ul></li><li style="line-height:18px;list-style-type:disc;">减少JVM的GC触发。
<ul style="list-style:disc;"><li style="line-height:18px;list-style-type:disc;">JVM中的对象会占用除实际数据外的较多空间（如类的信息等等），结构不够紧凑，浪费空间。而当内存中维护的消息数据逐渐增多后，GC便会被频繁触发，这会极大影响应用的响应速度。因此，舍弃内存，使用磁盘可以减少GC触发带来的影响。</li></ul></li></ul><p style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:25px;color:rgb(51,51,51);">
Kafka<a href="http://research.microsoft.com/en-us/um/people/srikanth/netdb11/netdb11papers/netdb11-final12.pdf" rel="nofollow" style="color:rgb(51,51,238);text-decoration:none;">论文</a>中与ActiveMQ等消息队列的性能比较也进一步肯定了Kafka的这一设计理念。</p>
<h3 id="section-1" style="color:rgb(102,102,102);line-height:27px;font-size:18px;font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;">
强调吞吐率</h3>
<p style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:25px;color:rgb(51,51,51);">
Kafka的设计初衷便是要能处理TB级的数据，其更强调的是吞吐率。吞吐率表现在读和写两个方面，Kafka在这两个方面都做了很多优化。</p>
<h4 id="section-2" style="color:rgb(51,51,51);line-height:18px;font-size:14px;font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;">
写</h4>
<p style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:25px;color:rgb(51,51,51);">
前面的文章中我们曾经提到过kafka存储消息数据的形式，如下图： topic-partition–0000.kafka –1024.kafka –2048.kafka kafka在启动时会将该文件夹中的所有文件以channel形式打开，并且只有最后一个kafka文件以读写形式打开，其他都以只读方式打开，新到的消息都直接append到最后一个kafka文件中，这样就实现了顺序写。而前面已经提到，顺序写的性能是极高的，这样写的性能就有了保障。</p>
<h4 id="section-3" style="color:rgb(51,51,51);line-height:18px;font-size:14px;font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;">
读</h4>
<p style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:25px;color:rgb(51,51,51);">
Kafka在读方面使用了sendfile这个高级系统函数，也即zero-copy技术，感兴趣的同学可以去阅读<a href="http://www.ibm.com/developerworks/linux/library/j-zerocopy" rel="nofollow" style="color:rgb(51,51,238);text-decoration:none;">IBM的文章</a>。 这项技术通过减少系统拷贝次数，极大地提高了数据传输的效率。简单说明如下：</p>
<p style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:25px;color:rgb(51,51,51);">
程序读文件内容，调用socket发送内容，过程涉及以下几个步骤</p>
<ul style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:13px;line-height:18px;"><li style="line-height:18px;list-style-type:disc;">
<p style="font-size:14px;line-height:25px;">调用syscall，如read，陷入内核，读文件内容到内核缓存区pagecache</p>
</li><li style="line-height:18px;list-style-type:disc;">
<p style="font-size:14px;line-height:25px;">程序将文件内容由内核缓存区拷贝到用户空间内存</p>
</li><li style="line-height:18px;list-style-type:disc;">
<p style="font-size:14px;line-height:25px;">调用syscall，如socket write函数，陷入内核，将用户空间的内容拷贝的socket缓冲区内存，准备发送</p>
</li><li style="line-height:18px;list-style-type:disc;">
<p style="font-size:14px;line-height:25px;">将socket缓冲区内存拷贝到NIC（Network Interface Controller）缓冲区，发送数据。</p>
</li></ul><p style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:25px;color:rgb(51,51,51);">
其中涉及了2次syscall和4次数据拷贝。相信读者一定发现这4次数据拷贝是显然没有必要的，直接把数据从pagecache的内核缓存区读到NIC缓冲区不就可以了吗？sendfile系统函数做的就是这件事情。显然这会极大地提升数据传输的效率。在java中对应的函数调用是</p>
<pre style="font-family:Menlo, Monaco, 'Courier New', monospace;font-size:12px;color:rgb(51,51,51);line-height:18px;background-color:rgb(238,238,238);overflow:auto;"><code style="font-family:Menlo, Monaco, 'Courier New', monospace;font-size:12px;color:inherit;background-color:transparent;border:0px;">FileChannle.transferTo
</code></pre>
<p style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:25px;color:rgb(51,51,51);">
另外，Kafka还通过多条数据压缩传输、存取的办法来进一步提升吞吐率。</p>
<h3 id="section-4" style="color:rgb(102,102,102);line-height:27px;font-size:18px;font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;">
消费状态由消费者自己维护</h3>
<p style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:25px;color:rgb(51,51,51);">
Kafka消息数据的消费状态由消费者自己维护，原因这里不再详述了，感兴趣的同学可以去阅读文章开头的参考文献。这里简单说一下这么做的好处：</p>
<ul style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:13px;line-height:18px;"><li style="line-height:18px;list-style-type:disc;">
<p style="font-size:14px;line-height:25px;">去除了服务端维护消费状态的压力。</p>
</li><li style="line-height:18px;list-style-type:disc;">
<p style="font-size:14px;line-height:25px;">提升了消费者存储消费状态的自由度，如存储位置，可以存在zookeeper 数据库或者HDFS中，根据消费者自身的需求即可。</p>
</li><li style="line-height:18px;list-style-type:disc;">
<p style="font-size:14px;line-height:25px;">针对特殊需求，如消息消费失败，消费者可以回滚而重新消费消息。</p>
</li></ul><h3 id="section-5" style="color:rgb(102,102,102);line-height:27px;font-size:18px;font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;">
分布式</h3>
<p style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:25px;color:rgb(51,51,51);">
Kafka的broker producer和consumer都是可分布的，其实现是通过zookeeper来维护集群中这三者的信息，从而实现三者的交互，详细实现留待以后再说。</p>
<p style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:25px;color:rgb(51,51,51);">
通过上面的介绍相信大家已经对Kafka的设计有了一定的了解。这样的设计完全可以整合offline大数据处理和online实时处理的需求。对于offline处理，kafka支持将数据批量的迁移到hdfs中，而对于online的实时处理，只要合理的设置consumer处理特性就可以很容易地做到。LinkedIn在其文章中曾举到一个kafka的应用案例，这里与大家分享下。使用kafka来更新索引，当用户更新了自己的数据后，producer便产生一条消息发送到broker，consumer会即时获取该数据，进而更新索引，这样也就可以做到搜索时数据”秒级更新“的特性了。</p>
<h2 id="section-6" style="color:rgb(51,51,51);font-size:24px;line-height:36px;border-bottom-width:1px;border-bottom-style:dotted;border-bottom-color:rgb(170,170,170);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;">
小结</h2>
<p style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:25px;color:rgb(51,51,51);">
本文尝试向大家讲解Kafka的由来和设计特色，但笔者能力有限，如果有理解错误的地方，欢迎读者留言交流。接下来，笔者会针对kafka源码进行简单的剖析，也希望感兴趣的读者可以参与进来。</p>
<p style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:25px;color:rgb(51,51,51);">
转载自：<a href="http://www.tianjiaguo.com/programmer-living/kafka%E7%9B%91%E6%8E%A7%E6%B5%8B%E8%AF%95%E4%BB%A3%E7%A0%81/" rel="nofollow">rockybean's blog</a></p>
<p style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:25px;color:rgb(51,51,51);">
Kafka主要参考文献：<a href="http://research.microsoft.com/en-us/um/people/srikanth/netdb11/netdb11papers/netdb11-final12.pdf" rel="nofollow">kafka的设计论文</a></p>
<p style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:25px;color:rgb(51,51,51);">
<br></p>
<p style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:25px;color:rgb(51,51,51);">
<br></p>
            </div>
                </div>