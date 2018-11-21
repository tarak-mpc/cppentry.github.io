---
layout:     post
title:      Hadoop笔记：Java API 操作HDFS文件大集合
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/japson_iot/article/details/80467498				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="大数据笔记java-api-操作hdfs文件大集合">大数据笔记：Java API 操作HDFS文件大集合</h1>

<p>标签： 大数据</p>

<p></p><div class="toc">
<ul>
<li><a href="#%E5%A4%A7%E6%95%B0%E6%8D%AE%E7%AC%94%E8%AE%B0java-api-%E6%93%8D%E4%BD%9Chdfs%E6%96%87%E4%BB%B6%E5%A4%A7%E9%9B%86%E5%90%88" rel="nofollow">大数据笔记：Java API 操作HDFS文件大集合</a><ul>
<li><a href="#javaapi%E6%93%8D%E4%BD%9Chdfs%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA" rel="nofollow">JAVAAPI操作HDFS环境搭建</a></li>
<li><a href="#java%E6%93%8D%E4%BD%9C%E5%9C%A8hdfs%E4%B8%8A%E5%88%9B%E5%BB%BA%E4%B8%80%E4%B8%AA%E7%9B%AE%E5%BD%95" rel="nofollow">JAVA操作在hdfs上创建一个目录</a><ul>
<li><a href="#%E4%BB%A3%E7%A0%81%E5%B1%95%E7%A4%BA" rel="nofollow">代码展示</a></li>
<li><a href="#%E6%96%87%E6%A1%A3%E5%88%86%E6%9E%90" rel="nofollow">文档分析</a></li>
<li><a href="#%E7%BB%93%E6%9E%9C" rel="nofollow">结果</a></li>
</ul>
</li>
<li><a href="#%E5%88%9B%E5%BB%BA%E4%B8%80%E4%B8%AA%E6%96%87%E4%BB%B6%E5%B9%B6%E5%86%99%E5%85%A5%E5%86%85%E5%AE%B9" rel="nofollow">创建一个文件并写入内容</a><ul>
<li><a href="#%E5%88%9B%E5%BB%BA%E6%96%87%E4%BB%B6" rel="nofollow">创建文件</a></li>
<li><a href="#%E5%86%99%E5%85%A5%E5%86%85%E5%AE%B9" rel="nofollow">写入内容</a></li>
<li><a href="#%E4%BB%A3%E7%A0%81" rel="nofollow">代码</a></li>
</ul>
</li>
<li><a href="#%E6%9F%A5%E7%9C%8Bhdfs%E7%9A%84%E5%86%85%E5%AE%B9" rel="nofollow">查看HDFS的内容</a><ul>
<li><a href="#%E5%91%BD%E4%BB%A4%E8%A1%8C%E6%9F%A5%E7%9C%8B" rel="nofollow">命令行查看</a></li>
<li><a href="#java%E6%93%8D%E4%BD%9C" rel="nofollow">java操作</a></li>
</ul>
</li>
<li><a href="#%E5%88%A0%E9%99%A4%E6%96%87%E4%BB%B6" rel="nofollow">删除文件</a></li>
<li><a href="#%E6%96%87%E4%BB%B6%E9%87%8D%E5%91%BD%E5%90%8D" rel="nofollow">文件重命名</a></li>
<li><a href="#%E4%B8%8A%E4%BC%A0%E6%9C%AC%E5%9C%B0%E6%96%87%E4%BB%B6%E5%88%B0hdfs" rel="nofollow">上传本地文件到HDFS</a><ul>
<li><a href="#%E5%B0%8F%E6%96%87%E4%BB%B6%E7%9B%B4%E6%8E%A5%E4%B8%8A%E4%BC%A0" rel="nofollow">小文件直接上传</a></li>
<li><a href="#%E5%A4%A7%E6%96%87%E4%BB%B6%E8%BF%9B%E5%BA%A6%E6%9D%A1" rel="nofollow">大文件进度条</a></li>
</ul>
</li>
<li><a href="#%E4%B8%8B%E8%BD%BDhdfs%E4%B8%8A%E7%9A%84%E6%96%87%E4%BB%B6%E5%88%B0%E6%9C%AC%E5%9C%B0" rel="nofollow">下载HDFS上的文件到本地</a></li>
<li><a href="#%E6%9F%A5%E7%9C%8B%E6%9F%90%E4%B8%AA%E7%9B%AE%E5%BD%95%E4%B8%8B%E7%9A%84%E6%89%80%E6%9C%89%E6%96%87%E4%BB%B6" rel="nofollow">查看某个目录下的所有文件</a></li>
</ul>
</li>
</ul>
</div>


