---
layout:     post
title:      Hadoop实战-中高级部分 之 Hadoop RestFul
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<span class="topictitle" style="font-size:13px;font-weight:bold;font-family:verdana, arial, helvetica, sans-serif;"><a href="http://sishuok.com/forum/blogPost/list/5833.html;jsessionid=057174F64851E03C717EC8661A992E40" rel="nofollow" title="Hadoop实战-中高级部分 之 Hadoop RestFul" class="blogtitle" style="font-size:18px !important;color:rgb(0,94,167);text-decoration:none;">Hadoop实战-中高级部分
 之 Hadoop RestFul </a></span><span style="font-family:verdana, arial, helvetica, sans-serif;background-color:rgb(250,250,250);"></span><br style="font-family:verdana, arial, helvetica, sans-serif;"><span style="font-family:verdana, arial, helvetica, sans-serif;"><span>浏览(8094)|<a href="http://sishuok.com/forum/blogPost/list/5833.html#comments" rel="nofollow" style="color:rgb(0,94,167);text-decoration:none;">评论</a>(2)
   <span>交流分类：<a href="http://sishuok.com/forum/blog/index/0/26.html" rel="nofollow" style="color:rgb(0,94,167);text-decoration:none;">Java</a></span>|笔记分类: <a href="http://sishuok.com/forum/blogCategory/showByCategory.html?categories_id=134&amp;user_id=8636" rel="nofollow" style="color:rgb(0,94,167);text-decoration:none;">Hadoop实战-中……</a> </span></span><br style="font-family:verdana, arial, helvetica, sans-serif;"><br style="font-family:verdana, arial, helvetica, sans-serif;"><div class="div_content_text_all" style="font-size:14px;border:0px;overflow:hidden;background-color:rgb(250,250,250);line-height:1.5em;font-family:verdana, arial, helvetica, sans-serif;">
<div style="font-size:12px;border-width:0px;overflow:hidden;">
<a href="http://sishuok.com/forum/blogPost/list/5833.html" rel="nofollow" style="color:rgb(0,94,167);text-decoration:none;">Hadoop RestFul</a></div>
<div style="font-size:12px;border-width:0px;overflow:hidden;">
<a href="http://sishuok.com/forum/blogPost/list/5936.html" rel="nofollow" style="color:rgb(0,94,167);text-decoration:none;">Hadoop HDFS原理1</a></div>
<div style="font-size:12px;border-width:0px;overflow:hidden;">
<a href="http://sishuok.com/forum/blogPost/list/5937.html" rel="nofollow" style="color:rgb(0,94,167);text-decoration:none;">Hadoop HDFS原理2</a></div>
<div style="font-size:12px;border-width:0px;overflow:hidden;">
<a href="http://sishuok.com/forum/blogPost/list/5938.html" rel="nofollow" style="color:rgb(0,94,167);text-decoration:none;">Hadoop作业调优参数调整及原理</a></div>
<div style="font-size:12px;border-width:0px;overflow:hidden;">
<a href="http://sishuok.com/forum/blogPost/list/5939.html" rel="nofollow" style="color:rgb(0,94,167);text-decoration:none;">Hadoop HA</a></div>
<div style="font-size:12px;border-width:0px;overflow:hidden;">
<a href="http://sishuok.com/forum/blogPost/list/5961.html" rel="nofollow" style="color:rgb(0,94,167);text-decoration:none;">Hadoop MapReduce高级编程</a></div>
<div style="font-size:12px;border-width:0px;overflow:hidden;">
<a href="http://sishuok.com/forum/blogPost/list/5963.html" rel="nofollow" style="color:rgb(0,94,167);text-decoration:none;">Hadoop IO</a></div>
<div style="font-size:12px;border-width:0px;overflow:hidden;">
<a href="http://sishuok.com/forum/blogPost/list/5965.html" rel="nofollow" style="color:rgb(0,94,167);text-decoration:none;">Hadoop MapReduce工作原理</a></div>
<div style="font-size:12px;border-width:0px;overflow:hidden;">
<a href="http://sishuok.com/forum/blogPost/list/5966.html" rel="nofollow" style="color:rgb(0,94,167);text-decoration:none;">Hadoop 管理</a></div>
<div style="font-size:12px;border-width:0px;overflow:hidden;">
<a href="http://sishuok.com/forum/blogPost/list/5969.html" rel="nofollow" style="color:rgb(0,94,167);text-decoration:none;">Hadoop 集群安装</a></div>
<div style="font-size:12px;border-width:0px;overflow:hidden;">
<a href="http://sishuok.com/forum/blogPost/list/5968.html" rel="nofollow" style="color:rgb(0,94,167);text-decoration:none;">Hadoop RPC</a></div>
<div class="O" style="font-size:12px;border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;"><span class="bold" style="font-size:20px;font-weight:bold;">本节课程概览</span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">什么是REST 与RESTful</div>
<div style="border-width:0px;overflow:hidden;">Hadoop的RESTful API接口</div>
<div style="border-width:0px;overflow:hidden;">API示例</div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;"><span class="bold" style="font-size:20px;font-weight:bold;"><span lang="en-us" style="font-size:12px;" xml:lang="en-us">第一部分：</span>什么是<span lang="en-us" style="font-size:12px;" xml:lang="en-us">REST </span>与<span lang="en-us" style="font-size:12px;" xml:lang="en-us">RESTful</span></span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;"><span class="bold" style="font-weight:bold;">•什么是REST及RESTful</span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;"> REST 描述了一个架构样式的互联系统（如 Web 应用程序）。REST 约束条件作为一个整体应用时，将生成一个简单、可扩展、有效、安全、可靠的架构。由于它简便、轻量级以及通过 HTTP 直接传输数据的特性，RESTful Web 服务成为基于 SOAP 服务的一个最有前途的替代方案。用于 web 服务和动态 Web 应用程序的多层架构可以实现可重用性、简单性、可扩展性和组件可响应性的清晰分离。Ajax
 和 RESTful Web 服务本质上是互为补充的。开发人员可以轻松使用 Ajax 和 RESTful Web 服务一起创建丰富的界面。</div>
