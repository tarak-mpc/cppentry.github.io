---
layout:     post
title:      HBase Java API
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/tengxing007/article/details/53446917				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div id="cnblogs_post_body" style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<h1><span></span>HBase Java API</h1>
<h3>　　几个相关类与HBase数据模型之间的对应关系</h3>
<table border="1" style="border:1px solid #C0C0C0;border-collapse:collapse;width:600px;"><tbody><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
java类</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
HBase数据模型</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
HBaseAdmin</td>
<td rowspan="2" style="border:1px solid #C0C0C0;border-collapse:collapse;">
数据库（DataBase)</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
HBaseConfiguration</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
HTable</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
表（Table)</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
HTableDescriptor</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
列族（Column Family)</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
Put</td>
<td rowspan="3" style="border:1px solid #C0C0C0;border-collapse:collapse;">
列修饰符（Column Qualifier）</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
Get</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
Scanner</td>
</tr></tbody></table><p> </p>
<h2>一、HBaseConfiguration</h2>
<p>关系：org.apache.hadoop.hbase.HBaseConfiguration</p>
<p>作用：对HBase进行配置</p>
<table border="1" style="border:1px solid #C0C0C0;border-collapse:collapse;width:650px;"><tbody><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
 返回值</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
 函数</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
 描述</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
 void</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
 addResource(Path file)</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
 通过给定的路径所指的文件来添加资源</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
 void</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
 clear()</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
 清空所有已设置的属性</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
 string</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
 get(String name)</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
 获取属性名对应的值</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
 String</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
 getBoolean(String name, boolean defaultValue)</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
 获取为boolean类型的属性值，如果其属性值类型部位boolean,则返回默认属性值</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
 void</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
 set(String name, String value)</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
 通过属性名来设置值</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
 void</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
 setBoolean(String name, boolean value)</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
 设置boolean类型的属性值</td>
</tr></tbody></table><p> 用法示例：</p>
<div class="cnblogs_code" style="background-color:rgb(245,245,245);border:1px solid rgb(204,204,204);overflow:auto;color:rgb(0,0,0);font-family:'Courier New' !important;font-size:12px !important;">
<pre style="font-family:'Courier New' !important;">HBaseConfiguration hconfig = <span style="line-height:1.8;color:rgb(0,0,255);">new</span><span style="line-height:1.8;"> HBaseConfiguration();
hconfig.set(</span>"hbase.zookeeper.property.clientPort","2181");</pre>
</div>
<p>　　该方法设置了"hbase.zookeeper.property.clientPort"的端口号为2181。一般情况下，HBaseConfiguration会使用构造函数进行初始化，然后在使用其他方法。</p>
<h2>二、HBaseAdmin</h2>
<p>关系：org.apache.hadoop.hbase.client.HBaseAdmin</p>
<p>作用：提供了一个接口来管理HBase数据库的表信息。它提供的方法包括：创建表，删除表，列出表项，使表有效或无效，以及添加或删除表列族成员等。</p>
<table border="1" style="border:1px solid #C0C0C0;border-collapse:collapse;width:650px;"><tbody><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
 返回值</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
 函数</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
 描述</td>
</tr><tr><td rowspan="6" style="border:1px solid #C0C0C0;border-collapse:collapse;">
      void</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
 addColumn(String tableName, HColumnDescriptor column)</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
 向一个已经存在的表添加咧</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
 checkHBaseAvailable(HBaseConfiguration conf)</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
 静态函数，查看HBase是否处于运行状态</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
 createTable(HTableDescriptor desc)</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
 创建一个表，同步操作</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
 deleteTable(byte[] tableName)</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
 删除一个已经存在的表</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
 enableTable(byte[] tableName)</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
 使表处于有效状态</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
 disableTable(byte[] tableName)</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
 使表处于无效状态</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
 HTableDescriptor[]</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
 listTables()</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
 列出所有用户控件表项</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
 void</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
 modifyTable(byte[] tableName, HTableDescriptor htd)</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
 修改表的模式，是异步的操作，可能需要花费一定的时间</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
 boolean</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
 tableExists(String tableName)</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
 检查表是否存在</td>
