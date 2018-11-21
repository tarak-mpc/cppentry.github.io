---
layout:     post
title:      HDFS--Hadoop分布式文件系统
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div id="article_content" class="article_content">
<p><br></p>
<h1><a name="t0"></a>HDFS是什么</h1>
<p><br></p>
<h2><a name="t1"></a>HDFS设计特性和概念</h2>
<p><br></p>
<p>HDFS，全称是Hadoop Distributed Filesystem，是一个分布式的文件系统，以流式数据访问模式来存储超大文件（一次写入、多次读取）。</p>
<p><strong>HDFS具有如下设计特性：</strong></p>
<p>（1）处理超大文件，指的是GB、TB、PB级别的文件。百度、淘宝都有PB级别的HDFS，百度应该有国内最大规模的HDFS，几十PB。</p>
<p>（2）流式数据访问，一次写入，多次读取，所处理的场景中，读取整个数据的延迟比读取第一条记录的时间延迟重要。</p>
<p>（3）运行在普通商用PC即可，比如3万级别的普通PC服务器（16-32G ECC内存，8-16核CPU）。</p>
<p>（4）是为高数据吞吐量优化的，以高时间延迟为代价。</p>
<p>（5）推荐处理大量小文件，由于namenode将文件系统的元数据存储在内存中，故文件总数受制于namenode节点内存。根据经验，一个文件/目录/block大约占用150自己，所以亿级别文件还可以，10亿级别内存就不够了。</p>
<p>（6）对于写入，只能有一个写入操作，也只能把内容添加在文件的末尾。</p>
<p><strong>概念：</strong></p>
<p>（1）数据块（block），默认64M，一般用128M，相对于文件系统块（几K字节大小）、磁盘块（一般512毕节），HDFS的块设计明显大的多，这是为了最小为寻址开销（寻址占传输的百分比，比如：寻址10S，传输100MB/S，则寻址时间仅占传输时间的1%）。</p>
<p>（2）名称节点（namenode），是管理者，维护整个HDFS的文件系统树及树内所有的文件和目录。</p>
<p>（3）数据节点（datanode），是文件系统工作节点，根据namenode调度，存储并检索数据块，定期向namenode发送它所存储的块列表。</p>
<p><strong>namenode单点风险的2种解决办法：</strong></p>
<p>（1）备份那些组成文件系统元数据持久状态的文件，比如，持久状态写入本地磁盘的同时，写入一个远程的文件系统。</p>
<p>（2）运行一个辅助namecode，由于辅助namecode的滞后性，所以namecode损坏时，难免会丢失部分数据。</p>
<p><br></p>
<h2><a name="t2"></a>Hadoop抽象文件系统</h2>
<p><br></p>
<p>Hadoop有一个抽象文件系统，由org.apache.hadoop.fs.FileSystem定义，HDSF只是其中的一个实现。</p>
<p><strong>Hadoop所实现的文件系统列表，大致如下图所示：</strong></p>
<p><img src="https://img-blog.csdn.net/20140418161149984" alt=""><br></p>
<p>Hadoop对文件系统提供了许多接口，它一般使用URI方案来选取合适的文件系统实例进行交互。，比如如下代码：</p>
<p><span></span>String uri = "hdfs:///test/input/t/temperature.txt";<br><span></span>Configuration conf = new Configuration();<br><span></span>FileSystem fs = FileSystem.get(URI.create(uri), conf);<br></p>
<p>即时根据hdsfs://来判断，使用hdfs.DistributedFileSystem；如果改成file://，则使用fs.LocalFileSystem。</p>
<p><br></p>
<h1><a name="t3"></a>HDFS命令行接口</h1>
<p><br></p>
<p><strong>查看所有命令：</strong></p>
<p>hadoop fs -help</p>
<p>hadoop fsck -help</p>
<p><strong>也可以通过Web界面浏览文件系统：</strong>http://192.168.1.10:50070/</p>
<p><br></p>
<h1><a name="t4"></a>HDFS JAVA API</h1>
<p><br></p>
<h2><a name="t5"></a>从Hadoop URL中读取数据</h2>
<p><br></p>
<div class="dp-highlighter bg_java">
<div class="bar">
<div class="tools"><strong>[java]</strong> <a href="http://blog.csdn.net/puma_dong/article/details/24020793#" rel="nofollow" class="ViewSource" title="view plain">
view plain</a><a href="http://blog.csdn.net/puma_dong/article/details/24020793#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a><a href="http://blog.csdn.net/puma_dong/article/details/24020793#" rel="nofollow" class="PrintSource" title="print">print</a><a href="http://blog.csdn.net/puma_dong/article/details/24020793#" rel="nofollow" class="About" title="?">?</a><a href="https://code.csdn.net/snippets/300286" rel="nofollow" title="在CODE上查看代码片" style="text-indent:0;"><img src="https://code.csdn.net/assets/CODE_ico.png" alt="在CODE上查看代码片" height="12" width="12"></a><a href="https://code.csdn.net/snippets/300286/fork" rel="nofollow" title="派生到我的代码片" style="text-indent:0;"><img src="https://code.csdn.net/assets/ico_fork.svg" alt="派生到我的代码片" height="12" width="12"></a></div>
</div>
<ol class="dp-j" start="1"><li class="alt"><span><span class="keyword">import</span><span> java.io.InputStream;  </span></span></li><li><span><span class="keyword">import</span><span> java.net.URL;  </span></span></li><li class="alt"><span><span class="keyword">import</span><span> org.apache.hadoop.fs.FsUrlStreamHandlerFactory;  </span></span></li><li><span><span class="keyword">import</span><span> org.apache.zookeeper.common.IOUtils;  </span></span></li><li class="alt"><span>  </span></li><li><span><span class="keyword">public</span><span> </span><span class="keyword">class</span><span> URLCat {  </span></span></li><li class="alt"><span>    <span class="keyword">static</span><span> {  </span></span></li><li><span>        URL.setURLStreamHandlerFactory(<span class="keyword">new</span><span> FsUrlStreamHandlerFactory());  </span></span></li><li class="alt"><span>    }  </span></li><li><span>  </span></li><li class="alt"><span>    <span class="keyword">public</span><span> </span><span class="keyword">static</span><span> </span><span class="keyword">void</span><span> main(String[] args) </span><span class="keyword">throws</span><span> Exception {  </span></span></li><li><span>        InputStream in = <span class="keyword">null</span><span>;  </span></span></li><li class="alt"><span>        <span class="keyword">try</span><span> {  </span></span></li><li><span>            in = <span class="keyword">new</span><span> URL(</span><span class="string">"hdfs:///test/input/t/temperature.txt"</span><span>).openStream();  </span></span></li><li class="alt"><span>            IOUtils.copyBytes(in, System.out, <span class="number">4096</span><span>, </span><span class="keyword">false</span><span>);  </span></span></li><li><span>        } <span class="keyword">finally</span><span> {  </span></span></li><li class="alt"><span>            IOUtils.closeStream(in);  </span></li><li><span>        }  </span></li><li class="alt"><span>    }  </span></li><li><span>}  </span></li></ol></div>
<br>
把hdfs换成file，则可以直接处理当前本地文件系统。
<p>以上的方法是很简单的，利用的java.net.URL对象打开数据流，从中读取数据。但是这个方法有个限制，Java虚拟机只能调用这个set方法一次，这个限制意味着如果有其他不受控制的第三方组件（已经声明了URLStreamHandlerFactory实例），则我们无法再使用这种方法读取数据，因而，不推荐使用。</p>
<p><br></p>
<h2><a name="t6"></a>通过FileSystem API读取数据</h2>
<p><br></p>
<div class="dp-highlighter bg_java">
<div class="bar">
<div class="tools"><strong>[java]</strong> <a href="http://blog.csdn.net/puma_dong/article/details/24020793#" rel="nofollow" class="ViewSource" title="view plain">
view plain</a><a href="http://blog.csdn.net/puma_dong/article/details/24020793#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a><a href="http://blog.csdn.net/puma_dong/article/details/24020793#" rel="nofollow" class="PrintSource" title="print">print</a><a href="http://blog.csdn.net/puma_dong/article/details/24020793#" rel="nofollow" class="About" title="?">?</a><a href="https://code.csdn.net/snippets/300286" rel="nofollow" title="在CODE上查看代码片" style="text-indent:0;"><img src="https://code.csdn.net/assets/CODE_ico.png" alt="在CODE上查看代码片" height="12" width="12"></a><a href="https://code.csdn.net/snippets/300286/fork" rel="nofollow" title="派生到我的代码片" style="text-indent:0;"><img src="https://code.csdn.net/assets/ico_fork.svg" alt="派生到我的代码片" height="12" width="12"></a></div>
</div>
<ol class="dp-j" start="1"><li class="alt"><span><span class="keyword">import</span><span> java.io.InputStream;  </span></span></li><li><span><span class="keyword">import</span><span> java.net.URI;  </span></span></li><li class="alt"><span><span class="keyword">import</span><span> org.apache.hadoop.conf.Configuration;  </span></span></li><li><span><span class="keyword">import</span><span> org.apache.hadoop.fs.FileSystem;  </span></span></li><li class="alt"><span><span class="keyword">import</span><span> org.apache.hadoop.fs.Path;  </span></span></li><li><span><span class="keyword">import</span><span> org.apache.zookeeper.common.IOUtils;  </span></span></li><li class="alt"><span>  </span></li><li><span><span class="keyword">public</span><span> </span><span class="keyword">class</span><span> FileSystemCat {  </span></span></li><li class="alt"><span>    <span class="keyword">public</span><span> </span><span class="keyword">static</span><span> </span><span class="keyword">void</span><span> main(String[] args) </span><span class="keyword">throws</span><span> Exception {  </span></span></li><li><span>        String uri = <span class="string">"hdfs:///test/input/t/temperature.txt"</span><span>;  </span></span></li><li class="alt"><span>        Configuration conf = <span class="keyword">new</span><span> Configuration();  </span></span></li><li><span>        FileSystem fs = FileSystem.get(URI.create(uri), conf);  </span></li><li class="alt"><span>        InputStream in = <span class="keyword">null</span><span>;  </span></span></li><li><span>        <span class="keyword">try</span><span> {  </span></span></li><li class="alt"><span>            in = fs.open(<span class="keyword">new</span><span> Path(uri));  </span></span></li><li><span>            IOUtils.copyBytes(in, System.out, <span class="number">4096</span><span>, </span><span class="keyword">false</span><span>);  </span></span></li><li class="alt"><span>        } <span class="keyword">finally</span><span> {  </span></span></li><li><span>            IOUtils.closeStream(in);  </span></li><li class="alt"><span>        }  </span></li><li><span>    }  </span></li><li class="alt"><span>}  </span></li></ol></div>
实际上，open方法返回的是FSDataInputStream对象，是继承java.io.DataInputStream的一个特殊类，支持随机访问，由此可以从流的任意位置读取数据，比如，我们把try段的代码变成：
<div class="dp-highlighter bg_java">
<div class="bar">
<div class="tools"><strong>[java]</strong> <a href="http://blog.csdn.net/puma_dong/article/details/24020793#" rel="nofollow" class="ViewSource" title="view plain">
view plain</a><a href="http://blog.csdn.net/puma_dong/article/details/24020793#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a><a href="http://blog.csdn.net/puma_dong/article/details/24020793#" rel="nofollow" class="PrintSource" title="print">print</a><a href="http://blog.csdn.net/puma_dong/article/details/24020793#" rel="nofollow" class="About" title="?">?</a><a href="https://code.csdn.net/snippets/300286" rel="nofollow" title="在CODE上查看代码片" style="text-indent:0;"><img src="https://code.csdn.net/assets/CODE_ico.png" alt="在CODE上查看代码片" height="12" width="12"></a><a href="https://code.csdn.net/snippets/300286/fork" rel="nofollow" title="派生到我的代码片" style="text-indent:0;"><img src="https://code.csdn.net/assets/ico_fork.svg" alt="派生到我的代码片" height="12" width="12"></a></div>
</div>
<ol class="dp-j" start="1"><li class="alt"><span><span>in = fs.open(</span><span class="keyword">new</span><span> Path(uri));  </span></span></li><li><span>IOUtils.copyBytes(in, System.out, <span class="number">4096</span><span>, </span><span class="keyword">false</span><span>);  </span></span></li><li class="alt"><span>((FSDataInputStream) in).seek(<span class="number">0</span><span>);   </span><span class="comment">//go back to the start of the file</span><span>  </span></span></li><li><span>IOUtils.copyBytes(in, System.out, <span class="number">4096</span><span>, </span><span class="keyword">false</span><span>)  </span></span></li></ol></div>
则，会显示两遍文件temperature.txt文件的内容。
<p><br></p>
<h2><a name="t7"></a>将本地文件复制到Hadoop文件系统</h2>
<p><br></p>
<div class="dp-highlighter bg_java">
<div class="bar">
<div class="tools"><strong>[java]</strong> <a href="http://blog.csdn.net/puma_dong/article/details/24020793#" rel="nofollow" class="ViewSource" title="view plain">
view plain</a><a href="http://blog.csdn.net/puma_dong/article/details/24020793#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a><a href="http://blog.csdn.net/puma_dong/article/details/24020793#" rel="nofollow" class="PrintSource" title="print">print</a><a href="http://blog.csdn.net/puma_dong/article/details/24020793#" rel="nofollow" class="About" title="?">?</a><a href="https://code.csdn.net/snippets/300286" rel="nofollow" title="在CODE上查看代码片" style="text-indent:0;"><img src="https://code.csdn.net/assets/CODE_ico.png" alt="在CODE上查看代码片" height="12" width="12"></a><a href="https://code.csdn.net/snippets/300286/fork" rel="nofollow" title="派生到我的代码片" style="text-indent:0;"><img src="https://code.csdn.net/assets/ico_fork.svg" alt="派生到我的代码片" height="12" width="12"></a></div>
</div>
<ol class="dp-j" start="1"><li class="alt"><span><span class="keyword">import</span><span> java.io.BufferedInputStream;  </span></span></li><li><span><span class="keyword">import</span><span> java.io.FileInputStream;  </span></span></li><li class="alt"><span><span class="keyword">import</span><span> java.io.InputStream;  </span></span></li><li><span><span class="keyword">import</span><span> java.io.OutputStream;  </span></span></li><li class="alt"><span><span class="keyword">import</span><span> java.net.URI;  </span></span></li><li><span>  </span></li><li class="alt"><span><span class="keyword">import</span><span> org.apache.hadoop.conf.Configuration;  </span></span></li><li><span><span class="keyword">import</span><span> org.apache.hadoop.fs.FileSystem;  </span></span></li><li class="alt"><span><span class="keyword">import</span><span> org.apache.hadoop.fs.Path;  </span></span></li><li><span><span class="keyword">import</span><span> org.apache.hadoop.io.IOUtils;  </span></span></li><li class="alt"><span><span class="keyword">import</span><span> org.apache.hadoop.util.Progressable;  </span></span></li><li><span>  </span></li><li class="alt"><span>  </span></li><li><span><span class="keyword">public</span><span> </span><span class="keyword">class</span><span> FileCopyWithProgress {  </span></span></li><li class="alt"><span>  </span></li><li><span>    <span class="keyword">public</span><span> </span><span class="keyword">static</span><span> </span><span class="keyword">void</span><span> main(String[] args) </span><span class="keyword">throws</span><span> Exception  </span></span></li><li class="alt"><span>    {  </span></li><li><span>        String localSrc = <span class="string">"/home/hadoop/temperature.txt"</span><span>;  </span></span></li><li class="alt"><span>        String dst = <span class="string">"hdfs:///test/input/t/temperature2.txt"</span><span>;  </span></span></li><li><span>        InputStream in = <span class="keyword">new</span><span> BufferedInputStream(</span><span class="keyword">new</span><span> FileInputStream(localSrc));  </span></span></li><li class="alt"><span>        Configuration conf = <span class="keyword">new</span><span> Configuration();  </span></span></li><li><span>        FileSystem fs = FileSystem.get(URI.create(dst),conf);  </span></li><li class="alt"><span>        OutputStream out = fs.create(<span class="keyword">new</span><span> Path(dst),</span><span class="keyword">new</span><span> Progressable(){  </span></span></li><li><span>            <span class="keyword">public</span><span> </span><span class="keyword">void</span><span> progress(){  </span></span></li><li class="alt"><span>                System.out.println(<span class="string">"."</span><span>);  </span></span></li><li><span>            }  </span></li><li class="alt"><span>        });  </span></li><li><span>        IOUtils.copyBytes(in,out,<span class="number">4096</span><span>,</span><span class="keyword">true</span><span>);  </span></span></li><li class="alt"><span>    }  </span></li><li><span>}  </span></li></ol></div>
<br>
每次Hadoop调用progress()方法时，也就是每次讲64KB数据包写入datanode后。<br><br><h2><a name="t8"></a>列出文件</h2>
<p><br></p>
<div class="dp-highlighter bg_java">
<div class="bar">
<div class="tools"><strong>[java]</strong> <a href="http://blog.csdn.net/puma_dong/article/details/24020793#" rel="nofollow" class="ViewSource" title="view plain">
view plain</a><a href="http://blog.csdn.net/puma_dong/article/details/24020793#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a><a href="http://blog.csdn.net/puma_dong/article/details/24020793#" rel="nofollow" class="PrintSource" title="print">print</a><a href="http://blog.csdn.net/puma_dong/article/details/24020793#" rel="nofollow" class="About" title="?">?</a><a href="https://code.csdn.net/snippets/300286" rel="nofollow" title="在CODE上查看代码片" style="text-indent:0;"><img src="https://code.csdn.net/assets/CODE_ico.png" alt="在CODE上查看代码片" height="12" width="12"></a><a href="https://code.csdn.net/snippets/300286/fork" rel="nofollow" title="派生到我的代码片" style="text-indent:0;"><img src="https://code.csdn.net/assets/ico_fork.svg" alt="派生到我的代码片" height="12" width="12"></a></div>
</div>
<ol class="dp-j" start="1"><li class="alt"><span><span class="keyword">import</span><span> java.net.URI;  </span></span></li><li><span><span class="keyword">import</span><span> org.apache.hadoop.conf.Configuration;  </span></span></li><li class="alt"><span><span class="keyword">import</span><span> org.apache.hadoop.fs.FileStatus;  </span></span></li><li><span><span class="keyword">import</span><span> org.apache.hadoop.fs.FileSystem;  </span></span></li><li class="alt"><span><span class="keyword">import</span><span> org.apache.hadoop.fs.FileUtil;  </span></span></li><li><span><span class="keyword">import</span><span> org.apache.hadoop.fs.Path;  </span></span></li><li class="alt"><span>  </span></li><li><span><span class="keyword">public</span><span> </span><span class="keyword">class</span><span> ListStatus {  </span></span></li><li class="alt"><span>  </span></li><li><span>    <span class="keyword">public</span><span> </span><span class="keyword">static</span><span> </span><span class="keyword">void</span><span> main(String[] args) </span><span class="keyword">throws</span><span> Exception {  </span></span></li><li class="alt"><span>        String uri = <span class="string">"hdfs:///test/input/t/"</span><span>;  </span></span></li><li><span>        Configuration conf = <span class="keyword">new</span><span> Configuration();  </span></span></li><li class="alt"><span>        FileSystem fs = FileSystem.get(URI.create(uri), conf);  </span></li><li><span>        <span class="comment">// 显示一组路径的目录列表的并集</span><span>  </span></span></li><li class="alt"><span>        <span class="comment">/*</span> </span></li><li><span><span class="comment">         * Path[] paths = new Path[] { new Path("hdfs:///test/input/t/"), new</span> </span></li><li class="alt"><span><span class="comment">         * Path("hdfs:///test/input/wc") }; FileStatus[] status =</span> </span></li><li><span><span class="comment">         * fs.listStatus(paths);</span> </span></li><li class="alt"><span><span class="comment">         */</span><span>  </span></span></li><li><span>        <span class="comment">// 通配方式</span><span>  </span></span></li><li class="alt"><span>        FileStatus[] status = fs.globStatus(<span class="keyword">new</span><span> Path(  </span></span></li><li><span>                <span class="string">"hdfs:///test/input/wc/*02.txt"</span><span>));  </span></span></li><li class="alt"><span>  </span></li><li><span>        Path[] listedPaths = FileUtil.stat2Paths(status);  </span></li><li class="alt"><span>        <span class="keyword">for</span><span> (Path p : listedPaths) {  </span></span></li><li><span>            System.out.println(p);  </span></li><li class="alt"><span>        }  </span></li><li><span>    }  </span></li><li class="alt"><span>}  </span></li></ol></div>
<br><p>文件元数据FileStatus，封装了文件系统中文件和目录的元数据，包括文件长度、块大小、备份、修改时间、所有者以及权限信息。</p>
<p><br></p>
<h2><a name="t9"></a>删除数据</h2>
<p><br></p>
<p>使用FileSystem的delete()方法可以永久性删除文件或目录。</p>
<p>public boolean delete(Path f,boolean recursive) throws IOException</p>
<p>如果f是一个文件或空目录，那么recursive的值就会被忽略。如果f是一个非空目录，则只有recursive为true才能删除，否则会抛出IOException。</p>
<p><br></p>
<h1><a name="t10"></a>Hadoop数据流</h1>
<p><br></p>
<h2><a name="t11"></a>文件读取剖析</h2>
<p><br></p>
<p><img src="https://img-blog.csdn.net/20140418181640046" alt=""><br></p>
<p><br></p>
<h2><a name="t12"></a>文件写入剖析</h2>
<p><br></p>
<p><img src="https://img-blog.csdn.net/20140418181756578" alt=""><br></p>
<p><br></p>
<p>复本的布局策略（以3个为例）：1、运行客户端的节点，2、离架节点，3、2所在机架的随机节点。</p>
<p><br></p>
<h2><a name="t13"></a>一致模型</h2>
<p><br></p>
<p>文件系统的一致模型，描述了对文件读/写的数据可见性。HDFS为性能牺牲了一些POSIX要求，因此一些操作与你期望的不同。</p>
<div class="dp-highlighter bg_java">
<div class="bar">
<div class="tools"><strong>[java]</strong> <a href="http://blog.csdn.net/puma_dong/article/details/24020793#" rel="nofollow" class="ViewSource" title="view plain">
view plain</a><a href="http://blog.csdn.net/puma_dong/article/details/24020793#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a><a href="http://blog.csdn.net/puma_dong/article/details/24020793#" rel="nofollow" class="PrintSource" title="print">print</a><a href="http://blog.csdn.net/puma_dong/article/details/24020793#" rel="nofollow" class="About" title="?">?</a><a href="https://code.csdn.net/snippets/300286" rel="nofollow" title="在CODE上查看代码片" style="text-indent:0;"><img src="https://code.csdn.net/assets/CODE_ico.png" alt="在CODE上查看代码片" height="12" width="12"></a><a href="https://code.csdn.net/snippets/300286/fork" rel="nofollow" title="派生到我的代码片" style="text-indent:0;"><img src="https://code.csdn.net/assets/ico_fork.svg" alt="派生到我的代码片" height="12" width="12"></a></div>
</div>
<ol class="dp-j" start="1"><li class="alt"><span><span>Path p = </span><span class="keyword">new</span><span> Path(</span><span class="string">"p"</span><span>);  </span></span></li><li><span>Fs.create(p);  </span></li></ol></div>
<br><div class="dp-highlighter bg_java">
<div class="bar">
<div class="tools"><strong>[java]</strong> <a href="http://blog.csdn.net/puma_dong/article/details/24020793#" rel="nofollow" class="ViewSource" title="view plain">
view plain</a><a href="http://blog.csdn.net/puma_dong/article/details/24020793#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a><a href="http://blog.csdn.net/puma_dong/article/details/24020793#" rel="nofollow" class="PrintSource" title="print">print</a><a href="http://blog.csdn.net/puma_dong/article/details/24020793#" rel="nofollow" class="About" title="?">?</a><a href="https://code.csdn.net/snippets/300286" rel="nofollow" title="在CODE上查看代码片" style="text-indent:0;"><img src="https://code.csdn.net/assets/CODE_ico.png" alt="在CODE上查看代码片" height="12" width="12"></a><a href="https://code.csdn.net/snippets/300286/fork" rel="nofollow" title="派生到我的代码片" style="text-indent:0;"><img src="https://code.csdn.net/assets/ico_fork.svg" alt="派生到我的代码片" height="12" width="12"></a></div>
</div>
<ol class="dp-j" start="1"><li class="alt"><span><span>Path p = </span><span class="keyword">new</span><span> Path(</span><span class="string">"p"</span><span>);  </span></span></li><li><span>OutputStream out = fs.create(p);  </span></li><li class="alt"><span>out.write(<span class="string">"content"</span><span>.getBytes(</span><span class="string">"UTF-8"</span><span>));  </span></span></li><li><span>out.flush();  </span></li></ol></div>
<br>
以上两段代码都不能这个文件在文件系统立即可见，除非在out.flush();后面增加一行out.sync()，目的是强制所有的缓存与数据节点同步，另外，在HDFS中关闭文件out.close(0其实隐含执行了sync()方法。
<p><strong>这个一致模型对应用设计的重要性：</strong></p>
<p>如果不调用sync()，可能因客户端故障而丢失数据，而经常调用sync()也会有额外性能开销，所以需要在数据健壮性和吞吐量直接有所取舍，这与具体的应用有关，通过设置不同的调用sync()的频率来衡量应用的性能，最终找到一个合适的频率。</p>
<p><br></p>
<h1><a name="t14"></a>通过distcp进行并行复制</h1>
<p><br></p>
<p>前面介绍的都是单线程的HDFS访问模型，distcp是一个分布式的复制程序。典型应用是在两个HDFS直接传输 数据，如果两个集群运行相同版本的Hadoop，则可以如下：</p>
<p>hadoop distcp hdfs://集群1的某节点/foo hdfs://集群2的某节点/foo</p>
<p>可以通过-overrite，指定覆盖现有的文件；通过-update指定仅更新修改过的文件。</p>
<p>如果两个集群版本不一样，可以如下这样：</p>
<p>hadoop distcp hftp://集群1的某节点:50070/foo hdfs://集群2的某节点/foo </p>
<p>默认情况下，每个集群节点（tasktracker），最多分配20个map任务，如果复制1000G数据到100个节点的集群，一共会有2000个map任务，每个map任务平均分配512M数据；可以指定-m参数，减少map任务数，比如-m 1000，将分配1000个map任务，平均每个复制1GB数据；但是一般不推荐这么做，可能导致集群不平衡。</p>
<p><br></p>
<h1><a name="t15"></a>Hadoop存档</h1>
<p><br></p>
<p>Hadoop存档文件（HAR文件），是一个高效的文件存档工具，它将文件存入HDFS块，减少namenode内存使用的同时，依然允许对文件进行透明的访问（即Hadoop文档可以作为MapReduce的输入）。</p>
<p>命令演示如下：</p>
<p>hadoop fs -ls -R /test/input<br></p>
<p>hadoop archive -archiveName files.har -p /test input /test/file</p>
<p>hadoop fs -ls /test/file<br></p>
<p>hadoop fs -ls /test/file/files.har<br></p>
<p>hadoop fs -ls -R har:///test/file/files.har<br></p>
<p>hadoop fs -rm -r /test/file<br></p>
<p><strong>har文件的不足：</strong></p>
<p>（1）创建一个存档文件会创建原始文件的一个复本，因此需要额外的和原始文件一样大小的磁盘空间。当然，创建了存档文件，可以删除原始文件。har是不压缩的，非常类似于tar文件</p>
<p>（2）一旦创建，存档文件不能修改。事实上，一般不会修改存档文件，因为它们是定期成批存档的，比如每日或每周。</p>
<p>（3）Har文件作为mapreduce输入时，InputFormat类并不知道文件已经存档，尽管该类可以将多个文件打包成一个MapReduce分片，所以即使在har文件中处理许多小文件，依然和原来一样低效。</p>
</div>
            </div>
                </div>