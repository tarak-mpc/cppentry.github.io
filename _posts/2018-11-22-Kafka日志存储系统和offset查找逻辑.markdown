---
layout:     post
title:      Kafka日志存储系统和offset查找逻辑
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主瞎抄的文章，未经博主允许可以随意转载。					https://blog.csdn.net/lkforce/article/details/77854813				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h3>起始篇</h3>
<p> 1，kafka通过文件系统来保存和缓存处理的消息，每个发送到kafka的消息，都会被记录到日志文件中，由partition的leader记录，并由partition的follower同步。</p>
<p> 2，kafka的消息采用顺序写磁盘的方式记录，速度非常快，时间复杂度是O(1)，是kafka高效性的体现。</p>
<p> 3，kafka日志的目录在server.properties可以配置，配置项是log.dirs，也可以在创建topic时指定。</p>
<p> 4，kafka日志是以partition为单位记录的，每个partition都有一个单独的文件夹，比如一个名为test0的topic，设置了5个partition，那么在log.dirs指向的目录下，将会有5个文件夹，名字分别是：</p>
<blockquote style="border:none;">
<p>test0-0</p>
test0-1
<p>test0-2</p>
test0-3
<p>test0-4</p>
</blockquote>
<p> </p>
<h3>日志文件篇</h3>
<p> 下面详细解释一下其中一个partition的日志。</p>
<p><strong>1，segment分段</strong></p>
<p>比如test0-0目录，保存了partition0的日志，这里面的日志文件也不只是一个，而是被切断成了多个段，每段叫做一个segment，每个segment其实包含了两个文件，一个log文件，记录消息，另一个是index文件，这是log文件的索引文件，这两个文件总是成对出现的。这种分段的方式便于consumer快速的查询到想要定位的消息。</p>
<p> <strong>2，offset偏移量</strong></p>
<p>kafka用offset来区别每条不同的消息，offset也叫偏移量，是有序的数字，相当于消息的id，长度20位，不够20位的补0。比如第一条消息的offset就是00000000000000000001。</p>
<p><strong> 3，日志文件</strong></p>
<p>每个segment的log文件和index文件，都以上一个segment最后一条消息的offset来命名。也就是说，该partition的第一段，log文件和index文件的名字分别是00000000000000000000.log和00000000000000000000.index，log文件里的第一条日志的offset就会是00000000000000000001。假设第一段里面存了1000条消息然后分段了（分段的方式是可以配置的），那么第二段的文件名就是00000000000000001000.log和00000000000000001000.index，log文件里的第一条消息的offset将是00000000000000001001。</p>
<p> <strong>4，log文件的存储格式</strong></p>
<p>log文件定义了严格的存储格式，以便快速查询消息。</p>
<p>每条消息的内容由以下部分组成：</p>
<p>
</p><table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p>关键字</p>
</td>
<td valign="top">
<p>解释</p>
</td>
</tr><tr><td valign="top">
<p>8 byte offset</p>
</td>
<td valign="top">
<p>这是该条消息在partition中的绝对offset。能表示这是partition的第多少条消息</p>
</td>
</tr><tr><td valign="top">
<p>4 byte message</p>
</td>
<td valign="top">
<p>message大小</p>
</td>
</tr><tr><td valign="top">
<p>4 byte CRC32</p>
</td>
<td valign="top">
<p>用crc32校验message</p>
</td>
</tr><tr><td valign="top">
<p>1 byte “magic”</p>
</td>
<td valign="top">
<p>表示本次发布Kafka服务程序协议版本号</p>
</td>
</tr><tr><td valign="top">
<p>1 byte “attributes”</p>
</td>
<td valign="top">
<p>表示为独立版本、或标识压缩类型、或编码类型</p>
</td>
</tr><tr><td valign="top">
<p>4 byte key length</p>
</td>
<td valign="top">
<p>表示key的长度,当key为-1时，K byte key字段不填</p>
</td>
</tr><tr><td valign="top">
<p>K byte key</p>
</td>
<td valign="top">
<p>可选</p>
</td>
</tr><tr><td valign="top">
<p>value bytes payload</p>
</td>
<td valign="top">
<p>实际消息数据</p>
</td>
</tr></tbody></table><br><p><strong> 5，index文件的存储方式</strong></p>
<p>index文件是二进制存储的，里面的每条索引都记录了消息的相对offset和在文件中的物理位置。这里的相对offset和log文件里的绝对offset不同，相对offset是每个segment都从1开始的，而绝对offset在整个partition中都是唯一的。</p>
<p>假设第一个segment记录了1000条消息，第二段segment记录了1234条消息，那么第二段segment的log文件里大概是这样的：</p>
<blockquote style="border:none;">Message1001<br>
Message1002<br>
Message1003<br>
Message1004<br>
Message1005<br>
Message1006<br>
....
<p>Message2234</p>
</blockquote>
<p>（后面的数字是消息在partition绝对offset，实际上log文件有自己的存储格式）</p>
<p>那么index文件里的内容可能是这样的：</p>
<blockquote style="border:none;">
<p>1,0</p>
<p>3,22</p>
<p>6,45</p>
<p>10,77</p>
<p>...</p>
<p>1230,8765</p>
</blockquote>
<p>每行的第一个数代表某条消息在此segment的相对offset，第二个数代表这条消息在log文件中的偏移量。（偏移量的单位还没搞明白，好像是byte）</p>
<p>第一行1,0代表segment的第一条消息，也就是Message1001，在segment最开头的位置。</p>
<p>第二行3,22代表segment的第三条消息，也就是Message1003，从第22个偏移量开始。</p>
<p>最后一行1230,8765代表segment的第1230条消息，也就是Message2230，从第8765个偏移量开始。</p>
<p>可以看到，index文件没有记录每一条消息的偏移量，而是采用稀疏索引的方式，隔几条记录一次，这样使得索引文件的变的比较小，但是在查询时要付出一点的性能损失。</p>
<p>只要consumer从index文件定位了消息的位置，就能快速的从log文件里找到这条消息。</p>
<p> <strong>6，分段的策略</strong></p>
<p>segment分段有两种方式，按大小和按文件生成时间。</p>
<p>按大小：server.properties中的log.segment.bytes定义了每段的log文件的大小上限，如果超出该上限则后面的消息会创建新文件来存储。该配置项默认值是1014*1024*1024。该配置可以在创建topic时额外指定，不使用配置文件的配置项。</p>
<p>按生成时间：server.properties中的log.roll.hours定义了按创建时间分组的方式。如果一个log文件创建时间达到了该配置中的小时数，即使文件大小没有达到log.segment.bytes，后面的消息也会创建新文件。该配置可以在创建topic时额外指定，不使用配置文件的配置项。</p>
<p> </p>
<h3>查询篇</h3>
<p>日志分好了，可以按照consumer的要求查询消息了。</p>
<p>比如，日志文件有：</p>
<blockquote style="border:none;">
<p>00000000000000000000.log</p>
<p>00000000000000000000.index</p>
<p>00000000000000001000.log</p>
<p>00000000000000001000.index</p>
<p>00000000000000002234.log</p>
<p>00000000000000002234.index</p>
</blockquote>
<p> </p>
<p>其中00000000000000001000.log的内容：</p>
<blockquote style="border:none;">
<p>Message1001</p>
<p>Message1002</p>
<p>Message1003</p>
<p>Message1004</p>
<p>Message1005</p>
<p>Message1006</p>
<p>....</p>
<p>Message2234</p>
</blockquote>
<p>00000000000000001000.index的内容：</p>
<blockquote style="border:none;">
<p>1,0</p>
<p>3,22</p>
<p>6,45</p>
<p>10,77</p>
<p>...</p>
<p>1230,8765</p>
</blockquote>
<p> </p>
<p>如果consumer要找offset是1008的消息，那么，</p>
<p>1，按照二分法找到小于1008的segment，也就是00000000000000001000.log和00000000000000001000.index</p>
<p>2，用目标offset减去文件名中的offset得到消息在这个segment中的偏移量。也就是1008-1000=8，偏移量是8。</p>
<p>3，再次用二分法在index文件中找到对应的索引，也就是第三行6,45。</p>
<p>4，到log文件中，从偏移量45的位置开始（实际上这里的消息offset是1006），顺序查找，直到找到offset为1008的消息。查找期间kafka是按照log的存储格式来判断一条消息是否结束的。</p>
<p> </p>
<p>over</p>
            </div>
                </div>