---
layout:     post
title:      HBase之JavaAPI
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/C_FuL/article/details/77199823				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<blockquote>
  <p>HBase提供了Java API的访问接口，实际开发中我们经常用来操作HBase，就和我们通过java API操作RDBMS一样。如下</p>
</blockquote>

<table>
<thead>
<tr>
  <th>API</th>
  <th align="center">作用</th>
</tr>
</thead>
<tbody><tr>
  <td>HBaseAdmin</td>
  <td align="center">HBase 客户端，用来操作HBase</td>
</tr>
<tr>
  <td>Configuration</td>
  <td align="center">配置对象</td>
</tr>
<tr>
  <td>Connection</td>
  <td align="center">连接对象</td>
</tr>
<tr>
  <td>Table</td>
  <td align="center">HBase 表对象</td>
</tr>
<tr>
  <td>TableName</td>
  <td align="center">HBase 中的表名</td>
</tr>
<tr>
  <td>HTableDescriptor</td>
  <td align="center">HBase 表描述信息对象</td>
</tr>
<tr>
  <td>HColumnDescriptor</td>
  <td align="center">HBase 列族描述对象</td>
</tr>
<tr>
  <td>Put</td>
  <td align="center">用于插入数据</td>
</tr>
<tr>
  <td>Get</td>
  <td align="center">用于查询单条记录</td>
</tr>
<tr>
  <td>Delete</td>
  <td align="center">删除数据对象</td>
</tr>
<tr>
  <td>Scan</td>
  <td align="center">全表扫描对象，查询所有记录</td>
</tr>
<tr>
  <td>ResultScanner</td>
  <td align="center">查询数据返回结果集</td>
</tr>
<tr>
  <td>Result</td>
  <td align="center">查询返回的单条记录结果</td>
</tr>
<tr>
  <td>Cell</td>
  <td align="center">对应HBase中的列</td>
</tr>
<tr>
  <td>SingleColumnValueFilter</td>
  <td align="center">列值过滤器（过滤列植的相等、不等、范围等）</td>
</tr>
<tr>
  <td>ColumnPrefixFilter</td>
  <td align="center">列名前缀过滤器（过滤指定前缀的列名）</td>
</tr>
<tr>
  <td>multipleColumnPrefixFilter</td>
  <td align="center">多个列名前缀过滤器（过滤多个指定前缀的列名）</td>
</tr>
<tr>
  <td>RowFilter  rowKey</td>
  <td align="center">过滤器（通过正则，过滤rowKey值）</td>
</tr>
</tbody></table>


<blockquote>
  <p>上面这些API,个人做了一个小Demo,代码如下;</p>
</blockquote>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">package</span> com.cfl.hbase;

<span class="hljs-keyword">import</span> org.apache.hadoop.conf.Configuration;
<span class="hljs-keyword">import</span> org.apache.hadoop.hbase.*;
<span class="hljs-keyword">import</span> org.apache.hadoop.hbase.client.*;
<span class="hljs-keyword">import</span> org.apache.hadoop.hbase.filter.*;
<span class="hljs-keyword">import</span> org.apache.hadoop.hbase.util.Bytes;
<span class="hljs-keyword">import</span> org.junit.After;
<span class="hljs-keyword">import</span> org.junit.Before;
<span class="hljs-keyword">import</span> org.junit.Test;

<span class="hljs-keyword">import</span> java.util.ArrayList;
<span class="hljs-keyword">import</span> java.util.List;

