---
layout:     post
title:      Hadop经典例子WordCount
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h6 id="1配置eclipse的hadoop插件">1.配置eclipse的hadoop插件</h6>

<p>（1）使用SCP上传eclipse-SDK-4.2.1-linux-gtk-x86_64.tar.gz和hadoop-eclipse-plugin-2.5.1.jar到CentOS中。 <br>
<img src="https://img-blog.csdn.net/20180425185320426?/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZwcXR4em16ZXplcWpqOTk3Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
（2）在CentOS中使用hadoop创建新文件夹： <br>
注意在执行hadoop命令之前要先启动hadoop</p>



<pre class="prettyprint"><code class="language-cmd hljs bash"><span class="hljs-built_in">cd</span> /home/
hadoop fs -mkdir /data01</code></pre>

<p><img src="https://img-blog.csdn.net/20180425185643546?/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZwcXR4em16ZXplcWpqOTk3Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
（3）使用cat和put命令： <br>
<img src="https://img-blog.csdn.net/20180425185730708?/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZwcXR4em16ZXplcWpqOTk3Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
（4）使用mv命令移动文件到/home/gznc-hadoop下:</p>



<pre class="prettyprint"><code class=" hljs lasso">mv /home/gznc<span class="hljs-attribute">-hadoop</span>/Desktop<span class="hljs-subst">/</span> eclipse<span class="hljs-attribute">-SDK</span><span class="hljs-subst">-</span><span class="hljs-number">4.2</span><span class="hljs-number">.1</span><span class="hljs-attribute">-linux</span><span class="hljs-attribute">-gtk</span><span class="hljs-attribute">-x86_64</span><span class="hljs-built_in">.</span>tar<span class="hljs-built_in">.</span>gz /home/gznc<span class="hljs-attribute">-hadoop</span><span class="hljs-subst">/</span></code></pre>

<p>（5）解压：tar –zxvf eclipse-SDK-4.2.1-linux-gtk-x86_64.tar.gz <br>
（6）拷贝hadoop-eclipse-plugin-2.5.1.jar到/plugins/</p>



<pre class="prettyprint"><code class=" hljs lasso">cp /home/gznc<span class="hljs-attribute">-hadoop</span>/hadoop<span class="hljs-attribute">-eclipse</span><span class="hljs-attribute">-plugin</span><span class="hljs-number">.2</span><span class="hljs-number">.5</span><span class="hljs-number">.1</span><span class="hljs-built_in">.</span>jar /home/gznc<span class="hljs-attribute">-hadoop</span>/eclipse/plugins</code></pre>

<p>（7）启动eclipse： ./eclipse &amp; <br>
<img src="https://img-blog.csdn.net/20180425185925338?/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZwcXR4em16ZXplcWpqOTk3Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
（8）在eclipse中加载hadoop：windows-&gt;preferences-&gt;Hadoop Map/Reduce-&gt;选择hadoop的根目录 <br>
<img src="https://img-blog.csdn.net/2018042519000186?/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZwcXR4em16ZXplcWpqOTk3Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
（9）创建一个Map/Reduce 项目： <br>
<img src="https://img-blog.csdn.net/20180425190037631?/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZwcXR4em16ZXplcWpqOTk3Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
（10）机构图如下： <br>
<img src="https://img-blog.csdn.net/2018042522565181?/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZwcXR4em16ZXplcWpqOTk3Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
WordCountJob类如下：</p>



<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-keyword">package</span> con.gznc.hadoop;

<span class="hljs-keyword">import</span> java.io.IOException;

