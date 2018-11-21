---
layout:     post
title:      java 实现hadoop的hdfs文件的上传下载删除创建
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/Agly_Clarlie/article/details/55809414				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>##java 实现 hdfs文件的上传下载删除创建 ##</p>
<p>对于ubuntu14.04 下 Hadoop 的安装和eclipse配置hadoop的文档参考：<br>
安装hadoop <a href="http://www.powerxing.com/install-hadoop/" rel="nofollow">http://www.powerxing.com/install-hadoop/</a><br>
安装eclipse配置hadoop <a href="http://www.powerxing.com/hadoop-build-project-using-eclipse/" rel="nofollow">http://www.powerxing.com/hadoop-build-project-using-eclipse/</a></p>
<pre><code class="prism language-java"><span class="token keyword">package</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>hadoop<span class="token punctuation">.</span>examples<span class="token punctuation">;</span>

<span class="token keyword">import</span> java<span class="token punctuation">.</span>io<span class="token punctuation">.</span>FileInputStream<span class="token punctuation">;</span>
<span class="token keyword">import</span> java<span class="token punctuation">.</span>io<span class="token punctuation">.</span>FileNotFoundException<span class="token punctuation">;</span>
<span class="token keyword">import</span> java<span class="token punctuation">.</span>io<span class="token punctuation">.</span>IOException<span class="token punctuation">;</span>
<span class="token keyword">import</span> java<span class="token punctuation">.</span>net<span class="token punctuation">.</span>URI<span class="token punctuation">;</span>

<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>hadoop<span class="token punctuation">.</span>conf<span class="token punctuation">.</span>Configuration<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>hadoop<span class="token punctuation">.</span>fs<span class="token punctuation">.</span>FSDataInputStream<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>hadoop<span class="token punctuation">.</span>fs<span class="token punctuation">.</span>FSDataOutputStream<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>hadoop<span class="token punctuation">.</span>fs<span class="token punctuation">.</span>FileSystem<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>hadoop<span class="token punctuation">.</span>fs<span class="token punctuation">.</span>Path<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>hadoop<span class="token punctuation">.</span>io<span class="token punctuation">.</span>IOUtils<span class="token punctuation">;</span>

<span class="token comment">/**
 * hdfs在java 的方法（创建文件夹 上传文件 下载文件 删除文件）
 * @author hadoop
 *
 */</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">HdfsUtil</span> <span class="token punctuation">{</span>
	
	<span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">final</span> String HDFS_PATH <span class="token operator">=</span> <span class="token string">"hdfs://localhost:9000/user/hadoop/hello"</span><span class="token punctuation">;</span>
	<span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">final</span> String DIR_PATH <span class="token operator">=</span> <span class="token string">"hdfs://localhost:9000/user/hadoop/hello"</span><span class="token punctuation">;</span>
	<span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">final</span> String FILE_PATH <span class="token operator">=</span> <span class="token string">"hdfs://localhost:9000/user/hadoop/hello"</span><span class="token punctuation">;</span>

	<span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span>String<span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token keyword">throws</span> Exception <span class="token punctuation">{</span>
		<span class="token keyword">final</span> FileSystem fileSystem <span class="token operator">=</span> FileSystem<span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">URI</span><span class="token punctuation">(</span>HDFS_PATH<span class="token punctuation">)</span><span class="token punctuation">,</span>
				<span class="token keyword">new</span> <span class="token class-name">Configuration</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token comment">// 创建文件夹</span>
<span class="token comment">//		 makeDirectory(fileSystem);</span>
		<span class="token comment">// 上传文件</span>
<span class="token comment">//		 uploadData(fileSystem);</span>
		<span class="token comment">// 下载文件</span>
<span class="token comment">//		 downloadData(fileSystem);</span>
		<span class="token comment">// 删除文件</span>
		<span class="token function">deleteFile</span><span class="token punctuation">(</span>fileSystem<span class="token punctuation">)</span><span class="token punctuation">;</span>

	<span class="token punctuation">}</span>

	<span class="token keyword">private</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">deleteFile</span><span class="token punctuation">(</span><span class="token keyword">final</span> FileSystem fileSystem<span class="token punctuation">)</span>
			<span class="token keyword">throws</span> IOException <span class="token punctuation">{</span>
		<span class="token comment">// 删除文件-true/false（文件夹-true）</span>
		fileSystem<span class="token punctuation">.</span><span class="token function">delete</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">Path</span><span class="token punctuation">(</span>FILE_PATH<span class="token punctuation">)</span><span class="token punctuation">,</span> <span class="token boolean">true</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">private</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">downloadData</span><span class="token punctuation">(</span><span class="token keyword">final</span> FileSystem fileSystem<span class="token punctuation">)</span>
			<span class="token keyword">throws</span> IOException <span class="token punctuation">{</span>
		<span class="token keyword">final</span> FSDataInputStream in <span class="token operator">=</span> fileSystem<span class="token punctuation">.</span><span class="token function">open</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">Path</span><span class="token punctuation">(</span>FILE_PATH<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		IOUtils<span class="token punctuation">.</span><span class="token function">copyBytes</span><span class="token punctuation">(</span>in<span class="token punctuation">,</span> System<span class="token punctuation">.</span>out<span class="token punctuation">,</span> <span class="token number">1024</span><span class="token punctuation">,</span> <span class="token boolean">true</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">private</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">makeDirectory</span><span class="token punctuation">(</span><span class="token keyword">final</span> FileSystem fileSystem<span class="token punctuation">)</span>
			<span class="token keyword">throws</span> IOException <span class="token punctuation">{</span>
		<span class="token comment">// 创建文件夹</span>
		fileSystem<span class="token punctuation">.</span><span class="token function">mkdirs</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">Path</span><span class="token punctuation">(</span>DIR_PATH<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">private</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">uploadData</span><span class="token punctuation">(</span><span class="token keyword">final</span> FileSystem fileSystem<span class="token punctuation">)</span>
			<span class="token keyword">throws</span> IOException<span class="token punctuation">,</span> FileNotFoundException <span class="token punctuation">{</span>
		<span class="token comment">// 上传文件</span>
		<span class="token keyword">final</span> FSDataOutputStream out <span class="token operator">=</span> fileSystem<span class="token punctuation">.</span><span class="token function">create</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">Path</span><span class="token punctuation">(</span>FILE_PATH<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">//上传后的文件命令名</span>
		FileInputStream in <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">FileInputStream</span><span class="token punctuation">(</span><span class="token string">"./hello.txt"</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">//上传的文件</span>
		IOUtils<span class="token punctuation">.</span><span class="token function">copyBytes</span><span class="token punctuation">(</span>in<span class="token punctuation">,</span> out<span class="token punctuation">,</span> <span class="token number">1024</span><span class="token punctuation">,</span> <span class="token boolean">true</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

<span class="token punctuation">}</span>

</code></pre>
<p>谢谢。</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>