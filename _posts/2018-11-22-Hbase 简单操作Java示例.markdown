---
layout:     post
title:      Hbase 简单操作Java示例
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h3 id="hbase-简单操作java示例">Hbase 简单操作Java示例</h3>

<p>继之前完成hbase搭建之后，接下来学习了以下hbase简单的Java操作。在执行代码前需要先开启hadoop和zookeeper. <br>
下面给出hbase代码：</p>

<pre class="prettyprint"><code class=" hljs avrasm">import java<span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.IOException</span><span class="hljs-comment">;</span>

import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.conf</span><span class="hljs-preprocessor">.Configuration</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span>.*<span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span>.*<span class="hljs-comment">;</span>

import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.Bytes</span><span class="hljs-comment">;</span>

public class HbaseTest {

    public static Configuration configuration<span class="hljs-comment">;</span>
    static {
        configuration = HBaseConfiguration<span class="hljs-preprocessor">.create</span>()<span class="hljs-comment">;</span>
        configuration<span class="hljs-preprocessor">.set</span>(<span class="hljs-string">"hbase.zookeeper.property.clientPort"</span>, <span class="hljs-string">"2181"</span>)<span class="hljs-comment">;</span>
        configuration<span class="hljs-preprocessor">.set</span>(<span class="hljs-string">"hbase.zookeeper.quorum"</span>, <span class="hljs-string">"localhost"</span>)<span class="hljs-comment">;</span>
    }

    public static void main(String[] args) {
        //createTable(<span class="hljs-string">"hbase_test1"</span>)<span class="hljs-comment">;</span>
        //insertData(<span class="hljs-string">"hbase_test1"</span>)<span class="hljs-comment">;</span>
        getRowData(<span class="hljs-string">"hbase_test1"</span>,<span class="hljs-string">"rowkey00000001"</span>,<span class="hljs-string">"F3"</span>,<span class="hljs-string">"Q1"</span>)<span class="hljs-comment">;</span>
        //scanData(<span class="hljs-string">"hbase_test1"</span>)<span class="hljs-comment">;</span>
        //deleteRow(<span class="hljs-string">"hbase_test1"</span>,<span class="hljs-string">"rowkey00000001"</span>)<span class="hljs-comment">;</span>
    }


