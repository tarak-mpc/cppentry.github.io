---
layout:     post
title:      hive on spark
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>装了一个多星期的hive on spark 遇到了许多坑。还是写一篇随笔，免得以后自己忘记了。同事也给我一样苦逼的人参考。</p>
<p>先说明一下，这里说的<span class="wp_keywordlink_affiliate"><a title="" href="http://www.iteblog.com/archives/tag/hive" rel="nofollow">Hive</a> on
<span class="wp_keywordlink_affiliate"><a title="" href="http://www.iteblog.com/archives/tag/spark" rel="nofollow">Spark</a>是<span class="wp_keywordlink_affiliate"><a title="" href="http://www.iteblog.com/archives/tag/hive" rel="nofollow">Hive</a>跑在<span class="wp_keywordlink_affiliate"><a title="" href="http://www.iteblog.com/archives/tag/spark" rel="nofollow">Spark</a>上，用的是<span class="wp_keywordlink_affiliate"><a title="" href="http://www.iteblog.com/archives/tag/spark" rel="nofollow">Spark</a>执行引擎，而不是MapReduce，和<span class="wp_keywordlink_affiliate"><a title="" href="http://www.iteblog.com/archives/tag/hive" rel="nofollow">Hive</a>
 on Tez的道理一样。</span></span></span></span></span></span></p>
<p><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate">先看官网的资源<a href="https://cwiki.apache.org/confluence/display/Hive/Hive+on+Spark%3A+Getting+Started" rel="nofollow">Hive
 on Spark: Getting Started</a><span> <br></span></span></span></span></span></span></span></p>
<p><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate">要想在Hive中使用Spark执行引擎，第一步当前就是环境设置，我们需要在Hive启动的时候加载<span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate">spark-assembly-1.5.0-hadoop2.6.0.jar</span></span></span></span></span></span>，最简单的方法是把spark-assembly-1.5.0-hadoop2.6.0.jar包直接拷贝
 到$HIVE_HOME/lib目录下。我采用的方法是在hive-site里面添加spark.home。具体给出官方的建议，这个不算坑，基本都能做好。</span></span></span></span></span></span></p>
<p><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><img src="" alt=""></span></span></span></span></span></span></p>
<p>好了到这里开始讲我遇到的坑</p>
<p>第一个坑：直接使用spark pre-build版本，下来后按照上面配置弄结果报错</p>
<p><code class="plain">java.lang.NoSuchFieldError: SPARK_RPC_CLIENT_CONNECT_TIMEOUT</code></p>
<p><code class="spaces">    </code><code class="plain">at org.apache.hive.spark.client.rpc.RpcConfiguration.&lt;clinit&gt;(RpcConfiguration.java:46)</code></p>
<p>报了rpc错误，这个坑要好查点，发现是因为需要自己编译spark因为预编译的带了hive的支持也就是-Phive，这个参数是spark-sql对hive的支持用的，结果用在hive on spark就不行。没事，我们开始编译吧。接下来苦逼日子来了。</p>
<p>第<code><strong>二</strong></code>个坑：版本不对，刚开始以为hive 能使用 spark的任何版本，结果发现错了，hive对spark版本有着严格要求，具体对应版本你可以下载hive源码里面，搜索他pom.xml文件里面的spark版本，如果版本不对，启动hive后会报错。具体错误如下：</p>
<p><span class="line">Failed to execute spark task, with exception 'org.apache.hadoop.hive.ql.metadata.HiveException(Failed to create spark client.)'
<span class="line">FAILED: Execution Error, return code 1 from org.apache.hadoop.hive.ql.exec.spark.SparkTask</span></span></p>
<p><span class="line"><span class="line">第三个坑：spark的编译，在这里我使用的是hive稳定版本2.01,查看他的pom.xml需要的spark版本是1.5.0.接着讲诉我遇到坑爹的事情，直接让我快一个星期时间白费。在编译的时候使用了hive 官网提供的命令，悲催的事情发生了，使用的命令是：</span></span></p>
<p><span class="line"><span class="line"><code class="bash plain">.</code><code class="bash plain">/make-distribution</code><code class="bash plain">.sh --name<span> </span></code><code class="bash string">"hadoop2-without-hive"</code><span> <code class="bash plain">--tgz<span> </span></code><code class="bash string">"-Pyarn,hadoop-provided,hadoop-2.6"</code></span></span></span></p>
<p><span class="line"><span class="line"><span>然后将 lib/<span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate">spark-assembly-1.5.0-hadoop2.6.0.jar</span></span></span></span></span></span></span></span></span></span></span></span>
 这个包拷到你现在的spark/lib 下另外一个相同的注意备份</span></span></span></p>
