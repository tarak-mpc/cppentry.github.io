---
layout:     post
title:      Hbase Split 解析
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u010039929/article/details/74295869				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>HBase Split是hbase根据一定的触发条件和一定的分裂策略将HBase的一个region进行分裂成两个子region并对父region进行清除处理的过程。Region是HBase中一个非常核心的组织单元，所有的region组成了整个HBase集群，如下面的HBase的体系结构所示：</p>

<p><img src="https://img-blog.csdn.net/20170704095128355?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDAzOTkyOQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>在HBase中，split其实是进行sharding的一种技术手段，通过HBase的split条件和split策略，将region进行合理的split，再通过HBase的balance策略，将分裂的region负载均衡到各个regionserver上，最大化的发挥分布式系统的优点。HBase这种自动的sharding技术比传统的数据库sharding要省事的多，减轻了维护的成本，但是这样也会给HBase带来额外的IO开销，因此在很多系统中如果能很好的预计rowkey的分布和数据增长情况，可以通过<strong>预先分区</strong>，事先将region分配好，再将HBase的自动分区禁掉。</p>

<p>Region split的大概流程如下：</p>

<p>1.region先更改ZK中该region的状态为SPLITING。 <br>
2.Master检测到region状态改变。 <br>
3.region会在存储目录下新建.split文件夹用于保存split后的daughter region信息。 <br>
4.Parent region关闭数据写入并触发flush操作，保证所有写入Parent region的数据都能持久化。 <br>
5.在.split文件夹下新建两个region，称之为daughter A、daughter B。 <br>
6.Daughter A、Daughter B拷贝到HBase根目录下，形成两个新的region。 <br>
7.Parent region通知修改.META.表后下线，不再提供服务。 <br>
8.Daughter A、Daughter B上线，开始向外提供服务。 <br>
9.如果开启了balance_switch服务，split后的region将会被重新分布。</p>

<p>上面1 ~ 9就是region split的整个过程，split过程非常快，速度基本会在秒级内，那么在这么快的时间内，region中的数据怎么被重新组织的？其实，split只是简单的把region从逻辑上划分成两个，并没有涉及到底层数据的重组，split完成后，Parent region并没有被销毁，只是被做下线处理，不再对外部提供服务。而新产生的region Daughter A 和 Daughter B，内部的数据只是简单的到Parent region数据的索引，Parent region数据的清理在Daughter A和Daughter B进行major compact以后，发现已经没有到其内部数据的索引后，Parent region才会被真正的清理。</p>

<p><strong>HBase Split触发条件</strong></p>

<p>1 Pre-splitting <br>
当一个table刚被创建的时候，Hbase默认的分配一个region给table。也就是说这个时候，所有的读写请求都会访问到同一个regionServer的同一个region中，这个时候就达不到负载均衡的效果了，集群中的其他regionServer就可能会处于比较空闲的状态。解决这个问题可以用pre-splitting，在创建table的时候就配置好，生成多个region。</p>

<p>在table初始化的时候如果不配置的话，Hbase是不知道如何去split region的，因为Hbase不知道应该那个row key可以作为split的开始点。如果我们可以大概预测到row key的分布，我们可以使用pre-spliting来帮助我们提前split region。不过如果我们预测得不准确的话，还是可能导致某个region过热，被集中访问，不过还好我们还有auto-split。最好的办法就是首先预测split的切分点，做pre-splitting,然后后面让auto-split来处理后面的负载均衡。</p>

<p>Hbase自带了两种pre-split的算法，分别是 HexStringSplit 和  UniformSplit 。如果我们的row key是十六进制的字符串作为前缀的，就比较适合用HexStringSplit，作为pre-split的算法。例如，我们使用HexHash(prefix)作为row key的前缀，其中Hexhash为最终得到十六进制字符串的hash算法。我们也可以用我们自己的split算法。</p>

<p>2 Auto splitting</p>

<p>1.当memstore flush操作后，HRegion写入新的HFile，有可能产生较大的HFile，HBase就会调用CompactSplitThread.requestSplit判断是否需要split操作。</p>

<p>2.HStore刚刚进行完compact操作后有可能产生较大的HFile，当满足HBase的某一分裂策略后就会进行split操作。</p>

<p>HBase默认有三种自动split的策略，ConstantSizeRegionSplitPolicy,IncreasingToUpperBoundRegionSplitPolicy还有 KeyPrefixRegionSplitPolicy。在0.94版本之前ConstantSizeRegionSplitPolicy 是默认和唯一的split策略。当某个store（对应一个column family）的大小大于配置值 ‘hbase.hregion.max.filesize’的时候（默认10G）region就会自动分裂。而0.94版本中，IncreasingToUpperBoundRegionSplitPolicy 是默认的split策略。这个策略中，最小的分裂大小和table的某个region server的region 个数有关，当store file的大小大于如下公式得出的值的时候就会split，公式如下</p>

<pre class="prettyprint"><code class=" hljs avrasm"> Min (R^<span class="hljs-number">2</span> * “hbase<span class="hljs-preprocessor">.hregion</span><span class="hljs-preprocessor">.memstore</span><span class="hljs-preprocessor">.flush</span><span class="hljs-preprocessor">.size</span>”, “hbase<span class="hljs-preprocessor">.hregion</span><span class="hljs-preprocessor">.max</span><span class="hljs-preprocessor">.filesize</span>”)  R为同一个table中在同一个region server中region的个数。</code></pre>