<span class="hljs-keyword">import</span> org.apache.hadoop.conf.Configuration;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.Path;  
<span class="hljs-keyword">import</span> org.apache.hadoop.io.*;  
<span class="hljs-keyword">import</span> org.apache.hadoop.mapreduce.*;  
<span class="hljs-keyword">import</span> org.apache.hadoop.mapreduce.lib.input.FileInputFormat;  
<span class="hljs-keyword">import</span> org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;  
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">WordCountJob</span> {</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) <span class="hljs-keyword">throws</span> IOException, ClassNotFoundException, InterruptedException {  
        Configuration conf = <span class="hljs-keyword">new</span> Configuration();  
        Job wordCountJob = Job.getInstance(conf);  

        <span class="hljs-comment">//重要：指定本job所在的jar包  </span>
        wordCountJob.setJarByClass(WordCountJob.class);  

        <span class="hljs-comment">//设置wordCountJob所用的mapper逻辑类为哪个类  </span>
        wordCountJob.setMapperClass(WordCountMapper.class);  
        <span class="hljs-comment">//设置wordCountJob所用的reducer逻辑类为哪个类  </span>
        wordCountJob.setReducerClass(WordCountReducer.class);  

        <span class="hljs-comment">//设置map阶段输出的kv数据类型  </span>
        wordCountJob.setMapOutputKeyClass(Text.class);  
        wordCountJob.setMapOutputValueClass(IntWritable.class);  

        <span class="hljs-comment">//设置最终输出的kv数据类型  </span>
        wordCountJob.setOutputKeyClass(Text.class);  
        wordCountJob.setOutputValueClass(IntWritable.class);  

        <span class="hljs-comment">//设置要处理的文本数据所存放的路径  </span>
        FileInputFormat.setInputPaths(wordCountJob, args[<span class="hljs-number">0</span>]);  
        FileOutputFormat.setOutputPath(wordCountJob, <span class="hljs-keyword">new</span> Path(args[<span class="hljs-number">1</span>]));  

        <span class="hljs-comment">//提交job给hadoop集群  </span>
        wordCountJob.waitForCompletion(<span class="hljs-keyword">true</span>);  
    }  
}
</code></pre>

<p>WordCountMapper类如下：</p>



<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-keyword">package</span> con.gznc.hadoop;

<span class="hljs-keyword">import</span> java.io.IOException;

<span class="hljs-keyword">import</span> org.apache.hadoop.io.IntWritable;
<span class="hljs-keyword">import</span> org.apache.hadoop.io.LongWritable;
<span class="hljs-keyword">import</span> org.apache.hadoop.io.Text;
<span class="hljs-keyword">import</span> org.apache.hadoop.mapreduce.Mapper;




<span class="hljs-comment">/* 
 * KEYIN：输入kv数据对中key的数据类型 
 * VALUEIN：输入kv数据对中value的数据类型 
 * KEYOUT：输出kv数据对中key的数据类型 
 * VALUEOUT：输出kv数据对中value的数据类型 
 */</span>  
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">WordCountMapper</span> <span class="hljs-keyword">extends</span> <span class="hljs-title">Mapper</span>&lt;<span class="hljs-title">LongWritable</span>, <span class="hljs-title">Text</span>, <span class="hljs-title">Text</span>, <span class="hljs-title">IntWritable</span>&gt;{</span>

    <span class="hljs-comment">/* 
     * map方法是提供给map task进程来调用的，map task进程是每读取一行文本来调用一次我们自定义的map方法 
     * map task在调用map方法时，传递的参数： 
     *      一行的起始偏移量LongWritable作为key 
     *      一行的文本内容Text作为value 
     */</span>  
    <span class="hljs-annotation">@Override</span>  
    <span class="hljs-keyword">protected</span> <span class="hljs-keyword">void</span> <span class="hljs-title">map</span>(LongWritable key, Text value,Context context) <span class="hljs-keyword">throws</span> IOException, InterruptedException {  
        <span class="hljs-comment">//拿到一行文本内容，转换成String 类型  </span>
        String line = value.toString();  
        <span class="hljs-comment">//将这行文本切分成单词  </span>
        String[] words=line.split(<span class="hljs-string">" "</span>);  

        <span class="hljs-comment">//输出&lt;单词，1&gt;  </span>
        <span class="hljs-keyword">for</span>(String word:words){  
            context.write(<span class="hljs-keyword">new</span> Text(word), <span class="hljs-keyword">new</span> IntWritable(<span class="hljs-number">1</span>));  
        }  
    }  
}
</code></pre>

