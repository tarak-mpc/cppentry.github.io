---
layout:     post
title:      HDFS常用Shell命令和基础编程开发
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_38265137/article/details/80634694				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="hdfs常用shell命令">HDFS常用Shell命令</h1>

<p>Hadoop支持很多Shell命令，其中fs是HDFS最常用的命令，利用fs可以查看HDFS文件系统的目录结构、上传和下载数据、创建文件等。</p>

<p>HDFS有三种shell命令方式：</p>

<ol>
<li>hadoop fs :适用于任何不同的文件系统，比如本地文件系统和HDFS文件系统。</li>
<li>Hadoop dfs:只能适用与HDFS文件系统。</li>
<li>hdfs dfs：跟hadoop dfs命令作用一样，也只能适用与HDfS文件系统。</li>
</ol>

<blockquote>
  <p>我这里的的命令用的都是第三种，hdfs dfs。</p>
</blockquote>



<h2 id="对文件和文件夹的操作">对文件和文件夹的操作：</h2>



<pre class="prettyprint"><code class="language-shell hljs vala">hdfs dfs -mkdir input
<span class="hljs-preprocessor">#在HDFS文件系统中创建一个'input'目录</span>

hdfs dfs -ls input
<span class="hljs-preprocessor">#列出 input 目录下的内容</span>

hdfs dfs -put /home/hadoop/myFlie.txt input
<span class="hljs-preprocessor">#将本地的文件myFile.txt上传到HDFS文件系统的input中。</span>

hdfs dfs -<span class="hljs-keyword">get</span> input/myFlie.txt /home/hadoop/下载
<span class="hljs-preprocessor">#从HDFS文件系统中下载文件到本地文件系统</span>

hdfs dfs -cat input/myFlie.txt
<span class="hljs-preprocessor">#查看文件的全部内容</span>

hdfs dfs -cp input/myFile.txt input
<span class="hljs-preprocessor">#在HDFS上复制文件</span>

hdfs dfs -mv input/myFile.txt output
<span class="hljs-preprocessor">#在HDFS上移动文件</span>

hdfs dfs -rm input/myFile.txt 
<span class="hljs-preprocessor">#从HDFS删除文件</span>

hdfs dfs -du input/myFile.txt
<span class="hljs-preprocessor">#查看HDFS上某目录下所有文件大小，指定文件后显示具体文件大小</span>

hdfs dfs -touchz input/file.txt
<span class="hljs-preprocessor">#创建一个0字节的空文件。</span>

hdfs dfs -chmod 
<span class="hljs-preprocessor">#改名文件权限</span>

hdfs dfs -chown 
<span class="hljs-preprocessor">#改变文件所有者</span></code></pre>

<p><img src="https://img-blog.csdn.net/20180609173642767?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM4MjY1MTM3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="&lt;center&gt;" title=""></p>

<p></p><center>图；操作示例<center></center></center><p></p>



<h2 id="hdfs-dfsadmin管理命令">HDFS dfsadmin管理命令：</h2>

<ol>
<li><p><code>hdfs dfsadmin -report</code></p>

<p>查看文件系统的基本信息和统计信息。</p></li>
<li><p><code>hdfs dfsadmin -safemode get/enter</code></p>

<p>enter | leave | get |  wait：安全模式命令。安全模式是NameNode的一种状态，在这种状态下，NameNode不接受对名字空间的更改（只读）；不复制或删除块。NameNode在启动时自动进入安全模式，当配置块的最小百分数满足最小副本数的条件时，会自动离开安全模式。enter是进入，leave是离开。</p></li>
<li><p><code>hdfs dfsadmin -refreshNodes</code></p>

<p>重新读取hosts和exclude文件，使新的节点或需要退出集群的节点能够被NameNode重新识别。这个命令在新增节点或注销节点时用到。</p></li>
<li><p><code>hdfs dfsadmin -finalizeUpgrade</code></p>

<p>终结HDFS的升级操作。DataNode删除前一个版本的工作目录，之后NameNode也这样做。</p></li>
<li><p><code>hdfs dfsadmin -fupgradeProgress</code></p>

<p>status| details | force：请求当前系统的升级状态 | 升级状态的细节| 强制升级操作</p></li>
<li><p><code>hdfs dfsadmin -metasave filename</code></p>

