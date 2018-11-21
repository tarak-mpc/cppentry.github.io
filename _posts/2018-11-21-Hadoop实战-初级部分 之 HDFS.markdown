---
layout:     post
title:      Hadoop实战-初级部分 之 HDFS
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p></p>
<div class="div_content_text_all" style="border:0px;overflow:hidden;background-color:rgb(250,250,250);line-height:1.5em;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;">第一部分：什么是HDFS</span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;">       Hadoop Distributed File System，简称HDFS，是一个分布式文件系统。 HDFS是高容错性的，可以部署在低成本的硬件之上，HDFS提供高吞吐量地对应用程序数据访问，它适合大数据集的应用程序。</span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;"> </span></div>
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;">第二部分：HDFS架构设计</span></div>
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;"><img src="http://sishuok.com/forum/upload/2012/8/9/ce952b0311e3faddda44f39aa7a303ca__1.JPG" alt="" style="border-width:0px;border-style:none;"></span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span style="font-family:'Comic Sans MS';font-size:18px;">HDFS 设计目标</span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
<span style="font-family:'Comic Sans MS';font-size:18px;"><img src="http://sishuok.com/forum/upload/2012/8/9/172e0f6a00a09a976341008097e4658a__2.JPG" alt="" style="border-width:0px;border-style:none;"></span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
<span style="font-family:'Comic Sans MS';font-size:18px;">HDFS不适合做什么</span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;">存储小文件</span></div>
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;">大量随机读</span></div>
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;">需要对文件修改</span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;">第三部分：HDFS概念</span></div>
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;"> </span>
<table dir="ltr" cellspacing="0" cellpadding="0" style="border:none;width:446px;"><tbody><tr><td bgcolor="#00CC99" width="136" height="23">
<div style="border-width:0px;overflow:hidden;"><span><span style="font-family:'Comic Sans MS';font-size:18px;">NameNode</span></span></div>
</td>
<td bgcolor="#00CC99" width="136" height="23">
<div style="border-width:0px;overflow:hidden;"><span><span style="font-family:'Comic Sans MS';font-size:18px;">DataNode</span></span></div>
</td>
<td bgcolor="#00CC99" width="176" height="23">
<div style="border-width:0px;overflow:hidden;"><span><span style="font-family:'Comic Sans MS';font-size:18px;">SecondaryNameNode</span></span></div>
</td>
</tr><tr><td bgcolor="#CBECDE" width="136" height="73">
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;"> </span></div>
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;">存储元数据</span></div>
</td>
<td bgcolor="#CBECDE" width="136" height="73">
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;"> </span></div>
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;">    存储文件内容</span></div>
</td>
<td bgcolor="#CBECDE" width="176" height="73">
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;"> </span></div>
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;">将NameNode的fsimage与edit log从NameNode</span></div>
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;">     复制到临时目录</span></div>
</td>
</tr><tr><td bgcolor="#E7F6EF" width="136" height="40">
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;">元数据保存在内存中与磁盘上</span></div>
</td>
<td bgcolor="#E7F6EF" width="136" height="40">
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;">文件内容保存在磁盘</span></div>
</td>
<td bgcolor="#E7F6EF" width="176" height="40">
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;">将fsimage同edit log合并 并产生新的fsimage</span></div>
</td>
</tr><tr><td bgcolor="#CBECDE" width="136" height="73">
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;">保存文件，block dataNode之间的映射关系</span></div>
</td>
<td bgcolor="#CBECDE" width="136" height="73">
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;">维护block id 到datanode本地文件的映射关系</span></div>
</td>
<td bgcolor="#CBECDE" width="176" height="73">
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;">将产生的新的fsimage上传给NameNode    </span></div>
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;">     清除NameNode中的edit log</span></div>
</td>
</tr></tbody></table></div>
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;"> </span></div>
</div>
<span style="font-family:'Comic Sans MS';font-size:18px;"> </span>
<div class="O" style="border-width:0px;overflow:hidden;">
<span><span style="font-family:'Comic Sans MS';font-size:18px;">Block块</span></span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;"><span>   </span> <span>数据块</span> <span lang="en-us" xml:lang="en-us">(block</span> <span lang="en-us" xml:lang="en-us">)
 HDFS(Hadoop Distributed File System)</span> <span>默认的最基本存储单</span> <span>位默认大小是</span> <span lang="en-us" xml:lang="en-us">64M </span><span>。</span></span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;">第四部分：HDFS命令行接口</span></div>
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;"> </span>
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;"><span lang="en-us" xml:lang="en-us">  </span> <span lang="en-us" xml:lang="en-us">hadoop fs :</span> <span lang="en-us" xml:lang="en-us">      </span></span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us"><span style="font-family:'Comic Sans MS';font-size:18px;">          [-ls &lt;path&gt;]</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;"><span lang="en-us" xml:lang="en-us">          </span><span lang="en-us" xml:lang="en-us">[-lsr
 &lt;path&gt;]</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us"><span style="font-family:'Comic Sans MS';font-size:18px;">           [-du &lt;path&gt;]</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us"><span style="font-family:'Comic Sans MS';font-size:18px;">           [-dus &lt;path&gt;]</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us"><span style="font-family:'Comic Sans MS';font-size:18px;">           [-count[-q] &lt;path&gt;]</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us"><span style="font-family:'Comic Sans MS';font-size:18px;">           [-mv &lt;src&gt; &lt;dst&gt;]</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us"><span style="font-family:'Comic Sans MS';font-size:18px;">           [-cp &lt;src&gt; &lt;dst&gt;]</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us"><span style="font-family:'Comic Sans MS';font-size:18px;">           [-rm [-skipTrash] &lt;path&gt;]</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us"><span style="font-family:'Comic Sans MS';font-size:18px;">           [-rmr [-skipTrash] &lt;path&gt;]</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us"><span style="font-family:'Comic Sans MS';font-size:18px;">           [-put &lt;localsrc&gt; ... &lt;dst&gt;]</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us"><span style="font-family:'Comic Sans MS';font-size:18px;">           [-copyFromLocal &lt;localsrc&gt; ... &lt;dst&gt;]</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us"><span style="font-family:'Comic Sans MS';font-size:18px;">           [-moveFromLocal &lt;localsrc&gt; ... &lt;dst&gt;]</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;"> </span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us"><span style="font-family:'Comic Sans MS';font-size:18px;">           [-getmerge &lt;src&gt; &lt;localdst&gt; [addnl]]</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us"><span style="font-family:'Comic Sans MS';font-size:18px;">           [-cat &lt;src&gt;]</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us"><span style="font-family:'Comic Sans MS';font-size:18px;">           [-text &lt;src&gt;]</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us"><span style="font-family:'Comic Sans MS';font-size:18px;">           [-copyToLocal [-ignoreCrc] [-crc] &lt;src&gt; &lt;localdst&gt;]</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us"><span style="font-family:'Comic Sans MS';font-size:18px;">           [-moveToLocal [-crc] &lt;src&gt; &lt;localdst&gt;]</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us"><span style="font-family:'Comic Sans MS';font-size:18px;">           [-mkdir &lt;path&gt;]</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us"><span style="font-family:'Comic Sans MS';font-size:18px;">           [-tail [-f] &lt;file&gt;]</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us"><span style="font-family:'Comic Sans MS';font-size:18px;">           [-chmod [-R] &lt;MODE[,MODE]... | OCTALMODE&gt; PATH...]</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us"><span style="font-family:'Comic Sans MS';font-size:18px;">           [-chown [-R] [OWNER][:[GROUP]] PATH...]</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us"><span style="font-family:'Comic Sans MS';font-size:18px;">           [-chgrp [-R] GROUP PATH...]</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us"><span style="font-family:'Comic Sans MS';font-size:18px;">           [-help [cmd]]</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;"> </span></div>
