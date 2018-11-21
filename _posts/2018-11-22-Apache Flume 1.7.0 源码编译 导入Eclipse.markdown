---
layout:     post
title:      Apache Flume 1.7.0 源码编译 导入Eclipse
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div id="main">
<div id="mainContent">
<div class="forFlow">
<div id="post_detail">
<div id="topics">
<div class="post">
<h1 class="postTitle"><a id="cb_post_title_url" class="postTitle2" href="http://www.cnblogs.com/chenpi/p/7214745.html" rel="nofollow">Apache Flume 1.7.0 源码编译 导入Eclipse</a></h1>
<div class="clear"></div>
<div class="postBody">
<div id="cnblogs_post_body"><a name="_labelTop"></a>
<div id="navCategory">
<h1>阅读目录</h1>
<ul><li><a href="#_label0" rel="nofollow">前言</a></li><li><a href="#_label1" rel="nofollow">下载</a></li><li><a href="#_label2" rel="nofollow">Maven编译安装</a></li><li><a href="#_label3" rel="nofollow">导入Eclipse</a></li></ul></div>
<div style="text-align:right;"><a href="#_labelTop" rel="nofollow"></a><a name="_label0"></a></div>
<h1>前言</h1>
<p>最近看了看Apache Flume，在虚拟机里跑了一下flume + kafka + storm + mysql架构的demo，功能很简单，主要是用flume收集数据源（http上报信息），放入到kafka队列里，然后用storm消费kafka里的资源，计算结果并存入到mysql中；</p>
<p>在这期间遇到了很多问题，也学到了一些知识，打算做个笔记吧，帮助自己也帮助别人；</p>
<p>先从Flume源码的编译开始；</p>
<div style="text-align:right;"><a href="#_labelTop" rel="nofollow"></a><a name="_label1"></a></div>
<h1>下载</h1>
<p>下载源码很简单，去官网或者去github下载，Apache Flume 1.7.0的github源码地址如下：</p>
<p><a href="https://github.com/apache/flume/tree/release-1.7.0" rel="nofollow">https://github.com/apache/flume/tree/release-1.7.0</a></p>
<div style="text-align:right;"><a href="#_labelTop" rel="nofollow"></a><a name="_label2"></a></div>
<h1>Maven编译安装</h1>
<p>在mvn install之前，最好先设置下maven的国内镜像地址，加快依赖的下载速度，时间还是很宝贵的，别浪费在无聊的等待上，</p>
<p>打开maven的setting.xml配置文件，添加如下镜像即可：</p>
<div class="cnblogs_code">
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="" rel="nofollow" title="复制代码"><img src="" alt="复制代码"></a></span></div>
<pre>  <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">mirrors</span><span style="color:#0000ff;">&gt;</span>
    <span style="color:#008000;">&lt;!--</span><span style="color:#008000;"> mirror
     | Specifies a repository mirror site to use instead of a given repository. The repository that
     | this mirror serves has an ID that matches the mirrorOf element of this mirror. IDs are used
     | for inheritance and direct lookup purposes, and must be unique across the set of mirrors.
     |
    &lt;mirror&gt;
      &lt;id&gt;mirrorId&lt;/id&gt;
      &lt;mirrorOf&gt;repositoryId&lt;/mirrorOf&gt;
      &lt;name&gt;Human Readable Name for this Mirror.&lt;/name&gt;
      &lt;url&gt;http://my.repository.com/repo/path&lt;/url&gt;
    &lt;/mirror&gt;
     </span><span style="color:#008000;">--&gt;</span>
     
    <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">mirror</span><span style="color:#0000ff;">&gt;</span>
      <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">id</span><span style="color:#0000ff;">&gt;</span>alimaven<span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">id</span><span style="color:#0000ff;">&gt;</span>
      <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">name</span><span style="color:#0000ff;">&gt;</span>aliyun maven<span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">name</span><span style="color:#0000ff;">&gt;</span>
      <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">url</span><span style="color:#0000ff;">&gt;</span>http://maven.aliyun.com/nexus/content/groups/public/<span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">url</span><span style="color:#0000ff;">&gt;</span>
      <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">mirrorOf</span><span style="color:#0000ff;">&gt;</span>central<span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">mirrorOf</span><span style="color:#0000ff;">&gt;</span>        
    <span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">mirror</span><span style="color:#0000ff;">&gt;</span>
      
    <span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">mirrors</span><span style="color:#0000ff;">&gt;</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="" rel="nofollow" title="复制代码"><img src="" alt="复制代码"></a></span></div>
