---
layout:     post
title:      python通过thrift方式连接hive
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div class="blog-heading" style="color:rgb(51,51,51);font-family:'Pingfang SC', STHeiti, 'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', 'WenQuanYi Micro Hei', SimSun, sans-serif;font-size:16px;">
<div class="title" style="font-size:28px;">
<div class="blog-heading" style="color:rgb(51,51,51);font-family:'Pingfang SC', STHeiti, 'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', 'WenQuanYi Micro Hei', SimSun, sans-serif;font-size:16px;">
<div class="title" style="font-size:28px;">python通过thrift方式连接hive</div>
<div class="info-opr layout" style="color:rgb(102,102,102);border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(204,204,204);">
<div class="layout-column" style="display:table-cell;">学习，存储，共享。。。。<br><br><span style="color:#3d464d;">hive安装完成后，如果只是本地使用，启用</span></div>
</div>
</div>
<div class="blog-body" id="blogBody" style="font-size:16px;font-family:'Pingfang SC', STHeiti, 'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', 'WenQuanYi Micro Hei', SimSun, sans-serif;color:rgb(61,70,77);line-height:28px;">
<div class="BlogContent">
<pre class="hljs groovy" style="background-color:rgb(63,63,63);color:rgb(220,220,220);line-height:1.4;font-size:13px;font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;"><code class="language-bash hljs" style="display:block;line-height:1.4;font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;">nohup hive --service metastore &amp;

[hadoop<span class="hljs-meta" style="color:rgb(127,159,127);">@master</span>1 usr]$ hive

Logging initialized using configuration <span class="hljs-keyword" style="color:rgb(227,206,171);">in</span> <span class="hljs-string" style="color:rgb(204,147,147);">file:</span><span class="hljs-regexp">/data/</span>usr<span class="hljs-regexp">/hive/</span>conf/hive-log4j.properties
hive&gt; use fmcm;
OK
Time <span class="hljs-string" style="color:rgb(204,147,147);">taken:</span> <span class="hljs-number" style="color:rgb(140,208,211);">0.874</span> seconds</code></pre>
<p>如果是要脚本调用，则需要启用HiveServer2,确保10000端口已经被监听（可在hive-site.xml中修改端口）</p>
<pre class="hljs nginx" style="background-color:rgb(63,63,63);color:rgb(220,220,220);line-height:1.4;font-size:13px;font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;"><code class="language-bash hljs" style="display:block;line-height:1.4;font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;"> <span class="hljs-attribute" style="color:rgb(239,220,188);">nohup</span> hive --service hiveserver2﻿ &amp;

