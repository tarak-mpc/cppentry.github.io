---
layout:     post
title:      Hadoop实战-中高级部分 之 Hadoop 管理 
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p> </p>
<p>
</p>
<div style="font-size:14px;">
<div><a href="http://sishuok.com/forum/blogPost/list/5833.html" rel="nofollow">Hadoop RestFul</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5936.html" rel="nofollow">Hadoop HDFS原理1</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5937.html" rel="nofollow">Hadoop HDFS原理2</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5938.html" rel="nofollow">Hadoop作业调优参数调整及原理</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5939.html" rel="nofollow">Hadoop HA</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5961.html" rel="nofollow">Hadoop MapReduce高级编程</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5963.html" rel="nofollow">Hadoop IO</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5965.html" rel="nofollow">Hadoop MapReduce工作原理</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5966.html" rel="nofollow">Hadoop 管理</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5969.html" rel="nofollow">Hadoop 集群安装</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5968.html" rel="nofollow">Hadoop RPC</a></div>
<div> </div>
</div>
<div style="font-size:14px;"><span class="bold" style="font-weight:bold;">第一部分：HDFS管理</span></div>
<div style="font-size:14px;">
<div class="O"><span class="bold" style="font-weight:bold;">命令行工具</span></div>
<div>
<div class="O">•HDFS文件系统检查工具FSCK</div>
<div class="O1">•用法：hadoop fsck [ <span style="text-decoration:underline;">GENERIC_OPTIONS</span>] &lt;path&gt; [-move | -delete | -openforwrite] [-files [-blocks [-locations | -racks]]]</div>
<div class="O1">•命令选项描述&lt;path&gt;检查的起始目录。</div>
<div class="O1">•-move移动受损文件到/lost+found-delete删除受损文件。</div>
<div class="O1">•-openforwrite打印出写打开的文件。</div>
<div class="O1">•-files打印出正被检查的文件。</div>
<div class="O1">•-blocks打印出块信息报告。</div>
<div class="O1">•-locations打印出每个块的位置信息。</div>
<div class="O1">•-racks打印出data-node的网络拓扑结构。</div>
<div class="O1">
<div class="O">• <strong>d</strong> <strong>fsadmin</strong>
</div>
<div class="O1">
<table style="width:494px;" dir="ltr" border="0" cellspacing="0" cellpadding="0"><tbody><tr><td style="font-size:14px;" width="154" height="23"><strong>命令选项</strong></td>
<td style="font-size:14px;" width="341" height="23"><strong>描述</strong></td>
</tr><tr><td style="font-size:14px;" width="154" height="23">-report</td>
<td style="font-size:14px;" width="341" height="23">报告文件系统的基本信息和统计信息。</td>
</tr><tr><td style="font-size:14px;" width="154" height="39">-refreshNodes</td>
<td style="font-size:14px;" width="341" height="39">重新读取hosts和exclude文件，更新允许连到Namenode的或那些需要退出或入编的Datanode的集合。</td>
</tr><tr><td style="font-size:14px;" width="154" height="55">-finalizeUpgrade</td>
<td style="font-size:14px;" width="341" height="55">终结HDFS的升级操作。Datanode删除前一个版本的工作目录，之后Namenode也这样做。这个操作完结整个升级过程。</td>
</tr></tbody></table></div>
<div class="O">
<table style="width:494px;" dir="ltr" border="0" cellspacing="0" cellpadding="0"><tbody><tr><td style="font-size:14px;" width="180" height="22"><strong>命令选项</strong></td>
<td style="font-size:14px;" width="315" height="22"><strong>描述</strong></td>
</tr><tr><td style="font-size:14px;" width="180" height="96">-metasave filename</td>
<td style="font-size:14px;" width="315" height="96">保存Namenode的主要数据结构到hadoop.log.dir属性指定的目录下的&lt;filename&gt;文件。对于下面的每一项，&lt;filename&gt;中都会一行内容与之对应 <br>1. Namenode收到的Datanode的心跳信号 <br>2. 等待被复制的块 <br>3. 正在被复制的块 <br>4. 等待被删除的块</td>
</tr><tr><td style="font-size:14px;" width="180" height="96">-setQuota &lt;quota&gt; &lt;dirname&gt;...&lt;dirname&gt;</td>
<td style="font-size:14px;" width="315" height="96">为每个目录 &lt;dirname&gt;设定配额&lt;quota&gt;。目录配额是一个长整型整数，强制限定了目录树下的名字个数。 <br>命令会在这个目录上工作良好，以下情况会报错：<br>1. N不是一个正整数，或者 <br>2. 用户不是管理员，或者 <br>3. 这个目录不存在或是文件，或者 <br>4. 目录会马上超出新设定的配额。</td>
</tr><tr><td style="font-size:14px;" width="180" height="42">-upgradeProgress status | details | force</td>
<td style="font-size:14px;" width="315" height="42">请求当前系统的升级状态，状态的细节，或者强制升级操作进行。</td>
</tr><tr><td style="font-size:14px;" width="180" height="70">-clrQuota &lt;dirname&gt;...&lt;dirname&gt;</td>
<td style="font-size:14px;" width="315" height="70">为每一个目录&lt;dirname&gt;清除配额设定。 <br>命令会在这个目录上工作良好，以下情况会报错：<br>1. 这个目录不存在或是文件，或者 <br>2. 用户不是管理员。 <br>如果目录原来没有配额不会报错。</td>
</tr></tbody></table></div>
</div>
<div class="O">
<br><div class="O">
<span class="bold" style="font-weight:bold;">规划管理<span lang="en-us" xml:lang="en-us">HDFS</span>数据</span> <span lang="en-us" xml:lang="en-us"><br></span>
</div>
</div>
</div>
<div class="O">
<div>•无论是做为开发者还是管理员了解HDFS目录结构与规划都是很重要的一件事情。</div>
<div>•它们可以帮助我们诊断问题</div>
<div>•如何管理HDFS数据也是我们需要研究的课题</div>
<div>
<div class="O">
<div>•Namenode的目录结构：</div>
<div>           ${dfs.name.dir}/current /VERSION <br>                                                  /edits <br>                                                  /fsimage <br>                                                  /fstime</div>
<div>    dfs.name.dir是hdfs-site.xml里配置的若干个目录组成的列表。</div>
<div>•通过设置Sencondary  NameNode解决edits log变大问题</div>
<div>
<div class="O">
<div>•Sencondary  Namenode的目录结构：</div>
<div>           ${fs.checkpoint.dir}/current /VERSION <br>                                                  /edits <br>                                                  /fsimage <br>                                                  /fstime</div>
<div>                                                       /previous.checkpoint/VERSION</div>
<div>                                                                                       /edits <br>                                                                                  /fsimage <br>                                                                                  /fstime</div>
<div>•fs.checkpoint.dir 属性位于core-site.xml</div>
<div>•previous.checkpoint 存储之前的保存的元数据</div>
<div>
<div>
<div class="O">•Datanode 的目录结构：</div>
<div class="O">           ${dfs.data.dir}/current /VERSION <br>                                          /blk_id</div>
<div class="O"> /blk_id.meta</div>
<div class="O">                                                  …..</div>
<div class="O">                                               /subdir0</div>
<div class="O">                                                ….</div>
<div class="O">•dfs.data.dir 属性位与hdfs-site.xml</div>
<div class="O">•Subdir0 为子文件夹，防止HDFS一个目录下的文件块太多</div>
<div class="O1">•通过dfs.datanode.numblocks可以设置多少个块会生成一个子文件夹默认是64</div>
<div class="O"> </div>
</div>
 
