---
layout:     post
title:      Hadoop实战-中高级部分 之 Hadoop RPC  
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p> </p>
<p>
</p>
<p> </p>
<p><a href="http://weibo.com/u/2428827111" rel="nofollow">关注我们的微博 </a></p>
<p> </p>
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
<div><a href="http://sishuok.com/forum/blogPost/list/5968.html" rel="nofollow">Hadoop RPC<br></a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5968.html" rel="nofollow"> </a></div>
</div>
<div style="font-size:14px;"><span class="bold" style="font-weight:bold;">第一部分：什么是RPC</span></div>
<div style="font-size:14px;">
<div class="O">
<div>
<span lang="en-us" xml:lang="en-us">         RPC</span> <span lang="en-us" xml:lang="en-us">(Remote Procedure Call Protocol) </span><span lang="en-us" xml:lang="en-us">–</span> 远程过程协议调用 。通过 <span lang="en-us" xml:lang="en-us">RPC</span>我们可以从网络上的计算机请求服务，而不需要了 解底层网络协议。 <span lang="en-us" xml:lang="en-us">Hadoop</span> 底层的交互都是通过 <span lang="en-us" xml:lang="en-us">rpc</span> 进行的。例 如： <span lang="en-us" xml:lang="en-us">datanode</span> 和 <span lang="en-us" xml:lang="en-us">namenode </span><span lang="en-us" xml:lang="en-us">、</span> <span lang="en-us" xml:lang="en-us">tasktracker</span> 和<span lang="en-us" xml:lang="en-us">jobtracker</span> <span lang="en-us" xml:lang="en-us">、</span> <span lang="en-us" xml:lang="en-us">secondary namenode</span> 和 <span lang="en-us" xml:lang="en-us">namenode</span> 之间的通信都是通过 <span lang="en-us" xml:lang="en-us">rpc</span> 实现的。</div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;" lang="en-us" xml:lang="en-us">RPC 模式</span></div>
</div>
<div>
<div class="O">
<div>
<span lang="en-us" xml:lang="en-us">         RPC</span> 采用客户机 <span lang="en-us" xml:lang="en-us">/</span> 服务器 <span style="text-decoration:underline;">模式</span> 。请求程序就是一个客户机， 而服务提供程序就是一个服务器。首先，客户机调用进程发送 一个有进程参数的调用信息到服务进程，然后等待应答信息。 在服务器端，进程保持睡眠状态直到调用信息的到达为止。当 一个调用信息到达，服务器获得进程参数，计算结果，发送答 复信息，然后等待下一个调用信息，最后， <span style="text-decoration:underline;">客户端</span> 调用进程接 收答复信息，获得进程结果，然后调用执行继续进行。</div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;">工作原理</span></div>
</div>
<div>
<div class="O">
<div>
<span lang="en-us" xml:lang="en-us">         </span> 运行时 <span lang="en-us" xml:lang="en-us">,</span> 一次客户机对服务器的 <span lang="en-us" xml:lang="en-us">RPC</span> 调用 <span lang="en-us" xml:lang="en-us">,</span> 其内部操作大致有如下十步：</div>
<div>
<span lang="en-us" xml:lang="en-us">1.</span> 调用客户端句柄；执行传送参数</div>
<div>
<span lang="en-us" xml:lang="en-us">2.</span> 调用本地系统内核发送网络 <span style="text-decoration:underline;">消息</span>
</div>
<div>
<span lang="en-us" xml:lang="en-us">3.</span> 消息传送到远程 <span style="text-decoration:underline;">主机</span>
</div>
<div>
<span lang="en-us" xml:lang="en-us">4.</span> 服务器句柄得到消息并取得参数</div>
<div>
<span lang="en-us" xml:lang="en-us">5.</span> 执行远程过程</div>
<div>
<span lang="en-us" xml:lang="en-us">6.</span> 执行的过程将结果返回服务器句柄</div>
<div>
<span lang="en-us" xml:lang="en-us">7.</span> 服务器句柄返回结果，调用远程系统内核</div>
<div>
<span lang="en-us" xml:lang="en-us">8.</span> 消息传回 <span style="text-decoration:underline;">本地主机</span>
</div>
<div>
<span lang="en-us" xml:lang="en-us">9.</span> 客户句柄由内核接收消息</div>
<div>
<span lang="en-us" xml:lang="en-us">10.</span> 客户接收句柄返回的数据</div>
<div>
<div class="O">
<div>  </div>
<div><span class="bold" style="font-weight:bold;">第二部分：Hadoop的RPC机制</span></div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;" lang="en-us" xml:lang="en-us">Hadoop PRC</span></div>
<div class="O">
<div>•Hadoop RPC在整个Hadoop中应用非常广泛，Client、DataNode、NameNode之间的通讯全靠它了。</div>
<div>         举个例子，我们平时操作HDFS的时候，使用的是FileSystem类，它的内部有个DFSClient对象，这个对象负责与NameNode打交道。在运行时，DFSClient在本地创建一个NameNode的代理，然后就操作这个代理，这个代理就会通过网络，远程调用到NameNode的方法，也能返回值。</div>
<div>•Hadoop RPC 位于org.apache.hadoop.ipc</div>
<div>•Hadoop RPC = 动态代理 + 定制好的二进制流</div>
<div>•分为Server与Clinet端</div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;">服务端流程</span></div>
<div class="O">
<div>•Listener线程监视RPC Client发送过来的数据。</div>
<div>•当有数据可以接收时，调用Connection的readAndProcess方法。</div>
<div>• Connection边接收边对数据进行处理，如果接收到一个完整的Call包，则构建一个Call对象。PUSH到Call队列中，由Handler线程来处理Call队列中的所有Call。</div>
<div>•Handler线程监听Call队列，如果Call队列非空，按FIFO规则从Call队列取出Call。</div>
<div>•将Call交给RPC.Server处理。</div>
<div>•借助JDK提供的Method，完成对目标方法的调用，目标方法由具体的业务逻辑实现。</div>
<div>•返回响应。Server.Handler按照异步非阻塞的方式向RPC Client发送响应，如果有未发送出的数据，则交由Server.Responder来完成。</div>
<div><img src="http://sishuok.com/forum/upload/2012/9/6/39a7e7c9de9b20fb811e09f9996dd8c1__1.JPG" alt=""></div>
<div> </div>
<div>
<div class="O">服务端</div>
</div>
<div>
<table style="width:407px;" dir="ltr" border="0" cellspacing="0" cellpadding="0"><tbody><tr><td style="font-size:14px;" width="149" height="40"><strong>结构</strong></td>
<td style="font-size:14px;" width="258" height="40">
<div><strong>功能</strong></div>
<div> </div>
</td>
</tr><tr><td style="font-size:14px;" width="149" height="51">Server.Listener</td>
<td style="font-size:14px;" width="258" height="51">RPC Server的监听者，用来接收RPC Client的连接请求和数据，其中数据封装成Call后PUSH到Call队列。</td>
</tr><tr><td style="font-size:14px;" width="149" height="36">Server.Handler</td>
<td style="font-size:14px;" width="258" height="36">RPC Server的Call处理者，和Server.Listener通过Call队列交互。</td>
</tr><tr><td style="font-size:14px;" width="149" height="65">Server.Responder</td>
<td style="font-size:14px;" width="258" height="65">RPC Server的响应者。Server.Handler按照异步非阻塞的方式向RPC Client发送响应，如果有未发送出的数据，交由Server.Responder来完成。</td>
</tr><tr><td style="font-size:14px;" width="149" height="36">Server.Connection</td>
<td style="font-size:14px;" width="258" height="36">RPC Server数据接收者。提供接收数据，解析数据包的功能。</td>
</tr><tr><td style="font-size:14px;" width="149" height="23">Server.Call</td>
<td style="font-size:14px;" width="258" height="23">持有客户端的Call信息。</td>
</tr></tbody></table></div>
</div>
</div>
<div> 
<div class="O">客户端</div>
</div>
</div>
 