<p>例如:</p>

<p>hbase.hregion.memstore.flush.size 默认值 128MB。</p>

<p>hbase.hregion.max.filesize默认值为10GB 。</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">  如果初始时R=<span class="hljs-number">1</span>,那么Min(<span class="hljs-number">128</span>MB,<span class="hljs-number">10</span>GB)=<span class="hljs-number">128</span>MB,也就是说在第一个flush的时候就会触发分裂操作。

  当R=<span class="hljs-number">2</span>的时候Min(<span class="hljs-number">2</span>*<span class="hljs-number">2</span>*<span class="hljs-number">128</span>MB,<span class="hljs-number">10</span>GB)=<span class="hljs-number">512</span>MB ,当某个store <span class="hljs-built_in">file</span>大小达到<span class="hljs-number">512</span>MB的时候，就会触发分裂。

  如此类推，当R=<span class="hljs-number">9</span>的时候，store <span class="hljs-built_in">file</span> 达到<span class="hljs-number">10</span>GB的时候就会分裂，也就是说当R&gt;=<span class="hljs-number">9</span>的时候，store <span class="hljs-built_in">file</span> 达到<span class="hljs-number">10</span>GB的时候就会分裂。</code></pre>

<p>split 点都位于region中row key的中间点。KeyPrefixRegionSplitPolicy可以保证相同的前缀的row保存在同一个region中。指定rowkey前缀位数划分region，通过读取 KeyPrefixRegionSplitPolicy.prefix_length  属性，该属性为数字类型，表示前缀长度，在进行split时，按此长度对splitPoint进行截取。此种策略比较适合固定前缀的rowkey。当table中没有设置该属性，指定此策略效果等同与使用IncreasingToUpperBoundRegionSplitPolicy。我们可以通过配置 hbase.regionserver.region.split.policy 来指定split策略，我们也可以写我们自己的split策略。</p>

<p>3 Forced Splits <br>
   当HBaseAdmin手动发起split时，也会触发split操作。 、</p>

<p><strong>split策略</strong></p>

<ol>
<li>采用ConstantSizeRegionSplitPolicy策略，即storefile固定大小策略：</li>
</ol>

<p>在0.94版本之前ConstantSizeRegionSplitPolicy 是默认和唯一的split策略。当某个storefile（对应一个columnfamily）的大小大于配置值 ‘hbase.hregion.max.filesize’的时候（默认DEFAULT_MAX_FILE_SIZE = 10 * 1024 * 1024 *1024L=10G）region就会自动分裂:</p>

<p>对应的源代码如下：</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-annotation">@Override</span>
  <span class="hljs-keyword">protected</span> <span class="hljs-keyword">void</span> <span class="hljs-title">configureForRegion</span>(HRegion region) {
    <span class="hljs-keyword">super</span>.configureForRegion(region);
    Configuration conf = getConf();
    HTableDescriptor desc = region.getTableDesc();
    <span class="hljs-keyword">if</span> (desc != <span class="hljs-keyword">null</span>) {
      <span class="hljs-keyword">this</span>.desiredMaxFileSize = desc.getMaxFileSize();
    }
    <span class="hljs-keyword">if</span> (<span class="hljs-keyword">this</span>.desiredMaxFileSize &lt;= <span class="hljs-number">0</span>) {
      <span class="hljs-keyword">this</span>.desiredMaxFileSize = conf.getLong(HConstants.HREGION_MAX_FILESIZE,
        HConstants.DEFAULT_MAX_FILE_SIZE);
    }
    <span class="hljs-keyword">double</span> jitter = conf.getDouble(<span class="hljs-string">"hbase.hregion.max.filesize.jitter"</span>, <span class="hljs-number">0.25</span>D);
    <span class="hljs-keyword">this</span>.jitterRate = (RANDOM.nextFloat() - <span class="hljs-number">0.5</span>D) * jitter;
    <span class="hljs-keyword">long</span> jitterValue = (<span class="hljs-keyword">long</span>) (<span class="hljs-keyword">this</span>.desiredMaxFileSize * <span class="hljs-keyword">this</span>.jitterRate);
    <span class="hljs-comment">// make sure the long value won't overflow with jitter</span>
    <span class="hljs-keyword">if</span> (<span class="hljs-keyword">this</span>.jitterRate &gt; <span class="hljs-number">0</span> &amp;&amp; jitterValue &gt; (Long.MAX_VALUE - <span class="hljs-keyword">this</span>.desiredMaxFileSize)) {
      <span class="hljs-keyword">this</span>.desiredMaxFileSize = Long.MAX_VALUE;
    } <span class="hljs-keyword">else</span> {
      <span class="hljs-keyword">this</span>.desiredMaxFileSize += jitterValue;
    }
  }

  <span class="hljs-annotation">@Override</span>
  <span class="hljs-keyword">protected</span> <span class="hljs-keyword">boolean</span> <span class="hljs-title">shouldSplit</span>() {
    <span class="hljs-keyword">boolean</span> force = region.shouldForceSplit();
    <span class="hljs-keyword">boolean</span> foundABigStore = <span class="hljs-keyword">false</span>;

    <span class="hljs-keyword">for</span> (Store store : region.getStores()) {
      <span class="hljs-comment">// If any of the stores are unable to split (eg they contain reference files)</span>
      <span class="hljs-comment">// then don't split</span>
      <span class="hljs-keyword">if</span> ((!store.canSplit())) {
        <span class="hljs-keyword">return</span> <span class="hljs-keyword">false</span>;
      }

      <span class="hljs-comment">// Mark if any store is big enough</span>
      <span class="hljs-keyword">if</span> (store.getSize() &gt; desiredMaxFileSize) {
        foundABigStore = <span class="hljs-keyword">true</span>;
      }
    }

    <span class="hljs-keyword">return</span> foundABigStore || force;
  }</code></pre>

