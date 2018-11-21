---
layout:     post
title:      使用HIVE的WEB界面：HWI
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h2 style="font-family:georgia, verdana, arial, sans-serif;color:rgb(153,51,0);text-transform:uppercase;font-size:.8em;">
<a id="cb_post_title_url" href="http://www.cnblogs.com/gpcuster/archive/2010/02/25/1673480.html" rel="nofollow" style="color:rgb(153,51,0);">使用HIVE的WEB界面：HWI</a></h2>
<div id="cnblogs_post_body" style="color:rgb(102,102,102);font-family:georgia, verdana, arial, sans-serif;font-size:14px;">
<p style="font-size:.9em;">HWI是Hive Web Interface的简称，是hive cli的一个web替换方案。</p>
<p style="font-size:.9em;">关于如何搭建Hive平台，可以参考：<a href="http://www.cnblogs.com/gpcuster/archive/2010/02/24/1672635.html" rel="nofollow" style="color:rgb(153,51,0);">搭建Hive平台</a></p>
<p style="font-size:.9em;">但是目前这个功能做的比较简陋，这篇文章我们一起来看看如何使用hive-0.4.1中自带的hwi来进行操作。</p>
<h2 style="color:rgb(153,51,0);text-transform:uppercase;font-size:.8em;">
打开HWI</h2>
<p style="font-size:.9em;">假设hive部署在10.20.151.7机器上，conf/hive-default.xml文件都是默认值，那么我们直接在浏览器中输入：<a title="http://10.20.151.7:9999/hwi/" href="http://10.20.151.7:9999/hwi/" rel="nofollow" style="color:rgb(153,51,0);">http://10.20.151.7:9999/hwi/</a> 就可以访问了。</p>
<p style="font-size:.9em;"><a href="http://images.cnblogs.com/cnblogs_com/gpcuster/WindowsLiveWriter/HivewebHWI_C37E/image_2.png" rel="nofollow" style="color:rgb(153,51,0);"><img title="image" alt="image" src="http://images.cnblogs.com/cnblogs_com/gpcuster/WindowsLiveWriter/HivewebHWI_C37E/image_thumb.png" border="0" width="559" height="472" style="border:0px;display:inline;"></a></p>
<h2 style="color:rgb(153,51,0);text-transform:uppercase;font-size:.8em;">
访问SCHEMA信息（BROWSE SCHEMA）</h2>
<p style="font-size:.9em;">我们在web界面点击Browsers Schema或者输入：<a title="http://10.20.151.7:9999/hwi/show_databases.jsp" href="http://10.20.151.7:9999/hwi/show_databases.jsp" rel="nofollow" style="color:rgb(153,51,0);">http://10.20.151.7:9999/hwi/show_databases.jsp</a>，就可以浏览了：</p>
<p style="font-size:.9em;"><a href="http://images.cnblogs.com/cnblogs_com/gpcuster/WindowsLiveWriter/HivewebHWI_C37E/image_4.png" rel="nofollow" style="color:rgb(153,51,0);"><img title="image" alt="image" src="http://images.cnblogs.com/cnblogs_com/gpcuster/WindowsLiveWriter/HivewebHWI_C37E/image_thumb_1.png" border="0" width="559" height="474" style="border:0px;display:inline;"></a></p>
<p style="font-size:.9em;">界面中显示的是当前可以使用的数据库信息，只包含一个数据库（default），我们再点击default，就可以看到default数据库中包含的所有表的信息了。</p>
<p style="font-size:.9em;"><a href="http://images.cnblogs.com/cnblogs_com/gpcuster/WindowsLiveWriter/HivewebHWI_C37E/image_6.png" rel="nofollow" style="color:rgb(153,51,0);"><img title="image" alt="image" src="http://images.cnblogs.com/cnblogs_com/gpcuster/WindowsLiveWriter/HivewebHWI_C37E/image_thumb_2.png" border="0" width="559" height="474" style="border:0px;display:inline;"></a></p>
<p style="font-size:.9em;">我们可以看到，有3个表的信息，继续点击表名，就可以看到更加详细的表结构信息了，如我们点击pokes：</p>
<p style="font-size:.9em;"><a href="http://images.cnblogs.com/cnblogs_com/gpcuster/WindowsLiveWriter/HivewebHWI_C37E/image_8.png" rel="nofollow" style="color:rgb(153,51,0);"><img title="image" alt="image" src="http://images.cnblogs.com/cnblogs_com/gpcuster/WindowsLiveWriter/HivewebHWI_C37E/image_thumb_3.png" border="0" width="559" height="474" style="border:0px;display:inline;"></a></p>
<p style="font-size:.9em;">这就是浏览schema信息的功能，用于替换cli中的show tables的功能。</p>
<h2 style="color:rgb(153,51,0);text-transform:uppercase;font-size:.8em;">
查看系统诊断信息（DIAGNOSTICS）</h2>
<p style="font-size:.9em;">我们点击<a href="http://10.20.151.7:9999/diagnostics.jsp" rel="nofollow" style="color:rgb(153,51,0);">Diagnostics</a>，就可以看到系统的相关诊断信息，如：</p>
<p style="font-size:.9em;"><a href="http://images.cnblogs.com/cnblogs_com/gpcuster/WindowsLiveWriter/HivewebHWI_C37E/image_10.png" rel="nofollow" style="color:rgb(153,51,0);"><img title="image" alt="image" src="http://images.cnblogs.com/cnblogs_com/gpcuster/WindowsLiveWriter/HivewebHWI_C37E/image_thumb_4.png" border="0" width="559" height="473" style="border:0px;display:inline;"></a></p>
<h2 style="color:rgb(153,51,0);text-transform:uppercase;font-size:.8em;">
用户认证（AUTHORIZE），创建会话（CREATE SESSION）与会话管理（LIST SESSIONS）</h2>
<p style="font-size:.9em;">在讲解这些功能之前，我们需要先了解一下用户认证与会话之间的关系。</p>
<p style="font-size:.9em;">在hwi中的用户认证需要输入用户名和用户组，如：</p>
<p style="font-size:.9em;"><a href="http://images.cnblogs.com/cnblogs_com/gpcuster/WindowsLiveWriter/HivewebHWI_C37E/image_12.png" rel="nofollow" style="color:rgb(153,51,0);"><img title="image" alt="image" src="http://images.cnblogs.com/cnblogs_com/gpcuster/WindowsLiveWriter/HivewebHWI_C37E/image_thumb_5.png" border="0" width="559" height="473" style="border:0px;display:inline;"></a></p>
<p style="font-size:.9em;">每一个用户认证（Authorize）信息对应着一组会话（session）。这些数据在hive重启后，session信息都会丢失。</p>
<p style="font-size:.9em;">在创建Hive查询之前，我们需要创建一个会话（session），点击<a href="http://10.20.151.7:9999/session_create.jsp" rel="nofollow" style="color:rgb(153,51,0);">Create Session</a>即可。</p>
<p style="font-size:.9em;">我们再点击<a href="http://10.20.151.7:9999/session_list.jsp" rel="nofollow" style="color:rgb(153,51,0);">List Sessions</a>，就可以看到该用户认证（Authorize）上所对应的所以的会话组了。</p>
<h2 style="color:rgb(153,51,0);text-transform:uppercase;font-size:.8em;">
执行查询</h2>
<p style="font-size:.9em;">绝大多数情况下，我们不需要设置用户认证（Authorize）信息，假设我们没有设置用户认证（Authorize）信息，然后创建了一个session为s1</p>
<p style="font-size:.9em;">点击<a href="http://10.20.151.7:9999/session_list.jsp" rel="nofollow" style="color:rgb(153,51,0);">List Sessions</a>后，可以看到如下界面：</p>
<p style="font-size:.9em;"><a href="http://images.cnblogs.com/cnblogs_com/gpcuster/WindowsLiveWriter/HivewebHWI_C37E/image_14.png" rel="nofollow" style="color:rgb(153,51,0);"><img title="image" alt="image" src="http://images.cnblogs.com/cnblogs_com/gpcuster/WindowsLiveWriter/HivewebHWI_C37E/image_thumb_6.png" border="0" width="559" height="473" style="border:0px;display:inline;"></a></p>
<p style="font-size:.9em;">点击<a href="http://10.20.151.7:9999/hwi/session_manage.jsp?sessionName=s1" rel="nofollow" style="color:rgb(153,51,0);">Manager</a>后，我们就进入到查询界面了：</p>
<p style="font-size:.9em;"><a href="http://images.cnblogs.com/cnblogs_com/gpcuster/WindowsLiveWriter/HivewebHWI_C37E/image_16.png" rel="nofollow" style="color:rgb(153,51,0);"><img title="image" alt="image" src="http://images.cnblogs.com/cnblogs_com/gpcuster/WindowsLiveWriter/HivewebHWI_C37E/image_thumb_7.png" border="0" width="559" height="432" style="border:0px;display:inline;"></a></p>
<p style="font-size:.9em;">我们可以直接在Query中填写查询语句，然后在Result中执行输出文件名称，同时将Start Query设置为Yes，如：</p>
<p style="font-size:.9em;"><a href="http://images.cnblogs.com/cnblogs_com/gpcuster/WindowsLiveWriter/HivewebHWI_C37E/image_20.png" rel="nofollow" style="color:rgb(153,51,0);"><img title="image" alt="image" src="http://images.cnblogs.com/cnblogs_com/gpcuster/WindowsLiveWriter/HivewebHWI_C37E/image_thumb_9.png" border="0" width="559" height="427" style="border:0px;display:inline;"></a></p>
<p style="font-size:.9em;"><span style="color:rgb(0,64,128);">注意，这里的查询语句与cli有一点点不同，查询语句最后没有分号（；）。</span></p>
<p style="font-size:.9em;">点击“提交查询内容”后，我们可以看到如下界面：</p>
<p style="font-size:.9em;"><a href="http://images.cnblogs.com/cnblogs_com/gpcuster/WindowsLiveWriter/HivewebHWI_C37E/image_22.png" rel="nofollow" style="color:rgb(153,51,0);"><img title="image" alt="image" src="http://images.cnblogs.com/cnblogs_com/gpcuster/WindowsLiveWriter/HivewebHWI_C37E/image_thumb_10.png" border="0" width="559" height="430" style="border:0px;display:inline;"></a></p>
<p style="font-size:.9em;">这个时候，我们可以点击View File查看结果信息：</p>
<p style="font-size:.9em;"><a href="http://images.cnblogs.com/cnblogs_com/gpcuster/WindowsLiveWriter/HivewebHWI_C37E/image_24.png" rel="nofollow" style="color:rgb(153,51,0);"><img title="image" alt="image" src="http://images.cnblogs.com/cnblogs_com/gpcuster/WindowsLiveWriter/HivewebHWI_C37E/image_thumb_11.png" border="0" width="559" height="427" style="border:0px;display:inline;"></a></p>
<p style="font-size:.9em;">这样，我们就完成查询了。</p>
<p style="font-size:.9em;">同时我们可以在hive的安装目录的更目录下找到这个s1_result文件。</p>
<h2 style="color:rgb(153,51,0);text-transform:uppercase;font-size:.8em;">
HWI与CLI对比</h2>
<p style="font-size:.9em;">如果使用过cli的朋友看了上面的介绍，一定会发现一个很严重的问题：执行的过程没有提示。我们不知道某一个查询执行是什么时候结束的。</p>
<p style="font-size:.9em;">总结一下HWI与CLI对比的优缺点：</p>
<p style="font-size:.9em;">优点：HWI支持浏览器的方式浏览，方便直观。</p>
<p style="font-size:.9em;">缺点：无执行过程提示。</p>
<p style="font-size:.9em;">我个人还是更倾向于使用cli的方式:)</p>
<p style="font-size:.9em;">转自：http://www.cnblogs.com/gpcuster/archive/2010/02/25/1673480.html</p>
</div>
            </div>
                </div>