<div style="border-width:0px;overflow:hidden;"><span>REST 指的是一组架构约束条件和原则。</span>满足这些约束条件和原则的应用程序或设计就是 RESTful。 </div>
</div>
</div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" style="font-weight:bold;"><span lang="en-us" xml:lang="en-us"><span>•</span>RESTful </span>原则</span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">• <span>客户端</span>和服务器之间的交互在请求之间是无状态的。 从客户端到服务器的每个请求都必须包含理解请求所必需的信息。如果服务器在请求之间的任何时间点重启，客户端不会得到通知。此外，无状态请求可以由任何可用服务器回答，这十分适合云计算之类的环境。客户端可以缓存数据以改进性能。</div>
<div style="border-width:0px;overflow:hidden;">•在服务器端，应用程序状态和功能可以分为各种资源。资源是一个有趣的概念实体，它向客户端公开。资源的例子有：应用程序对象、数据库记录、算法等等。每个资源都使用 URI (Universal Resource Identifier) 得到一个惟一的地址。所有资源都共享统一的界面，以便在客户端和服务器之间传输状态。使用的是标准的 HTTP 方法，比如 GET、PUT、POST
 和 DELETE。Hypermedia 是应用程序状态的引擎，资源表示通过超链接互联。</div>
<div style="border-width:0px;overflow:hidden;"> </div>
<div style="border-width:0px;overflow:hidden;">• <span>分层系统</span>，这表示组件无法了解它与之交互的中间层以外的组件。 通过将系统知识限制在单个层，可以限制整个系统的复杂性，促进了底层的独立性。</div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" style="font-weight:bold;"><span lang="en-us" xml:lang="en-us">REST </span>实现方式</span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
• <span>RESTful Web 服务与 RPC 样式的 Web 服务</span></div>
<div class="O1" style="border-width:0px;overflow:hidden;">
• <span>WebService</span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
• <span>RESTful Web 服务的 Java 框架</span></div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•Restlet</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•JSR-311<span></span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
• <span>构建 RESTful Web 服务的多层架构</span></div>
<div class="O1" style="border-width:0px;overflow:hidden;">
• <span>SSH</span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
  </div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" style="font-weight:bold;"><span lang="en-us" xml:lang="en-us">REST</span><span lang="en-us" xml:lang="en-us"> 特点</span></span></div>
