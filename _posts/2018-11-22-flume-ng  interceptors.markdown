---
layout:     post
title:      flume-ng  interceptors
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/linlinv3/article/details/50058603				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="font-family:'Helvetica Neue';font-size:14px;">
<br>
flume-ng  interceptors 可以理解为一个过滤器，通过配置可以收集到符合自己需要类型的日志</p>
<p style="font-family:'Helvetica Neue';font-size:14px;">
官网提供了以下几种interceptors：</p>
<p style="font-family:'Helvetica Neue';font-size:14px;">
</p>
<h4 style="font-size:19.2000007629395px;font-weight:normal;color:rgb(0,0,139);border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(204,204,204);font-family:STHeiti;background-color:rgb(242,242,242);">
  Timestamp Interceptor</h4>
<p style="font-family:'Helvetica Neue';font-size:14px;">
在event的header中添加一个key叫：timestamp,value为当前的时间戳</p>
<p style="font-family:'Helvetica Neue';font-size:14px;">
</p>
<p style="text-align:justify;line-height:20.7999992370605px;font-family:STHeiti;font-size:16px;">
Example for agent named a1:</p>
<div class="highlight-properties" style="font-family:STHeiti;font-size:16px;">
<div class="highlight">
<pre style="color:rgb(51,51,51);line-height:15.6000003814697px;border-top-width:1px;border-bottom-width:1px;border-style:solid none;border-top-color:rgb(170,204,153);border-bottom-color:rgb(170,204,153);background-color:rgb(238,255,204);"><span class="na" style="color:rgb(0,128,128);">a1.sources</span> <span class="o" style="font-weight:bold;">=</span> <span class="s" style="color:rgb(187,136,68);">r1</span>
<span class="na" style="color:rgb(0,128,128);">a1.channels</span> <span class="o" style="font-weight:bold;">=</span> <span class="s" style="color:rgb(187,136,68);">c1</span>
<span class="na" style="color:rgb(0,128,128);">a1.sources.r1.channels</span> <span class="o" style="font-weight:bold;">=</span>  <span class="s" style="color:rgb(187,136,68);">c1</span>
<span class="na" style="color:rgb(0,128,128);">a1.sources.r1.type</span> <span class="o" style="font-weight:bold;">=</span> <span class="s" style="color:rgb(187,136,68);">seq</span>
<span class="na" style="color:rgb(0,128,128);">a1.sources.r1.interceptors</span> <span class="o" style="font-weight:bold;">=</span> <span class="s" style="color:rgb(187,136,68);">i1</span>
<span class="na" style="color:rgb(0,128,128);">a1.sources.r1.interceptors.i1.type</span> <span class="o" style="font-weight:bold;">=</span> <span class="s" style="color:rgb(187,136,68);">timestamp</span></pre>
</div>
</div>
<p style="font-family:'Helvetica Neue';font-size:14px;">
</p>
<h4 style="font-size:19.2000007629395px;font-weight:normal;color:rgb(0,0,139);border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(204,204,204);font-family:STHeiti;background-color:rgb(242,242,242);">
  Host Interceptor</h4>
<p style="font-family:'Helvetica Neue';font-size:14px;">
在event的header中添加一个key叫：host,value为当前机器的hostname或者ip</p>
<div class="section" id="host-interceptor" style="font-family:STHeiti;font-size:16px;">
<p style="text-align:justify;line-height:20.7999992370605px;">
Example for agent named a1:</p>
<div class="highlight-properties">
<div class="highlight">
<pre style="color:rgb(51,51,51);line-height:15.6000003814697px;border-top-width:1px;border-bottom-width:1px;border-style:solid none;border-top-color:rgb(170,204,153);border-bottom-color:rgb(170,204,153);background-color:rgb(238,255,204);"><span class="na" style="color:rgb(0,128,128);">a1.sources</span> <span class="o" style="font-weight:bold;">=</span> <span class="s" style="color:rgb(187,136,68);">r1</span>
<span class="na" style="color:rgb(0,128,128);">a1.channels</span> <span class="o" style="font-weight:bold;">=</span> <span class="s" style="color:rgb(187,136,68);">c1</span>
<span class="na" style="color:rgb(0,128,128);">a1.sources.r1.interceptors</span> <span class="o" style="font-weight:bold;">=</span> <span class="s" style="color:rgb(187,136,68);">i1</span>
<span class="na" style="color:rgb(0,128,128);">a1.sources.r1.interceptors.i1.type</span> <span class="o" style="font-weight:bold;">=</span> <span class="s" style="color:rgb(187,136,68);">host</span>
<span class="na" style="color:rgb(0,128,128);">a1.sources.r1.interceptors.i1.hostHeader</span> <span class="o" style="font-weight:bold;">=</span> <span class="s" style="color:rgb(187,136,68);">hostname</span>
</pre>
<br><h4 style="font-size:19.2000007629395px;font-weight:normal;color:rgb(0,0,139);border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(204,204,204);background-color:rgb(242,242,242);">
  Static Interceptor</h4>
