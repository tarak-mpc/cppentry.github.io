---
layout:     post
title:      hadoop hdfs存储原理
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="font-family:Arial;font-size:14px;line-height:26px;">
来源url:<a href="http://www.36dsj.com/archives/41391" rel="nofollow" style="color:rgb(202,0,0);text-decoration:none;">http://www.36dsj.com/archives/41391</a></p>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
<br></p>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
</p>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
根据Maneesh Varshney的漫画改编，以简洁易懂的漫画形式讲解HDFS存储机制与运行原理。</p>
<h3 style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-weight:normal;line-height:1.1;color:rgb(68,68,68);font-size:16px;border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(238,238,238);">
<a name="t0" style="color:rgb(202,0,0);"></a>一、角色出演</h3>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<center style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
<img class="aligncenter size-full wp-image-41393" src="http://www.36dsj.com/wp-content/uploads/2016/02/765_1.png" alt="角色" width="640" height="360" style="border:0px;vertical-align:middle;display:block;"></center>
<span style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">如上图所示，HDFS存储相关角色与功能如下：</span><span style="font-family:Arial;font-size:14px;line-height:26px;"></span>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
Client：客户端，系统使用者，调用HDFS API操作文件;与NN交互获取文件元数据;与DN交互进行数据读写。</p>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
Namenode：元数据节点，是系统唯一的管理者。负责元数据的管理;与client交互进行提供元数据查询;分配数据存储节点等。</p>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
Datanode：数据存储节点，负责数据块的存储与冗余备份;执行数据块的读写操作等。</p>
<h3 style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-weight:normal;line-height:1.1;color:rgb(68,68,68);font-size:16px;border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(238,238,238);">
<a name="t1" style="color:rgb(202,0,0);"></a>二、写入数据</h3>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
1、发送写数据请求</p>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<center style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
<img class="aligncenter size-full wp-image-41394" src="http://www.36dsj.com/wp-content/uploads/2016/02/766_1.png" alt="发送写数据请求" width="640" height="360" style="border:0px;vertical-align:middle;display:block;"></center>
<span style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">HDFS中的存储单元是block。文件通常被分成64或128M一块的数据块进行存储。与普通文件系统不同的是，在HDFS中，如果一个文件大小小于一个数据块的大小，它是不需要占用整个数据块的存储空间的。</span><span style="font-family:Arial;font-size:14px;line-height:26px;"></span>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
2、文件切分</p>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<center style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
<img class="aligncenter size-full wp-image-41395" src="http://www.36dsj.com/wp-content/uploads/2016/02/767_1.png" alt="文件切割" width="640" height="360" style="border:0px;vertical-align:middle;display:block;"></center>
<span style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">3、DN分配</span><span style="font-family:Arial;font-size:14px;line-height:26px;"></span>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<center style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
<img class="aligncenter size-full wp-image-41396" src="http://www.36dsj.com/wp-content/uploads/2016/02/768_1.png" alt="DN分配1" width="640" height="360" style="border:0px;vertical-align:middle;display:block;"><img class="aligncenter size-full wp-image-41397" src="http://www.36dsj.com/wp-content/uploads/2016/02/769_1.png" alt="DN分配2" width="559" height="331" style="border:0px;vertical-align:middle;display:block;"></center>
<span style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">4、数据写入</span><span style="font-family:Arial;font-size:14px;line-height:26px;"></span>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<center style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
<img class="aligncenter size-full wp-image-41398" src="http://www.36dsj.com/wp-content/uploads/2016/02/770_1.png" alt="数据写入1" width="581" height="315" style="border:0px;vertical-align:middle;display:block;"><img class="aligncenter size-full wp-image-41399" src="http://www.36dsj.com/wp-content/uploads/2016/02/771_1.png" alt="数据写入2" width="620" height="267" style="border:0px;vertical-align:middle;display:block;"></center>
<span style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">5、完成写入</span><span style="font-family:Arial;font-size:14px;line-height:26px;"></span>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<center style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
<img class="aligncenter size-full wp-image-41400" src="http://www.36dsj.com/wp-content/uploads/2016/02/772_1.png" alt="完成写入1" width="543" height="338" style="border:0px;vertical-align:middle;display:block;"><img class="aligncenter size-full wp-image-41401" src="http://www.36dsj.com/wp-content/uploads/2016/02/773_1.png" alt="完成写入2" width="509" height="310" style="border:0px;vertical-align:middle;display:block;"><img class="aligncenter size-full wp-image-41402" src="http://www.36dsj.com/wp-content/uploads/2016/02/774_1.png" alt="完成写入3" width="550" height="321" style="border:0px;vertical-align:middle;display:block;"></center>
<span style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">6、角色定位</span><span style="font-family:Arial;font-size:14px;line-height:26px;"></span>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<center style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
<img class="aligncenter size-full wp-image-41403" src="http://www.36dsj.com/wp-content/uploads/2016/02/775_1.png" alt="角色定位" width="583" height="351" style="border:0px;vertical-align:middle;display:block;"></center>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<h3 style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-weight:normal;line-height:1.1;color:rgb(68,68,68);font-size:16px;border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(238,238,238);">
<a name="t2" style="color:rgb(202,0,0);"></a>三、HDFS读文件</h3>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
1、用户需求</p>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<center style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
<img class="aligncenter size-full wp-image-41404" src="http://www.36dsj.com/wp-content/uploads/2016/02/776_1.png" alt="用户需求" width="351" height="292" style="border:0px;vertical-align:middle;display:block;"></center>
<span style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">HDFS采用的是“一次写入多次读取”的文件访问模型。一个文件经过创建、写入和关闭之后就不需要改变。这一假设简化了数据一致性问题，并且使高吞吐量的数据访问成为可能。</span><span style="font-family:Arial;font-size:14px;line-height:26px;"></span>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
2、先联系元数据节点</p>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<center style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
<img class="aligncenter size-full wp-image-41405" src="http://www.36dsj.com/wp-content/uploads/2016/02/777_1.png" alt="联系元数据节点1" width="340" height="262" style="border:0px;vertical-align:middle;display:block;"><img class="aligncenter size-full wp-image-41406" src="http://www.36dsj.com/wp-content/uploads/2016/02/778_1.png" alt="联系元数据节点2" width="478" height="384" style="border:0px;vertical-align:middle;display:block;"><img class="aligncenter size-full wp-image-41407" src="http://www.36dsj.com/wp-content/uploads/2016/02/779_1.png" alt="联系元数据节点2" width="403" height="285" style="border:0px;vertical-align:middle;display:block;"></center>
<span style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">3、下载数据</span><span style="font-family:Arial;font-size:14px;line-height:26px;"></span>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<center style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
<img class="aligncenter size-full wp-image-41408" src="http://www.36dsj.com/wp-content/uploads/2016/02/780_1.png" alt="下载数据" width="417" height="291" style="border:0px;vertical-align:middle;display:block;"></center>
<span style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">前文提到在写数据过程中，数据存储已经按照客户端与DataNode节点之间的距离进行了排序，距客户端越近的DataNode节点被放在最前面，客户端会优先从本地读取该数据块。</span><span style="font-family:Arial;font-size:14px;line-height:26px;"></span>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
4、思考</p>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<center style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
<img class="aligncenter size-full wp-image-41409" src="http://www.36dsj.com/wp-content/uploads/2016/02/781_1.png" alt="思考" width="448" height="332" style="border:0px;vertical-align:middle;display:block;"></center>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<h3 style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-weight:normal;line-height:1.1;color:rgb(68,68,68);font-size:16px;border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(238,238,238);">
<a name="t3" style="color:rgb(202,0,0);"></a>四、HDFS容错机制——第一部分：故障类型及监测方法</h3>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
1、三类故障</p>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
(1)第一类：节点失败</p>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<center style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
<img class="aligncenter size-full wp-image-41410" src="http://www.36dsj.com/wp-content/uploads/2016/02/782_1.png" alt="节点失败" width="393" height="289" style="border:0px;vertical-align:middle;display:block;"></center>
<span style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">(2)第二类：网络故障</span><span style="font-family:Arial;font-size:14px;line-height:26px;"></span>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<center style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
<img class="aligncenter size-full wp-image-41411" src="http://www.36dsj.com/wp-content/uploads/2016/02/783_1.png" alt="网络故障" width="454" height="332" style="border:0px;vertical-align:middle;display:block;"></center>
<span style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">(3)第三类：数据损坏(脏数据)</span><span style="font-family:Arial;font-size:14px;line-height:26px;"></span>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<center style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
<img class="aligncenter size-full wp-image-41412" src="http://www.36dsj.com/wp-content/uploads/2016/02/784_1.png" alt="数据损坏" width="489" height="292" style="border:0px;vertical-align:middle;display:block;"></center>
<span style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">2、故障监测机制</span><span style="font-family:Arial;font-size:14px;line-height:26px;"></span>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
(1)节点失败监测机制</p>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<center style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
<img class="aligncenter size-full wp-image-41413" src="http://www.36dsj.com/wp-content/uploads/2016/02/785_1.png" alt="节点失败检测机制1" width="393" height="253" style="border:0px;vertical-align:middle;display:block;"><img class="aligncenter size-full wp-image-41414" src="http://www.36dsj.com/wp-content/uploads/2016/02/786_1.png" alt="节点失败检测机制2" width="450" height="281" style="border:0px;vertical-align:middle;display:block;"><img class="aligncenter size-full wp-image-41415" src="http://www.36dsj.com/wp-content/uploads/2016/02/787_1.png" alt="节点失败检测机制3" width="431" height="281" style="border:0px;vertical-align:middle;display:block;"></center>
<span style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">(2)通信故障监测机制</span><span style="font-family:Arial;font-size:14px;line-height:26px;"></span>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<center style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
<img class="aligncenter size-full wp-image-41416" src="http://www.36dsj.com/wp-content/uploads/2016/02/788_1.png" alt="通信故障检测机制" width="407" height="300" style="border:0px;vertical-align:middle;display:block;"></center>
<span style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">(3)数据错误监测机制</span><span style="font-family:Arial;font-size:14px;line-height:26px;"></span>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<center style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
<br><img class="aligncenter size-full wp-image-41417" src="http://www.36dsj.com/wp-content/uploads/2016/02/789_1.png" alt="数据错误检测机制1" width="464" height="342" style="border:0px;vertical-align:middle;display:block;"><img class="aligncenter size-full wp-image-41418" src="http://www.36dsj.com/wp-content/uploads/2016/02/790_1.png" alt="数据错误检测机制2" width="408" height="234" style="border:0px;vertical-align:middle;display:block;"><img class="aligncenter size-full wp-image-41419" src="http://www.36dsj.com/wp-content/uploads/2016/02/791_1.png" alt="数据错误检测机制3" width="469" height="334" style="border:0px;vertical-align:middle;display:block;"></center>
<span style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">3、回顾：心跳信息与数据块报告</span><span style="font-family:Arial;font-size:14px;line-height:26px;"></span>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<center style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
<img class="aligncenter size-full wp-image-41420" src="http://www.36dsj.com/wp-content/uploads/2016/02/792_1.png" alt="回顾：心跳信息与数据块报告" width="640" height="289" style="border:0px;vertical-align:middle;display:block;"></center>
<span style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">HDFS存储理念是以最少的钱买最烂的机器并实现最安全、难度高的分布式文件系统(高容错性低成本)，从上可以看出，HDFS认为机器故障是种常态，所以在设计时充分考虑到单个机器故障，单个磁盘故障，单个文件丢失等情况。</span><span style="font-family:Arial;font-size:14px;line-height:26px;"></span>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<h3 style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-weight:normal;line-height:1.1;color:rgb(68,68,68);font-size:16px;border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(238,238,238);">
<a name="t4" style="color:rgb(202,0,0);"></a>五、容错第二部分：读写容错</h3>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
1、写容错</p>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<center style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
<img class="aligncenter wp-image-41421 size-full" src="http://www.36dsj.com/wp-content/uploads/2016/02/793_1.png" alt="写容错1" width="621" height="348" style="border:0px;vertical-align:middle;display:block;"><img class="aligncenter wp-image-41422 size-full" src="http://www.36dsj.com/wp-content/uploads/2016/02/794_1.png" alt="写容错2" width="603" height="320" style="border:0px;vertical-align:middle;display:block;"><img class="aligncenter wp-image-41423 size-full" src="http://www.36dsj.com/wp-content/uploads/2016/02/795_1.png" alt="写容错3" width="502" height="315" style="border:0px;vertical-align:middle;display:block;"><img class="aligncenter wp-image-41424 size-full" src="http://www.36dsj.com/wp-content/uploads/2016/02/796_1.png" alt="写容错4" width="501" height="338" style="border:0px;vertical-align:middle;display:block;"></center>
<span style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">2、读容错</span><span style="font-family:Arial;font-size:14px;line-height:26px;"></span>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<center style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
<img class="aligncenter size-full wp-image-41425" src="http://www.36dsj.com/wp-content/uploads/2016/02/797_1.png" alt="读容错" width="453" height="298" style="border:0px;vertical-align:middle;display:block;"><img class="aligncenter size-full wp-image-41426" src="http://www.36dsj.com/wp-content/uploads/2016/02/798_1.png" alt="读容错2" width="522" height="304" style="border:0px;vertical-align:middle;display:block;"></center>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<h3 style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-weight:normal;line-height:1.1;color:rgb(68,68,68);font-size:16px;border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(238,238,238);">
<a name="t5" style="color:rgb(202,0,0);"></a>六、容错第三部分：数据节点(DN)失效</h3>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<center style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
<img class="aligncenter size-full wp-image-41427" src="http://www.36dsj.com/wp-content/uploads/2016/02/799_1.png" alt="数据节点(DN)失效1" width="443" height="363" style="border:0px;vertical-align:middle;display:block;"><img class="aligncenter size-full wp-image-41428" src="http://www.36dsj.com/wp-content/uploads/2016/02/800_1.png" alt="数据节点(DN)失效2" width="453" height="304" style="border:0px;vertical-align:middle;display:block;"><img class="aligncenter size-full wp-image-41429" src="http://www.36dsj.com/wp-content/uploads/2016/02/811_1.png" alt="数据节点(DN)失效3" width="309" height="368" style="border:0px;vertical-align:middle;display:block;"><img class="aligncenter size-full wp-image-41430" src="http://www.36dsj.com/wp-content/uploads/2016/02/812_1.png" alt="数据节点(DN)失效4" width="310" height="371" style="border:0px;vertical-align:middle;display:block;"><img class="aligncenter size-full wp-image-41431" src="http://www.36dsj.com/wp-content/uploads/2016/02/813_1.png" alt="数据节点(DN)失效5" width="462" height="329" style="border:0px;vertical-align:middle;display:block;"><img class="aligncenter size-full wp-image-41432" src="http://www.36dsj.com/wp-content/uploads/2016/02/814_1.png" alt="数据节点(DN)失效6" width="418" height="332" style="border:0px;vertical-align:middle;display:block;"></center>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<h3 style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-weight:normal;line-height:1.1;color:rgb(68,68,68);font-size:16px;border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(238,238,238);">
<a name="t6" style="color:rgb(202,0,0);"></a>七、备份规则</h3>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<center style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
<img class="aligncenter size-full wp-image-41433" src="http://www.36dsj.com/wp-content/uploads/2016/02/815_1.png" alt="备份规则" width="444" height="221" style="border:0px;vertical-align:middle;display:block;"></center>
<span style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">1、机架与数据节点</span><span style="font-family:Arial;font-size:14px;line-height:26px;"></span>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<center style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
<img class="aligncenter size-full wp-image-41434" src="http://www.36dsj.com/wp-content/uploads/2016/02/816_1.png" alt="1、机架与数据节点" width="452" height="279" style="border:0px;vertical-align:middle;display:block;"></center>
<span style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">2、副本放置策略</span><span style="font-family:Arial;font-size:14px;line-height:26px;"></span>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<center style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
<img class="aligncenter size-full wp-image-41435" src="http://www.36dsj.com/wp-content/uploads/2016/02/817_1.png" alt="2、副本放置策略" width="454" height="215" style="border:0px;vertical-align:middle;display:block;"></center>
<span style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">数据块的第一个副本优先放在写入数据块的客户端所在的节点上，但是如果这个客户端上的数据节点空间不足或者是当前负载过重，则应该从该数据节点所在的机架中选择一个合适的数据节点作为本地节点。</span><span style="font-family:Arial;font-size:14px;line-height:26px;"></span>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
如果客户端上没有一个数据节点的话，则从整个集群中随机选择一个合适的数据节点作为此时这个数据块的本地节点。</p>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<center style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
<img class="aligncenter size-full wp-image-41436" src="http://www.36dsj.com/wp-content/uploads/2016/02/818_1.png" alt="数字节点放置" width="485" height="283" style="border:0px;vertical-align:middle;display:block;"></center>
<span style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">HDFS的存放策略是将一个副本存放在本地机架节点上，另外两个副本放在不同机架的不同节点上。</span><span style="font-family:Arial;font-size:14px;line-height:26px;"></span>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
这样集群可在完全失去某一机架的情况下还能存活。同时，这种策略减少了机架间的数据传输，提高了写操作的效率，因为数据块只存放在两个不同的机架上，减少了读取数据时需要的网络传输总带宽。这样在一定程度上兼顾了数据安全和网络传输的开销。</p>
<p style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
</p>
<center style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
<img class="aligncenter size-full wp-image-41437" src="http://www.36dsj.com/wp-content/uploads/2016/02/819_1.png" alt="DN节点选取" width="461" height="265" style="border:0px;vertical-align:middle;display:block;"><img class="aligncenter size-full wp-image-41438" src="http://www.36dsj.com/wp-content/uploads/2016/02/820_1.png" alt="细则" width="414" height="329" style="border:0px;vertical-align:middle;display:block;">via:京东大数据
                                                                                                                                        </center>
<center style="color:rgb(102,102,102);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:16px;line-height:27px;">
End. </center>
<div><br></div>
            </div>
                </div>