[hadoop<span class="hljs-variable" style="color:rgb(239,220,188);">@master1</span> usr]$ netstat -an|grep <span class="hljs-number" style="color:rgb(140,208,211);">10000</span>            
tcp        <span class="hljs-number" style="color:rgb(140,208,211);">0</span>      <span class="hljs-number" style="color:rgb(140,208,211);">0</span> <span class="hljs-number" style="color:rgb(140,208,211);">0.0.0.0:10000</span>           <span class="hljs-number" style="color:rgb(140,208,211);">0.0.0.0</span>:*               LISTEN    </code></pre>
<p> </p>
<p>HiveServer2为客户端在远程执行hive查询提供了接口，通过Thrift RPC来实现，还提供了多用户并发和认证功能。目前python可以通过pyhs2这个模块来连接HiveServer2，实现查询和取回结果的操作。</p>
<p>不过pyhs2已经不在维护,追新的可以参考另外2个很好的python package(已经被证明pyhs2存在性能瓶颈，最好尽快切换到pyhive)</p>
<p><a href="https://github.com/dropbox/PyHive" rel="nofollow" style="border:none;text-decoration:none;color:rgb(68,102,187);">https://github.com/dropbox/PyHive</a></p>
<p><a href="https://github.com/cloudera/impyla" rel="nofollow" style="border:none;text-decoration:none;color:rgb(68,102,187);">https://github.com/cloudera/impyla</a></p>
<p> </p>
<pre><code class="language-css"><code class="hljs css" style="display:block;line-height:1.4;font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;">安装<span class="hljs-selector-tag" style="color:rgb(227,206,171);">sasl</span>失败的话，先安装：
<span class="hljs-selector-tag" style="color:rgb(227,206,171);">yum</span> <span class="hljs-selector-tag" style="color:rgb(227,206,171);">install</span> <span class="hljs-selector-tag" style="color:rgb(227,206,171);">gcc-c</span>++ <span class="hljs-selector-tag" style="color:rgb(227,206,171);">python-devel</span><span class="hljs-selector-class" style="color:rgb(239,239,143);">.x86_64</span> <span class="hljs-selector-tag" style="color:rgb(227,206,171);">cyrus-sasl-devel</span><span class="hljs-selector-class" style="color:rgb(239,239,143);">.x86_64</span></code></code></pre>
<p>pyhs2的项目托管在github之上，地址为https://github.com/BradRuderman/pyhs2或在https://pypi.python.org/pypi/pyhs2/0.2直接下载</p>
<p> </p>
<p>如果安装不成功，可以尝试先安装以下的组件：</p>
<blockquote style="color:rgb(0,0,0);background-color:rgb(244,244,244);">
<p>yum install cyrus-sasl-plain<br>
yum install cyrus-sasl-devel</p>
</blockquote>
<p>安装时如果遇到报错: </p>
<pre class="hljs lua" style="background-color:rgb(63,63,63);color:rgb(220,220,220);line-height:1.4;font-size:13px;font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;"><code class="hljs lua" style="display:block;line-height:1.4;font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;"><span class="hljs-built_in" style="color:rgb(204,147,147);">error</span>: sasl/sasl.h: No such file <span class="hljs-keyword" style="color:rgb(227,206,171);">or</span> directory </code></pre>
<p>可以尝试先安装sasl , ubantu可以用sudo apt-get install libsasl2-dev, CentOS可以使用anaconda的pip安装, 或者按照以下步骤安装:</p>
<p> </p>
<pre class="hljs nginx" style="background-color:rgb(63,63,63);color:rgb(220,220,220);line-height:1.4;font-size:13px;font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;"><code class="hljs nginx" style="display:block;line-height:1.4;font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;"><span class="hljs-attribute" style="color:rgb(239,220,188);">curl</span> -O -L ftp://ftp.cyrusimap.org/cyrus-sasl/cyrus-sasl-2.1.26.tar.gz
tar xzf cyrus-sasl-<span class="hljs-number" style="color:rgb(140,208,211);">2.1.2.26</span>.tar.gz
cd cyrus-sasl-<span class="hljs-number" style="color:rgb(140,208,211);">2</span>.<span class="hljs-number" style="color:rgb(140,208,211);">1</span>.<span class="hljs-number" style="color:rgb(140,208,211);">26</span>.tar.gz
./configure &amp;&amp; make install


最后附上测试代码:</code></pre>
<pre><code class="language-python"><code class="hljs python" style="display:block;line-height:1.4;font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;"><span class="hljs-comment" style="color:rgb(127,159,127);"># -*- coding:utf-8 -*-</span>
<span class="hljs-string" style="color:rgb(204,147,147);">'''
采用Hive和thrift方式连接数据库
'''</span>
<span class="hljs-keyword" style="color:rgb(227,206,171);">import</span> pyhs2
<span class="hljs-keyword" style="color:rgb(227,206,171);">import</span> sys
reload(sys)
sys.setdefaultencoding(<span class="hljs-string" style="color:rgb(204,147,147);">'utf8'</span>)

