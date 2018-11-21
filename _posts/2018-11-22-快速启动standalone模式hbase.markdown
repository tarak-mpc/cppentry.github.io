---
layout:     post
title:      快速启动standalone模式hbase
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                

<p>快速安装<span style="font-family:'DejaVu Serif', serif;"><span style="font-size:14px;"><span lang="en-us" xml:lang="en-us">Hbase</span></span></span></p>
<p><span style="font-family:'DejaVu Serif', serif;"><span style="font-size:14px;"><span lang="en-us" xml:lang="en-us">1.mkdir ~/hbase</span></span></span></p>
<p><span style="font-family:'DejaVu Serif', serif;"><span style="font-size:14px;"><span lang="en-us" xml:lang="en-us">2.cd ~/hbase</span></span></span></p>
<p><span style="font-family:'DejaVu Serif', serif;"><span style="font-size:14px;"><span lang="en-us" xml:lang="en-us">3.<a href="http://mirror.esocc.com/apache/hbase/stable/hbase-0.94.8.tar.gz" rel="nofollow">http://mirror.esocc.com/apache/hbase/stable/hbase-0.94.8.tar.gz</a></span></span></span>下载<span style="font-family:'DejaVu Serif', serif;"><span style="font-size:14px;"><span lang="en-us" xml:lang="en-us">hbase</span></span></span>安装包。</p>
<p><span style="font-family:'DejaVu Serif', serif;"><span style="font-size:14px;"><span lang="en-us" xml:lang="en-us">wgethttp://mirror.esocc.com/apache/hbase/stable/hbase-0.94.8.tar.gz</span></span></span></p>
<p><span style="font-family:'DejaVu Serif', serif;"><span style="font-size:14px;"><span lang="en-us" xml:lang="en-us">4.tar xvfz hbase-0.94.8.tar.gz</span></span></span></p>
<p><span style="font-family:'DejaVu Serif', serif;"><span style="font-size:14px;"><span lang="en-us" xml:lang="en-us">5.</span></span></span>配置环境变量：</p>
<p><span style="font-family:'DejaVu Serif', serif;"><span style="font-size:14px;"><span lang="en-us" xml:lang="en-us">sudo-s vim /etc/profile</span></span></span></p>
<p>添加下面两行：</p>
<p><span style="font-family:'DejaVu Serif', serif;"><span style="font-size:14px;"><span lang="en-us" xml:lang="en-us">exportHBASE_HOME=~/hbase/hbase-0.94.8</span></span></span></p>
<p><span style="font-family:'DejaVu Serif', serif;"><span style="font-size:14px;"><span lang="en-us" xml:lang="en-us">PATH=$HBASE_HOME/bin/:$PATH</span></span></span></p>
<p><font face="DejaVu Serif,serif"><font size="3"><span lang="en-us" xml:lang="en-us"><font size="3"><span style="font-family:'DejaVu Serif', serif;">6.
<span style="font-size:14px;">在<code class="filename">conf/hbase-site.xml</code><span style="font-size:14px;">文件中添加下面属性设置hbase.rootdir，hbase的写目录。DIRECTORY是你安装hbase的目录</span></span></span></font></span></font></font></p>
<p><font face="DejaVu Serif,serif"><font size="3"><span lang="en-us" xml:lang="en-us"><span style="font-size:14px;"><span style="font-family:'DejaVu Serif', serif;"><span style="font-size:14px;"></span></span></span></span></font></font></p>
<pre class="programlisting">&lt;property&gt;
    &lt;name&gt;hbase.rootdir&lt;/name&gt;
    &lt;value&gt;file:///DIRECTORY/hbase&lt;/value&gt;
  &lt;/property&gt;</pre>
<br><p></p>
<p><font face="DejaVu Serif,serif"><span style="font-size:14px;"><span lang="en-us" xml:lang="en-us"><span style="font-size:14px;"><span style="font-family:'DejaVu Serif', serif;">7</span></span>.start-hbase.sh</span></span></font></p>
<p>这样<span style="font-family:'DejaVu Serif', serif;"><span style="font-size:14px;"><span lang="en-us" xml:lang="en-us">standalone</span></span></span>模式的<span style="font-family:'DejaVu Serif', serif;"><span style="font-size:14px;"><span lang="en-us" xml:lang="en-us">hbase</span></span></span>就起来，你可以输入<span style="font-family:'DejaVu Serif', serif;"><span style="font-size:14px;"><span lang="en-us" xml:lang="en-us">hbaseshell</span></span></span>开始进行交互操作<span style="font-family:'DejaVu Serif', serif;"><span style="font-size:14px;"><span lang="en-us" xml:lang="en-us">hbase</span></span></span>了。</p>
<br>            </div>
                </div>