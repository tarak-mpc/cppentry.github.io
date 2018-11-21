---
layout:     post
title:      spring封装hbase的代码实现
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/antao592/article/details/52788763				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>前面我们讲了spring封装mongodb的代码实现，这里我们讲一下spring封装hbase的代码实现。</p>
<p><strong>hbase的简介：</strong></p>
<p>此处大概说一下，不是我们要讨论的重点。</p>
<p>HBase是一个分布式的、面向列的开源数据库，HBase在Hadoop之上提供了类似于Bigtable的能力。HBase是Apache的Hadoop项目的子项目。HBase不同于一般的关系数据库，它是一个适合于非结构化数据存储的数据库。另一个不同的是HBase基于列的而不是基于行的模式。hbase是bigtable的开源山寨版本。是建立的hdfs之上，提供高可靠性、高性能、列存储、可伸缩、实时读写的数据库系统。它介于nosql和RDBMS之间，仅能通过主键(row key)和主键的range来检索数据，仅支持单行事务(可通过hive支持来实现多表join等复杂操作)。主要用来存储非结构化和半结构化的松散数据。与hadoop一样，Hbase目标主要依靠横向扩展，通过不断增加廉价的商用服务器，来增加计算和存储能力。hbase给我的印象就是无限存，按照Key读取。</p>
<p>那么在我们的java程序中应该如何使用hbase呢。</p>
<p><strong>首先：</strong></p>
<p>引入hbase的jar包，如果不是Maven项目，可以单独按照以下格式下载hbase的jar包引入到你的项目里。</p>
<p></p><pre><code class="language-html">        &lt;dependency&gt;
            &lt;groupId&gt;org.apache.hbase&lt;/groupId&gt;
            &lt;artifactId&gt;hbase-client&lt;/artifactId&gt;
            &lt;version&gt;0.96.2-hadoop2&lt;/version&gt;
        &lt;/dependency&gt;</code></pre><strong>其次：</strong>
<p>增加hbase在spring中的配置。</p>
<p>1.    新增hbase-site.xml配置文件。以下是通用配置，具体每个参数的含义可以百度以下，这里不做详细讲解。</p>
<p></p><pre><code class="language-html">&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;configuration&gt;
    &lt;!--&lt;property&gt;--&gt;
        &lt;!--&lt;name&gt;hbase.rootdir&lt;/name&gt;--&gt;
        &lt;!--&lt;value&gt;hdfs://ns1/hbase&lt;/value&gt;--&gt;
    &lt;!--&lt;/property&gt;--&gt;
    &lt;property&gt;
        &lt;name&gt;hbase.client.write.buffer&lt;/name&gt;
        &lt;value&gt;62914560&lt;/value&gt;
    &lt;/property&gt;
    &lt;property&gt;
        &lt;name&gt;hbase.client.pause&lt;/name&gt;
        &lt;value&gt;1000&lt;/value&gt;
    &lt;/property&gt;
    &lt;property&gt;
        &lt;name&gt;hbase.client.retries.number&lt;/name&gt;
        &lt;value&gt;10&lt;/value&gt;
    &lt;/property&gt;
    &lt;property&gt;
        &lt;name&gt;hbase.client.scanner.caching&lt;/name&gt;
        &lt;value&gt;1&lt;/value&gt;
    &lt;/property&gt;
    &lt;property&gt;
        &lt;name&gt;hbase.client.keyvalue.maxsize&lt;/name&gt;
        &lt;value&gt;6291456&lt;/value&gt;
    &lt;/property&gt;
    &lt;property&gt;
        &lt;name&gt;hbase.rpc.timeout&lt;/name&gt;
        &lt;value&gt;60000&lt;/value&gt;
    &lt;/property&gt;
    &lt;property&gt;
        &lt;name&gt;hbase.security.authentication&lt;/name&gt;
        &lt;value&gt;simple&lt;/value&gt;
    &lt;/property&gt;
    &lt;property&gt;
        &lt;name&gt;zookeeper.session.timeout&lt;/name&gt;
        &lt;value&gt;60000&lt;/value&gt;
    &lt;/property&gt;
    &lt;property&gt;
        &lt;name&gt;zookeeper.znode.parent&lt;/name&gt;
        &lt;value&gt;ZooKeeper中的HBase的根ZNode&lt;/value&gt;
    &lt;/property&gt;
    &lt;property&gt;
        &lt;name&gt;zookeeper.znode.rootserver&lt;/name&gt;
        &lt;value&gt;root-region-server&lt;/value&gt;
    &lt;/property&gt;
    &lt;property&gt;
        &lt;name&gt;hbase.zookeeper.quorum&lt;/name&gt;
        &lt;value&gt;zookeeper集群&lt;/value&gt;
    &lt;/property&gt;
    &lt;property&gt;
        &lt;name&gt;hbase.zookeeper.property.clientPort&lt;/name&gt;
        &lt;value&gt;2181&lt;/value&gt;
    &lt;/property&gt;
&lt;/configuration&gt;</code></pre>2. 新建spring-config-hbase.xml文件，记得在spring的配置文件中把这个文件Import进去。<br><br><pre><code class="language-html">&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;beans
        xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:hdp="http://www.springframework.org/schema/hadoop"
        xsi:schemaLocation="http://www.springframework.org/schema/beans
