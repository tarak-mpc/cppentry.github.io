---
layout:     post
title:      Java API 读取HBase表数据
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：如若转载，请联系作者。					https://blog.csdn.net/liu16659/article/details/84072196				</div>
								            <div id="content_views" class="markdown_views prism-dracula">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h3><a id="Java_API_HBase_0"></a>Java API 读取HBase表数据</h3>
<h4><a id="1_1"></a>1.</h4>
<p>在使用java api 去获取数据的时候，先用 <code>hbase shell</code> 展示一下 hbase 中的表。</p>
<pre><code class="prism language-shell">hbase<span class="token punctuation">(</span>main<span class="token punctuation">)</span>:005:0<span class="token operator">&gt;</span> scan <span class="token string">'tsdb-uid'</span>
ROW                                                 COLUMN+CELL                                                                                                                                           
 \x00                                               column<span class="token operator">=</span>id:metrics, timestamp<span class="token operator">=</span>1541500656882, value<span class="token operator">=</span>\x00\x00\x00\x00\x00\x00\x00\x05                                                                    
 \x00                                               column<span class="token operator">=</span>id:tagk, timestamp<span class="token operator">=</span>1535982247222, value<span class="token operator">=</span>\x00\x00\x00\x00\x00
 ···
 \x00\x00\x08                                       column<span class="token operator">=</span>name:tagv, timestamp<span class="token operator">=</span>1541425665725, value<span class="token operator">=</span>firminal                                                                                             
 Firminal                                           column<span class="token operator">=</span>id:tagv, timestamp<span class="token operator">=</span>1537103490289, value<span class="token operator">=</span>\x00\x00\x06                                                                                           
 accessNumber                                       column<span class="token operator">=</span>id:tagk, timestamp<span class="token operator">=</span>1535982247235, value<span class="token operator">=</span>\x00\x00\x03                                                                                           
 chl                                                column<span class="token operator">=</span>id:tagk, timestamp<span class="token operator">=</span>1535891521203, value<span class="token operator">=</span>\x00\x00\x02                                                                                           
 cs                                                 column<span class="token operator">=</span>id:tagv, timestamp<span class="token operator">=</span>1535982247259, value<span class="token operator">=</span>\x00x00\x05                                                                                           
 ···
25 row<span class="token punctuation">(</span>s<span class="token punctuation">)</span> <span class="token keyword">in</span> 0.0770 seconds
</code></pre>
<pre><code class="prism language-shell">hbase<span class="token punctuation">(</span>main<span class="token punctuation">)</span>:007:0<span class="token operator">&gt;</span> get <span class="token string">'tsdb-uid'</span>,<span class="token string">'cs'</span>
COLUMN                                              CELL                                                                                                                                                  
 id:tagv                                            timestamp<span class="token operator">=</span>1535982247259, value<span class="token operator">=</span>\x00\x00\x05                                                                                                           
