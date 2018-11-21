---
layout:     post
title:      hbase学习--1 使用 java api 创建表
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/wslyk606/article/details/78428688				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:18px;">项目中用到的数据库时hbase，因此学习一下hbase的基本操作。</span></p>
<p><span style="font-size:18px;">1  hbase是使用java编写的，使用java  api很对hbase进行操作，首先创建一个hbase表。</span></p>
<p><span style="font-size:18px;">我的java工程使用maven管理的，hbase对应的maven依赖为</span></p>
<p></p>
<pre><code class="language-html"><span style="font-size:18px;">        &lt;dependency&gt;
            &lt;groupId&gt;org.apache.hadoop&lt;/groupId&gt;
            &lt;artifactId&gt;hadoop-hdfs&lt;/artifactId&gt;
            &lt;version&gt;2.2.0&lt;/version&gt;
        &lt;/dependency&gt;
        &lt;dependency&gt;
            &lt;groupId&gt;org.apache.hbase&lt;/groupId&gt;
            &lt;artifactId&gt;hbase-common&lt;/artifactId&gt;
            &lt;version&gt;1.2.6&lt;/version&gt;
        &lt;/dependency&gt;
        &lt;dependency&gt;
            &lt;groupId&gt;org.apache.hbase&lt;/groupId&gt;
            &lt;artifactId&gt;hbase-client&lt;/artifactId&gt;
            &lt;version&gt;1.2.6&lt;/version&gt;
        &lt;/dependency&gt;</span></code></pre><span style="font-size:18px;"><br>
注意hbase的版本和maven中的版本对应，否则会出现异常。</span>
<p></p>
<p><span style="font-size:18px;"><span style="color:rgb(51,51,68);font-family:'Helvetica Neue', Helvetica, 'PingFang SC', '微软雅黑', Tahoma, Arial, sans-serif;">使用HBaseAdmin类的createTable()方法来创建表，</span>下面是创建表的代码</span></p>
<p></p>
<pre><code class="language-java"><span style="font-size:18px;">import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.HColumnDescriptor;
import org.apache.hadoop.hbase.HTableDescriptor;
import org.apache.hadoop.hbase.TableName;
import org.apache.hadoop.hbase.client.HBaseAdmin;

import java.io.IOException;

public class CreateTable {
    public static void main(String[] args) throws IOException {</span></code></pre><pre><code class="language-java"><span style="font-size:18px;">
</span></code></pre><pre><code class="language-java"><span style="font-size:18px;">        //第一步：实例化HbaseAdmin
        Configuration conf = HBaseConfiguration.create();
<span>	</span>HBaseAdmin admin = new HBaseAdmin(conf);

        //第二步：添加表描述 </span></code></pre><pre><code class="language-java"><span style="font-size:18px;"><span>	</span>HTableDescriptor tableDescriptor = new HTableDescriptor(TableName.valueOf("People"));

        tableDescriptor.addFamily(new HColumnDescriptor("personal data"));
        tableDescriptor.addFamily(new HColumnDescriptor(("professional data")));
<span>
</span></span></code></pre><pre><code class="language-java"><span style="font-size:18px;"><span>	</span>//第三步：创建表
        admin.createTable(tableDescriptor);
        System.out.println("Table created");
    }
}</span></code></pre><pre><code class="language-java"></code></pre><span style="font-family:monospace;"><span style="font-size:18px;"><span></span></span></span><p></p><p></p><p style="color:rgb(51,51,68);font-family:'Helvetica Neue', Helvetica, 'PingFang SC', '微软雅黑', Tahoma, Arial, sans-serif;"><span style="font-size:18px;">对应的HBase shell命令来创建表。</span></p><pre class="prettyprint notranslate prettyprinted" style="line-height:16px;background-color:rgb(245,245,245);width:496.188px;overflow:auto;"><span style="font-size:18px;"><span class="pln" style="color:rgb(0,0,0);">create </span><span class="str" style="color:rgb(0,136,0);">'People'</span><span class="pun" style="color:rgb(102,102,0);">,</span><span class="pln" style="color:rgb(0,0,0);"> </span><span class="str" style="color:rgb(0,136,0);">'personal data'</span><span class="pun" style="color:rgb(102,102,0);">,</span><span class="pln" style="color:rgb(0,0,0);"> </span><span class="pln"><span style="color:#666600;">'</span><span style="color:#009900;">professional data</span><span style="color:#666600;">'</span></span><span style="color:rgb(49,49,49);">
</span></span></pre><span style="font-size:18px;"><br></span>
<p><span style="font-size:18px;"> </span></p>
            </div>
                </div>