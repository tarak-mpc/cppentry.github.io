---
layout:     post
title:      hbase的练习和phonenix的使用
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>1.进入hbase的操作</p>

<p>./hbase shell</p>

<p>list 展示 所有表格</p>

<p><img alt="" class="has" height="288" src="https://img-blog.csdn.net/20181025152833887?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3R0a3NneHg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="715"></p>

<p>此时hdfs上可以查询到hbase上设置的t1</p>

<p><img alt="" class="has" height="347" src="https://img-blog.csdn.net/201810251533433?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3R0a3NneHg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="714"></p>

<p> </p>

<p>java代码连接zookeeper控制hbase代码</p>

<pre class="has">
<code class="hljs">import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.*;
import org.apache.hadoop.hbase.client.*;
import org.apache.hadoop.hbase.filter.CompareFilter;
import org.apache.hadoop.hbase.filter.FilterList;
import org.apache.hadoop.hbase.filter.PrefixFilter;
import org.apache.hadoop.hbase.filter.SingleColumnValueFilter;
import org.junit.After;
import org.junit.Before;
import org.junit.Test;

import java.io.IOException;
import java.util.ArrayList;
import java.util.Iterator;
import java.util.List;
import java.util.Random;

/**
 * Created by Administrator on 2016/2/28 0028.
 */
public class TestHbase {
   public static Connection conn = null;
    public static TableName test = TableName.valueOf("t_phone");
    public static Random ra = new Random();
    @Test
    public void create() throws IOException {

        Admin admin = conn.getAdmin();
        if(admin.tableExists(test)){
            admin.disableTable(test);
            admin.deleteTable(test);
        }
        HTableDescriptor ht = new HTableDescriptor(test);
        HColumnDescriptor hc = new HColumnDescriptor("cf1".getBytes());
        hc.setMaxVersions(5);
        hc.setBlockCacheEnabled(true);
        hc.setBlocksize(1800000);
        ht.addFamily(hc);
        admin.createTable(ht);
    }

    @Before
    public void setup() throws IOException {

        Configuration conf = HBaseConfiguration.create();
        conf.set("hbase.zookeeper.quorum", "192.168.218.130,192.168.218.134,192.168.218.132");
        conn = ConnectionFactory.createConnection(conf);
    }
    @After
    public void after() throws IOException {
        if(conn!=null){
            conn.close();
        }
    }

    @Test
    public void insert() throws IOException {
        Table table = conn.getTable(test);
        List&lt;Put&gt; list = new ArrayList&lt;Put&gt;();
        for(int i=0;i&lt;1000;i++){
            Put put = new Put(getRowKey("138").getBytes());
            put.addColumn("cf1".getBytes(),"address".getBytes(),"北京".getBytes());
            put.addColumn("cf1".getBytes(),"type".getBytes(),String.valueOf(ra.nextInt(2)).getBytes());
            list.add(put);
        }
        table.put(list);
    }
    @Test
    public void find() throws IOException {
        Table table = conn.getTable(test);
        Scan scan = new Scan("13899459154_2016106133221".getBytes(),"13899950496_20167720852".getBytes());
        ResultScanner scanner = table.getScanner(scan);
        Iterator&lt;Result&gt; it = scanner.iterator();
        while(it.hasNext()){
            Result next = it.next();
            byte[] value = next.getValue("cf1".getBytes(), "type".getBytes());
            System.out.println(new String(value,"GBK"));
        }


    }
    //某个手机号，某段时间，主叫电话
    //查询首字母1389，type=1
    @Test
    public void find1() throws IOException {
        Table table = conn.getTable(test);
        Scan scan = new Scan();
        FilterList fl = new FilterList(FilterList.Operator.MUST_PASS_ALL);
        PrefixFilter pf = new PrefixFilter("1389".getBytes());
        SingleColumnValueFilter sf = new SingleColumnValueFilter("cf1".getBytes(),"type".getBytes(), CompareFilter.CompareOp.EQUAL,"1".getBytes());
        //过滤器的顺序影响效率
        fl.addFilter(pf);
        fl.addFilter(sf);
        scan.setFilter(fl);
        ResultScanner scanner = table.getScanner(scan);
        Iterator&lt;Result&gt; it = scanner.iterator();
        while(it.hasNext()){
            Result next = it.next();
            byte[] value = next.getValue("cf1".getBytes(), "address".getBytes());
            System.out.println(new String(value,"utf8"));
        }
    }

    @Test
    public void search() throws IOException {
        Table table = conn.getTable(test);
        Get get = new Get("RK123".getBytes());
//        get.addColumn("cf1".getBytes(),"name".getBytes());
        Result result = table.get(get);
        Cell cell = result.getColumnLatestCell("cf1".getBytes(), "age".getBytes());
        System.out.printf(new String(cell.getValueArray()));
    }

    public String getRowKey(String pre){
        return pre+ ra.nextInt(99999999)+"_2016"+ra.nextInt(12)+ra.nextInt(30)+ra.nextInt(24)+ra.nextInt(60)+ra.nextInt(60);
    }

}
</code></pre>

<p><img alt="" class="has" height="42" src="https://img-blog.csdn.net/20181025160323567?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3R0a3NneHg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="288"></p>

<p>可以看到运行create和insert后插入的数据</p>

<p>hbase的具体操作命令</p>

<p><a href="https://www.cnblogs.com/cxzdy/p/5583239.html" rel="nofollow">https://www.cnblogs.com/cxzdy/p/5583239.html</a></p>

<p>查询命令：</p>

<p><img alt="" class="has" height="251" src="https://img-blog.csdn.net/20181025162311463?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3R0a3NneHg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="679"></p>

<p>phonenix:</p>

<p>1.解压phoenix-4.5.2-HBase-1.1-bin.tar</p>

<p>2.将jar放入hbase集群的各个节点中去</p>

<p>   cp /home/phoenix-4.5.2-HBase-1.1-bin/phoenix-core-4.5.2-HBase-1.1.jar /home//hbase-1.1.3/lib/</p>

<p>   scp /home/phoenix-4.5.2-HBase-1.1-bin/phoenix-core-4.5.2-HBase-1.1.jar root@node2:/home//hbase-1.1.3/lib/</p>

<p>  scp /home/phoenix-4.5.2-HBase-1.1-bin/phoenix-core-4.5.2-HBase-1.1.jar root@node3:/home//hbase-1.1.3/lib/</p>

<p>3.重启hbase</p>

<p>4../stop-hbase.sh</p>

<p>5../start-hbase.sh</p>

<p>6.连接使用phonenix</p>

<p>   ./sqlline.py node1</p>

<p><img alt="" class="has" height="237" src="https://img-blog.csdnimg.cn/20181025190528491.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3R0a3NneHg=,size_27,color_FFFFFF,t_70" width="601"></p>

<p>成功使用phonenix来使用sql操作hbase;完美</p>            </div>
                </div>