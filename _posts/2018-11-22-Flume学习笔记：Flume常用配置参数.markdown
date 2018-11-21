---
layout:     post
title:      Flume学习笔记：Flume常用配置参数
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<div id="article_content" class="article_content clearfix csdn-tracking-statistics">
                                            <div class="markdown_views">
                <h1 id="概述"><a></a>概述</h1>



<ul>
<li>本篇文章是根据<a href="http://flume.apache.org/FlumeUserGuide.html" rel="nofollow" target="_blank">Flume官网</a>对Flume组件（Source,Channel,Sink）的常用配置参数做一个主要介绍，如有表达意思错误希望不吝指出。</li>
</ul>





<h1 id="sources"><a></a>Sources</h1>



<ul>
<li>Flume中常用的Source有NetCat，Avro，Exec，Spooling Directory，Taildir，也可以根据业务场景的需要自定义Source,具体介绍如下。</li>
</ul>



<h4 id="netcat-source">NetCat Source</h4>



<ul>
<li><p>NetCat Source可以使用TCP和UDP两种协议方式，使用方法基本相同，通过监听指定的IP和端口来传输数据，它会将监听到的每一行数据转化成一个Event写入到Channel中。（必须参数以@标示，下类同）</p>

<pre class="prettyprint"><code class="has-numbering hljs oxygene"><span class="hljs-keyword">Property</span> Name   <span class="hljs-keyword">Default</span>       Description
channels@          –     
<span class="hljs-keyword">type</span>@              –          类型指定为：netcat
bind@              –          绑定机器名或IP地址
port@              –          端口号
max-line-length   <span class="hljs-number">512</span>         一行的最大字节数
ack-every-<span class="hljs-keyword">event</span>   <span class="hljs-keyword">true</span>        对成功接受的<span class="hljs-keyword">Event</span>返回OK
<span class="hljs-keyword">selector</span>.<span class="hljs-keyword">type</span>   replicating   选择器类型replicating <span class="hljs-keyword">or</span> multiplexing
<span class="hljs-keyword">selector</span>.*                    选择器相关参数
interceptors       –          拦截器列表，多个以空格分隔
interceptors.*                拦截器相关参数
</code></pre><ul class="pre-numbering"><li>1</li><li>2</li><li>3</li><li>4</li><li>5</li><li>6</li><li>7</li><li>8</li><li>9</li><li>10</li><li>11</li><li>12</li></ul></li>
</ul>



<h4 id="avro-source">Avro Source</h4>



<ul>
<li><p>不同主机上的Agent通过网络传输数据可使用的Source，一般是接受Avro  client的数据，或和是上一级Agent的Avro Sink成对存在。</p>

<pre class="prettyprint"><code class="has-numbering hljs oxygene"><span class="hljs-keyword">Property</span> Name              <span class="hljs-keyword">Default</span>          Description
channels@                    –   
<span class="hljs-keyword">type</span>@                        –              类型指定为：avro
bind@                        –              监听的主机名或IP地址
port@                        –              端口号
threads                      –              传输可使用的最大线程数
<span class="hljs-keyword">selector</span>.<span class="hljs-keyword">type</span>        
<span class="hljs-keyword">selector</span>.*       
interceptors                 –              拦截器列表
interceptors.*       
compression-<span class="hljs-keyword">type</span>            none            可设置为“none” 或 “deflate”. 压缩类型需要和AvroSource匹配
</code></pre><ul class="pre-numbering"><li>1</li><li>2</li><li>3</li><li>4</li><li>5</li><li>6</li><li>7</li><li>8</li><li>9</li><li>10</li><li>11</li><li>12</li></ul></li>
</ul>



<h4 id="exec-source">Exec Source</h4>



<ul>
<li><p>Exec source通过执行给定的Unix命令的传输结果数据，如，cat，tail -F等，实时性比较高，但是一旦Agent进程出现问题，可能会导致数据的丢失。</p>