<p><span class="line"><span class="line"><span>但是当启动spark集群的时候报错了，错误如下</span></span></span></p>
<p><span class="line"><span class="line">Exception in thread "main" java.lang.NoClassDefFoundError: org/slf4j/impl/StaticLoggerBinder</span></span></p>
<p><span class="line"><span class="line">这个时候坑爹了，网上找来找去都没找到这个问题解决方案，上面错误原因就是说slf4j这个包找不到，你妹，完全按照官网来的，官网是怎么测试的呀，悲催了。接下来就是苦恼我一周的安装过程。</span></span></p>
<p><span class="line"><span class="line">在网上找呀找，稍微看到一个靠谱的说法是编译的时候没有讲hadoop的jar包导进来，但是给出解决办法是在spark-env.sh里面添加</span></span></p>
<p><span class="line"><span class="line">export SPARK_DIST_CLASSPATH=$(hadoop classpath)</span></span></p>
<p><span class="line"><span class="line">好了添加了这个，spark master可以起来了，但是slaves仍然是上面错误，又开始找资料了。每天都在痛苦的阅读英文文档，差点抑郁了。而且国内google这不给力，用vpn时断时续，差点砸电脑。最后快绝望的时候回到spark官网，仔细阅读他提供的编译命令，死马当活马医吧。按照官网提供的编译命令再次编译spark.官网地址：http://spark.apache.org/docs/1.5.0/building-spark.html。他默认1.5.0使用是scala2.10进行编译，但是我安装的scala是2.11所以使用了以下命令。<br></span></span></p>
<pre><code>./dev/change-scala-version.sh 2.11
mvn -Pyarn -Phadoop-2.4 -Dscala-2.11 -DskipTests clean package

<span style="font-size:14px;">这次编译的s<span class="line"><span class="line"><span><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate">park-assembly-1.5.0-hadoop2.6.0.jar 包有140mb左右比上次的多了40mb，感觉有点靠谱了，将这个包移过去，ok了！太开心了，一切都跑起来了。泪奔了，hive官网害人呀。

第四个坑，这个坑也算一个不大不小的坑，因为我偶然想装一下hue，在装hue遇到的错误，现在记录一下。
首先坑是hadoop的坑，如果在hadoop core-site.xml下面没有配置
&lt;property&gt;
    &lt;name&gt;hadoop.proxyuser.hdfs.hosts&lt;/name&gt;
    &lt;value&gt;*&lt;/value&gt;
    &lt;description&gt;Defines the Unix user, hdfs, that will run the HttpFS server as
      a proxyuser.&lt;/description&gt;
  &lt;/property&gt;
  &lt;property&gt;
    &lt;name&gt;hadoop.proxyuser.hdfs.groups&lt;/name&gt;
    &lt;value&gt;*&lt;/value&gt;
    &lt;description&gt;Replaces hdfs with the Unix user that will start the HttpFS server.
      &lt;/description&gt;
  &lt;/property&gt;
httpfs-site.xml
  &lt;property&gt;
    &lt;name&gt;hadoop.proxyuser.hue.hosts&lt;/name&gt;
    &lt;value&gt;*&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
    &lt;name&gt;hadoop.proxyuser.hue.groups&lt;/name&gt;
    &lt;value&gt;*&lt;/value&gt;
