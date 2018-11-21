---
layout:     post
title:      HBase Java API 基本操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h4><a id="hbase_shell_APIJava_API_hbase_0"></a>学完hbase shell API的基本操作之后，可以通过Java API 对hbase基本操作实现一把。</h4>
<ol>
<li>基本概念</li>
</ol>

<table>
<thead>
<tr>
<th>java类</th>
<th>对应数据模型</th>
</tr>
</thead>
<tbody>
<tr>
<td>HBaseConfiguration</td>
<td>HBase配置类</td>
</tr>
<tr>
<td>HBaseAdmin</td>
<td>HBase管理Admin类</td>
</tr>
<tr>
<td>Table</td>
<td>HBase Table操作类</td>
</tr>
<tr>
<td>Put</td>
<td>HBase添加操作数据模型</td>
</tr>
<tr>
<td>Get</td>
<td>HBase单个查询操作数据模型</td>
</tr>
<tr>
<td>Scan</td>
<td>HBase Scan检索操作数据模型</td>
</tr>
<tr>
<td>Result</td>
<td>HBase单个查询的结果模型</td>
</tr>
<tr>
<td>ResultScanner</td>
<td>HBase检索结果模型</td>
</tr>
</tbody>
</table><p>2.首先封装一个hbase connection工具类，这是一个单例的类，用于获取获取连接，关闭连接，获取hbase table对象。</p>
<pre><code class="prism language-java"><span class="token keyword">package</span> com<span class="token punctuation">.</span>zsk<span class="token punctuation">.</span>hbase<span class="token punctuation">.</span>api<span class="token punctuation">;</span>

<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>hadoop<span class="token punctuation">.</span>conf<span class="token punctuation">.</span>Configuration<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>hadoop<span class="token punctuation">.</span>hbase<span class="token punctuation">.</span>HBaseConfiguration<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>hadoop<span class="token punctuation">.</span>hbase<span class="token punctuation">.</span>TableName<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>hadoop<span class="token punctuation">.</span>hbase<span class="token punctuation">.</span>client<span class="token punctuation">.</span>Connection<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>hadoop<span class="token punctuation">.</span>hbase<span class="token punctuation">.</span>client<span class="token punctuation">.</span>ConnectionFactory<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>hadoop<span class="token punctuation">.</span>hbase<span class="token punctuation">.</span>client<span class="token punctuation">.</span>Table<span class="token punctuation">;</span>

