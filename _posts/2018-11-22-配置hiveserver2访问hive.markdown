---
layout:     post
title:      配置hiveserver2访问hive
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p></p><div class="toc">
<ul>
<li><a href="#%E7%9B%AE%E5%BD%95" rel="nofollow">目录：</a><ul>
<li><a href="#1%E5%89%8D%E8%A8%80" rel="nofollow">1、前言</a></li>
<li><a href="#2beeline%E7%9B%B8%E5%85%B3%E7%9A%84serverthrift%E9%85%8D%E7%BD%AE" rel="nofollow">2、beeline相关的Server.Thrift配置</a></li>
<li><a href="#3%E5%90%AF%E5%8A%A8beeline%E5%B9%B6%E8%AE%BF%E9%97%AEhive" rel="nofollow">3、启动beeline并访问Hive</a></li>
<li><a href="#4%E6%9C%9F%E9%97%B4%E9%81%87%E5%88%B0%E7%9A%84%E9%97%AE%E9%A2%98%E5%92%8C%E8%A7%A3%E5%86%B3%E6%96%B9%E6%B3%95" rel="nofollow">4、期间遇到的问题和解决方法</a></li>
</ul>
</li>
</ul>
</div>


<h1 id="目录">目录：</h1>

<h2 id="1前言">1、前言</h2>

<p>作为数据仓库的工具，hive提供了两种ETL运行方式，分别是通过Hive 命令行和beeline客户端；</p>

<p>命令行方式即通过hive进入命令模式后通过执行不同的HQL命令得到对应的结果；相当于胖客户端模式，即客户机中需要安装JRE环境和Hive程序。</p>

<p>beeline客户端方式相当于瘦客户端模式，采用JDBC方式借助于Hive Thrift服务访问Hive数据仓库。</p>

<p>HiveThrift(HiveServer)是Hive中的组件之一，设计目的是为了实现跨语言轻量级访问Hive数据仓库，有Hiveserver和 Hiveserver2两个版本，两者不兼容，使用中要注意区分。体现在启动HiveServer的参数和jdbc:hiveX的参数上。</p>

<h2 id="2beeline相关的serverthrift配置">2、beeline相关的Server.Thrift配置</h2>

<p>主要是hive/conf/hive-site.xml中hive.server2.thrift相关的一些配置项，但要注意一致性。</p>

<pre class="prettyprint"><code class=" hljs xml"> <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hive.server2.thrift.bind.host<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>ha1<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">description</span>&gt;</span>Bind host on which to run the HiveServer2 Thrift service.<span class="hljs-tag">&lt;/<span class="hljs-title">description</span>&gt;</span>
  <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

  <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hive.server2.thrift.port<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>10000<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">description</span>&gt;</span>Port number of HiveServer2 Thrift interface when hive.server2.transport.mode is 'binary'.<span class="hljs-tag">&lt;/<span class="hljs-title">description</span>&gt;</span>
  <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
</code></pre>

<p>进入beeline连接数据库后，因为要访问的文件在HDFS上，对应的路径有访问权限限制，所以，这里要设成hadoop中的用户名，实例中用户名即为’root’(看你用什么用户来启动hadoop的)。如果使用其它用户名，可能会报权限拒绝的错误。或通过修改hadoop中的配置项hadoop.proxyuser.ＸＸ为“*”　来放宽用户名和权限，如示例。 <br>
hadoop/etc/hadoop/core-site.xml</p>



<pre class="prettyprint"><code class=" hljs xml">
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hadoop.proxyuser.hadoop.hosts<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-comment">&lt;!--value&gt;master&lt;/value--&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>*<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hadoop.proxyuser.hadoop.groups<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-comment">&lt;!--value&gt;hadoop&lt;/value--&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>*<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>

<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span></code></pre>

<h2 id="3启动beeline并访问hive">3、启动beeline并访问Hive</h2>

<p>slave01上启动hiveserver2,   </p>

<pre class="prettyprint"><code class=" hljs ruby">nohup hive --service hiveserver2 &amp; 
nohup hiveserver2 <span class="hljs-number">1</span>&gt;<span class="hljs-regexp">/root/apps</span><span class="hljs-regexp">/hive-2.1.1/logs</span><span class="hljs-regexp">/hiveserver.log 2&gt;/root</span><span class="hljs-regexp">/apps/hive</span>-<span class="hljs-number">2.1</span>.<span class="hljs-number">1</span>/logs/hiveserver.err &amp;（将hivesever的日志导入对应的目录）
</code></pre>



<pre class="prettyprint"><code class=" hljs 1c">ps -ef <span class="hljs-string">| grep Hive </span></code></pre>

<p>能看到Hiveserver2已启动</p>

<p>master机器上执行beeline并访问hive</p>



<pre class="prettyprint"><code class=" hljs lasso">root@master:~/bigdata/hive$ beeline
Beeline version <span class="hljs-number">1.2</span><span class="hljs-number">.1</span><span class="hljs-built_in">.</span>spark2 <span class="hljs-keyword">by</span> Apache Hive
beeline<span class="hljs-subst">&gt;</span> 

