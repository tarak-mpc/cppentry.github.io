---
layout:     post
title:      结合案例讲解MapReduce重要知识点  -----------   倒排序
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：个人原创，转载请标注！					https://blog.csdn.net/Z_Date/article/details/83863790				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p><strong>需求：</strong></p>

<p><strong>文章及其内容</strong>：​ index.html : hadoop is good hadoop hadoop is ok​ page.html : hadoop has hbase hbase is good hbase and hive​ content.html : hadoop spark hbase are good ok</p>

<p><strong>输出：</strong> and page.html:1 are content.html:1 hadoop index.html:3;page.html:1;content.html:1 hbase page.html:3;content.html:1</p>

<p><strong>DescSortCombiner</strong></p>

<pre class="has">
<code class="language-java">import java.io.IOException;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Reducer;

public class DescSortCombiner extends Reducer&lt;Text, Text, Text, Text&gt;{

	/**
	 * index.html_hadoop list(1,1,1)
	 * index.html_is list(1,1)
	 * index.html_good list(1)
	 * index.html_ok list(1)
	 * page.html_hadoop list(1)
	 * 
	 * 
	 * hadoop index.html:3
	 * hadoop page.html:1
	 * 
	 * 
	 */
	@Override
	protected void reduce(Text key, Iterable&lt;Text&gt; value,Context context) throws IOException,
			InterruptedException {
		 int counter = 0;
		 Text k = new Text();
		 Text v = new Text();
		String s [] = key.toString().split("_");
		for (Text t : value) {
			counter += Integer.parseInt(t.toString());
		}
		k.set(s[1]);
		v.set(s[0]+":"+counter);
		context.write(k, v);
	}
	
}</code></pre>

<p><strong>DescSort </strong></p>

<pre class="has">
<code class="language-java">import java.io.IOException;
import java.util.ArrayList;
import java.util.List;
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.conf.Configured;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.InputSplit;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.input.FileSplit;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.util.GenericOptionsParser;
import org.apache.hadoop.util.Tool;
import org.apache.hadoop.util.ToolRunner;

/**
 * 
 * @author lyd
 *
 *倒排索引：
 *
 */
public class DescSort  extends Configured implements Tool{
	/**
	 * 自定义的myMapper
	 * @author lyd
	 *
	 */
	static class MyMapper extends Mapper&lt;LongWritable, Text, Text, Text&gt;{

		@Override
		protected void setup(Context context)throws IOException, InterruptedException {
		}

		@Override
		protected void map(LongWritable key, Text value,Context context)
				throws IOException, InterruptedException {
			String line = value.toString();
			//获取文件名
			InputSplit is = context.getInputSplit();
			String fileName = ((FileSplit)is).getPath().getName();
			String lines [] = line.split(" ");
			for (String s: lines) {
				context.write(new Text(fileName+"_"+s), new Text(1+""));
			}
			/**
			 * index.html_hadoop 1
			 * index.html_is 1
			 * index.html_good 1
			 * index.html_hadoop 1
			 * index.html_hadoop 1
			 * index.html_is 1
			 * index.html_ok 1
			 * page.html_hadoop 1
			 */
		}

		@Override
		protected void cleanup(Context context)throws IOException, InterruptedException {
		}
		
	}
	
	/**
	 * 自定义MyReducer
	 * @author lyd
	 *
	 */
	static class MyReducer extends Reducer&lt;Text, Text, Text, Text&gt;{

		@Override
		protected void setup(Context context)throws IOException, InterruptedException {
		}
		
		List&lt;String&gt; li = new ArrayList&lt;String&gt;();
		@Override
		protected void reduce(Text key, Iterable&lt;Text&gt; value,Context context)
				throws IOException, InterruptedException {
		
			/**
			 * index.html_hadoop list(1,1,1)
			 * index.html_is list(1,1)
			 * index.html_good list(1)
			 * index.html_ok list(1)
			 * page.html_hadoop list(1)
			 * 
			 * 
			 *hadoop list(index.html:3,page.html:1)
			 */
			
			/*
			 int counter = 0;
			 for (Text t : value) {
				counter += Integer.parseInt(t.toString());
			}
			String s [] = key.toString().split("_");
			li.add(s[1]+" "+s[0]+":"+counter);*/
			String v = "";
			for (Text t : value) {
				v += t.toString() +";";
			}
			context.write(key, new Text(v.substring(0, v.length()-1)));
		}
		
		@Override
		protected void cleanup(Context context)throws IOException, InterruptedException {
			/*for (String s : li) {
				String ss [] = s.split(" ");
			}*/
		}
	}
	
	@Override
	public int run(String[] args) throws Exception {
		//1、获取conf对象
		Configuration conf = super.getConf();
		//2、创建job
		Job job = Job.getInstance(conf, "model03");
		//3、设置运行job的class
		job.setJarByClass(DescSort.class);
		//4、设置map相关属性
		job.setMapperClass(MyMapper.class);
		job.setMapOutputKeyClass(Text.class);
		job.setMapOutputValueClass(Text.class);
		FileInputFormat.addInputPath(job, new Path(args[0]));
		
		//设置commbiner
		job.setCombinerClass(DescSortCombiner.class);
		
		//5、设置reduce相关属性
		job.setReducerClass(MyReducer.class);
		job.setOutputKeyClass(Text.class);
		job.setOutputValueClass(Text.class);
		//判断输出目录是否存在，若存在则删除
		FileSystem fs = FileSystem.get(conf);
		if(fs.exists(new Path(args[1]))){
			fs.delete(new Path(args[1]), true);
		}
		FileOutputFormat.setOutputPath(job, new Path(args[1]));
		
		//6、提交运行job
		int isok = job.waitForCompletion(true) ? 0 : 1;
		return isok;
	}
	
	/**
	 * job的主入口
	 * @param args
	 */
	public static void main(String[] args) {
		try {
			//对输入参数作解析
			String [] argss = new GenericOptionsParser(new Configuration(), args).getRemainingArgs();
			System.exit(ToolRunner.run(new DescSort(), argss));
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
}
</code></pre>

<p> </p>            </div>
                </div>