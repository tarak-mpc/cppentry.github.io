---
layout:     post
title:      Hbase数据源在linux上单机搭建，windows实现远程操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/QCIWYY/article/details/81358343				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>前提条件Hadoop环境已经搭建好</p>

<p>1.官网下载hbase：http://archive.apache.org/dist/hbase<br>
2.解压hbase： tar -zxvf hbase-1.4.5-bin.tar.gz -C /temp/<br>
3.进入到hbase的conf目录下<br>
①vim hbase-env.sh<br>
添加export JAVA_HOME=/usr/java/jdk1.8.0_11<br>
②vim hbase-site.xml<br>
&lt;configuration&gt;<br>
        &lt;!-- 指定hbase在HDFS上存储的路径 --&gt;<br>
        &lt;property&gt;<br>
                &lt;name&gt;hbase.rootdir&lt;/name&gt;<br>
                &lt;value&gt;file:///hbaseSpace&lt;/value&gt;<br>
        &lt;/property&gt;<br>
4.进入到bin目录启动hbase<br>
./start-hbase.sh<br>
5.使用./hbase shell进行表操作</p>

<p>启动成功之后，windows便可以远程操作linux上的Hbase数据库了</p>

<p>首先在windows系统的C:\Windows\System32\drivers\etc\hosts文件.<br>
对应到hbase运行服务器的ip,比如:<br>
192.168.2.6 master</p>

<p>然后写java代码调用。</p>

<pre class="has">
<code class="language-java">package hbaseTest;


import java.io.IOException;
import java.util.ArrayList;
import java.util.List;
 
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.HColumnDescriptor;
import org.apache.hadoop.hbase.HTableDescriptor;
import org.apache.hadoop.hbase.KeyValue;
import org.apache.hadoop.hbase.client.Delete;
import org.apache.hadoop.hbase.client.Get;
import org.apache.hadoop.hbase.client.HBaseAdmin;
import org.apache.hadoop.hbase.client.HTable;
import org.apache.hadoop.hbase.client.HTablePool;
import org.apache.hadoop.hbase.client.Put;
import org.apache.hadoop.hbase.client.Result;
import org.apache.hadoop.hbase.client.ResultScanner;
import org.apache.hadoop.hbase.client.Scan;
 
public class HbaseTest {
	private HBaseAdmin admin = null;
	// 定义配置对象HBaseConfiguration
	private HBaseConfiguration cfg = null;
 
	public HbaseTest() throws Exception {
		Configuration HBASE_CONFIG = new Configuration();
 
		HBASE_CONFIG.set("hbase.zookeeper.quorum", "192.168.192.129");
 
		HBASE_CONFIG.set("hbase.zookeeper.property.clientPort", "2181");
 
		cfg = new HBaseConfiguration(HBASE_CONFIG);
 
		admin = new HBaseAdmin(cfg);
	}
 
	// 创建一张表，指定表名，列族
	public void createTable(String tableName, String columnFarily)
			throws Exception {
 
		if (admin.tableExists(tableName)) {
			System.out.println(tableName + "存在！");
			System.exit(0);
		} else {
			HTableDescriptor tableDesc = new HTableDescriptor(tableName);
			tableDesc.addFamily(new HColumnDescriptor(columnFarily));
			admin.createTable(tableDesc);
			System.out.println("创建表成功！");
		}
	}
 
	// Hbase获取所有的表信息
	public List getAllTables() {
		List&lt;String&gt; tables = null;
		if (admin != null) {
			try {
				HTableDescriptor[] allTable = admin.listTables();
				if (allTable.length &gt; 0)
					tables = new ArrayList&lt;String&gt;();
				for (HTableDescriptor hTableDescriptor : allTable) {
					tables.add(hTableDescriptor.getNameAsString());
					System.out.println(hTableDescriptor.getNameAsString());
				}
			} catch (IOException e) {
				e.printStackTrace();
			}
		}
		return tables;
	}
 
	// Hbase中往某个表中添加一条记录
	@SuppressWarnings("deprecation")
	public boolean addOneRecord(String table, String key, String family,
			String col, byte[] dataIn) {
		HTablePool tp = new HTablePool(cfg, 1000);
		//HTable tb = (HTable) tp.getTable(table);
		Put put = new Put(key.getBytes());
		put.add(family.getBytes(), col.getBytes(), dataIn);
		try {
			//tb.put(put);
			//如今应用的api版本中pool.getTable返回的类型是HTableInterface ，无法强转为HTable
	        tp.getTable(table).put(put);
			System.out.println("插入数据条" + key + "成功！！！");
			return true;
		} catch (IOException e) {
			System.out.println("插入数据条" + key + "失败！！！");
			return false;
		}
	}
 
	// Hbase表中记录信息的查询
	@SuppressWarnings("deprecation")
	public void getValueFromKey(String table, String key) {
		HTablePool tp = new HTablePool(cfg, 1000);
		//HTable tb = (HTable) tp.getTable(table);
		Get get = new Get(key.getBytes());
		try {
			Result rs = tp.getTable(table).get(get);
			if (rs.raw().length == 0) {
				System.out.println("不存在关键字为" + key + "的行！!");
 
			} else {
				for (KeyValue kv : rs.raw()) {
					System.out.println(new String(kv.getKey()) + " "
							+ new String(kv.getValue()));
				}
 
			}
		} catch (IOException e) {
			e.printStackTrace();
		}
	}
 
	// 显示所有数据，通过HTable Scan类获取已有表的信息
	public void getAllData(String tableName) throws Exception {
		HTable table = new HTable(cfg, tableName);
		Scan scan = new Scan();
		ResultScanner rs = table.getScanner(scan);
		for (Result r : rs) {
			for (KeyValue kv : r.raw()) {
				System.out.println(new String(kv.getKey())
						+ new String(kv.getValue()));
			}
		}
	}
 
	// Hbase表中记录信息的删除
	public boolean deleteRecord(String table, String key) {
		HTablePool tp = new HTablePool(cfg, 1000);
		//HTable tb = (HTable) tp.getTable(table);
		Delete de = new Delete(key.getBytes());
		try {
			tp.getTable(table).delete(de);
			return true;
		} catch (IOException e) {
			System.out.println("删除记录" + key + "异常！！！");
			return false;
		}
	}
 
	// Hbase中表的删除
	public boolean deleteTable(String table) {
		try {
			if (admin.tableExists(table)) {
				admin.disableTable(table);
				admin.deleteTable(table);
				System.out.println("删除表" + table + "!!!");
			}
			return true;
		} catch (IOException e) {
			System.out.println("删除表" + table + "异常!!!");
			return false;
		}
	}
 
	// 测试函数
	public static void main(String[] args) {
		try {
			HbaseTest hbase = new HbaseTest();
			//hbase.createTable("student", "fam1");
			// hbase.getAllTables();
 
			 //hbase.addOneRecord("student","id1","fam1","name","Jack".getBytes());
			// hbase.addOneRecord("student","id1","fam1","address","HZ".getBytes());
			 hbase.getValueFromKey("student","id1");
			 //hbase.getAllData("student");
 
			//hbase.deleteRecord("student", "id1");
			
			//hbase.deleteTable("student");
			
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
}
</code></pre>

<p> </p>

<p> </p>            </div>
                </div>