---
layout:     post
title:      Spark学习笔记-HBase操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/wulinshishen/article/details/38437647				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:18px;">Scala版本操作HBase</span></p>
<pre><code class="language-plain">import org.apache.spark.SparkContext
import org.apache.spark._
import org.apache.hadoop.hbase.HBaseConfiguration
import org.apache.hadoop.hbase.HTableDescriptor
import org.apache.hadoop.hbase.client.HBaseAdmin
import org.apache.hadoop.hbase.mapreduce.TableInputFormat
import org.apache.hadoop.hbase.HColumnDescriptor
import org.apache.hadoop.hbase.util.Bytes
import org.apache.hadoop.hbase.client.Put
import org.apache.hadoop.hbase.client.HTable
import org.apache.hadoop.hbase.client.Result
import org.apache.hadoop.hbase.io.ImmutableBytesWritable
import org.apache.hadoop.hbase.client.Delete

object SparkHBase1 extends Serializable {  
  
  def main(args: Array[String]) {  
    val sc = new SparkContext("spark://centos.host1:7077", "SparkHBase")  
  
    val conf = HBaseConfiguration.create()  
    conf.set("hbase.zookeeper.property.clientPort", "2181")
    conf.set("hbase.zookeeper.quorum", "centos.host1")
    conf.set("hbase.master", "centos.host1:60000") 
    conf.addResource("/home/hadoop/software/hbase-0.92.2/conf/hbase-site.xml")  
    conf.set(TableInputFormat.INPUT_TABLE, "user")  
  
    val admin = new HBaseAdmin(conf)  
    if (!admin.isTableAvailable("test")) {  
      print("Table Not Exists! Create Table")  
      val tableDesc = new HTableDescriptor("test")  
      tableDesc.addFamily(new HColumnDescriptor("basic".getBytes()))
      admin.createTable(tableDesc)  
    }  
  
    //Put操作
    val table = new HTable(conf, "user");  
    for (i &lt;- 1 to 5) {  
      var put = new Put(Bytes.toBytes("row" + i)) 
      put.add(Bytes.toBytes("basic"), Bytes.toBytes("name"), Bytes.toBytes("value " + i))
      table.put(put) 
    }  
    table.flushCommits()
    
    //Delete操作
    val delete = new Delete(Bytes.toBytes("row1"))
    table.delete(delete)
  
    //Scan操作
    val hbaseRDD = sc.newAPIHadoopRDD(conf, classOf[TableInputFormat],  
      classOf[org.apache.hadoop.hbase.io.ImmutableBytesWritable],  
      classOf[org.apache.hadoop.hbase.client.Result])  
  
    val count = hbaseRDD.count()  
    println("HBase RDD Count:" + count)  
    hbaseRDD.cache()  
  
    val res = hbaseRDD.take(count.toInt)  
    for (j &lt;- 1 until count.toInt) {  
      println("j: " + j)  
      var rs = res(j - 1)._2  
      var kvs = rs.raw  
      for (kv &lt;- kvs)  
        println("rowkey:" + new String(kv.getRow()) +  
          " cf:" + new String(kv.getFamily()) +  
          " column:" + new String(kv.getQualifier()) +  
          " value:" + new String(kv.getValue()))  
    }  
    System.exit(0)  
  }  
}  </code></pre>
<p><br></p>
<p><span style="font-size:18px;">Java版本操作HBase</span><br></p>
<p><span style="font-size:18px;"></span></p>
<p></p>
<pre><code class="language-java">import java.io.ByteArrayOutputStream;  
import java.io.DataOutputStream;  
import java.util.List;  

import org.apache.hadoop.conf.Configuration;  
import org.apache.hadoop.hbase.HBaseConfiguration;  
import org.apache.hadoop.hbase.KeyValue;  
import org.apache.hadoop.hbase.client.Delete;
import org.apache.hadoop.hbase.client.HTable;  
import org.apache.hadoop.hbase.client.Put;  
import org.apache.hadoop.hbase.client.Result;  
import org.apache.hadoop.hbase.client.Scan;  
import org.apache.hadoop.hbase.io.ImmutableBytesWritable;  
import org.apache.hadoop.hbase.mapreduce.TableInputFormat;  
import org.apache.hadoop.hbase.util.Base64;  
import org.apache.hadoop.hbase.util.Bytes;  
import org.apache.solr.util.IOUtils;  
import org.apache.spark.SparkConf;  
import org.apache.spark.api.java.JavaPairRDD;  
import org.apache.spark.api.java.JavaSparkContext;  

