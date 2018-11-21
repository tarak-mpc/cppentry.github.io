---
layout:     post
title:      hadoop shell 常用命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_22027637/article/details/78442387				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
1）启动hdfs<br><span></span>格式化文件系统（仅第一次执行即可，不要重复执行）：hdfs/hadoop namenode -format<br><span></span>启动hdfs: sbin/start-dfs.sh<br><span></span>验证是否启动成功：<br><span></span>jps<br><span></span>DataNode<br><span></span>SecondaryNameNode<br><span></span>NameNode<br><br><p>浏览器访问方式： http://hadoop000:50070</p>
<p><img src="" alt=""><img src="" alt=""><br></p>
<br>
2）停止hdfs<br><p>sbin/stop-dfs.sh </p>
<p><br></p>
<p>3) hadoop shell 命令</p>
<p><span></span>hadoop fs -cat  /        //  -text</p>
<p><span>hadoop fs -</span>ls   <span>/        //hadoop fs -ls -r //递归查看目录</span></p>
<p><span><span>hadoop fs -</span>get<span>/   
</span></span></p>
<p><span><span><span>hadoop fs -</span>mkdir<span>/    // -p 递归创建目录 hadoop fs -mkdir -p /text/a/b</span></span></span></p>
<p><span><span><span><span>hadoop fs -</span>mv<span>/  移动       </span>    </span>    </span></span></p>
<p><span><span><span>hadoop fs -</span>put<span>  /         // hadoop fs -put hello.txt /</span></span></span></p>
<p><span><span><span><span>hadoop fs -</span>copyFromLocal<span>/    从本地上传到HDFS</span></span></span></span></p>
<p><span><span><span><span><span>hadoop fs -</span>rm<span>/        // -r递归删除 hadoop fs -rm -r /</span></span></span></span></span></p>
            </div>
                </div>