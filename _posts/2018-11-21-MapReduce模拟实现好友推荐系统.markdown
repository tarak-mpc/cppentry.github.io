---
layout:     post
title:      MapReduce模拟实现好友推荐系统
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：有一种生活不去经历不知其中艰辛,有一种艰辛不去体会,不会知道其中快乐,有一种快乐,没有拥有不知其中纯粹					https://blog.csdn.net/wwwzydcom/article/details/83960039				</div>
								            <div id="content_views" class="markdown_views prism-tomorrow-night-eighties">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>数据集:</p>
<p>tom hello hadoop cat<br>
world hadoop hello hive<br>
cat tom hive<br>
mr hive hello<br>
hive cat hadoop world hello mr<br>
hadoop tom hive world<br>
hello tom world hive mr</p>
<p>思想: tom和world之间不是好友,通过判断它们间接好友数据集的相同的个数进行对比 个数多的,将两个好友进行推荐</p>
<p>mapper</p>
<pre><code>package com.zyd.friends;

import java.io.IOException;

import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.util.StringUtils;

public class FriendsMapper extends Mapper&lt;LongWritable, Text, Text, IntWritable&gt;{
		Text mkey = new Text();
		IntWritable mval = new IntWritable();
		@Override
		protected void map(LongWritable key, Text value,Context context)
				throws IOException, InterruptedException {
			//不能用双引号,要用单引号 将传递过来的值进行分割
			String[] strs = StringUtils.split(value.toString(),' ');
			// 直接好友的 key为直接好友列表  value为0
			for (int i = 1; i &lt; strs.length; i++) {
				//直接好友关系
				mkey.set(fof(strs[0],strs[i]));
				mval.set(0);
				context.write(mkey, mval);
				//间接好友关系 设置value为1
				for (int j = i+1; j &lt; strs.length; j++) {
					mkey.set(fof(strs[i],strs[j]));
					mval.set(1);
					context.write(mkey, mval);
				}
			}
		}
		
		//两个共同好友的间接好友之间,可能存在 B C 和C B 的情况,但是比对累加时,计算机不识别,所以需要字典排序
		private static String fof(String str1, String str2) {
			//compareTo比较的 正数说明大
			if (str1.compareTo(str2)&gt;0) {
				return str2+":"+str1;
			}
			return str1+":"+str2 ;
		}
		
		
}
</code></pre>
<p>Reducer</p>
<pre><code>package com.zyd.friends;

import java.io.IOException;

import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Reducer;

public class FriendsReducer extends Reducer&lt;Text, IntWritable, Text, IntWritable&gt; {
	IntWritable rval = new IntWritable();

	@Override
	protected void reduce(Text key, Iterable&lt;IntWritable&gt; vals,Context context)
			throws IOException, InterruptedException {
	int sum=0;
	for (IntWritable v : vals) {
		//发现直接是0的 是直接好友 舍弃
		if (v.get() ==0 ) {
			return ;
		}
		sum +=1;
	}
	rval.set(sum);
	context.write(key, rval);
	}
	
}
</code></pre>
<p>Runner</p>
<pre><code>package com.zyd.friends;

import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;

public class FriendsRunner {
		public static void main(String[] args) throws IOException, ClassNotFoundException, InterruptedException {
			Configuration conf = new Configuration();
			Job job = Job.getInstance(conf);
			
			job.setJarByClass(FriendsRunner.class);
			
			Path input = new Path(args[0]);
			FileInputFormat.addInputPath(job, input);
			
			Path output = new Path(args[1]);
			//如果文件存在,,删除文件,方便后续调试代码
			if (output.getFileSystem(conf).exists(output)) {
				output.getFileSystem(conf).delete(output,true);
			}
			
			FileOutputFormat.setOutputPath(job, output);
			
			job.setMapperClass(FriendsMapper.class);
			job.setMapOutputKeyClass(Text.class);
			job.setOutputValueClass(IntWritable.class);
			job.setReducerClass(FriendsReducer.class);
			
			job.waitForCompletion(true);
		}
}
</code></pre>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>