<pre class="prettyprint"><code class="has-numbering hljs oxygene"><span class="hljs-keyword">Property</span> Name            <span class="hljs-keyword">Default</span>                 Description
channels@                   –    
<span class="hljs-keyword">type</span>@                       –                    类型指定为：exec
command@                    –                    需要去执行的命令
shell                       –                    运行命令的shell脚本文件
restartThrottle           <span class="hljs-number">10000</span>                  尝试重启的超时时间
restart                   <span class="hljs-keyword">false</span>                  如果命令执行失败，是否重启
logStdErr                 <span class="hljs-keyword">false</span>                  是否记录错误日志
batchSize                  <span class="hljs-number">20</span>                    批次写入channel的最大日志数量
batchTimeout              <span class="hljs-number">3000</span>                   批次写入数据的最大等待时间（毫秒）
<span class="hljs-keyword">selector</span>.<span class="hljs-keyword">type</span>          replicating               选择器类型replicating <span class="hljs-keyword">or</span> multiplexing
<span class="hljs-keyword">selector</span>.*                                       选择器其他参数
interceptors                –                    拦截器列表，多个空格分隔
interceptors.*       
</code></pre><ul class="pre-numbering"><li>1</li><li>2</li><li>3</li><li>4</li><li>5</li><li>6</li><li>7</li><li>8</li><li>9</li><li>10</li><li>11</li><li>12</li><li>13</li><li>14</li><li>15</li></ul></li>
</ul>



<h4 id="spooling-directory-source">Spooling Directory Source</h4>



<ul>
<li><p>通过监控一个文件夹将新增文件内容转换成Event传输数据，特点是不会丢失数据，使用Spooling Directory Source需要注意的两点是，1)不能对被监控的文件夹下的新增的文件做出任何更改，2）新增到监控文件夹的文件名称必须是唯一的。由于是对整个新增文件的监控，Spooling Directory Source的实时性相对较低，不过可以采用对文件高粒度分割达到近似实时。</p>

<pre class="prettyprint"><code class="has-numbering hljs oxygene"><span class="hljs-keyword">Property</span> Name                 <span class="hljs-keyword">Default</span>             Description
channels@                        –   
<span class="hljs-keyword">type</span>@                            –                类型指定：spooldir.
spoolDir@                        –                被监控的文件夹目录
fileSuffix                  .COMPLETED            完成数据传输的文件后缀标志
deletePolicy                   never              删除已经完成数据传输的文件时间：never <span class="hljs-keyword">or</span> immediate
fileHeader                     <span class="hljs-keyword">false</span>              是否在header中添加文件的完整路径信息
fileHeaderKey                   file              如果header中添加文件的完整路径信息时key的名称
basenameHeader                 <span class="hljs-keyword">false</span>              是否在header中添加文件的基本名称信息
basenameHeaderKey             basename            如果header中添加文件的基本名称信息时key的名称
includePattern                 ^.*$               使用正则来匹配新增文件需要被传输数据的文件
ignorePattern                   ^$                使用正则来忽略新增的文件
trackerDir                  .flumespool           存储元数据信息目录
consumeOrder                  oldest              文件消费顺序：oldest, youngest <span class="hljs-keyword">and</span> random.
maxBackoff                     <span class="hljs-number">4000</span>               如果channel容量不足，尝试写入的超时时间，如果仍然不能写入，则会抛出ChannelException
batchSize                      <span class="hljs-number">100</span>                批次处理粒度
inputCharset                  UTF-<span class="hljs-number">8</span>               输入码表格式
decodeErrorPolicy              FAIL               遇到不可解码字符后的处理方式：FAIL，REPLACE，IGNORE
<span class="hljs-keyword">selector</span>.<span class="hljs-keyword">type</span>               replicating           选择器类型：replicating <span class="hljs-keyword">or</span> multiplexing
<span class="hljs-keyword">selector</span>.*                                        选择器其他参数
interceptors                    –                 拦截器列表，空格分隔
interceptors.*  
</code></pre><ul class="pre-numbering"><li>1</li><li>2</li><li>3</li><li>4</li><li>5</li><li>6</li><li>7</li><li>8</li><li>9</li><li>10</li><li>11</li><li>12</li><li>13</li><li>14</li><li>15</li><li>16</li><li>17</li><li>18</li><li>19</li><li>20</li><li>21</li><li>22</li><li>23</li></ul></li>
</ul>



<h4 id="taildir-source">Taildir Source</h4>



