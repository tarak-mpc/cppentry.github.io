---
layout:     post
title:      HBase读写code
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>最近用HBase存储数据，记录下方法</p>

<ol>
<li>HBase批量写数据(rowkey、列簇、value) <br>
a:连接： <br>
private Connection initHbaseConnection(){ <br>
    if (StringUtils.isBlank(hbaseZookeeperQuorum) || StringUtils.isBlank(hbaseZookeeperZnodeParent)) { <br>
        LOG.warn(“hbaseZookeeperQuorum = {}, hbaseZookeeperZnodeParent = {}, initHbaseConnection return null”, <br>
                hbaseZookeeperQuorum, hbaseZookeeperZnodeParent); <br>
        throw new IllegalArgumentException( <br>
                “initHbaseConnection hbaseZookeeperQuorum/hbaseZookeeperZnodeParent is empty”); <br>
    } <br>
    Configuration configuration = HBaseConfiguration.create(); <br>
    configuration.set(“hbase.zookeeper.quorum”, hbaseZookeeperQuorum); <br>
    configuration.set(“zookeeper.znode.parent”, hbaseZookeeperZnodeParent); <br>
    try { <br>
        return ConnectionFactory.createConnection(configuration); <br>
    } catch (IOException e) { <br>
        LOG.error(“ERROR init Hbase Connection, Hbase Connection is null”); <br>
        return null; <br>
    } <br>
} <br>
b:单个写 <br>
public boolean setProductTag() { <br>
    final String family = snTypeEnum == m; <br>
    final String rowKey = getRowKey(productType, productId); <br>
    Table table = null; <br>
    try { <br>
        table = hbaseConnection.getTable(TableName.valueOf(HBASE_TABLE_NAME)); <br>
        Put p = new Put(Bytes.toBytes(rowKey)); <br>
        p.addColumn(Bytes.toBytes(family), Bytes.toBytes(Sn), expireTime, Bytes.toBytes(Value)); <br>
        table.put(p); <br>
        return true; <br>
    } catch (IOException e)  <br>
        return false; <br>
    } finally { <br>
        try { <br>
            if (table != null) { <br>
                table.close(); <br>
            } <br>
        } catch (IOException e) { <br>
            LOG.error(“table.close error”, e); <br>
        } <br>
    } <br>
} <br>
c:写多列rowkey <br>
try { <br>
        table = hbaseConnection.getTable(TableName.valueOf(HBASE_TABLE_NAME)); <br>
        List puts = Lists.newArrayListWithExpectedSize(ptpProductTags.size()); <br>
        for (PtpProductTag ptpProductTag : ptpProductTags) { <br>
            final String family =m; <br>
            int productType = 0; <br>
            String tId = ; <br>
            String Sn = ; <br>
            String Value = ; <br>
            Long expireTime = ; <br>
            final String rowKey = <br>
            Put p = new Put(Bytes.toBytes(rowKey)); <br>
            p.addColumn(Bytes.toBytes(family), Bytes.toBytes(Sn), expireTime, Bytes.toBytes(Value)); <br>
            puts.add(p); <br>
        } <br>
        if (!puts.isEmpty()) { <br>
            table.put(puts); <br>
        } <br>
        return true; <br>
    } catch (IOException e) { <br>
        return false; <br>
    } finally { <br>
        try { <br>
            if (table != null) { <br>
                table.close(); <br>
            } <br>
        } catch (IOException e) { <br>
            LOG.error(“table.close error”, e); <br>
        } <br>
    } <br>
2、HBase拉取全量数据 <br>
由于HBase没有offSet，拉取数据方法如下： <br>
public interface Comparator { <br>
    void compare(Multimap</li></ol>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>