</div>
</div>
•REST 从资源的角度来观察整个网络，分布在各处的资源由URI确定，而客户端的应用通过URI来获取资源的表示方式。获得这些表徵致使这些应用程序转变了其状态。随着不断获取资源的表示方式，客户端应用不断地在转变着其状态
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;"> </div>
</div>
</div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span lang="en-us" xml:lang="en-us">REST</span> <span>中的资源操作</span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">•资源是由URI来指定。</div>
<div style="border-width:0px;overflow:hidden;">•对资源的操作包括获取、创建、修改和删除资源，这些操作正好对应HTTP协议提供的GET、POST、PUT和DELETE方法。</div>
<div style="border-width:0px;overflow:hidden;">•通过操作资源的表现形式来操作资源。</div>
<div style="border-width:0px;overflow:hidden;">•资源的表现形式则是XML或者HTML，取决于读者是机器还是人，是消费web服务的客户软件还是web浏览器。当然也可以是任何其他的格式。</div>
<div style="border-width:0px;overflow:hidden;"> </div>
</div>
</div>
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" style="font-weight:bold;"><span lang="en-us" xml:lang="en-us">RESTful </span><span lang="en-us" xml:lang="en-us">API 要求</span></span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">•客户端和服务器结构</div>
<div style="border-width:0px;overflow:hidden;">•连接协议具有无状态性</div>
<div style="border-width:0px;overflow:hidden;">•能够利用Cache机制增进性能</div>
<div style="border-width:0px;overflow:hidden;">•层次化的系统 随需代码 - Javascript （可选）</div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" style="font-weight:bold;"><span lang="en-us" xml:lang="en-us">REST</span>的优点</span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
•可以利用缓存Cache来提高响应速度</div>
<div class="O" style="border-width:0px;overflow:hidden;">
•通讯本身的无状态性可以让不同的服务器的处理一系列请求中的不同请求，提高服务器的扩展性 浏览器即可作为客户端，</div>
<div class="O" style="border-width:0px;overflow:hidden;">
•简化软件需求</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•相对于其他叠加在HTTP协议之上的机制，REST的软件依赖性更小 不需要额外的资源发现机制</div>
<div class="O" style="border-width:0px;overflow:hidden;">
•在软件技术演进中的长期的兼容性更好</div>
<div class="O" style="border-width:0px;overflow:hidden;">
 </div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" style="font-weight:bold;"><span lang="en-us" xml:lang="en-us">RESTful </span><span lang="en-us" xml:lang="en-us">Web 服务</span></span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">•RESTful Web 服务（也称为 RESTful Web API）是一个使用HTTP并遵循REST原则的Web服务。</div>
