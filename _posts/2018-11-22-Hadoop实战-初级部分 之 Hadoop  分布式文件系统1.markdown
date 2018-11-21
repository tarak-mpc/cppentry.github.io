---
layout:     post
title:      Hadoop实战-初级部分 之 Hadoop  分布式文件系统1
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div>  </div>
<div class="O">
<div class="O">
<div>第一部分：什么是HDFS </div>
<div>
<div class="O">
<div>       Hadoop Distributed File System，简称HDFS，是一个分布式文件系统。 HDFS是高容错性的，可以部署在低成本的硬件之上，HDFS提供高吞吐量地对应用程序数据访问，它适合大数据集的应用程序。</div>
<div>
<div class="O">
<div>  </div>
<div>第二部分：HDFS架构设计 </div>
<div><img alt="" src="http://sishuok.com/forum/upload/2012/8/9/ce952b0311e3faddda44f39aa7a303ca__1.JPG"></div>
<div>
<div class="O">HDFS 设计目标 </div>
<div class="O"><img alt="" src="http://sishuok.com/forum/upload/2012/8/9/172e0f6a00a09a976341008097e4658a__2.JPG"></div>
<div class="O">HDFS不适合做什么 </div>
<div class="O">
<div class="O">
<div>存储小文件 </div>
<div>大量随机读 </div>
<div>需要对文件修改 </div>
<div>
<div class="O">
<div>第三部分：HDFS概念 </div>
<div> 
<table dir="ltr" cellspacing="0" cellpadding="0" style="width:446px;"><tbody><tr><td bgcolor="#00cc99" height="23" width="136">
<div><strong>NameNode</strong> </div>
</td>
<td bgcolor="#00cc99" height="23" width="136">
<div><strong>DataNode</strong> </div>
</td>
<td bgcolor="#00cc99" height="23" width="176">
<div><strong>SecondaryNameNode</strong> </div>
</td>
</tr><tr><td bgcolor="#cbecde" height="73" width="136">
<div>  </div>
<div>存储元数据 </div>
</td>
<td bgcolor="#cbecde" height="73" width="136">
<div>  </div>
<div>    存储文件内容 </div>
</td>
<td bgcolor="#cbecde" height="73" width="176">
<div>  </div>
<div>将NameNode的fsimage与edit log从NameNode </div>
<div>     复制到临时目录 </div>
</td>
</tr><tr><td bgcolor="#e7f6ef" height="40" width="136">
<div>元数据保存在内存中与磁盘上 </div>
</td>
<td bgcolor="#e7f6ef" height="40" width="136">
<div>文件内容保存在磁盘 </div>
</td>
<td bgcolor="#e7f6ef" height="40" width="176">
<div>将fsimage同edit log合并 并产生新的fsimage </div>
</td>
</tr><tr><td bgcolor="#cbecde" height="73" width="136">
<div>保存文件，block dataNode之间的映射关系 </div>
</td>
<td bgcolor="#cbecde" height="73" width="136">
<div>维护block id 到datanode本地文件的映射关系 </div>
</td>
<td bgcolor="#cbecde" height="73" width="176">
<div>将产生的新的fsimage上传给NameNode     </div>
<div>     清除NameNode中的edit log </div>
</td>
</tr></tbody></table></div>
<div>  </div>
</div>
 
<div class="O"><strong>Block块</strong> </div>
<div class="O">
<div class="O">
<div><span>   </span> <span>数据块</span> <span lang="en-us" xml:lang="en-us">(block</span> <span lang="en-us" xml:lang="en-us">
) HDFS(Hadoop Distributed File System)</span> <span>默认的最基本存储单</span> <span>位默认大小是</span><span lang="en-us" xml:lang="en-us">64M
</span><span>。</span> </div>
<div>
<div class="O">
<div>第四部分：HDFS命令行接口 </div>
<div> 
<div class="O">
<div><span lang="en-us" xml:lang="en-us">  </span> <span lang="en-us" xml:lang="en-us">hadoop fs :</span> <span lang="en-us" xml:lang="en-us">
       </span></div>