<p>WordCountReducer类如下：</p>



<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-keyword">package</span> con.gznc.hadoop;

<span class="hljs-keyword">import</span> java.io.IOException;

<span class="hljs-keyword">import</span> org.apache.hadoop.io.IntWritable;
<span class="hljs-keyword">import</span> org.apache.hadoop.io.Text;
<span class="hljs-keyword">import</span> org.apache.hadoop.mapreduce.Reducer;

<span class="hljs-comment">/* 
 * KEYIN：对应mapper阶段输出的key类型 
 * VALUEIN：对应mapper阶段输出的value类型 
 * KEYOUT：reduce处理完之后输出的结果kv对中key的类型 
 * VALUEOUT：reduce处理完之后输出的结果kv对中value的类型 
 */</span>  
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">WordCountReducer</span>  <span class="hljs-keyword">extends</span> <span class="hljs-title">Reducer</span>&lt;<span class="hljs-title">Text</span>, <span class="hljs-title">IntWritable</span>, <span class="hljs-title">Text</span>, <span class="hljs-title">IntWritable</span>&gt;{</span>
    <span class="hljs-annotation">@Override</span>  
    <span class="hljs-comment">/* 
     * reduce方法提供给reduce task进程来调用 
     *  
     * reduce task会将shuffle阶段分发过来的大量kv数据对进行聚合，聚合的机制是相同key的kv对聚合为一组 
     * 然后reduce task对每一组聚合kv调用一次我们自定义的reduce方法 
     * 比如：&lt;hello,1&gt;&lt;hello,1&gt;&lt;hello,1&gt;&lt;tom,1&gt;&lt;tom,1&gt;&lt;tom,1&gt; 
     *  hello组会调用一次reduce方法进行处理，tom组也会调用一次reduce方法进行处理 
     *  调用时传递的参数： 
     *          key：一组kv中的key 
     *          values：一组kv中所有value的迭代器 
     */</span>  
    <span class="hljs-keyword">protected</span> <span class="hljs-keyword">void</span> <span class="hljs-title">reduce</span>(Text key, Iterable&lt;IntWritable&gt; values,Context context) <span class="hljs-keyword">throws</span> IOException, InterruptedException {  
        <span class="hljs-comment">//定义一个计数器  </span>
        <span class="hljs-keyword">int</span> count = <span class="hljs-number">0</span>;  
        <span class="hljs-comment">//通过value这个迭代器，遍历这一组kv中所有的value，进行累加  </span>
        <span class="hljs-keyword">for</span>(IntWritable value:values){  
            count+=value.get();  
        }  

        <span class="hljs-comment">//输出这个单词的统计结果  </span>
        context.write(key, <span class="hljs-keyword">new</span> IntWritable(count));  
    }  
}
</code></pre>

<p>（11）代码编写完成过后导出变为jar包： <br>
<img src="https://img-blog.csdn.net/2018042522593161?/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZwcXR4em16ZXplcWpqOTk3Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
选择导出项目和路径，路径为/home/hadoop/myjar ，今后的jar包都放这，方便管理 <br>
<img src="https://img-blog.csdn.net/20180425230102732?/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZwcXR4em16ZXplcWpqOTk3Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>（12）准备要进行计算的word.txt文本： <br>
在/home/hadoop/下面新建一个文件夹test_data用来存放以后需要计算的数据 <br>
然后新建一个word.txt文档，里面可以随便放一些英文测试数据，用hadoop来数里面的单词数。可以用vim修改txt文档。</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">mkdir test_data
cd test_data/
touch <span class="hljs-built_in">word</span>.txt
vim <span class="hljs-built_in">word</span>.txt</code></pre>

<p>（13）将要计算的数据上传到hadoop： <br>
先用hadoop命令新建两个文件夹，用来存放，然后使用input上传</p>



