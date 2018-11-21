---
layout:     post
title:      HBase Java简单示例
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div id="cnblogs_post_body" style="color:rgb(51,51,51);font-family:Georgia, 'Times New Roman', Times, sans-serif;font-size:14px;line-height:25.2000007629395px;">
<p style="line-height:1.8;"></p>
<p style="font-size:14px;color:rgb(51,51,51);font-family:Arial;"><strong><span style="font-size:18px;">欢迎关注本人公众号</span></strong></p>
<p style="font-size:14px;color:rgb(51,51,51);font-family:Arial;"><img src="https://img-blog.csdn.net/20170109145547061?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaGF2YXJkdW5pdg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none;"></p>
<br><p style="line-height:1.8;"><br></p>
<p style="line-height:1.8;">Hbase采用Java实现，原生客户端也是Java实现，其他语言需要通过thritf接口服务间接访问Hbase的数据。</p>
<p style="line-height:1.8;">Hbase作为大数据存储数据库，其写能力非常强，加上Hbase本身就脱胎于Hadoop故和Hadoop的兼容性极好，非常适合于存储半规则数据(灵活、可扩展性强、大数据存储)。基于Hadoop的mapreduce + Hbase存储，非常适合处理大数据。</p>
<h2 style="font-family:'微软雅黑', sans-serif;font-size:21px;letter-spacing:-1px;line-height:25.2000007629395px;color:rgb(51,102,153);">
Hbase基本使用示例：</h2>
<div class="cnblogs_code" style="border:1px solid rgb(204,204,204);overflow:auto;color:rgb(0,0,0);font-family:'Courier New' !important;font-size:12px !important;background-color:rgb(245,245,245);">
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy" style="line-height:1.5 !important;"><a title="复制代码" style="color:rgb(61,129,238);border:none !important;"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码" style="border:none rgb(221,221,221) !important;background-color:rgb(255,255,255);"></a></span></div>
<pre style="font-family:'Courier New' !important;"><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> java.io.IOException; 
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> java.util.ArrayList; 
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> java.util.List; 
 
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.conf.Configuration; 
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.HBaseConfiguration; 
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.HColumnDescriptor; 
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.HTableDescriptor; 
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.KeyValue; 
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.MasterNotRunningException; 
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.ZooKeeperConnectionException; 
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.client.Delete; 
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.client.Get; 
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.client.HBaseAdmin; 
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.client.HTable; 
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.client.HTablePool; 
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.client.Put; 
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.client.Result; 
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.client.ResultScanner; 
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.client.Scan; 
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.filter.Filter; 
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.filter.FilterList; 
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.filter.SingleColumnValueFilter; 
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.filter.CompareFilter.CompareOp; 
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.util.Bytes; 
 
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">public</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">class</span><span style="line-height:1.5 !important;"> HbaseTest { 
 
    </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">public</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">static</span><span style="line-height:1.5 !important;"> Configuration configuration; 
    </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">static</span><span style="line-height:1.5 !important;"> { 
        configuration </span>=<span style="line-height:1.5 !important;"> HBaseConfiguration.create(); 
        configuration.set(</span>"hbase.zookeeper.property.clientPort", "2181"<span style="line-height:1.5 !important;">); 
        configuration.set(</span>"hbase.zookeeper.quorum", "192.168.1.100"<span style="line-height:1.5 !important;">); 
        configuration.set(</span>"hbase.master", "192.168.1.100:600000"<span style="line-height:1.5 !important;">); 
    } 
 
    </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">public</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">static</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">void</span><span style="line-height:1.5 !important;"> main(String[] args) { 
        </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">//</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> createTable("wujintao"); 
        </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">//</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> insertData("wujintao"); 
        </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">//</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> QueryAll("wujintao"); 
        </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">//</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> QueryByCondition1("wujintao"); 
        </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">//</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> QueryByCondition2("wujintao"); 
        </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">//</span><span style="color:rgb(0,128,0);line-height:1.5 !important;">QueryByCondition3("wujintao"); 
        </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">//</span><span style="color:rgb(0,128,0);line-height:1.5 !important;">deleteRow("wujintao","abcdef"); </span>
        deleteByCondition("wujintao","abcdef"<span style="line-height:1.5 !important;">); 
    } 
 
     
    </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">public</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">static</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">void</span><span style="line-height:1.5 !important;"> createTable(String tableName) { 
        System.out.println(</span>"start create table ......"<span style="line-height:1.5 !important;">); 
        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">try</span><span style="line-height:1.5 !important;"> { 
            HBaseAdmin hBaseAdmin </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> HBaseAdmin(configuration); 
            </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">if</span> (hBaseAdmin.tableExists(tableName)) {<span style="color:rgb(0,128,0);line-height:1.5 !important;">//</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> 如果存在要创建的表，那么先删除，再创建 </span>
<span style="line-height:1.5 !important;">                hBaseAdmin.disableTable(tableName); 
                hBaseAdmin.deleteTable(tableName); 
                System.out.println(tableName </span>+ " is exist,detele...."<span style="line-height:1.5 !important;">); 
            } 
            HTableDescriptor tableDescriptor </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> HTableDescriptor(tableName); 
            tableDescriptor.addFamily(</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span> HColumnDescriptor("column1"<span style="line-height:1.5 !important;">)); 
            tableDescriptor.addFamily(</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span> HColumnDescriptor("column2"<span style="line-height:1.5 !important;">)); 
            tableDescriptor.addFamily(</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span> HColumnDescriptor("column3"<span style="line-height:1.5 !important;">)); 
            hBaseAdmin.createTable(tableDescriptor); 
        } </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">catch</span><span style="line-height:1.5 !important;"> (MasterNotRunningException e) { 
            e.printStackTrace(); 
        } </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">catch</span><span style="line-height:1.5 !important;"> (ZooKeeperConnectionException e) { 
            e.printStackTrace(); 
        } </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">catch</span><span style="line-height:1.5 !important;"> (IOException e) { 
            e.printStackTrace(); 
        } 
        System.out.println(</span>"end create table ......"<span style="line-height:1.5 !important;">); 
    } 
 
     
    </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">public</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">static</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">void</span><span style="line-height:1.5 !important;"> insertData(String tableName) { 
        System.out.println(</span>"start insert data ......"<span style="line-height:1.5 !important;">); 
        HTablePool pool </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span> HTablePool(configuration, 1000<span style="line-height:1.5 !important;">); 
        HTable table </span>=<span style="line-height:1.5 !important;"> (HTable) pool.getTable(tableName); 
        Put put </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span> Put("112233bbbcccc".getBytes());<span style="color:rgb(0,128,0);line-height:1.5 !important;">//</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> 一个PUT代表一行数据，再NEW一个PUT表示第二行数据,每行一个唯一的ROWKEY，此处rowkey为put构造方法中传入的值 </span>
        put.add("column1".getBytes(), <span style="color:rgb(0,0,255);line-height:1.5 !important;">null</span>, "aaa".getBytes());<span style="color:rgb(0,128,0);line-height:1.5 !important;">//</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> 本行数据的第一列 </span>
        put.add("column2".getBytes(), <span style="color:rgb(0,0,255);line-height:1.5 !important;">null</span>, "bbb".getBytes());<span style="color:rgb(0,128,0);line-height:1.5 !important;">//</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> 本行数据的第三列 </span>
        put.add("column3".getBytes(), <span style="color:rgb(0,0,255);line-height:1.5 !important;">null</span>, "ccc".getBytes());<span style="color:rgb(0,128,0);line-height:1.5 !important;">//</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> 本行数据的第三列 </span>
        <span style="color:rgb(0,0,255);line-height:1.5 !important;">try</span><span style="line-height:1.5 !important;"> { 
            table.put(put); 
        } </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">catch</span><span style="line-height:1.5 !important;"> (IOException e) { 
            e.printStackTrace(); 
        } 
        System.out.println(</span>"end insert data ......"<span style="line-height:1.5 !important;">); 
    } 
 
     
    </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">public</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">static</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">void</span><span style="line-height:1.5 !important;"> dropTable(String tableName) { 
        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">try</span><span style="line-height:1.5 !important;"> { 
            HBaseAdmin admin </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> HBaseAdmin(configuration); 
            admin.disableTable(tableName); 
            admin.deleteTable(tableName); 
        } </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">catch</span><span style="line-height:1.5 !important;"> (MasterNotRunningException e) { 
            e.printStackTrace(); 
        } </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">catch</span><span style="line-height:1.5 !important;"> (ZooKeeperConnectionException e) { 
            e.printStackTrace(); 
        } </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">catch</span><span style="line-height:1.5 !important;"> (IOException e) { 
            e.printStackTrace(); 
        } 
 
    } 
     
     </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">public</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">static</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">void</span><span style="line-height:1.5 !important;"> deleteRow(String tablename, String rowkey)  { 
        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">try</span><span style="line-height:1.5 !important;"> { 
            HTable table </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> HTable(configuration, tablename); 
            List list </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> ArrayList(); 
            Delete d1 </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> Delete(rowkey.getBytes()); 
            list.add(d1); 
             
            table.delete(list); 
            System.out.println(</span>"删除行成功!"<span style="line-height:1.5 !important;">); 
             
        } </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">catch</span><span style="line-height:1.5 !important;"> (IOException e) { 
            e.printStackTrace(); 
        } 
         
 
    } 
 
      
     </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">public</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">static</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">void</span><span style="line-height:1.5 !important;"> deleteByCondition(String tablename, String rowkey)  { 
            </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">//</span><span style="color:rgb(0,128,0);line-height:1.5 !important;">目前还没有发现有效的API能够实现根据非rowkey的条件删除这个功能能，还有清空表全部数据的API操作 </span>
