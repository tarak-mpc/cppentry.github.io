---
layout:     post
title:      HBase in Action-第二部分Advanced concepts-(3)非Java客户端【REST、Thrift】
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1 style="color:rgb(54,46,43);font-family:Arial;line-height:26px;">
<br></h1>
<h1 style="color:rgb(54,46,43);font-family:Arial;line-height:26px;">
HBase Shell</h1>
<p style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">
HBase shell使用起来最方便，进入HBase shell控制台即可使用。</p>
<p style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">
</p>
<div class="dp-highlighter bg_plain" style="font-family:Consolas, 'Courier New', Courier, mono, serif;width:693px;overflow:auto;color:rgb(54,46,43);line-height:26px;background-color:rgb(231,229,220);">
<div class="bar">
<div class="tools" style="font-size:9px;line-height:normal;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;color:#C0C0C0;border-left-width:3px;border-left-style:solid;border-left-color:rgb(108,226,108);background-color:rgb(248,248,248);">
<strong>[plain]</strong> <a href="http://blog.csdn.net/idontwantobe/article/details/36644785#" rel="nofollow" class="ViewSource" title="view plain" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;background-color:inherit;">view
 plain</a><a href="http://blog.csdn.net/idontwantobe/article/details/36644785#" rel="nofollow" class="CopyToClipboard" title="copy" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;background-color:inherit;">copy</a><a href="https://code.csdn.net/snippets/415188" rel="nofollow" title="在CODE上查看代码片" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;background-color:inherit;"><img src="https://code.csdn.net/assets/CODE_ico.png" width="12" height="12" alt="在CODE上查看代码片" style="border:none;"></a><a href="https://code.csdn.net/snippets/415188/fork" rel="nofollow" title="派生到我的代码片" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;background-color:inherit;"><img src="https://code.csdn.net/assets/ico_fork.svg" width="12" height="12" alt="派生到我的代码片" style="border:none;"></a>
<div style="width:18px;z-index:99;">
</div>
</div>
</div>
<ol start="1" style="border:none;color:rgb(92,92,92);background-color:rgb(255,255,255);"><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span style="border:none;background-color:inherit;">$ $HBASE_HOME/bin/hbase shell  </span></span></li></ol></div>
<span style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">常见操作有</span><span style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">create</span><span style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">(创建表)/</span><span style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">put</span><span style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">(插入或更新数据)/</span><span style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">get</span><span style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">(根据rowkey查询)/</span><span style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">scan</span><span style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">(范围查询)/</span><span style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">delete</span><span style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">(删除列)/</span><span style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">deleteAll</span><span style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">(根据rowkey删除整行数据)/</span><span style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">disable</span><span style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">&amp;</span><span style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">drop</span><span style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">(禁用表之后再删除)。</span>
<p style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">
基于数据库的项目，往往会在某个目录下存储专门的sql脚本，记录每次迭代数据库变更；同理，HBase脚本也可以组织成文件，用相同的方式管理起来：</p>
<div class="dp-highlighter bg_plain" style="font-family:Consolas, 'Courier New', Courier, mono, serif;width:693px;overflow:auto;color:rgb(54,46,43);line-height:26px;background-color:rgb(231,229,220);">
<div class="bar">
<div class="tools" style="font-size:9px;line-height:normal;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;color:#C0C0C0;border-left-width:3px;border-left-style:solid;border-left-color:rgb(108,226,108);background-color:rgb(248,248,248);">
<strong>[plain]</strong> <a href="http://blog.csdn.net/idontwantobe/article/details/36644785#" rel="nofollow" class="ViewSource" title="view plain" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;background-color:inherit;">view
 plain</a><a href="http://blog.csdn.net/idontwantobe/article/details/36644785#" rel="nofollow" class="CopyToClipboard" title="copy" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;background-color:inherit;">copy</a><a href="https://code.csdn.net/snippets/415188" rel="nofollow" title="在CODE上查看代码片" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;background-color:inherit;"><img src="https://code.csdn.net/assets/CODE_ico.png" width="12" height="12" alt="在CODE上查看代码片" style="border:none;"></a><a href="https://code.csdn.net/snippets/415188/fork" rel="nofollow" title="派生到我的代码片" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;background-color:inherit;"><img src="https://code.csdn.net/assets/ico_fork.svg" width="12" height="12" alt="派生到我的代码片" style="border:none;"></a>
