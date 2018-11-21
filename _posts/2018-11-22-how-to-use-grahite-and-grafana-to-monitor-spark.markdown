---
layout:     post
title:      how-to-use-grahite-and-grafana-to-monitor-spark
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>how-to-use-grahite-and-grafana-to-monitor-spark</p>

<hr>

<p>写作缘由：最近看到spark社区有人使用 graphite-grafana监控spark， <br>
spark-developers-list <a href="http://apache-spark-developers-list.1001551.n3.nabble.com/Monitoring-Spark-with-Graphite-and-Grafana-td10804.html#a10805" rel="nofollow">Monitoring Spark with Graphite and Grafana</a> <br>
hammerlab <a href="http://www.hammerlab.org/2015/02/27/monitoring-spark-with-graphite-and-grafana/" rel="nofollow">Monitoring Spark with Graphite and Grafana</a> <br>
github <a href="https://github.com/hammerlab/grafana-spark-dashboards" rel="nofollow">grafana-spark-dashboards</a></p>

<p>因为工作中涉及spark监控，故测试一下，记录如下：</p>

<p>首先，看看graphite+grafana的监控效果：</p>

<ul>
<li>graphite-web展示的效果：</li>
</ul>

<p>自己测试的web展示，对metric的graph 定制操作还不太熟悉，比较原始不美观，这里先看看<a href="http://www.jsxubar.info/graphite-introduction.html/" rel="nofollow">Graphite 简介</a>里的两张图片。 <br>
<img src="http://www.jsxubar.info/wp-content/uploads/2012/06/Graphite-Browser-Web-Interface-600x369.jpg" alt="Graphite brower web interface" title="Graphite brower web interface"> <br>
Graphite brower web interface <br>
<img src="http://www.jsxubar.info/wp-content/uploads/2012/06/Graphite-Command-Line-Web-Interface-600x339.jpg" alt="Graphite CLI web interface" title="Graphite CLI web interface"> <br>
Graphite CLI web interface</p>

<ul>
<li>grafana-web的监控 spark 的展示效果：感觉比 ganglia 的效果好一些 <br>
自己测试的web展示，对metric的graph 定制操作还不太熟悉，比较原始不美观，这里先看看hammerlab一篇博文<a href="http://www.hammerlab.org/2015/02/27/monitoring-spark-with-graphite-and-grafana/" rel="nofollow">Monitoring Spark with Graphite and Grafana</a>里的几张图片。 <br>
<img src="http://www.hammerlab.org/images/rdh-tasks.png" alt="Task Completion Rate" title="Task Completion Rate"> <br>
Task Completion Rate <br>
<img src="http://www.hammerlab.org/images/hdfs-graphs.png" alt="HDFS I/O" title="HDFS I/O"> <br>
HDFS I/O <br>
<img src="http://www.hammerlab.org/images/per-executor-jvm-metrics.png" alt="JVM statistics exported by Spark" title="JVM statistics exported by Spark"> <br>
JVM statistics exported by Spark</li>
</ul>

<hr>

<h1 id="1-graphite-简介">1 graphite 简介</h1>

<p>1)是什么？发展情况</p>

<blockquote>
  <p>Graphite is an enterprise-scale monitoring tool that runs well on <br>
  cheap hardware. It was originally designed and written by Chris Davis <br>
  at Orbitz in 2006 as side project that ultimately grew to be a <br>
  foundational monitoring tool. In 2008, Orbitz allowed Graphite to be <br>
  released under the open source Apache 2.0 license. Since then Chris <br>
  has continued to work on Graphite and has deployed it at other <br>
  companies including Sears, where it serves as a pillar of the <br>
  e-commerce monitoring system. Today many large companies use it.</p>
  
  <p>Graphite是一个企业级的监控工具，可以在廉价机硬件上运行。 最初由Chris Davis在 Orbitz <br>
  工作时，作为一个辅助项目在2006年使用Python语言编写，最终成一个基本的监控工具。 在2008，Orbitz <br>
  允许软件以开源Apache 2.0 license的授权方式发行。 从那之后 Chris继续开发这个软件，并将其部署在其它公司，包括 <br>
  Sears, 使得Graphite成为电子商务监控系统的一个支柱。今天有很多大公司使用它。</p>
