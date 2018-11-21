---
layout:     post
title:      hadoop fs api
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/daguanjia11/article/details/77933279				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>hadoop的环境搭建好之后，本篇博客来使用一下hadoop提供的分布式文件系统(hdfs)的java api。 <br>
我做了一个简单的例子，包含文件的读取、写入、删除、创建文件夹、读取文件列表等基本操作。最后会贴出来maven依赖和完整的java代码。</p>



<h2 id="连接到hdfs">连接到hdfs</h2>

<p>只需要通过一个hdfs的uri，即可连接到hdfs。如果连接失败的话，检查一下你的hdfs是否成功启动，以及是不是9000端口。</p>



<pre class="prettyprint"><code class=" hljs vhdl"><span class="hljs-typename">String</span> uri = <span class="hljs-string">"hdfs://localhost:9000"</span>;
<span class="hljs-keyword">Configuration</span> conf = <span class="hljs-keyword">new</span> <span class="hljs-keyword">Configuration</span>();
FileSystem fs = FileSystem.get(URI.create(uri), conf);</code></pre>



<h2 id="创建文件夹">创建文件夹</h2>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">public</span> <span class="hljs-keyword">boolean</span> <span class="hljs-title">mkdirs</span>(Path p) <span class="hljs-keyword">throws</span> IOException</code></pre>

<p>通过<code>org.apache.hadoop.fs.FileSystem.mkdirs()</code>方法来创建文件夹。下面是示例代码</p>



<pre class="prettyprint"><code class=" hljs avrasm">String folder = <span class="hljs-string">"/user/zdk/test-fs"</span><span class="hljs-comment">;</span>
Path newFolderPath = new Path(folder)<span class="hljs-comment">;</span>
if (!fs<span class="hljs-preprocessor">.exists</span>(newFolderPath)) {
    fs<span class="hljs-preprocessor">.mkdirs</span>(newFolderPath)<span class="hljs-comment">;</span>
    System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"new folder created:"</span> + folder)<span class="hljs-comment">;</span>
}</code></pre>



<h2 id="创建文件">创建文件</h2>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">public</span> FSDataOutputStream <span class="hljs-title">create</span>(Path p) <span class="hljs-keyword">throws</span> IOException</code></pre>

<p>通过<code>org.apache.hadoop.fs.FileSystem.create()</code>方法来创建文件。下面是示例代码</p>



<pre class="prettyprint"><code class=" hljs avrasm">String fileName = <span class="hljs-string">"file1.txt"</span><span class="hljs-comment">;</span>
String fileContent = <span class="hljs-string">"hello,hadoop!"</span><span class="hljs-comment">;</span>
Path newFilePath = new Path(newFolderPath + <span class="hljs-string">"/"</span> + fileName)<span class="hljs-comment">;</span>
FSDataOutputStream output = fs<span class="hljs-preprocessor">.create</span>(newFilePath)<span class="hljs-comment">;</span>
output<span class="hljs-preprocessor">.writeBytes</span>(fileContent)<span class="hljs-comment">;</span>
output<span class="hljs-preprocessor">.close</span>()<span class="hljs-comment">;</span>
System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"file content write end"</span>)<span class="hljs-comment">;</span></code></pre>

<p><code>org.apache.hadoop.fs.FileSystem.create()</code>方法返回一个<code>org.apache.hadoop.fs.FSDataOutputStream</code>对象，通过这个对象写入数据时，它内部会调用namenode创建元数据，然后将数据写入到datanode中，在这个过程中，数据会被直接复制N份，这个N取决于<code>/etc/hadoop/hdfs-site.xml</code>中的<code>dfs.replication</code>值。如果某个datanode在写入数据时发生故障的话，会自动写入另一个datanode中。这个复杂的过程都被封装到了<code>org.apache.hadoop.fs.FSDataOutputStream</code>中。</p>



<h2 id="读取文件列表">读取文件列表</h2>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">public</span> FileStatus[] <span class="hljs-title">listStatus</span>(Path p) <span class="hljs-keyword">throws</span> IOException</code></pre>

<p>这个方法可以列出一个文件夹下的所有文件</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">public</span> FileStatus[] <span class="hljs-title">globStatus</span>(Path pathPattern) <span class="hljs-keyword">throws</span> IOException</code></pre>

<p>这个方法可以列出符合某个pattern的所有文件</p>

<p>下面是示例代码</p>



