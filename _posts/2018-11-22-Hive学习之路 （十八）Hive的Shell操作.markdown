---
layout:     post
title:      Hive学习之路 （十八）Hive的Shell操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<div id="topics">
    <div class="post">
        <h1 class="postTitle" id="hive学习之路-十八hive的shell操作">
            <a id="cb_post_title_url" class="postTitle2" href="https://www.cnblogs.com/qingyunzong/p/8847532.html" rel="nofollow">Hive学习之路 （十八）Hive的Shell操作</a>
        </h1>
        <div class="clear"></div>
        <div class="postBody">
            <div id="cnblogs_post_body" class="blogpost-body"><h2 id="一hive的命令行">一、Hive的命令行</h2>
<h3 id="1hive支持的一些命令">1、Hive支持的一些命令</h3>
<blockquote>
<p>Command Description</p>
<p><strong><span>quit</span></strong> Use quit or exit to leave the interactive shell.</p>
<p><strong><span>set key=value</span></strong> Use this to set value of particular configuration variable. One thing to note here is that if you misspell the variable name, cli will not show an error.</p>
<p><strong><span>set</span> </strong>This will print a list of configuration variables that are overridden by user or hive.</p>
<p><strong><span>set -v</span></strong> This will print all hadoop and hive configuration variables.</p>
<p><strong><span>add FILE [file] [file]*</span></strong> Adds a file to the list of resources</p>
<p><span><strong>add jar jarname</strong></span></p>
<p><span><strong> list FILE</strong></span> list all the files added to the distributed cache</p>
<p><strong><span>list FILE [file]*</span></strong> Check if given resources are already added to distributed cache</p>
<p><strong><span>! [cmd]</span></strong> Executes a shell command from the hive shell</p>
<p><strong><span>dfs [dfs cmd]</span></strong> Executes a dfs command from the hive shell</p>
<p><strong><span>[query]</span></strong> Executes a hive query and prints results to standard out</p>
<p><strong><span>source FILE</span></strong> Used to execute a script file inside the CLI.</p>
</blockquote>
<h3 id="2语法结构">2、语法结构</h3>
<div class="cnblogs_code">
<pre>hive [-hiveconf x=y]* [&lt;-i filename&gt;]* [&lt;-f filename&gt;|&lt;-e query-<span>string</span>&gt;] [-S]</pre>
</div>

<p>说明：</p>

<blockquote>
<p>1、-i 从文件初始化 HQL</p>
<p>2、-e 从命令行执行指定的 HQL</p>
<p>3、-f 执行 HQL 脚本</p>
<p>4、-v 输出执行的 HQL 语句到控制台</p>
<p>5、-p connect to Hive Server on port number</p>
<p>6、-hiveconf x=y（Use this to set hive/hadoop configuration variables）</p>
<p>7、-S：表示以不打印日志的形式执行命名操作</p>
</blockquote>

<h3 id="3示例">3、示例</h3>

<h4 id="1运行一个查询">（1）运行一个查询</h4>

<div class="cnblogs_code">
<pre>[hadoop@hadoop3 ~]$ <strong><span>hive -e "select * from cookie.cookie1;"</span></strong></pre>
</div>

<p><img src="https://images2018.cnblogs.com/blog/1228818/201804/1228818-20180415141508766-1214413094.png" alt="" width="777" height="298"></p>

<h4 id="2运行一个文件">（2）运行一个文件</h4>

<p> 编写hive.sql文件</p>

<p><img src="https://images2018.cnblogs.com/blog/1228818/201804/1228818-20180415141653695-2137330979.png" alt=""></p>

<p>运行编写的文件</p>

<p><img src="https://images2018.cnblogs.com/blog/1228818/201804/1228818-20180415141814477-1348619144.png" alt="" width="782" height="298"></p>

<h4 id="3运行参数文件">（3）运行参数文件</h4>

<p>从配置文件启动 hive，并加载配置文件当中的配置参数</p>

