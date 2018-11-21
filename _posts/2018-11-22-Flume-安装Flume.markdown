---
layout:     post
title:      Flume-安装Flume
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/zhangdong2012/article/details/53065493				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>Flume最初是由Cloudera开发的，后来被纳入Apache软件基金会。</p>

<h3 id="下载flume">下载Flume</h3>

<p><a href="http://flume.apache.org/download.html" rel="nofollow">下载地址</a> <br>
选择对于版本的Flume压缩包下载到本地</p>

<h3 id="安装flume">安装Flume</h3>

<ul>
<li>解压</li>
</ul>

<pre class="prettyprint"><code class=" hljs lasso"><span class="hljs-subst">&gt;</span>tar <span class="hljs-attribute">-xvzf</span> apache<span class="hljs-attribute">-flume</span><span class="hljs-subst">-</span><span class="hljs-number">1.7</span><span class="hljs-number">.0</span><span class="hljs-attribute">-bin</span><span class="hljs-built_in">.</span>tar<span class="hljs-built_in">.</span>gz</code></pre>

<ul>
<li>配置Flume运行环境</li>
</ul>

<pre class="prettyprint"><code class=" hljs avrasm">&gt;cd flume1<span class="hljs-number">.7</span><span class="hljs-number">.0</span>/conf
&gt;<span class="hljs-keyword">cp</span> flume-env<span class="hljs-preprocessor">.sh</span><span class="hljs-preprocessor">.template</span> flume-evn<span class="hljs-preprocessor">.sh</span>
&gt;vim flume-env<span class="hljs-preprocessor">.sh</span>

export JAVA_HOME=/usr/develop-env/jdk1<span class="hljs-number">.7</span><span class="hljs-number">.0</span>_65</code></pre>

<ul>
<li>验证安装是否成功</li>
</ul>



<pre class="prettyprint"><code class=" hljs livecodeserver">&gt;cd ../bin
&gt;./flume-ng <span class="hljs-built_in">version</span>
Flume <span class="hljs-number">1.7</span><span class="hljs-number">.0</span>
Source code repository: <span class="hljs-keyword">https</span>://git-wip-us.apache.org/repos/asf/flume.git
Revision: <span class="hljs-number">511</span>d868555dd4d16e6ce4fedc72c2d1454546707
Compiled <span class="hljs-keyword">by</span> bessbd <span class="hljs-command"><span class="hljs-keyword">on</span> <span class="hljs-title">Wed</span> <span class="hljs-title">Oct</span> <span class="hljs-title">12</span> <span class="hljs-title">20</span>:<span class="hljs-title">51</span>:<span class="hljs-title">10</span> <span class="hljs-title">CEST</span> <span class="hljs-title">2016</span></span>
From source <span class="hljs-operator">with</span> checksum <span class="hljs-number">0</span>d21b3ffdc55a07e1d08875872c00523</code></pre>

<p>出现以上信息，说明安装Flume成功</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>