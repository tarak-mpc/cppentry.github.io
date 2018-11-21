---
layout:     post
title:      使用Kafka、Elasticsearch、Grafana搭建业务监控系统（三）Elasticsearch
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-tomorrow-night-eighties">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2><a id="_0"></a>系列目录</h2>
<p><a href="https://blog.csdn.net/tonywu1992/article/details/83448286" rel="nofollow">使用Kafka、Elasticsearch、Grafana搭建业务监控系统（一）技术选择</a><br>
<a href="https://blog.csdn.net/tonywu1992/article/details/83506671" rel="nofollow">使用Kafka、Elasticsearch、Grafana搭建业务监控系统（二）Kafka</a><br>
<a href="https://blog.csdn.net/tonywu1992/article/details/83576863" rel="nofollow">使用Kafka、Elasticsearch、Grafana搭建业务监控系统（三）Elasticsearch</a><br>
使用Kafka、Elasticsearch、Grafana搭建业务监控系统（四）Grafana（填坑ing）</p>
<h2><a id="Elasticsearch_6"></a>一、Elasticsearch是什么</h2>
<p>如果之前学习过ES基础知识可以跳过，直接看spring整合ES</p>
<blockquote>
<p>Elasticsearch是一个基于Apache Lucene™的开源搜索引擎。无论在开源还是专有领域，Lucene可以被认为是迄今为止最先进、性能最好的、功能最全的搜索引擎库。<br>
但是，Lucene只是一个库。想要使用它，你必须使用Java来作为开发语言并将其直接集成到你的应用中，更糟糕的是，Lucene非常复杂，你需要深入了解检索的相关知识来理解它是如何工作的。<br>
Elasticsearch也使用Java开发并使用Lucene作为其核心来实现所有索引和搜索的功能，但是它的目的是通过简单的RESTful API来隐藏Lucene的复杂性，从而让全文搜索变得简单。<br>
不过，Elasticsearch不仅仅是Lucene和全文搜索，我们还能这样去描述它：</p>
<ul>
<li>分布式的实时文件存储，每个字段都被索引并可被搜索</li>
<li>分布式的实时分析搜索引擎</li>
<li>可以扩展到上百台服务器，处理PB级结构化或非结构化数据</li>
</ul>
<p>而且，所有的这些功能被集成到一个服务里面，你的应用可以通过简单的RESTful API、各种语言的客户端甚至命令行与之交互。<br>
上手Elasticsearch非常容易。它提供了许多合理的缺省值，并对初学者隐藏了复杂的搜索引擎理论。它开箱即用（安装即可使用），只需很少的学习既可在生产环境中使用。<br>
Elasticsearch在Apache 2 license下许可使用，可以免费下载、使用和修改。<br>
随着你对Elasticsearch的理解加深，你可以根据不同的问题领域定制Elasticsearch的高级特性，这一切都是可配置的，并且配置非常灵活。</p>
</blockquote>
<p>简单来说，Elasticsearch就是一个基于Lucene库封装的分布式、可扩展、实时的搜索与数据分析引擎。隐藏了 Lucene 的复杂性，取而代之的提供一套简单一致的 RESTful API。</p>
<p>这里穿插一个Elasticsearch的小故事：</p>
<blockquote>
<p>许多年前，一个刚结婚的名叫 Shay Banon 的失业开发者，跟着他的妻子去了伦敦，他的妻子在那里学习厨师。 在寻找一个赚钱的工作的时候，为了给他的妻子做一个食谱搜索引擎，他开始使用 Lucene 的一个早期版本。<br>
直接使用 Lucene 是很难的，因此 Shay 开始做一个抽象层，Java 开发者使用它可以很简单的给他们的程序添加搜索功能。 他发布了他的第一个开源项目 Compass。<br>
后来 Shay 获得了一份工作，主要是高性能，分布式环境下的内存数据网格。这个对于高性能，实时，分布式搜索引擎的需求尤为突出， 他决定重写 Compass，把它变为一个独立的服务并取名 Elasticsearch。<br>
第一个公开版本在2010年2月发布，从此以后，Elasticsearch 已经成为了 Github 上最活跃的项目之一，他拥有超过300名 contributors(目前736名 contributors )。 一家公司已经开始围绕 Elasticsearch 提供商业服务，并开发新的特性，但是，Elasticsearch 将永远开源并对所有人可用。<br>
据说，Shay 的妻子还在等着她的食谱搜索引擎…</p>
</blockquote>
<p>好了，言归正传，我们先了解一下ES的几个基本概念：</p>
<h6><a id="cluster_33"></a>集群（cluster）</h6>
<p>一个集群就是由一个或多个节点组织在一起， 它们共同持有你全部的数据， 并一起提供索引和搜索功能。 一个集群由一个唯一的名字标识， <strong><mark>这个名字默认就是“elasticsearch”</mark></strong>。 这个名字很重要， 因为一个节点只能通过指定某个集群的名字，来加入这个集群。在生产环境中显式地设定这个名字是一个好习惯，但是使用默认值来进行测试/开发也是不错的。</p>
<p>注意，一个集群中只包含一个节点是合法的。另外，你也可以拥有多个集群，集群以名字区分。</p>
<hr>
<h6><a id="node_38"></a>节点（node）</h6>
<p>一个节点是你集群中的一个服务器，作为集群的一部分，它存储你的数据，参与集群的索引和搜索功能。 和集群类似， 一个节点也是由一个名字来标识的， 默认情况下， 这个名字是一个随机的Marvel角色的名字，这个名字会在节点启动时分配给它。这个名字对于管理工作来说很重要，因为在这个管理过程中，你会去确定网络中的哪些 服务器对应于Elasticsearch集群中的哪些节点。</p>
<p>一个节点可以通过配置集群名称的方式来加入一个指定的集群。 <strong><mark>默认情况下，每个节点都会被安排加入到一个叫做“elasticsearch”的集群中</mark></strong>，这意味着，如果你在你的网络中启动了若干个节点， 并假定它们能够相互发现彼此，它们将会自动地形成并加入到一个叫做“elasticsearch” 的集群中。</p>
<p>在一个集群里可以拥有任意多个节点。而且，如果当前你的网络中没有运行任何Elasticsearch节点，这时启动一个节点，会默认创建并加入一个叫做“elasticsearch”的单节点集群。</p>
<hr>
<h6><a id="index_45"></a>索引（index）</h6>
<p>一个索引就是一个拥有相似特征的文档的集合。比如说，你可以有一个客户数据的索引，另一个产品目录的索引，还有一个订单数据的索引。一个索引由一个名字来 标识（<mark><strong>必须全部是小写字母的</strong></mark>），并且当我们要对这个索引中的文档进行索引、搜索、更新和删除的时候，都要使用到这个名字。在一个集群中，你能够创建任意多个索引。</p>
<hr>
<h6><a id="type_48"></a>类型（type）</h6>
<p>在一个索引中，你可以定义一种或多种类型。一个类型是你的索引的一个逻辑上的分类/分区，其语义完全由你来定。通常，会为具有一组相同字段的文档定义一个类型。比如说，我们假设你运营一个博客平台 并且将你所有的数据存储到一个索引中。在这个索引中，你可以为用户数据定义一个类型，为博客数据定义另一个类型，当然，也可以为评论数据定义另一个类型。</p>
<hr>
<h6><a id="document_51"></a>文档（document）</h6>
<p>一个文档是一个可被索引的基础信息单元。比如，你可以拥有某一个客户的文档、某一个产品的一个文档、某个订单的一个文档。文档以JSON格式来表示，而JSON是一个到处存在的互联网数据交互格式。</p>
<p>在一个index/type里面，你可以存储任意多的文档。注意，一个文档物理上存在于一个索引之中，但文档必须被索引/赋予一个索引的type。</p>
<hr>
<h6><a id="shards_and_replicas_56"></a>分片和复制（shards and replicas）</h6>
<p>一个索引可以存储超出单个结点硬件限制的大量数据。比如，一个具有10亿文档的索引占据1TB的磁盘空间，而任一节点可能没有这样大的磁盘空间来存储或者单个节点处理搜索请求，响应会太慢。</p>
<p>为了解决这个问题，Elasticsearch提供了将索引划分成多片的能力，这些片叫做分片。当你创建一个索引的时候，你可以指定你想要的分片的数量。每个分片本身也是一个功能完善并且独立的“索引”，这个“索引” 可以被放置到集群中的任何节点上。</p>
<p>分片之所以重要，主要有两方面的原因：</p>
<ul>
<li>允许你水平分割/扩展你的内容容量</li>
<li>允许你在分片（位于多个节点上）之上进行分布式的、并行的操作，进而提高性能/吞吐量</li>
</ul>
<p>至于一个分片怎样分布，它的文档怎样聚合回搜索请求，是完全由Elasticsearch管理的，对于作为用户的你来说，这些都是透明的。</p>
<p>在一个网络/云的环境里，失败随时都可能发生。在某个分片/节点因为某些原因处于离线状态或者消失的情况下，故障转移机制是非常有用且强烈推荐的。为此， Elasticsearch允许你创建分片的一份或多份拷贝，这些拷贝叫做复制分片，或者直接叫复制。</p>
<p>复制之所以重要，有两个主要原因：</p>
<ul>
<li>在分片/节点失败的情况下，复制提供了高可用性。复制分片不与原/主要分片置于同一节点上是非常重要的。</li>
<li>因为搜索可以在所有的复制上并行运行，复制可以扩展你的搜索量/吞吐量<br>
总之，每个索引可以被分成多个分片。一个索引也可以被复制0次（即没有复制） 或多次。一旦复制了，每个索引就有了主分片（作为复制源的分片）和复制分片（主分片的拷贝）。 分片和复制的数量可以在索引创建的时候指定。在索引创建之后，你可以在任何时候动态地改变复制的数量，但是你不能再改变分片的数量。</li>
</ul>
<p>默认情况下，Elasticsearch中的每个索引分配<mark>5个主分片</mark>和1个复制。这意味着，如果你的集群中至少有两个节点，你的索引将会有5个主分片和另外5个复制分片（1个完全拷贝），这样每个索引总共就有10个分片。</p>
<hr>
<p>我们用关系型数据库Mysql类比Elasticsearch，可以更好的理解ES<br>
<img src="https://img-blog.csdnimg.cn/2018103119131567.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Rvbnl3dTE5OTI=,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<blockquote>
<p>（1）关系型数据库中的数据库（DataBase），等价于ES中的索引（Index）<br>
（2）一个数据库下面有N张表（Table），等价于1个索引Index下面有N多类型（Type），<br>
（3）一个数据库表（Table）下的数据由多行（ROW）多列（column，属性）组成，等价于1个Type由多个文档（Document）和多Field组成。<br>
（4）在一个关系型数据库里面，schema定义了表、每个表的字段，还有表和字段之间的关系。 与之对应的，在ES中：Mapping定义索引下的Type的字段处理规则，即索引如何建立、索引类型、是否保存原始索引JSON文档、是否压缩原始JSON文档、是否需要分词处理、如何进行分词处理等。<br>
（5）在数据库中的增insert、删delete、改update、查search操作等价于ES中的增PUT/POST、删Delete、改_update、查GET.</p>
</blockquote>
<p>关于分片和副本，我们可以从<code>head</code>插件直观理解（建议安装）<br>
<img src="https://img-blog.csdnimg.cn/20181031191721999.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Rvbnl3dTE5OTI=,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"><br>
上图中<code>20181025</code>索引有5个主分片、2个副本，所以最终应该有5*（2+1）=15个分片<br>
<img src="https://img-blog.csdnimg.cn/20181031192222884.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Rvbnl3dTE5OTI=,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"><br>
head插件中主分片用粗线标出，我们可以发现主分片以及副本分布在哪个节点都是完全随机，由ES管理的</p>
<hr>
<p>ES的基本概念介绍到这里，由于ES基础知识繁多，我也是接触不久，更多的ES基础知识请参考下方链接，本文不做过多阐述。</p>
<ul>
<li><a href="https://elasticsearch.cn/book/elasticsearch_definitive_guide_2.x/" rel="nofollow">简体中文版官方文档</a></li>
<li><a href="https://www.imooc.com/learn/889" rel="nofollow">慕课网基础教程视频</a>（有head插件安装、集群部署）</li>
<li><a href="https://endymecy.gitbooks.io/elasticsearch-guide-chinese/content/index.html" rel="nofollow">elasticsearch中文指南</a></li>
</ul>
<h2><a id="springelasticsearch_96"></a>二、spring-elasticsearch配置</h2>
<h5><a id="pom_97"></a>pom文件配置</h5>
<pre><code class="prism language-xml">    <span class="token comment">&lt;!-- elasticsearch client --&gt;</span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>dependency</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>groupId</span><span class="token punctuation">&gt;</span></span>org.elasticsearch<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>groupId</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>artifactId</span><span class="token punctuation">&gt;</span></span>elasticsearch<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>artifactId</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>version</span><span class="token punctuation">&gt;</span></span>2.1.1<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>version</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>dependency</span><span class="token punctuation">&gt;</span></span>

    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>dependency</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>groupId</span><span class="token punctuation">&gt;</span></span>org.springframework.data<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>groupId</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>artifactId</span><span class="token punctuation">&gt;</span></span>spring-data-elasticsearch<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>artifactId</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>version</span><span class="token punctuation">&gt;</span></span>2.0.4.RELEASE<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>version</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>dependency</span><span class="token punctuation">&gt;</span></span>
