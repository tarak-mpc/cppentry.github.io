---
layout:     post
title:      Hadoop集群初步使用-编写wordcount程序
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/weixin_35852328/article/details/78120981				</div>
								            <div id="content_views" class="markdown_views prism-dracula">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="1hdfs使用">1.HDFS使用</h2>

<p>查看集群的状态：hdfs dfsadmin -report <br>
web控制台查看hdfs集群信息：<a href="http://hadoop1:50070/" rel="nofollow" target="_blank">http://hadoop1:50070/</a> <br>
查看HDFS中的目录信息：hadoop fs -ls / <br>
在HDFS上创建文件夹：hadoop fs -mkdir -p /aaa/bbb/ccc <br>
上传文件： hadoop fs -put 本地文件路径 to hdfs路径 <br>
下载文件：hadoop fs -get hdfs路径</p>



<h2 id="2mapreduce使用">2.MAPREDUCE使用</h2>

<p>mapreduce是hadoop中的分布式运算编程框架，只要按照其编程规范，只需要编写少量的业务逻辑代码即可实现一个强大的海量数据并发处理程序。 <br>
<strong>Demo-统计单词出现次数</strong> <br>
1）.mapper</p>



<pre class="prettyprint"><code class=" hljs axapta"><span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">WordCountMapper</span> <span class="hljs-inheritance"><span class="hljs-keyword">extends</span></span> <span class="hljs-title">Mapper</span>&lt;<span class="hljs-title">LongWritable</span>, <span class="hljs-title">Text</span>, <span class="hljs-title">Text</span>, <span class="hljs-title">IntWritable</span>&gt;{</span>
    IntWritable one = <span class="hljs-keyword">new</span> IntWritable(<span class="hljs-number">1</span>);
    Text word = <span class="hljs-keyword">new</span> Text();

    <span class="hljs-comment">//map方法的生命周期：  框架每传一行数据就被调用一次</span>
    <span class="hljs-comment">//key :  这一行的起始点在文件中的偏移量</span>
    <span class="hljs-comment">//value: 这一行的内容</span>
    @Override
    <span class="hljs-keyword">protected</span> <span class="hljs-keyword">void</span> map(LongWritable key, Text value, Context context) throws IOException, InterruptedException {
        <span class="hljs-comment">//利用jdk的工具类拆分value，工具类默认是用空格分隔数据的</span>
        StringTokenizer itr = <span class="hljs-keyword">new</span> StringTokenizer(value.toString());
        <span class="hljs-keyword">while</span>(itr.hasMoreTokens()) {
            word.set(itr.nextToken());
            <span class="hljs-comment">//输出&lt;单词，1&gt;</span>
            context.write(word, one);
        }
    }
}</code></pre>

<p>2）reducer</p>



<pre class="prettyprint"><code class=" hljs axapta"><span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">WordCountReducer</span> <span class="hljs-inheritance"><span class="hljs-keyword">extends</span></span> <span class="hljs-title">Reducer</span>&lt;<span class="hljs-title">Text</span>, <span class="hljs-title">IntWritable</span>, <span class="hljs-title">Text</span>, <span class="hljs-title">IntWritable</span>&gt; {</span>

    <span class="hljs-comment">//生命周期：框架每传递进来一个kv 组，reduce方法被调用一次</span>
    @Override
    <span class="hljs-keyword">protected</span> <span class="hljs-keyword">void</span> reduce(Text key, Iterable&lt;IntWritable&gt; values, Context context) throws IOException, InterruptedException {
        <span class="hljs-comment">//定义一个计数器</span>
        <span class="hljs-keyword">int</span> <span class="hljs-keyword">count</span> = <span class="hljs-number">0</span>;
        <span class="hljs-comment">//遍历这一组kv的所有v，累加到count中</span>
        <span class="hljs-keyword">for</span>(IntWritable value:values){
            <span class="hljs-keyword">count</span> += value.get();
        }
        context.write(key, <span class="hljs-keyword">new</span> IntWritable(<span class="hljs-keyword">count</span>));
    }
}</code></pre>