<hr>



<h2 id="javaapi操作hdfs环境搭建">JAVAAPI操作HDFS环境搭建</h2>

<p>IDEA + Maven创建java工程</p>

<p>我们使用maven，选择maven的 QuickStart 来创建一个maven。</p>

<p>然后在选择setting.xml时，可以选择默认的，也可以选择自己的，不论如何，切记要将maven下载的镜像换成阿里云等国内的。</p>

<p>然后就生成了maven工程。</p>

<p>在pom.xml中添加依赖关系。</p>

<p>首先在properties标签中新建一个标签，为了方便后面的引用：</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">hadoop.version</span>&gt;</span>2.6.0-cdh5.7.0<span class="hljs-tag">&lt;/<span class="hljs-title">hadoop.version</span>&gt;</span></code></pre>

<p>然后写一个repository元素，repository就是个仓库。maven里有两种仓库，本地仓库和远程仓库。远程仓库相当于公共的仓库，本地仓库主要起缓存作用。当向仓库请求插件或依赖的时候，会先检查本地仓库里是否有。如果有则直接返回，否则会向远程仓库请求，并做缓存。这里就是在pom.xml文件中指定远程仓库。如果没指定，默认会到<a href="http://repo1.maven.org/maven2" rel="nofollow">http://repo1.maven.org/maven2</a>这个地方去请求插件和依赖包。 </p>

<p>这里指定：</p>



<pre class="prettyprint"><code class=" hljs xml">  <span class="hljs-tag">&lt;<span class="hljs-title">repositories</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">repository</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">id</span>&gt;</span>cloudera<span class="hljs-tag">&lt;/<span class="hljs-title">id</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">url</span>&gt;</span>https://repository.cloudera.com/artifactory/cloudera-repos<span class="hljs-tag">&lt;/<span class="hljs-title">url</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">repository</span>&gt;</span>
  <span class="hljs-tag">&lt;/<span class="hljs-title">repositories</span>&gt;</span></code></pre>

<p>然后就是添加依赖：</p>



<pre class="prettyprint"><code class=" hljs xml">    <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.hadoop<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>hadoop-client<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>${hadoop.version}<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span></code></pre>

<p>让maven自己去下载吧！当然下载的过程会出现一些问题，也都解决了。</p>



<h2 id="java操作在hdfs上创建一个目录">JAVA操作在hdfs上创建一个目录</h2>

<p>这个时候我们就需要把java程序和hadoop服务器连接起来。</p>

<p>首先我们要启动hdfs。然后在创建一个test文件。先看代码，再分析。</p>



<h3 id="代码展示">代码展示</h3>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">package</span> com.japson.hadoop.hdfs;

<span class="hljs-keyword">import</span> org.apache.hadoop.conf.Configuration;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.FileSystem;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.Path;
<span class="hljs-keyword">import</span> org.junit.After;
<span class="hljs-keyword">import</span> org.junit.Before;
<span class="hljs-keyword">import</span> org.junit.Test;

<span class="hljs-keyword">import</span> java.net.URI;