<p>保存NameNode的主要数据结构到hadoop.log.dir属性指定的目录下的文件中。</p></li>
</ol>

<p></p><center><img src="https://img-blog.csdn.net/20180609173652823?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM4MjY1MTM3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></center><p></p>

<p></p><center>图：操作示例<center></center></center><p></p>



<h1 id="hdfs-api详解">HDFS API详解</h1>

<p>Hadoop中关于文件操作类基本上全部是在”org.apache.hadoop.fs”包中，这些API能够支持的操作包含：打开文件，读写文件，删除文件等。</p>

<p>Hadoop类库中最终面向用户提供的接口类是FileSystem，该类是个抽象类，只能通过来类的get方法得到具体类。get方法存在几个重载版本，常用的是这个：</p>



<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-keyword">static</span> FileSystem get(Configuration conf);</code></pre>

<p>该类封装了几乎所有的文件操作，例如mkdir，delete等。综上基本上可以得出操作文件的程序库框架：</p>



<pre class="prettyprint"><code class="language-java hljs ">operator()
{
    得到Configuration对象

    得到FileSystem对象

    进行文件操作
}</code></pre>

<p>为了编写一个能够与HDFS交互的Java应用程序，一般需要向Java工程中添加以下JAR包： <br>
（1）”/usr/local/hadoop/share/hadoop/common”目录下的hadoop-common-2.7.1.jar和haoop-nfs-2.7.1.jar； <br>
（2）/usr/local/hadoop/share/hadoop/common/lib”目录下的所有JAR包； <br>
（3）“/usr/local/hadoop/share/hadoop/hdfs”目录下的haoop-hdfs-2.7.1.jar和haoop-hdfs-nfs-2.7.1.jar； <br>
（4）“/usr/local/hadoop/share/hadoop/hdfs/lib”目录下的所有JAR包。</p>

<h2 id="上传本地文件">上传本地文件：</h2>

<p>通过”<code>FileSystem.copyFromLocalFile（Path src，Patch dst）</code> “可将本地文件上传到HDFS的制定位置上，其中src和dst均为文件的完整路径。具体代码如下：</p>



<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-keyword">import</span> org.apache.hadoop.conf.Configuration;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.FileStatus;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.FileSystem;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.Path;


<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">CopyFile</span> {</span>


    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) <span class="hljs-keyword">throws</span> Exception {

        Configuration conf = <span class="hljs-keyword">new</span> Configuration();
        FileSystem hdfs = FileSystem.get(conf);

        <span class="hljs-comment">//本地文件路径</span>
        Path srcPath = <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/home/hadoop/myFile.txt"</span>);

        <span class="hljs-comment">//HDFS路径</span>
        Path dstPath = <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/input/"</span>);

        <span class="hljs-comment">//进行文件上传</span>
        hdfs.copyFromLocalFile(srcPath, dstPath);

        <span class="hljs-comment">//打印hdfs的文件默认路径</span>
        System.out.println(<span class="hljs-string">"复制文件到： "</span> + conf.get(<span class="hljs-string">"fs.default.name"</span>));

        FileStatus[] files= hdfs.listStatus(dstPath);

        <span class="hljs-comment">//打印文件被复制到的路径</span>
        <span class="hljs-keyword">for</span>(FileStatus file:files)
            System.out.println(file.getPath());
    }
}</code></pre>

<p>程序运行结果：</p>



<pre class="prettyprint"><code class="language-java hljs ">复制文件到： file:<span class="hljs-comment">///</span>
file:/input</code></pre>

<p>如果遇到因为文件权限不够，程序运行失败，解决方法如下：</p>

<blockquote>
  <p>可能出现问题的原因有三种：</p>
  
  <ol>
  <li>hdfs 中的文件或文件夹 没有读取权限；</li>
  <li>hdfs 的配置中未允许拷出文件；</li>
  <li>linux 文件夹没有写入权限；</li>
  </ol>
  
  <p>针对上述三个原因，解决方法如下：</p>
  
  <ol>
  <li><p>增加hdfs文件夹权限</p>
  
  <p><code>hdfs dfs -chmod 777 /</code></p></li>
  <li><p>修改hdfs配置文件：</p>
  
  <pre class="prettyprint"><code class="language-xml hljs ">