<pre class="prettyprint"><code class=" hljs lasso">hadoop fs <span class="hljs-attribute">-mkdir</span> /user/hadoop

hadoop fs <span class="hljs-attribute">-mkdir</span> /user/hadoop/input
hadoop fs <span class="hljs-attribute">-put</span> <span class="hljs-built_in">.</span>/test_data/word<span class="hljs-built_in">.</span>txt /user/hadoop/input<span class="hljs-subst">/</span></code></pre>

<p>（14）运行 <br>
使用jar包运行：</p>



<pre class="prettyprint"><code class=" hljs avrasm">hadoop jar ./myjar/HadoopCountWord<span class="hljs-preprocessor">.jar</span> con<span class="hljs-preprocessor">.gznc</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.WordCountJob</span> /user/hadoop/input/word<span class="hljs-preprocessor">.txt</span> /user/hadoop/output/
</code></pre>

<p>（15）查看计算结果文件</p>



<pre class="prettyprint"><code class=" hljs lasso">hadoop fs <span class="hljs-attribute">-cat</span> /user/hadoop/output/part<span class="hljs-attribute">-r</span><span class="hljs-subst">-</span><span class="hljs-number">00000</span></code></pre>

<p>（16）常见报错信息解决办法 <br>
报错信息如下：</p>



