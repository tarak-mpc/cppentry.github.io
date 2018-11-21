---
layout:     post
title:      HBase Create Table
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div class="blog_title" style="font-family:Helvetica, Tahoma, Arial, sans-serif;line-height:18px;">
<h3 style="font-size:16px;line-height:1.5em;">
<a href="http://bupt04406.iteye.com/blog/1767537" rel="nofollow" style="color:rgb(16,138,198);">HBase Create Table</a><span class="actions" style="font-style:italic;"></span></h3>
<strong>博客分类：</strong> 

<a href="http://bupt04406.iteye.com/category/224710" rel="nofollow" style="color:rgb(16,138,198);">hbase</a><div class="news_tag" style="clear:both;line-height:20px;"> </div>
</div>
<div id="blog_content" class="blog_content" style="font-size:14px;line-height:1.8em;font-family:Helvetica, Tahoma, Arial, sans-serif;">
<p>
 </p>
<p>
基于HBase trunk代码http://svn.apache.org/repos/asf/hbase/trunk，Revision: 1431176</p>
<p>
 </p>
<p>
 </p>
<p>
<span>Create table 的过程，Create table时 只有一个region 的assign 过程。 图示 </span></p>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><br></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><br></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span>Client                                                                    HMaster                                                                                                                        HRegionServer</span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<hr style="border-bottom-color:#000000;border-bottom-width:thin;"></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span>Client 发送请求到HMaster （HBaseAdmin）                        </span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<hr style="border-bottom-color:#000000;border-bottom-width:thin;"></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span>                                                                            HMaster收到请求</span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span>                                                                            创建<span style="font-family:Consolas;">CreateTableHandler，CreateTableHandler</span><span style="font-family:Consolas;">的构造函数</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span style="font-family:Consolas;"><span>                                      里面会 </span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span style="font-family:Consolas;"><span>                                      (1)调用</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                      MetaReader.tableExists(</span><span style="font-family:Consolas;">CatalogTracker catalogTracker,String tableName)来</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span style="font-family:Consolas;"><span>                                      扫描META表，检查table是否已存在</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                      (2)</span>在ZK中设置table的状态是<span style="font-family:Consolas;"><span style="background-color:rgb(232,242,254);">ZooKeeperProtos.Table.State.</span></span><span style="font-family:Consolas;color:rgb(0,0,192);"><span style="background-color:rgb(232,242,254);"><em>ENABLING</em></span></span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;color:rgb(0,0,192);"><span style="background-color:rgb(232,242,254);"><em>                                      /hbase/</em></span></span><span style="color:rgb(42,0,255);font-family:Consolas;">table/tablename</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="color:rgb(42,0,255);font-family:Consolas;">                                     </span> <span style="font-family:Consolas;">CreateTableHandler构造好了之后放入线程池（</span><span style="font-family:Consolas;">ExecutorService</span><span style="font-family:Consolas;">），</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span style="font-family:Consolas;"><span>                                      由线程池异步去执行</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span style="color:rgb(42,0,255);font-family:Consolas;"><span>                                      构造好CreateTableResponse，返回client </span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<hr style="border-bottom-color:#000000;border-bottom-width:thin;"><span>Client收到<span style="color:rgb(42,0,255);font-family:Consolas;">CreateTableResponse，</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span style="color:rgb(42,0,255);font-family:Consolas;"><span>然后就wait，知道table创建好</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<hr style="border-bottom-color:#000000;border-bottom-width:thin;"></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span>                                                                            HMaster开始执行<span style="font-family:Consolas;">CreateTableHandler，调用</span><span style="font-family:Consolas;">CreateTableHandler.</span><span style="font-family:Consolas;">process</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                      (1)</span><span style="font-family:Consolas;">在HDFS上面创建Table的目录，并写上HTableDescriptor(文件</span><span style="color:rgb(42,0,255);font-family:Consolas;">.tableinfo</span><span style="font-family:Consolas;">)</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span>                                                                            (2) 创建HRegion，<span style="font-family:Consolas;"><span style="background-color:rgb(232,242,254);">HRegion.</span><span style="background-color:rgb(232,242,254);"><em>createHRegion</em></span></span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span>                                                                            (3) Close HRegion，<span style="font-family:Consolas;">region.close();</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span>                                                                            (4) <span style="font-family:Consolas;">RegionInfo信息往META表里面put</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<div><span style="font-family:Consolas;"><span style="font-family:Tahoma;"><span style="font-family:Consolas;"><span style="background-color:rgb(232,242,254);">                                       MetaEditor.</span><span style="background-color:rgb(232,242,254);"><em>addRegionsToMeta</em></span><span style="background-color:rgb(232,242,254);">(</span><span style="color:rgb(127,0,85);"><span style="background-color:rgb(232,242,254);"><strong>this</strong></span></span><span style="background-color:rgb(232,242,254);">.</span><span style="color:rgb(0,0,192);"><span style="background-color:rgb(232,242,254);">catalogTracker</span></span><span style="background-color:rgb(232,242,254);">,
 regionInfos);</span></span></span></span></div>
