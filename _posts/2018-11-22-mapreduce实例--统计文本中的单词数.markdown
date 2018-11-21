---
layout:     post
title:      mapreduce实例--统计文本中的单词数
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/apple9005/article/details/80582845				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="mapreduce实例统计文本中的单词数">mapreduce实例–统计文本中的单词数</h1>



<h2 id="一环境描述">一：环境描述：</h2>

<p>hadoop2.8.1 <br>
文件上传至hdfs，程序从hdfs读取计算，计算结果存储到hdfs</p>



<h2 id="二前期准备">二：前期准备</h2>



<h3 id="21-上传文件wordtxt至hdfs">2.1 上传文件word.txt至hdfs</h3>

<p>word.txt 文件内容：</p>



<pre class="prettyprint"><code class="language-python hljs ">Could <span class="hljs-keyword">not</span> obtain block, Could <span class="hljs-keyword">not</span> obtain block, Could <span class="hljs-keyword">not</span> obtain block
Could <span class="hljs-keyword">not</span> obtain block
Could <span class="hljs-keyword">not</span> obtain block
Could <span class="hljs-keyword">not</span> obtain block</code></pre>

<pre class="prettyprint"><code class="language-python hljs ">&gt; hdfs dfs -put ./word.txt /user/zhangsan/</code></pre>



<h3 id="22-依赖包引入项目">2.2 依赖包引入项目：</h3>

<p>hadoop-2.8.1\share\hadoop\hdfs\hadoop-hdfs-2.8.1.jar <br>
hadoop-2.8.1\share\hadoop\hdfs\lib\所有jar包</p>

<p>hadoop-2.8.1\share\hadoop\common\hadoop-common-2.8.1.jar <br>
hadoop-2.8.1\share\hadoop\common\lib\所有jar包</p>

<p>hadoop-2.8.1\share\hadoop\mapreduce\除hadoop-mapreduce-examples-2.8.1.jar之外的jar包 <br>
hadoop-2.8.1\share\hadoop\mapreduce\lib\所有jar包</p>

<p>hadoop-2.8.1\share\hadoop\yarn\所有jar包 <br>
hadoop-2.8.1\share\hadoop\yarn\lib\所有jar包</p>



<h2 id="三编码">三：编码</h2>



<h3 id="21-map类编写">2.1 map类编写</h3>



<pre class="prettyprint"><code class="language-python hljs ">package wordcount;
<span class="hljs-keyword">import</span> java.io.IOException;

<span class="hljs-keyword">import</span> org.apache.hadoop.io.IntWritable;
<span class="hljs-keyword">import</span> org.apache.hadoop.io.LongWritable;
<span class="hljs-keyword">import</span> org.apache.hadoop.io.Text;
<span class="hljs-keyword">import</span> org.apache.hadoop.mapreduce.Mapper;
/* 
 * KEYIN：输入kv数据对中key的数据类型 
 * VALUEIN：输入kv数据对中value的数据类型 
 * KEYOUT：输出kv数据对中key的数据类型 
 * VALUEOUT：输出kv数据对中value的数据类型 
 */  
public <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">WordCountMapper</span> <span class="hljs-title">extends</span> <span class="hljs-title">Mapper</span>&lt;<span class="hljs-title">LongWritable</span>, <span class="hljs-title">Text</span>, <span class="hljs-title">Text</span>, <span class="hljs-title">IntWritable</span>&gt;{  

    /* 
     * <span class="hljs-title">map</span>方法是提供给<span class="hljs-title">map</span> <span class="hljs-title">task</span>进程来调用的，<span class="hljs-title">map</span> <span class="hljs-title">task</span>进程是每读取一行文本来调用一次我们自定义的<span class="hljs-title">map</span>方法 
     * <span class="hljs-title">map</span> <span class="hljs-title">task</span>在调用<span class="hljs-title">map</span>方法时，传递的参数： 
     *      一行的起始偏移量<span class="hljs-title">LongWritable</span>作为<span class="hljs-title">key</span> 
     *      一行的文本内容<span class="hljs-title">Text</span>作为<span class="hljs-title">value</span> 
     */  
    @<span class="hljs-title">Override</span>  
    <span class="hljs-title">protected</span> <span class="hljs-title">void</span> <span class="hljs-title">map</span><span class="hljs-params">(LongWritable key, Text value,Context context)</span> <span class="hljs-title">throws</span> <span class="hljs-title">IOException</span>, <span class="hljs-title">InterruptedException</span> {  
        //拿到一行文本内容，转换成<span class="hljs-title">String</span> 类型  
        <span class="hljs-title">String</span> <span class="hljs-title">line</span> = <span class="hljs-title">value</span>.<span class="hljs-title">toString</span><span class="hljs-params">()</span>;  
        //将这行文本切分成单词  
        <span class="hljs-title">String</span>[] <span class="hljs-title">words</span>=<span class="hljs-title">line</span>.<span class="hljs-title">split</span><span class="hljs-params">(<span class="hljs-string">" "</span>)</span>;  

        //输出&lt;单词，1&gt;  
        <span class="hljs-title">for</span><span class="hljs-params">(String word:words)</span>{  
            <span class="hljs-title">context</span>.<span class="hljs-title">write</span><span class="hljs-params">(new Text<span class="hljs-params">(word)</span>, new IntWritable<span class="hljs-params">(<span class="hljs-number">1</span>)</span>)</span>;  
        }  
    }  
}  </span></code></pre>



