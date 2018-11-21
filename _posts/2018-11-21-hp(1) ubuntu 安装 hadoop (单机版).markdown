---
layout:     post
title:      hp(1) ubuntu 安装 hadoop (单机版)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：欢迎评论、补充；24小时内回复；及时更新；欢迎转载。					https://blog.csdn.net/li905663280/article/details/78234878				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<blockquote>
  <p>下载</p>
</blockquote>

<p><a href="https://mirrors.cnnic.cn/apache/hadoop/common/" rel="nofollow">https://mirrors.cnnic.cn/apache/hadoop/common/</a></p>

<blockquote>
  <p>创建用户 </p>
</blockquote>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-built_in">sudo</span> useradd -m hadoop <span class="hljs-operator">-s</span> /bin/bash
<span class="hljs-built_in">sudo</span> passwd hadoop
<span class="hljs-built_in">sudo</span> adduser hadoop <span class="hljs-built_in">sudo</span>

</code></pre>

<blockquote>
  <p>ssh</p>
</blockquote>



<pre class="prettyprint"><code class=" hljs lasso">切换 hadoop
sudo apt<span class="hljs-attribute">-get</span> update
sudo apt<span class="hljs-attribute">-get</span> install openssh<span class="hljs-attribute">-server</span>

配置无密码ssh

cd ~<span class="hljs-subst">/</span><span class="hljs-built_in">.</span>ssh<span class="hljs-subst">/</span>
ssh<span class="hljs-attribute">-keygen</span> <span class="hljs-attribute">-t</span> rsa 会有提示，都按回车就可以
cat <span class="hljs-built_in">.</span>/id_rsa<span class="hljs-built_in">.</span>pub <span class="hljs-subst">&gt;&gt;</span> <span class="hljs-built_in">.</span>/authorized_keys 加入授权

ssh localhost 无需密码就可以访问</code></pre>

<blockquote>
  <p>wordcount</p>
</blockquote>



<pre class="prettyprint"><code class=" hljs livecodeserver"><span class="hljs-built_in">file</span>中创建txt 文件

./bin/hadoop fs -<span class="hljs-built_in">put</span> <span class="hljs-built_in">file</span><span class="hljs-comment">/*.txt input

./bin/hadoop jar ./share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.4.jar wordcount input output
</span></code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>