<ol>
<li>采用IncreasingToUpperBoundRegionSplitPolicy策略，即根据region数来决定：</li>
</ol>

<p>0.94.0（包含）之后，默认采用此策略，即当同一table在同一regionserver上的region数量在[0,100)之间时按照如下的计算公式算，否则按照上一策略计算：</p>

<p>Min (R^2* “hbase.hregion.memstore.flush.size”*2, “hbase.hregion.max.filesize”)R为同一个table中在同一个regionserver中region的个数，hbase.hregion.memstore.flush.size默认为128M，hbase.hregion.max.filesize默认为10G。</p>



<pre class="prettyprint"><code class=" hljs java"> <span class="hljs-annotation">@Override</span>
  <span class="hljs-keyword">protected</span> <span class="hljs-keyword">void</span> <span class="hljs-title">configureForRegion</span>(HRegion region) {
    <span class="hljs-keyword">super</span>.configureForRegion(region);
    Configuration conf = getConf();
    initialSize = conf.getLong(<span class="hljs-string">"hbase.increasing.policy.initial.size"</span>, -<span class="hljs-number">1</span>);
    <span class="hljs-keyword">if</span> (initialSize &gt; <span class="hljs-number">0</span>) {
      <span class="hljs-keyword">return</span>;
    }
    HTableDescriptor desc = region.getTableDesc();
    <span class="hljs-keyword">if</span> (desc != <span class="hljs-keyword">null</span>) {
      initialSize = <span class="hljs-number">2</span> * desc.getMemStoreFlushSize();
    }
    <span class="hljs-keyword">if</span> (initialSize &lt;= <span class="hljs-number">0</span>) {
      initialSize = <span class="hljs-number">2</span> * conf.getLong(HConstants.HREGION_MEMSTORE_FLUSH_SIZE,
                                     HTableDescriptor.DEFAULT_MEMSTORE_FLUSH_SIZE);
    }
  }

  <span class="hljs-annotation">@Override</span>
  <span class="hljs-keyword">protected</span> <span class="hljs-keyword">boolean</span> <span class="hljs-title">shouldSplit</span>() {
    <span class="hljs-keyword">boolean</span> force = region.shouldForceSplit();
    <span class="hljs-keyword">boolean</span> foundABigStore = <span class="hljs-keyword">false</span>;
    <span class="hljs-comment">// Get count of regions that have the same common table as this.region</span>
    <span class="hljs-keyword">int</span> tableRegionsCount = getCountOfCommonTableRegions();
    <span class="hljs-comment">// Get size to check</span>
    <span class="hljs-keyword">long</span> sizeToCheck = getSizeToCheck(tableRegionsCount);

    <span class="hljs-keyword">for</span> (Store store : region.getStores()) {
      <span class="hljs-comment">// If any of the stores is unable to split (eg they contain reference files)</span>
      <span class="hljs-comment">// then don't split</span>
      <span class="hljs-keyword">if</span> (!store.canSplit()) {
        <span class="hljs-keyword">return</span> <span class="hljs-keyword">false</span>;
      }

      <span class="hljs-comment">// Mark if any store is big enough</span>
      <span class="hljs-keyword">long</span> size = store.getSize();
      <span class="hljs-keyword">if</span> (size &gt; sizeToCheck) {
        LOG.debug(<span class="hljs-string">"ShouldSplit because "</span> + store.getColumnFamilyName() + <span class="hljs-string">" size="</span> + size
                  + <span class="hljs-string">", sizeToCheck="</span> + sizeToCheck + <span class="hljs-string">", regionsWithCommonTable="</span>
                  + tableRegionsCount);
        foundABigStore = <span class="hljs-keyword">true</span>;
      }
    }

    <span class="hljs-keyword">return</span> foundABigStore | force;
  }

  <span class="hljs-javadoc">/**
   *<span class="hljs-javadoctag"> @return</span> Count of regions on this server that share the table this.region
   * belongs to
   */</span>
  <span class="hljs-keyword">private</span> <span class="hljs-keyword">int</span> <span class="hljs-title">getCountOfCommonTableRegions</span>() {
    RegionServerServices rss = region.getRegionServerServices();
    <span class="hljs-comment">// Can be null in tests</span>
    <span class="hljs-keyword">if</span> (rss == <span class="hljs-keyword">null</span>) {
      <span class="hljs-keyword">return</span> <span class="hljs-number">0</span>;
    }
    TableName tablename = region.getTableDesc().getTableName();
    <span class="hljs-keyword">int</span> tableRegionsCount = <span class="hljs-number">0</span>;
    <span class="hljs-keyword">try</span> {
      List&lt;Region&gt; hri = rss.getOnlineRegions(tablename);
      tableRegionsCount = hri == <span class="hljs-keyword">null</span> || hri.isEmpty() ? <span class="hljs-number">0</span> : hri.size();
    } <span class="hljs-keyword">catch</span> (IOException e) {
      LOG.debug(<span class="hljs-string">"Failed getOnlineRegions "</span> + tablename, e);
    }
    <span class="hljs-keyword">return</span> tableRegionsCount;
  }