</div>
<span style="font-family:'Comic Sans MS';font-size:18px;"> </span>
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us"><span style="font-family:'Comic Sans MS';font-size:18px;">hadoop dfsadmin :</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us"><span style="font-family:'Comic Sans MS';font-size:18px;">           [-report]</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;"><span lang="en-us" xml:lang="en-us">             </span><span>报告文件系统的基本信息</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us"><span style="font-family:'Comic Sans MS';font-size:18px;">           [-safemode enter | leave | get | wait]</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;"><span lang="en-us" xml:lang="en-us">           </span><span>安全模式维护命令</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us"><span style="font-family:'Comic Sans MS';font-size:18px;">           [-saveNamespace]</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;"><span lang="en-us" xml:lang="en-us">             </span><span>保存当前的命名空间</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us"><span style="font-family:'Comic Sans MS';font-size:18px;">           [-refreshNodes]</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;"><span lang="en-us" xml:lang="en-us">            </span><span> </span> <span>重新读取</span> <span lang="en-us" xml:lang="en-us">Hosts</span> <span>和</span> <span lang="en-us" xml:lang="en-us">eclude</span> <span>文件，使新的节点或需要退出集群的节点能够重新被</span> <span lang="en-us" xml:lang="en-us">NameNode</span> <span>识别。</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us"><span style="font-family:'Comic Sans MS';font-size:18px;">           [-finalizeUpgrade]</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;"><span lang="en-us" xml:lang="en-us">          </span><span> </span> <span>终结</span> <span lang="en-us" xml:lang="en-us">HDFS</span> <span>的升级操作</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us"><span style="font-family:'Comic Sans MS';font-size:18px;">           [-upgradeProgress status | details | force]</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;"> </span>
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us"><span style="font-family:'Comic Sans MS';font-size:18px;">          [-metasave filename]</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;"><span lang="en-us" xml:lang="en-us">           </span><span>保存</span> <span lang="en-us" xml:lang="en-us">Namenode</span> <span>的主要数据结构到</span> <span lang="en-us" xml:lang="en-us">Hadoop.log.dir </span><span>属性指定目录下的</span> <span lang="en-us" xml:lang="en-us">filename</span> <span>上</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us"><span style="font-family:'Comic Sans MS';font-size:18px;">           [-setQuota &lt;quota&gt; &lt;dirname&gt;...&lt;dirname&gt;]</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;"><span lang="en-us" xml:lang="en-us">           </span><span>为每个目录设定配额，强制限定目录树下的名字个数。</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us"><span style="font-family:'Comic Sans MS';font-size:18px;">           [-clrQuota &lt;dirname&gt;...&lt;dirname&gt;]</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;"><span>          </span><span>为每个目录清除配额设定。</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us"><span style="font-family:'Comic Sans MS';font-size:18px;">           [-setBalancerBandwidth &lt;bandwidth in bytes per second&gt;]</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;"><span lang="en-us" xml:lang="en-us">           </span><span>设定负载均衡时使用的带宽</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;"> </span>
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;"> </span></div>
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;">第五部分：Hadoop文件系统</span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;"><span>目前</span> <span lang="en-us" xml:lang="en-us">Hadoop</span> <span>支持的文件系统：</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us"><span style="font-family:'Comic Sans MS';font-size:18px;"> </span></span>
<table dir="ltr" cellspacing="0" cellpadding="0" style="border:none;width:417px;"><tbody><tr><td bgcolor="#E7E7E7" width="121" height="24">
<div style="border-width:0px;overflow:hidden;"><span><span><span style="font-family:'Comic Sans MS';font-size:18px;">文件系统</span></span></span></div>
</td>
<td bgcolor="#E7E7E7" width="118" height="24">
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;"><span lang="en-us" xml:lang="en-us"><span>Java</span></span> <span><span>实现</span></span></span></div>
</td>
<td bgcolor="#E7E7E7" width="180" height="24">
<div style="border-width:0px;overflow:hidden;"><span><span><span style="font-family:'Comic Sans MS';font-size:18px;">描述</span></span></span></div>
</td>
</tr><tr><td bgcolor="#CBCBCB" width="121" height="65">
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us"><span style="font-family:'Comic Sans MS';font-size:18px;">KFS</span></span></div>
</td>
<td bgcolor="#CBCBCB" width="118" height="65">
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;"><span lang="en-us" xml:lang="en-us">fs.kfs.KosmosFil</span> <span lang="en-us" xml:lang="en-us">eSystem</span></span></div>
</td>
<td bgcolor="#CBCBCB" width="180" height="65">
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;"><span lang="en-us" xml:lang="en-us">Cloudstore  </span><span>是类似</span> <span lang="en-us" xml:lang="en-us">HDFS</span> <span>的</span> <span>由</span> <span lang="en-us" xml:lang="en-us">C++</span> <span>编写的文件系统</span></span></div>
</td>
</tr><tr><td bgcolor="#E7E7E7" width="121" height="57">
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;"><span lang="en-us" xml:lang="en-us">S3</span> <span>（本地）</span></span></div>
</td>
<td bgcolor="#E7E7E7" width="118" height="57">
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;"><span lang="en-us" xml:lang="en-us">fs.s3native.Nativ</span> <span lang="en-us" xml:lang="en-us">eS3File</span> <span lang="en-us" xml:lang="en-us">System</span></span></div>
</td>
<td bgcolor="#E7E7E7" width="180" height="57">
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;"><span>由</span> <span lang="en-us" xml:lang="en-us">Amazon S3</span> <span>支持的文件</span> <span>系统</span></span></div>
</td>
</tr><tr><td bgcolor="#CBCBCB" width="121" height="66">
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;"><span lang="en-us" xml:lang="en-us">S3</span> <span>（基于块）</span></span></div>
</td>
<td bgcolor="#CBCBCB" width="118" height="66">
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;"><span lang="en-us" xml:lang="en-us">fs.s3.S3FileSyst</span> <span lang="en-us" xml:lang="en-us">em</span></span></div>
</td>
<td bgcolor="#CBCBCB" width="180" height="66">
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;"><span>由</span> <span lang="en-us" xml:lang="en-us">AmazonS3</span> <span>支持的文件系</span> <span>统，以块格式来存储文件</span></span></div>
</td>
</tr><tr><td bgcolor="#E7E7E7" width="121" height="58">
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us"><span style="font-family:'Comic Sans MS';font-size:18px;">HAR</span></span></div>
</td>
<td bgcolor="#E7E7E7" width="118" height="58">
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us"><span style="font-family:'Comic Sans MS';font-size:18px;">fs.HarFileSystem</span></span></div>
</td>
<td bgcolor="#E7E7E7" width="180" height="58">
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;"><span>一个构建在其他文件系统来</span> <span>存档的文件系统。</span></span></div>
</td>
</tr></tbody></table></div>
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;"> </span></div>
</div>
</div>
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;">  <span>转载请注明出处【 <a href="http://sishuok.com/forum/blogPost/list/5458.html" rel="nofollow" style="color:rgb(0,94,167);text-decoration:none;">http://sishuok.com/forum/blogPost/list/5458.html</a></span>  <span>】</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="font-family:'Comic Sans MS';font-size:18px;"> </span></div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
<div style="border-width:0px;overflow:hidden;width:700px;">
<span style="font-family:'Comic Sans MS';font-size:18px;">资源附件</span></div>
<hr style="border-style:solid;border-color:rgb(223,223,223);"><div style="border:1px solid rgb(170,187,204);overflow:hidden;width:700px;">
<a href="http://sishuok.com/forum/download?filename=2012/8/9/abc683e14dae4946b9c7039e03dbef3e__Hadoop%20HDFS.pdf" rel="nofollow" style="color:rgb(0,94,167);text-decoration:none;"><span style="font-family:'Comic Sans MS';font-size:18px;">Hadoop
 HDFS.pdf</span></a></div>
<span style="font-family:'Comic Sans MS';font-size:18px;"><br></span>
<p></p>
<p><span style="font-family:'Comic Sans MS';font-size:18px;"><br></span></p>
<p><span style="font-family:'Comic Sans MS';font-size:18px;"><br></span></p>
<p><span style="font-family:'Comic Sans MS';font-size:18px;"><br></span></p>
<p><span style="font-family:'Comic Sans MS';font-size:18px;">原文地址：<a href="http://sishuok.com/forum/blogPost/list/40/5458.html#34331" rel="nofollow">点击打开链接</a></span></p>
            </div>
                </div>