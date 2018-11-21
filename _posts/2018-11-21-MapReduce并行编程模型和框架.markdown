---
layout:     post
title:      MapReduce并行编程模型和框架
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为【疯狂燃烧的岁月】的原创文章，欢迎转载，转载请注明出处！博客地址：http://blog.csdn.net/zwto1					https://blog.csdn.net/u013067629/article/details/54906775				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="传统的串行处理方式">传统的串行处理方式</h2>

<p>有四组文本数据：</p>

<blockquote>
  <p>“the weather is good”, <br>
  “today is good”, <br>
  “good weather is good”, <br>
  “today has good weather”</p>
</blockquote>

<p>对这些文本数据进行词频统计:</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">import</span> java.util.Hashtable;
<span class="hljs-keyword">import</span> java.util.Iterator;
<span class="hljs-keyword">import</span> java.util.StringTokenizer;

<span class="hljs-javadoc">/**
 * 传统的串行计算方式词频统计
 *
 *<span class="hljs-javadoctag"> @version</span> 2017年1月12日  下午4:05:33
 */</span>

<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">WordCount</span> {</span>

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) {

        String[] text = <span class="hljs-keyword">new</span> String[]{
                <span class="hljs-string">"the weather is good"</span>,<span class="hljs-string">"today is good"</span>,
                <span class="hljs-string">"good weather is good"</span>,<span class="hljs-string">"today has good weather"</span>
        };

        <span class="hljs-comment">//同步、线程安全</span>
         Hashtable ht = <span class="hljs-keyword">new</span> Hashtable();
         <span class="hljs-comment">//HashMap ht = new HashMap();</span>
        <span class="hljs-keyword">for</span>(<span class="hljs-keyword">int</span> i=<span class="hljs-number">0</span>;i&lt;=<span class="hljs-number">3</span>;i++){
             <span class="hljs-comment">//字符串根据分隔符解析</span>
            StringTokenizer st  = <span class="hljs-keyword">new</span> StringTokenizer(text[i]);

            <span class="hljs-keyword">while</span> (st.hasMoreTokens()) {

                String world = st.nextToken();

                <span class="hljs-keyword">if</span>(!ht.containsKey(world)){
                    ht.put(world, <span class="hljs-keyword">new</span> Integer(<span class="hljs-number">1</span>));
                }<span class="hljs-keyword">else</span>{
                    <span class="hljs-keyword">int</span> wc  = ((Integer)ht.get(world)).intValue()+<span class="hljs-number">1</span>;
                    ht.put(world, <span class="hljs-keyword">new</span> Integer(wc));     
                }
            }<span class="hljs-comment">//end of while </span>
        }<span class="hljs-comment">//end of for</span>

        <span class="hljs-comment">//输出统计结果</span>
        <span class="hljs-keyword">for</span>(Iterator itr = ht.keySet().iterator();itr.hasNext();){
            String world = (String) itr.next();
            System.out.println(world+<span class="hljs-string">": "</span> +(Integer)ht.get(world)+ <span class="hljs-string">"; "</span>);

        }


    }
}</code></pre>



<h2 id="一个mr分布式程序">一个MR分布式程序</h2>

<p>求出每个年份的最高气温:</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-label">MaxTemperatureMapper.Java:</span>

import java<span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.IOException</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.IntWritable</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.LongWritable</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.Text</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.mapreduce</span><span class="hljs-preprocessor">.Mapper</span><span class="hljs-comment">;</span>


public class MaxTemperatureMapper extends Mapper&lt;LongWritable, Text,Text, IntWritable&gt;
{
      @Override
    protected void map(LongWritable key, Text value, Mapper&lt;LongWritable, Text, Text, IntWritable&gt;<span class="hljs-preprocessor">.Context</span> context)
            throws IOException, InterruptedException {

           //解析字段
           String line =value<span class="hljs-preprocessor">.toString</span>()<span class="hljs-comment">;                 </span>
           try{

               String year  = line<span class="hljs-preprocessor">.substring</span>(<span class="hljs-number">0</span>,<span class="hljs-number">4</span>)<span class="hljs-comment">;</span>
               int airTemperature =Integer<span class="hljs-preprocessor">.parseInt</span>(line<span class="hljs-preprocessor">.substring</span>(<span class="hljs-number">5</span>))<span class="hljs-comment">;</span>

               context<span class="hljs-preprocessor">.write</span>(new Text(year),new IntWritable(airTemperature))<span class="hljs-comment">;</span>

           }catch(Exception e){
               System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"error in line:"</span> + line)<span class="hljs-comment">;</span>
           }
    }

}  

<span class="hljs-label">MaxTemperatureReducer.java:</span>

import java<span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.IOException</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.IntWritable</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.Text</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.mapreduce</span><span class="hljs-preprocessor">.Reducer</span><span class="hljs-comment">;</span>
<span class="hljs-comment">/**
 *  reducer 比较每年度温度最高值
 * */</span>
public class MaxTemperatureReducer extends Reducer&lt;Text, IntWritable, Text, IntWritable&gt; {