<h3 id="22-reduce类编写">2.2 reduce类编写</h3>



<pre class="prettyprint"><code class="language-python hljs ">package wordcount;

<span class="hljs-keyword">import</span> java.io.IOException;

<span class="hljs-keyword">import</span> org.apache.hadoop.io.IntWritable;
<span class="hljs-keyword">import</span> org.apache.hadoop.mapreduce.Reducer;
<span class="hljs-keyword">import</span> org.apache.hadoop.io.Text;

/* 
 * KEYIN：对应mapper阶段输出的key类型 
 * VALUEIN：对应mapper阶段输出的value类型 
 * KEYOUT：reduce处理完之后输出的结果kv对中key的类型 
 * VALUEOUT：reduce处理完之后输出的结果kv对中value的类型 
 */  
public <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">WordCountReducer</span> <span class="hljs-title">extends</span> <span class="hljs-title">Reducer</span>&lt;<span class="hljs-title">Text</span>, <span class="hljs-title">IntWritable</span>, <span class="hljs-title">Text</span>, <span class="hljs-title">IntWritable</span>&gt;{  
    @<span class="hljs-title">Override</span>  
    /* 
     * <span class="hljs-title">reduce</span>方法提供给<span class="hljs-title">reduce</span> <span class="hljs-title">task</span>进程来调用 
     *  
     * <span class="hljs-title">reduce</span> <span class="hljs-title">task</span>会将<span class="hljs-title">shuffle</span>阶段分发过来的大量<span class="hljs-title">kv</span>数据对进行聚合，聚合的机制是相同<span class="hljs-title">key</span>的<span class="hljs-title">kv</span>对聚合为一组 
     * 然后<span class="hljs-title">reduce</span> <span class="hljs-title">task</span>对每一组聚合<span class="hljs-title">kv</span>调用一次我们自定义的<span class="hljs-title">reduce</span>方法 
     * 比如：&lt;<span class="hljs-title">hello</span>,1&gt;&lt;<span class="hljs-title">hello</span>,1&gt;&lt;<span class="hljs-title">hello</span>,1&gt;&lt;<span class="hljs-title">tom</span>,1&gt;&lt;<span class="hljs-title">tom</span>,1&gt;&lt;<span class="hljs-title">tom</span>,1&gt; 
     *  <span class="hljs-title">hello</span>组会调用一次<span class="hljs-title">reduce</span>方法进行处理，<span class="hljs-title">tom</span>组也会调用一次<span class="hljs-title">reduce</span>方法进行处理 
     *  调用时传递的参数： 
     *          <span class="hljs-title">key</span>：一组<span class="hljs-title">kv</span>中的<span class="hljs-title">key</span> 
     *          <span class="hljs-title">values</span>：一组<span class="hljs-title">kv</span>中所有<span class="hljs-title">value</span>的迭代器 
     */  
    <span class="hljs-title">protected</span> <span class="hljs-title">void</span> <span class="hljs-title">reduce</span><span class="hljs-params">(Text key, Iterable&lt;IntWritable&gt; values,Context context)</span> <span class="hljs-title">throws</span> <span class="hljs-title">IOException</span>, <span class="hljs-title">InterruptedException</span> {  
        //定义一个计数器  
        <span class="hljs-title">int</span> <span class="hljs-title">count</span> = 0;  
        //通过<span class="hljs-title">value</span>这个迭代器，遍历这一组<span class="hljs-title">kv</span>中所有的<span class="hljs-title">value</span>，进行累加  
        <span class="hljs-title">for</span><span class="hljs-params">(IntWritable value:values)</span>{  
            <span class="hljs-title">count</span>+=<span class="hljs-title">value</span>.<span class="hljs-title">get</span><span class="hljs-params">()</span>;  
        }  

        //输出这个单词的统计结果  
        <span class="hljs-title">context</span>.<span class="hljs-title">write</span><span class="hljs-params">(key, new IntWritable<span class="hljs-params">(count)</span>)</span>;  
    }  
}  </span></code></pre>



<h3 id="23-job类编写">2.3 job类编写</h3>



<pre class="prettyprint"><code class="language-python hljs ">package wordcount;

<span class="hljs-keyword">import</span> java.io.IOException;

<span class="hljs-keyword">import</span> org.apache.hadoop.conf.Configuration;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.Path;
<span class="hljs-keyword">import</span> org.apache.hadoop.io.IntWritable;
<span class="hljs-keyword">import</span> org.apache.hadoop.io.Text;
<span class="hljs-keyword">import</span> org.apache.hadoop.mapreduce.Job;
<span class="hljs-keyword">import</span> org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
<span class="hljs-keyword">import</span> org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;