import scala.Tuple2;  

public class SparkHBase {

	public static void main(String[] args) throws Exception {  
        SparkConf sparkConf = new SparkConf();  
        sparkConf.setMaster("spark://centos.host1:7077");  
        sparkConf.setAppName("Spark HBase");  
        JavaSparkContext context = new JavaSparkContext(sparkConf);  
  
        Configuration conf = HBaseConfiguration.create();  
        conf.set("hbase.zookeeper.property.clientPort", "2181");  
        conf.set("hbase.zookeeper.quorum", "centos.host1");  
        conf.set("hbase.master", "centos.host1:60000");  
        
        //Put操作
        HTable table = new HTable(conf, "user");  
        Put put = new Put(Bytes.toBytes("row6"));  
        put.add(Bytes.toBytes("basic"), Bytes.toBytes("name"), Bytes.toBytes("value6"));  
        table.put(put);  
        table.flushCommits();  
        
        //Delete操作
        Delete delete = new Delete(Bytes.toBytes("row1"));
        table.delete(delete);
        
        table.close();  
        
        //Scan操作
        Scan scan = new Scan();  
        scan.setStartRow(Bytes.toBytes("0120140722"));  
        scan.setStopRow(Bytes.toBytes("1620140728"));  
        scan.addFamily(Bytes.toBytes("basic"));  
        scan.addColumn(Bytes.toBytes("basic"), Bytes.toBytes("name"));  
  
        String tableName = "user";  
        conf.set(TableInputFormat.INPUT_TABLE, tableName);  
  
        ByteArrayOutputStream out = new ByteArrayOutputStream();  
        DataOutputStream dos = new DataOutputStream(out);  
        scan.write(dos);  
        String scanStr = Base64.encodeBytes(out.toByteArray());  
        IOUtils.closeQuietly(dos);  
        IOUtils.closeQuietly(out);  
        //高版本可以用如下方式：  
        //ClientProtos.Scan proto = ProtobufUtil.toScan(scan);  
        //String scanStr = Base64.encodeBytes(proto.toByteArray());  
        conf.set(TableInputFormat.SCAN, scanStr);  
  
        JavaPairRDD&lt;ImmutableBytesWritable, Result&gt; hBaseRDD = context  
                .newAPIHadoopRDD(conf, TableInputFormat.class,  
                        ImmutableBytesWritable.class, Result.class);  
  
        Long count = hBaseRDD.count();  
        System.out.println("count: " + count);  
  
        List&lt;Tuple2&lt;ImmutableBytesWritable, Result&gt;&gt; tuples = hBaseRDD  
                .take(count.intValue());  
        for (int i = 0, len = count.intValue(); i &lt; len; i++) {  
            Result result = tuples.get(i)._2();  
            KeyValue[] kvs = result.raw();  
            for (KeyValue kv : kvs) {  
                System.out.println("rowkey:" + new String(kv.getRow()) + " cf:"  
                        + new String(kv.getFamily()) + " column:"  
                        + new String(kv.getQualifier()) + " value:"  
                        + new String(kv.getValue()));  
            }  
        }  
    }  
}</code></pre>
<p></p>
<p><br></p>
打包成jar包后执行如下命令
<p></p>
<p><span style="font-size:18px;"></span></p>
<pre><code class="language-javascript">bin/spark-submit --class org.platform.modules.hbase.SparkHBase --master spark://centos.host1:7077 /home/hadoop/temp/spark.jar</code></pre><br>
注意需要将依赖的HBase jar包添加到Spark的CLASSPATH下，打开conf/spark-env.sh，添加如下内容
<p></p>
<p><span style="font-size:18px;"></span></p>
<pre><code class="language-javascript">SPARK_CLASSPATH=/home/hadoop/software/hbase-0.92.2/hbase-0.92.2.jar:/home/hadoop/software/hbase-0.92.2/lib</code></pre>
<p></p>
<p>或者也可以在执行命令的时候加一个jars参数，如下</p>
<p></p>
<pre><code class="language-javascript">bin/spark-submit --jars $(echo /home/hadoop/software/hbase-0.96.0/lib/*.jar | tr ' ' ',') --class org.platform.modules.hbase.SparkHBase --master spark://centos.host1:7077 /home/hadoop/temp/spark.jar</code></pre><br><br><p><br></p>
            </div>
                </div>