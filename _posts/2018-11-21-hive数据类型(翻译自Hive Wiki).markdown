---
layout:     post
title:      hive数据类型(翻译自Hive Wiki)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="font-size:13px;line-height:19.5px;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;">
翻译自 <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Types" rel="nofollow" style="color:#000080;">https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Types</a></p>
<h2 style="font-size:18px;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;">
列类型</h2>
<p style="font-size:13px;line-height:19.5px;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;">
Hive支持的数据类型如下:</p>
<p style="font-size:13px;line-height:19.5px;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;">
原生类型:</p>
<ul style="margin-left:45px;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;font-size:13px;line-height:19.5px;"><li style="list-style: !important;"><tt>TINYINT</tt></li><li style="list-style: !important;"><tt>SMALLINT</tt></li><li style="list-style: !important;"><tt>INT</tt></li><li style="list-style: !important;"><tt>BIGINT</tt></li><li style="list-style: !important;"><tt>BOOLEAN</tt></li><li style="list-style: !important;"><tt>FLOAT</tt></li><li style="list-style: !important;"><tt>DOUBLE</tt></li><li style="list-style: !important;"><tt>STRING</tt></li><li style="list-style: !important;"><tt>BINARY</tt> (Hive 0.8.0以上才可用)</li><li style="list-style: !important;"><tt>TIMESTAMP</tt> (Hive 0.8.0以上才可用)</li></ul><p style="font-size:13px;line-height:19.5px;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;">
复合类型:</p>
<ul style="margin-left:45px;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;font-size:13px;line-height:19.5px;"><li style="list-style: !important;">arrays: <tt>ARRAY&lt;data_type&gt;</tt></li><li style="list-style: !important;">maps: <tt>MAP&lt;primitive_type, data_type&gt;</tt></li><li style="list-style: !important;">structs: <tt>STRUCT&lt;col_name : data_type [COMMENT col_comment], ...&gt;</tt></li><li style="list-style: !important;">union: <tt>UNIONTYPE&lt;data_type, data_type, ...&gt;</tt></li></ul><h3 style="font-size:16px;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;">
时间戳</h3>
<p style="font-size:13px;line-height:19.5px;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;">
支持传统的unix时间戳,可选的纳秒级精度。</p>
<p style="font-size:13px;line-height:19.5px;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;">
支持的转换:</p>
<ul style="margin-left:45px;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;font-size:13px;line-height:19.5px;"><li style="list-style: !important;">整型数值类型：解读为以秒为单位的UNIX时间戳</li><li style="list-style: !important;">浮动点数值类型：解读为以秒和小数精度为单位的UNIX时间戳。</li><li style="list-style: !important;">字符串：JDBC兼容的java.sql.Timestamp格式“YYYY-MM-DD HH：MM：SS.fffffffff”（9位小数位精度）</li></ul><p style="font-size:13px;line-height:19.5px;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;">
时间戳被解释是与timezone无关，存储为从UNIX纪元的偏移量。提供便利的UDF和时区转换（to_utc_timestamp，from_utc_timestamp）。<br>
所有现有datetime的UDF（月，日，年，小时，等）可以工作于TIMESTAMP数据类型。</p>
<p style="font-size:13px;line-height:19.5px;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;">
 </p>
<h3 style="font-size:16px;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;">
<a name="LanguageManualTypes-Uniontypes"></a>Union类型</h3>
<p style="font-size:13px;line-height:19.5px;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;">
Union类型可以在同一时间点，保持恰好有一个指定的数据类型。您可以使用create_union的UDF创建一个实例的类型：</p>
<div class="preformatted panel" style="font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;font-size:13px;line-height:19.5px;">
<div class="preformattedContent panelContent">
<div class="cnblogs_code" style="background-color:rgb(245,245,245);border:1px solid rgb(204,204,204);overflow:auto;font-family:'Courier New' !important;font-size:12px !important;">
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy" style="line-height:1.5 !important;"><a title="复制代码" style="color:#000080;border:none !important;"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码" style="border:none !important;"></a></span></div>
<pre style="font-family:'Courier New' !important;">CREATE TABLE union_test(foo UNIONTYPE&lt;int, double, array&lt;string&gt;, struct&lt;a:int,b:string&gt;&gt;);
SELECT foo FROM union_test;