<span class="hljs-javadoc">/**
 * Created by japson on 5/26/2018.
 * Hadoop HDFS Java API 操作
 */</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">HDFSApp</span> {</span>

    <span class="hljs-comment">// HDFS_PATH 很重要！是访问连接HDFS的关键</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">final</span> String HDFS_PATH = <span class="hljs-string">"hdfs://192.168.0.117:8020"</span>;

    <span class="hljs-comment">// 必需的FileSystem类</span>
    FileSystem fileSystem = <span class="hljs-keyword">null</span>;
    <span class="hljs-comment">// 配置对象</span>
    Configuration configuration = <span class="hljs-keyword">null</span>;

    <span class="hljs-javadoc">/**
     * 初始化一些资源
     */</span>
    <span class="hljs-annotation">@Before</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">setUp</span>() <span class="hljs-keyword">throws</span> Exception{
        configuration = <span class="hljs-keyword">new</span> Configuration();
        <span class="hljs-comment">// 获取一个fileSystem对象，这就相当于建立连接了</span>
        fileSystem = FileSystem.get(<span class="hljs-keyword">new</span> URI(HDFS_PATH),configuration,<span class="hljs-string">"japson"</span>);
        System.out.println(<span class="hljs-string">"HDFSApp.setUp"</span>);
    }

    <span class="hljs-javadoc">/**
     * 创建HDFS目录
     *<span class="hljs-javadoctag"> @throws</span> Exception
     */</span>
    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">mkdir</span>() <span class="hljs-keyword">throws</span> Exception {
        <span class="hljs-comment">// 创建一个新的目录，参数为路径</span>
        fileSystem.mkdirs(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/hdfsapi/test3"</span>));
        System.out.println(<span class="hljs-string">"mkdir已执行"</span>);
    }


    <span class="hljs-javadoc">/**
     * 释放资源
     */</span>
    <span class="hljs-annotation">@After</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">tearDown</span>() <span class="hljs-keyword">throws</span> Exception {
        configuration = <span class="hljs-keyword">null</span>;
        fileSystem = <span class="hljs-keyword">null</span>;
        System.out.println(<span class="hljs-string">"HDFSApp.tearDown"</span>);
    }

}</code></pre>



<h3 id="文档分析">文档分析</h3>

<p>首先我们要引入两个关键的类：<strong>org.apache.hadoop.fs.FileSystem</strong>和<strong>org.apache.hadoop.conf.Configuration</strong></p>

<p>One by one：</p>

<p><strong>org.apache.hadoop.fs.FileSystem：</strong></p>

<blockquote>
  <p>An abstract base class for a fairly generic filesystem. It may be implemented as a distributed filesystem, or as a “local” one that reflects the locally-connected disk. The local version exists for small Hadoop instances and for testing. <br>
  All user code that may potentially use the Hadoop Distributed File System should be written to use a FileSystem object or its successor, FileContext.</p>
  
  <p>The local implementation is LocalFileSystem and distributed implementation is DistributedFileSystem. There are other implementations for object stores and (outside the Apache Hadoop codebase), third party filesystems.</p>
</blockquote>

<p>大体意思就是说：</p>

<ul>
<li><p>这是一个比较通用的文件系统的抽象基类，可以实现分布式系统或者本地连接磁盘，本地版本可以做hadoop的小测试。</p></li>
<li><p>要想使用HDFS，那么就得使用这个FileSystem的对象或者子类对象。</p></li>
<li><p>本地实现是LocalFileSystem，分布式实现是DistributedFileSystem。剩下的还有其他第三方实现。</p></li>
</ul>

<p>那么现在我就要使用这个方法：</p>



<pre class="prettyprint"><code class=" hljs vbnet"><span class="hljs-keyword">static</span> FileSystem <span class="hljs-keyword">get</span>(URI uri, Configuration conf, <span class="hljs-built_in">String</span> user)</code></pre>

<p>该方法通过文件系统的URI、要使用的Configuration对象和用户名（Linux的，需要权限）来获取一个FileSystem对象。</p>

<p><strong>org.apache.hadoop.conf.Configuration</strong></p>

<p>在API中对于这个类的介绍：</p>

<blockquote>
  <p>Provides access to configuration parameters.</p>
</blockquote>

<p>对配置参数提供访问。</p>

<p>Configuration做为Hadoop的一个基础功能承担着重要的责任，为Yarn、HSFS、MapReduce、NFS、调度器等提供参数的配置、配置文件的分布式传输(实现了Writable接口)等重要功能。</p>

