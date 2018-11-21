---
layout:     post
title:      windows搭建spark运行环境(windows scala,hadoop,spark安装,idea使用配置等)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载					https://blog.csdn.net/T1DMzks/article/details/69055014				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p><strong>关键字</strong>: spark windows安装，spark运行环境，idea普通模式构建spark程序，idea maven构建spark程序，idea运行wordcount</p>



<h2 id="安装scala">安装scala</h2>

<p>下载地址 <a href="http://www.scala-lang.org/download/" rel="nofollow" target="_blank">http://www.scala-lang.org/download/</a> <br>
 下载scala-xxx.msi ，然后直接安装好就行了（注意，默认的位置最好别有空格，对于windows10我直接安装没有出现问题，但是对于windows7却出现  此时不应有XXX，这种情况就是安装目录有空格），一般他会默认帮我们配置好环境，当在命令行窗口输出scala -version能出现版本，和输入scala能出现scala的编辑的时候，说明成功了</p>



<h2 id="安装spark">安装spark</h2>

<p>直接去官网，下载spark-1.4.0-bin-hadoop2.6.tgz，下载地址，解压后建议改名为spark，然后配置环境变量，把解压后的bin目录，写在path里面，然后尝试运行spark-shell, 一般可以成功运行，但是spark还依赖于hadoop。所以还需要安装hadoop</p>



<h2 id="安装hadoop">安装hadoop</h2>

<p>直接去官网下载，我下的是2.6，我之前测试2.7版本的其实也兼容2.6版本的，为了保险起见，下载和spark对应版本的hadoop，下载地址<a href="http://mirrors.hust.edu.cn/apache/hadoop/common/" rel="nofollow" target="_blank">http://mirrors.hust.edu.cn/apache/hadoop/common/</a> <br>
下载好后解压，然后把bin目录写在path环境里面，这时候，为了防止运行程序的时候出现nullpoint异常，我们需要去github <a href="https://github.com/steveloughran/winutils" rel="nofollow" target="_blank">https://github.com/steveloughran/winutils</a> 找到对应的hadoop版本，然后进入bin目录下，下载hadoop.dll和winutils.exe, 然后复制到所安装hadoop目录下</p>



<h2 id="安装idea">安装idea</h2>

<p>进入官网 <a href="http://www.jetbrains.com/idea/download/#section=windows" rel="nofollow" target="_blank">http://www.jetbrains.com/idea/download/#section=windows</a> 下载右边的Community版本的idea, 然后默认一直安装，安装好后，如果是运行scala项目，需要安装scala插件，在File-&gt;Settings&gt;Pligins下，点击Install JeBrains plugins, 找到scala，注意要看其版本和自己的idea要对应（一般都是对应的），然后点击install, 需要等待一段时间，然后安装好重启idea就行了</p>



<h2 id="运行wordcount">运行wordcount</h2>

<p>File-&gt;New-&gt;Project-&gt;scala-&gt;IDEA <br>
<img src="http://i1.piimg.com/567571/b7683db6f586455c.png" alt="" title=""></p>

<p>选择next，注意，这里jdk和scala版本一定要选择，如果jdk没有就手动指定jdk安装目录，scala没有就点击create，然后会出现版本进行选择就行了，没有版本就点击左下角download，一般第一次需要点击坐下家download <br>
<img src="http://i2.muimg.com/567571/ccc5b4c41ae94a6c.png" alt="" title=""> <br>
<img src="http://i1.piimg.com/567571/90080539b88c9c09.png" alt="" title="">  </p>

<p>创建一个wordcount, 其中我们需要依赖于spark的spark-assembly-1.4.0-hadoop2.6.0.jar包， idea点击File-&gt;Project Structure-&gt;Module, 选择右边的Dependencies,点击右边的+号， 选择jars or direc… 引入spark-assembly-1.4.0-hadoop2.6.0.jar包即可</p>



<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-comment">/*
 * Licensed to the Apache Software Foundation (ASF) under one or more
 * contributor license agreements.  See the NOTICE file distributed with
 * this work for additional information regarding copyright ownership.
 * The ASF licenses this file to You under the Apache License, Version 2.0
 * (the "License"); you may not use this file except in compliance with
 * the License.  You may obtain a copy of the License at
 *
 *    http://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed


