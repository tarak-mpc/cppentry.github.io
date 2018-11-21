---
layout:     post
title:      IDEA构建UDF函数&注册到Hive源码&编译&验证及使用
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/Gavin_chun/article/details/78183719				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>一、注册到Hive源码目的 <br>
在上一篇UDF函数博文中说过，Hive自带的函数并不能满足日常工作，有很多功能需要我们自己去编写。对于经常使用的功能就希望注册到Hive当中和Hive的函数一样使用了。</p>

<p>二、IDEA构建UDF函数</p>

<p>请参考<a href="http://blog.csdn.net/gavin_chun/article/details/78169317" rel="nofollow" target="_blank">IDEA构建Hive的UDF函数</a></p>

<p>三、注册到Hive源码 <br>
1、<a href="http://archive.cloudera.com/cdh5/cdh/5/hive-1.1.0-cdh5.7.0-src.tar.gz" rel="nofollow" target="_blank">下载Hive源码</a></p>

<p>2、将Hive源码放到/opt/sourcecode目录下并解压</p>

<pre class="prettyprint"><code class=" hljs avrasm">[root@hadoop001 sourcecode]<span class="hljs-preprocessor"># ls</span>
hive-<span class="hljs-number">1.1</span><span class="hljs-number">.0</span>-cdh5<span class="hljs-number">.7</span><span class="hljs-number">.0</span>  hive-<span class="hljs-number">1.1</span><span class="hljs-number">.0</span>-cdh5<span class="hljs-number">.7</span><span class="hljs-number">.0</span>-src<span class="hljs-preprocessor">.tar</span><span class="hljs-preprocessor">.gz</span></code></pre>

<p>3、将IDEA构建的HelloUDF2.java放到以下目录（Hive自带函数目录）</p>

<pre class="prettyprint"><code class=" hljs lasso">/opt/sourcecode/hive<span class="hljs-subst">-</span><span class="hljs-number">1.1</span><span class="hljs-number">.0</span><span class="hljs-attribute">-cdh5</span><span class="hljs-number">.7</span><span class="hljs-number">.0</span>/ql/src/java/org/apache/hadoop/hive/ql/udf</code></pre>

<p>4、修改HelloUDF2.java <br>
vi  HelloUDF2.java </p>

<blockquote>
  <p>将package com.ruozedata.hive更改成package.org.apache.hadoop.hive.ql.udf</p>
</blockquote>

<p>5、修改FunctionRegistry.java 文件</p>

<pre class="prettyprint"><code class=" hljs bash">vi  /opt/sourcecode/hive-<span class="hljs-number">1.1</span>.<span class="hljs-number">0</span>-cdh5.<span class="hljs-number">7.0</span>/ql/src/java/org/apache/hadoop/hive/ql/<span class="hljs-keyword">exec</span>/FunctionRegistry.java</code></pre>

<p>在FunctionRegistry.java文件19行下面添加</p>

<pre class="prettyprint"><code class=" hljs avrasm">import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hive</span><span class="hljs-preprocessor">.ql</span><span class="hljs-preprocessor">.udf</span><span class="hljs-preprocessor">.HelloUDF</span>2<span class="hljs-comment">;</span></code></pre>

<p>在static{}代码块(174行)中添加</p>

<pre class="prettyprint"><code class=" hljs ruby">system.registerUDF(<span class="hljs-string">"HelloUDF2"</span>, <span class="hljs-constant">HelloUDF2</span>.<span class="hljs-keyword">class</span>, <span class="hljs-keyword">false</span>);</code></pre>

<p>四、开始编译 <br>
在/opt/sourcecode/hive-1.1.0-cdh5.7.0路径下执行</p>

<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-built_in">cd</span> /opt/sourcecode/hive-<span class="hljs-number">1.1</span>.<span class="hljs-number">0</span>-cdh5.<span class="hljs-number">7.0</span>
<span class="hljs-comment">#编译时间较长，耐心等待</span>
mvn clean package -DskipTests -Phadoop-<span class="hljs-number">2</span> -Pdist</code></pre>

<p>编译完成后会在/opt/sourcecode/hive-1.1.0-cdh5.7.0/packaging目录下生成一个target文件夹</p>

<p>target文件夹下的apache-hive-1.1.0-cdh5.7.0-bin.tar.gz包就是编译之后的文件</p>

<p>五、验证及使用 <br>
如果要使用自己构建的函数有两种方法 <br>
㈠将apache-hive-1.1.0-cdh5.7.0-bin.tar.gz部署到机器上，可以参考<a href="http://blog.csdn.net/gavin_chun/article/details/78074744" rel="nofollow" target="_blank">Hive简单部署</a></p>

<p>然后hive (default)&gt; show functions; <br>
可以看到helloudf2函数(add jar方式看不到)</p>

<p>查看函数的描述</p>