<p>Hadoop实现了一个自己的加载配置文件的功能的Configuration类：org.apache.hadoop.conf.Configuration。其实也就是我们之前的core-site.xml等配置文件。</p>



<h3 id="结果">结果</h3>

<p>我们在HDFS中查看：</p>



<pre class="prettyprint"><code class=" hljs oxygene">[japson@localhost hadoop-<span class="hljs-number">2.6</span>.<span class="hljs-number">0</span>-cdh5.<span class="hljs-number">7.0</span>]$ hdfs dfs -ls -R /
<span class="hljs-number">18</span>/<span class="hljs-number">05</span>/<span class="hljs-number">26</span> <span class="hljs-number">00</span>:<span class="hljs-number">30</span>:<span class="hljs-number">27</span> WARN util.NativeCodeLoader: Unable <span class="hljs-keyword">to</span> load native-hadoop <span class="hljs-keyword">library</span> <span class="hljs-keyword">for</span> your <span class="hljs-keyword">platform</span>... <span class="hljs-keyword">using</span> builtin-java classes <span class="hljs-keyword">where</span> applicable
drwxr-xr-x   - japson supergroup          <span class="hljs-number">0</span> <span class="hljs-number">2018</span>-<span class="hljs-number">05</span>-<span class="hljs-number">25</span> <span class="hljs-number">20</span>:<span class="hljs-number">46</span> /hdfsapi
drwxr-xr-x   - japson supergroup          <span class="hljs-number">0</span> <span class="hljs-number">2018</span>-<span class="hljs-number">05</span>-<span class="hljs-number">25</span> <span class="hljs-number">20</span>:<span class="hljs-number">46</span> /hdfsapi/test3
-rw-r--r--   <span class="hljs-number">1</span> japson supergroup         <span class="hljs-number">48</span> <span class="hljs-number">2018</span>-<span class="hljs-number">05</span>-<span class="hljs-number">25</span> <span class="hljs-number">20</span>:<span class="hljs-number">44</span> /hello.txt</code></pre>



<h2 id="创建一个文件并写入内容">创建一个文件并写入内容</h2>



<h3 id="创建文件">创建文件</h3>

<p>我们所有的操作都是在fileSystem的基础上的，我们使用下面的方法来创建文件：</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">public</span> FSDataOutputStream <span class="hljs-title">create</span>(Path f) <span class="hljs-keyword">throws</span> IOException {
        <span class="hljs-keyword">return</span> <span class="hljs-keyword">this</span>.create(f, <span class="hljs-keyword">true</span>);
    }</code></pre>

<p>需要我们传入要创建的文件的路径。</p>



<h3 id="写入内容">写入内容</h3>

<p>创建文件的方法有一个 FSDataOutputStream 类型的返回值。</p>

<p>因此我们创建一个对象将其接住，其实这就是一个输出流，我们就当做输出流来写文件就好了。</p>



<pre class="prettyprint"><code class=" hljs avrasm">FSDataOutputStream outputStream = fileSystem<span class="hljs-preprocessor">.create</span>(new Path(<span class="hljs-string">"/hdfsapi/test3/a.txt"</span>))<span class="hljs-comment">;</span>
outputStream<span class="hljs-preprocessor">.write</span>(<span class="hljs-string">"hello hadoop"</span><span class="hljs-preprocessor">.getBytes</span>())<span class="hljs-comment">;</span>
outputStream<span class="hljs-preprocessor">.flush</span>()<span class="hljs-comment">;</span>
outputStream<span class="hljs-preprocessor">.close</span>()<span class="hljs-comment">;</span></code></pre>

<p>要注意，没有String的参数类型，需要转为字节数组，记得flush和close。</p>



<h3 id="代码">代码</h3>



<pre class="prettyprint"><code class=" hljs java">    <span class="hljs-javadoc">/**
     * 创建一个文件并写入内容
     *<span class="hljs-javadoctag"> @throws</span> Exception
     */</span>
    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">create</span>() <span class="hljs-keyword">throws</span> Exception {
        FSDataOutputStream outputStream = fileSystem.create(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/hdfsapi/test3/a.txt"</span>));
        outputStream.write(<span class="hljs-string">"hello hadoop"</span>.getBytes());
        outputStream.flush();
        outputStream.close();
    }</code></pre>