</code></pre>
<p>公司使用的ES版本有点老…各位可以根据实际情况选择<br>
关于spring-data-elasticsearch与elasticsearch的对应关系官方文档是这样说明的：<a href="https://github.com/spring-projects/spring-data-elasticsearch#maven-configuration" rel="nofollow">github地址</a></p>

<table>
<thead>
<tr>
<th align="center">spring data elasticsearch</th>
<th align="center">elasticsearch</th>
</tr>
</thead>
<tbody>
<tr>
<td align="center">3.1.x</td>
<td align="center">6.2.2</td>
</tr>
<tr>
<td align="center">3.0.x</td>
<td align="center">5.5.0</td>
</tr>
<tr>
<td align="center">2.1.x</td>
<td align="center">2.4.0</td>
</tr>
<tr>
<td align="center">2.0.x</td>
<td align="center">2.2.0</td>
</tr>
<tr>
<td align="center">1.3.x</td>
<td align="center">1.5.2</td>
</tr>
</tbody>
</table><h5><a id="springelasticsearchxml_122"></a>spring-elasticsearch.xml</h5>
<pre><code class="prism language-xml"><span class="token prolog">&lt;?xml version="1.0" encoding="UTF-8"?&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span> <span class="token attr-name">xmlns</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/beans<span class="token punctuation">"</span></span>
    <span class="token attr-name"><span class="token namespace">xmlns:</span>xsi</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.w3.org/2001/XMLSchema-instance<span class="token punctuation">"</span></span>
    <span class="token attr-name"><span class="token namespace">xmlns:</span>elasticsearch</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/data/elasticsearch<span class="token punctuation">"</span></span>
    <span class="token attr-name"><span class="token namespace">xmlns:</span>context</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/context<span class="token punctuation">"</span></span>
    <span class="token attr-name"><span class="token namespace">xsi:</span>schemaLocation</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                            http://www.springframework.org/schema/data/elasticsearch http://www.springframework.org/schema/data/elasticsearch/spring-elasticsearch.xsd
                            http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">elasticsearch:</span>transport-client</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>esTransportClient<span class="token punctuation">"</span></span>
        <span class="token attr-name">cluster-nodes</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>127.0.0.1:9300<span class="token punctuation">"</span></span> <span class="token attr-name">cluster-name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>elasticsearch<span class="token punctuation">"</span></span> <span class="token punctuation">/&gt;</span></span>

    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>elasticsearchTemplate<span class="token punctuation">"</span></span>
        <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.springframework.data.elasticsearch.core.ElasticsearchTemplate<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>constructor-arg</span> <span class="token attr-name">ref</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>esTransportClient<span class="token punctuation">"</span></span> <span class="token punctuation">/&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span>
