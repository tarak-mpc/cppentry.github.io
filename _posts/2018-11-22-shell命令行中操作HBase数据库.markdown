---
layout:     post
title:      shell命令行中操作HBase数据库
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div class="Blog_tit4 Blog_tit5" style="border-bottom-width:0px;color:rgb(86,86,86);font-family:'宋体', Arial;line-height:12px;">
<br></div>
<div class="Blog_con2" style="color:rgb(102,102,102);line-height:26px;overflow:hidden;font-family:'宋体', Arial;">
<div class="Blog_con3" style="line-height:22px;">
<p style="color:rgb(142,3,0);">
</p>
</div>
<div class="Blog_wz1">
<h3>Shell控制</h3>
 
<p>
进入到shell命令行界面，执行hbase命令，并附加shell关键字：</p>
 
<div style="border:1px solid rgb(204,204,204);background-color:rgb(246,246,246);">
<ul style="list-style:none;"><li>
 
</li></ul><p>
[grid@hdnode3 ~]$ hbase shell</p>
 
<p>
HBase Shell; enter ¨help¨ for list of supported commands.</p>
 
<p>
Type "exit" to leave the HBase Shell</p>
 
<p>
Version 0.90.5, r1212209, Fri Dec  9 05:40:36 UTC 2011</p>
 
<p>
</p>
  hbase(main):001:0&gt;  </div>
 
<p>
虽然成功登录进去了，可是我们也不知道现在能做什么，也不了解SHELL下都有哪些命令。这个时候，我们可以选择，去看官方文档中的说明，或者，敲个help上去看看。</p>
 
<div style="border:1px solid rgb(204,204,204);background-color:rgb(246,246,246);">
<ul style="list-style:none;"><li>
 
</li></ul><p>
hbase(main):002:0&gt; help</p>
 
<p>
..................</p>
 
<p>
..................</p>
 
<p>
COMMAND GROUPS:</p>
 
<p>
 Group name: general</p>
 
<p>
 Commands: status, version</p>
 
<p>
</p>
 
<p>
 Group name: ddl</p>
 
<p>
 Commands: alter, create, describe, disable, drop, enable, exists, is_disabled, is_enabled, list</p>
 
<p>
</p>
 
<p>
 Group name: dml</p>
 
<p>
 Commands: count, delete, deleteall, get, get_counter, incr, put, scan, truncate</p>
 
<p>
</p>
 
<p>
 Group name: tools</p>
 
<p>
 Commands: assign, balance_switch, balancer, close_region, compact, flush, major_compact, move, split, unassign, zk_dump</p>
 
<p>
</p>
 
<p>
 Group name: replication</p>
 
<p>
 Commands: add_peer, disable_peer, enable_peer, remove_peer, start_replication, stop_replication</p>
 
<p>
..................</p>
  ..................</div>
 
<p>
帮助信息果然有帮助，通过输出的信息，我们大致了解能够做什么。可以看到hbase中也是分有ddl/dml这类语句，此外还有与复制相关的，与管理相关的命令等等。</p>
 
<p>
先来试试通用(general)命令，查询状态：</p>
 
<div style="border:1px solid rgb(204,204,204);background-color:rgb(246,246,246);">
<ul style="list-style:none;"><li>
 
</li></ul><p>
hbase(main):003:0&gt; status</p>
  5 servers, 0 dead, 0.4000 average load</div>
 
<p>
查询版本：</p>
 
<div style="border:1px solid rgb(204,204,204);background-color:rgb(246,246,246);">
<ul style="list-style:none;"><li>
 
</li></ul><p>
hbase(main):004:0&gt; version</p>
  0.90.5, r1212209, Fri Dec  9 05:40:36 UTC 2011</div>
 
<p>
接下来重点项，DDL和DML(想不到HBase也分了DML/DDL语句)。HBase中没有库的概念，做为BigTable的山寨产品，尽管没名山寨到名字，但山寨到了精髓，从设计上来说，它也不需要分库，甚至不需要分表，所有数据放到同一张表中也是可以的，这就是真正的BigTable嘛。</p>
 
<p>
创建表对象：</p>
 
<div style="border:1px solid rgb(204,204,204);background-color:rgb(246,246,246);">
<ul style="list-style:none;"><li>
 
