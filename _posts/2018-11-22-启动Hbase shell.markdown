---
layout:     post
title:      启动Hbase shell
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h3>首先，可以试试如下命令，看看能否启动，   </h3>

<pre class="has">
<code>./bin/hbase shell   </code></pre>

<h3>很有可能，像我一样，启动不了。</h3>

<h3>接下来，要做的是找到HBASE的安装目录，使用如下命令：</h3>

<pre class="has">
<code>whereis hbase</code></pre>

<h3><span><img alt="" class="has" height="61" src="https://img-blog.csdnimg.cn/20181112155805505.png" width="474"><span style='background:rgba(220,220,220,.5) url("https://csdnimg.cn/release/blog_editor_html/release1.3.1/ckeditor/plugins/widget/images/handle.png");'></span><span title="点击并拖拽以改变尺寸">​</span></span><br>
可以看到，hbase的安装目录。<br>
接着，进入该目录：</h3>

<pre class="has">
<code>cd /usr/bin</code></pre>

<h3>最后使用 <span style="color:#f33b45;">./hbase shell</span> 命令就可以正确的启动hbase，然后使用shell命令来进行数据库的相关操作。</h3>            </div>
                </div>