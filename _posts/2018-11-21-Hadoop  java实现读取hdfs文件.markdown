---
layout:     post
title:      Hadoop  java实现读取hdfs文件
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/Agly_Clarlie/article/details/55808237				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2><a id="javahadoophdfs_0"></a>java实现读取hadoop的hdfs文件</h2>
<p>ubuntu14.04 下 hadoop 的安装和eclipse配置hadoop的文档参考：<br>
安装hadoop   <a href="http://www.powerxing.com/install-hadoop/" rel="nofollow">http://www.powerxing.com/install-hadoop/</a><br>
安装eclipse配置hadoop   <a href="http://www.powerxing.com/hadoop-build-project-using-eclipse/" rel="nofollow">http://www.powerxing.com/hadoop-build-project-using-eclipse/</a></p>
<p><strong>进行eclipse的hadoop操作前启动hadoop</strong></p>
<pre><code class="prism language-bash"><span class="token comment">#启动命令</span>
hadoop@h:/usr/local/hadoop$ sbin/start-dfs.sh 
<span class="token comment">#检查是否成功</span>
hadoop@h:/usr/local/hadoop$ jsp
<span class="token comment">#如果出现jps、SecondaryNameNode、NameNode、DataNode说明成功启动,或者浏览器访问 http://localhost:50070</span>
<span class="token comment">#关闭命令</span>
hadoop@iiip-Lenovo:/usr/local/hadoop$ sbin/stop-dfs.sh 
</code></pre>
<p>安装完成后可以在eclipse里看到文件结构：<br>
<img src="https://img-blog.csdn.net/20170219201119098?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWdseV9DbGFybGll/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述"></p>
<p>再次强调一定要启动hadoop,并且安装是按照上面的教程完成。</p>
<p>eclipse中input的文件夹对应着hdfs的文件目录，在eclipse中可视化<br>
然后编码实现读取这里面的文档，编码实现</p>
<pre><code class="prism language-java"><span class="token keyword">package</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>hadoop<span class="token punctuation">.</span>examples<span class="token punctuation">;</span>

<span class="token keyword">import</span> java<span class="token punctuation">.</span>io<span class="token punctuation">.</span>InputStream<span class="token punctuation">;</span>
<span class="token keyword">import</span> java<span class="token punctuation">.</span>net<span class="token punctuation">.</span>URL<span class="token punctuation">;</span>

<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>hadoop<span class="token punctuation">.</span>fs<span class="token punctuation">.</span>FsUrlStreamHandlerFactory<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>hadoop<span class="token punctuation">.</span>io<span class="token punctuation">.</span>IOUtils<span class="token punctuation">;</span>

<span class="token comment">/**
 * 读出文件控制台输出
 * @author hadoop
 *
 */</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ReadHdfsFile</span> <span class="token punctuation">{</span>

	<span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">final</span> String HDFS_PATH <span class="token operator">=</span> <span class="token string">"hdfs://localhost:9000/user/hadoop/input/input.txt"</span><span class="token punctuation">;</span>
	<span class="token comment">//这里的路由要正确，对应着上面的截图</span>
	
	<span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span>String<span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token keyword">throws</span> Exception<span class="token punctuation">{</span>
		
		URL<span class="token punctuation">.</span><span class="token function">setURLStreamHandlerFactory</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">FsUrlStreamHandlerFactory</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		
		<span class="token keyword">final</span> URL url <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">URL</span><span class="token punctuation">(</span>HDFS_PATH<span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token keyword">final</span> InputStream in <span class="token operator">=</span> url<span class="token punctuation">.</span><span class="token function">openStream</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		
		<span class="token comment">/**
		 * in 输入流
		 * out输出流
		 * 1024 buffersize 缓存区大小自定义大小
		 * close 是否关闭流
		 */</span>
		IOUtils<span class="token punctuation">.</span><span class="token function">copyBytes</span><span class="token punctuation">(</span>in<span class="token punctuation">,</span> System<span class="token punctuation">.</span>out<span class="token punctuation">,</span> <span class="token number">1024</span><span class="token punctuation">,</span> <span class="token boolean">true</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span>

</code></pre>
<p>ps:</p>
<p>如果报错<br>
java.net.NoRouteToHostException: 没有到主机的路由…<br>
这类的，请查看</p>
<ol>
<li>代码中这一行是否正确</li>
</ol>
<pre><code class="prism language-java">	<span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">final</span> String HDFS_PATH <span class="token operator">=</span> <span class="token string">"hdfs://localhost:9000/user/hadoop/input/input.txt"</span><span class="token punctuation">;</span>
	<span class="token comment">//这里的路由要正确，对应着上面的截图</span>
</code></pre>
<ol start="2">
<li>确认防火墙是否关闭成功</li>
</ol>
<p>window系统</p>
<pre><code class="prism language-bash"><span class="token function">service</span> iptables stop  
</code></pre>
<p>ubuntu系统</p>
<pre><code class="prism language-bash"><span class="token function">sudo</span> ufw disable
</code></pre>
<p>谢谢。</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>