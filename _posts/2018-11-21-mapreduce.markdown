---
layout:     post
title:      mapreduce
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
Google三篇论文           Hadoop<br>
    GFS            --&gt;    HDFS<br>
    mapreduce     --&gt;       Mapreduce<br>
    bigtable    --&gt;    HBase<br><br>
Hadoop<br>
    ** common<br>
    ** HDFS<br>
    ** mapreduce<br>
    ** YARN <br><br>
mapreduce<br>
    ** 分布式离线计算模型<br>
    ** 周期性(每天、每周、每月)分析历史数据<br>
    ** Mapreduce分为两个阶段<br>
        ** map阶段        :  找出关键数据，会产生多个mapper<br>
                           默认情况，一个split对应一个mapper，一个block对应一个mapper<br>
        ** reduce阶段    ： 把map阶段运行的结果进行合并<br>
    <br>
    例如：一个100G文件：<br>
        被分隔成多个block,分散存放在不同的datanode(通常一个节点即是datanode又是nodemanager)<br>
        这些nodemanager会为每个split启动一个mapper<br><br>
    ** Input --&gt; map --&gt; reduce --&gt; output<br>
    ** 整个过程数据流都是键值对<br>
    <br>
====wordcount=========================================================<br><br>
以此例分析Mapreduce:<br><br>
需求：    单词统计，分隔符是\t<br>
hadoop    mapreduce<br>
spark    storm<br>
map    hadoop    mapreduce<br>
reduce    storm    hadoop<br>
hbase    map    storm    <br><br>
** map 输入<br>
    ** 按行读取数据，然后转换成key-value<br>
    &lt;0,hadoop    mapreduce&gt;<br>
    &lt;15,spark    storm&gt;<br>
    &lt;26,map    hadoop    mapreduce&gt;<br>
    &lt;40,reduce    storm    hadoop&gt;<br>
    &lt;50,hbase    map    storm&gt;<br>
** map 输出<br>
    &lt;hadoop,1&gt; &lt;mapreduce,1&gt; &lt;spark,1&gt; &lt;storm,1&gt; &lt;map,1&gt; &lt;hadoop,1&gt;  ...<br>
    ** 中间结果临时存储在本地目录，而非HDFS<br>
    <br>
** reduce<br>
    ** 从相关nodemanager拉取map输出的结果<br>
    ** 运行reduce函数<br>
** 输入<br>
    &lt;hadoop,(1,1,1)&gt; &lt;storm,(1,1,1)&gt; &lt;hbase,1&gt;　....    <br>
** 输出<br>
    hadoop    3<br>
    storm    3<br>
    hbase    1  ...<br>
    <br>
** 结果写入HDFS<br>
    hadoop    3<br>
    storm    3<br>
    hbase    1  ...<br><br>
--------------------------------<br><br>
编写Mapreduce代码实现wordcount        <br>
    ** 八股文模型<br>
        ** mapper class     --&gt; mapper<br>
        ** reducer class    --&gt; reducer<br>
        ** Driver              --&gt; 创建、设置、运行Job<br><br>
1、(可选)<br>
创建"Source Folder"：src/main/resources目录，用来存放core-site.xml等<br>
拷贝log4j.properties<br>
$ cp /opt/modules/hadoop-2.5.0/etc/hadoop/log4j.properties /home/tom/workspace/myhdfs/src/main/resources<br><br>
2、编写代码<br>
Hadoop常用类型： <br>
    IntWritable    LongWritable  Text  NullWritable<br><br>