<div style="width:18px;z-index:99;">
</div>
</div>
</div>
<ol start="1" style="border:none;color:rgb(92,92,92);background-color:rgb(255,255,255);"><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span style="border:none;background-color:inherit;">#!/bin/sh  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">exec $HBASE_HOME/bin/hbase shell &lt;&lt;EOF  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">create 'twits',{NAME =&gt; 't', VERSIONS =&gt; 1}  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">EOF  </span></li></ol></div>
<p style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">
值得一提的是，HBase shell是用JRuby实现的，所以create语句语法类似于Ruby的函数调用。HBase同样提供了JRuby版client，简单强大。。</p>
<p style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">
<br></p>
<h1 style="color:rgb(54,46,43);font-family:Arial;line-height:26px;">
<a name="t1" style="color:rgb(106,57,6);"></a>REST</h1>
<p style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">
如图，REST Gateway Service以独立进程运行，它负责和HBase通信。为了避免REST服务器单点问题，提高吞吐量，可以启动多台REST服务器组成集群，但要注意的是，Scanner API是有状态的，需要进行资源分配，所以进行scan操作时，客户端要始终连接同一台REST服务器。</p>
<p style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">
<img src="https://img-blog.csdn.net/20140703125917187?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaWRvbnR3YW50b2Jl/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none;">                         
                                                         以后台进程启动REST服务。</p>
<div class="dp-highlighter bg_plain" style="font-family:Consolas, 'Courier New', Courier, mono, serif;width:693px;overflow:auto;color:rgb(54,46,43);line-height:26px;background-color:rgb(231,229,220);">
<div class="bar">
<div class="tools" style="font-size:9px;line-height:normal;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;color:#C0C0C0;border-left-width:3px;border-left-style:solid;border-left-color:rgb(108,226,108);background-color:rgb(248,248,248);">
<strong>[plain]</strong> <a href="http://blog.csdn.net/idontwantobe/article/details/36644785#" rel="nofollow" class="ViewSource" title="view plain" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;background-color:inherit;">view
 plain</a><a href="http://blog.csdn.net/idontwantobe/article/details/36644785#" rel="nofollow" class="CopyToClipboard" title="copy" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;background-color:inherit;">copy</a><a href="https://code.csdn.net/snippets/415188" rel="nofollow" title="在CODE上查看代码片" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;background-color:inherit;"><img src="https://code.csdn.net/assets/CODE_ico.png" width="12" height="12" alt="在CODE上查看代码片" style="border:none;"></a><a href="https://code.csdn.net/snippets/415188/fork" rel="nofollow" title="派生到我的代码片" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;background-color:inherit;"><img src="https://code.csdn.net/assets/ico_fork.svg" width="12" height="12" alt="派生到我的代码片" style="border:none;"></a>
<div style="width:18px;z-index:99;">
</div>
</div>
</div>
<ol start="1" style="border:none;color:rgb(92,92,92);background-color:rgb(255,255,255);"><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span style="border:none;background-color:inherit;">$ hbase-daemon.sh start rest -p 9999  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">starting rest, logging to logs/hbase-hbase-rest-ubuntu.out  </span></li></ol></div>
<span style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">REST服务支持多种响应格式，比如XML、JSON、Protobufs和文本。下面示例调用RESTAPI，更改用户密码。注意列名和值都需要使用Base64编码。</span>
<div class="dp-highlighter bg_php" style="font-family:Consolas, 'Courier New', Courier, mono, serif;width:693px;overflow:auto;color:rgb(54,46,43);line-height:26px;background-color:rgb(231,229,220);">
<div class="bar">
<div class="tools" style="font-size:9px;line-height:normal;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;color:#C0C0C0;border-left-width:3px;border-left-style:solid;border-left-color:rgb(108,226,108);background-color:rgb(248,248,248);">
<strong>[php]</strong> <a href="http://blog.csdn.net/idontwantobe/article/details/36644785#" rel="nofollow" class="ViewSource" title="view plain" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;background-color:inherit;">view
 plain</a><a href="http://blog.csdn.net/idontwantobe/article/details/36644785#" rel="nofollow" class="CopyToClipboard" title="copy" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;background-color:inherit;">copy</a><a href="https://code.csdn.net/snippets/415188" rel="nofollow" title="在CODE上查看代码片" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;background-color:inherit;"><img src="https://code.csdn.net/assets/CODE_ico.png" width="12" height="12" alt="在CODE上查看代码片" style="border:none;"></a><a href="https://code.csdn.net/snippets/415188/fork" rel="nofollow" title="派生到我的代码片" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;background-color:inherit;"><img src="https://code.csdn.net/assets/ico_fork.svg" width="12" height="12" alt="派生到我的代码片" style="border:none;"></a>
