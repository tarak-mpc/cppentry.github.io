---
layout:     post
title:      hadoop-2 HDFS API
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/sima64/article/details/49456887				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<ul>
<li><p><strong>1.HDFS读取数据</strong></p></li>
</ul>



<pre class="prettyprint"><code class=" hljs cs">
 <span class="hljs-number">1.</span> 使用Hadoop 的URL读取数据:使用URL打开一个数据流,从中读取数据.
    <span class="hljs-comment">/*需要FsUrlStreamHandlerFactory实例来调用在URL中的setURLStreamHandlerFactory方法
    *   该方法在一个java虚拟机中只能调用一次,因此放在一个静态方法中.
    */</span>
    <span class="hljs-keyword">static</span>{
     URL.setURLStreamHandlerFactory(<span class="hljs-keyword">new</span> FsUrlStreamHandlerFactory());
    }
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args){
        InputStream input = <span class="hljs-keyword">null</span>;
        <span class="hljs-keyword">try</span>{
       input = <span class="hljs-keyword">new</span> URL(<span class="hljs-string">"hdfs://masterIP/path"</span>).openStream();
       IOUtils.copyBytes(input,System.<span class="hljs-keyword">out</span>,<span class="hljs-number">2048</span>,<span class="hljs-keyword">false</span>);
        }<span class="hljs-keyword">finally</span>{
       input.close();
        }
    }

 <span class="hljs-number">2.</span> 使用FileSystem 读取数据.hadoop把一个文件视为一个 Path对象,把一个路径视为一个URI
        获取FileSystem 有两种方法:
        <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> FileSystem <span class="hljs-title">get</span>(Configuration conf)
            默认返回本地文件系统.
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> FileSystem <span class="hljs-title">get</span>(URI uri,Configuration conf)---&gt;
        使用指定的URI获取文件系统,如果指定的URI中没有指定的方案(即没有获得相应的文件系统),返回本地文件系统.
    Configuratin 封装了一个服务端或者客户端的配置,用路径读取配置文件(conf/core.site.xml).

    InputStream input = <span class="hljs-keyword">null</span>;
    <span class="hljs-keyword">try</span>{
        Configuration conf = <span class="hljs-keyword">new</span> Configuration();
        URI uri = URI.create(<span class="hljs-string">"hdfs://192.168.1.128:9000/work/word.txt"</span>);
        FileSystem fileSystem = FileSystem.<span class="hljs-keyword">get</span>(uri, conf);
        <span class="hljs-comment">//该方法默认使用4KB缓冲区.</span>
        input = fileSystem.open(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"hdfs://192.168.1.128:9000/work/word.txt"</span>));
        IOUtils.copyBytes(input, System.<span class="hljs-keyword">out</span>, <span class="hljs-number">2048</span>,<span class="hljs-keyword">false</span>);
    }<span class="hljs-keyword">catch</span>(Exception e ){
        e.printStackTrace();
    }<span class="hljs-keyword">finally</span>{
        <span class="hljs-keyword">try</span> {
            input.close();
        } <span class="hljs-keyword">catch</span> (IOException e) {
            e.printStackTrace();
        }
    }
 <span class="hljs-number">3.</span> 


</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>