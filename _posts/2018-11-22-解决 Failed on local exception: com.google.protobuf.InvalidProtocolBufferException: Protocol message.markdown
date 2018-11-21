---
layout:     post
title:      解决 Failed on local exception: com.google.protobuf.InvalidProtocolBufferException: Protocol message
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，请随意转载。					https://blog.csdn.net/a_hui_tai_lang/article/details/81676429				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>这个错误主要是端口号写错了，我来演示一下错误</p>
<pre><code>[root@hadoop1 ~]# hdfs dfs -ls hdfs://192.168.0.37:50070/
ls: Failed on local exception: com.google.protobuf.InvalidProtocolBufferException: Protocol message end-group tag did not match expected tag.; Host Details : local host is: "hadoop1/192.168.0.37"; destination host is: "hadoop1":50070;
</code></pre>
<p><img src="https://img-blog.csdn.net/20180815104736598" alt="这里写图片描述"></p>
<p>那么怎么获取正确的端口号呢？<br>
hdfs getconf -confKey <a href="http://fs.default.name" rel="nofollow">fs.default.name</a><br>
<img src="https://img-blog.csdn.net/20180815104918200" alt="这里写图片描述"><br>
获取正确的端口号就可以正常访问hdfs啦！<br>
<img src="https://img-blog.csdn.net/20180815104804159" alt="这里写图片描述"></p>
<p>微信公众号:程序国度<br>
<img src="https://img-blog.csdn.net/201810231038032" alt="在这里插入图片描述"></p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>