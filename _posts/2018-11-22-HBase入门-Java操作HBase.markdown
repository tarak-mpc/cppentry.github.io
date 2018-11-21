---
layout:     post
title:      HBase入门-Java操作HBase
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p class="artdir1" style="text-indent:20pt;line-height:20px;font-weight:bold;"><span style="font-family:'Comic Sans MS';font-size:14px;">Java操作HBase例子<a name="2"></a></span></p>
<p class="artcon" style="text-indent:2em;line-height:20px;"><span style="font-family:'Comic Sans MS';font-size:14px;">本篇文章讲述用HBase Shell命令 和 HBase Java API 对HBase 服务器 进行操作。在此之前需要对HBase的总体上有个大概的了解。比如说HBase服务器内部由哪些主要部件构成？HBase的内部工作原理是什么？我想学习任何一项知识、技术的态度不能只是知道如何使用，对产品的内部构建一点都不去关心，那样出了问题，很难让你很快的找到答案，甚至我们希望最后能对该项技术的领悟出自己的心得，为我所用，借鉴该项技术其中的设计思想创造出自己的解决方案，更灵活的去应对多变的计算场景与架构设计。以我目前的对HBase的了解还不够深入，将来不断的学习，我会把我所知道的点滴分享到这个Blog上。</span></p>
<p class="artcon" style="text-indent:2em;line-height:20px;"><span style="font-family:'Comic Sans MS';font-size:14px;">先来看一下读取一行记录HBase是如何进行工作的，首先HBase Client端会连接Zookeeper Qurom(从下面的代码也能看出来，例如：HBASE_CONFIG.set("hbase.zookeeper.quorum", "192.168.50.216") )。通过Zookeeper组件Client能获知哪个Server管理-ROOT-
 Region。那么Client就去访问管理-ROOT-的Server，在META中记录了HBase中所有表信息，(你可以使用 scan '.META.' 命令列出你创建的所有表的详细信息),从而获取Region分布的信息。一旦Client获取了这一行的位置信息，比如这一行属于哪个Region，Client将会缓存这个信息并直接访问HRegionServer。久而久之Client缓存的信息渐渐增多，即使不访问.META.表也能知道去访问哪个HRegionServer。HBase中包含两种基本类型的文件，一种用于存储WAL的log，另一种用于存储具体的数据，这些数据都通过DFS
 Client和分布式的文件系统HDFS进行交互实现存储。</span></p>