</blockquote>

<p>闲话<a href="http://www.orbitz.com/" rel="nofollow">Orbitz</a> </p>

<blockquote>
  <p>Orbitz Worldwide公司(Nasdaq:OWW)是全球领先的在线旅游公司，采用创新的技术帮助休闲和商务旅行者研究、计划和预订全面的旅行产品 <br>
  Orbitz成立于1999年，当时，美国的旅游业发展迅速，Orbitz也就趁势而入。Orbitz的航空公司投资者包括全美航空，三角洲航空，西北航空公司和联合航空公司。Orbitz网站成立于2001年，在2004年11月的时候被Cendant收购，成为了Cendant的国内在线旅游服务部门，于2007年上市。 <br>
  20150213, <br>
  艺龙（纳斯达克股票代码：LONG）网的大股东——在线旅游服务网站Expedia将以每股12美元的价格(16亿美元)收购另一家美国在线旅游网站Orbitz，以此向美国最大在线旅游服务网站Priceline发起挑战。</p>
</blockquote>

<p>2)功能，能做什么？</p>

<blockquote>
  <p>What Graphite is and is not Graphite does two things:</p>
  
  <p>Store numeric time-series data Render graphs of this data on demand <br>
  What Graphite does not do is collect data for you, however there are <br>
  some tools out there that know how to send data to graphite. Even <br>
  though it often requires a little code, sending data to Graphite is <br>
  very simple.</p>
  
  <p>Graphite 功能 Graphite是一个画图工具？，将数据以图形的方式展现出来。它主要做两件事：  <br>
   - 存储时间序列数据 <br>
   - 根据需要呈现数据的图形  <br>
  Graphite不收集数据，有一些工具知道如何发送数据给Graphite</p>
</blockquote>

<p>3)架构 <br>
<img src="http://graphite.readthedocs.org/en/latest/_images/overview.png" alt="graphite 架构图1" title=""> <br>
graphite 架构图1</p>

<p><img src="http://graphite.wikidot.com/local--files/high-level-diagram/Graphite%20High%20Level.png" alt="graphite架构图2" title=""> <br>
graphite 架构图2</p>

<p>4) <br>
参考资料 <br>
#graphite <br>
英文 <br>
<a href="http://graphite.wikidot.com" rel="nofollow">graphite wikidot/website</a> <br>
<a href="http://graphite.wikidot.com/high-level-diagram" rel="nofollow">graphite high level diagram</a> <br>
文章中有架构图 <br>
<a href="https://wiki.icinga.org/display/howtos/graphite" rel="nofollow">Graphite wiki how-to</a></p>

<p><a href="http://graphite.readthedocs.org/en/latest/overview.html" rel="nofollow">graphite docs Overview</a> <br>
<a href="http://graphite.readthedocs.org/en/latest/faq.html" rel="nofollow">graphite docs FQA</a> <br>
最后有一张架构图 <br>
<a href="https://answers.launchpad.net/graphite" rel="nofollow">Questions forum on Launchpad</a></p>

<p><a href="http://graphite.wikidot.com/installation" rel="nofollow">graphite website installation</a> <br>
<a href="http://graphite.readthedocs.org/en/latest/install.html" rel="nofollow">graphite docs installation</a></p>

<p>[10 Things I Learned Deploying Graphite — Kevin McCarthy]<a href="https://kevinmccarthy.org/blog/2013/07/18/10-things-i-learned-deploying-graphite/" rel="nofollow">https://kevinmccarthy.org/blog/2013/07/18/10-things-i-learned-deploying-graphite/</a></p>

