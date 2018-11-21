---
layout:     post
title:      HBase Java API 使用示例
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<div id="article_content" class="article_content tracking-ad">

<p>在使用HBase Java API 之前，大家首先要了解HBase Java API类，可参考博客：http://www.cnblogs.com/ggjucheng/p/3380267.html</p>
<p></p>
<p>
几个相关类与HBase数据模型之间的对应关系</p>
<p>
</p>
<table border="1">
<tbody>
<tr>
<td>
java类</td>
<td>
HBase数据模型</td>
</tr>
<tr>
<td>
HBaseAdmin</td>
<td rowspan="2">
数据库（DataBase)</td>
</tr>
<tr>
<td>
HBaseConfiguration</td>
</tr>
<tr>
<td>
HTable</td>
<td>
表（Table)</td>
</tr>
<tr>
<td>
HTableDescriptor</td>
<td>
列族（Column Family)</td>
</tr>
<tr>
<td>
Put</td>
<td rowspan="3">
列修饰符（Column Qualifier）</td>
</tr>
<tr>
<td>
Get</td>
</tr>
<tr>
<td>
Scanner</td>
</tr>
</tbody>
</table>
<br>
<p></p>
<h2 id="hbaseconfiguration对hbase进行配置"><a target="_blank"></a>
<span>HBaseConfiguration：<span>对HBase进行配置。</span></span></h2>
<div><span></span>
<h2 id="hbaseadmin提供了一个接口来管理hbase数据库的表信息它提供的方法包括创建表删除表列出表项使表有效或无效以及添加或删除表列族成员等"><a target="_blank"></a>
<span>HBaseAdmin：<span>提供了一个接口来管理HBase数据库的表信息。它提供的方法包括：创建表，删除表，列出表项，使表有效或无效，以及添加或删除表列族成员等。</span></span></h2>
<div><span><span></span></span>
<h2 id="htabledescriptor包含了表的名字极其对应表的列族"><a target="_blank"></a>
<span>HTableDescriptor：<span>包含了表的名字极其对应表的列族</span></span></h2>
<div><span></span>
<h2 id="hcolumndescriptor维护着关于列族的信息例如版本号压缩设置等它通常在创建表或者为表添加列族的时候使用列族被创建后不能直接修改只能通过删除然后重新创建的方式列族被删除的时候列族里面的数据也会同时被删除">
<span>HColumnDescriptor：<span>维护着关于列族的信息，例如版本号，压缩设置等。它通常在创建表或者为表添加列族的时候使用。列族被创建后不能直接修改，只能通过删除然后重新创建的方式。列族被删除的时候，列族里面的数据也会同时被删除。</span></span></h2>
<div><span></span>
<h2 id="htable可以用来和hbase表直接通信此方法对于更新操作来说是非线程安全的"><a target="_blank"></a>
<span>HTable：<span>可以用来和HBase表直接通信。此方法对于更新操作来说是非线程安全的。</span></span></h2>
<div><span></span>
<h2 id="put用来对单个行执行添加操作"><a target="_blank"></a>
<span>Put：<span>用来对单个行执行添加操作。</span></span></h2>
<div><span></span>
<h2 id="get用来获取单个行的相关信息"><a target="_blank"></a>
<span>Get：<span>用来获取单个行的相关信息。</span></span></h2>
<div><span></span>
<h2 id="result存储get或者scan操作后获取表的单行值使用此类提供的方法可以直接获取值或者各种map结构key-value对"><a target="_blank"></a>
<span>Result：<span>存储Get或者Scan操作后获取表的单行值。使用此类提供的方法可以直接获取值或者各种Map结构（key-value对）。</span></span></h2>
<div><span></span>
<h2 id="resultscanner客户端获取值的接口"><a target="_blank"></a>
<span>ResultScanner：<span>客户端获取值的接口。</span></span></h2>
<div><span><span>代码示例：</span></span></div>
</div>

