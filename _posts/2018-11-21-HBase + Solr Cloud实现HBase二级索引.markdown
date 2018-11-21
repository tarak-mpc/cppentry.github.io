---
layout:     post
title:      HBase + Solr Cloud实现HBase二级索引
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u011462328/article/details/53008434				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h2>1. <strong>执行流程</strong></h2>
<p></p>
<p> <img src="https://img-blog.csdn.net/20161102153129292" alt=""></p>
<h2>2. <strong>Solr Cloud<span style="font-family:'黑体';">实现</span></strong></h2>
<p><a href="http://blog.csdn.net/u011462328/article/details/53008344" rel="nofollow">http://blog.csdn.net/u011462328/article/details/53008344</a><br></p>
<h2>3. <strong>HBase<span style="font-family:'黑体';">实现</span></strong></h2>
<p>1) 自定义<span style="font-family:Calibri;">Observer</span></p>
<p>①　代码</p>
<p></p>
<pre><code class="language-java">package cn.bfire.coprocessor;

import com.typesafe.config.Config;
import com.typesafe.config.ConfigFactory;
import org.apache.hadoop.hbase.Cell;
import org.apache.hadoop.hbase.CellUtil;
import org.apache.hadoop.hbase.client.Delete;
import org.apache.hadoop.hbase.client.Durability;
import org.apache.hadoop.hbase.client.Put;
import org.apache.hadoop.hbase.coprocessor.BaseRegionObserver;
import org.apache.hadoop.hbase.coprocessor.ObserverContext;
import org.apache.hadoop.hbase.coprocessor.RegionCoprocessorEnvironment;
import org.apache.hadoop.hbase.regionserver.wal.WALEdit;
import org.apache.hadoop.hbase.util.Bytes;
import org.apache.solr.common.SolrInputDocument;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import java.io.IOException;
import java.util.List;

/**
 * 为hbase提供二级索引的协处理器 Coprocesser
 */
public class UserDevPiSolrObserver extends BaseRegionObserver {

    //加载配置文件属性
    static Config config = ConfigFactory.load("userdev_pi_solr.properties");

    //log记录
    private static final Logger logger = LoggerFactory.getLogger(UserDevPiSolrObserver.class);

    @Override
    public void postPut(ObserverContext&lt;RegionCoprocessorEnvironment&gt; e, Put put, WALEdit edit, Durability durability) throws IOException {
        // 获取行键值
        String rowkey = Bytes.toString(put.getRow());
        //实例化 SolrDoc
        SolrInputDocument doc = new SolrInputDocument();
        //添加Solr uniqueKey值
        doc.addField("rowkey", rowkey);
        // 获取需要索引的列
        String[] hbase_columns = config.getString("hbase_column").split(",");

        // 获取需要索引的列的值并将其添加到SolrDoc
        for (int i = 0; i &lt; hbase_columns.length; i++) {
            String colName = hbase_columns[i];
            String colValue = "";
            // 获取指定列
            List&lt;Cell&gt; cells = put.get("cf".getBytes(), colName.getBytes());
            if (cells != null) {
                try {
                    colValue = Bytes.toString(CellUtil.cloneValue(cells.get(0)));
                } catch (Exception ex) {
                    logger.error("添加solrdoc错误", ex);
                }
            }

            doc.addField(colName, colValue);
        }

        //发送数据到本地缓存
        SolrIndexTools.addDoc(doc);
    }

    @Override
    public void postDelete(ObserverContext&lt;RegionCoprocessorEnvironment&gt; e, Delete delete, WALEdit edit, Durability durability) throws IOException {
        //得到rowkey
        String rowkey = Bytes.toString(delete.getRow());
        //发送数据本地缓存
        String solr_collection = config.getString("solr_collection");
        SolrIndexTools.delDoc(rowkey);
    }
}
</code></pre><br><br><p></p><pre><code class="language-java">package cn.bfire.coprocessor;

import com.typesafe.config.Config;
import com.typesafe.config.ConfigFactory;
import org.apache.solr.client.solrj.impl.CloudSolrClient;
import org.apache.solr.common.SolrInputDocument;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import java.util.ArrayList;
import java.util.List;
import java.util.Timer;
import java.util.TimerTask;
import java.util.concurrent.Semaphore;

/**
 * solr索引处理客户端
 * 注意问题，并发提交时，需要线程协作资源
 */
