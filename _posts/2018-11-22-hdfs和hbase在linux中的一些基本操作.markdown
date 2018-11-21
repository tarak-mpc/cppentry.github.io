---
layout:     post
title:      hdfs和hbase在linux中的一些基本操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h3 id="hdfs基本操作">hdfs基本操作</h3>

<p>hdfs作为hadoop分布式文件系统的实现接下来将讲解一些它的基本操作</p>



<pre class="prettyprint"><code class=" hljs scss"><span class="hljs-comment">//列出所有的文件夹</span>
hadoop fs -ls
<span class="hljs-comment">//新建文件</span>
hadoop fs -mkdir ／<span class="hljs-tag">input</span>（路径）
<span class="hljs-comment">//上传本地文件到hdfs</span>
hadoop fs copyFromLocal <span class="hljs-attr_selector">[本地文件]</span> <span class="hljs-attr_selector">[hdfs中的路径]</span>
<span class="hljs-comment">//查看hdfs中的文件内容</span>
hadoop fs -cat <span class="hljs-attr_selector">[hdfs中文件的路径]</span></code></pre>

<p>当然可以痛殴网页 <br>
<a href="http://localhost:50070/" rel="nofollow">http://localhost:50070/</a>对其中的信息进行查看</p>



<h3 id="hbase基本操作">hbase基本操作</h3>

<p>hbase作为一个分布式的，本地化，面向列的，多维度的存储系统，具有高性能和高可用性 <br>
主要包括如下操作： <br>
* get：获取制定行的版本信息，获取指定行和指定列簇的所有column <br>
* put：键值不存在时，在表中添加新航，如果键值已经存在，则更新对应值即可 <br>
* scan：允许在多行上迭代以查找特定值，该特定值可以包括正航或其任意子集 <br>
* delete：删除一行或者指定列簇</p>



<pre class="prettyprint"><code class=" hljs sql">在hbase的bin文件夹中通过shell文件启动hbase
./<span class="hljs-operator"><span class="hljs-keyword">start</span>-hbase.sh

通过hbase shell命令进入shell进行相应的操作

status命令查看当前分布式状态
version查看版本号
whoami返回当前登陆用户的id
list查看当前所有的表信息
<span class="hljs-keyword">create</span>创建表
eg(创建一个表为employee ，列簇为professional和personal):<span class="hljs-keyword">create</span> <span class="hljs-string">'employee'</span>,<span class="hljs-string">'professional'</span>,<span class="hljs-string">'personal'</span>

<span class="hljs-keyword">describe</span> <span class="hljs-keyword">describe</span> <span class="hljs-string">'employee'</span>:了解employee表的结构，将以javascript对象返回，列族按升序排列

<span class="hljs-keyword">drop</span>：删除表
<span class="hljs-keyword">alter</span>：修改表
disable：在<span class="hljs-keyword">drop</span>和<span class="hljs-keyword">alter</span>之前都要改变使得要操作的表失效
put: 插入信息
eg：put <span class="hljs-string">'employee'</span>,<span class="hljs-string">'vishal'</span>,<span class="hljs-string">'personal:telno'</span>,<span class="hljs-string">'12123123'</span>,
向表emplyee中vishal行中插入两列
同时通过put操作可以覆盖原来的信息，既可以实现更新
scan <span class="hljs-string">'employee'</span>,{VERSIONS =&gt;<span class="hljs-number">1</span> } versions 后的数字可以通过describle进行查看，通过scan返回不同用户的所有版本信息
<span class="hljs-keyword">get</span> <span class="hljs-string">'employee'</span>,<span class="hljs-string">'vishal'</span>查看信息（返回最新的信息）
<span class="hljs-keyword">get</span> <span class="hljs-string">'employee'</span>,<span class="hljs-string">'vishal'</span>,{COLUMNS=&gt;<span class="hljs-string">'personal:localtion'</span>,VERSIONS=&gt;<span class="hljs-number">3</span>}（返回该版本该行的对应列的所有信息）</span></code></pre>

<p><a href="http://localhost:60010/" rel="nofollow">http://localhost:60010/</a>通过网页查看hbase的想信息</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>