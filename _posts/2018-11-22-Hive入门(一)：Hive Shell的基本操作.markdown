---
layout:     post
title:      Hive入门(一)：Hive Shell的基本操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>相关内容： <br>
<a href="https://blog.csdn.net/m0_37324825/article/details/80324682" rel="nofollow">Hive入门(一)：Hive Shell的基本操作</a> <br>
<a href="https://blog.csdn.net/m0_37324825/article/details/80337693" rel="nofollow">Hive入门(二)：HQL数据定义</a> <br>
<a href="https://blog.csdn.net/m0_37324825/article/details/80404821" rel="nofollow">Hive入门(三)：HQL数据操作——数据装载与保存</a> <br>
<a href="https://blog.csdn.net/m0_37324825/article/details/80485002" rel="nofollow">Hive入门(四)：HQL查询语句（一）</a> <br>
<a href="https://blog.csdn.net/m0_37324825/article/details/80489872" rel="nofollow">Hive入门(四)：HQL查询语句（二）</a></p>

<h1 id="hive入门一hive-shell的基本操作">Hive入门(一)：Hive Shell的基本操作</h1>

<blockquote>
  <p>总结Hive基本的命令行操作</p>
</blockquote>

<p>进入Hive部署包的bin目录，在命令行输入./hive启动hive cli。</p>



<pre class="prettyprint"><code class="language-shell hljs ruby"><span class="hljs-variable">$ </span>cd /user/home/apache-hive-<span class="hljs-number">2.2</span>.<span class="hljs-number">0</span>-bin/bin
<span class="hljs-variable">$ </span>./hive</code></pre>



<h2 id="变量和属性">变量和属性</h2>

<table>
<thead>
<tr>
  <th>命名空间</th>
  <th>使用权限</th>
  <th>描述</th>
</tr>
</thead>
<tbody><tr>
  <td>hivevar</td>
  <td>读/写</td>
  <td>用户自定义变量</td>
</tr>
<tr>
  <td>env</td>
  <td>只读</td>
  <td>shell环境定义的环境变量,比如~/.bashrc中定义的左右环境变量，如“HADOOP_HOME=/user/stack/hadoop-2.6.0”。</td>
</tr>
<tr>
  <td>hiveconf</td>
  <td>读/写</td>
  <td>hive相关的配置属性</td>
</tr>
<tr>
  <td>system</td>
  <td>读/写</td>
  <td>java相关的配置属性,比如“system:java.version=1.8.0_40”</td>
</tr>
</tbody></table>


<p>set用来显示和修改变量。 <br>
显示单个变量。</p>



<pre class="prettyprint"><code class="language-shell hljs avrasm">hive&gt; <span class="hljs-keyword">set</span> system:user<span class="hljs-preprocessor">.dir</span><span class="hljs-comment">;</span>
<span class="hljs-label">system:</span>user<span class="hljs-preprocessor">.dir</span>=/home/stack/apache-hive-<span class="hljs-number">2.2</span><span class="hljs-number">.0</span>-bin/bin</code></pre>

<p>如果只使用set，则显示所有变量和属性，包括varconf，env，system和hiveconf。如果加上-v还可以显示hadoop相关的信息。</p>



<h3 id="hivevar">hivevar</h3>



<h4 id="创建变量">创建变量</h4>

<p><strong>方法1：</strong> <br>
在启动hive时，通过–define key=value 或者 –hivevar key=value的形式创建变量。</p>



<pre class="prettyprint"><code class="language-shell hljs bash">$ hive --define testkey=testvalue //或者使用 --hivevar或<span class="hljs-operator">-d</span>
hive&gt; <span class="hljs-keyword">set</span> hivevar:testkey; //此处的hivevar:可省略，即直接使用<span class="hljs-keyword">set</span> 
hivevar:testkey=testvalue</code></pre>

<p><strong>方法2：</strong> <br>
在启动hive后，通过set创建变量。</p>



<pre class="prettyprint"><code class="language-shell hljs bash">hive&gt; <span class="hljs-keyword">set</span> hivevar:testkey2=testvalue2;</code></pre>



<h4 id="修改变量">修改变量</h4>



<pre class="prettyprint"><code class="language-shell hljs bash">hive&gt; <span class="hljs-keyword">set</span> testkey=newvalue;
hive&gt; <span class="hljs-keyword">set</span> testkey;
testkey=newvalue</code></pre>

