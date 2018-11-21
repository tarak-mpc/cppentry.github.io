---
layout:     post
title:      spark 支持native redis Api（精华）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<pre style="font-family:Menlo;font-size:9pt;background-color:rgb(255,255,255);"></pre><pre style="background-color:rgb(255,255,255);font-family:Menlo;font-size:9pt;"><span style="color:#000080;"><strong>def </strong></span>main(args: Array[<span style="color:#20999d;">String</span>]): Unit = {

  <span style="color:#000080;"><strong>val </strong></span>conf = <span style="color:#000080;"><strong>new </strong></span>SparkConf().setMaster(<span style="color:#008000;"><strong>"local[*]"</strong></span>)
    .setAppName(<span style="color:#008000;"><strong>"redis"</strong></span>)
    .buildRedis()


  <span style="color:#000080;"><strong>val </strong></span>sc = <span style="color:#000080;"><strong>new </strong></span>SparkContext(conf)


  <span style="color:#000080;"><strong>val </strong></span>inithost = sc.getConf.inithost()</pre><pre style="background-color:rgb(255,255,255);font-family:Menlo;font-size:9pt;"> </pre><pre style="background-color:rgb(255,255,255);font-size:9pt;font-family:Menlo;">  <span style="color:#ff0000;">//原生的操作</span></pre><pre style="background-color:rgb(255,255,255);font-family:Menlo;font-size:9pt;"><span style="color:#000080;"><strong>  val </strong></span>rediscontext=<span style="color:#000080;"><strong>new </strong></span>RedisContext(sc)</pre>  <span style="color:#808080;"><em>//原声native的支持，从redis读取数据，主要.getZSet()，要和redis存入的类型一致(list,zset,hash)</em></span><span style="color:#808080;"><em>  </em></span><span style="color:#000080;"><strong>val </strong></span>runKeyRDD = sc.fromRedisKeyPattern(inithost, <span style="color:#008000;"><strong>"runkey"</strong></span>).getZSet()  runKeyRDD.foreach(<span style="font-style:italic;">println</span>)  <span style="color:#000080;"><strong>val </strong></span>runTestRDD = sc.parallelize(<span style="color:#660e7a;"><em>Seq</em></span>(<span style="color:#008000;"><strong>"DB2"</strong></span>)).map(x =&gt; (x, x.length.toString))  <span style="color:#808080;"><em>//RDD的类型为RDD[String,String]</em></span><span style="color:#808080;"><em>  </em></span>sc.toRedisZSET(runTestRDD, <span style="color:#008000;"><strong>"runkey"</strong></span>, inithost)}<br><pre style="background-color:rgb(255,255,255);font-family:Menlo;font-size:9pt;">//隐式类
<span style="color:#000080;"><strong>implicit class </strong></span>ResiConf(conf: SparkConf) {

  <span style="color:#000080;"><strong>def </strong></span>buildRedis(): SparkConf = {
    conf.set(<span style="color:#008000;"><strong>"redis.host"</strong></span>, <span style="color:#008000;"><strong>"127.0.0.1"</strong></span>)
    conf.set(<span style="color:#008000;"><strong>"redis.port"</strong></span>, <span style="color:#008000;"><strong>"6379"</strong></span>)

  }
  <span style="color:#000080;"><strong>def </strong></span>inithost(): (<span style="color:#20999d;">String</span>, Int) = {
    (<span style="color:#008000;"><strong>"localhost"</strong></span>, <span style="color:#0000ff;">6379</span>)
  }


}</pre><br>
            </div>
                </div>