<div class="O"><span class="bold" style="font-weight:bold;">安全模式</span></div>
</div>
</div>
<div>
<div class="O">•安全模式是Namenode的一个状态，这种状态下，Namenode  <br>1. 不接受对名字空间的更改(只读) <br>2. 不复制或删除块</div>
<div class="O">•NameNode会在启动时自动进入安全模式，当配置的块最小百分比数满足最小的副本数条件时，会自动离开安全模式。安全模式可以手动进入，但是这样的话也必须手动关闭安全模式</div>
<div class="O1">• 默认为99.99%</div>
<div class="O1">•参数dfs.safemode.threshold.pct 位与hdfs-site.xml</div>
<div class="O">•命令行hadoop dfsadmin -safemode enter | leave | get | wait<strong></strong>
</div>
<div class="O"> </div>
</div>
<div class="O">
<span class="bold" style="font-weight:bold;">访问操作记录日志</span> <span lang="en-us" xml:lang="en-us"><br></span>
</div>
<div>
<div class="O">•HDFS可以记录文件系统的所有访问请求</div>
<div class="O">•默认情况下该功能是关闭的</div>
<div class="O">•可以通过设置log4j来开启该属性</div>
<div class="O1">•log4j.logger.org.apache.hadoop.fs.FSNamesystem.audit= WARN设置为info</div>
<div class="O">•该请求记录文件会单独存放</div>
<div class="O"> </div>
</div>
<div class="O"><span class="bold" style="font-weight:bold;">数据扫描块</span></div>
<div>
<div class="O">•每一个DataNode都会运行一个数据扫描线程</div>
<div class="O">•扫描线程定期的检查所有数据块以确保修复损坏的块</div>
<div class="O">•周期可以设定</div>
<div class="O1">•dfs.datanode.scan.period.hourses 默认是504小时</div>
<div class="O">•在UI部分可以查看检查的情况</div>
<div class="O1">•http://datanode：50075/blockScannerReport</div>
<div class="O">•通过指定listblocks参数可以报告最新的状态</div>
<div class="O1">•http://datanode:50075/blockScannerReport?Listblocks</div>
<div class="O"> </div>
</div>
<div class="O">
<div><span class="bold" style="font-weight:bold;">第二部分：MapReduce作业管理</span></div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;">命令行工具</span></div>
<div>
<div class="O">• <strong>查看</strong> <strong>Job</strong> <strong>信息</strong>
</div>
<div class="O1">• <strong>hadoop job -list</strong>
</div>
<div class="O">• <strong>杀掉</strong> <strong>Job</strong>
</div>
<div class="O1">• <strong>hadoop  job </strong><strong>–kill</strong>
</div>
<div class="O">•指定路径下查看历史日志汇总</div>
<div class="O1">•hadoop job -history output-dir</div>
<div class="O">•作业的更多细节</div>
<div class="O1">•hadoop job -history all output-dir</div>
<div class="O">•打印map和reduce完成百分比和所有计数器</div>
<div class="O1">• <strong>hadoop job </strong>–status job_id<strong></strong>
</div>
<div class="O">•杀死任务。被杀死的任务不会不利于失败尝试。</div>
<div class="O1">•hadoop jab -kill-task &lt;task-id&gt;</div>
<div class="O">•使任务失败。被失败的任务会对失败尝试不利。</div>
<div class="O1">•hadoop job  -fail-task &lt;task-id&gt;</div>
<div class="O1">
<div class="O">
<div><span class="bold" style="font-weight:bold;">Hadoop mradmin</span></div>
<div>
<table style="width:489px;" dir="ltr" border="0" cellspacing="0" cellpadding="0"><tbody><tr><td style="font-size:14px;" width="267" height="32"><strong>命令</strong></td>
<td style="font-size:14px;" width="223" height="32"><strong>说明</strong></td>
</tr><tr><td style="font-size:14px;" width="267" height="32">-refreshServiceAcl</td>
<td style="font-size:14px;" width="223" height="32">重新装载ACL认证文件</td>
</tr><tr><td style="font-size:14px;" width="267" height="32">-refreshQueues</td>
<td style="font-size:14px;" width="223" height="32">刷新任务队列的信息</td>
</tr><tr><td style="font-size:14px;" width="267" height="32">-refreshUserToGroupsMappings</td>
<td style="font-size:14px;" width="223" height="32">刷新用户与用户组对应关系</td>
</tr><tr><td style="font-size:14px;" width="267" height="32">-refreshSuperUserGroupsConfiguration</td>
<td style="font-size:14px;" width="223" height="32">刷新用户组的配置</td>
</tr><tr><td style="font-size:14px;" width="267" height="32">-refreshNodes</td>
<td style="font-size:14px;" width="223" height="32">刷新JobTracker的主机配置信息</td>
</tr></tbody></table>
 </div>
