---
layout:     post
title:      HBase Admin 新增和修改ColumnFamily
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/dongbeiMan/article/details/51768208				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>HBase 版本： 1.2.1 </p>

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

<span class="hljs-comment">/**
 * 增加和修改columnFamily
 * 
 * @author feng
 *
 */</span>
public class AddAndModifyColumn {

    public static void main(String[] args) {

        String sTalbeName = <span class="hljs-string">"test1"</span><span class="hljs-comment">;</span>
        String columnFamily = <span class="hljs-string">"cfadd-"</span><span class="hljs-comment">;</span>
        String columnFamily1 = <span class="hljs-string">"cf11"</span><span class="hljs-comment">;</span>

        columnFamily = columnFamily + Math<span class="hljs-preprocessor">.random</span>()<span class="hljs-comment">;</span>

        Connection connection = null<span class="hljs-comment">;</span>
        Admin admin = null<span class="hljs-comment">;</span>
        try {
            Configuration config = HBaseConfiguration<span class="hljs-preprocessor">.create</span>()<span class="hljs-comment">;</span>
            config<span class="hljs-preprocessor">.set</span>(<span class="hljs-string">"hbase.zookeeper.quorum"</span>, <span class="hljs-string">"localhost"</span>)<span class="hljs-comment">;</span>
            connection = ConnectionFactory<span class="hljs-preprocessor">.createConnection</span>(config)<span class="hljs-comment">;</span>
            admin = connection<span class="hljs-preprocessor">.getAdmin</span>()<span class="hljs-comment">;</span>

            TableName tableName = TableName<span class="hljs-preprocessor">.valueOf</span>(sTalbeName)<span class="hljs-comment">;</span>

            // 判断是否存在此表
            boolean ifexists = admin<span class="hljs-preprocessor">.tableExists</span>(tableName)<span class="hljs-comment">;</span>

            if (ifexists) {
                // 判断是否可以获取
                if (admin<span class="hljs-preprocessor">.isTableAvailable</span>(tableName)) {

                    if (!admin<span class="hljs-preprocessor">.isTableDisabled</span>(tableName)) {
                        // 如果没关闭则关闭
                        admin<span class="hljs-preprocessor">.disableTable</span>(tableName)<span class="hljs-comment">;</span>
                        System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(tableName + <span class="hljs-string">" disable..."</span>)<span class="hljs-comment">;</span>
                    }

                    HTableDescriptor tableDesc = admin
                            <span class="hljs-preprocessor">.getTableDescriptor</span>(tableName)<span class="hljs-comment">;</span>

                    try {
                        // adding new ColumnFamily
                        HColumnDescriptor cf = new HColumnDescriptor(
                                columnFamily)<span class="hljs-comment">;</span>
                        admin<span class="hljs-preprocessor">.addColumn</span>(tableName, cf)<span class="hljs-comment">;</span>
                        System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(cf<span class="hljs-preprocessor">.getNameAsString</span>()
                                + <span class="hljs-string">" add column success!"</span>)<span class="hljs-comment">;</span>
                    } catch (Exception e) {
                        e<span class="hljs-preprocessor">.printStackTrace</span>()<span class="hljs-comment">;</span>
                    }

                    try {
                        HColumnDescriptor tempColumnDesc = tableDesc
                                <span class="hljs-preprocessor">.getFamily</span>(columnFamily1<span class="hljs-preprocessor">.getBytes</span>())<span class="hljs-comment">;</span>
                        if(tempColumnDesc == null){
                            System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(columnFamily1 + <span class="hljs-string">" is null column "</span>)<span class="hljs-comment">;</span>
                        }else{
                            // modifying existing ColumnFamily
                            HColumnDescriptor cf2 = new HColumnDescriptor(
                                    columnFamily1)<span class="hljs-comment">;</span>
                            admin<span class="hljs-preprocessor">.modifyColumn</span>(tableName, cf2)<span class="hljs-comment">;</span>
                            System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(cf2<span class="hljs-preprocessor">.getNameAsString</span>()
                                    + <span class="hljs-string">"  modify column success!"</span>)<span class="hljs-comment">;</span>
                        }
                    } catch (Exception e) {
                        e<span class="hljs-preprocessor">.printStackTrace</span>()<span class="hljs-comment">;</span>
                    }

                    if (!admin<span class="hljs-preprocessor">.isTableEnabled</span>(tableName)) {
                        // 如果没有打开则打开表
                        admin<span class="hljs-preprocessor">.enableTable</span>(tableName)<span class="hljs-comment">;</span>
                        System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(tableName + <span class="hljs-string">" enable..."</span>)<span class="hljs-comment">;</span>
                    }

                    System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(sTalbeName
                            + <span class="hljs-string">" add And modify column success!"</span>)<span class="hljs-comment">;</span>
                } else {
                    System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(sTalbeName + <span class="hljs-string">" is not available!"</span>)<span class="hljs-comment">;</span>
                }

            } else {
                System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"There is not "</span> + sTalbeName)<span class="hljs-comment">;</span>
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