<p>中文 <br>
<a href="http://www.jsxubar.info/graphite-introduction.html/" rel="nofollow">Graphite 简介</a> <br>
该文部分是对 [graphite Overview] 的翻译 <br>
<a href="http://www.jsxubar.info/graphite-installation.html" rel="nofollow">Graphite 安装和常见问题</a> <br>
该文部分是对 [graphite docs installation] 的翻译</p>

<p><a href="http://www.jsxubar.info/carbon-faq-graphite.html" rel="nofollow">Carbon FAQ – Graphite 中文版</a> <br>
<a href="http://www.jsxubar.info/graphite-url-api.html" rel="nofollow">Graphite Url Api 教程</a> <br>
<a href="http://www.jsxubar.info/graphite-dashboard.html/" rel="nofollow">Graphite dashboard 使用指南</a> <br>
<a href="http://www.jsxubar.info/graphite-cli.html" rel="nofollow">Graphite CLI 教程</a></p>

<hr>

<h1 id="2-为什么关注-graphite">2 为什么关注 graphite ？</h1>

<p><strong>spark 监控的技术有哪些？</strong></p>

<ul>
<li>1) ganglia</li>
</ul>

<blockquote>
  <p>组件 gmond  //client端， c编写 gmetad   //server端， c编写 rrd      //存储 <br>
  gweb  //php编写，模板框架  httpd/nginx + phpd    //提供 web服务</p>
  
  <p>memcached //gmetad 写 io 优化</p>
  
  <p>优点：安装部署简单，spark metricsSystem 支持，能够进行常用指标的实时与历史信息 <br>
   不足：不支持告警，graph控制不够灵活</p>
</blockquote>

<ul>
<li><p>2) nagios <br>
组件和优点/不足，待整理补充</p></li>
<li><p>3) ambari(ganglia + nagios + puppet + hadoop)</p></li>
</ul>

<blockquote>
  <p>组件  <br>
  ambari-agent  <br>
  ambari-server  <br>
  ambari-web</p>
</blockquote>

<p>优点与不足：待整理补充</p>

<ul>
<li>4) graphite + grafana <br>
架构：</li>
</ul>

<blockquote>
  <p>Graphite consists of 3 software components: carbon - a Twisted daemon <br>
  that listens for time-series data whisper - a simple database library <br>
  for storing time-series data (similar in design to RRD) graphite <br>
  webapp - A Django webapp that renders graphs on-demand using Cairo</p>
  
  <p>Graphite由三个软件组件组成： carbon - 一个Twisted守护进程，监听时间序列数据 whisper – <br>
  一个简单的数据库库，用来存储时间序列数据，在设计上类似于RRD graphite webapp – Django webapp，使用 <br>
  Cairo来根据需要呈现图形</p>
</blockquote>

<p>闲话<a href="https://twistedmatrix.com/trac/" rel="nofollow">Twisted</a></p>

<blockquote>
  <p>Twisted is an event-driven networking engine written in Python and <br>
  licensed under the open source <br>
  <a href="http://blog.sina.com.cn/s/blog_704b6af70100py9n.html" rel="nofollow">我看到的最棒的Twisted入门教程！</a></p>
</blockquote>

<p><strong><em>Grafana</em></strong></p>

<blockquote>
  <p>Graphite-web 自带的界面不够美观，这里使用 grafana ，</p>
  
  <p>Grafana is a general purpose dashboard and graph composer. It’s <br>
  focused on providing rich ways to visualize time series metrics, <br>
  mainly though graphs but supports other ways to visualize data through <br>
  a pluggable panel architecture. It currently has rich support for for <br>
  Graphite, InfluxDB and OpenTSDB. But supports other data sources via <br>
  plugins. <br>
  grafana是一种通用的仪表板和图形的专业生成软件。它专注于提供丰富的方式来可视化时间序列指标，主要通过图形，也支持通过一个可插拔的面板结构提供其他的方式可视化数据。它目前对 <br>
  Graphite, InfluxDB and OpenTSDB 数据源支持非常丰富。也可以通过插件支持其他数据源。</p>