</code></pre>

<p>两种用户自定义策略：KeyPrefixRegionSplitPolicy和DelimitedKeyPrefixRegionSplitPolicy，这两种策略是IncreasingToUpperBoundRegionSplitPolicy策略的具体实现。</p>

<p><strong>KeyPrefixRegionSplitPolicy</strong></p>

<p>KeyPrefixRegionSplitPolicy策略，即根据rowkey指定长度的前缀来划分region：</p>

<p>即保证相同的前缀的row保存在同一个region中。指定rowkey前缀位数划分region，通过读取 KeyPrefixRegionSplitPolicy.prefix_length 属性，该属性为数字类型，表示前缀长度，在进行split时，按此长度对splitPoint进行截取。此种策略比较适合固定前缀的rowkey。当table中没有设置该属性，指定此策略效果等同与使用IncreasingToUpperBoundRegionSplitPolicy。</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-annotation">@Override</span>
  <span class="hljs-keyword">protected</span> <span class="hljs-keyword">void</span> <span class="hljs-title">configureForRegion</span>(HRegion region) {
    <span class="hljs-keyword">super</span>.configureForRegion(region);
    prefixLength = <span class="hljs-number">0</span>;

    <span class="hljs-comment">// read the prefix length from the table descriptor</span>
    String prefixLengthString = region.getTableDesc().getValue(
        PREFIX_LENGTH_KEY);
    <span class="hljs-keyword">if</span> (prefixLengthString == <span class="hljs-keyword">null</span>) {
      <span class="hljs-comment">//read the deprecated value</span>
      prefixLengthString = region.getTableDesc().getValue(PREFIX_LENGTH_KEY_DEPRECATED);
      <span class="hljs-keyword">if</span> (prefixLengthString == <span class="hljs-keyword">null</span>) {
        LOG.error(PREFIX_LENGTH_KEY + <span class="hljs-string">" not specified for table "</span>
            + region.getTableDesc().getTableName()
            + <span class="hljs-string">". Using default RegionSplitPolicy"</span>);
        <span class="hljs-keyword">return</span>;
      }
    }
    <span class="hljs-keyword">try</span> {
      prefixLength = Integer.parseInt(prefixLengthString);
    } <span class="hljs-keyword">catch</span> (NumberFormatException nfe) {
      <span class="hljs-comment">/* Differentiate NumberFormatException from an invalid value range reported below. */</span>
      LOG.error(<span class="hljs-string">"Number format exception when parsing "</span> + PREFIX_LENGTH_KEY + <span class="hljs-string">" for table "</span>
          + region.getTableDesc().getTableName() + <span class="hljs-string">":"</span>
          + prefixLengthString + <span class="hljs-string">". "</span> + nfe);
      <span class="hljs-keyword">return</span>;
    }
    <span class="hljs-keyword">if</span> (prefixLength &lt;= <span class="hljs-number">0</span>) {
      LOG.error(<span class="hljs-string">"Invalid value for "</span> + PREFIX_LENGTH_KEY + <span class="hljs-string">" for table "</span>
          + region.getTableDesc().getTableName() + <span class="hljs-string">":"</span>
          + prefixLengthString + <span class="hljs-string">". Using default RegionSplitPolicy"</span>);
    }
  }</code></pre>

<p><strong>DelimitedKeyPrefixRegionSplitPolicy策略</strong></p>

<p>保证以分隔符前面的前缀为splitPoint，保证相同RowKey前缀的数据在一个Region中。</p>



