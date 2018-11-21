---
layout:     post
title:      HDFS的JAVA API 操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-github-gist">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<hr>



<h3 id="hdfs-的应用开发">HDFS 的应用开发</h3>

<blockquote>
  <p>HDFS 在生产应用中主要是客户端的开发，其核心步骤是从 HDFS 提供的 api中构造一个 HDFS 的访问客户端对象，然后通过该客户端对象操作（增删改查）HDFS 上的文件。</p>
</blockquote>

<p>HDFS基于java的API操作，需要本机创建HDFS客户端： <br>
<img src="https://img-blog.csdn.net/20171206234046078?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2VpeGluXzM5MTU4Mjcx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<hr>



<h3 id="一配置-windows-平台-hadoop-环境">（一）配置 windows 平台 Hadoop 环境</h3>

<ul>
<li>在 windows 上做 HDFS 客户端应用开发，需要设置 Hadoop 环境,而且要求是windows 平台编译的 Hadoop,不然会报以下的错误:</li>
</ul>



<pre class="prettyprint"><code class="language-shell hljs livecodeserver">Failed <span class="hljs-built_in">to</span> locate <span class="hljs-operator">the</span> winutils binary <span class="hljs-operator">in</span> <span class="hljs-operator">the</span> hadoop binary path java.io.IOException: Could <span class="hljs-operator">not</span>
locate executable <span class="hljs-constant">null</span>\bin\winutils.exe <span class="hljs-operator">in</span> <span class="hljs-operator">the</span> Hadoop binaries.</code></pre>

<p><strong>为此我们需要进行如下的操作：</strong> <br>
A、在 windows 平台下编译 Hadoop 源码（参考资料编译：<a href="http://blog.csdn.net/weixin_39158271/article/details/78722648" rel="nofollow" target="_blank">Windows 源码编译Hadoop 2.7.4步骤详解</a>，但不推荐） <br>
B、使用已经编译好的 Windows 版本 Hadoop：hadoop-2.7.4-with-windows.tar.gz</p>

<blockquote>
  <p>有兴趣推荐下载链接：<a href="http://download.csdn.net/download/weixin_39158271/10148786" rel="nofollow" target="_blank">Windows 源码编译Hadoop 2.7.4</a></p>
</blockquote>

<p>C、解压一份到 windows 的任意一个目录下 <br>
D、在 windows 系统中配置 HADOOP_HOME 指向你解压的安装包目录 <br>
E、在 windows 系统的 path 变量中加入 HADOOP_HOME 的 bin 目录</p>

<hr>



<h3 id="二-构造客户端对象">（二） 构造客户端对象</h3>

<p>在 java 中操作 HDFS，主要涉及以下 Class：</p>

<ul>
<li>Configuration：该类的对象封转了客户端或者服务器的配置;</li>
<li>FileSystem：该类的对象是一个文件系统对象，可以用该对象的一些方法来对文件进行操作，通过FileSystem 静态方法 get 获得该对象。 <br>
<strong>FileSystem fs = FileSystem.get(conf)</strong> <br>
get 方法从 conf 中的一个参数 fs.defaultFS 的配置值判断具体是什么类型的文件系统。 <br>
如果我们的代码中没有指定 fs.defaultFS，并且工程 classpath下也没有给定相应的配置，conf中的默认值就来自于hadoop的jar包中的core-default.xml ，默认值为file:/// ,则获取的将不是一个DistributedFileSystem 的实例，而是一个本地文件系统的客户端对象。</li>
</ul>

<hr>



<h3 id="三小试牛刀注意保证hadoop集群正常启动状态">（三）小试牛刀：（注意保证Hadoop集群正常启动状态）</h3>

<p><em>框架封装的 API 操作：</em></p>



<pre class="prettyprint"><code class="language-shell hljs cs">Configuration conf = <span class="hljs-keyword">new</span> Configuration();
<span class="hljs-comment">//这里指定使用的是 hdfs 文件系统</span>
conf.<span class="hljs-keyword">set</span>(<span class="hljs-string">"fs.defaultFS"</span>, <span class="hljs-string">"hdfs://node-01:9000"</span>);
<span class="hljs-comment">//需要配置本地hosts文件192.168.10.201  node-01主机节点</span></code></pre>

<p><img src="https://img-blog.csdn.net/20171206234403890?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2VpeGluXzM5MTU4Mjcx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>



<pre class="prettyprint"><code class="language-shell hljs avrasm">//通过如下的方式进行客户端身份的设置
System<span class="hljs-preprocessor">.setProperty</span>(<span class="hljs-string">"HADOOP_USER_NAME"</span>, <span class="hljs-string">"root"</span>)<span class="hljs-comment">;</span>
//通过 FileSystem 的静态方法获取文件系统客户端对象
FileSystem fs = FileSystem<span class="hljs-preprocessor">.get</span>(conf)<span class="hljs-comment">;</span>
//也可以通过如下的方式去指定文件系统的类型 并且同时设置用户身份
//FileSystem fs = FileSystem<span class="hljs-preprocessor">.get</span>(new URI(<span class="hljs-string">"hdfs://node-21:9000"</span>), conf, <span class="hljs-string">"root"</span>)<span class="hljs-comment">;</span>
//列出 hdfs 根目录下的所有文件信息
RemoteIterator&lt;LocatedFileStatus&gt; listFiles = fs<span class="hljs-preprocessor">.listFiles</span>(new Path(<span class="hljs-string">"/"</span>), false)<span class="hljs-comment">;</span>
//遍历出我们得到的指定文件路径的迭代器 获取相应的文件信息
while (listFiles<span class="hljs-preprocessor">.hasNext</span>()) {
LocatedFileStatus fileStatus = listFiles<span class="hljs-preprocessor">.next</span>()<span class="hljs-comment">;</span>
String name = fileStatus<span class="hljs-preprocessor">.getPath</span>()<span class="hljs-preprocessor">.getName</span>()<span class="hljs-comment">;</span>
System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(name)<span class="hljs-comment">;</span>
}
//关闭我们的文件系统
fs<span class="hljs-preprocessor">.close</span>()<span class="hljs-comment">;</span></code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>