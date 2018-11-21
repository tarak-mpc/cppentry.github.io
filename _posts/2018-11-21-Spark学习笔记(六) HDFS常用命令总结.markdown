---
layout:     post
title:      Spark学习笔记(六) HDFS常用命令总结
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p></p><div class="toc"><h3>HDFS常用命令总结</h3><ul><li><a href="#1__1" rel="nofollow">1. 目录和文件管理</a></li><ul><li><a href="#11__mkdir_3" rel="nofollow">1.1 创建目录 -mkdir</a></li><li><a href="#12__ls_11" rel="nofollow">1.2 列出目录和文件 -ls</a></li><li><a href="#13_HDFS_copyFromLocal_18" rel="nofollow">1.3 复制本地文件到HDFS -copyFromLocal</a></li><ul><li><a href="#131_winterbooks_24" rel="nofollow">1.3.1 在winter目录下创建books目录</a></li><li><a href="#132__29" rel="nofollow">1.3.2 查看刚刚创建的目录</a></li><li><a href="#133_test3txtbooks_35" rel="nofollow">1.3.3 复制test3.txt到books目录下</a></li><li><a href="#134_HDFS_42" rel="nofollow">1.3.4 同时复制多个文件到HDFS</a></li></ul><li><a href="#14_HDFS_copyToLocal_49" rel="nofollow">1.4 复制HDFS文件到本地 -copyToLocal</a></li><li><a href="#17_HDFS_cp_55" rel="nofollow">1.7 复制HDFS文件 -cp</a></li><li><a href="#18_HDFS_rm_61" rel="nofollow">1.8 删除HDFS文件 -rm</a></li></ul><li><a href="#2_HDFS_WebHDFS_66" rel="nofollow">2. HDFS Web页面浏览HDFS</a></li><ul><li><a href="#21__68" rel="nofollow">2.1 页面地址</a></li><li><a href="#22_hdfssitexml_72" rel="nofollow">2.2 如果没有权限操作文件修改配置文件hdfs-site.xml</a></li></ul></ul></div><p></p>
<h1><a id="1__1"></a>1. 目录和文件管理</h1>
<p>HDFS的命令和Linux的命令比较相似，所以也比较容易记忆。</p>
<h2><a id="11__mkdir_3"></a>1.1 创建目录 -mkdir</h2>
<p>新部署的Hadoop的HDFS里是空的，需要创建当前用户权限能访问的目录。</p>

<table>
<thead>
<tr>
<th align="left">命令</th>
<th align="left">说明</th>
</tr>
</thead>
<tbody>
<tr>
<td align="left">hadoop fs -mkdir /user</td>
<td align="left">在根目录（/）下面创建user目录</td>
</tr>
<tr>
<td align="left">hadoop fs -mkdir /user/winter</td>
<td align="left">在user目录下面创建winter目录 ,"winter"为当前系统用户名</td>
</tr>
<tr>
<td align="left">hadoop fs -mkdir -p /user/winter</td>
<td align="left">一次性在根目录（/）下创建/user/winter这2个目录</td>
</tr>
</tbody>
</table><h2><a id="12__ls_11"></a>1.2 列出目录和文件 -ls</h2>

<table>
<thead>
<tr>
<th align="left">命令</th>
<th align="left">说明</th>
</tr>
</thead>
<tbody>
<tr>
<td align="left">hadoop fs -ls</td>
<td align="left">列出当前用户目录</td>
</tr>
<tr>
<td align="left">hadoop fs -ls /</td>
<td align="left">列出根目录（/）下目录和文件</td>
</tr>
<tr>
<td align="left">hadoop fs -ls -R /</td>
<td align="left">递归的列出根目录（/）下所有目录和文件</td>
</tr>
</tbody>
</table><h2><a id="13_HDFS_copyFromLocal_18"></a>1.3 复制本地文件到HDFS -copyFromLocal</h2>

<table>
<thead>
<tr>
<th align="left">命令</th>
<th align="left">说明</th>
</tr>
</thead>
<tbody>
<tr>
<td align="left">hadoop fs - copyFromLocal [localsrc] [dst]</td>
<td align="left">复制本地文件到HDFS</td>
</tr>
<tr>
<td align="left">hadoop fs - copyFromLocal -f [localsrc] [dst]</td>
<td align="left">强制复制本地文件到HDFS，如果已存在，则直接覆盖</td>
</tr>
</tbody>
</table><p>比如本机当前目录下有个文件叫test3.txt，现在需要把它拷到HDFS里的winter目录下的books目录下</p>
<h3><a id="131_winterbooks_24"></a>1.3.1 在winter目录下创建books目录</h3>
<pre><code>hadoop fs -mkdir books
</code></pre>
<h3><a id="132__29"></a>1.3.2 查看刚刚创建的目录</h3>
<pre><code>hadoop fs -ls
</code></pre>
<p><img src="https://img-blog.csdnimg.cn/20181115170813681.png" alt="在这里插入图片描述"></p>
<h3><a id="133_test3txtbooks_35"></a>1.3.3 复制test3.txt到books目录下</h3>
<pre><code>hadoop fs -copyFromLocal test3.txt books
</code></pre>
<p>如图test3.txt已经复制到HDFS里了。<br>
<img src="https://img-blog.csdnimg.cn/20181115171107346.png" alt="在这里插入图片描述"></p>
<h3><a id="134_HDFS_42"></a>1.3.4 同时复制多个文件到HDFS</h3>
<pre><code>hadoop fs -copyFromLocal test4.txt test5.txt books
</code></pre>
<p><img src="https://img-blog.csdnimg.cn/20181115171847640.png" alt="在这里插入图片描述"></p>
<h2><a id="14_HDFS_copyToLocal_49"></a>1.4 复制HDFS文件到本地 -copyToLocal</h2>
<pre><code>hadoop fs -copyToLocal books/test3.txt test7.txt
</code></pre>
<p>这个时候查看本机可以发现多了个文件test7.txt</p>
<h2><a id="17_HDFS_cp_55"></a>1.7 复制HDFS文件 -cp</h2>
<pre><code>hadoop fs -cp books/test3.txt books/test6.txt
</code></pre>
<p><img src="https://img-blog.csdnimg.cn/20181115172625509.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dpbnRlcmtpbmcz,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<h2><a id="18_HDFS_rm_61"></a>1.8 删除HDFS文件 -rm</h2>
<pre><code>hadoop fs -rm books/test6.txt
</code></pre>
<p><img src="https://img-blog.csdnimg.cn/20181115172832582.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dpbnRlcmtpbmcz,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<h1><a id="2_HDFS_WebHDFS_66"></a>2. HDFS Web页面浏览HDFS</h1>
<h2><a id="21__68"></a>2.1 页面地址</h2>
<p><a href="http://localhost:50070" rel="nofollow">http://localhost:50070</a><br>
然后打开浏览文件系统<br>
<img src="https://img-blog.csdnimg.cn/20181115173151485.png" alt="在这里插入图片描述"></p>
<h2><a id="22_hdfssitexml_72"></a>2.2 如果没有权限操作文件修改配置文件hdfs-site.xml</h2>
<pre><code class="prism language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>name</span><span class="token punctuation">&gt;</span></span>dfs.permissions.enabled<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>name</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>value</span><span class="token punctuation">&gt;</span></span>false<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>value</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>property</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>name</span><span class="token punctuation">&gt;</span></span>dfs.webhdfs.enabled<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>name</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>value</span><span class="token punctuation">&gt;</span></span>true<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>value</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>property</span><span class="token punctuation">&gt;</span></span>
</code></pre>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>