</div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;"><span style="font-family:Tahoma;"><span style="font-family:Consolas;"><span style="background-color:rgb(232,242,254);">                                       //这里只有 </span></span></span></span><span style="color:rgb(42,0,255);font-family:Consolas;">info</span><span style="font-family:Consolas;"><span style="font-family:Tahoma;"><span style="font-family:Consolas;"><span style="background-color:rgb(232,242,254);">:</span></span></span></span><span style="color:rgb(42,0,255);font-family:Consolas;">regioninfo
 这个cq，</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="color:rgb(42,0,255);font-family:Consolas;">                                         没有</span><span style="color:rgb(42,0,255);font-family:Consolas;">info:</span><span style="color:rgb(42,0,255);font-family:Consolas;">server、</span><span style="color:rgb(42,0,255);font-family:Consolas;">info：</span><span style="color:rgb(42,0,255);font-family:Consolas;">serverstartcode等其他cq。</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                       这里region的location是null，在</span><span style="font-family:Consolas;">AssignmentManager的</span><span style="font-family:Consolas;">rebuildUserRegions</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span style="font-family:Consolas;"><span>                                        里面有对这块（region的location为null时）单独进行处理。</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span>                                                                             (5) <span style="font-family:Consolas;"><span style="font-family:Tahoma;"><span style="font-family:Consolas;"><span style="background-color:rgb(232,242,254);">在RegionStates里面为每个Region创建一个</span></span></span></span><span style="font-family:Consolas;">RegionState，状态为</span><span style="font-family:Consolas;"><span style="background-color:rgb(232,242,254);">State.</span></span><span style="font-family:Consolas;color:rgb(0,0,192);"><span style="background-color:rgb(232,242,254);"><em>OFFLINE</em></span></span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span>                                                                             (6) <span style="font-family:Consolas;">assign region,调用 </span><span style="font-family:Consolas;">AssignmentManager.</span><span style="font-family:Consolas;">assign(List&lt;HRegionInfo&gt;
 regions)</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<hr style="border-bottom-color:#000000;border-bottom-width:thin;"><span style="font-family:Consolas;">                                      assign region出去</span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                      (1) 调用</span>LoadBalancer进行Round Robin分配，给每个region分配一个Server，生成分配计划</span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<div><span>                                                                            (2)new 一个 GeneralBulkAssigner 按照生成的分配计划把region assign 出去</span></div>
<div>
<div><span><span style="font-family:Consolas;">                                        BulkAssigner ba =</span> <span style="font-family:Consolas;color:rgb(127,0,85);"><strong>new</strong></span> <span style="font-family:Consolas;">GeneralBulkAssigner(</span><span style="font-family:Consolas;color:rgb(127,0,85);"><strong>this</strong></span><span style="font-family:Consolas;">.</span><span style="font-family:Consolas;color:rgb(0,0,192);">server</span><span style="font-family:Consolas;">,
 bulkPlan,</span> <span style="font-family:Consolas;color:rgb(127,0,85);"><strong>this</strong></span><span style="font-family:Consolas;">);</span></span></div>