<pre class="prettyprint"><code class=" hljs java"> <span class="hljs-annotation">@Override</span>
  <span class="hljs-keyword">protected</span> <span class="hljs-keyword">void</span> <span class="hljs-title">configureForRegion</span>(HRegion region) {
    <span class="hljs-keyword">super</span>.configureForRegion(region);
    <span class="hljs-comment">// read the prefix length from the table descriptor</span>
    String delimiterString = region.getTableDesc().getValue(DELIMITER_KEY);
    <span class="hljs-keyword">if</span> (delimiterString == <span class="hljs-keyword">null</span> || delimiterString.length() == <span class="hljs-number">0</span>) {
      LOG.error(DELIMITER_KEY + <span class="hljs-string">" not specified for table "</span> + region.getTableDesc().getTableName() +
        <span class="hljs-string">". Using default RegionSplitPolicy"</span>);
      <span class="hljs-keyword">return</span>;
    }
    delimiter = Bytes.toBytes(delimiterString);
  }

  <span class="hljs-annotation">@Override</span>
  <span class="hljs-keyword">protected</span> <span class="hljs-keyword">byte</span>[] <span class="hljs-title">getSplitPoint</span>() {
    <span class="hljs-keyword">byte</span>[] splitPoint = <span class="hljs-keyword">super</span>.getSplitPoint();
    <span class="hljs-keyword">if</span> (splitPoint != <span class="hljs-keyword">null</span> &amp;&amp; delimiter != <span class="hljs-keyword">null</span>) {

      <span class="hljs-comment">//find the first occurrence of delimiter in split point</span>
      <span class="hljs-keyword">int</span> index = com.google.common.primitives.Bytes.indexOf(splitPoint, delimiter);
      <span class="hljs-keyword">if</span> (index &lt; <span class="hljs-number">0</span>) {
        LOG.warn(<span class="hljs-string">"Delimiter "</span> + Bytes.toString(delimiter) + <span class="hljs-string">"  not found for split key "</span>
            + Bytes.toString(splitPoint));
        <span class="hljs-keyword">return</span> splitPoint;
      }

      <span class="hljs-comment">// group split keys by a prefix</span>
      <span class="hljs-keyword">return</span> Arrays.copyOf(splitPoint, Math.min(index, splitPoint.length));
    } <span class="hljs-keyword">else</span> {
      <span class="hljs-keyword">return</span> splitPoint;
    }
  }</code></pre>

<p><strong>HBase split的流程</strong></p>

<p><img src="https://img-blog.csdn.net/20170704103757672?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDAzOTkyOQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>1.RegionServer触发在本地进行split，并准备split。第一步就是在zk的/hbase/region-in-transition/region-name的节点下创建一个znode节点，并置为SPLITTING状态；</p>

<p>2.因为Master一直watch着zk的znode，发现parentregion需要split。</p>

<p>3.region server  在hdfs的parent region的目录下创建一个名为“.splits”的子目录。</p>

<p>4.region server关闭parent region。强制flush缓存，并且在本地数据结构中标记region为下线状态。如果这个时候客户端刚好请求到parent region，会抛出NotServingRegionException。这时客户端会进行重试。</p>

<p>5.region server在.split目录下分别为两个daughter region（A,B）创建目录和必要的数据结构。然后创建两个引用文件指向parent regions的文件。</p>

<p>6.region server在HDFS中，创建真正的region目录，并且把引用文件移到对应的目录下。</p>

<p>7.region server发送一个put的请求到.META.表中，并且在.META.表中设置parent region为下线状态,并添加关于daughter regions的信息。但是这个时候在.META.表中daughter region 还不是独立的row，客户端在此时scan .META.表时会发现parent region在split，但是还不能获得daughter region的信息，直到她们独立的出现.META.表中。如果此时这个往.META.表中的put操作成功，parent region会高效的split，如果此时rs在RPC请求成功前失败，Master和下一个regionserver会重新打开这个parent region并将之前产生的split的脏数据清掉，.META.表成功更新后，HBase继续进行下面split的流程。</p>

<p>8.region server并行打开两个daughter region接受写操作。</p>

<p>9.region server在.META.表中增加daughters A和 B  region的相关信息，在这以后，client就能发现这两个新的regions并且能发送请求到这两个新的region了。client本地具体有.META.表的缓存，当他们访问到parent region的时候，发现parent region下线了，就会重新访问.META.表获取最新的信息，并且更新本地缓存。</p>

<p>10.region server 更新znode 的状态为SPLIT。master就能知道状态更新了，master的平衡机制会判断是否需要把daughter regions 分配到其他region server中。</p>

<p>11.在split之后，meta和HDFS依然会有引用指向parentregion.当compact操作发生在daughter regions中，会重写数据file，这个时候引用就会被逐渐的去掉。GC任务会定时检测daughter regions是否还有引用指向parent files，如果没有引用指向parent files的话，parent region 就会被删除。</p>

<p><strong>HBase split的过程分析</strong></p>

<p><img src="https://img-blog.csdn.net/20170704104131299?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDAzOTkyOQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>解析：</p>

<p>分析Hbase的split流程，先从RegionServer中的相应线程作为突破口，依次分析split的触发条件和split的执行实现。</p>

<p>split的触发条件：</p>

<p>在hbase的regionserver中维护着一个CompactSplitThread类型的变量，所有的compact/split请求都会交给该变量处理，以下是CompactSplitThread中定义的几个与split相关的变量。</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">private</span> <span class="hljs-keyword">final</span> ThreadPoolExecutor longCompactions;     <span class="hljs-comment">//long合并线程池  </span>
<span class="hljs-keyword">private</span> <span class="hljs-keyword">final</span> ThreadPoolExecutor shortCompactions;    <span class="hljs-comment">//short合并线程池  </span>
<span class="hljs-keyword">private</span> <span class="hljs-keyword">final</span> ThreadPoolExecutor splits;              <span class="hljs-comment">//split线程池  </span>
<span class="hljs-keyword">private</span> <span class="hljs-keyword">final</span> ThreadPoolExecutor mergePool;           <span class="hljs-comment">//merge线程池  </span>
<span class="hljs-keyword">private</span> <span class="hljs-keyword">int</span> regionSplitLimit;  </code></pre>