http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
http://www.springframework.org/schema/hadoop
http://www.springframework.org/schema/hadoop/spring-hadoop.xsd
"&gt;
    &lt;hdp:configuration resources="classpath:spring/hbase-site.xml" /&gt;
    &lt;hdp:hbase-configuration configuration-ref="hadoopConfiguration" /&gt;
    &lt;bean id="htemplate" class="org.springframework.data.hadoop.hbase.HbaseTemplate"&gt;</code></pre><pre><code class="language-html">&lt;!--注意到没有，spring的一贯风格，正如我们在mongodb篇讲到的一样，xxxTemplate封装--&gt;
        &lt;property name="configuration" ref="hbaseConfiguration"&gt;
        &lt;/property&gt;
    &lt;/bean&gt;
    &lt;bean class="com..HbaseDaoImpl" id="hbaseDao"&gt;
        &lt;constructor-arg ref="htemplate"/&gt;
    &lt;/bean&gt;
&lt;/beans&gt;</code></pre><strong>最后：</strong>
<p>我们就可以重写我们的HbaseDaoImple类了。在这里可以实现我们操作hbase的代码逻辑。其中prism:OrderInfo是我们的表名，f是列族名称，OrderInfo的属性是列族下的列名。orderInfo是我程序定义的bean，你可以按照自己的需求定义自己的bean。</p>
<p></p><pre><code class="language-html">public class HbaseDaoImpl{


    private HbaseTemplate hbaseTemplate;

    private HConnection hconnection = null;

    public HbaseDaoImpl(HbaseTemplate htemplate) throws Exception {
        if (hconnection == null) {
            hconnection = HConnectionManager.createConnection(htemplate.getConfiguration());
        }
        if (this.hbaseTemplate == null) {
            this.hbaseTemplate = htemplate;
        }
    }
    public void writeDataOrderinfo(final OrderInfo orderInfo) {
        HTableInterface table = null;
        try {
            table = hconnection.getTable(Bytes.toBytes("prism:orderInfo"));
            Put p = new Put(Bytes.toBytes( orderInfo.getHistoryId()));
            p.add(Bytes.toBytes("f"), Bytes.toBytes("id"), Bytes.toBytes(orderInfo.getId()));
            p.add(Bytes.toBytes("f"), Bytes.toBytes("historyId"), Bytes.toBytes(orderInfo.getHistoryId()));
            p.add(Bytes.toBytes("f"), Bytes.toBytes("orderId"), Bytes.toBytes(orderInfo.getOrderId()));
            p.add(Bytes.toBytes("f"), Bytes.toBytes("orderDirection"), Bytes.toBytes(orderInfo.getOrderDirection()));
            p.add(Bytes.toBytes("f"), Bytes.toBytes("overStatus"), Bytes.toBytes(orderInfo.getOverStatus()));
            p.add(Bytes.toBytes("f"), Bytes.toBytes("orgArea"), Bytes.toBytes(orderInfo.getOrgArea()));
            table.put(p);

        } catch (IOException e) {
            throw new RuntimeException(e);
        } finally {
            if (table != null) {
                try {
                    table.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
    public OrderInfo getOrderInfoByRowkey(String rowKey) {
        Get get = new Get(Bytes.toBytes(rowKey));
        Scan scan = new Scan(get);
        List&lt;OrderInfo&gt; list = hbaseTemplate.find("prism:orderInfo", scan, new RowMapper&lt;OrderInfo&gt;() {
            @Override
            public OrderInfo mapRow(Result result, int rowNum) throws Exception {
                OrderInfo orderInfo = new OrderInfo();
                orderInfo.setId(Bytes.toString(result.getValue(Bytes.toBytes("f"), Bytes.toBytes("id"))));
                orderInfo.setHistoryId(Bytes.toString(result.getValue(Bytes.toBytes("f"), Bytes.toBytes("historyId"))));
                orderInfo.setOrderId(Bytes.toLong(result.getValue(Bytes.toBytes("f"), Bytes.toBytes("orderId"))));              
                return orderInfo;
            }

        });
        if(list.size() &gt; 0){
        	return list.get(0);
        }else{
        	return null;
        }
                
    }

    public List&lt;OrderInfo&gt; getOrderInfoByRange(String start_rowKey,String stop_rowKey) {
        Scan scan = new Scan();
        scan.setStartRow(Bytes.toBytes(start_rowKey));
        scan.setStopRow(Bytes.toBytes(stop_rowKey));
        HTableInterface table = null;
        ResultScanner rs = null;
        List&lt;OrderInfo&gt; list = new ArrayList&lt;OrderInfo&gt;();
        try {
			table = hconnection.getTable(Bytes.toBytes("prism:orderInfo"));
			rs = table.getScanner(scan);
			for(Result result : rs){
				OrderInfo orderInfo = new OrderInfo();
                orderInfo.setId(Bytes.toString(result.getValue(Bytes.toBytes("f"), Bytes.toBytes("id"))));
                orderInfo.setHistoryId(Bytes.toString(result.getValue(Bytes.toBytes("f"), Bytes.toBytes("historyId"))));
                orderInfo.setOrderId(Bytes.toLong(result.getValue(Bytes.toBytes("f"), Bytes.toBytes("orderId"))));
                orderInfo.setOrderDirection(Bytes.toString(result.getValue(Bytes.toBytes("f"), Bytes.toBytes("orderDirection"))));
                list.add(orderInfo);
			}
		} catch (IOException e) {
			e.printStackTrace();
		}finally{
			rs.close();
		}
		return list;       
    }
    public HbaseTemplate getHbaseTemplate() {
        return hbaseTemplate;
    }

    public void setHbaseTemplate(HbaseTemplate hbaseTemplate) {
        this.hbaseTemplate = hbaseTemplate;
    }

}
</code></pre><br>
注：在程序中，你可以使用spring封装的HbaseTemplate，也可以使用原生的hconnection等的操作方式，如何操作在我们的代码示例中都有。个人觉得，spring封装的HbaseTemplate不太好使，比如每次请求都会重新链接一下zookeeper集群（其中缘由我也没去研究，有研究透的同学还望不吝赐教）。建议用原生的方式。<br><br><br><br>            </div>
                </div>