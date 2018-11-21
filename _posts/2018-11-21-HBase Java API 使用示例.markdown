---
layout:     post
title:      HBase Java API 使用示例
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/Vinsuan1993/article/details/71036590				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>在使用HBase Java API 之前，大家首先要了解HBase Java API类，可参考博客：http://www.cnblogs.com/ggjucheng/p/3380267.html</p>
<p></p>
<p style="font-size:13.3333px;line-height:20px;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;">
几个相关类与HBase数据模型之间的对应关系</p>
<p style="font-size:13.3333px;line-height:20px;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;">
</p>
<table border="1" style="border:1px solid #C0C0C0;border-collapse:collapse;color:rgb(0,0,0);font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;font-size:13.3333px;line-height:20px;"><tbody><tr><td style="font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;border:1px solid #C0C0C0;border-collapse:collapse;">
java类</td>
<td style="font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;border:1px solid #C0C0C0;border-collapse:collapse;">
HBase数据模型</td>
</tr><tr><td style="font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;border:1px solid #C0C0C0;border-collapse:collapse;">
HBaseAdmin</td>
<td rowspan="2" style="font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;border:1px solid #C0C0C0;border-collapse:collapse;">
数据库（DataBase)</td>
</tr><tr><td style="font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;border:1px solid #C0C0C0;border-collapse:collapse;">
HBaseConfiguration</td>
</tr><tr><td style="font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;border:1px solid #C0C0C0;border-collapse:collapse;">
HTable</td>
<td style="font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;border:1px solid #C0C0C0;border-collapse:collapse;">
表（Table)</td>
</tr><tr><td style="font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;border:1px solid #C0C0C0;border-collapse:collapse;">
HTableDescriptor</td>
<td style="font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;border:1px solid #C0C0C0;border-collapse:collapse;">
列族（Column Family)</td>
</tr><tr><td style="font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;border:1px solid #C0C0C0;border-collapse:collapse;">
Put</td>
<td rowspan="3" style="font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;border:1px solid #C0C0C0;border-collapse:collapse;">
列修饰符（Column Qualifier）</td>
</tr><tr><td style="font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;border:1px solid #C0C0C0;border-collapse:collapse;">
Get</td>
</tr><tr><td style="font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;border:1px solid #C0C0C0;border-collapse:collapse;">
Scanner</td>
</tr></tbody></table><br><p></p>
<h2 style="line-height:1.5;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;">
<span style="font-size:12px;">HBaseConfiguration：<span style="font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;line-height:20px;">对HBase进行配置。</span></span></h2>
<div><span style="font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;line-height:20px;"></span>
<h2 style="line-height:1.5;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;">
<span style="font-size:12px;">HBaseAdmin：<span style="font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;line-height:20px;">提供了一个接口来管理HBase数据库的表信息。它提供的方法包括：创建表，删除表，列出表项，使表有效或无效，以及添加或删除表列族成员等。</span></span></h2>
<div><span style="font-size:12px;"><span style="font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;line-height:20px;"></span></span>
<h2 style="line-height:1.5;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;">
<span style="font-size:12px;">HTableDescriptor：<span style="font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;line-height:20px;">包含了表的名字极其对应表的列族</span></span></h2>
<div><span style="font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;line-height:20px;"></span>
<h2 style="line-height:1.5;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;">
<span style="font-size:12px;">HColumnDescriptor：<span style="font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;line-height:20px;">维护着关于列族的信息，例如版本号，压缩设置等。它通常在创建表或者为表添加列族的时候使用。列族被创建后不能直接修改，只能通过删除然后重新创建的方式。列族被删除的时候，列族里面的数据也会同时被删除。</span></span></h2>
<div><span style="font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;line-height:20px;"></span>
<h2 style="line-height:1.5;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;">
<span style="font-size:12px;">HTable：<span style="font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;line-height:20px;">可以用来和HBase表直接通信。此方法对于更新操作来说是非线程安全的。</span></span></h2>
<div><span style="font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;line-height:20px;"></span>
<h2 style="line-height:1.5;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;">
<span style="font-size:12px;">Put：<span style="font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;line-height:20px;">用来对单个行执行添加操作。</span></span></h2>
<div><span style="font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;line-height:20px;"></span>
<h2 style="line-height:1.5;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;">
<span style="font-size:12px;">Get：<span style="font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;line-height:20px;">用来获取单个行的相关信息。</span></span></h2>
<div><span style="font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;line-height:20px;"></span>
<h2 style="line-height:1.5;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;">
<span style="font-size:12px;">Result：<span style="font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;line-height:20px;">存储Get或者Scan操作后获取表的单行值。使用此类提供的方法可以直接获取值或者各种Map结构（key-value对）。</span></span></h2>
<div><span style="font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;line-height:20px;"></span>
<h2 style="line-height:1.5;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;">
<span style="font-size:12px;">ResultScanner：<span style="font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;line-height:20px;">客户端获取值的接口。</span></span></h2>
<div><span style="font-size:12px;"><span style="font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;line-height:20px;">代码示例：</span></span></div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
<div><span style="font-size:12px;"><span style="font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;line-height:20px;"></span></span><pre><code class="language-java">public class HBaseDemo {

	private Configuration conf = null;
	
	@Before
	public void init(){
		conf = HBaseConfiguration.create();
		//客户端连接zookeeper
		conf.set("hbase.zookeeper.quorum", "hadoop01,hadoop02,hadoop03");
	}
	
	@Test
	public void testDrop() throws Exception{
		
		HBaseAdmin admin = new HBaseAdmin(conf);
		admin.disableTable("account");
		admin.deleteTable("account");
		admin.close();
	}
	/**
	*插入数据
	*/
	@Test
	public void testPut() throws Exception{
		HTable table = new HTable(conf, "user");
		//提供row key
		Put put = new Put(Bytes.toBytes("rk0003"));
		//列族、列的标示符、值，参数都是字节数组
		put.add(Bytes.toBytes("info"), Bytes.toBytes("name"), Bytes.toBytes("liuyan"));
		table.put(put);
		table.close();
	}
	/**
	*获取数据
	*/
	@Test
	public void testGet() throws Exception{
		//HTablePool pool = new HTablePool(conf, 10);
		//HTable table = (HTable) pool.getTable("user");
		HTable table = new HTable(conf, "user");
		Get get = new Get(Bytes.toBytes("rk0001"));
		//get.addColumn(Bytes.toBytes("info"), Bytes.toBytes("name"));
		get.setMaxVersions(5);
		Result result = table.get(get);
		
	  //String r = Bytes.toString(result.getValue(family, qualifier) );这个获取方法不常用
		
		for(KeyValue kv : result.list()){
			String family = new String(kv.getFamily());
			System.out.println(family);
			String qualifier = new String(kv.getQualifier());
			System.out.println(qualifier);
			System.out.println(new String(kv.getValue()));
		}
		table.close();
	}
	
	@Test
	public void testScan() throws Exception{
		HTablePool pool = new HTablePool(conf, 10);
		HTableInterface table = pool.getTable("user");
		Scan scan = new Scan(Bytes.toBytes("rk0001"), Bytes.toBytes("rk0002"));
		scan.addFamily(Bytes.toBytes("info"));
		//结果集 
		ResultScanner scanner = table.getScanner(scan);
		for(Result r : scanner){
			/**
			for(KeyValue kv : r.list()){
				String family = new String(kv.getFamily());
				System.out.println(family);
				String qualifier = new String(kv.getQualifier());
				System.out.println(qualifier);
				System.out.println(new String(kv.getValue()));
			}
			*/
			byte[] value = r.getValue(Bytes.toBytes("info"), Bytes.toBytes("name"));
			System.out.println(new String(value));
		}
		pool.close();
	}
	
	
	@Test
	public void testDel() throws Exception{
		HTable table = new HTable(conf, "user");
		Delete del = new Delete(Bytes.toBytes("rk0001"));
		del.deleteColumn(Bytes.toBytes("data"), Bytes.toBytes("pic"));
		table.delete(del);
		table.close();
	}
	
	
	
	
	public static void main(String[] args) throws Exception {
		Configuration conf = HBaseConfiguration.create();
		conf.set("hbase.zookeeper.quorum", "hadoop01,hadoop02,hadoop03");
		//ddl操作，传入conf，可知操作哪个hbase集群
		HBaseAdmin admin = new HBaseAdmin(conf);
		HTableDescriptor td = new HTableDescriptor("account");
		HColumnDescriptor cd = new HColumnDescriptor("info");
		cd.setMaxVersions(10);
		td.addFamily(cd);
		admin.createTable(td);
		admin.close();

	}
	
	public void createTable(String tableName, int maxVersion, String... cf){
		
	}

}
</code></pre><br><br></div>
<p></p>
            </div>
                </div>