<table style="width:442px;" dir="ltr" border="0" cellspacing="0" cellpadding="0"><tbody><tr><td style="font-size:14px;" width="167" height="23"><strong>结构</strong></td>
<td style="font-size:14px;" width="276" height="23"><strong>功能</strong></td>
</tr><tr><td style="font-size:14px;" width="167" height="23">Client.ConnectionId</td>
<td style="font-size:14px;" width="276" height="23">到RPC Server对象连接的标识</td>
</tr><tr><td style="font-size:14px;" width="167" height="23">Client.Call</td>
<td style="font-size:14px;" width="276" height="23">Call调用信息。</td>
</tr><tr><td style="font-size:14px;" width="167" height="23">Client.ParallelResults</td>
<td style="font-size:14px;" width="276" height="23">Call响应。</td>
</tr><tr><td style="font-size:14px;" width="167" height="56">RPC.Invoker</td>
<td style="font-size:14px;" width="276" height="56">对InvocationHandler的实现，提供invoke方法，实现RPC Client对RPC Server对象的调用。</td>
</tr><tr><td style="font-size:14px;" width="167" height="56">RPC.Invocation</td>
<td style="font-size:14px;" width="276" height="56">用来序列化和反序列化RPC Client的调用信息。（主要应用JAVA的反射机制和InputStream/OutputStream）</td>
</tr></tbody></table></div>
<div> </div>
<div> 
<div class="O">一些细节</div>
<div class="O">
<div class="O">
<div>
<span lang="en-us" xml:lang="en-us">1.</span> 根据 <span lang="en-us" xml:lang="en-us">RPC Server</span> 的 <span lang="en-us" xml:lang="en-us">IP</span> 与 <span lang="en-us" xml:lang="en-us">PORT</span> 从连接池中拿 <span lang="en-us" xml:lang="en-us">Con</span>
</div>
<div>
<span lang="en-us" xml:lang="en-us">2.</span> 如果为空，新建连接放到连接池</div>
<div>
<span lang="en-us" xml:lang="en-us">3.</span> 创建 <span lang="en-us" xml:lang="en-us">Socket</span> 建立到 <span lang="en-us" xml:lang="en-us">Server</span> 的连接</div>
<div>
<span lang="en-us" xml:lang="en-us">4.</span> 创建输入输出流对象</div>
<div>
<span lang="en-us" xml:lang="en-us">5.</span> 将序列化的参数发到服务端</div>
<div>
<span lang="en-us" xml:lang="en-us">6.</span> 等待服务端响应</div>
<div> 
<div class="O">
<div>•客户端发起的RPC调用是同步的，而服务端处理RPC调用是异步的。客户端调用线程以阻塞同步的方式发起RPC连接及RPC调用，将参数等信息发送给Listener，然后等待Connection接收响应返回。</div>
<div>•Listener负责接收RPC连接和RPC数据，当一个Call的数据接收完后，组装成Call，并将Call放入由Handler提供的Call队列中。</div>
<div>•Handler线程监听Call队列，如果Call队列不为空，则按FIFO方式取出Call，并转为实际调用，以非阻塞方式将响应发回给Connection，未发送完毕的响应交给Responder处理。</div>
<div> </div>
<div>
<div class="O">
<div><span class="bold" style="font-weight:bold;">第三部分：使用Hadoop RPC</span></div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;">流程</span></div>
<div class="O">
<div>•实现VersionedProtocol</div>
<div>•继承VersionedProtocol ，定义Server(即NameNode)</div>
<div>•实现Clinet(即DataNode)</div>
<div>  </div>
</div>
<div class="O"><span class="bold" style="font-weight:bold;">实现<span lang="en-us" xml:lang="en-us">VersionedProtocol </span></span></div>
</div>
</div>
<div class="O">
<div>
<span lang="en-us" xml:lang="en-us">public interface </span><span lang="en-us" xml:lang="en-us">RPC</span> <span lang="en-us" xml:lang="en-us">Protocol</span> <span lang="en-us" xml:lang="en-us">Test</span> <span lang="en-us" xml:lang="en-us">extends VersionedProtocol {<br></span><span lang="en-us" xml:lang="en-us">    public Text println(Text t);<br></span><span lang="en-us" xml:lang="en-us">}</span>
</div>
<div> </div>
<div>
<br>  转载请注明出处【 <a href="http://sishuok.com/forum/blogPost/list/0/5968.html" rel="nofollow">http://sishuok.com/forum/blogPost/list/0/5968.html</a>】</div>
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