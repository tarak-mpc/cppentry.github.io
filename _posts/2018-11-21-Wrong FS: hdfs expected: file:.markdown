---
layout:     post
title:      Wrong FS: hdfs expected: file:
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许最好别转载，你非要转我也没办法。 ╮(╯▽╰)╭					https://blog.csdn.net/u011478909/article/details/51872524				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
场景：错误java.lang.IllegalArgumentException:<span style="color:#FF0000;">Wrong</span> <span style="color:#FF0000;">
FS</span>: <span style="color:#FF0000;">hdfs</span>:/, <span style="color:#FF0000;">expected</span>:
<span style="color:#FF0000;">file</span>:///<br><br>
异常java.lang.IllegalArgumentException: Wrong FS: hdfs:/, expected: file:///<br><p> </p>
<p>在hadoop-2.2.0版本中对hdfs进行简单的测试操作，代码如下所示</p>
<pre><code class="language-java">                Configuration conf = new Configuration();
		try {
			FileSystem fs = FileSystem.get(conf);
			Path f = new Path("hdfs:///dfs_operator.txt");
			FSDataOutputStream os = fs.create(f, true);
			int i = 0;
			for (i = 0; i &lt; 10; ++i)
				os.writeChars("test");
			os.close();
		} catch (IOException e) {
			e.printStackTrace();
		}</code></pre>
<p> </p>
<p> 执行报错信息如下：</p>
<pre><code class="language-java">Exception in thread "main" java.lang.reflect.InvocationTargetException
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:601)
        at org.eclipse.jdt.internal.jarinjarloader.JarRsrcLoader.main(JarRsrcLoader.java:58)
Caused by: java.lang.IllegalArgumentException: Wrong FS: hdfs:/, expected: file:///
        at org.apache.hadoop.fs.FileSystem.checkPath(FileSystem.java:642)
        at org.apache.hadoop.fs.RawLocalFileSystem.pathToFile(RawLocalFileSystem.java:69)
        at org.apache.hadoop.fs.RawLocalFileSystem.mkdirs(RawLocalFileSystem.java:419)
        at org.apache.hadoop.fs.ChecksumFileSystem.mkdirs(ChecksumFileSystem.java:584)
        at org.apache.hadoop.fs.ChecksumFileSystem.create(ChecksumFileSystem.java:437)
        at org.apache.hadoop.fs.ChecksumFileSystem.create(ChecksumFileSystem.java:424)
        at org.apache.hadoop.fs.FileSystem.create(FileSystem.java:905)
        at org.apache.hadoop.fs.FileSystem.create(FileSystem.java:886)
        at org.apache.hadoop.fs.FileSystem.create(FileSystem.java:783)
        at com.ailk.hadoopdemo.DFSOperator.main(DFSOperator.java:22)
        ... 5 more</code></pre>
<p> 解决方法一：</p>
<pre class="prettyprint prettyprinted"><code><span class="com" style="color:#880000;">// explicitely add other config files</span><span class="com" style="color:#880000;">// PASS A PATH NOT A STRING!</span><span class="pln" style="color:#000000;">
 conf</span><span class="pun" style="color:#666600;">.</span><span class="pln" style="color:#000000;">addResource</span><span class="pun" style="color:#666600;">(</span><span class="kwd" style="color:#000088;">new</span><span class="typ" style="color:#660066;">Path</span><span class="pun" style="color:#666600;">(</span><span class="str" style="color:#008800;">"/home/hadoop/conf/core-site.xml"</span><span class="pun" style="color:#666600;">));</span></code></pre>
<p>解决方法二：</p>
<p>    把hadoop集群上的core-site.xml和hdfs-site.xml放到工程的src目录下</p>
            </div>
                </div>