<pre class="prettyprint"><code class=" hljs avrasm">// list files
FileStatus[] fileStatusList = fs<span class="hljs-preprocessor">.listStatus</span>(newFolderPath)<span class="hljs-comment">;</span>
// list all text files
//FileStatus[] fileStatusList = fs<span class="hljs-preprocessor">.globStatus</span>(new Path(folder + <span class="hljs-string">"/*.txt"</span>))<span class="hljs-comment">;</span>
int length = fileStatusList<span class="hljs-preprocessor">.length</span><span class="hljs-comment">;</span>
System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"found "</span> + length + <span class="hljs-string">" files"</span>)<span class="hljs-comment">;</span>
for (FileStatus status : fileStatusList) {
    System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(status<span class="hljs-preprocessor">.getPath</span>()<span class="hljs-preprocessor">.toString</span>())<span class="hljs-comment">;</span>
}</code></pre>



<h2 id="读取文件内容">读取文件内容</h2>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">public</span> FSDataInputStream <span class="hljs-title">open</span>(Path p) <span class="hljs-keyword">throws</span> IOException</code></pre>

<p><code>org.apache.hadoop.fs.FileSystem.open()</code>方法返回一个<code>org.apache.hadoop.fs.FSDataInputStream</code>类，这个类会先调用namenode来拿到包含该文件的所有block，对于每个block，namenode会返回多个datanode的地址，这些地址是按照他们与距离的客户端排好序的，客户端会通过最近的一个datanode来获取数据，如果最近的datanode已损坏，则会使用下一个datanode来获取数据。这个复杂的过程被封装在了<code>org.apache.hadoop.fs.FSDataInputStream</code>类中。</p>

<p>下面是示例代码</p>



<pre class="prettyprint"><code class=" hljs avrasm">FSDataInputStream input = fs<span class="hljs-preprocessor">.open</span>(newFilePath)<span class="hljs-comment">;</span>
String content= IOUtils<span class="hljs-preprocessor">.toString</span>(input)<span class="hljs-comment">;</span>
input<span class="hljs-preprocessor">.close</span>()<span class="hljs-comment">;</span></code></pre>



<h2 id="删除文件和文件夹">删除文件和文件夹</h2>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">public</span> <span class="hljs-keyword">boolean</span> <span class="hljs-title">delete</span>(Path path, <span class="hljs-keyword">boolean</span> recursive) <span class="hljs-keyword">throws</span> IOException</code></pre>

<p>如果<code>path</code>是一个文件夹的话，<code>recursive</code>参数必须是true，否则会抛出异常。如果<code>path</code>是文件的话，<code>recursive</code>就无所谓true或false了。下面是示例代码</p>



<pre class="prettyprint"><code class=" hljs sql">// <span class="hljs-operator"><span class="hljs-keyword">delete</span> file
fs.<span class="hljs-keyword">delete</span>(newFilePath, <span class="hljs-keyword">false</span>);</span>

// <span class="hljs-operator"><span class="hljs-keyword">delete</span> folder
fs.<span class="hljs-keyword">delete</span>(newFolderPath, <span class="hljs-keyword">true</span>);</span></code></pre>

<p>下面我会贴出来完整的maven配置文件和java代码</p>



