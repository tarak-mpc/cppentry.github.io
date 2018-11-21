---
layout:     post
title:      HBase 之HFileOutputFormat
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p> hadoop mr 输出需要导入hbase的话最好先输出成HFile格式， 再导入到HBase,因为HFile是HBase的内部存储格式， 所以导入效率很高,下面是一个示例<br>1. 创建HBase表t1</p>
<pre></pre><ol class="dp-xml"><li class="alt"><span><span>hbase(main):157:0* create 't1','f1' </span></span></li>
<li><span>0 row(s) in 1.3280 seconds </span></li>
<li class="alt"><span> </span></li>
<li><span>hbase(main):158:0<span class="tag">&gt;</span><span> scan 't1' </span></span></li>
<li class="alt"><span>ROW                   COLUMN+CELL                                                </span></li>
<li><span>0 row(s) in 1.2770 seconds </span></li>
</ol>
<p>2.写MR作业<br>HBaseHFileMapper.java</p>
<pre></pre><ol class="dp-xml"><li class="alt"><span><span>package com.test.hfile; </span></span></li>
<li><span>import java.io.IOException; </span></li>
<li class="alt"><span>import org.apache.hadoop.hbase.io.ImmutableBytesWritable; </span></li>
<li><span>import org.apache.hadoop.hbase.util.Bytes; </span></li>
<li class="alt"><span>import org.apache.hadoop.io.LongWritable; </span></li>
<li><span>import org.apache.hadoop.io.Text; </span></li>
<li class="alt"><span>import org.apache.hadoop.mapreduce.Mapper; </span></li>
<li><span> </span></li>
<li class="alt"><span>public class HBaseHFileMapper extends Mapper<span class="tag">&lt;</span><span class="tag-name">LongWritable</span><span>, Text, ImmutableBytesWritable, Text</span><span class="tag">&gt;</span><span> { </span></span></li>
<li><span>    private ImmutableBytesWritable <span class="attribute">immutableBytesWritable</span><span> = </span><span class="attribute-value">new</span><span> ImmutableBytesWritable(); </span></span></li>
<li class="alt"><span>    @Override </span></li>
<li><span>    protected void map(LongWritable key, Text value, </span></li>
<li class="alt"><span>            org.apache.hadoop.mapreduce.Mapper.Context context) </span></li>
<li><span>            throws IOException, InterruptedException { </span></li>
<li class="alt"><span>        immutableBytesWritable.set(Bytes.toBytes(key.get())); </span></li>
<li><span>        context.write(immutableBytesWritable, value); </span></li>
<li class="alt"><span>    } </span></li>
<li><span>} </span></li>
</ol>
<p>HBaseHFileReducer.java</p>
<pre></pre><ol class="dp-xml"><li class="alt"><span><span>package com.test.hfile; </span></span></li>
<li><span>import java.io.IOException; </span></li>
<li class="alt"><span>import org.apache.hadoop.hbase.KeyValue; </span></li>
<li><span>import org.apache.hadoop.hbase.io.ImmutableBytesWritable; </span></li>
<li class="alt"><span>import org.apache.hadoop.hbase.util.Bytes; </span></li>
<li><span>import org.apache.hadoop.io.Text; </span></li>
<li class="alt"><span>import org.apache.hadoop.mapreduce.Reducer; </span></li>
<li><span> </span></li>
<li class="alt"><span>public class HBaseHFileReducer extends Reducer<span class="tag">&lt;</span><span class="tag-name">ImmutableBytesWritable</span><span>, Text, ImmutableBytesWritable, KeyValue</span><span class="tag">&gt;</span><span> {     </span></span></li>
<li><span>    protected void reduce(ImmutableBytesWritable key, Iterable<span class="tag">&lt;</span><span class="tag-name">Text</span><span class="tag">&gt;</span><span> values, </span></span></li>
<li class="alt"><span>            Context context) </span></li>
<li><span>            throws IOException, InterruptedException { </span></li>
<li class="alt"><span>        String <span class="attribute">value</span><span>=</span><span class="attribute-value">""</span><span>; </span></span></li>
<li><span>        while(values.iterator().hasNext()) </span></li>
<li class="alt"><span>        { </span></li>
<li><span>            <span class="attribute">value</span><span> = </span><span class="attribute-value">values</span><span>.iterator().next().toString(); </span></span></li>
<li class="alt"><span>            if(value != null &amp;&amp; !"".equals(value)) </span></li>
<li><span>            { </span></li>
<li class="alt"><span>                KeyValue <span class="attribute">kv</span><span> = </span><span class="attribute-value">createKeyValue</span><span>(value.toString()); </span></span></li>
<li><span>                if(kv!=null) </span></li>
<li class="alt"><span>                    context.write(key, kv); </span></li>
<li><span>            } </span></li>
<li class="alt"><span>        } </span></li>
<li>
<span>    } 
    // str格式为</span><span class="Apple-style-span" style="color:#ff0000;">row:</span><span class="Apple-style-span" style="color:#ff0000;">family:</span><span class="Apple-style-span" style="color:#ff0000;">qualifier:</span><span class="Apple-style-span" style="color:#ff0000;">value 简单模拟下</span>
</li>
<li class="alt"><span>    private KeyValue createKeyValue(String str) </span></li>
<li><span>    { </span></li>
<li class="alt"><span>        String[] <span class="attribute-value">str</span><span class="attribute">strs</span><span> = str.split(":"); </span></span></li>
<li><span>        if(strs.length<span class="tag">&lt;</span><span class="tag-name">4</span><span>) </span></span></li>
<li class="alt"><span>            return null; </span></li>
<li><span>        String <span class="attribute">row</span><span>=</span><span class="attribute-value">strs</span><span>[0]; </span></span></li>
<li class="alt"><span>        String <span class="attribute">family</span><span>=</span><span class="attribute-value">strs</span><span>[1]; </span></span></li>
<li><span>        String <span class="attribute">qualifier</span><span>=</span><span class="attribute-value">strs</span><span>[2]; </span></span></li>
<li class="alt"><span>        String <span class="attribute">value</span><span>=</span><span class="attribute-value">strs</span><span>[3]; </span></span></li>
<li><span>        return new KeyValue(Bytes.toBytes(row),Bytes.toBytes(family),Bytes.toBytes(qualifier),System.currentTimeMillis(), Bytes.toBytes(value)); </span></li>
<li class="alt"><span>    } </span></li>
<li><span>} </span></li>
</ol>
<p>HbaseHFileDriver.java</p>
<pre></pre><ol class="dp-xml"><li class="alt"><span><span>package com.test.hfile; </span></span></li>
<li><span>import java.io.IOException; </span></li>
<li class="alt"><span>import org.apache.hadoop.conf.Configuration; </span></li>
<li><span>import org.apache.hadoop.fs.Path; </span></li>
<li class="alt"><span>import org.apache.hadoop.hbase.HBaseConfiguration; </span></li>
<li><span>import org.apache.hadoop.hbase.client.HTable; </span></li>
<li class="alt"><span>import org.apache.hadoop.hbase.io.ImmutableBytesWritable; </span></li>
<li><span>import org.apache.hadoop.hbase.mapreduce.HFileOutputFormat; </span></li>
<li class="alt"><span>import org.apache.hadoop.io.Text; </span></li>
<li><span>import org.apache.hadoop.mapreduce.Job; </span></li>
<li class="alt"><span>import org.apache.hadoop.mapreduce.lib.input.FileInputFormat; </span></li>
<li><span>import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat; </span></li>
<li class="alt"><span>import org.apache.hadoop.util.GenericOptionsParser; </span></li>
<li><span> </span></li>
<li class="alt"><span>public class HbaseHFileDriver { </span></li>
<li><span>    public static void main(String[] args) throws IOException, </span></li>
<li class="alt"><span>            InterruptedException, ClassNotFoundException { </span></li>
<li><span>         </span></li>
<li class="alt"><span>        Configuration <span class="attribute">conf</span><span> = </span><span class="attribute-value">new</span><span> Configuration(); </span></span></li>
<li><span>        String[] <span class="attribute">otherArgs</span><span> = </span><span class="attribute-value">new</span><span> GenericOptionsParser(conf, args).getRemainingArgs(); </span></span></li>
<li class="alt"><span> </span></li>
<li><span>        Job <span class="attribute">job</span><span> = </span><span class="attribute-value">new</span><span> Job(conf, "testhbasehfile"); </span></span></li>
<li class="alt"><span>        job.setJarByClass(HbaseHFileDriver.class); </span></li>
<li><span> </span></li>
<li class="alt"><span>        job.setMapperClass(com.test.hfile.HBaseHFileMapper.class); </span></li>
<li><span>        job.setReducerClass(com.test.hfile.HBaseHFileReducer.class); </span></li>
<li class="alt"><span> </span></li>
<li><span>        job.setMapOutputKeyClass(ImmutableBytesWritable.class); </span></li>
<li class="alt"><span>        job.setMapOutputValueClass(Text.class); 

</span></li>
<li><span>        // 偷懒， 直接写死在程序里了，实际应用中不能这样, 应从命令行获取</span></li>
<li class="alt"><span>        FileInputFormat.addInputPath(job, new Path("<span style="color:#ff0000;">/home/yinjie/input</span>")); </span></li>
<li><span>        FileOutputFormat.setOutputPath(job, new Path("<span style="color:#ff0000;">/home/yinjie/output</span>")); </span></li>
<li class="alt"><span> </span></li>
<li><span>        Configuration <span class="attribute">HBASE_CONFIG</span><span> = </span><span class="attribute-value">new</span><span> Configuration(); </span></span></li>
<li class="alt"><span>        HBASE_CONFIG.set("hbase.zookeeper.quorum", "localhost"); </span></li>
<li><span>        HBASE_CONFIG.set("hbase.zookeeper.property.clientPort", "2181"); </span></li>
<li class="alt"><span>        HBaseConfiguration <span class="attribute">cfg</span><span> = </span><span class="attribute-value">new</span><span> HBaseConfiguration(HBASE_CONFIG); </span></span></li>
<li><span>        String <span class="attribute">tableName</span><span> = </span><span class="attribute-value">"t1"</span><span>; </span></span></li>
<li class="alt"><span>        HTable <span class="attribute">htable</span><span> = </span><span class="attribute-value">new</span><span> HTable(cfg, tableName); </span></span></li>
<li><span>        HFileOutputFormat.configureIncrementalLoad(job, htable); </span></li>
<li class="alt"><span> </span></li>
<li><span>        System.exit(job.waitForCompletion(true) ? 0 : 1); </span></li>
<li class="alt"><span>    } </span></li>
<li><span>} </span></li>
</ol>
<p><span class="Apple-style-span" style="font-size:12px;color:#555555;line-height:15px;font-family:'Courier New', monospace;background-color:#f7f7f7;">/home/yinjie/input目录下有一个hbasedata.txt文件,内容为 </span></p>
<pre></pre><ol class="dp-xml"><li class="alt"><span><span>[root@localhost input]# cat hbasedata.txt  </span></span></li>
<li><span>r1:f1:c1:value1 </span></li>
<li class="alt"><span>r2:f1:c2:value2 </span></li>
<li><span>r3:f1:c3:value3 </span></li>
</ol>
<p>将作业打包，我的到处路径为/home/yinjie/job/hbasetest.jar<br>提交作业到hadoop运行:</p>
<pre></pre><ol class="dp-xml"><li class="alt"><span><span>[root@localhost job]# hadoop jar /home/yinjie/job/hbasetest.jar com.test.hfile.HbaseHFileDriver -libjars /home/yinjie/hbase-0.90.3/hbase-0.90.3.jar </span></span></li></ol>
<p>作业运行完毕后查看下输出目录:</p>
<pre></pre><ol class="dp-xml"><li class="alt"><span><span>[root@localhost input]# hadoop fs -ls /home/yinjie/output </span></span></li>
<li><span>Found 2 items </span></li>
<li class="alt"><span>drwxr-xr-x   - root supergroup          0 2011-08-28 21:02 /home/yinjie/output/_logs </span></li>
<li><span>drwxr-xr-x   - root supergroup          0 2011-08-28 21:03 /home/yinjie/output/f1 </span></li>
</ol>
<p>OK, 已经生成以列族f1命名的文件夹了。<br>接下去使用Bulk Load将数据导入到HBbase</p>
<pre></pre><ol class="dp-xml"><li class="alt"><span><span>[root@localhost job]# hadoop jar /home/yinjie/hbase-0.90.3/hbase-0.90.3.jar completebulkload /home/yinjie/output t1 </span></span></li></ol>
<p>导入完毕，查询hbase表t1进行验证</p>
<pre></pre><ol class="dp-xml"><li class="alt"><span><span>hbase(main):166:0</span><span class="tag">&gt;</span><span> scan 't1' </span></span></li>
<li><span>ROW                              COLUMN+CELL                                                                                  </span></li>
<li class="alt"><span> r1                              <span class="attribute">column</span><span>=</span><span class="attribute-value">f1</span><span>:c1, </span><span class="attribute">timestamp</span><span>=</span><span class="attribute-value">1314591150788</span><span>, </span><span class="attribute">value</span><span>=</span><span class="attribute-value">value1</span><span>                                          </span></span></li>
<li><span> r2                              <span class="attribute">column</span><span>=</span><span class="attribute-value">f1</span><span>:c2, </span><span class="attribute">timestamp</span><span>=</span><span class="attribute-value">1314591150814</span><span>, </span><span class="attribute">value</span><span>=</span><span class="attribute-value">value2</span><span>                                          </span></span></li>
<li class="alt"><span> r3                              <span class="attribute">column</span><span>=</span><span class="attribute-value">f1</span><span>:c3, </span><span class="attribute">timestamp</span><span>=</span><span class="attribute-value">1314591150815</span><span>, </span><span class="attribute">value</span><span>=</span><span class="attribute-value">value3</span><span>                                          </span></span></li>
<li><span>3 row(s) in 0.0210 seconds </span></li>
</ol>
<p>数据已经导入!<br> </p>
<p>本文出自 “<a href="http://3199782.blog.51cto.com/" rel="nofollow">炽天使</a>” 博客，请务必保留此出处<a href="http://3199782.blog.51cto.com/3189782/652244" rel="nofollow">http://3199782.blog.51cto.com/3189782/652244</a></p>            </div>
                </div>