<p>需要注意的是最后一个整型变量regionSplitLimit，它定义了一个regionserver所持有的最大region总数，如果region的数量超过了它的限制，则split不会再发生。该变量的值由hbase.regionserver.regionSplitLimit配置，默认是1000；splits是该regionserver用于参与split的线程池，线程池中的线程数量由“hbase.regionserver.thread.split”配置，默认是1。</p>

<p>region发生split时，调用方会调用CompactSplitThread中的requestSplit方法，该方案将split请求包装成一个Runnable的SplitRequest对象放入前文所说的线程池split中去执行。</p>



<pre class="prettyprint"><code class=" hljs axapta">    <span class="hljs-keyword">this</span>.splits.execute(<span class="hljs-keyword">new</span> SplitRequest(r, midKey, <span class="hljs-keyword">this</span>.<span class="hljs-keyword">server</span>));  </code></pre>

<p>分析requestSplit方法的调用时机就可以理出region发生split行为的时间：</p>

<p>第一种方式是region发生compact之后，此时会形成比较大的文件，split会在这个时候被调用；</p>

<p>第二种方式是region发生flush的时候，这一部分的代码如下：</p>



<pre class="prettyprint"><code class=" hljs axapta">    lock.readLock().lock();                 
    <span class="hljs-keyword">try</span> {  
      notifyFlushRequest(region, emergencyFlush);  
      FlushResult flushResult = region.flush(forceFlushAllStores);  
      <span class="hljs-keyword">boolean</span> shouldCompact = flushResult.isCompactionNeeded();  
      <span class="hljs-comment">// We just want to check the size  </span>
      <span class="hljs-keyword">boolean</span> shouldSplit = ((HRegion)region).checkSplit() != <span class="hljs-keyword">null</span>;  
      <span class="hljs-keyword">if</span> (shouldSplit) {  
        <span class="hljs-keyword">this</span>.<span class="hljs-keyword">server</span>.compactSplitThread.requestSplit(region);          
      } <span class="hljs-keyword">else</span> <span class="hljs-keyword">if</span> (shouldCompact) {  
        <span class="hljs-keyword">server</span>.compactSplitThread.requestSystemCompaction(  
            region, Thread.currentThread().getName());               
      }  
      <span class="hljs-keyword">if</span> (flushResult.isFlushSucceeded()) {  
        <span class="hljs-keyword">long</span> endTime = EnvironmentEdgeManager.currentTime();  
        <span class="hljs-keyword">server</span>.metricsRegionServer.updateFlushTime(endTime - startTime);      <span class="hljs-comment">//将本次flush的时间记录下来  </span>
      }  
    } <span class="hljs-keyword">catch</span> (DroppedSnapshotException ex) {  
      ..........  
    } <span class="hljs-keyword">catch</span> (IOException ex) {  
      ..........  
    } <span class="hljs-keyword">finally</span> {  
      lock.readLock().unlock();  
      wakeUpIfBlocking();           <span class="hljs-comment">//唤醒所有等待的线程  </span>
    }  </code></pre>

<p>需要注意，region上的memstore发生flush的时候会获取readLock。readLock是读锁，读写锁是一种多线程同步的方案，所谓读锁其实就是共享锁，所谓写锁就是排他锁，当一个线程获取读锁时，所有其他以读模式访问的线程都可以获得访问权，而已写模式对它进行加锁的线程都会被阻塞。而当一个线程获取写锁的时候，所有其他试图获取锁的线程都会被阻塞。这里获取了readLock，所以flush时对region的更新都会被阻塞。</p>

<p>第三种调用是在RSRpcServices的splitRegion函数中，表示的是用户对region发出的split请求。</p>

<p>split的执行实现：</p>

<p>前面说过spilt请求会包装成SplitRequest类型的对象交由splits线程处理。所以具体的split实现是在SplitRequest的run方法中，下面我们筛选出run方法的重点流程以分析split的实现：</p>



<pre class="prettyprint"><code class=" hljs axapta">    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> run() {  
        .......  
        SplitTransactionImpl st = <span class="hljs-keyword">new</span> SplitTransactionImpl(parent, midKey);  
        <span class="hljs-keyword">try</span> {  
           tableLock = <span class="hljs-keyword">server</span>.getTableLockManager().readLock(.........);  
           <span class="hljs-keyword">try</span> {  
            tableLock.acquire();  
           } <span class="hljs-keyword">catch</span> (IOException ex) {  
              .......  
           }  
           <span class="hljs-keyword">if</span> (!st.prepare()) <span class="hljs-keyword">return</span>;  
           <span class="hljs-keyword">try</span> {  
              st.execute(<span class="hljs-keyword">this</span>.<span class="hljs-keyword">server</span>, <span class="hljs-keyword">this</span>.<span class="hljs-keyword">server</span>);  
           } <span class="hljs-keyword">catch</span> (Exception e) {  
              .......  
           }  
        } <span class="hljs-keyword">catch</span> (Exception e) {  
           <span class="hljs-keyword">server</span>.checkFileSystem();  
        } <span class="hljs-keyword">finally</span> {  
           <span class="hljs-comment">//处理post coprocessor  </span>
           releaseTableLock();  
           <span class="hljs-comment">//更新metrics  </span>
        }  
    }  </code></pre>

