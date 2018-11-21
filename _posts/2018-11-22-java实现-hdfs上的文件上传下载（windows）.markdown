---
layout:     post
title:      java实现-hdfs上的文件上传下载（windows）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/zhaofuxiang/article/details/72821232				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="javahdfs文件操作实现">java–HDFS文件操作实现</h2>

<p>全局静态变量：hdfs的地址和端口号</p>



<pre class="prettyprint"><code class=" hljs cs"> <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> String HDFSUri = <span class="hljs-string">"hdfs://10.8.177.29:8020"</span>;</code></pre>



<h4 id="1-获取文件系统">1. 获取文件系统</h4>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-javadoc">/**
     * 获取文件系统
     *
     *<span class="hljs-javadoctag"> @return</span> FileSystem 文件系统
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> FileSystem <span class="hljs-title">getFileSystem</span>() {
        <span class="hljs-comment">//读取配置文件</span>
        Configuration conf = <span class="hljs-keyword">new</span> Configuration();
        <span class="hljs-comment">// 文件系统</span>
        FileSystem fs = <span class="hljs-keyword">null</span>;
        String hdfsUri = HDFSUri;
        <span class="hljs-keyword">if</span>(StringUtils.isBlank(hdfsUri)){
            <span class="hljs-comment">// 返回默认文件系统  如果在 Hadoop集群下运行，使用此种方法可直接获取默认文件系统</span>
            <span class="hljs-keyword">try</span> {
                fs = FileSystem.get(conf);
            } <span class="hljs-keyword">catch</span> (IOException e) {
                e.printStackTrace();
            }
        }<span class="hljs-keyword">else</span>{
            <span class="hljs-comment">// 返回指定的文件系统,如果在本地测试，需要使用此种方法获取文件系统</span>
            <span class="hljs-keyword">try</span> {
                URI uri = <span class="hljs-keyword">new</span> URI(hdfsUri.trim());
                fs = FileSystem.get(uri,conf);
            } <span class="hljs-keyword">catch</span> (Exception e) {
                e.printStackTrace();
            }
        }
        <span class="hljs-keyword">return</span> fs;
    }</code></pre>



<h4 id="2-创建文件目录">2. 创建文件目录</h4>

<p>入参是创建的文件目录的地址</p>