<span style="line-height:1.5 !important;"> 
    } 
 
 
     
    </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">public</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">static</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">void</span><span style="line-height:1.5 !important;"> QueryAll(String tableName) { 
        HTablePool pool </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span> HTablePool(configuration, 1000<span style="line-height:1.5 !important;">); 
        HTable table </span>=<span style="line-height:1.5 !important;"> (HTable) pool.getTable(tableName); 
        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">try</span><span style="line-height:1.5 !important;"> { 
            ResultScanner rs </span>= table.getScanner(<span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> Scan()); 
            </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">for</span><span style="line-height:1.5 !important;"> (Result r : rs) { 
                System.out.println(</span>"获得到rowkey:" + <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> String(r.getRow())); 
                </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">for</span><span style="line-height:1.5 !important;"> (KeyValue keyValue : r.raw()) { 
                    System.out.println(</span>"列：" + <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> String(keyValue.getFamily()) 
                            </span>+ "====值:" + <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> String(keyValue.getValue())); 
                } 
            } 
        } </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">catch</span><span style="line-height:1.5 !important;"> (IOException e) { 
            e.printStackTrace(); 
        } 
    } 
 
     
    </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">public</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">static</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">void</span><span style="line-height:1.5 !important;"> QueryByCondition1(String tableName) { 
 
        HTablePool pool </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span> HTablePool(configuration, 1000<span style="line-height:1.5 !important;">); 
        HTable table </span>=<span style="line-height:1.5 !important;"> (HTable) pool.getTable(tableName); 
        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">try</span><span style="line-height:1.5 !important;"> { 
            Get scan </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span> Get("abcdef".getBytes());<span style="color:rgb(0,128,0);line-height:1.5 !important;">//</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> 根据rowkey查询 </span>
            Result r =<span style="line-height:1.5 !important;"> table.get(scan); 
            System.out.println(</span>"获得到rowkey:" + <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> String(r.getRow())); 
            </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">for</span><span style="line-height:1.5 !important;"> (KeyValue keyValue : r.raw()) { 
                System.out.println(</span>"列：" + <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> String(keyValue.getFamily()) 
                        </span>+ "====值:" + <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> String(keyValue.getValue())); 
            } 
        } </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">catch</span><span style="line-height:1.5 !important;"> (IOException e) { 
            e.printStackTrace(); 
        } 
    } 
 
     
    </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">public</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">static</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">void</span><span style="line-height:1.5 !important;"> QueryByCondition2(String tableName) { 
 
        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">try</span><span style="line-height:1.5 !important;"> { 
            HTablePool pool </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span> HTablePool(configuration, 1000<span style="line-height:1.5 !important;">); 
            HTable table </span>=<span style="line-height:1.5 !important;"> (HTable) pool.getTable(tableName); 
            Filter filter </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> SingleColumnValueFilter(Bytes 
                    .toBytes(</span>"column1"), <span style="color:rgb(0,0,255);line-height:1.5 !important;">null</span><span style="line-height:1.5 !important;">, CompareOp.EQUAL, Bytes 
                    .toBytes(</span>"aaa")); <span style="color:rgb(0,128,0);line-height:1.5 !important;">//</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> 当列column1的值为aaa时进行查询 </span>
            Scan s = <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> Scan(); 
            s.setFilter(filter); 
            ResultScanner rs </span>=<span style="line-height:1.5 !important;"> table.getScanner(s); 
            </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">for</span><span style="line-height:1.5 !important;"> (Result r : rs) { 
                System.out.println(</span>"获得到rowkey:" + <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> String(r.getRow())); 
                </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">for</span><span style="line-height:1.5 !important;"> (KeyValue keyValue : r.raw()) { 
                    System.out.println(</span>"列：" + <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> String(keyValue.getFamily()) 
                            </span>+ "====值:" + <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> String(keyValue.getValue())); 
                } 
            } 
        } </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">catch</span><span style="line-height:1.5 !important;"> (Exception e) { 
            e.printStackTrace(); 
        } 
 
    } 
 
     
    </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">public</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">static</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">void</span><span style="line-height:1.5 !important;"> QueryByCondition3(String tableName) { 
 
        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">try</span><span style="line-height:1.5 !important;"> { 
            HTablePool pool </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span> HTablePool(configuration, 1000<span style="line-height:1.5 !important;">); 
            HTable table </span>=<span style="line-height:1.5 !important;"> (HTable) pool.getTable(tableName); 
 
            List</span>&lt;Filter&gt; filters = <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span> ArrayList&lt;Filter&gt;<span style="line-height:1.5 !important;">(); 
 
            Filter filter1 </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> SingleColumnValueFilter(Bytes 
                    .toBytes(</span>"column1"), <span style="color:rgb(0,0,255);line-height:1.5 !important;">null</span><span style="line-height:1.5 !important;">, CompareOp.EQUAL, Bytes 
                    .toBytes(</span>"aaa"<span style="line-height:1.5 !important;">)); 
            filters.add(filter1); 
 
            Filter filter2 </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> SingleColumnValueFilter(Bytes 
                    .toBytes(</span>"column2"), <span style="color:rgb(0,0,255);line-height:1.5 !important;">null</span><span style="line-height:1.5 !important;">, CompareOp.EQUAL, Bytes 
                    .toBytes(</span>"bbb"<span style="line-height:1.5 !important;">)); 
            filters.add(filter2); 
 
            Filter filter3 </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> SingleColumnValueFilter(Bytes 
                    .toBytes(</span>"column3"), <span style="color:rgb(0,0,255);line-height:1.5 !important;">null</span><span style="line-height:1.5 !important;">, CompareOp.EQUAL, Bytes 
                    .toBytes(</span>"ccc"<span style="line-height:1.5 !important;">)); 
            filters.add(filter3); 
 
            FilterList filterList1 </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> FilterList(filters); 
 
            Scan scan </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> Scan(); 
            scan.setFilter(filterList1); 
            ResultScanner rs </span>=<span style="line-height:1.5 !important;"> table.getScanner(scan); 
            </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">for</span><span style="line-height:1.5 !important;"> (Result r : rs) { 
                System.out.println(</span>"获得到rowkey:" + <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> String(r.getRow())); 
                </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">for</span><span style="line-height:1.5 !important;"> (KeyValue keyValue : r.raw()) { 
                    System.out.println(</span>"列：" + <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> String(keyValue.getFamily()) 
                            </span>+ "====值:" + <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> String(keyValue.getValue())); 
                } 
            } 
            rs.close(); 
 
        } </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">catch</span><span style="line-height:1.5 !important;"> (Exception e) { 
            e.printStackTrace(); 
        } 
 
    } 
 
} </span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy" style="line-height:1.5 !important;"><a title="复制代码" style="color:rgb(61,129,238);border:none !important;"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码" style="border:none rgb(221,221,221) !important;background-color:rgb(255,255,255);"></a></span></div>
</div>
<h2 style="font-family:'微软雅黑', sans-serif;font-size:21px;letter-spacing:-1px;line-height:25.2000007629395px;color:rgb(51,102,153);">
Hbase数据获取示例：</h2>
<div class="cnblogs_code" style="border:1px solid rgb(204,204,204);overflow:auto;color:rgb(0,0,0);font-family:'Courier New' !important;font-size:12px !important;background-color:rgb(245,245,245);">
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy" style="line-height:1.5 !important;"><a title="复制代码" style="color:rgb(61,129,238);border:none !important;"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码" style="border:none rgb(221,221,221) !important;background-color:rgb(255,255,255);"></a></span></div>
<pre style="font-family:'Courier New' !important;"><span style="color:rgb(0,128,0);line-height:1.5 !important;">/*</span><span style="color:rgb(0,128,0);line-height:1.5 !important;">
 * Need Packages:
 * commons-codec-1.4.jar
 *
 * commons-logging-1.1.1.jar
 *
 * hadoop-0.20.2-core.jar
 *
 * hbase-0.90.2.jar
 *
 * log4j-1.2.16.jar
 *
 * zookeeper-3.3.2.jar
 *
 </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">*/</span>

