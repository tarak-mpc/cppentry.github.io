---
layout:     post
title:      java操作hbase例子
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，转载请注明本文链接。文章内容如有错误望能指正，以免误导更多人。					https://blog.csdn.net/gnail_oug/article/details/47030277				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>hbase安装方法请参考：<a href="http://blog.csdn.net/gnail_oug/article/details/46973429" rel="nofollow">hbase-0.94安装方法详解</a></p>
<p>hbase常用的shell命令请参考：<a href="http://blog.csdn.net/gnail_oug/article/details/46997997" rel="nofollow">hbase常用的shell命令例子</a></p>
<p><br></p>
<p>java操作hbase，在eclipse中创建一个java项目，将hbase安装文件根目录的jar包和lib目录下jar包导入项目，然后就可以编写java代码操作hbase了。下面代码给出来一个简单的示例</p>
<p><br></p>
<pre><code class="language-java">/**
 * @date 2015-07-23 21:28:10
 * @author sgl
 */
package com.songguoliang.hbase;

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
import org.apache.hadoop.hbase.client.Put;
import org.apache.hadoop.hbase.client.Result;
import org.apache.hadoop.hbase.client.ResultScanner;
import org.apache.hadoop.hbase.client.Scan;
import org.apache.hadoop.hbase.util.Bytes;

public class HBaseTest {
	//声明静态配置，HBaseConfiguration
	private static Configuration conf=null;
	static{
		conf=HBaseConfiguration.create();
		conf.set("hbase.zookeeper.quorum", "sdw1,sdw2");
	}
	/**
	 * 创建一个表
	 * @date 2015-07-23 21:44:32
	 * @author sgl
	 * @param tableName 表名
	 * @param columnFamilys 列族
	 * @throws IOException
	 */
	public static void createTable(String tableName,String[]columnFamilys) throws IOException{
		HBaseAdmin admin =new HBaseAdmin(conf);
		if (admin.tableExists(tableName)) {
			System.out.println("table already exists!");
		}else{
			HTableDescriptor tableDesc=new HTableDescriptor(tableName);
			for(int i=0;i&lt;columnFamilys.length;i++){
				tableDesc.addFamily(new HColumnDescriptor(columnFamilys[i]));
			}
			admin.createTable(tableDesc);
			System.out.println("create table "+tableName+" success!");
		}
		
	}
	/**
	 * 添加一条记录
	 * @date 2015-07-23 22:16:30
	 * @author sgl
	 * @param tableName 表名
	 * @param rowKey 行健
	 * @param family 列族
	 * @param qualifier 限定符
	 * @param value 值
	 * @throws IOException
	 */
	public static void addRecord(String tableName,String rowKey,String family,String qualifier,String value) throws IOException{
		HTable table =new HTable(conf, tableName);
		Put put=new Put(Bytes.toBytes(rowKey));
		put.add(Bytes.toBytes(family),Bytes.toBytes(qualifier),Bytes.toBytes(value));
		table.put(put);
		System.out.println("insert record "+rowKey+" to table "+tableName+" success");
		
	}
	/**
	 * 删除一行记录
	 * @date 2015-07-23 22:17:53
	 * @author sgl
	 * @param tableName 表名
	 * @param rowKey 行健
	 * @throws IOException
	 */
	public static void deleteRecord(String tableName,String rowKey) throws IOException{
		HTable table=new HTable(conf, tableName);
		List&lt;Delete&gt;list=new ArrayList&lt;Delete&gt;();
		Delete delete=new Delete(rowKey.getBytes());
		list.add(delete);
		table.delete(list);
		System.out.println("delete record "+ rowKey+" success!");
		
	}
	/**
	 * 获取一行记录
	 * @date 2015-07-23 22:21:33
	 * @author sgl
	 * @param tableName 表名
	 * @param rowKey 行健
	 * @throws IOException 
	 */
	public static void getOneRecord(String tableName,String rowKey) throws IOException{
		HTable table=new HTable(conf, tableName);
		Get get=new Get(rowKey.getBytes());
		Result rs=table.get(get);
		for(KeyValue kv:rs.raw()){
			System.out.print(new String(kv.getRow()) + " " );       
            System.out.print(new String(kv.getFamily()) + ":" );       
            System.out.print(new String(kv.getQualifier()) + " " );       
            System.out.print(kv.getTimestamp() + " " );       
            System.out.println(new String(kv.getValue()));      
		}
		
	}
	/**
	 * 获取所有数据
	 * @date 2015-07-23 22:26:19
	 * @author sgl
	 * @param tableName 表名
	 * @throws IOException 
	 */
	public static void getAllRecord(String tableName) throws IOException{
		HTable table=new HTable(conf, tableName);
		Scan scan=new Scan();
		ResultScanner scanner=table.getScanner(scan);
		for(Result result:scanner){
			for(KeyValue kv:result.raw()){
				System.out.print(new String(kv.getRow()) + " ");       
                System.out.print(new String(kv.getFamily()) + ":");       
                System.out.print(new String(kv.getQualifier()) + " ");       
                System.out.print(kv.getTimestamp() + " ");       
                System.out.println(new String(kv.getValue()));   
			}
		}
		
	}
	/**
	 * 删除一个表
	 * @date 2015-07-23 22:29:35
	 * @author sgl
	 * @param tableName 表名
	 * @throws IOException 
	 */
	public static void deleteTable(String tableName) throws IOException{
		HBaseAdmin admin=new HBaseAdmin(conf);
		admin.disableTable(tableName);
		admin.deleteTable(tableName);
		System.out.println("delete table "+tableName+" success!");
		
	}
	
	public static void main(String[] args) {
		try {
			String tableName="scores";
			String[]columnFamilys={"grade","course"};
			HBaseTest.createTable(tableName, columnFamilys);
			
			HBaseTest.addRecord(tableName, "sgl", "grade", "", "5");
			HBaseTest.addRecord(tableName, "sgl", "course", "", "90");
			HBaseTest.addRecord(tableName, "sgl", "course", "math", "97");
			HBaseTest.addRecord(tableName, "sgl", "course", "art", "87");
			HBaseTest.addRecord(tableName, "guoguo", "grade", "", "4");
			HBaseTest.addRecord(tableName, "guoguo", "course", "math", "89");
			
			System.out.println("********get one record*********");
			HBaseTest.getOneRecord(tableName, "sgl");
			
			System.out.println("********get all record*********");
			HBaseTest.getAllRecord(tableName);
			
			System.out.println("********delete one record*********");
			HBaseTest.deleteRecord(tableName, "guoguo");
			HBaseTest.getAllRecord(tableName);
			
			System.out.println("********delete table*********");
			HBaseTest.deleteTable(tableName);
		} catch (IOException e) {
			e.printStackTrace();
		}
	}
}
</code></pre><br><br><p></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
            </div>
                </div>