</div>
<p>在控制台输入如下命令，后面的参数表示跳过单元测试</p>
<p><code>mvn install -Dmaven.test.skip=true</code></p>
<p>很快就开始下载依赖了，骚等片刻：</p>
<p><img src="http://images2015.cnblogs.com/blog/879896/201707/879896-20170720204636740-1602487657.jpg" alt=""></p>
<p>遗憾的是报错了，坑爹，又是国外的网络不能访问，ping下<span>maven.twttr.com</span>，果真不行，哎，，，</p>
<h6> [ERROR] Failed to execute goal on project flume-ng-morphline-solr-sink: Could not resolve dependencies for project org.apache.flume.flume-ng-sinks:flume-ng-morphline-solr-sink:jar:1.7.0: Failed to collect dependencies at org.kitesdk:kite-morphlines-all:pom:1.0.0
 -&gt; org.kitesdk:kite-morphlines-useragent:jar:1.0.0 -&gt; ua_parser:ua-parser:jar:1.3.0: Failed to read artifact descriptor for ua_parser:ua-parser:jar:1.3.0: Could not transfer artifact ua_parser:ua-parser:pom:1.3.0 from/to maven-twttr<span style="background-color:#ff0000;">(http://maven.twttr.com):
 maven.twttr.com: Unknown host maven.twttr.com</span> -&gt; [Help 1]</h6>
<p> 网上找了半天解决方案，搞什么代理啊 VPN什么的，有点麻烦，好在找到了一个ip，添加到hosts里即可，如下：</p>
<div class="cnblogs_code">
<pre>199.16.156.89 maven.twttr.com</pre>
</div>
<p>添加完host后，继续执行<code>mvn install -Dmaven.test.skip=true</code>，耐心等待...</p>
<p>结果等了半天，还是不行，卡在这，又是坑爹的天朝网络，速度真的太慢了，没办法。。。。。conjars.org的访问速度真心太慢...</p>
<p>Downloading: http://conjars.org/repo/eigenbase/eigenbase-properties/1.1.4/eigenbase-properties-1.1.4.pom</p>
<p> 多试几次吧，反正我是试了好几次，最后终于成功了，也可以尝试在父pom.xml加个<span style="color:#800000;">repository</span>，如下，实在不行，真的只能代理了，或者把别人已经下好的依赖拷贝到自己的maven本地仓库。</p>
<div class="cnblogs_code">
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="" rel="nofollow" title="复制代码"><img src="" alt="复制代码"></a></span></div>
<pre>  <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">repositories</span><span style="color:#0000ff;">&gt;</span>
    <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">repository</span><span style="color:#0000ff;">&gt;</span>
        <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">id</span><span style="color:#0000ff;">&gt;</span>nexus.axiomalaska.com<span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">id</span><span style="color:#0000ff;">&gt;</span>
        <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">url</span><span style="color:#0000ff;">&gt;</span>http://nexus.axiomalaska.com/nexus/content/repositories/public<span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">url</span><span style="color:#0000ff;">&gt;</span>
    <span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">repository</span><span style="color:#0000ff;">&gt;</span>
  <span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">repositories</span><span style="color:#0000ff;">&gt;</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="" rel="nofollow" title="复制代码"><img src="" alt="复制代码"></a></span></div>
</div>
<p><img src="http://images2015.cnblogs.com/blog/879896/201707/879896-20170721095551677-2084601699.png" alt=""></p>
<div style="text-align:right;"><a href="#_labelTop" rel="nofollow"></a><a name="_label3"></a></div>
<h1>导入Eclipse</h1>
<p>这个没啥好说的，直接导入maven工程即可，遗憾的是flume-ng-core工程还是报错，如下：</p>
<p><span style="background-color:#ff0000;">TransferStateFileMeta cannot be resolved to a type</span></p>
<p>仔细看看源码，发现确实没有定义<span>TransferStateFileMeta </span>这个类，这就尴尬了，在检查下，发现pom.xml有错误，需要安装，execution元素那边报错了，鼠标放上去，提示需要安装相应插件，那就安装吧，骚等片刻，终于安装好了，update下maven工程，pom.xml也没报错了。。。</p>
<p>坑爹的是发现还是报那个错误</p>
<p><span style="background-color:#ff0000;">TransferStateFileMeta cannot be resolved to a type</span></p>
<p><span style="background-color:#ffffff;">不过发现问题还是出在pom.xml里的build-helper-maven-plugin这个插件的配置上，好像原因是DurablePositionTracker引用的TransferStateFileMeta这个类是自动生成的，查看target目录，确实找到了这个类，但是为什么还是报错，仔细观察，原来是source没配对，因为<span>TransferStateFileMeta</span>类是在generated-sources的avro目录下的，那就增加个目录呗，在sources节点增加<span style="color:#0000ff;">&lt;<span style="color:#800000;">source<span style="color:#0000ff;">&gt;target/generated-sources/<span style="color:#0000ff;">avro&lt;/<span style="color:#800000;">source<span style="color:#0000ff;">&gt;，如下所示。。</span></span></span></span></span></span></span></p>
<div class="cnblogs_code">
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="" rel="nofollow" title="复制代码"><img src="" alt="复制代码"></a></span></div>
<pre>            <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">executions</span><span style="color:#0000ff;">&gt;</span>
              <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">execution</span><span style="color:#0000ff;">&gt;</span>
                <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">id</span><span style="color:#0000ff;">&gt;</span>add-source<span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">id</span><span style="color:#0000ff;">&gt;</span>
                <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">phase</span><span style="color:#0000ff;">&gt;</span>generate-sources<span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">phase</span><span style="color:#0000ff;">&gt;</span>
                <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">goals</span><span style="color:#0000ff;">&gt;</span>
                  <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">goal</span><span style="color:#0000ff;">&gt;</span>add-source<span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">goal</span><span style="color:#0000ff;">&gt;</span>
                <span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">goals</span><span style="color:#0000ff;">&gt;</span>
                <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">configuration</span><span style="color:#0000ff;">&gt;</span>
                  <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">sources</span><span style="color:#0000ff;">&gt;</span>
                    <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">source</span><span style="color:#0000ff;">&gt;</span>target/generated-sources/java<span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">source</span><span style="color:#0000ff;">&gt;
　　　　　　　　　　　 <span style="background-color:#ffff00;"><span style="color:#0000ff;">&lt;<span style="color:#800000;">source<span style="color:#0000ff;">&gt;target/generated-sources/<span style="color:#0000ff;">avro&lt;/<span style="color:#800000;">source<span style="color:#0000ff;">&gt;</span></span></span></span></span></span></span></span>
                  <span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">sources</span><span style="color:#0000ff;">&gt;</span>
                <span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">configuration</span><span style="color:#0000ff;">&gt;</span>
              <span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">execution</span><span style="color:#0000ff;">&gt;</span>
            <span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">executions</span><span style="color:#0000ff;">&gt;</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="" rel="nofollow" title="复制代码"><img src="" alt="复制代码"></a></span></div>
</div>
<p>update下工程，终于Ok了，没报任何错误。。。(如果还有错的话，试着先执行mvn eclipse:eclipse命令后再导入)</p>
<p><img src="http://images2015.cnblogs.com/blog/879896/201707/879896-20170721100754083-1810601476.png" alt=""></p>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
<div id="sideBar">
<div id="sideBarMain">
<div class="newsItem">
<div class="clear"></div>
</div>
</div>
</div>
</div>
            </div>
                </div>