<h2 id="查看hdfs的内容">查看HDFS的内容</h2>



<h3 id="命令行查看">命令行查看</h3>

<p>对于之前我们创建的文件，我们可以通过命令行查看其内容</p>



<pre class="prettyprint"><code class=" hljs oxygene">[japson@localhost hadoop-<span class="hljs-number">2.6</span>.<span class="hljs-number">0</span>-cdh5.<span class="hljs-number">7.0</span>]$ hdfs dfs -text /hdfsapi/test3/a.txt
<span class="hljs-number">18</span>/<span class="hljs-number">05</span>/<span class="hljs-number">26</span> <span class="hljs-number">00</span>:<span class="hljs-number">39</span>:<span class="hljs-number">34</span> WARN util.NativeCodeLoader: Unable <span class="hljs-keyword">to</span> load native-hadoop <span class="hljs-keyword">library</span> <span class="hljs-keyword">for</span> your <span class="hljs-keyword">platform</span>... <span class="hljs-keyword">using</span> builtin-java classes <span class="hljs-keyword">where</span> applicable
hello hadoop</code></pre>



<h3 id="java操作">java操作</h3>

<p>fileSytem中右一个open方法，是可以将指定路径的内容返回为一个输入流的。</p>

<p>对于一个输入流，我们借助Hadoop的IOUtils来把输入流读到的内容拷贝到控制台</p>



<pre class="prettyprint"><code class=" hljs java">    <span class="hljs-javadoc">/**
     * 查看HDFS文件的内容
     *<span class="hljs-javadoctag"> @throws</span> Exception
     */</span>
    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">cat</span>() <span class="hljs-keyword">throws</span> Exception {
        FSDataInputStream inputStream = fileSystem.open(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/hdfsapi/test3/a.txt"</span>));
        IOUtils.copyBytes(inputStream,System.out,<span class="hljs-number">1024</span>);
        inputStream.close();

    }</code></pre>



<h2 id="删除文件">删除文件</h2>

<p>第二个参数为递归地删除文件。</p>



<pre class="prettyprint"><code class=" hljs java">    <span class="hljs-javadoc">/**
     * 删除文件
     *<span class="hljs-javadoctag"> @throws</span> Exception
     */</span>
    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">delete</span>() <span class="hljs-keyword">throws</span> Exception {

        fileSystem.delete(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/hdfsapi/test/a.txt"</span>),<span class="hljs-keyword">true</span>);

    }</code></pre>



<h2 id="文件重命名">文件重命名</h2>

<p><code>fileSystem.rename(oldPath,newPath);</code>，需要注意的是：每次只能修改一个目录名或文件名，不能同时修改两个及以上。</p>



<pre class="prettyprint"><code class=" hljs java">    <span class="hljs-javadoc">/**
     * 文件重命名
     *<span class="hljs-javadoctag"> @throws</span> Exception
     */</span>
    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">rename</span>() <span class="hljs-keyword">throws</span> Exception {
        Path oldPath = <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/hdfsapi/test3/a.txt"</span>);
        Path newPath = <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/hdfsapi/test3/b.txt"</span>);
        fileSystem.rename(oldPath,newPath);
    }</code></pre>



<h2 id="上传本地文件到hdfs">上传本地文件到HDFS</h2>



<h3 id="小文件直接上传">小文件直接上传</h3>

<p>注意，这里的本地文件，指的就是java的本地环境，而不是服务器的。</p>

<p>使用<code>fileSystem.copyFromLocalFile(localPath,hdfsPath);</code>方法。</p>



