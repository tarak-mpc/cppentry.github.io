---
layout:     post
title:      mapreduce Top_N
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<pre><code>/*

 求统计结果的前3名：
 hadoop hadoop hadoop hadoop hadoop is ok is nice is better
 spark hbase hive flume nice

 输出：
 hadoop 5
 is 3
 nice 2
 */
 * 自定义数据类型：
 * 1、需要实现WritableComparable(可实现排序)  或者Writable(不能排序，只能序列化)
 * 2、需要注意write方法和readFields()方法中的字段的顺序、类型、个数相同
 * 3、compareTo()用于排序使用
import org.apache.hadoop.io.WritableComparable;

import java.io.DataInput;
import java.io.DataOutput;
import java.io.IOException;

public class TopWritable implements WritableComparable&lt;TopWritable &gt; {
    private String words;
    private int count;

    public TopWritable() {
    }

    public TopWritable(String words, int count) {
        this.words = words;
        this.count = count;
    }

    public void write(DataOutput dataOutput) throws IOException {
        dataOutput.writeUTF(this.words);
        dataOutput.writeInt(this.count);
    }

    public void readFields(DataInput dataInput) throws IOException {
        this.words = dataInput.readUTF();
        this.count = dataInput.readInt();
    }


    public int compareTo(TopWritable o) {
        if(this == o){
         return 0;
        }

        if(o.count - this.count != 0){
            return o.count - this.count;
        }
        return this.words.compareTo(this.words);
    }

    @Override
    public String toString() {
        return "words='" + words + '\'' + ", count=" + count;
    }
}

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.NullWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.util.Tool;
import org.apache.hadoop.util.ToolRunner;

import java.io.IOException;
import java.util.TreeSet;


public class Top_N implements Tool {
    private Configuration conf = new Configuration();

    public static class MyMapper extends Mapper&lt;Object,Text,Text,IntWritable&gt;{

        @Override
        protected void setup(Context context) throws IOException, InterruptedException {

        }

        @Override
        protected void map(Object key, Text value, Context context) throws IOException, InterruptedException {
            String line = value.toString();
            String[] words = line.split(" ");
            for (String word : words) {
                context.write(new Text(word),new IntWritable(1 ));
            }
        }
    }

    public static class MyReducer extends Reducer&lt;Text,IntWritable,TopWritable,NullWritable&gt; {
        TreeSet&lt;TopWritable&gt; ts = new TreeSet&lt;TopWritable&gt;();
        @Override
        protected void reduce(Text key, Iterable&lt;IntWritable&gt; values, Context context) throws IOException, InterruptedException {
            int count = 0;
            for (IntWritable iw: values) {
                count += iw.get();
            }
            int size = 3;
            TopWritable tw = new TopWritable(key.toString(),count);
            ts.add(tw);
            if(ts.size() &gt; size){
                ts.remove(ts.last());
            }
        }

        @Override
        protected void cleanup(Context context) throws IOException, InterruptedException {
            for (TopWritable topwritable : ts) {
                context.write(topwritable,NullWritable.get());
            }
        }
    }

    public void setConf(Configuration conf) {

    }

    public Configuration getConf() {
        return this.conf;
    }

    /**
     * 驱动方法
     */
    public int run(String[] args) throws Exception {
        Configuration conf = getConf();
        conf.set("fs.defaultFS", "hdfs://SZ01:8020");
        Job job = Job.getInstance(conf, "Top_N");
        job.setJarByClass(Top_N.class);
        job.setMapperClass(MyMapper.class);

        job.setMapOutputKeyClass(Text.class);
        job.setMapOutputValueClass(Text.class);

        job.setReducerClass(MyReducer.class);

        job.setOutputKeyClass(TopWritable.class);
        job.setOutputValueClass(NullWritable.class);

        setArgs(job,args);
        //提交作业
        int issuccessed = job.waitForCompletion(true) ? 0 : 1;
        return issuccessed;
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

    /**
     * 主函数
     * @param args
     * @throws Exception
     */
    public static void main(String[] args) throws Exception {
        int isok = ToolRunner.run(new Configuration(), new Top_N(), args);
        System.exit(isok);
    }


}

</code></pre>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>