<div style="width:18px;z-index:99;">
</div>
</div>
</div>
<ol start="1" class="dp-c" style="border:none;color:rgb(92,92,92);background-color:rgb(255,255,255);"><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="vars" style="border:none;color:rgb(221,0,0);background-color:inherit;">$curl</span><span style="border:none;background-color:inherit;"> -XPUT \  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">-H<span class="string" style="border:none;color:#0000FF;background-color:inherit;">"Content-Type: application/json"</span><span style="border:none;background-color:inherit;"> \  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">http:<span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">//localhost:9999/users/TheRealMT/info:password\</span><span style="border:none;background-color:inherit;">  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">-d'{  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  <span class="string" style="border:none;color:#0000FF;background-color:inherit;">"Row"</span><span style="border:none;background-color:inherit;">: [  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">    {  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">      <span class="string" style="border:none;color:#0000FF;background-color:inherit;">"Cell"</span><span style="border:none;background-color:inherit;">: [  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">      {  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        <span class="string" style="border:none;color:#0000FF;background-color:inherit;">"column"</span><span style="border:none;background-color:inherit;">:</span><span class="string" style="border:none;color:#0000FF;background-color:inherit;">"aW5mbzpwYXNzd29yZA=="</span><span style="border:none;background-color:inherit;">,  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">        <span class="string" style="border:none;color:#0000FF;background-color:inherit;">"$"</span><span style="border:none;background-color:inherit;">: </span><span class="string" style="border:none;color:#0000FF;background-color:inherit;">"NzBOQHJJIE4wIDcwdDBSMA=="</span><span style="border:none;background-color:inherit;">  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">      }  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">      ],  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">      <span class="string" style="border:none;color:#0000FF;background-color:inherit;">"key"</span><span style="border:none;background-color:inherit;">:</span><span class="string" style="border:none;color:#0000FF;background-color:inherit;">"VGhlUmVhbE1U"</span><span style="border:none;background-color:inherit;">  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">    }  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  ]  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">}'  </span></li></ol></div>
<h1 style="color:rgb(54,46,43);font-family:Arial;line-height:26px;">
<a name="t2" style="color:rgb(106,57,6);"></a>Thrift</h1>
<p style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">
对于非Java码农，最方便的还是使用跨语言RPC调用框架Thrift。HBase提供了Thrift IDL服务描述接口，支持多达14种不同语言，并实现了相应的后台服务。</p>
<p style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">
Thrift服务的架构与REST服务类似。Thrift客户端在一次会话内，都和一台服务器保持连接，所以建立Thrift Gateway集群，进行负载均衡要方便一些。</p>
<p style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">
<img src="https://img-blog.csdn.net/20140703130339781?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaWRvbnR3YW50b2Jl/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none;"><br></p>
<p style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">
</p>
<p style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">
首先启动Thrift服务：</p>
<div class="dp-highlighter bg_plain" style="font-family:Consolas, 'Courier New', Courier, mono, serif;width:693px;overflow:auto;color:rgb(54,46,43);line-height:26px;background-color:rgb(231,229,220);">
<div class="bar">
<div class="tools" style="font-size:9px;line-height:normal;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;color:#C0C0C0;border-left-width:3px;border-left-style:solid;border-left-color:rgb(108,226,108);background-color:rgb(248,248,248);">
<strong>[plain]</strong> <a href="http://blog.csdn.net/idontwantobe/article/details/36644785#" rel="nofollow" class="ViewSource" title="view plain" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;background-color:inherit;">view
 plain</a><a href="http://blog.csdn.net/idontwantobe/article/details/36644785#" rel="nofollow" class="CopyToClipboard" title="copy" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;background-color:inherit;">copy</a><a href="https://code.csdn.net/snippets/415188" rel="nofollow" title="在CODE上查看代码片" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;background-color:inherit;"><img src="https://code.csdn.net/assets/CODE_ico.png" width="12" height="12" alt="在CODE上查看代码片" style="border:none;"></a><a href="https://code.csdn.net/snippets/415188/fork" rel="nofollow" title="派生到我的代码片" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;background-color:inherit;"><img src="https://code.csdn.net/assets/ico_fork.svg" width="12" height="12" alt="派生到我的代码片" style="border:none;"></a>