   @Override
    protected void reduce(Text key, Iterable&lt;IntWritable&gt; values,
            Reducer&lt;Text, IntWritable, Text, IntWritable&gt;<span class="hljs-preprocessor">.Context</span> context) throws IOException, InterruptedException {

            int MaxValue = Integer<span class="hljs-preprocessor">.MIN</span>_VALUE<span class="hljs-comment">;</span>

            for(IntWritable value:values){
                MaxValue = Math<span class="hljs-preprocessor">.max</span>(MaxValue, value<span class="hljs-preprocessor">.get</span>())<span class="hljs-comment">;</span>
            }

            context<span class="hljs-preprocessor">.write</span>(key , new IntWritable(MaxValue))<span class="hljs-comment">;</span>
    }
}

MaxTemperatureDriver<span class="hljs-preprocessor">.java</span>：

import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.conf</span><span class="hljs-preprocessor">.Configuration</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.conf</span><span class="hljs-preprocessor">.Configured</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.fs</span><span class="hljs-preprocessor">.Path</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.IntWritable</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.Text</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.mapreduce</span><span class="hljs-preprocessor">.Job</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.mapreduce</span><span class="hljs-preprocessor">.lib</span><span class="hljs-preprocessor">.input</span><span class="hljs-preprocessor">.FileInputFormat</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.mapreduce</span><span class="hljs-preprocessor">.lib</span><span class="hljs-preprocessor">.output</span><span class="hljs-preprocessor">.FileOutputFormat</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.Tool</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.ToolRunner</span><span class="hljs-comment">;</span>

public class MaxTemperatureDriver extends Configured implements Tool{

    @Override
    public int run(String[] args) throws Exception {
        // 对 参数进行判断：参数个数不为<span class="hljs-number">2</span>，打印错误信息 
        if (args<span class="hljs-preprocessor">.length</span> != <span class="hljs-number">2</span>){

            System<span class="hljs-preprocessor">.err</span><span class="hljs-preprocessor">.printf</span>(<span class="hljs-string">"Usage: %s &lt;input&gt;&lt;output&gt;"</span>,getClass()<span class="hljs-preprocessor">.getSimpleName</span>())<span class="hljs-comment">;</span>

            ToolRunner<span class="hljs-preprocessor">.printGenericCommandUsage</span>(System<span class="hljs-preprocessor">.err</span>)<span class="hljs-comment">;</span>

            return -<span class="hljs-number">1</span><span class="hljs-comment">;                  </span>

    }        

        Configuration conf =getConf()<span class="hljs-comment">;                </span>

        @SuppressWarnings(<span class="hljs-string">"deprecation"</span>) //不检测过期的方法
        Job job = new Job(conf)<span class="hljs-comment">;</span>

        job<span class="hljs-preprocessor">.setJobName</span>(<span class="hljs-string">"Max Temperature"</span>)<span class="hljs-comment">;                  </span>

        job<span class="hljs-preprocessor">.setJarByClass</span>(getClass())<span class="hljs-comment">;</span>

        FileInputFormat<span class="hljs-preprocessor">.addInputPath</span>(job,new Path(args[<span class="hljs-number">0</span>]))<span class="hljs-comment">;</span>

        FileOutputFormat<span class="hljs-preprocessor">.setOutputPath</span>(job,new Path(args[<span class="hljs-number">1</span>]))<span class="hljs-comment">;                  </span>

        job<span class="hljs-preprocessor">.setMapperClass</span>(MaxTemperatureMapper<span class="hljs-preprocessor">.class</span>)<span class="hljs-comment">;</span>

        job<span class="hljs-preprocessor">.setReducerClass</span>(MaxTemperatureReducer<span class="hljs-preprocessor">.class</span>)<span class="hljs-comment">;                  </span>

        job<span class="hljs-preprocessor">.setOutputKeyClass</span>(Text<span class="hljs-preprocessor">.class</span>)<span class="hljs-comment">;</span>

        job<span class="hljs-preprocessor">.setOutputValueClass</span>(IntWritable<span class="hljs-preprocessor">.class</span>)<span class="hljs-comment">;                  </span>

        return job<span class="hljs-preprocessor">.waitForCompletion</span>(true)?<span class="hljs-number">0</span>:<span class="hljs-number">1</span><span class="hljs-comment">; </span>
    }


    public static void main(String[] args)throws Exception{

        int exitcode = ToolRunner<span class="hljs-preprocessor">.run</span>(new MaxTemperatureDriver(), args)<span class="hljs-comment">;</span>

        System<span class="hljs-preprocessor">.exit</span>(exitcode)<span class="hljs-comment">;                  </span>

    }
}   </code></pre>

<p><strong>上传数据至hadoop集群：</strong></p>

<p><img src="https://img-blog.csdn.net/20170207094036507?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvend0bzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p><strong>原始数据：</strong> <br>
Temperature1：</p>

<pre><code>1990 21

1990 18

1991 21

1992 30

1990 21
</code></pre>

<p>Temperature2：</p>

<pre><code>1991 21

1990 18

1991 24

1992 30

1993 21
</code></pre>

<p>将程序打包上传至主节点某个目录下,执行</p>