public class SolrIndexTools {
    //加载配置文件属性
    static Config config = ConfigFactory.load("userdev_pi_solr.properties");
    //log记录
    private static final Logger logger = LoggerFactory.getLogger(SolrIndexTools.class);
    //实例化solr的client
    static CloudSolrClient client = null;
    //添加批处理阈值
    static int add_batchCount = config.getInt("add_batchCount");
    //删除的批处理阈值
    static int del_batchCount = config.getInt("del_batchCount");
    //添加的集合缓冲
    static List&lt;SolrInputDocument&gt; add_docs = new ArrayList&lt;SolrInputDocument&gt;();
    //删除的集合缓冲
    static List&lt;String&gt; del_docs = new ArrayList&lt;String&gt;();


    static final List&lt;String&gt; zkHosts = new ArrayList&lt;String&gt;();

    static {
        logger.info("初始化索引调度........");
        String zk_host = config.getString("zk_host");
        String[] data = zk_host.split(",");
        for (String zkHost : data) {
            zkHosts.add(zkHost);
        }
        client = new CloudSolrClient.Builder().withZkHost(zkHosts).build();
        // 获取Solr collection
        String solr_collection = config.getString("solr_collection");
        client.setDefaultCollection(solr_collection);
        client.setZkClientTimeout(10000);
        client.setZkConnectTimeout(10000);

        //启动定时任务，第一次延迟1s执行,之后每隔指定时间30S执行一次
        Timer timer = new Timer();
        timer.schedule(new SolrCommit(), config.getInt("first_delay") * 1000, config.getInt("interval_commit_index") * 1000);
    }

    public static class SolrCommit extends TimerTask {
        @Override
        public void run() {

            logger.info("索引线程运行中........");
            //只有等于true时才执行下面的提交代码
            try {
                semp.acquire();//获取信号量
                if (add_docs.size() &gt; 0) {
                    client.add(add_docs);//添加
                }
                if (del_docs.size() &gt; 0) {
                    client.deleteById(del_docs);//删除
                }
                //确保都有数据才提交
                if (add_docs.size() &gt; 0 || del_docs.size() &gt; 0) {
                    client.commit();//共用一个提交策略
                    //清空缓冲区的添加和删除数据
                    add_docs.clear();
                    del_docs.clear();
                } else {
                    logger.info("暂无索引数据，跳过commit，继续监听......");
                }
            } catch (Exception e) {
                logger.error("间隔提交索引数据出错！", e);
            } finally {
                semp.release();//释放信号量
            }


        }
    }


    /**
     * 添加数据到临时存储中，如果
     * 大于等于batchCount时，就提交一次，
     * 再清空集合,其他情况下走对应的时间间隔提交
     *
     * @param doc 单个document对象
     */
    public static void addDoc(SolrInputDocument doc) {
        commitIndex(add_docs, add_batchCount, doc, true);
    }


    /***
     * 删除的数据添加到临时存储中，如果大于
     * 对应的批处理就直接提交，再清空集合，
     * 其他情况下走对应的时间间隔提交
     *
     * @param rowkey 删除的rowkey
     */
    public static void delDoc(String rowkey) {
        commitIndex(del_docs, del_batchCount, rowkey, false);
    }

    // 任何时候，保证只能有一个线程在提交索引，并清空集合
    final static Semaphore semp = new Semaphore(1);


