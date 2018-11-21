---
layout:     post
title:      第46课：Spark中的新解析引擎Catalyst源码初探
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：王家林大咖2018年新书《SPARK大数据商业实战三部曲》清华大学出版，清华大学出版社官方旗舰店（天猫）https://qhdx.tmall.com/?spm=a220o.1000855.1997427721.d4918089.4b2a2e5dT6bUsM					https://blog.csdn.net/duan_zhihua/article/details/53385083				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>第46课：Spark中的新解析引擎Catalyst源码初探</p>
<p><br></p>
<p>spark 1.6.0 与spark 2.0.2 中sql的版本迭代更新， 刚看一个开头，SQLContext 就不同了。sparksession闪亮登场</p>
<p><br></p>
<p>但以后的学习仍基于spark 1.6.0 学习。</p>
<p><br></p>
<p><span style="font-size:18px;color:#ff0000;">spark 1.6.0 </span><br></p>
<p></p>
<pre style="font-family:'宋体';font-size:13.5pt;background-color:rgb(255,255,255);"><span style="color:#808080;"><em>/**
</em></span><span style="color:#808080;"><em> * The entry point for working with structured data (rows and columns) in Spark.  Allows the
</em></span><span style="color:#808080;"><em> * creation of </em></span><span style="background-color:#e2ffe2;">[[</span><span style="color:#808080;"><em>DataFrame</em></span><span style="background-color:#e2ffe2;">]]</span> <span style="color:#808080;"><em>objects as well as the execution of SQL queries.
</em></span><span style="color:#808080;"><em> *
</em></span><span style="color:#808080;"><em> * </em></span><span style="font-weight:bold;">@groupname </span><span style="color:#808080;"><em>basic Basic Operations
</em></span><span style="color:#808080;"><em> * </em></span><span style="font-weight:bold;">@groupname </span><span style="color:#808080;"><em>ddl_ops Persistent Catalog DDL
</em></span><span style="color:#808080;"><em> * </em></span><span style="font-weight:bold;">@groupname </span><span style="color:#808080;"><em>cachemgmt Cached Table Management
</em></span><span style="color:#808080;"><em> * </em></span><span style="font-weight:bold;">@groupname </span><span style="color:#808080;"><em>genericdata Generic Data Sources
</em></span><span style="color:#808080;"><em> * </em></span><span style="font-weight:bold;">@groupname </span><span style="color:#808080;"><em>specificdata Specific Data Sources
</em></span><span style="color:#808080;"><em> * </em></span><span style="font-weight:bold;">@groupname </span><span style="color:#808080;"><em>config Configuration
</em></span><span style="color:#808080;"><em> * </em></span><span style="font-weight:bold;">@groupname </span><span style="color:#808080;"><em>dataframes Custom DataFrame Creation
</em></span><span style="color:#808080;"><em> * </em></span><span style="font-weight:bold;">@groupname </span><span style="color:#808080;"><em>Ungrouped Support functions for language integrated queries
</em></span><span style="color:#808080;"><em> *
</em></span><span style="color:#808080;"><em> * </em></span><span style="font-weight:bold;">@since </span><span style="color:#808080;"><em>1.0.0
</em></span><span style="color:#808080;"><em> */
</em></span><span style="color:#000080;"><strong>class </strong></span>SQLContext <span style="color:#000080;"><strong>private</strong></span>[sql](
    <span style="color:#808000;">@transient </span><span style="color:#000080;"><strong>val </strong></span>sparkContext: SparkContext,
    <span style="color:#808000;">@transient </span><span style="color:#000080;"><strong>protected</strong></span>[sql] <span style="color:#000080;"><strong>val </strong></span>cacheManager: CacheManager,
    <span style="color:#808000;">@transient </span><span style="color:#000080;"><strong>private</strong></span>[sql] <span style="color:#000080;"><strong>val </strong></span>listener: SQLListener,
    <span style="color:#000080;"><strong>val </strong></span>isRootContext: Boolean)
  <span style="color:#000080;"><strong>extends </strong></span>org.apache.spark.Logging <span style="color:#000080;"><strong>with </strong></span>Serializable {
</pre>
<br><p></p>
<p><br></p>
<p><strong><span style="font-size:18px;color:#ff0000;">spark 2.0.2</span></strong><br></p>
<p></p>
<pre style="font-family:'宋体';font-size:13.5pt;background-color:rgb(255,255,255);"><span style="color:#808080;"><em>/**
</em></span><span style="color:#808080;"><em> * The entry point for working with structured data (rows and columns) in Spark 1.x.
</em></span><span style="color:#808080;"><em> *
</em></span><span style="color:#808080;"><em> * As of Spark 2.0, this is replaced by </em></span><span style="background-color:#e2ffe2;">[[</span><span style="color:#808080;"><em>SparkSession</em></span><span style="background-color:#e2ffe2;">]]</span><span style="color:#808080;"><em>. However, we are keeping the class
</em></span><span style="color:#808080;"><em> * here for backward compatibility.
</em></span><span style="color:#808080;"><em> *
</em></span><span style="color:#808080;"><em> * </em></span><span style="font-weight:bold;">@groupname </span><span style="color:#808080;"><em>basic Basic Operations
</em></span><span style="color:#808080;"><em> * </em></span><span style="font-weight:bold;">@groupname </span><span style="color:#808080;"><em>ddl_ops Persistent Catalog DDL
</em></span><span style="color:#808080;"><em> * </em></span><span style="font-weight:bold;">@groupname </span><span style="color:#808080;"><em>cachemgmt Cached Table Management
</em></span><span style="color:#808080;"><em> * </em></span><span style="font-weight:bold;">@groupname </span><span style="color:#808080;"><em>genericdata Generic Data Sources
</em></span><span style="color:#808080;"><em> * </em></span><span style="font-weight:bold;">@groupname </span><span style="color:#808080;"><em>specificdata Specific Data Sources
</em></span><span style="color:#808080;"><em> * </em></span><span style="font-weight:bold;">@groupname </span><span style="color:#808080;"><em>config Configuration
</em></span><span style="color:#808080;"><em> * </em></span><span style="font-weight:bold;">@groupname </span><span style="color:#808080;"><em>dataframes Custom DataFrame Creation
</em></span><span style="color:#808080;"><em> * </em></span><span style="font-weight:bold;">@groupname </span><span style="color:#808080;"><em>dataset Custom Dataset Creation
</em></span><span style="color:#808080;"><em> * </em></span><span style="font-weight:bold;">@groupname </span><span style="color:#808080;"><em>Ungrouped Support functions for language integrated queries
</em></span><span style="color:#808080;"><em> * </em></span><span style="font-weight:bold;">@since </span><span style="color:#808080;"><em>1.0.0
</em></span><span style="color:#808080;"><em> */
</em></span><span style="color:#000080;"><strong>class </strong></span>SQLContext <span style="color:#000080;"><strong>private</strong></span>[sql](<span style="color:#000080;"><strong>val </strong></span>sparkSession: SparkSession)
  <span style="color:#000080;"><strong>extends </strong></span>Logging <span style="color:#000080;"><strong>with </strong></span>Serializable {
</pre>
<br><p></p>
<p><br></p>
<p><br></p>
<pre style="font-family:'宋体';font-size:13.5pt;background-color:rgb(255,255,255);"></pre>
<p></p>
            </div>
                </div>