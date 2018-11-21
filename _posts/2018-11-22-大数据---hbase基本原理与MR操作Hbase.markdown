---
layout:     post
title:      大数据---hbase基本原理与MR操作Hbase
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>一、基本原理 <br>
　　1.hbase的位置</p>

<p>　　上图描述了Hadoop 2.0生态系统中的各层结构。其中HBase位于结构化存储层，HDFS为HBase提供了高可靠性的底层存储支持， MapReduce为HBase提供了高性能的批处理能力，Zookeeper为HBase提供了稳定服务和failover机制，Pig和Hive为HBase提供了进行数据统计处理的高层语言支持，Sqoop则为HBase提供了便捷的RDBMS数据导入功能，使业务数据从传统数据库向HBase迁移变的非常方便。</p>

<p>　　2.体系图</p>

<p>　　体系图中各个组件的含义，参考：<a href="http://blog.csdn.net/carl810224/article/details/51970039/" rel="nofollow">http://blog.csdn.net/carl810224/article/details/51970039/</a></p>

<p>　　　　　　　　　　　　　　　　　　<a href="https://www.cnblogs.com/qiaoyihang/p/6246424.html" rel="nofollow">https://www.cnblogs.com/qiaoyihang/p/6246424.html</a></p>

<p>　　3.基本流程　</p>

<p>　　1. 写流程</p>

<p>　　　　1、 client向hregionserver发送写请求。</p>

<p>　　　　2、 hregionserver将数据写到hlog（write ahead log）。为了数据的持久化和恢复。</p>

<p>　　　　3、 hregionserver将数据写到内存（memstore）</p>

<p>　　　　4、 反馈client写成功。</p>

<p>　　2. 数据flush过程</p>

<p>　　　　1、 当memstore数据达到阈值（老版本默认是64M），将数据刷到硬盘，将内存中的数据删除，同时删除Hlog中的历史数据。</p>

<p>　　　　2、 并将数据存储到hdfs中。</p>

<p>　　　　3、 在hlog中做标记点。</p>

<p>　　3. 数据合并过程</p>

<p>　　　　1、 当数据块达到4块，hmaster将数据块加载到本地，进行合并</p>

<p>　　　　2、 当合并的数据超过256M，进行拆分，将拆分后的region分配给不同的hregionserver管理</p>

<p>　　　　3、 当hregionser宕机后，将hregionserver上的hlog拆分，然后分配给不同的hregionserver加载，修改.META.</p>

<p>　　　　4、 注意：hlog会同步到hdfs</p>

<p>　　4. hbase的读流程</p>

<p>　　　　1、 通过zookeeper和-ROOT- .META.表定位hregionserver。</p>

<p>　　　　2、 数据从内存和硬盘合并后返回给client</p>

<p>　　　　3、 数据块会缓存</p>

<p>二、MR操作 <br>
　　 1.实现方法　</p>

<p>　　　　Hbase对MapReduce提供支持，它实现了TableMapper类和TableReducer类，我们只需要继承这两个类即可。</p>

<p>　　2.准备相关表　　</p>

<p>　　　　1、建立数据来源表‘word’，包含一个列族‘content’</p>

<p>　　　　　　向表中添加数据，在列族中放入列‘info’，并将短文数据放入该列中，如此插入多行，行键为不同的数据即可</p>

<p>　　　　2、建立输出表‘stat’，包含一个列族‘content’</p>

<p>　　　　3、通过Mr操作Hbase的‘word’表，对‘content：info’中的短文做词频统计，并将统计结果写入‘stat’表的‘content：info中’，行键为单词</p>

<p>　　3.实现代码</p>

<p>复制代码 <br>
package com.itcast.hbase;</p>

<p>import java.io.IOException; <br>
import java.util.ArrayList; <br>
import java.util.List;</p>

