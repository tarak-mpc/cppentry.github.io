---
layout:     post
title:      Hbase复制(Replication )
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/shenliang1985/article/details/51420112				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1 style="text-align:center;">Hbase复制使用手册</h1>
<h1>1 Hbase复制简介</h1>
<p><a name="OLE_LINK177"></a><a name="OLE_LINK151"></a><a name="OLE_LINK104"></a><a name="OLE_LINK103">通过hbase的replication功能实现集群间的相互复制.</a></p>
<h1>2 环境</h1>
<p>这里hbase版本为hbase-0.98.6-cdh5.3.3</p>
<p> </p>
<h1>3 配置</h1>
<p> </p>
<p>各个集群里配置hbase-site.xml里的复制功能:</p>
<p>&lt;property&gt;</p>
<p>&lt;name&gt;hbase.replication&lt;/name&gt;</p>
<p>&lt;value&gt;true&lt;/value&gt;</p>
<p>&lt;/property&gt;</p>
<p> </p>
<p>完整配置文件附录:</p>
<p align="left">&lt;configuration&gt;</p>
<p align="left">&lt;property&gt;</p>
<p align="left">    &lt;name&gt;hbase.master&lt;/name&gt;</p>
<p align="left">    &lt;value&gt;shenl:6000&lt;/value&gt;</p>
<p align="left">&lt;/property&gt;</p>
<p align="left">&lt;property&gt;</p>
<p align="left">    &lt;name&gt;hbase.master.maxclockskew&lt;/name&gt;</p>
<p align="left">    &lt;value&gt;180000&lt;/value&gt;</p>
<p align="left">&lt;/property&gt;</p>
<p align="left">&lt;property&gt;</p>
<p align="left">    &lt;name&gt;hbase.rootdir&lt;/name&gt;</p>
<p align="left">    &lt;value&gt;hdfs://shenl:8020/hbase&lt;/value&gt;</p>
<p align="left">&lt;/property&gt;</p>
<p align="left">&lt;property&gt;</p>
<p align="left">    &lt;name&gt;hbase.cluster.distributed&lt;/name&gt;</p>
<p align="left">    &lt;value&gt;true&lt;/value&gt;</p>
<p align="left">&lt;/property&gt;</p>
<p align="left">&lt;property&gt;</p>
<p align="left">    &lt;name&gt;hbase.zookeeper.quorum&lt;/name&gt;</p>
<p align="left">    &lt;value&gt;shenl&lt;/value&gt;</p>
<p align="left">&lt;/property&gt;</p>
<p align="left">&lt;property&gt;</p>
<p align="left">    &lt;name&gt;hbase.zookeeper.property.dataDir&lt;/name&gt;</p>
<p align="left">    &lt;value&gt;/root/zookeeper/data&lt;/value&gt;</p>
<p align="left">&lt;/property&gt;</p>
<p align="left">&lt;property&gt;</p>
<p align="left">    &lt;name&gt;dfs.replication&lt;/name&gt;</p>
<p align="left">    &lt;value&gt;1&lt;/value&gt;</p>
<p align="left">&lt;/property&gt;</p>
<p align="left"><strong><span style="color:#FF0000;">&lt;property&gt;</span></strong></p>
<p align="left"><strong><span style="color:#FF0000;">&lt;name&gt;hbase.replication&lt;/name&gt;</span></strong></p>
<p align="left"><strong><span style="color:#FF0000;">&lt;value&gt;true&lt;/value&gt;</span></strong></p>
<p align="left"><strong><span style="color:#FF0000;">&lt;/property&gt;</span></strong></p>
<p align="left">&lt;/configuration&gt;</p>
<p align="left"><br><br></p>
<h1>4 使用</h1>
<h2>3.1 场景1:主节点同名表同步到各从节点</h2>
<p></p>
<p><img src="https://img-blog.csdn.net/20160515231630058?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p>1 主节点里创建student表,指定有score和course两个列族</p>
<p><strong><span style="color:#00B050;">create 'student', 'score', 'course'</span></strong></p>
<p><strong><span style="color:#00B050;">put 'student', 'xiapi001','score:english', '10'</span></strong></p>
<p><strong><span style="color:#00B050;">put 'student', 'xiapi002','score:chinese', '20'</span></strong></p>
<p><strong><span style="color:#00B050;">put 'student','xiapi002','course:chinese', '001'</span></strong></p>
<p><strong><span style="color:#00B050;">scan 'scores'</span></strong></p>
<p>2打开主节点里表student的复制特性</p>
<p><strong><span style="color:#00B050;">disable 'student'</span></strong></p>
<p><strong><span style="color:#00B050;">alter 'student', {NAME =&gt; 'score',REPLICATION_SCOPE =&gt; '1'},{NAME =&gt; 'course', REPLICATION_SCOPE =&gt; '1'}</span></strong></p>
<p><strong><span style="color:#00B050;">enable 'student'</span></strong></p>
<p><img src="https://img-blog.csdn.net/20160515231745902?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""></p>
<p align="left"> </p>
<p>3主节点里添加要复制的从节点的peer</p>
<p align="left"><strong><span style="color:#00B050;">add_peer '10','192.168.56.103:2181:/hbase'</span></strong></p>
<p align="left"><strong><span style="color:#00B050;">set_peer_tableCFs '10','student'</span></strong></p>
<p> <img src="https://img-blog.csdn.net/20160515231822043?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""></p>
<p></p>
<p>4从节点里新增跟主节点一样的表</p>
<p><strong><span style="color:#00B050;">create 'student', 'score', 'course'</span></strong></p>
<p> </p>
<p>5主节点里新增数据到从节点里观察数据是否同步</p>
<p>1)  主节点里新增数据</p>
<p></p>
<p> <img src="https://img-blog.csdn.net/20160515231839668?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""></p>
<p>2)  从节点里查看数据</p>
<p> <img src="https://img-blog.csdn.net/20160515231934294?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""></p>
<p align="left"></p>
<p align="left"> </p>
<p align="left"><strong><span style="color:#FF0000;">注</span></strong>:1 这里数据同步是从replication功能打开之后,之前的数据需要手工同步.</p>
<p align="left">2同步时可以指定列族，如仅同步student的score列族:</p>
<p align="left"><img src="https://img-blog.csdn.net/20160515232145904?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p align="left"></p>
<p align="left">从节点里:</p>
<p align="left"></p>
<p align="left"> <img src="https://img-blog.csdn.net/20160515232222267?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""></p>
<p>6同理添加peer,重复动作 34 5，即可实现场景1.</p>
<p align="left"><strong><span style="color:#00B050;">add_peer '11','192.168.56.104:2181:/hbase'</span></strong></p>
<p align="left"><strong><span style="color:#00B050;">set_peer_tableCFs '10','student'</span></strong></p>
<p align="left"> </p>
<h2>3.2场景2:主节点不同表同步到不同集群相应表</h2>
<p><img src="https://img-blog.csdn.net/20160515232303779?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""></p>
<p><br></p>
<p>实现方式类似3.1,通过set_peer_tableCFs设置</p>
<p align="left"><strong><span style="color:#00B050;">set_peer_tableCFs '4', "slave1:grade; scores:grade"</span></strong></p>
<p align="left"><strong><span style="color:#00B050;">set_peer_tableCFs '5', "slave2:grade; scores:grade"</span></strong></p>
<p align="left"><strong><span style="color:#00B050;">list_peers</span></strong></p>
<p align="left"><strong><span style="color:#00B050;"><img src="https://img-blog.csdn.net/20160515232350936?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></span></strong></p>
<p align="left"></p>
<h2>3.3场景3:各主节点表同步到同从集群相应表</h2>
<p></p>
<p><img src="https://img-blog.csdn.net/20160515232429020?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p>实现方式:</p>
<p> </p>
<p>主集群1里增加peer,主集群2里增加peer,同时通过set_peer_tableCFs设置需要同步的表即可.</p>
<p>#主hbase1 peers:</p>
<p></p>
<p><span style="color:#1F497D;"> <img src="https://img-blog.csdn.net/20160515232446514?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""></span></p>
<p>#主hbase2 peers :</p>
<p align="left"><strong><span style="color:#00B050;">add_peer '6','192.168.56.103:2181:/hbase'</span></strong></p>
<p align="left"><strong><span style="color:#00B050;">set_peer_tableCFs '5', " master2:grade"</span></strong></p>
<p></p>
<p> <img src="https://img-blog.csdn.net/20160515232542437?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""></p>
<h2>3.4复制命令详解</h2>
<p><strong>Group name: replication</strong></p>
<p> </p>
<table border="1" cellspacing="0" cellpadding="0" width="721"><tbody><tr><td valign="top">
<p><strong>命令</strong></p>
</td>
<td valign="top">
<p><strong>官网解释</strong></p>
</td>
<td valign="top">
<p><strong>中文意思</strong></p>
</td>
</tr><tr><td valign="top">
<p>add_peer</p>
</td>
<td valign="top">
<p>adds a replication relationship between two clusters</p>
</td>
<td valign="top">
<p>为两个集群添加复制管理</p>
</td>
</tr><tr><td valign="top">
<p>disable_peer</p>
</td>
<td valign="top">
<p>Disable a replication relationship</p>
</td>
<td valign="top">
<p>禁用复制关系</p>
</td>
</tr><tr><td valign="top">
<p>enable_peer</p>
</td>
<td valign="top">
<p>Enable a previously-disabled replication relationship</p>
</td>
<td valign="top">
<p>启用复制关系</p>
</td>
</tr><tr><td valign="top">
<p>list_peers</p>
</td>
<td valign="top">
<p>list all replication relationships known by this cluster</p>
</td>
<td valign="top">
<p>显示当前集群的复制关系</p>
</td>
</tr><tr><td valign="top">
<p>remove_peer</p>
</td>
<td valign="top">
<p>Disable and remove a replication relationship</p>
</td>
<td valign="top">
<p>禁用并删除复制关系</p>
</td>
</tr><tr><td valign="top">
<p>list_replicated_tables</p>
</td>
<td valign="top">
<p>List all the tables and column families replicated from this cluster</p>
</td>
<td valign="top">
<p>显示一个hbase集群下处于复制状态的表</p>
</td>
</tr><tr><td valign="top">
<p>set_peer_tableCFs</p>
</td>
<td valign="top">
<p>Set the replicable table-cf config for the specified peer</p>
</td>
<td valign="top">
<p>设置peer下的表、列族复制关系</p>
</td>
</tr><tr><td valign="top">
<p>show_peer_tableCFs</p>
</td>
<td valign="top">
<p>Show replicable table-cf config for the specified peer. </p>
</td>
<td valign="top">
<p>显示peer下表、列族复制关系</p>
</td>
</tr></tbody></table><p> </p>
<h1>5 问题总结</h1>
<p>1 验证是需要先核对下hbase的版本,不同版本的复制命令不同</p>
<p>2 同步是针对配置后复制的新数据，旧数据需要手动迁移</p>
            </div>
                </div>