</tr></tbody></table><p>用法示例：</p>
<div class="cnblogs_code" style="background-color:rgb(245,245,245);border:1px solid rgb(204,204,204);overflow:auto;color:rgb(0,0,0);font-family:'Courier New' !important;font-size:12px !important;">
<pre style="font-family:'Courier New' !important;">HBaseAdmin admin = <span style="line-height:1.8;color:rgb(0,0,255);">new</span><span style="line-height:1.8;"> HBaseAdmin(config);
admin.disableTable(</span>"tablename")</pre>
</div>
<h2>三、HTableDescriptor</h2>
<p>关系：org.apache.hadoop.hbase.HTableDescriptor</p>
<p>作用：包含了表的名字极其对应表的列族</p>
<table border="1" style="border:1px solid #C0C0C0;border-collapse:collapse;width:650px;"><tbody><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
返回值</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
函数</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
描述</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
void</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
addFamily(HColumnDescriptor)</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
添加一个列族</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
HColumnDescriptor</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
removeFamily(byte[] column)</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
移除一个列族</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
byte[]</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
getName()</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
获取表的名字</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
byte[]</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
getValue(byte[] key)</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
获取属性的值</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
void</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
setValue(String key, String value)</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
设置属性的值</td>
</tr></tbody></table><p>用法示例：</p>
<div class="cnblogs_code" style="background-color:rgb(245,245,245);border:1px solid rgb(204,204,204);overflow:auto;color:rgb(0,0,0);font-family:'Courier New' !important;font-size:12px !important;">
<pre style="font-family:'Courier New' !important;">HTableDescriptor htd = <span style="line-height:1.8;color:rgb(0,0,255);">new</span><span style="line-height:1.8;"> HTableDescriptor(table);
htd.addFamily(</span><span style="line-height:1.8;color:rgb(0,0,255);">new</span> HcolumnDescriptor("family"));</pre>
</div>
<p>在上述例子中，通过一个HColumnDescriptor实例，为HTableDescriptor添加了一个列族：family</p>
<h2>四、HColumnDescriptor</h2>
<p>关系：org.apache.hadoop.hbase.HColumnDescriptor</p>
<p>作用：维护着关于列族的信息，例如版本号，压缩设置等。它通常在创建表或者为表添加列族的时候使用。列族被创建后不能直接修改，只能通过删除然后重新创建的方式。列族被删除的时候，列族里面的数据也会同时被删除。</p>
<table border="1" style="border:1px solid #C0C0C0;border-collapse:collapse;width:650px;"><tbody><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
返回值</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
函数</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
描述</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
byte[]</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
getName()</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
获取列族的名字</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
byte[]</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
getValue(byte[] key)</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
获取对应的属性的值</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
void</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
setValue(String key, String value)</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
设置对应属性的值</td>
</tr></tbody></table><p>用法示例：</p>
<div class="cnblogs_code" style="background-color:rgb(245,245,245);border:1px solid rgb(204,204,204);overflow:auto;color:rgb(0,0,0);font-family:'Courier New' !important;font-size:12px !important;">
<pre style="font-family:'Courier New' !important;">HTableDescriptor htd = <span style="line-height:1.8;color:rgb(0,0,255);">new</span><span style="line-height:1.8;"> HTableDescriptor(tablename);
HColumnDescriptor col </span>= <span style="line-height:1.8;color:rgb(0,0,255);">new</span> HColumnDescriptor("content:"<span style="line-height:1.8;">);
htd.addFamily(col);</span></pre>
</div>
<p>此例添加了一个content的列族</p>
<p>五、HTable</p>
<p>关系：org.apache.hadoop.hbase.client.HTable</p>
<p>作用：可以用来和HBase表直接通信。此方法对于更新操作来说是非线程安全的。</p>
<table border="1" style="border:1px solid #C0C0C0;border-collapse:collapse;width:650px;"><tbody><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
返回值</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
函数</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
描述</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
void</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
checkAdnPut(byte[] row, byte[] family, byte[] qualifier, byte[] value, Put put</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
自动的检查row/family/qualifier是否与给定的值匹配</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
void</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
close()</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
释放所有的资源或挂起内部缓冲区中的更新</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
Boolean</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
exists(Get get)</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
检查Get实例所指定的值是否存在于HTable的列中</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
Result</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
get(Get get)</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
获取指定行的某些单元格所对应的值</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
byte[][]</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
getEndKeys()</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
获取当前一打开的表每个区域的结束键值</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
ResultScanner</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
getScanner(byte[] family)</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
获取当前给定列族的scanner实例</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
HTableDescriptor</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
getTableDescriptor()</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
获取当前表的HTableDescriptor实例</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
byte[]</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
getTableName()</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
获取表名</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
static boolean</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
isTableEnabled(HBaseConfiguration conf, String tableName)</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
检查表是否有效</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
void</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
put(Put put)</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
向表中添加值</td>
</tr></tbody></table><p>用法示例：</p>
<div class="cnblogs_code" style="background-color:rgb(245,245,245);border:1px solid rgb(204,204,204);overflow:auto;color:rgb(0,0,0);font-family:'Courier New' !important;font-size:12px !important;">
<pre style="font-family:'Courier New' !important;">HTable table = <span style="line-height:1.8;color:rgb(0,0,255);">new</span><span style="line-height:1.8;"> HTable(conf, Bytes.toBytes(tablename));
ResultScanner scanner </span>=  table.getScanner(family);</pre>
</div>
<h2>六、Put</h2>
<p>关系：org.apache.hadoop.hbase.client.Put</p>
<p>作用：用来对单个行执行添加操作</p>
<table border="1" style="border:1px solid #C0C0C0;border-collapse:collapse;width:650px;"><tbody><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
返回值</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
函数</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
描述</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
Put</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
add(byte[] family, byte[] qualifier, byte[] value)</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
将指定的列和对应的值添加到Put实例中</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
Put</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
add(byte[] family, byte[] qualifier, long ts, byte[] value)</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
将指定的列和对应的值及时间戳添加到Put实例中</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
byte[]</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
getRow()</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
获取Put实例的行</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
RowLock</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
getRowLock()</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
获取Put实例的行锁</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
long</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
getTimeStamp()</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
获取Put实例的时间戳</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
boolean</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
isEmpty()</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
检查familyMap是否为空</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
Put</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
setTimeStamp(long timeStamp)</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
设置Put实例的时间戳</td>
</tr></tbody></table><p>用法示例：</p>
<div class="cnblogs_code" style="background-color:rgb(245,245,245);border:1px solid rgb(204,204,204);overflow:auto;color:rgb(0,0,0);font-family:'Courier New' !important;font-size:12px !important;">
<pre style="font-family:'Courier New' !important;">HTable table = <span style="line-height:1.8;color:rgb(0,0,255);">new</span><span style="line-height:1.8;"> HTable(conf,Bytes.toBytes(tablename));
Put p </span>= <span style="line-height:1.8;color:rgb(0,0,255);">new</span> Put(brow);<span style="line-height:1.8;color:rgb(0,128,0);">//</span><span style="line-height:1.8;color:rgb(0,128,0);">为指定行创建一个Put操作</span>
<span style="line-height:1.8;">p.add(family,qualifier,value);
table.put(p);</span></pre>
</div>
<h2>七、Get</h2>
<p>关系：org.apache.hadoop.hbase.client.Get</p>
<p>作用：用来获取单个行的相关信息</p>
<table border="1" style="border:1px solid #C0C0C0;border-collapse:collapse;width:650px;"><tbody><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
返回值</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
函数</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
描述</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
Get</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
addColumn(byte[] family, byte[] qualifier)</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
获取指定列族和列修饰符对应的列</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
Get</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
addFamily(byte[] family)</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
通过指定的列族获取其对应列的所有列</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
Get</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
setTimeRange(long minStamp,long maxStamp)</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
获取指定取件的列的版本号</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
Get</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
setFilter(Filter filter)</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
当执行Get操作时设置服务器端的过滤器</td>
</tr></tbody></table><p>用法示例：</p>
<div class="cnblogs_code" style="background-color:rgb(245,245,245);border:1px solid rgb(204,204,204);overflow:auto;color:rgb(0,0,0);font-family:'Courier New' !important;font-size:12px !important;">
<pre style="font-family:'Courier New' !important;">HTable table = <span style="line-height:1.8;color:rgb(0,0,255);">new</span><span style="line-height:1.8;"> HTable(conf, Bytes.toBytes(tablename));
Get g </span>= <span style="line-height:1.8;color:rgb(0,0,255);">new</span> Get(Bytes.toBytes(row));</pre>
</div>
<h2>八、Result</h2>
<p>关系：org.apache.hadoop.hbase.client.Result</p>
<p>作用：存储Get或者Scan操作后获取表的单行值。使用此类提供的方法可以直接获取值或者各种Map结构（key-value对）</p>
<table border="1" style="border:1px solid #C0C0C0;border-collapse:collapse;width:650px;"><tbody><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
返回值</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
函数</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
描述</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
boolean</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
containsColumn(byte[] family, byte[] qualifier)</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
检查指定的列是否存在</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
NavigableMap&lt;byte[],byte[]&gt;</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
getFamilyMap(byte[] family)</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
获取对应列族所包含的修饰符与值的键值对</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
byte[]</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
getValue(byte[] family, byte[] qualifier)</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
获取对应列的最新值</td>
</tr></tbody></table><h2>九、ResultScanner</h2>
<p>关系：Interface</p>
<p>作用：客户端获取值的接口</p>
<table border="1" style="border:1px solid #C0C0C0;border-collapse:collapse;width:650px;"><tbody><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
返回值</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
函数</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
描述</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
void</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
close()</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
关闭scanner并释放分配给它的资源</td>
</tr><tr><td style="border:1px solid #C0C0C0;border-collapse:collapse;">
Result</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
next()</td>
<td style="border:1px solid #C0C0C0;border-collapse:collapse;">
获取下一行的值<br></td>
</tr></tbody></table><br></div>
<div style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
参考书籍：《hadoop实战》第二版 陆嘉恒 著</div>
<div style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<br></div>
            </div>
                </div>