<h2 id="完整的maven配置文件">完整的maven配置文件</h2>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">project</span> <span class="hljs-attribute">xmlns</span>=<span class="hljs-value">"http://maven.apache.org/POM/4.0.0"</span> <span class="hljs-attribute">xmlns:xsi</span>=<span class="hljs-value">"http://www.w3.org/2001/XMLSchema-instance"</span>
    <span class="hljs-attribute">xsi:schemaLocation</span>=<span class="hljs-value">"http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">modelVersion</span>&gt;</span>4.0.0<span class="hljs-tag">&lt;/<span class="hljs-title">modelVersion</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.zdk<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>hadoop-demo<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>0.0.1-SNAPSHOT<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">packaging</span>&gt;</span>jar<span class="hljs-tag">&lt;/<span class="hljs-title">packaging</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hadoop-demo<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">url</span>&gt;</span>http://maven.apache.org<span class="hljs-tag">&lt;/<span class="hljs-title">url</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">properties</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">project.build.sourceEncoding</span>&gt;</span>UTF-8<span class="hljs-tag">&lt;/<span class="hljs-title">project.build.sourceEncoding</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">hadoop.version</span>&gt;</span>2.8.1<span class="hljs-tag">&lt;/<span class="hljs-title">hadoop.version</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">log4j.version</span>&gt;</span>1.2.17<span class="hljs-tag">&lt;/<span class="hljs-title">log4j.version</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">commons-io.version</span>&gt;</span>1.3.2<span class="hljs-tag">&lt;/<span class="hljs-title">commons-io.version</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">properties</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">dependencies</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>junit<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>junit<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>3.8.1<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">scope</span>&gt;</span>test<span class="hljs-tag">&lt;/<span class="hljs-title">scope</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-comment">&lt;!-- Hadoop main client artifact --&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.hadoop<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>hadoop-client<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>${hadoop.version}<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.commons<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>commons-io<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>${commons-io.version}<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">dependencies</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">build</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">plugins</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">plugin</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>io.saagie<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>saagie-maven-plugin<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.0.2<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
                    <span class="hljs-tag">&lt;<span class="hljs-title">platformId</span>&gt;</span>1<span class="hljs-tag">&lt;/<span class="hljs-title">platformId</span>&gt;</span>
                    <span class="hljs-tag">&lt;<span class="hljs-title">jobName</span>&gt;</span>example-java-read-and-write-from-hdfs<span class="hljs-tag">&lt;/<span class="hljs-title">jobName</span>&gt;</span>
                    <span class="hljs-tag">&lt;<span class="hljs-title">jobCategory</span>&gt;</span>extract<span class="hljs-tag">&lt;/<span class="hljs-title">jobCategory</span>&gt;</span>
                <span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span>
            <span class="hljs-tag">&lt;/<span class="hljs-title">plugin</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">plugin</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.maven.plugins<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>maven-compiler-plugin<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
                    <span class="hljs-tag">&lt;<span class="hljs-title">source</span>&gt;</span>1.8<span class="hljs-tag">&lt;/<span class="hljs-title">source</span>&gt;</span>
                    <span class="hljs-tag">&lt;<span class="hljs-title">target</span>&gt;</span>1.8<span class="hljs-tag">&lt;/<span class="hljs-title">target</span>&gt;</span>
                <span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span>
            <span class="hljs-tag">&lt;/<span class="hljs-title">plugin</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">plugin</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>maven-assembly-plugin<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
                    <span class="hljs-tag">&lt;<span class="hljs-title">archive</span>&gt;</span>
                        <span class="hljs-tag">&lt;<span class="hljs-title">manifest</span>&gt;</span>
                            <span class="hljs-tag">&lt;<span class="hljs-title">mainClass</span>&gt;</span>io.saagie.example.hdfs.Main<span class="hljs-tag">&lt;/<span class="hljs-title">mainClass</span>&gt;</span>
                        <span class="hljs-tag">&lt;/<span class="hljs-title">manifest</span>&gt;</span>
                    <span class="hljs-tag">&lt;/<span class="hljs-title">archive</span>&gt;</span>
                    <span class="hljs-tag">&lt;<span class="hljs-title">descriptorRefs</span>&gt;</span>
                        <span class="hljs-tag">&lt;<span class="hljs-title">descriptorRef</span>&gt;</span>jar-with-dependencies<span class="hljs-tag">&lt;/<span class="hljs-title">descriptorRef</span>&gt;</span>
                    <span class="hljs-tag">&lt;/<span class="hljs-title">descriptorRefs</span>&gt;</span>
                <span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">executions</span>&gt;</span>
                    <span class="hljs-tag">&lt;<span class="hljs-title">execution</span>&gt;</span>
                        <span class="hljs-tag">&lt;<span class="hljs-title">id</span>&gt;</span>make-assembly<span class="hljs-tag">&lt;/<span class="hljs-title">id</span>&gt;</span> <span class="hljs-comment">&lt;!-- this is used for inheritance merges --&gt;</span>
                        <span class="hljs-tag">&lt;<span class="hljs-title">phase</span>&gt;</span>package<span class="hljs-tag">&lt;/<span class="hljs-title">phase</span>&gt;</span> <span class="hljs-comment">&lt;!-- bind to the packaging phase --&gt;</span>
                        <span class="hljs-tag">&lt;<span class="hljs-title">goals</span>&gt;</span>
                            <span class="hljs-tag">&lt;<span class="hljs-title">goal</span>&gt;</span>single<span class="hljs-tag">&lt;/<span class="hljs-title">goal</span>&gt;</span>
                        <span class="hljs-tag">&lt;/<span class="hljs-title">goals</span>&gt;</span>
                    <span class="hljs-tag">&lt;/<span class="hljs-title">execution</span>&gt;</span>
                <span class="hljs-tag">&lt;/<span class="hljs-title">executions</span>&gt;</span>
            <span class="hljs-tag">&lt;/<span class="hljs-title">plugin</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">plugins</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">build</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">project</span>&gt;</span></code></pre>