<span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> java.io.IOException;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> java.util.ArrayList;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> java.util.List;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.conf.Configuration;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.HBaseConfiguration;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.KeyValue;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.client.Get;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.client.HTable;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.client.Result;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.client.ResultScanner;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.client.Scan;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.filter.CompareFilter.CompareOp;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.filter.FilterList;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.filter.SingleColumnValueFilter;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.util.Bytes;

</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">public</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">class</span><span style="line-height:1.5 !important;"> HbaseSelecter
{
    </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">public</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">static</span> Configuration configuration = <span style="color:rgb(0,0,255);line-height:1.5 !important;">null</span><span style="line-height:1.5 !important;">;
    </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">static</span><span style="line-height:1.5 !important;">
    {
        configuration </span>=<span style="line-height:1.5 !important;"> HBaseConfiguration.create();
        </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">//</span><span style="color:rgb(0,128,0);line-height:1.5 !important;">configuration.set("hbase.master", "192.168.0.201:60000");</span>
        configuration.set("hbase.zookeeper.quorum", "idc01-hd-nd-03,idc01-hd-nd-04,idc01-hd-nd-05"<span style="line-height:1.5 !important;">);
        </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">//</span><span style="color:rgb(0,128,0);line-height:1.5 !important;">configuration.set("hbase.zookeeper.property.clientPort", "2181");</span>
<span style="line-height:1.5 !important;">    }

    </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">public</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">static</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">void</span> selectRowKey(String tablename, String rowKey) <span style="color:rgb(0,0,255);line-height:1.5 !important;">throws</span><span style="line-height:1.5 !important;"> IOException
    {
        HTable table </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> HTable(configuration, tablename);
        Get g </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> Get(rowKey.getBytes());
        Result rs </span>=<span style="line-height:1.5 !important;"> table.get(g);

        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">for</span><span style="line-height:1.5 !important;"> (KeyValue kv : rs.raw())
        {
            System.out.println(</span>"--------------------" + <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span> String(kv.getRow()) + "----------------------------"<span style="line-height:1.5 !important;">);
            System.out.println(</span>"Column Family: " + <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> String(kv.getFamily()));
            System.out.println(</span>"Column       :" + <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> String(kv.getQualifier()));
            System.out.println(</span>"value        : " + <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> String(kv.getValue()));
        }
    }

    </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">public</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">static</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">void</span> selectRowKeyFamily(String tablename, String rowKey, String family) <span style="color:rgb(0,0,255);line-height:1.5 !important;">throws</span><span style="line-height:1.5 !important;"> IOException
    {
        HTable table </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> HTable(configuration, tablename);
        Get g </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> Get(rowKey.getBytes());
        g.addFamily(Bytes.toBytes(family));
        Result rs </span>=<span style="line-height:1.5 !important;"> table.get(g);
        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">for</span><span style="line-height:1.5 !important;"> (KeyValue kv : rs.raw())
        {
            System.out.println(</span>"--------------------" + <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span> String(kv.getRow()) + "----------------------------"<span style="line-height:1.5 !important;">);
            System.out.println(</span>"Column Family: " + <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> String(kv.getFamily()));
            System.out.println(</span>"Column       :" + <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> String(kv.getQualifier()));
            System.out.println(</span>"value        : " + <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> String(kv.getValue()));
        }
    }

    </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">public</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">static</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">void</span><span style="line-height:1.5 !important;"> selectRowKeyFamilyColumn(String tablename, String rowKey, String family, String column)
            </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">throws</span><span style="line-height:1.5 !important;"> IOException
    {
        HTable table </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> HTable(configuration, tablename);
        Get g </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> Get(rowKey.getBytes());
        g.addColumn(family.getBytes(), column.getBytes());

        Result rs </span>=<span style="line-height:1.5 !important;"> table.get(g);

        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">for</span><span style="line-height:1.5 !important;"> (KeyValue kv : rs.raw())
        {
            System.out.println(</span>"--------------------" + <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span> String(kv.getRow()) + "----------------------------"<span style="line-height:1.5 !important;">);
            System.out.println(</span>"Column Family: " + <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> String(kv.getFamily()));
            System.out.println(</span>"Column       :" + <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> String(kv.getQualifier()));
            System.out.println(</span>"value        : " + <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> String(kv.getValue()));
        }
    }

    </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">public</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">static</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">void</span> selectFilter(String tablename, List&lt;String&gt; arr) <span style="color:rgb(0,0,255);line-height:1.5 !important;">throws</span><span style="line-height:1.5 !important;"> IOException
    {
        HTable table </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> HTable(configuration, tablename);
        Scan scan </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span> Scan();<span style="color:rgb(0,128,0);line-height:1.5 !important;">//</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> 实例化一个遍历器</span>
        FilterList filterList = <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span> FilterList(); <span style="color:rgb(0,128,0);line-height:1.5 !important;">//</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> 过滤器List</span>

        <span style="color:rgb(0,0,255);line-height:1.5 !important;">for</span><span style="line-height:1.5 !important;"> (String v : arr)
        { </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">//</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> 下标0为列簇，1为列名，3为条件</span>
            String[] wheres = v.split(","<span style="line-height:1.5 !important;">);

            filterList.addFilter(</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span> SingleColumnValueFilter(<span style="color:rgb(0,128,0);line-height:1.5 !important;">//</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> 过滤器</span>
                    wheres[0].getBytes(), wheres[1<span style="line-height:1.5 !important;">].getBytes(),

                    CompareOp.EQUAL,</span><span style="color:rgb(0,128,0);line-height:1.5 !important;">//</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> 各个条件之间是" and "的关系</span>
                    wheres[2<span style="line-height:1.5 !important;">].getBytes()));
        }
        scan.setFilter(filterList);
        ResultScanner ResultScannerFilterList </span>=<span style="line-height:1.5 !important;"> table.getScanner(scan);
        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">for</span> (Result rs = ResultScannerFilterList.next(); rs != <span style="color:rgb(0,0,255);line-height:1.5 !important;">null</span>; rs =<span style="line-height:1.5 !important;"> ResultScannerFilterList.next())
        {
            </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">for</span><span style="line-height:1.5 !important;"> (KeyValue kv : rs.list())
            {
                System.out.println(</span>"--------------------" + <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span> String(kv.getRow()) + "----------------------------"<span style="line-height:1.5 !important;">);
                System.out.println(</span>"Column Family: " + <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> String(kv.getFamily()));
                System.out.println(</span>"Column       :" + <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> String(kv.getQualifier()));
                System.out.println(</span>"value        : " + <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> String(kv.getValue()));
            }
        }
    }

    </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">public</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">static</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">void</span> main(String[] args) <span style="color:rgb(0,0,255);line-height:1.5 !important;">throws</span><span style="line-height:1.5 !important;"> Exception
    {
        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">if</span>(args.length &lt; 2<span style="line-height:1.5 !important;">){
            System.out.println(</span>"Usage: HbaseSelecter table key"<span style="line-height:1.5 !important;">);
            System.exit(</span>-1<span style="line-height:1.5 !important;">);
        }

        System.out.println(</span>"Table: " + args[0] + " , key: " + args[1<span style="line-height:1.5 !important;">]);
        selectRowKey(args[</span>0], args[1<span style="line-height:1.5 !important;">]);

        </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">/*</span><span style="color:rgb(0,128,0);line-height:1.5 !important;">
        System.out.println("------------------------行键  查询----------------------------------");
        selectRowKey("b2c", "yihaodian1002865");
        selectRowKey("b2c", "yihaodian1003396");

        System.out.println("------------------------行键+列簇 查询----------------------------------");
        selectRowKeyFamily("riapguh", "用户A", "user");
        selectRowKeyFamily("riapguh", "用户B", "user");

        System.out.println("------------------------行键+列簇+列名 查询----------------------------------");
        selectRowKeyFamilyColumn("riapguh", "用户A", "user", "user_code");
        selectRowKeyFamilyColumn("riapguh", "用户B", "user", "user_code");

        System.out.println("------------------------条件 查询----------------------------------");
        List&lt;String&gt; arr = new ArrayList&lt;String&gt;();
        arr.add("dpt,dpt_code,d_001");
        arr.add("user,user_code,u_0001");
        selectFilter("riapguh", arr);
        </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">*/</span><span style="line-height:1.5 !important;">
    }
}</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy" style="line-height:1.5 !important;"><a title="复制代码" style="color:rgb(61,129,238);border:none !important;"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码" style="border:none rgb(221,221,221) !important;background-color:rgb(255,255,255);"></a></span></div>
</div>
<h2 style="font-family:'微软雅黑', sans-serif;font-size:21px;letter-spacing:-1px;line-height:25.2000007629395px;color:rgb(51,102,153);">
Hbase 导出特定列 示例(小量数据):</h2>
<div class="cnblogs_code" style="border:1px solid rgb(204,204,204);overflow:auto;color:rgb(0,0,0);font-family:'Courier New' !important;font-size:12px !important;background-color:rgb(245,245,245);">
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy" style="line-height:1.5 !important;"><a title="复制代码" style="color:rgb(61,129,238);border:none !important;"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码" style="border:none rgb(221,221,221) !important;background-color:rgb(255,255,255);"></a></span></div>
<pre style="font-family:'Courier New' !important;"><span style="color:rgb(0,128,0);line-height:1.5 !important;">/*</span><span style="color:rgb(0,128,0);line-height:1.5 !important;">
 * Need Packages:
 * commons-codec-1.4.jar
 *
 * commons-logging-1.1.1.jar
 *
 * hadoop-0.20.2-core.jar
 *
 * hbase-0.90.2.jar
 *
 * log4j-1.2.16.jar
 *
 * zookeeper-3.3.2.jar
 *
 * Example: javac -classpath ./:/data/chenzhenjing/code/panama/lib/hbase-0.90.2.jar:/data/chenzhenjing/code/panama/lib/hadoop-core-0.20-append-for-hbase.jar:/data/chenzhenjing/code/panama/lib/commons-logging-1.0.4.jar:/data/chenzhenjing/code/panama/lib/commons-lang-2.4.jar:/data/chenzhenjing/code/panama/lib/commons-io-1.2.jar:/data/chenzhenjing/code/panama/lib/zookeeper-3.3.2.jar:/data/chenzhenjing/code/panama/lib/log4j-1.2.15.jar:/data/chenzhenjing/code/panama/lib/commons-codec-1.3.jar   DiffHbase.java   
 </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">*/</span>