<ul>
<li><p>可以实时的监控指定一个或多个文件中的新增内容，由于该方式将数据的偏移量保存在一个指定的json文件中，即使在Agent挂掉或被kill也不会有数据的丢失，需要注意的是，该Source不能在Windows上使用。</p>

<pre class="prettyprint"><code class="has-numbering hljs vbnet"><span class="hljs-keyword">Property</span> Name                      <span class="hljs-keyword">Default</span>            Description
channels@                             –  
type@                                 –               指定类型：TAILDIR.
filegroups@                           –               文件组的名称，多个空格分隔
filegroups.&lt;filegroupName&gt;@           –               被监控文件的绝对路径
positionFile         ~/.flume/taildir_position.json      存储数据偏移量路径
headers.&lt;filegroupName&gt;.&lt;headerKey&gt;   –               Header <span class="hljs-keyword">key</span>的名称
byteOffsetHeader                    <span class="hljs-literal">false</span>             是否添加字节偏移量到<span class="hljs-keyword">key</span>为‘byteoffset’值中
skipToEnd                           <span class="hljs-literal">false</span>             当偏移量不能写入到文件时是否跳到文件结尾
idleTimeout                         <span class="hljs-number">120000</span>            关闭没有新增内容的文件超时时间（毫秒）
writePosInterval                    <span class="hljs-number">3000</span>              在positionfile 写入每一个文件lastposition的时间间隔
batchSize                            <span class="hljs-number">100</span>              批次处理行数
fileHeader                          <span class="hljs-literal">false</span>             是否添加header存储文件绝对路径
fileHeaderKey                       file              fileHeader启用时，使用的<span class="hljs-keyword">key</span>
</code></pre><ul class="pre-numbering"><li>1</li><li>2</li><li>3</li><li>4</li><li>5</li><li>6</li><li>7</li><li>8</li><li>9</li><li>10</li><li>11</li><li>12</li><li>13</li><li>14</li><li>15</li></ul></li>
</ul>





<h1 id="channels"><a></a>Channels</h1>



<ul>
<li>官网提供的Channel有多种类型可供选择，这里介绍Memory Channel和File Channel。</li>
</ul>



<h4 id="memory-channel">Memory Channel</h4>



<ul>
<li><p>Memory Channel是使用内存来存储Event，使用内存的意味着数据传输速率会很快，但是当Agent挂掉后，存储在Channel中的数据将会丢失。</p>

<pre class="prettyprint"><code class="has-numbering hljs vhdl"><span class="hljs-keyword">Property</span> Name                 <span class="hljs-keyword">Default</span>                Description
<span class="hljs-keyword">type</span>@                            –                   类型指定为：memory
capacity                        <span class="hljs-number">100</span>                  存储在channel中的最大容量
transactionCapacity             <span class="hljs-number">100</span>                  从一个source中去或者给一个sink，每个事务中最大的事件数
keep-alive                       <span class="hljs-number">3</span>                   对于添加或者删除一个事件的超时的秒钟
byteCapacityBufferPercentage    <span class="hljs-number">20</span>                   定义缓存百分比
byteCapacity              see description            Channel中允许存储的最大字节总数
</code></pre><ul class="pre-numbering"><li>1</li><li>2</li><li>3</li><li>4</li><li>5</li><li>6</li><li>7</li><li>8</li></ul></li>
</ul>



<h4 id="file-channel">File Channel</h4>



<ul>
<li><p>File Channel使用磁盘来存储Event，速率相对于Memory Channel较慢，但数据不会丢失。</p>