</div>
</div>
</div>
<p style="font-family:'Helvetica Neue';font-size:14px;">
可以在event的header中添加自定义的key和value。</p>
<p style="font-family:'Helvetica Neue';font-size:14px;">
</p>
<div class="section" id="static-interceptor" style="font-family:STHeiti;font-size:16px;">
<p style="text-align:justify;line-height:20.7999992370605px;">
Example for agent named a1:</p>
<div class="highlight-properties">
<div class="highlight">
<pre style="color:rgb(51,51,51);line-height:15.6000003814697px;border-top-width:1px;border-bottom-width:1px;border-style:solid none;border-top-color:rgb(170,204,153);border-bottom-color:rgb(170,204,153);background-color:rgb(238,255,204);"><span class="na" style="color:rgb(0,128,128);">a1.sources</span> <span class="o" style="font-weight:bold;">=</span> <span class="s" style="color:rgb(187,136,68);">r1</span>
<span class="na" style="color:rgb(0,128,128);">a1.channels</span> <span class="o" style="font-weight:bold;">=</span> <span class="s" style="color:rgb(187,136,68);">c1</span>
<span class="na" style="color:rgb(0,128,128);">a1.sources.r1.channels</span> <span class="o" style="font-weight:bold;">=</span>  <span class="s" style="color:rgb(187,136,68);">c1</span>
<span class="na" style="color:rgb(0,128,128);">a1.sources.r1.type</span> <span class="o" style="font-weight:bold;">=</span> <span class="s" style="color:rgb(187,136,68);">seq</span>
<span class="na" style="color:rgb(0,128,128);">a1.sources.r1.interceptors</span> <span class="o" style="font-weight:bold;">=</span> <span class="s" style="color:rgb(187,136,68);">i1</span>
<span class="na" style="color:rgb(0,128,128);">a1.sources.r1.interceptors.i1.type</span> <span class="o" style="font-weight:bold;">=</span> <span class="s" style="color:rgb(187,136,68);">static</span>
<span class="na" style="color:rgb(0,128,128);">a1.sources.r1.interceptors.i1.key</span> <span class="o" style="font-weight:bold;">=</span> <span class="s" style="color:rgb(187,136,68);">datacenter</span>
<span class="na" style="color:rgb(0,128,128);">a1.sources.r1.interceptors.i1.value</span> <span class="o" style="font-weight:bold;">=</span> <span class="s" style="color:rgb(187,136,68);">NEW_YORK</span>
</pre>
<span class="s" style="color:rgb(187,136,68);"><br></span></div>
</div>
</div>
<p style="font-family:'Helvetica Neue';font-size:14px;">
</p>
<h4 style="font-size:19.2000007629395px;font-weight:normal;color:rgb(0,0,139);border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(204,204,204);font-family:STHeiti;background-color:rgb(242,242,242);">
  UUID Interceptor</h4>
<p style="font-family:'Helvetica Neue';font-size:14px;">
<br></p>
<p style="font-family:'Helvetica Neue';font-size:14px;">
<br></p>
<h4 style="font-size:19.2000007629395px;font-weight:normal;color:rgb(0,0,139);border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(204,204,204);font-family:STHeiti;background-color:rgb(242,242,242);">
  Morphline Interceptor</h4>