<div><span style="font-family:Consolas;">                                        ba.bulkAssign();</span></div>
</div>
<span style="font-family:Consolas;"><span>                                        GeneralBulkAssigner里面运行就</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<div>
<div>
<div><span><span style="background-color:rgb(232,242,254);"><span style="font-family:Consolas;">                                        BulkAssigner.bulkAssign</span></span><span style="font-family:Consolas;">()</span></span></div>
</div>
</div>
</div>
<p>
<span><span style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;"><span style="font-family:Consolas;"><span style="background-color:rgb(232,242,254);">                                            BulkAssigner.bulkAssign(</span></span></span><span style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;"><span style="font-family:Consolas;color:rgb(127,0,85);"><span style="background-color:rgb(232,242,254);"><strong>boolean</strong></span></span></span><span style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;"> </span><span style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;"><span style="font-family:Consolas;"><span style="background-color:rgb(232,242,254);">sync)</span></span></span></span></p>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<div>
<div>
<div><span><span style="background-color:rgb(232,242,254);"><span style="font-family:Consolas;">                                                 </span></span><span style="font-family:Consolas;">GeneralBulkAssigner.</span><span style="font-family:Consolas;">populatePool
   具体调用</span><span style="font-family:Consolas;">SingleServerBulkAssigner来完成</span></span></div>
</div>
</div>
</div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                                 </span><span style="font-family:Consolas;">GeneralBulkAssigner.</span><span style="font-family:Consolas;">waitUntilDone</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                        这个的分配计划是</span><span style="font-family:Consolas;">Map&lt;ServerName, List&lt;HRegionInfo&gt;&gt;，Server对应一些HRegion， </span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                        </span><span style="font-family:Consolas;">GeneralBulkAssigner执行时每一个Server对应一个</span><span style="font-family:Consolas;">SingleServerBulkAssigner，由线程池去执行</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                        </span><span style="font-family:Consolas;">SingleServerBulkAssigner，而自己只需要</span><span style="font-family:Consolas;">waitUntilDone</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                        这里</span><span style="font-family:Consolas;">GeneralBulkAssigner的</span><span style="font-family:Consolas;">waitUntilDone条件是：</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span style="font-family:Consolas;"><span>                                        所有需要分配的Region在RegionStates中的状态满足</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                        【1】在</span><span style="color:rgb(0,0,192);font-family:Consolas;">regionsInTransition没有这个region</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                        【2】在</span><span style="color:rgb(0,0,192);font-family:Consolas;">regionAssignments中有这个region</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<hr style="border-bottom-color:#000000;border-bottom-width:thin;font-family:Consolas;"></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                        </span><span style="font-family:Consolas;">SingleServerBulkAssigner的执行</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span style="font-family:Consolas;"><span><span style="background-color:rgb(232,242,254);">                                        SingleServerBulkAssigner.run</span></span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Tahoma;"><span style="font-family:Consolas;color:rgb(0,0,192);"><span style="background-color:rgb(232,242,254);">                                            assignmentManager</span></span></span><span style="font-family:Tahoma;"><span style="font-family:Consolas;"><span style="background-color:rgb(232,242,254);">.assign(</span></span></span><span style="font-family:Tahoma;"><span style="font-family:Consolas;color:rgb(0,0,192);"><span style="background-color:rgb(232,242,254);">regionserver</span></span></span><span style="font-family:Tahoma;"><span style="font-family:Consolas;"><span style="background-color:rgb(232,242,254);">,</span></span></span> <span style="font-family:Tahoma;"><span style="font-family:Consolas;color:rgb(0,0,192);"><span style="background-color:rgb(232,242,254);">regions</span></span></span><span style="font-family:Tahoma;"><span style="font-family:Consolas;"><span style="background-color:rgb(232,242,254);">)</span></span></span><span style="font-family:Consolas;"> 
                                       </span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<hr style="border-bottom-color:#000000;border-bottom-width:thin;"><span><span style="font-family:Consolas;">                                        A</span><span style="font-family:Tahoma;"><span style="font-family:Consolas;color:rgb(0,0,192);"><span style="background-color:rgb(232,242,254);">ssignmentManager</span></span></span><span style="font-family:Tahoma;"><span style="font-family:Consolas;"><span style="background-color:rgb(232,242,254);">.</span></span></span><span style="font-family:Consolas;">assign(</span><span style="font-family:Consolas;color:rgb(127,0,85);"><strong>final</strong></span> <span style="font-family:Consolas;">ServerName
 destination,</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                              </span><span style="font-family:Consolas;color:rgb(127,0,85);"><strong>final</strong></span> <span style="font-family:Consolas;">List&lt;HRegionInfo&gt;
 regions)</span><span style="font-family:Tahoma;"><span style="font-family:Consolas;"><span style="background-color:rgb(232,242,254);">的执行</span></span></span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                        (1)</span><span style="font-family:Consolas;">asyncSetOfflineInZooKeeper</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                          (1.1)</span><span style="color:rgb(0,0,192);font-family:Consolas;">regionStates中更新Region的状态，从</span><span style="font-family:Consolas;">OFFLINE-&gt;</span><span style="font-family:Consolas;">OFFLINE </span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                          (1.2)ZK中/hbase/</span><span style="font-family:Consolas;">unassigned下面异步创建Region对应的节点</span><span style="font-family:Consolas;">,</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                          数据</span><span style="font-family:Consolas;">RegionTransition</span><span style="font-family:Consolas;">是EventType.</span><span style="font-family:Consolas;"><span style="color:rgb(0,0,192);"><em>M_ZK_REGION_OFFLINE</em></span></span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;"><span style="color:rgb(0,0,192);"><em>                                          其中有注册回调函数</em></span></span><span style="font-family:Consolas;">OfflineCallback</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span style="font-family:Consolas;"><span>                                                (1.3)每个Region创建一个RegionPlan</span></span></div>