<div><span lang="en-us" xml:lang="en-us">          [-ls &lt;path&gt;] </span></div>
<div><span lang="en-us" xml:lang="en-us">          </span><span lang="en-us" xml:lang="en-us">[-lsr &lt;path&gt;] </span>
</div>
<div><span lang="en-us" xml:lang="en-us">           [-du &lt;path&gt;] </span></div>
<div><span lang="en-us" xml:lang="en-us">           [-dus &lt;path&gt;] </span></div>
<div><span lang="en-us" xml:lang="en-us">           [-count[-q] &lt;path&gt;] </span></div>
<div><span lang="en-us" xml:lang="en-us">           [-mv &lt;src&gt; &lt;dst&gt;] </span></div>
<div><span lang="en-us" xml:lang="en-us">           [-cp &lt;src&gt; &lt;dst&gt;] </span></div>
<div><span lang="en-us" xml:lang="en-us">           [-rm [-skipTrash] &lt;path&gt;] </span></div>
<div><span lang="en-us" xml:lang="en-us">           [-rmr [-skipTrash] &lt;path&gt;] </span></div>
<div><span lang="en-us" xml:lang="en-us">           [-put &lt;localsrc&gt; ... &lt;dst&gt;] </span></div>
<div><span lang="en-us" xml:lang="en-us">           [-copyFromLocal &lt;localsrc&gt; ... &lt;dst&gt;] </span></div>
<div><span lang="en-us" xml:lang="en-us">           [-moveFromLocal &lt;localsrc&gt; ... &lt;dst&gt;] </span></div>
<div>  </div>
<div>
<div class="O">
<div><span lang="en-us" xml:lang="en-us">           [-getmerge &lt;src&gt; &lt;localdst&gt; [addnl]] </span></div>
<div><span lang="en-us" xml:lang="en-us">           [-cat &lt;src&gt;] </span></div>
<div><span lang="en-us" xml:lang="en-us">           [-text &lt;src&gt;] </span></div>
<div><span lang="en-us" xml:lang="en-us">           [-copyToLocal [-ignoreCrc] [-crc] &lt;src&gt; &lt;localdst&gt;]</span></div>
<div><span lang="en-us" xml:lang="en-us">           [-moveToLocal [-crc] &lt;src&gt; &lt;localdst&gt;] </span>
</div>
<div><span lang="en-us" xml:lang="en-us">           [-mkdir &lt;path&gt;] </span></div>
<div><span lang="en-us" xml:lang="en-us">           [-tail [-f] &lt;file&gt;] </span></div>
<div><span lang="en-us" xml:lang="en-us">           [-chmod [-R] &lt;MODE[,MODE]... | OCTALMODE&gt; PATH...]</span></div>
<div><span lang="en-us" xml:lang="en-us">           [-chown [-R] [OWNER][:[GROUP]] PATH...] </span>
</div>
<div><span lang="en-us" xml:lang="en-us">           [-chgrp [-R] GROUP PATH...] </span></div>
<div><span lang="en-us" xml:lang="en-us">           [-help [cmd]] </span></div>
<div>  </div>
</div>
 