</code></pre>
<p><code>cluster-nodes</code>是集群中的节点地址，如果是集群用<code>,</code>隔开。<strong><mark>这里注意一下java中节点通信默认<code>9300</code>端口，而<code>RESTful API</code>默认使用<code>9200</code>端口通信</mark></strong><br>
<code>cluster-name</code>是集群名称，默认值是<code>elasticsearch</code></p>
<h2><a id="_146"></a>三、代码实现</h2>
<h5><a id="ElasticsearchTemplate_147"></a>ElasticsearchTemplate</h5>
<p><code>ElasticsearchTemplate</code>是Spring对ES的java api进行的封装。<br>
常用的几个方法：</p>
<pre><code class="prism language-java">	<span class="token comment">/**
	 * Bulk index all objects. Will do save or update
	 *
	 * @param queries
	 */</span>
	<span class="token keyword">void</span> <span class="token function">bulkIndex</span><span class="token punctuation">(</span>List<span class="token generics function"><span class="token punctuation">&lt;</span>IndexQuery<span class="token punctuation">&gt;</span></span> queries<span class="token punctuation">)</span><span class="token punctuation">;</span>

	<span class="token comment">/**
	 * return number of elements found by given query
	 *
	 * @param query
	 * @return
	 */</span>
	<span class="token generics function"><span class="token punctuation">&lt;</span>T<span class="token punctuation">&gt;</span></span> <span class="token keyword">long</span> <span class="token function">count</span><span class="token punctuation">(</span>SearchQuery query<span class="token punctuation">)</span><span class="token punctuation">;</span>

	<span class="token comment">/**
	 * Create an index for given indexName
	 *
	 * @param indexName
	 */</span>
	<span class="token keyword">boolean</span> <span class="token function">createIndex</span><span class="token punctuation">(</span>String indexName<span class="token punctuation">)</span><span class="token punctuation">;</span>

	<span class="token comment">/**
	 * Create mapping for a given indexName and type
	 *
	 * @param indexName
	 * @param type
	 * @param mappings
	 */</span>
	<span class="token keyword">boolean</span> <span class="token function">putMapping</span><span class="token punctuation">(</span>String indexName<span class="token punctuation">,</span> String type<span class="token punctuation">,</span> Object mappings<span class="token punctuation">)</span><span class="token punctuation">;</span>

	<span class="token comment">/**
	 * Deletes an index for given indexName
	 *
	 * @param indexName
	 * @return
	 */</span>
	<span class="token keyword">boolean</span> <span class="token function">deleteIndex</span><span class="token punctuation">(</span>String indexName<span class="token punctuation">)</span><span class="token punctuation">;</span>

	<span class="token comment">/**
	 * Execute the search query against elasticsearch and return result as {@link List}
	 *
	 * @param query
	 * @param clazz
	 * @param &lt;T&gt;
	 * @return
	 */</span>
	<span class="token generics function"><span class="token punctuation">&lt;</span>T<span class="token punctuation">&gt;</span></span> List<span class="token generics function"><span class="token punctuation">&lt;</span>T<span class="token punctuation">&gt;</span></span> <span class="token function">queryForList</span><span class="token punctuation">(</span>SearchQuery query<span class="token punctuation">,</span> Class<span class="token generics function"><span class="token punctuation">&lt;</span>T<span class="token punctuation">&gt;</span></span> clazz<span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre>
<p>其中对于监控业务来说，主要需要用到新增/删除索引、创建mapping、批量写入数据，而查询或者统计更多是在grafana展示曾处理。但是条件查询是spring-es中很重要的一环，我也会介绍一下最常使用的<code>queryForList</code>方法</p>
<h5><a id="KafkaES_203"></a>Kafka数据写入ES</h5>
<p>先看下<code>bulkIndex</code>的参数<code>List&lt;IndexQuery&gt; queries</code></p>
<pre><code class="prism language-java"><span class="token keyword">package</span> org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>data<span class="token punctuation">.</span>elasticsearch<span class="token punctuation">.</span>core<span class="token punctuation">.</span>query<span class="token punctuation">;</span>

<span class="token comment">/**
 * IndexQuery
 *
 * @author Rizwan Idrees
 * @author Mohsin Husen
 */</span>

<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">IndexQuery</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> String id<span class="token punctuation">;</span>
	<span class="token keyword">private</span> Object object<span class="token punctuation">;</span>
	<span class="token keyword">private</span> Long version<span class="token punctuation">;</span>
	<span class="token keyword">private</span> String indexName<span class="token punctuation">;</span>
	<span class="token keyword">private</span> String type<span class="token punctuation">;</span>
	<span class="token keyword">private</span> String source<span class="token punctuation">;</span>
	<span class="token keyword">private</span> String parentId<span class="token punctuation">;</span>

	<span class="token comment">//省略get set</span>
<span class="token punctuation">}</span>
</code></pre>
<p><code>id</code>是<code>es</code>中文档的主键，写入时不用处理<br>
<code>object</code>和<code>soruce</code>就是实际的文档内容，<code>object</code>是写入的实体类对象，<code>source</code>是<code>json</code>格式字符串<br>
<code>version</code>是用来控制版本的，不用处理<br>
<code>indexName</code>是索引名<br>
<code>type</code>是类型名</p>
<p>构建完IndexQuery后，就可以调用方法插入数据了，具体插入代码如下：</p>
<p><code>EsIndexBO</code> 是我构建的一个实体类，<code>JSONObject</code> 是alibaba的fastjson</p>
<pre><code class="prism language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">EsIndexBO</span> <span class="token punctuation">{</span>

    <span class="token comment">/**
     * 索引名
     */</span>
    <span class="token keyword">private</span> String indexName<span class="token punctuation">;</span>

    <span class="token comment">/**
     * 类型
     */</span>
    <span class="token keyword">private</span> String type<span class="token punctuation">;</span>

    <span class="token comment">/**
     * 文档内容
     */</span>
    <span class="token keyword">private</span> JSONObject source<span class="token punctuation">;</span>
    
    <span class="token comment">//忽略get set</span>
<span class="token punctuation">}</span>