<span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> java.io.BufferedReader;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> java.io.File;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> java.io.IOException;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> java.io.FileInputStream;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> java.io.InputStreamReader;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> java.io.FileOutputStream;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> java.io.OutputStreamWriter;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> java.io.StringReader;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> java.text.SimpleDateFormat;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> java.util.Date;

</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> java.io.IOException;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> java.util.ArrayList;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> java.util.List;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.conf.Configuration;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.HBaseConfiguration;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.KeyValue;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.client.Get;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.client.HTable;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.client.Result;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.client.ResultScanner;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.client.Scan;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.filter.CompareFilter.CompareOp;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.filter.FilterList;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.filter.SingleColumnValueFilter;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.util.Bytes;

</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">class</span><span style="line-height:1.5 !important;"> ColumnUtils {

    </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">public</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">static</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">byte</span><span style="line-height:1.5 !important;">[] getFamily(String column){
        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">return</span> getBytes(column, 0<span style="line-height:1.5 !important;">);
    }

    </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">public</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">static</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">byte</span><span style="line-height:1.5 !important;">[] getQualifier(String column){
        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">return</span> getBytes(column, 1<span style="line-height:1.5 !important;">);
    }

    </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">private</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">static</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">byte</span>[] getBytes(String column , <span style="color:rgb(0,0,255);line-height:1.5 !important;">int</span><span style="line-height:1.5 !important;"> offset){
        String[] split </span>= column.split(":"<span style="line-height:1.5 !important;">);
        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">return</span> Bytes.toBytes(offset &gt; split.length -1 ? split[0<span style="line-height:1.5 !important;">] :split[offset]);
    }
}

</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">public</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">class</span><span style="line-height:1.5 !important;"> DiffHbase
{
    </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">public</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">static</span> Configuration configuration = <span style="color:rgb(0,0,255);line-height:1.5 !important;">null</span><span style="line-height:1.5 !important;">;
    </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">static</span><span style="line-height:1.5 !important;">
    {
        configuration </span>=<span style="line-height:1.5 !important;"> HBaseConfiguration.create();
        configuration.set(</span>"hbase.zookeeper.quorum", "idc01-hd-ds-01,idc01-hd-ds-02,idc01-hd-ds-03"<span style="line-height:1.5 !important;">);
    }

    </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">public</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">static</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">void</span> selectRowKey(String tablename, String rowKey) <span style="color:rgb(0,0,255);line-height:1.5 !important;">throws</span><span style="line-height:1.5 !important;"> IOException
    {
        HTable table </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> HTable(configuration, tablename);
        Get g </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> Get(rowKey.getBytes());
        Result rs </span>=<span style="line-height:1.5 !important;"> table.get(g);

        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">for</span><span style="line-height:1.5 !important;"> (KeyValue kv : rs.raw())
        {
            System.out.println(</span>"--------------------" + <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span> String(kv.getRow()) + "----------------------------"<span style="line-height:1.5 !important;">);
            System.out.println(</span>"Column Family: " + <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> String(kv.getFamily()));
            System.out.println(</span>"Column       :" + <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span> String(kv.getQualifier()) + "t"<span style="line-height:1.5 !important;">);
            System.out.println(</span>"value        : " + <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> String(kv.getValue()));
        }
    }

    </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">public</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">static</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">void</span> selectRowKeyFamily(String tablename, String rowKey, String family) <span style="color:rgb(0,0,255);line-height:1.5 !important;">throws</span><span style="line-height:1.5 !important;"> IOException
    {
        HTable table </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> HTable(configuration, tablename);
        Get g </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> Get(rowKey.getBytes());
        g.addFamily(Bytes.toBytes(family));
        Result rs </span>=<span style="line-height:1.5 !important;"> table.get(g);
        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">for</span><span style="line-height:1.5 !important;"> (KeyValue kv : rs.raw())
        {
            System.out.println(</span>"--------------------" + <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span> String(kv.getRow()) + "----------------------------"<span style="line-height:1.5 !important;">);
            System.out.println(</span>"Column Family: " + <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> String(kv.getFamily()));
            System.out.println(</span>"Column       :" + <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span> String(kv.getQualifier()) + "t"<span style="line-height:1.5 !important;">);
            System.out.println(</span>"value        : " + <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> String(kv.getValue()));
        }
    }

    </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">public</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">static</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">void</span><span style="line-height:1.5 !important;"> selectRowKeyFamilyColumn(String tablename, String rowKey, String family, String column)
        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">throws</span><span style="line-height:1.5 !important;"> IOException
    {
        HTable table </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> HTable(configuration, tablename);
        Get g </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> Get(rowKey.getBytes());
        g.addColumn(family.getBytes(), column.getBytes());

        Result rs </span>=<span style="line-height:1.5 !important;"> table.get(g);

        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">for</span><span style="line-height:1.5 !important;"> (KeyValue kv : rs.raw())
        {
            System.out.println(</span>"--------------------" + <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span> String(kv.getRow()) + "----------------------------"<span style="line-height:1.5 !important;">);
            System.out.println(</span>"Column Family: " + <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> String(kv.getFamily()));
            System.out.println(</span>"Column       :" + <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span> String(kv.getQualifier()) + "t"<span style="line-height:1.5 !important;">);
            System.out.println(</span>"value        : " + <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> String(kv.getValue()));
        }
    }



    </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">private</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">static</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">final</span> String USAGE = "Usage: DiffHbase [-o outfile] tablename infile filterColumns..."<span style="line-height:1.5 !important;">;

    </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">/**</span><span style="color:rgb(0,128,0);line-height:1.5 !important;">
     * Prints the usage message and exists the program.
     * 
     * </span><span style="color:rgb(128,128,128);line-height:1.5 !important;">@param</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> message  The message to print first.
     </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">*/</span>
    <span style="color:rgb(0,0,255);line-height:1.5 !important;">private</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">static</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">void</span><span style="line-height:1.5 !important;"> printUsage(String message) {
        System.err.println(message);
        System.err.println(USAGE);
        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">throw</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> RuntimeException(USAGE);
    }

    </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">private</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">static</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">void</span><span style="line-height:1.5 !important;"> PrintId(String id, Result rs){
        String value </span>= Bytes.toString( rs.getValue(ColumnUtils.getFamily("info:url"), ColumnUtils.getQualifier("info:url"<span style="line-height:1.5 !important;">)));
        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">if</span>(value == <span style="color:rgb(0,0,255);line-height:1.5 !important;">null</span><span style="line-height:1.5 !important;">){
            System.out.println( id </span>+ "\tNULL"<span style="line-height:1.5 !important;">);
        }</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">else</span><span style="line-height:1.5 !important;">{
            System.out.println( id </span>+ "\t" +<span style="line-height:1.5 !important;"> value);
        }
    }

    </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">private</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">static</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">void</span><span style="line-height:1.5 !important;"> WriteId(String id, Result rs, FileOutputStream os){
        String value </span>= Bytes.toString( rs.getValue(ColumnUtils.getFamily("info:url"), ColumnUtils.getQualifier("info:url"<span style="line-height:1.5 !important;">)));
        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">try</span><span style="line-height:1.5 !important;">{
            </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">if</span>(value == <span style="color:rgb(0,0,255);line-height:1.5 !important;">null</span><span style="line-height:1.5 !important;">){
                os.write( (id </span>+ "\tNULL\n"<span style="line-height:1.5 !important;">).getBytes());
            }</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">else</span><span style="line-height:1.5 !important;">{
                os.write( (id </span>+ "\t" + value + "\n"<span style="line-height:1.5 !important;">).getBytes());
            }
        }
        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">catch</span><span style="line-height:1.5 !important;"> (IOException e) {
            e.printStackTrace();
        }
    }

    </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">private</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">static</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">void</span><span style="line-height:1.5 !important;"> PrintRow(String id, Result rs){

        System.out.println(</span>"--------------------" + id + "----------------------------"<span style="line-height:1.5 !important;">);
        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">for</span><span style="line-height:1.5 !important;"> (KeyValue kv : rs.raw())
        {
            System.out.println(</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span> String(kv.getFamily()) + ":" + <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span> String(kv.getQualifier()) + " : " + <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> String(kv.getValue()));
        }
    }

    </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">public</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">static</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">void</span> main(String[] args) <span style="color:rgb(0,0,255);line-height:1.5 !important;">throws</span><span style="line-height:1.5 !important;"> Exception
    { 
        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">if</span> (args.length &lt; 3<span style="line-height:1.5 !important;">) {
            printUsage(</span>"Too few arguments"<span style="line-height:1.5 !important;">);
        }

        String outfile </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">null</span><span style="line-height:1.5 !important;">;
        String tablename </span>= args[0<span style="line-height:1.5 !important;">];
        String dictfile  </span>= args[1<span style="line-height:1.5 !important;">];
        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">int</span> skilLen = 2<span style="line-height:1.5 !important;">;

        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">if</span>( args[0].equals("-o"<span style="line-height:1.5 !important;">)){
            outfile </span>= args[1<span style="line-height:1.5 !important;">];
            tablename </span>= args[2<span style="line-height:1.5 !important;">];
            dictfile  </span>= args[3<span style="line-height:1.5 !important;">];
            skilLen </span>= 4<span style="line-height:1.5 !important;">;
        }

        HTable table </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> HTable(configuration, tablename);

        String[] filterColumns </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span> String[args.length -<span style="line-height:1.5 !important;"> skilLen];
        System.arraycopy(args, skilLen, filterColumns, </span>0, args.length -<span style="line-height:1.5 !important;"> skilLen);

        System.out.println(</span>"filterColumns: "<span style="line-height:1.5 !important;">);
        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">for</span>(<span style="color:rgb(0,0,255);line-height:1.5 !important;">int</span> i=0; i&lt;filterColumns.length; ++<span style="line-height:1.5 !important;">i){
            System.out.println(</span>"\t" +<span style="line-height:1.5 !important;"> filterColumns[i]);
        }

        FileOutputStream os </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">null</span><span style="line-height:1.5 !important;">;
        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">if</span>(outfile != <span style="color:rgb(0,0,255);line-height:1.5 !important;">null</span><span style="line-height:1.5 !important;">){
            os </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> FileOutputStream(outfile);
        }
        
        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">int</span> count = 0<span style="line-height:1.5 !important;">;
        SimpleDateFormat df </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span> SimpleDateFormat("yyyy-MM-dd HH:mm:ss");<span style="color:rgb(0,128,0);line-height:1.5 !important;">//</span><span style="color:rgb(0,128,0);line-height:1.5 !important;">设置日期格式</span>