<span class="token keyword">import</span> java<span class="token punctuation">.</span>io<span class="token punctuation">.</span>IOException<span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">HBaseConn</span> <span class="token punctuation">{</span>
    <span class="token keyword">private</span> <span class="token keyword">static</span> <span class="token keyword">final</span> HBaseConn INSTANCE <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">HBaseConn</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token keyword">static</span>  Configuration configuration<span class="token punctuation">;</span> <span class="token comment">//hbase配置</span>
    <span class="token keyword">private</span> <span class="token keyword">static</span>  Connection connection<span class="token punctuation">;</span> <span class="token comment">//hbase connection</span>
    <span class="token keyword">private</span> <span class="token function">HBaseConn</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">{</span>
        <span class="token keyword">try</span><span class="token punctuation">{</span>
            <span class="token keyword">if</span> <span class="token punctuation">(</span>configuration<span class="token operator">==</span>null<span class="token punctuation">)</span><span class="token punctuation">{</span>
                 configuration <span class="token operator">=</span> HBaseConfiguration<span class="token punctuation">.</span><span class="token function">create</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
                 configuration<span class="token punctuation">.</span><span class="token function">set</span><span class="token punctuation">(</span><span class="token string">"hbase.zookeeper.quorum"</span><span class="token punctuation">,</span><span class="token string">"192.168.217.129:2181"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span>

        <span class="token punctuation">}</span><span class="token keyword">catch</span> <span class="token punctuation">(</span><span class="token class-name">Exception</span> e<span class="token punctuation">)</span><span class="token punctuation">{</span>
            e<span class="token punctuation">.</span><span class="token function">printStackTrace</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>
    <span class="token keyword">private</span>  Connection <span class="token function">getConnection</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">{</span>
        <span class="token keyword">if</span> <span class="token punctuation">(</span>connection<span class="token operator">==</span>null <span class="token operator">||</span> connection<span class="token punctuation">.</span><span class="token function">isClosed</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">{</span>
            <span class="token keyword">try</span><span class="token punctuation">{</span>
                connection <span class="token operator">=</span> ConnectionFactory<span class="token punctuation">.</span><span class="token function">createConnection</span><span class="token punctuation">(</span>configuration<span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span><span class="token keyword">catch</span> <span class="token punctuation">(</span><span class="token class-name">Exception</span> e<span class="token punctuation">)</span><span class="token punctuation">{</span>
                e<span class="token punctuation">.</span><span class="token function">printStackTrace</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span>
        <span class="token punctuation">}</span>
        <span class="token keyword">return</span> connection<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    <span class="token keyword">public</span> <span class="token keyword">static</span> Connection <span class="token function">getHBaseConn</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">{</span>
        <span class="token keyword">return</span> INSTANCE<span class="token punctuation">.</span><span class="token function">getConnection</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    <span class="token keyword">public</span> <span class="token keyword">static</span> Table <span class="token function">getTable</span><span class="token punctuation">(</span>String tableName<span class="token punctuation">)</span> <span class="token keyword">throws</span> IOException <span class="token punctuation">{</span>
        <span class="token keyword">return</span> INSTANCE<span class="token punctuation">.</span><span class="token function">getConnection</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">getTable</span><span class="token punctuation">(</span>TableName<span class="token punctuation">.</span><span class="token function">valueOf</span><span class="token punctuation">(</span>tableName<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">closeConn</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">{</span>
        <span class="token keyword">if</span> <span class="token punctuation">(</span>connection<span class="token operator">!=</span>null<span class="token punctuation">)</span><span class="token punctuation">{</span>
            <span class="token keyword">try</span> <span class="token punctuation">{</span>
                connection<span class="token punctuation">.</span><span class="token function">close</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span> <span class="token keyword">catch</span> <span class="token punctuation">(</span><span class="token class-name">IOException</span> e<span class="token punctuation">)</span> <span class="token punctuation">{</span>
                e<span class="token punctuation">.</span><span class="token function">printStackTrace</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span>
        <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>

<span class="token punctuation">}</span>

</code></pre>
<p>3.具体操作hbase 工具类</p>
<pre><code class="prism language-java">
<span class="token keyword">package</span> com<span class="token punctuation">.</span>zsk<span class="token punctuation">.</span>hbase<span class="token punctuation">.</span>api<span class="token punctuation">;</span>

<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>hadoop<span class="token punctuation">.</span>hbase<span class="token punctuation">.</span>HColumnDescriptor<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>hadoop<span class="token punctuation">.</span>hbase<span class="token punctuation">.</span>HTableDescriptor<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>hadoop<span class="token punctuation">.</span>hbase<span class="token punctuation">.</span>TableName<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>hadoop<span class="token punctuation">.</span>hbase<span class="token punctuation">.</span>client<span class="token punctuation">.</span>*<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>hadoop<span class="token punctuation">.</span>hbase<span class="token punctuation">.</span>filter<span class="token punctuation">.</span>FilterList<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>hadoop<span class="token punctuation">.</span>hbase<span class="token punctuation">.</span>util<span class="token punctuation">.</span>Bytes<span class="token punctuation">;</span>

<span class="token keyword">import</span> java<span class="token punctuation">.</span>util<span class="token punctuation">.</span>Arrays<span class="token punctuation">;</span>
<span class="token keyword">import</span> java<span class="token punctuation">.</span>util<span class="token punctuation">.</span>List<span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">HBaseUtil</span> <span class="token punctuation">{</span>
    <span class="token comment">/**
     * 创建表
     * @param tableName 创建表的表名称
     * @param cfs 列簇的集合
     * @return
     */</span>
    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">boolean</span> <span class="token function">createTable</span><span class="token punctuation">(</span>String tableName<span class="token punctuation">,</span> String<span class="token punctuation">[</span><span class="token punctuation">]</span> cfs<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">try</span> <span class="token punctuation">(</span>HBaseAdmin admin <span class="token operator">=</span> <span class="token punctuation">(</span>HBaseAdmin<span class="token punctuation">)</span> HBaseConn<span class="token punctuation">.</span><span class="token function">getHBaseConn</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">getAdmin</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token keyword">if</span> <span class="token punctuation">(</span>admin<span class="token punctuation">.</span><span class="token function">tableExists</span><span class="token punctuation">(</span>tableName<span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
                <span class="token keyword">return</span> <span class="token boolean">false</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span>
            HTableDescriptor tableDescriptor <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">HTableDescriptor</span><span class="token punctuation">(</span>TableName<span class="token punctuation">.</span><span class="token function">valueOf</span><span class="token punctuation">(</span>tableName<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            Arrays<span class="token punctuation">.</span><span class="token function">stream</span><span class="token punctuation">(</span>cfs<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">forEach</span><span class="token punctuation">(</span>cf <span class="token operator">-</span><span class="token operator">&gt;</span> <span class="token punctuation">{</span>
                HColumnDescriptor columnDescriptor <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">HColumnDescriptor</span><span class="token punctuation">(</span>cf<span class="token punctuation">)</span><span class="token punctuation">;</span>
                columnDescriptor<span class="token punctuation">.</span><span class="token function">setMaxVersions</span><span class="token punctuation">(</span><span class="token number">1</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
                tableDescriptor<span class="token punctuation">.</span><span class="token function">addFamily</span><span class="token punctuation">(</span>columnDescriptor<span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            admin<span class="token punctuation">.</span><span class="token function">createTable</span><span class="token punctuation">(</span>tableDescriptor<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span> <span class="token keyword">catch</span> <span class="token punctuation">(</span><span class="token class-name">Exception</span> e<span class="token punctuation">)</span> <span class="token punctuation">{</span>
            e<span class="token punctuation">.</span><span class="token function">printStackTrace</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
        <span class="token keyword">return</span> <span class="token boolean">true</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token comment">/**
     * 删除表
     * @param tableName 表名称
     * @return
     */</span>
    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">boolean</span> <span class="token function">deleteTable</span><span class="token punctuation">(</span>String tableName<span class="token punctuation">)</span><span class="token punctuation">{</span>
        <span class="token keyword">try</span><span class="token punctuation">(</span>HBaseAdmin admin <span class="token operator">=</span> <span class="token punctuation">(</span>HBaseAdmin<span class="token punctuation">)</span>HBaseConn<span class="token punctuation">.</span><span class="token function">getHBaseConn</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">getAdmin</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">{</span>
            <span class="token keyword">if</span> <span class="token punctuation">(</span><span class="token operator">!</span>admin<span class="token punctuation">.</span><span class="token function">tableExists</span><span class="token punctuation">(</span>tableName<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">{</span>
                <span class="token keyword">return</span> <span class="token boolean">false</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span>
            admin<span class="token punctuation">.</span><span class="token function">disableTable</span><span class="token punctuation">(</span>tableName<span class="token punctuation">)</span><span class="token punctuation">;</span>
            admin<span class="token punctuation">.</span><span class="token function">deleteTable</span><span class="token punctuation">(</span>tableName<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span><span class="token keyword">catch</span> <span class="token punctuation">(</span><span class="token class-name">Exception</span> e<span class="token punctuation">)</span><span class="token punctuation">{</span>
            e<span class="token punctuation">.</span><span class="token function">printStackTrace</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
        <span class="token keyword">return</span> <span class="token boolean">true</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token comment">/**
     * 插入数据
     * @param tableName
     * @param rowkey
     * @param cfName
     * @param qualifer
     * @param data
     * @return
     */</span>
    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">boolean</span> <span class="token function">putRow</span><span class="token punctuation">(</span>String tableName<span class="token punctuation">,</span>String rowkey<span class="token punctuation">,</span>String cfName<span class="token punctuation">,</span>String qualifer<span class="token punctuation">,</span>String data<span class="token punctuation">)</span><span class="token punctuation">{</span>
        <span class="token keyword">try</span><span class="token punctuation">(</span>Table table <span class="token operator">=</span> HBaseConn<span class="token punctuation">.</span><span class="token function">getTable</span><span class="token punctuation">(</span>tableName<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">{</span>
            Put put <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Put</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span>rowkey<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            put<span class="token punctuation">.</span><span class="token function">addColumn</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span>cfName<span class="token punctuation">)</span><span class="token punctuation">,</span>Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span>qualifer<span class="token punctuation">)</span><span class="token punctuation">,</span>Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span>data<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            table<span class="token punctuation">.</span><span class="token function">put</span><span class="token punctuation">(</span>put<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span><span class="token keyword">catch</span> <span class="token punctuation">(</span><span class="token class-name">Exception</span> e<span class="token punctuation">)</span><span class="token punctuation">{</span>
            e<span class="token punctuation">.</span><span class="token function">printStackTrace</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
        <span class="token keyword">return</span> <span class="token boolean">true</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token comment">/**
     *批量出入数据
     * @param tableName
     * @param puts
     * @return
     */</span>
    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">boolean</span> <span class="token function">putRows</span><span class="token punctuation">(</span>String tableName<span class="token punctuation">,</span> List<span class="token generics function"><span class="token punctuation">&lt;</span>Put<span class="token punctuation">&gt;</span></span> puts<span class="token punctuation">)</span><span class="token punctuation">{</span>
        <span class="token keyword">try</span><span class="token punctuation">(</span>Table table <span class="token operator">=</span> HBaseConn<span class="token punctuation">.</span><span class="token function">getTable</span><span class="token punctuation">(</span>tableName<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">{</span>
            table<span class="token punctuation">.</span><span class="token function">put</span><span class="token punctuation">(</span>puts<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span><span class="token keyword">catch</span> <span class="token punctuation">(</span><span class="token class-name">Exception</span> e<span class="token punctuation">)</span><span class="token punctuation">{</span>
            e<span class="token punctuation">.</span><span class="token function">printStackTrace</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
        <span class="token keyword">return</span> <span class="token boolean">true</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token comment">/**
     * 查询单条数据
     * @param tableName
     * @param rowkey
     * @return
     */</span>
    <span class="token keyword">public</span> <span class="token keyword">static</span> Result <span class="token function">getRow</span><span class="token punctuation">(</span>String tableName<span class="token punctuation">,</span>String rowkey<span class="token punctuation">)</span><span class="token punctuation">{</span>
        <span class="token keyword">try</span><span class="token punctuation">(</span> Table table <span class="token operator">=</span> HBaseConn<span class="token punctuation">.</span><span class="token function">getTable</span><span class="token punctuation">(</span>tableName<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">{</span>
            Get get <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Get</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span>rowkey<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token keyword">return</span> table<span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span>get<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span><span class="token keyword">catch</span> <span class="token punctuation">(</span><span class="token class-name">Exception</span> e<span class="token punctuation">)</span><span class="token punctuation">{</span>
            e<span class="token punctuation">.</span><span class="token function">printStackTrace</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
        <span class="token keyword">return</span> null<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token comment">/**
     * 带有过滤器的插入数据
     * @param tableName
     * @param rowkey
     * @param filterList
     * @return
     */</span>
    <span class="token keyword">public</span> <span class="token keyword">static</span> Result <span class="token function">getRow</span><span class="token punctuation">(</span>String tableName<span class="token punctuation">,</span> String rowkey<span class="token punctuation">,</span> FilterList filterList<span class="token punctuation">)</span><span class="token punctuation">{</span>
        <span class="token keyword">try</span><span class="token punctuation">(</span> Table table <span class="token operator">=</span> HBaseConn<span class="token punctuation">.</span><span class="token function">getTable</span><span class="token punctuation">(</span>tableName<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">{</span>
            Get get <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Get</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span>rowkey<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            get<span class="token punctuation">.</span><span class="token function">setFilter</span><span class="token punctuation">(</span>filterList<span class="token punctuation">)</span><span class="token punctuation">;</span>
            Result result <span class="token operator">=</span> table<span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span>get<span class="token punctuation">)</span><span class="token punctuation">;</span>
            System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"rowkey == "</span><span class="token operator">+</span>Bytes<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span>result<span class="token punctuation">.</span><span class="token function">getRow</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"basic:name == "</span><span class="token operator">+</span>Bytes<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span>result<span class="token punctuation">.</span><span class="token function">getValue</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"basic"</span><span class="token punctuation">)</span><span class="token punctuation">,</span> Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"name"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"basic:age == "</span><span class="token operator">+</span>Bytes<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span>result<span class="token punctuation">.</span><span class="token function">getValue</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"basic"</span><span class="token punctuation">)</span><span class="token punctuation">,</span> Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"age"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"basic:sex == "</span><span class="token operator">+</span>Bytes<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span>result<span class="token punctuation">.</span><span class="token function">getValue</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"basic"</span><span class="token punctuation">)</span><span class="token punctuation">,</span> Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"sex"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"basic:salary == "</span><span class="token operator">+</span>Bytes<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span>result<span class="token punctuation">.</span><span class="token function">getValue</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"extend"</span><span class="token punctuation">)</span><span class="token punctuation">,</span> Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"salary"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"basic:job == "</span><span class="token operator">+</span>Bytes<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span>result<span class="token punctuation">.</span><span class="token function">getValue</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"extend"</span><span class="token punctuation">)</span><span class="token punctuation">,</span> Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"job"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token keyword">return</span> result<span class="token punctuation">;</span>
        <span class="token punctuation">}</span><span class="token keyword">catch</span> <span class="token punctuation">(</span><span class="token class-name">Exception</span> e<span class="token punctuation">)</span><span class="token punctuation">{</span>
            e<span class="token punctuation">.</span><span class="token function">printStackTrace</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
        <span class="token keyword">return</span> null<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token comment">/**
     *scan扫描数据，
     * @param tableName
     * @return
     */</span>
    <span class="token keyword">public</span> <span class="token keyword">static</span> ResultScanner <span class="token function">getScanner</span><span class="token punctuation">(</span>String tableName<span class="token punctuation">)</span><span class="token punctuation">{</span>
        <span class="token keyword">try</span><span class="token punctuation">(</span> Table table <span class="token operator">=</span> HBaseConn<span class="token punctuation">.</span><span class="token function">getTable</span><span class="token punctuation">(</span>tableName<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">{</span>
            Scan scan <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Scan</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            scan<span class="token punctuation">.</span><span class="token function">setCaching</span><span class="token punctuation">(</span><span class="token number">1000</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            ResultScanner results <span class="token operator">=</span> table<span class="token punctuation">.</span><span class="token function">getScanner</span><span class="token punctuation">(</span>scan<span class="token punctuation">)</span><span class="token punctuation">;</span>
            results<span class="token punctuation">.</span><span class="token function">forEach</span><span class="token punctuation">(</span>result <span class="token operator">-</span><span class="token operator">&gt;</span> <span class="token punctuation">{</span>
                System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"rowkey == "</span><span class="token operator">+</span>Bytes<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span>result<span class="token punctuation">.</span><span class="token function">getRow</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
                System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"basic:name == "</span><span class="token operator">+</span>Bytes<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span>result<span class="token punctuation">.</span><span class="token function">getValue</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"basic"</span><span class="token punctuation">)</span><span class="token punctuation">,</span> Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"name"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
                System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"basic:age == "</span><span class="token operator">+</span>Bytes<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span>result<span class="token punctuation">.</span><span class="token function">getValue</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"basic"</span><span class="token punctuation">)</span><span class="token punctuation">,</span> Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"age"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
                System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"basic:sex == "</span><span class="token operator">+</span>Bytes<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span>result<span class="token punctuation">.</span><span class="token function">getValue</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"basic"</span><span class="token punctuation">)</span><span class="token punctuation">,</span> Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"sex"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
                System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"basic:salary == "</span><span class="token operator">+</span>Bytes<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span>result<span class="token punctuation">.</span><span class="token function">getValue</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"extend"</span><span class="token punctuation">)</span><span class="token punctuation">,</span> Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"salary"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
                System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"basic:job == "</span><span class="token operator">+</span>Bytes<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span>result<span class="token punctuation">.</span><span class="token function">getValue</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"extend"</span><span class="token punctuation">)</span><span class="token punctuation">,</span> Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"job"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token keyword">return</span> results<span class="token punctuation">;</span>
        <span class="token punctuation">}</span><span class="token keyword">catch</span> <span class="token punctuation">(</span><span class="token class-name">Exception</span> e<span class="token punctuation">)</span><span class="token punctuation">{</span>
            e<span class="token punctuation">.</span><span class="token function">printStackTrace</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
        <span class="token keyword">return</span> null<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token comment">/**
     *can 检索数据，控制startrow，stoprow 注意包括startrow 不包括stoprow，
     * @param tableName
     * @param startKey
     * @param stopKey
     * @return
     */</span>
    <span class="token keyword">public</span> <span class="token keyword">static</span> ResultScanner <span class="token function">getScanner</span><span class="token punctuation">(</span>String tableName<span class="token punctuation">,</span>String startKey<span class="token punctuation">,</span>String stopKey<span class="token punctuation">)</span><span class="token punctuation">{</span>
        <span class="token keyword">try</span><span class="token punctuation">(</span> Table table <span class="token operator">=</span> HBaseConn<span class="token punctuation">.</span><span class="token function">getTable</span><span class="token punctuation">(</span>tableName<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">{</span>
            Scan scan <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Scan</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            scan<span class="token punctuation">.</span><span class="token function">setStartRow</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span>startKey<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            scan<span class="token punctuation">.</span><span class="token function">setStopRow</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span>stopKey<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            scan<span class="token punctuation">.</span><span class="token function">setCaching</span><span class="token punctuation">(</span><span class="token number">1000</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            ResultScanner results <span class="token operator">=</span> table<span class="token punctuation">.</span><span class="token function">getScanner</span><span class="token punctuation">(</span>scan<span class="token punctuation">)</span><span class="token punctuation">;</span>
            results<span class="token punctuation">.</span><span class="token function">forEach</span><span class="token punctuation">(</span>result <span class="token operator">-</span><span class="token operator">&gt;</span> <span class="token punctuation">{</span>
                System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"rowkey == "</span><span class="token operator">+</span>Bytes<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span>result<span class="token punctuation">.</span><span class="token function">getRow</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
                System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"basic:name == "</span><span class="token operator">+</span>Bytes<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span>result<span class="token punctuation">.</span><span class="token function">getValue</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"basic"</span><span class="token punctuation">)</span><span class="token punctuation">,</span> Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"name"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
                System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"basic:age == "</span><span class="token operator">+</span>Bytes<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span>result<span class="token punctuation">.</span><span class="token function">getValue</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"basic"</span><span class="token punctuation">)</span><span class="token punctuation">,</span> Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"age"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
                System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"basic:sex == "</span><span class="token operator">+</span>Bytes<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span>result<span class="token punctuation">.</span><span class="token function">getValue</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"basic"</span><span class="token punctuation">)</span><span class="token punctuation">,</span> Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"sex"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
                System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"basic:salary == "</span><span class="token operator">+</span>Bytes<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span>result<span class="token punctuation">.</span><span class="token function">getValue</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"extend"</span><span class="token punctuation">)</span><span class="token punctuation">,</span> Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"salary"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
                System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"basic:job == "</span><span class="token operator">+</span>Bytes<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span>result<span class="token punctuation">.</span><span class="token function">getValue</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"extend"</span><span class="token punctuation">)</span><span class="token punctuation">,</span> Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"job"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token keyword">return</span> results<span class="token punctuation">;</span>
        <span class="token punctuation">}</span><span class="token keyword">catch</span> <span class="token punctuation">(</span><span class="token class-name">Exception</span> e<span class="token punctuation">)</span><span class="token punctuation">{</span>
            e<span class="token punctuation">.</span><span class="token function">printStackTrace</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
        <span class="token keyword">return</span> null<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token comment">/**
     * scan 检索数据，控制startrow，stoprow 注意包括startrow 不包括stoprow，filterList对查询过滤
     * @param tableName
     * @param startKey
     * @param stopKey
     * @param filterList
     * @return
     */</span>
    <span class="token keyword">public</span> <span class="token keyword">static</span> ResultScanner <span class="token function">getScanner</span><span class="token punctuation">(</span>String tableName<span class="token punctuation">,</span>String startKey<span class="token punctuation">,</span>String stopKey<span class="token punctuation">,</span>FilterList filterList<span class="token punctuation">)</span><span class="token punctuation">{</span>
        <span class="token keyword">try</span><span class="token punctuation">(</span> Table table <span class="token operator">=</span> HBaseConn<span class="token punctuation">.</span><span class="token function">getTable</span><span class="token punctuation">(</span>tableName<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">{</span>
            Scan scan <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Scan</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            scan<span class="token punctuation">.</span><span class="token function">setFilter</span><span class="token punctuation">(</span>filterList<span class="token punctuation">)</span><span class="token punctuation">;</span>
            scan<span class="token punctuation">.</span><span class="token function">setStartRow</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span>startKey<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            scan<span class="token punctuation">.</span><span class="token function">setStopRow</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span>stopKey<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            scan<span class="token punctuation">.</span><span class="token function">setCaching</span><span class="token punctuation">(</span><span class="token number">1000</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            ResultScanner results <span class="token operator">=</span> table<span class="token punctuation">.</span><span class="token function">getScanner</span><span class="token punctuation">(</span>scan<span class="token punctuation">)</span><span class="token punctuation">;</span>
            results<span class="token punctuation">.</span><span class="token function">forEach</span><span class="token punctuation">(</span>result <span class="token operator">-</span><span class="token operator">&gt;</span> <span class="token punctuation">{</span>
                System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"rowkey == "</span><span class="token operator">+</span>Bytes<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span>result<span class="token punctuation">.</span><span class="token function">getRow</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
                System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"basic:name == "</span><span class="token operator">+</span>Bytes<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span>result<span class="token punctuation">.</span><span class="token function">getValue</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"basic"</span><span class="token punctuation">)</span><span class="token punctuation">,</span> Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"name"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
                System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"basic:age == "</span><span class="token operator">+</span>Bytes<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span>result<span class="token punctuation">.</span><span class="token function">getValue</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"basic"</span><span class="token punctuation">)</span><span class="token punctuation">,</span> Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"age"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
                System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"basic:sex == "</span><span class="token operator">+</span>Bytes<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span>result<span class="token punctuation">.</span><span class="token function">getValue</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"basic"</span><span class="token punctuation">)</span><span class="token punctuation">,</span> Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"sex"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
                System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"basic:salary == "</span><span class="token operator">+</span>Bytes<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span>result<span class="token punctuation">.</span><span class="token function">getValue</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"extend"</span><span class="token punctuation">)</span><span class="token punctuation">,</span> Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"salary"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
                System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"basic:job == "</span><span class="token operator">+</span>Bytes<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span>result<span class="token punctuation">.</span><span class="token function">getValue</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"extend"</span><span class="token punctuation">)</span><span class="token punctuation">,</span> Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"job"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token keyword">return</span> results<span class="token punctuation">;</span>
        <span class="token punctuation">}</span><span class="token keyword">catch</span> <span class="token punctuation">(</span><span class="token class-name">Exception</span> e<span class="token punctuation">)</span><span class="token punctuation">{</span>
            e<span class="token punctuation">.</span><span class="token function">printStackTrace</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
        <span class="token keyword">return</span> null<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token comment">/**
     * 删除行
     * @param tableName
     * @param rowkey
     * @return
     */</span>
    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">boolean</span> <span class="token function">deleteRow</span><span class="token punctuation">(</span>String tableName<span class="token punctuation">,</span>String rowkey<span class="token punctuation">)</span><span class="token punctuation">{</span>
        <span class="token keyword">try</span><span class="token punctuation">(</span> Table table <span class="token operator">=</span> HBaseConn<span class="token punctuation">.</span><span class="token function">getTable</span><span class="token punctuation">(</span>tableName<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">{</span>
            Delete delete <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Delete</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span>rowkey<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            table<span class="token punctuation">.</span><span class="token function">delete</span><span class="token punctuation">(</span>delete<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span><span class="token keyword">catch</span> <span class="token punctuation">(</span><span class="token class-name">Exception</span> e<span class="token punctuation">)</span><span class="token punctuation">{</span>
            e<span class="token punctuation">.</span><span class="token function">printStackTrace</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
        <span class="token keyword">return</span> <span class="token boolean">true</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token comment">/**
     *删除列簇
     * @param tableName
     * @param cfName
     * @return
     */</span>
    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">boolean</span> <span class="token function">deleteColumnFamily</span><span class="token punctuation">(</span>String tableName<span class="token punctuation">,</span>String cfName<span class="token punctuation">)</span><span class="token punctuation">{</span>
        <span class="token keyword">try</span><span class="token punctuation">(</span>HBaseAdmin admin <span class="token operator">=</span> <span class="token punctuation">(</span>HBaseAdmin<span class="token punctuation">)</span>HBaseConn<span class="token punctuation">.</span><span class="token function">getHBaseConn</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">getAdmin</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">{</span>
            admin<span class="token punctuation">.</span><span class="token function">deleteColumn</span><span class="token punctuation">(</span>tableName<span class="token punctuation">,</span>cfName<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span><span class="token keyword">catch</span> <span class="token punctuation">(</span><span class="token class-name">Exception</span> e<span class="token punctuation">)</span><span class="token punctuation">{</span>
            e<span class="token punctuation">.</span><span class="token function">printStackTrace</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
        <span class="token keyword">return</span> <span class="token boolean">true</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    <span class="token comment">/**
     * 删除列
     * @param tableName
     * @param cfName
     * @return
    */</span>
    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">boolean</span> <span class="token function">deleteQualifier</span><span class="token punctuation">(</span>String tableName<span class="token punctuation">,</span>String rowkey<span class="token punctuation">,</span>String cfName<span class="token punctuation">,</span>String qualiferName<span class="token punctuation">)</span><span class="token punctuation">{</span>
        <span class="token keyword">try</span><span class="token punctuation">(</span>Table table <span class="token operator">=</span> HBaseConn<span class="token punctuation">.</span><span class="token function">getTable</span><span class="token punctuation">(</span>tableName<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">{</span>
            Delete delete <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Delete</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span>rowkey<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            delete<span class="token punctuation">.</span><span class="token function">addColumn</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span>cfName<span class="token punctuation">)</span><span class="token punctuation">,</span>Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span>qualiferName<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            table<span class="token punctuation">.</span><span class="token function">delete</span><span class="token punctuation">(</span>delete<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span><span class="token keyword">catch</span> <span class="token punctuation">(</span><span class="token class-name">Exception</span> e<span class="token punctuation">)</span><span class="token punctuation">{</span>
            e<span class="token punctuation">.</span><span class="token function">printStackTrace</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
        <span class="token keyword">return</span> <span class="token boolean">true</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>
<h3><a id="_340"></a>总结：</h3>
<ul>
<li>
<p>hbase 数据类型只有bytes类型，而关系型数据库 可以有多种多样的数据类型。所以我们根据rowkey，cf，qualifer等条件进行查询的时候，需要将对应的字段通过Bytes.toBytes（）方法进行封装。同样，对于返回的result，也需要通过Bytes.toString（）方法转为string对象。</p>
</li>
<li>
<p>hbase 不支持更新操作，要实现更新操作，只能够先删除该数据 然后重新插入。</p>
</li>
<li>
<p>我们可以看到某些情况下只需要Table实例，即可实现响应操作，但是某些情况需要HBaseAdmin实例，才能进行修改，删除等操作。具体什么时候，使用那种操作呢？通过前面文章HBase读写流程https://blog.csdn.net/zhangshk_/article/details/83663642应该不难明白。对于修改删除列簇，还有涉及表的创建删除等操作，是需要HBaseAdmin操作的，因为这些操作需要涉及meta表的更新。而其他的操作，只需要通过zookeeper获取数据的regionserver，通过regionserver就可完成读写请求。</p>
</li>
</ul>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>