---
layout:     post
title:      【Hive自定义UDF函数】  与  【hive字符串函数】
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div style="text-align:center;"><span style="font-size:24px;">【Hive自定义UDF函数】  与  【hive字符串函数】</span></div>
<div style="text-align:justify;"><span style="font-size:24px;"></span>
<div id="cnblogs_post_body">
<div>
<div style="line-height:160%;font-size:14px;">
<p>Hive进行UDF开发十分简单，此处所说UDF为Temporary的function，所以需要hive版本在0.4.0以上才可以。</p>
<p>Hive的UDF开发只需要重构UDF类的evaluate函数即可。例：</p>
<p>package com.hrj.hive.udf;</p>
<p>import org.apache.hadoop.hive.ql.exec.UDF;</p>
<p>public class helloUDF extends UDF {</p>
<p>    public String evaluate(String str) {</p>
<p>        try {</p>
<p>            return "HelloWorld " + str;</p>
<p>        } catch (Exception e) {</p>
<p>            return null;</p>
<p>        }</p>
<p>    }</p>
<p>} </p>
<p> </p>
<p>将该java文件编译成helloudf.jar</p>
<p>hive&gt; add jar helloudf.jar;</p>
<p>hive&gt; create temporary function helloworld as 'com.hrj.hive.udf.helloUDF';</p>
<p>hive&gt; select helloworld(t.col1) from t limit 10;</p>
<p>hive&gt; drop temporary function helloworld;</p>
<p> </p>
<p>注：</p>
<p>1.helloworld为临时的函数，所以每次进入hive都需要add jar以及create temporary操作</p>
<p>2.UDF只能实现一进一出的操作，如果需要实现多进一出，则需要实现UDAF</p>
</div>
</div>
</div>
<br></div>
<div style="text-align:justify;"><span style="font-size:24px;"><br></span></div>
<div style="text-align:justify;">