<pre class="prettyprint"><code class=" hljs ruby">hadoop jar  /data/jar/maxtemperature.jar   <span class="hljs-symbol">hdfs:</span>/<span class="hljs-regexp">/192.168.75.128:9000/input</span>  <span class="hljs-symbol">hdfs:</span>/<span class="hljs-regexp">/192.168.75.128:9000/output</span><span class="hljs-regexp">/temperature</span></code></pre>

<p><strong>执行结果：</strong></p>

<p><strong>结果数据：</strong></p>

<pre><code>1990    21
1991    24
1992    30
1993    21
</code></pre>



<h2 id="完整的mapreduce编程模型">完整的MapReduce编程模型</h2>

<p>Combiner:进行中间结果数据网络传输优化的工作。Combiner程序的执行是在Map节点完成计算之后、输出结果之前。</p>

<p>Partitioner:将所有主键相同的键值对传输给同一个Reduce节点。分区的过程在Map节点输出后、传入Reduce节点之前完成的。</p>

<p>下面是针对四组数据的MapReduce完整的并行编程模型：</p>

<blockquote>
  <p>“the weather is good”, <br>
  “today is good”, <br>
  “good weather is good”, <br>
  “today has good weather”</p>
</blockquote>

<p><img src="https://img-blog.csdn.net/20170207094345622?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvend0bzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p></p><center>完整的MapReduce编程模型</center><p></p>

<p>（1）用户程序会分成三个部分：Mapper，Reducer，Driver <br>
（2）Mapper的输入数据是KV对的形式，KV的类型可以设置 <br>
（3）Mapper的输出数据是KV对的形式，KV的类型可以设置 <br>
（4）Mapper中的业务逻辑写在map方法中 <br>
（5）map方法是每进来一个KV对调用一次 <br>
（6）Reducer的输入数据应该对应Mapper的输出数据，也是KV <br>
（7）Reducer的业务逻辑写在reduce方法中 <br>
（8）reduce方法是对每一个&lt; key,valueList&gt; 调用一次 <br>
（9）用户的Mapper和Reducer都要继承各自的父类 <br>
（10）整个程序需要一个Drvier来进行提交，提交的是一个描述了各种必要信息的job对象。</p>

<h2 id="hadoop系统架构和mapreduce执行流程">Hadoop系统架构和MapReduce执行流程</h2>

<p>为了实现Hadoop系统设计中本地化计算的原则，数据存储节点DataNode与计算节点TaskTracker将合并设置，让每个从节点同时运行作为DataNode和TaskTracker，以此让每个Tasktracker尽量处理存储在本地DataNode上的数据。</p>

<p>而数据存储主控节点NameNode与作业执行主控节点JobTracker既可以设置在同一个主控节点上，在集群规模较大或者这两个主控节点负载都很高以至于互相影响时，也可以分开设置在两个不同的节点上。</p>

<p><img src="https://img-blog.csdn.net/20170207100003623?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvend0bzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p></p><center>Hadoop系统的基本组成构架</center><p></p>

<p><strong>MapReduce程序的执行流程：</strong></p>

<p>MapReduce执行一个用户提交的MapReduce程序的基本过程。</p>

<p><img src="https://img-blog.csdn.net/20170207100114660?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvend0bzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p></p><center>Hadoop MapReduce 程序执行流程</center><p></p>

<p>1）  首先，用户程序客户端通过作业客户端接口程序JobClient提交一个用户程序。 <br>
2）  然后JobClient向JobTracker提交作业执行请求并获得一个Job ID。 <br>
3）  JobClient同时也会将用户程序作业和待处理的数据文件信息准备好并存储在HDFS中。 <br>
4）  JobClient正式向JobTracker提交和执行该作业。 <br>
5）  JobTracker接受并调度该作业，进行作业的初始化准备工作，根据待处理数据的实际分片情况，调度和分配一定的Map节点来完成作业。 <br>
6）  JobTracker 查询作业中的数据分片信息，构建并准备相应的任务。 <br>
7）  JobTracker 启动TaskTracker节点开始执行具体的任务。 <br>
8）  TaskTracker根据所分配的具体任务，获取相应的作业数据。 <br>
9）  TaskTracker节点创建所需要的Java虚拟机，并启动相应的Map任务（或Reduce任务）的执行。 <br>
10） TaskTracker执行完所分配的任务之后，若是Map任务，则把中间结果数据输出到HDFS中；若是Reduce任务，则输出最终结果。 <br>
11） TaskTracker向JobTracker报告所分配的任务的完成。若是Map任务完成并且后续还有Reduce任务，则JobTracker会分配和启动Reduce节点继续处理中间结果并输出最终结果。</p>



<h2 id="参考学习资料">参考学习资料：</h2>

<p>1.HashMap和Hashtable的区别: <br>
<a href="http://www.importnew.com/7010.html" rel="nofollow">http://www.importnew.com/7010.html</a> <br>
2.StringTokenizer类的使用方法: <br>
<a href="http://yacole.iteye.com/blog/41512" rel="nofollow">http://yacole.iteye.com/blog/41512</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>