<span class="hljs-javadoc">/**
 * HBase Java API 操作
 * 一般我们使用Java API 主要操作的是数据即DML操作，DDL的操作较少
 */</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">HBaseTest</span> {</span>

    <span class="hljs-keyword">static</span> Configuration conf = <span class="hljs-keyword">null</span>;
    <span class="hljs-keyword">private</span> Connection conn = <span class="hljs-keyword">null</span>;
    <span class="hljs-keyword">private</span> HBaseAdmin admin = <span class="hljs-keyword">null</span>;
    <span class="hljs-keyword">private</span> TableName tableName = <span class="hljs-keyword">null</span>;
    <span class="hljs-keyword">private</span> Table table = <span class="hljs-keyword">null</span>;

    <span class="hljs-comment">// 初始化配置</span>
    <span class="hljs-annotation">@Before</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">init</span>() <span class="hljs-keyword">throws</span> Exception {
        conf = HBaseConfiguration.create();
        <span class="hljs-comment">// 如果不设置zookeeper地址，可以将hbase-site.xml文件复制到resource目录下</span>
        conf.set(<span class="hljs-string">"hbase.zookeeper.quorum"</span>,<span class="hljs-string">"node3,node4,node5"</span>);<span class="hljs-comment">// zookeeper 地址</span>
        <span class="hljs-comment">// conf.set("hbase.zookeeper.property.clientPort","2188");// zookeeper 客户端端口，默认为2188，可以不用设置</span>
        conn = ConnectionFactory.createConnection(conf);<span class="hljs-comment">// 创建连接</span>

        <span class="hljs-comment">// admin = new HBaseAdmin(conf); // 已弃用，不推荐使用</span>
        admin = (HBaseAdmin) conn.getAdmin(); <span class="hljs-comment">// hbase 表管理类</span>

        tableName = TableName.valueOf(<span class="hljs-string">"students"</span>); <span class="hljs-comment">// 表名</span>

        table = conn.getTable(tableName);<span class="hljs-comment">// 表对象</span>
    }

    <span class="hljs-comment">// --------------------DDL 操作 Start------------------</span>
    <span class="hljs-comment">// 创建表 HTableDescriptor、HColumnDescriptor、addFamily()、createTable()</span>
    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">createTable</span>() <span class="hljs-keyword">throws</span> Exception {
        <span class="hljs-comment">// 创建表描述类</span>
        HTableDescriptor desc = <span class="hljs-keyword">new</span> HTableDescriptor(tableName);

        <span class="hljs-comment">// 添加列族info</span>
        HColumnDescriptor family_info = <span class="hljs-keyword">new</span> HColumnDescriptor(<span class="hljs-string">"info"</span>);
        desc.addFamily(family_info);

        <span class="hljs-comment">// 添加列族address</span>
        HColumnDescriptor family_address = <span class="hljs-keyword">new</span> HColumnDescriptor(<span class="hljs-string">"address"</span>);
        desc.addFamily(family_address);

        <span class="hljs-comment">// 创建表</span>
        admin.createTable(desc);
    }

    <span class="hljs-comment">// 删除表 先弃用表disableTable(表名)，再删除表 deleteTable(表名)</span>
    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">deleteTable</span>() <span class="hljs-keyword">throws</span> Exception {
        admin.disableTable(tableName);
        admin.deleteTable(tableName);
    }

    <span class="hljs-comment">// 添加列族 addColumn(表名,列族)</span>
    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">addFamily</span>() <span class="hljs-keyword">throws</span> Exception {
        admin.addColumn(tableName, <span class="hljs-keyword">new</span> HColumnDescriptor(<span class="hljs-string">"hobbies"</span>));
    }

    <span class="hljs-comment">// 删除列族 deleteColumn(表名,列族)</span>
    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">deleteFamily</span>() <span class="hljs-keyword">throws</span> Exception {
        admin.deleteColumn(tableName, Bytes.toBytes(<span class="hljs-string">"hobbies"</span>));
    }

    <span class="hljs-comment">// --------------------DDL 操作 End---------------------</span>

    <span class="hljs-comment">// ----------------------DML 操作 Start-----------------</span>
    <span class="hljs-comment">// 添加数据 Put(列族,列,列值)（HBase 中没有修改，插入时rowkey相同，数据会覆盖）</span>
    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">insertData</span>() <span class="hljs-keyword">throws</span> Exception {
        <span class="hljs-comment">// 添加一条记录</span>
        <span class="hljs-comment">// Put put = new Put(Bytes.toBytes("1001"));</span>
        <span class="hljs-comment">// put.addColumn(Bytes.toBytes("info"), Bytes.toBytes("name"), Bytes.toBytes("San-Qiang Zhang"));</span>
        <span class="hljs-comment">// put.addColumn(Bytes.toBytes("address"), Bytes.toBytes("province"), Bytes.toBytes("Hebei"));</span>
        <span class="hljs-comment">// put.addColumn(Bytes.toBytes("address"), Bytes.toBytes("city"), Bytes.toBytes("Shijiazhuang"));</span>
        <span class="hljs-comment">// table.put(put);</span>

        <span class="hljs-comment">// 添加多条记录（批量插入）</span>
        List&lt;Put&gt; putList = <span class="hljs-keyword">new</span> ArrayList&lt;Put&gt;();
        Put put1 = <span class="hljs-keyword">new</span> Put(Bytes.toBytes(<span class="hljs-string">"1002"</span>));
        put1.addColumn(Bytes.toBytes(<span class="hljs-string">"info"</span>), Bytes.toBytes(<span class="hljs-string">"name"</span>), Bytes.toBytes(<span class="hljs-string">"Lisi"</span>));
        put1.addColumn(Bytes.toBytes(<span class="hljs-string">"info"</span>), Bytes.toBytes(<span class="hljs-string">"sex"</span>), Bytes.toBytes(<span class="hljs-string">"1"</span>));
        put1.addColumn(Bytes.toBytes(<span class="hljs-string">"address"</span>), Bytes.toBytes(<span class="hljs-string">"city"</span>), Bytes.toBytes(<span class="hljs-string">"Shanghai"</span>));
        Put put2 = <span class="hljs-keyword">new</span> Put(Bytes.toBytes(<span class="hljs-string">"1003"</span>));
        put2.addColumn(Bytes.toBytes(<span class="hljs-string">"info"</span>), Bytes.toBytes(<span class="hljs-string">"name"</span>), Bytes.toBytes(<span class="hljs-string">"Lili"</span>));
        put2.addColumn(Bytes.toBytes(<span class="hljs-string">"info"</span>), Bytes.toBytes(<span class="hljs-string">"sex"</span>), Bytes.toBytes(<span class="hljs-string">"0"</span>));
        put2.addColumn(Bytes.toBytes(<span class="hljs-string">"address"</span>), Bytes.toBytes(<span class="hljs-string">"city"</span>), Bytes.toBytes(<span class="hljs-string">"Beijing"</span>));
        Put put3 = <span class="hljs-keyword">new</span> Put(Bytes.toBytes(<span class="hljs-string">"1004"</span>));
        put3.addColumn(Bytes.toBytes(<span class="hljs-string">"info"</span>), Bytes.toBytes(<span class="hljs-string">"name_a"</span>), Bytes.toBytes(<span class="hljs-string">"Zhaosi"</span>));
        Put put4 = <span class="hljs-keyword">new</span> Put(Bytes.toBytes(<span class="hljs-string">"1004"</span>));
        put4.addColumn(Bytes.toBytes(<span class="hljs-string">"info"</span>), Bytes.toBytes(<span class="hljs-string">"name_b"</span>), Bytes.toBytes(<span class="hljs-string">"Wangwu"</span>));
        putList.add(put1);
        putList.add(put2);
        putList.add(put3);
        putList.add(put4);
        table.put(putList);
    }

    <span class="hljs-comment">// 删除数据 Delete</span>
    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">deleteData</span>() <span class="hljs-keyword">throws</span> Exception {
        <span class="hljs-comment">// 删除一条数据（行健为1002）</span>
        <span class="hljs-comment">// Delete delete = new Delete(Bytes.toBytes("1002"));</span>
        <span class="hljs-comment">// table.delete(delete);</span>

        <span class="hljs-comment">// 删除行健为1003，列族为info的数据</span>
        <span class="hljs-comment">// Delete delete = new Delete(Bytes.toBytes("1003"));</span>
        <span class="hljs-comment">// delete.addFamily(Bytes.toBytes("info"));</span>
        <span class="hljs-comment">// table.delete(delete);</span>

        <span class="hljs-comment">// 删除行健为1，列族为address，列为city的数据</span>
        Delete delete = <span class="hljs-keyword">new</span> Delete(Bytes.toBytes(<span class="hljs-string">"1001"</span>));
        delete.addColumn(Bytes.toBytes(<span class="hljs-string">"address"</span>), Bytes.toBytes(<span class="hljs-string">"city"</span>));
        table.delete(delete);
    }

    <span class="hljs-comment">// 单条查询 Get</span>
    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">getData</span>() <span class="hljs-keyword">throws</span> Exception {
        Get get = <span class="hljs-keyword">new</span> Get(Bytes.toBytes(<span class="hljs-string">"1001"</span>));
        <span class="hljs-comment">// get.addFamily(Bytes.toBytes("info")); //指定获取某个列族</span>
        <span class="hljs-comment">// get.addColumn(Bytes.toBytes("info"), Bytes.toBytes("name")); //指定获取某个列族中的某个列</span>
        Result result = table.get(get);

        System.out.println(<span class="hljs-string">"行健："</span> + Bytes.toString(result.getRow()));
        <span class="hljs-keyword">byte</span>[] name = result.getValue(Bytes.toBytes(<span class="hljs-string">"info"</span>), Bytes.toBytes(<span class="hljs-string">"name"</span>));
        <span class="hljs-keyword">byte</span>[] sex = result.getValue(Bytes.toBytes(<span class="hljs-string">"info"</span>), Bytes.toBytes(<span class="hljs-string">"sex"</span>));
        <span class="hljs-keyword">byte</span>[] city = result.getValue(Bytes.toBytes(<span class="hljs-string">"address"</span>), Bytes.toBytes(<span class="hljs-string">"city"</span>));
        <span class="hljs-keyword">byte</span>[] province = result.getValue(Bytes.toBytes(<span class="hljs-string">"address"</span>), Bytes.toBytes(<span class="hljs-string">"province"</span>));
        <span class="hljs-keyword">if</span> (name != <span class="hljs-keyword">null</span>) System.out.println(<span class="hljs-string">"姓名："</span> +  Bytes.toString( name));
        <span class="hljs-keyword">if</span> (sex != <span class="hljs-keyword">null</span>) System.out.println(<span class="hljs-string">"性别："</span> +  Bytes.toString( sex));
        <span class="hljs-keyword">if</span> (province != <span class="hljs-keyword">null</span>) System.out.println(<span class="hljs-string">"省份："</span> +  Bytes.toString(province));
        <span class="hljs-keyword">if</span> (city != <span class="hljs-keyword">null</span>) System.out.println(<span class="hljs-string">"城市："</span> +  Bytes.toString(city));
    }

    <span class="hljs-comment">// 全表扫描 Scan</span>
    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">scanData</span>() <span class="hljs-keyword">throws</span> Exception {
        Scan scan = <span class="hljs-keyword">new</span> Scan(); <span class="hljs-comment">// Scan 全表扫描对象</span>
        <span class="hljs-comment">// 行健是以字典序排序，可以使用scan.setStartRow()，scan.setStopRow()设置行健的字典序</span>
        <span class="hljs-comment">// scan.addFamily(Bytes.toBytes("info")); // 只查询列族info</span>
        <span class="hljs-comment">//scan.addColumn(Bytes.toBytes("info"), Bytes.toBytes("name")); // 只查询列name</span>
        ResultScanner scanner = table.getScanner(scan);
        printResult1(scanner);
    }

    <span class="hljs-comment">// 全表扫描：列值过滤器（过滤列植的相等、不等、范围等） SingleColumnValueFilter</span>
    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">singleColumnValueFilter</span>() <span class="hljs-keyword">throws</span> Exception {
        <span class="hljs-javadoc">/**
         * CompareOp 是一个枚举，有如下几个值
         * LESS                 小于
         * LESS_OR_EQUAL        小于或等于
         * EQUAL                等于
         * NOT_EQUAL            不等于
         * GREATER_OR_EQUAL     大于或等于
         * GREATER              大于
         * NO_OP                无操作
         */</span>
        <span class="hljs-comment">// 查询列名大于San-Qiang Zhang的数据</span>
        SingleColumnValueFilter singleColumnValueFilter = <span class="hljs-keyword">new</span> SingleColumnValueFilter(
                                                            Bytes.toBytes(<span class="hljs-string">"info"</span>), Bytes.toBytes(<span class="hljs-string">"name"</span>),
                                                            CompareFilter.CompareOp.EQUAL, Bytes.toBytes(<span class="hljs-string">"San-Qiang Zhang"</span>));
        Scan scan = <span class="hljs-keyword">new</span> Scan();
        scan.setFilter(singleColumnValueFilter);
        ResultScanner scanner = table.getScanner(scan);
        printResult1(scanner);
    }

    <span class="hljs-comment">// 全表扫描：列名前缀过滤器（过滤指定前缀的列名） ColumnPrefixFilter</span>
    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">columnPrefixFilter</span>() <span class="hljs-keyword">throws</span> Exception {
        <span class="hljs-comment">// 查询列以name_开头的数据</span>
        ColumnPrefixFilter columnPrefixFilter = <span class="hljs-keyword">new</span> ColumnPrefixFilter(Bytes.toBytes(<span class="hljs-string">"name_"</span>));
        Scan scan = <span class="hljs-keyword">new</span> Scan();
        scan.setFilter(columnPrefixFilter);
        ResultScanner scanner = table.getScanner(scan);
        printResult1(scanner);
    }

    <span class="hljs-comment">// 全表扫描：多个列名前缀过滤器（过滤多个指定前缀的列名） MultipleColumnPrefixFilter</span>
    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">multipleColumnPrefixFilter</span>() <span class="hljs-keyword">throws</span> Exception {
        <span class="hljs-comment">// 查询列以name_或c开头的数据</span>
        <span class="hljs-keyword">byte</span>[][] bytes = <span class="hljs-keyword">new</span> <span class="hljs-keyword">byte</span>[][]{Bytes.toBytes(<span class="hljs-string">"name_"</span>), Bytes.toBytes(<span class="hljs-string">"c"</span>)};
        MultipleColumnPrefixFilter multipleColumnPrefixFilter = <span class="hljs-keyword">new</span> MultipleColumnPrefixFilter(bytes);
        Scan scan = <span class="hljs-keyword">new</span> Scan();
        scan.setFilter(multipleColumnPrefixFilter);
        ResultScanner scanner = table.getScanner(scan);
        printResult1(scanner);
    }

    <span class="hljs-comment">// rowKey过滤器（通过正则，过滤rowKey值） RowFilter</span>
    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">rowFilter</span>() <span class="hljs-keyword">throws</span> Exception {
        <span class="hljs-comment">// 匹配rowkey以100开头的数据</span>
        <span class="hljs-comment">// Filter filter = new RowFilter(CompareFilter.CompareOp.EQUAL, new RegexStringComparator("^100"));</span>
        <span class="hljs-comment">// 匹配rowkey以2结尾的数据</span>
        RowFilter filter = <span class="hljs-keyword">new</span> RowFilter(CompareFilter.CompareOp.EQUAL, <span class="hljs-keyword">new</span> RegexStringComparator(<span class="hljs-string">"2$"</span>));
        Scan scan = <span class="hljs-keyword">new</span> Scan();
        scan.setFilter(filter);
        ResultScanner scanner = table.getScanner(scan);
        printResult1(scanner);
    }

    <span class="hljs-comment">// 多个过滤器一起使用</span>
    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">multiFilterTest</span>() <span class="hljs-keyword">throws</span> Exception {
        <span class="hljs-javadoc">/**
         * Operator 为枚举类型，有两个值 MUST_PASS_ALL 表示 and，MUST_PASS_ONE 表示 or
         */</span>
        FilterList filterList = <span class="hljs-keyword">new</span> FilterList(FilterList.Operator.MUST_PASS_ALL);
        <span class="hljs-comment">// 查询性别为0（nv）且 行健以10开头的数据</span>
        SingleColumnValueFilter singleColumnValueFilter = <span class="hljs-keyword">new</span> SingleColumnValueFilter(
                                                        Bytes.toBytes(<span class="hljs-string">"info"</span>), Bytes.toBytes(<span class="hljs-string">"sex"</span>),
                                                        CompareFilter.CompareOp.EQUAL, Bytes.toBytes(<span class="hljs-string">"0"</span>));
        RowFilter rowFilter = <span class="hljs-keyword">new</span> RowFilter(CompareFilter.CompareOp.EQUAL, <span class="hljs-keyword">new</span> RegexStringComparator(<span class="hljs-string">"^10"</span>));
        filterList.addFilter(singleColumnValueFilter);
        filterList.addFilter(rowFilter);
        Scan scan = <span class="hljs-keyword">new</span> Scan();
        scan.setFilter(rowFilter);
        ResultScanner scanner = table.getScanner(scan);
        <span class="hljs-comment">// printResult1(scanner);</span>
        printResult2(scanner);
    }

    <span class="hljs-comment">// --------------------DML 操作 End-------------------</span>
    <span class="hljs-javadoc">/** 打印查询结果：方法一 */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">printResult1</span>(ResultScanner scanner) <span class="hljs-keyword">throws</span> Exception {
        <span class="hljs-keyword">for</span> (Result result: scanner) {
            System.out.println(<span class="hljs-string">"行健："</span> + Bytes.toString(result.getRow()));
            <span class="hljs-keyword">byte</span>[] name = result.getValue(Bytes.toBytes(<span class="hljs-string">"info"</span>), Bytes.toBytes(<span class="hljs-string">"name"</span>));
            <span class="hljs-keyword">byte</span>[] sex = result.getValue(Bytes.toBytes(<span class="hljs-string">"info"</span>), Bytes.toBytes(<span class="hljs-string">"sex"</span>));
            <span class="hljs-keyword">byte</span>[] city = result.getValue(Bytes.toBytes(<span class="hljs-string">"address"</span>), Bytes.toBytes(<span class="hljs-string">"city"</span>));
            <span class="hljs-keyword">byte</span>[] province = result.getValue(Bytes.toBytes(<span class="hljs-string">"address"</span>), Bytes.toBytes(<span class="hljs-string">"province"</span>));
            <span class="hljs-keyword">if</span> (name != <span class="hljs-keyword">null</span>) System.out.println(<span class="hljs-string">"姓名："</span> +  Bytes.toString( name));
            <span class="hljs-keyword">if</span> (sex != <span class="hljs-keyword">null</span>) System.out.println(<span class="hljs-string">"性别："</span> +  Bytes.toString( sex));
            <span class="hljs-keyword">if</span> (province != <span class="hljs-keyword">null</span>) System.out.println(<span class="hljs-string">"省份："</span> +  Bytes.toString(province));
            <span class="hljs-keyword">if</span> (city != <span class="hljs-keyword">null</span>) System.out.println(<span class="hljs-string">"城市："</span> +  Bytes.toString(city));
            System.out.println(<span class="hljs-string">"------------------------------"</span>);
        }
    }

    <span class="hljs-javadoc">/** 打印查询结果：方法二 */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">printResult2</span>(ResultScanner scanner) <span class="hljs-keyword">throws</span> Exception {
        <span class="hljs-keyword">for</span> (Result result: scanner) {
            System.out.println(<span class="hljs-string">"-----------------------"</span>);
            <span class="hljs-comment">// 遍历所有的列及列值</span>
            <span class="hljs-keyword">for</span> (Cell cell : result.listCells()) {
                System.out.print(Bytes.toString(CellUtil.cloneQualifier(cell)) + <span class="hljs-string">"："</span>);
                System.out.print(Bytes.toString(CellUtil.cloneValue(cell)) + <span class="hljs-string">"\t"</span>);
            }
            System.out.println();
            System.out.println(<span class="hljs-string">"-----------------------"</span>);
        }
    }

    <span class="hljs-comment">// 释放资源</span>
    <span class="hljs-annotation">@After</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">destory</span>() <span class="hljs-keyword">throws</span> Exception {
        admin.close();
    }
}</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>