---
layout:     post
title:      HBase Admin createTable 创建表
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/dongbeiMan/article/details/51768193				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>HBase 版本： 1.2.1</p>

<pre class="prettyprint"><code class=" hljs avrasm">package <span class="hljs-keyword">com</span><span class="hljs-preprocessor">.feng</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-comment">;</span>

import java<span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.IOException</span><span class="hljs-comment">;</span>

import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.conf</span><span class="hljs-preprocessor">.Configuration</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.HBaseConfiguration</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.HColumnDescriptor</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.HTableDescriptor</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.TableName</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.Admin</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.Connection</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.ConnectionFactory</span><span class="hljs-comment">;</span>

<span class="hljs-comment">/** 参考网页
 * http://www.tutorialspoint.com/hbase/hbase_create_table.htm
 * @author feng
 *
 */</span>
public class CreateTable {

    public static void main(String[] args) {

        String talbeName = <span class="hljs-string">"test1"</span><span class="hljs-comment">;</span>
        String columnFamily = <span class="hljs-string">"cf"</span><span class="hljs-comment">;</span>
        String columnFamily1 = <span class="hljs-string">"cf1"</span><span class="hljs-comment">;</span>

        Connection connection = null<span class="hljs-comment">;</span>
        Admin admin = null<span class="hljs-comment">;</span>

        try {
            Configuration config = HBaseConfiguration<span class="hljs-preprocessor">.create</span>()<span class="hljs-comment">;</span>
            config<span class="hljs-preprocessor">.set</span>(<span class="hljs-string">"hbase.zookeeper.quorum"</span>, <span class="hljs-string">"localhost"</span>)<span class="hljs-comment">;</span>
            connection = ConnectionFactory<span class="hljs-preprocessor">.createConnection</span>(config)<span class="hljs-comment">;</span>
            admin = connection<span class="hljs-preprocessor">.getAdmin</span>()<span class="hljs-comment">;</span>

            boolean ifexists = admin<span class="hljs-preprocessor">.tableExists</span>(TableName<span class="hljs-preprocessor">.valueOf</span>(talbeName))<span class="hljs-comment">;</span>

            if (!ifexists) {
                // 不存在则创建
                HTableDescriptor tableDesc = new HTableDescriptor(
                        TableName<span class="hljs-preprocessor">.valueOf</span>(talbeName))<span class="hljs-comment">;</span>
                HColumnDescriptor columnDesc = new HColumnDescriptor(
                        columnFamily)<span class="hljs-comment">;</span>
                HColumnDescriptor columnDesc1 = new HColumnDescriptor(
                        columnFamily1)<span class="hljs-comment">;</span>
                tableDesc<span class="hljs-preprocessor">.addFamily</span>(columnDesc)<span class="hljs-comment">;</span>
                tableDesc<span class="hljs-preprocessor">.addFamily</span>(columnDesc1)<span class="hljs-comment">;</span>

                admin<span class="hljs-preprocessor">.createTable</span>(tableDesc)<span class="hljs-comment">;</span>
                System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(talbeName + <span class="hljs-string">" create success!"</span>)<span class="hljs-comment">;</span>
            } else {
                System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(talbeName + <span class="hljs-string">" is exists:"</span> + ifexists)<span class="hljs-comment">;</span>
            }

        } catch (Exception e) {
            e<span class="hljs-preprocessor">.printStackTrace</span>()<span class="hljs-comment">;</span>
        } finally {
            if (admin != null) {
                try {
                    admin<span class="hljs-preprocessor">.close</span>()<span class="hljs-comment">;</span>
                } catch (IOException e) {
                    e<span class="hljs-preprocessor">.printStackTrace</span>()<span class="hljs-comment">;</span>
                }
            }
            if (connection != null) {
                try {
                    connection<span class="hljs-preprocessor">.close</span>()<span class="hljs-comment">;</span>
                } catch (IOException e) {
                    e<span class="hljs-preprocessor">.printStackTrace</span>()<span class="hljs-comment">;</span>
                }
            }
        }

    }

}</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>