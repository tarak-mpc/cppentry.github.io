---
layout:     post
title:      java  api  操作 hdfs
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>文章地址 ：<a href="http://www.haha174.top/article/details/255189" rel="nofollow">http://www.haha174.top/article/details/255189</a></p>

<h2 id="1简介">1.简介</h2>

<p>之前说到了shell  脚本操作hdfs  现在那么本篇文章讲述java  怎么操作hdfs <br>
使用 sh start-dfs.sh 启动 hdfs  jps   查看hdfs  状态  确保  hdfs <br>
SecondaryNameNode  NameNode DataNode  都已经 启动（如果没有安装hdfs  可以参考这篇 <a href="http://blog.csdn.net/u012957549/article/details/78787411" rel="nofollow" target="_blank">http://blog.csdn.net/u012957549/article/details/78787411</a>）</p>



<h2 id="2创建工程">2.创建工程</h2>

<p>使用idea  创建一个maven  工程  <br>
编写依赖</p>

<pre class="prettyprint"><code class=" hljs xml"> <span class="hljs-tag">&lt;<span class="hljs-title">dependencies</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.hadoop<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>hadoop-client<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>3.0.0-alpha1<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>junit<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>junit<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>4.12<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">scope</span>&gt;</span>test<span class="hljs-tag">&lt;/<span class="hljs-title">scope</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">dependencies</span>&gt;</span>
</code></pre>



<h2 id="3-编写">3. 编写</h2>

<p>3.1 before  和after  <br>
junit  提供一个 before   和 after   的注解 <br>
before    在测试代码 执行之前生效 <br>
after    在测试代码执行结束后生效 <br>
3.1.1 before  把链接配置 放在 before  中 </p>



<pre class="prettyprint"><code class=" hljs java">    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">final</span> String HDFS_PATH = <span class="hljs-string">"hdfs://192.168.1.223:8020"</span>;
    FileSystem fileSystem = <span class="hljs-keyword">null</span>;
    Configuration configuration = <span class="hljs-keyword">null</span>;</code></pre>

<pre class="prettyprint"><code class=" hljs cs">  @Before
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">setUp</span>() throws Exception {
        System.<span class="hljs-keyword">out</span>.println(<span class="hljs-string">"HDFSApp.setUp"</span>);
        configuration = <span class="hljs-keyword">new</span> Configuration();
        fileSystem = FileSystem.<span class="hljs-keyword">get</span>(<span class="hljs-keyword">new</span> URI(HDFS_PATH), configuration, <span class="hljs-string">"root"</span>);
    }</code></pre>

<p>使用 这种方法  填入url  和user  获取远程配置 <br>
3.1.2 after </p>



<pre class="prettyprint"><code class=" hljs java">    <span class="hljs-annotation">@After</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">tearDown</span>() <span class="hljs-keyword">throws</span> Exception {
        configuration = <span class="hljs-keyword">null</span>;
        fileSystem = <span class="hljs-keyword">null</span>;

        System.out.println(<span class="hljs-string">"HDFSApp.tearDown"</span>);
    }</code></pre>

<p>3.2 创建HDFS目录</p>

<pre class="prettyprint"><code class=" hljs java">
    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">mkdir</span>() <span class="hljs-keyword">throws</span> Exception {
        fileSystem.mkdirs(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/hdfsapi/test"</span>));
    }</code></pre>

<p>3.3  创建文件</p>

<pre class="prettyprint"><code class=" hljs java">    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">create</span>() <span class="hljs-keyword">throws</span> Exception {
        FSDataOutputStream output = fileSystem.create(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/hdfsapi/test/a.txt"</span>));
        output.write(<span class="hljs-string">"hello hadoop"</span>.getBytes());
        output.flush();
        output.close();
    }</code></pre>

<p>3.4 查看HDFS文件的内容</p>

