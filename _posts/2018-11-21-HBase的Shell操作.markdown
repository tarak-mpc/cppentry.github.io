---
layout:     post
title:      HBase的Shell操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>前提是你需要安装好自己的HBase环境，并启动HBase,启动成功如下所示：</p>

<p><img src="https://img-blog.csdn.net/20151202114048011" alt="这里写图片描述" title=""></p>

<p>首先我们查看下如何进入shell: <br>
ykp@ubuntu:~/hbase/hbase-1.0.0/bin$ ./hbase <br>
<img src="https://img-blog.csdn.net/20151202114536402" alt="这里写图片描述" title=""></p>

<p>进入shell： <br>
ykp@ubuntu:~/hbase/hbase-1.0.0/bin$ ./hbase shell <br>
<img src="https://img-blog.csdn.net/20151202114759154" alt="这里写图片描述" title=""></p>

<p>进入shell之后我们就可以进行各种操作了。 <br>
下面我们来进行table的增删改查操作： <br>
<img src="https://img-blog.csdn.net/20151202115008661" alt="这里写图片描述" title=""></p>

<ul>
<li>创建table <br>
hbase(main):002:0&gt; create ‘user’,’info’</li>
<li>查看表 <br>
hbase(main):003:0&gt; list <br>
<img src="https://img-blog.csdn.net/20151202115321864" alt="这里写图片描述" title=""></li>
<li>查看表结构 <br>
hbase(main):002:0&gt; describe ‘user’ <br>
<img src="https://img-blog.csdn.net/20151202115411576" alt="这里写图片描述" title=""> </li>
<li>添加数据 <br>
hbase(main):004:0&gt; put ‘user’,’1’,’info:name’,’ykp’ <br>
<img src="https://img-blog.csdn.net/20151202115538760" alt="这里写图片描述" title=""></li>
<li>查询单条记录 <br>
hbase(main):008:0&gt; get ‘user’,’1’ <br>
<img src="https://img-blog.csdn.net/20151202115656649" alt="这里写图片描述" title=""></li>
<li>查询记录的条数 <br>
hbase(main):009:0&gt; count ‘user’ <br>
<img src="https://img-blog.csdn.net/20151202115741703" alt="这里写图片描述" title=""></li>
<li>查询全表 <br>
hbase(main):007:0&gt; scan ‘user’ <br>
<img src="https://img-blog.csdn.net/20151202124133669" alt="这里写图片描述" title=""></li>
<li>查询某个表某个列中所有数据 <br>
hbase(main):003:0&gt; scan ‘user’,{COLUMNS=&gt;’info:name’} <br>
注意：COLUMNS关键字是大小写敏感的 <br>
<img src="https://img-blog.csdn.net/20151202124243461" alt="这里写图片描述" title=""></li>
<li><p>STARTROW和ENDROW的使用 <br>
hbase(main):007:0&gt; scan ‘user’,{STARTROW=&gt;’1’,ENDROW=&gt;’3’} <br>
<img src="https://img-blog.csdn.net/20151202124437478" alt="这里写图片描述" title=""> <br>
由此可见查询的结果集是前闭后开形式的。我们再举个例子： <br>
<img src="https://img-blog.csdn.net/20151202124447687" alt="这里写图片描述" title=""></p></li>
<li><p>更新数据记录 <br>
没有更新操作，实际上就是重写一遍 <br>
hbase(main):019:0&gt; put ‘user’,’3’,’info:name’,’zzz’ <br>
<img src="https://img-blog.csdn.net/20151202124600561" alt="这里写图片描述" title=""></p></li>
<li>删除数据 <br>
hbase(main):004:0&gt; delete ‘user’,’3’,’info:name’ <br>
<img src="https://img-blog.csdn.net/20151202124719002" alt="这里写图片描述" title=""></li>
<li>删除table <br>
hbase(main):009:0&gt; disable ‘user’ <br>
hbase(main):011:0&gt; drop ‘user’ <br>
<img src="https://img-blog.csdn.net/20151202124813939" alt="这里写图片描述" title=""> <br>
由此我们可以看出经过disable操作之后的table是不能够进行各种操作的。</li>
</ul>

<p>到此处我们有关table的增删改查操作已经讲解完毕。下面讲解写如何通过help来获取帮助。</p>

<p>学会使用help： <br>
对于shell命令，我们可以通过help来进行帮助提示，这些命令是分组的，如，General：status, table_help, version, whoami；namespace：alter_namespace, create_namespace, describe_namespace……..</p>

<ul>
<li><p>查看当前集群状态 <br>
hbase(main):028:0&gt; status <br>
<img src="https://img-blog.csdn.net/20151202125132677" alt="这里写图片描述" title=""></p></li>
<li><p>查看当前用户 <br>
hbase(main):026:0&gt; whoami <br>
<img src="https://img-blog.csdn.net/20151202130117012" alt="这里写图片描述" title=""></p></li>
<li>namespace <br>
<img src="https://img-blog.csdn.net/20151202125334181" alt="这里写图片描述" title=""></li>
</ul>

<p>退出shell： <br>
hbase(main):014:0&gt; exit <br>
或者Ctrl+c</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>