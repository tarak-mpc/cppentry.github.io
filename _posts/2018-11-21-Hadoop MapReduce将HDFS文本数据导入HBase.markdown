---
layout:     post
title:      Hadoop MapReduce将HDFS文本数据导入HBase
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/lan12334321234/article/details/70049932				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <span style="color:#FF0000;"><strong>HBase本身提供了很多种数据导入的方式</strong></span>，通常有两种常用方式：
<br><br><span style="font-size:medium;"><span style="color:#FF0000;"><strong>1.使用HBase提供的TableOutputFormat，原理是通过一个Mapreduce作业将数据导入HBase</strong></span></span>
<br><span style="font-size:medium;"><span style="color:#0000FF;"><strong>2.另一种方式就是使用HBase原生Client API</strong></span></span>
<br>本文就是示范如何通过MapReduce作业从一个文件读取数据并写入到HBase中。
<br><br>首先启动Hadoop与HBase，然后创建一个空表，用于后面导入数据：
<br><br><pre class="command">
hbase(main):006:0&gt; create 'mytable','cf'
0 row(s) in 10.8310 seconds

=&gt; Hbase::Table - mytable
hbase(main):007:0&gt; list
TABLE                                                                                                   
mytable                                                                                                 
1 row(s) in 0.1220 seconds

=&gt; ["mytable"]
hbase(main):008:0&gt; scan 'mytable'
ROW                         COLUMN+CELL                                                                 
0 row(s) in 0.2130 seconds
</pre>
<br><br><span style="font-size:large;"><strong>一、示例程序</strong></span>
<br><br>下面的示例程序通过 <span style="font-size:medium;"><span style="color:#FF0000;"><strong>TableOutputFormat</strong></span></span> 将HDFS上具有一定格式的文本数据导入到HBase中。
<br><br>首先创建MapReduce作业，目录结构如下：
<br><br><pre class="txt">Hdfs2HBase/
├── classes
└── src
    ├── Hdfs2HBase.java
    ├── Hdfs2HBaseMapper.java
    └── Hdfs2HBaseReducer.java</pre>
<br><br><span style="color:#FF0000;"><strong>Hdfs2HBaseMapper.java</strong></span>
<br><br><pre><code class="language-java">
package com.lisong.hdfs2hbase;

import java.io.IOException;

import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Mapper;

public class Hdfs2HBaseMapper extends Mapper&lt;LongWritable, Text, Text, Text&gt; {
        public void map(LongWritable key, Text line, Context context) throws IOException,InterruptedException {
                String lineStr = line.toString();
                int index = lineStr.indexOf(":");
                String rowkey = lineStr.substring(0, index);
                String left = lineStr.substring(index+1);
                context.write(new Text(rowkey), new Text(left));
        }
}
</code></pre>
<br><br><strong>Hdfs2HBaseReducer.java</strong>
<br><br><pre><code class="language-java">
package com.lisong.hdfs2hbase;

import java.io.IOException;

import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.hbase.io.ImmutableBytesWritable;
import org.apache.hadoop.hbase.client.Put;
import org.apache.hadoop.hbase.util.Bytes;

public class Hdfs2HBaseReducer extends Reducer&lt;Text, Text, ImmutableBytesWritable, Put&gt; {
        public void reduce(Text rowkey, Iterable&lt;Text&gt; value, Context context) throws IOException,InterruptedException {
                String k = rowkey.toString();
                for(Text val : value) {
// 设置行键值
                        Put put = new Put(k.getBytes());
                        String[] strs = val.toString().split(":");
                        String family = strs[0];
                        String qualifier = strs[1];
                        String v = strs[2];
// 设置列簇、列名和列值
                        put.add(family.getBytes(), qualifier.getBytes(), v.getBytes());
                        context.write(new ImmutableBytesWritable(k.getBytes()), put);
                }
        }
}
</code></pre>
<br><br><strong>Hdfs2HBase.java</strong>
<br><br><pre><code class="language-java">
package com.lisong.hdfs2hbase;

import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.hbase.io.ImmutableBytesWritable;
import org.apache.hadoop.hbase.client.Put;
import org.apache.hadoop.hbase.mapreduce.TableOutputFormat;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.util.GenericOptionsParser;

