---
layout:     post
title:      Hive内嵌模式安装（基于Hadoop）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/dpengwang/article/details/81413252				</div>
								            <div id="content_views" class="markdown_views prism-github-gist">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>下载解压到指定目录</p>

<p>（1）配置环境变量 /etc/profile</p>

<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-keyword">export</span> HIVE_HOME=/usr/local/hive/

<span class="hljs-keyword">export</span> PATH=<span class="hljs-variable">$PATH</span>:<span class="hljs-variable">$HIVE_HOME</span>/bin</code></pre>

<p>（2）修改hive配置文件 hive-env.sh</p>

<p>加入hadoop路径 hive路径 hive的lib路径</p>



<pre class="prettyprint"><code class=" hljs vala"><span class="hljs-preprocessor">#HADOOP_HOME=${bin}/../../hadoop</span>

HADOOP_HOME=/usr/local/hadoop


<span class="hljs-preprocessor"># HiveConfiguration Directory can be controlled by:</span>
 export HIVE_CONF_DIR=/usr/local/hive/conf


<span class="hljs-preprocessor"># Foldercontaining extra ibraries required for hive compilation/execution can becontrolled by:</span>
exportHIVE_AUX_JARS_PATH=/usr/local/hive/lib</code></pre>

<p>（3）修改Hive配置文件 hive-site.xml</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
   <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hive.exec.local.scratchdir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
   <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/usr/local/hive/iotmp<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">description</span>&gt;</span>Local scratch space forHive jobs<span class="hljs-tag">&lt;/<span class="hljs-title">description</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
   <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hive.downloaded.resources.dir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
   <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/usr/local/hive/iotmp/${hive.session.id}_resources<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
   <span class="hljs-tag">&lt;<span class="hljs-title">description</span>&gt;</span>Temporary localdirectory for added resources in the remote file system.<span class="hljs-tag">&lt;/<span class="hljs-title">description</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
   <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hive.querylog.location<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
   <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/usr/local/hive/iotmp<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
   <span class="hljs-tag">&lt;<span class="hljs-title">description</span>&gt;</span>Location of Hive runtime structured log file<span class="hljs-tag">&lt;/<span class="hljs-title">description</span>&gt;</span>

<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>


<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hive.server2.logging.operation.log.location<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/usr/local/hive/iotmp/operation_logs<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">description</span>&gt;</span>Top level directorywhere operation logs are stored if logging functionality isenabled<span class="hljs-tag">&lt;/<span class="hljs-title">description</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>javax.jdo.option.ConnectionURL<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>jdbc:derby:;databaseName=metastore_db;create=true<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">description</span>&gt;</span>JDBC connect string for aJDBC metastore<span class="hljs-tag">&lt;/<span class="hljs-title">description</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>



<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
 <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>javax.jdo.option.ConnectionDriverName<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
 <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>org.apache.derby.jdbc.EmbeddedDriver<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
 <span class="hljs-tag">&lt;<span class="hljs-title">description</span>&gt;</span>Driver class name for aJDBC metastore<span class="hljs-tag">&lt;/<span class="hljs-title">description</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span></code></pre>

<p>（4）修改hive配置文件 hive-config.sh bin目录下</p>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-keyword">export</span> JAVA_HOME=

<span class="hljs-keyword">export</span> HADOOP_HOME=

<span class="hljs-keyword">export</span> HIVE_HOME=</code></pre>

<p>（5）查看hive-site.xml 搜索关键字HDFS看需要在HDFS上创建哪些目录并创建</p>

<p>（6）初始化hive（先把hadoop启动）</p>



<pre class="prettyprint"><code class=" hljs lasso">schematool <span class="hljs-attribute">-dbType</span> derby <span class="hljs-attribute">-initSchema</span></code></pre>

<p>这里可能遇到的坑</p>

<p><img src="https://img-blog.csdn.net/20180804172719886?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2RwZW5nd2FuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="&lt;img src='2.png'&gt;" title=""></p>

<p>原因是根目录下已经有了一个metastore_db，把该文件夹删掉再次执行一下初始化命令即可</p>

<p>如果conf目录下也有metastore_db，那么也删掉</p>

<p>（7）在终端输入hive即可进行hive的命令行</p>

<p>通过运行mysql中的创表和插入语句来判断安装是否成功</p>

<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">create</span> <span class="hljs-keyword">table</span> student(<span class="hljs-keyword">varchar</span>(<span class="hljs-number">10</span>));</span>
<span class="hljs-operator"><span class="hljs-keyword">insert</span> <span class="hljs-keyword">into</span> student <span class="hljs-keyword">values</span>(<span class="hljs-number">1</span>);</span></code></pre>

<hr>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>