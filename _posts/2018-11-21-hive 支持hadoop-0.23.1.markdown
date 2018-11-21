---
layout:     post
title:      hive 支持hadoop-0.23.1
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>hive要支持hadoop-0.23.1需要自己从源码编译，具体的做法如下：</p>
<p> </p>
<p>
</p>
<pre><code class="language-java">  $ svn co http:<span class="code-comment" style="color:#808080;background-color:inherit;">//svn.apache.org/repos/asf/hive/trunk hive
</span>  $ cd hive
  $ ant clean <span class="code-keyword" style="color:#000091;background-color:inherit;">package</span> -Dhadoop.version=0.23.1 -Dhadoop-0.23.version=0.23.1 -Dhadoop.mr.rev=23</code></pre>

<p> </p>            </div>
                </div>