# 在 $HADOOP_HOME/etc/hadoop/目录中，找到hdfs-site.xml，添加或更改以下属性：


<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.permissions<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>false<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>


# 将true该为false。
</code></pre></li>
  <li><p>增加Linux文件夹权限：</p>
  
  <p><code>sudo chmod 777 /</code></p></li>
  </ol>
</blockquote>



<h2 id="创建hdfs文件">创建HDFS文件：</h2>

<p>通过”FileSystem.create（Path f）”可在HDFS上创建文件，其中f为文件的完整路径。具体代码如下：</p>



<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-keyword">import</span> org.apache.hadoop.conf.Configuration;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.FSDataOutputStream;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.FileSystem;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.Path;
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">CreatFile</span> {</span>

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) <span class="hljs-keyword">throws</span> Exception {

        Configuration conf = <span class="hljs-keyword">new</span> Configuration();
        conf.set(<span class="hljs-string">"fs.defaultFS"</span>, <span class="hljs-string">"hdfs://localhost:9000"</span>);
        FileSystem hdfs = FileSystem.get(conf); 

        <span class="hljs-comment">//要输入文件的字符串</span>
        <span class="hljs-keyword">byte</span>[] s = <span class="hljs-string">"hello hdfs\n"</span>.getBytes();   
        Path dfsPath = <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/text.txt"</span>);   
        FSDataOutputStream outputStream = hdfs.create(dfsPath);
        <span class="hljs-comment">//写入文件</span>
        outputStream.write(s, <span class="hljs-number">0</span>, s.length);
    }
}</code></pre>



<h2 id="写入文件和读取文件">写入文件和读取文件：</h2>



<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-keyword">import</span> java.io.BufferedReader;
<span class="hljs-keyword">import</span> java.io.InputStreamReader;

<span class="hljs-keyword">import</span> org.apache.hadoop.conf.Configuration;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.FSDataInputStream;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.FSDataOutputStream;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.FileSystem;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.Path;

<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">ReadFile</span> {</span>

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) <span class="hljs-keyword">throws</span> Exception{

        Configuration conf = <span class="hljs-keyword">new</span> Configuration();
        conf.set(<span class="hljs-string">"fs.defaultFS"</span>, <span class="hljs-string">"hdfs://localhost:9000"</span>);
        FileSystem hdfs = FileSystem.get(conf);

        <span class="hljs-comment">//要写入文件的内容</span>
        <span class="hljs-keyword">byte</span>[] wString = <span class="hljs-string">"hello word! \nHello Hadoop!\nHello HDFS!\n"</span>.getBytes();

        <span class="hljs-comment">//要写入的文件名</span>
        String filename = <span class="hljs-string">"file"</span>;

        FSDataOutputStream os = hdfs.create(<span class="hljs-keyword">new</span> Path(filename));
        <span class="hljs-comment">//写入文件</span>
        os.write(wString, <span class="hljs-number">0</span>, wString.length);
        os.close();


        FSDataInputStream is = hdfs.open(<span class="hljs-keyword">new</span> Path(filename));
        BufferedReader br = <span class="hljs-keyword">new</span> BufferedReader(<span class="hljs-keyword">new</span> InputStreamReader(is));

        <span class="hljs-comment">//读取文件</span>
        String line;
        <span class="hljs-keyword">while</span>((line = br.readLine()) != <span class="hljs-keyword">null</span>)
            System.out.println(line);

        is.close();
        br.close();<span class="hljs-comment">//</span>
        hdfs.close();   
    }
}</code></pre>

<p>程序运行结果：</p>



<pre class="prettyprint"><code class="language-java hljs ">hello word! 
Hello Hadoop!
Hello HDFS!</code></pre>



<h2 id="创建hdfs目录">创建HDFS目录：</h2>

<p>通过”FileSystem.mkdirs（Path f）”可在HDFS上创建文件夹，其中f为文件夹的完整路径。具体实现如下：</p>



<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-keyword">import</span> org.apache.hadoop.conf.Configuration;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.FileSystem;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.Path;