<span style="line-height:1.5 !important;">
        File srcFile </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> File(dictfile);
        FileInputStream in </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> FileInputStream(srcFile);
        InputStreamReader isr </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> InputStreamReader(in);
        BufferedReader br </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> BufferedReader(isr);
        String read </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">null</span><span style="line-height:1.5 !important;">;
        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">while</span> ((read = br.readLine()) != <span style="color:rgb(0,0,255);line-height:1.5 !important;">null</span><span style="line-height:1.5 !important;">) {
            String[] split </span>= read.trim().split("\\s");   <span style="color:rgb(0,128,0);line-height:1.5 !important;">//</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> space split</span>
            <span style="color:rgb(0,0,255);line-height:1.5 !important;">if</span>( split.length &lt; 1<span style="line-height:1.5 !important;"> ){
                System.out.println(</span>"Error line: " +<span style="line-height:1.5 !important;"> read);
                </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">continue</span><span style="line-height:1.5 !important;">;
            }

            </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">if</span>( ++count % 1000 == 0<span style="line-height:1.5 !important;">){
                System.out.println(df.format(</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span> Date()) + " : " + count + " rows processed." );  <span style="color:rgb(0,128,0);line-height:1.5 !important;">//</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> new Date()为获取当前系统时间</span>
<span style="line-height:1.5 !important;">            }
            </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">//</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> System.out.println("ROWKEY:" + split[0]);</span>
<span style="line-height:1.5 !important;">
            Get g </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span> Get(split[0<span style="line-height:1.5 !important;">].getBytes());
            Result rs </span>=<span style="line-height:1.5 !important;"> table.get(g);
            </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">if</span>( rs == <span style="color:rgb(0,0,255);line-height:1.5 !important;">null</span><span style="line-height:1.5 !important;">){
                System.out.println(</span>"No Result for " + split[0<span style="line-height:1.5 !important;">]);
                </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">continue</span><span style="line-height:1.5 !important;">;
            }

            </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">for</span>(<span style="color:rgb(0,0,255);line-height:1.5 !important;">int</span> i=0; i&lt;filterColumns.length; ++<span style="line-height:1.5 !important;">i){
                String value </span>=<span style="line-height:1.5 !important;"> Bytes.toString(rs.getValue(ColumnUtils.getFamily(filterColumns[i]), ColumnUtils.getQualifier(filterColumns[i])));
                </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">if</span>(value == <span style="color:rgb(0,0,255);line-height:1.5 !important;">null</span><span style="line-height:1.5 !important;">){
                    </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">if</span>( os == <span style="color:rgb(0,0,255);line-height:1.5 !important;">null</span><span style="line-height:1.5 !important;">){
                        PrintId(split[</span>0<span style="line-height:1.5 !important;">], rs);
                    }</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">else</span><span style="line-height:1.5 !important;">{
                        WriteId(split[</span>0<span style="line-height:1.5 !important;">], rs, os);
                    }

                    </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">//</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> PrintRow(split[0], rs);</span>
                    <span style="color:rgb(0,0,255);line-height:1.5 !important;">break</span><span style="line-height:1.5 !important;">;
                }
            }
        }

        br.close();
        isr.close();
        in.close();

    }
}</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy" style="line-height:1.5 !important;"><a title="复制代码" style="color:rgb(61,129,238);border:none !important;"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码" style="border:none rgb(221,221,221) !important;background-color:rgb(255,255,255);"></a></span></div>
</div>
<h2 style="font-family:'微软雅黑', sans-serif;font-size:21px;letter-spacing:-1px;line-height:25.2000007629395px;color:rgb(51,102,153);">
Hbase Mapreduce示例：全库扫描(大量数据)：</h2>
<div class="cnblogs_code" style="border:1px solid rgb(204,204,204);overflow:auto;color:rgb(0,0,0);font-family:'Courier New' !important;font-size:12px !important;background-color:rgb(245,245,245);">
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy" style="line-height:1.5 !important;"><a title="复制代码" style="color:rgb(61,129,238);border:none !important;"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码" style="border:none rgb(221,221,221) !important;background-color:rgb(255,255,255);"></a></span></div>
<pre style="font-family:'Courier New' !important;"><span style="color:rgb(0,0,255);line-height:1.5 !important;">package</span><span style="line-height:1.5 !important;"> com.hbase.mapreduce;

</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> java.io.File;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> java.io.FileInputStream;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> java.io.IOException;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> java.util.ArrayList;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> java.util.List;

</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.io.Text;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.conf.Configuration;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.fs.Path;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.HBaseConfiguration;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.HConstants;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.client.Scan;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.mapreduce.IdentityTableMapper;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.mapreduce.TableMapReduceUtil;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.io.ImmutableBytesWritable;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.mapreduce.Job;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.mapred.JobConf;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.util.GenericOptionsParser;

</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.filter.SingleColumnValueFilter;                                                                      
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.filter.CompareFilter;                                                                                
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.filter.CompareFilter.CompareOp;                                                                      
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.filter.BinaryComparator;                                                                             
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.util.Bytes; 

</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> com.goodhope.utils.ColumnUtils;