</blockquote>

<p>部署测试graphite+grafana监控spark要安装的相关软件</p>

<blockquote>
  <p>python+easy_install+pip  <br>
  Django  <br>
  carbon    //server端, 接收 metrics, python编写 <br>
  whisper   //server端, 存储 metrics， python编写  <br>
  graphite-web  // web前端展示 <br>
  httpd/nginx <br>
  grafana   //web 前端展示</p>
  
  <p>memcached //io优化</p>
  
  <p>hammerlab/grafana-spark-dashboards</p>
</blockquote>

<p>优点：待整理 <br>
结合grafana 展示的图表确实很艳丽（参考上面的图表） <br>
不足： <br>
涉及技术多 <br>
测试过程，部署复杂；使用不够简单</p>

<p>相关技术： <br>
<a href="http://graphite.readthedocs.org/en/latest/tools.html" rel="nofollow">Tools That Work With Graphite</a></p>

<p>如何开发graphite-web <br>
<a href="http://graphite.readthedocs.org/en/latest/development.html" rel="nofollow">Working on Graphite-web</a></p>

<p>官方列举的使用公司 <br>
<a href="http://graphite.readthedocs.org/en/latest/who-is-using.html" rel="nofollow">Who is using Graphite?</a></p>

<hr>



<h1 id="3-怎么用-graphite-监控-spark">3 怎么用 graphite 监控 spark</h1>



<h2 id="测试拓扑">测试拓扑</h2>

<p>3个节点 主机名：spark1/spark2/spark3 <br>
部署 hadoop/spark 集群（略）</p>

<p>1个节点 主机名：monitor1 <br>
部署 python + esay_install + pip + graphite(carbon+whisper+graphite-web) + httpd + grafana + hammerlab/grafana-spark-dashboards</p>



<h2 id="1-安装">1 安装</h2>

<p>参考链接 <br>
<a href="http://graphite.wikidot.com/installation" rel="nofollow">graphite website installation</a> <br>
<a href="http://graphite.readthedocs.org/en/latest/install.html" rel="nofollow">graphite docs installation</a></p>

<p><a href="http://www.jsxubar.info/graphite-installation.html" rel="nofollow">Graphite 安装和常见问题</a> <br>
该文部分是对 [graphite docs installation] 的翻译</p>

<p>参考 <a href="http://blog.csdn.net/Tsingfu1986/article/details/44239503" rel="nofollow">how-to-install-graphite-on-centos6.6-x86_64</a> <br>
参考 <a href="http://blog.csdn.net/Tsingfu1986/article/details/44240157" rel="nofollow">how-to-install-grafana-on-centos6.6-x86_64</a></p>



<h2 id="2-配置-spark-向-graphite-发-metric-数据">2 配置 spark 向 graphite 发 metric 数据</h2>

<p>vi conf/metrics.properites</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor"># org.apache.spark.metrics.sink.GraphiteSink</span>
<span class="hljs-preprocessor">#   Name:     Default:      Description:</span>
<span class="hljs-preprocessor">#   host      NONE          Hostname of Graphite server</span>
<span class="hljs-preprocessor">#   port      NONE          Port of Graphite server</span>
<span class="hljs-preprocessor">#   period    10            Poll period</span>
<span class="hljs-preprocessor">#   unit      seconds       Units of poll period</span>
<span class="hljs-preprocessor">#   prefix    EMPTY STRING  Prefix to prepend to metric name</span>
<span class="hljs-preprocessor">#   protocol  tcp           Protocol ("tcp" or "udp") to use</span>

