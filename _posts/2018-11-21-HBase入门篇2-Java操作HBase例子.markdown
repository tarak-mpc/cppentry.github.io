---
layout:     post
title:      HBase入门篇2-Java操作HBase例子
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<span style="font-size:12px;color:#C0C0C0;">网址: <a href="http://www.javabloger.com/article/apache-hbase-shell-and-java-api-html.html" rel="nofollow">
<span style="color:#C0C0C0;">http://www.javabloger.com/article/apache-hbase-shell-and-java-api-html.html</span></a></span><br><br><p>     本篇文章讲述用HBase Shell命令 和 HBase Java API 对HBase 服务器 进行操作。在此之前需要对HBase的总体上有个大概的了解。比如说HBase服务器内部由哪些主要部件构成？HBase的内部工作原理是什么？我想学习任何一项知识、技术的态度不能只是知道如何使用，对产品的内部构建一点都不去关心，那样出了问题，很难让你很快的找到答案，甚至我们希望最后能对该项技术的领悟出自己的心得，为我所用，借鉴该项技术其中的设计思想创造出自己的解决方案，更灵活的去应对多变的计算场景与架构设计。以我目前的对HBase的了解还不够深入，将来不断的学习，我会把我所知道的点滴分享到这个Blog上。</p>
<p>     先来看一下读取一行记录HBase是如何进行工作的，首先HBase Client端会连接Zookeeper Qurom<strong>(从下面的代码也能看出来，例如：HBASE_CONFIG.set("hbase.zookeeper.quorum", "192.168.50.216")
</strong>)。通过Zookeeper组件Client能获知哪个Server管理-ROOT- Region。那么Client就去访问管理-ROOT-的Server，在META中记录了HBase中所有表信息，(你可以使用  scan '.META.' 命令列出你创建的所有表的详细信息),从而获取Region分布的信息。一旦Client获取了这一行的位置信息，比如这一行属于哪个Region，Client将会缓存这个信息并直接访问HRegionServer。久而久之Client缓存的信息渐渐增多，即使不访问.META.表也能知道去访问哪个HRegionServer。HBase中包含两种基本类型的文件，一种用于存储WAL的log，另一种用于存储具体的数据，这些数据都通过DFS
 Client和分布式的文件系统HDFS进行交互实现存储。</p>
<p>如图所示：</p>
<p style="text-align:center;"><a href="http://niaklq.bay.livefilestore.com/y1pX0l7uDaGqyf11KWFupkIkan9-yVosEZOno4HK8qpCdO8NIfbtwrYtckBTf73hsoDphtQ34WLC36HmqWLsHUrkFpMpuflRs_t/HBase-Architecture-1.jpg?psid=1" rel="nofollow"><br><img alt="http://niaklq.bay.livefilestore.com/y1pX0l7uDaGqyf11KWFupkIkan9-yVosEZOwr7jYdmxypoBrpCS3gFLRE1SSVoQKJzdNm5p_xJtDydyp-rvVX6hglK3zHtb_xu3/HBase-Architecture-1.jpg?psid=1" src="http://niaklq.bay.livefilestore.com/y1pX0l7uDaGqyf11KWFupkIkan9-yVosEZOwr7jYdmxypoBrpCS3gFLRE1SSVoQKJzdNm5p_xJtDydyp-rvVX6hglK3zHtb_xu3/HBase-Architecture-1.jpg?psid=1"><br></a></p>
<p><a href="http://niaklq.bay.livefilestore.com/y1pX0l7uDaGqyf11KWFupkIkan9-yVosEZOno4HK8qpCdO8NIfbtwrYtckBTf73hsoDphtQ34WLC36HmqWLsHUrkFpMpuflRs_t/HBase-Architecture-1.jpg?psid=1" rel="nofollow">查看大图请点击这里<br></a></p>
<p>再来看看HBase的一些内存实现原理：    <br>
    * HMaster— HBase中仅有一个Master server。<br>
    * HRegionServer—负责多个HRegion使之能向client端提供服务，在HBase cluster中会存在多个HRegionServer。<br>
    * ServerManager—负责管理Region server信息，如每个Region server的HServerInfo(这个对象包含HServerAddress和startCode),已load Region个数，死亡的Region server列表<br>
    * RegionManager—负责将region分配到region server的具体工作，还监视root和meta 这2个系统级的region状态。<br>
    * RootScanner—定期扫描root region，以发现没有分配的meta region。<br>
    * MetaScanner—定期扫描meta region,以发现没有分配的user region。</p>
