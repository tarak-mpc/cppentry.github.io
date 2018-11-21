---
layout:     post
title:      Apache Kafka 入门 - Kafka-manager的基本配置和运行
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p><a href="https://blog.csdn.net/isea533/article/details/73727485" rel="nofollow">https://blog.csdn.net/isea533/article/details/73727485</a></p>

<h1 id="apache-kafka-入门">Apache Kafka 入门</h1>

<ul><li>Kafka的基本配置和运行</li>
	<li>Kafka命令详细介绍</li>
	<li>Kafka-manager的基本配置和运行</li>
	<li>Kafka API 简单用法</li>
	<li>Spring Boot 集成Kafka</li>
</ul><p>Kafka支持Linux和WIndows环境，本文运行环境使用Linux（CentOS）。</p>

<p>本篇为第三篇。</p>

<h2 id="kafka-manager的基本配置和运行"><a name="t1"></a>Kafka-manager的基本配置和运行</h2>

<p>想要查看和管理Kafka，完全使用命令并不方便，我们可以使用雅虎开源的Kafka-manager，GitHub地址如下：</p>

<blockquote>
<p><a href="https://github.com/yahoo/kafka-manager" rel="nofollow">https://github.com/yahoo/kafka-manager</a></p>
</blockquote>

<p>我们可以使用Git或者直接从Releases中下载，此处从下面的地址下载 1.3.3.7 版本：</p>

<blockquote>
<p><a href="https://github.com/yahoo/kafka-manager/releases" rel="nofollow">https://github.com/yahoo/kafka-manager/releases</a></p>
</blockquote>

<p>下载完成后解压。</p>

<blockquote>
<p>注意：上面下载的是源码，下载后需要按照后面步骤进行编译。如果觉得麻烦，可以直接从下面地址下载编译好的 kafka-manager-1.3.3.7.zip。 <br>
链接: <a href="http://pan.baidu.com/s/1qY8sGoO" rel="nofollow">http://pan.baidu.com/s/1qY8sGoO</a> 密码: ye7b</p>
</blockquote>

<h3 id="准备"><a name="t2"></a>准备</h3>

<blockquote>
<p>请先准备好Java8环境！</p>
</blockquote>

<p>在开始安装官方文档进行配置前，因为Kafka-manager使用的<a href="https://www.playframework.com/" rel="nofollow">Play框架</a>，为了编译的速度更快，先配置sbt的maven仓库，由于默认仓库速度较慢，因此使用aliyun提供的maven仓库。</p>

<p>通过 <code>cd ~</code>进入当前用户目录，然后通过命令<code>mkdir .sbt</code>创建<code>.sbt</code>目录，进入创建的该目录，使用vi创建<code>repositories</code>文件，编辑内容如下：</p>

<pre class="has">
<code>[repositories]
local
aliyun: http://maven.aliyun.com/nexus/content/groups/public
typesafe: http://repo.typesafe.com/typesafe/ivy-releases/, [organization]/[module]/(scala_[scalaVersion]/)(sbt_[sbtVersion]/)[revision]/[type]s/[artifact](-[classifier]).[ext], bootOnly</code></pre>

<ul><li>1</li>
	<li>2</li>
	<li>3</li>
	<li>4</li>
</ul><blockquote>
<p>配置参考：<a href="http://www.scala-sbt.org/0.13.2/docs/Detailed-Topics/Library-Management.html#override-all-resolvers-for-all-builds" rel="nofollow">http://www.scala-sbt.org/0.13.2/docs/Detailed-Topics/Library-Management.html#override-all-resolvers-for-all-builds</a></p>
</blockquote>

<p>然后进入解压后的 Kafka-manager 目录，执行下面的命令：</p>

<pre class="has">
<code>&gt; ./sbt clean dist</code></pre>

<ul><li>1</li>
</ul><p>如果之前从来没有用过Play框架，这个步骤会需要一定时间，主要是下载依赖的jar包，使用aliyun的私服可以让这个过程缩短很多。</p>

<p>命令执行完成后，在 <code>target/universal</code> 目录中会生产一个zip压缩包<code>kafka-manager-1.3.3.7.zip</code>。将压缩包拷贝到要部署的目录下解压。</p>

<p>在解压后的<code>conf</code>目录中打开 <code>application.conf</code>文件，修改其中的配置信息，最主要的内容为：</p>

<pre class="has">
<code>kafka-manager.zkhosts="localhost:2181"</code></pre>

<ul><li>1</li>
</ul><p>配置为Kafka的 zookeeper 服务器。你还可以通过环境变量<code>ZK_HOSTS</code>配置这个参数值。</p>

<p>其他详细的配置信息参考官方文档：<a href="https://github.com/yahoo/kafka-manager" rel="nofollow">https://github.com/yahoo/kafka-manager</a></p>

<h3 id="启动"><a name="t3"></a>启动</h3>

