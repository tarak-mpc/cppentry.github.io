---
layout:     post
title:      Hadoop常用API
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="hadoop-api">Hadoop API</h1>



<h2 id="命令">命令</h2>

<ul>
<li><p>上传文件 <code>hadoop fs -put 文件 hdfs文件夹</code></p>

<pre class="prettyprint"><code class="language-shell hljs livecodeserver">hadoop fs -<span class="hljs-built_in">put</span> test.txt /<span class="hljs-built_in">files</span>     <span class="hljs-comment">#将test.txt文件上传到hdfs文件夹files下</span></code></pre></li>
<li><p>删除文件<code>hadoop fs -rm -r 文件/文件夹</code></p>

<pre class="prettyprint"><code class="language-shell hljs lasso">hadoop fs <span class="hljs-attribute">-rm</span> <span class="hljs-attribute">-rf</span> /quanfeihu/test<span class="hljs-built_in">.</span>txt</code></pre></li>
<li><p>查看文件<code>hadoop fs -ls [-R] 文件夹</code></p>

<pre class="prettyprint"><code class="language-shell hljs bash">hadoop fs -ls -R /quanfeihu   <span class="hljs-comment">#递归显示quanfiehu文件夹下的文件</span></code></pre></li>
<li><p>创建文件夹<code>hadoop fs -mkdir [-p] 文件夹</code></p>

<pre class="prettyprint"><code class="language-shell hljs perl">hadoop fs -<span class="hljs-keyword">mkdir</span> -p /quanfeihu/test/<span class="hljs-number">01</span> <span class="hljs-comment">#创建文件夹/quanfeihu/test/01</span></code></pre></li>
<li><p>下载文件<code>hadoop fs -get  远程文件  本地文件</code></p>

<pre class="prettyprint"><code class="language-shell hljs cs">hadoop fs -<span class="hljs-keyword">get</span> /quanfeihu/test.txt c:<span class="hljs-comment">//</span></code></pre></li>
<li><p>复制文件<code>hadoop fs -cp 远程文件  远程文件夹</code></p>

<pre class="prettyprint"><code class="language-shell hljs avrasm">hadoop fs -<span class="hljs-keyword">cp</span> /quanfeihu/test<span class="hljs-preprocessor">.txt</span> /quanfeihu/test/</code></pre></li>
<li><p>移动/重命名 <code>hadoop fs -mv 远程文件 远程文件(夹)</code></p>

<pre class="prettyprint"><code class="language-shell hljs avrasm">hadoop fs -mv /quanfeihu/test<span class="hljs-preprocessor">.txt</span> /quanfeihu/test/</code></pre></li>
<li><p>tail查看文件<code>hadoop fs -tail [-f] 远程文件</code></p>

<pre class="prettyprint"><code class="language-shell hljs lasso">hadoop fs <span class="hljs-attribute">-tail</span> <span class="hljs-attribute">-f</span> /quanfeihu/test<span class="hljs-built_in">.</span>txt</code></pre></li>
<li><p>在文件后追加<code>hadoop fs -appendToFile 本地文件 远程文件</code>(将本地文件追加到远程文件下)</p>

<pre class="prettyprint"><code class="language-shell hljs avrasm">hadoop fs -appendToFile c://test<span class="hljs-preprocessor">.txt</span> /quanfiehu/test<span class="hljs-preprocessor">.txt</span></code></pre></li>
<li><p>修改文件组/用户<code>hadoop fs -chown owner:group 文件</code></p>

<pre class="prettyprint"><code class="language-shell hljs perl">hadoop fs -<span class="hljs-keyword">chown</span> root:root /quanfeihu/test.txt</code></pre></li>
</ul>



<h2 id="java-api">java API</h2>

<ul>
<li><p>maven依赖</p>

<pre class="prettyprint"><code class="language-xml hljs "><span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
   <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.hadoop<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
   <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>hadoop-client<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
   <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>2.8.3<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span></code></pre></li>
<li><p>初始化</p></li>
</ul>



<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-comment">//创建客户端对象</span>
Configuration conf = <span class="hljs-keyword">new</span> Configuration();
conf.set(<span class="hljs-string">"dfs.blocksize"</span>, <span class="hljs-string">"128m"</span>);   <span class="hljs-comment">//设置block大小</span>
FileSystem fs = FileSystem.get(<span class="hljs-keyword">new</span> URI(<span class="hljs-string">"hdfs://linux11:9000/"</span>), conf, <span class="hljs-string">"root"</span>);</code></pre>

<ul>
<li><p>创建文件夹</p>