</li></ul><p>
hbase(main):005:0&gt; create ¨t¨,¨t_id¨,¨t_vl¨</p>
  0 row(s) in 2.3490 seconds</div>
 
<p>
HBase中创建对象的语法比较灵活，前面这个示例是简写法，其功能等效于完整写法，"hbase&gt; create ¨t¨, {NAME =&gt; ¨t_id¨}, {NAME =&gt; ¨t_vl¨}"，第一个参数用于指定表名，后面跟的所有参数都是列族的名称。每个表的列族需要在表创建时定义好(尽管后期也可以修改，但最好一开始就定义好)，从这个角度来看，HBase中的对象是结构化的。</p>
 
<p>
查看表对象：</p>
 
<div style="border:1px solid rgb(204,204,204);background-color:rgb(246,246,246);">
<ul style="list-style:none;"><li>
 
</li></ul><p>
hbase(main):006:0&gt; list</p>
 
<p>
TABLE                                                                                                                                                                    </p>
 
<p>
t                                                                                                                                                                        </p>
 
<p>
1 row(s) in 0.0080 seconds</p>
 
<p>
hbase(main):018:0&gt; describe ¨t¨</p>
 
<p>
DESCRIPTION                                                                                                  ENABLED                                                    </p>
 
<p>
{NAME =&gt; ¨t¨, FAMILIES =&gt; [{NAME =&gt; ¨t_id¨, BLOOMFILTER =&gt; ¨NONE¨, REPLICATION_SCOPE =&gt; ¨0¨, COMPRESSION =&gt; true                                                        </p>
 
<p>
 ¨NONE¨, VERSIONS =&gt; ¨3¨, TTL =&gt; ¨2147483647¨, BLOCKSIZE =&gt; ¨65536¨, IN_MEMORY =&gt; ¨false¨, BLOCKCACHE =&gt; ¨t                                                            </p>
 
<p>
rue¨}, {NAME =&gt; ¨t_vl¨, BLOOMFILTER =&gt; ¨NONE¨, REPLICATION_SCOPE =&gt; ¨0¨, COMPRESSION =&gt; ¨NONE¨, VERSIONS =&gt;                                                            </p>
 
<p>
 ¨3¨, TTL =&gt; ¨2147483647¨, BLOCKSIZE =&gt; ¨65536¨, IN_MEMORY =&gt; ¨false¨, BLOCKCACHE =&gt; ¨true¨}]}                                                                          </p>
  1 row(s) in 0.0100 seconds</div>
 
<p>
输出的格式也是JSON串的形式，从中可以看到保留的版本数，TTL号(Time to Live，保留时间)，列的定义，块大小等等。</p>
 
<p>
修改表对象，修改(含删除)前必须首先禁用对象，执行修改命令成功后，再启用对象。</p>
 
<p>
禁用对象：</p>
 
<div style="border:1px solid rgb(204,204,204);background-color:rgb(246,246,246);">
<ul style="list-style:none;"><li>
 
</li></ul><p>
hbase(main):004:0&gt; disable ¨t¨</p>
  0 row(s) in 2.0430 seconds</div>
 
<p>
判断当前表对象启用或禁用：</p>
 
<div style="border:1px solid rgb(204,204,204);background-color:rgb(246,246,246);">
<ul style="list-style:none;"><li>
 
</li></ul><p>
hbase(main):007:0&gt; is_enabled ¨t¨</p>
 
<p>
false                                                                                                                                                                    </p>
 
<p>
0 row(s) in 0.0040 seconds</p>
 
<p>
</p>
 
<p>
hbase(main):008:0&gt; is_disabled ¨t¨</p>
 
<p>
true                                                                                                                                                                    </p>
  0 row(s) in 0.0040 seconds</div>
 
<p>
修改表对象，增加一个列族：</p>
 
<div style="border:1px solid rgb(204,204,204);background-color:rgb(246,246,246);">
<ul style="list-style:none;"><li>
 
</li></ul><p>
hbase(main):021:0&gt; alter ¨t¨, {NAME =&gt; ¨t_info¨, VERSIONS =&gt; 3}      </p>
 
<p>
0 row(s) in 0.0360 seconds</p>
 
<p>
</p>
 
<p>
hbase(main):023:0&gt; enable ¨t¨</p>
  0 row(s) in 2.0250 seconds</div>
 
