---
layout:     post
title:      [Hbase]eclipse下操作hbase
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>ubuntu14.04,eclipse下操作hbase。下面是一个利用hbase java api操作hbase，查看hbase中表student1列族情况的example:</p>
<p></p><pre><code class="language-java">import java.io.IOException;
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.HColumnDescriptor;
import org.apache.hadoop.hbase.HTableDescriptor;
import org.apache.hadoop.hbase.client.HBaseAdmin;
import org.apache.hadoop.hbase.util.Bytes;

public class OperateTable {
        public static void main(String[] args) throws IOException {
                
                  Configuration conf = HBaseConfiguration.create();
                  conf.set("hbase.zookeeper.quorum", "localhost");//使用eclipse时必须添加这个，否则无法定位.后一个参数写地址，我这里是伪分布的，所以为localhost
                  conf.set("hbase.zookeeper.property.clientPort", "2181");
                  HBaseAdmin admin = new HBaseAdmin(conf);// 新建一个数据库管理员
                  HTableDescriptor tableDescriptor = admin.getTableDescriptor(Bytes.toBytes("student1"));
                  byte[] name = tableDescriptor.getName();
                  System.out.println("result:");
                  System.out.println("table name: "+ new String(name));
                  HColumnDescriptor[] columnFamilies = tableDescriptor
                                  .getColumnFamilies();
                  for(HColumnDescriptor d : columnFamilies){
                          System.out.println("column Families: "+ d.getNameAsString());
                          }
            }
}</code></pre><br><br>            </div>
                </div>