<p><code>Note</code>: 在Hive处理一条查询语句之前，会将查询语句中的变量替换成相应的值，然后再处理。但是set创建变量时hivevar不可以省略。否则，在执行create table语句时，会报错“AILED: ParseException line 1:23 cannot recognize input near ‘$’ ‘{’ ‘label’ in column name or primary key or foreign key”。</p>



<pre class="prettyprint"><code class="language-shell hljs delphi">hive&gt; <span class="hljs-keyword">set</span> hivevar:<span class="hljs-keyword">label</span>=id;  <span class="hljs-comment">//不能使用set label=id。</span>
hive&gt; <span class="hljs-keyword">set</span> <span class="hljs-keyword">label</span>;
<span class="hljs-keyword">label</span>=id
hive&gt; create table student($<span class="hljs-comment">{hivevar:label}</span> int, name <span class="hljs-keyword">string</span>);
hive&gt; describe student;
id                      int                                         
name                    <span class="hljs-keyword">string</span>
hive&gt; drop table student; <span class="hljs-comment">//删除测试表student。</span></code></pre>



<h3 id="hiveconf">hiveconf</h3>

<p>用于进行hive相关的配置。包含两种方式： <br>
<strong>方法1：</strong> <br>
Hive启动时，通过<code>--hiveconf</code>配置。比如配置显示当前所在的数据库，该值默认为false。</p>



<pre class="prettyprint"><code class="language-shell hljs avrasm">./hive --hiveconf hive<span class="hljs-preprocessor">.cli</span><span class="hljs-preprocessor">.print</span><span class="hljs-preprocessor">.current</span><span class="hljs-preprocessor">.db</span>=true
hive (default)&gt;</code></pre>

<p><strong>方法2：</strong> <br>
Hive启动后，通过<code>set</code>设置。</p>



<pre class="prettyprint"><code class="language-shell hljs avrasm">hive (default)&gt; hive<span class="hljs-preprocessor">.cli</span><span class="hljs-preprocessor">.print</span><span class="hljs-preprocessor">.current</span><span class="hljs-preprocessor">.db</span>=true
hive&gt;</code></pre>



<h2 id="hql执行方式">HQL执行方式</h2>

<p>方法1：启动Hive cli运行。 <br>
方法2：通过添加<code>-e</code>的参数执行一次HQL语句。<code>-S</code>参数用来去掉输出中的执行时间“Time taken:   seconds”和“OK”，仅保留执行结果。下面的语句将show databases的结果输出到showdbs的文本文件中。然后，用<code>cat</code>输出showdb的内容。</p>



<pre class="prettyprint"><code class="language-shell hljs ruby"><span class="hljs-variable">$ </span>hive -<span class="hljs-constant">S</span> -e <span class="hljs-string">"show databases"</span> &gt; ./showdbs
<span class="hljs-variable">$ </span>cat showdbs 
default
testdb</code></pre>

<p>另外，在用<code>set</code>查看属性和变量值时，可以用如下语句进行模糊查询。比如想查询jvm有关的内容。</p>



<pre class="prettyprint"><code class="language-shell hljs lasso">$ hive <span class="hljs-attribute">-S</span> <span class="hljs-attribute">-e</span> <span class="hljs-string">"set"</span> <span class="hljs-subst">|</span> grep <span class="hljs-string">"jvm"</span></code></pre>

<p>方法三：通过添加<code>-f</code>执行指定文件。首先，先写一个<code>.hql</code>文件。</p>



<pre class="prettyprint"><code class="language-shell hljs ruby"><span class="hljs-variable">$ </span>echo <span class="hljs-string">"show databases"</span> &gt; ./showdb.hql
<span class="hljs-variable">$ </span></code></pre>

<p>然后启动Hive cli，用<code>source</code>来加载执行<code>.hql</code>文件。</p>



<pre class="prettyprint"><code class="language-shell hljs haskell"><span class="hljs-title">hive</span>&gt; source ./showdb.hql;
<span class="hljs-type">OK</span>
<span class="hljs-default"><span class="hljs-keyword">default</span></span>
<span class="hljs-title">testdb</span>
<span class="hljs-type">Time</span> taken: <span class="hljs-number">1.106</span> seconds, <span class="hljs-type">Fetched</span>: <span class="hljs-number">2</span> row(s)</code></pre>

<p>方法四：在Hive cli启动时通过<code>-i</code>来执行指定文件。这个选项很适合用于添加jar包以及设定Hive相关的配置参数。如下，hive会在Hive部署包存放目录下搜索my.hiverc文件，并自动执行里面的hql语句。因此，启动hive cli后，设置便生效。</p>



<pre class="prettyprint"><code class="language-shell hljs ruby"><span class="hljs-variable">$ </span>echo <span class="hljs-string">"set hive.cli.print.current.db=true;"</span> &gt; ./my.hiverc
<span class="hljs-variable">$ </span>hive -i my.hiverc
hive (default)&gt;</code></pre>

<p>另外，添加jar的形式为<code>ADD JAR /path/my.jar</code>。每条语句依然用<code>;</code>结尾。</p>



<h2 id="hive-shell的其他用法">Hive shell的其他用法</h2>

<p>1.直接在hive shell中执行一些简单的命令，只要在命令前加上<code>!</code>。 <br>
比如，显示当前目录。</p>



<pre class="prettyprint"><code class="language-shell hljs ruby"><span class="hljs-variable">$ </span>! pwd;
<span class="hljs-regexp">/home/stack</span><span class="hljs-regexp">/apache-hive-2.2.0-bin/bin</span></code></pre>

<p>2.直接在Hive shell中操作hdfs。只要去掉命令前面的hdfs即可。 <br>
比如，显示warehouse的文件。</p>



<pre class="prettyprint"><code class="language-shell hljs lasso">hive<span class="hljs-subst">&gt;</span> dfs <span class="hljs-attribute">-ls</span> /user/hive/warehouse<span class="hljs-subst">/</span>;
Found <span class="hljs-number">1</span> items
drwxrwxrwx   <span class="hljs-subst">-</span> <span class="hljs-built_in">stack</span> supergroup          <span class="hljs-number">0</span> <span class="hljs-number">2018</span><span class="hljs-subst">-</span><span class="hljs-number">05</span><span class="hljs-subst">-</span><span class="hljs-number">15</span> <span class="hljs-number">15</span>:<span class="hljs-number">16</span> /user/hive/warehouse/testdb<span class="hljs-built_in">.</span>db</code></pre>

<p><code>Note</code>: 在hive shell中操作hdfs更快，因为hdfs dfs的用法每次操作都需要重新启动一个jvm实例，而Hive在同一个进程执行这些操作。</p>



<h4 id="其他">其他</h4>

<p>注释： <br>
通过–作为前缀来进行注释。在cli，<code>.hql</code>和<code>.hiverc</code>均适用。比如在<code>.hiverc</code>中添加注释，如下：</p>



<pre class="prettyprint"><code class="language-shell hljs sql"><span class="hljs-comment">-- this is my comment, show current database</span>
<span class="hljs-operator"><span class="hljs-keyword">set</span> hive.cli.print.<span class="hljs-keyword">current</span>.db=<span class="hljs-keyword">true</span>;</span></code></pre>

<p>历史操作： <br>
最近的100条历史操作保存在<code>$HOME/.hivehistory</code>。</p>



<pre class="prettyprint"><code class="language-shell hljs ruby"><span class="hljs-variable">$ </span>vi <span class="hljs-variable">$HOME</span>/.hivehistory</code></pre>



<h3 id="目录">目录</h3>

<p></p><div class="toc"><div class="toc">
<ul>
<li><a href="#hive%E5%85%A5%E9%97%A8%E4%B8%80hive-shell%E7%9A%84%E5%9F%BA%E6%9C%AC%E6%93%8D%E4%BD%9C" rel="nofollow">Hive入门(一)：Hive Shell的基本操作</a><ul>
<li><a href="#%E5%8F%98%E9%87%8F%E5%92%8C%E5%B1%9E%E6%80%A7" rel="nofollow">变量和属性</a><ul>
<li><a href="#hivevar" rel="nofollow">hivevar</a><ul>
<li><a href="#%E5%88%9B%E5%BB%BA%E5%8F%98%E9%87%8F" rel="nofollow">创建变量</a></li>
<li><a href="#%E4%BF%AE%E6%94%B9%E5%8F%98%E9%87%8F" rel="nofollow">修改变量</a></li>
</ul>
</li>
<li><a href="#hiveconf" rel="nofollow">hiveconf</a></li>
</ul>
</li>
<li><a href="#hql%E6%89%A7%E8%A1%8C%E6%96%B9%E5%BC%8F" rel="nofollow">HQL执行方式</a></li>
<li><a href="#hive-shell%E7%9A%84%E5%85%B6%E4%BB%96%E7%94%A8%E6%B3%95" rel="nofollow">Hive shell的其他用法</a><ul>
<li><ul>
<li><a href="#%E5%85%B6%E4%BB%96" rel="nofollow">其他</a></li>
</ul>
</li>
<li><a href="#%E7%9B%AE%E5%BD%95" rel="nofollow">目录</a></li>
</ul>
</li>
</ul>
</li>
</ul>
</div>
</div>
            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>