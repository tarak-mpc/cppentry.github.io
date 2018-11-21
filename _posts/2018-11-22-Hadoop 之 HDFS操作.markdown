---
layout:     post
title:      Hadoop 之 HDFS操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-dracula">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h4 id="hdfs的设计">HDFS的设计</h4>

<blockquote>
  <p>HDFS 以<strong>流式数据</strong>访问模式来存储<strong>超大文件</strong>，运行于<strong>商用硬件集群</strong>上。HDFS是分布式文件系统</p>
</blockquote>



<h4 id="hdfs的数据块">HDFS的数据块</h4>

<blockquote>
  <p>HDFS的数据块一般的大小是128MB <br>
  <strong>PS：</strong> 寻址速度10ms  硬盘读取速度100MB/s 一般寻址速度是数据IO的百分之一 <br>
  我们以拷贝一个大于128M的文件为例子 <br>
  步骤一： 首先找到大于128MB的文件，将文件拷贝到hdfs下，hdfs只是一个逻辑上的文件系统 <br>
  hdfs dfs -copyFromLocal /Users/zzjmay/Downloads/pycharm-professional-2016.3.1.dmg  /zzjmay/hadoop/ <br>
  本地文件拷贝 copyFromLocal，可以通过WebUI看到，<strong>一个268MB的文件被切割成了三个数据块</strong>，其中需要说明的是BlockId是其在数据节点中存储的ID <br>
  <img src="https://upload-images.jianshu.io/upload_images/6706159-ba686d16d99771d4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240" alt="image.png" title=""></p>
</blockquote>



<h4 id="hdfs的namenode和datanode">HDFS的Namenode和datanode</h4>

<blockquote>
  <p><strong>NameNode</strong>: 名称节点，维护文件系统树和以及树中的文件和目录。说白了，就是一个目录。 但是这个目录有非常的重要，里面存放了命名空间的镜像文件和编辑日志文件，用户拼接数据节点的信息（存放目录 /Users/zzjmay/hadoopwork/hadoop_tmp/dfs/name/） <br>
  <strong>datanode</strong>：数据节点，用于存放数据块的节点(存放目录/Users/zzjmay/hadoopwork/hadoop_tmp/dfs/data/ ) <br>
  <strong>注意</strong>： <br>
  1. 正常开发中，名称节点和数据节点是要分开部署的 <br>
  2. 名称节点要做好容灾措施，两种方式：第一种，配置远程的文件系统，以保证一致性 第二种，创建辅助的名称节点 <br>
  <img src="https://upload-images.jianshu.io/upload_images/6706159-74a2d183e19f925c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240" alt="image.png" title=""></p>
  
  <p>回到之前说的pycharm的分割的数据块存放的路径 <br>
  /Users/zzjmay/hadoopwork/hadoop_tmp/dfs/data/current/BP-1423851909-127.0.0.1-1522918427220/current/finalized/subdir0/subdir0</p>
  
  <p>Hadoop的采用的是java的切割技术，只需要将这三个数据块拼接在一起就可以回到原来的文件，亲测有效</p>
</blockquote>

<p>namenode只存放元数据信息，不会存储块信息。当datanode启动后，会主动请求namenode，去报备对应的快列表</p>



<h4 id="java-操作hdfs文件系统步骤maven模式下">Java 操作HDFS文件系统步骤（Maven模式下）</h4>



<h5 id="步骤1-创建maven项目不说">步骤1 创建maven项目（不说）</h5>



<h5 id="步骤2-引入hadoop的依赖">步骤2 引入hadoop的依赖</h5>



<pre class="prettyprint"><code class="language-java hljs "> &lt;dependency&gt;
            &lt;groupId&gt;org.apache.hadoop&lt;/groupId&gt;
            &lt;artifactId&gt;hadoop-client&lt;/artifactId&gt;
            &lt;version&gt;<span class="hljs-number">2.7</span><span class="hljs-number">.3</span>&lt;/version&gt;
  &lt;/dependency&gt;</code></pre>



<h5 id="步骤3-写代码了">步骤3 写代码了</h5>

<p>先介绍几个重要的类</p>



<h4 id="filesystem">FileSystem</h4>

<blockquote>
  <p>定义了Hadoop的文件系统的API接口，提供了多种构造函数</p>
</blockquote>



<h4 id="configuration">Configuration</h4>

<blockquote>
  <p>这个类的作用有两个，1. 读取core-site类文件和设置相关配置信息 2. 后面会用到它来传递参数</p>
</blockquote>



<h4 id="fsdatainputstream">FSDataInputStream</h4>

<blockquote>
  <p>一般通过FileSystem.open（Path）获取. hdfs的输入流</p>
</blockquote>



<h4 id="fsdataoutputstream">FSDataOutputStream</h4>