&lt;/property&gt;
<samp class="ph codeph">hdfs-site.xml</samp><samp class="ph codeph"></samp><samp class="ph codeph"></samp></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></code></pre>
<pre class="literallayout">&lt;property&gt;
  &lt;name&gt;dfs.webhdfs.enabled&lt;/name&gt;
  &lt;value&gt;true&lt;/value&gt;
&lt;/property&gt;</pre>
<pre><code><span style="font-size:14px;"><span class="line"><span class="line"><span><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><samp class="ph codeph"></samp>这三个配置没配好的你讲没有权限访问hadoop,但是这个都是小事情，网上教程多，不怎么算坑。
接下来hive配置才是一个不大不小的坑，因为网上基本没有什么资料，对应版本是hive是2.0.1
网上基本的版本都教了怎么连接，但是用网上连接方法hive.log老是显示 old version ? 我一直以为我hive版本过高了，但是换成1.2.1这个版本一样错误。
于是又开始看google，看的又快奔溃了。开始网上有人叫把hive.server2.authentication这个属性改为NOSASL,但是改来改去没啥卵用，好了我就不卖关子了，
hue要连接hive其实还需要修改两个属性
第一个属性是hive.server2.authentication 这个要是NOSASl
第二个属性是hive.server2.enable.doAs 一定要是flase 不然就会报 old version?
看到可视化界面的时候还是有点成就感的。

第五个坑，装好了那么多东西，肯定想跑跑试试，于是觉得对nginx 日志进行分析，这时候对网上hive 分析 nginx进行了搜索，搜了一大堆，但是按他们的正则建表都不可以用，又开始泪奔了。
不停百度，资料结果全是千篇一律，好像大家抄的都是一篇。结果还是找呀找，终于找到原因了
原来正则必须不是一根斜杠，原来java必须是两根\\把我建表sql展示一下，不然还是太空了。其中不需要out那一行，直接用input就行
create table logs(ipaddress STRING,identity STRING,`user` STRING,time STRING,request STRING,status STRING,size STRING,host STRING,referer STRING,rbody STRING,agent STRING,forwarded STRING,uaddr STRING,ustatus STRING,urtime STRING,rtime STRING)ROW FORMAT SERDE 'org.apache.hadoop.hive.contrib.serde2.RegexSerDe' WITH SERDEPROPERTIES ('input.regex' = 
'([0-9]{1,3}\\.[0-9]{1,3}\\.[0-9]{1,3}\\.[0-9]{1,3}) (-) (-) (\\[.*\\]) (\\"[^\\"]*\\") (\\d{3}) (\\d{1,}) (\\"[^\\"]*\\") (\\"[^\\"]*\") (-|[^\\s]*) (\\"[^\\"]*\\") (\\"[^\\"]*\\") ([0-9]{1,3}\\.[0-9]{1,3}\\.[0-9]{1,3}\\.[0-9]{1,3}:[0-9]{1,5}) (\\d{3}) ([\\d]{1,}\\.[\\d]{1,}) ([\\d]{1,}\\.[\\d]{1,})')
</span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span>接下来可以跑了，扔了一个sql，你妹又报错了
<span class="line"><span class="line">return code 3 from org.apache.hadoop.hive.ql.exec.spark.SparkTask</span></span>

这个坑不算大，因为通过查找hive,spark日志发现原来spark找不到<code><span style="font-size:14px;"><span class="line"><span class="line"><span><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate"><span class="wp_keywordlink_affiliate">org.apache.hadoop.hive.contrib.serde2.RegexSerDe</span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></code>
这个网上解决方法就多了，就是加上hive.aux.jars.path 这个属性里面你这个jar包地址如果是本地的记住前面要加file://哦不然找不到


好了这是安装hive on spark 遇到的一些坑，希望我的资料能对需要的人有些帮助吧。个人总结：遇到不会的别看网上的文章了，全是千篇一律，还是看官网的资料跟官网论坛，比看那些安装篇强上太多。</code></pre>
            </div>
                </div>