1 row<span class="token punctuation">(</span>s<span class="token punctuation">)</span> <span class="token keyword">in</span> 0.0240 seconds
</code></pre>
<p>通过观察 <code>tsdb-uid</code>表，可以看到有一个 <code>rowKey = cs</code>，且其值为<code>（hex）000005</code>，那么使用java api去获取数据的代码如下：</p>
<h4><a id="2_JavaHBase_24"></a>2. 使用Java获取HBase的数据</h4>
<h5><a id="21_get_25"></a>2.1 <code>get</code>方法</h5>
<ul>
<li>静态变量</li>
</ul>
<pre><code class="prism language-java">    <span class="token keyword">public</span> <span class="token keyword">static</span> Configuration conf <span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token keyword">static</span> Connection connection<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token keyword">static</span> Admin admin<span class="token punctuation">;</span>

    <span class="token comment">//step2:you should set some properties</span>
    <span class="token comment">//connection should be closed,but not</span>
    <span class="token keyword">static</span><span class="token punctuation">{</span>
        System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"Hello,static code is started..."</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        conf <span class="token operator">=</span> HBaseConfiguration<span class="token punctuation">.</span><span class="token function">create</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        conf<span class="token punctuation">.</span><span class="token function">set</span><span class="token punctuation">(</span><span class="token string">"hbase.zookeeper.quorum"</span><span class="token punctuation">,</span> <span class="token string">"192.168.211.4"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        conf<span class="token punctuation">.</span><span class="token function">set</span><span class="token punctuation">(</span><span class="token string">"hbase.zookeeper.property.clientPort"</span><span class="token punctuation">,</span> <span class="token string">"2181"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token keyword">try</span> <span class="token punctuation">{</span>
            connection<span class="token operator">=</span> ConnectionFactory<span class="token punctuation">.</span><span class="token function">createConnection</span><span class="token punctuation">(</span>conf<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span> <span class="token keyword">catch</span> <span class="token punctuation">(</span><span class="token class-name">IOException</span> e<span class="token punctuation">)</span> <span class="token punctuation">{</span>
            e<span class="token punctuation">.</span><span class="token function">printStackTrace</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
        <span class="token comment">//new way to get an HBaseAdmin</span>
        <span class="token keyword">try</span> <span class="token punctuation">{</span>
            admin <span class="token operator">=</span> connection<span class="token punctuation">.</span><span class="token function">getAdmin</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span> <span class="token keyword">catch</span> <span class="token punctuation">(</span><span class="token class-name">IOException</span> e<span class="token punctuation">)</span> <span class="token punctuation">{</span>
            e<span class="token punctuation">.</span><span class="token function">printStackTrace</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>
</code></pre>
<ul>
<li>get的使用</li>
</ul>
<pre><code class="prism language-java"> <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">getRowFromTable</span><span class="token punctuation">(</span>String tableName<span class="token punctuation">,</span>String qualifier<span class="token punctuation">)</span><span class="token punctuation">{</span>
        <span class="token keyword">try</span> <span class="token punctuation">{</span>
            Table table <span class="token operator">=</span> connection<span class="token punctuation">.</span><span class="token function">getTable</span><span class="token punctuation">(</span>TableName<span class="token punctuation">.</span><span class="token function">valueOf</span><span class="token punctuation">(</span>tableName<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token keyword">byte</span><span class="token punctuation">[</span><span class="token punctuation">]</span> row <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">byte</span><span class="token punctuation">[</span><span class="token punctuation">]</span><span class="token punctuation">{</span><span class="token punctuation">}</span><span class="token punctuation">;</span>
            row <span class="token operator">=</span> qualifier<span class="token punctuation">.</span><span class="token function">getBytes</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            Get g <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Get</span><span class="token punctuation">(</span>row<span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token comment">// Reading the data</span>
            Result result <span class="token operator">=</span> table<span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span>g<span class="token punctuation">)</span><span class="token punctuation">;</span>

            <span class="token comment">// Reading values from Result class object</span>
            <span class="token keyword">byte</span> <span class="token punctuation">[</span><span class="token punctuation">]</span> value1 <span class="token operator">=</span> result<span class="token punctuation">.</span><span class="token function">getValue</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"id"</span><span class="token punctuation">)</span><span class="token punctuation">,</span>Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"tagv"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token keyword">byte</span> <span class="token punctuation">[</span><span class="token punctuation">]</span> value2 <span class="token operator">=</span> result<span class="token punctuation">.</span><span class="token function">getValue</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"id"</span><span class="token punctuation">)</span><span class="token punctuation">,</span>Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"timestamp"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

            <span class="token keyword">for</span><span class="token punctuation">(</span><span class="token keyword">int</span> i <span class="token operator">=</span> <span class="token number">0</span><span class="token punctuation">;</span>i<span class="token operator">&lt;</span> value1<span class="token punctuation">.</span>length<span class="token punctuation">;</span>i<span class="token operator">++</span><span class="token punctuation">)</span><span class="token punctuation">{</span>
                System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">print</span><span class="token punctuation">(</span>value1<span class="token punctuation">[</span>i<span class="token punctuation">]</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span>
            CustomedMethod<span class="token punctuation">.</span><span class="token function">printDelimiter</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token punctuation">}</span> <span class="token keyword">catch</span> <span class="token punctuation">(</span><span class="token class-name">IOException</span> e<span class="token punctuation">)</span> <span class="token punctuation">{</span>
            e<span class="token punctuation">.</span><span class="token function">printStackTrace</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>
</code></pre>
<pre><code class="prism language-java"><span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span>String<span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">try</span> <span class="token punctuation">{</span>
            System<span class="token punctuation">.</span><span class="token function">setProperty</span><span class="token punctuation">(</span><span class="token string">"hadoop.home.dir"</span><span class="token punctuation">,</span> <span class="token string">"D:\\SoftWarePackages\\BigData\\hadoop-2.6.4"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>    
            HBaseUtils<span class="token punctuation">.</span><span class="token function">getRowFromTable</span><span class="token punctuation">(</span><span class="token string">"tsdb-uid"</span><span class="token punctuation">,</span><span class="token string">"cs"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            HBaseUtils<span class="token punctuation">.</span><span class="token function">closeAll</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span> <span class="token keyword">catch</span> <span class="token punctuation">(</span><span class="token class-name">IOException</span> e<span class="token punctuation">)</span> <span class="token punctuation">{</span>
            e<span class="token punctuation">.</span><span class="token function">printStackTrace</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>
</code></pre>
<p>得到的结果如下：<br>
<img src="https://img-blog.csdnimg.cn/20181114185704357.png" alt="在这里插入图片描述"></p>
<h5><a id="22_scan_94"></a>2.2 <code>scan</code>方法</h5>
<pre><code class="prism language-java">
</code></pre>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>