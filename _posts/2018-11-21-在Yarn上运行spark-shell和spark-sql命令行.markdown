---
layout:     post
title:      在Yarn上运行spark-shell和spark-sql命令行
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<article class="article-content">
            <p>关键字：spark-shell on yarn、spark-sql on yarn</p>
<p>前面的文章《<a title="" href="http://lxw1234.com/archives/2015/07/416.htm" rel="nofollow" target="_blank">Spark On Yarn：提交Spark应用程序到Yarn</a>》介绍了将Spark应用程序提交到Yarn上运行。有时候在做开发测试的时候，需要使用spark-shell和spark-sql命令行，除了Local和Spark standalone模式，spark-shell和spark-sql也可以运行在yarn上，这里就简单介绍一下使用方法。</p>
<h2 id="spark-shell-on-yarn">spark-shell On Yarn</h2>
<p>如果你已经有一个正常运行的Hadoop Yarn环境，那么只需要下载相应版本的Spark，解压之后做为Spark客户端即可。</p>
<p>需要配置Yarn的配置文件目录，export HADOOP_CONF_DIR=/etc/hadoop/conf   这个可以配置在spark-env.sh中。</p>
<p>运行命令：</p>
<pre class="prettyprint linenums"><ol class="linenums"><li class="L0"><span class="pln">cd $SPARK_HOME</span><span class="pun">/</span><span class="pln">bin</span></li><li class="L1"><span class="pun">./</span><span class="pln">spark</span><span class="pun">-</span><span class="pln">shell \</span></li><li class="L2"><span class="pun">--</span><span class="pln">master </span><strong><span class="pln">yarn</span><span class="pun">-</span><span class="pln">client</span></strong><span class="pln"> \</span></li><li class="L3"><span class="pun">--</span><span class="pln">executor</span><span class="pun">-</span><span class="pln">memory </span><span class="lit">1G</span><span class="pln"> \</span></li><li class="L4"><span class="pun">--</span><span class="pln">num</span><span class="pun">-</span><span class="pln">executors </span><span class="lit">10</span></li></ol></pre>
<p>注意，这里的–master必须使用yarn-client模式，如果指定yarn-cluster，则会报错：</p>
<p>Error: Cluster deploy mode is not applicable to Spark shells.</p>
<p>因为spark-shell作为一个与用户交互的命令行，必须将Driver运行在本地，而不是yarn上。</p>
<p>其中的参数与提交Spark应用程序到yarn上用法一样。</p>
<p>启动之后，在命令行看上去和standalone模式下的无异：</p>
<p><img class="aligncenter" src="http://7xipth.com1.z0.glb.clouddn.com/0811-1.jpg" alt="spark-shell on yarn" width="576" height="309"></p>
<p>在ResourceManager的WEB页面上，看到了该应用程序（spark-shell是被当做一个长服务的应用程序运行在yarn上）：</p>
<p><img class="aligncenter" src="http://7xipth.com1.z0.glb.clouddn.com/0811-2.jpg" alt="spark-shell on yarn" width="650" height="150"></p>
<p>点击ApplicationMaster的UI，进入到了Spark应用程序监控的WEB页面：</p>
<p><img class="aligncenter" src="http://7xipth.com1.z0.glb.clouddn.com/0811-3.jpg" alt="spark-shell on yarn" width="620" height="272"></p>
<p> </p>
<h2 id="spark-sql-on-yarn">spark-sql On Yarn</h2>
<p>spark-sql命令行运行在yarn上，原理和spark-shell on yarn一样。只不过需要将Hive使用的相关包都加到Spark环境变量。</p>
<p>1. 将hive-site.xml拷贝到$SPARK_HOME/conf</p>
<p>2.export HIVE_HOME=/usr/local/apache-hive-0.13.1-bin 添加到spark-env.sh</p>
<p>3.将以下jar包添加到Spark环境变量：</p>
<p>datanucleus-api-jdo-3.2.6.jar、datanucleus-core-3.2.10.jar、datanucleus-rdbms-3.2.9.jar、mysql-connector-java-5.1.15-bin.jar</p>
<p>可以在spark-env.sh中直接添加到SPARK_CLASSPATH变量中。</p>
<p> </p>
<p>运行命令：</p>
<pre class="prettyprint linenums"><ol class="linenums"><li class="L0"><span class="pln">cd $SPARK_HOME</span><span class="pun">/</span><span class="pln">bin</span></li><li class="L1"><span class="pun">./</span><span class="pln">spark</span><span class="pun">-</span><span class="pln">sql \</span></li><li class="L2"><span class="pun">--</span><span class="pln">master yarn</span><span class="pun">-</span><span class="pln">client \</span></li><li class="L3"><span class="pun">--</span><span class="pln">executor</span><span class="pun">-</span><span class="pln">memory </span><span class="lit">1G</span><span class="pln"> \</span></li><li class="L4"><span class="pun">--</span><span class="pln">num</span><span class="pun">-</span><span class="pln">executors </span><span class="lit">10</span></li></ol></pre>
<p>即可在yarn上运行spark-sql命令行。<br>
<img class="aligncenter" src="http://7xipth.com1.z0.glb.clouddn.com/0811-5.jpg" alt="spark-sql on yarn" width="753" height="196"></p>
<p>在ResourceManager上的显示以及点击ApplicationMaster进去Spark的WEB UI，与spark-shell无异。</p>
<p><img class="aligncenter" src="http://7xipth.com1.z0.glb.clouddn.com/0811-4.jpg" alt="spark-sql on yarn" width="555" height="322"></p>
<p> </p>
<p>这样，只要之前有使用Hadoop Yarn，那么就不需要搭建standalone的Spark集群，也能发挥Spark的强大威力了。</p>
<p> </p>
<p>其他相关阅读：</p>
<h2 id="spark-on-yarn提交spark应用程序到yarn"><a title="" href="http://lxw1234.com/archives/2015/07/416.htm" rel="nofollow" target="_blank">Spark On Yarn：提交Spark应用程序到Yarn </a></h2>
<h2 id="spark131安装配置运行"><a title="" href="http://lxw1234.com/archives/2015/06/281.htm" rel="nofollow" target="_blank">Spark1.3.1安装配置运行 </a></h2>
<h2 id="spark140-sparksql与hive整合-支持窗口分析函数"><a title="" href="http://lxw1234.com/archives/2015/06/294.htm" rel="nofollow" target="_blank">Spark1.4.0-SparkSQL与Hive整合-支持窗口分析函数 </a></h2>
<h2 id="spark算子系列文章"><a title="" href="http://lxw1234.com/archives/2015/07/363.htm" rel="nofollow" target="_blank">Spark算子系列文章 </a></h2>
<p> </p>
<p>如果觉得本博客对您有帮助，请 <a href="http://lxw1234.com/pay-blog" rel="nofollow" target="_blank" title="">赞助作者</a> 。</p><p>转载请注明：<a href="http://lxw1234.com" rel="nofollow" title="">lxw的大数据田地</a> » <a href="http://lxw1234.com/archives/2015/08/448.htm" rel="nofollow" title="">在Yarn上运行spark-shell和spark-sql命令行</a></p>


<div class="article-social">
            <a id="Addlike" class="action" title=""><i class="fa fa-heart-o"></i>喜欢 (<span class="count">4</span>)</a><span class="or"><a class="none" title="">赏</a></span><span class="action action-share bdsharebuttonbox bdshare-button-style0-24"><i class="fa fa-share-alt"></i>分享 (<span class="bds_count" title="累计分享4次">4</span>)</span><div class="action-popover"><div class="popover top in"><div class="arrow"></div><div class="popover-content"><a href="#" rel="nofollow" class="sinaweibo fa fa-weibo" title=""></a><a href="#" rel="nofollow" class="bds_qzone fa fa-star" title=""></a><a href="#" rel="nofollow" class="tencentweibo fa fa-tencent-weibo" title=""></a><a href="#" rel="nofollow" class="qq fa fa-qq" title=""></a><a href="#" rel="nofollow" class="bds_renren fa fa-renren" title=""></a><a href="#" rel="nofollow" class="bds_weixin fa fa-weixin" title=""></a><a href="#" rel="nofollow" class="bds_more fa fa-ellipsis-h" title=""></a></div></div></div> 
</div>

<pre><code>&lt;/article&gt;
</code></pre></article>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>