{0:1}
{1:2.0}
{2:["three","four"]}
{3:{"a":5,"b":"five"}}
{2:["six","seven"]}
{3:{"a":8,"b":"eight"}}
{0:9}
{1:10.0}</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy" style="line-height:1.5 !important;"><a title="复制代码" style="color:#000080;border:none !important;"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码" style="border:none !important;"></a></span></div>
</div>
</div>
</div>
<p style="font-size:13px;line-height:19.5px;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;">
第一部分，对于union的发序列化来说是一个tag，让它知道哪个union的部分被使用。在例子0，表明声明的第一个数据类型，即是int，其他如此。</p>
<p style="font-size:13px;line-height:19.5px;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;">
创建union，必须提供为<tt>create_union UDF提供tag。</tt></p>
<div class="preformatted panel" style="font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;font-size:13px;line-height:19.5px;">
<div class="preformattedContent panelContent">
<div class="cnblogs_code" style="background-color:rgb(245,245,245);border:1px solid rgb(204,204,204);overflow:auto;font-family:'Courier New' !important;font-size:12px !important;">
<pre style="font-family:'Courier New' !important;">SELECT create_union(0, key), create_union(if(key&lt;100, 0, 1), 2.0, value), create_union(1, "a", struct(2, "b")) FROM src LIMIT 2;

{0:"238"}    {1:"val_238"}    {1:{"col1":2,"col2":"b"}}
{0:"86"}    {0:2.0}    {1:{"col1":2,"col2":"b"}}</pre>
</div>
</div>
</div>
<h2 style="font-size:18px;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;">
<a name="LanguageManualTypes-Literals"></a>常量</h2>
<h3 style="font-size:16px;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;">
<a name="LanguageManualTypes-Integraltypes"></a>数值类型</h3>
<p style="font-size:13px;line-height:19.5px;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;">
默认，数值类型认为是INT，除非INT超出了INT的范围，那么会被解释为BIGINT,或者是如下的后缀在数值中被使用。</p>
<div class="table-wrap" style="font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;font-size:13px;line-height:19.5px;">
<table class="confluenceTable" style="border:1px solid #C0C0C0;border-collapse:collapse;"><tbody><tr><th class="confluenceTh" style="border:1px solid #C0C0C0;border-collapse:collapse;">
Type</th>
<th class="confluenceTh" style="border:1px solid #C0C0C0;border-collapse:collapse;">
Postfix</th>
<th class="confluenceTh" style="border:1px solid #C0C0C0;border-collapse:collapse;">
Example</th>
</tr><tr><td class="confluenceTd" style="font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;border:1px solid #C0C0C0;border-collapse:collapse;">
TINYINT</td>
<td class="confluenceTd" style="font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;border:1px solid #C0C0C0;border-collapse:collapse;">
Y</td>
<td class="confluenceTd" style="font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;border:1px solid #C0C0C0;border-collapse:collapse;">
100Y</td>
</tr><tr><td class="confluenceTd" style="font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;border:1px solid #C0C0C0;border-collapse:collapse;">
SMALLINT</td>
<td class="confluenceTd" style="font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;border:1px solid #C0C0C0;border-collapse:collapse;">
S</td>
<td class="confluenceTd" style="font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;border:1px solid #C0C0C0;border-collapse:collapse;">
100S</td>
</tr><tr><td class="confluenceTd" style="font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;border:1px solid #C0C0C0;border-collapse:collapse;">
BIGINT</td>
<td class="confluenceTd" style="font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;border:1px solid #C0C0C0;border-collapse:collapse;">
L</td>
<td class="confluenceTd" style="font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;border:1px solid #C0C0C0;border-collapse:collapse;">
100L</td>
</tr></tbody></table></div>
<h3 style="font-size:16px;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;">
<a name="LanguageManualTypes-Stringtypes"></a>字符串类型</h3>
<p style="font-size:13px;line-height:19.5px;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;">
字符串可以用单引号（'）或双引号（“）。Hive在字符串中使用C-Style的转义。</p>
<h3 style="font-size:16px;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;">
<a name="LanguageManualTypes-Floatingpointtypes"></a>浮点类型</h3>
<p style="font-size:13px;line-height:19.5px;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;">
浮点类型数值，被假定为双浮点类型。目前还不支持科学记数法。</p>
<h2 style="font-size:18px;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;">
<a name="LanguageManualTypes-HandlingofNULLValues"></a>NULL值的处理</h2>
<p style="font-size:13px;line-height:19.5px;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;">
遗漏的值被代表为特殊值NULL。 要导入NULL字段的数据，需要查阅使用的SerDe的文档。 （默认的文本格式使用LazySimpleSerDe的解释字符串\ N为NULL）</p>
            </div>
                </div>