<p>
插入记录：</p>
 
<div style="border:1px solid rgb(204,204,204);background-color:rgb(246,246,246);">
<ul style="list-style:none;"><li>
 
</li></ul><p>
hbase(main):025:0&gt; put ¨t¨,¨10001¨,¨t_vl:name¨,¨jss¨</p>
 
<p>
0 row(s) in 0.0060 seconds</p>
 
<p>
</p>
 
<p>
hbase(main):026:0&gt; put ¨t¨,¨10001¨,¨t_vl:age¨,¨99¨</p>
 
<p>
0 row(s) in 0.0070 seconds</p>
 
<p>
</p>
 
<p>
hbase(main):027:0&gt; put ¨t¨,¨10001¨,¨t_info:general¨,¨his fullname is junsanis!¨  </p>
  0 row(s) in 0.0040 seconds</div>
 
<p>
记录获取：</p>
 
<div style="border:1px solid rgb(204,204,204);background-color:rgb(246,246,246);">
<ul style="list-style:none;"><li>
 
</li></ul><p>
hbase(main):028:0&gt; get ¨t¨,¨10001¨</p>
 
<p>
COLUMN                                      CELL                                                                                                                        </p>
 
<p>
t_info:general                             timestamp=1365670813664, value=his fullname is junsanis!                                                                    </p>
 
<p>
t_vl:age                                   timestamp=1365670733223, value=99                                                                                            </p>
 
<p>
t_vl:name                                  timestamp=1365670723056, value=jss                                                                                          </p>
  3 row(s) in 0.0450 seconds</div>
 
<p>
获取指定记录中指定列族的数据：</p>
 
<div style="border:1px solid rgb(204,204,204);background-color:rgb(246,246,246);">
<ul style="list-style:none;"><li>
 
</li></ul><p>
hbase(main):029:0&gt; get ¨t¨,¨10001¨,¨t_vl¨</p>
 
<p>
COLUMN                                      CELL                                                                                                                        </p>
 
<p>
t_vl:age                                   timestamp=1365670733223, value=99                                                                                            </p>
 
<p>
t_vl:name                                  timestamp=1365670723056, value=jss                                                                                          </p>
  2 row(s) in 0.0070 seconds</div>
 
<p>
获取指定记录中指定列族中指定列的数据：</p>
 
<div style="border:1px solid rgb(204,204,204);background-color:rgb(246,246,246);">
<ul style="list-style:none;"><li>
 
</li></ul><p>
hbase(main):030:0&gt; get ¨t¨,¨10001¨,¨t_vl:age¨</p>
 
<p>
COLUMN                                      CELL                                                                                                                        </p>
 
<p>
t_vl:age                                   timestamp=1365670733223, value=99                                                                                            </p>
  1 row(s) in 0.0070 seconds</div>
 
<p>
记录更新(跟插入没有区别)：</p>
 
<div style="border:1px solid rgb(204,204,204);background-color:rgb(246,246,246);">
<ul style="list-style:none;"><li>
 
</li></ul><p>
hbase(main):031:0&gt; put ¨t¨,¨10001¨,¨t_vl:age¨,¨10¨</p>
 
<p>
0 row(s) in 0.0050 seconds</p>
 
<p>
</p>
 
<p>
hbase(main):032:0&gt; get ¨t¨,¨10001¨,¨t_vl:age¨</p>
 
<p>
COLUMN                                      CELL                                                                                                                        </p>
 
<p>
t_vl:age                                   timestamp=1365670912700, value=10                                                                                            </p>
  1 row(s) in 0.0080 seconds</div>
 
<p>
全表扫描：</p>
 
<div style="border:1px solid rgb(204,204,204);background-color:rgb(246,246,246);">
<ul style="list-style:none;"><li>
 
</li></ul><p>
hbase(main):033:0&gt; scan ¨t¨</p>
 
<p>
ROW                                         COLUMN+CELL                                                                                                                  </p>
 
<p>
10001                                      column=t_info:general, timestamp=1365670813664, value=his fullname is junsanis!                                              </p>
 
<p>
10001                                      column=t_vl:age, timestamp=1365670912700, value=10                                                                          </p>
 
