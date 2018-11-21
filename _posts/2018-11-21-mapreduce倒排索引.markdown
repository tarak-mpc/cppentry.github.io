---
layout:     post
title:      mapreduce倒排索引
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<pre><code>
/**
 倒排索引：
 数据：
 index.html
 hadoop hadoop hadoop is nice is good hadoop hadoop
 hadoop-info.html
 hadoop hadoop hadoop is better
 spark-info.html
 spark spark spark hadoop is nice nice nice

 输出数据：
 better hadoop-info.html:1
 good hadoop-info.html:1
 hadoop index.html:5;hadoop-info.html:3;spark-info.html:1
 ....
 spark spark-info.html:3
 */

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.Text;


import org.apache.hadoop.mapreduce.InputSplit;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.input.FileSplit;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;

import java.io.IOException;

public class InvertedIndex {
    public static class MyMapper extends Mapper&lt;Object,Text,Text,Text&gt;{
        @Override
        protected void map(Object key, Text value, Context context) throws IOException, InterruptedException {
            //获取文件名
            InputSplit is = context.getInputSplit();
            String fileName = ((FileSplit)is).getPath().getName();
            //
            String line = value.toString();
            for (String word : line.split(" ")) {
                //map端输出
                //hadoop_hadoop-info.html:1
             context.write(new Text(word + "_" + fileName),new Text(1 + ""));
            }
        }
    }
    /*
    自定义combiner类：
 * 1、自定义combiner类需要继承reducer类
 * 2、combiner类的输入和输出key-value类型需要map阶段的输出和reducer的输入一致
 * 3、combiner类是redcuer的特殊实现
 * 4、combiner类也是reducer的提前批次执行
 * 5、combiner类也是mr的一种优化，但是需要一定的场景，否则可能会适得其反(key重复率较高的会起到优化作用)
     */
    public static class MyCombiner extends Reducer&lt;Text,Text,Text,Text&gt;{
        @Override
        protected void reduce(Text key, Iterable&lt;Text&gt; values, Context context) throws IOException, InterruptedException {
            int count = 0;
            for (Text t : values) {
                count += Integer.parseInt(t.toString());
            }
            String[] word = key.toString().split("_");
            //combiner的输出
            //hadoop:list{index.html:5,hadoop-info.html:3..}
            context.write(new Text(word[0]),new Text(word[1] + ":" + count));
        }
    }

    public static class MyReducer extends Reducer&lt;Text,Text,Text,Text&gt;{
        @Override
        protected void reduce(Text key, Iterable&lt;Text&gt; values, Context context) throws IOException, InterruptedException {
            String str = "";
            for (Text t : values) {
                str += t.toString() + ";";
            }
            //reduce输出
            // hadoop index.html:5;hadoop-info.html:3;spark-info.html:1
            context.write(new Text(key),new Text(str.substring(0,str.length()-1)));
        }
    }

    public static void main(String[] args) throws IOException, ClassNotFoundException, InterruptedException {
        Configuration conf = new Configuration();
        conf.set("fs.defaultFS", "hdfs://hdoop:9000");
        Job job = Job.getInstance(conf, "InvertedIndex");
        job.setJarByClass(InvertedIndex.class);
        job.setMapperClass(MyMapper.class);

        job.setMapOutputKeyClass(Text.class);
        job.setMapOutputValueClass(Text.class);

        job.setReducerClass(MyReducer.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(Text.class);

        //设置commberiner的类
        job.setCombinerClass(MyCombiner.class);

        setArgs(job,args);
        //提交作业
        int issuccessed = job.waitForCompletion(true) ? 0 : 1;
        //关闭job
        System.exit(issuccessed);

    }
    /**
     * 作业参数处理
     * @param job
     * @param args
     */
    public static void setArgs(Job job , String[] args){
        try {
            if(args.length != 2){
                System.out.println("argments size is not enough!!!");
                System.out.println("Useage :yarn jar *.jar wordcount /inputdata /outputdata");
            }
            //设置输入文件路径
            FileInputFormat.addInputPath(job, new Path(args[0]));
            //判断输出目录是否存在
            FileSystem fs = FileSystem.get(job.getConfiguration());
            Path op = new Path(args[1]);
            if(fs.exists(op)){
                fs.delete(op, true);
            }
            //设置输出数据目录
            FileOutputFormat.setOutputPath(job, new Path(args[1]));

        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}

</code></pre>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>