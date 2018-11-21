---
layout:     post
title:      【hadoop】hadoop安装篇
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u013412066/article/details/70038188				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h3 id="下载hadoop">下载hadoop</h3>

<blockquote>
  <p>在<a href="http://hadoop.apache.org/releases.html%20%E2%80%9Chadoop%E4%B8%8B%E8%BD%BD%E5%9C%B0%E5%9D%80%E2%80%9D" rel="nofollow">hadoop下载界面</a>选择适合的版本</p>
</blockquote>

<h3 id="配置hadoop">配置hadoop</h3>

<blockquote>
  <p>hadoop有两种类型的配置文件： <br>
      1.只读配置文件 - core-default.xml, hdfs-default.xml, yarn-default.xml 和 mapred-default.xml <br>
      2.可配置的配置文件 etc/hadoop/core-site.xml, etc/hadoop/hdfs-site.xml, etc/hadoop/yarn-site.xml and etc/hadoop/mapred-site.xml</p>
</blockquote>

<p>也可以通过etc/hadoop/hadoop-env.sh and etc/hadoop/yarn-env.sh来修改hadoop和yarn的运行时环境</p>

<p>修改hadoop的配置之前需要先配置ssh免密登录。如果对ssh免密登录不了解的可以先行百度。 <br>
首先贴上core-site.xml</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>fs.defaultFS<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>hdfs://hadoop:9000<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hadoop.tmp.dir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/home/hadoop/tmp<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>io.file.buffer.size<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>131072<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>

<p>hdfs-site.xml</p>

<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.namenode.name.dir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/home/hadoop/data/namenode<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.datanode.data.dir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/home/hadoop/data/datanode<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>

<p>yarn-site.xml</p>

<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>

<span class="hljs-comment">&lt;!-- Site specific YARN configuration properties --&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.resourcemanager.hostname<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>hadoop<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.log-appregation-enable<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>true<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.nodemanager.aux-services<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>mapreduce_shuffle<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.nodemanager.env-whitelist<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PR
EPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_MAPRED_HOME<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>

<p>mapred-site.xml,需要修改mr的计算框架为yarn，修改的时候一定要修改yarn.nodemanager.env-whitelist配置项</p>

<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
    <span class="hljs-comment">&lt;!-- 默认的是用mr框架来计算 --&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>mapreduce.framework.name<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>yarn<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>