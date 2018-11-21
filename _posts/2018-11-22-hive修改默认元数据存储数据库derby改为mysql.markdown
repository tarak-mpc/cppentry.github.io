---
layout:     post
title:      hive修改默认元数据存储数据库derby改为mysql
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h4 id="进入mysql">进入mysql</h4>



<pre class="prettyprint"><code class=" hljs lasso">mysql <span class="hljs-attribute">-uroot</span> <span class="hljs-attribute">-p</span> <span class="hljs-comment">//默认没有密码</span></code></pre>

<p><strong>为hive建立相应的用户以及密码</strong></p>



<pre class="prettyprint"><code class=" hljs sql"> <span class="hljs-operator"><span class="hljs-keyword">CREATE</span> <span class="hljs-keyword">USER</span> <span class="hljs-string">'hive'</span> IDENTIFIED <span class="hljs-keyword">BY</span> <span class="hljs-string">'mysql'</span>;</span></code></pre>

<p><strong>授权hive用户访问权限</strong></p>



<pre class="prettyprint"><code class=" hljs sql"> <span class="hljs-operator"><span class="hljs-keyword">GRANT</span> <span class="hljs-keyword">ALL</span> <span class="hljs-keyword">PRIVILEGES</span> <span class="hljs-keyword">ON</span> *.* <span class="hljs-keyword">TO</span> <span class="hljs-string">'hive'</span>@<span class="hljs-string">'%'</span> <span class="hljs-keyword">WITH</span> <span class="hljs-keyword">GRANT</span> <span class="hljs-keyword">OPTION</span>;</span></code></pre>

<p><strong>创建hive数据库</strong></p>



<pre class="prettyprint"><code class=" hljs sql">mysql -uhive -pmysql//以hive用户进入mysql
<span class="hljs-operator"><span class="hljs-keyword">create</span> <span class="hljs-keyword">database</span> hive;</span></code></pre>

<p><strong>将mysql的jdbc驱动放置到hive的lib目录下</strong></p>



<pre class="prettyprint"><code class=" hljs avrasm">wget http://dev<span class="hljs-preprocessor">.mysql</span><span class="hljs-preprocessor">.com</span>/get/Downloads/Connector-J/mysql-connector-java-<span class="hljs-number">5.1</span><span class="hljs-number">.11</span><span class="hljs-preprocessor">.tar</span><span class="hljs-preprocessor">.gz</span>/from/http://mysql<span class="hljs-preprocessor">.he</span><span class="hljs-preprocessor">.net</span>/
tar -xvzf mysql-connector-java-<span class="hljs-number">5.1</span><span class="hljs-number">.11</span><span class="hljs-preprocessor">.tar</span><span class="hljs-preprocessor">.gz</span>
<span class="hljs-keyword">cp</span> mysql-connector-java-<span class="hljs-number">5.1</span><span class="hljs-number">.11</span><span class="hljs-comment">/*.jar $HIVE_HOME/lib</span></code></pre>

<hr>



<h4 id="接下来配置hivesitexml">接下来配置hive.site.xml</h4>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-pi">&lt;?xml version="1.0"?&gt;</span>
<span class="hljs-pi">&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt;</span>

<span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hive.metastore.local<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>true<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>javax.jdo.option.ConnectionURL<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>jdbc:mysql://localhost:3306/hive?characterEncoding=UTF-8<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>javax.jdo.option.ConnectionDriverName<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>com.mysql.jdbc.Driver<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>javax.jdo.option.ConnectionUserName<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>hive<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>javax.jdo.option.ConnectionPassword<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>mysql<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>

<p><strong>修改为mysql</strong></p>



<pre class="prettyprint"><code class=" hljs lasso">schematool <span class="hljs-attribute">-initSchema</span> <span class="hljs-attribute">-dbType</span> mysql</code></pre>

<p>遇到的问题：</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">Warning: Using <span class="hljs-operator">a</span> password <span class="hljs-command"><span class="hljs-keyword">on</span> <span class="hljs-title">the</span> <span class="hljs-title">command</span> <span class="hljs-title">line</span> <span class="hljs-title">interface</span> <span class="hljs-title">can</span> <span class="hljs-title">be</span> <span class="hljs-title">insecure</span>.</span>
ERROR <span class="hljs-number">1045</span> (<span class="hljs-number">28000</span>): Access denied <span class="hljs-keyword">for</span> user <span class="hljs-string">'hive'</span>@<span class="hljs-string">'localhost'</span> (<span class="hljs-keyword">using</span> password: YES)
</code></pre>

<p>这是由于我们设置的时候允许全网段的访问，忽略了localhost</p>

<p>可以这样改（为其添加一个localhost）：</p>

<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">grant</span> <span class="hljs-keyword">all</span> <span class="hljs-keyword">privileges</span> <span class="hljs-keyword">on</span> *.* <span class="hljs-keyword">to</span> hive@localhost identified <span class="hljs-keyword">by</span> <span class="hljs-string">'pass'</span> <span class="hljs-keyword">with</span> <span class="hljs-keyword">grant</span> <span class="hljs-keyword">option</span>;</span></code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>