<pre class="prettyprint"><code class="has-numbering hljs lasso">Property Name            Default                  Description    
<span class="hljs-keyword">type</span>@                      –                      类型指定：file<span class="hljs-built_in">.</span>
checkpointDir   ~<span class="hljs-subst">/</span><span class="hljs-built_in">.</span>flume/file<span class="hljs-attribute">-channel</span>/checkpoint  checkpoint目录
useDualCheckpoints       <span class="hljs-literal">false</span>                    备份checkpoint，为<span class="hljs-literal">True</span>，backupCheckpointDir必须设置
backupCheckpointDir        –                      备份checkpoint目录
dataDirs    ~<span class="hljs-subst">/</span><span class="hljs-built_in">.</span>flume/file<span class="hljs-attribute">-channel</span>/<span class="hljs-built_in">data</span>           数据存储所在的目录设置
transactionCapacity      <span class="hljs-number">10000</span>                    Event存储最大值
checkpointInterval       <span class="hljs-number">30000</span>                    checkpoint间隔时间
maxFileSize            <span class="hljs-number">2146435071</span>                 单一日志最大设置字节数
minimumRequiredSpace    <span class="hljs-number">524288000</span>                 最小的请求闲置空间（以字节为单位）
capacity                 <span class="hljs-number">1000000</span>                  Channel最大容量
keep<span class="hljs-attribute">-alive</span>                 <span class="hljs-number">3</span>                      一个存放操作的等待时间值（秒）
use<span class="hljs-attribute">-log</span><span class="hljs-attribute">-replay</span><span class="hljs-attribute">-v1</span>         <span class="hljs-literal">false</span>                   Expert: 使用老的回复逻辑
use<span class="hljs-attribute">-fast</span><span class="hljs-attribute">-replay</span>           <span class="hljs-literal">false</span>                   Expert: 回复不需要队列
checkpointOnClose         <span class="hljs-literal">true</span>         
</code></pre><ul class="pre-numbering"><li>1</li><li>2</li><li>3</li><li>4</li><li>5</li><li>6</li><li>7</li><li>8</li><li>9</li><li>10</li><li>11</li><li>12</li><li>13</li><li>14</li><li>15</li><li>16</li></ul></li>
</ul>



<h1 id="sinks"><a></a>Sinks</h1>



<ul>
<li>Flume常用Sinks有Log Sink，HDFS Sink，Avro Sink，Kafka Sink，当然也可以自定义Sink。</li>
</ul>



<h4 id="logger-sink">Logger Sink</h4>



<ul>
<li><p>Logger Sink以INFO 级别的日志记录到log日志中，这种方式通常用于测试。</p>

<pre class="prettyprint"><code class="has-numbering hljs vhdl"><span class="hljs-keyword">Property</span> Name          <span class="hljs-keyword">Default</span>           Description
channel@                 –   
<span class="hljs-keyword">type</span>＠                   –               类型指定：logger
maxBytesToLog           <span class="hljs-number">16</span>               能够记录的最大Event <span class="hljs-keyword">Body</span>字节数  
</code></pre><ul class="pre-numbering"><li>1</li><li>2</li><li>3</li><li>4</li><li>5</li></ul></li>
</ul>



<h4 id="hdfs-sink">HDFS Sink</h4>



<ul>
<li><p>Sink数据到HDFS，目前支持text 和 sequence files两种文件格式，支持压缩，并可以对数据进行分区，分桶存储。</p>

<pre class="prettyprint"><code class="has-numbering hljs avrasm">Name                   Default               Description
channel@                  –  
type@                     –                  指定类型：hdfs
hdfs<span class="hljs-preprocessor">.path</span>@                –                  HDFS的路径，eg hdfs://namenode/flume/webdata/
hdfs<span class="hljs-preprocessor">.filePrefix</span>        FlumeData             保存数据文件的前缀名
hdfs<span class="hljs-preprocessor">.fileSuffix</span>           –                  保存数据文件的后缀名
hdfs<span class="hljs-preprocessor">.inUsePrefix</span>          –                  临时写入的文件前缀名
hdfs<span class="hljs-preprocessor">.inUseSuffix</span>         <span class="hljs-preprocessor">.tmp</span>                临时写入的文件后缀名
hdfs<span class="hljs-preprocessor">.rollInterval</span>         <span class="hljs-number">30</span>                 间隔多长将临时文件滚动成最终目标文件，单位：秒，
                                             如果设置成<span class="hljs-number">0</span>，则表示不根据时间来滚动文件
hdfs<span class="hljs-preprocessor">.rollSize</span>            <span class="hljs-number">1024</span>                当临时文件达到多少（单位：bytes）时，滚动成目标文件，
                                             如果设置成<span class="hljs-number">0</span>，则表示不根据临时文件大小来滚动文件