<h2 id="完整的java代码">完整的java代码</h2>



<pre class="prettyprint"><code class=" hljs avrasm">package org<span class="hljs-preprocessor">.zdk</span><span class="hljs-preprocessor">.hadoop</span>_demo<span class="hljs-comment">;</span>

import java<span class="hljs-preprocessor">.net</span><span class="hljs-preprocessor">.URI</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.commons</span><span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.IOUtils</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.conf</span><span class="hljs-preprocessor">.Configuration</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.fs</span><span class="hljs-preprocessor">.FSDataInputStream</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.fs</span><span class="hljs-preprocessor">.FSDataOutputStream</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.fs</span><span class="hljs-preprocessor">.FileStatus</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.fs</span><span class="hljs-preprocessor">.FileSystem</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.fs</span><span class="hljs-preprocessor">.Path</span><span class="hljs-comment">;</span>

public class App {
    public static void main(String[] args) throws Exception {
        String uri = <span class="hljs-string">"hdfs://localhost:9000"</span><span class="hljs-comment">;</span>
        Configuration conf = new Configuration()<span class="hljs-comment">;</span>
        FileSystem fs = FileSystem<span class="hljs-preprocessor">.get</span>(URI<span class="hljs-preprocessor">.create</span>(uri), conf)<span class="hljs-comment">;</span>

        // create new folder
        String folder = <span class="hljs-string">"/user/zdk/test-fs"</span><span class="hljs-comment">;</span>
        Path newFolderPath = new Path(folder)<span class="hljs-comment">;</span>
        if (!fs<span class="hljs-preprocessor">.exists</span>(newFolderPath)) {
            fs<span class="hljs-preprocessor">.mkdirs</span>(newFolderPath)<span class="hljs-comment">;</span>
            System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"new folder created:"</span> + folder)<span class="hljs-comment">;</span>
        }

        // create new file <span class="hljs-keyword">and</span> write content
        String fileName = <span class="hljs-string">"file1.txt"</span><span class="hljs-comment">;</span>
        String fileContent = <span class="hljs-string">"hello,hadoop!"</span><span class="hljs-comment">;</span>
        Path newFilePath = new Path(newFolderPath + <span class="hljs-string">"/"</span> + fileName)<span class="hljs-comment">;</span>
        FSDataOutputStream output = fs<span class="hljs-preprocessor">.create</span>(newFilePath)<span class="hljs-comment">;</span>
        output<span class="hljs-preprocessor">.writeBytes</span>(fileContent)<span class="hljs-comment">;</span>
        output<span class="hljs-preprocessor">.close</span>()<span class="hljs-comment">;</span>
        System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"file content write end"</span>)<span class="hljs-comment">;</span>

        // list files
        FileStatus[] fileStatusList = fs<span class="hljs-preprocessor">.listStatus</span>(newFolderPath)<span class="hljs-comment">;</span>
        // list all text files
        //FileStatus[] fileStatusList = fs<span class="hljs-preprocessor">.globStatus</span>(new Path(folder + <span class="hljs-string">"/*.txt"</span>))<span class="hljs-comment">;</span>
        int length = fileStatusList<span class="hljs-preprocessor">.length</span><span class="hljs-comment">;</span>
        System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"found "</span> + length + <span class="hljs-string">" files"</span>)<span class="hljs-comment">;</span>
        for (FileStatus status : fileStatusList) {
            System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(status<span class="hljs-preprocessor">.getPath</span>()<span class="hljs-preprocessor">.toString</span>())<span class="hljs-comment">;</span>
        }

        // read file content
        FSDataInputStream input = fs<span class="hljs-preprocessor">.open</span>(newFilePath)<span class="hljs-comment">;</span>
        String content = IOUtils<span class="hljs-preprocessor">.toString</span>(input)<span class="hljs-comment">;</span>
        System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(content)<span class="hljs-comment">;</span>
        input<span class="hljs-preprocessor">.close</span>()<span class="hljs-comment">;</span>

        // delete file
        fs<span class="hljs-preprocessor">.delete</span>(newFilePath, false)<span class="hljs-comment">;</span>

        // delete folder
        fs<span class="hljs-preprocessor">.delete</span>(newFolderPath, true)<span class="hljs-comment">;</span>

        // close fileSystem
        fs<span class="hljs-preprocessor">.close</span>()<span class="hljs-comment">;</span>

    }
}</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>