<p>import org.apache.hadoop.conf.Configuration; <br>
import org.apache.hadoop.hbase.HBaseConfiguration; <br>
import org.apache.hadoop.hbase.HColumnDescriptor; <br>
import org.apache.hadoop.hbase.HTableDescriptor; <br>
import org.apache.hadoop.hbase.client.HBaseAdmin; <br>
import org.apache.hadoop.hbase.client.HTable; <br>
import org.apache.hadoop.hbase.client.Put; <br>
import org.apache.hadoop.hbase.client.Result; <br>
import org.apache.hadoop.hbase.client.Scan; <br>
import org.apache.hadoop.hbase.io.ImmutableBytesWritable; <br>
import org.apache.hadoop.hbase.mapreduce.TableMapReduceUtil; <br>
import org.apache.hadoop.hbase.mapreduce.TableMapper; <br>
import org.apache.hadoop.hbase.mapreduce.TableReducer; <br>
import org.apache.hadoop.hbase.util.Bytes; <br>
import org.apache.hadoop.io.IntWritable; <br>
import org.apache.hadoop.io.Text; <br>
import org.apache.hadoop.mapreduce.Job; <br>
/** <br>
 * mapreduce操作hbase <br>
 * @author wilson <br>
 * <br>
 */ <br>
public class HBaseMr { <br>
    /** <br>
     * 创建hbase配置 <br>
     */ <br>
    static Configuration config = null; <br>
    static { <br>
        config = HBaseConfiguration.create(); <br>
        config.set(“hbase.zookeeper.quorum”, “slave1,slave2,slave3”); <br>
        config.set(“hbase.zookeeper.property.clientPort”, “2181”); <br>
    } <br>
    /** <br>
     * 表信息 <br>
     */ <br>
    public static final String tableName = “word”;//表名1 <br>
    public static final String colf = “content”;//列族 <br>
    public static final String col = “info”;//列 <br>
    public static final String tableName2 = “stat”;//表名2 <br>
    /** <br>
     * 初始化表结构，及其数据 <br>
     */ <br>
    public static void initTB() { <br>
        HTable table=null; <br>
        HBaseAdmin admin=null; <br>
        try { <br>
            admin = new HBaseAdmin(config);//创建表管理 <br>
            /<em>删除表</em>/ <br>
            if (admin.tableExists(tableName)||admin.tableExists(tableName2)) { <br>
                System.out.println(“table is already exists!”); <br>
                admin.disableTable(tableName); <br>
                admin.deleteTable(tableName); <br>
                admin.disableTable(tableName2); <br>
                admin.deleteTable(tableName2); <br>
            } <br>
            /<em>创建表</em>/ <br>
                HTableDescriptor desc = new HTableDescriptor(tableName); <br>
                HColumnDescriptor family = new HColumnDescriptor(colf); <br>
                desc.addFamily(family); <br>
                admin.createTable(desc); <br>
                HTableDescriptor desc2 = new HTableDescriptor(tableName2); <br>
                HColumnDescriptor family2 = new HColumnDescriptor(colf); <br>
                desc2.addFamily(family2); <br>
                admin.createTable(desc2); <br>
            /<em>插入数据</em>/ <br>
                table = new HTable(config,tableName); <br>
                table.setAutoFlush(false); <br>
                table.setWriteBufferSize(5); <br>
                List lp = new ArrayList(); <br>
                Put p1 = new Put(Bytes.toBytes(“1”)); <br>
                p1.add(colf.getBytes(), col.getBytes(),    (“The Apache Hadoop software library is a framework”).getBytes()); <br>
                lp.add(p1); <br>
                Put p2 = new Put(Bytes.toBytes(“2”));p2.add(colf.getBytes(),col.getBytes(),(“The common utilities that support the other Hadoop modules”).getBytes()); <br>
                lp.add(p2); <br>
                Put p3 = new Put(Bytes.toBytes(“3”)); <br>
                p3.add(colf.getBytes(), col.getBytes(),(“Hadoop by reading the documentation”).getBytes()); <br>
                lp.add(p3); <br>
                Put p4 = new Put(Bytes.toBytes(“4”)); <br>
                p4.add(colf.getBytes(), col.getBytes(),(“Hadoop from the release page”).getBytes()); <br>
                lp.add(p4); <br>
                Put p5 = new Put(Bytes.toBytes(“5”)); <br>
                p5.add(colf.getBytes(), col.getBytes(),(“Hadoop on the mailing list”).getBytes()); <br>
                lp.add(p5); <br>
                table.put(lp); <br>
                table.flushCommits(); <br>
                lp.clear(); <br>
        } catch (Exception e) { <br>
            e.printStackTrace(); <br>
        } finally { <br>
            try { <br>
                if(table!=null){ <br>
                    table.close(); <br>
                } <br>
            } catch (IOException e) { <br>
                e.printStackTrace(); <br>
            } <br>
        } <br>
    } <br>
    /** <br>
     * MyMapper 继承 TableMapper <br>
     * TableMapper</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>