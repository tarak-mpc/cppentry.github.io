---
layout:     post
title:      Hadoop实战-中高级部分 之 Hadoop RestFul  
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>
</p>
<div style="font-size:14px;"><a href="http://sishuok.com/forum/blogPost/list/5833.html" rel="nofollow">Hadoop RestFul</a></div>
<div style="font-size:14px;">Hadoop HDFS原理</div>
<div style="font-size:14px;">Hadoop作业调优参数调整及原理</div>
<div style="font-size:14px;">Hadoop HA</div>
<div style="font-size:14px;">Hadoop MapReduce高级编程</div>
<div style="font-size:14px;">Hadoop IO</div>
<div style="font-size:14px;">Hadoop MapReduce工作原理</div>
<div style="font-size:14px;">Hadoop 管理</div>
<div style="font-size:14px;">Hadoop 集群安装</div>
<div style="font-size:14px;">Hadoop RPC</div>
<div class="O" style="font-size:14px;">
<div><span class="bold" style="font-weight:bold;font-size:20px;">本节课程概览</span></div>
<div>
<div class="O">
<div>什么是REST 与RESTful</div>
<div>Hadoop的RESTful API接口</div>
<div>API示例</div>
<div>
<div class="O">
<div><span class="bold" style="font-weight:bold;font-size:20px;"><span lang="en-us" xml:lang="en-us">第一部分：</span>什么是<span lang="en-us" xml:lang="en-us">REST </span>与<span lang="en-us" xml:lang="en-us">RESTful</span></span></div>
<div>
<div class="O">
<div><span class="bold" style="font-weight:bold;">•什么是REST及RESTful</span></div>
<div>
<div class="O">
<div> REST 描述了一个架构样式的互联系统（如 Web 应用程序）。REST 约束条件作为一个整体应用时，将生成一个简单、可扩展、有效、安全、可靠的架构。由于它简便、轻量级以及通过 HTTP 直接传输数据的特性，RESTful Web 服务成为基于 SOAP 服务的一个最有前途的替代方案。用于 web 服务和动态 Web 应用程序的多层架构可以实现可重用性、简单性、可扩展性和组件可响应性的清晰分离。Ajax 和 RESTful Web 服务本质上是互为补充的。开发人员可以轻松使用 Ajax 和 RESTful Web 服务一起创建丰富的界面。</div>
<div>
<strong>REST </strong><strong>指的是一组架构约束条件和原则。</strong>满足这些约束条件和原则的应用程序或设计就是 RESTful。 </div>
</div>
</div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;"><span lang="en-us" xml:lang="en-us">•RESTful </span>原则</span></div>
<div class="O">
<div>• <span style="text-decoration:underline;">客户端</span>和服务器之间的交互在请求之间是无状态的。 从客户端到服务器的每个请求都必须包含理解请求所必需的信息。如果服务器在请求之间的任何时间点重启，客户端不会得到通知。此外，无状态请求可以由任何可用服务器回答，这十分适合云计算之类的环境。客户端可以缓存数据以改进性能。</div>
<div>•在服务器端，应用程序状态和功能可以分为各种资源。资源是一个有趣的概念实体，它向客户端公开。资源的例子有：应用程序对象、数据库记录、算法等等。每个资源都使用 URI (Universal Resource Identifier) 得到一个惟一的地址。所有资源都共享统一的界面，以便在客户端和服务器之间传输状态。使用的是标准的 HTTP 方法，比如 GET、PUT、POST 和 DELETE。Hypermedia 是应用程序状态的引擎，资源表示通过超链接互联。</div>
<div> </div>
<div>• <strong>分层系统</strong>，这表示组件无法了解它与之交互的中间层以外的组件。 通过将系统知识限制在单个层，可以限制整个系统的复杂性，促进了底层的独立性。</div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;"><span lang="en-us" xml:lang="en-us">REST </span>实现方式</span></div>
<div class="O">
<div class="O">• <strong>RESTful Web </strong><strong>服务与</strong> <strong></strong><strong>RPC </strong><strong>样式的</strong> <strong></strong><strong>Web </strong><strong>服务</strong>
</div>
<div class="O1">• <strong>WebService</strong>
</div>
<div class="O">• <strong>RESTful Web </strong><strong>服务的</strong> <strong></strong><strong>Java </strong><strong>框架</strong>
</div>
<div class="O1">•Restlet</div>
<div class="O1">•JSR-311<strong></strong>
</div>
<div class="O">• <strong>构建</strong> <strong></strong><strong>RESTful Web </strong><strong>服务的多层架构</strong>
</div>
<div class="O1">• <strong>SSH</strong>
</div>
<div class="O">  </div>
<div class="O">
<div class="O"><span class="bold" style="font-weight:bold;"><span lang="en-us" xml:lang="en-us">REST</span><span lang="en-us" xml:lang="en-us"> 特点</span></span></div>
</div>
</div>
•REST 从资源的角度来观察整个网络，分布在各处的资源由URI确定，而客户端的应用通过URI来获取资源的表示方式。获得这些表徵致使这些应用程序转变了其状态。随着不断获取资源的表示方式，客户端应用不断地在转变着其状态
<div class="O"> </div>
</div>
<div>
<div class="O">
<span lang="en-us" xml:lang="en-us">REST</span> 中的资源操作</div>
<div class="O">
<div class="O">
<div>•资源是由URI来指定。</div>
<div>•对资源的操作包括获取、创建、修改和删除资源，这些操作正好对应HTTP协议提供的GET、POST、PUT和DELETE方法。</div>
<div>•通过操作资源的表现形式来操作资源。</div>
<div>•资源的表现形式则是XML或者HTML，取决于读者是机器还是人，是消费web服务的客户软件还是web浏览器。当然也可以是任何其他的格式。</div>
<div> </div>
</div>
</div>
<div class="O"><span class="bold" style="font-weight:bold;"><span lang="en-us" xml:lang="en-us">RESTful </span><span lang="en-us" xml:lang="en-us">API 要求</span></span></div>
<div class="O">
<div class="O">
<div>•客户端和服务器结构</div>
<div>•连接协议具有无状态性</div>
<div>•能够利用Cache机制增进性能</div>
<div>•层次化的系统 随需代码 - Javascript （可选）</div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;"><span lang="en-us" xml:lang="en-us">REST</span>的优点</span></div>
<div>
<div class="O">•可以利用缓存Cache来提高响应速度</div>
<div class="O">•通讯本身的无状态性可以让不同的服务器的处理一系列请求中的不同请求，提高服务器的扩展性 浏览器即可作为客户端，</div>
<div class="O">•简化软件需求</div>
<div class="O1">•相对于其他叠加在HTTP协议之上的机制，REST的软件依赖性更小 不需要额外的资源发现机制</div>
<div class="O">•在软件技术演进中的长期的兼容性更好</div>
<div class="O"> </div>
<div class="O">
<div class="O"><span class="bold" style="font-weight:bold;"><span lang="en-us" xml:lang="en-us">RESTful </span><span lang="en-us" xml:lang="en-us">Web 服务</span></span></div>
<div class="O">
<div>•RESTful Web 服务（也称为 RESTful Web API）是一个使用HTTP并遵循REST原则的Web服务。</div>
<div>• Web服务接受与返回的互联网媒体类型，比如：JSON，XML ，YAML 等</div>
<div>•Web服务在该资源上所支持的一系列请求方法（比如：POST，GET，PUT或DELETE）。</div>
<div>•不像基于SOAP的Web服务，RESTful Web服务并没有的“正式”标准。 这是因为REST是一种架构，而SOAP只是一个协议。虽然REST不是一个标准，但在实现RESTful Web服务时可以使用其他各种标准（比如HTTP，URL，XML，PNG等）。</div>
<div>
<div class="O">
<div><span class="bold" style="font-weight:bold;font-size:20px;">第二部分：Hadoop RESTful API接口</span></div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;"><span lang="en-us" xml:lang="en-us">Hadoop </span><span lang="en-us" xml:lang="en-us">RESTful </span><span lang="en-us" xml:lang="en-us">API 介绍</span></span></div>
<div class="O">
<div>•全称为webHDFS</div>
<div>•在Hadoop 1.0 提供</div>
<div>
<div class="O">
<span lang="en-us" xml:lang="en-us">Hadoop </span><span lang="en-us" xml:lang="en-us">RESTful </span><span lang="en-us" xml:lang="en-us">API-Get </span>
</div>
<div class="O">
<table style="width:463px;" dir="ltr" border="0" cellspacing="0" cellpadding="0"><tbody><tr><td style="font-size:14px;" width="212" height="36"><strong>操作</strong></td>
<td style="font-size:14px;" width="252" height="36">
<div>
<strong>参考</strong><strong></strong>
</div>
<div> </div>
</td>
</tr><tr><td style="font-size:14px;" width="212" height="32">
<div>OPEN</div>
<div> </div>
</td>
<td style="font-size:14px;" width="252" height="32">FileSystem.open</td>
</tr><tr><td style="font-size:14px;" width="212" height="32">GETFILESTATUS</td>
<td style="font-size:14px;" width="252" height="32">
<div>FileSystem.getFileStatus</div>
<div> </div>
</td>
</tr><tr><td style="font-size:14px;" width="212" height="42">GETCONTENTSUMMARY</td>
<td style="font-size:14px;" width="252" height="42">
<div>FileSystem.getContentSummary</div>
<div> </div>
</td>
</tr><tr><td style="font-size:14px;" width="212" height="19">LISTSTATUS</td>
<td style="font-size:14px;" width="252" height="19">FileSystem.listStatus</td>
</tr><tr><td style="font-size:14px;" width="212" height="32">
<div>GETFILECHECKSUM</div>
<div> </div>
</td>
<td style="font-size:14px;" width="252" height="32">
<div>FileSystem.getFileChecksum</div>
<div> </div>
</td>
</tr><tr><td style="font-size:14px;" width="212" height="42">
<div>GETHOMEDIRECTORY</div>
<div> </div>
</td>
<td style="font-size:14px;" width="252" height="42">
<div>FileSystem.getHomeDirectory</div>
<div> </div>
</td>
</tr><tr><td style="font-size:14px;" width="212" height="42">GETDELEGATIONTOKEN</td>
<td style="font-size:14px;" width="252" height="42">
<div>FileSystem.getDelegationToken</div>
<div> </div>
</td>
</tr></tbody></table></div>
</div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;"><span lang="en-us" xml:lang="en-us">Hadoop </span><span lang="en-us" xml:lang="en-us">RESTful </span><span lang="en-us" xml:lang="en-us">API-Put</span></span></div>
<table style="width:463px;" dir="ltr" border="0" cellspacing="0" cellpadding="0"><tbody><tr><td style="font-size:14px;" width="212" height="36"><strong>操作</strong></td>
<td style="font-size:14px;" width="252" height="36">
<div>
<strong>参考</strong><strong></strong>
</div>
<div> </div>
</td>
</tr><tr><td style="font-size:14px;" width="212" height="32">
<div>CREATE</div>
<div> </div>
</td>
<td style="font-size:14px;" width="252" height="32">FileSystem.create</td>
</tr><tr><td style="font-size:14px;" width="212" height="32">MKDIRS</td>
<td style="font-size:14px;" width="252" height="32">
<div>FileSystem.mkdirs</div>
<div> </div>
</td>
</tr><tr><td style="font-size:14px;" width="212" height="19">RENAME</td>
<td style="font-size:14px;" width="252" height="19">FileSystem.rename</td>
</tr><tr><td style="font-size:14px;" width="212" height="19">SETREPLICATION</td>
<td style="font-size:14px;" width="252" height="19">FileSystem.setReplication</td>
</tr><tr><td style="font-size:14px;" width="212" height="32">
<div>SETOWNER</div>
<div> </div>
</td>
<td style="font-size:14px;" width="252" height="32">
<div>FileSystem.setOwner</div>
<div> </div>
</td>
</tr><tr><td style="font-size:14px;" width="212" height="32">
<div>SETPERMISSION</div>
<div> </div>
</td>
<td style="font-size:14px;" width="252" height="32">FileSystem.setTimes</td>
</tr><tr><td style="font-size:14px;" width="212" height="32">SETTIMES</td>
<td style="font-size:14px;" width="252" height="32">
<div>FileSystem.getDelegationToken</div>
<div> </div>
</td>
</tr><tr><td style="font-size:14px;" width="212" height="32">RENEWDELEGATIONTOKEN</td>
<td style="font-size:14px;" width="252" height="32">
<div>DistributedFileSystem.renewDelegationToken</div>
<div> </div>
</td>
</tr><tr><td style="font-size:14px;" width="212" height="19">CANCELDELEGATIONTOKEN</td>
<td style="font-size:14px;" width="252" height="19">DistributedFileSystem.cancelDelegationToken</td>
</tr></tbody></table>
 </div>
