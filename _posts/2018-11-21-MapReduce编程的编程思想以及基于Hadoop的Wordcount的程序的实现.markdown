---
layout:     post
title:      MapReduce编程的编程思想以及基于Hadoop的Wordcount的程序的实现
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>1.MapReduce编程的编程思想(比如该文件就只输入三行，如下面所示)</p>

<pre><code>hive spark hive hbase
hadoop hive spark
sqoop flume scala


（0，“hive spark hive hbase”）
（22，“hadoop hive spark”）
（40，“sqoop flume scala”）

 输出：（hive,1）,(spark,1）,(hive,1）
在shuffle阶段，相同key的值进行合并，合并放到一个 集合中
(hive,&lt;1,1&gt;)，（spark,&lt;1&gt;）
</code></pre>

<p>reduce：聚合</p>

<pre><code>输入：(hive,&lt;1,1&gt;)，（spark,&lt;1&gt;）
输出:(hive,2)
</code></pre>

<p>2.mapreduce处理数据过程</p>

<pre><code>整个MapReduce程序，所有数据以（key,value）形式流动的
input:正常情况下，不需要编写代码，仅仅在MapReduce程序运行的时候指定一个路径即可

map(核心关注):map(key,value,output,context)
key:每行数据的偏移量  --毛用       value：每行数据的内容 --真正需要处理的内容
</code></pre>

<p>shuffle:阶段</p>

<pre><code> key：业务需求中的key值
    value：要聚合的值
        reduce(核心关注):reduce(K2 key, Iterator&lt;V2&gt; values,OutputCollector&lt;K3, V3&gt;，output, Reporter reporter)
</code></pre>

<p>3.通过idea实现MapReduce的Wordcount的代码实现，</p>

<pre><code> public class WordCountMapReduce {

public static void main(String[] args) throws IOException, ClassNotFoundException, InterruptedException {
    //检查参数
   if (args.length &lt;2){
       System.out.println("Usage: wordcount &lt;in&gt; [&lt;in&gt;...] &lt;out&gt;");
       return;
   }
   //1、读取配置文件
    Configuration configuration = new Configuration();
   //2、创建job
    Job job = Job.getInstance( configuration, "UserWordCountMapReduce" );
    job.setJarByClass( WordCountMapReduce.class );

    //a、input

    Path inputPath = new Path( args[0] );
    FileInputFormat.setInputPaths( job,inputPath );
    //b、map
    job.setMapperClass( WordCountMapper.class );
    job.setMapOutputKeyClass( Text.class ); //字符串String
    job.setMapOutputValueClass( IntWritable.class );//int

    //b.b、shuffle

    //c、reduce
    job.setReducerClass( WordCountReduce.class );
    job.setOutputKeyClass(  Text.class  );
    job.setOutputValueClass( IntWritable.class );
    //d、output
    Path outputPath = new Path(  args[1]);
    FileOutputFormat.setOutputPath( job,outputPath );

    //判断是否成功
    boolean isSuccess = job.waitForCompletion( true );//进度条
    System.exit( isSuccess?0:1 );
}


/**
 * class Mapper&lt;KEYIN, VALUEIN, KEYOUT, VALUEOUT&gt;
 *     KEYIN:行偏移量   long
 *     VALUEIN：行内容  String
 *     KEYOUT：
 *      map方法输出key的类型
 *      词频统计的单词   String
 *     VALUEOUT：
 *     map方法输出value的类型
 *      出现的次数，就是1，int
 *
 */
private static class WordCountMapper extends Mapper&lt;LongWritable, Text, Text, IntWritable&gt; {
    private Text mapOutputKey = new Text( );
    private static  final IntWritable mapOutputValue = new IntWritable( 1 );
    @Override
    protected void map(LongWritable key, Text value, Context context) throws IOException, InterruptedException {
        //（0，“hive spark hive hbase”）
        String valueStr = value.toString();
        String[] items = valueStr.split( " " );
        for (String item:items) {
            mapOutputKey.set( item );
            //使用Context将map方法处理的结果返回输出
            //public void write(KEYOUT key, VALUEOUT value)
            context.write( mapOutputKey ,mapOutputValue);
        }
    }
}

/**
 * public class Reducer&lt;KEYIN,VALUEIN,KEYOUT,VALUEOUT&gt;
 */
private static class WordCountReduce extends Reducer&lt;Text,IntWritable,Text,IntWritable&gt; {

    private IntWritable outputValue = new IntWritable(  );
    @Override
    protected void reduce(Text key, Iterable&lt;IntWritable&gt; values, Context context) throws IOException, InterruptedException {

        int sum = 0;
        for (IntWritable valus:values) {
            sum+=valus.get();
        }
        outputValue.set( sum );
        context.write( key,outputValue );
    }
}
</code></pre>

<p>} <br>
以上代码是通过MapReduce程序实现的词频统计，统计单词的个数，这是我自己的一个学习笔记，希望能帮助到正在学习该方面知识的人，我也是一个刚入门的小白，写的不好，希望各位大佬提出相关建议，以后改进</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>