    /***
     * 此方法需要加锁，并且提交索引时，与时间间隔提交是互斥的
     * 百分百确保不会丢失数据
     *
     * @param datas 用来提交的数据集合
     * @param count 对应的集合提交数量
     * @param doc   添加的单个doc
     * @param isAdd 是否为添加动作
     */
    public synchronized static void commitIndex(List datas, int count, Object doc, boolean isAdd) {
        try {
            semp.acquire();//获取信号量
            if (datas.size() &gt;= count) {

                if (isAdd) {
                    client.add(datas);//添加数据到服务端中
                } else {
                    client.deleteById(datas);//删除数据
                }
                client.commit();//提交数据

                datas.clear();//清空临时集合


            }
        } catch (Exception e) {
            e.printStackTrace();
            logger.error("按阈值" + (isAdd == true ? "添加" : "删除") + "操作索引数据出错！", e);
        } finally {
            datas.add(doc);//添加单条数据
            semp.release();//释放信号量
        }

    }

}</code></pre><br><br><pre><code class="language-java"></code></pre><pre style="font-family:Consolas;font-size:11.3pt;background-color:rgb(255,255,255);">pom文件配置</pre>
<pre style="font-family:Consolas;font-size:11.3pt;background-color:rgb(255,255,255);"></pre><pre><code class="language-html">&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"&gt;
    &lt;modelVersion&gt;4.0.0&lt;/modelVersion&gt;

    &lt;groupId&gt;cn.gcks&lt;/groupId&gt;
    &lt;artifactId&gt;hbase&lt;/artifactId&gt;
    &lt;version&gt;1.0-SNAPSHOT&lt;/version&gt;

    &lt;dependencies&gt;
        &lt;!-- https://mvnrepository.com/artifact/org.apache.solr/solr-solrj --&gt;
        &lt;dependency&gt;
            &lt;groupId&gt;org.apache.solr&lt;/groupId&gt;
            &lt;artifactId&gt;solr-solrj&lt;/artifactId&gt;
            &lt;version&gt;6.2.1&lt;/version&gt;
            &lt;exclusions&gt;
                &lt;exclusion&gt;
                    &lt;groupId&gt;org.slf4j&lt;/groupId&gt;
                    &lt;artifactId&gt;slf4j-api&lt;/artifactId&gt;
                &lt;/exclusion&gt;
            &lt;/exclusions&gt;
        &lt;/dependency&gt;

        &lt;!-- https://mvnrepository.com/artifact/org.apache.hbase/hbase-client --&gt;
        &lt;dependency&gt;
            &lt;groupId&gt;org.apache.hbase&lt;/groupId&gt;
            &lt;artifactId&gt;hbase-client&lt;/artifactId&gt;
            &lt;version&gt;1.1.2&lt;/version&gt;
            &lt;exclusions&gt;
                &lt;exclusion&gt;
                    &lt;groupId&gt;org.apache.hadoop&lt;/groupId&gt;
                    &lt;artifactId&gt;*&lt;/artifactId&gt;
                &lt;/exclusion&gt;
            &lt;/exclusions&gt;
        &lt;/dependency&gt;
        &lt;dependency&gt;
            &lt;groupId&gt;org.apache.hbase&lt;/groupId&gt;
            &lt;artifactId&gt;hbase-server&lt;/artifactId&gt;
            &lt;version&gt;1.1.2&lt;/version&gt;
            &lt;exclusions&gt;
                &lt;exclusion&gt;
                    &lt;groupId&gt;org.apache.hadoop&lt;/groupId&gt;
                    &lt;artifactId&gt;*&lt;/artifactId&gt;
                &lt;/exclusion&gt;
            &lt;/exclusions&gt;
        &lt;/dependency&gt;

        &lt;!-- https://mvnrepository.com/artifact/com.typesafe/config --&gt;
        &lt;dependency&gt;
            &lt;groupId&gt;com.typesafe&lt;/groupId&gt;
            &lt;artifactId&gt;config&lt;/artifactId&gt;
            &lt;version&gt;1.3.1&lt;/version&gt;
        &lt;/dependency&gt;

    &lt;/dependencies&gt;
&lt;/project&gt;</code></pre>
<pre style="font-family:Consolas;font-size:11.3pt;background-color:rgb(255,255,255);"></pre><p>
</p><p><span style="font-weight:bold;color:rgb(0,128,0);font-size:11.3pt;background-color:rgb(228,228,255);">userdev_pi_solr.properties</span></p><p></p><pre><code class="language-plain">#需要建索引的列
hbase_column=oid,pi_id,statdate
# solr的collection名称
solr_collection=userdev_pi_day
#定义solr的url地址，如果是cloud模式，可以配置多个以逗号分隔
zk_host=1.1.1.1:2181,1.1.1.2:2181,1.1.1.3:2181
#调度第一次开始时，延迟多少秒执行
first_delay=10
#后台线程多久提交一次索引，单位秒
interval_commit_index=30
#添加索引的批处理数量
add_batchCount=10000
#删除索引的批处理数量
del_batchCount=2000</code></pre><br><br><p></p><p>②　打包代码并上传到<span style="font-family:Calibri;">hdfs</span><span style="font-family:'宋体';">目录</span></p><p>③　修改<span style="font-family:Calibri;">HBase</span><span style="font-family:'宋体';">表（设置自定义</span><span style="font-family:Calibri;">observer</span><span style="font-family:'宋体';">所在</span><span style="font-family:Calibri;">hdfs</span><span style="font-family:'宋体';">位置，以及指定自定义</span><span style="font-family:Calibri;">Observer</span><span style="font-family:'宋体';">全类名）</span></p><p>alter 'radius:raduserlog', 'coprocessor' =&gt; 'hdfs://<span style="color:rgb(0,112,192);">/apps/hbase/jars/hbase_solr.jar</span>|cn.bfire.coprocessor.UserDevPiSolrObserver|'</p><p>2) 数据查询代码</p><p></p><pre><code class="language-java">package cn.bfire.solr;