----------


 on an "AS IS" BASIS,
 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 * See the License for the specific language governing permissions and
 * limitations under the License.
 */</span>

<span class="hljs-keyword">package</span> cn.kaishun.spark;

<span class="hljs-keyword">import</span> scala.Tuple2;
<span class="hljs-keyword">import</span> org.apache.spark.SparkConf;
<span class="hljs-keyword">import</span> org.apache.spark.api.java.JavaPairRDD;
<span class="hljs-keyword">import</span> org.apache.spark.api.java.JavaRDD;
<span class="hljs-keyword">import</span> org.apache.spark.api.java.JavaSparkContext;
<span class="hljs-keyword">import</span> org.apache.spark.api.java.function.FlatMapFunction;
<span class="hljs-keyword">import</span> org.apache.spark.api.java.function.Function2;
<span class="hljs-keyword">import</span> org.apache.spark.api.java.function.PairFunction;

<span class="hljs-keyword">import</span> java.util.Arrays;
<span class="hljs-keyword">import</span> java.util.List;
<span class="hljs-keyword">import</span> java.util.regex.Pattern;

<span class="hljs-keyword">public</span> <span class="hljs-keyword">final</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">JavaWordCount</span> {</span>
  <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">final</span> Pattern SPACE = Pattern.compile(<span class="hljs-string">" "</span>);

  <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) <span class="hljs-keyword">throws</span> Exception {

    <span class="hljs-keyword">if</span> (args.length &lt; <span class="hljs-number">1</span>) {
      System.err.println(<span class="hljs-string">"Usage: JavaWordCount &lt;file&gt;"</span>);
      System.exit(<span class="hljs-number">1</span>);
    }

    SparkConf sparkConf = <span class="hljs-keyword">new</span> SparkConf().setAppName(<span class="hljs-string">"JavaWordCount"</span>).setMaster(<span class="hljs-string">"local"</span>);
    JavaSparkContext ctx = <span class="hljs-keyword">new</span> JavaSparkContext(sparkConf);
    JavaRDD&lt;String&gt; lines = ctx.textFile(args[<span class="hljs-number">0</span>], <span class="hljs-number">1</span>);

    JavaRDD&lt;String&gt; words = lines.flatMap(<span class="hljs-keyword">new</span> FlatMapFunction&lt;String, String&gt;() {
      <span class="hljs-annotation">@Override</span>
      <span class="hljs-keyword">public</span> Iterable&lt;String&gt; <span class="hljs-title">call</span>(String s) {
        <span class="hljs-keyword">return</span> Arrays.asList(SPACE.split(s));
      }
    });

    JavaPairRDD&lt;String, Integer&gt; ones = words.mapToPair(<span class="hljs-keyword">new</span> PairFunction&lt;String, String, Integer&gt;() {
      <span class="hljs-annotation">@Override</span>
      <span class="hljs-keyword">public</span> Tuple2&lt;String, Integer&gt; <span class="hljs-title">call</span>(String s) {
        <span class="hljs-keyword">return</span> <span class="hljs-keyword">new</span> Tuple2&lt;String, Integer&gt;(s, <span class="hljs-number">1</span>);
      }
    });

    JavaPairRDD&lt;String, Integer&gt; counts = ones.reduceByKey(<span class="hljs-keyword">new</span> Function2&lt;Integer, Integer, Integer&gt;() {
      <span class="hljs-annotation">@Override</span>
      <span class="hljs-keyword">public</span> Integer <span class="hljs-title">call</span>(Integer i1, Integer i2) {
        <span class="hljs-keyword">return</span> i1 + i2;
      }
    });
    counts.saveAsTextFile(<span class="hljs-string">"G:\\ceshi\\bigdata\\spark\\wordcount\\output\\out1"</span>);
    List&lt;Tuple2&lt;String, Integer&gt;&gt; output = counts.collect();
    <span class="hljs-keyword">for</span> (Tuple2&lt;?,?&gt; tuple : output) {
      System.out.println(tuple._1() + <span class="hljs-string">": "</span> + tuple._2());
    }
    ctx.stop();
  }
}

