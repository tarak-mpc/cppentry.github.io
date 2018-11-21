---
layout:     post
title:      hbase 备份HMatser服务——完全分布式
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/wild46cat/article/details/53219162				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1>hbase 备份HMatser服务——完全分布式</h1>
<div><span style="font-size:18px;">如果目前使用的是伪分布式，那么请看这篇文章实现备份HMaster服务：</span></div>
<div><span style="font-size:18px;">http://blog.csdn.net/wild46cat/article/details/53218489</span></div>
<div><span style="font-size:18px;"><br></span></div>
<div><span style="font-size:18px;">1、保证正常的HBase能够跑起来：</span></div>
<div><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20161118170854848?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<div><span style="font-size:18px;"><br></span></div>
<div><span style="font-size:18px;">2、关闭HBase服务：stop-hbase.sh</span></div>
<div><span style="font-size:18px;"><br></span></div>
<div><span style="font-size:18px;">3、在hbase/conf/文件加下添加backup-masters文件，文件中的内容是需要启动备份HMaster服务的主机名：</span></div>
<div><span style="font-size:18px;">vim backup-masters</span></div>
<div><span style="font-size:18px;"><br></span></div>
<div><span style="font-size:18px;">4、拷贝文件到集群中的每个节点的conf/文件夹下。</span></div>
<div><span style="font-size:18px;"><br></span></div>
<div><span style="font-size:18px;">5、确保每一个节点的hbase和zookeeper服务已经关闭。</span></div>
<div><span style="font-size:18px;"><br></span></div>
<div><span style="font-size:18px;">6、重新启动HBase服务：start-hbase.sh</span></div>
<div><span style="font-size:18px;"><br></span></div>
<div><span style="font-size:18px;">7、观察配置备份HMaster服务的机器上的状态。下面是一个测试截图，我使用的备份机器是host2。</span></div>
<div><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20161118171407123?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
            </div>
                </div>