<div> 
<div class="O"><span class="bold" style="font-weight:bold;" lang="en-us" xml:lang="en-us">UI详细介绍</span></div>
</div>
</div>
<div>
<div class="O">•UI 有足够的信息，供我们查看Job运行的信息与历史运行记录</div>
<div class="O">•URL</div>
<div class="O1">•http://&lt;jobtracker&gt;50030/jobtracker</div>
<div class="O"> </div>
<div class="O">
<div class="O">
<div><span class="bold" style="font-weight:bold;">第三部分：Hadoop安全与权限的管理</span></div>
<div>
<div>
<div class="O">•安全认证</div>
<div class="O1">•确保用户的身份</div>
<div class="O">•授权</div>
<div class="O1">•确保用户的操作只能是在被允许的范围内</div>
<div class="O">•用户</div>
<div class="O1">•Hadoop用户</div>
<div class="O">•服务</div>
<div class="O1">•NameNode 等</div>
</div>
 
<div class="O">
<div>•Hadoop原来的版本安全性比较低。</div>
<div>• <strong>Namenode</strong> <strong>或者</strong> <strong>J</strong> <strong>obtracker</strong> <strong>缺乏安全认证机制</strong>
</div>
<div>• <strong>DataNode</strong> <strong>缺乏安全授权机制</strong>
</div>
<div>•Hadoop提供了两种安全机制：Simple和Kerberos</div>
<div> </div>
</div>
</div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;"><span lang="en-us" xml:lang="en-us">Simple</span>机制</span></div>
</div>
</div>
 