<p style="font-family:'Helvetica Neue';font-size:14px;">
</p>
<p style="font-family:'Helvetica Neue';font-size:14px;text-align:justify;line-height:20.7999992370605px;">
<br></p>
<p style="text-align:justify;line-height:20.7999992370605px;font-family:STHeiti;font-size:16px;">
Sample flume.conf file:</p>
<div class="highlight-properties" style="font-family:STHeiti;font-size:16px;">
<div class="highlight">
<pre style="color:rgb(51,51,51);line-height:15.6000003814697px;border-top-width:1px;border-bottom-width:1px;border-style:solid none;border-top-color:rgb(170,204,153);border-bottom-color:rgb(170,204,153);background-color:rgb(238,255,204);"><span class="na" style="color:rgb(0,128,128);">a1.sources.avroSrc.interceptors</span> <span class="o" style="font-weight:bold;">=</span> <span class="s" style="color:rgb(187,136,68);">morphlineinterceptor</span>
<span class="na" style="color:rgb(0,128,128);">a1.sources.avroSrc.interceptors.morphlineinterceptor.type</span> <span class="o" style="font-weight:bold;">=</span> <span class="s" style="color:rgb(187,136,68);">org.apache.flume.sink.solr.morphline.MorphlineInterceptor$Builder</span>
<span class="na" style="color:rgb(0,128,128);">a1.sources.avroSrc.interceptors.morphlineinterceptor.morphlineFile</span> <span class="o" style="font-weight:bold;">=</span> <span class="s" style="color:rgb(187,136,68);">/etc/flume-ng/conf/morphline.conf</span>
<span class="na" style="color:rgb(0,128,128);">a1.sources.avroSrc.interceptors.morphlineinterceptor.morphlineId</span> <span class="o" style="font-weight:bold;">=</span> <span class="s" style="color:rgb(187,136,68);">morphline1</span></pre>
</div>
</div>
<p style="font-family:'Helvetica Neue';font-size:14px;">
<br></p>
<p style="font-family:'Helvetica Neue';font-size:14px;">
</p>
<h4 style="font-size:19.2000007629395px;font-weight:normal;color:rgb(0,0,139);border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(204,204,204);font-family:STHeiti;background-color:rgb(242,242,242);">
  Search and Replace Interceptor</h4>
<p style="font-family:'Helvetica Neue';font-size:14px;">
<br></p>
<p style="font-family:'Helvetica Neue';font-size:14px;">
</p>
<p style="text-align:justify;line-height:20.7999992370605px;font-family:STHeiti;font-size:16px;">
Example configuration:</p>
<div class="highlight-properties" style="font-family:STHeiti;font-size:16px;">
<div class="highlight">
<pre style="color:rgb(51,51,51);line-height:15.6000003814697px;border-top-width:1px;border-bottom-width:1px;border-style:solid none;border-top-color:rgb(170,204,153);border-bottom-color:rgb(170,204,153);background-color:rgb(238,255,204);"><span class="na" style="color:rgb(0,128,128);">a1.sources.avroSrc.interceptors</span> <span class="o" style="font-weight:bold;">=</span> <span class="s" style="color:rgb(187,136,68);">search-replace</span>
<span class="na" style="color:rgb(0,128,128);">a1.sources.avroSrc.interceptors.search-replace.type</span> <span class="o" style="font-weight:bold;">=</span> <span class="s" style="color:rgb(187,136,68);">search_replace</span>

