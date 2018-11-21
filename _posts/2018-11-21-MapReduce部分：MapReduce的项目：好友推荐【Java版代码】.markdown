---
layout:     post
title:      MapReduce部分：MapReduce的项目：好友推荐【Java版代码】
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>源数据： </p>

<pre class="has">
<code>tom hello hadoop cat
world hadoop hello hive
cat tom hive
mr hive hello
hive cat hadoop world hello mr
hadoop tom hive world
hello tom world hive mr</code></pre>

<p>是简单的好友列表的差集吗？</p>

<p>最应该推荐的好友TopN，如何排名？</p>

<p><img alt="" class="has" height="324" src="https://img-blog.csdn.net/2018082117332057?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d5cXdpbGxpYW0=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="506"></p>

<p>思路： 推荐者与被推荐者一定有一个或多个相同的好友</p>

<p>全局去寻找好友列表中两两关系</p>

<p>去除直接好友</p>

<p>统计两两关系出现次数</p>

<p>API：</p>

<p>map：按好友列表输出两俩关系</p>

<p>reduce：sum两两关系 再设计一个MR 生成详细报表</p>

<p>主方法：</p>

<pre class="has">
<code class="language-java">package com.bjsxt.fof;

import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;

public class MyFoF {
	public static void main(String[] args) throws IOException, ClassNotFoundException, InterruptedException {
		Configuration conf = new Configuration(true);
		Job job =Job.getInstance(conf);
		
		//设置in
		
		Path input = new Path("/data/fof/input");
		FileInputFormat.addInputPath(job, input );
		//设置out
		
		Path output = new Path("/data/fof/outpath");
		if (output.getFileSystem(conf).exists(output)){
			output.getFileSystem(conf).delete(output);
		}
		FileOutputFormat.setOutputPath(job, output );
		
		//设置map
		job.setMapperClass(FMapper.class);
		job.setMapOutputKeyClass(Text.class);
		job.setMapOutputValueClass(IntWritable.class);
		
		//设置reduce
		job.setReducerClass(FReducer.class);
		
		//提交
		job.waitForCompletion(true);
		
		
	}

}
</code></pre>

<p>map端方法：</p>

<pre class="has">
<code class="language-java">package com.bjsxt.fof;

import java.io.IOException;

import org.apache.commons.lang.StringUtils;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Mapper;

public class FMapper extends Mapper&lt;LongWritable, Text, Text, IntWritable&gt;{
	
	@Override
	public void map(LongWritable key, Text value, Mapper&lt;LongWritable ,Text ,Text, IntWritable&gt;.Context context) throws IOException, InterruptedException{
		Text mkey = new Text();
		IntWritable mval = new IntWritable();
		String [] strs=StringUtils.split(value.toString(),' ');
		for ( int i=1;i&lt;strs.length;i++){
			mkey.set(getfof(strs[0],strs[i]));
			mval.set(0);
			
			context.write(mkey,mval);
			
			for(int j = i+1;j&lt;strs.length;j++){
				mkey.set(getfof(strs[i],strs[j]));
				mval.set(1);
				context.write(mkey,mval);
			}
			
		}
	}

	private static String getfof(String s1, String s2) {
		
		if(s1.compareTo(s2)&lt;0){
			return s1+":"+s2;
		}else{
			return s2+":"+s1;
		}

	}
}
</code></pre>

<p>reduce端方法：</p>

<pre class="has">
<code class="language-java">package com.bjsxt.fof;

import java.io.IOException;

import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Reducer;

public class FReducer extends Reducer&lt;Text, IntWritable, Text, IntWritable&gt;{

	IntWritable rval = new IntWritable();
	
	@Override
	protected void reduce(Text key, Iterable&lt;IntWritable&gt; values,
			Reducer&lt;Text, IntWritable, Text, IntWritable&gt;.Context context) throws IOException, InterruptedException {
		
		//相同的key为一组,这一组数据调用一次reduce方法
		//方法内迭代这一组数据
		
		
		//hadoop:hello 0
		//hadoop:hello 1
		//hadoop:hello 0
		//hadoop:hello 1
		//hadoop:hello 0
		
		int flg =0;
		int sum=0;
		for (IntWritable v : values) {
			
			
			if(v.get() == 0){
				flg = 1;
			}
			sum += v.get();
		}
		if(flg ==0 ){
			rval.set(sum);
			context.write(key, rval);
			
		}
		
	}
}
</code></pre>

<p>提交命令：</p>

<p> <img alt="" class="has" height="383" src="https://img-blog.csdn.net/20180821165942626?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d5cXdpbGxpYW0=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="607"></p>

<p>总结：</p>

<p>1.写前边的配置部分：</p>

<p>Configuration conf = new Configuration(true);<br>
Job job =Job.getInstance(conf); </p>

<p>2.写in：</p>

<p>Path input = new Path("/data/fof/input");<br>
FileInputFormat.addInputPath(job, input );</p>

<p>3.写out：</p>

<p>Path output = new Path("/data/fof/outpath");<br>
 if (output.getFileSystem(conf).exists(output)){<br>
       output.getFileSystem(conf).delete(output);<br>
 }<br>
 FileOutputFormat.setOutputPath(job, output );</p>

<p>4.写map端：</p>

<p>job.setMapperClass(FMapper.class);</p>

<p>5.写map端的输出key的类型：<br>
 job.setMapOutputKeyClass(Text.class);</p>

<p>6.写map端输出value的类型：</p>

<p> job.setMapOutputValueClass(IntWritable.class);</p>

<p>7.写reduce端</p>

<p>job.setReducerClass(FReducer.class);</p>

<p>8.写提交部分    <br>
 job.waitForCompletion(true);</p>

<p>（本次省略了map端的sort和reduce端的sort）</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180821165803779?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d5cXdpbGxpYW0=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"><img alt="" class="has" src="https://img-blog.csdn.net/2018082116585714?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d5cXdpbGxpYW0=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>            </div>
                </div>