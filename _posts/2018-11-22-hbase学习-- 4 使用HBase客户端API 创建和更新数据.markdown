---
layout:     post
title:      hbase学习-- 4 使用HBase客户端API 创建和更新数据
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/wslyk606/article/details/78431780				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="color:rgb(51,51,68);font-family:'Helvetica Neue', Helvetica, 'PingFang SC', '微软雅黑', Tahoma, Arial, sans-serif;"><span style="font-size:18px;">学习对HBase表执行CRUD操作的HBase Java客户端API </span></span></p>
<p><span style="font-size:18px;"><br></span></p>
<p></p>
<h2><span style="font-size:18px;">使用Java API插入数据</span></h2>
<p></p>
<p><span style="font-size:18px;">可以使用Put 类的add()方法将数据插入到HBase。可以使用HTable类的put()方法保存数据。这些类属于org.apache.hadoop.hbase.client包。下面是在一个HBase表中创建数据的步骤。</span></p>
<h3><span style="font-size:18px;">第1步：实例化配置类</span></h3>
<h3><span style="font-weight:normal;"><span style="font-size:18px;">使用HbaseConfiguration类的create()方法。</span></span></h3>
<div><span style="font-weight:normal;"><span style="font-size:18px;">Configuration conf = HBaseConfiguration.create();<br></span></span></div>
<h3><span style="font-size:18px;">第2步：实例化HTable类</span></h3>
<p><span style="font-size:18px;">HTable类用于单个HBase表进行通信。在这个类实例接受配置对象和表名作为参数。</span></p>
<p><span style="font-size:18px;"> HTable hTable = new HTable(conf,"People");<br></span></p>
<h3><span style="font-size:18px;">第3步：实例化Put类</span></h3>
<p><span style="font-size:18px;">为了将数据插入到HBase表中，需要使用add()方法和变体。这种方法属于Put类，因此实例化Put类。这个类必须要以字符串格式的列名插入数据。下面dingyi8的列名为1。</span></p>
<p><span style="font-size:18px;"> Put put = new Put(Bytes.toBytes("1"));<br></span></p>
<h3><span style="font-size:18px;">第4步：插入数据</span></h3>
<p><span style="font-size:18px;">Put类的add()方法用于插入数据。参数分别为列族名，列名，以及要插入的值。</span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;">put.add(Bytes.toBytes("personal data"), Bytes.toBytes("name"),Bytes.toBytes("Jim"));<br></span></p>
<h3><span style="font-size:18px;">第5步：保存数据到表中</span></h3>
<p><span style="font-size:18px;">插入所需的行后，HTable类put实例的put()方法添加，如下所示保存更改。</span></p>
<p><span style="font-size:18px;">hTable.put(put);</span></p>
<h3><span style="font-size:18px;">第6步：关闭HTable实例</span></h3>
<p><span style="font-size:18px;">创建在HBase的表数据之后，使用close()方法关闭HTable实例。</span></p>
<p><span style="font-size:18px;">hTable.close();</span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;">具体的代码为</span></p>
<p></p>
<pre><code class="language-java"><span style="font-size:18px;">import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.HTable;
import org.apache.hadoop.hbase.client.Put;
import org.apache.hadoop.hbase.util.Bytes;

import java.io.IOException;

public class InsertData {
    public static void main(String[] args) throws IOException {

        Configuration conf = HBaseConfiguration.create();

        HTable hTable = new HTable(conf,"People");

        Put put = new Put(Bytes.toBytes("1"));

        put.add(Bytes.toBytes("personal data"), Bytes.toBytes("name"),Bytes.toBytes("Jim"));

        hTable.put(put);

        hTable.close();
    }
}</span></code></pre><span style="font-size:18px;"><br></span>
<p></p>
<p><span style="font-size:18px;">更新数据和创建数据类似，只需要改变put.add的参数即可，比如，</span></p>
<p></p>
<pre><code class="language-java"><span style="font-size:18px;">put.add(Bytes.toBytes("name"), Bytes.toBytes("name"),Bytes.toBytes("Tom"));</span></code></pre><span style="font-size:18px;">即可更新表中的值。</span>
<p></p>
            </div>
                </div>