<span class="c" style="color:rgb(153,153,136);font-style:italic;"># Remove leading alphanumeric characters in an event body.</span>
<span class="na" style="color:rgb(0,128,128);">a1.sources.avroSrc.interceptors.search-replace.searchPattern</span> <span class="o" style="font-weight:bold;">=</span> <span class="s" style="color:rgb(187,136,68);">^[A-Za-z0-9_]+</span>
<span class="na" style="color:rgb(0,128,128);">a1.sources.avroSrc.interceptors.search-replace.replaceString</span> <span class="o" style="font-weight:bold;">=</span>
</pre>
</div>
</div>
<p style="text-align:justify;line-height:20.7999992370605px;font-family:STHeiti;font-size:16px;">
Another example:</p>
<div class="highlight-properties" style="font-family:STHeiti;font-size:16px;">
<div class="highlight">
<pre style="color:rgb(51,51,51);line-height:15.6000003814697px;border-top-width:1px;border-bottom-width:1px;border-style:solid none;border-top-color:rgb(170,204,153);border-bottom-color:rgb(170,204,153);background-color:rgb(238,255,204);"><span class="na" style="color:rgb(0,128,128);">a1.sources.avroSrc.interceptors</span> <span class="o" style="font-weight:bold;">=</span> <span class="s" style="color:rgb(187,136,68);">search-replace</span>
<span class="na" style="color:rgb(0,128,128);">a1.sources.avroSrc.interceptors.search-replace.type</span> <span class="o" style="font-weight:bold;">=</span> <span class="s" style="color:rgb(187,136,68);">search_replace</span>

<span class="c" style="color:rgb(153,153,136);font-style:italic;"># Use grouping operators to reorder and munge words on a line.</span>
<span class="na" style="color:rgb(0,128,128);">a1.sources.avroSrc.interceptors.search-replace.searchPattern</span> <span class="o" style="font-weight:bold;">=</span> <span class="s" style="color:rgb(187,136,68);">The quick brown ([a-z]+) jumped over the lazy ([a-z]+)</span>
<span class="na" style="color:rgb(0,128,128);">a1.sources.avroSrc.interceptors.search-replace.replaceString</span> <span class="o" style="font-weight:bold;">=</span> <span class="s" style="color:rgb(187,136,68);">The hungry $2 ate the careless $1</span></pre>
</div>
</div>
<p style="font-family:'Helvetica Neue';font-size:14px;">
<br></p>
<p style="font-family:'Helvetica Neue';font-size:14px;">
</p>
<h4 style="font-size:19.2000007629395px;font-weight:normal;color:rgb(0,0,139);border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(204,204,204);font-family:STHeiti;background-color:rgb(242,242,242);">
  Regex Filtering Interceptor<a class="headerlink" href="http://flume.apache.org/FlumeUserGuide.html#regex-filtering-interceptor" rel="nofollow" title="Permalink to this headline" style="color:rgb(198,15,15);text-decoration:none;visibility:hidden;font-size:.8em;"></a></h4>
<p style="font-family:'Helvetica Neue';font-size:14px;">
通过正则来清洗或包含匹配的events。</p>
<p style="font-family:'Helvetica Neue';font-size:14px;">
<br></p>
<p style="font-family:'Helvetica Neue';font-size:14px;">
<br></p>
<h4 style="font-size:19.2000007629395px;font-weight:normal;color:rgb(0,0,139);border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(204,204,204);font-family:STHeiti;background-color:rgb(242,242,242);">
  Regex Extractor Intercepto</h4>
<p style="font-family:'Helvetica Neue';font-size:14px;">
<br></p>
<p style="font-family:'Helvetica Neue';font-size:14px;">
通过正则表达式来在header中添加指定的key,value则为正则匹配的部分<br></p>
<p style="font-family:'Helvetica Neue';font-size:14px;">
<br></p>
<p style="font-family:'Helvetica Neue';font-size:14px;">
</p>
<div class="section" id="example-1" style="font-family:'Helvetica Neue';font-size:14px;">
<h4 style="font-size:19.2000007629395px;font-weight:normal;color:rgb(0,0,139);border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(204,204,204);background-color:rgb(242,242,242);">
  Example 1:<a class="headerlink" href="http://flume.apache.org/FlumeUserGuide.html#example-1" rel="nofollow" title="Permalink to this headline" style="color:rgb(198,15,15);text-decoration:none;visibility:hidden;font-size:.8em;"></a></h4>
