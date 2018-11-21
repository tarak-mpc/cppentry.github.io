---
layout:     post
title:      MapReduce编程
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span></span>有两种方式编写MapReduce程序，一种通过eclipse的hadoop插件，另一种通过引入相关的包。这里先介绍通过引入jar包来编程。</p>
<p><span></span>一、<span> </span>打开eclipse，导入开发MapReduce需要的jar包：</p>
<p><span></span><span></span>1.<span></span>hadoop/share/hadoop/mapreduce下的所有jar包，但是子文件夹下面的jar包不需要导入</p>
<p><span></span><span></span>2.<span></span>hadoop/share/hadoop/common下的hadoop-common-2.7.1.jar</p>
<p><span></span><span></span>3.<span></span>hadoop/share/hadoop/common/lib下的commons-cli-1.2.jar</p>
<p><span></span>二、<span> </span>编写程序，这里以一个简单的案例来介绍。</p>
<p><span></span>1.<span> </span>程序结构</p>
<p><span><img src="https://img-blog.csdn.net/20161102171911466?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p><span></span>2.<span> </span>程序代码：</p>
<p><span></span>MaxTemperatureDriver.java</p>
<p><span></span></p>
<pre><code class="language-java">package pers.peng.maxtemperature;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.conf.Configured;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.util.Tool;
import org.apache.hadoop.util.ToolRunner;

public class MaxTemperatureDriver extends Configured implements Tool {
	public int run(String[] args) throws Exception{
		if(args.length != 2){
            System.err.printf("Usage: %s &lt;input&gt;&lt;output&gt;",getClass().getSimpleName());
            ToolRunner.printGenericCommandUsage(System.err);
            return -1;  
		}

		Configuration conf =getConf();                
        Job job = new Job(getConf());
        job.setJobName("Max Temperature");                  
        job.setJarByClass(getClass());
        FileInputFormat.addInputPath(job,new Path(args[0]));
        FileOutputFormat.setOutputPath(job,new Path(args[1]));                  
        job.setMapperClass(MaxTemperatureMapper.class);
        job.setReducerClass(MaxTemperatureReducer.class);            
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);                  
        return job.waitForCompletion(true)?0:1;
	}
	
	public static void main(String[] args) throws Exception{
		int exitcode = ToolRunner.run(new MaxTemperatureDriver(), args);
		System.exit(exitcode);
	}
}
</code></pre><span></span>MaxTemperatureMapper.java<br><p></p>
<p></p>
<pre><code class="language-java">package pers.peng.maxtemperature;

import java.io.IOException; 
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Counter;

public class MaxTemperatureMapper extends Mapper&lt;LongWritable, Text,Text, IntWritable&gt;{
	public void map(LongWritable key, Text value, Context context) throws IOException, InterruptedException{                                 
		String line =value.toString();                               
		try {
			String year = line.substring(0, 5);
			int airTemperature = Integer.parseInt(line.substring(5, 7));            
			context.write(new Text(year),	new IntWritable(airTemperature));
			Counter countPrint0 = context.getCounter("Test", "空");
			countPrint0.increment(1l);
			Counter countPrint = context.getCounter("Map1111", line.substring(5, 7));
			countPrint.increment(1l);
		} catch (Exception e) {
			System.out.print("Error in line:" + line);
		}                                  
	}        
}</code></pre><span> </span>MaxTemperatureReducer.java
<p></p>
<p></p>
<pre><code class="language-java">package pers.peng.maxtemperature;

import java.io.IOException; 
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Reducer;

public class MaxTemperatureReducer extends Reducer&lt;Text,IntWritable,Text,IntWritable&gt; {        
         public void reduce(Text key, Iterable&lt;IntWritable&gt; values, Context context) throws IOException,  InterruptedException{

                   int maxValue = Integer.MIN_VALUE;                  

                   for(IntWritable value: values){

                            maxValue = Math.max(maxValue,value.get());              

                   }        

                   context.write(key, new IntWritable(maxValue));                 

         } 

}
</code></pre><br><span></span>3.<span> </span>导出为jar包
<p></p>
<p><span></span>“右击项目”-&gt;“Export”-&gt;"Java"-&gt;"JAR file"-&gt;"Next"<br></p>
<p><span></span>此时选择一个放jar包的文件位置，并给该包取名，我的为/usr/local/hadoop/MaxTemperature.jar。其他默认。</p>
<p><span></span>"Next"-&gt;"Next"-&gt;"此时在最下面要选择一个主类作为程序的入口，我选择MaxTemperatureDriver"-&gt;"Finish"</p>
<p><span></span>4.<span> </span>编写测试数据</p>
<p><span></span>数据格式为形如：2016 25<br></p>
<p><span></span>前面为年份，空格，然后是温度。具体要写多少这种数据自己决定。可以分成三四个.txt文件来存放。</p>
<p><span></span>5.<span> </span>启动hadoop，并将测试数据放到hadoop上</p>
<p><span></span>将测试数据放到hadoop</p>
<p><span></span></p>
<p><span></span></p>
            </div>
                </div>