</code></pre>

<p>创建测试文件，在args进行配置输入和输出路径，点击运行，即可 <br>
若有空指针问题，大多情况是hadoop下的winutils.exe的问题，github找一份复制就可以了(注： 我记得之前如果第一次出问题了，以后怎么解决都解决不了，一直报空指针，后来我复制winutils.exe和hadoop.dll,然后重启电脑才解决)  </p>



<h2 id="idea-maven-构建spark程序">idea  Maven 构建spark程序</h2>

<p>大多情况下，我不想按照上面的方式，各种jar包的导入，我更多的是想用maven来构建spark程序。</p>



<h3 id="maven下载速度慢的解决办法">maven下载速度慢的解决办法</h3>

<p>在maven安装目录的conf目录下，修改settings.xml文件, 添加阿里云的加速即可，参考我的setting</p>



<pre class="prettyprint"><code class=" hljs xml"> <span class="hljs-pi">&lt;?xml version="1.0" encoding="UTF-8"?&gt;</span>

<span class="hljs-comment">&lt;!--
Licensed to the Apache Software Foundation (ASF) under one
or more contributor license agreements.  See the NOTICE file
distributed with this work for additional information
regarding copyright ownership.  The ASF licenses this file
to you under the Apache License, Version 2.0 (the
"License"); you may not use this file except in compliance
with the License.  You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing,
software distributed under the License is distributed on an
"AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
KIND, either express or implied.  See the License for the
specific language governing permissions and limitations
under the License.
--&gt;</span>

<span class="hljs-comment">&lt;!--
 | This is the configuration file for Maven. It can be specified at two levels:
 |
 |  1. User Level. This settings.xml file provides configuration for a single user,
 |                 and is normally provided in ${user.home}/.m2/settings.xml.
 |
 |                 NOTE: This location can be overridden with the CLI option:
 |
 |                 -s /path/to/user/settings.xml
 |
 |  2. Global Level. This settings.xml file provides configuration for all Maven
 |                 users on a machine (assuming they're all using the same Maven
 |                 installation). It's normally provided in
 |                 ${maven.home}/conf/settings.xml.
 |
 |                 NOTE: This location can be overridden with the CLI option:
 |
 |                 -gs /path/to/global/settings.xml
 |
 | The sections in this sample file are intended to give you a running start at
 | getting the most out of your Maven installation. Where appropriate, the default
 | values (values used when the setting is not specified) are provided.
 |
 |--&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">settings</span> <span class="hljs-attribute">xmlns</span>=<span class="hljs-value">"http://maven.apache.org/SETTINGS/1.0.0"</span>
          <span class="hljs-attribute">xmlns:xsi</span>=<span class="hljs-value">"http://www.w3.org/2001/XMLSchema-instance"</span>
          <span class="hljs-attribute">xsi:schemaLocation</span>=<span class="hljs-value">"http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd"</span>&gt;</span>

  <span class="hljs-tag">&lt;<span class="hljs-title">pluginGroups</span>&gt;</span>

  <span class="hljs-tag">&lt;/<span class="hljs-title">pluginGroups</span>&gt;</span>

  <span class="hljs-tag">&lt;<span class="hljs-title">proxies</span>&gt;</span>

  <span class="hljs-tag">&lt;/<span class="hljs-title">proxies</span>&gt;</span>

  <span class="hljs-tag">&lt;<span class="hljs-title">servers</span>&gt;</span>

  <span class="hljs-tag">&lt;/<span class="hljs-title">servers</span>&gt;</span>