<div style="border-width:0px;overflow:hidden;">• Web服务接受与返回的互联网媒体类型，比如：JSON，XML ，YAML 等</div>
<div style="border-width:0px;overflow:hidden;">•Web服务在该资源上所支持的一系列请求方法（比如：POST，GET，PUT或DELETE）。</div>
<div style="border-width:0px;overflow:hidden;">•不像基于SOAP的Web服务，RESTful Web服务并没有的“正式”标准。 这是因为REST是一种架构，而SOAP只是一个协议。虽然REST不是一个标准，但在实现RESTful Web服务时可以使用其他各种标准（比如HTTP，URL，XML，PNG等）。</div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;"><span class="bold" style="font-size:20px;font-weight:bold;">第二部分：Hadoop RESTful API接口</span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" style="font-weight:bold;"><span lang="en-us" xml:lang="en-us">Hadoop </span><span lang="en-us" xml:lang="en-us">RESTful </span><span lang="en-us" xml:lang="en-us">API 介绍</span></span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">•全称为webHDFS</div>
<div style="border-width:0px;overflow:hidden;">•在Hadoop 1.0 提供</div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span lang="en-us" xml:lang="en-us">Hadoop </span><span lang="en-us" xml:lang="en-us">RESTful </span><span lang="en-us" xml:lang="en-us">API-Get </span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
<table dir="ltr" cellspacing="0" cellpadding="0" style="font-size:12px;border:none;width:463px;"><tbody><tr><td bgcolor="#00CC99" width="212" height="36" style="font-family:verdana, arial, helvetica, sans-serif;">
<div style="border-width:0px;overflow:hidden;"><span>操作</span></div>
</td>
<td bgcolor="#00CC99" width="252" height="36" style="font-family:verdana, arial, helvetica, sans-serif;">
<div style="border-width:0px;overflow:hidden;"><span>参考</span></div>
<div style="border-width:0px;overflow:hidden;"> </div>
</td>
</tr><tr><td bgcolor="#CBECDE" width="212" height="32" style="font-family:verdana, arial, helvetica, sans-serif;">
<div style="border-width:0px;overflow:hidden;">OPEN</div>
<div style="border-width:0px;overflow:hidden;"> </div>
</td>
<td bgcolor="#CBECDE" width="252" height="32" style="font-family:verdana, arial, helvetica, sans-serif;">
<div style="border-width:0px;overflow:hidden;">FileSystem.open</div>
</td>
</tr><tr><td bgcolor="#E7F6EF" width="212" height="32" style="font-family:verdana, arial, helvetica, sans-serif;">
<div style="border-width:0px;overflow:hidden;">GETFILESTATUS</div>
</td>
<td bgcolor="#E7F6EF" width="252" height="32" style="font-family:verdana, arial, helvetica, sans-serif;">
<div style="border-width:0px;overflow:hidden;">FileSystem.getFileStatus</div>
<div style="border-width:0px;overflow:hidden;"> </div>
</td>
</tr><tr><td bgcolor="#CBECDE" width="212" height="42" style="font-family:verdana, arial, helvetica, sans-serif;">
<div style="border-width:0px;overflow:hidden;">GETCONTENTSUMMARY</div>
</td>
<td bgcolor="#CBECDE" width="252" height="42" style="font-family:verdana, arial, helvetica, sans-serif;">
<div style="border-width:0px;overflow:hidden;">FileSystem.getContentSummary</div>
<div style="border-width:0px;overflow:hidden;"> </div>
</td>
</tr><tr><td bgcolor="#E7F6EF" width="212" height="19" style="font-family:verdana, arial, helvetica, sans-serif;">
<div style="border-width:0px;overflow:hidden;">LISTSTATUS</div>
</td>
<td bgcolor="#E7F6EF" width="252" height="19" style="font-family:verdana, arial, helvetica, sans-serif;">
<div style="border-width:0px;overflow:hidden;">FileSystem.listStatus</div>
</td>
</tr><tr><td bgcolor="#CBECDE" width="212" height="32" style="font-family:verdana, arial, helvetica, sans-serif;">
<div style="border-width:0px;overflow:hidden;">GETFILECHECKSUM</div>
<div style="border-width:0px;overflow:hidden;"> </div>
</td>
<td bgcolor="#CBECDE" width="252" height="32" style="font-family:verdana, arial, helvetica, sans-serif;">
<div style="border-width:0px;overflow:hidden;">FileSystem.getFileChecksum</div>
<div style="border-width:0px;overflow:hidden;"> </div>
</td>
</tr><tr><td bgcolor="#E7F6EF" width="212" height="42" style="font-family:verdana, arial, helvetica, sans-serif;">
<div style="border-width:0px;overflow:hidden;">GETHOMEDIRECTORY</div>
<div style="border-width:0px;overflow:hidden;"> </div>
</td>
<td bgcolor="#E7F6EF" width="252" height="42" style="font-family:verdana, arial, helvetica, sans-serif;">
<div style="border-width:0px;overflow:hidden;">FileSystem.getHomeDirectory</div>
<div style="border-width:0px;overflow:hidden;"> </div>
</td>
</tr><tr><td bgcolor="#CBECDE" width="212" height="42" style="font-family:verdana, arial, helvetica, sans-serif;">
<div style="border-width:0px;overflow:hidden;">GETDELEGATIONTOKEN</div>
</td>
<td bgcolor="#CBECDE" width="252" height="42" style="font-family:verdana, arial, helvetica, sans-serif;">
<div style="border-width:0px;overflow:hidden;">FileSystem.getDelegationToken</div>
<div style="border-width:0px;overflow:hidden;"> </div>
</td>
</tr></tbody></table></div>
</div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" style="font-weight:bold;"><span lang="en-us" xml:lang="en-us">Hadoop </span><span lang="en-us" xml:lang="en-us">RESTful </span><span lang="en-us" xml:lang="en-us">API-Put</span></span></div>
<table dir="ltr" cellspacing="0" cellpadding="0" style="font-size:12px;border:none;width:463px;"><tbody><tr><td bgcolor="#00CC99" width="212" height="36" style="font-family:verdana, arial, helvetica, sans-serif;">
<div style="border-width:0px;overflow:hidden;"><span>操作</span></div>
</td>
<td bgcolor="#00CC99" width="252" height="36" style="font-family:verdana, arial, helvetica, sans-serif;">
<div style="border-width:0px;overflow:hidden;"><span>参考</span></div>
<div style="border-width:0px;overflow:hidden;"> </div>
</td>
</tr><tr><td bgcolor="#CBECDE" width="212" height="32" style="font-family:verdana, arial, helvetica, sans-serif;">
<div style="border-width:0px;overflow:hidden;">CREATE</div>
<div style="border-width:0px;overflow:hidden;"> </div>
</td>
<td bgcolor="#CBECDE" width="252" height="32" style="font-family:verdana, arial, helvetica, sans-serif;">
<div style="border-width:0px;overflow:hidden;">FileSystem.create</div>
</td>
</tr><tr><td bgcolor="#E7F6EF" width="212" height="32" style="font-family:verdana, arial, helvetica, sans-serif;">
<div style="border-width:0px;overflow:hidden;">MKDIRS</div>
</td>
<td bgcolor="#E7F6EF" width="252" height="32" style="font-family:verdana, arial, helvetica, sans-serif;">
<div style="border-width:0px;overflow:hidden;">FileSystem.mkdirs</div>
<div style="border-width:0px;overflow:hidden;"> </div>
</td>
</tr><tr><td bgcolor="#CBECDE" width="212" height="19" style="font-family:verdana, arial, helvetica, sans-serif;">
<div style="border-width:0px;overflow:hidden;">RENAME</div>
</td>
<td bgcolor="#CBECDE" width="252" height="19" style="font-family:verdana, arial, helvetica, sans-serif;">
<div style="border-width:0px;overflow:hidden;">FileSystem.rename</div>
</td>
</tr><tr><td bgcolor="#E7F6EF" width="212" height="19" style="font-family:verdana, arial, helvetica, sans-serif;">
<div style="border-width:0px;overflow:hidden;">SETREPLICATION</div>
</td>
<td bgcolor="#E7F6EF" width="252" height="19" style="font-family:verdana, arial, helvetica, sans-serif;">
<div style="border-width:0px;overflow:hidden;">FileSystem.setReplication</div>
</td>
</tr><tr><td bgcolor="#CBECDE" width="212" height="32" style="font-family:verdana, arial, helvetica, sans-serif;">
<div style="border-width:0px;overflow:hidden;">SETOWNER</div>
<div style="border-width:0px;overflow:hidden;"> </div>
</td>
<td bgcolor="#CBECDE" width="252" height="32" style="font-family:verdana, arial, helvetica, sans-serif;">
<div style="border-width:0px;overflow:hidden;">FileSystem.setOwner</div>
<div style="border-width:0px;overflow:hidden;"> </div>
</td>
</tr><tr><td bgcolor="#E7F6EF" width="212" height="32" style="font-family:verdana, arial, helvetica, sans-serif;">
<div style="border-width:0px;overflow:hidden;">SETPERMISSION</div>
<div style="border-width:0px;overflow:hidden;"> </div>
</td>
<td bgcolor="#E7F6EF" width="252" height="32" style="font-family:verdana, arial, helvetica, sans-serif;">
<div style="border-width:0px;overflow:hidden;">FileSystem.setTimes</div>
</td>
</tr><tr><td bgcolor="#CBECDE" width="212" height="32" style="font-family:verdana, arial, helvetica, sans-serif;">
<div style="border-width:0px;overflow:hidden;">SETTIMES</div>
</td>
<td bgcolor="#CBECDE" width="252" height="32" style="font-family:verdana, arial, helvetica, sans-serif;">
<div style="border-width:0px;overflow:hidden;">FileSystem.getDelegationToken</div>
<div style="border-width:0px;overflow:hidden;"> </div>
</td>
</tr><tr><td bgcolor="#E7F6EF" width="212" height="32" style="font-family:verdana, arial, helvetica, sans-serif;">
<div style="border-width:0px;overflow:hidden;">RENEWDELEGATIONTOKEN</div>
</td>
<td bgcolor="#E7F6EF" width="252" height="32" style="font-family:verdana, arial, helvetica, sans-serif;">
<div style="border-width:0px;overflow:hidden;">DistributedFileSystem.renewDelegationToken</div>
<div style="border-width:0px;overflow:hidden;"> </div>
</td>
</tr><tr><td bgcolor="#CBECDE" width="212" height="19" style="font-family:verdana, arial, helvetica, sans-serif;">
<div style="border-width:0px;overflow:hidden;">CANCELDELEGATIONTOKEN</div>
</td>
<td bgcolor="#CBECDE" width="252" height="19" style="font-family:verdana, arial, helvetica, sans-serif;">
<div style="border-width:0px;overflow:hidden;">DistributedFileSystem.cancelDelegationToken</div>
</td>
</tr></tbody></table>
 </div>