<p></p></div> <br>
</div> <br>
</div> <br>
</div> <br>
</div> <br>
</div> <br>
</div>

<div><span><span></span></span><div class="dp-highlighter bg_java"><div class="bar"><div class="tools"><b>[java]</b> <a href="#" rel="nofollow" class="ViewSource" title="view plain" target="_blank">view plain</a><span> <a href="#" rel="nofollow" class="CopyToClipboard" title="copy" target="_blank">copy</a></span><div></div><span> <a href="#" rel="nofollow" class="PrintSource" title="print" target="_blank">print</a></span><a href="#" rel="nofollow" class="About" title="?" target="_blank">?</a></div></div><ol start="1" class="dp-j"><li class="alt"><span><span class="keyword">public</span><span> </span><span class="keyword">class</span><span> HBaseDemo {  </span></span></li><li class=""><span>  </span></li><li class="alt"><span>    <span class="keyword">private</span><span> Configuration conf = </span><span class="keyword">null</span><span>;  </span></span></li><li class=""><span>      </span></li><li class="alt"><span>    <span class="annotation">@Before</span><span>  </span></span></li><li class=""><span>    <span class="keyword">public</span><span> </span><span class="keyword">void</span><span> init(){  </span></span></li><li class="alt"><span>        conf = HBaseConfiguration.create();  </span></li><li class=""><span>        <span class="comment">//客户端连接zookeeper</span><span>  </span></span></li><li class="alt"><span>        conf.set(<span class="string">”hbase.zookeeper.quorum”</span><span>, </span><span class="string">“hadoop01,hadoop02,hadoop03”</span><span>);  </span></span></li><li class=""><span>    }  </span></li><li class="alt"><span>      </span></li><li class=""><span>    <span class="annotation">@Test</span><span>  </span></span></li><li class="alt"><span>    <span class="keyword">public</span><span> </span><span class="keyword">void</span><span> testDrop() </span><span class="keyword">throws</span><span> Exception{  </span></span></li><li class=""><span>          </span></li><li class="alt"><span>        HBaseAdmin admin = <span class="keyword">new</span><span> HBaseAdmin(conf);  </span></span></li><li class=""><span>        admin.disableTable(<span class="string">”account”</span><span>);  </span></span></li><li class="alt"><span>        admin.deleteTable(<span class="string">”account”</span><span>);  </span></span></li><li class=""><span>        admin.close();  </span></li><li class="alt"><span>    }  </span></li><li class=""><span>    <span class="comment">/**</span> </span></li><li class="alt"><span><span class="comment">    *插入数据</span> </span></li><li class=""><span><span class="comment">    */</span><span>  </span></span></li><li class="alt"><span>    <span class="annotation">@Test</span><span>  </span></span></li><li class=""><span>    <span class="keyword">public</span><span> </span><span class="keyword">void</span><span> testPut() </span><span class="keyword">throws</span><span> Exception{  </span></span></li><li class="alt"><span>        HTable table = <span class="keyword">new</span><span> HTable(conf, </span><span class="string">“user”</span><span>);  </span></span></li><li class=""><span>        <span class="comment">//提供row key</span><span>  </span></span></li><li class="alt"><span>        Put put = <span class="keyword">new</span><span> Put(Bytes.toBytes(</span><span class="string">“rk0003”</span><span>));  </span></span></li><li class=""><span>        <span class="comment">//列族、列的标示符、值，参数都是字节数组</span><span>  </span></span></li><li class="alt"><span>        put.add(Bytes.toBytes(<span class="string">”info”</span><span>), Bytes.toBytes(</span><span class="string">“name”</span><span>), Bytes.toBytes(</span><span class="string">“liuyan”</span><span>));  </span></span></li><li class=""><span>        table.put(put);  </span></li><li class="alt"><span>        table.close();  </span></li><li class=""><span>    }  </span></li><li class="alt"><span>    <span class="comment">/**</span> </span></li><li class=""><span><span class="comment">    *获取数据</span> </span></li><li class="alt"><span><span class="comment">    */</span><span>  </span></span></li><li class=""><span>    <span class="annotation">@Test</span><span>  </span></span></li><li class="alt"><span>    <span class="keyword">public</span><span> </span><span class="keyword">void</span><span> testGet() </span><span class="keyword">throws</span><span> Exception{  </span></span></li><li class=""><span>        <span class="comment">//HTablePool pool = new HTablePool(conf, 10);</span><span>  </span></span></li><li class="alt"><span>        <span class="comment">//HTable table = (HTable) pool.getTable(“user”);</span><span>  </span></span></li><li class=""><span>        HTable table = <span class="keyword">new</span><span> HTable(conf, </span><span class="string">“user”</span><span>);  </span></span></li><li class="alt"><span>        Get get = <span class="keyword">new</span><span> Get(Bytes.toBytes(</span><span class="string">“rk0001”</span><span>));  </span></span></li><li class=""><span>        <span class="comment">//get.addColumn(Bytes.toBytes(“info”), Bytes.toBytes(“name”));</span><span>  </span></span></li><li class="alt"><span>        get.setMaxVersions(<span class="number">5</span><span>);  </span></span></li><li class=""><span>        Result result = table.get(get);  </span></li><li class="alt"><span>          </span></li><li class=""><span>      <span class="comment">//String r = Bytes.toString(result.getValue(family, qualifier) );这个获取方法不常用</span><span>  </span></span></li><li class="alt"><span>          </span></li><li class=""><span>        <span class="keyword">for</span><span>(KeyValue kv : result.list()){  </span></span></li><li class="alt"><span>            String family = <span class="keyword">new</span><span> String(kv.getFamily());  </span></span></li><li class=""><span>            System.out.println(family);  </span></li><li class="alt"><span>            String qualifier = <span class="keyword">new</span><span> String(kv.getQualifier());  </span></span></li><li class=""><span>            System.out.println(qualifier);  </span></li><li class="alt"><span>            System.out.println(<span class="keyword">new</span><span> String(kv.getValue()));  </span></span></li><li class=""><span>        }  </span></li><li class="alt"><span>        table.close();  </span></li><li class=""><span>    }  </span></li><li class="alt"><span>      </span></li><li class=""><span>    <span class="annotation">@Test</span><span>  </span></span></li><li class="alt"><span>    <span class="keyword">public</span><span> </span><span class="keyword">void</span><span> testScan() </span><span class="keyword">throws</span><span> Exception{  </span></span></li><li class=""><span>        HTablePool pool = <span class="keyword">new</span><span> HTablePool(conf, </span><span class="number">10</span><span>);  </span></span></li><li class="alt"><span>        HTableInterface table = pool.getTable(<span class="string">”user”</span><span>);  </span></span></li><li class=""><span>        Scan scan = <span class="keyword">new</span><span> Scan(Bytes.toBytes(</span><span class="string">“rk0001”</span><span>), Bytes.toBytes(</span><span class="string">“rk0002”</span><span>));  </span></span></li><li class="alt"><span>        scan.addFamily(Bytes.toBytes(<span class="string">”info”</span><span>));  </span></span></li><li class=""><span>        <span class="comment">//结果集 </span><span>  </span></span></li><li class="alt"><span>        ResultScanner scanner = table.getScanner(scan);  </span></li><li class=""><span>        <span class="keyword">for</span><span>(Result r : scanner){  </span></span></li><li class="alt"><span>            <span class="comment">/**</span> </span></li><li class=""><span><span class="comment">            for(KeyValue kv : r.list()){</span> </span></li><li class="alt"><span><span class="comment">                String family = new String(kv.getFamily());</span> </span></li><li class=""><span><span class="comment">                System.out.println(family);</span> </span></li><li class="alt"><span><span class="comment">                String qualifier = new String(kv.getQualifier());</span> </span></li><li class=""><span><span class="comment">                System.out.println(qualifier);</span> </span></li><li class="alt"><span><span class="comment">                System.out.println(new String(kv.getValue()));</span> </span></li><li class=""><span><span class="comment">            }</span> </span></li><li class="alt"><span><span class="comment">            */</span><span>  </span></span></li><li class=""><span>            <span class="keyword">byte</span><span>[] value = r.getValue(Bytes.toBytes(</span><span class="string">“info”</span><span>), Bytes.toBytes(</span><span class="string">“name”</span><span>));  </span></span></li><li class="alt"><span>            System.out.println(<span class="keyword">new</span><span> String(value));  </span></span></li><li class=""><span>        }  </span></li><li class="alt"><span>        pool.close();  </span></li><li class=""><span>    }  </span></li><li class="alt"><span>      </span></li><li class=""><span>      </span></li><li class="alt"><span>    <span class="annotation">@Test</span><span>  </span></span></li><li class=""><span>    <span class="keyword">public</span><span> </span><span class="keyword">void</span><span> testDel() </span><span class="keyword">throws</span><span> Exception{  </span></span></li><li class="alt"><span>        HTable table = <span class="keyword">new</span><span> HTable(conf, </span><span class="string">“user”</span><span>);  </span></span></li><li class=""><span>        Delete del = <span class="keyword">new</span><span> Delete(Bytes.toBytes(</span><span class="string">“rk0001”</span><span>));  </span></span></li><li class="alt"><span>        del.deleteColumn(Bytes.toBytes(<span class="string">”data”</span><span>), Bytes.toBytes(</span><span class="string">“pic”</span><span>));  </span></span></li><li class=""><span>        table.delete(del);  </span></li><li class="alt"><span>        table.close();  </span></li><li class=""><span>    }  </span></li><li class="alt"><span>      </span></li><li class=""><span>      </span></li><li class="alt"><span>      </span></li><li class=""><span>      </span></li><li class="alt"><span>    <span class="keyword">public</span><span> </span><span class="keyword">static</span><span> </span><span class="keyword">void</span><span> main(String[] args) </span><span class="keyword">throws</span><span> Exception {  </span></span></li><li class=""><span>        Configuration conf = HBaseConfiguration.create();  </span></li><li class="alt"><span>        conf.set(<span class="string">”hbase.zookeeper.quorum”</span><span>, </span><span class="string">“hadoop01,hadoop02,hadoop03”</span><span>);  </span></span></li><li class=""><span>        <span class="comment">//ddl操作，传入conf，可知操作哪个hbase集群</span><span>  </span></span></li><li class="alt"><span>        HBaseAdmin admin = <span class="keyword">new</span><span> HBaseAdmin(conf);  </span></span></li><li class=""><span>        HTableDescriptor td = <span class="keyword">new</span><span> HTableDescriptor(</span><span class="string">“account”</span><span>);  </span></span></li><li class="alt"><span>        HColumnDescriptor cd = <span class="keyword">new</span><span> HColumnDescriptor(</span><span class="string">“info”</span><span>);  </span></span></li><li class=""><span>        cd.setMaxVersions(<span class="number">10</span><span>);  </span></span></li><li class="alt"><span>        td.addFamily(cd);  </span></li><li class=""><span>        admin.createTable(td);  </span></li><li class="alt"><span>        admin.close();  </span></li><li class=""><span>  </span></li><li class="alt"><span>    }  </span></li><li class=""><span>      </span></li><li class="alt"><span>    <span class="keyword">public</span><span> </span><span class="keyword">void</span><span> createTable(String tableName, </span><span class="keyword">int</span><span> maxVersion, String… cf){  </span></span></li><li class=""><span>          </span></li><li class="alt"><span>    }  </span></li><li class=""><span>  </span></li><li class="alt"><span>}  </span></li></ol></div><pre><code class="language-java">public class HBaseDemo {

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
</code></pre><br>
<br>
</div>

<p></p>

<p></p></div>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>