public class Hdfs2HBase {
	public static void main(String[] args) throws Exception {
		Configuration conf = new Configuration();
		String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
		if(otherArgs.length != 2) {
			System.err.println("Usage: wordcount &lt;infile&gt; &lt;table&gt;");
			System.exit(2);
		}
		
		Job job = new Job(conf, "hdfs2hbase");
		job.setJarByClass(Hdfs2HBase.class);
		job.setMapperClass(Hdfs2HBaseMapper.class);
		job.setReducerClass(Hdfs2HBaseReducer.class);
		
		job.setMapOutputKeyClass(Text.class);    // +
		job.setMapOutputValueClass(Text.class);  // +
	
		job.setOutputKeyClass(ImmutableBytesWritable.class);
		job.setOutputValueClass(Put.class);
// 以表输出的格式
		job.setOutputFormatClass(TableOutputFormat.class);
		
		FileInputFormat.addInputPath(job, new Path(otherArgs[0]));

		job.getConfiguration().set(TableOutputFormat.OUTPUT_TABLE, otherArgs[1]);
		
		System.exit(job.waitForCompletion(true)?0:1);
	}
}
</code></pre>
<br>编译
<br><br><pre class="command">$ javac -d classes/ src/*.java</pre>
<br>打包
<br><br><pre class="command">$ jar -cvf hdfs2hbase.jar classes</pre>
<br>运行
<br><br>创建一个 <span style="color:#FF0000;"><strong>data.txt</strong></span> 文件，内容如下（列族是建表时创建的列族 cf ）：
<br><br><pre class="txt">
r1:cf:c1:value1 
r2:cf:c2:value2 
r3:cf:c3:value3
</pre>
<br><br><strong>将文件复制到hdfs上：</strong>
<br><br><pre class="command">
$ hadoop/bin/hadoop fs -put data.txt /hbase
</pre>
<br><br>把HBase的jar包加到 hadoop-env.sh 中。
<br><pre class="command">
TEMP=`ls /home/hadoop/hbase/lib/*.jar`
HBASE_JARS=`echo $TEMP | sed 's/ /:/g'`
HADOOP_CLASSPATH=$HBASE_JARS
</pre>
<br>运行MapReduce作业：
<br><br><pre class="command">
$ hadoop/bin/hadoop jar Hdfs2HBase/hdfs2hbase.jar com.lisong.hdfs2hbase.Hdfs2HBase /hbase/data.txt mytable
</pre>
<br><br>查询HBase表，验证数据是否已导入：
<br><br><pre class="command">
hbase(main):001:0&gt; scan 'mytable'
ROW                         COLUMN+CELL                                                                 
 r1                         column=cf:c1, timestamp=1439223857492, value=value1                         
 r2                         column=cf:c2, timestamp=1439223857492, value=value2                         
 r3                         column=cf:c3, timestamp=1439223857492, value=value3                         
3 row(s) in 1.3820 seconds
</pre>
<br><br>可以看到，数据导入成功！
<br><br><span style="color:#FF0000;"><strong>由于需要频繁的与存储数据的RegionServer通信，占用资源较大，一次性入库大量数据时，TableOutputFormat效率并不好。
<br></strong></span>
<br><br><span style="font-size:large;"><strong>二、拓展-TableReducer</strong></span>
<br><br>我们可以将 Hdfs2HBaseReducer.java 代码改成下面这样，作用是一样的：
<br><br><pre><code class="language-java">
package com.lisong.hdfs2hbase;

import java.io.IOException;

import org.apache.hadoop.io.Text;
import org.apache.hadoop.io.Writable;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.hbase.mapreduce.TableReducer;
import org.apache.hadoop.hbase.io.ImmutableBytesWritable;
import org.apache.hadoop.hbase.client.Put;
import org.apache.hadoop.hbase.util.Bytes;

public class Hdfs2HBaseReducer extends TableReducer&lt;Text, Text, ImmutableBytesWritable&gt; {
	public void reduce(Text rowkey, Iterable&lt;Text&gt; value, Context context) throws IOException,InterruptedException {
		String k = rowkey.toString();
		for(Text val : value) {
			Put put = new Put(k.getBytes());
			String[] strs = val.toString().split(":");
			String family = strs[0];
			String qualifier = strs[1];
			String v = strs[2];
			put.add(family.getBytes(), qualifier.getBytes(), v.getBytes());
			context.write(new ImmutableBytesWritable(k.getBytes()), put);
		}
	}
}
</code></pre>
<br><br>这里直接<span style="color:#FF0000;"><strong>继承了 TableReducer</strong></span> ， TableReducer是部分特例化的 Reducer ，它只有三个类型参数：输入Key/Value是对应Mapper的输出，输出Key可以是任意的类型，但是输出Value必须是一个 Put 或 Delete 实例。
<br><br>转自：<a href="http://www.tuicool.com/articles/jInQ3y2" rel="nofollow">http://www.tuicool.com/articles/jInQ3y2</a>
<br>            </div>
                </div>