<div class="O">
<div>•Simple机制（默认情况，Hadoop采用该机制）是JAAS协议与delegation token整合机制</div>
<div>•JAAS是Java 认证和授权服务（Java Authentication and Authorization Service）的缩写，是PAM框架的Java实现。</div>
<div>
<div class="O">
<span class="bold" style="font-weight:bold;" lang="en-us" xml:lang="en-us">Kerberos</span> <span class="bold" style="font-weight:bold;">机制</span><br></div>
<div>
<div class="O">•Kerberos 提供了这样一种机制，可以认证的密钥放到服务器上，用户在进行操作时，要先到认证服务器上做校验，只有通过，才可以进行相应的操作。</div>
<div class="O">•Hadoop的安全问题</div>
<div class="O1">• <strong>解决服务器到服务器的认证</strong>
</div>
<div class="O1">• <strong>解决</strong> <strong>client</strong> <strong>到服务器的认证</strong>
</div>
<div class="O1">• <strong>对用户级别上的认证并没有实现</strong>
</div>
<div class="O1">
<div class="O">
<span class="bold" style="font-weight:bold;"><span lang="en-us" xml:lang="en-us">Kerberos</span>概念</span> <span lang="en-us" xml:lang="en-us"><br></span>
</div>
<div class="O">
<div>•Princal：被认证的个体，有一个名字和口令</div>
<div>•KDC: 是一个网络服务，提供ticket 和临时会话密钥</div>
<div>•Ticket：一个记录，客户用它来向服务器证明自己的身份，包括客户标识、会话密钥、时间戳。</div>
<div>•AS ： 认证服务器</div>
<div>•TSG： 许可证服务器</div>
<div>
<div class="O">
<span lang="en-us" xml:lang="en-us"><br></span><span class="bold" style="font-weight:bold;"><span lang="en-us" xml:lang="en-us">Hadoop</span>使用<span lang="en-us" xml:lang="en-us">Kerberos</span>好处</span> <span lang="en-us" xml:lang="en-us"><br></span>
</div>
<div class="O">
<div>•可靠： Hadoop 本身并没有认证功能和创建用户组功能，使用依靠外围的认证系统</div>
<div>•高效 ：Kerberos使用对称钥匙操作，比SSL的公共密钥快</div>
<div>•操作简单： 用户可以方便进行操作，不需要很复杂的指令。比如废除一个用户只需要从Kerbores的KDC数据库中删除即可。</div>
<div><img src="http://sishuok.com/forum/upload/2012/9/6/ca69654d19d43f1d526ce692d3c79d37__4.JPG" alt=""></div>
<div><img src="http://sishuok.com/forum/upload/2012/9/6/5f56d916247f96f6da0f767f64401593__5.JPG" alt=""></div>
<div>  <img src="http://sishuok.com/forum/upload/2012/9/6/3cab243b5f981d4c93e91dd39319d777__6.JPG" alt=""></div>
<div> </div>
</div>
<div class="O">
<span lang="en-us" xml:lang="en-us"><br></span><span class="bold" style="font-weight:bold;" lang="en-us" xml:lang="en-us">HDFS安全机制</span> <span lang="en-us" xml:lang="en-us"><br></span>
</div>
<div class="O">
<div>•Client去NameNode获取访问DataNode的凭证</div>
<div>•获得凭证后，才可以访问相应的数据库</div>
<div>•DataNode在启动时会在NameNode拿来相应的凭证</div>
<div>•客户端访问时，DataNode 会校验相应的凭证</div>
<div> </div>
<div>
<div class="O">
<span lang="en-us" xml:lang="en-us">MapReduce安全机制</span> <span lang="en-us" xml:lang="en-us"><br></span>
</div>
<div>
<div class="O">•作业提交</div>
<div class="O1">•所有关于作业的提交或者作业运行状态的追踪均是采用带有Kerberos认证的RPC实现的</div>
<div class="O1">•授权用户提交作业时，JobTracker会为之生 成一个令牌，该令牌将被作为job的一部分存储到HDFS上并通过RPC分发给各个TaskTracker，一旦job运行结束，该token失 效。</div>
<div class="O">•任务运行</div>
<div class="O1">•用户提交作业的每个task均是以用户身份启动的，这样，一个用户的task便不可以向TaskTracker或者其他用户的task发送操作系统信号，最其他用户造成干扰。这要求为每个用户在所有TaskTracker上建一个账号。</div>
<div class="O">•Shuffle</div>
<div class="O1">•reduce task对“请求URL”和“当前时间”计算HMAC-SHA1值，并将该值作为请求的一部分发动给TaskTracker，TaskTracker收到 后会验证该值的正确性</div>
<div class="O">
<div class="O">
<span class="bold" style="font-weight:bold;"><span lang="en-us" xml:lang="en-us">RPC </span>安全机制</span> <span lang="en-us" xml:lang="en-us"><br></span>
</div>
<div class="O">
<div>•用户发送RPC时，会将自己的用户名放在请求信息里</div>
<div>•RPCServer 接受到信息后， RPCServer 会校验授权，如果通过请发送给用户响应</div>
<div> </div>
</div>
 