<span class="hljs-tag">&lt;<span class="hljs-title">repositories</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">repository</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">id</span>&gt;</span>central<span class="hljs-tag">&lt;/<span class="hljs-title">id</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>Central Repository<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">url</span>&gt;</span>http://maven.aliyun.com/nexus/content/repositories/central<span class="hljs-tag">&lt;/<span class="hljs-title">url</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">layout</span>&gt;</span>default<span class="hljs-tag">&lt;/<span class="hljs-title">layout</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">snapshots</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">enabled</span>&gt;</span>false<span class="hljs-tag">&lt;/<span class="hljs-title">enabled</span>&gt;</span>
      <span class="hljs-tag">&lt;/<span class="hljs-title">snapshots</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">repository</span>&gt;</span>
  <span class="hljs-tag">&lt;/<span class="hljs-title">repositories</span>&gt;</span>


  <span class="hljs-tag">&lt;<span class="hljs-title">mirrors</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">mirror</span>&gt;</span>  
        <span class="hljs-tag">&lt;<span class="hljs-title">id</span>&gt;</span>nexus-aliyun<span class="hljs-tag">&lt;/<span class="hljs-title">id</span>&gt;</span>  
        <span class="hljs-tag">&lt;<span class="hljs-title">mirrorOf</span>&gt;</span>*<span class="hljs-tag">&lt;/<span class="hljs-title">mirrorOf</span>&gt;</span>  
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>Nexus aliyun<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>  
        <span class="hljs-tag">&lt;<span class="hljs-title">url</span>&gt;</span>http://maven.aliyun.com/nexus/content/groups/public<span class="hljs-tag">&lt;/<span class="hljs-title">url</span>&gt;</span>  
    <span class="hljs-tag">&lt;/<span class="hljs-title">mirror</span>&gt;</span>  


  <span class="hljs-tag">&lt;/<span class="hljs-title">mirrors</span>&gt;</span>

  <span class="hljs-tag">&lt;<span class="hljs-title">profiles</span>&gt;</span>

  <span class="hljs-tag">&lt;/<span class="hljs-title">profiles</span>&gt;</span>


<span class="hljs-tag">&lt;/<span class="hljs-title">settings</span>&gt;</span>
</code></pre>

<p>pom中增加</p>



<pre class="prettyprint"><code class=" hljs xml">        <span class="hljs-tag">&lt;<span class="hljs-title">repository</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">id</span>&gt;</span>central<span class="hljs-tag">&lt;/<span class="hljs-title">id</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>Central Repository<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">url</span>&gt;</span>http://maven.aliyun.com/nexus/content/repositories/central<span class="hljs-tag">&lt;/<span class="hljs-title">url</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">layout</span>&gt;</span>default<span class="hljs-tag">&lt;/<span class="hljs-title">layout</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">snapshots</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">enabled</span>&gt;</span>false<span class="hljs-tag">&lt;/<span class="hljs-title">enabled</span>&gt;</span>
            <span class="hljs-tag">&lt;/<span class="hljs-title">snapshots</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">repository</span>&gt;</span></code></pre>



<h3 id="spark中的maven配置"><strong>spark中的maven配置</strong></h3>

<p>这是我的本身配置，有的不必要可以删除，其中的groupId，artifactId 根据自己的设置而设置 <br>
<strong>spark1.6的配置</strong></p>



