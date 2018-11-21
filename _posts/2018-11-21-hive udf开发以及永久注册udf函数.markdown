---
layout:     post
title:      hive udf开发以及永久注册udf函数
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>网上大部分资料显示注册 hive udf 函数大体有两种方法：</p>
<p>第一种、创建临时函数。如在hive CLI执行下面命令：</p>
<p></p>
<div>
<div align="left"><span style="font-family:'Times New Roman';color:#010101;"><span style="font-size:12pt;">hive&gt; add jar helloudf.jar;</span></span></div>
<div align="left"><span style="font-family:'Times New Roman';color:#010101;"><span style="font-size:12pt;">hive&gt; create temporary function helloworld as 'com.hrj.hive.udf.helloUDF';</span></span></div>
<div align="left"><span style="font-family:'Times New Roman';color:#010101;"><span style="font-size:12pt;">hive&gt; select helloworld(t.col1) from t limit 10;</span></span></div>
<div align="left"><span style="font-family:'Times New Roman';color:#010101;"><span style="font-size:12pt;">hive&gt; drop temporary function helloworld;</span></span></div>
</div>
<p></p>
<p><br></p>
<p>第二种、修改源代码，重编译，注册函数。这种方法可以永久性的保存udf函数，但是风险很大，我刚开始也是使用这种方法，结果悲剧了；hiveserver被搞挂了，最后换了一个节点另外安装hiveserver服务器。至于这种方法的实现网上资料很多。</p>
<p><br></p>
<p>对比分析上面两种方法，你会发现，第一种方法无法满足现实开发需要，因为实际业务中需要一些稳定的公共udf函数；第二种方法虽然满足了第一种的需求，但是风险太大，容易造成hive异常。</p>
<p>于是我查看了hive官方文档，关于udf 函数开发有以下一段话：</p>
<p></p>
<h3 id="LanguageManualDDL-PermanentFunctions">Permanent Functions</h3>
<p></p>
<p>In Hive 0.13 or later, functions can be registered to the metastore, so they can be referenced in a query without having to create a temporary function each session.</p>
<h4 id="LanguageManualDDL-CreateFunction">Create Function</h4>
<div class="aui-message hint shadowed information-macro">
<p class="title">Version information</p>
<span class="aui-icon icon-hint">Icon</span>
<div class="message-content">
<p>As of Hive 0.13.0 (<a href="https://issues.apache.org/jira/browse/HIVE-6047" rel="nofollow" class="external-link">HIVE-6047</a>).</p>
</div>
</div>
<div class="code panel pdl" style="border-width:1px;">
<div class="codeContent panelContent pdl">
<div>
<div id="highlighter_970842" class="syntaxhighlighter nogutter java">
<table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="code">
<div title="Hint: double-click to select code">
<div class="line number1 index0 alt2"><code class="java plain">CREATE FUNCTION [db_name.]function_name AS class_name</code></div>
<div class="line number2 index1 alt1"><code class="java spaces">  </code><code class="java plain">[USING JAR|FILE|ARCHIVE</code><code class="java string">'file_uri'</code>
<code class="java plain">[, JAR|FILE|ARCHIVE</code><code class="java string">'file_uri'</code><code class="java plain">] ];</code></div>
</div>
</td>
</tr></tbody></table></div>
</div>
</div>
</div>
<p>This statement lets you create a function that is implemented by the class_name. Jars, files, or archives which need to be added to the environment can be specified with the USING clause; when the function is referenced for the first time by a Hive session,
 these resources will be added to the environment as if <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli#LanguageManualCli-HiveResources" rel="nofollow">
ADD JAR/FILE</a> had been issued. If Hive is not in local mode, then the resource location must be a non-local URI such as an HDFS location.</p>
<p>The function will be added to the database specified, or to the current database at the time that the function was created. The function can be referenced by fully qualifying the function name (db_name.funciton_name), or can be referenced without qualification
 if the function is in the current database.</p>
<p>上面所述的意思是，hive 0.13版本以后（包过0.13）是支持注册永久函数的，而且提供了注册的方法。注意在上面创建注册udf函数的时候，要注意环境模式，我是用的是hdfs上面的jar包，所以我的代码实现如下：</p>
<p>create function default.url_decode as 'com.richinfo.udf.DecodeURL' using jar 'hdfs://192.168.220.111:21900/user/zjf/udf/URLDecodeHiveUDF.jar';<br><br></p>
<p>创建完以后我注意了下，我如果在另外一个数据库引用url_decode函数需要加上数据库名，因为上面所创建的url_decode函数是隶属于default的。还有就是如果你已经在default中创建了url_decode函数，就不能在其他数据库创建函数名一样的udf函数。<br></p>
<p><br></p>
<p>官网udf 函数开发：https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-CreateFunction<br></p>
<p><br></p>
            </div>
                </div>