<pre class="prettyprint"><code class=" hljs cs">    @Test
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">cat</span>() throws Exception {
        FSDataInputStream <span class="hljs-keyword">in</span> = fileSystem.open(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/hdfsapi/test/a.txt"</span>));
        IOUtils.copyBytes(<span class="hljs-keyword">in</span>, System.<span class="hljs-keyword">out</span>, <span class="hljs-number">1024</span>);
        <span class="hljs-keyword">in</span>.close();
    }</code></pre>

<p>3.5  重命名</p>

<pre class="prettyprint"><code class=" hljs java">    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">rename</span>() <span class="hljs-keyword">throws</span> Exception {
        Path oldPath = <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/hdfsapi/test/a.txt"</span>);
        Path newPath = <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/hdfsapi/test/b.txt"</span>);
        fileSystem.rename(oldPath, newPath);
    }</code></pre>

<p>3.6 上传文件到HDFS</p>

<pre class="prettyprint"><code class=" hljs java">    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">copyFromLocalFile</span>() <span class="hljs-keyword">throws</span> Exception {
        Path localPath = <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"C:\\Users\\haha174\\Desktop\\work\\mesos.yaml"</span>);
        Path hdfsPath = <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/hdfsapi/test"</span>);
        fileSystem.copyFromLocalFile(localPath, hdfsPath);
    }</code></pre>

<p>3.7 上传文件到HDFS</p>

<pre class="prettyprint"><code class=" hljs cs"> @Test
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">copyFromLocalFileWithProgress</span>() throws Exception {
        InputStream <span class="hljs-keyword">in</span> = <span class="hljs-keyword">new</span> BufferedInputStream(
                <span class="hljs-keyword">new</span> FileInputStream(
                        <span class="hljs-keyword">new</span> File(<span class="hljs-string">"C:\\haha174\\nginx\\html\\hadoop-3.0.0.tar.gz"</span>)));

        FSDataOutputStream output = fileSystem.create(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/hdfsapi/test/hadoop-3.0.0.tar.gz"</span>),
                <span class="hljs-keyword">new</span> Progressable() {
                    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">progress</span>() {
                        System.<span class="hljs-keyword">out</span>.print(<span class="hljs-string">"."</span>);  <span class="hljs-comment">//带进度提醒信息</span>
                    }
                });


        IOUtils.copyBytes(<span class="hljs-keyword">in</span>, output, <span class="hljs-number">4096</span>);
    }</code></pre>

<p>3.8 下载HDFS文件</p>

<pre class="prettyprint"><code class=" hljs java">    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">copyToLocalFile</span>() <span class="hljs-keyword">throws</span> Exception {
        Path localPath = <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"c:/tmp/h.txt"</span>);
        Path hdfsPath = <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/hdfsapi/test/hello.txt"</span>);
        fileSystem.copyToLocalFile(hdfsPath, localPath);
    }</code></pre>

<p>3.9 查看某个目录下的所有文件</p>

<pre class="prettyprint"><code class=" hljs java">    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">listFiles</span>() <span class="hljs-keyword">throws</span> Exception {
        FileStatus[] fileStatuses = fileSystem.listStatus(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/"</span>));

        <span class="hljs-keyword">for</span>(FileStatus fileStatus : fileStatuses) {
            String isDir = fileStatus.isDirectory() ? <span class="hljs-string">"文件夹"</span> : <span class="hljs-string">"文件"</span>;
            <span class="hljs-keyword">short</span> replication = fileStatus.getReplication();
            <span class="hljs-keyword">long</span> len = fileStatus.getLen();
            String path = fileStatus.getPath().toString();

            System.out.println(isDir + <span class="hljs-string">"\t"</span> + replication + <span class="hljs-string">"\t"</span> + len + <span class="hljs-string">"\t"</span> + path);
        }</code></pre>

<p>3.10  删除</p>

<pre class="prettyprint"><code class=" hljs java">    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">delete</span>() <span class="hljs-keyword">throws</span> Exception{
        fileSystem.delete(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/"</span>), <span class="hljs-keyword">true</span>);
    }
</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>