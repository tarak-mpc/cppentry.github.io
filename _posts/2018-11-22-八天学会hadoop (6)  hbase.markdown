---
layout:     post
title:      八天学会hadoop (6)  hbase
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/mmalan/article/details/83449833				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>hbase 基于Hdfs 文件系统  hbase 支持随机高速访问</p>

<p>hbase 没有事务特性 hbase不能处理联合关系处理 比如mysql 里面的join </p>

<p>hbase优点在于存放非常大的表 百万行 百万列 支持随机高速访问</p>

<p> </p>

<h1>Hbase 表结构</h1>

<p>表名-&gt;行键-&gt;列族(column family)-&gt;列名(qualifier)-&gt;版本号</p>

<p><img alt="" class="has" height="520" src="https://img-blog.csdnimg.cn/20181027213058659.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21tYWxhbg==,size_27,color_FFFFFF,t_70" width="1062"></p>

<p> </p>

<p>具体请查看:<a href="https://www.cnblogs.com/steven-note/p/7209398.html" rel="nofollow">https://www.cnblogs.com/steven-note/p/7209398.html</a></p>

<h1>hbase 创建 增加数据代码展示</h1>

<pre class="has">
<code class="language-java">public class HbaseTest {


    public static void main(String[] args) {
        Configuration configuration = HBaseConfiguration.create();
        configuration.set("hbase.zookeeper.quorum","192.168.36.139:2181,192.168.36.139:2182,192.168.36.139:2183");
        //createTable(configuration);

        InsertTest(configuration);

    }

    public static void InsertTest(Configuration configuration) {
        HTable hTable = null;
        try {
            hTable = new HTable(configuration, "dj");
        } catch (IOException e) {
            e.printStackTrace();
        }

        Put row1 = new Put(Bytes.toBytes("row1"));
        row1.add(Bytes.toBytes("f1"), Bytes.toBytes("tuya"), Bytes.toBytes("zhuangbi"));
        Put row2 = new Put(Bytes.toBytes("row2"));
        row2.add(Bytes.toBytes("f1"), Bytes.toBytes("xiaopeng"), Bytes.toBytes("wanyingde"));

        ArrayList&lt;Put&gt; puts = new ArrayList&lt;&gt;();
        puts.add(row1);
        puts.add(row2);

        try {
            hTable.put(puts);
        } catch (IOException e) {
            e.printStackTrace();
        }

    }




    public static void createTable(Configuration configuration) {


        HBaseAdmin hBaseAdmin=null;
        try {
            hBaseAdmin= new HBaseAdmin(configuration);
        } catch (IOException e) {
            e.printStackTrace();
        }
        TableName table = TableName.valueOf("dj");
        HTableDescriptor hTableDescriptor = new HTableDescriptor(table);
        HColumnDescriptor f1 = new HColumnDescriptor("f1");
        hTableDescriptor.addFamily(f1);
        try {
            hBaseAdmin.createTable(hTableDescriptor);
        } catch (IOException e) {
            e.printStackTrace();
        }


    }



}</code></pre>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>            </div>
                </div>