*<span class="hljs-preprocessor">.source</span><span class="hljs-preprocessor">.jvm</span><span class="hljs-preprocessor">.class</span>=org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.metrics</span><span class="hljs-preprocessor">.source</span><span class="hljs-preprocessor">.JvmSource</span>
<span class="hljs-preprocessor">#master.source.jvm.class=org.apache.spark.metrics.source.JvmSource</span>
<span class="hljs-preprocessor">#worker.source.jvm.class=org.apache.spark.metrics.source.JvmSource</span>
<span class="hljs-preprocessor">#driver.source.jvm.class=org.apache.spark.metrics.source.JvmSource</span>
<span class="hljs-preprocessor">#executor.source.jvm.class=org.apache.spark.metrics.source.JvmSource</span>

<span class="hljs-preprocessor">#Enable GraphiteSink</span>
*<span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.Graphite</span><span class="hljs-preprocessor">.class</span>=org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.metrics</span><span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.GraphiteSink</span>
*<span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.Graphite</span><span class="hljs-preprocessor">.host</span>=monitor1
*<span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.Graphite</span><span class="hljs-preprocessor">.port</span>=<span class="hljs-number">2003</span></code></pre>

<p>spark standalone需要分发metrics.properties</p>



<pre class="prettyprint"><code class=" hljs ruby">scp metrics.properties <span class="hljs-symbol">spark2:</span>~<span class="hljs-regexp">/app/spark</span><span class="hljs-regexp">/conf/</span>
scp metrics.properties <span class="hljs-symbol">spark3:</span>~<span class="hljs-regexp">/app/spark</span><span class="hljs-regexp">/conf/</span></code></pre>

<hr>



<h2 id="3-graphite-web-查看-spark-metrics-graph">3 graphite-web 查看 spark metrics graph</h2>

<p>web访问： <code>http://monitor1:61080</code> <br>
<strong>更新20150320:</strong> <br>
这里看看 graphite对metric的定制功能： <br>
<img src="https://img-blog.csdn.net/20150320140751585" alt="graphite-web默认以树形结构组织metrics，然后选择要查看的metric，就可以展示" title=""> <br>
graphite-web默认以树形结构组织metrics，然后选择要查看的metric，就可以展示</p>

<p><img src="https://img-blog.csdn.net/20150320140957275" alt="graphite也支持自定义dashboard，选择需要展示的一些metrics，进行组合操作，访问dashboard可以展示定制的任何metrics graph" title=""> <br>
graphite也支持自定义dashboard，选择需要展示的一些metrics，进行组合操作，访问dashboard可以展示定制的任何metrics graph</p>

<hr>



<h2 id="4-grafana-查看-spark-metrics-graph">4 grafana 查看 spark metrics graph</h2>

<p>brower访问：<a href="http://monitor1:61081" rel="nofollow">http://monitor1:61081</a> <br>
<strong>更新：20150320</strong> <br>
首页显示如下： <br>
<img src="https://img-blog.csdn.net/20150320134430209" alt="grafana-web首页" title=""> <br>
grafana-web首页</p>

<p><img src="https://img-blog.csdn.net/20150320134644668" alt="在自定义的dashboard上创建自定义graph，可以选择任意想要展示的metrics" title=""> <br>
在自定义的dashboard上创建自定义graph，可以选择任意想要展示的metrics</p>

<p><img src="https://img-blog.csdn.net/20150320134858407" alt="自己创建的dashboard，展示了 load, mem, jvm_heap相关的指标" title=""> <br>
自己创建的dashboard，展示了 load, mem, jvm_heap相关的指标</p>

<p><strong>grafana常见问题：</strong> <br>
问题1：grafana web 添加 graphic 不能选择 metric，导致不能完成定制 <br>
grafana-web日志没有相关错误</p>

<p>原因： <br>
参考<a href="http://docs.grafana.org/troubleshooting/" rel="nofollow">Troubleshooting</a> <br>
查看首页，发现 grafana的 graph panel 提示 connected <br>
使用 firefox -&gt;tools-&gt;web developers-&gt;toggle tools(option+commond+I) <br>
发现提示 Cross-Origin Request Blocked</p>