<div class="O"><span class="bold" style="font-weight:bold;" lang="en-us" xml:lang="en-us">Hadoop权限管理</span></div>
</div>
</div>
<div>
<div class="O">•用户分组管理</div>
<div class="O1">•用户按组为单位组织管理</div>
<div class="O1">•可以限制用户使用的资源等内容</div>
<div class="O1">•Hadoop 使用Linux的用户与用户组</div>
<div class="O">•作业管理</div>
<div class="O1">•作业提交权限控制</div>
<div class="O1">•作业运行状态查看权限控制。</div>
<div class="O1">•可限定可提交作业的用户</div>
<div class="O1">•可限定可查看作业运行状态的用户</div>
<div class="O1">•可限定普通用户只能修改自己作业的优先级，kill自己的作业</div>
<div class="O1">•高级用户可以控制所有作业。 </div>
<div class="O"> 
<div class="O">
<div>•这一点很重要，如果没有权限，会造成数据混乱</div>
<div>•主要是通过系统的用户组权限来进行控制</div>
<div> 
<div class="O"><span class="bold" style="font-weight:bold;" lang="en-us" xml:lang="en-us">MapReduce权限管理</span></div>
</div>
</div>
</div>
</div>
<div>
<div class="O">•MapReduce支持权限管理需使用Fair Scheduler或者 Capacity Scheduler</div>
<div class="O">•为MapReduce添加权限管理一共分3步</div>
<div class="O1">•配置Fair Scheduler或者 Capacity Scheduler</div>
<div class="O1">•配置用户管理</div>
<div class="O1">•配置作业的ACL</div>
</div>
<div class="O"><span class="bold" style="font-weight:bold;">配置权限</span></div>
<div>
<div class="O">•配置Fair Scheduler</div>
<div class="O"> </div>
<div class="O1">•配置用户管理</div>
<div class="O2">•配置Queue相关信息</div>
<div class="O2">•配置属性在mapre-site.xml</div>
<div class="O2"> </div>
<div class="O2">
<div class="O"><span class="bold" style="font-weight:bold;">配置<span lang="en-us" xml:lang="en-us">Queue</span>的相关信息</span></div>
<div class="O">
<div><span lang="en-us" xml:lang="en-us">&lt;property&gt;</span></div>
<div><span lang="en-us" xml:lang="en-us">  &lt;name&gt;mapred.acls.enabled&lt;/name&gt;</span></div>
<div><span lang="en-us" xml:lang="en-us">  &lt;value&gt;true&lt;/value&gt;</span></div>
<div><span lang="en-us" xml:lang="en-us">&lt;/property&gt;</span></div>
<div><span lang="en-us" xml:lang="en-us">&lt;property&gt;</span></div>
<div><span lang="en-us" xml:lang="en-us">  &lt;name&gt;mapred.queue.names&lt;/name&gt;</span></div>
<div><span lang="en-us" xml:lang="en-us">  &lt;value&gt;test,default&lt;/value&gt;</span></div>
<div>
<span lang="en-us" xml:lang="en-us">&lt;/property&gt;</span> 属性 <span lang="en-us" xml:lang="en-us">mapred.queue.names</span> 是 <span lang="en-us" xml:lang="en-us">queue</span> 的所有名字，在这一名字中，必须有一个叫 “ <span lang="en-us" xml:lang="en-us">default”</span> 的 <span lang="en-us" xml:lang="en-us">queue</span>
</div>
<div><span lang="en-us" xml:lang="en-us">&lt;property&gt;</span></div>
<div><span lang="en-us" xml:lang="en-us">  &lt;name&gt;mapred.queue.my_queue.acl-submit-job&lt;/name&gt;</span></div>
<div><span lang="en-us" xml:lang="en-us">  &lt;value&gt; test&lt;/value&gt;</span></div>
<div><span lang="en-us" xml:lang="en-us">&lt;/property&gt;</span></div>
<div> 
<div class="O">
<div> </div>
<div>&lt;property&gt;</div>
<div>  &lt;name&gt;mapred.queue.default.acl-administer-jobs&lt;/name&gt;</div>
<div>  &lt;value&gt;&lt;/value&gt;</div>
<div>&lt;/property&gt;</div>
<div>&lt;property&gt;</div>
<div> &lt;name&gt;mapred.queue.my_queue.acl-administer-jobs&lt;/name&gt;</div>
<div>  &lt;value&gt;&lt;/value&gt;</div>
<div>&lt;/property&gt;</div>
<div> </div>
<div> </div>
<div> 
<div>
<div class="O">•mapred.queue.names是queue的所有名字</div>
<div class="O1">•必须有一个叫“default”的queue</div>
<div class="O">• mapred.queue.&lt;queue-name&gt;.acl-submit-job，指定哪些user或者group可以向该queue中提交作业</div>
<div class="O">•mapred.queue.&lt;queue-name&gt;.acl-administer-jobs，指定哪些user或者group可以管理该queue中的所有作业。</div>
<div class="O">• 每个queue拥有的资源量和其他信息，在其他配置文件里指定</div>
<div class="O">• mapred.queue.&lt;queue-name&gt;.acl-submit-job和mapred.queue.&lt;queue-name&gt;.acl-administer-jobs配置方法如下：</div>
<div class="O">      用户和用户组之间用空格分开，用户之间用“，”分割，用户组之间用“，”分割，如果queue的属性值中只有用户组，则前面保留一个空格。</div>
<div class="O"> </div>
<div class="O"> 
<div class="O"><span class="bold" style="font-weight:bold;">配置<span lang="en-us" xml:lang="en-us">fair-scheduler.xml</span></span></div>
</div>
</div>
</div>
<div>
<div class="O">
<div><span lang="en-us" xml:lang="en-us">&lt;?xml version="1.0"?&gt;</span></div>
<div><span lang="en-us" xml:lang="en-us">&lt;allocations&gt;</span></div>
<div>
<span lang="en-us" xml:lang="en-us"> &lt;pool name=“</span> <span lang="en-us" xml:lang="en-us">pool</span> <span lang="en-us" xml:lang="en-us">"&gt;</span>
</div>
<div>
<span lang="en-us" xml:lang="en-us">    &lt;minMaps&gt;</span> <span lang="en-us" xml:lang="en-us">4</span> <span lang="en-us" xml:lang="en-us">&lt;/minMaps&gt;</span>
</div>
<div>
<span lang="en-us" xml:lang="en-us">    &lt;minReduces&gt;</span> <span lang="en-us" xml:lang="en-us">4</span> <span lang="en-us" xml:lang="en-us">&lt;/minReduces&gt;</span>
</div>
<div>
<span lang="en-us" xml:lang="en-us">     &lt;maxRunningJobs&gt;</span> <span lang="en-us" xml:lang="en-us">4</span> <span lang="en-us" xml:lang="en-us">&lt;/maxRunningJobs&gt;</span>
</div>
<div>
<span lang="en-us" xml:lang="en-us">     &lt;minSharePreemptionTimeout&gt;</span> <span lang="en-us" xml:lang="en-us">400</span> <span lang="en-us" xml:lang="en-us">&lt;/minSharePreemptionTimeout&gt;</span>
</div>
<div>
<span lang="en-us" xml:lang="en-us">      &lt;weight&gt;</span> <span lang="en-us" xml:lang="en-us">1</span> <span lang="en-us" xml:lang="en-us">.0&lt;/weight&gt;</span>
</div>
<div><span lang="en-us" xml:lang="en-us">  &lt;/pool&gt;</span></div>
<div><span lang="en-us" xml:lang="en-us">      &lt;user name="bob"&gt;</span></div>
<div>
<span lang="en-us" xml:lang="en-us">      &lt;maxRunningJobs&gt;</span> <span lang="en-us" xml:lang="en-us">4</span> <span lang="en-us" xml:lang="en-us">&lt;/maxRunningJobs&gt;</span>
</div>
<div><span lang="en-us" xml:lang="en-us">       &lt;/user&gt;</span></div>
<div>
<span lang="en-us" xml:lang="en-us">       &lt;poolMaxJobsDefault&gt;</span> <span lang="en-us" xml:lang="en-us">30</span> <span lang="en-us" xml:lang="en-us">&lt;/poolMaxJobsDefault&gt;</span>
</div>
<div>
<span lang="en-us" xml:lang="en-us">       &lt;userMaxJobsDefault&gt;</span> <span lang="en-us" xml:lang="en-us">3</span> <span lang="en-us" xml:lang="en-us">&lt;/userMaxJobsDefault&gt;</span>
</div>
<div>
<span lang="en-us" xml:lang="en-us">       &lt;defaultMinSharePreemptionTimeout&gt;</span> <span lang="en-us" xml:lang="en-us">5</span><span lang="en-us" xml:lang="en-us">00&lt;/defaultMinSharePreemptionTimeout&gt;</span>
</div>
<div>
<span lang="en-us" xml:lang="en-us">       &lt;fairSharePreemptionTimeout&gt;</span> <span lang="en-us" xml:lang="en-us">5</span> <span lang="en-us" xml:lang="en-us">00&lt;/fairSharePreemptionTimeout&gt;</span>
</div>
<div><span lang="en-us" xml:lang="en-us">&lt;/allocations&gt;</span></div>
<div> </div>
<div> 
<div class="O"><span class="bold" style="font-weight:bold;">作业管理</span></div>
</div>
</div>
<div>
<div class="O">•在core-site.xmll中配置以下属性：</div>
<div class="O2">&lt;property&gt;</div>
<div class="O2">  &lt;name&gt;hadoop.security.authorization&lt;/name&gt;</div>
<div class="O2">  &lt;value&gt;true&lt;/value&gt;</div>
<div class="O2">&lt;/property&gt;</div>
<div class="O">•配置${HADOOP_CONF_DIR}/hadoop-policy.xml</div>
<div class="O1">•参数格式</div>
<div class="O2">•用户名与用户组之间用空格分开</div>
<div class="O2">•用户用户之间用“,”</div>
<div class="O2">•Tom,cat super,no</div>
<div class="O">
<table style="width:511px;" dir="ltr" border="0" cellspacing="0" cellpadding="0"><tbody><tr><td style="font-size:14px;" width="245" height="23"><strong>属性</strong></td>
<td style="font-size:14px;" width="267" height="23"><strong>说明</strong></td>
</tr><tr><td style="font-size:14px;" width="245" height="23">security.clinet.protocol.acl</td>
<td style="font-size:14px;" width="267" height="23">控制访问HDFS的权限</td>
</tr><tr><td style="font-size:14px;" width="245" height="40">
<div>security.datanode.protocol.acl</div>
<div> </div>
</td>
<td style="font-size:14px;" width="267" height="40">
<div>控制datanode到namenode的通信权限</div>
<div> </div>
</td>
</tr><tr><td style="font-size:14px;" width="245" height="23">security.clinet.datanode.protocol.acl</td>
<td style="font-size:14px;" width="267" height="23">用户block恢复</td>
</tr><tr><td style="font-size:14px;" width="245" height="40">security.namenode.protocol.acl</td>
<td style="font-size:14px;" width="267" height="40">控制sencondary namenode与datanode权限</td>
</tr><tr><td style="font-size:14px;" width="245" height="40">
<div>security.inter.datanode.protocol.acl</div>
<div> </div>
</td>
<td style="font-size:14px;" width="267" height="40">DataNode之前更新时间戳</td>
</tr><tr><td style="font-size:14px;" width="245" height="23">security.task.umbilical.protocol.acl</td>
<td style="font-size:14px;" width="267" height="23">控制task与tasktracker通信</td>
</tr><tr><td style="font-size:14px;" width="245" height="23">security.refresh.policy.protocol.acl</td>
<td style="font-size:14px;" width="267" height="23">控制更新作业管理配置文件</td>
</tr><tr><td style="font-size:14px;" width="245" height="23">security.jobsubmission.protocol.acl</td>
<td style="font-size:14px;" width="267" height="23">控制作业提交等权限</td>
</tr></tbody></table>
 </div>