<br></div>
<div style="text-align:justify;"><span style="font-size:24px;"><span style="color:#000;font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-family:Arial;"><span style="font-weight:700;"><span style="color:rgb(85,85,85);"><span style="font-family:'宋体';"><span style="font-size:12pt;"><br></span></span></span></span></span></span></span></div>
<div style="text-align:justify;"><span style="font-size:24px;"><span style="font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-family:Arial;"><span style="font-weight:700;"><span><span style="font-family:'宋体';"><span style="font-size:12pt;"><span style="color:#ff0000;background-color:rgb(255,255,255);">字符串函数<br>
字符串长度函数：length<br>
字符串反转函数：reverse<br>
字符串连接函数：concat<br>
带分隔符字符串连接函数：concat_ws<br>
字符串截取函数：substr,substring<br>
字符串截取函数：substr,substring<br>
字符串转大写函数：upper,ucase<br>
字符串转小写函数：lower,lcase<br>
去空格函数：trim<br>
左边去空格函数：ltrim<br>
右边去空格函数：rtrim<br>
正则表达式替换函数：regexp_replace<br>
正则表达式解析函数：regexp_extract<br>
URL解析函数：parse_url<br>
json解析函数：get_json_object<br>
空格字符串函数：space<br>
重复字符串函数：repeat<br>
首字符ascii函数：ascii<br>
左补足函数：lpad<br>
右补足函数：rpad<br>
分割字符串函数: split<br>
集合查找函数: find_in_set</span><br></span></span></span></span></span></span></span></div>
<div style="text-align:justify;"><span style="font-size:24px;"><span style="color:#000;font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-family:Arial;"><span style="font-weight:700;"><span style="color:rgb(85,85,85);"><span style="font-family:'宋体';"><span style="font-size:12pt;"><br></span></span></span></span></span></span></span></div>
<div style="text-align:justify;"><span style="font-size:24px;"><span style="color:#000;font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-family:Arial;"><span style="font-weight:700;"><span style="color:rgb(85,85,85);"><span style="font-family:'宋体';"><span style="font-size:12pt;">字符串函数</span></span></span></span></span></span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(85,85,85);"><span style="font-family:'宋体';">字符串长度函数：</span></span>length</span></span>
<table cellspacing="0" class="t_table" style="border-collapse:collapse;border:1px solid rgb(227,237,245);table-layout:auto;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><tbody><tr><td width="568" style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
<div align="left">语法: length(string A)</div>
<div align="left">返回值: int</div>
<div align="left">说明：返回字符串A的长度</div>
<div align="left">举例：</div>
<div align="left">hive&gt; select length(‘abcedfg’) from dual;</div>
<div align="left">7</div>
</td>
</tr></tbody></table><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(85,85,85);"><span style="font-family:'宋体';">字符串反转函数：</span></span>reverse</span>
<table cellspacing="0" class="t_table" style="border-collapse:collapse;border:1px solid rgb(227,237,245);table-layout:auto;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><tbody><tr><td width="568" style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
<div align="left">语法: reverse(string A)</div>
<div align="left">返回值: string</div>
<div align="left">说明：返回字符串A的反转结果</div>
<div align="left">举例：</div>
<div align="left">hive&gt; select reverse(‘abcedfg’) from dual;</div>
<div align="left">gfdecba</div>
</td>
</tr></tbody></table><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(85,85,85);"><span style="font-family:'宋体';">字符串连接函数：</span></span>concat</span>
<table cellspacing="0" class="t_table" style="border-collapse:collapse;border:1px solid rgb(227,237,245);table-layout:auto;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><tbody><tr><td width="568" style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
<div align="left">语法: concat(string A, string B…)</div>
<div align="left">返回值: string</div>
<div align="left">说明：返回输入字符串连接后的结果，支持任意个输入字符串</div>
<div align="left">举例：</div>
<div align="left">hive&gt; select concat(‘abc’,'def’,'gh’) from dual;</div>
<div align="left">abcdefgh</div>
</td>
</tr></tbody></table><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(85,85,85);"><span style="font-family:'宋体';">带分隔符字符串连接函数：</span></span>concat_ws</span>
<table cellspacing="0" class="t_table" style="border-collapse:collapse;border:1px solid rgb(227,237,245);table-layout:auto;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><tbody><tr><td width="568" style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
<div align="left">语法: concat_ws(string SEP, string A, string B…)</div>
<div align="left">返回值: string</div>
<div align="left">说明：返回输入字符串连接后的结果，SEP表示各个字符串间的分隔符</div>
<div align="left">举例：</div>
<div align="left">hive&gt; select concat_ws(‘,’,'abc’,'def’,'gh’) from dual;</div>
<div align="left">abc,def,gh</div>
</td>
</tr></tbody></table><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(85,85,85);"><span style="font-family:'宋体';">字符串截取函数：</span></span>substr,substring</span>
<table cellspacing="0" class="t_table" style="border-collapse:collapse;border:1px solid rgb(227,237,245);table-layout:auto;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><tbody><tr><td width="568" style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
<div align="left">语法: substr(string A, int start),substring(string A, int start)</div>
<div align="left">返回值: string</div>
<div align="left">说明：返回字符串A从start位置到结尾的字符串</div>
<div align="left">举例：</div>
<div align="left">hive&gt; select substr(‘abcde’,3) from dual;</div>
<div align="left">cde</div>
<div align="left">hive&gt; select substring(‘abcde’,3) from dual;</div>
<div align="left">cde</div>
<div align="left">hive&gt;  select substr(‘abcde’,-1) from dual;  （和ORACLE相同）</div>
<div align="left">e</div>
</td>
</tr></tbody></table><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(85,85,85);"><span style="font-family:'宋体';">字符串截取函数：</span></span>substr,substring</span>
<table cellspacing="0" class="t_table" style="border-collapse:collapse;border:1px solid rgb(227,237,245);table-layout:auto;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><tbody><tr><td width="568" style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
<div align="left">语法: substr(string A, int start, int len),substring(string A, int start, int len)</div>
<div align="left">返回值: string</div>
<div align="left">说明：返回字符串A从start位置开始，长度为len的字符串</div>
<div align="left">举例：</div>
<div align="left">hive&gt; select substr(‘abcde’,3,2) from dual;</div>
<div align="left">cd</div>
<div align="left">hive&gt; select substring(‘abcde’,3,2) from dual;</div>
<div align="left">cd</div>
<div align="left">hive&gt;select substring(‘abcde’,-2,2) from dual;</div>
<div align="left">de</div>
</td>
</tr></tbody></table><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(85,85,85);"><span style="font-family:'宋体';">字符串转大写函数：</span></span>upper,ucase</span>
<table cellspacing="0" class="t_table" style="border-collapse:collapse;border:1px solid rgb(227,237,245);table-layout:auto;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><tbody><tr><td width="568" style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
<div align="left">语法: upper(string A) ucase(string A)</div>
<div align="left">返回值: string</div>
<div align="left">说明：返回字符串A的大写格式</div>
<div align="left">举例：</div>
<div align="left">hive&gt; select upper(‘abSEd’) from dual;</div>
<div align="left">ABSED</div>
<div align="left">hive&gt; select ucase(‘abSEd’) from dual;</div>
<div align="left">ABSED</div>
</td>
</tr></tbody></table><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(85,85,85);"><span style="font-family:'宋体';">字符串转小写函数：</span></span>lower,lcase</span>
<table cellspacing="0" class="t_table" style="border-collapse:collapse;border:1px solid rgb(227,237,245);table-layout:auto;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><tbody><tr><td width="568" style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
<div align="left">语法: lower(string A) lcase(string A)</div>
<div align="left">返回值: string</div>
<div align="left">说明：返回字符串A的小写格式</div>
<div align="left">举例：</div>
<div align="left">hive&gt; select lower(‘abSEd’) from dual;</div>
<div align="left">absed</div>
<div align="left">hive&gt; select lcase(‘abSEd’) from dual;</div>
<div align="left">absed</div>
</td>
</tr></tbody></table><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(85,85,85);"><span style="font-family:'宋体';">去空格函数：</span></span>trim</span>
<table cellspacing="0" class="t_table" style="border-collapse:collapse;border:1px solid rgb(227,237,245);table-layout:auto;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><tbody><tr><td width="568" style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
<div align="left">语法: trim(string A)</div>
<div align="left">返回值: string</div>
<div align="left">说明：去除字符串两边的空格</div>
<div align="left">举例：</div>
<div align="left">hive&gt; select trim(‘ abc ‘) from dual;</div>
<div align="left">abc</div>
</td>
</tr></tbody></table><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(85,85,85);"><span style="font-family:'宋体';">左边去空格函数：</span></span>ltrim</span>
<table cellspacing="0" class="t_table" style="border-collapse:collapse;border:1px solid rgb(227,237,245);table-layout:auto;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><tbody><tr><td width="568" style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
<div align="left">语法: ltrim(string A)</div>
<div align="left">返回值: string</div>
<div align="left">说明：去除字符串左边的空格</div>
<div align="left">举例：</div>
<div align="left">hive&gt; select ltrim(‘ abc ‘) from dual;</div>
<div align="left">abc</div>
</td>
</tr></tbody></table><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(85,85,85);"><span style="font-family:'宋体';">右边去空格函数：</span></span>rtrim</span>
<table cellspacing="0" class="t_table" style="border-collapse:collapse;border:1px solid rgb(227,237,245);table-layout:auto;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><tbody><tr><td width="568" style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
<div align="left">语法: rtrim(string A)</div>
<div align="left">返回值: string</div>
<div align="left">说明：去除字符串右边的空格</div>
<div align="left">举例：</div>
<div align="left">hive&gt; select rtrim(‘ abc ‘) from dual;</div>
<div align="left">abc</div>
</td>
</tr></tbody></table><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(85,85,85);"><span style="font-family:'宋体';">正则表达式替换函数：</span></span>regexp_replace</span>
<table cellspacing="0" class="t_table" style="border-collapse:collapse;border:1px solid rgb(227,237,245);table-layout:auto;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><tbody><tr><td width="568" style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
<div align="left">语法: regexp_replace(string A, string B, string C)</div>
<div align="left">返回值: string</div>
<div align="left">说明：将字符串A中的符合java正则表达式B的部分替换为C。注意，在有些情况下要使用转义字符</div>
<div align="left">举例：</div>
<div align="left">hive&gt; select regexp_replace(‘foobar’, ‘oo|ar’, ”) from dual;</div>
<div align="left">fb</div>
</td>
</tr></tbody></table><span style="color:#000;font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-family:Arial;"><br><span style="color:#FF0000;"><span style="font-family:Verdana, sans-serif;"><span style="font-size:10.5pt;">(mysql</span></span></span><span style="color:#FF0000;"><span style="font-family:'宋体';"><span style="font-size:10.5pt;">不支持，请用</span></span></span><span style="color:#FF0000;"><span style="font-family:Verdana, sans-serif;"><span style="font-size:10.5pt;">hive</span></span></span><span style="color:#FF0000;"><span style="font-family:'宋体';"><span style="font-size:10.5pt;">测试，请先建一个伪表</span></span></span><span style="color:#FF0000;"><span style="font-family:Verdana, sans-serif;"><span style="font-size:10.5pt;">dual)</span></span></span></span></span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(85,85,85);"><span style="font-family:'宋体';">正则表达式解析函数：</span></span>regexp_extract</span>
<table cellspacing="0" class="t_table" style="border-collapse:collapse;border:1px solid rgb(227,237,245);table-layout:auto;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><tbody><tr><td width="568" style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
<div align="left">语法: regexp_extract(string subject, string pattern, int index)</div>
<div align="left">返回值: string</div>
<div align="left">说明：将字符串subject按照pattern正则表达式的规则拆分，返回index指定的字符。注意，在有些情况下要使用转义字符<br></div>
<div align="left">X?   X，一次或一次也没有<br>
X*    X，零次或多次<br>
X+    X，一次或多次<br>
X{n}    X，恰好 n 次<br>
X{n,}    X，至少 n 次<br>
X{n,m}    X，至少 n 次，但是不超过 m 次</div>
<div align="left">举例：</div>
<div align="left">hive&gt; select regexp_extract(‘foothebar’, ‘foo(.*?)(bar)’, 1) from dual;</div>
<div align="left">the</div>
<div align="left">hive&gt; select regexp_extract(‘foothebar’, ‘foo(.*?)(bar)’, 2) from dual;</div>
<div align="left">bar</div>
<div align="left">hive&gt; select regexp_extract(‘foothebar’, ‘foo(.*?)(bar)’, 0) from dual;</div>
<div align="left">foothebar<br>
hive&gt;select regexp_extract('userid=13333333', '.*=(.*)', 1) from dual; <br>
(说明=不是特殊字符)</div>
<div align="left">13333333</div>
<div align="left">hive&gt;select regexp_extract('userid.13333333', '.*\\.(.*)', 1) from dual; <br>
(\\如果是特殊字符请用，例如：. * (  )  ) <br>
13333333</div>
</td>
</tr></tbody></table><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">URL<span style="color:rgb(85,85,85);"><span style="font-family:'宋体';">解析函数：</span></span>parse_url</span>
<table cellspacing="0" class="t_table" style="border-collapse:collapse;border:1px solid rgb(227,237,245);table-layout:auto;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><tbody><tr><td width="568" style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
<div align="left">语法: parse_url(string urlString, string partToExtract [, string keyToExtract])</div>
<div align="left">返回值: string</div>
<div align="left">说明：返回URL中指定的部分。partToExtract的有效值为：HOST, PATH, QUERY, REF, PROTOCOL, AUTHORITY, FILE, and USERINFO.</div>
<div align="left">举例：</div>
<div align="left">hive&gt; select parse_url(‘<a href="http://facebook.com/path1/p.php?k1=v1&amp;k2=v2#Ref1" rel="nofollow">http://facebook.com/path1/p.php?k1=v1&amp;k2=v2#Ref1</a>′, ‘HOST’) from dual;</div>
<div align="left">facebook.com</div>
<div align="left">hive&gt; select parse_url(‘<a href="http://facebook.com/path1/p.php?k1=v1&amp;k2=v2#Ref1" rel="nofollow">http://facebook.com/path1/p.php?k1=v1&amp;k2=v2#Ref1</a>′, ‘QUERY’, ‘k1′) from dual;</div>
<div align="left">v1</div>
</td>
</tr></tbody></table><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">json<span style="color:rgb(85,85,85);"><span style="font-family:'宋体';">解析函数：</span></span>get_json_object</span>
<table cellspacing="0" class="t_table" style="border-collapse:collapse;border:1px solid rgb(227,237,245);table-layout:auto;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><tbody><tr><td width="568" style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
<div align="left">语法: get_json_object(string json_string, string path)</div>
<div align="left">返回值: string</div>
<div align="left">说明：解析json的字符串json_string,返回path指定的内容。如果输入的json字符串无效，那么返回NULL。</div>
<div align="left">举例：</div>
<div align="left">hive&gt; select  get_json_object(‘{“store”:</div>
<div align="left">&gt;   {“fruit”:\[{"weight":8,"type":"apple"},{"weight":9,"type":"pear"}],</div>
<div align="left">&gt;    “bicycle”:{“price”:19.95,”color”:”red”}</div>
<div align="left">&gt;   },</div>
<div align="left">&gt;  “email”:”<a href="mailto:amy@only_for_json_udf_test.net" rel="nofollow">amy@only_for_json_udf_test.net</a>”,</div>
<div align="left">&gt;  “owner”:”amy”</div>
<div align="left">&gt; }</div>
<div align="left">&gt; ‘,’$.owner’) from dual;</div>
<div align="left">amy</div>
</td>
</tr></tbody></table><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(85,85,85);"><span style="font-family:'宋体';">空格字符串函数：</span></span>space</span>
<table cellspacing="0" class="t_table" style="border-collapse:collapse;border:1px solid rgb(227,237,245);table-layout:auto;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><tbody><tr><td width="568" style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
<div align="left">语法: space(int n)</div>
<div align="left">返回值: string</div>
<div align="left">说明：返回长度为n的字符串</div>
<div align="left">举例：</div>
<div align="left">hive&gt; select space(10) from dual;</div>
<div align="left">hive&gt; select length(space(10)) from dual;</div>
<div align="left">10</div>
</td>
</tr></tbody></table><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(85,85,85);"><span style="font-family:'宋体';">重复字符串函数：</span></span>repeat</span>
<table cellspacing="0" class="t_table" style="border-collapse:collapse;border:1px solid rgb(227,237,245);table-layout:auto;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><tbody><tr><td width="568" style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
<div align="left">语法: repeat(string str, int n)</div>
<div align="left">返回值: string</div>
<div align="left">说明：返回重复n次后的str字符串</div>
<div align="left">举例：</div>
<div align="left">hive&gt; select repeat(‘abc’,5) from dual;</div>
<div align="left">abcabcabcabcabc</div>
</td>
</tr></tbody></table><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(85,85,85);"><span style="font-family:'宋体';">首字符</span></span>ascii<span style="color:rgb(85,85,85);"><span style="font-family:'宋体';">函数：</span></span>ascii</span>
<table cellspacing="0" class="t_table" style="border-collapse:collapse;border:1px solid rgb(227,237,245);table-layout:auto;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><tbody><tr><td width="568" style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
<div align="left">语法: ascii(string str)</div>
<div align="left">返回值: int</div>
<div align="left">说明：返回字符串str第一个字符的ascii码</div>
<div align="left">举例：</div>
<div align="left">hive&gt; select ascii(‘abcde’) from dual;</div>
<div align="left">97</div>
</td>
</tr></tbody></table><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(85,85,85);"><span style="font-family:'宋体';">左补足函数：</span></span>lpad</span>
<table cellspacing="0" class="t_table" style="border-collapse:collapse;border:1px solid rgb(227,237,245);table-layout:auto;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><tbody><tr><td width="568" style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
<div align="left">语法: lpad(string str, int len, string pad)</div>
<div align="left">返回值: string</div>
<div align="left">说明：将str进行用pad进行左补足到len位</div>
<div align="left">举例：</div>
<div align="left">hive&gt; select lpad(‘abc’,10,’td’) from dual;</div>
<div align="left">tdtdtdtabc</div>
<div align="left">与GP，ORACLE不同，pad 不能默认</div>
</td>
</tr></tbody></table><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(85,85,85);"><span style="font-family:'宋体';">右补足函数：</span></span>rpad</span>
<table cellspacing="0" class="t_table" style="border-collapse:collapse;border:1px solid rgb(227,237,245);table-layout:auto;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><tbody><tr><td width="568" style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
<div align="left">语法: rpad(string str, int len, string pad)</div>
<div align="left">返回值: string</div>
<div align="left">说明：将str进行用pad进行右补足到len位</div>
<div align="left">举例：</div>
<div align="left">hive&gt; select rpad(‘abc’,10,’td’) from dual;</div>
<div align="left">abctdtdtdt</div>
</td>
</tr></tbody></table><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(85,85,85);"><span style="font-family:'宋体';">分割字符串函数</span></span>:
 split</span>
<table cellspacing="0" class="t_table" style="border-collapse:collapse;border:1px solid rgb(227,237,245);table-layout:auto;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><tbody><tr><td width="568" style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
<div align="left">语法:  split(string str, string pat)</div>
<div align="left">返回值:  array</div>
<div align="left">说明: 按照pat字符串分割str，会返回分割后的字符串数组</div>
<div align="left">举例：</div>
<div align="left">hive&gt; select split(‘abtcdtef’,'t’) from dual;</div>
<div align="left">["ab","cd","ef"]</div>
</td>
</tr></tbody></table><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(85,85,85);"><span style="font-family:'宋体';">集合查找函数</span></span>:
 find_in_set</span>
<table cellspacing="0" class="t_table" style="border-collapse:collapse;border:1px solid rgb(227,237,245);table-layout:auto;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><tbody><tr><td width="553" style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
<div align="left">语法: find_in_set(string str, string strList)</div>
<div align="left">返回值: int</div>
<div align="left">说明: 返回str在strlist第一次出现的位置，strlist是用逗号分割的字符串。如果没有找该str字符，则返回0（只能是逗号分隔，不然返回0）</div>
<div align="left">举例：</div>
<div align="left">hive&gt; select find_in_set(‘ab’,'ef,ab,de’) from dual;</div>
<div align="left">2</div>
<div align="left">hive&gt; select find_in_set(‘at’,'ef,ab,de’) from dual;</div>
<div align="left">0</div>
</td>
</tr></tbody></table><br></div>
<div style="text-align:justify;"><span style="font-size:24px;"><br></span></div>
            </div>
                </div>