<pre class="prettyprint"><code class=" hljs java">    <span class="hljs-javadoc">/**
     * 上传本地文件到HDFS
     *<span class="hljs-javadoctag"> @throws</span> Exception
     */</span>
    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">copyFromLocalFile</span>() <span class="hljs-keyword">throws</span> Exception {
        Path localPath = <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"E:\\IntelliJ IDEA\\hadoopTest1\\localText.txt"</span>);
        Path hdfsPath = <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/hdfsapi/test"</span>);

        fileSystem.copyFromLocalFile(localPath,hdfsPath);
    }</code></pre>



<h3 id="大文件进度条">大文件进度条</h3>

<p>我们就不使用<code>fileSystem.copyFromLocalFile(localPath,hdfsPath);</code>了。</p>

<p>我们先将要上传的文件放入输入流，然后用输出流写，并在参数中选择输出进度条。</p>

<p>然后使用IOUtils，将输入流的内容拷贝到输出流：</p>



<pre class="prettyprint"><code class=" hljs java">    <span class="hljs-javadoc">/**
     * 上传本地大文件且需要进度条
     *<span class="hljs-javadoctag"> @throws</span> Exception
     */</span>
    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">copyFromLocalFileWithProgress</span>() <span class="hljs-keyword">throws</span> Exception {
        InputStream in = <span class="hljs-keyword">new</span> BufferedInputStream(<span class="hljs-keyword">new</span> FileInputStream(<span class="hljs-keyword">new</span> File(<span class="hljs-string">"D:\\迅雷下载\\逃避可耻却有用1.mp4"</span>)));
        FSDataOutputStream outputStream = fileSystem.create(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/hdfsapi/test/gakki.mp4"</span>),
                <span class="hljs-keyword">new</span> Progressable() {
                    <span class="hljs-annotation">@Override</span>
                    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">progress</span>() {
                        System.out.print(<span class="hljs-string">"."</span>);   <span class="hljs-comment">// 带进度提醒</span>
                    }
                });
        IOUtils.copyBytes(in,outputStream,<span class="hljs-number">4096</span>);
    }</code></pre>



<h2 id="下载hdfs上的文件到本地">下载HDFS上的文件到本地</h2>



<pre class="prettyprint"><code class=" hljs java">    <span class="hljs-javadoc">/**
     * 下载HDFS文件到本地
     *<span class="hljs-javadoctag"> @throws</span> Exception
     */</span>
    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">copyToLocalFile</span>() <span class="hljs-keyword">throws</span> Exception {
        Path localPath = <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"E:\\IntelliJ IDEA\\hadoopTest1\\download.txt"</span>);
        Path hdfsPath = <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/hdfsapi/test/b.txt"</span>);
        fileSystem.copyToLocalFile(hdfsPath,localPath);
    }</code></pre>



<h2 id="查看某个目录下的所有文件">查看某个目录下的所有文件</h2>



<pre class="prettyprint"><code class=" hljs java">    <span class="hljs-javadoc">/**
     * 查看某个目录下的所有文件
     *<span class="hljs-javadoctag"> @throws</span> Exception
     */</span>
    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">listFiles</span>() <span class="hljs-keyword">throws</span> Exception {
        FileStatus[] fileStatuses = fileSystem.listStatus(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/hdfsapi/test"</span>));
        <span class="hljs-keyword">for</span> (FileStatus fileStatus : fileStatuses) {
            String isDir = fileStatus.isDirectory() ? <span class="hljs-string">"文件夹"</span> : <span class="hljs-string">"文件"</span>;
            <span class="hljs-keyword">short</span> replication = fileStatus.getReplication();
            <span class="hljs-keyword">long</span> len = fileStatus.getLen();
            String path = fileStatus.getPath().toString();

            System.out.println(isDir + <span class="hljs-string">"\t"</span> + replication + <span class="hljs-string">"\t"</span> + len + <span class="hljs-string">"\t"</span> + path);
        }
    }</code></pre>

<p>输出：</p>



<pre class="prettyprint"><code class=" hljs ruby">文件  <span class="hljs-number">3</span>   <span class="hljs-number">12</span>  <span class="hljs-symbol">hdfs:</span>/<span class="hljs-regexp">/192.168.0.103:8020/hdfsapi</span><span class="hljs-regexp">/test/a</span>.txt</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>