<p class="artcon" style="text-indent:2em;line-height:20px;"><span style="font-family:'Comic Sans MS';font-size:14px;">如图所示：</span></p>
<p align="center" class="artcon" style="text-indent:2em;line-height:20px;"><span style="font-family:'Comic Sans MS';font-size:14px;"><img src="http://www.uml.org.cn/sjjm/images/2012121417.jpg" alt=""></span></p>
<p class="artcon" style="text-indent:2em;line-height:20px;"><span style="font-family:'Comic Sans MS';font-size:14px;">再来看看HBase的一些内存实现原理：</span></p>
<ul><li><span style="font-family:'Comic Sans MS';font-size:14px;">HMaster— HBase中仅有一个Master server。</span></li><li><span style="font-family:'Comic Sans MS';font-size:14px;">HRegionServer—负责多个HRegion使之能向client端提供服务，在HBase cluster中会存在多个HRegionServer。</span></li><li><span style="font-family:'Comic Sans MS';font-size:14px;">ServerManager—负责管理Region server信息，如每个Region server的HServerInfo(这个对象包含HServerAddress和startCode),已load Region个数，死亡的Region server列表</span></li><li><span style="font-family:'Comic Sans MS';font-size:14px;">RegionManager—负责将region分配到region server的具体工作，还监视root和meta 这2个系统级的region状态。</span></li><li><span style="font-family:'Comic Sans MS';font-size:14px;">RootScanner—定期扫描root region，以发现没有分配的meta region。</span></li><li><span style="font-family:'Comic Sans MS';font-size:14px;">MetaScanner—定期扫描meta region,以发现没有分配的user region。</span></li></ul><p class="artcon" style="text-indent:2em;line-height:20px;"><strong><span style="font-family:'Comic Sans MS';font-size:14px;">HBase基本命令</span></strong></p>
<p class="artcon" style="text-indent:2em;line-height:20px;"><span style="font-family:'Comic Sans MS';font-size:14px;">下面我们再看看看HBase的一些基本操作命令，我列出了几个常用的HBase Shell命令，如下：</span></p>
<table width="674" border="0" align="center" bgcolor="#666666" class="content" style="color:rgb(0,0,0);line-height:20px;"><tbody><tr bgcolor="#FFFFFF" valign="top"><td>
<div align="center"><span style="font-family:'Comic Sans MS';font-size:14px;">名称</span></div>
</td>
<td colspan="2">
<div align="center"><span style="font-family:'Comic Sans MS';font-size:14px;">命令表达式</span></div>
</td>
</tr><tr bgcolor="#FFFFFF" valign="top"><td><span style="font-family:'Comic Sans MS';font-size:14px;">创建表</span></td>
<td colspan="2"><span style="font-family:'Comic Sans MS';font-size:14px;">create '表名称', '列名称1','列名称2','列名称N'</span></td>
</tr><tr bgcolor="#FFFFFF" valign="top"><td><span style="font-family:'Comic Sans MS';font-size:14px;">添加记录</span></td>
<td colspan="2"><span style="font-family:'Comic Sans MS';font-size:14px;">put '表名称', '行名称', '列名称:', '值'</span></td>
</tr><tr bgcolor="#FFFFFF" valign="top"><td><span style="font-family:'Comic Sans MS';font-size:14px;">查看记录</span></td>
<td colspan="2"><span style="font-family:'Comic Sans MS';font-size:14px;">get '表名称', '行名称'</span></td>
</tr><tr bgcolor="#FFFFFF" valign="top"><td><span style="font-family:'Comic Sans MS';font-size:14px;">查看表中的记录总数</span></td>
<td colspan="2"><span style="font-family:'Comic Sans MS';font-size:14px;">count '表名称'</span></td>
</tr><tr bgcolor="#FFFFFF" valign="top"><td><span style="font-family:'Comic Sans MS';font-size:14px;">删除记录</span></td>
<td colspan="2"><span style="font-family:'Comic Sans MS';font-size:14px;">delete '表名' ,'行名称' , '列名称'</span></td>
</tr><tr bgcolor="#FFFFFF" valign="top"><td><span style="font-family:'Comic Sans MS';font-size:14px;">删除一张表</span></td>
<td colspan="2"><span style="font-family:'Comic Sans MS';font-size:14px;">先要屏蔽该表，才能对该表进行删除，第一步 disable '表名称' 第二步 drop '表名称'</span></td>
</tr><tr bgcolor="#FFFFFF" valign="top"><td><span style="font-family:'Comic Sans MS';font-size:14px;">查看所有记录</span></td>
<td colspan="2"><span style="font-family:'Comic Sans MS';font-size:14px;">scan "表名称"</span></td>
</tr><tr bgcolor="#FFFFFF" valign="top"><td><span style="font-family:'Comic Sans MS';font-size:14px;">查看某个表某个列中所有数据</span></td>
<td colspan="2"><span style="font-family:'Comic Sans MS';font-size:14px;">scan "表名称" , ['列名称:']</span></td>
</tr><tr bgcolor="#FFFFFF" valign="top"><td><span style="font-family:'Comic Sans MS';font-size:14px;">更新记录</span></td>
<td colspan="2"><span style="font-family:'Comic Sans MS';font-size:14px;">就是重写一遍进行覆盖</span></td>
</tr></tbody></table><p class="artcon" style="text-indent:2em;line-height:20px;"><span style="font-family:'Comic Sans MS';font-size:14px;">如果你是一个新手队HBase的一些命令还不算非常熟悉的话，你可以进入 hbase 的shell 模式中你可以输入 help 命令查看到你可以执行的命令和对该命令的说明，例如对scan这个命令，help中不仅仅提到有这个命令，还详细的说明了scan命令中可以使用的参数和作用，例如，根据列名称查询的方法和带LIMIT
 、STARTROW的使用方法：</span></p>
