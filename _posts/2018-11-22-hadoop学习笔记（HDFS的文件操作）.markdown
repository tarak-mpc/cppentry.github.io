---
layout:     post
title:      hadoop学习笔记（HDFS的文件操作）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/arivn_jianming/article/details/47035807				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>命令行方式和API方式：</p>
<p><span></span>列出HDFS下的文件    bin/hadoop dfs -ls   /-ls ./in</p>
<p><span></span>上传文件到HDFS   <span></span>bin/hadoop dfs -put ../abc abc</p>
<p><span></span>将HDFS的文件复制到本地<span></span>bin/hadoop dfs -get abc ./xyz</p>
<p><span></span>删除HDFS下的文档<span> </span>
bin/hadoop dfs -rmr abc</p>
<p><span></span>查看HDFS下某个文件内容<span></span>bin/hadoop dfs -cat ./out/part-00000</p>
<p><span></span>查看HDFS基本统计信息<span></span>bin/hadoop dfsadmin -report</p>
<p><span></span>进入和退出安全模式<span> </span>
bin/hadoop dfsadmin -safemode enter<span> </span>bin/hadoop dfsadmin -safemode leave</p>
<p><span></span>启动某些后台进程而不是全部进程<span></span>start -all.sh <span>
</span>stop -all.sh<span></span>(stop)start -dfs.sh      (stop)start -mapred.sh </p>
<p>API列表：</p>
<p><span></span>hadoop.apache.org/hdfs/docs/r0.22.0/api/index.html</p>
<p>负载均衡：当节点出现故障，或新增节点时，数据块分布可能不均匀，负载均衡可以重新平衡各个datanode上的数据块的分布。</p>
            </div>
                </div>