<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">CreatDir</span> {</span>

    <span class="hljs-javadoc">/**
     *<span class="hljs-javadoctag"> @param</span> args
     *<span class="hljs-javadoctag"> @throws</span> Exception 
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) <span class="hljs-keyword">throws</span> Exception {
        Configuration conf = <span class="hljs-keyword">new</span> Configuration();
        conf.set(<span class="hljs-string">"fs.defaultFS"</span>, <span class="hljs-string">"hdfs://localhost:9000"</span>);
        FileSystem hdfs = FileSystem.get(conf);

        Path dfs = <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/TextDir"</span>);
        hdfs.mkdirs(dfs);

        System.out.println(hdfs.exists(dfs));
    }
}</code></pre>



<h2 id="重命名hdfs文件">重命名HDFS文件：</h2>

<p>通过”FileSystem.rename（Path src，Path dst）”可为指定的HDFS文件重命名，其中src和dst均为文件的完整路径。具体实现如下：</p>



<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-keyword">import</span> org.apache.hadoop.conf.Configuration;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.FileSystem;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.Path;


<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">Rename</span> {</span>

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) <span class="hljs-keyword">throws</span> Exception {
        Configuration conf = <span class="hljs-keyword">new</span> Configuration();
        conf.set(<span class="hljs-string">"fs.defaultFS"</span>, <span class="hljs-string">"hdfs://localhost:9000"</span>);
        FileSystem hdfs = FileSystem.get(conf);

        Path oldname = <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/text.txt"</span>);
        Path newname = <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/newtext.txt"</span>);

        hdfs.rename(oldname, newname);
        System.out.println(hdfs.exists(newname));
    }
}</code></pre>



<h2 id="删除hdfs上的文件">删除HDFS上的文件：</h2>

<p>通过”FileSystem.delete（Path f，Boolean recursive）”可删除指定的HDFS文件，其中f为需要删除文件的完整路径，recuresive用来确定是否进行递归删除。具体实现如下：</p>



<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-keyword">import</span> org.apache.hadoop.conf.Configuration;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.FileSystem;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.Path;

<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">DeleteFile</span> {</span>

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) <span class="hljs-keyword">throws</span> Exception {       
        Configuration conf = <span class="hljs-keyword">new</span> Configuration();
        conf.set(<span class="hljs-string">"fs.defaultFS"</span>, <span class="hljs-string">"hdfs://localhost:9000"</span>);      
        FileSystem hdfs = FileSystem.get(conf);

        Path deletePath = <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/text4.txt"</span>);

        hdfs.delete(deletePath, <span class="hljs-keyword">true</span>);
    }
}</code></pre>

<blockquote>
  <p>删除目录和删除文件代码一样，换成路径就行，如果目录下有文件，递归删除。</p>
</blockquote>



<h2 id="查看某个hdfs文件是否存在">查看某个HDFS文件是否存在：</h2>

<p>通过”FileSystem.exists（Path f）”可查看指定HDFS文件是否存在，其中f为文件的完整路径。具体实现如下：</p>



<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-keyword">import</span> org.apache.hadoop.conf.Configuration;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.FileSystem;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.Path;


<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">CheckFile</span> {</span>

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) <span class="hljs-keyword">throws</span> Exception{

        Configuration conf = <span class="hljs-keyword">new</span> Configuration();
        conf.set(<span class="hljs-string">"fs.defaultFS"</span>, <span class="hljs-string">"hdfs://localhost:9000"</span>);
        FileSystem hdfs = FileSystem.get(conf);

        Path findpPath = <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/input/myFile.txt"</span>);

        System.out.println(<span class="hljs-string">"文件是否存在："</span> + hdfs.exists(findpPath));
    }
}</code></pre>

<p></p><center><img src="https://img-blog.csdn.net/20180609173710944?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM4MjY1MTM3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></center><p></p>

<p></p><center>图：HDFS文件系统结构图<center></center></center><p></p>

<p>程序运行结果：</p>



<pre class="prettyprint"><code class="language-java hljs ">文件是否存在：<span class="hljs-keyword">true</span></code></pre>



<h2 id="查看hdfs文件的信息状态">查看HDFS文件的信息状态：</h2>

<p>通过”FileSystem.getModificationTime()”可查看指定HDFS文件的修改时间。具体实现如下：</p>



<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-keyword">import</span> java.text.SimpleDateFormat;
<span class="hljs-keyword">import</span> java.util.Calendar;
<span class="hljs-keyword">import</span> java.util.Date;

<span class="hljs-keyword">import</span> javax.ws.rs.core.NewCookie;

<span class="hljs-keyword">import</span> org.apache.hadoop.conf.Configuration;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.FileStatus;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.FileSystem;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.Path;


<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">GetTime</span> {</span>

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) <span class="hljs-keyword">throws</span> Exception{

        Configuration conf = <span class="hljs-keyword">new</span> Configuration();
        conf.set(<span class="hljs-string">"fs.defaultFS"</span>, <span class="hljs-string">"hdfs://localhost:9000"</span>);
        FileSystem hdfs = FileSystem.get(conf);

        Path findpPath = <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/input/myFile.txt"</span>);

        FileStatus fileStatus = hdfs.getFileStatus(findpPath);
        <span class="hljs-keyword">long</span> accessTime = fileStatus.getAccessTime();
        <span class="hljs-keyword">long</span> modeTime = fileStatus.getModificationTime();
        <span class="hljs-keyword">long</span> size = fileStatus.getBlockSize();
        <span class="hljs-keyword">long</span> len = fileStatus.getLen();
        String owner = fileStatus.getOwner();
        Path path = fileStatus.getPath();
        String group = fileStatus.getGroup();

        <span class="hljs-comment">//将时间戳转换为格式化日期</span>
        SimpleDateFormat sdf = <span class="hljs-keyword">new</span> SimpleDateFormat();
        String time1 = sdf.format(<span class="hljs-keyword">new</span> Date(accessTime));
        String time2 = sdf.format(<span class="hljs-keyword">new</span> Date(modeTime));


        <span class="hljs-comment">//获取文件的权限信息</span>
        System.out.println(<span class="hljs-string">"文件的权限："</span> + fileStatus.getPermission());  
        System.out.println(<span class="hljs-string">"文件创建时间："</span> + time1);
        System.out.println(<span class="hljs-string">"文件修改时间："</span> + time2);
        System.out.println(<span class="hljs-string">"HDFS文件块大小："</span> + size);
        System.out.println(<span class="hljs-string">"文件大小："</span> + len);
        System.out.println(<span class="hljs-string">"文件所有者："</span> + owner);
        System.out.println(<span class="hljs-string">"文件所在路径："</span> + path);
        System.out.println(<span class="hljs-string">"文件所属组："</span> + group);

    }
}</code></pre>

<p>程序运行结果：</p>



<pre class="prettyprint"><code class="language-java hljs ">文件的权限：rw-r--r--
文件创建时间：<span class="hljs-number">18</span>-<span class="hljs-number">6</span>-<span class="hljs-number">9</span> 下午<span class="hljs-number">1</span>:<span class="hljs-number">59</span>
文件修改时间：<span class="hljs-number">18</span>-<span class="hljs-number">6</span>-<span class="hljs-number">9</span> 下午<span class="hljs-number">1</span>:<span class="hljs-number">59</span>
HDFS文件块大小：<span class="hljs-number">134217728</span>
文件大小：<span class="hljs-number">37</span>
文件所有者：hadoop
文件所在路径：hdfs:<span class="hljs-comment">//localhost:9000/input/myFile.txt</span>
文件所属组：supergroup</code></pre>



<h2 id="读取hdfs某个目录下的所有文件">读取HDFS某个目录下的所有文件：</h2>

<p>通过”FileStatus.getPath（）”可查看指定HDFS中某个目录下所有文件。具体实现如下：</p>



<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-keyword">import</span> org.apache.hadoop.conf.Configuration;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.FileStatus;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.FileSystem;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.Path;

<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">ReadDirFile</span> {</span>

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) <span class="hljs-keyword">throws</span> Exception {

        Configuration conf = <span class="hljs-keyword">new</span> Configuration();
        conf.set(<span class="hljs-string">"fs.defaultFS"</span>, <span class="hljs-string">"hdfs://localhost:9000"</span>);
        FileSystem hdfs = FileSystem.get(conf);

        Path dirPath = <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/input"</span>);

        FileStatus[] stats = hdfs.listStatus(dirPath);

        <span class="hljs-keyword">for</span>(<span class="hljs-keyword">int</span> i = <span class="hljs-number">0</span>; i &lt; stats.length; i++)
            System.out.println(stats[i].getPath().toString());
        hdfs.close();
    }
}</code></pre>

<p></p><center><img src="https://img-blog.csdn.net/20180609173735910?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM4MjY1MTM3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></center><p></p>

<p></p><center>图：input目录下有两文件<center></center></center><p></p>

<p>程序运行结果如下：</p>



<pre class="prettyprint"><code class="language-java hljs ">hdfs:<span class="hljs-comment">//localhost:9000/input/myFile.txt</span>
hdfs:<span class="hljs-comment">//localhost:9000/input/text2.txt</span></code></pre>



<h2 id="查找某个文件在hdfs集群的位置">查找某个文件在HDFS集群的位置：</h2>

<p>通过”FileSystem.getFileBlockLocation（FileStatus file，long start，long len）”可查找指定文件在HDFS集群上的位置，其中file为文件的完整路径，start和len来标识查找文件的路径。具体实现如下：</p>



<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-keyword">import</span> org.apache.hadoop.conf.Configuration;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.BlockLocation;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.FileStatus;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.FileSystem;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.Path;
<span class="hljs-keyword">import</span> org.apache.hadoop.hdfs.protocol.BlockLocalPathInfo;


<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">FileLoc</span> {</span>

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) <span class="hljs-keyword">throws</span> Exception {


        Configuration conf = <span class="hljs-keyword">new</span> Configuration();
        conf.set(<span class="hljs-string">"fs.defaultFS"</span>, <span class="hljs-string">"hdfs://localhost:9000"</span>);
        FileSystem hdfs = FileSystem.get(conf);
        Path fPath = <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/input/myFile.txt"</span>);

        FileStatus status = hdfs.getFileStatus(fPath);

        BlockLocation[] blInfo = hdfs.getFileBlockLocations(status, <span class="hljs-number">0</span>, status.getLen());

        <span class="hljs-keyword">for</span>(<span class="hljs-keyword">int</span> i = <span class="hljs-number">0</span>; i &lt; blInfo.length; i++){
            String [] hosts = blInfo[i].getHosts();
            System.out.println(<span class="hljs-string">"block: "</span> + i + <span class="hljs-string">" Location: "</span> + hosts[<span class="hljs-number">0</span>]);
        }
    }
}</code></pre>

<p>程序运行结果：</p>



<pre class="prettyprint"><code class="language-java hljs ">block: <span class="hljs-number">0</span> Location: ubuntu</code></pre>



<h2 id="获取hdfs集群上所有节点名称信息">获取HDFS集群上所有节点名称信息：</h2>

<p>通过”DatanodeInfo.getHostName（）”可获取HDFS集群上的所有节点名称。具体实现如下：</p>



<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-keyword">import</span> org.apache.hadoop.conf.Configuration;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.FileSystem;
<span class="hljs-keyword">import</span> org.apache.hadoop.hdfs.DistributedFileSystem;
<span class="hljs-keyword">import</span> org.apache.hadoop.hdfs.protocol.DatanodeInfo;


<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">GetInfo</span> {</span>

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) <span class="hljs-keyword">throws</span> Exception{

        Configuration conf = <span class="hljs-keyword">new</span> Configuration();
        conf.set(<span class="hljs-string">"fs.defaultFS"</span>, <span class="hljs-string">"hdfs://localhost:9000"</span>);
        FileSystem hdfs = FileSystem.get(conf);

        DistributedFileSystem dFileSystem = (DistributedFileSystem) hdfs;
        DatanodeInfo[] dInfos = dFileSystem.getDataNodeStats();

        <span class="hljs-keyword">for</span>(<span class="hljs-keyword">int</span> i = <span class="hljs-number">0</span>; i &lt; dInfos.length; i++)
            System.out.println(<span class="hljs-string">"DataNode_"</span> + i + <span class="hljs-string">"_Name:"</span> + dInfos[i].getHostName());
    }
}</code></pre>

<p>程序运行结果：</p>



<pre class="prettyprint"><code class="language-java hljs ">DataNode_0_Name:ubuntu</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>