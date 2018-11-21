---
layout:     post
title:      hadoop streaming 使用总结
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <ol><li>hadoop client： 客户端可以让用户访问集群，hadoop client <a href="http://www.baidu.com/" rel="nofollow">使用手册</a>，通过配置hadoop-site.xml文件，实现访问不同集群，同时该客户端目录的 xx/xx/hadoop/bin/hadoop 是你配置的hadoop命令路径，在streaming命令参数配置时，需要指明该hadoop位置，对于该命令，可以通过 alias hadoopdr='xx/xx/hadoop/bin/hadoop'，用hadoopdr别名代替，或者可写入~/.bashrc</li>
	<li>streaming是管道流，不同于文件流，在streaming Python编程中，输入输出采用的是：sys.stdin，sys.stderr；管道流可以使streaming的编程语言多样化，在 -mapper 或-reduce阶段，可以采用Python语言，同样可以采用sh等其他语言，在使用<a href="http://www.baidu.com/" rel="nofollow">某工具</a>根据图片ID查询图片对应的words时：-mapper ' ./bin/query --flagfile=word_flags.conf | ./merged'，同样的道理，有job在map阶段仅仅做一个输入、输出的操作，可以通过 -mapper 'cat' 实现，这相当于用sh语句实现了map函数</li>
	<li>仅需要map函数时候，可以在配置mapred.reduce.tasks=0，map/reduce框架就不会创建reducer任务，mapper任务的输出就是整个作业的最终输出</li>
	<li>streaming参数配置的文件地址说明：
	<ol><li>cmd "$HADOOP_HOME jar $STREAMING    ##HADOOP_HOME、STREAMING 都是在配置hadoop客户端时的地址，本地地址，streaming我目前没配置，不知道为什么可以跑成功</li>
		<li>-cacheArchive ${PYTHON_URI}  ## 用于把 集群中的PYTHON_URI 文件拷贝到任务当前工作目录并自动把文件解压缩，常用方式："/app/share/python2.7.9.tar.gz#python27"后面 # 后的python27是当前工作目录下的符号链接，它指向python2.7.9.tar.gz解压后的目录</li>
		<li>-mapper '${PYTHON_IN_HADOOP} ${SCRIPT} map'   # PYTHON_IN_HADOOP表示{PYTHON_URI}解压之后的python位置，是个相对路径，一般是：“python27/bin/python”，类似于sh命令中的 python wordcount.py，后面的map是输入参数</li>
		<li>-input \ output 计算输入集群文件、输出结果到集群</li>
	</ol></li>
</ol>            </div>
                </div>