</div>
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" style="font-weight:bold;"><span lang="en-us" xml:lang="en-us">Hadoop </span><span lang="en-us" xml:lang="en-us">RESTful </span><span lang="en-us" xml:lang="en-us">API-Post与Delete</span></span></div>
<table dir="ltr" cellspacing="0" cellpadding="0" style="font-size:12px;border:none;width:463px;"><tbody><tr><td bgcolor="#00CC99" width="212" height="36" style="font-family:verdana, arial, helvetica, sans-serif;">
<div style="border-width:0px;overflow:hidden;"><span>操作</span></div>
</td>
<td bgcolor="#00CC99" width="252" height="36" style="font-family:verdana, arial, helvetica, sans-serif;">
<div style="border-width:0px;overflow:hidden;"><span>参考</span></div>
<div style="border-width:0px;overflow:hidden;"> </div>
</td>
</tr><tr><td bgcolor="#CBECDE" width="212" height="30" style="font-family:verdana, arial, helvetica, sans-serif;">
<div style="border-width:0px;overflow:hidden;">APPEND</div>
</td>
<td bgcolor="#CBECDE" width="252" height="30" style="font-family:verdana, arial, helvetica, sans-serif;">
<div style="border-width:0px;overflow:hidden;">FileSystem.append</div>
</td>
</tr><tr><td bgcolor="#E7F6EF" width="212" height="32" style="font-family:verdana, arial, helvetica, sans-serif;">
<div style="border-width:0px;overflow:hidden;">DELETE</div>
</td>
<td bgcolor="#E7F6EF" width="252" height="32" style="font-family:verdana, arial, helvetica, sans-serif;">
<div style="border-width:0px;overflow:hidden;">FileSystem.delete</div>
<div style="border-width:0px;overflow:hidden;"> </div>
</td>
</tr></tbody></table>
 </div>