<pre class="prettyprint"><code class="language-xml hljs "><span class="hljs-pi">&lt;?xml version="1.0" encoding="UTF-8"?&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">project</span> <span class="hljs-attribute">xmlns</span>=<span class="hljs-value">"http://maven.apache.org/POM/4.0.0"</span>
         <span class="hljs-attribute">xmlns:xsi</span>=<span class="hljs-value">"http://www.w3.org/2001/XMLSchema-instance"</span>
         <span class="hljs-attribute">xsi:schemaLocation</span>=<span class="hljs-value">"http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">modelVersion</span>&gt;</span>4.0.0<span class="hljs-tag">&lt;/<span class="hljs-title">modelVersion</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>cn.kaishun<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>mvntest<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.0-SNAPSHOT<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">properties</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">project.build.sourceEncoding</span>&gt;</span>UTF-8<span class="hljs-tag">&lt;/<span class="hljs-title">project.build.sourceEncoding</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">spark.version</span>&gt;</span>1.6.0<span class="hljs-tag">&lt;/<span class="hljs-title">spark.version</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">scala.version</span>&gt;</span>2.10<span class="hljs-tag">&lt;/<span class="hljs-title">scala.version</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">hadoop.version</span>&gt;</span>2.6.0<span class="hljs-tag">&lt;/<span class="hljs-title">hadoop.version</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">properties</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">dependencies</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.spark<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spark-core_${scala.version}<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>${spark.version}<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.spark<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spark-sql_${scala.version}<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>${spark.version}<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.spark<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spark-hive_${scala.version}<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>${spark.version}<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.spark<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spark-streaming_${scala.version}<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>${spark.version}<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.hadoop<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>hadoop-client<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>2.6.0<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.spark<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spark-streaming-kafka_${scala.version}<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>${spark.version}<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.spark<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spark-mllib_${scala.version}<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>${spark.version}<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>mysql<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>mysql-connector-java<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>5.1.39<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>junit<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>junit<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>4.12<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.slf4j<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>slf4j-api<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.6.6<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.slf4j<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>slf4j-log4j12<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.6.6<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>log4j<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>log4j<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.2.16<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">dependencies</span>&gt;</span>

    <span class="hljs-comment">&lt;!-- maven官方 http://repo1.maven.org/maven2/  或 http://repo2.maven.org/maven2/ （延迟低一些） --&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">repositories</span>&gt;</span>
               <span class="hljs-tag">&lt;<span class="hljs-title">repository</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">id</span>&gt;</span>central<span class="hljs-tag">&lt;/<span class="hljs-title">id</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>Central Repository<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">url</span>&gt;</span>http://maven.aliyun.com/nexus/content/repositories/central<span class="hljs-tag">&lt;/<span class="hljs-title">url</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">layout</span>&gt;</span>default<span class="hljs-tag">&lt;/<span class="hljs-title">layout</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">snapshots</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">enabled</span>&gt;</span>false<span class="hljs-tag">&lt;/<span class="hljs-title">enabled</span>&gt;</span>
            <span class="hljs-tag">&lt;/<span class="hljs-title">snapshots</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">repository</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">repository</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">id</span>&gt;</span>central<span class="hljs-tag">&lt;/<span class="hljs-title">id</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>Maven Repository Switchboard<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">layout</span>&gt;</span>default<span class="hljs-tag">&lt;/<span class="hljs-title">layout</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">url</span>&gt;</span>http://repo2.maven.org/maven2<span class="hljs-tag">&lt;/<span class="hljs-title">url</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">snapshots</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">enabled</span>&gt;</span>false<span class="hljs-tag">&lt;/<span class="hljs-title">enabled</span>&gt;</span>
            <span class="hljs-tag">&lt;/<span class="hljs-title">snapshots</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">repository</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">repositories</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">build</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">sourceDirectory</span>&gt;</span>src/main/scala<span class="hljs-tag">&lt;/<span class="hljs-title">sourceDirectory</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">testSourceDirectory</span>&gt;</span>src/test/scala<span class="hljs-tag">&lt;/<span class="hljs-title">testSourceDirectory</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">plugins</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">plugin</span>&gt;</span>
                <span class="hljs-comment">&lt;!-- MAVEN 编译使用的JDK版本 --&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.maven.plugins<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>maven-compiler-plugin<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>3.3<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
                    <span class="hljs-tag">&lt;<span class="hljs-title">source</span>&gt;</span>1.7<span class="hljs-tag">&lt;/<span class="hljs-title">source</span>&gt;</span>
                    <span class="hljs-tag">&lt;<span class="hljs-title">target</span>&gt;</span>1.7<span class="hljs-tag">&lt;/<span class="hljs-title">target</span>&gt;</span>
                    <span class="hljs-tag">&lt;<span class="hljs-title">encoding</span>&gt;</span>UTF-8<span class="hljs-tag">&lt;/<span class="hljs-title">encoding</span>&gt;</span>
                <span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span>
            <span class="hljs-tag">&lt;/<span class="hljs-title">plugin</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">plugins</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">build</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">project</span>&gt;</span>
</code></pre>