</code></pre>
<pre><code class="prism language-java">    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">bulkIndex</span><span class="token punctuation">(</span>List<span class="token generics function"><span class="token punctuation">&lt;</span>EsIndexBO<span class="token punctuation">&gt;</span></span> esIndexBOList<span class="token punctuation">)</span> <span class="token punctuation">{</span>

        <span class="token keyword">if</span> <span class="token punctuation">(</span>CollectionUtils<span class="token punctuation">.</span><span class="token function">isEmpty</span><span class="token punctuation">(</span>esIndexBOList<span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
            LOGGER<span class="token punctuation">.</span><span class="token function">warn</span><span class="token punctuation">(</span><span class="token string">".bulkIndex() esIndexBOList is emtpy"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token keyword">return</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>

        SimpleDateFormat sdf <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">SimpleDateFormat</span><span class="token punctuation">(</span><span class="token string">"yyyy-MM-dd'T'HH:mm:ss.SSS"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        Date date <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Date</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        List<span class="token generics function"><span class="token punctuation">&lt;</span>IndexQuery<span class="token punctuation">&gt;</span></span> indexList <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">ArrayList</span><span class="token operator">&lt;</span><span class="token operator">&gt;</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token keyword">int</span> counter <span class="token operator">=</span> <span class="token number">0</span><span class="token punctuation">;</span>

        <span class="token keyword">for</span> <span class="token punctuation">(</span>EsIndexBO esIndexBO <span class="token operator">:</span> esIndexBOList<span class="token punctuation">)</span> <span class="token punctuation">{</span>

            IndexQuery indexQuery <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">IndexQuery</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

            JSONObject source <span class="token operator">=</span> esIndexBO<span class="token punctuation">.</span><span class="token function">getSource</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token comment">//这个字段的用处后面会有说明</span>
            source<span class="token punctuation">.</span><span class="token function">put</span><span class="token punctuation">(</span><span class="token string">"esTimeField"</span><span class="token punctuation">,</span> sdf<span class="token punctuation">.</span><span class="token function">format</span><span class="token punctuation">(</span>date<span class="token punctuation">)</span> <span class="token operator">+</span> <span class="token string">"+08:00"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

            indexQuery<span class="token punctuation">.</span><span class="token function">setSource</span><span class="token punctuation">(</span>JSON<span class="token punctuation">.</span><span class="token function">toJSONString</span><span class="token punctuation">(</span>source<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            indexQuery<span class="token punctuation">.</span><span class="token function">setIndexName</span><span class="token punctuation">(</span>esIndexBO<span class="token punctuation">.</span><span class="token function">getIndexName</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            indexQuery<span class="token punctuation">.</span><span class="token function">setType</span><span class="token punctuation">(</span>esIndexBO<span class="token punctuation">.</span><span class="token function">getType</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

            indexList<span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span>indexQuery<span class="token punctuation">)</span><span class="token punctuation">;</span>

            <span class="token keyword">if</span> <span class="token punctuation">(</span>counter <span class="token operator">%</span> <span class="token number">1000</span> <span class="token operator">==</span> <span class="token number">0</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
                <span class="token function">bulkIndexWithRetry</span><span class="token punctuation">(</span>indexList<span class="token punctuation">)</span><span class="token punctuation">;</span>
                indexList<span class="token punctuation">.</span><span class="token function">clear</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span>

            counter<span class="token operator">++</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>

        <span class="token keyword">if</span> <span class="token punctuation">(</span>indexList<span class="token punctuation">.</span><span class="token function">size</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">&gt;</span> <span class="token number">0</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token function">bulkIndexWithRetry</span><span class="token punctuation">(</span>indexList<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>

    <span class="token punctuation">}</span>

    <span class="token keyword">private</span> <span class="token keyword">void</span> <span class="token function">bulkIndexWithRetry</span><span class="token punctuation">(</span>List<span class="token generics function"><span class="token punctuation">&lt;</span>IndexQuery<span class="token punctuation">&gt;</span></span> indexList<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">try</span> <span class="token punctuation">{</span>
            elasticsearchTemplate<span class="token punctuation">.</span><span class="token function">bulkIndex</span><span class="token punctuation">(</span>indexList<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span> <span class="token keyword">catch</span> <span class="token punctuation">(</span><span class="token class-name">Exception</span> e<span class="token punctuation">)</span> <span class="token punctuation">{</span>

            Random random <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Random</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token keyword">int</span> millis <span class="token operator">=</span> <span class="token punctuation">(</span>random<span class="token punctuation">.</span><span class="token function">nextInt</span><span class="token punctuation">(</span><span class="token number">5</span><span class="token punctuation">)</span> <span class="token operator">+</span> <span class="token number">1</span><span class="token punctuation">)</span> <span class="token operator">*</span> <span class="token number">1000</span><span class="token punctuation">;</span>

            <span class="token keyword">try</span> <span class="token punctuation">{</span>
                Thread<span class="token punctuation">.</span><span class="token function">sleep</span><span class="token punctuation">(</span>millis<span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span> <span class="token keyword">catch</span> <span class="token punctuation">(</span><span class="token class-name">InterruptedException</span> e1<span class="token punctuation">)</span> <span class="token punctuation">{</span>
                <span class="token keyword">throw</span> <span class="token keyword">new</span> <span class="token class-name">RuntimeException</span><span class="token punctuation">(</span>e1<span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span>

            LOGGER<span class="token punctuation">.</span><span class="token function">warn</span><span class="token punctuation">(</span><span class="token string">"bulkIndex failed, now retry. ExceptionMsg={}"</span><span class="token punctuation">,</span> e<span class="token punctuation">.</span><span class="token function">getMessage</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            elasticsearchTemplate<span class="token punctuation">.</span><span class="token function">bulkIndex</span><span class="token punctuation">(</span>indexList<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>
</code></pre>
<p>每到1000就会批量插入一次，并且会有一次失败尝试的机会<br>
索引不存在的话会自动创建，并且有默认的mapping<br>
<mark><strong>非常重要</strong></mark>：关于<code>esTimeField</code>这个额外添加的字段，主要是用于之后grafana分析ES数据时的聚合查询，并且必须符合ES要求的时间格式<code>yyyyMMdd'T'HHmmssZ</code>。由于中国属于东八区，所以<code>Z</code>替换为<code>+08:00</code>。参考官方文档：<a href="https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-date-format.html#built-in-date-formats" rel="nofollow">built-in-date-formats</a></p>
<h5><a id="_328"></a>定时任务删除索引</h5>
<p>由于线上数据量巨大，所以创建的索引是按天分割，这个从我上面head插件的图片就能看出<br>
因为是监控数据，我不会保留太久，所以需要定期删除数据，也就是按天删除索引</p>
<p>配置spring定时任务</p>
<pre><code class="prism language-xml"><span class="token prolog">&lt;?xml version="1.0" encoding="UTF-8"?&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span> <span class="token attr-name">xmlns</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/beans<span class="token punctuation">"</span></span>
    <span class="token attr-name"><span class="token namespace">xmlns:</span>xsi</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.w3.org/2001/XMLSchema-instance<span class="token punctuation">"</span></span> <span class="token attr-name"><span class="token namespace">xmlns:</span>task</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/task<span class="token punctuation">"</span></span>
    <span class="token attr-name"><span class="token namespace">xsi:</span>schemaLocation</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.1.xsd
 http://www.springframework.org/schema/task http://www.springframework.org/schema/task/spring-task.xsd<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

    <span class="token comment">&lt;!-- 定时任务相关配置 --&gt;</span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">task:</span>executor</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>executor<span class="token punctuation">"</span></span> <span class="token attr-name">pool-size</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>10<span class="token punctuation">"</span></span>
        <span class="token attr-name">queue-capacity</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>128<span class="token punctuation">"</span></span> <span class="token punctuation">/&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">task:</span>scheduler</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>scheduler<span class="token punctuation">"</span></span> <span class="token attr-name">pool-size</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>10<span class="token punctuation">"</span></span> <span class="token punctuation">/&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">task:</span>annotation-driven</span> <span class="token attr-name">executor</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>executor<span class="token punctuation">"</span></span>
        <span class="token attr-name">scheduler</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>scheduler<span class="token punctuation">"</span></span> <span class="token punctuation">/&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span>
</code></pre>
<p>定时任务<code>cron</code>表达式 每天凌晨1点执行（如果是多节点需采用分布式锁或根据ip判断），删除7天前的那个索引</p>
<pre><code class="prism language-java"><span class="token annotation punctuation">@Component</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">DropIndexTask</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Autowired</span>
    <span class="token keyword">private</span> ElasticsearchTemplate elasticsearchTemplate<span class="token punctuation">;</span>

    <span class="token comment">/**
     * log
     */</span>
    <span class="token keyword">private</span> <span class="token keyword">static</span> <span class="token keyword">final</span> Logger LOGGER <span class="token operator">=</span> LoggerFactory<span class="token punctuation">.</span><span class="token function">getLogger</span><span class="token punctuation">(</span>DropIndexTask<span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token annotation punctuation">@Scheduled</span><span class="token punctuation">(</span>cron <span class="token operator">=</span> <span class="token string">"0 0 1 * * ?"</span><span class="token punctuation">)</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">dropNgLogIndex</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">try</span> <span class="token punctuation">{</span>
            String deleteIndex <span class="token operator">=</span> <span class="token function">dateAdd</span><span class="token punctuation">(</span><span class="token operator">-</span><span class="token number">7</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token keyword">boolean</span> result <span class="token operator">=</span> elasticsearchTemplate<span class="token punctuation">.</span><span class="token function">deleteIndex</span><span class="token punctuation">(</span>deleteIndex<span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token keyword">if</span> <span class="token punctuation">(</span><span class="token operator">!</span>result<span class="token punctuation">)</span> <span class="token punctuation">{</span>
                LOGGER<span class="token punctuation">.</span><span class="token function">warn</span><span class="token punctuation">(</span><span class="token string">"index:{} delete fail"</span><span class="token punctuation">,</span> deleteIndex<span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span> <span class="token keyword">else</span> <span class="token punctuation">{</span>
                LOGGER<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"index:{} delete success"</span><span class="token punctuation">,</span> deleteIndex<span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span>
        <span class="token punctuation">}</span> <span class="token keyword">catch</span> <span class="token punctuation">(</span><span class="token class-name">Exception</span> e<span class="token punctuation">)</span> <span class="token punctuation">{</span>
            LOGGER<span class="token punctuation">.</span><span class="token function">error</span><span class="token punctuation">(</span><span class="token string">"dropIndex task error,error message:{}"</span><span class="token punctuation">,</span> e<span class="token punctuation">.</span><span class="token function">getMessage</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> e<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>

    <span class="token comment">/**
     * 获得当前日期前n天的日期
     */</span>
    <span class="token keyword">private</span> String <span class="token function">dateAdd</span><span class="token punctuation">(</span><span class="token keyword">int</span> interval<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token comment">// 得到当前时间n天前的日期并转换成yyyyMMdd格式</span>
        Calendar rightNow <span class="token operator">=</span> Calendar<span class="token punctuation">.</span><span class="token function">getInstance</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        rightNow<span class="token punctuation">.</span><span class="token function">setTime</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">Date</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        rightNow<span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span>Calendar<span class="token punctuation">.</span>DAY_OF_YEAR<span class="token punctuation">,</span> interval<span class="token punctuation">)</span><span class="token punctuation">;</span>
        Date date <span class="token operator">=</span> rightNow<span class="token punctuation">.</span><span class="token function">getTime</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        SimpleDateFormat sdf <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">SimpleDateFormat</span><span class="token punctuation">(</span><span class="token string">"yyyyMMdd"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token keyword">return</span> sdf<span class="token punctuation">.</span><span class="token function">format</span><span class="token punctuation">(</span>date<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>
<h5><a id="mapping_391"></a>定时任务创建索引以及mapping</h5>
<p>为什么没有一开始就提到这个呢？是因为写入ES时没有索引会自动创建，并且下面的type会创建默认的mapping，但是在最后grafana分析的时候发现了问题，导致我们必须回过头来手动创建。</p>
<p>举个例子，假设创建了一个<code>index</code>为20181101，<code>type</code>为test，有一个字段packageName以及时间字段esTimeField，那么这时候通过RESTful查询<code>http://127.0.0.1:9200/20181101/test/_mapping</code>会得到该type的mapping：</p>
<pre><code class="prism language-json"><span class="token punctuation">{</span>
  <span class="token string">"20181101"</span><span class="token punctuation">:</span> <span class="token punctuation">{</span>
    <span class="token string">"mappings"</span><span class="token punctuation">:</span> <span class="token punctuation">{</span>
      <span class="token string">"test"</span><span class="token punctuation">:</span> <span class="token punctuation">{</span>
        <span class="token string">"properties"</span><span class="token punctuation">:</span> <span class="token punctuation">{</span>
          <span class="token string">"packageName"</span><span class="token punctuation">:</span> <span class="token punctuation">{</span>
            <span class="token string">"type"</span><span class="token punctuation">:</span> <span class="token string">"string"</span>
          <span class="token punctuation">}</span><span class="token punctuation">,</span>
          <span class="token string">"esTimeField"</span><span class="token punctuation">:</span> <span class="token punctuation">{</span>
            <span class="token string">"type"</span><span class="token punctuation">:</span> <span class="token string">"date"</span><span class="token punctuation">,</span>
            <span class="token string">"format"</span><span class="token punctuation">:</span> <span class="token string">"strict_date_optional_time||epoch_millis"</span>
          <span class="token punctuation">}</span><span class="token punctuation">,</span>
        <span class="token punctuation">}</span>
      <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>
  <span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>
<p>ES中有一个分词的概念，并且字段默认都是分词的，这样就会导致一个问题。假设<code>packageName</code>有2个值<code>com.tecent.wechat</code>、<code>com.tecent.qq</code>，由于默认分词，ES根据<code>.</code>会分成多个词语，导致最终分成了多组<br>
所以我们需要将packageName这个字段的mapping设置为不分词，也就是<code>not_analyzed</code>，这样才能正确的分为2组</p>
<p>同时，出现这个问题后我做了相关调查，了解了ES建立index、type以及mapping的规则，并且自己做了实验，这里用通俗易懂的文字分享给大家</p>
<blockquote>
<p>同一个index下先创建了template1的mapping，如下所示；这时候可以创建template2和template3，但是不可以创建template4<br>
就是说  在一个index下只要创建好了一个type的mapping(不管是主动创建还是写入数据自动创建的)，这时候可以在新的type下多出一些字段创建新的mapping规则，也可以去掉一些字段创建新的mapping规则，但是不可以修改相同位置且名字一样的字段的mapping规则。<br>
<img src="https://img-blog.csdnimg.cn/20181101195235353.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Rvbnl3dTE5OTI=,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
</blockquote>
<blockquote>
<p><mark><strong>总结：index下想要新创建一个type的mapping  先和该index下所有的type的mapping对比   可以多  可以少  但是不能修改相同字段（位置结构、名字都相同）的mapping规则。在一个index下，不管有多少type，相同字段（位置结构、名字都相同）的mapping只能有一个</strong></mark></p>
</blockquote>
<p><mark><strong>重要</strong></mark>：由于写入数据会自动创建索引以及type的mapping，所以我们需要提前创建，那么定时任务就需要在前一天创建好index和mapping（我是在每天的23点创建第二天的）<br>
这里省略定时任务代码，可以参考上面删除索引的</p>
<p>定义存入ES的实体类，假设只有1个字段<code>packageName</code></p>
<pre><code class="prism language-java"><span class="token annotation punctuation">@Document</span><span class="token punctuation">(</span>indexName <span class="token operator">=</span> <span class="token string">"stdomainindexmappingindex"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">StDomainIndexMapping</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Field</span><span class="token punctuation">(</span>type <span class="token operator">=</span> FieldType<span class="token punctuation">.</span>String<span class="token punctuation">,</span> index <span class="token operator">=</span> FieldIndex<span class="token punctuation">.</span>not_analyzed<span class="token punctuation">)</span>
    <span class="token keyword">private</span> String packageName<span class="token punctuation">;</span>

<span class="token punctuation">}</span>
</code></pre>
<p>我们看一下<code>@Document</code>注解</p>
<pre><code class="prism language-java"><span class="token annotation punctuation">@Persistent</span>
<span class="token annotation punctuation">@Inherited</span>
<span class="token annotation punctuation">@Retention</span><span class="token punctuation">(</span>RetentionPolicy<span class="token punctuation">.</span>RUNTIME<span class="token punctuation">)</span>
<span class="token annotation punctuation">@Target</span><span class="token punctuation">(</span><span class="token punctuation">{</span>ElementType<span class="token punctuation">.</span>TYPE<span class="token punctuation">}</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> @<span class="token keyword">interface</span> <span class="token class-name">Document</span> <span class="token punctuation">{</span>

	String <span class="token function">indexName</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

	String <span class="token function">type</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">default</span> <span class="token string">""</span><span class="token punctuation">;</span>

	<span class="token keyword">boolean</span> <span class="token function">useServerConfiguration</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">default</span> <span class="token boolean">false</span><span class="token punctuation">;</span>

	<span class="token keyword">short</span> <span class="token function">shards</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">default</span> <span class="token number">5</span><span class="token punctuation">;</span>

	<span class="token keyword">short</span> <span class="token function">replicas</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">default</span> <span class="token number">1</span><span class="token punctuation">;</span>

	String <span class="token function">refreshInterval</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">default</span> <span class="token string">"1s"</span><span class="token punctuation">;</span>

	String <span class="token function">indexStoreType</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">default</span> <span class="token string">"fs"</span><span class="token punctuation">;</span>

	<span class="token keyword">boolean</span> <span class="token function">createIndex</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">default</span> <span class="token boolean">true</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<p><code>indexName</code>必填，其他我都采用默认值。提前说明一下，如果<code>type</code>没有填写，在之后的<code>putMapping</code>操作结束后，该<code>index</code>下会自动生成一个实体类类名全小写的<code>type</code>以及我们定义的<code>mapping</code></p>
<pre><code class="prism language-json"><span class="token punctuation">{</span>
  <span class="token string">"20181101"</span><span class="token punctuation">:</span> <span class="token punctuation">{</span>
    <span class="token string">"mappings"</span><span class="token punctuation">:</span> <span class="token punctuation">{</span>
      <span class="token string">"stdomainindexmapping "</span><span class="token punctuation">:</span> <span class="token punctuation">{</span>
        <span class="token string">"properties"</span><span class="token punctuation">:</span> <span class="token punctuation">{</span>
          <span class="token string">"packageName"</span><span class="token punctuation">:</span> <span class="token punctuation">{</span>
            <span class="token string">"type"</span><span class="token punctuation">:</span> <span class="token string">"string"</span><span class="token punctuation">,</span>
            <span class="token string">"index"</span><span class="token punctuation">:</span> <span class="token string">"not_analyzed"</span>
          <span class="token punctuation">}</span><span class="token punctuation">,</span>
          <span class="token string">"esTimeField"</span><span class="token punctuation">:</span> <span class="token punctuation">{</span>
            <span class="token string">"type"</span><span class="token punctuation">:</span> <span class="token string">"date"</span><span class="token punctuation">,</span>
            <span class="token string">"format"</span><span class="token punctuation">:</span> <span class="token string">"strict_date_optional_time||epoch_millis"</span>
          <span class="token punctuation">}</span><span class="token punctuation">,</span>
        <span class="token punctuation">}</span>
      <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>
  <span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>
<p>另外然后按照我前面总结的<code>mapping</code>规则，之后<code>type</code><strong>对应的字段</strong>都会适用<code>mapping</code>，可能不太好理解，大家自己尝试下就懂了</p>
<p>再看一下<code>@Field</code>注解</p>
<pre><code class="prism language-java"><span class="token annotation punctuation">@Retention</span><span class="token punctuation">(</span>RetentionPolicy<span class="token punctuation">.</span>RUNTIME<span class="token punctuation">)</span>
<span class="token annotation punctuation">@Target</span><span class="token punctuation">(</span>ElementType<span class="token punctuation">.</span>FIELD<span class="token punctuation">)</span>
<span class="token annotation punctuation">@Documented</span>
<span class="token annotation punctuation">@Inherited</span>
<span class="token keyword">public</span> @<span class="token keyword">interface</span> <span class="token class-name">Field</span> <span class="token punctuation">{</span>

	FieldType <span class="token function">type</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">default</span> FieldType<span class="token punctuation">.</span>Auto<span class="token punctuation">;</span>

	FieldIndex <span class="token function">index</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">default</span> FieldIndex<span class="token punctuation">.</span>analyzed<span class="token punctuation">;</span>

	DateFormat <span class="token function">format</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">default</span> DateFormat<span class="token punctuation">.</span>none<span class="token punctuation">;</span>

	String <span class="token function">pattern</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">default</span> <span class="token string">""</span><span class="token punctuation">;</span>

	<span class="token keyword">boolean</span> <span class="token function">store</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">default</span> <span class="token boolean">false</span><span class="token punctuation">;</span>

	String <span class="token function">searchAnalyzer</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">default</span> <span class="token string">""</span><span class="token punctuation">;</span>

	String <span class="token function">analyzer</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">default</span> <span class="token string">""</span><span class="token punctuation">;</span>

	String<span class="token punctuation">[</span><span class="token punctuation">]</span> <span class="token function">ignoreFields</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">default</span> <span class="token punctuation">{</span><span class="token punctuation">}</span><span class="token punctuation">;</span>

	<span class="token keyword">boolean</span> <span class="token function">includeInParent</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">default</span> <span class="token boolean">false</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<p>对我来说需要使用的就是<code>type</code>和<code>index</code>，将<code>index</code>设为<code>FieldIndex.not_analyzed</code>就是不分词。另外如果有<mark>嵌套对象</mark>，type需要使用<code>FieldType.Object</code>，嵌套对象只需要<code>@Field</code>，不需要<code>@Document</code>了</p>
<p>到这里大家可能会有疑问，<code>@Document</code>需要把索引名写在注解里，我给的默认名字是<code>stdomainindexmappingindex</code>，而我又是按天创建索引的，如何动态修改成<code>20181101</code>的呢？答案是用<mark>反射</mark>处理</p>
<p>先放上反射工具类</p>
<pre><code class="prism language-java">    <span class="token keyword">public</span> <span class="token keyword">static</span> Object <span class="token function">changeAnnotationValue</span><span class="token punctuation">(</span>Annotation annotation<span class="token punctuation">,</span> String key<span class="token punctuation">,</span> Object newValue<span class="token punctuation">)</span> <span class="token punctuation">{</span>

        Object handler <span class="token operator">=</span> Proxy<span class="token punctuation">.</span><span class="token function">getInvocationHandler</span><span class="token punctuation">(</span>annotation<span class="token punctuation">)</span><span class="token punctuation">;</span>

        Field f<span class="token punctuation">;</span>

        <span class="token keyword">try</span> <span class="token punctuation">{</span>
            f <span class="token operator">=</span> handler<span class="token punctuation">.</span><span class="token function">getClass</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">getDeclaredField</span><span class="token punctuation">(</span><span class="token string">"memberValues"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span> <span class="token keyword">catch</span> <span class="token punctuation">(</span><span class="token class-name">NoSuchFieldException</span> <span class="token operator">|</span> SecurityException e<span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token keyword">throw</span> <span class="token keyword">new</span> <span class="token class-name">IllegalStateException</span><span class="token punctuation">(</span>e<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>

        f<span class="token punctuation">.</span><span class="token function">setAccessible</span><span class="token punctuation">(</span><span class="token boolean">true</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        Map<span class="token generics function"><span class="token punctuation">&lt;</span>String<span class="token punctuation">,</span> Object<span class="token punctuation">&gt;</span></span> memberValues<span class="token punctuation">;</span>
        <span class="token keyword">try</span> <span class="token punctuation">{</span>
            memberValues <span class="token operator">=</span> <span class="token punctuation">(</span>Map<span class="token generics function"><span class="token punctuation">&lt;</span>String<span class="token punctuation">,</span> Object<span class="token punctuation">&gt;</span></span><span class="token punctuation">)</span> f<span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span>handler<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span> <span class="token keyword">catch</span> <span class="token punctuation">(</span><span class="token class-name">IllegalArgumentException</span> <span class="token operator">|</span> IllegalAccessException e<span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token keyword">throw</span> <span class="token keyword">new</span> <span class="token class-name">IllegalStateException</span><span class="token punctuation">(</span>e<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>

        Object oldValue <span class="token operator">=</span> memberValues<span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span>key<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token keyword">if</span> <span class="token punctuation">(</span>null <span class="token operator">==</span> oldValue <span class="token operator">||</span> oldValue<span class="token punctuation">.</span><span class="token function">getClass</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">!=</span> newValue<span class="token punctuation">.</span><span class="token function">getClass</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token keyword">throw</span> <span class="token keyword">new</span> <span class="token class-name">IllegalArgumentException</span><span class="token punctuation">(</span><span class="token string">"oldValue's class and newValue's class not mapping."</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>

        memberValues<span class="token punctuation">.</span><span class="token function">put</span><span class="token punctuation">(</span>key<span class="token punctuation">,</span> newValue<span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token keyword">return</span> oldValue<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
</code></pre>
<p>创建<code>index</code>以及<code>mapping</code></p>
<pre><code class="prism language-java">    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">createIndexMapping</span><span class="token punctuation">(</span>Class<span class="token operator">&lt;</span><span class="token operator">?</span><span class="token operator">&gt;</span> clazz<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token comment">// 我们采用的策略是当天晚上执行定时任务，提前创建第二天的索引和mapping</span>
        String index <span class="token operator">=</span> <span class="token function">dateAdd</span><span class="token punctuation">(</span><span class="token number">1</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        Document annotation <span class="token operator">=</span> <span class="token punctuation">(</span>Document<span class="token punctuation">)</span> clazz<span class="token punctuation">.</span><span class="token function">getAnnotation</span><span class="token punctuation">(</span>Document<span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        ReflectUtil<span class="token punctuation">.</span><span class="token function">changeAnnotationValue</span><span class="token punctuation">(</span>annotation<span class="token punctuation">,</span> <span class="token string">"indexName"</span><span class="token punctuation">,</span> index<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token function">insertIndexMapping</span><span class="token punctuation">(</span>clazz<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">private</span> <span class="token keyword">boolean</span> <span class="token function">insertIndexMapping</span><span class="token punctuation">(</span>Class<span class="token operator">&lt;</span><span class="token operator">?</span><span class="token operator">&gt;</span> clazz<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">boolean</span> createResult <span class="token operator">=</span> elasticsearchTemplate<span class="token punctuation">.</span><span class="token function">createIndex</span><span class="token punctuation">(</span>clazz<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token keyword">if</span> <span class="token punctuation">(</span>createResult<span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token keyword">boolean</span> putMappingResult <span class="token operator">=</span> elasticsearchTemplate<span class="token punctuation">.</span><span class="token function">putMapping</span><span class="token punctuation">(</span>clazz<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>
</code></pre>
<p>我们在定时任务中调用该方法并传入<code>StDomainIndexMapping.class</code>参数就可以创建index和mapping了</p>
<hr>
<p>本以为这样就可以了，但随之而来出现了2个严重问题：</p>
<ol>
<li>创建mapping的时候会出现异常<code>java.lang.NoSuchMethodError:org.springframework.core.annotation.AnnotatedElementUtils.findMergedAnnotation</code><br>
原因是项目使用的springframework版本为4.1.6，而spring-data-elasticsearch 2.1.7默认依赖的spring-context是4.3.11，所以初步确定是我们的项目使用的spring版本太低导致。<br>
参考spring的api文档，发现原来<mark>AnnotatedElementUtils.findMergedAnnotation是4.2版才有的</mark><br>
所以我们需要修改pom文件将引用的spring版本替换为4.2以上，这也是我在Kafka篇一开始所提到的</li>
<li>解决了报错问题，项目正式上线，第二天的数据没有问题，但是第三天在grafana分组查询时又将词语做了分词处理。<br>
这是怎么回事呢？检查了es的索引mapping，发现第三天并没有主动创建，而是被动写入时创建了默认的mapping，默认分词。<br>
问题不应该出现在反射工具类上，如果有问题，第二天肯定也不会创建正确的mapping呀。那么问题就很可能出现在<code>putMapping</code>方法上，需要跟一下源码！</li>
</ol>
<pre><code class="prism language-java">	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">public</span> <span class="token generics function"><span class="token punctuation">&lt;</span>T<span class="token punctuation">&gt;</span></span> <span class="token keyword">boolean</span> <span class="token function">putMapping</span><span class="token punctuation">(</span>Class<span class="token generics function"><span class="token punctuation">&lt;</span>T<span class="token punctuation">&gt;</span></span> clazz<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">if</span> <span class="token punctuation">(</span>clazz<span class="token punctuation">.</span><span class="token function">isAnnotationPresent</span><span class="token punctuation">(</span>Mapping<span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
			String mappingPath <span class="token operator">=</span> clazz<span class="token punctuation">.</span><span class="token function">getAnnotation</span><span class="token punctuation">(</span>Mapping<span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">mappingPath</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
			<span class="token keyword">if</span> <span class="token punctuation">(</span><span class="token function">isNotBlank</span><span class="token punctuation">(</span>mappingPath<span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
				String mappings <span class="token operator">=</span> <span class="token function">readFileFromClasspath</span><span class="token punctuation">(</span>mappingPath<span class="token punctuation">)</span><span class="token punctuation">;</span>
				<span class="token keyword">if</span> <span class="token punctuation">(</span><span class="token function">isNotBlank</span><span class="token punctuation">(</span>mappings<span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
					<span class="token keyword">return</span> <span class="token function">putMapping</span><span class="token punctuation">(</span>clazz<span class="token punctuation">,</span> mappings<span class="token punctuation">)</span><span class="token punctuation">;</span>
				<span class="token punctuation">}</span>
			<span class="token punctuation">}</span> <span class="token keyword">else</span> <span class="token punctuation">{</span>
				logger<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"mappingPath in @Mapping has to be defined. Building mappings using @Field"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
			<span class="token punctuation">}</span>
		<span class="token punctuation">}</span>
		ElasticsearchPersistentEntity<span class="token generics function"><span class="token punctuation">&lt;</span>T<span class="token punctuation">&gt;</span></span> persistentEntity <span class="token operator">=</span> <span class="token function">getPersistentEntityFor</span><span class="token punctuation">(</span>clazz<span class="token punctuation">)</span><span class="token punctuation">;</span>
		XContentBuilder xContentBuilder <span class="token operator">=</span> null<span class="token punctuation">;</span>
		<span class="token keyword">try</span> <span class="token punctuation">{</span>
			xContentBuilder <span class="token operator">=</span> <span class="token function">buildMapping</span><span class="token punctuation">(</span>clazz<span class="token punctuation">,</span> persistentEntity<span class="token punctuation">.</span><span class="token function">getIndexType</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> persistentEntity
					<span class="token punctuation">.</span><span class="token function">getIdProperty</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">getFieldName</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> persistentEntity<span class="token punctuation">.</span><span class="token function">getParentType</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token punctuation">}</span> <span class="token keyword">catch</span> <span class="token punctuation">(</span><span class="token class-name">Exception</span> e<span class="token punctuation">)</span> <span class="token punctuation">{</span>
			<span class="token keyword">throw</span> <span class="token keyword">new</span> <span class="token class-name">ElasticsearchException</span><span class="token punctuation">(</span><span class="token string">"Failed to build mapping for "</span> <span class="token operator">+</span> clazz<span class="token punctuation">.</span><span class="token function">getSimpleName</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> e<span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token punctuation">}</span>
		<span class="token keyword">return</span> <span class="token function">putMapping</span><span class="token punctuation">(</span>clazz<span class="token punctuation">,</span> xContentBuilder<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">public</span> <span class="token generics function"><span class="token punctuation">&lt;</span>T<span class="token punctuation">&gt;</span></span> <span class="token keyword">boolean</span> <span class="token function">putMapping</span><span class="token punctuation">(</span>Class<span class="token generics function"><span class="token punctuation">&lt;</span>T<span class="token punctuation">&gt;</span></span> clazz<span class="token punctuation">,</span> Object mapping<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token function">putMapping</span><span class="token punctuation">(</span><span class="token function">getPersistentEntityFor</span><span class="token punctuation">(</span>clazz<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">getIndexName</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> <span class="token function">getPersistentEntityFor</span><span class="token punctuation">(</span>clazz<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">getIndexType</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> mapping<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">public</span> <span class="token keyword">boolean</span> <span class="token function">putMapping</span><span class="token punctuation">(</span>String indexName<span class="token punctuation">,</span> String type<span class="token punctuation">,</span> Object mapping<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		Assert<span class="token punctuation">.</span><span class="token function">notNull</span><span class="token punctuation">(</span>indexName<span class="token punctuation">,</span> <span class="token string">"No index defined for putMapping()"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		Assert<span class="token punctuation">.</span><span class="token function">notNull</span><span class="token punctuation">(</span>type<span class="token punctuation">,</span> <span class="token string">"No type defined for putMapping()"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		PutMappingRequestBuilder requestBuilder <span class="token operator">=</span> client<span class="token punctuation">.</span><span class="token function">admin</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">indices</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
				<span class="token punctuation">.</span><span class="token function">preparePutMapping</span><span class="token punctuation">(</span>indexName<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">setType</span><span class="token punctuation">(</span>type<span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token keyword">if</span> <span class="token punctuation">(</span>mapping <span class="token keyword">instanceof</span> <span class="token class-name">String</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
			requestBuilder<span class="token punctuation">.</span><span class="token function">setSource</span><span class="token punctuation">(</span>String<span class="token punctuation">.</span><span class="token function">valueOf</span><span class="token punctuation">(</span>mapping<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token punctuation">}</span> <span class="token keyword">else</span> <span class="token keyword">if</span> <span class="token punctuation">(</span>mapping <span class="token keyword">instanceof</span> <span class="token class-name">Map</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
			requestBuilder<span class="token punctuation">.</span><span class="token function">setSource</span><span class="token punctuation">(</span><span class="token punctuation">(</span>Map<span class="token punctuation">)</span> mapping<span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token punctuation">}</span> <span class="token keyword">else</span> <span class="token keyword">if</span> <span class="token punctuation">(</span>mapping <span class="token keyword">instanceof</span> <span class="token class-name">XContentBuilder</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
			requestBuilder<span class="token punctuation">.</span><span class="token function">setSource</span><span class="token punctuation">(</span><span class="token punctuation">(</span>XContentBuilder<span class="token punctuation">)</span> mapping<span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token punctuation">}</span>
		<span class="token keyword">return</span> requestBuilder<span class="token punctuation">.</span><span class="token function">execute</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">actionGet</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">isAcknowledged</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
</code></pre>
<p><code>putMapping</code>有3个重载方法，我们调用的是第一个，其实经过处理，我们最终还是调用的还是第三个方法。是不是中间<code>indexName</code>获取的时候出现了问题呢？<br>
看一下是如何获取<code>indexName</code>的，<code>getPersistentEntityFor(clazz).getIndexName()</code>，跟进去看一下，其中有这么一个方法：</p>
<pre><code class="prism language-java">	<span class="token keyword">public</span> E <span class="token function">getPersistentEntity</span><span class="token punctuation">(</span>Class<span class="token operator">&lt;</span><span class="token operator">?</span><span class="token operator">&gt;</span> type<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token function">getPersistentEntity</span><span class="token punctuation">(</span>ClassTypeInformation<span class="token punctuation">.</span><span class="token function">from</span><span class="token punctuation">(</span>type<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
</code></pre>
<p>跟进<code>from</code>方法</p>
<pre><code class="prism language-java">	<span class="token keyword">private</span> <span class="token keyword">static</span> <span class="token keyword">final</span> Map<span class="token operator">&lt;</span>Class<span class="token operator">&lt;</span><span class="token operator">?</span><span class="token operator">&gt;</span><span class="token punctuation">,</span> Reference<span class="token operator">&lt;</span>ClassTypeInformation<span class="token operator">&lt;</span><span class="token operator">?</span><span class="token operator">&gt;&gt;&gt;</span> CACHE <span class="token operator">=</span> Collections
			<span class="token punctuation">.</span><span class="token function">synchronizedMap</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">WeakHashMap</span><span class="token operator">&lt;</span>Class<span class="token operator">&lt;</span><span class="token operator">?</span><span class="token operator">&gt;</span><span class="token punctuation">,</span> Reference<span class="token operator">&lt;</span>ClassTypeInformation<span class="token operator">&lt;</span><span class="token operator">?</span><span class="token operator">&gt;&gt;&gt;</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

	<span class="token keyword">static</span> <span class="token punctuation">{</span>
		<span class="token keyword">for</span> <span class="token punctuation">(</span>ClassTypeInformation<span class="token operator">&lt;</span><span class="token operator">?</span><span class="token operator">&gt;</span> info <span class="token operator">:</span> Arrays<span class="token punctuation">.</span><span class="token function">asList</span><span class="token punctuation">(</span>COLLECTION<span class="token punctuation">,</span> LIST<span class="token punctuation">,</span> SET<span class="token punctuation">,</span> MAP<span class="token punctuation">,</span> OBJECT<span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
			CACHE<span class="token punctuation">.</span><span class="token function">put</span><span class="token punctuation">(</span>info<span class="token punctuation">.</span><span class="token function">getType</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> <span class="token keyword">new</span> <span class="token class-name">WeakReference</span><span class="token operator">&lt;</span>ClassTypeInformation<span class="token operator">&lt;</span><span class="token operator">?</span><span class="token operator">&gt;&gt;</span><span class="token punctuation">(</span>info<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token punctuation">}</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">private</span> <span class="token keyword">final</span> Class<span class="token generics function"><span class="token punctuation">&lt;</span>S<span class="token punctuation">&gt;</span></span> type<span class="token punctuation">;</span>

	<span class="token comment">/**
	 * Simple factory method to easily create new instances of {@link ClassTypeInformation}.
	 * 
	 * @param &lt;S&gt;
	 * @param type must not be {@literal null}.
	 * @return
	 */</span>
	<span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token generics function"><span class="token punctuation">&lt;</span>S<span class="token punctuation">&gt;</span></span> ClassTypeInformation<span class="token generics function"><span class="token punctuation">&lt;</span>S<span class="token punctuation">&gt;</span></span> <span class="token function">from</span><span class="token punctuation">(</span>Class<span class="token generics function"><span class="token punctuation">&lt;</span>S<span class="token punctuation">&gt;</span></span> type<span class="token punctuation">)</span> <span class="token punctuation">{</span>

		Assert<span class="token punctuation">.</span><span class="token function">notNull</span><span class="token punctuation">(</span>type<span class="token punctuation">,</span> <span class="token string">"Type must not be null!"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

		Reference<span class="token operator">&lt;</span>ClassTypeInformation<span class="token operator">&lt;</span><span class="token operator">?</span><span class="token operator">&gt;&gt;</span> cachedReference <span class="token operator">=</span> CACHE<span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span>type<span class="token punctuation">)</span><span class="token punctuation">;</span>
		TypeInformation<span class="token operator">&lt;</span><span class="token operator">?</span><span class="token operator">&gt;</span> cachedTypeInfo <span class="token operator">=</span> cachedReference <span class="token operator">==</span> null <span class="token operator">?</span> null <span class="token operator">:</span> cachedReference<span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

		<span class="token keyword">if</span> <span class="token punctuation">(</span>cachedTypeInfo <span class="token operator">!=</span> null<span class="token punctuation">)</span> <span class="token punctuation">{</span>
			<span class="token keyword">return</span> <span class="token punctuation">(</span>ClassTypeInformation<span class="token generics function"><span class="token punctuation">&lt;</span>S<span class="token punctuation">&gt;</span></span><span class="token punctuation">)</span> cachedTypeInfo<span class="token punctuation">;</span>
		<span class="token punctuation">}</span>

		ClassTypeInformation<span class="token generics function"><span class="token punctuation">&lt;</span>S<span class="token punctuation">&gt;</span></span> result <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">ClassTypeInformation</span><span class="token generics function"><span class="token punctuation">&lt;</span>S<span class="token punctuation">&gt;</span></span><span class="token punctuation">(</span>type<span class="token punctuation">)</span><span class="token punctuation">;</span>
		CACHE<span class="token punctuation">.</span><span class="token function">put</span><span class="token punctuation">(</span>type<span class="token punctuation">,</span> <span class="token keyword">new</span> <span class="token class-name">WeakReference</span><span class="token operator">&lt;</span>ClassTypeInformation<span class="token operator">&lt;</span><span class="token operator">?</span><span class="token operator">&gt;&gt;</span><span class="token punctuation">(</span>result<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token keyword">return</span> result<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
</code></pre>
<p>原来如此，<code>springframework</code>包中的这个方法会把类信息缓存再<code>Map</code>中，导致我们之后取的都是历史值。<br>
那么下面就很容易了，我们只需要在调用<code>public boolean putMapping(String indexName, String type, Object mapping)</code>方法的时候手动赋上<code>indexName</code>就可以了，这就需要我们手动构建一个mapping，也就是生成一个<code>XContentBuilder</code> ,最终代码如下</p>
<pre><code class="prism language-java">    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">createIndexMapping</span><span class="token punctuation">(</span>Class<span class="token operator">&lt;</span><span class="token operator">?</span><span class="token operator">&gt;</span> clazz<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token comment">// 我们采用的策略是当天晚上执行定时任务，提前创建第二天的索引和mapping</span>
        String index <span class="token operator">=</span> <span class="token function">dateAdd</span><span class="token punctuation">(</span><span class="token number">1</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        Document annotation <span class="token operator">=</span> <span class="token punctuation">(</span>Document<span class="token punctuation">)</span> clazz<span class="token punctuation">.</span><span class="token function">getAnnotation</span><span class="token punctuation">(</span>Document<span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        ReflectUtil<span class="token punctuation">.</span><span class="token function">changeAnnotationValue</span><span class="token punctuation">(</span>annotation<span class="token punctuation">,</span> <span class="token string">"indexName"</span><span class="token punctuation">,</span> index<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token function">insertIndexMapping</span><span class="token punctuation">(</span>clazz<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">private</span> <span class="token keyword">boolean</span> <span class="token function">insertIndexMapping</span><span class="token punctuation">(</span>Class<span class="token operator">&lt;</span><span class="token operator">?</span><span class="token operator">&gt;</span> clazz<span class="token punctuation">)</span> <span class="token punctuation">{</span>

        Document annotation <span class="token operator">=</span> <span class="token punctuation">(</span>Document<span class="token punctuation">)</span> clazz<span class="token punctuation">.</span><span class="token function">getAnnotation</span><span class="token punctuation">(</span>Document<span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        String indexName <span class="token operator">=</span> annotation<span class="token punctuation">.</span><span class="token function">indexName</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        ElasticsearchPersistentEntity<span class="token operator">&lt;</span><span class="token operator">?</span><span class="token operator">&gt;</span> persistentEntity <span class="token operator">=</span> elasticsearchTemplate<span class="token punctuation">.</span><span class="token function">getPersistentEntityFor</span><span class="token punctuation">(</span>clazz<span class="token punctuation">)</span><span class="token punctuation">;</span>

        XContentBuilder xContentBuilder <span class="token operator">=</span> null<span class="token punctuation">;</span>
        <span class="token keyword">try</span> <span class="token punctuation">{</span>
            xContentBuilder <span class="token operator">=</span> MappingBuilder<span class="token punctuation">.</span><span class="token function">buildMapping</span><span class="token punctuation">(</span>clazz<span class="token punctuation">,</span> persistentEntity<span class="token punctuation">.</span><span class="token function">getIndexType</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> <span class="token string">""</span><span class="token punctuation">,</span>
                    persistentEntity<span class="token punctuation">.</span><span class="token function">getParentType</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span> <span class="token keyword">catch</span> <span class="token punctuation">(</span><span class="token class-name">IOException</span> e<span class="token punctuation">)</span> <span class="token punctuation">{</span>
            LOGGER<span class="token punctuation">.</span><span class="token function">error</span><span class="token punctuation">(</span><span class="token string">".insertIndexMapping() IOException={}"</span><span class="token punctuation">,</span> e<span class="token punctuation">.</span><span class="token function">getMessage</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> e<span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token keyword">throw</span> <span class="token keyword">new</span> <span class="token class-name">RuntimeException</span><span class="token punctuation">(</span>e<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>

        <span class="token keyword">boolean</span> createIndexResult <span class="token operator">=</span> elasticsearchTemplate<span class="token punctuation">.</span><span class="token function">createIndex</span><span class="token punctuation">(</span>indexName<span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token keyword">if</span> <span class="token punctuation">(</span>createIndexResult<span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token keyword">boolean</span> putMappingResult <span class="token operator">=</span> elasticsearchTemplate<span class="token punctuation">.</span><span class="token function">putMapping</span><span class="token punctuation">(</span>indexName<span class="token punctuation">,</span> persistentEntity<span class="token punctuation">.</span><span class="token function">getIndexType</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span>
                    xContentBuilder<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>
</code></pre>
<p>需要注意的是源代码中<code>MappingBuilder.buildMapping</code>方法的访问修饰符是default，所以无法访问到，需要将源代码拷贝一份到相同包下</p>
<p>到这里，定时创建/删除索引、创建mapping、动态索引名、写入ES数据就都介绍完了。</p>
<h2><a id="springelasticsearch_718"></a>四、spring-elasticsearch中的查询操作</h2>
<p>简单介绍一下spring-es中的查询，主要使用<code>queryForList</code>方法</p>
<pre><code class="prism language-java">	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">public</span> <span class="token generics function"><span class="token punctuation">&lt;</span>T<span class="token punctuation">&gt;</span></span> List<span class="token generics function"><span class="token punctuation">&lt;</span>T<span class="token punctuation">&gt;</span></span> <span class="token function">queryForList</span><span class="token punctuation">(</span>SearchQuery query<span class="token punctuation">,</span> Class<span class="token generics function"><span class="token punctuation">&lt;</span>T<span class="token punctuation">&gt;</span></span> clazz<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token function">queryForPage</span><span class="token punctuation">(</span>query<span class="token punctuation">,</span> clazz<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">getContent</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
</code></pre>
<p>ES的查询方法需要的参数<code>SearchQuery</code>是一个接口，有一个实现类叫<code>NativeSearchQuery</code>，实际使用中，我们的主要任务就是构建<code>NativeSearchQuery</code>来完成一些复杂的查询的。</p>
<p>多数情况需要使用实现类<code>BoolQueryBuilder</code>进行组合查询：</p>
<h5><a id="must_730"></a>must</h5>
<p>代表返回的文档必须满足must子句的条件，会参与计算分值；</p>
<h5><a id="filter_732"></a>filter</h5>
<p>代表返回的文档必须满足filter子句的条件，但不会参与计算分值；</p>
<h5><a id="should_734"></a>should</h5>
<p>代表返回的文档可能满足should子句的条件，也可能不满足，有多个should时满足任何一个就可以，通过minimum_should_match设置至少满足几个。</p>
<h5><a id="mustnot_736"></a>mustnot</h5>
<p>代表必须不满足子句的条件。</p>
<p><code>termQuery</code>、<code>termsQuery</code>为精确查询，<code>matchQuery</code>为模糊查询，<code>matchPhraseQuery</code>为短语匹配，类似mysql的%xxx%</p>
<p>直接上代码举个例子让大家更好理解：</p>
<pre><code class="prism language-java">        <span class="token comment">// 构建QueryBuilder</span>
        <span class="token comment">// 精确查找出开发者名为"tecent",游戏名包含"刺激战场"这个短语，精确匹配支付状态不为"UNPAID"和"PAYING",支付时间在"20181101"和"20181102"之间</span>
        BoolQueryBuilder bqb <span class="token operator">=</span> QueryBuilders<span class="token punctuation">.</span><span class="token function">boolQuery</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">filter</span><span class="token punctuation">(</span>QueryBuilders<span class="token punctuation">.</span><span class="token function">termQuery</span><span class="token punctuation">(</span><span class="token string">"appDeveloper"</span><span class="token punctuation">,</span> <span class="token string">"tecent"</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
                <span class="token punctuation">.</span><span class="token function">filter</span><span class="token punctuation">(</span>QueryBuilders<span class="token punctuation">.</span><span class="token function">matchPhraseQuery</span><span class="token punctuation">(</span><span class="token string">"packageName"</span><span class="token punctuation">,</span> <span class="token string">"刺激战场"</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
                <span class="token punctuation">.</span><span class="token function">mustNot</span><span class="token punctuation">(</span>QueryBuilders<span class="token punctuation">.</span><span class="token function">termsQuery</span><span class="token punctuation">(</span><span class="token string">"payStatus"</span><span class="token punctuation">,</span> <span class="token string">"UNPAID"</span><span class="token punctuation">,</span> <span class="token string">"PAYING"</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
                <span class="token punctuation">.</span><span class="token function">filter</span><span class="token punctuation">(</span>QueryBuilders<span class="token punctuation">.</span><span class="token function">rangeQuery</span><span class="token punctuation">(</span><span class="token string">"PAYTIME"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">gte</span><span class="token punctuation">(</span><span class="token string">"20181101"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">lte</span><span class="token punctuation">(</span><span class="token string">"20181102"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token comment">// 按照支付时间降序排序</span>
        Sort sort <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Sort</span><span class="token punctuation">(</span>Direction<span class="token punctuation">.</span>DESC<span class="token punctuation">,</span> <span class="token string">"PAYTIME"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token comment">// JPA规定中 页码是从0开始的</span>
        Pageable pageable <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">PageRequest</span><span class="token punctuation">(</span><span class="token number">0</span><span class="token punctuation">,</span> <span class="token number">10</span><span class="token punctuation">,</span> sort<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token comment">// 构建NativeSearchQuery</span>
        NativeSearchQuery query <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">NativeSearchQueryBuilder</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">withIndices</span><span class="token punctuation">(</span><span class="token string">"20181101"</span><span class="token punctuation">,</span> <span class="token string">"20181102"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">withTypes</span><span class="token punctuation">(</span><span class="token string">"order"</span><span class="token punctuation">)</span>
                <span class="token punctuation">.</span><span class="token function">withFilter</span><span class="token punctuation">(</span>bqb<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">withPageable</span><span class="token punctuation">(</span>pageable<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">build</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        elasticsearchTemplate<span class="token punctuation">.</span><span class="token function">queryForList</span><span class="token punctuation">(</span>query<span class="token punctuation">,</span> OrderEs<span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre>
<p>这里只是简单介绍一下，更多内容请参考官方文档</p>
<h2><a id="_762"></a>五、结语</h2>
<p>监控系统的第二步Elasticsearch已经全部结束了，下面将介绍第三步：Grafana展示</p>
<h4><a id="_765"></a>参考文档</h4>
<p><a href="https://blog.csdn.net/laoyang360/article/details/52244917" rel="nofollow">Elasticsearch学习，请先看这一篇</a><br>
<a href="https://www.cnblogs.com/softidea/p/5891552.html" rel="nofollow">elasticsearch spring 集成</a><br>
<a href="https://www.cnblogs.com/liqipeng/p/7657854.html" rel="nofollow">spring-data-elasticsearch使用笔记</a><br>
<a href="https://blog.csdn.net/tianyaleixiaowu/article/details/76149547" rel="nofollow">ElasticSearchRepository和ElasticSearchTemplate的使用</a></p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>