<div class="O">
<div><span lang="en-us" xml:lang="en-us">hadoop dfsadmin : </span></div>
<div><span lang="en-us" xml:lang="en-us">           [-report] </span></div>
<div><span lang="en-us" xml:lang="en-us">             </span><span>报告文件系统的基本信息</span> </div>
<div><span lang="en-us" xml:lang="en-us">           [-safemode enter | leave | get | wait] </span>
</div>
<div><span lang="en-us" xml:lang="en-us">           </span><span>安全模式维护命令</span> </div>
<div><span lang="en-us" xml:lang="en-us">           [-saveNamespace] </span></div>
<div><span lang="en-us" xml:lang="en-us">             </span><span>保存当前的命名空间</span> </div>
<div><span lang="en-us" xml:lang="en-us">           [-refreshNodes] </span></div>
<div><span lang="en-us" xml:lang="en-us">            </span><span> </span> <span>重新读取</span> <span lang="en-us" xml:lang="en-us">
Hosts</span> <span>和</span> <span lang="en-us" xml:lang="en-us">eclude</span> <span>文件，使新的节点或需要退出集群的节点能够重新被</span><span lang="en-us" xml:lang="en-us">NameNode</span>
<span>识别。</span> </div>
<div><span lang="en-us" xml:lang="en-us">           [-finalizeUpgrade] </span></div>
<div><span lang="en-us" xml:lang="en-us">          </span><span> </span> <span>终结</span> <span lang="en-us" xml:lang="en-us">
HDFS</span> <span>的升级操作</span> </div>
<div><span lang="en-us" xml:lang="en-us">           [-upgradeProgress status | details | force] </span>
</div>
<div> 
<div class="O">
<div><span lang="en-us" xml:lang="en-us">          [-metasave filename] </span></div>
<div><span lang="en-us" xml:lang="en-us">           </span><span>保存</span> <span lang="en-us" xml:lang="en-us">Namenode</span><span>的主要数据结构到</span>
<span lang="en-us" xml:lang="en-us">Hadoop.log.dir </span><span>属性指定目录下的</span><span lang="en-us" xml:lang="en-us">filename</span>
<span>上</span> </div>
<div><span lang="en-us" xml:lang="en-us">           [-setQuota &lt;quota&gt; &lt;dirname&gt;...&lt;dirname&gt;] </span>
</div>
<div><span lang="en-us" xml:lang="en-us">           </span><span>为每个目录设定配额，强制限定目录树下的名字个数。</span> </div>
<div><span lang="en-us" xml:lang="en-us">           [-clrQuota &lt;dirname&gt;...&lt;dirname&gt;] </span></div>
<div><span>          </span><span>为每个目录清除配额设定。</span> </div>
<div><span lang="en-us" xml:lang="en-us">           [-setBalancerBandwidth &lt;bandwidth in bytes per second&gt;]</span></div>
<div><span lang="en-us" xml:lang="en-us">           </span><span>设定负载均衡时使用的带宽</span> </div>
<div> 
<div class="O">
<div>  </div>
<div>第五部分：Hadoop文件系统 </div>
<div>
<div class="O">
<div><span>目前</span> <span lang="en-us" xml:lang="en-us">Hadoop</span> <span>支持的文件系统： </span></div>
<div><span lang="en-us" xml:lang="en-us"><span lang="en-us" xml:lang="en-us"> </span></span>
<table dir="ltr" cellspacing="0" cellpadding="0" style="width:417px;"><tbody><tr><td bgcolor="#e7e7e7" height="24" width="121">
<div><span><strong>文件系统</strong></span> </div>
</td>
<td bgcolor="#e7e7e7" height="24" width="118">
<div><span lang="en-us" xml:lang="en-us"><strong>Java</strong></span> <span><strong>实现</strong></span></div>
</td>
<td bgcolor="#e7e7e7" height="24" width="180">
<div><span><strong>描述</strong></span> </div>
</td>
</tr><tr><td bgcolor="#cbcbcb" height="65" width="121">
<div><span lang="en-us" xml:lang="en-us">KFS</span> </div>
</td>
<td bgcolor="#cbcbcb" height="65" width="118">
<div><span lang="en-us" xml:lang="en-us">fs.kfs.KosmosFil</span> <span lang="en-us" xml:lang="en-us">eSystem</span></div>
</td>
<td bgcolor="#cbcbcb" height="65" width="180">
<div><span lang="en-us" xml:lang="en-us">Cloudstore  </span><span>是类似</span> <span lang="en-us" xml:lang="en-us">HDFS</span><span>的</span>
<span>由</span> <span lang="en-us" xml:lang="en-us">C++</span> <span>编写的文件系统</span> </div>
</td>
</tr><tr><td bgcolor="#e7e7e7" height="57" width="121">
<div><span lang="en-us" xml:lang="en-us">S3</span> <span>（本地）</span> </div>
</td>
<td bgcolor="#e7e7e7" height="57" width="118">
<div><span lang="en-us" xml:lang="en-us">fs.s3native.Nativ</span> <span lang="en-us" xml:lang="en-us">eS3File</span><span lang="en-us" xml:lang="en-us">System</span>
</div>
</td>
<td bgcolor="#e7e7e7" height="57" width="180">
<div><span>由</span> <span lang="en-us" xml:lang="en-us">Amazon S3</span> <span>支持的文件</span> <span>
系统</span> </div>
</td>
</tr><tr><td bgcolor="#cbcbcb" height="66" width="121">
<div><span lang="en-us" xml:lang="en-us">S3</span> <span>（基于块）</span> </div>
</td>
<td bgcolor="#cbcbcb" height="66" width="118">
<div><span lang="en-us" xml:lang="en-us">fs.s3.S3FileSyst</span> <span lang="en-us" xml:lang="en-us">em</span> </div>
</td>
<td bgcolor="#cbcbcb" height="66" width="180">
<div><span>由</span> <span lang="en-us" xml:lang="en-us">AmazonS3</span> <span>支持的文件系</span> <span>
统，以块格式来存储文件</span> </div>
</td>
</tr><tr><td bgcolor="#e7e7e7" height="58" width="121">
<div><span lang="en-us" xml:lang="en-us">HAR</span> </div>
</td>
<td bgcolor="#e7e7e7" height="58" width="118">
<div><span lang="en-us" xml:lang="en-us">fs.HarFileSystem</span> </div>
</td>
<td bgcolor="#e7e7e7" height="58" width="180">
<div><span>一个构建在其他文件系统来</span> <span>存档的文件系统。</span> </div>
</td>
</tr></tbody></table></div>
<div>  </div>
</div>
</div>
<div>  <span>转载请注明出处【 <a href="http://sishuok.com/forum/blogPost/list/5458.html" rel="nofollow">http://sishuok.com/forum/blogPost/list/5458.html</a></span> <span>】</span>
</div>
<div>  </div>
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