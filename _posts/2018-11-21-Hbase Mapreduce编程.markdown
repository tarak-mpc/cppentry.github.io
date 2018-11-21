---
layout:     post
title:      Hbase Mapreduce编程
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/mapengbo521521/article/details/43924891				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p align="center"><span style="font-size:14px;"><strong>Hbase Mapreduce编程</strong></span></p>
<p align="left"><span style="font-size:14px;">hadoop，hbase安装参考：<a href="http://blog.csdn.net/mapengbo521521/article/details/41777721" rel="nofollow">http://blog.csdn.net/mapengbo521521/article/details/41777721</a></span></p>
<p align="left"><span style="font-size:14px;">hbase表创建数据插入参考：<a href="http://blog.csdn.net/mapengbo521521/article/details/43917119" rel="nofollow">http://blog.csdn.net/mapengbo521521/article/details/43917119</a></span></p>
<p><span style="font-size:14px;">hbase mapreduce参考：</span><a href="http://wenku.baidu.com/link?url=w5WwJHqI2KWOx_xQcIrP0Q2GYo0s3t6SWDz_plT-D2WiTBrU6hn_6CGwCD6XbbQ72EPeRTmIxpDJ-6Ju5LFyHh4P27eLyVXJ5xwBdmqYVR_" rel="nofollow"><span style="font-family:Calibri;font-size:14px;color:#0000ff;">http://wenku.baidu.com/link?url=w5WwJHqI2KWOx_xQcIrP0Q2GYo0s3t6SWDz_plT-D2WiTBrU6hn_6CGwCD6XbbQ72EPeRTmIxpDJ-6Ju5LFyHh4P27eLyVXJ5xwBdmqYVR_</span></a></p>
<p> </p>
<p><span style="font-size:14px;">对于Hhbase中的表<span style="font-family:Calibri;">blog2</span>的数据如下格式：</span></p>
<p><img alt="" src="https://img-blog.csdn.net/20150224123441714?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbWFwZW5nYm81MjE1MjE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center"></p>
<p><span style="font-size:14px;">我们需要转化成<span style="font-family:Calibri;">blog3</span>表的数据如下，即统计每种技术作者的nicknames：</span></p>
<p><img alt="" src="https://img-blog.csdn.net/20150224123515204?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbWFwZW5nYm81MjE1MjE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center"></p>
<p><span style="font-size:14px;">实际运算过程如下：</span></p>
<p><img alt="" src="https://img-blog.csdn.net/20150224123313761?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbWFwZW5nYm81MjE1MjE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center"></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-size:14px;">具体代码如下(注意如果表blog3不存在则需要创建)：</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">packageorg.apache.hadoop.hbase;</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">importjava.io.IOException;</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">importorg.apache.hadoop.conf.Configuration;</span></p>
<p><span style="font-family:Calibri;font-size:14px;">importorg.apache.hadoop.hbase.client.HBaseAdmin;</span></p>
<p><span style="font-family:Calibri;font-size:14px;">importorg.apache.hadoop.hbase.client.Put;</span></p>
<p><span style="font-family:Calibri;font-size:14px;">importorg.apache.hadoop.hbase.client.Result;</span></p>
<p><span style="font-family:Calibri;font-size:14px;">importorg.apache.hadoop.hbase.client.Scan;</span></p>
<p><span style="font-family:Calibri;font-size:14px;">importorg.apache.hadoop.hbase.io.ImmutableBytesWritable;</span></p>
<p><span style="font-family:Calibri;font-size:14px;">importorg.apache.hadoop.hbase.mapreduce.TableMapReduceUtil;</span></p>
<p><span style="font-family:Calibri;font-size:14px;">importorg.apache.hadoop.hbase.mapreduce.TableMapper;</span></p>
<p><span style="font-family:Calibri;font-size:14px;">importorg.apache.hadoop.hbase.mapreduce.TableReducer;</span></p>
<p><span style="font-family:Calibri;font-size:14px;">importorg.apache.hadoop.hbase.util.Bytes;</span></p>
<p><span style="font-family:Calibri;font-size:14px;">importorg.apache.hadoop.mapreduce.Job;</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">publicclass HbaseMapReduce {</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">public static class Mapper extendsTableMapper&lt;ImmutableBytesWritable,ImmutableBytesWritable&gt; {</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">           public Mapper() {</span></p>
<p><span style="font-family:Calibri;font-size:14px;">                    </span></p>
<p><span style="font-family:Calibri;font-size:14px;">           }</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">           @Override</span></p>
<p><span style="font-family:Calibri;font-size:14px;">           public voidmap(ImmutableBytesWritable row, Result</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">           values, Context context) throwsIOException {</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">                    ImmutableBytesWritable value= null;</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">                    String[] tags = null;</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">                    for (KeyValue kv :values.list()) {</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">                             if("author".equals(Bytes.toString(kv.getFamily())) &amp;&amp;"nickname".equals(Bytes.toString(kv.getQualifier()))) {</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">                                       value =new ImmutableBytesWritable(kv.getValue());</span></p>
<p><span style="font-family:Calibri;font-size:14px;">                             }</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">                             if("article".equals(Bytes.toString(kv.getFamily())) &amp;&amp;"tag".equals(Bytes.toString(kv.getQualifier()))) {</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">                                       tags =Bytes.toString(kv.getValue()).split(",");</span></p>
<p><span style="font-family:Calibri;font-size:14px;">                             }</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">                    }</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">                    for (int i = 0; i &lt;tags.length; i++) {</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">                             ImmutableBytesWritablekey = new ImmutableBytesWritable(</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">                             Bytes.toBytes(tags[i].toLowerCase()));</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">                             try {</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">                                       context.write(key,value);</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">                             } catch(InterruptedException e) {</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">                             }</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">                    }</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">           }</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">}</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">public static class Reducer extendsTableReducer&lt;ImmutableBytesWritable,ImmutableBytesWritable,ImmutableBytesWritable&gt; {</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">           @Override</span></p>
<p><span style="font-family:Calibri;font-size:14px;">           public voidreduce(ImmutableBytesWritable key, Iterable&lt;ImmutableBytesWritable&gt;values, Context</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">           context) throws IOException,InterruptedException {</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">                    String friends ="";</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">                    for (ImmutableBytesWritableval : values) {</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">                             friends +=(friends.length() &gt; 0 ? "," : "")+Bytes.toString(val.get());</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">                    }</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">                    Put put = newPut(key.get());</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">                    put.add(Bytes.toBytes("person"),</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">                    Bytes.toBytes("nicknames"),Bytes.toBytes(friends));</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">                    context.write(key, put);</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">           }</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">}</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">public static void main(String[] args) throwsException {</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">           Configuration conf = newConfiguration();</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">           conf = HBaseConfiguration.create(conf);</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">           Job job = new Job(conf,"HBase_FindFriend");</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">           job.setJarByClass(HbaseMapReduce.class);</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">           Scan scan = new Scan();</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">           scan.addColumn(Bytes.toBytes("author"),Bytes.toBytes("nickname"));</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">           scan.addColumn(Bytes.toBytes("article"),Bytes.toBytes("tag"));</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">           TableMapReduceUtil.initTableMapperJob("blog2",scan,HbaseMapReduce.Mapper.class, ImmutableBytesWritable.class,</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">           ImmutableBytesWritable.class, job);</span></p>
<p><span style="font-family:Calibri;font-size:14px;">           String []family={"person"};</span></p>
<p><span style="font-family:Calibri;font-size:14px;">           creatTable("blog3",family,conf);</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">           TableMapReduceUtil.initTableReducerJob("blog3",HbaseMapReduce.Reducer.class,job);</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">           System.exit(job.waitForCompletion(true)? 0 : 1);</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">}</span></p>
<p></p>
<p><span style="font-family:Calibri;font-size:14px;">public static void creatTable(String tableName,String[] family,Configuration conf)throws Exception {</span></p>
<p><span style="font-family:Calibri;font-size:14px;">           HBaseAdmin admin = newHBaseAdmin(conf);</span></p>
<p><span style="font-family:Calibri;font-size:14px;">           HTableDescriptor desc = newHTableDescriptor(tableName);</span></p>
<p><span style="font-family:Calibri;font-size:14px;">           for (int i = 0; i &lt; family.length;i++) {</span></p>
<p><span style="font-family:Calibri;font-size:14px;">                    desc.addFamily(newHColumnDescriptor(family[i]));</span></p>
<p><span style="font-family:Calibri;font-size:14px;">           }</span></p>
<p><span style="font-family:Calibri;font-size:14px;">           if (admin.tableExists(tableName)) {</span></p>
<p><span style="font-family:Calibri;font-size:14px;">                    System.out.println("tableExists!");</span></p>
<p><span style="font-family:Calibri;font-size:14px;">                    System.exit(0);</span></p>
<p><span style="font-family:Calibri;font-size:14px;">           } else {</span></p>
<p><span style="font-family:Calibri;font-size:14px;">                    admin.createTable(desc);</span></p>
<p><span style="font-family:Calibri;font-size:14px;">                    System.out.println("createtable Success!");</span></p>
<p><span style="font-family:Calibri;font-size:14px;">           }</span></p>
<p><span style="font-family:Calibri;font-size:14px;">}</span></p>
<p><span style="font-family:Calibri;font-size:14px;">}</span></p>
<p><span style="font-family:Calibri;font-size:14px;"></span> </p>
<p><span style="font-family:Calibri;font-size:14px;"></span> </p>
<p><span style="font-size:14px;">如果以<span style="font-family:Calibri;">hbase</span>作为输入<span style="font-family:Calibri;">HDFS</span>作为输出：</span></p>
<p><span style="font-family:Calibri;font-size:14px;">Configurationconf = HBaseConfiguration.create();</span></p>
<p><span style="font-family:Calibri;font-size:14px;">Job job =new Job(conf, "job name ");</span></p>
<p><span style="font-family:Calibri;font-size:14px;">job.setJarByClass(test.class);</span></p>
<p><span style="font-family:Calibri;font-size:14px;">Scan scan= new Scan();</span></p>
<p><span style="font-family:Calibri;font-size:14px;">TableMapReduceUtil.initTableMapperJob(inputTable,scan, mapper.class,</span></p>
<p><span style="font-family:Calibri;font-size:14px;">           Writable.class, Writable.class, job);</span></p>
<p><span style="font-family:Calibri;font-size:14px;">job.setOutputKeyClass(Writable.class);</span></p>
<p><span style="font-family:Calibri;font-size:14px;">job.setOutputValueClass(Writable.class);</span></p>
<p><span style="font-family:Calibri;font-size:14px;">FileOutputFormat.setOutputPath(job,Path);</span></p>
<p><span style="font-family:Calibri;font-size:14px;">    job.waitForCompletion(true);</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-size:14px;">如果以<span style="font-family:Calibri;">HDFS</span>作为输入<span style="font-family:Calibri;">hbase</span>作为输出：</span></p>
<p><span style="font-family:Calibri;font-size:14px;">Configurationconf = HBaseConfiguration.create();</span></p>
<p><span style="font-family:Calibri;font-size:14px;">Job job =new Job(conf, "job name ");</span></p>
<p><span style="font-family:Calibri;font-size:14px;">job.setJarByClass(test.class);</span></p>
<p><span style="font-family:Calibri;font-size:14px;">job.setMapperClass(mapper.class);</span></p>
<p><span style="font-family:Calibri;font-size:14px;">job.setMapOutputKeyClass(Text.class);</span></p>
<p><span style="font-family:Calibri;font-size:14px;">job.setMapOutputValueClass(LongWritable.class);</span></p>
<p><span style="font-family:Calibri;font-size:14px;">FileInputFormat.setInputPaths(job,path);</span></p>
<p><span style="font-family:Calibri;font-size:14px;">TableMapReduceUtil.initTableReducerJob(tableName,</span></p>
<p><span style="font-family:Calibri;font-size:14px;">                             reducer.class,job);</span></p>
<p> </p>
<p> </p>
            </div>
                </div>