public <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">WordCountJobSubmitter</span> {  

    <span class="hljs-title">public</span> <span class="hljs-title">static</span> <span class="hljs-title">void</span> <span class="hljs-title">main</span><span class="hljs-params">(String[] args)</span> <span class="hljs-title">throws</span> <span class="hljs-title">IOException</span>, <span class="hljs-title">ClassNotFoundException</span>, <span class="hljs-title">InterruptedException</span> {  
        <span class="hljs-title">Configuration</span> <span class="hljs-title">conf</span> = <span class="hljs-title">new</span> <span class="hljs-title">Configuration</span><span class="hljs-params">()</span>;  
        <span class="hljs-title">Job</span> <span class="hljs-title">wordCountJob</span> = <span class="hljs-title">Job</span>.<span class="hljs-title">getInstance</span><span class="hljs-params">(conf)</span>;  

        //重要：指定本<span class="hljs-title">job</span>所在的<span class="hljs-title">jar</span>包  
        <span class="hljs-title">wordCountJob</span>.<span class="hljs-title">setJarByClass</span><span class="hljs-params">(WordCountJobSubmitter.class)</span>;  

        //设置<span class="hljs-title">wordCountJob</span>所用的<span class="hljs-title">mapper</span>逻辑类为哪个类  
        <span class="hljs-title">wordCountJob</span>.<span class="hljs-title">setMapperClass</span><span class="hljs-params">(WordCountMapper.class)</span>;  
        //设置<span class="hljs-title">wordCountJob</span>所用的<span class="hljs-title">reducer</span>逻辑类为哪个类  
        <span class="hljs-title">wordCountJob</span>.<span class="hljs-title">setReducerClass</span><span class="hljs-params">(WordCountReducer.class)</span>;  

        //设置<span class="hljs-title">map</span>阶段输出的<span class="hljs-title">kv</span>数据类型  
        <span class="hljs-title">wordCountJob</span>.<span class="hljs-title">setMapOutputKeyClass</span><span class="hljs-params">(Text.class)</span>;  
        <span class="hljs-title">wordCountJob</span>.<span class="hljs-title">setMapOutputValueClass</span><span class="hljs-params">(IntWritable.class)</span>;  

        //设置最终输出的<span class="hljs-title">kv</span>数据类型  
        <span class="hljs-title">wordCountJob</span>.<span class="hljs-title">setOutputKeyClass</span><span class="hljs-params">(Text.class)</span>;  
        <span class="hljs-title">wordCountJob</span>.<span class="hljs-title">setOutputValueClass</span><span class="hljs-params">(IntWritable.class)</span>;  

        //设置要处理的文本数据所存放的路径  
        <span class="hljs-title">FileInputFormat</span>.<span class="hljs-title">setInputPaths</span><span class="hljs-params">(wordCountJob, <span class="hljs-string">"hdfs://172.16.29.11:9000/user/zhangsan/word.txt"</span>)</span>;  
        <span class="hljs-title">FileOutputFormat</span>.<span class="hljs-title">setOutputPath</span><span class="hljs-params">(wordCountJob, new Path<span class="hljs-params">(<span class="hljs-string">"hdfs://172.16.29.11:9000/output/"</span>)</span>)</span>;  

        //提交<span class="hljs-title">job</span>给<span class="hljs-title">hadoop</span>集群  
        <span class="hljs-title">wordCountJob</span>.<span class="hljs-title">waitForCompletion</span><span class="hljs-params">(true)</span>;  
    }  
}  </span></code></pre>



<h3 id="24-eclipse-调试运行">2.4 eclipse 调试运行</h3>

<p>略</p>

<h3 id="25-打包运行">2.5 打包运行</h3>

<p>打包成mapreduce.jar，并运行</p>

<pre class="prettyprint"><code class="language-python hljs ">&gt; hadoop jar mapreduce.jar</code></pre>

<p>查看yarn： <a href="http://172.16.29.11:8088/cluster/apps" rel="nofollow">http://172.16.29.11:8088/cluster/apps</a>  执行成功。</p>

<blockquote>
  <p>注意 <br>
  output目录不能存在；如果已存在，执行此命令会报错：Output directory hdfs://172.16.29.11:9000/output already exists</p>
</blockquote>

<p>查看输出文件的内容：</p>



<pre class="prettyprint"><code class="language-python hljs ">&gt; hdfs dfs -ls /output/  <span class="hljs-comment"># 列出文件</span>
&gt; hdfs dfs -cat /output/part-r-<span class="hljs-number">00000</span>  <span class="hljs-comment"># 查看此文件内容</span>
&gt; 
Could   <span class="hljs-number">6</span>
block   <span class="hljs-number">4</span>
block,  <span class="hljs-number">2</span>
<span class="hljs-keyword">not</span>     <span class="hljs-number">6</span>
obtain  <span class="hljs-number">6</span></code></pre>

<p>参考博文：<a href="https://blog.csdn.net/litianxiang_kaola/article/details/71154302" rel="nofollow">https://blog.csdn.net/litianxiang_kaola/article/details/71154302</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>