import org.apache.commons.logging.Log;
import org.apache.commons.logging.LogFactory;
import org.apache.hadoop.hbase.Cell;
import org.apache.hadoop.hbase.CellUtil;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.TableName;
import org.apache.hadoop.hbase.client.*;
import org.apache.hadoop.hbase.util.Bytes;
import org.apache.solr.client.solrj.SolrQuery;
import org.apache.solr.client.solrj.impl.CloudSolrClient;
import org.apache.solr.client.solrj.response.QueryResponse;
import org.apache.solr.common.SolrDocument;
import org.apache.solr.common.SolrDocumentList;
import org.apache.solr.common.SolrInputDocument;

import java.util.ArrayList;
import java.util.Collection;
import java.util.List;

public class SolrCloudTest {
    public static final Log LOG = LogFactory.getLog(SolrCloudTest.class);
    private static CloudSolrClient cloudSolrClient;

    private static Connection connection;
    private static Table table;
    private static Get get;
    private static String defaultCollection = "userdev_pi_day";
    private static String hbaseTable = "<span style="font-family:Arial, Helvetica, sans-serif;">userdev_pi_day</span><span style="font-family:Arial, Helvetica, sans-serif;">";</span>
    List&lt;Get&gt; list = new ArrayList&lt;Get&gt;();

    static {
        final List&lt;String&gt; zkHosts = new ArrayList&lt;String&gt;();
        zkHosts.add("1.1.1.1:2181");
        zkHosts.add("1.1.1.2:2181");
        zkHosts.add("1.1.1.3:2181");
        cloudSolrClient = new CloudSolrClient.Builder().withZkHost(zkHosts).build();
        final int zkClientTimeout = 10000;
        final int zkConnectTimeout = 10000;
        cloudSolrClient.setDefaultCollection(defaultCollection);
        cloudSolrClient.setZkClientTimeout(zkClientTimeout);
        cloudSolrClient.setZkConnectTimeout(zkConnectTimeout);
        try {
            connection = ConnectionFactory.createConnection(HBaseConfiguration.create());
            table = connection.getTable(TableName.valueOf(hbaseTable));
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    private void addIndex(CloudSolrClient cloudSolrClient) throws Exception {
        Collection&lt;SolrInputDocument&gt; docs = new ArrayList&lt;SolrInputDocument&gt;();
        for (int i = 0; i &lt;= 100; i++) {
            SolrInputDocument doc = new SolrInputDocument();
            String key = "";
            key = String.valueOf(i);
            doc.addField("rowkey", key);
            doc.addField("usermac", key + "usermac");
            doc.addField("userid", key + "userid");
            doc.addField("usertype", key + "usertype");
            doc.addField("city_id", key + "city_id");
            docs.add(doc);
        }
        LOG.info("docs info:" + docs + "\n");
        cloudSolrClient.add(docs);
        cloudSolrClient.commit();
    }

    public void search(CloudSolrClient cloudSolrClient, String Str) throws Exception {
        SolrQuery query = new SolrQuery();
        query.setRows(100);
        query.setQuery(Str);
        LOG.info("query string: " + Str);
        QueryResponse response = cloudSolrClient.query(query);
        SolrDocumentList docs = response.getResults();
        System.out.println("文档个数：" + docs.getNumFound()); //数据总条数也可轻易获取
        System.out.println("查询时间：" + response.getQTime());
        System.out.println("查询总时间：" + response.getElapsedTime());
        for (SolrDocument doc : docs) {
            String rowkey = (String) doc.getFieldValue("rowkey");
            get = new Get(Bytes.toBytes(rowkey));
            list.add(get);
        }

        Result[] res = table.get(list);

        for (Result rs : res) {
            Cell[] cells = rs.rawCells();

            for (Cell cell : cells) {
                System.out.println("============");
                System.out.println(new String(CellUtil.cloneRow(cell)));
                System.out.println(new String(CellUtil.cloneFamily(cell)));
                System.out.println(new String(CellUtil.cloneQualifier(cell)));
                System.out.println(new String(CellUtil.cloneValue(cell)));
                System.out.println("============");
                break;
            }
        }


        table.close();
    }

    public static void main(String[] args) throws Exception {
        cloudSolrClient.connect();
        SolrCloudTest solrt = new SolrCloudTest();
//            solrt.addIndex(cloudSolrClient);
        solrt.search(cloudSolrClient, "userid:11111");
        cloudSolrClient.close();
    }
}
</code></pre><br><br><p></p><p></p><pre></pre><pre></pre>
<pre></pre>
            </div>
                </div>