</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">public</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">class</span><span style="line-height:1.5 !important;"> ExportHbase {
    </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">private</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">static</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">final</span> String INFOCATEGORY = "info:storecategory"<span style="line-height:1.5 !important;">;

    </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">private</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">static</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">final</span> String USAGE = "Usage: ExportHbase " +
        "-r &lt;numReduceTasks&gt; -indexConf &lt;iconfFile&gt;\n" +
        "-indexDir &lt;indexDir&gt; -webSite &lt;amazon&gt; [-needupdate &lt;true&gt; -isVisible -startTime &lt;long&gt;] -table &lt;tableName&gt; -columns &lt;columnName1&gt; " +
        "[&lt;columnName2&gt; ...]"<span style="line-height:1.5 !important;">;

    </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">/**</span><span style="color:rgb(0,128,0);line-height:1.5 !important;">
     * Prints the usage message and exists the program.
     * 
     * </span><span style="color:rgb(128,128,128);line-height:1.5 !important;">@param</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> message  The message to print first.
     </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">*/</span>
    <span style="color:rgb(0,0,255);line-height:1.5 !important;">private</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">static</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">void</span><span style="line-height:1.5 !important;"> printUsage(String message) {
        System.err.println(message);
        System.err.println(USAGE);
        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">throw</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> RuntimeException(USAGE);
    }

    </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">/**</span><span style="color:rgb(0,128,0);line-height:1.5 !important;">
     * Creates a new job.
     * </span><span style="color:rgb(128,128,128);line-height:1.5 !important;">@param</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> conf 
     * 
     * </span><span style="color:rgb(128,128,128);line-height:1.5 !important;">@param</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> args  The command line arguments.
     * </span><span style="color:rgb(128,128,128);line-height:1.5 !important;">@throws</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> IOException When reading the configuration fails.
     </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">*/</span>
    <span style="color:rgb(0,0,255);line-height:1.5 !important;">public</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">static</span><span style="line-height:1.5 !important;"> Job createSubmittableJob(Configuration conf, String[] args) 
        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">throws</span><span style="line-height:1.5 !important;"> IOException {
        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">if</span> (args.length &lt; 7<span style="line-height:1.5 !important;">) {
            printUsage(</span>"Too few arguments"<span style="line-height:1.5 !important;">);
        }

        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">int</span> numReduceTasks = 1<span style="line-height:1.5 !important;">;
        String iconfFile </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">null</span><span style="line-height:1.5 !important;">;
        String indexDir </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">null</span><span style="line-height:1.5 !important;">;
        String tableName </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">null</span><span style="line-height:1.5 !important;">;
        String website </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">null</span><span style="line-height:1.5 !important;">;
        String needupdate </span>= ""<span style="line-height:1.5 !important;">;
        String expectShopGrade </span>= ""<span style="line-height:1.5 !important;">;
        String dino </span>= "6"<span style="line-height:1.5 !important;">;
        String isdebug </span>= "0"<span style="line-height:1.5 !important;">;
        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">long</span> debugThreshold = 10000<span style="line-height:1.5 !important;">;
        String debugThresholdStr </span>=<span style="line-height:1.5 !important;"> Long.toString(debugThreshold);
        String queue </span>= "offline"<span style="line-height:1.5 !important;">;

        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">long</span> endTime =<span style="line-height:1.5 !important;">  Long.MAX_VALUE;
        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">int</span> maxversions = 1<span style="line-height:1.5 !important;">;
        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">long</span> startTime = System.currentTimeMillis() - 28*24*60*60*1000l<span style="line-height:1.5 !important;">;
        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">long</span> distartTime = System.currentTimeMillis() - 30*24*60*60*1000l<span style="line-height:1.5 !important;">;
        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">long</span> diusedTime = System.currentTimeMillis() - 30*24*60*60*1000l<span style="line-height:1.5 !important;">;
        String startTimeStr </span>=<span style="line-height:1.5 !important;"> Long.toString(startTime);
        String diusedTimeStr </span>=<span style="line-height:1.5 !important;"> Long.toString(diusedTime);
        String quorum </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">null</span><span style="line-height:1.5 !important;">;

        String isVisible </span>= ""<span style="line-height:1.5 !important;">;
        List</span>&lt;String&gt; columns = <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span> ArrayList&lt;String&gt;<span style="line-height:1.5 !important;">() ;  

        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">boolean</span> bFilter = <span style="color:rgb(0,0,255);line-height:1.5 !important;">false</span><span style="line-height:1.5 !important;">;

        </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">//</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> parse args</span>
        <span style="color:rgb(0,0,255);line-height:1.5 !important;">for</span> (<span style="color:rgb(0,0,255);line-height:1.5 !important;">int</span> i = 0; i &lt; args.length - 1; i++<span style="line-height:1.5 !important;">) {
            </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">if</span> ("-r"<span style="line-height:1.5 !important;">.equals(args[i])) {
                numReduceTasks </span>= Integer.parseInt(args[++<span style="line-height:1.5 !important;">i]);
            } </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">else</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">if</span> ("-indexConf"<span style="line-height:1.5 !important;">.equals(args[i])) {
                iconfFile </span>= args[++<span style="line-height:1.5 !important;">i];
            } </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">else</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">if</span> ("-indexDir"<span style="line-height:1.5 !important;">.equals(args[i])) {
                indexDir </span>= args[++<span style="line-height:1.5 !important;">i];
            } </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">else</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">if</span> ("-table"<span style="line-height:1.5 !important;">.equals(args[i])) {
                tableName </span>= args[++<span style="line-height:1.5 !important;">i];
            } </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">else</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">if</span> ("-webSite"<span style="line-height:1.5 !important;">.equals(args[i])) {
                website </span>= args[++<span style="line-height:1.5 !important;">i];
            } </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">else</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">if</span> ("-startTime"<span style="line-height:1.5 !important;">.equals(args[i])) {
                startTimeStr </span>= args[++<span style="line-height:1.5 !important;">i];
                startTime </span>=<span style="line-height:1.5 !important;"> Long.parseLong(startTimeStr);
            } </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">else</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">if</span> ("-needupdate"<span style="line-height:1.5 !important;">.equals(args[i])) {
                needupdate </span>= args[++<span style="line-height:1.5 !important;">i];
            } </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">else</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">if</span> ("-isVisible"<span style="line-height:1.5 !important;">.equals(args[i])) {
                isVisible </span>= "true"<span style="line-height:1.5 !important;">;
            } </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">else</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">if</span> ("-shopgrade"<span style="line-height:1.5 !important;">.equals(args[i])) {
                expectShopGrade </span>= args[++<span style="line-height:1.5 !important;">i]; 
            } </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">else</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">if</span> ("-queue"<span style="line-height:1.5 !important;">.equals(args[i])) {
                queue </span>= args[++<span style="line-height:1.5 !important;">i];
            } </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">else</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">if</span> ("-dino"<span style="line-height:1.5 !important;">.equals(args[i])) {
                dino </span>= args[++<span style="line-height:1.5 !important;">i];
            } </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">else</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">if</span> ("-maxversions"<span style="line-height:1.5 !important;">.equals(args[i])) {
                maxversions </span>= Integer.parseInt(args[++<span style="line-height:1.5 !important;">i]);
            } </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">else</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">if</span> ("-distartTime"<span style="line-height:1.5 !important;">.equals(args[i])) {
                distartTime </span>= Long.parseLong(args[++<span style="line-height:1.5 !important;">i]); 
            } </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">else</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">if</span> ("-diendTime"<span style="line-height:1.5 !important;">.equals(args[i])) {
                endTime </span>= Long.parseLong(args[++<span style="line-height:1.5 !important;">i]);
            } </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">else</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">if</span> ("-diusedTime"<span style="line-height:1.5 !important;">.equals(args[i])) {
                diusedTimeStr </span>= args[++<span style="line-height:1.5 !important;">i];
                diusedTime </span>=<span style="line-height:1.5 !important;"> Long.parseLong(diusedTimeStr);
            } </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">else</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">if</span> ("-quorum"<span style="line-height:1.5 !important;">.equals(args[i])) {
                quorum </span>= args[++<span style="line-height:1.5 !important;">i];
            } </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">else</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">if</span> ("-filter"<span style="line-height:1.5 !important;">.equals(args[i])) {
                bFilter </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">true</span><span style="line-height:1.5 !important;">;
            } </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">else</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">if</span> ("-columns"<span style="line-height:1.5 !important;">.equals(args[i])) {
                columns.add(args[</span>++<span style="line-height:1.5 !important;">i]);
                </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">while</span> (i + 1 &lt; args.length &amp;&amp; !args[i + 1].startsWith("-"<span style="line-height:1.5 !important;">)) {
                    String columnname </span>= args[++<span style="line-height:1.5 !important;">i];
                    columns.add(columnname);
                    System.out.println(</span>"args column----: " +<span style="line-height:1.5 !important;"> columnname);
                }
            } </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">else</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">if</span> ("-debugThreshold"<span style="line-height:1.5 !important;">.equals(args[i])) {
                isdebug </span>= "1"<span style="line-height:1.5 !important;">;
                debugThresholdStr </span>= args[++<span style="line-height:1.5 !important;">i];
                debugThreshold </span>=<span style="line-height:1.5 !important;">  Long.parseLong( debugThresholdStr );
            }
            </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">else</span><span style="line-height:1.5 !important;"> {
                printUsage(</span>"Unsupported option " +<span style="line-height:1.5 !important;"> args[i]);
            }
        }

        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">if</span> (distartTime &gt;<span style="line-height:1.5 !important;"> endTime) {
            printUsage(</span>"distartTime must &lt;= diendTime"<span style="line-height:1.5 !important;">);  
        }

        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">if</span> (indexDir == <span style="color:rgb(0,0,255);line-height:1.5 !important;">null</span> || tableName == <span style="color:rgb(0,0,255);line-height:1.5 !important;">null</span> ||<span style="line-height:1.5 !important;"> columns.isEmpty()) {
            printUsage(</span>"Index directory, table name and at least one column must " +
                    "be specified"<span style="line-height:1.5 !important;">);
        }

        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">if</span> (iconfFile != <span style="color:rgb(0,0,255);line-height:1.5 !important;">null</span><span style="line-height:1.5 !important;">) {
            </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">//</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> set index configuration content from a file</span>
            String content =<span style="line-height:1.5 !important;"> readContent(iconfFile);
            conf.set(</span>"hbase.index.conf"<span style="line-height:1.5 !important;">, content);
            conf.set(</span>"hbase.website.name"<span style="line-height:1.5 !important;">, website);
            conf.set(</span>"hbase.needupdate.productDB"<span style="line-height:1.5 !important;">, needupdate);
            conf.set(</span>"hbase.expect.shopgrade"<span style="line-height:1.5 !important;">, expectShopGrade);
            conf.set(</span>"hbase.di.no"<span style="line-height:1.5 !important;">, dino);
            conf.set(</span>"hbase.expect.item.visible"<span style="line-height:1.5 !important;">, isVisible);
            conf.set(</span>"hbase.index.startTime"<span style="line-height:1.5 !important;">, startTimeStr);
            conf.set(</span>"hbase.index.diusedTime"<span style="line-height:1.5 !important;">, diusedTimeStr);
            conf.set(</span>"hbase.index.debugThreshold"<span style="line-height:1.5 !important;">, debugThresholdStr);
            conf.set(</span>"hbase.index.debug"<span style="line-height:1.5 !important;">, isdebug);
            </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">if</span> (quorum != <span style="color:rgb(0,0,255);line-height:1.5 !important;">null</span><span style="line-height:1.5 !important;">) {
                conf.set(</span>"hbase.zookeeper.quorum"<span style="line-height:1.5 !important;">, quorum);
            }
            String temp </span>= ""<span style="line-height:1.5 !important;">;
            </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">for</span><span style="line-height:1.5 !important;"> (String column : columns) {
                temp </span>= temp + column + "|"<span style="line-height:1.5 !important;">;
            }
            temp </span>= temp.substring(0, temp.length() - 1<span style="line-height:1.5 !important;">);
            conf.set(</span>"hbase.index.column"<span style="line-height:1.5 !important;">, temp);
            System.out.println(</span>"hbase.index.column: " +<span style="line-height:1.5 !important;"> temp);
        }


        Job job </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span> Job(conf, "export data from table " +<span style="line-height:1.5 !important;"> tableName);
        ((JobConf) job.getConfiguration()).setQueueName(queue);

        </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">//</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> number of indexes to partition into</span>
