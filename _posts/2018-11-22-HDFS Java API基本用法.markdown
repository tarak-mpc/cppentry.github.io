---
layout:     post
title:      HDFS Java API基本用法
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/yang1464657625/article/details/66990912				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>HDFS:Hadoop设计的一种分布式文件系统，本文主要介绍hdfs文件系统的JavaAPI的使用。 <br>
①：公共代码：设置hdfs的访问接口</p>

<pre class="prettyprint"><code class=" hljs cs"><span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> String hdfsPath = <span class="hljs-string">"hdfs://192.168.174.131:9090"</span>;
<span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> Configuration conf = <span class="hljs-keyword">new</span> Configuration();</code></pre>

<p>②：获取HDFS分布式文件系统内部的DataNode节点信息</p>

<pre class="prettyprint"><code class=" hljs cs"><span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> DatanodeInfo[] <span class="hljs-title">getDataNodeInfos</span>(){
        FileSystem hdfs = <span class="hljs-keyword">null</span>;
        DatanodeInfo[] datanodeInfos= <span class="hljs-keyword">null</span>;
        <span class="hljs-keyword">try</span> {
            hdfs = FileSystem.<span class="hljs-keyword">get</span>(URI.create(hdfsPath),conf);
            DistributedFileSystem dbfs = (DistributedFileSystem) hdfs;
            datanodeInfos = dbfs.getDataNodeStats();
            hdfs.close();
        } <span class="hljs-keyword">catch</span> (IOException e) {
            <span class="hljs-keyword">return</span> <span class="hljs-keyword">null</span>;
        }
        <span class="hljs-keyword">return</span> datanodeInfos;
    }</code></pre>

<p>③：判断HDFS是否存储目标文件</p>

<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">boolean</span> <span class="hljs-title">judgeFileExist</span>(String hdfsFile){
        FileSystem hdfs = <span class="hljs-keyword">null</span>;
        <span class="hljs-keyword">boolean</span> isSuccess = <span class="hljs-keyword">false</span>;
        <span class="hljs-keyword">try</span> {
            hdfs = FileSystem.get(URI.create(hdfsPath),conf);
            isSuccess = hdfs.exists(<span class="hljs-keyword">new</span> Path(hdfsFile));
            hdfs.close();
        } <span class="hljs-keyword">catch</span> (IOException e) {
            <span class="hljs-keyword">return</span> <span class="hljs-keyword">false</span>;
        }
        <span class="hljs-keyword">return</span> isSuccess;
    }</code></pre>

<p>④：获取指定HDFS目录下的文件列表信息</p>

<pre class="prettyprint"><code class=" hljs cs"><span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> FileStatus[] <span class="hljs-title">getFilesByDir</span>(String hdfsFileDir){
        FileSystem hdfs = <span class="hljs-keyword">null</span>;
        FileStatus[] fileStatus = <span class="hljs-keyword">null</span>;
        <span class="hljs-keyword">try</span> {
            hdfs = FileSystem.<span class="hljs-keyword">get</span>(URI.create(hdfsPath),conf);
            fileStatus = hdfs.listStatus(<span class="hljs-keyword">new</span> Path(hdfsFileDir));
            hdfs.close();
        } <span class="hljs-keyword">catch</span> (IOException e) {
            <span class="hljs-keyword">return</span> <span class="hljs-keyword">null</span>;
        }
        <span class="hljs-keyword">return</span> fileStatus;
    }</code></pre>

<p>⑤：HDFS文件系统中创建目录</p>

<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">boolean</span> <span class="hljs-title">mkdirFromHdfs</span>(String hdfsFileDir){

        FileSystem hdfs = <span class="hljs-keyword">null</span>;
        <span class="hljs-keyword">boolean</span> isSuccess = <span class="hljs-keyword">false</span>;
        <span class="hljs-keyword">try</span> {
            hdfs = FileSystem.get(URI.create(hdfsPath),conf);
            isSuccess = hdfs.mkdirs(<span class="hljs-keyword">new</span> Path(hdfsFileDir));
            hdfs.close();
        } <span class="hljs-keyword">catch</span> (IOException e) {
            <span class="hljs-keyword">return</span> <span class="hljs-keyword">false</span>;
        }
        <span class="hljs-keyword">return</span> isSuccess;
    }</code></pre>

<p>⑥：将文本信息写入HDFS的指定文件</p>

<pre class="prettyprint"><code class=" hljs cs"><span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> boolean <span class="hljs-title">writeInfoToHdfsFile</span>(String dest,<span class="hljs-keyword">byte</span>[] content){

            FileSystem hdfs = <span class="hljs-keyword">null</span>;
            FSDataOutputStream fsDataOutputStream = <span class="hljs-keyword">null</span>;

            <span class="hljs-keyword">try</span> {
                hdfs = FileSystem.<span class="hljs-keyword">get</span>(URI.create(hdfsPath),conf);
                fsDataOutputStream = hdfs.create(<span class="hljs-keyword">new</span> Path(dest));
                fsDataOutputStream.write(content);
                fsDataOutputStream.flush();
                fsDataOutputStream.close();
                hdfs.close();
            } <span class="hljs-keyword">catch</span> (IOException e) {
                <span class="hljs-keyword">return</span> <span class="hljs-keyword">false</span>;
            }
            <span class="hljs-keyword">return</span> <span class="hljs-keyword">true</span>;
    }</code></pre>