</div>
 </div>
</div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;"><span class="bold" style="font-size:20px;font-weight:bold;">第三部分：API 示例</span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" style="font-weight:bold;"><span lang="en-us" xml:lang="en-us">Hadoop </span><span lang="en-us" xml:lang="en-us">RESTful </span><span lang="en-us" xml:lang="en-us">API 示例</span></span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">•什么是curl</div>
<div style="border-width:0px;overflow:hidden;">             curl是一个利用URL语法在命令行方式下工作的文件传输工具。它支持很多协议：FTP, FTPS, HTTP, HTTPS, GOPHER,  TELNET, DICT, FILE 以及 LDAP。</div>
<div style="border-width:0px;overflow:hidden;">            curl同样支持HTTPS认证，HTTP POST方法, HTTP PUT方法, FTP 上传, kerberos认证, HTTP上传, 代理服务器, cookies, 用户名/密码认证, 下载文件断点续传, 上载文件断点续传,  http代理服务器管道（ proxy tunneling）, 甚至它还支持IPv6, socks5代理服务器,
 通过http代理服务器上传文件到FTP服务器等等，功能十分强大。Windows操作系统下的网络蚂蚁，网际快车(FlashGet)的功能它都可以做到。准确的说，curl支 <br>
持文件的上传和下载，所以是一个综合传输工具，但是按照传统，用户习惯称curl为下载工具。</div>
<div style="border-width:0px;overflow:hidden;"> </div>
</div>
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" style="font-weight:bold;">配置RESTful API 可用</span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">• 安装sudo apt-get install curl</div>
<div style="border-width:0px;overflow:hidden;">• 在HDFS中设置 dfs.webhdfs.enabled = true</div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" style="font-weight:bold;">示例</span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">1.创建并写一个文件</div>
<div style="border-width:0px;overflow:hidden;">• curl -i -X PUT "http://localhost:50070/webhdfs/v1/&lt;PATH&gt;?op=CREATE</div>
<div style="border-width:0px;overflow:hidden;">[&amp;overwrite=&lt;true|false&gt;][&amp;blocksize=&lt;LONG&gt;][&amp;replication=&lt;SHORT&gt;]</div>
<div style="border-width:0px;overflow:hidden;">[&amp;permission=&lt;OCTAL&gt;][&amp;buffersize=&lt;INT&gt;]“</div>
<div style="border-width:0px;overflow:hidden;">•</div>
<div style="border-width:0px;overflow:hidden;">•curl -i -X PUT -T &lt;LOCAL_FILE&gt; "http://&lt;DATANODE&gt;:&lt;PORT&gt;/webhdfs/v1/&lt;PATH&gt;?</div>
<div style="border-width:0px;overflow:hidden;">op=CREATE...“</div>
<div style="border-width:0px;overflow:hidden;">注意这个地方填入的是DataNode的信息</div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">2.在一个文件内追加内容</div>
<div style="border-width:0px;overflow:hidden;">•curl -i -X POST "http://&lt;HOST&gt;:&lt;PORT&gt;/webhdfs/v1/&lt;PATH&gt;?op=APPEND[&amp;buffersize=&lt;INT&gt;]”</div>
<div style="border-width:0px;overflow:hidden;">•</div>
<div style="border-width:0px;overflow:hidden;">•curl -i -X POST -T &lt;LOCAL_FILE&gt; "http://&lt;DATANODE&gt;:&lt;PORT&gt;/webhdfs/v1/&lt;PATH&gt;?</div>
<div style="border-width:0px;overflow:hidden;">op=APPEND...“</div>
<div style="border-width:0px;overflow:hidden;">注意该条命令获得的是DataNode的信息。</div>
<div style="border-width:0px;overflow:hidden;"> 
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">3.打开并读取一个文件</div>
<div style="border-width:0px;overflow:hidden;">•curl -i -L "http://&lt;HOST&gt;:&lt;PORT&gt;/webhdfs/v1/&lt;PATH&gt;?op=OPEN</div>
<div style="border-width:0px;overflow:hidden;">[&amp;offset=&lt;LONG&gt;][&amp;length=&lt;LONG&gt;][&amp;buffersize=&lt;INT&gt;]“</div>
<div style="border-width:0px;overflow:hidden;"> </div>
<div style="border-width:0px;overflow:hidden;">4.创建一个目录</div>
<div style="border-width:0px;overflow:hidden;">curl -i -X PUT "http://&lt;HOST&gt;:&lt;PORT&gt;/&lt;PATH&gt;?op=MKDIRS[&amp;permission=&lt;OCTAL&gt;]“</div>
<div style="border-width:0px;overflow:hidden;">5.重名命文件、文件夹</div>
<div style="border-width:0px;overflow:hidden;">curl -i -X PUT "&lt;HOST&gt;:&lt;PORT&gt;/webhdfs/v1/&lt;PATH&gt;?op=RENAME&amp;destination=&lt;PATH&gt;"</div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us">6.删除文件/文件夹</span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="no-nyn" xml:lang="no-nyn">curl -i -X DELETE "http://&lt;host&gt;:&lt;port&gt;/webhdfs/v1/&lt;path&gt;?op=DELETE</span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us">[&amp;recursive=&lt;true|false&gt;]</span> <span lang="en-us" xml:lang="en-us">“</span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us">7.文件/ 文件夹的状态信息</span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="no-nyn" xml:lang="no-nyn">curl -i “http://&lt;HOST&gt;:&lt;PORT&gt;/webhdfs/v1/&lt;PATH&gt;?op=GETFILESTATUS</span> <span>”</span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us">8.目录列表</span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="no-nyn" xml:lang="no-nyn">curl -i "http://&lt;HOST&gt;:&lt;PORT&gt;/webhdfs/v1/&lt;PATH&gt;?op=LISTSTATUS</span> <span lang="no-nyn" xml:lang="no-nyn">”</span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us">9.获取目录的上下文环境汇总信息</span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="no-nyn" xml:lang="no-nyn">curl -i "http://&lt;HOST&gt;:&lt;PORT&gt;/webhdfs/v1/&lt;PATH&gt;?op=GETCONTENTSUMMARY"</span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us">10.获取Check Sum File</span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="no-nyn" xml:lang="no-nyn">curl -i "http://&lt;HOST&gt;:&lt;PORT&gt;/webhdfs/v1/&lt;PATH&gt;?op=GETFILECHECKSUM</span> <span lang="no-nyn" xml:lang="no-nyn">”</span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="no-nyn" xml:lang="no-nyn">11</span> <span lang="en-us" xml:lang="en-us">.获取Home 目录</span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="no-nyn" xml:lang="no-nyn">curl -i "http://&lt;HOST&gt;:&lt;PORT&gt;/webhdfs/v1/?op=GETHOMEDIRECTORY</span> <span lang="no-nyn" xml:lang="no-nyn">”</span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us">12.设置权限</span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us">curl -i -X PUT "http://&lt;HOST&gt;:&lt;PORT&gt;/webhdfs/v1/&lt;PATH&gt;?op=SETPERMISSION</span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us">[&amp;permission=&lt;OCTAL&gt;]</span> <span lang="en-us" xml:lang="en-us">“</span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us">13.设置所有者</span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us">curl -i -X PUT "http://&lt;HOST&gt;:&lt;PORT&gt;/webhdfs/v1/&lt;PATH&gt;?op=SETOWNER</span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us">[&amp;owner=&lt;USER&gt;][&amp;group=&lt;GROUP&gt;]"</span></div>
<div style="border-width:0px;overflow:hidden;">14.设置备份
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us"> curl -i -X PUT "http://&lt;HOST&gt;:&lt;PORT&gt;/webhdfs/v1/&lt;PATH&gt;?op=SETREPLICATION</span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us">[&amp;replication=&lt;SHORT&gt;]</span> <span lang="en-us" xml:lang="en-us">“</span></div>
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
</div>
</div>
</div>
            </div>
                </div>