<span class="hljs-class"><span class="hljs-keyword" style="color:rgb(227,206,171);"><span class="hljs-class"><span class="hljs-keyword">class</span></span></span><span class="hljs-class"> </span><span class="hljs-title" style="color:rgb(239,239,143);"><span class="hljs-class"><span class="hljs-title">HiveClient</span></span></span><span class="hljs-class">:</span></span>
    <span class="hljs-function"><span class="hljs-keyword" style="color:rgb(227,206,171);"><span class="hljs-function"><span class="hljs-keyword">def</span></span></span><span class="hljs-function"> </span><span class="hljs-title" style="color:rgb(239,239,143);"><span class="hljs-function"><span class="hljs-title">__init__</span></span></span><span class="hljs-params"><span class="hljs-function"><span class="hljs-params">(self, db_host, user, password, database, port=</span></span><span class="hljs-number" style="color:rgb(140,208,211);"><span class="hljs-function"><span class="hljs-params"><span class="hljs-number">10000</span></span></span></span><span class="hljs-function"><span class="hljs-params">, authMechanism=</span></span><span class="hljs-string" style="color:rgb(204,147,147);"><span class="hljs-function"><span class="hljs-params"><span class="hljs-string">"PLAIN"</span></span></span></span><span class="hljs-function"><span class="hljs-params">)</span></span></span><span class="hljs-function">:</span></span>
      
        self.conn = pyhs2.connect(host=db_host,
                                  port=port,
                                  authMechanism=authMechanism,
                                  user=user,
                                  password=password,
                                  database=database,
                                  )

    <span class="hljs-function"><span class="hljs-keyword" style="color:rgb(227,206,171);"><span class="hljs-function"><span class="hljs-keyword">def</span></span></span><span class="hljs-function"> </span><span class="hljs-title" style="color:rgb(239,239,143);"><span class="hljs-function"><span class="hljs-title">query</span></span></span><span class="hljs-params"><span class="hljs-function"><span class="hljs-params">(self, sql)</span></span></span><span class="hljs-function">:</span></span>
        <span class="hljs-keyword" style="color:rgb(227,206,171);">with</span> self.conn.cursor() <span class="hljs-keyword" style="color:rgb(227,206,171);">as</span> cursor:
            cursor.execute(sql)
            <span class="hljs-keyword" style="color:rgb(227,206,171);">return</span> cursor.fetch()

    <span class="hljs-function"><span class="hljs-keyword" style="color:rgb(227,206,171);"><span class="hljs-function"><span class="hljs-keyword">def</span></span></span><span class="hljs-function"> </span><span class="hljs-title" style="color:rgb(239,239,143);"><span class="hljs-function"><span class="hljs-title">close</span></span></span><span class="hljs-params"><span class="hljs-function"><span class="hljs-params">(self)</span></span></span><span class="hljs-function">:</span></span>
        self.conn.close()


<span class="hljs-function"><span class="hljs-keyword" style="color:rgb(227,206,171);"><span class="hljs-function"><span class="hljs-keyword">def</span></span></span><span class="hljs-function"> </span><span class="hljs-title" style="color:rgb(239,239,143);"><span class="hljs-function"><span class="hljs-title">main</span></span></span><span class="hljs-params"><span class="hljs-function"><span class="hljs-params">()</span></span></span><span class="hljs-function">:</span></span>
    <span class="hljs-string" style="color:rgb(204,147,147);">"""
    main process
    @rtype:
    @return:
    @note:

    """</span>
    hive_client = HiveClient(db_host=<span class="hljs-string" style="color:rgb(204,147,147);">'10.24.33.3'</span>, port=<span class="hljs-number" style="color:rgb(140,208,211);">10000</span>, user=<span class="hljs-string" style="color:rgb(204,147,147);">'hadoop'</span>, password=<span class="hljs-string" style="color:rgb(204,147,147);">'hadoop'</span>,
                             database=<span class="hljs-string" style="color:rgb(204,147,147);">'fmcm'</span>, authMechanism=<span class="hljs-string" style="color:rgb(204,147,147);">'PLAIN'</span>)
    result = hive_client.query(<span class="hljs-string" style="color:rgb(204,147,147);">'select * from fm_news_newsaction limit 10'</span>)
    <span class="hljs-keyword" style="color:rgb(227,206,171);">print</span> result
    hive_client.close()


<span class="hljs-keyword" style="color:rgb(227,206,171);">if</span> __name__ == <span class="hljs-string" style="color:rgb(204,147,147);">'__main__'</span>:
    main()</code></code></pre>
</div>
</div>
<br></div>
</div>
            </div>
                </div>