<p>处理方法1： <br>
<a href="http://enable-cors.org/server_apache.html" rel="nofollow">CORS on Apache</a></p>

<blockquote>
  <p>To add the CORS authorization to the header using Apache, simply add the following line inside either the , ,  or  sections of your server config (usually located in a *.conf file, such as httpd.conf or apache.conf), or within a .htaccess file:</p>
  
  <p>Header set Access-Control-Allow-Origin “*”</p>
</blockquote>

<p>vi /etc/httpd/conf.d/graphite-vhost.conf <br>
在  部分，添加 </p>

<blockquote>
  <p>Header set Access-Control-Allow-Origin “*”</p>
</blockquote>

<p>apachectl -t  <br>
service httpd reload</p>

<p>结果： grafana web 能正常添加 metric</p>

<hr>



<h2 id="5测试-grafana-spark-dashboards">5测试 grafana-spark-dashboards</h2>

<p><a href="https://github.com/hammerlab/grafana-spark-dashboards" rel="nofollow">github grafana-spark-dashboards</a> <br>
<em>Note</em> <br>
grafana-spark-dashboards目前仅提供监控spark-on-yarn模式下dashboard，对于standalone模式下的监控好像还不支持</p>

<p>1) install <br>
git clone <a href="https://github.com/hammerlab/grafana-spark-dashboards.git" rel="nofollow">https://github.com/hammerlab/grafana-spark-dashboards.git</a></p>

<p>cd grafana-spark-dashboards <br>
cp spark.js /data/grafana/app/dashboards/</p>

<p>cd /data/grafana/app/dashboards/ <br>
cp spark.js spark.js.org <br>
vi spark.js <br>
a 查找 fetchYarnApps 设置 spark.js try to hit </p>

<blockquote>
  <p><a href="http://spark1:8088/ws/v1/cluster/apps" rel="nofollow">http://spark1:8088/ws/v1/cluster/apps</a>, which should be your YARN RM’s <br>
  JSON API (try this with a curl first to be sure). <br>
  jQuery.ajax(‘<a href="http://spark1:8088/ws/v1/cluster/apps" rel="nofollow">http://spark1:8088/ws/v1/cluster/apps</a>‘, {</p>
</blockquote>

<p>2)测试grafana-spark-dashboards 监控 spark-app running on yarn <br>
测试 <code>http://monitor1:61081/#/dashboard/file/spark.js</code> <br>
问题1： <code>http://monitor1:61081/#/dashboard/file/spark.js</code> 访问失败，web提示Error Could not load dashboards/spark.js. Please make sure it exists ,日志提示</p>

<blockquote>
  <p>[Thu Mar 12 09:23:42 2015] [error] [client 192.168.99.1] File does not <br>
  exist: /var/www/html/grafana/app/dashboards/spark, referer: <br>
  <a href="http://monitor1:61081/" rel="nofollow">http://monitor1:61081/</a></p>
</blockquote>

<p>处理方法1：</p>



<pre class="prettyprint"><code class=" hljs lasso">mkdir /<span class="hljs-built_in">var</span>/www/html/grafana/app/dashboards/spark
chown <span class="hljs-attribute">-R</span> apache /<span class="hljs-built_in">var</span>/www/html/grafana/app/dashboards/spark</code></pre>

<p>结果：问题依旧：日志提示</p>

<blockquote>
  <p>[Thu Mar 12 09:27:29 2015] [error] [client 192.168.99.1] File does not <br>
  exist: /var/www/html/grafana/app/dashboards/spark/js, referer: <br>
  <a href="http://monitor1:61081/" rel="nofollow">http://monitor1:61081/</a></p>
</blockquote>

<p>处理方法2：</p>