<pre class="prettyprint"><code class=" hljs avrasm">Exception <span class="hljs-keyword">in</span> thread <span class="hljs-string">"main"</span> org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.ipc</span><span class="hljs-preprocessor">.RemoteException</span>(org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.server</span><span class="hljs-preprocessor">.namenode</span><span class="hljs-preprocessor">.SafeModeException</span>): Cannot delete /tmp/hadoop-yarn/staging/root/<span class="hljs-preprocessor">.staging</span>/job_1524727489800_0001. Name node is <span class="hljs-keyword">in</span> safe mode.
The reported blocks <span class="hljs-number">0</span> needs additional <span class="hljs-number">33</span> blocks to reach the threshold <span class="hljs-number">0.9990</span> of total blocks <span class="hljs-number">33.</span>
The number of live datanodes <span class="hljs-number">0</span> has reached the minimum number <span class="hljs-number">0.</span> Safe mode will be turned off automatically once the thresholds have been reached.
    at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.server</span><span class="hljs-preprocessor">.namenode</span><span class="hljs-preprocessor">.FSNamesystem</span><span class="hljs-preprocessor">.checkNameNodeSafeMode</span>(FSNamesystem<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">1272</span>)
    at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.server</span><span class="hljs-preprocessor">.namenode</span><span class="hljs-preprocessor">.FSNamesystem</span><span class="hljs-preprocessor">.deleteInternal</span>(FSNamesystem<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">3521</span>)
    at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.server</span><span class="hljs-preprocessor">.namenode</span><span class="hljs-preprocessor">.FSNamesystem</span><span class="hljs-preprocessor">.deleteInt</span>(FSNamesystem<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">3479</span>)
    at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.server</span><span class="hljs-preprocessor">.namenode</span><span class="hljs-preprocessor">.FSNamesystem</span><span class="hljs-preprocessor">.delete</span>(FSNamesystem<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">3463</span>)
    at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.server</span><span class="hljs-preprocessor">.namenode</span><span class="hljs-preprocessor">.NameNodeRpcServer</span><span class="hljs-preprocessor">.delete</span>(NameNodeRpcServer<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">751</span>)
    at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.protocolPB</span><span class="hljs-preprocessor">.ClientNamenodeProtocolServerSideTranslatorPB</span><span class="hljs-preprocessor">.delete</span>(ClientNamenodeProtocolServerSideTranslatorPB<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">562</span>)
    at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.protocol</span><span class="hljs-preprocessor">.proto</span><span class="hljs-preprocessor">.ClientNamenodeProtocolProtos</span>$ClientNamenodeProtocol$2<span class="hljs-preprocessor">.callBlockingMethod</span>(ClientNamenodeProtocolProtos<span class="hljs-preprocessor">.java</span>)
    at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.ipc</span><span class="hljs-preprocessor">.ProtobufRpcEngine</span>$Server$ProtoBufRpcInvoker<span class="hljs-preprocessor">.call</span>(ProtobufRpcEngine<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">585</span>)
    at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.ipc</span><span class="hljs-preprocessor">.RPC</span>$Server<span class="hljs-preprocessor">.call</span>(RPC<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">928</span>)
    at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.ipc</span><span class="hljs-preprocessor">.Server</span>$Handler$1<span class="hljs-preprocessor">.run</span>(Server<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">2013</span>)
    at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.ipc</span><span class="hljs-preprocessor">.Server</span>$Handler$1<span class="hljs-preprocessor">.run</span>(Server<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">2009</span>)
    at java<span class="hljs-preprocessor">.security</span><span class="hljs-preprocessor">.AccessController</span><span class="hljs-preprocessor">.doPrivileged</span>(Native Method)
    at javax<span class="hljs-preprocessor">.security</span><span class="hljs-preprocessor">.auth</span><span class="hljs-preprocessor">.Subject</span><span class="hljs-preprocessor">.doAs</span>(Subject<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">415</span>)
    at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.security</span><span class="hljs-preprocessor">.UserGroupInformation</span><span class="hljs-preprocessor">.doAs</span>(UserGroupInformation<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">1614</span>)
    at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.ipc</span><span class="hljs-preprocessor">.Server</span>$Handler<span class="hljs-preprocessor">.run</span>(Server<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">2007</span>)

    at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.ipc</span><span class="hljs-preprocessor">.Client</span><span class="hljs-preprocessor">.call</span>(Client<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">1411</span>)
    at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.ipc</span><span class="hljs-preprocessor">.Client</span><span class="hljs-preprocessor">.call</span>(Client<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">1364</span>)
    at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.ipc</span><span class="hljs-preprocessor">.ProtobufRpcEngine</span>$Invoker<span class="hljs-preprocessor">.invoke</span>(ProtobufRpcEngine<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">206</span>)
    at <span class="hljs-keyword">com</span><span class="hljs-preprocessor">.sun</span><span class="hljs-preprocessor">.proxy</span>.$Proxy9<span class="hljs-preprocessor">.delete</span>(Unknown Source)
    at sun<span class="hljs-preprocessor">.reflect</span><span class="hljs-preprocessor">.NativeMethodAccessorImpl</span><span class="hljs-preprocessor">.invoke</span>0(Native Method)
    at sun<span class="hljs-preprocessor">.reflect</span><span class="hljs-preprocessor">.NativeMethodAccessorImpl</span><span class="hljs-preprocessor">.invoke</span>(NativeMethodAccessorImpl<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">57</span>)
    at sun<span class="hljs-preprocessor">.reflect</span><span class="hljs-preprocessor">.DelegatingMethodAccessorImpl</span><span class="hljs-preprocessor">.invoke</span>(DelegatingMethodAccessorImpl<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">43</span>)
    at java<span class="hljs-preprocessor">.lang</span><span class="hljs-preprocessor">.reflect</span><span class="hljs-preprocessor">.Method</span><span class="hljs-preprocessor">.invoke</span>(Method<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">606</span>)
    at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.retry</span><span class="hljs-preprocessor">.RetryInvocationHandler</span><span class="hljs-preprocessor">.invokeMethod</span>(RetryInvocationHandler<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">187</span>)
    at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.retry</span><span class="hljs-preprocessor">.RetryInvocationHandler</span><span class="hljs-preprocessor">.invoke</span>(RetryInvocationHandler<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">102</span>)
    at <span class="hljs-keyword">com</span><span class="hljs-preprocessor">.sun</span><span class="hljs-preprocessor">.proxy</span>.$Proxy9<span class="hljs-preprocessor">.delete</span>(Unknown Source)
    at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.protocolPB</span><span class="hljs-preprocessor">.ClientNamenodeProtocolTranslatorPB</span><span class="hljs-preprocessor">.delete</span>(ClientNamenodeProtocolTranslatorPB<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">490</span>)
    at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.DFSClient</span><span class="hljs-preprocessor">.delete</span>(DFSClient<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">1726</span>)
    at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.DistributedFileSystem</span>$11<span class="hljs-preprocessor">.doCall</span>(DistributedFileSystem<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">588</span>)
    at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.DistributedFileSystem</span>$11<span class="hljs-preprocessor">.doCall</span>(DistributedFileSystem<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">584</span>)
    at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.fs</span><span class="hljs-preprocessor">.FileSystemLinkResolver</span><span class="hljs-preprocessor">.resolve</span>(FileSystemLinkResolver<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">81</span>)
    at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.DistributedFileSystem</span><span class="hljs-preprocessor">.delete</span>(DistributedFileSystem<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">584</span>)
    at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.mapreduce</span><span class="hljs-preprocessor">.JobSubmitter</span><span class="hljs-preprocessor">.submitJobInternal</span>(JobSubmitter<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">443</span>)
    at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.mapreduce</span><span class="hljs-preprocessor">.Job</span>$10<span class="hljs-preprocessor">.run</span>(Job<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">1285</span>)
    at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.mapreduce</span><span class="hljs-preprocessor">.Job</span>$10<span class="hljs-preprocessor">.run</span>(Job<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">1282</span>)
    at java<span class="hljs-preprocessor">.security</span><span class="hljs-preprocessor">.AccessController</span><span class="hljs-preprocessor">.doPrivileged</span>(Native Method)
    at javax<span class="hljs-preprocessor">.security</span><span class="hljs-preprocessor">.auth</span><span class="hljs-preprocessor">.Subject</span><span class="hljs-preprocessor">.doAs</span>(Subject<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">415</span>)
    at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.security</span><span class="hljs-preprocessor">.UserGroupInformation</span><span class="hljs-preprocessor">.doAs</span>(UserGroupInformation<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">1614</span>)
    at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.mapreduce</span><span class="hljs-preprocessor">.Job</span><span class="hljs-preprocessor">.submit</span>(Job<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">1282</span>)
    at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.mapreduce</span><span class="hljs-preprocessor">.Job</span><span class="hljs-preprocessor">.waitForCompletion</span>(Job<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">1303</span>)
    at con<span class="hljs-preprocessor">.gznc</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.WordCountJob</span><span class="hljs-preprocessor">.main</span>(WordCountJob<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">37</span>)
    at sun<span class="hljs-preprocessor">.reflect</span><span class="hljs-preprocessor">.NativeMethodAccessorImpl</span><span class="hljs-preprocessor">.invoke</span>0(Native Method)
    at sun<span class="hljs-preprocessor">.reflect</span><span class="hljs-preprocessor">.NativeMethodAccessorImpl</span><span class="hljs-preprocessor">.invoke</span>(NativeMethodAccessorImpl<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">57</span>)
    at sun<span class="hljs-preprocessor">.reflect</span><span class="hljs-preprocessor">.DelegatingMethodAccessorImpl</span><span class="hljs-preprocessor">.invoke</span>(DelegatingMethodAccessorImpl<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">43</span>)
    at java<span class="hljs-preprocessor">.lang</span><span class="hljs-preprocessor">.reflect</span><span class="hljs-preprocessor">.Method</span><span class="hljs-preprocessor">.invoke</span>(Method<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">606</span>)
    at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.RunJar</span><span class="hljs-preprocessor">.main</span>(RunJar<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">212</span>)</code></pre>

<p>解决办法： <br>
在hadoop根目录输入如下命令：</p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@master</span> hadoop-<span class="hljs-number">2.5</span>.<span class="hljs-number">1</span>]<span class="hljs-comment"># bin/hadoop dfsadmin -safemode leave</span></code></pre>

<p><img src="https://img-blog.csdn.net/20180426003617833?/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZwcXR4em16ZXplcWpqOTk3Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
然后再运行计算命令即可。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>