<blockquote>
  <p>HDFS的输出流，比如向创建一个文件FileSystem.create就会产生这个流</p>
</blockquote>



<h4 id="filestatus">FileStatus</h4>

<blockquote>
  <p>存放的是Hdfs文件目录或文件的相关信息</p>
</blockquote>



<h4 id="按照下面的代码对于hdfs的操作你就可以基本掌握了">按照下面的代码，对于Hdfs的操作你就可以基本掌握了</h4>



<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-keyword">package</span> com.zzjmay.hdfsapi;

<span class="hljs-keyword">import</span> com.zzjmay.utils.ConfUtils;
<span class="hljs-keyword">import</span> org.apache.hadoop.conf.Configuration;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.*;
<span class="hljs-keyword">import</span> org.apache.hadoop.io.IOUtils;
<span class="hljs-keyword">import</span> org.apache.hadoop.util.Progressable;
<span class="hljs-keyword">import</span> org.junit.Test;

<span class="hljs-keyword">import</span> java.io.BufferedInputStream;
<span class="hljs-keyword">import</span> java.io.FileInputStream;
<span class="hljs-keyword">import</span> java.io.InputStream;
<span class="hljs-keyword">import</span> java.net.URI;
<span class="hljs-keyword">import</span> java.net.URL;
<span class="hljs-keyword">import</span> java.net.URLConnection;