<p>3）定义一个主类，用来描述job并提交job</p>



<pre class="prettyprint"><code class=" hljs actionscript"><span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">WordCountRunner</span> {</span>
    <span class="hljs-comment">//把业务逻辑相关的信息（哪个是mapper，哪个是reducer，要处理的数据在哪里，输出的结果放哪里。。。。。。）描述成一个job对象</span>
    <span class="hljs-comment">//把这个描述好的job提交给集群去运行</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> main(String[] args) throws Exception {
        Configuration conf = <span class="hljs-keyword">new</span> Configuration();
        Job wcjob = Job.getInstance(conf);
        <span class="hljs-comment">//指定我这个job所在的jar包</span>
<span class="hljs-comment">//      wcjob.setJar("/home/hadoop/wordcount.jar");</span>
        wcjob.setJarByClass(WordCountRunner.<span class="hljs-keyword">class</span>);

        wcjob.setMapperClass(WordCountMapper.<span class="hljs-keyword">class</span>);
        wcjob.setReducerClass(WordCountReducer.<span class="hljs-keyword">class</span>);
        <span class="hljs-comment">//设置我们的业务逻辑Mapper类的输出key和value的数据类型</span>
        wcjob.setMapOutputKeyClass(Text.<span class="hljs-keyword">class</span>);
        wcjob.setMapOutputValueClass(IntWritable.<span class="hljs-keyword">class</span>);
        <span class="hljs-comment">//设置我们的业务逻辑Reducer类的输出key和value的数据类型</span>
        wcjob.setOutputKeyClass(Text.<span class="hljs-keyword">class</span>);
        wcjob.setOutputValueClass(IntWritable.<span class="hljs-keyword">class</span>);

        <span class="hljs-comment">//指定要处理的数据所在的位置</span>
        FileInputFormat.setInputPaths(wcjob, <span class="hljs-string">"hdfs://hadoop1:9000/wordcount/intput/wordcount.txt"</span>);
        <span class="hljs-comment">//指定处理完成之后的结果所保存的位置</span>
        FileOutputFormat.setOutputPath(wcjob, <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"hdfs://hdp-server01:9000/wordcount/output/"</span>));

        <span class="hljs-comment">//向yarn集群提交这个job</span>
        boolean res = wcjob.waitForCompletion(<span class="hljs-literal">true</span>);
        System.exit(res?<span class="hljs-number">0</span>:<span class="hljs-number">1</span>);
    }</code></pre>

<p>4）打包项目，准备好输入的数据并上传到hdfs中，将打包的jar包放到集群机器的任意位置中。</p>



<pre class="prettyprint"><code class=" hljs lasso">打包项目用myeclipse打包并指定好运行方法
准备好一个文本文件并输入一些内容，名称叫wordcount<span class="hljs-built_in">.</span>txt

hadoop fs <span class="hljs-attribute">-mkdir</span> <span class="hljs-attribute">-p</span> wordcount/input<span class="hljs-subst">/</span>
hadoop fs <span class="hljs-attribute">-put</span> /home/wordcount<span class="hljs-built_in">.</span>txt /wordcount/input</code></pre>

<p>5）使用命令启动wordcount程序jar包</p>



<pre class="prettyprint"><code class=" hljs avrasm">hodoop jar wordcount<span class="hljs-preprocessor">.jar</span> <span class="hljs-keyword">com</span><span class="hljs-preprocessor">.xt</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.mapreduce</span> wordcount/input wordcount/ouput</code></pre>

<p>6）查看执行结果</p>



<pre class="prettyprint"><code class=" hljs lasso">hadoop fs <span class="hljs-attribute">-cat</span> /wordcount/output/part<span class="hljs-attribute">-r</span><span class="hljs-subst">-</span><span class="hljs-number">00000</span></code></pre>

<p><strong>参考：</strong> <br>
<a href="http://blog.csdn.net/lisonglisonglisong/article/details/47125319" rel="nofollow">http://blog.csdn.net/lisonglisonglisong/article/details/47125319</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>