<span style="line-height:1.5 !important;">        job.setNumReduceTasks(numReduceTasks);
        Scan scan </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> Scan();
        scan.setCacheBlocks(</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">false</span><span style="line-height:1.5 !important;">);

        </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">//</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> limit scan range</span>
<span style="line-height:1.5 !important;">        scan.setTimeRange(distartTime, endTime);
        </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">//</span><span style="color:rgb(0,128,0);line-height:1.5 !important;">  scan.setMaxVersions(maxversions);</span>
        scan.setMaxVersions(1<span style="line-height:1.5 !important;">);

        </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">/*</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> limit scan columns </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">*/</span>
        <span style="color:rgb(0,0,255);line-height:1.5 !important;">for</span><span style="line-height:1.5 !important;"> (String column : columns) {
            scan.addColumn(ColumnUtils.getFamily(column), ColumnUtils.getQualifier(column));
            scan.addFamily(ColumnUtils.getFamily(column));
        }

        </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">//</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> set filter</span>
        <span style="color:rgb(0,0,255);line-height:1.5 !important;">if</span><span style="line-height:1.5 !important;">( bFilter ){
            System.out.println(</span>"only export guangtaobao data. "<span style="line-height:1.5 !important;">);
            SingleColumnValueFilter filter </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> SingleColumnValueFilter(
                    Bytes.toBytes(</span>"info"<span style="line-height:1.5 !important;">),
                    Bytes.toBytes(</span>"producttype"<span style="line-height:1.5 !important;">),
                    CompareFilter.CompareOp.EQUAL,
                    </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span> BinaryComparator(Bytes.toBytes("guangtaobao"<span style="line-height:1.5 !important;">)) );
            filter.setFilterIfMissing(</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">true</span><span style="line-height:1.5 !important;">);
            scan.setFilter(filter);
        }

        TableMapReduceUtil.initTableMapperJob(tableName, scan, ExportHbaseMapper.</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">class</span><span style="line-height:1.5 !important;">,
                Text.</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">class</span>, Text.<span style="color:rgb(0,0,255);line-height:1.5 !important;">class</span><span style="line-height:1.5 !important;">, job);
        </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">//</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> job.setReducerClass(ExportHbaseReducer.class);</span>
        FileOutputFormat.setOutputPath(job, <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> Path(indexDir));


        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">return</span><span style="line-height:1.5 !important;"> job;
    }

    </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">/**</span><span style="color:rgb(0,128,0);line-height:1.5 !important;">
     * Reads xml file of indexing configurations.  The xml format is similar to
     * hbase-default.xml and hadoop-default.xml. For an example configuration,
     * see the &lt;code&gt;createIndexConfContent&lt;/code&gt; method in TestTableIndex.
     * 
     * </span><span style="color:rgb(128,128,128);line-height:1.5 !important;">@param</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> fileName  The file to read.
     * </span><span style="color:rgb(128,128,128);line-height:1.5 !important;">@return</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> XML configuration read from file.
     * </span><span style="color:rgb(128,128,128);line-height:1.5 !important;">@throws</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> IOException When the XML is broken.
     </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">*/</span>
    <span style="color:rgb(0,0,255);line-height:1.5 !important;">private</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">static</span> String readContent(String fileName) <span style="color:rgb(0,0,255);line-height:1.5 !important;">throws</span><span style="line-height:1.5 !important;"> IOException {
        File file </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> File(fileName);
        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">int</span> length = (<span style="color:rgb(0,0,255);line-height:1.5 !important;">int</span><span style="line-height:1.5 !important;">) file.length();
        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">if</span> (length == 0<span style="line-height:1.5 !important;">) {
            printUsage(</span>"Index configuration file " + fileName + " does not exist"<span style="line-height:1.5 !important;">);
        }

        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">int</span> bytesRead = 0<span style="line-height:1.5 !important;">;
        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">byte</span>[] bytes = <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">byte</span><span style="line-height:1.5 !important;">[length];
        FileInputStream fis </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> FileInputStream(file);

        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">try</span><span style="line-height:1.5 !important;"> {
            </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">//</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> read entire file into content</span>
            <span style="color:rgb(0,0,255);line-height:1.5 !important;">while</span> (bytesRead &lt;<span style="line-height:1.5 !important;"> length) {
                </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">int</span> read = fis.read(bytes, bytesRead, length -<span style="line-height:1.5 !important;"> bytesRead);
                </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">if</span> (read &gt; 0<span style="line-height:1.5 !important;">) {
                    bytesRead </span>+=<span style="line-height:1.5 !important;"> read;
                } </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">else</span><span style="line-height:1.5 !important;"> {
                    </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">break</span><span style="line-height:1.5 !important;">;
                }
            }
        } </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">finally</span><span style="line-height:1.5 !important;"> {
            fis.close();
        }

        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">return</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span> String(bytes, 0<span style="line-height:1.5 !important;">, bytesRead, HConstants.UTF8_ENCODING);
    }

    </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">/**</span><span style="color:rgb(0,128,0);line-height:1.5 !important;">
     * The main entry point.
     * 
     * </span><span style="color:rgb(128,128,128);line-height:1.5 !important;">@param</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> args  The command line arguments.
     * </span><span style="color:rgb(128,128,128);line-height:1.5 !important;">@throws</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> Exception When running the job fails.
     </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">*/</span>
    <span style="color:rgb(0,0,255);line-height:1.5 !important;">public</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">static</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">void</span> main(String[] args) <span style="color:rgb(0,0,255);line-height:1.5 !important;">throws</span><span style="line-height:1.5 !important;"> Exception {
        Configuration conf </span>=<span style="line-height:1.5 !important;"> HBaseConfiguration.create();
        String[] otherArgs </span>= 
            <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> GenericOptionsParser(conf, args).getRemainingArgs();
        Job job </span>=<span style="line-height:1.5 !important;"> createSubmittableJob(conf, otherArgs);
        System.exit(job.waitForCompletion(</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">true</span>) ? 0 : 1<span style="line-height:1.5 !important;">);
    }

}

</span><span style="color:rgb(0,128,0);line-height:1.5 !important;">//////////////////////////////////////////////////////////
</span>
<span style="color:rgb(0,0,255);line-height:1.5 !important;">package</span><span style="line-height:1.5 !important;"> com.hbase.mapreduce;

</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> java.io.IOException;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> java.util.List;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> java.util.ArrayList;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> java.lang.String;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> java.lang.StringBuffer;

</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.io.Text;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.conf.Configurable;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.conf.Configuration;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.commons.lang.StringUtils;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.client.Result;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.io.ImmutableBytesWritable;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.mapreduce.TableMapper;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.util.Bytes;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.KeyValue;

</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> com.goodhope.utils.ColumnUtils;


</span><span style="color:rgb(0,128,0);line-height:1.5 !important;">/**</span><span style="color:rgb(0,128,0);line-height:1.5 !important;">
 * Pass the given key and record as-is to the reduce phase.
 </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">*/</span><span style="line-height:1.5 !important;">
@SuppressWarnings(</span>"deprecation"<span style="line-height:1.5 !important;">)
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">public</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">class</span> ExportHbaseMapper <span style="color:rgb(0,0,255);line-height:1.5 !important;">extends</span> TableMapper&lt;Text,Text&gt; <span style="color:rgb(0,0,255);line-height:1.5 !important;">implements</span><span style="line-height:1.5 !important;"> Configurable {
    </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">private</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">static</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">final</span> Text keyTEXT = <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> Text();
    </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">private</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">static</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">final</span> Text SENDTEXT = <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> Text();

    </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">private</span> Configuration conf = <span style="color:rgb(0,0,255);line-height:1.5 !important;">null</span><span style="line-height:1.5 !important;">;

    </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">private</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">long</span> startTime = 0<span style="line-height:1.5 !important;">;
    List</span>&lt;String&gt; columnMap = <span style="color:rgb(0,0,255);line-height:1.5 !important;">null</span><span style="line-height:1.5 !important;">;

    </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">private</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">long</span> rCount = 0<span style="line-height:1.5 !important;">;
    </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">private</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">long</span> errCount = 0<span style="line-height:1.5 !important;">;
    </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">private</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">int</span>  debug  = 0<span style="line-height:1.5 !important;">;
    </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">private</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">long</span> thresCount  = 10000<span style="line-height:1.5 !important;">;

    </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">public</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">void</span> map(ImmutableBytesWritable key, Result value, Context context) <span style="color:rgb(0,0,255);line-height:1.5 !important;">throws</span><span style="line-height:1.5 !important;"> IOException, InterruptedException {

        rCount</span>++<span style="line-height:1.5 !important;">;

        String itemid </span>=<span style="line-height:1.5 !important;"> Bytes.toString(key.get());
        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">if</span> (itemid.contains("&amp;"<span style="line-height:1.5 !important;">)) {
            context.getCounter(</span>"Error", "rowkey contains \"&amp;\"").increment(1<span style="line-height:1.5 !important;">);
            </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">return</span><span style="line-height:1.5 !important;">;
        }

        StringBuffer outstr </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> StringBuffer();
        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">for</span><span style="line-height:1.5 !important;"> (String col : columnMap) {

            String tmp </span>=<span style="line-height:1.5 !important;"> Bytes.toString(value.getValue(ColumnUtils.getFamily(col), ColumnUtils.getQualifier(col)));
            </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">if</span> (tmp == <span style="color:rgb(0,0,255);line-height:1.5 !important;">null</span><span style="line-height:1.5 !important;">){
                context.getCounter(</span>"Error", col+" No value in hbase").increment(1<span style="line-height:1.5 !important;">);
                
                errCount</span>++<span style="line-height:1.5 !important;">;
                </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">if</span>( debug &gt; 0 &amp;&amp; (errCount % thresCount == 0<span style="line-height:1.5 !important;">)){
                    System.err.println( itemid </span>+ ": doesn't has " + col + " data!"<span style="line-height:1.5 !important;">);
                }

                outstr.append(</span>"NULL" + "\t"<span style="line-height:1.5 !important;">);
            }</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">else</span><span style="line-height:1.5 !important;">{
                </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">if</span>( tmp.contains("guangtaobao"<span style="line-height:1.5 !important;">) ){
                    outstr.append(</span>"1" + "\t"<span style="line-height:1.5 !important;">);
                }</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">else</span><span style="line-height:1.5 !important;">{
                    outstr.append(tmp.trim() </span>+ "\t"<span style="line-height:1.5 !important;">);
                }
            }
        }

        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">if</span> ( !<span style="line-height:1.5 !important;"> outstr.toString().isEmpty() ) {

            SENDTEXT.set( outstr.toString() );
            keyTEXT.set(itemid);
            context.write(keyTEXT, SENDTEXT);

            </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">if</span>( debug &gt; 0 &amp;&amp; (rCount % thresCount*10000 == 0<span style="line-height:1.5 !important;">)){
                System.out.println( SENDTEXT.toString() </span>+<span style="line-height:1.5 !important;"> keyTEXT.toString() );
            }
        }
        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">else</span><span style="line-height:1.5 !important;">
        {
            context.getCounter(</span>"Error", "No Colume output").increment(1<span style="line-height:1.5 !important;">);
            </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">return</span><span style="line-height:1.5 !important;">;
        }
    }

    </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">/**</span><span style="color:rgb(0,128,0);line-height:1.5 !important;">
     * Returns the current configuration.
     * 
     * </span><span style="color:rgb(128,128,128);line-height:1.5 !important;">@return</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> The current configuration.
     * </span><span style="color:rgb(128,128,128);line-height:1.5 !important;">@see</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> org.apache.hadoop.conf.Configurable#getConf()
     </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">*/</span><span style="line-height:1.5 !important;">
    @Override
        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">public</span><span style="line-height:1.5 !important;"> Configuration getConf() {
            </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">return</span><span style="line-height:1.5 !important;"> conf;
        }

    </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">/**</span><span style="color:rgb(0,128,0);line-height:1.5 !important;">
     * Sets the configuration. This is used to set up the index configuration.
     * 
     * </span><span style="color:rgb(128,128,128);line-height:1.5 !important;">@param</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> configuration
     *            The configuration to set.
     * </span><span style="color:rgb(128,128,128);line-height:1.5 !important;">@see</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> org.apache.hadoop.conf.Configurable#setConf(org.apache.hadoop.conf.Configuration)
     </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">*/</span><span style="line-height:1.5 !important;">
    @Override
        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">public</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">void</span><span style="line-height:1.5 !important;"> setConf(Configuration configuration) {
            </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">this</span>.conf =<span style="line-height:1.5 !important;"> configuration;
            
            startTime </span>= Long.parseLong(conf.get("hbase.index.startTime"<span style="line-height:1.5 !important;">));
            thresCount </span>= Long.parseLong(conf.get("hbase.index.debugThreshold"<span style="line-height:1.5 !important;">));
            debug </span>= Integer.parseInt(conf.get("hbase.index.debug"<span style="line-height:1.5 !important;">));

            String[] columns </span>= conf.get("hbase.index.column").split("\\|"<span style="line-height:1.5 !important;">);

            columnMap </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span> ArrayList&lt;String&gt;<span style="line-height:1.5 !important;">();
            </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">for</span><span style="line-height:1.5 !important;"> (String column : columns) {
                System.out.println(</span>"Output column: " +<span style="line-height:1.5 !important;"> column);

                columnMap.add(column);
            }

        }

}