<p><strong>HBase基本命令</strong><br>
下面我们再看看看HBase的一些基本操作命令，我列出了几个常用的HBase Shell命令，如下：</p>
<table border="1" cellpadding="1" cellspacing="1" style="width:639px;"><tbody><tr><td><strong>名称<br></strong></td>
<td><strong>命令表达式<br></strong></td>
</tr><tr><td>创建表</td>
<td>create '表名称', '列名称1','列名称2','列名称N'</td>
</tr><tr><td>添加记录      </td>
<td>put '表名称', '行名称', '列名称:', '值'</td>
</tr><tr><td>查看记录</td>
<td>get '表名称', '行名称'</td>
</tr><tr><td>查看表中的记录总数</td>
<td>count  '表名称'</td>
</tr><tr><td>删除记录</td>
<td>delete  '表名' ,'行名称' , '列名称'</td>
</tr><tr><td>删除一张表</td>
<td>先要屏蔽该表，才能对该表进行删除，第一步 disable '表名称' 第二步  drop '表名称'</td>
</tr><tr><td>查看所有记录</td>
<td>scan "表名称"  </td>
</tr><tr><td>查看某个表某个列中所有数据</td>
<td>scan "表名称" , ['列名称:']</td>
</tr><tr><td>更新记录 </td>
<td>就是重写一遍进行覆盖</td>
</tr></tbody></table><p>  如果你是一个新手队HBase的一些命令还不算非常熟悉的话，你可以进入 hbase 的shell 模式中你可以输入 help 命令查看到你可以执行的命令和对该命令的说明，例如对scan这个命令，help中不仅仅提到有这个命令，还详细的说明了scan命令中可以使用的参数和作用，例如，根据列名称查询的方法和带LIMIT 、STARTROW的使用方法：<br>
scan   Scan a table; pass table name and optionally a dictionary of scanner specifications.  Scanner specifications may include one or more of  the following: LIMIT, STARTROW, STOPROW, TIMESTAMP, or COLUMNS.  If no columns are specified, all columns will be
 scanned.  To scan all members of a column family, leave the qualifier empty as in  'col_family:'.  Examples:<br>
            hbase&gt; scan '.META.'<br>
            hbase&gt; scan '.META.', {COLUMNS =&gt; 'info:regioninfo'}<br>
            hbase&gt; scan 't1', {COLUMNS =&gt; ['c1', 'c2'], LIMIT =&gt; 10, STARTROW =&gt; 'xyz'}</p>
<p><strong><br>
使用Java API对HBase服务器进行操作<br></strong></p>
<p><strong>需要下列jar包</strong><br>
     hbase-0.20.6.jar<br>
     hadoop-core-0.20.1.jar<br>
     commons-logging-1.1.1.jar<br>
     zookeeper-3.3.0.jar<br>
     log4j-1.2.91.jar</p>
<p>import org.apache.hadoop.conf.Configuration;<br>
import org.apache.hadoop.hbase.HBaseConfiguration;<br>
import org.apache.hadoop.hbase.HColumnDescriptor;<br>
import org.apache.hadoop.hbase.HTableDescriptor;<br>
import org.apache.hadoop.hbase.KeyValue;<br>
import org.apache.hadoop.hbase.client.HBaseAdmin;<br>
import org.apache.hadoop.hbase.client.HTable;<br>
import org.apache.hadoop.hbase.client.Result;<br>
import org.apache.hadoop.hbase.client.ResultScanner;<br>
import org.apache.hadoop.hbase.client.Scan;<br>
import org.apache.hadoop.hbase.io.BatchUpdate;</p>
<p>@SuppressWarnings("deprecation")<br>
public class HBaseTestCase {<br>
    <br>
    static HBaseConfiguration cfg = null;<br>
    static {<br>
        Configuration HBASE_CONFIG = new Configuration();<br>
        HBASE_CONFIG.set("hbase.zookeeper.quorum", "192.168.50.216");<br>
        HBASE_CONFIG.set("hbase.zookeeper.property.clientPort", "2181");<br>
        cfg = new HBaseConfiguration(HBASE_CONFIG);<br>
    }<br>
    <br>
    <br>
    /**<br>
     * 创建一张表<br>
     */<br>
    public static void creatTable(String tablename) throws Exception {<br>
        HBaseAdmin admin = new HBaseAdmin(cfg);<br>
        if (admin.tableExists(tablename)) {<br>
            System.out.println("table   Exists!!!");<br>
        }<br>
        else{<br>
            HTableDescriptor tableDesc = new HTableDescriptor(tablename);<br>
            tableDesc.addFamily(new HColumnDescriptor("name:"));<br>
            admin.createTable(tableDesc);<br>
            System.out.println("create table ok .");<br>
        }<br>
    <br>
         <br>
    }<br>
    <br>
    /**<br>
     * 添加一条数据<br>
     */<br>
    public static void addData (String tablename) throws Exception{<br>
         HTable table = new HTable(cfg, tablename);<br>
             BatchUpdate update = new BatchUpdate("Huangyi");  <br>
             update.put("name:java", "http://www.javabloger.com".getBytes());  <br>
             table.commit(update);  <br>
         System.out.println("add data ok .");<br>
    }<br>
    <br>
    /**<br>
     * 显示所有数据<br>
     */<br>
    public static void getAllData (String tablename) throws Exception{<br>
         HTable table = new HTable(cfg, tablename);<br>
         Scan s = new Scan();<br>
         ResultScanner ss = table.getScanner(s);<br>
         for(Result r:ss){<br>
             for(KeyValue kv:r.raw()){<br>
                System.out.print(new String(kv.getColumn()));<br>
                System.out.println(new String(kv.getValue()    ));<br>
             }</p>
<p>         }<br>
    }<br>
    <br>
    <br>
    public static void  main (String [] agrs) {<br>
        try {<br>
                String tablename="tablename";<br>
                HBaseTestCase.creatTable(tablename);<br>
                HBaseTestCase.addData(tablename);<br>
                HBaseTestCase.getAllData(tablename);<br>
            } <br>
        catch (Exception e) {<br>
            e.printStackTrace();<br>
        }<br>
        <br>
    }<br>
    <br>
}</p>
            </div>
                </div>