    public static void createTable(String tableName) {
        System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"start create table ......"</span>)<span class="hljs-comment">;</span>
        try {
            Connection connection = ConnectionFactory<span class="hljs-preprocessor">.createConnection</span>(configuration)<span class="hljs-comment">;</span>
            Admin admin = connection<span class="hljs-preprocessor">.getAdmin</span>()<span class="hljs-comment">;</span>
            TableName table = TableName<span class="hljs-preprocessor">.valueOf</span>(tableName)<span class="hljs-comment">;</span>
            if (admin<span class="hljs-preprocessor">.tableExists</span>(table)) {// 如果存在要创建的表，那么先删除，再创建
                admin<span class="hljs-preprocessor">.disableTable</span>(table)<span class="hljs-comment">;</span>
                admin<span class="hljs-preprocessor">.deleteTable</span>(table)<span class="hljs-comment">;</span>
                System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(tableName + <span class="hljs-string">" is exist,detele...."</span>)<span class="hljs-comment">;</span>
            }
            HTableDescriptor tableDescriptor = new HTableDescriptor(table)<span class="hljs-comment">;</span>
            tableDescriptor<span class="hljs-preprocessor">.addFamily</span>(new HColumnDescriptor(<span class="hljs-string">"F1"</span>))<span class="hljs-comment">;</span>
            tableDescriptor<span class="hljs-preprocessor">.addFamily</span>(new HColumnDescriptor(<span class="hljs-string">"F2"</span>))<span class="hljs-comment">;</span>
            tableDescriptor<span class="hljs-preprocessor">.addFamily</span>(new HColumnDescriptor(<span class="hljs-string">"F3"</span>))<span class="hljs-comment">;</span>
            admin<span class="hljs-preprocessor">.createTable</span>(tableDescriptor)<span class="hljs-comment">;</span>
            admin<span class="hljs-preprocessor">.close</span>()<span class="hljs-comment">;</span>
            connection<span class="hljs-preprocessor">.close</span>()<span class="hljs-comment">;</span>
        } catch (MasterNotRunningException e) {
            e<span class="hljs-preprocessor">.printStackTrace</span>()<span class="hljs-comment">;</span>
        } catch (ZooKeeperConnectionException e) {
            e<span class="hljs-preprocessor">.printStackTrace</span>()<span class="hljs-comment">;</span>
        } catch (IOException e) {
            e<span class="hljs-preprocessor">.printStackTrace</span>()<span class="hljs-comment">;</span>
        }
        System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"end create table ......"</span>)<span class="hljs-comment">;</span>
    }


    public static void insertData(String tableName) {
        System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"start insert data ......"</span>)<span class="hljs-comment">;</span>
        Put put = new Put(<span class="hljs-string">"rowkey00000001"</span><span class="hljs-preprocessor">.getBytes</span>())<span class="hljs-comment">;// 一个PUT代表一行数据，再NEW一个PUT表示第二行数据,每行一个唯一的ROWKEY，此处rowkey为put构造方法中传入的值</span>
        put<span class="hljs-preprocessor">.addColumn</span>(<span class="hljs-string">"F1"</span><span class="hljs-preprocessor">.getBytes</span>(), <span class="hljs-string">"Q1"</span><span class="hljs-preprocessor">.getBytes</span>(), <span class="hljs-string">"第一family第一qualifer_update"</span><span class="hljs-preprocessor">.getBytes</span>())<span class="hljs-comment">;// 本行数据的第一列</span>
        //put<span class="hljs-preprocessor">.addColumn</span>(<span class="hljs-string">"F1"</span><span class="hljs-preprocessor">.getBytes</span>(), <span class="hljs-string">"Q2"</span><span class="hljs-preprocessor">.getBytes</span>(), <span class="hljs-string">"第一family第二qualifer"</span><span class="hljs-preprocessor">.getBytes</span>())<span class="hljs-comment">;</span>
        put<span class="hljs-preprocessor">.addColumn</span>(<span class="hljs-string">"F2"</span><span class="hljs-preprocessor">.getBytes</span>(), <span class="hljs-string">"Q1"</span><span class="hljs-preprocessor">.getBytes</span>(), <span class="hljs-string">"第二family第一qualifer"</span><span class="hljs-preprocessor">.getBytes</span>())<span class="hljs-comment">;</span>
        put<span class="hljs-preprocessor">.addColumn</span>(<span class="hljs-string">"F2"</span><span class="hljs-preprocessor">.getBytes</span>(), <span class="hljs-string">"Q2"</span><span class="hljs-preprocessor">.getBytes</span>(), <span class="hljs-string">"第二family第二qualifer"</span><span class="hljs-preprocessor">.getBytes</span>())<span class="hljs-comment">;</span>
        put<span class="hljs-preprocessor">.addColumn</span>(<span class="hljs-string">"F3"</span><span class="hljs-preprocessor">.getBytes</span>(), <span class="hljs-string">"Q1"</span><span class="hljs-preprocessor">.getBytes</span>(), <span class="hljs-string">"第三family第一qualifer"</span><span class="hljs-preprocessor">.getBytes</span>())<span class="hljs-comment">;</span>
        put<span class="hljs-preprocessor">.addColumn</span>(<span class="hljs-string">"F3"</span><span class="hljs-preprocessor">.getBytes</span>(), <span class="hljs-string">"Q2"</span><span class="hljs-preprocessor">.getBytes</span>(), <span class="hljs-string">"第三family第二qualifer"</span><span class="hljs-preprocessor">.getBytes</span>())<span class="hljs-comment">;</span>
        put<span class="hljs-preprocessor">.addColumn</span>(<span class="hljs-string">"F1"</span><span class="hljs-preprocessor">.getBytes</span>(), null, <span class="hljs-string">"第一family,nullqualifer"</span><span class="hljs-preprocessor">.getBytes</span>())<span class="hljs-comment">;</span>
        put<span class="hljs-preprocessor">.addColumn</span>(<span class="hljs-string">"F2"</span><span class="hljs-preprocessor">.getBytes</span>(), null, <span class="hljs-string">"第二family,nullqualifer"</span><span class="hljs-preprocessor">.getBytes</span>())<span class="hljs-comment">;</span>
        put<span class="hljs-preprocessor">.addColumn</span>(<span class="hljs-string">"F3"</span><span class="hljs-preprocessor">.getBytes</span>(), null, <span class="hljs-string">"第三family,nullqualifer"</span><span class="hljs-preprocessor">.getBytes</span>())<span class="hljs-comment">;</span>
        try {
            Connection connection = ConnectionFactory<span class="hljs-preprocessor">.createConnection</span>(configuration)<span class="hljs-comment">;</span>
            Table table = connection<span class="hljs-preprocessor">.getTable</span>(TableName<span class="hljs-preprocessor">.valueOf</span>(tableName))<span class="hljs-comment">;</span>
            table<span class="hljs-preprocessor">.put</span>(put)<span class="hljs-comment">;</span>
            table<span class="hljs-preprocessor">.close</span>()<span class="hljs-comment">;</span>
            connection<span class="hljs-preprocessor">.close</span>()<span class="hljs-comment">;</span>
        } catch (IOException e) {
            e<span class="hljs-preprocessor">.printStackTrace</span>()<span class="hljs-comment">;</span>
        }
        System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"end insert data ......"</span>)<span class="hljs-comment">;</span>
    }


    public static void dropTable(String tableName) {
        try {
            Connection connection = ConnectionFactory<span class="hljs-preprocessor">.createConnection</span>(configuration)<span class="hljs-comment">;</span>
            Admin admin = connection<span class="hljs-preprocessor">.getAdmin</span>()<span class="hljs-comment">;</span>
            TableName table = TableName<span class="hljs-preprocessor">.valueOf</span>(tableName)<span class="hljs-comment">;</span>
            admin<span class="hljs-preprocessor">.disableTable</span>(table)<span class="hljs-comment">;</span>
            admin<span class="hljs-preprocessor">.deleteTable</span>(table)<span class="hljs-comment">;</span>
            admin<span class="hljs-preprocessor">.close</span>()<span class="hljs-comment">;</span>
            connection<span class="hljs-preprocessor">.close</span>()<span class="hljs-comment">;</span>
        } catch (MasterNotRunningException e) {
            e<span class="hljs-preprocessor">.printStackTrace</span>()<span class="hljs-comment">;</span>
        } catch (ZooKeeperConnectionException e) {
            e<span class="hljs-preprocessor">.printStackTrace</span>()<span class="hljs-comment">;</span>
        } catch (IOException e) {
            e<span class="hljs-preprocessor">.printStackTrace</span>()<span class="hljs-comment">;</span>
        }

    }

    public static void deleteRow(String tableName, String rowkey)  {
        try {
            Connection connection = ConnectionFactory<span class="hljs-preprocessor">.createConnection</span>(configuration)<span class="hljs-comment">;</span>
            Table table = connection<span class="hljs-preprocessor">.getTable</span>(TableName<span class="hljs-preprocessor">.valueOf</span>(tableName))<span class="hljs-comment">;</span>
            Delete delete = new Delete(Bytes<span class="hljs-preprocessor">.toBytes</span>(rowkey))<span class="hljs-comment">;</span>
            //删除指定列族
            //delete<span class="hljs-preprocessor">.addFamily</span>(Bytes<span class="hljs-preprocessor">.toBytes</span>(colFamily))<span class="hljs-comment">;</span>
            //删除指定列
            //delete<span class="hljs-preprocessor">.addColumn</span>(Bytes<span class="hljs-preprocessor">.toBytes</span>(colFamily),Bytes<span class="hljs-preprocessor">.toBytes</span>(col))<span class="hljs-comment">;</span>
            table<span class="hljs-preprocessor">.delete</span>(delete)<span class="hljs-comment">;</span>
            table<span class="hljs-preprocessor">.close</span>()<span class="hljs-comment">;</span>
            connection<span class="hljs-preprocessor">.close</span>()<span class="hljs-comment">;</span>
            System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"删除行成功!"</span>)<span class="hljs-comment">;</span>

        } catch (IOException e) {
            e<span class="hljs-preprocessor">.printStackTrace</span>()<span class="hljs-comment">;</span>
        }


    }

    public static void getRowData(String tableName,String rowkey,String colFamily,String col){
        try{
            Connection connection = ConnectionFactory<span class="hljs-preprocessor">.createConnection</span>(configuration)<span class="hljs-comment">;</span>
            Table table = connection<span class="hljs-preprocessor">.getTable</span>(TableName<span class="hljs-preprocessor">.valueOf</span>(tableName))<span class="hljs-comment">;</span>
            Get get = new Get(Bytes<span class="hljs-preprocessor">.toBytes</span>(rowkey))<span class="hljs-comment">;</span>
            //获取指定列族数据
            get<span class="hljs-preprocessor">.addFamily</span>(Bytes<span class="hljs-preprocessor">.toBytes</span>(colFamily))<span class="hljs-comment">;</span>
            //获取指定列数据
            get<span class="hljs-preprocessor">.addColumn</span>(Bytes<span class="hljs-preprocessor">.toBytes</span>(colFamily),Bytes<span class="hljs-preprocessor">.toBytes</span>(col))<span class="hljs-comment">;</span>
            Result result = table<span class="hljs-preprocessor">.get</span>(get)<span class="hljs-comment">;</span>
            showCell(result)<span class="hljs-comment">;</span>
            table<span class="hljs-preprocessor">.close</span>()<span class="hljs-comment">;</span>
            connection<span class="hljs-preprocessor">.close</span>()<span class="hljs-comment">;</span>
        }catch (IOException e) {
            e<span class="hljs-preprocessor">.printStackTrace</span>()<span class="hljs-comment">;</span>
        }
    }

    public static void scanData(String tableName) {

        try {
            Connection connection = ConnectionFactory<span class="hljs-preprocessor">.createConnection</span>(configuration)<span class="hljs-comment">;</span>
            Table table = connection<span class="hljs-preprocessor">.getTable</span>(TableName<span class="hljs-preprocessor">.valueOf</span>(tableName))<span class="hljs-comment">;</span>
            Scan scan = new Scan()<span class="hljs-comment">;</span>
            //scan<span class="hljs-preprocessor">.setStartRow</span>(Bytes<span class="hljs-preprocessor">.toBytes</span>(startRow))<span class="hljs-comment">;</span>
            //scan<span class="hljs-preprocessor">.setStopRow</span>(Bytes<span class="hljs-preprocessor">.toBytes</span>(stopRow))<span class="hljs-comment">;</span>
            ResultScanner resultScanner = table<span class="hljs-preprocessor">.getScanner</span>(scan)<span class="hljs-comment">;</span>
            for(Result result : resultScanner){
                showCell(result)<span class="hljs-comment">;</span>
            }
            table<span class="hljs-preprocessor">.close</span>()<span class="hljs-comment">;</span>
            connection<span class="hljs-preprocessor">.close</span>()<span class="hljs-comment">;</span>
        } catch (IOException e) {
            e<span class="hljs-preprocessor">.printStackTrace</span>()<span class="hljs-comment">;</span>
        }
    }


    private static void showCell(Result result){
        Cell[] cells = result<span class="hljs-preprocessor">.rawCells</span>()<span class="hljs-comment">;</span>
        for(Cell cell:cells){
            System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"RowName:"</span>+new String(CellUtil<span class="hljs-preprocessor">.cloneRow</span>(cell))+<span class="hljs-string">" "</span>)<span class="hljs-comment">;</span>
            System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"Timetamp:"</span>+cell<span class="hljs-preprocessor">.getTimestamp</span>()+<span class="hljs-string">" "</span>)<span class="hljs-comment">;</span>
            System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"column Family:"</span>+new String(CellUtil<span class="hljs-preprocessor">.cloneFamily</span>(cell))+<span class="hljs-string">" "</span>)<span class="hljs-comment">;</span>
            System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"row Name:"</span>+new String(CellUtil<span class="hljs-preprocessor">.cloneQualifier</span>(cell))+<span class="hljs-string">" "</span>)<span class="hljs-comment">;</span>
            System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"value:"</span>+new String(CellUtil<span class="hljs-preprocessor">.cloneValue</span>(cell))+<span class="hljs-string">" "</span>)<span class="hljs-comment">;</span>
        }
    }
}</code></pre>

<h3 id="注意">注意</h3>

<p>在hbase 1.0版本之后不建议使用HTablePool对象，可使用Admin来跟Hbase交互。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>