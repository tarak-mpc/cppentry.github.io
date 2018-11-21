---
layout:     post
title:      hive on spark 遇到的坑
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p></p>
<p style="color:rgb(20,25,30);font-family:'PingFang SC', '微软雅黑', 'Microsoft YaHei', Helvetica, 'Helvetica Neue', Tahoma, Arial, sans-serif;font-size:14px;">
装了一个多星期的hive on spark 遇到了许多坑。还是写一篇随笔，免得以后自己忘记了。同事也给我一样苦逼的人参考。</p>
<p style="color:rgb(20,25,30);font-family:'PingFang SC', '微软雅黑', 'Microsoft YaHei', Helvetica, 'Helvetica Neue', Tahoma, Arial, sans-serif;font-size:14px;">
先说明一下，这里说的Hive on Spark是Hive跑在Spark上，用的是Spark执行引擎，而不是MapReduce，和Hive on Tez的道理一样。</p>
<p style="color:rgb(20,25,30);font-family:'PingFang SC', '微软雅黑', 'Microsoft YaHei', Helvetica, 'Helvetica Neue', Tahoma, Arial, sans-serif;font-size:14px;">
先看官网的资源Hive on Spark: Getting Started</p>
<p style="color:rgb(20,25,30);font-family:'PingFang SC', '微软雅黑', 'Microsoft YaHei', Helvetica, 'Helvetica Neue', Tahoma, Arial, sans-serif;font-size:14px;">
要想在Hive中使用Spark执行引擎，第一步当前就是环境设置，我们需要在Hive启动的时候加载spark-assembly-1.5.0-hadoop2.6.0.jar，最简单的方法是把spark-assembly-1.5.0-hadoop2.6.0.jar包直接拷贝 到$HIVE_HOME/lib目录下。我采用的方法是在hive-site里面添加spark.home。具体给出官方的建议，这个不算坑，基本都能做好。</p>
<p style="color:rgb(20,25,30);font-family:'PingFang SC', '微软雅黑', 'Microsoft YaHei', Helvetica, 'Helvetica Neue', Tahoma, Arial, sans-serif;font-size:14px;">
好了到这里开始讲我遇到的坑</p>
<p style="color:rgb(20,25,30);font-family:'PingFang SC', '微软雅黑', 'Microsoft YaHei', Helvetica, 'Helvetica Neue', Tahoma, Arial, sans-serif;font-size:14px;">
第一个坑：直接使用spark pre-build版本，下来后按照上面配置弄结果报错</p>
<p style="color:rgb(20,25,30);font-family:'PingFang SC', '微软雅黑', 'Microsoft YaHei', Helvetica, 'Helvetica Neue', Tahoma, Arial, sans-serif;font-size:14px;">
java.lang.NoSuchFieldError: SPARK_RPC_CLIENT_CONNECT_TIMEOUT</p>
<pre style="color:rgb(20,25,30);font-size:14px;background-color:rgb(255,255,255);"><code>at org.apache.hive.spark.client.rpc.RpcConfiguration.&lt;clinit&gt;(RpcConfiguration.java:46)</code></pre>
<p style="color:rgb(20,25,30);font-family:'PingFang SC', '微软雅黑', 'Microsoft YaHei', Helvetica, 'Helvetica Neue', Tahoma, Arial, sans-serif;font-size:14px;">
报了rpc错误，这个坑要好查点，发现是因为需要自己编译spark因为预编译的带了hive的支持也就是-Phive，这个参数是spark-sql对hive的支持用的，结果用在hive on spark就不行。没事，我们开始编译吧。接下来苦逼日子来了。</p>
<p style="color:rgb(20,25,30);font-family:'PingFang SC', '微软雅黑', 'Microsoft YaHei', Helvetica, 'Helvetica Neue', Tahoma, Arial, sans-serif;font-size:14px;">
第二个坑：版本不对，刚开始以为hive 能使用 spark的任何版本，结果发现错了，hive对spark版本有着严格要求，具体对应版本你可以下载hive源码里面，搜索他pom.xml文件里面的spark版本，如果版本不对，启动hive后会报错。具体错误如下：</p>
<p style="color:rgb(20,25,30);font-family:'PingFang SC', '微软雅黑', 'Microsoft YaHei', Helvetica, 'Helvetica Neue', Tahoma, Arial, sans-serif;font-size:14px;">
Failed to execute spark task, with exception 'org.apache.hadoop.hive.ql.metadata.HiveException(Failed to create spark client.)' FAILED: Execution Error, return code 1 from org.apache.hadoop.hive.ql.exec.spark.SparkTask</p>
<p style="color:rgb(20,25,30);font-family:'PingFang SC', '微软雅黑', 'Microsoft YaHei', Helvetica, 'Helvetica Neue', Tahoma, Arial, sans-serif;font-size:14px;">
第三个坑：spark的编译，在这里我使用的是hive稳定版本2.01,查看他的pom.xml需要的spark版本是1.5.0.接着讲诉我遇到坑爹的事情，直接让我快一个星期时间白费。在编译的时候使用了hive 官网提供的命令，悲催的事情发生了，使用的命令是：</p>
<p style="color:rgb(20,25,30);font-family:'PingFang SC', '微软雅黑', 'Microsoft YaHei', Helvetica, 'Helvetica Neue', Tahoma, Arial, sans-serif;font-size:14px;">
./make-distribution.sh --name "hadoop2-without-hive" --tgz "-Pyarn,hadoop-provided,hadoop-2.6"</p>
<p style="color:rgb(20,25,30);font-family:'PingFang SC', '微软雅黑', 'Microsoft YaHei', Helvetica, 'Helvetica Neue', Tahoma, Arial, sans-serif;font-size:14px;">
然后将 lib/spark-assembly-1.5.0-hadoop2.6.0.jar 这个包拷到你现在的spark/lib 下另外一个相同的注意备份</p>
<p style="color:rgb(20,25,30);font-family:'PingFang SC', '微软雅黑', 'Microsoft YaHei', Helvetica, 'Helvetica Neue', Tahoma, Arial, sans-serif;font-size:14px;">
但是当启动spark集群的时候报错了，错误如下</p>
<p style="color:rgb(20,25,30);font-family:'PingFang SC', '微软雅黑', 'Microsoft YaHei', Helvetica, 'Helvetica Neue', Tahoma, Arial, sans-serif;font-size:14px;">
Exception in thread "main" java.lang.NoClassDefFoundError: org/slf4j/impl/StaticLoggerBinder</p>
<p style="color:rgb(20,25,30);font-family:'PingFang SC', '微软雅黑', 'Microsoft YaHei', Helvetica, 'Helvetica Neue', Tahoma, Arial, sans-serif;font-size:14px;">
这个时候坑爹了，网上找来找去都没找到这个问题解决方案，上面错误原因就是说slf4j这个包找不到，你妹，完全按照官网来的，官网是怎么测试的呀，悲催了。接下来就是苦恼我一周的安装过程。</p>
<p style="color:rgb(20,25,30);font-family:'PingFang SC', '微软雅黑', 'Microsoft YaHei', Helvetica, 'Helvetica Neue', Tahoma, Arial, sans-serif;font-size:14px;">
在网上找呀找，稍微看到一个靠谱的说法是编译的时候没有讲hadoop的jar包导进来，但是给出解决办法是在spark-env.sh里面添加</p>
<p style="color:rgb(20,25,30);font-family:'PingFang SC', '微软雅黑', 'Microsoft YaHei', Helvetica, 'Helvetica Neue', Tahoma, Arial, sans-serif;font-size:14px;">
export SPARK_DIST_CLASSPATH=$(hadoop classpath)</p>
<p style="color:rgb(20,25,30);font-family:'PingFang SC', '微软雅黑', 'Microsoft YaHei', Helvetica, 'Helvetica Neue', Tahoma, Arial, sans-serif;font-size:14px;">
好了添加了这个，spark master可以起来了，但是slaves仍然是上面错误，又开始找资料了。每天都在痛苦的阅读英文文档，差点抑郁了。而且国内google这不给力，用vpn时断时续，差点砸电脑。最后快绝望的时候回到spark官网，仔细阅读他提供的编译命令，死马当活马医吧。按照官网提供的编译命令再次编译spark.官网地址：<a href="http://spark.apache.org/docs/1.5.0/building-spark.html%E3%80%82%E4%BB%96%E9%BB%98%E8%AE%A41.5.0%E4%BD%BF%E7%94%A8%E6%98%AFscala2.10%E8%BF%9B%E8%A1%8C%E7%BC%96%E8%AF%91%EF%BC%8C%E4%BD%86%E6%98%AF%E6%88%91%E5%AE%89%E8%A3%85%E7%9A%84scala%E6%98%AF2.11%E6%89%80%E4%BB%A5%E4%BD%BF%E7%94%A8%E4%BA%86%E4%BB%A5%E4%B8%8B%E5%91%BD%E4%BB%A4" rel="nofollow" style="color:rgb(20,25,30);">http://spark.apache.org/docs/1.5.0/building-spark.html。他默认1.5.0使用是scala2.10进行编译，但是我安装的scala是2.11所以使用了以下命令</a>。</p>
<p style="color:rgb(20,25,30);font-family:'PingFang SC', '微软雅黑', 'Microsoft YaHei', Helvetica, 'Helvetica Neue', Tahoma, Arial, sans-serif;font-size:14px;">
./dev/change-scala-version.sh 2.11<br>
mvn -Pyarn -Phadoop-2.4 -Dscala-2.11 -DskipTests clean package</p>
<p style="color:rgb(20,25,30);font-family:'PingFang SC', '微软雅黑', 'Microsoft YaHei', Helvetica, 'Helvetica Neue', Tahoma, Arial, sans-serif;font-size:14px;">
这次编译的spark-assembly-1.5.0-hadoop2.6.0.jar 包有140mb左右比上次的多了40mb，感觉有点靠谱了，将这个包移过去，ok了！太开心了，一切都跑起来了。泪奔了，hive官网害人呀。</p>
<p style="color:rgb(20,25,30);font-family:'PingFang SC', '微软雅黑', 'Microsoft YaHei', Helvetica, 'Helvetica Neue', Tahoma, Arial, sans-serif;font-size:14px;">
第四个坑，这个坑也算一个不大不小的坑，因为我偶然想装一下hue，在装hue遇到的错误，现在记录一下。<br>
首先坑是hadoop的坑，如果在hadoop core-site.xml下面没有配置</p>
<pre class="prettyprint" style="color:rgb(20,25,30);font-size:14px;background-color:rgb(255,255,255);"><code><span class="tag" style="color:rgb(0,0,136);">&lt;property&gt;</span><span class="pln" style="color:rgb(0,0,0);">
    </span><span class="tag" style="color:rgb(0,0,136);">&lt;name&gt;</span><span class="pln" style="color:rgb(0,0,0);">hadoop.proxyuser.hdfs.hosts</span><span class="tag" style="color:rgb(0,0,136);">&lt;/name&gt;</span><span class="pln" style="color:rgb(0,0,0);">
    </span><span class="tag" style="color:rgb(0,0,136);">&lt;value&gt;</span><span class="pln" style="color:rgb(0,0,0);">*</span><span class="tag" style="color:rgb(0,0,136);">&lt;/value&gt;</span><span class="pln" style="color:rgb(0,0,0);">
    </span><span class="tag" style="color:rgb(0,0,136);">&lt;description&gt;</span><span class="pln" style="color:rgb(0,0,0);">Defines the Unix user, hdfs, that will run the HttpFS server as
      a proxyuser.</span><span class="tag" style="color:rgb(0,0,136);">&lt;/description&gt;</span><span class="pln" style="color:rgb(0,0,0);">
  </span><span class="tag" style="color:rgb(0,0,136);">&lt;/property&gt;</span><span class="pln" style="color:rgb(0,0,0);">
  </span><span class="tag" style="color:rgb(0,0,136);">&lt;property&gt;</span><span class="pln" style="color:rgb(0,0,0);">
    </span><span class="tag" style="color:rgb(0,0,136);">&lt;name&gt;</span><span class="pln" style="color:rgb(0,0,0);">hadoop.proxyuser.hdfs.groups</span><span class="tag" style="color:rgb(0,0,136);">&lt;/name&gt;</span><span class="pln" style="color:rgb(0,0,0);">
    </span><span class="tag" style="color:rgb(0,0,136);">&lt;value&gt;</span><span class="pln" style="color:rgb(0,0,0);">*</span><span class="tag" style="color:rgb(0,0,136);">&lt;/value&gt;</span><span class="pln" style="color:rgb(0,0,0);">
    </span><span class="tag" style="color:rgb(0,0,136);">&lt;description&gt;</span><span class="pln" style="color:rgb(0,0,0);">Replaces hdfs with the Unix user that will start the HttpFS server.
      </span><span class="tag" style="color:rgb(0,0,136);">&lt;/description&gt;</span><span class="pln" style="color:rgb(0,0,0);">
  </span><span class="tag" style="color:rgb(0,0,136);">&lt;/property&gt;</span><span class="pln" style="color:rgb(0,0,0);">