<pre class="prettyprint"><code class=" hljs lasso">mkdir /<span class="hljs-built_in">var</span>/www/html/grafana/app/dashboards/spark/js
chown <span class="hljs-attribute">-R</span> apache /<span class="hljs-built_in">var</span>/www/html/grafana/app/dashboards/spark/js</code></pre>

<p>结果：web可以进行操作，apache日志没有报错</p>

<p><strong>更新20150320</strong> <br>
使用 grafana-spark-dashboards 监控 spark应用访问 <code>http://monitor1:61081/#/dashboard/file/spark.js</code> 目前需要提供参数</p>

<p><strong>test steps:</strong> <br>
(1)step1: submit a spark-streaming example NetworkWordCount to yarn using spark-submit </p>



<pre class="prettyprint"><code class=" hljs haml">#yarn-client
terminal1
nc -lk 9999

terminal2
export SPARK_HOME=/data01/app/spark/spark-1.2.1-SNAPSHOT-bin-2.3.0-cdh5.1.3
export SPARK_HOME=$(pwd)
SPARK_APP_JAR=$SPARK_HOME/examples/target/spark-examples_2.10-1.3.0-SNAPSHOT.jar

./bin/spark-submit \
-<span class="ruby">-<span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">org</span>.<span class="hljs-title">apache</span>.<span class="hljs-title">spark</span>.<span class="hljs-title">examples</span>.<span class="hljs-title">streaming</span>.<span class="hljs-title">NetworkWordCount</span> \</span>
</span>-<span class="ruby">-master yarn-client \
</span>-<span class="ruby">-driver-memory <span class="hljs-number">300</span>M \
</span>-<span class="ruby">-executor-memory <span class="hljs-number">300</span>M \
</span>-<span class="ruby">-num-executors <span class="hljs-number">2</span> \
</span>-<span class="ruby">-executor-cores <span class="hljs-number">1</span> \
</span>-<span class="ruby">-files conf/metrics.properties \
</span>$SPARK_APP_JAR \
spark1 9999</code></pre>

<p>(2)step2: get YARNAppID from yarn WebUI <br>
示例：YARNAppID=application_1426815734479_0001</p>

<p>(3)step3:  access grafana-spark-dashboard to monitor specific YARNAppID <br>
<code>http://monitor1:62081/#/dashboard/script/spark.js?app=application_1426815734479_0001</code> <br>
问题：访问报错，具体信息见： <br>
spark-developers-list<a href="http://apache-spark-developers-list.1001551.n3.nabble.com/Monitoring-Spark-with-Graphite-and-Grafana-td10804.html#a11116" rel="nofollow">Monitoring Spark with Graphite and Grafana</a> <br>
问题已解决 <br>
<a href="https://github.com/hammerlab/grafana-spark-dashboards/issues/5" rel="nofollow">Need some FQA while using grafana-spark-dashboards</a></p>

<p>这里看看 grafana-spark-dashboards 展示效果 <br>
<img src="https://img-blog.csdn.net/20150327155038382" alt="grafana-spark-dashboards 展示效果" title="grafana-spark-dashboards 展示效果"> <br>
grafana-spark-dashboards 展示效果</p>

<hr>

<p><strong>进展</strong>： <br>
20150313，测试到grafana-web使用grafana-spark-dashboards查看spark指标时，目前遇到一些问题，对 grafana web 上定制 graph 的操作不够熟悉，grafana-spark-dashboards 的使用有些问题</p>

<p>20153020: 能够在 grafana-web定制自定义的dashboard，但测试github社区项目<a href="https://github.com/hammerlab/grafana-spark-dashboards" rel="nofollow">grafana-spark-dashboards</a> 时，遇到CORS(cross-origin request sharing)问题</p>

<p>20150327更新: 20130323解决使用 <a href="https://github.com/hammerlab/grafana-spark-dashboards" rel="nofollow">grafana-spark-dashboards</a> 过程中遇到的问题</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>