<p style="text-align:justify;line-height:20.7999992370605px;">
If the Flume event body contained <tt class="docutils literal" style="font-size:.95em;background-color:rgb(236,240,243);"><span class="pre">1:2:3.4foobar5</span></tt> and the following configuration was used</p>
<div class="highlight-properties">
<div class="highlight">
<pre style="color:rgb(51,51,51);line-height:15.6000003814697px;border-top-width:1px;border-bottom-width:1px;border-style:solid none;border-top-color:rgb(170,204,153);border-bottom-color:rgb(170,204,153);background-color:rgb(238,255,204);"><span class="na" style="color:rgb(0,128,128);">a1.sources.r1.interceptors.i1.regex</span> <span class="o" style="font-weight:bold;">=</span> <span class="s" style="color:rgb(187,136,68);">(\\d):(\\d):(\\d)</span>
<span class="na" style="color:rgb(0,128,128);">a1.sources.r1.interceptors.i1.serializers</span> <span class="o" style="font-weight:bold;">=</span> <span class="s" style="color:rgb(187,136,68);">s1 s2 s3</span>
<span class="na" style="color:rgb(0,128,128);">a1.sources.r1.interceptors.i1.serializers.s1.name</span> <span class="o" style="font-weight:bold;">=</span> <span class="s" style="color:rgb(187,136,68);">one</span>
<span class="na" style="color:rgb(0,128,128);">a1.sources.r1.interceptors.i1.serializers.s2.name</span> <span class="o" style="font-weight:bold;">=</span> <span class="s" style="color:rgb(187,136,68);">two</span>
<span class="na" style="color:rgb(0,128,128);">a1.sources.r1.interceptors.i1.serializers.s3.name</span> <span class="o" style="font-weight:bold;">=</span> <span class="s" style="color:rgb(187,136,68);">three</span>
</pre>
</div>
</div>
<p style="text-align:justify;line-height:20.7999992370605px;">
The extracted event will contain the same body but the following headers will have been added <tt class="docutils literal" style="font-size:.95em;background-color:rgb(236,240,243);"><span class="pre">one=&gt;1,</span> <span class="pre">two=&gt;2,</span> <span class="pre">three=&gt;3</span></tt></p>
</div>
<div class="section" id="example-2" style="font-family:'Helvetica Neue';font-size:14px;">
<h4 style="font-size:19.2000007629395px;font-weight:normal;color:rgb(0,0,139);border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(204,204,204);font-family:STHeiti;background-color:rgb(242,242,242);">
  Example 2:<a class="headerlink" href="http://flume.apache.org/FlumeUserGuide.html#example-2" rel="nofollow" title="Permalink to this headline" style="color:rgb(198,15,15);text-decoration:none;visibility:hidden;font-size:.8em;"></a></h4>
<p style="text-align:justify;line-height:20.7999992370605px;font-family:STHeiti;font-size:16px;">
If the Flume event body contained <tt class="docutils literal" style="font-size:.95em;background-color:rgb(236,240,243);"><span class="pre">2012-10-18</span> <span class="pre">18:47:57,614</span> <span class="pre">some</span> <span class="pre">log</span> <span class="pre">line</span></tt> and
 the following configuration was used</p>