</span><span style="color:rgb(0,128,0);line-height:1.5 !important;">//////////////////////////////////////////////////////////
</span>
<span style="color:rgb(0,0,255);line-height:1.5 !important;">package</span><span style="line-height:1.5 !important;"> com.hbase.utils;

</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.hbase.util.Bytes;

</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">public</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">class</span><span style="line-height:1.5 !important;"> ColumnUtils {

        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">public</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">static</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">byte</span><span style="line-height:1.5 !important;">[] getFamily(String column){
                </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">return</span> getBytes(column, 0<span style="line-height:1.5 !important;">);
        }

        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">public</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">static</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">byte</span><span style="line-height:1.5 !important;">[] getQualifier(String column){
                </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">return</span> getBytes(column, 1<span style="line-height:1.5 !important;">);
        }

        </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">private</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">static</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">byte</span>[] getBytes(String column , <span style="color:rgb(0,0,255);line-height:1.5 !important;">int</span><span style="line-height:1.5 !important;"> offset){
                String[] split </span>= column.split(":"<span style="line-height:1.5 !important;">);
                </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">return</span> Bytes.toBytes(offset &gt; split.length -1 ? split[0<span style="line-height:1.5 !important;">] :split[offset]);
        }
}</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy" style="line-height:1.5 !important;"><a title="复制代码" style="color:rgb(61,129,238);border:none !important;"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码" style="border:none rgb(221,221,221) !important;background-color:rgb(255,255,255);"></a></span></div>
</div>
</div>
<div id="MySignature" style="border:1px solid rgb(232,231,208);min-height:10px;color:rgb(102,102,102);font-family:Georgia, 'Times New Roman', Times, sans-serif;font-size:14px;line-height:25.2000007629395px;background-color:rgb(248,248,238);">
<div id="AllanboltSignature"></div>
</div>
            </div>
                </div>