<p>除了一些异常处理和回滚，run方法的主要逻辑已经梳理出来了，可以看出split前首先获取了table的共享锁，这样做的目的是防止其它并发的table修改行为修改当前table的状态或者schema等。然后初始化一个SplitTransactionImpl类型的变量，依次调用它的prepare和execute方法完成region切割。 <br>
perpare用以完成split的前期准备，包括构造两个子HRegionInfo，分别是hri_a和hri_b，其中hri_a的startKey胃parent的startKey，endKey为midKey；hri_b的startKey为midKey，endKey为parent的endKey。</p>

<p>接下来在execute方法中，主要代码包括以下三步：</p>



<pre class="prettyprint"><code class=" hljs axapta">    PairOfSameType&lt;Region&gt; regions = createDaughters(<span class="hljs-keyword">server</span>, services);  
    <span class="hljs-keyword">if</span> (<span class="hljs-keyword">this</span>.parent.getCoprocessorHost() != <span class="hljs-keyword">null</span>) {  
       <span class="hljs-keyword">this</span>.parent.getCoprocessorHost().preSplitAfterPONR();  
    }  
    regions = stepsAfterPONR(<span class="hljs-keyword">server</span>, services, regions);  
    transition(SplitTransactionPhase.COMPLETED);  </code></pre>

<p>从createDaughters方法进去，我们一点点分析split的实现，其中比较关键的步骤是createDaughters，我们把该方法的主要逻辑列出在下面：</p>



<pre class="prettyprint"><code class=" hljs avrasm">    PairOfSameType&lt;Region&gt; daughterRegions = stepsBeforePONR(server, services, testing)<span class="hljs-comment">;  </span>
    List&lt;Mutation&gt; metaEntries = new ArrayList&lt;&gt;<span class="hljs-comment">;  </span>
    if (!testing &amp;&amp; useZKForAssignment) {  
        if (metaEntries == null || metaEntries<span class="hljs-preprocessor">.isEmpty</span>()) {  
           MetaTableAccessor<span class="hljs-preprocessor">.splitRegion</span>(server<span class="hljs-preprocessor">.getConnection</span>(),  
             parent<span class="hljs-preprocessor">.getRegionInfo</span>(), daughterRegions<span class="hljs-preprocessor">.getFirst</span>()<span class="hljs-preprocessor">.getRegionInfo</span>(),  
             daughterRegions<span class="hljs-preprocessor">.getSecond</span>()<span class="hljs-preprocessor">.getRegionInfo</span>(), server<span class="hljs-preprocessor">.getServerName</span>(),  
             parent<span class="hljs-preprocessor">.getTableDesc</span>()<span class="hljs-preprocessor">.getRegionReplication</span>())<span class="hljs-comment">;  </span>
        } else {  
           offlineParentInMetaAndputMetaEntries(server<span class="hljs-preprocessor">.getConnection</span>(),  
             parent<span class="hljs-preprocessor">.getRegionInfo</span>(), daughterRegions<span class="hljs-preprocessor">.getFirst</span>()<span class="hljs-preprocessor">.getRegionInfo</span>(), daughterRegions  
                  <span class="hljs-preprocessor">.getSecond</span>()<span class="hljs-preprocessor">.getRegionInfo</span>(), server<span class="hljs-preprocessor">.getServerName</span>(), metaEntries,  
                  parent<span class="hljs-preprocessor">.getTableDesc</span>()<span class="hljs-preprocessor">.getRegionReplication</span>())<span class="hljs-comment">;  </span>
        }  
    } else if (services != null &amp;&amp; !useZKForAssignment) {  
        ..........  
    }  </code></pre>

<p>createDaughters返回的regions实际上是由stepsBeforePONR返回的，下面我们列出stepsBeforePONR中的主要逻辑：</p>



<pre class="prettyprint"><code class=" hljs lasso">    this<span class="hljs-built_in">.</span><span class="hljs-keyword">parent</span><span class="hljs-built_in">.</span>getRegionFileSystem()<span class="hljs-built_in">.</span>createSplitsDir();  
    try {  
       hstoreFilesToSplit <span class="hljs-subst">=</span> this<span class="hljs-built_in">.</span><span class="hljs-keyword">parent</span><span class="hljs-built_in">.</span>close(<span class="hljs-literal">false</span>);  
    } catch (Exception e) {  
       <span class="hljs-attribute">...</span><span class="hljs-attribute">...</span><span class="hljs-attribute">...</span>  
    }  
    Pari<span class="hljs-subst">&lt;</span><span class="hljs-built_in">Integer</span>,<span class="hljs-built_in">Integer</span><span class="hljs-subst">&gt;</span> expectedReferences <span class="hljs-subst">=</span> splitStoreFiles(hstoreFilesToSplit);  
    Region a <span class="hljs-subst">=</span> this<span class="hljs-built_in">.</span><span class="hljs-keyword">parent</span><span class="hljs-built_in">.</span>createDaughterRegionFromSplits(this<span class="hljs-built_in">.</span>hri_a);  
    Region b <span class="hljs-subst">=</span> this<span class="hljs-built_in">.</span><span class="hljs-keyword">parent</span><span class="hljs-built_in">.</span>createDaughterRegionFromSplits(this<span class="hljs-built_in">.</span>hri_b);  
    <span class="hljs-keyword">return</span> <span class="hljs-literal">new</span> PairOfSametype<span class="hljs-subst">&lt;</span>Region<span class="hljs-subst">&gt;</span>(a,b);  </code></pre>

