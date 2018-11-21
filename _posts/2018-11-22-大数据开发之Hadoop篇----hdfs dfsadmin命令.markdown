---
layout:     post
title:      大数据开发之Hadoop篇----hdfs dfsadmin命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>今天我们来了解一下hdfs dfsadmin这个命令，前面我们已经多次使用了hdfs dfs这个命令来对hdfs上的文件进行操作了。而是在生产上面我们还是会遇到不同的情况。今天我们简单讲解一下hdfs dfsadmin中的两个命令，以及他们在生产当中是怎样使用的</p>

<p>1，hdfs dfsadmin -report 这个命令其实是用来查看整个hdfs的健康情况的，因为我们现在是使用cdh的版本，他已经封装好了一个用户查看健康情况的web界面给你，你只需要在web上看就行了。但是，在使用apache版本的时候，我们只能将这个命令写到一个bash脚本当中，使其定期地返回hdfs健康情况给我们看。</p>

<p><img alt="" class="has" height="533" src="https://img-blog.csdn.net/20181016113227497?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zOTcwMjgzMQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="321"></p>

<p>2，hdfs dfsadmin -safemode &lt;enter | leave | get | wait&gt; 安全模式  这个命令当中，我们常用的是enter和leave。首先什么是安全模式，就是只读模式。所以我们一般在生产中什么场景下使用呢？</p>

<p>如果我们是手工进入到这个安全模式的话，其他用户只能读取里面的数据，而不能添加写入或者修改。这个时候我们一般是生产维护的过程，比如进行数据迁移，备份等。等完事后再解除安全模式</p>

<p>如果是系统自动进入到安全模式的话就说明我们的hdfs可能出现问题了，这个时候我们也分为两种操作：a，手动的输入命令hdfs dfsadmin -safemode leave这个命令，如果能正常退回普通模式的话就没有问题了。b，如果手工输入命令后还是不能返回普通模式的话，说明NN节点受损了，这个时候我们要仔细第阅读NN节点的log日志来排查可能的问题。</p>

<p><img alt="" class="has" height="403" src="https://img-blog.csdn.net/20181016114327978?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zOTcwMjgzMQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="330"></p>

<p>我们可以看到当进入到safemode模式后，系统提示Safe mode is ON这个提示，然后我就不能put文件上去了，当我退出以后就可以了。另外在safe模式下是可以从hadoop上面get文件下来的，但不能moveToLocal这个中命令，你只能copy下来本地。</p>            </div>
                </div>