<span class="hljs-javadoc">/**
 * 测试Hdfs Api
 * Created by zzjmay on 2018/4/7.
 */</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">TestHdfsApi</span> {</span>
    <span class="hljs-javadoc">/**
     * 这么做的原因是java程序是不识别hdfs协议的，通过加载这个方法让URL识别
     */</span>
    <span class="hljs-keyword">static</span> {
        URL.setURLStreamHandlerFactory(<span class="hljs-keyword">new</span> FsUrlStreamHandlerFactory());
    }

    <span class="hljs-javadoc">/**
     * 读取hdfs文件 URL读取数据
     */</span>
    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">readFileByURL</span>() {

        <span class="hljs-comment">//JDK7以上的写法，IO流在try中会自动关闭</span>
        <span class="hljs-keyword">try</span> (InputStream inputStream = <span class="hljs-keyword">new</span> URL(<span class="hljs-string">"hdfs://localhost:8000/zzjmay/hadoop/sumApp.txt"</span>).openStream();) {

            IOUtils.copyBytes(inputStream, System.out, <span class="hljs-number">4096</span>, <span class="hljs-keyword">false</span>);
        } <span class="hljs-keyword">catch</span> (Exception e) {
            System.out.println(<span class="hljs-string">"##出错了"</span>);
        }

    }

    <span class="hljs-javadoc">/**
     * 通过Hadoop API访问文件
     * 相较于第一种URL的方式，这种方式更常用
     * FileSystem API 来打开一个文件的输入流
     */</span>
    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">readFileByAPI</span>() {
        <span class="hljs-comment">//1.封装了客户端或服务器端的配置</span>
        Configuration conf = <span class="hljs-keyword">new</span> Configuration();
        FSDataInputStream inputStream = <span class="hljs-keyword">null</span>;
        <span class="hljs-keyword">try</span> {
            <span class="hljs-comment">//书本上的方式，配置URI和conf</span>
            <span class="hljs-comment">//2.通过fileSystem的静态方法get获取FileSystem 对象</span>
            FileSystem fileSystem = FileSystem.get(URI.create(<span class="hljs-string">"hdfs://127.0.0.1:8000/user/zzjmay/笔记.txt"</span>), conf);
            <span class="hljs-comment">//3.获取hdfs逻辑目录下的文件</span>
            inputStream = fileSystem.open(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/user/zzjmay/笔记.txt"</span>));
            <span class="hljs-comment">//4.读取文件输入流</span>
            IOUtils.copyBytes(inputStream, System.out, <span class="hljs-number">4096</span>, <span class="hljs-keyword">false</span>);

        } <span class="hljs-keyword">catch</span> (Exception e) {
            e.printStackTrace();
        } <span class="hljs-keyword">finally</span> {
            IOUtils.closeStream(inputStream);
        }


    }

    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">readFileByAPI2</span>() {
        <span class="hljs-comment">//1.封装了客户端或服务器端的配置</span>
        Configuration conf = <span class="hljs-keyword">new</span> Configuration();
        <span class="hljs-comment">//因为目前采用的是伪分布模式,需要设置读取的名称节点的主机名,这里要注意不能用localhost</span>
        <span class="hljs-comment">//在hadoop中，fs.default.name 已经替换为fs.defaultFS</span>
        conf.set(<span class="hljs-string">"fs.defaultFS"</span>, <span class="hljs-string">"hdfs://127.0.0.1:8000"</span>);
        FSDataInputStream inputStream = <span class="hljs-keyword">null</span>;
        <span class="hljs-keyword">try</span> {
            FileSystem fileSystem = FileSystem.get(conf);
            <span class="hljs-comment">//3.获取hdfs逻辑目录下的文件</span>
            inputStream = fileSystem.open(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/user/zzjmay/笔记.txt"</span>));
            <span class="hljs-comment">//4.读取文件输入流</span>
            IOUtils.copyBytes(inputStream, System.out, <span class="hljs-number">4096</span>, <span class="hljs-keyword">false</span>);

        } <span class="hljs-keyword">catch</span> (Exception e) {
            e.printStackTrace();
        } <span class="hljs-keyword">finally</span> {
            IOUtils.closeStream(inputStream);
        }
    }

    <span class="hljs-javadoc">/**
     * 在HDFS中创建目录
     */</span>
    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">mkdir</span>() {
        <span class="hljs-comment">//1.加载conf基础配置</span>
        Configuration conf = <span class="hljs-keyword">new</span> Configuration();
        conf.set(<span class="hljs-string">"fs.defaultFS"</span>, <span class="hljs-string">"hdfs://127.0.0.1:8000"</span>);

        <span class="hljs-keyword">try</span> {
            <span class="hljs-comment">//2。获取操作HDFS的API</span>
            FileSystem fileSystem = FileSystem.get(conf);
            <span class="hljs-comment">//3.获取FS输出流</span>
            fileSystem.mkdirs(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/user/zzjmay/hadoop"</span>));

        } <span class="hljs-keyword">catch</span> (Exception e) {
            e.printStackTrace();
        }
    }

    <span class="hljs-javadoc">/**
     * 写入文件
     */</span>
    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">putFile</span>() {
        FSDataOutputStream outputStream = <span class="hljs-keyword">null</span>;
        <span class="hljs-keyword">try</span> {
            FileSystem fileSystem = FileSystem.get(ConfUtils.getConf());
            outputStream = fileSystem.create(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/user/zzjmay/hadoop/test.txt"</span>));

            outputStream.write(<span class="hljs-string">"helloword"</span>.getBytes());

        } <span class="hljs-keyword">catch</span> (Exception e) {
            e.printStackTrace();
        } <span class="hljs-keyword">finally</span> {
            IOUtils.closeStream(outputStream);
        }
    }

    <span class="hljs-javadoc">/**
     * 删除文件
     */</span>
    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">delteFile</span>() {

        <span class="hljs-keyword">try</span> {
            FileSystem fileSystem = FileSystem.get(ConfUtils.getConf());
            fileSystem.delete(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/zzjmay/hadoop/pycharm-professional-2016.3.1.dmg"</span>),<span class="hljs-keyword">true</span>);

        } <span class="hljs-keyword">catch</span> (Exception e) {
            e.printStackTrace();
        }
    }

    <span class="hljs-javadoc">/**
     * 从本地复制文件当hdfs上
     * 并且显示进度
     */</span>
    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">testProcess</span>(){
        InputStream inputStream = <span class="hljs-keyword">null</span>;
        <span class="hljs-keyword">try</span> {
            <span class="hljs-comment">//1.读取本地文件</span>
             inputStream = <span class="hljs-keyword">new</span> BufferedInputStream(<span class="hljs-keyword">new</span> FileInputStream(<span class="hljs-string">"/Users/zzjmay/hadoopwork/data/test.txt"</span>));
             <span class="hljs-comment">//2. 获取操作hdfs操作流</span>
            FileSystem fileSystem = FileSystem.get(ConfUtils.getConf());
            FSDataOutputStream outputStream = fileSystem.create(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/user/zzjmay/process.txt"</span>), <span class="hljs-keyword">new</span> Progressable() {
                <span class="hljs-annotation">@Override</span>
                <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">progress</span>() {
                    System.out.println(<span class="hljs-string">"####测试哈哈哈"</span>);
                }
            });
            IOUtils.copyBytes(inputStream,outputStream,<span class="hljs-number">4096</span>,<span class="hljs-keyword">true</span>);
        }<span class="hljs-keyword">catch</span> (Exception e){

        }

    }
    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">getFileStatus</span>(){
        <span class="hljs-keyword">try</span> {
            FileSystem fileSystem = FileSystem.get(ConfUtils.getConf());
            <span class="hljs-comment">//获取文件的信息FileStauts</span>
            FileStatus fileStatus = fileSystem.getFileStatus(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/user/zzjmay/process.txt"</span>));
            System.out.println(<span class="hljs-string">"块大小"</span>+fileStatus.getBlockSize());
        }<span class="hljs-keyword">catch</span> (Exception e){

        }

    }


}
</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>