<pre class="prettyprint"><code class="language-java hljs ">fs.mkdirs(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/quanfeihu/"</span>));</code></pre></li>
<li><p>上传文件</p>

<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-comment">//将test.txt 上传到hdfs远程/quanfeihu文件夹下</span>
fs.copyFromLocalFile(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"C:\\test.txt"</span>), <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/quanfeihu"</span>));</code></pre></li>
<li><p>下载文件</p>

<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-comment">/*将远程文件/quanfeihu/test.txt下载到本地C盘*/</span>
<span class="hljs-comment">//使用hadoop本地环境 </span>
fs.copyToLocalFile(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/quanfeihu/test.txt"</span>), <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"C:\\"</span>));
<span class="hljs-comment">//不适用hadoop本地环境</span>
fs.copyToLocalFile(<span class="hljs-keyword">false</span>, <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/quanfeihu/test.txt"</span>), <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"C:\\"</span>),<span class="hljs-keyword">true</span>);</code></pre></li>
<li><p>移动或者重命名</p>

<pre class="prettyprint"><code class="language-java hljs ">fs.rename(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/quanfeihu/test.txt"</span>), <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/quanfeihu/test1.txt"</span>));</code></pre></li>
<li><p>删除文件/文件夹</p>

<pre class="prettyprint"><code class="language-java hljs ">fs.delete(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/quanfiehu"</span>), <span class="hljs-keyword">true</span>);</code></pre></li>
<li><p>查看目录信息</p>

<pre class="prettyprint"><code class="language-java hljs ">FileSystem fs = FileSystem.get(<span class="hljs-keyword">new</span> URI(<span class="hljs-string">"hdfs://linux:9000/"</span>), <span class="hljs-keyword">new</span> Configuration(), <span class="hljs-string">"root"</span>);

      RemoteIterator&lt;LocatedFileStatus&gt; listFiles = fs.listFiles(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/"</span>), <span class="hljs-keyword">true</span>);
      <span class="hljs-keyword">while</span> (listFiles.hasNext()) {
          LocatedFileStatus nextFile = listFiles.next();

          System.out.println(<span class="hljs-string">"get file path:"</span> + nextFile.getPath().toString());
          System.out.println(<span class="hljs-string">"get file Permission:"</span> + nextFile.getPermission());
          System.out.println(<span class="hljs-string">"get file Owner:"</span> + nextFile.getOwner());
          System.out.println(<span class="hljs-string">"get file Group:"</span> + nextFile.getGroup());
          System.out.println(<span class="hljs-string">"get file AccessTime:"</span> + nextFile.getAccessTime());
          System.out.println(<span class="hljs-string">"get file BlockSize:"</span> + nextFile.getBlockSize());
          System.out.println(<span class="hljs-string">"get file Len:"</span> + nextFile.getLen());
          System.out.println(<span class="hljs-string">"get file Replication:"</span> + nextFile.getReplication());
<span class="hljs-comment">//            System.out.println("get file Replication:" + nextFile.getBlockLocations());</span>
          BlockLocation[] blockLocations = nextFile.getBlockLocations();
          <span class="hljs-keyword">for</span> (BlockLocation b : blockLocations) {
              System.out.println(<span class="hljs-string">"blk 的长度："</span> + b.getLength());
              System.out.println(<span class="hljs-string">"blk 的偏移量："</span> + b.getOffset());
              <span class="hljs-keyword">for</span> (String host : b.getHosts()) {
                  System.out.println(<span class="hljs-string">"blk 的主机名："</span> + host);

              }

          }
      }
      fs.close();</code></pre></li>
<li><p>判断文件是否存在</p>

<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-keyword">boolean</span> exist = fs.exists(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/quanfeihu/hdfs_my_start.sh"</span>));</code></pre></li>
<li><p>写入文件</p>

<pre class="prettyprint"><code class="language-java hljs ">FileSystem fs = FileSystem.get(conf);
FSDataOutputStream out = fs.create(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/2.txt"</span>));

out.write(<span class="hljs-string">"xuanxuan\n"</span>.getBytes());

BufferedWriter bufferedWriter = <span class="hljs-keyword">new</span> BufferedWriter(<span class="hljs-keyword">new</span> OutputStreamWriter(out));
bufferedWriter.write(<span class="hljs-string">"测试一下"</span>);
bufferedWriter.newLine();

bufferedWriter.close();
out.close();
fs.close();</code></pre></li>
<li><p>读取文件流</p>

<pre class="prettyprint"><code class="language-java hljs ">FileSystem fs = FileSystem.get(conf);
FSDataInputStream in = fs.open(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/quanfeihu/hadoop-2.8.3.tar.gz"</span>));
FileOutputStream out = <span class="hljs-keyword">new</span> FileOutputStream(<span class="hljs-keyword">new</span> File(<span class="hljs-string">"g://hadoop.tar.gz"</span>));
in.seek(<span class="hljs-number">0</span>); <span class="hljs-comment">//从文件什么地方开始读(字节)</span>
<span class="hljs-keyword">byte</span>[] b = <span class="hljs-keyword">new</span> <span class="hljs-keyword">byte</span>[<span class="hljs-number">1024</span>];
<span class="hljs-keyword">int</span> read = -<span class="hljs-number">1</span>;
<span class="hljs-keyword">int</span> count = <span class="hljs-number">0</span>;
<span class="hljs-keyword">while</span> ((read = in.read(b)) != -<span class="hljs-number">1</span>) {
  <span class="hljs-comment">/*System.out.println(new String(b, 0, read));*/</span>
  out.write(b, <span class="hljs-number">0</span>, read);
}
in.close();
fs.close();
out.close();</code></pre></li>
</ul>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>