</div>
<div class="O"><span class="bold" style="font-weight:bold;"><span lang="en-us" xml:lang="en-us">Hadoop </span><span lang="en-us" xml:lang="en-us">RESTful </span><span lang="en-us" xml:lang="en-us">API-Post与Delete</span></span></div>
<table style="width:463px;" dir="ltr" border="0" cellspacing="0" cellpadding="0"><tbody><tr><td style="font-size:14px;" width="212" height="36"><strong>操作</strong></td>
<td style="font-size:14px;" width="252" height="36">
<div>
<strong>参考</strong><strong></strong>
</div>
<div> </div>
</td>
</tr><tr><td style="font-size:14px;" width="212" height="30">APPEND</td>
<td style="font-size:14px;" width="252" height="30">FileSystem.append</td>
</tr><tr><td style="font-size:14px;" width="212" height="32">DELETE</td>
<td style="font-size:14px;" width="252" height="32">
<div>FileSystem.delete</div>
<div> </div>
</td>
</tr></tbody></table>
 </div>
</div>
 </div>
</div>
<div class="O">
<div><span class="bold" style="font-weight:bold;font-size:20px;">第三部分：API 示例</span></div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;"><span lang="en-us" xml:lang="en-us">Hadoop </span><span lang="en-us" xml:lang="en-us">RESTful </span><span lang="en-us" xml:lang="en-us">API 示例</span></span></div>
<div class="O">
<div>•什么是curl</div>
<div>             curl是一个利用URL语法在命令行方式下工作的文件传输工具。它支持很多协议：FTP, FTPS, HTTP, HTTPS, GOPHER,  TELNET, DICT, FILE 以及 LDAP。</div>
<div>            curl同样支持HTTPS认证，HTTP POST方法, HTTP PUT方法, FTP 上传, kerberos认证, HTTP上传, 代理服务器, cookies, 用户名/密码认证, 下载文件断点续传, 上载文件断点续传,  http代理服务器管道（ proxy tunneling）, 甚至它还支持IPv6, socks5代理服务器, 通过http代理服务器上传文件到FTP服务器等等，功能十分强大。Windows操作系统下的网络蚂蚁，网际快车(FlashGet)的功能它都可以做到。准确的说，curl支 <br>持文件的上传和下载，所以是一个综合传输工具，但是按照传统，用户习惯称curl为下载工具。</div>
<div> </div>
</div>
<div class="O"><span class="bold" style="font-weight:bold;">配置RESTful API 可用</span></div>
<div class="O">
<div>• 安装sudo apt-get install curl</div>
<div>• 在HDFS中设置 dfs.webhdfs.enabled = true</div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;">示例</span></div>
<div class="O">
<div>1.创建并写一个文件</div>
<div>• curl -i -X PUT "http://localhost:50070/webhdfs/v1/&lt;PATH&gt;?op=CREATE</div>
<div>[&amp;overwrite=&lt;true|false&gt;][&amp;blocksize=&lt;LONG&gt;][&amp;replication=&lt;SHORT&gt;]</div>
<div>[&amp;permission=&lt;OCTAL&gt;][&amp;buffersize=&lt;INT&gt;]“</div>
<div>•</div>
<div>•curl -i -X PUT -T &lt;LOCAL_FILE&gt; "http://&lt;DATANODE&gt;:&lt;PORT&gt;/webhdfs/v1/&lt;PATH&gt;?</div>
<div>op=CREATE...“</div>
<div>注意这个地方填入的是DataNode的信息</div>
<div>
<div class="O">
<div>2.在一个文件内追加内容</div>
<div>•curl -i -X POST "http://&lt;HOST&gt;:&lt;PORT&gt;/webhdfs/v1/&lt;PATH&gt;?op=APPEND[&amp;buffersize=&lt;INT&gt;]”</div>
<div>•</div>
<div>•curl -i -X POST -T &lt;LOCAL_FILE&gt; "http://&lt;DATANODE&gt;:&lt;PORT&gt;/webhdfs/v1/&lt;PATH&gt;?</div>
<div>op=APPEND...“</div>
<div>注意该条命令获得的是DataNode的信息。</div>
<div> 
<div class="O">
<div>3.打开并读取一个文件</div>
<div>•curl -i -L "http://&lt;HOST&gt;:&lt;PORT&gt;/webhdfs/v1/&lt;PATH&gt;?op=OPEN</div>
<div>[&amp;offset=&lt;LONG&gt;][&amp;length=&lt;LONG&gt;][&amp;buffersize=&lt;INT&gt;]“</div>
<div> </div>
<div>4.创建一个目录</div>
<div>curl -i -X PUT "http://&lt;HOST&gt;:&lt;PORT&gt;/&lt;PATH&gt;?op=MKDIRS[&amp;permission=&lt;OCTAL&gt;]“</div>
<div>5.重名命文件、文件夹</div>
<div>curl -i -X PUT "&lt;HOST&gt;:&lt;PORT&gt;/webhdfs/v1/&lt;PATH&gt;?op=RENAME&amp;destination=&lt;PATH&gt;"</div>
<div>
<div class="O">
<div><span lang="en-us" xml:lang="en-us">6.删除文件/文件夹</span></div>
<div><span lang="no-nyn" xml:lang="no-nyn">curl -i -X DELETE "http://&lt;host&gt;:&lt;port&gt;/webhdfs/v1/&lt;path&gt;?op=DELETE</span></div>
<div>
<span lang="en-us" xml:lang="en-us">[&amp;recursive=&lt;true|false&gt;]</span> <span lang="en-us" xml:lang="en-us">“</span>
</div>
<div><span lang="en-us" xml:lang="en-us">7.文件/ 文件夹的状态信息</span></div>
<div>
<span lang="no-nyn" xml:lang="no-nyn">curl -i “http://&lt;HOST&gt;:&lt;PORT&gt;/webhdfs/v1/&lt;PATH&gt;?op=GETFILESTATUS</span> ”</div>
<div><span lang="en-us" xml:lang="en-us">8.目录列表</span></div>
<div>
<span lang="no-nyn" xml:lang="no-nyn">curl -i "http://&lt;HOST&gt;:&lt;PORT&gt;/webhdfs/v1/&lt;PATH&gt;?op=LISTSTATUS</span> <span lang="no-nyn" xml:lang="no-nyn">”</span>
</div>
<div><span lang="en-us" xml:lang="en-us">9.获取目录的上下文环境汇总信息</span></div>
<div><span lang="no-nyn" xml:lang="no-nyn">curl -i "http://&lt;HOST&gt;:&lt;PORT&gt;/webhdfs/v1/&lt;PATH&gt;?op=GETCONTENTSUMMARY"</span></div>
<div>
<div class="O">
<div><span lang="en-us" xml:lang="en-us">10.获取Check Sum File</span></div>
<div>
<span lang="no-nyn" xml:lang="no-nyn">curl -i "http://&lt;HOST&gt;:&lt;PORT&gt;/webhdfs/v1/&lt;PATH&gt;?op=GETFILECHECKSUM</span> <span lang="no-nyn" xml:lang="no-nyn">”</span>
</div>
<div>
<span lang="no-nyn" xml:lang="no-nyn">11</span> <span lang="en-us" xml:lang="en-us">.获取Home 目录</span>
</div>
<div>
<span lang="no-nyn" xml:lang="no-nyn">curl -i "http://&lt;HOST&gt;:&lt;PORT&gt;/webhdfs/v1/?op=GETHOMEDIRECTORY</span> <span lang="no-nyn" xml:lang="no-nyn">”</span>
</div>
<div><span lang="en-us" xml:lang="en-us">12.设置权限</span></div>
<div><span lang="en-us" xml:lang="en-us">curl -i -X PUT "http://&lt;HOST&gt;:&lt;PORT&gt;/webhdfs/v1/&lt;PATH&gt;?op=SETPERMISSION</span></div>
<div>
<span lang="en-us" xml:lang="en-us">[&amp;permission=&lt;OCTAL&gt;]</span> <span lang="en-us" xml:lang="en-us">“</span>
</div>
<div><span lang="en-us" xml:lang="en-us">13.设置所有者</span></div>
<div><span lang="en-us" xml:lang="en-us">curl -i -X PUT "http://&lt;HOST&gt;:&lt;PORT&gt;/webhdfs/v1/&lt;PATH&gt;?op=SETOWNER</span></div>
<div><span lang="en-us" xml:lang="en-us">[&amp;owner=&lt;USER&gt;][&amp;group=&lt;GROUP&gt;]"</span></div>
<div>14.设置备份
<div class="O">
<div><span lang="en-us" xml:lang="en-us"> curl -i -X PUT "http://&lt;HOST&gt;:&lt;PORT&gt;/webhdfs/v1/&lt;PATH&gt;?op=SETREPLICATION</span></div>
<div>
<span lang="en-us" xml:lang="en-us">[&amp;replication=&lt;SHORT&gt;]</span> <span lang="en-us" xml:lang="en-us">“</span>
</div>
<div> </div>
</div>
</div>
</div>
转载请注明出处【  <a href="http://sishuok.com/forum/blogPost/list/0/5461.html" rel="nofollow">http://sishuok.com/forum/blogPost/list/0/5461.html</a> 】</div>
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
</div>
</div>
            </div>
                </div>