<table width="700" border="0" align="center" bgcolor="#666666" class="content" style="color:rgb(0,0,0);line-height:20px;"><tbody><tr bgcolor="#FFFFFF" valign="top"><td colspan="3" bgcolor="#CCCCCC">
<pre><span style="font-family:'Comic Sans MS';font-size:14px;"> scan   Scan a table; pass table name and optionally a dictionary of scanner specifications.  
 Scanner specifications may include one or more of  the following: LIMIT, STARTROW, STOPROW, TIMESTAMP, or COLUMNS.
 If no columns are specified, all columns will be scanned.  To scan all members of a column family, leave the 
 qualifier empty as in  'col_family:'.  Examples:
   hbase&gt; scan '.META.'
   hbase&gt; scan '.META.', {COLUMNS =&gt; 'info:regioninfo'}
   hbase&gt; scan 't1', {COLUMNS =&gt; ['c1', 'c2'], LIMIT =&gt; 10, STARTROW =&gt; 'xyz'}</span></pre>
</td>
</tr></tbody></table><p class="artcon" style="text-indent:2em;line-height:20px;"><strong><span style="font-family:'Comic Sans MS';font-size:14px;">使用Java API对HBase服务器进行操作</span></strong></p>
<p class="artcon" style="text-indent:2em;line-height:20px;"><span style="font-family:'Comic Sans MS';font-size:14px;">需要下列jar包</span></p>
<table width="700" border="0" align="center" bgcolor="#666666" class="content" style="color:rgb(0,0,0);line-height:20px;"><tbody><tr bgcolor="#FFFFFF" valign="top"><td bgcolor="#CCCCCC">
<pre><span style="font-family:'Comic Sans MS';font-size:14px;">     hbase-0.20.6.jar
      hadoop-core-0.20.1.jar
      commons-logging-1.1.1.jar
      zookeeper-3.3.0.jar
      log4j-1.2.91.jar
 
import org.apache.hadoop.conf.Configuration;
 import org.apache.hadoop.hbase.HBaseConfiguration;
 import org.apache.hadoop.hbase.HColumnDescriptor;
 import org.apache.hadoop.hbase.HTableDescriptor;
 import org.apache.hadoop.hbase.KeyValue;
 import org.apache.hadoop.hbase.client.HBaseAdmin;
 import org.apache.hadoop.hbase.client.HTable;
 import org.apache.hadoop.hbase.client.Result;
 import org.apache.hadoop.hbase.client.ResultScanner;
 import org.apache.hadoop.hbase.client.Scan;
 import org.apache.hadoop.hbase.io.BatchUpdate;
 
@SuppressWarnings("deprecation")
 public class HBaseTestCase {
     
    static HBaseConfiguration cfg = null;
     static {
         Configuration HBASE_CONFIG = new Configuration();
         HBASE_CONFIG.set("hbase.zookeeper.quorum", "192.168.50.216");
         HBASE_CONFIG.set("hbase.zookeeper.property.clientPort", "2181");
         cfg = new HBaseConfiguration(HBASE_CONFIG);
     }
    
    /**
      * 创建一张表
     */
     public static void creatTable(String tablename) throws Exception {
         HBaseAdmin admin = new HBaseAdmin(cfg);
         if (admin.tableExists(tablename)) {
             System.out.println("table   Exists!!!");
         }
         else{
             HTableDescriptor tableDesc = new HTableDescriptor(tablename);
             tableDesc.addFamily(new HColumnDescriptor("name:"));
             admin.createTable(tableDesc);
             System.out.println("create table ok .");
         }
         
     }
     
    /**
      * 添加一条数据
     */
     public static void addData (String tablename) throws Exception{
          HTable table = new HTable(cfg, tablename);
              BatchUpdate update = new BatchUpdate("Huangyi");  
             update.put("name:java", "http://www.javabloger.com".getBytes());  
             table.commit(update);  
         System.out.println("add data ok .");
     }
     
    /**
      * 显示所有数据
     */
     public static void getAllData (String tablename) throws Exception{
          HTable table = new HTable(cfg, tablename);
          Scan s = new Scan();
          ResultScanner ss = table.getScanner(s);
          for(Result r:ss){
              for(KeyValue kv:r.raw()){
                 System.out.print(new String(kv.getColumn()));
                 System.out.println(new String(kv.getValue()    ));
              }
 
         }
     }
     
    
    public static void  main (String [] agrs) {
         try {
                 String tablename="tablename";
                 HBaseTestCase.creatTable(tablename);
                 HBaseTestCase.addData(tablename);
                 HBaseTestCase.getAllData(tablename);
             } 
        catch (Exception e) {
             e.printStackTrace();
         }
         
    }
     
} </span></pre>
<div><span style="font-family:'Comic Sans MS';font-size:14px;"><br></span></div>
</td>
</tr></tbody></table>            </div>
                </div>