<hr style="border-bottom-color:#000000;border-bottom-width:thin;color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;"><p>
<span><span style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">                                                                                 HMaster收到</span><span style="font-family:Consolas;">NodeChildrenChanged事件，path是</span><span style="font-family:Consolas;">/hbase/unassigned</span></span></p>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                         触发回调函数</span><span style="font-family:Consolas;">OfflineCallback以及OfflineCallback.ExistCallback</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span style="font-family:Consolas;"><span>                                               这样HMaster就能往下执行了</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                               </span><span style="color:rgb(63,127,95);font-family:Consolas;">// Wait until all unassigned nodes have been put up and watchers set.</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                         把RegionPlan put到</span><span style="color:rgb(0,0,192);font-family:Consolas;">regionPlans中</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                         更新Region的状态，从</span><span style="font-family:Consolas;">OFFLINE-&gt;</span><span style="font-family:Consolas;">PENDING_OPEN，S</span><span style="font-family:Consolas;">erverName不为null，同时会放入</span><span style="color:rgb(0,0,192);font-family:Consolas;">regionsInTransition</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<hr style="border-bottom-color:#000000;border-bottom-width:thin;font-family:Consolas;"></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span style="font-family:Consolas;"><span>                                         HMaster向HRegionServer发送rpc请求（open regions）</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<hr style="border-bottom-color:#000000;border-bottom-width:thin;"></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                                                                                        HRegionServer收到</span><span style="font-family:Consolas;">open region请求</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                                                                                        创建</span><span style="font-family:Consolas;">OpenRegionHandler，丢到线程池（</span><span style="font-family:Consolas;">ExecutorService</span><span style="font-family:Consolas;">），</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span style="font-family:Consolas;"><span>                                                                                                        由线程池运行</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                                                                                        返回HMaster Response，</span><span style="font-family:Consolas;"><span style="background-color:rgb(232,242,254);">RegionOpeningState.</span></span><span style="font-family:Consolas;color:rgb(0,0,192);"><span style="background-color:rgb(232,242,254);"><em>OPENED</em></span></span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<hr style="border-bottom-color:#000000;border-bottom-width:thin;font-family:Consolas;"></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span style="font-family:Consolas;"><span>                                         HMaster收到HRegionServer端的Response</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                         </span>AssignmentManager.assign(final ServerName destination,</span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span>                                                                                      final List&lt;HRegionInfo&gt; regions)<span style="font-family:Consolas;"> 结束</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                         </span><span style="font-family:Consolas;">SingleServerBulkAssigner.run结束</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                         但是</span><span style="font-family:Consolas;">GeneralBulkAssigner还没有结束，还在运行</span><span style="font-family:Consolas;">waitUntilDone</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<hr style="border-bottom-color:#000000;border-bottom-width:thin;"></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                                                                                        HRegionServer运行</span><span style="font-family:Consolas;">OpenRegionHandler</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span style="font-family:Consolas;"><span>                                                                                                        (1)第一步修改zk上面region节点的信息，</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                                                                                        M_ZK_REGION_OFFLINE-&gt;</span><span style="font-family:Consolas;">RS_ZK_REGION_OPENING</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                                                                                        </span><span style="font-family:Consolas;"><span style="background-color:rgb(232,242,254);">transitionZookeeperOfflineToOpening(encodedName,</span> 
  </span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span style="font-family:Consolas;"><span>                                                                                                               <span style="color:rgb(0,0,192);"><span style="background-color:rgb(232,242,254);">versionOfOfflineNode</span></span><span style="background-color:rgb(232,242,254);">)</span></span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<hr style="border-bottom-color:#000000;border-bottom-width:thin;"></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                         因为上面HRegionServer修改了ZK节点的信息，所以HMaster会收到       HRegionServer open region，</span><span style="font-family:Consolas;">HRegion region = openRegion();</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                         </span><span style="font-family:Consolas;">ZK的事件NodeDataChanged，path是                                 </span><span style="font-family:Consolas;">HRegion
 r =</span><span style="font-family:Consolas;"> </span><span style="font-family:Consolas;">HRegion.<em>newHRegion</em></span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                         </span><span style="font-family:Consolas;">/hbase/unassigned/98ab07ecd6bbfa1e3a170092f3fc4de6</span><span style="font-family:Consolas;">             </span><span style="font-family:Consolas;">r.openHRegion(reporter);</span><span style="font-family:Consolas;"> 
                                                                        </span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                         HMaster处理zk事件                                               HRegion.</span><span style="font-family:Consolas;">initialize</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                         更新Region的状态，</span><span style="font-family:Consolas;">PENDING_OPEN-&gt;</span><span style="font-family:Consolas;">OPENING,ServerName不为null       </span><span style="font-family:Consolas;">HRegion.</span><span style="font-family:Consolas;">initializeRegionInternals</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                                                                                         </span><span style="font-family:Consolas;">创建 .regioninfo 文件</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                                                                                         </span><span style="font-family:Consolas;">初始化HStore</span><span style="font-family:Consolas;"> 
       </span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                                                                                        </span><span style="font-family:Consolas;">HRegion open完成</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                                                                                        </span><span style="font-family:Consolas;">OpenRegionHandler 接着处理HRegion open完后的逻辑</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                                                                                        </span><span style="font-family:Consolas;">tickleOpening(<span style="color:rgb(42,0,255);">"post_region_open"</span>)</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                                                                                        </span><span style="font-family:Consolas;">retransitionNodeOpening</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                                                                                        </span><span style="color:rgb(0,0,192);font-family:Consolas;"><em>RS_ZK_REGION_OPENING-&gt;</em></span><span style="color:rgb(0,0,192);font-family:Consolas;"><em>RS_ZK_REGION_OPENING</em></span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<hr style="border-bottom-color:#000000;border-bottom-width:thin;"></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="color:rgb(0,0,192);font-family:Consolas;"><em>                                       HMaster收到ZK事件，</em></span><span style="font-family:Consolas;">NodeDataChanged，path是                        执行</span><span style="font-family:Consolas;">OpenRegionHandler里面的</span><span style="font-family:Consolas;">updateMeta</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                       </span><span style="font-family:Consolas;">/hbase/unassigned/98ab07ecd6bbfa1e3a170092f3fc4de6               启动线程</span><span style="font-family:Consolas;">PostOpenDeployTasksThread，并start运行</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="color:rgb(0,0,192);font-family:Consolas;"><em>                                       </em></span><span style="font-family:Consolas;">HMaster处理zk事件</span><span style="color:rgb(0,0,192);font-family:Consolas;"><em>  
                                              </em></span> <span style="font-family:Consolas;">PostOpenDeployTasksThread运行</span><span style="font-family:Consolas;">HRegionServer.</span><span style="font-family:Consolas;">postOpenDeployTasks</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                       </span><span style="font-family:Consolas;">更新Region的状态，</span><span style="font-family:Consolas;">OPENING</span><span style="font-family:Consolas;">-&gt;</span><span style="font-family:Consolas;">OPENING,ServerName不为null
            </span> <span style="font-family:Consolas;"><strong>HRegionServer:  更新META表，修改Region的Location信息</strong></span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span style="font-family:Consolas;"><strong><span>                                                                                                        （Location原先是没有的）。</span></strong></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                                                                                        </span><span style="font-family:Consolas;">OpenRegionHandler 接着处理，修改zk中region的状态为</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span style="font-family:Consolas;"><span>                                                                                                          RS_ZK_REGION_OPENED</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<hr style="border-bottom-color:#000000;border-bottom-width:thin;"></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                       HMaster收到ZK事件，</span><span style="font-family:Consolas;">NodeDataChanged</span><span style="font-family:Consolas;">，path是   </span><span style="font-family:Consolas;"> 
                    添加Region到</span><span style="color:rgb(63,127,95);font-family:Consolas;">OnlineRegions列表</span><span style="font-family:Consolas;">                                           </span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                       </span><span style="font-family:Consolas;">/hbase/unassigned/98ab07ecd6bbfa1e3a170092f3fc4de6               </span><span style="font-family:Consolas;">OpenRegionHandler处理完成</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                       事件是</span><span style="font-family:Consolas;">RS_ZK_REGION_OPENED</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                       更新Region的状态，</span><span style="font-family:Consolas;">OPENING-&gt;</span><span style="font-family:Consolas;">OPEN,ServerName不为null</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                       同时更新</span><span style="color:rgb(0,0,192);font-family:Consolas;">regionsInTransition中Region的状态为</span><span style="font-family:Consolas;">OPEN</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                       HMaster创建</span><span style="font-family:Consolas;">OpenedRegionHandler，并且丢到线程池去运行</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<hr style="border-bottom-color:#000000;border-bottom-width:thin;"></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                       HMaster运行</span><span style="font-family:Consolas;">OpenedRegionHandler</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                       </span><span style="font-family:Consolas;">OpenedRegionHandler主要是删除Region的ZK节点</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                       </span><span style="font-family:Consolas;">/hbase/unassigned/98ab07ecd6bbfa1e3a170092f3fc4de6 </span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<hr style="border-bottom-color:#000000;border-bottom-width:thin;"></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                       </span><span style="font-family:Consolas;">HMaster收到ZK事件，</span><span style="font-family:Consolas;">NodeDeleted</span><span style="font-family:Consolas;">，path是 </span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                       </span><span style="font-family:Consolas;">/hbase/unassigned/98ab07ecd6bbfa1e3a170092f3fc4de6</span><span style="font-family:Consolas;"> </span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                       </span><span style="font-family:Consolas;">HMaster收到ZK事件，</span><span style="font-family:Consolas;">NodeChildrenChanged</span><span style="font-family:Consolas;">，path是</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span style="font-family:Consolas;"><span>                                       /hbase/unassigned</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                       </span><span style="font-family:Consolas;">HMaster处理nodeDeleted事件</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                       运行</span><span style="font-family:Consolas;">AssignmentManager.</span><span style="font-family:Consolas;">nodeDeleted</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                       从</span><span style="color:rgb(0,0,192);font-family:Consolas;">regionsInTransition中获得Region的state状态</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                       </span><span style="font-family:Consolas;">AssignmentManager.</span><span style="font-family:Consolas;">regionOnline</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                        (1) </span><span style="font-family:Consolas;">RegionStates.</span><span style="font-family:Consolas;">regionOnline，</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                            更新Region的状态，从</span>OPEN-&gt;OPEN</span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                            从</span><span style="color:rgb(0,0,192);font-family:Consolas;">regionsInTransition中删除region的state信息</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="color:rgb(0,0,192);font-family:Consolas;">                                            往</span><span style="color:rgb(0,0,192);font-family:Consolas;">regionAssignments中put数据</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                        (2)</span><span style="font-family:Consolas;">AssignmentManager.</span><span style="font-family:Consolas;">clearRegionPlan</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                            从</span><span style="color:rgb(0,0,192);font-family:Consolas;">regionPlans中删除region的RegionPlan</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                        (3)</span><span style="font-family:Consolas;">AssignmentManager.</span><span style="font-family:Consolas;">addToServersInUpdatingTimer</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                            添加ServerName到</span><span style="color:rgb(0,0,192);font-family:Consolas;">serversInUpdatingTimer中</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span style="color:rgb(0,0,192);font-family:Consolas;"><span>                                      </span></span></div>