<pre class="prettyprint"><code class=" hljs oxygene">hive (<span class="hljs-keyword">default</span>)&gt; <span class="hljs-keyword">desc</span> <span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">extended</span> <span class="hljs-title">helloudf2</span>;</span>
OK
tab_name
helloudf2(input_str) - returns Hello:input
Example:
   &gt; <span class="hljs-keyword">SELECT</span> helloudf2(<span class="hljs-string">'zhangsan'</span>) <span class="hljs-keyword">FROM</span> src LIMIT <span class="hljs-number">1</span>;
  <span class="hljs-string">'Hello:zhangsan'</span>
Time taken: <span class="hljs-number">0.032</span> seconds, Fetched: <span class="hljs-number">4</span> row(s)</code></pre>

<p>㈡如果你已经部署了Hive了那么采用下面的方式 <br>
备份已部署的Hive的hive-exec-1.1.0-cdh5.7.0.jar文件</p>

<pre class="prettyprint"><code class=" hljs lasso">mv <span class="hljs-variable">$HIVE_HOME</span>/lib/hive<span class="hljs-attribute">-exec</span><span class="hljs-subst">-</span><span class="hljs-number">1.1</span><span class="hljs-number">.0</span><span class="hljs-attribute">-cdh5</span><span class="hljs-number">.7</span><span class="hljs-number">.0</span><span class="hljs-built_in">.</span>jar   <span class="hljs-variable">$HIVE_HOME</span>/lib/hive<span class="hljs-attribute">-exec</span><span class="hljs-subst">-</span><span class="hljs-number">1.1</span><span class="hljs-number">.0</span><span class="hljs-attribute">-cdh5</span><span class="hljs-number">.7</span><span class="hljs-number">.0</span><span class="hljs-built_in">.</span>jar<span class="hljs-built_in">.</span>bak </code></pre>

<p>将编译后的hive-exec-1.1.0-cdh5.7.0.jar文件拷贝到$HIVE_HOME/lib/目录下</p>

<pre class="prettyprint"><code class=" hljs lasso">cp hive<span class="hljs-attribute">-exec</span><span class="hljs-subst">-</span><span class="hljs-number">1.1</span><span class="hljs-number">.0</span><span class="hljs-attribute">-cdh5</span><span class="hljs-number">.7</span><span class="hljs-number">.0</span><span class="hljs-built_in">.</span>jar <span class="hljs-variable">$HIVE_HOME</span>/lib</code></pre>

<p>然后查看是否成功(有时需要hive-exec-1.1.0-cdh5.7.0.jar.bak文件移除lib才可以成功)</p>

<pre class="prettyprint"><code class=" hljs oxygene">hive (<span class="hljs-keyword">default</span>)&gt; show functions;   
hive (<span class="hljs-keyword">default</span>)&gt; <span class="hljs-keyword">desc</span> <span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">extended</span> <span class="hljs-title">helloudf2</span>;</span></code></pre>

<p>功能已实现</p>

<pre class="prettyprint"><code class=" hljs ruby">hive (default)&gt; select ename,helloudf2(ename) from emp;
<span class="hljs-constant">OK</span>
ename   _c1
<span class="hljs-constant">SMITH</span>   <span class="hljs-constant">Hello</span><span class="hljs-symbol">:SMITH</span>
<span class="hljs-constant">ALLEN</span>   <span class="hljs-constant">Hello</span><span class="hljs-symbol">:ALLEN</span>
<span class="hljs-constant">WARD</span>    <span class="hljs-constant">Hello</span><span class="hljs-symbol">:WARD</span>
<span class="hljs-constant">JONES</span>   <span class="hljs-constant">Hello</span><span class="hljs-symbol">:JONES</span>
<span class="hljs-constant">MARTIN</span>  <span class="hljs-constant">Hello</span><span class="hljs-symbol">:MARTIN</span>
<span class="hljs-constant">BLAKE</span>   <span class="hljs-constant">Hello</span><span class="hljs-symbol">:BLAKE</span>
<span class="hljs-constant">CLARK</span>   <span class="hljs-constant">Hello</span><span class="hljs-symbol">:CLARK</span>
<span class="hljs-constant">SCOTT</span>   <span class="hljs-constant">Hello</span><span class="hljs-symbol">:SCOTT</span>
<span class="hljs-constant">KING</span>    <span class="hljs-constant">Hello</span><span class="hljs-symbol">:KING</span>
<span class="hljs-constant">TURNER</span>  <span class="hljs-constant">Hello</span><span class="hljs-symbol">:TURNER</span>
<span class="hljs-constant">ADAMS</span>   <span class="hljs-constant">Hello</span><span class="hljs-symbol">:ADAMS</span>
<span class="hljs-constant">JAMES</span>   <span class="hljs-constant">Hello</span><span class="hljs-symbol">:JAMES</span>
<span class="hljs-constant">FORD</span>    <span class="hljs-constant">Hello</span><span class="hljs-symbol">:FORD</span>
<span class="hljs-constant">MILLER</span>  <span class="hljs-constant">Hello</span><span class="hljs-symbol">:MILLER</span>
<span class="hljs-constant">HIVE</span>    <span class="hljs-constant">Hello</span><span class="hljs-symbol">:HIVE</span></code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>