<p>在解压的目录中，使用下面的命令启动Kafka-manager。</p>

<pre class="has">
<code>&gt; bin/kafka-manager</code></pre>

<ul><li>1</li>
</ul><p>默认情况下端口为9000，你还可以通过下面的命令指定配置文件和端口：</p>

<pre class="has">
<code>&gt; bin/kafka-manager -Dconfig.file=/path/to/application.conf -Dhttp.port=8080</code></pre>

<ul><li>1</li>
</ul><p>如果没有配置Java的环境变量，还可以通过下面的方式指定Java8的目录：</p>

<pre class="has">
<code>&gt; bin/kafka-manager -java-home /usr/local/oracle-java-8</code></pre>

<ul><li>1</li>
</ul><p>启动后，从浏览器打开，显示如下：</p>

<p><img alt="这里写图片描述" class="has" src="https://img-blog.csdn.net/20170625192841911?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaXNlYTUzMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast"></p>

<h3 id="配置集群"><a name="t4"></a>配置集群</h3>

<p>点击【Cluster】&gt;【Add Cluster】打开如下添加集群的配置界面：</p>

<p><img alt="这里写图片描述" class="has" src="https://img-blog.csdn.net/20170625192943674?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaXNlYTUzMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast"></p>

<p>输入集群的名字（如<code>Kafka-Cluster-1</code>）和 Zookeeper 服务器地址（如<code>localhost:2181</code>）。</p>

<p>选择最接近的Kafka版本（如<code>0.10.1.0</code>）。</p>

<blockquote>
<p>注意：如果没有在 Kafka 中配置过 JMX_PORT，千万不要选择第一个复选框。</p>

<p><code>Enable JMX Polling</code></p>

<p>如果选择了该复选框，Kafka-manager 可能会无法启动。</p>
</blockquote>

<p>其他broker的配置可以根据自己需要进行配置，默认情况下，点击【保存】时，会提示几个默认值为<code>1</code>的配置错误，需要配置为<code>&gt;=2</code>的值。保存成功后，提示如下。</p>

<p><img alt="这里写图片描述" class="has" src="https://img-blog.csdn.net/20170625193400791?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaXNlYTUzMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast"></p>

<p>点击【Go to cluster view.】打开当前的集群界面。</p>

<p><img alt="这里写图片描述" class="has" src="https://img-blog.csdn.net/20170625193501068?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaXNlYTUzMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast"></p>

<p>在集群界面显示了主题和Broker的个数，点击数字可以查看具体的信息。</p>

<p>同时在顶部多了好几个菜单，可以查看集群、Broker、主题等信息。</p>

<h3 id="新建主题"><a name="t5"></a>新建主题</h3>

<p>点击【Topic】&gt;【Create】可以方便的创建并配置主题。如下显示。</p>

<p><img alt="这里写图片描述" class="has" src="https://img-blog.csdn.net/20170625193513512?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaXNlYTUzMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast"></p>

<p>这里只在Topic中输入<code>t1</code>(或其他名字)，分区和复制因子这里简单说明一下，详细的可以查看官方介绍。</p>

<p><img alt="这里写图片描述" class="has" src="https://img-blog.csdn.net/20170625193528575?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaXNlYTUzMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast"></p>

<p>在上图一个Kafka集群中，有两个服务器，每个服务器上都有2个分区。P0,P3可能属于同一个主题，也可能是两个不同的主题。</p>

<p>如果设置的Partitons和Replication Factor都是2，这种情况下该主题的分步就和上图中Kafka集群显示的相同，此时P0,P3是同一个主题的两个分区。P1,P2也是同一个主题的两个分区，Server1和Server2其中一个会作为Leader进行读写操作，另一个通过复制进行同步。</p>

<p>如果设置的Partitons和Replication Factor都是1，这时只会根据算法在某个Server上创建一个分区，可以是P0~4中的某一个（分区都是新建的，不是先存在4个然后从中取1个）。</p>

<p>这里我们都设置为<code>1</code>，点击【Create】然后进入创建的这个主题，显示如下。</p>

<p><img alt="这里写图片描述" class="has" src="https://img-blog.csdn.net/20170625193558455?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaXNlYTUzMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast"></p>

<p>这里显示了主题的基本信息，在右侧中间的Broker这里显示了当前的Broker Id为0，分区数为1，如果集群中存在多个服务，在创建该主题时，不一定会分配到哪个Broker。</p>

<p>这个页面的右上部分显示了针对主题的一些操作。这些操作相对命令的方式简单和直观了很多。</p>

<h3 id="其他操作"><a name="t6"></a>其他操作</h3>

<p>关于kafka-manager的其他功能和操作可以参考官网：<a href="https://github.com/yahoo/kafka-manager" rel="nofollow">https://github.com/yahoo/kafka-manager</a>。</p>            </div>
                </div>