httpfs-site.xml
  </span><span class="tag" style="color:rgb(0,0,136);">&lt;property&gt;</span><span class="pln" style="color:rgb(0,0,0);">
    </span><span class="tag" style="color:rgb(0,0,136);">&lt;name&gt;</span><span class="pln" style="color:rgb(0,0,0);">hadoop.proxyuser.hue.hosts</span><span class="tag" style="color:rgb(0,0,136);">&lt;/name&gt;</span><span class="pln" style="color:rgb(0,0,0);">
    </span><span class="tag" style="color:rgb(0,0,136);">&lt;value&gt;</span><span class="pln" style="color:rgb(0,0,0);">*</span><span class="tag" style="color:rgb(0,0,136);">&lt;/value&gt;</span><span class="pln" style="color:rgb(0,0,0);">
</span><span class="tag" style="color:rgb(0,0,136);">&lt;/property&gt;</span><span class="pln" style="color:rgb(0,0,0);">
</span><span class="tag" style="color:rgb(0,0,136);">&lt;property&gt;</span><span class="pln" style="color:rgb(0,0,0);">
    </span><span class="tag" style="color:rgb(0,0,136);">&lt;name&gt;</span><span class="pln" style="color:rgb(0,0,0);">hadoop.proxyuser.hue.groups</span><span class="tag" style="color:rgb(0,0,136);">&lt;/name&gt;</span><span class="pln" style="color:rgb(0,0,0);">
    </span><span class="tag" style="color:rgb(0,0,136);">&lt;value&gt;</span><span class="pln" style="color:rgb(0,0,0);">*</span><span class="tag" style="color:rgb(0,0,136);">&lt;/value&gt;</span><span class="pln" style="color:rgb(0,0,0);">