<p><strong>spark2.10</strong>  </p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-pi">&lt;?xml version="1.0" encoding="UTF-8"?&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">project</span> <span class="hljs-attribute">xmlns</span>=<span class="hljs-value">"http://maven.apache.org/POM/4.0.0"</span>
         <span class="hljs-attribute">xmlns:xsi</span>=<span class="hljs-value">"http://www.w3.org/2001/XMLSchema-instance"</span>
         <span class="hljs-attribute">xsi:schemaLocation</span>=<span class="hljs-value">"http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">modelVersion</span>&gt;</span>4.0.0<span class="hljs-tag">&lt;/<span class="hljs-title">modelVersion</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>sparkmaven<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>com.shuner.mvn<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.0-SNAPSHOT<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">properties</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">project.build.sourceEncoding</span>&gt;</span>UTF-8<span class="hljs-tag">&lt;/<span class="hljs-title">project.build.sourceEncoding</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">spark.version</span>&gt;</span>2.11<span class="hljs-tag">&lt;/<span class="hljs-title">spark.version</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">scala.version</span>&gt;</span>2.11.8<span class="hljs-tag">&lt;/<span class="hljs-title">scala.version</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">hadoop.version</span>&gt;</span>2.6.0<span class="hljs-tag">&lt;/<span class="hljs-title">hadoop.version</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">properties</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">dependencies</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.spark<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spark-core_2.10<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>2.1.0<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.spark<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spark-sql_2.10<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>2.1.0<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.spark<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spark-hive_2.10<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>2.1.0<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.spark<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spark-streaming_2.10<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>2.1.0<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.hadoop<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>hadoop-client<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>2.6.0<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>junit<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>junit<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>4.12<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.slf4j<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>slf4j-api<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.6.6<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.slf4j<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>slf4j-log4j12<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.6.6<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>log4j<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>log4j<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.2.16<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">dependencies</span>&gt;</span>

    <span class="hljs-comment">&lt;!-- maven官方 http://repo1.maven.org/maven2/  或 http://repo2.maven.org/maven2/ （延迟低一些） --&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">repositories</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">repository</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">id</span>&gt;</span>central<span class="hljs-tag">&lt;/<span class="hljs-title">id</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>Central Repository<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">url</span>&gt;</span>http://maven.aliyun.com/nexus/content/repositories/central<span class="hljs-tag">&lt;/<span class="hljs-title">url</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">layout</span>&gt;</span>default<span class="hljs-tag">&lt;/<span class="hljs-title">layout</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">snapshots</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">enabled</span>&gt;</span>false<span class="hljs-tag">&lt;/<span class="hljs-title">enabled</span>&gt;</span>
            <span class="hljs-tag">&lt;/<span class="hljs-title">snapshots</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">repository</span>&gt;</span>

    <span class="hljs-tag">&lt;/<span class="hljs-title">repositories</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">build</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">sourceDirectory</span>&gt;</span>src/main/scala<span class="hljs-tag">&lt;/<span class="hljs-title">sourceDirectory</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">testSourceDirectory</span>&gt;</span>src/test/scala<span class="hljs-tag">&lt;/<span class="hljs-title">testSourceDirectory</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">plugins</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">plugin</span>&gt;</span>
                <span class="hljs-comment">&lt;!-- MAVEN 编译使用的JDK版本 --&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.maven.plugins<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>maven-compiler-plugin<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>3.3<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
                    <span class="hljs-tag">&lt;<span class="hljs-title">source</span>&gt;</span>1.7<span class="hljs-tag">&lt;/<span class="hljs-title">source</span>&gt;</span>
                    <span class="hljs-tag">&lt;<span class="hljs-title">target</span>&gt;</span>1.7<span class="hljs-tag">&lt;/<span class="hljs-title">target</span>&gt;</span>
                    <span class="hljs-tag">&lt;<span class="hljs-title">encoding</span>&gt;</span>UTF-8<span class="hljs-tag">&lt;/<span class="hljs-title">encoding</span>&gt;</span>
                <span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span>
            <span class="hljs-tag">&lt;/<span class="hljs-title">plugin</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">plugins</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">build</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">project</span>&gt;</span></code></pre>

<p>最后补充，来一个既可以玩spark，又可以玩hadoop的pom.xml</p>