<hr style="border-bottom-color:#000000;border-bottom-width:thin;color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;"><div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="color:rgb(0,0,192);font-family:Consolas;">                                       </span><span style="font-family:Consolas;">GeneralBulkAssigner的</span><span style="font-family:Consolas;">waitUntilDone可以结束了,返回到</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                       </span><span style="font-family:Consolas;">AssignmentManager.</span><span style="font-family:Consolas;">assign(List&lt;HRegionInfo&gt; regions)</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                       </span><span style="font-family:Consolas;">AssignmentManager.</span><span style="font-family:Consolas;">assign结束,返回到</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<span><span style="font-family:Consolas;">                                       CreateTableHandler.</span><span style="font-family:Consolas;">handleCreateTable</span></span></div>
<div style="color:rgb(38,38,38);font-family:Arial, Helvetica, sans-serif;line-height:22.52252197265625px;">
<hr style="border-bottom-color:#000000;border-bottom-width:thin;font-family:Consolas;"></div>
<div style="color:rgb(38,38,38);font-family:Consolas;"><span>                                       <span style="font-family:Tahoma;"><span style="font-family:Consolas;">CreateTableHandler.</span></span><span style="font-family:Tahoma;"><span style="font-family:Consolas;">handleCreateTable</span></span></span></div>
<div style="color:rgb(38,38,38);font-family:Consolas;"><span>                                       在ZK中设置table的状态是<span style="background-color:rgb(232,242,254);">ZooKeeperProtos.Table.State.</span><span style="color:rgb(0,0,192);"><em>ENABLED</em></span></span></div>
<div style="color:rgb(38,38,38);font-family:Consolas;">
<div><span style="font-family:Tahoma;"><span style="font-family:Consolas;"><span style="color:rgb(0,0,192);"><span style="background-color:rgb(232,242,254);"><em>                                       /hbase/</em></span></span><span style="color:rgb(42,0,255);">table/tablename</span></span></span></div>
<div><span><span style="font-family:Tahoma;"><span style="font-family:Consolas;"><span style="color:rgb(42,0,255);">                                       </span></span></span>CreateTableHandler结束</span></div>
<div>
<hr style="border-bottom-color:#000000;border-bottom-width:thin;"><span>(1)Client扫描META表发现有这个Table的regions，</span></div>
<div><span style="font-family:Tahoma;"><span style="font-family:Consolas;"><span style="color:rgb(42,0,255);">并且这些regions都已经在某个regionserver</span></span></span></div>
<div><span><span style="font-family:Tahoma;"><span style="font-family:Consolas;"><span style="color:rgb(42,0,255);">上面了，那么设置</span></span></span>doneWithMetaScan=true</span></div>
<div><span><span style="background-color:rgb(232,242,254);">MetaScanner.</span><span style="background-color:rgb(232,242,254);"><em>metaScan</em></span><span style="background-color:rgb(232,242,254);">(</span><span style="color:rgb(0,0,192);"><span style="background-color:rgb(232,242,254);">conf</span></span><span style="background-color:rgb(232,242,254);">,
 visitor, desc.getName());</span></span></div>
<div><span style="color:rgb(42,0,255);">(2)发现这个Table已经是enabled状态了，那么就return</span></div>
<div><span>isTableEnabled(desc.getName())</span></div>
</div>
</div>
            </div>
                </div>