<pre class="prettyprint"><code class=" hljs java"> <span class="hljs-javadoc">/**
     * 创建文件目录
     *
     *<span class="hljs-javadoctag"> @param</span> path 文件路径
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">mkdir</span>(String path) {
        <span class="hljs-keyword">try</span> {
            FileSystem fs = getFileSystem();
            System.out.println(<span class="hljs-string">"FilePath="</span>+path);
            <span class="hljs-comment">// 创建目录</span>
            fs.mkdirs(<span class="hljs-keyword">new</span> Path(path));
            <span class="hljs-comment">//释放资源</span>
            fs.close();
        } <span class="hljs-keyword">catch</span> (Exception e) {
            e.printStackTrace();
        }
    }</code></pre>



<h4 id="3-判断目录是否存在">3. 判断目录是否存在</h4>

<p>判断某目录是否存在，存在返回true,不存在返回false;</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-javadoc">/**
     * 判断目录是否存在
     *
     *<span class="hljs-javadoctag"> @param</span> filePath 目录路径
     *<span class="hljs-javadoctag"> @param</span> create 若不存在是否创建
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span>  <span class="hljs-keyword">boolean</span> <span class="hljs-title">existDir</span>(String filePath, <span class="hljs-keyword">boolean</span> create){
        <span class="hljs-keyword">boolean</span> flag = <span class="hljs-keyword">false</span>;

        <span class="hljs-keyword">if</span> (StringUtils.isEmpty(filePath)){
            <span class="hljs-keyword">return</span> flag;
        }

        <span class="hljs-keyword">try</span>{
            Path path = <span class="hljs-keyword">new</span> Path(filePath);
            <span class="hljs-comment">// FileSystem对象</span>
            FileSystem fs = getFileSystem();

            <span class="hljs-keyword">if</span> (create){
                <span class="hljs-keyword">if</span> (!fs.exists(path)){
                    fs.mkdirs(path);
                }
            }

            <span class="hljs-keyword">if</span> (fs.isDirectory(path)){
                flag = <span class="hljs-keyword">true</span>;
            }
        }<span class="hljs-keyword">catch</span> (Exception e){
            e.printStackTrace();
        }

        <span class="hljs-keyword">return</span> flag;
    }</code></pre>



<h4 id="4-本地文件上传至-hdfs">4. 本地文件上传至 HDFS</h4>

<p>windows测试环境下，上传本地文件到HDFS文件系统。</p>

<p>srcFile本地文件路径，源文件路径；</p>

<p>destPath hdfs路径，目的文件路径。</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-javadoc">/**
     * 本地文件上传至 HDFS
     *
     *<span class="hljs-javadoctag"> @param</span> srcFile 源文件 路径
     *<span class="hljs-javadoctag"> @param</span> destPath hdfs路径
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">copyFileToHDFS</span>(String srcFile,String destPath)<span class="hljs-keyword">throws</span> Exception{


        FileInputStream fis=<span class="hljs-keyword">new</span> FileInputStream(<span class="hljs-keyword">new</span> File(srcFile));<span class="hljs-comment">//读取本地文件</span>
        Configuration config=<span class="hljs-keyword">new</span> Configuration();
        FileSystem fs=FileSystem.get(URI.create(HDFSUri+destPath), config);
        OutputStream os=fs.create(<span class="hljs-keyword">new</span> Path(destPath));
        <span class="hljs-comment">//copy</span>
        IOUtils.copyBytes(fis, os, <span class="hljs-number">4096</span>, <span class="hljs-keyword">true</span>);
        System.out.println(<span class="hljs-string">"拷贝完成..."</span>);
        fs.close();
    }</code></pre>



<h4 id="5从-hdfs-下载文件到本地">5.从 HDFS 下载文件到本地</h4>

<p>srcFile HDFS文件路径 源文件路径；</p>

<p>destPath 本地路径 目录文件路径。</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-javadoc">/**
     * 从 HDFS 下载文件到本地
     *
     *<span class="hljs-javadoctag"> @param</span> srcFile HDFS文件路径
     *<span class="hljs-javadoctag"> @param</span> destPath 本地路径
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">getFile</span>(String srcFile,String destPath)<span class="hljs-keyword">throws</span> Exception {
        <span class="hljs-comment">//hdfs文件 地址</span>
        String file=HDFSUri+srcFile;
        Configuration config=<span class="hljs-keyword">new</span> Configuration();
        <span class="hljs-comment">//构建FileSystem</span>
        FileSystem fs = FileSystem.get(URI.create(file),config);
        <span class="hljs-comment">//读取文件</span>
        InputStream is=fs.open(<span class="hljs-keyword">new</span> Path(file));
        IOUtils.copyBytes(is, <span class="hljs-keyword">new</span> FileOutputStream(<span class="hljs-keyword">new</span> File(destPath)),<span class="hljs-number">2048</span>, <span class="hljs-keyword">true</span>);<span class="hljs-comment">//保存到本地  最后 关闭输入输出流</span>
        System.out.println(<span class="hljs-string">"下载完成..."</span>);
        fs.close();
    }</code></pre>



<h4 id="6-删除文件或者文件目录">6. 删除文件或者文件目录</h4>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-javadoc">/**
     * 删除文件或者文件目录
     *
     *<span class="hljs-javadoctag"> @param</span> path
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">rmdir</span>(String path) {
        <span class="hljs-keyword">try</span> {
            <span class="hljs-comment">// 返回FileSystem对象</span>
            FileSystem fs = getFileSystem();

            String hdfsUri = HDFSUri;
            <span class="hljs-keyword">if</span>(StringUtils.isNotBlank(hdfsUri)){
                path = hdfsUri + path;
            }
            System.out.println(<span class="hljs-string">"path:"</span>+path);
            <span class="hljs-comment">// 删除文件或者文件目录  delete(Path f) 此方法已经弃用</span>
            System.out.println( fs.delete(<span class="hljs-keyword">new</span> Path(path),<span class="hljs-keyword">true</span>));

            <span class="hljs-comment">// 释放资源</span>
            fs.close();
        } <span class="hljs-keyword">catch</span> (Exception e) {
            e.printStackTrace();
        }
    }</code></pre>



<h4 id="7-读取文件的内容并打印">7. 读取文件的内容，并打印</h4>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-javadoc">/**
     * 读取文件的内容
     *<span class="hljs-javadoctag"> @param</span> filePath
     *<span class="hljs-javadoctag"> @throws</span> IOException
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">readFile</span>(String filePath) <span class="hljs-keyword">throws</span> IOException{
        Configuration config = <span class="hljs-keyword">new</span> Configuration();
        String file=HDFSUri+filePath;
        FileSystem fs = FileSystem.get(URI.create(file),config);
        <span class="hljs-comment">//读取文件</span>
        InputStream is=fs.open(<span class="hljs-keyword">new</span> Path(file));
        <span class="hljs-comment">//读取文件</span>
        IOUtils.copyBytes(is, System.out, <span class="hljs-number">2048</span>, <span class="hljs-keyword">false</span>); <span class="hljs-comment">//复制到标准输出流</span>
        fs.close();
    }</code></pre>



<h3 id="测试main方法调用">测试：main方法调用</h3>



<pre class="prettyprint"><code class=" hljs cs"> <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) throws Exception {
        String HDFSFile = <span class="hljs-string">"/hollycrm/data01/codecs/1月.zip"</span>;
        String localFile = <span class="hljs-string">"D:\\1月.zip"</span>;
        <span class="hljs-comment">//连接fs</span>
        FileSystem fs = getFileSystem();
        System.<span class="hljs-keyword">out</span>.println(fs.getUsed());
        <span class="hljs-comment">//创建路径</span>
        mkdir(<span class="hljs-string">"/zhaojy2"</span>);
        <span class="hljs-comment">//验证是否存在</span>
        System.<span class="hljs-keyword">out</span>.println(existDir(<span class="hljs-string">"/zhaojy2"</span>,<span class="hljs-keyword">false</span>));
        <span class="hljs-comment">//上传文件到HDFS</span>
        copyFileToHDFS(<span class="hljs-string">"E:\\HDFSTest.txt"</span>,<span class="hljs-string">"/zhaojy/HDFSTest.txt"</span>);
        <span class="hljs-comment">//下载文件到本地</span>
        getFile(<span class="hljs-string">"/zhaojy/HDFSTest.txt"</span>,<span class="hljs-string">"D:\\HDFSTest.txt"</span>);
      <span class="hljs-comment">// getFile(HDFSFile,localFile);</span>
        <span class="hljs-comment">//删除文件</span>
        rmdir(<span class="hljs-string">"/zhaojy2"</span>);
        <span class="hljs-comment">//读取文件</span>
        readFile(<span class="hljs-string">"/zhaojy/HDFSTest.txt"</span>);
    }</code></pre>

<p>注意： 测试时， 一个个调用。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>