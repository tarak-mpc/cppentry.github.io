---
layout:     post
title:      Hive shell 操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_41851454/article/details/79904213				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h2>目录</h2><p>1、Hive shell 操作</p><p>1.1、Hive 命令行</p><p>1.2、Hive 参数配置方式</p><h1>1、Hive shell 操作</h1><h2>1.1、Hive 命令行</h2><p>这是 hive 支持的一些命令：<br></p><pre><code class="language-html">Command Description
quit Use quit or exit to leave the interactive shell. 
set key=value Use this to set value of particular configuration variable. One thing to note
here is that if you misspell the variable name, cli will not show an error.
set This will print a list of configuration variables that are overridden by user or hive.
set -v This will print all hadoop and hive configuration variables.
add FILE [file] [file]* Adds a file to the list of resources
add jar jarname
list FILE list all the files added to the distributed cache
list FILE [file]* Check if given resources are already added to distributed cache
! [cmd] Executes a shell command from the hive shell
dfs [dfs cmd] Executes a dfs command from the hive shell
[query] Executes a hive query and prints results to standard out
source FILE Used to execute a script file inside the CLI. </code></pre><pre><code class="language-html">语法结构：
hive [-hiveconf x=y]* [&lt;-i filename&gt;]* [&lt;-f filename&gt;|&lt;-e query-string&gt;] [-S]</code></pre><p>说明：</p><p>1、-i 从文件初始化 HQL</p><p>2、-e 从命令行执行指定的 HQL</p><p>3、-f 执行 HQL 脚本</p><p>4、-v 输出执行的 HQL 语句到控制台</p><p>5、-p  connect to Hive Server on port number</p><p>6、-hiveconf x=y（Use this to set hive/hadoop configuration variables）</p><p>7、-S：表示以不打印日志的形式执行命名操作</p><p>示例：</p><p>1、 运行一个查询<br></p><p><img src="https://img-blog.csdn.net/20180411223423805?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxODUxNDU0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></p><p>2、 运行一个文件<br></p><p><img src="https://img-blog.csdn.net/20180411223512701?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxODUxNDU0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></p><p>3、 运行参数文件</p><p>从配置文件启动 hive，并加载配置文件当中的配置参数<br></p><p><img src="https://img-blog.csdn.net/20180411223912762?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxODUxNDU0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></p><p>1.2、Hive 参数配置方式<br></p><p>Hive 参数大全：https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties<br></p><p>开发 Hive 应用时，不可避免地需要设定 Hive 的参数。设定 Hive 的参数可以调优 HQL 代码的执行效率，或帮助定位问题。然而实践中经常遇到的一个问题是，为什么设定的参数没有起作用？这通常是错误的设定方式导致的<br></p><p>对于一般参数，有以下三种设定方式：</p><p><span style="color:#ff0000;">配置文件 （全局有效）</span></p><p><span style="color:#ff0000;">命令行参数（对 hive 启动实例有效）</span></p><p><span style="color:#ff0000;">参数声明 （对 hive 的连接 session 有效）</span><br></p><p><span style="color:#ff0000;">配置文件：Hive 的配置文件包括</span></p><p><span style="color:#ff0000;">    用户自定义配置文件：$HIVE_CONF_DIR/hive-site.xml</span></p><p><span style="color:#ff0000;">    默认配置文件：$HIVE_CONF_DIR/hive-default.xml</span></p><p>用户自定义配置会覆盖默认配置。</p><p>另外，Hive 也会读入 Hadoop 的配置，因为 Hive 是作为 Hadoop 的客户端启动的，Hive 的配置会覆盖 Hadoop 的配置。</p><p>配置文件的设定对本机启动的所有 Hive 进程都有效。<br></p><p><span style="color:#ff0000;">命令行参数：</span>启动 Hive（客户端或 Server 方式）时，可以在命令行添加-hiveconf param=value来设定参数，例如：</p><p>bin/hive -hiveconf hive.root.logger=INFO,console</p><p>这一设定对本次启动的 session（对于 server 方式启动，则是所有请求的 session）有效。<br></p><p><span style="color:#ff0000;">参数声明：</span>可以在 HQL 中使用 SET 关键字设定参数，例如：</p><p>set mapred.reduce.tasks = 10;</p><p>set mapreduce.job.reduces = 10;</p><p>这一设定的作用域也是 session 级的。<br></p><p>set hive.exec.reducers.bytes.per.reducer= 每个 reduce task 的平均负载数据量Hive 会估算总数据量，然后用该值除以上述参数值，就能得出需要运行的 reduceTask 数<br></p><p>set hive.exec.reducers.max= 设置 reduce task 数量的上限 <br></p><p>set mapreduce.job.reduces= 指定固定的 reduce task 数量但是，这个参数在必要时&lt;业务逻辑决定只能用一个 reduce task&gt; hive 会忽略，比如在设置了 set mapreduce.job.reduces = 3，但是 HQL 语句当中使用了 order by 的话，那么就会忽略该参数的设置<br></p><p>上述三种设定方式的优先级依次递增。即<span style="color:#ff0000;">参数声明覆盖命令行参数，命令行参数覆盖配置文件设定。</span>注意某些系统级的参数，例如 log4j 相关的设定，必须用前两种方式设定，因为那些参数的读取在 session 建立以前已经完成了。<br></p>            </div>
                </div>