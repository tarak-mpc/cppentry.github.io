---
layout:     post
title:      HDFS Java API配置用户（远程用户）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/m0_37793798/article/details/83417674				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-light">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<blockquote>
<p>2018.10.26</p>
</blockquote>
<p></p><div class="toc"><h3>文章目录</h3><ul><ul><li><a href="#_3" rel="nofollow">前言</a></li><li><a href="#_6" rel="nofollow">方法</a></li></ul></ul></div><p></p>
<h2><a id="_3"></a>前言</h2>
<p>某项目使用HDFS Java API操作HDFS，但在本地机器运行单元测试时发现会出现permission denied的问题，原因是该单元测试是直接操作HDFS集群，而本地运行单测时默认会使用本地机器的登录用户去操作HDFS。为了保证单测在不同机器上运行结果一致，就需要在配置HDFS时设置集群环境中的远程用户。</p>
<h2><a id="_6"></a>方法</h2>
<p>参考Hadoop Java API Doc<sup class="footnote-ref"><a href="#fn1" rel="nofollow" id="fnref1">1</a></sup>，使用<code>UserGroupInformation</code>创建一个远程用户：</p>
<pre><code class="prism language-java">UserGroupInformation ugi <span class="token operator">=</span> UserGroupInformation<span class="token punctuation">.</span><span class="token function">createRemoteUser</span><span class="token punctuation">(</span><span class="token string">'hdfsUser'</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token keyword">try</span> <span class="token punctuation">{</span>
	ugi<span class="token punctuation">.</span><span class="token function">doAs</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">PrivilegedExceptionAction</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">public</span> Void <span class="token function">run</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">throws</span> Exception <span class="token punctuation">{</span>
			<span class="token keyword">try</span> <span class="token punctuation">{</span>
				Configuration conf <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Configuration</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
				conf<span class="token punctuation">.</span><span class="token function">set</span><span class="token punctuation">(</span><span class="token string">"fs.defaultFS"</span><span class="token punctuation">,</span> <span class="token string">"http://remoteServerIP"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
				conf<span class="token punctuation">.</span><span class="token function">set</span><span class="token punctuation">(</span><span class="token string">"hadoop.job.ugi"</span><span class="token punctuation">,</span> <span class="token string">"hdfsUser"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
				FileSystem fs <span class="token operator">=</span> FileSystem<span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span>conf<span class="token punctuation">)</span><span class="token punctuation">;</span>
				<span class="token comment">// ...</span>
			<span class="token punctuation">}</span> <span class="token keyword">catch</span> <span class="token punctuation">(</span><span class="token class-name">IOException</span> e<span class="token punctuation">)</span> <span class="token punctuation">{</span>
				e<span class="token punctuation">.</span><span class="token function">printStackTrace</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
			<span class="token punctuation">}</span>
		<span class="token punctuation">}</span>
	<span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span> <span class="token keyword">catch</span> <span class="token punctuation">(</span><span class="token class-name">Exception</span> e<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	e<span class="token punctuation">.</span><span class="token function">printStackTrace</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<hr class="footnotes-sep">
<section class="footnotes">
<ol class="footnotes-list">
<li id="fn1" class="footnote-item"><p><a href="https://hadoop.apache.org/docs/r2.8.0/api/org/apache/hadoop/security/UserGroupInformation.html" rel="nofollow">Hadoop Java Doc</a> <a href="#fnref1" rel="nofollow" class="footnote-backref">↩︎</a></p>
</li>
</ol>
</section>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>