<p><img src="https://images2018.cnblogs.com/blog/1228818/201804/1228818-20180415142106103-736436497.png" alt="" width="784" height="288"></p>

<h2 id="二hive的参数配置方式">二、Hive的参数配置方式</h2>

<h3 id="1hive的参数配置大全">1、Hive的参数配置大全</h3>

<p><a href="https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties" rel="nofollow" target="_blank">https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties</a></p>

<h3 id="2hive的参数设置方式">2、Hive的参数设置方式</h3>

<p>开发 Hive 应用时，不可避免地需要设定 Hive 的参数。设定 Hive 的参数可以调优 HQL 代码 的执行效率，或帮助定位问题。然而实践中经常遇到的一个问题是，为什么设定的参数没有 起作用？这通常是错误的设定方式导致的</p>

<p>对于一般参数，有以下三种设定方式：</p>

<blockquote>
<p><strong><span>1、配置文件 （全局有效） </span></strong></p>
<p><strong><span>2、命令行参数（对 hive 启动实例有效） </span></strong></p>
<p><strong><span>3、参数声明 （对 hive 的连接 session 有效）</span></strong></p>
</blockquote>

<h4 id="1配置文件">（1）配置文件</h4>

<p>Hive 的配置文件包括：</p>

<p>　　A.　用户自定义配置文件：$HIVE_CONF_DIR/hive-site.xml</p>

<p>　　B.　默认配置文件：$HIVE_CONF_DIR/hive-default.xml</p>

<p>用户自定义配置会覆盖默认配置。</p>

<p>另外，Hive 也会读入 Hadoop 的配置，因为 Hive 是作为 Hadoop 的客户端启动的，Hive 的配 置会覆盖 Hadoop 的配置。</p>

<p>配置文件的设定对本机启动的所有 Hive 进程都有效。</p>

<h4 id="2命令行参数">（2）命令行参数</h4>

<p>启动 Hive（客户端或 Server 方式）时，可以在命令行添加-hiveconf param=value 来设定参数，例如：</p>

<p><img src="https://images2018.cnblogs.com/blog/1228818/201804/1228818-20180415142748613-2031600308.png" alt="" width="758" height="270"></p>

<p>这一设定对本次启动的 session（对于 server 方式启动，则是所有请求的 session）有效。</p>

<h4 id="3参数声明">（3）参数声明 </h4>

<p>可以在 HQL 中使用 SET 关键字设定参数，例如：</p>

<p><img src="https://images2018.cnblogs.com/blog/1228818/201804/1228818-20180415143012000-1554548601.png" alt=""></p>

<p>这一设定的作用域也是 session 级的。</p>

<blockquote>
<p><strong><span>set hive.exec.reducers.bytes.per.reducer=</span></strong> 每个 reduce task 的平均负载数据量 Hive 会估算总数据量，然后用该值除以上述参数值，就能得出需要运行的 reduceTask 数</p>
<p><strong><span>set hive.exec.reducers.max=</span> </strong>设置 reduce task 数量的上限</p>
<p><strong><span>set mapreduce.job.reduces=</span></strong> 指定固定的 reduce task 数量</p>
</blockquote>

<p>但是，这个参数在必要时&lt;业务逻辑决定只能用一个 reduce task&gt; hive 会忽略，比如在设置 了 set mapreduce.job.reduces = 3，但是 HQL 语句当中使用了 order by 的话，那么就会忽略该参数的设置。</p>

<p>上述三种设定方式的优先级依次递增。即<strong><span>参数声明覆盖命令行参数，命令行参数覆盖配置 文件设定</span></strong>。注意某些系统级的参数，例如 log4j 相关的设定，必须用前两种方式设定，因为 那些参数的读取在 session 建立以前已经完成了。</p>

<p></p><p> </p></div><div id="MySignature"></div>

<div class="clear"></div>
<div id="blog_post_info_block">
<div id="BlogPostCategory"></div>
<div id="EntryTag"></div>
<div id="blog_post_info">
</div>

<div class="clear"></div>
<div id="post_next_prev"></div>
</div></div></div></div>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>