<div class="O"> 
<div>
<div class="O">•作业提交时指定Pool来进行提交</div>
<div class="O">•示例</div>
<div class="O">   hadoop jar wordcount.jar wordcount  -D  pool.name= your_name input</div>
<div class="O">       output</div>
<div class="O"> </div>
<div class="O">
<div class="O">
<div> </div>
<div> </div>
<div>
<span class="bold" style="font-weight:bold;">第四部分：Hadoop集群的状态监控与管理工具</span> </div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;">我们需要监督Hadoop什么</span></div>
</div>
<div> 
<div>
<div class="O">•CPU使用率</div>
<div class="O">•硬盘使用情况</div>
<div class="O">•网络负载</div>
<div class="O">•进程服务是否正常运行</div>
<div class="O">•。。。。</div>
<div class="O1">手动每项去查看，非常痛苦的事情，我们需要工具</div>
<div class="O"> </div>
</div>
</div>
<div>  </div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;" lang="en-us" xml:lang="en-us">Ganglia</span></div>
</div>
</div>
</div>
</div>
</div>
</div>
<div>
<div class="O">•Ganglia 是什么</div>
<div class="O1">•Ganglia是一个跨平台可扩展的，高 性能计算系统下的分布式监控系统，如集群和网格。</div>
<div class="O1">•可以进行自动的进行信息的收集</div>
<div class="O">•Ganglia 组件</div>
<div class="O1">•Gmond</div>
<div class="O1">•Gmetad</div>
<div class="O1">•Web前端</div>
<div class="O"> </div>
</div>
</div>
</div>
</div>
</div>
<img src="http://sishuok.com/forum/upload/2012/9/6/541e95fea6e2f0d9e12edf10b063fdd4__7.JPG" alt=""></div>
<div class="O"> 
<div>
<div class="O">•Gmetad -服务端</div>
<div class="O1">•负责周期性的到各个datasource收集各个cluster的数据，并更新到rrd数据库中</div>
<div class="O">•Gmond –客户端</div>
<div class="O1">•收集本机的监控数据，发送到其他机器上</div>
<div class="O">•web 浏览界面</div>
<div class="O1">•一个基于ＰＨＰ的监控界面，通常和Gmetad安装在同一个节点上</div>
<div class="O">
<div class="O"><span lang="en-us" xml:lang="en-us">Ganglia优点</span></div>
</div>
</div>
</div>
<div class="O">
<div class="O">
<div>•自动收集数据</div>
<div>•图形界面</div>
<div>• 数据库rrdtool存储了历史数据</div>
<div>•Gmond 数据存储在内存中，使用开销比较小。</div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;">截图</span></div>
<img src="http://sishuok.com/forum/upload/2012/9/6/b468ac7d2756897a18a319819ff31cb2__8.JPG" alt=""></div>
<div> </div>
</div>
</div>
</div>
<div class="O"><span class="bold" style="font-weight:bold;">日志监控</span></div>
<div>
<div class="O">•设置日志级别</div>
<div class="O1">•在调试过程中非常的有用</div>
<div class="O1">•设置方式</div>
<div class="O2">•页面</div>
<div class="O2">•命令行</div>
<div class="O3">•hadoop  daemonlog –setlevel  jobtrackerhost-50030 packege level</div>
<div class="O2">•持久改变</div>
<div class="O3">•log.logger.org.apache.hadoop.mapred.JobTracker= level</div>
<div class="O">•获得运行栈的信息</div>
<div class="O1">•http://namenode:50070/stacks</div>
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
<br>  转载请注明出处【 <a href="http://sishuok.com/forum/blogPost/list/0/5966.html" rel="nofollow">http://sishuok.com/forum/blogPost/list/0/5966.html</a>】</div>
            </div>
                </div>