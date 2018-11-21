---
layout:     post
title:      Mapreduce实例---统计单词个数（wordcount）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：转载请注明出处！					https://blog.csdn.net/litianxiang_kaola/article/details/71154302				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><strong><span style="font-size:24px;">一：问题介绍</span></strong></p>
<p><span style="font-size:18px;">统计每一个单词在整个数据集中出现的总次数。</span><strong><span style="font-size:24px;"></span></strong></p>
<p><strong><span style="font-size:24px;"><br></span></strong></p>
<p><strong><span style="font-size:24px;"><span style="font-size:18px;">数据流程：</span><br></span></strong></p>
<p><strong><span style="font-size:24px;"><img src="https://img-blog.csdn.net/20170505172835008" alt=""><br></span></strong></p>
<p><strong><span style="font-size:24px;"><br></span></strong></p>
<p><strong><span style="font-size:24px;"><br></span></strong></p>
<p><strong><span style="font-size:24px;">二：需要的jar包</span></strong></p>
<p><span style="font-size:18px;color:#FF0000;">Hadoop-2.4.1\share\hadoop\hdfs\hadoop-hdfs-2.4.1.jar<br>
hadoop-2.4.1\share\hadoop\hdfs\lib\所有jar包<br><br>
hadoop-2.4.1\share\hadoop\common\hadoop-common-2.4.1.jar<br>
hadoop-2.4.1\share\hadoop\common\lib\所有jar包<br><br>
hadoop-2.4.1\share\hadoop\mapreduce\除hadoop-mapreduce-examples-2.4.1.jar之外的jar包<br>
hadoop-2.4.1\share\hadoop\mapreduce\lib\所有jar包</span></p>
<p><br></p>
<p><br></p>
<p><strong><span style="font-size:24px;">三：代码</span></strong></p>
<p><strong><span style="font-size:18px;">mapper类实现：</span></strong><br></p>
<p></p>
<pre><code class="language-java">/*
 * KEYIN：输入kv数据对中key的数据类型
 * VALUEIN：输入kv数据对中value的数据类型
 * KEYOUT：输出kv数据对中key的数据类型
 * VALUEOUT：输出kv数据对中value的数据类型
 */
public class WordCountMapper extends Mapper&lt;LongWritable, Text, Text, IntWritable&gt;{
	
	/*
	 * map方法是提供给map task进程来调用的，map task进程是每读取一行文本来调用一次我们自定义的map方法
	 * map task在调用map方法时，传递的参数：
	 * 		一行的起始偏移量LongWritable作为key
	 * 		一行的文本内容Text作为value
	 */
	@Override
	protected void map(LongWritable key, Text value,Context context) throws IOException, InterruptedException {
		//拿到一行文本内容，转换成String 类型
		String line = value.toString();
		//将这行文本切分成单词
		String[] words=line.split(" ");
		
		//输出&lt;单词，1&gt;
		for(String word:words){
			context.write(new Text(word), new IntWritable(1));
		}
	}
}</code></pre>
<p></p>
<p><span style="font-size:18px;"><strong>reducer类实现：</strong></span></p>
<p></p>
<pre><code class="language-java">/*
 * KEYIN：对应mapper阶段输出的key类型
 * VALUEIN：对应mapper阶段输出的value类型
 * KEYOUT：reduce处理完之后输出的结果kv对中key的类型
 * VALUEOUT：reduce处理完之后输出的结果kv对中value的类型
 */
public class WordCountReducer extends Reducer&lt;Text, IntWritable, Text, IntWritable&gt;{
	@Override
	/*
	 * reduce方法提供给reduce task进程来调用
	 * 
	 * reduce task会将shuffle阶段分发过来的大量kv数据对进行聚合，聚合的机制是相同key的kv对聚合为一组
	 * 然后reduce task对每一组聚合kv调用一次我们自定义的reduce方法
	 * 比如：&lt;hello,1&gt;&lt;hello,1&gt;&lt;hello,1&gt;&lt;tom,1&gt;&lt;tom,1&gt;&lt;tom,1&gt;
	 *  hello组会调用一次reduce方法进行处理，tom组也会调用一次reduce方法进行处理
	 *  调用时传递的参数：
	 *  		key：一组kv中的key
	 *  		values：一组kv中所有value的迭代器
	 */
	protected void reduce(Text key, Iterable&lt;IntWritable&gt; values,Context context) throws IOException, InterruptedException {
		//定义一个计数器
		int count = 0;
		//通过value这个迭代器，遍历这一组kv中所有的value，进行累加
		for(IntWritable value:values){
			count+=value.get();
		}
		
		//输出这个单词的统计结果
		context.write(key, new IntWritable(count));
	}
}</code></pre>
<p></p>
<p><span style="font-size:18px;"><strong>job提交客户端实现：</strong></span></p>
<p></p>
<pre><code class="language-java">public class WordCountJobSubmitter {
	
	public static void main(String[] args) throws IOException, ClassNotFoundException, InterruptedException {
		Configuration conf = new Configuration();
		Job wordCountJob = Job.getInstance(conf);
		
		//重要：指定本job所在的jar包
		wordCountJob.setJarByClass(WordCountJobSubmitter.class);
		
		//设置wordCountJob所用的mapper逻辑类为哪个类
		wordCountJob.setMapperClass(WordCountMapper.class);
		//设置wordCountJob所用的reducer逻辑类为哪个类
		wordCountJob.setReducerClass(WordCountReducer.class);
		
		//设置map阶段输出的kv数据类型
		wordCountJob.setMapOutputKeyClass(Text.class);
		wordCountJob.setMapOutputValueClass(IntWritable.class);
		
		//设置最终输出的kv数据类型
		wordCountJob.setOutputKeyClass(Text.class);
		wordCountJob.setOutputValueClass(IntWritable.class);
		
		//设置要处理的文本数据所存放的路径
		FileInputFormat.setInputPaths(wordCountJob, "hdfs://192.168.77.70:9000/wordcount/srcdata/");
		FileOutputFormat.setOutputPath(wordCountJob, new Path("hdfs://192.168.77.70:9000/wordcount/output/"));
		
		//提交job给hadoop集群
		wordCountJob.waitForCompletion(true);
	}
}</code></pre>
<p></p>
<p><br></p>
<p><br></p>
<p><span style="font-size:24px;"><strong>四：操作流程</strong></span></p>
<p><strong><span style="font-size:18px;">1、将项目打成jar包上传到虚拟机上</span></strong><br></p>
<p><img src="https://img-blog.csdn.net/20170503204316863?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl0aWFueGlhbmdfa2FvbGE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p><img src="https://img-blog.csdn.net/20170503204604115?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl0aWFueGlhbmdfa2FvbGE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p><br></p>
<p><strong><span style="font-size:18px;">2、创建文本数据</span></strong><br></p>
<p><img src="https://img-blog.csdn.net/20170503204333771?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl0aWFueGlhbmdfa2FvbGE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p><br></p>
<p><span style="font-size:18px;"><strong>3、将文本数据上传到hdfs</strong></span><br></p>
<p><img src="https://img-blog.csdn.net/20170503205316808" alt=""><br></p>
<p><br></p>
<p><span style="font-size:18px;"><strong>4、运行jar文件</strong></span><br><br><img src="https://img-blog.csdn.net/20170503205521387" alt="" width="907" height="24"><br></p>
<p><br></p>
<p><span style="font-size:18px;"><strong>5、结果</strong></span><br></p>
<p><img src="https://img-blog.csdn.net/20170503204445975?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl0aWFueGlhbmdfa2FvbGE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
            </div>
                </div>