<p>⑦：读取HDFS文件系统文本文件的内容</p>

<pre class="prettyprint"><code class=" hljs cs"><span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> String <span class="hljs-title">readFile</span>(String hdfsFilePath) throws IOException{

        FileSystem hdfs = <span class="hljs-keyword">null</span>;
        InputStream <span class="hljs-keyword">is</span> = <span class="hljs-keyword">null</span>;
        StringBuffer sb = <span class="hljs-keyword">new</span> StringBuffer();
        <span class="hljs-keyword">try</span> {
            hdfs = FileSystem.<span class="hljs-keyword">get</span>(URI.create(hdfsPath),conf);
            <span class="hljs-keyword">is</span> = hdfs.open(<span class="hljs-keyword">new</span> Path(hdfsFilePath));
            <span class="hljs-keyword">int</span> length = <span class="hljs-number">0</span>;
            <span class="hljs-keyword">byte</span>[] buf = <span class="hljs-keyword">new</span> <span class="hljs-keyword">byte</span>[<span class="hljs-number">1024</span>];
            <span class="hljs-keyword">while</span>((length=<span class="hljs-keyword">is</span>.read(buf))!=-<span class="hljs-number">1</span>){
                sb.append(<span class="hljs-keyword">new</span> String(buf,<span class="hljs-number">0</span>,length));
            }
        } <span class="hljs-keyword">finally</span> {
            IOUtils.closeStream(<span class="hljs-keyword">is</span>);
            hdfs.close();
        }
        <span class="hljs-keyword">return</span> sb.toString();
    }</code></pre>

<p>⑧：删除HDFS指定文件</p>

<pre class="prettyprint"><code class=" hljs java"> <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">boolean</span> <span class="hljs-title">deleteHdfsFile</span>(String hdfsFilePath){

        FileSystem hdfs = <span class="hljs-keyword">null</span>;
        <span class="hljs-keyword">boolean</span> isSuccess = <span class="hljs-keyword">false</span>;
        <span class="hljs-keyword">try</span> {
            hdfs = FileSystem.get(URI.create(hdfsPath),conf);

            <span class="hljs-comment">//hdfs.delete(path,true)  递归删除</span>
            isSuccess = hdfs.deleteOnExit(<span class="hljs-keyword">new</span> Path(hdfsFilePath));
            hdfs.close();
        } <span class="hljs-keyword">catch</span> (IOException e) {
            <span class="hljs-keyword">return</span> <span class="hljs-keyword">false</span>;
        }
        <span class="hljs-keyword">return</span> isSuccess;
    }</code></pre>

<p>⑨：重命名HDFS文件</p>

<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">boolean</span> <span class="hljs-title">renameHdfsFile</span>(String oldName,String newName){

            FileSystem hdfs = <span class="hljs-keyword">null</span>;
            <span class="hljs-keyword">boolean</span> isSuccess = <span class="hljs-keyword">false</span>;
            <span class="hljs-keyword">try</span> {
                hdfs = FileSystem.get(URI.create(hdfsPath),conf);
                isSuccess = hdfs.rename(<span class="hljs-keyword">new</span> Path(oldName), <span class="hljs-keyword">new</span> Path(newName));
                hdfs.close();
            } <span class="hljs-keyword">catch</span> (IOException e) {
                <span class="hljs-keyword">return</span> <span class="hljs-keyword">false</span>;
            }
           <span class="hljs-keyword">return</span> isSuccess;
    }</code></pre>

<p>⑩：上传本地文件到HDFS文件系统</p>



<pre class="prettyprint"><code class=" hljs cs"><span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> boolean <span class="hljs-title">uploadLocalFileToHDFS</span>(boolean delSrc,boolean <span class="hljs-keyword">override</span>,String src,String dest){

        FileSystem hdfs = <span class="hljs-keyword">null</span>;
        <span class="hljs-keyword">try</span> {
            hdfs = FileSystem.<span class="hljs-keyword">get</span>(URI.create(hdfsPath),conf);
            <span class="hljs-comment">//注意：目标地址可以写全路径，如果不写则默认在当前访问的用户主目录下操作</span>
            hdfs.copyFromLocalFile(delSrc,<span class="hljs-keyword">override</span>,<span class="hljs-keyword">new</span> Path(src), <span class="hljs-keyword">new</span> Path(dest));
            hdfs.close();
        } <span class="hljs-keyword">catch</span> (IOException e) {
            <span class="hljs-keyword">return</span> <span class="hljs-keyword">false</span>;
        }
        <span class="hljs-keyword">return</span> <span class="hljs-keyword">true</span>;
    }</code></pre>

<p>注意：可能出现“Permission Denied”等错误信息，设置hadoop目录的访问权限即可：hadoop dfs -chmod -R 777 hadoop文件路径 (例)</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>