<div class="highlight-properties" style="font-family:STHeiti;font-size:16px;">
<div class="highlight">
<pre style="color:rgb(51,51,51);line-height:15.6000003814697px;border-top-width:1px;border-bottom-width:1px;border-style:solid none;border-top-color:rgb(170,204,153);border-bottom-color:rgb(170,204,153);background-color:rgb(238,255,204);"><span class="na" style="color:rgb(0,128,128);">a1.sources.r1.interceptors.i1.regex</span> <span class="o" style="font-weight:bold;">=</span> <span class="s" style="color:rgb(187,136,68);">^(?:\\n)?(\\d\\d\\d\\d-\\d\\d-\\d\\d\\s\\d\\d:\\d\\d)</span>
<span class="na" style="color:rgb(0,128,128);">a1.sources.r1.interceptors.i1.serializers</span> <span class="o" style="font-weight:bold;">=</span> <span class="s" style="color:rgb(187,136,68);">s1</span>
<span class="na" style="color:rgb(0,128,128);">a1.sources.r1.interceptors.i1.serializers.s1.type</span> <span class="o" style="font-weight:bold;">=</span> <span class="s" style="color:rgb(187,136,68);">org.apache.flume.interceptor.RegexExtractorInterceptorMillisSerializer</span>
<span class="na" style="color:rgb(0,128,128);">a1.sources.r1.interceptors.i1.serializers.s1.name</span> <span class="o" style="font-weight:bold;">=</span> <span class="s" style="color:rgb(187,136,68);">timestamp</span>
<span class="na" style="color:rgb(0,128,128);">a1.sources.r1.interceptors.i1.serializers.s1.pattern</span> <span class="o" style="font-weight:bold;">=</span> <span class="s" style="color:rgb(187,136,68);">yyyy-MM-dd HH:mm</span>
</pre>
<span class="s" style="color:rgb(187,136,68);"><br></span></div>
</div>
</div>
<p style="font-family:'Helvetica Neue';font-size:14px;">
上面是官网提供的demo：</p>
<p style="font-family:'Helvetica Neue';font-size:14px;">
这里我简单介绍一下经常使用的 regex_filter</p>
<p style="font-family:'Helvetica Neue';font-size:14px;">
在配置中新增interceptors：</p>
<p style="font-family:'Helvetica Neue';font-size:14px;">
下面是给了两个正则 ，作为两个例子进行实现  其中 i1 是 匹配正则  i2 是匹配类似 d:d:d 格式的日志</p>
<div style="font-size:14px;font-family:'微软雅黑', 'Microsoft YaHei', Georgia, Helvetica, Arial, sans-serif, '宋体', PMingLiU, serif;line-height:21px;">
<div class="cnblogs_code" style="border:1px solid rgb(204,204,204);overflow:auto;background-color:rgb(245,245,245);">
<pre style="font-family:'Microsoft YaHei UI', 'Microsoft YaHei', SimSun, 'Segoe UI', Tahoma, Helvetica, sans-serif, 'Microsoft YaHei', Georgia, Helvetica, Arial, sans-serif, '宋体', PMingLiU, serif;"><span style="font-family:'Courier New';font-size:12px;">
a1.sources.source1.interceptors=i2
a1.sources.source1.interceptors.i1.type=regex_filter  
a1.sources.source1.interceptors.i1.regex=\\{.*\\}  

a1.sources.source1.interceptors.i2.type=regex_filter  
a1.sources.source1.interceptors.i2.regex = (\\d):(\\d):(\\d)
a1.sources.source1.interceptors.i2.serializers = s1 s2 s3
a1.sources.source1.interceptors.i2.serializers.s1.name = one
a1.sources.source1.interceptors.i2.serializers.s2.name = two
a1.sources.source1.interceptors.i2.serializers.s3.name = three</span></pre>
</div>
</div>
<p style="font-family:'Helvetica Neue';font-size:14px;">
<br></p>
<p style="font-family:'Helvetica Neue';font-size:14px;">
其他的配置请看上一篇日志 <a href="http://blog.csdn.net/linlinv3/article/details/50053333" rel="nofollow">http://blog.csdn.net/linlinv3/article/details/50053333</a>；</p>
<p style="font-family:'Helvetica Neue';font-size:14px;">
 在上一篇的日志中加入 interceptors；</p>
<p style="font-family:'Helvetica Neue';font-size:14px;">
若是不加interceptors 采集到的日志是 WriteLog所有的：</p>
<div style="font-family:'Helvetica Neue';font-size:14px;"></div>
<p style="font-family:'Helvetica Neue';font-size:14px;">
<img src="https://img-blog.csdn.net/20151126172021737?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p style="font-family:'Helvetica Neue';font-size:14px;">
若是加上i1后 日志只有 1：2：3 </p>
<div style="font-family:'Helvetica Neue';font-size:14px;"><img src="https://img-blog.csdn.net/20151126172028497?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<p style="font-family:'Helvetica Neue';font-size:14px;">
若是使用i2 则日志只有 那串json串</p>
<div style="font-family:'Helvetica Neue';font-size:14px;"></div>
<div><img src="https://img-blog.csdn.net/20151126172033363?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
            </div>
                </div>