<p>
10001                                      column=t_vl:name, timestamp=1365670723056, value=jss                                                                        </p>
  1 row(s) in 0.0370 seconds</div>
 
<p>
全表描述某个列：</p>
 
<div style="border:1px solid rgb(204,204,204);background-color:rgb(246,246,246);">
<ul style="list-style:none;"><li>
 
</li></ul><p>
hbase(main):036:0&gt; scan ¨t¨, {COLUMNS =&gt; ¨t_vl¨}</p>
 
<p>
ROW                                         COLUMN+CELL                                                                                                                  </p>
 
<p>
10001                                      column=t_vl:age, timestamp=1365670912700, value=10                                                                          </p>
 
<p>
10001                                      column=t_vl:name, timestamp=1365670723056, value=jss                                                                        </p>
  1 row(s) in 0.0080 seconds</div>
 
<p>
删除记录行：</p>
 
<div style="border:1px solid rgb(204,204,204);background-color:rgb(246,246,246);">
<ul style="list-style:none;"><li>
 
</li></ul><p>
hbase(main):043:0&gt; delete ¨t¨,¨10001¨,¨t_vl:age¨</p>
 
<p>
0 row(s) in 0.0050 seconds</p>
 
<p>
</p>
 
<p>
hbase(main):045:0&gt; get ¨t¨,¨10001¨</p>
 
<p>
COLUMN                                      CELL                                                                                                                        </p>
 
<p>
t_info:general                             timestamp=1365670813664, value=his fullname is junsanis!                                                                    </p>
 
<p>
t_vl:name                                  timestamp=1365670723056, value=jss                                                                                          </p>
  2 row(s) in 0.0070 seconds</div>
 
<p>
删除表：</p>
 
<div style="border:1px solid rgb(204,204,204);background-color:rgb(246,246,246);">
<ul style="list-style:none;"><li>
 
</li></ul><p>
hbase(main):047:0&gt; disable ¨t¨</p>
 
<p>
0 row(s) in 2.0230 seconds</p>
 
<p>
</p>
 
<p>
hbase(main):048:0&gt; drop ¨t¨</p>
  0 row(s) in 1.1170 seconds</div>
 
<p>
看完前面的例子，大家有没有问题，或者想到了什么？我脑子里反正是蹦出问号了：HBase中没有UPDATE操作，只有INSERT，可是我们每次put新记录都替换掉了旧的版本，怎么保存大量记录呢？难道每个row key的columns中只能存在一条记录？这不科学！这也显然不是人民群众期待并且喜闻乐见的表现嘛。</p>
 
<p>
这个问题呀，其实是列值保存版本(VERSIONS)或保留时间(TTL, Time to Liv)在起作用。</p>
 
<p>
比如，我们希望统计某用户的最近(n条)浏览记录，那么，创建HBase表对象如下：</p>
 
<div style="border:1px solid rgb(204,204,204);background-color:rgb(246,246,246);">
<ul style="list-style:none;"><li>
hbase&gt; create ¨rlog¨,¨userid¨,{NAME=&gt;¨article¨,VERSIONS=&gt;100}</li></ul></div>
 
<p>
当前设定，保留最近的100个版本。当用户浏览帖子时，就向rlog表中插入一条记录，形式如下：</p>
 
<div style="border:1px solid rgb(204,204,204);background-color:rgb(246,246,246);">
<ul style="list-style:none;"><li>
hbase&gt; put ¨rlog¨,$userid,¨article:id¨,$aid</li></ul></div>
 
<p>
这里仅选择记录浏览的用户ID和浏览页面ID，也可以根据实际情况，保存页面的URL地址，文章标题等等信息。HBase表列族是非结构化的，大家可以根据需求任意增加列值。</p>
 
<p>
那么，要获取用户最近浏览记录，应该怎么查呢？，比如说获取最近浏览的10条记录：</p>
 
<div style="border:1px solid rgb(204,204,204);background-color:rgb(246,246,246);">
<ul style="list-style:none;"><li>
hbase&gt; get ¨rlog¨,$userid,{COLUMN=&gt;¨article:id¨, VERSIONS=&gt;10}</li></ul></div>
 
<p>
除了通过VERSIONS控制外，还可以考虑通过版本的保存时间TTL来控制，TTL的单位是秒，默认一般是保存30天。</p>
</div>
</div>
            </div>
                </div>