beeline<span class="hljs-subst">&gt;</span> <span class="hljs-subst">!</span>connect jdbc:hive2:<span class="hljs-comment">//ha1:10000       // 2中配置项的host:port ，因为启动的是hiveserver2，所以参数中是hive2</span>
Connecting <span class="hljs-keyword">to</span> jdbc:hive2:<span class="hljs-comment">//ndh-ha1:10000</span>
Enter username for jdbc:hive2:<span class="hljs-comment">//ndh-ha1:10000: root</span>
Enter password for jdbc:hive2:<span class="hljs-comment">//ndh-ha1:10000: ****        //2中配置项的user/password </span>
<span class="hljs-number">17</span>/<span class="hljs-number">09</span>/<span class="hljs-number">08</span> <span class="hljs-number">14</span>:<span class="hljs-number">39</span>:<span class="hljs-number">27</span> INFO jdbc<span class="hljs-built_in">.</span>Utils: Supplied authorities: ndh<span class="hljs-attribute">-slave01</span>:<span class="hljs-number">10000</span>
<span class="hljs-number">17</span>/<span class="hljs-number">09</span>/<span class="hljs-number">08</span> <span class="hljs-number">14</span>:<span class="hljs-number">39</span>:<span class="hljs-number">27</span> INFO jdbc<span class="hljs-built_in">.</span>Utils: Resolved authority: ndh<span class="hljs-attribute">-slave01</span>:<span class="hljs-number">10000</span>
<span class="hljs-number">17</span>/<span class="hljs-number">09</span>/<span class="hljs-number">08</span> <span class="hljs-number">14</span>:<span class="hljs-number">39</span>:<span class="hljs-number">27</span> INFO jdbc<span class="hljs-built_in">.</span>HiveConnection: Will try <span class="hljs-keyword">to</span> open client transport <span class="hljs-keyword">with</span> JDBC Uri: jdbc:hive2:<span class="hljs-comment">//ha1:10000</span>
Connected <span class="hljs-keyword">to</span>: Apache Hive (version <span class="hljs-number">2.1</span><span class="hljs-number">.1</span>)
Driver: Hive JDBC (version <span class="hljs-number">1.2</span><span class="hljs-number">.1</span><span class="hljs-built_in">.</span>spark2)
Transaction isolation: TRANSACTION_REPEATABLE_READ
<span class="hljs-number">0</span>: jdbc:hive2:<span class="hljs-comment">//ha1:10000&gt; </span>

<span class="hljs-number">0</span>: jdbc:hive2:<span class="hljs-comment">//slave01:10000&gt; show databases;</span>
<span class="hljs-subst">+----------------+--+</span>
<span class="hljs-subst">|</span> database_name  <span class="hljs-subst">|</span>
<span class="hljs-subst">+----------------+--+</span>
<span class="hljs-subst">|</span> default        <span class="hljs-subst">|</span>
<span class="hljs-subst">|</span> feigu3         <span class="hljs-subst">|</span>
<span class="hljs-subst">|</span> wordcount      <span class="hljs-subst">|</span>
<span class="hljs-subst">+----------------+--+</span>
<span class="hljs-number">3</span> <span class="hljs-keyword">rows</span> selected (<span class="hljs-number">0.379</span> seconds)



<span class="hljs-number">0</span>: jdbc:hive2:<span class="hljs-comment">//slave01:10000&gt; select * from wordcount order by count desc limit 50;</span>

<span class="hljs-attribute">...</span><span class="hljs-attribute">...</span><span class="hljs-attribute">...</span><span class="hljs-attribute">...</span><span class="hljs-attribute">...</span><span class="hljs-attribute">...</span><span class="hljs-attribute">...</span><span class="hljs-attribute">...</span><span class="hljs-attribute">...</span><span class="hljs-attribute">...</span><span class="hljs-attribute">...</span><span class="hljs-attribute">...</span><span class="hljs-attribute">...</span><span class="hljs-attribute">...</span><span class="hljs-built_in">..</span>

<span class="hljs-attribute">...</span><span class="hljs-attribute">...</span><span class="hljs-attribute">...</span><span class="hljs-attribute">...</span><span class="hljs-attribute">...</span><span class="hljs-attribute">...</span><span class="hljs-attribute">...</span><span class="hljs-attribute">...</span><span class="hljs-attribute">...</span><span class="hljs-attribute">...</span><span class="hljs-attribute">...</span><span class="hljs-attribute">...</span><span class="hljs-attribute">...</span><span class="hljs-attribute">...</span><span class="hljs-attribute">...</span>


看到结果后，进入hadoop yarn
http:<span class="hljs-comment">//master:8088/cluster/apps/FINISHED　可看到刚执行的任务。</span>





<span class="hljs-number">0</span>: jdbc:hive2:<span class="hljs-comment">//slave01:10000&gt; !q　　　　//// 退出beeline</span>


</code></pre>



<h2 id="4期间遇到的问题和解决方法">4、期间遇到的问题和解决方法</h2>

<p>4.1    hadoop is not allowed to impersonate hive // hadoop是hadoop中配置的用户名，解决方法见２中说明； <br>
具体的原因见：<a href="https://blog.csdn.net/qq_16633405/article/details/82190440" rel="nofollow">https://blog.csdn.net/qq_16633405/article/details/82190440</a></p>

<p>转自：<a href="https://blog.csdn.net/wqhlmark64/article/details/77894026" rel="nofollow">https://blog.csdn.net/wqhlmark64/article/details/77894026</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>