package com.myblue.myhdfs;<br><br>
import java.io.IOException;<br>
import org.apache.hadoop.conf.Configuration;<br>
import org.apache.hadoop.fs.FileSystem;<br>
import org.apache.hadoop.fs.Path;<br>
import org.apache.hadoop.io.LongWritable;<br>
import org.apache.hadoop.io.Text;<br>
import org.apache.hadoop.mapreduce.Job;<br>
import org.apache.hadoop.mapreduce.Mapper;<br>
import org.apache.hadoop.mapreduce.Reducer;<br>
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;<br>
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;<br><br>
public class WordCountMapReduce{<br><br>
    //mapper<br>
    public static class WordCountMapper extends<br>
            Mapper&lt;LongWritable, Text, Text, LongWritable&gt; {<br><br>
        protected void map(LongWritable key, Text value, Context context)<br>
                throws IOException, InterruptedException {<br>
            <br>
            //输入行<br>
            System.out.println(key.get());<br>
            String lineValue = value.toString();<br>
            String[] splits = lineValue.split("\t");<br>
            <br>
            Text mapOutputKey = new Text(); //输出键<br>
            LongWritable mapOutputValue = new LongWritable(1); //输出值，本例恒为1<br>
            <br>
            for (String s : splits) {<br>
                mapOutputKey.set(s);<br>
                context.write(mapOutputKey, mapOutputValue);<br>
            }<br>
        }<br>
    }<br><br>
    //reducer<br>
    public static class WordCountReducer extends<br>
            Reducer&lt;Text, LongWritable, Text, LongWritable&gt; {<br><br>
        protected void reduce(Text key, Iterable&lt;LongWritable&gt; values,<br>
                Context context) throws IOException, InterruptedException {<br><br>
            long sum = 0;<br>
            for (LongWritable value : values) {<br>
                sum += value.get();<br>
            }<br>
            <br>
            LongWritable outputValue = new LongWritable();<br>
            outputValue.set(sum);<br>
            context.write(key, outputValue);<br>
        }<br>
    }<br><br>
    public static void main(String[] args) throws Exception {<br>
        <br>
        //args=new String[]{"/input","/output"};<br>
        Configuration conf = new Configuration();<br><br>
        //创建作业<br>
        Job job = Job.getInstance(conf);<br>
        job.setJarByClass(WordCountMapReduce.class);<br><br>
        //输入路径<br>
        FileInputFormat.addInputPath(job, new Path(args[0]));<br>
        //输出路径<br>
        Path outPath = new Path(args[1]);<br>
        FileSystem dfs = FileSystem.get(conf);<br>
        if (dfs.exists(outPath)) {<br>
            dfs.delete(outPath, true);<br>
        }<br>
        FileOutputFormat.setOutputPath(job, outPath);<br><br>
        //mapper<br>
        job.setMapperClass(WordCountMapper.class);<br>
        job.setMapOutputKeyClass(Text.class);<br>
        job.setMapOutputValueClass(LongWritable.class);<br><br>
        //reducer<br>
        job.setReducerClass(WordCountReducer.class);<br>
        job.setOutputKeyClass(Text.class);<br>
        job.setOutputValueClass(LongWritable.class);<br><br>
        //提交作业    <br>
        System.exit(job.waitForCompletion(true) ? 0 : 1);<br>
    }<br>
}<br><br>
3、运行<br>
在eclipse里直接运行（需要core-site.xml）<br><br><br>
PS：打包运行<br>
a) 启动Hadoop (若是报进程已启动的错误，可以到tmp目录下删除对应的pid文件  --ls /tmp/*.pid)<br>
b) 将WordCountMapReduce.java文件导出为jar(需要填写文件名，如：XXX.jar)<br>
c) /opt/modules/hadoop-2.5.0/bin/yarn jar WordCountMapReduce.jar com.myblue.myhdfs.WordCountMapReduce /input /output<br><br><br><br>
====YARN================================================<br><br>
YARN架构<br>
    ** 集群资源管理、作业和任务管理    <br>
    ** hadoop2.0以前<br>
        ** jobtracker<br>
        ** tasktracker<br>
    ** hadoop2.0以后<br>
        ** resourcemanager<br>
        ** nodemanager<br>
    <br>
    <br>
    ** resourcemanager<br>
        --接收客户端请求  bin/yarn jar xxx.jar wordcount /input /output<br>
        --启动/监控ApplicationMaster<br>
        --监控NodeManager<br>
        --资源分配与调度<br>
    ** nodemanager<br>
        --单个节点上的资源管理<br>
        --处理来自ResourceManager的命令<br>
        --处理来自ApplicationMaster的命令<br>
        <br>
    ** applicationmaster<br>
        --当前这个任务的管理者,任务运行结束,applicationmaster会消失<br>
        --数据切分<br>
        --为应用程序申请资源，并分配给任务使用<br>
        --任务监控与容错<br>
    ** Container<br>
        --对任务运行环境的抽象，封装了CPU、内存等多维资源以及环境变量、启动命令等任务运行相关的信息<br><br><br>
MapReduce的运行流程<br>
    1、client向集群提交job任务,resourcemanager接收到任务请求<br>
    2、resourcemanager收到请求以后,会选择一台nodemanager启动一个applicationmaster<br>
    3、applicationmaster向resourcemanager申请资源(运行当前job任务需要哪些nodemanager、每个nodemanager需要多少CPU、MEM...)<br>
    4、resourcemanager把对应资源信息响应给applicationmaster<br>
    5、applicationmaster收到以后,调度指挥其他nodemanager运行任务<br>
    6、相关nodemanager接受任务并运行任务(map\reduce)<br>
    7、nodemanager任务运行结束以后会向applicationmaster报告<br>
    8、applicationmaster向resourcemanager报告，并反馈结果给client<br>
    <br><br>
再次认识Hadoop<br>
    ** HDFS<br>
        --分布式文件系统的架构、存储数据<br>
        --namenode<br>
        --datanode<br>
    ** yarn<br>
        --集群资源管理、作业和任务管理<br>
        --resourcemanager<br>
        --nodemanager<br>
            <br><br>
通常集群资源配置：            <br>
    **　内存<br>
    yarn.nodemanager.resource.memory-mb        8G   64G  128G    <br>
            <br>
    **  CPU        <br>
    yarn.nodemanager.resource.cpu-vcores    8核  16核        <br>
        <br>
    **  内存不够，会直接影响job任务运行成败<br>
    **  CPU不够，只会影响job任务运行的快慢
            </div>
                </div>