hdfs<span class="hljs-preprocessor">.rollCount</span>            <span class="hljs-number">10</span>                 当 events 数据达到该数量时候，将临时文件滚动成目标文件，
                                             如果设置成<span class="hljs-number">0</span>，则表示不根据events数据来滚动文件
hdfs<span class="hljs-preprocessor">.idleTimeout</span>          <span class="hljs-number">0</span>                  当目前被打开的临时文件在该参数指定的时间（秒）内，
                                             没有任何数据写入，则将该临时文件关闭并重命名成目标文件
hdfs<span class="hljs-preprocessor">.batchSize</span>           <span class="hljs-number">100</span>                 每个批次刷新到 HDFS 上的 events 数量
hdfs<span class="hljs-preprocessor">.codeC</span>                –                  文件压缩格式，包括：gzip, bzip2, lzo, lzop, snappy
hdfs<span class="hljs-preprocessor">.fileType</span>         SequenceFile           文件格式，包括：SequenceFile, DataStream,CompressedStre，
                                             当使用DataStream时候，文件不会被压缩，不需要设置hdfs<span class="hljs-preprocessor">.codeC</span><span class="hljs-comment">;</span>
                                             当使用CompressedStream时候，必须设置一个正确的hdfs<span class="hljs-preprocessor">.codeC</span>值；
hdfs<span class="hljs-preprocessor">.maxOpenFiles</span>        <span class="hljs-number">5000</span>                最大允许打开的HDFS文件数，当打开的文件数达到该值，
                                             最早打开的文件将会被关闭
hdfs<span class="hljs-preprocessor">.minBlockReplicas</span>     –                  HDFS副本数，写入 HDFS 文件块的最小副本数。
                                             该参数会影响文件的滚动配置，一般将该参数配置成<span class="hljs-number">1</span>，才可以按照配置正确滚动文件
hdfs<span class="hljs-preprocessor">.writeFormat</span>        Writable             写 sequence 文件的格式。包含：Text, Writable（默认）
hdfs<span class="hljs-preprocessor">.callTimeout</span>         <span class="hljs-number">10000</span>               执行HDFS操作的超时时间（单位：毫秒）
hdfs<span class="hljs-preprocessor">.threadsPoolSize</span>      <span class="hljs-number">10</span>                 hdfs sink 启动的操作HDFS的线程数
hdfs<span class="hljs-preprocessor">.rollTimerPoolSize</span>    <span class="hljs-number">1</span>                  hdfs sink 启动的根据时间滚动文件的线程数
hdfs<span class="hljs-preprocessor">.kerberosPrincipal</span>    –                  HDFS安全认证kerberos配置
hdfs<span class="hljs-preprocessor">.kerberosKeytab</span>       –                  HDFS安全认证kerberos配置
hdfs<span class="hljs-preprocessor">.proxyUser</span>                               代理用户
hdfs<span class="hljs-preprocessor">.round</span>              false                是否启用时间上的”舍弃”
hdfs<span class="hljs-preprocessor">.roundValue</span>           <span class="hljs-number">1</span>                  时间上进行“舍弃”的值
hdfs<span class="hljs-preprocessor">.roundUnit</span>          second               时间上进行”舍弃”的单位，包含：second,minute,hour
hdfs<span class="hljs-preprocessor">.timeZone</span>         Local Time             时区。
hdfs<span class="hljs-preprocessor">.useLocalTimeStamp</span>  false                是否使用当地时间
hdfs<span class="hljs-preprocessor">.closeTries</span> <span class="hljs-number">0</span>       Number               hdfs sink 关闭文件的尝试次数；
                                             如果设置为<span class="hljs-number">1</span>，当一次关闭文件失败后，hdfs sink将不会再次尝试关闭文件，
                                             这个未关闭的文件将会一直留在那，并且是打开状态；
                                             设置为<span class="hljs-number">0</span>，当一次关闭失败后，hdfs sink会继续尝试下一次关闭，直到成功
hdfs<span class="hljs-preprocessor">.retryInterval</span>        <span class="hljs-number">180</span>                hdfs sink 尝试关闭文件的时间间隔，
                                             如果设置为<span class="hljs-number">0</span>，表示不尝试，相当于于将hdfs<span class="hljs-preprocessor">.closeTries</span>设置成<span class="hljs-number">1</span>