<div style="width:18px;z-index:99;">
</div>
</div>
</div>
<ol start="1" style="border:none;color:rgb(92,92,92);background-color:rgb(255,255,255);"><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span style="border:none;background-color:inherit;">$ hbase thrift start  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">...  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">ThriftServer:starting HBase ThreadPool Thrift server on /0.0.0.0:9090  </span></li></ol></div>
<span style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">客户端根据不同语言进行代码生成，以Python为例：</span>
<div class="dp-highlighter bg_plain" style="font-family:Consolas, 'Courier New', Courier, mono, serif;width:693px;overflow:auto;color:rgb(54,46,43);line-height:26px;background-color:rgb(231,229,220);">
<div class="bar">
<div class="tools" style="font-size:9px;line-height:normal;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;color:#C0C0C0;border-left-width:3px;border-left-style:solid;border-left-color:rgb(108,226,108);background-color:rgb(248,248,248);">
<strong>[plain]</strong> <a href="http://blog.csdn.net/idontwantobe/article/details/36644785#" rel="nofollow" class="ViewSource" title="view plain" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;background-color:inherit;">view
 plain</a><a href="http://blog.csdn.net/idontwantobe/article/details/36644785#" rel="nofollow" class="CopyToClipboard" title="copy" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;background-color:inherit;">copy</a><a href="https://code.csdn.net/snippets/415188" rel="nofollow" title="在CODE上查看代码片" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;background-color:inherit;"><img src="https://code.csdn.net/assets/CODE_ico.png" width="12" height="12" alt="在CODE上查看代码片" style="border:none;"></a><a href="https://code.csdn.net/snippets/415188/fork" rel="nofollow" title="派生到我的代码片" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;background-color:inherit;"><img src="https://code.csdn.net/assets/ico_fork.svg" width="12" height="12" alt="派生到我的代码片" style="border:none;"></a>
<div style="width:18px;z-index:99;">
</div>
</div>
</div>
<ol start="1" style="border:none;color:rgb(92,92,92);background-color:rgb(255,255,255);"><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span style="border:none;background-color:inherit;">$ thrift -gen py../hbase-0.92.1/src/main/resources/org/apache/hadoop/hbase/  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">thrift/Hbase.thrift  </span></li></ol></div>
<p style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">
各个语言的API接口都和JAVA客户端基本一致，使用方法就不多说了。</p>
<p style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">
<br></p>
<h1 style="color:rgb(54,46,43);font-family:Arial;line-height:26px;">
<a name="t3" style="color:rgb(106,57,6);"></a>Asynchbase：异步客户端</h1>
<p style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">
原生的JAVA客户端完全是同步的。Asynchbase是全新实现的异步客户端，Asynchbase的最主要用户是OpenTSDB（存储层使用HBase的<span style="color:rgb(51,51,51);">开源大规模集群监控系统</span>，请参考<span style="color:rgb(51,51,51);"><a href="http://opentsdb.net/" rel="nofollow" style="color:rgb(106,57,6);text-decoration:none;">http://opentsdb.net/</a></span><span style="color:rgb(51,51,51);">）。</span></p>
<p style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">
<span style="color:rgb(51,51,51);">下图异步请求过程参考</span><span style="color:rgb(51,51,51);">opentsdb</span><span style="color:rgb(51,51,51);">：</span></p>
<p style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">
<span style="color:rgb(51,51,51);"><img src="https://img-blog.csdn.net/20140703130339859?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaWRvbnR3YW50b2Jl/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none;"><br></span></p>
<p style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">
</p>
<p style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">
</p>
<p style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">
</p>
<ol style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;"><li>客户端发送异步请求。</li><li>获得异步结果Deferred（类似于Java的Future接口）。</li><li>注册回调Callbacks到Deferred，完成客户端逻辑所在线程返回。</li><li><span style="color:rgb(51,51,51);"> </span>服务端异步执行数据请求，完成后触发回调。</li></ol><p style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">
<span style="color:rgb(51,51,51);">Asynchbase</span><span style="color:rgb(51,51,51);">具有以下几个特点：</span></p>
<ol style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;"><li><span style="color:rgb(51,51,51);">异步</span><span style="color:rgb(51,51,51);">&amp;</span><span style="color:rgb(51,51,51);">非阻塞</span><span style="color:rgb(51,51,51);">&amp;</span><span style="color:rgb(51,51,51);">线程安全，所以特别适合大量并发写操作场景，提供更高的吞吐量</span></li><li><span style="color:rgb(51,51,51);">多版本支持，</span><span style="color:rgb(51,51,51);">Asynchbase</span><span style="color:rgb(51,51,51);">是完全重新实现的接口，所以不像原生客户端，需要和对应版本的集群配套使用。</span></li><li><span style="color:rgb(51,51,51);">API</span><span style="color:rgb(51,51,51);">方面最强大的是</span><span style="color:rgb(51,51,51);">Callback</span>回调链，链上的前一个callback将返回结果作为参数传递给下一个callback，进行一连串的异步操作组合。</li></ol><div><span style="font-family:Arial;color:#362e2b;"><span style="font-size:14px;line-height:26px;">转自：http://blog.csdn.net/idontwantobe/article/details/36644785，如有侵权，联系删除</span></span></div>
            </div>
                </div>