</span><span class="tag" style="color:rgb(0,0,136);">&lt;/property&gt;</span><span class="pln" style="color:rgb(0,0,0);">
hdfs-site.xml

</span><span class="tag" style="color:rgb(0,0,136);">&lt;property&gt;</span><span class="pln" style="color:rgb(0,0,0);">
  </span><span class="tag" style="color:rgb(0,0,136);">&lt;name&gt;</span><span class="pln" style="color:rgb(0,0,0);">dfs.webhdfs.enabled</span><span class="tag" style="color:rgb(0,0,136);">&lt;/name&gt;</span><span class="pln" style="color:rgb(0,0,0);">
  </span><span class="tag" style="color:rgb(0,0,136);">&lt;value&gt;</span><span class="pln" style="color:rgb(0,0,0);">true</span><span class="tag" style="color:rgb(0,0,136);">&lt;/value&gt;</span><span class="pln" style="color:rgb(0,0,0);">
</span><span class="tag" style="color:rgb(0,0,136);">&lt;/property&gt;</span></code></pre>
<p style="color:rgb(20,25,30);font-family:'PingFang SC', '微软雅黑', 'Microsoft YaHei', Helvetica, 'Helvetica Neue', Tahoma, Arial, sans-serif;font-size:14px;">
这三个配置没配好的你讲没有权限访问hadoop,但是这个都是小事情，网上教程多，不怎么算坑。<br>
接下来hive配置才是一个不大不小的坑，因为网上基本没有什么资料，对应版本是hive是2.0.1<br>
网上基本的版本都教了怎么连接，但是用网上连接方法hive.log老是显示 old version ? 我一直以为我hive版本过高了，但是换成1.2.1这个版本一样错误。<br>
于是又开始看google，看的又快奔溃了。开始网上有人叫把hive.server2.authentication这个属性改为NOSASL,但是改来改去没啥卵用，好了我就不卖关子了，<br>
hue要连接hive其实还需要修改两个属性<br>
第一个属性是hive.server2.authentication 这个要是NONE<br>
第二个属性是hive.server2.enable.doAs 一定要是flase 不然就会报 old version?<br>
看到可视化界面的时候还是有点成就感的。</p>
<p style="color:rgb(20,25,30);font-family:'PingFang SC', '微软雅黑', 'Microsoft YaHei', Helvetica, 'Helvetica Neue', Tahoma, Arial, sans-serif;font-size:14px;">
第五个坑，装好了那么多东西，肯定想跑跑试试，于是觉得对nginx 日志进行分析，这时候对网上hive 分析 nginx进行了搜索，搜了一大堆，但是按他们的正则建表都不可以用，又开始泪奔了。<br>
不停百度，资料结果全是千篇一律，好像大家抄的都是一篇。结果还是找呀找，终于找到原因了<br>
原来正则必须不是一根斜杠，原来java必须是两根\把我建表sql展示一下，不然还是太空了。其中不需要out那一行，直接用input就行<br>
create table logs(ipaddress STRING,identity STRING,<code>user</code> STRING,time STRING,request STRING,status STRING,size STRING,host STRING,referer STRING,rbody STRING,agent STRING,forwarded STRING,uaddr STRING,ustatus STRING,urtime
 STRING,rtime STRING)ROW FORMAT SERDE 'org.apache.hadoop.hive.contrib.serde2.RegexSerDe' WITH SERDEPROPERTIES ('input.regex' =<br>
'([0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}) (-) (-) (\[.\]) (\"[^\"]\") (\d{3}) (\d{1,}) (\"[^\"]\") (\"[^\"]\") (-|[^\s]) (\"[^\"]\") (\"[^\"]*\") ([0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}:[0-9]{1,5}) (\d{3}) ([\d]{1,}\.[\d]{1,}) ([\d]{1,}\.[\d]{1,})')<br>
接下来可以跑了，扔了一个sql，你妹又报错了<br>
return code 3 from org.apache.hadoop.hive.ql.exec.spark.SparkTask</p>
<p style="color:rgb(20,25,30);font-family:'PingFang SC', '微软雅黑', 'Microsoft YaHei', Helvetica, 'Helvetica Neue', Tahoma, Arial, sans-serif;font-size:14px;">
这个坑不算大，因为通过查找hive,spark日志发现原来spark找不到org.apache.hadoop.hive.contrib.serde2.RegexSerDe<br>
这个网上解决方法就多了，就是加上hive.aux.jars.path 这个属性里面你这个jar包地址如果是本地的记住前面要加file://哦不然找不到</p>
<p style="color:rgb(20,25,30);font-family:'PingFang SC', '微软雅黑', 'Microsoft YaHei', Helvetica, 'Helvetica Neue', Tahoma, Arial, sans-serif;font-size:14px;">
好了这是安装hive on spark 遇到的一些坑，希望我的资料能对需要的人有些帮助吧。个人总结：遇到不会的别看网上的文章了，全是千篇一律，还是看官网的资料跟官网论坛，比看那些安装篇强上太多。</p>
<br style="color:rgb(20,25,30);font-family:'PingFang SC', '微软雅黑', 'Microsoft YaHei', Helvetica, 'Helvetica Neue', Tahoma, Arial, sans-serif;font-size:14px;"><span style="color:rgb(20,25,30);font-family:'PingFang SC', '微软雅黑', 'Microsoft YaHei', Helvetica, 'Helvetica Neue', Tahoma, Arial, sans-serif;font-size:14px;">作者： 暗瞳 </span><br style="color:rgb(20,25,30);font-family:'PingFang SC', '微软雅黑', 'Microsoft YaHei', Helvetica, 'Helvetica Neue', Tahoma, Arial, sans-serif;font-size:14px;"><span style="color:rgb(20,25,30);font-family:'PingFang SC', '微软雅黑', 'Microsoft YaHei', Helvetica, 'Helvetica Neue', Tahoma, Arial, sans-serif;font-size:14px;">链接：http://www.imooc.com/article/8613</span><br>            </div>
                </div>