<p>上面我们列出了几个主要步骤，穿插在这些主要步骤之间的是将当前split的状态更新到zookeeper的节点上，hbase的region在发生split的同时，会在zookeeper的region-in-transition目录下创建一个节点，供master同步监听新region的上线和老region的下线这些信息，此外如果split中间发生错误，也需要zookeeper上的状态信息同步以协调region之间的变化。</p>

<p>首先createSplitsDir在parent region的目录下创建了一个.split目录，接着在.split目录下创建daughter region A和region B两个子目录，然后调用close将parent关掉，调用是传入的参数false表示在关掉parent region前先强制执行一次flush，将region memstore中的数据刷写到磁盘。关闭region后region server将region标记为offline状态。</p>

<p>region的关闭在实现上是提交了该region拥有的store到一个线程池中，然后每个store的close方法进行关闭的，store的关闭结果异步获取。hbase在实现这一步中使用了CompletionService，返回结果通过CompletionService的take方法获取，使用这种方式的优势就是当多线程启动了多个Callable之后，每个callable都会返回一个future，CompletionService自己维护了一个线程安全的list，保证先完成的future一定先返回。</p>

<p>现在回来继续讲解我们的split流程，进入splitStoreFiles方法，该方法实际上是为parent中的每个storeFile创建了两个索引文件，核心代码在下面的片段中：</p>



<pre class="prettyprint"><code class=" hljs php">    ThreadFactoryBuilder builder = <span class="hljs-keyword">new</span> ThreadFactoryBuilder();  
    builder.setNameFormat(<span class="hljs-string">"StoreFileSplitter-%1$d"</span>);  
    ThreadFactory factory = builder.build();  
    ThreadPoolExecutor threadPool =  
       (ThreadPoolExecutor) Executors.newFixedThreadPool(maxThreads, factory);  
    <span class="hljs-keyword">List</span>&lt;Future&lt;Pair&lt;Path,Path&gt;&gt;&gt; futures = <span class="hljs-keyword">new</span> ArrayList&lt;Future&lt;Pair&lt;Path,Path&gt;&gt;&gt; (nbFiles);  

    <span class="hljs-comment">// Split each store file.  </span>
    <span class="hljs-keyword">for</span> (Map.Entry&lt;byte[], <span class="hljs-keyword">List</span>&lt;StoreFile&gt;&gt; entry: hstoreFilesToSplit.entrySet()) {  
       <span class="hljs-keyword">for</span> (StoreFile sf: entry.getValue()) {  
          StoreFileSplitter sfs = <span class="hljs-keyword">new</span> StoreFileSplitter(entry.getKey(), sf);  
          futures.add(threadPool.submit(sfs));         <span class="hljs-comment">//storefile被提交执行split了  </span>
       }  
    }  </code></pre>

<p>StoreFileSpliitter最终调用HRegionFileSystem中的下面一句代码完成索引文件的创建：</p>



<pre class="prettyprint"><code class=" hljs oxygene">    <span class="hljs-keyword">Reference</span> r =  
          top ? <span class="hljs-keyword">Reference</span>.createTopReference(splitRow): <span class="hljs-keyword">Reference</span>.createBottomReference(splitRow);  </code></pre>

<p>回到stepsBeforePONR方法，最后两步根据子region的元信息创建了HRegion A和B，实际上就是创建了A/B的实际存储目录。完成这些后stepsBeforePONR方法返回，然后调用如下的代码修改meta表中parent region和分裂出的region A和region B的信息。</p>



<pre class="prettyprint"><code class=" hljs avrasm">MetaTableAccessor<span class="hljs-preprocessor">.splitRegion</span>  </code></pre>

<p>splitRegion是一个原子方法，它将父region的信息置为offline，并写入子region的信息，但是此时的子region A和B还不能对外提供服务。需要等待regionServer打开该子region才可以，带着这个疑问，我们进入split流程的最后一个方法——stepsAfterPORN，在该方法中调用openDaughters将子RegionA和RegionB打开以接受写请求，regionsrver打开A和B之后会补充上述两个子region在.META.表中的信息，此时客户端才能够发现两个子region并向该两个region发送请求。</p>

<p>负责open region的线程是继承了HasThread接口的DaughterOpener类，主要包括了下面两个步骤：</p>

<p>1&gt; 调用openHRegion函数的initilize，主要步骤如下：</p>

<pre><code>a、向hdfs上写入.regionInfo文件以便meta挂掉时恢复；

b、初始化其下的HStore，主要是调LoadStoreFiles函数；
</code></pre>

<p>2&gt; 将子Region添加到rs的online region列表上，并添加到meta表中； <br>
最后更新zk节点上/hbase/region-in-transition/region-name节点的状态为COMPLETED，指示split结束。</p>

<p>Split过程结束后，HDFS和META中还会保留着指向parent region索引文件的信息，这些索引文件会在子region执行major compact重写的时候被删除掉。master的Garbage Collection任务会周期性地检查子region中是否还包含指向parents Region的索引文件，如果不再包含，此时master会将parent Region删除掉。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>