serializer               TEXT                序列化类型
serializer.*                 
</code></pre><ul class="pre-numbering"><li>1</li><li>2</li><li>3</li><li>4</li><li>5</li><li>6</li><li>7</li><li>8</li><li>9</li><li>10</li><li>11</li><li>12</li><li>13</li><li>14</li><li>15</li><li>16</li><li>17</li><li>18</li><li>19</li><li>20</li><li>21</li><li>22</li><li>23</li><li>24</li><li>25</li><li>26</li><li>27</li><li>28</li><li>29</li><li>30</li><li>31</li><li>32</li><li>33</li><li>34</li><li>35</li><li>36</li><li>37</li><li>38</li><li>39</li><li>40</li><li>41</li><li>42</li><li>43</li><li>44</li><li>45</li><li>46</li></ul></li>
</ul>



<h4 id="avro-sink">Avro Sink</h4>

<pre class="prettyprint"><code class="has-numbering hljs lasso">    Property Name              Default              Description
    channel@                         –   
    <span class="hljs-keyword">type</span>@                        –                  指定类型：avro<span class="hljs-built_in">.</span>
    hostname@                    –                  主机名或IP
    port@                        –                  端口号
    batch<span class="hljs-attribute">-size</span>                  <span class="hljs-number">100</span>                 批次处理Event数
    connect<span class="hljs-attribute">-timeout</span>            <span class="hljs-number">20000</span>                连接超时时间
    request<span class="hljs-attribute">-timeout</span>            <span class="hljs-number">20000</span>                请求超时时间
    compression<span class="hljs-attribute">-type</span>            <span class="hljs-literal">none</span>                压缩类型，“<span class="hljs-literal">none</span>” <span class="hljs-literal">or</span> “deflate”<span class="hljs-built_in">.</span>
    compression<span class="hljs-attribute">-level</span>            <span class="hljs-number">6</span>                  压缩级别，<span class="hljs-number">0</span>表示不压缩，<span class="hljs-number">1</span><span class="hljs-subst">-</span><span class="hljs-number">9</span>数字越大，压缩比越高
    ssl                        <span class="hljs-literal">false</span>                使用ssl加密
</code></pre><ul class="pre-numbering"><li>1</li><li>2</li><li>3</li><li>4</li><li>5</li><li>6</li><li>7</li><li>8</li><li>9</li><li>10</li><li>11</li><li>12</li></ul>

<h4 id="kafka-sink">Kafka Sink</h4>

<ul>
<li><p>传输数据到Kafka中，需要注意的是Flume版本和Kafka版本的兼容性</p>

<pre class="prettyprint"><code class="has-numbering hljs avrasm">Property Name              Default             Description
type                         –                 指定类型：org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.KafkaSink</span>
kafka<span class="hljs-preprocessor">.bootstrap</span><span class="hljs-preprocessor">.servers</span>      –                 kafka服务地址
kafka<span class="hljs-preprocessor">.topic</span>          default-flume-topic       kafka Topic
flumeBatchSize              <span class="hljs-number">100</span>                批次写入kafka Event数
kafka<span class="hljs-preprocessor">.producer</span><span class="hljs-preprocessor">.acks</span>          <span class="hljs-number">1</span>                 多少个副本确认后才能确定消息传递成功，<span class="hljs-number">0</span>表示不需要确认
                                               <span class="hljs-number">1</span>表示只需要首要的副本得到确认，-<span class="hljs-number">1</span>表示等待所有确认。
</code></pre><ul class="pre-numbering"><li>1</li><li>2</li><li>3</li><li>4</li><li>5</li><li>6</li><li>7</li><li>8</li></ul></li>
</ul>            </div>

<pre><code>        &lt;link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/markdown_views-ea0013b516.css"&gt;
            &lt;/div&gt;
</code></pre>

<p>转载自 <br>
<a href="https://blog.csdn.net/realoyou/article/details/81514128" rel="nofollow">https://blog.csdn.net/realoyou/article/details/81514128</a> <br>
感谢大牛的总结</p></div>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>