<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-pi">&lt;?xml version="1.0" encoding="UTF-8"?&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">project</span> <span class="hljs-attribute">xmlns</span>=<span class="hljs-value">"http://maven.apache.org/POM/4.0.0"</span>
         <span class="hljs-attribute">xmlns:xsi</span>=<span class="hljs-value">"http://www.w3.org/2001/XMLSchema-instance"</span>
         <span class="hljs-attribute">xsi:schemaLocation</span>=<span class="hljs-value">"http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">modelVersion</span>&gt;</span>4.0.0<span class="hljs-tag">&lt;/<span class="hljs-title">modelVersion</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>cn.mingtong<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spark16test<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.0-SNAPSHOT<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">properties</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">project.build.sourceEncoding</span>&gt;</span>UTF-8<span class="hljs-tag">&lt;/<span class="hljs-title">project.build.sourceEncoding</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">spark.version</span>&gt;</span>1.6.0<span class="hljs-tag">&lt;/<span class="hljs-title">spark.version</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">scala.version</span>&gt;</span>2.10<span class="hljs-tag">&lt;/<span class="hljs-title">scala.version</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">hadoop.version</span>&gt;</span>2.7.3<span class="hljs-tag">&lt;/<span class="hljs-title">hadoop.version</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">properties</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">dependencies</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.spark<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spark-core_${scala.version}<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>${spark.version}<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.spark<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spark-sql_${scala.version}<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>${spark.version}<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.spark<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spark-hive_${scala.version}<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>${spark.version}<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.spark<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spark-streaming_${scala.version}<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>${spark.version}<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>


        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>mysql<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>mysql-connector-java<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>5.1.39<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>junit<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>junit<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>4.12<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.slf4j<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>slf4j-api<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.6.6<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.slf4j<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>slf4j-log4j12<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.6.6<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>log4j<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>log4j<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.2.16<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>


        <span class="hljs-comment">&lt;!-- 配置hadoop的环境 --&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.hadoop<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>hadoop-client<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>${hadoop.version}<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.hadoop<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>hadoop-common<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>${hadoop.version}<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.hadoop<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>hadoop-hdfs<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>${hadoop.version}<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>

    <span class="hljs-tag">&lt;/<span class="hljs-title">dependencies</span>&gt;</span>




    <span class="hljs-comment">&lt;!-- maven官方 http://repo1.maven.org/maven2/  或 http://repo2.maven.org/maven2/ （延迟低一些） --&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">repositories</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">repository</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">id</span>&gt;</span>central<span class="hljs-tag">&lt;/<span class="hljs-title">id</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>Central Repository<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">url</span>&gt;</span>http://maven.aliyun.com/nexus/content/repositories/central<span class="hljs-tag">&lt;/<span class="hljs-title">url</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">layout</span>&gt;</span>default<span class="hljs-tag">&lt;/<span class="hljs-title">layout</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">snapshots</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">enabled</span>&gt;</span>false<span class="hljs-tag">&lt;/<span class="hljs-title">enabled</span>&gt;</span>
            <span class="hljs-tag">&lt;/<span class="hljs-title">snapshots</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">repository</span>&gt;</span>

    <span class="hljs-tag">&lt;/<span class="hljs-title">repositories</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">build</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">sourceDirectory</span>&gt;</span>src/main/scala<span class="hljs-tag">&lt;/<span class="hljs-title">sourceDirectory</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">testSourceDirectory</span>&gt;</span>src/test/scala<span class="hljs-tag">&lt;/<span class="hljs-title">testSourceDirectory</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">plugins</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">plugin</span>&gt;</span>
                <span class="hljs-comment">&lt;!-- MAVEN 编译使用的JDK版本 --&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.maven.plugins<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>maven-compiler-plugin<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>3.3<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
                    <span class="hljs-tag">&lt;<span class="hljs-title">source</span>&gt;</span>1.8<span class="hljs-tag">&lt;/<span class="hljs-title">source</span>&gt;</span>
                    <span class="hljs-tag">&lt;<span class="hljs-title">target</span>&gt;</span>1.8<span class="hljs-tag">&lt;/<span class="hljs-title">target</span>&gt;</span>
                    <span class="hljs-tag">&lt;<span class="hljs-title">encoding</span>&gt;</span>UTF-8<span class="hljs-tag">&lt;/<span class="hljs-title">encoding</span>&gt;</span>
                <span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span>
            <span class="hljs-tag">&lt;/<span class="hljs-title">plugin</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">plugins</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">build</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">project</span>&gt;</span></code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>