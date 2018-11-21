---
layout:     post
title:      Hadoop运维(2) Hive部署
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/irving512/article/details/53899870				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="1-下载hive并对文件进行配置">1. 下载Hive并对文件进行配置</h2>

<hr>

<h3 id="11-下载hive">1.1. 下载Hive</h3>

<p><a href="http://hive.apache.org/downloads.html" rel="nofollow">下载地址，需要选择镜像</a>，下载完成后将hive文件夹放置/usr/local/hive中。</p>

<hr>

<h3 id="12-生成配置文件">1.2. 生成配置文件</h3>



<pre class="prettyprint"><code class="language-linux hljs avrasm"><span class="hljs-preprocessor"># hive的配置文件都在conf文件夹中，但都是一些模板，需要进行以下操作</span>
cd /usr/local/hive/conf
<span class="hljs-keyword">cp</span> hive-env<span class="hljs-preprocessor">.sh</span><span class="hljs-preprocessor">.template</span> hive-env<span class="hljs-preprocessor">.sh</span>
<span class="hljs-keyword">cp</span> hive-default<span class="hljs-preprocessor">.xml</span><span class="hljs-preprocessor">.template</span> hive-site<span class="hljs-preprocessor">.xml</span>
<span class="hljs-keyword">cp</span> hive-log4j2<span class="hljs-preprocessor">.properties</span><span class="hljs-preprocessor">.template</span> hive-log4j2<span class="hljs-preprocessor">.properties</span>
<span class="hljs-keyword">cp</span> hive-exec-log4j2<span class="hljs-preprocessor">.properties</span><span class="hljs-preprocessor">.template</span> hive-exec-log4j2<span class="hljs-preprocessor">.properties</span></code></pre>

<hr>

<h3 id="13-hive-envsh文件配置">1.3. hive-env.sh文件配置</h3>



<pre class="prettyprint"><code class="language-linux hljs bash"><span class="hljs-comment"># 添加以下内容</span>
<span class="hljs-keyword">export</span> JAVA_HOME=/usr/lib/jvm/jdk8    <span class="hljs-comment">##Java路径</span>
<span class="hljs-keyword">export</span> HADOOP_HOME=/usr/local/hadoop   <span class="hljs-comment">##Hadoop安装路径</span>
<span class="hljs-keyword">export</span> HIVE_HOME=/usr/local/hive    <span class="hljs-comment">##Hive安装路径</span>
<span class="hljs-keyword">export</span> HIVE_CONF_DIR=<span class="hljs-variable">$HIVE_HOME</span>/conf    <span class="hljs-comment">##Hive配置文件路径</span></code></pre>

<hr>

<h3 id="14-hive-sitexml文件配置">1.4. hive-site.xml文件配置</h3>



<pre class="prettyprint"><code class="language-xml hljs "><span class="hljs-comment">&lt;!-- 其实就是替换了${system:java.io.tmpdir}和${system:user.name} --&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hive.exec.scratchdir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/tmp/hive-${user.name}<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hive.exec.local.scratchdir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/tmp/${user.name}<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hive.downloaded.resources.dir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/tmp/hive/resources<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hive.querylog.location<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/tmp/${user.name}<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hive.server2.logging.operation.log.location<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/tmp/${user.name}/operation_logs<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span></code></pre>

<hr>

<h2 id="2-配置hive-metastore">2. 配置Hive Metastore</h2>

<hr>

<h3 id="21-下载mysql-connector">2.1. 下载MySQL-connector</h3>

<p><a href="http://dev.mysql.com/downloads/connector/j/" rel="nofollow">下载地址</a>，下载后放置$HIVE_HOME/bin中。</p>

<hr>

<h3 id="22-修改hive-sitexml">2.2. 修改hive-site.xml</h3>



<pre class="prettyprint"><code class="language-xml hljs "><span class="hljs-comment">&lt;!-- 注意要在对应的mysql数据库中创建对应的用户 --&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>javax.jdo.option.ConnectionURL<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>jdbc:mysql://master:3306/hive?createDatabaseIfNotExist=true&amp;amp;characterEncoding=UTF-8&amp;amp;useSSL=false<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
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
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>hive<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span></code></pre>

<hr>

<h2 id="3-调试hive">3. 调试Hive</h2>

<hr>

<h3 id="31-创建hdfs目录">3.1. 创建HDFS目录</h3>



<pre class="prettyprint"><code class="language-linux hljs perl">hdfs dfs -<span class="hljs-keyword">mkdir</span> /tmp
hdfs dfs -<span class="hljs-keyword">mkdir</span> /usr/hive/warehouse
hdfs dfs -<span class="hljs-keyword">chmod</span> g+w /tmp
hdfs dfs -<span class="hljs-keyword">chmod</span> g+w /usr/hive/warehouse</code></pre>

<hr>

<h3 id="32-运行hive">3.2. 运行Hive</h3>



<pre class="prettyprint"><code class="language-linux hljs vala"><span class="hljs-preprocessor"># Hive2.1之后需要先进行初始化操作</span>
schematool -dbType mysql -initSchema

<span class="hljs-preprocessor"># 运行hive</span>
hive</code></pre>

<hr>

<h2 id="4-参考资料">4. 参考资料</h2>

<p><a href="https://my.oschina.net/jackieyeah/blog/735424" rel="nofollow">Hive 2.1.0安装配置</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>