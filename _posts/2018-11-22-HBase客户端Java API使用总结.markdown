---
layout:     post
title:      HBase客户端Java API使用总结
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>HBase版本：HBase 0.95.0-hadoop1</p>
<p>客户端程序依赖的库，全部可从HBase的lib目录下找到<br>
commons-codec-1.7.jar<br>
commons-configuration-1.6.jar<br>
commons-lang-2.6.jar<br>
commons-logging-1.1.1.jar<br>
guava-12.0.1.jar<br>
hadoop-core-1.1.2.jar<br>
hbase-client-0.95.0-hadoop1.jar<br>
hbase-common-0.95.0-hadoop1.jar<br>
hbase-protocol-0.95.0-hadoop1.jar<br>
htrace-1.50.jar<br>
log4j-1.2.17.jar<br>
protobuf-java-2.4.1.jar<br>
slf4j-api-1.4.3.jar<br>
slf4j-log4j12-1.4.3.jar<br>
zookeeper-3.4.5.jar</p>
<p>HBase客户端编程框架</p>
<pre><code class="language-java">public class XXX {

 public static void main(String args[]) throws Exception {
  /* 唯一必须指定的配置项是ZooKeeper的位置 */
  Configuration conf = new Configuration();
  conf.set("hbase.zookeeper.quorum",
    "192.168.1.87,192.168.1.92,192.168.1.76");
  
  /* HTable是客户端访问HBase的入口类，对指定Table的CRUD操作通过该类请求 */
  HTable table = new HTable(conf, "test");
  
  try {
   
   // 增加或修改行
   Put put = new Put(Bytes.toBytes("row-x"));
   // 添加要增加或修改的列，需依次指定列族、列、值，还可指定版本号
   put.add(Bytes.toBytes("xx"), Bytes.toBytes("col-1"),
     Bytes.toBytes("value-1"));
   put.add(Bytes.toBytes("xx"), Bytes.toBytes("col-2"),
     123, Bytes.toBytes("value-2 of version 123"));
   // 提交PUT请求
   table.put(put);
   
   // 删除行
   Delete delete = new Delete(Bytes.toBytes("row-x"));
   // 若不想删除整行，可指定要删除的列族、列、列的某个版本
   /*
   删除指定列族：
   delete.deleteFamily(Bytes.toBytes("xx"));
   删除指定列：
   delete.deleteColumn(Bytes.toBytes("xx"), Bytes.toBytes("col-1"));
   删除指定列的某个版本：
   delete.deleteColumn(Bytes.toBytes("xx"), Bytes.toBytes("col-1"), 123);
   */
   // 提交DELETE请求
   table.delete(delete);
   
   // 查找行
   /*
   查找单行使用Get，查找多行使用Scan，Get是Scan的特殊情况，查找的结果都是Result实例
   使用Get查找单行：
   Get get = new Get(Bytes.toBytes("row-x"));
   Result result = table.get(get);
   使用Scan查找多行：
   Scan scan = new Scan();
   还可使用Scan的相应方法限定查找的行范围、列范围，若不限定则查找全部
   Iterator&lt;Result&gt; it = table.getScanner(scan).iterator();
   while (it.hasNext()) {
    Result result = it.next();
    ...
   }
   */
   // Result封装了一行的被查询内容：KV对的列表
   String row = Bytes.toString(result.getRow());
   System.out.println("Row Key: " + row);
   List&lt;KeyValue&gt; list = result.list();
   for (KeyValue pair : list) {
    pair.getRow(); // Row Key
    pair.getFamily(); // 列族
    pair.getQualifier(); // 列
    pair.getTimestamp(); //版本号
   }

  } finally {
   table.close(); /* 操作完Table后关闭相应的HTable实例 */
  }
 }

}
</code></pre>
<p> </p>
            </div>
                </div>