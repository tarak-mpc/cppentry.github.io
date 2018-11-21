---
layout:     post
title:      HADOOP -hdfs of wordcount
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<pre class="prettyprint"><code class=" hljs avrasm">package <span class="hljs-keyword">com</span><span class="hljs-preprocessor">.younglibin</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.test</span><span class="hljs-comment">;</span>

import java<span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.BufferedReader</span><span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.FileOutputStream</span><span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.FileReader</span><span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.IOException</span><span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.Iterator</span><span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.StringTokenizer</span><span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.net</span><span class="hljs-preprocessor">.URI</span><span class="hljs-comment">;</span>

import net<span class="hljs-preprocessor">.iharder</span><span class="hljs-preprocessor">.base</span>64<span class="hljs-preprocessor">.Base</span>64<span class="hljs-preprocessor">.InputStream</span><span class="hljs-comment">;</span>

import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.conf</span><span class="hljs-preprocessor">.Configuration</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.fs</span><span class="hljs-preprocessor">.FSDataInputStream</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.fs</span><span class="hljs-preprocessor">.FSDataOutputStream</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.fs</span><span class="hljs-preprocessor">.FileSystem</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.fs</span><span class="hljs-preprocessor">.Path</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.IOUtils</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.IntWritable</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.LongWritable</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.Text</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.mapred</span><span class="hljs-preprocessor">.FileInputFormat</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.mapred</span><span class="hljs-preprocessor">.FileOutputFormat</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.mapred</span><span class="hljs-preprocessor">.JobClient</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.mapred</span><span class="hljs-preprocessor">.JobConf</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.mapred</span><span class="hljs-preprocessor">.MapReduceBase</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.mapred</span><span class="hljs-preprocessor">.Mapper</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.mapred</span><span class="hljs-preprocessor">.OutputCollector</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.mapred</span><span class="hljs-preprocessor">.Reducer</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.mapred</span><span class="hljs-preprocessor">.Reporter</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.mapred</span><span class="hljs-preprocessor">.TextInputFormat</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.mapred</span><span class="hljs-preprocessor">.TextOutputFormat</span><span class="hljs-comment">;</span>



public class WordCount {

    public static class Map extends MapReduceBase implements
            Mapper&lt;LongWritable, Text, Text, IntWritable&gt; {
        private final static IntWritable one = new IntWritable(<span class="hljs-number">1</span>)<span class="hljs-comment">;</span>
        private Text word = new Text()<span class="hljs-comment">;</span>

        public void map(LongWritable key, Text value,
                OutputCollector&lt;Text, IntWritable&gt; output, Reporter reporter)
                throws IOException {
            String line = value<span class="hljs-preprocessor">.toString</span>()<span class="hljs-comment">;</span>
            StringTokenizer tokenizer = new StringTokenizer(line)<span class="hljs-comment">;</span>
            while (tokenizer<span class="hljs-preprocessor">.hasMoreTokens</span>()) {
                word<span class="hljs-preprocessor">.set</span>(tokenizer<span class="hljs-preprocessor">.nextToken</span>())<span class="hljs-comment">;</span>
                output<span class="hljs-preprocessor">.collect</span>(word, one)<span class="hljs-comment">;</span>
            }
        }
    }

    public static class Reduce extends MapReduceBase implements
            Reducer&lt;Text, IntWritable, Text, IntWritable&gt; {
        public void reduce(Text key, Iterator&lt;IntWritable&gt; values,
                OutputCollector&lt;Text, IntWritable&gt; output, Reporter reporter)
                throws IOException {
            int sum = <span class="hljs-number">0</span><span class="hljs-comment">;</span>
            while (values<span class="hljs-preprocessor">.hasNext</span>()) {
                sum += values<span class="hljs-preprocessor">.next</span>()<span class="hljs-preprocessor">.get</span>()<span class="hljs-comment">;</span>
            }
            output<span class="hljs-preprocessor">.collect</span>(key, new IntWritable(sum))<span class="hljs-comment">;</span>
        }
    }

    public static void main(String[] args) throws Exception {
        try{
        JobConf conf = new JobConf(WordCount<span class="hljs-preprocessor">.class</span>)<span class="hljs-comment">;</span>
        conf<span class="hljs-preprocessor">.setJobName</span>(<span class="hljs-string">"wordcount"</span>)<span class="hljs-comment">;</span>

        conf<span class="hljs-preprocessor">.setOutputKeyClass</span>(Text<span class="hljs-preprocessor">.class</span>)<span class="hljs-comment">;</span>
        conf<span class="hljs-preprocessor">.setOutputValueClass</span>(IntWritable<span class="hljs-preprocessor">.class</span>)<span class="hljs-comment">;</span>

        conf<span class="hljs-preprocessor">.setMapperClass</span>(Map<span class="hljs-preprocessor">.class</span>)<span class="hljs-comment">;</span>
        conf<span class="hljs-preprocessor">.setCombinerClass</span>(Reduce<span class="hljs-preprocessor">.class</span>)<span class="hljs-comment">;</span>
        conf<span class="hljs-preprocessor">.setReducerClass</span>(Reduce<span class="hljs-preprocessor">.class</span>)<span class="hljs-comment">;</span>

        conf<span class="hljs-preprocessor">.setInputFormat</span>(TextInputFormat<span class="hljs-preprocessor">.class</span>)<span class="hljs-comment">;</span>
        conf<span class="hljs-preprocessor">.setOutputFormat</span>(TextOutputFormat<span class="hljs-preprocessor">.class</span>)<span class="hljs-comment">;</span>

        FileInputFormat<span class="hljs-preprocessor">.setInputPaths</span>(conf, new Path(<span class="hljs-string">"hdfs://localhost:9000/user/joke/joey"</span>))<span class="hljs-comment">;</span>
        FileOutputFormat<span class="hljs-preprocessor">.setOutputPath</span>(conf, new Path(<span class="hljs-string">"hdfs://localhost:9000/user/joke/ko"</span>))<span class="hljs-comment">;</span>

        JobClient<span class="hljs-preprocessor">.runJob</span>(conf)<span class="hljs-comment">;</span>
        }catch(Exception e){
            Configuration conf=new Configuration()<span class="hljs-comment">;</span>
       String file=<span class="hljs-string">"hdfs://localhost:9000/user/joke/kong3/part-00000"</span><span class="hljs-comment">;</span>
        FileSystem fs=FileSystem<span class="hljs-preprocessor">.get</span>(URI<span class="hljs-preprocessor">.create</span>(file),conf)<span class="hljs-comment">;//构建FileSystem</span>
        FSDataInputStream is=fs<span class="hljs-preprocessor">.open</span>(new Path(file))<span class="hljs-comment">;//读取文件</span>

        IOUtils<span class="hljs-preprocessor">.copyBytes</span>(is, System<span class="hljs-preprocessor">.out</span>, <span class="hljs-number">4096</span>, true)<span class="hljs-comment">;</span>

        }
        }
    }
</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>