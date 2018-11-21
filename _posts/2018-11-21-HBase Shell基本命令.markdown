---
layout:     post
title:      HBase Shell基本命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>HBase 为用户提供了一个非常方便的使用方式, 我们称之为“HBase Shell”。<br>
HBase Shell 提供了大多数的 HBase 命令, 通过 HBase Shell 用户可以方便地创建、删除及修改表, 还可以向表中添加数据、列出表中的相关信息等。<br><span style="color:#FF0000;">备注</span><span style="color:#FF0000;">:</span><span style="color:#FF0000;">写错</span><span style="color:#FF0000;">HBase Shell</span><span style="color:#FF0000;">命令时用键盘上的</span><span style="color:#FF0000;">“Delete”</span><span style="color:#FF0000;">进行向前删除</span><span style="color:#FF0000;">,“Backspace”</span><span style="color:#FF0000;">向后删除。</span><br><br></p>
<p>在启动 HBase 之后,用户可以通过下面的命令进入 HBase Shell 之中,命令如下所示:</p>
<p><span style="color:#BFBFBF;">[root@masterhbase0941]# bin/hbase shell</span></p>
<p><span style="color:#BFBFBF;">HBase Shell; enter'help&lt;RETURN&gt;' for list of supported commands.</span></p>
<p><span style="color:#BFBFBF;">Type"exit&lt;RETURN&gt;" to leave the HBase Shell</span></p>
<p><span style="color:#BFBFBF;">Version 0.94.1,r1365210, Tue Jul 24 18:40:10 UTC 2012</span></p>
<p><span style="color:#BFBFBF;"> </span></p>
<p><span style="color:#BFBFBF;">hbase(main):001:0&gt;</span></p>
<p> </p>
<p>常用的HBase Shell 命令如下表 1.1-1 所示:</p>
<p></p>
<p>下面是一些常见命令的说明，在hbase shell中输入help，会显示命令行支持的命令列表，通过help ‘命令名’可以显示命令的详细说明及示例：</p>
<p><span style="color:#FF0000;">hbase(main):017:0&gt;help</span></p>
<p><span style="color:#BFBFBF;">HBase Shell, version 0.94.1, r1365210, Tue Jul 24 18:40:10UTC 2012</span></p>
<p><span style="color:#BFBFBF;">Type 'help "COMMAND"', (e.g. 'help"get"' -- the quotes are necessary) for help on a specific command.</span></p>
<p><span style="color:#BFBFBF;">Commands are grouped. Type 'help"COMMAND_GROUP"', (e.g. 'help "general"') for help on acommand group.</span></p>
<p><span style="color:#BFBFBF;"> </span></p>
<p><span style="color:#BFBFBF;">COMMAND GROUPS:</span></p>
<p><span style="color:#BFBFBF;">  </span><span style="color:#CCCCCC;">Group name: general</span></p>
<p><span style="color:#CCCCCC;">  Commands: status,version, whoami</span></p>
<p><span style="color:#CCCCCC;"> </span></p>
<p><span style="color:#CCCCCC;">  Group name: ddl</span></p>
<p><span style="color:#CCCCCC;">  Commands: alter,alter_async, alter_status, create, describe, disable, disable_all, drop,drop_all, enable, enable_all, exists, is_disabled, is_enabled, list,show_filters</span></p>
<p><span style="color:#CCCCCC;"> </span></p>
<p><span style="color:#CCCCCC;">  Group name: dml</span></p>
<p><span style="color:#CCCCCC;">  Commands: count,delete, deleteall, get, get_counter, incr, put, scan, truncate</span></p>
<p><span style="color:#CCCCCC;"> </span></p>
<p><span style="color:#CCCCCC;">  Group name: tools</span></p>
<p><span style="color:#CCCCCC;">  Commands: assign,balance_switch, balancer, close_region, compact, flush, hlog_roll,major_compact, move, split, unassign, zk_dump</span></p>
<p><span style="color:#CCCCCC;"> </span></p>
<p><span style="color:#CCCCCC;">  Group name:replication</span></p>
<p><span style="color:#CCCCCC;">  Commands: add_peer,disable_peer, enable_peer, list_peers, remove_peer, start_replication,stop_replication</span></p>
<p><span style="color:#CCCCCC;"> </span></p>
<p><span style="color:#CCCCCC;">  Group name:security</span></p>
<p><span style="color:#CCCCCC;">  Commands: grant,revoke, user_permission</span></p>
<p><span style="color:#BFBFBF;"> </span></p>
<p><span style="color:#BFBFBF;">SHELL USAGE:</span></p>
<p><span style="color:#BFBFBF;">Quote all names in <span style="color:#C0C0C0;">HBase Shell such as table and columnnames. </span></span><span style="color:#C0C0C0;">Commas delimit</span></p>
<p><span style="color:#C0C0C0;">command parameters. Type &lt;RETURN&gt; after entering a command to run it.</span></p>
<p><span style="color:#C0C0C0;">Dictionaries of configuration used in the creation andalteration of tables are</span></p>
<p><span style="color:#C0C0C0;">Ruby Hashes. They look like this:</span></p>
<p><span style="color:#C0C0C0;"> </span></p>
<p><span style="color:#C0C0C0;">  {'key1' =&gt;'value1', 'key2' =&gt; 'value2', ...}</span></p>
<p><span style="color:#C0C0C0;"> </span></p>
<p><span style="color:#C0C0C0;">and are opened and closed with curley-braces.  Key/values are delimited by the</span></p>
<p><span style="color:#C0C0C0;">'=&gt;' character combination.  Usually keys are predefined constants such as</span></p>
<p><span style="color:#C0C0C0;">NAME, VERSIONS, COMPRESSION, etc.  Constants do not need to be quoted.  Type</span></p>
<p><span style="color:#C0C0C0;">'Object.constants' to see a (messy) list of all constantsin the environment.</span></p>
<p><span style="color:#BFBFBF;"> </span></p>
<p><span style="color:#BFBFBF;">If you are using binary keys or values and need to enterthem in the shell, use</span></p>
<p><span style="color:#C0C0C0;">double-quote'd hexadecimal representation. For example:</span></p>
<p><span style="color:#C0C0C0;"> </span></p>
<p><span style="color:#C0C0C0;">  hbase&gt; get 't1',"key\x03\x3f\xcd"</span></p>
<p><span style="color:#C0C0C0;">  hbase&gt; get 't1',"key\003\023\011"</span></p>
<p><span style="color:#C0C0C0;">  hbase&gt; put 't1',"test\xef\xff", 'f1:', "\x01\x33\x40"</span></p>
<p><span style="color:#C0C0C0;"> </span></p>
<p><span style="color:#BFBFBF;"><span style="color:#C0C0C0;">The HBase shell is the (J)Ruby IRB with the aboveH</span>Base-specific commands added.</span></p>
<p><span style="color:#BFBFBF;">For more on the HBase Shell, see http://hbase.apache.org/docs/current/book.html</span></p>
<p><span style="color:#BFBFBF;">hbase(main):018:0&gt;</span></p>
<p><span style="color:#BFBFBF;"> </span></p>
<p><span style="color:#FF0000;">hbase(main):001:0&gt;help 'create'</span></p>
<p><span style="color:#BFBFBF;">Create table; pass table name, a dictionary ofspecifications per</span></p>
<p><span style="color:#BFBFBF;">column family, and optionally a dictionary of tableconfiguration.</span></p>
<p><span style="color:#BFBFBF;">Dictionaries are described below in the GENERAL NOTESsection.</span></p>
<p><span style="color:#C0C0C0;">Examples:</span></p>
<p><span style="color:#C0C0C0;"> </span></p>
<p><span style="color:#C0C0C0;">  hbase&gt; create't1', {NAME =&gt; 'f1', VERSIONS =&gt; 5}</span></p>
<p><span style="color:#C0C0C0;">  hbase&gt; create't1', {NAME =&gt; 'f1'}, {NAME =&gt; 'f2'}, {NAME =&gt; 'f3'}</span></p>
<p><span style="color:#C0C0C0;">  hbase&gt; # Theabove in shorthand would be the following:</span></p>
<p><span style="color:#C0C0C0;">  hbase&gt; create't1', 'f1', 'f2', 'f3'</span></p>
<p><span style="color:#C0C0C0;">  hbase&gt; create't1', {NAME =&gt; 'f1', VERSIONS =&gt; 1, TTL =&gt; 2592000, BLOCKCACHE =&gt;true}</span></p>
<p><span style="color:#C0C0C0;">  hbase&gt; create't1', 'f1', {SPLITS =&gt; ['10', '20', '30', '40']}</span></p>
<p><span style="color:#C0C0C0;">  hbase&gt; create't1', 'f1', {SPLITS_FILE =&gt; 'splits.txt'}</span></p>
<p><span style="color:#C0C0C0;">  hbase&gt; #Optionally pre-split the table into NUMREGIONS, using</span></p>
<p><span style="color:#C0C0C0;">  hbase&gt; #SPLITALGO ("HexStringSplit", "UniformSplit" or classname)</span></p>
<p><span style="color:#C0C0C0;">  hbase&gt; create't1', 'f1', {NUMREGIONS =&gt; 15, SPLITALGO =&gt; 'HexStringSplit'}</span></p>
<p><span style="color:#C0C0C0;">hbase(main):002:0&gt;</span></p>
<h2><strong>一、一般操作</strong></h2>
<p>1.查询服务器状态</p>
<p><span style="color:#BFBFBF;">hbase(main):024:0&gt;status</span></p>
<p><span style="color:#BFBFBF;">3 servers, 0 dead,1.0000 average load</span></p>
<p>2.查询hbase版本</p>
<p><span style="color:#BFBFBF;">hbase(main):001:0&gt; version</span></p>
<p><span style="color:#BFBFBF;">0.94.1, r1365210, Tue Jul 24 18:40:10 UTC 2012 </span></p>
<p>3.查看用户身份</p>
<p><span style="color:#BFBFBF;">hbase(main):002:0&gt; whoami</span></p>
<p><span style="color:#BFBFBF;">root</span></p>
<h2><strong>二、DDL操作</strong></h2>
<p> 1.创建一个表</p>
<p><span style="color:#BFBFBF;">hbase(main):011:0&gt;create'member','member_id','address','info'   </span></p>
<p><span style="color:#BFBFBF;">0 row(s) in 1.2210seconds</span></p>
<p> 2.获得表的描述</p>
<p><span style="color:#BFBFBF;">hbase(main):012:0&gt;list</span></p>
<p><span style="color:#BFBFBF;">TABLE                                                                                                                                                     
<br></span></p>
<p><span style="color:#BFBFBF;">member                                                                                                                                                      </span></p>
<p><span style="color:#BFBFBF;">1 row(s) in 0.0160seconds</span></p>
<p><span style="color:#BFBFBF;">hbase(main):006:0&gt;describe 'member'</span></p>
<p><span style="color:#BFBFBF;">DESCRIPTION                                                                                          ENABLED                                               </span></p>
<p><span style="color:#BFBFBF;"> {NAME =&gt; 'member', FAMILIES =&gt; [{NAME=&gt;'address', BLOOMFILTER =&gt; 'NONE', REPLICATION_SCOPE =&gt; '0',true                                                 </span></p>
<p><span style="color:#BFBFBF;">  VERSIONS =&gt; '3', COMPRESSION =&gt; 'NONE',TTL=&gt; '2147483647', BLOCKSIZE =&gt; '65536', IN_MEMORY =&gt;'fa                                                       </span></p>
<p><span style="color:#BFBFBF;"> lse', BLOCKCACHE =&gt; 'true'}, {NAME =&gt;'info',BLOOMFILTER =&gt; 'NONE', REPLICATION_SCOPE =&gt; '0',VERSI                                                       </span></p>
<p><span style="color:#BFBFBF;"> ONS =&gt; '3', COMPRESSION =&gt; 'NONE', TTL=&gt;'2147483647', BLOCKSIZE =&gt; '65536', IN_MEMORY =&gt;'false',                                                        </span></p>
<p><span style="color:#BFBFBF;"> BLOCKCACHE =&gt;'true'}]}                                                                                                                                    </span></p>
<p><span style="color:#BFBFBF;">1 row(s) in 0.0230seconds</span></p>
<p> </p>
<p>3.删除一个列族，alter，disable，enable</p>
<p>我们之前建了3个列族，但是发现member_id这个列族是多余的，因为他就是主键，所以我们要将其删除。</p>
<p><span style="color:#BFBFBF;">hbase(main):003:0&gt;alter'member',{NAME=&gt;'member_id',METHOD=&gt;'delete'}</span></p>
<p><span style="color:#BFBFBF;"> </span></p>
<p><span style="color:#BFBFBF;">ERROR: Table memberis enabled. Disable it first beforealtering.</span></p>
<p><span style="color:#FF0000;">报错，删除列族的时候必须先将表给disab<span style="color:#FF6666;">le</span></span><span style="color:#FF6666;">掉。</span></p>
<p><span style="color:#BFBFBF;">hbase(main):004:0&gt;disable'member'                                  </span></p>
<p><span style="color:#BFBFBF;">0 row(s) in 2.0390seconds</span></p>
<p><span style="color:#BFBFBF;">hbase(main):005:0&gt;alter'member',{NAME=&gt;'member_id',METHOD=&gt;'delete'}</span></p>
<p><span style="color:#BFBFBF;">0 row(s) in 0.0560seconds</span></p>
<p><span style="color:#BFBFBF;">hbase(main):006:0&gt;describe 'member'</span></p>
<p><span style="color:#BFBFBF;">DESCRIPTION                                                                                          ENABLED                                               </span></p>
<p><span style="color:#BFBFBF;"> {NAME =&gt; 'member', FAMILIES =&gt; [{NAME=&gt;'address', BLOOMFILTER =&gt; 'NONE', REPLICATION_SCOPE =&gt;'0',false                                                 </span></p>
<p><span style="color:#BFBFBF;">  VERSIONS =&gt; '3', COMPRESSION =&gt; 'NONE',TTL=&gt; '2147483647', BLOCKSIZE =&gt; '65536', IN_MEMORY =&gt;'fa                                                       </span></p>
<p><span style="color:#BFBFBF;"> lse', BLOCKCACHE =&gt; 'true'}, {NAME =&gt;'info',BLOOMFILTER =&gt; 'NONE', REPLICATION_SCOPE =&gt; '0',VERSI                                                       </span></p>
<p><span style="color:#BFBFBF;"> ONS =&gt; '3', COMPRESSION =&gt; 'NONE', TTL=&gt;'2147483647', BLOCKSIZE =&gt; '65536', IN_MEMORY =&gt;'false',                                                        </span></p>
<p><span style="color:#BFBFBF;"> BLOCKCACHE =&gt;'true'}]}                                                                                                                                    </span></p>
<p><span style="color:#BFBFBF;">1 row(s) in 0.0230seconds</span></p>
<p>该列族已经删除，我们继续将表enable</p>
<p><span style="color:#BFBFBF;">hbase(main):008:0&gt; enable 'member'  </span></p>
<p><span style="color:#BFBFBF;">0 row(s) in 2.0420seconds</span></p>
<p> </p>
<p>4.列出所有的表</p>
<p><span style="color:#BFBFBF;">hbase(main):028:0&gt;list</span></p>
<p><span style="color:#BFBFBF;">TABLE                                                                                                                                                       </span></p>
<p><span style="color:#BFBFBF;">member                                                                                                                                                      </span></p>
<p><span style="color:#BFBFBF;">temp_table                                                                                                                                                  </span></p>
<p><span style="color:#BFBFBF;">2 row(s) in 0.0150seconds</span></p>
<p> </p>
<p>5.drop一个表</p>
<p><span style="color:#BFBFBF;">hbase(main):029:0&gt;disable 'temp_table' </span></p>
<p><span style="color:#BFBFBF;">0 row(s) in 2.0590seconds</span></p>
<p><span style="color:#BFBFBF;"> </span></p>
<p><span style="color:#BFBFBF;">hbase(main):030:0&gt;drop 'temp_table'</span></p>
<p><span style="color:#BFBFBF;">0 row(s) in 1.1070seconds</span></p>
<p> </p>
<p>6.查询表是否存在</p>
<p><span style="color:#BFBFBF;">hbase(main):021:0&gt;exists 'member'</span></p>
<p><span style="color:#BFBFBF;">Table memberdoesexist                                                                                                                                     </span></p>
<p><span style="color:#BFBFBF;">0 row(s) in 0.1610seconds</span></p>
<p> </p>
<p>7.判断表是否enable</p>
<p><span style="color:#BFBFBF;">hbase(main):034:0&gt;is_enabled 'member' </span></p>
<p><span style="color:#BFBFBF;">true                                                                                                                                                        </span></p>
<p><span style="color:#BFBFBF;">0 row(s) in 0.0110seconds</span></p>
<p><span style="color:#BFBFBF;"> </span></p>
<p>8.判断表是否disable</p>
<p><span style="color:#BFBFBF;">hbase(main):032:0&gt;is_disabled 'member'</span></p>
<p><span style="color:#BFBFBF;">false                                                                                                                                                       </span></p>
<p><span style="color:#BFBFBF;">0 row(s) in 0.0110seconds</span></p>
<p> </p>
<h2><strong>三、DML操作</strong></h2>
<p> 1.插入几条记录</p>
<p><span style="color:#BFBFBF;">put'member','scutshuxue','info:age','24'</span></p>
<p><span style="color:#BFBFBF;">put'member','scutshuxue','info:birthday','1987-06-17'</span></p>
<p><span style="color:#BFBFBF;">put'member','scutshuxue','info:company','alibaba'</span></p>
<p><span style="color:#BFBFBF;">put'member','scutshuxue','address:contry','china' </span>
</p>
<p><span style="color:#BFBFBF;">put'member','scutshuxue','address:province','zhejiang'</span></p>
<p><span style="color:#BFBFBF;">put'member','scutshuxue','address:city','hangzhou'</span></p>
<p><span style="color:#BFBFBF;"> </span></p>
<p><span style="color:#BFBFBF;">put'member','xiaofeng','info:birthday','1987-4-17'</span></p>
<p><span style="color:#BFBFBF;">put'member','xiaofeng','info:favorite','movie' </span></p>
<p><span style="color:#BFBFBF;">put'member','xiaofeng','info:company','alibaba'</span></p>
<p><span style="color:#BFBFBF;">put'member','xiaofeng','address:contry','china' </span>
</p>
<p><span style="color:#BFBFBF;">put'member','xiaofeng','address:province','guangdong'</span></p>
<p><span style="color:#BFBFBF;">put'member','xiaofeng','address:city','jieyang' </span>
</p>
<p><span style="color:#BFBFBF;">put'member','xiaofeng','address:town','xianqiao' </span>
</p>
<p> </p>
<p>2.获取一条数据</p>
<p>获取一个id的所有数据</p>
<p><span style="color:#BFBFBF;">hbase(main):001:0&gt;get 'member','scutshuxue'</span></p>
<p><span style="color:#BFBFBF;">COLUMN                                  CELL                                                                                                               </span></p>
<p><span style="color:#BFBFBF;"> address:city                           timestamp=1321586240244,value=hangzhou                                                                            </span></p>
<p><span style="color:#BFBFBF;"> address:contry                         timestamp=1321586239126,value=china                                                                               </span></p>
<p><span style="color:#BFBFBF;"> address:province                       timestamp=1321586239197,value=zhejiang                                                                            </span></p>
<p><span style="color:#BFBFBF;"> info:age                               timestamp=1321586238965,value=24                                                                                  </span></p>
<p><span style="color:#BFBFBF;"> info:birthday                          timestamp=1321586239015,value=1987-06-17                                                                          </span></p>
<p><span style="color:#BFBFBF;"> info:company                           timestamp=1321586239071,value=alibaba                                                                             </span></p>
<p><span style="color:#BFBFBF;">6 row(s) in 0.4720seconds</span></p>
<p> 获取一个id，一个列族的所有数据</p>
<p><span style="color:#BFBFBF;">hbase(main):002:0&gt;get 'member','scutshuxue','info'</span></p>
<p><span style="color:#BFBFBF;">COLUMN                                  CELL                                                                                                               </span></p>
<p><span style="color:#BFBFBF;"> info:age                               timestamp=1321586238965,value=24                                                                                  </span></p>
<p><span style="color:#BFBFBF;"> info:birthday                          timestamp=1321586239015,value=1987-06-17                                                                          </span></p>
<p><span style="color:#BFBFBF;"> info:company                           timestamp=1321586239071,value=alibaba                                                                             </span></p>
<p><span style="color:#BFBFBF;">3 row(s) in 0.0210seconds</span></p>
<p> 获取一个id，一个列族中一个列的所有数据</p>
<p><span style="color:#BFBFBF;">hbase(main):002:0&gt;get'member','scutshuxue','info:age' </span></p>
<p><span style="color:#BFBFBF;">COLUMN                                  CELL                                                                                                               </span></p>
<p><span style="color:#BFBFBF;"> info:age                               timestamp=1321586238965,value=24                                                                                  </span></p>
<p><span style="color:#BFBFBF;">1 row(s) in 0.0320seconds</span></p>
<p> </p>
<p>3.更新一条记录</p>
<p>将scutshuxue的年龄改成99</p>
<p><span style="color:#BFBFBF;">hbase(main):004:0&gt;put 'member','scutshuxue','info:age','99'</span></p>
<p><span style="color:#BFBFBF;">0 row(s) in 0.0210seconds</span></p>
<p><span style="color:#BFBFBF;"> </span></p>
<p><span style="color:#BFBFBF;">hbase(main):005:0&gt;get'member','scutshuxue','info:age' </span></p>
<p><span style="color:#BFBFBF;">COLUMN                                  CELL                                                                                                               </span></p>
<p><span style="color:#BFBFBF;"> info:age                               timestamp=1321586571843,value=99                                                                                  </span></p>
<p><span style="color:#BFBFBF;">1 row(s) in 0.0180seconds</span></p>
<p> </p>
<p>通过timestamp来获取两个版本的数据</p>
<p><span style="color:#BFBFBF;">hbase(main):010:0&gt;get'member','scutshuxue',{COLUMN=&gt;'info:age',TIMESTAMP=&gt;1321586238965}</span></p>
<p><span style="color:#BFBFBF;">COLUMN                                  CELL                                                                                                               </span></p>
<p><span style="color:#BFBFBF;"> info:age                               timestamp=1321586238965,value=24                                                                                  </span></p>
<p><span style="color:#BFBFBF;">1 row(s) in 0.0140seconds</span></p>
<p><span style="color:#BFBFBF;"> </span></p>
<p><span style="color:#BFBFBF;">hbase(main):011:0&gt;get'member','scutshuxue',{COLUMN=&gt;'info:age',TIMESTAMP=&gt;1321586571843}</span></p>
<p><span style="color:#BFBFBF;">COLUMN                                  CELL                                                                                                               </span></p>
<p><span style="color:#BFBFBF;"> info:age                               timestamp=1321586571843,value=99                                                                                  </span></p>
<p><span style="color:#BFBFBF;">1 row(s) in 0.0180seconds</span></p>
<p> </p>
<p>4.全表扫描</p>
<p><span style="color:#BFBFBF;">hbase(main):013:0&gt;scan 'member'</span></p>
<p><span style="color:#BFBFBF;">ROW                                     COLUMN+CELL                                                                                                        </span></p>
<p><span style="color:#BFBFBF;"> scutshuxue                             column=address:city,timestamp=1321586240244, value=hangzhou                                                       </span></p>
<p><span style="color:#BFBFBF;"> scutshuxue                             column=address:contry,timestamp=1321586239126,value=china                                                        </span></p>
<p><span style="color:#BFBFBF;"> scutshuxue                             column=address:province,timestamp=1321586239197, value=zhejiang                                                   </span></p>
<p><span style="color:#BFBFBF;"> scutshuxue                             column=info:age,timestamp=1321586571843, value=99                                                                 </span></p>
<p><span style="color:#BFBFBF;"> scutshuxue                             column=info:birthday,timestamp=1321586239015,value=1987-06-17                                                    </span></p>
<p><span style="color:#BFBFBF;"> scutshuxue                             column=info:company,timestamp=1321586239071, value=alibaba                                                        </span></p>
<p><span style="color:#BFBFBF;"> temp                                   column=info:age,timestamp=1321589609775,value=59                                                                 </span></p>
<p><span style="color:#BFBFBF;"> xiaofeng                               column=address:city,timestamp=1321586248400, value=jieyang                                                        </span></p>
<p><span style="color:#BFBFBF;"> xiaofeng                               column=address:contry,timestamp=1321586248316, value=china                                                        </span></p>
<p><span style="color:#BFBFBF;"> xiaofeng                               column=address:province,timestamp=1321586248355,value=guangdong                                                  </span></p>
<p><span style="color:#BFBFBF;"> xiaofeng                               column=address:town,timestamp=1321586249564, value=xianqiao                                                       </span></p>
<p><span style="color:#BFBFBF;"> xiaofeng                               column=info:birthday,timestamp=1321586248202,value=1987-4-17                                                     </span></p>
<p><span style="color:#BFBFBF;"> xiaofeng                               column=info:company,timestamp=1321586248277, value=alibaba                                                        </span></p>
<p><span style="color:#BFBFBF;"> xiaofeng                               column=info:favorite,timestamp=1321586248241, value=movie                                                         </span></p>
<p><span style="color:#BFBFBF;">3 row(s) in 0.0570seconds</span></p>
<p> </p>
<p>5.删除id为temp的值的‘info:age’字段</p>
<p><span style="color:#BFBFBF;">hbase(main):016:0&gt;delete 'member','temp','info:age'</span></p>
<p><span style="color:#BFBFBF;">0 row(s) in 0.0150seconds</span></p>
<p><span style="color:#BFBFBF;">hbase(main):018:0&gt;get 'member','temp'</span></p>
<p><span style="color:#BFBFBF;">COLUMN                                  CELL                                                                                                               </span></p>
<p><span style="color:#BFBFBF;">0 row(s) in 0.0150seconds</span></p>
<p> </p>
<p>6.删除整行</p>
<p><span style="color:#BFBFBF;">hbase(main):001:0&gt;deleteall 'member','xiaofeng'</span></p>
<p><span style="color:#BFBFBF;">0 row(s) in 0.3990seconds</span></p>
<p> </p>
<p>7.查询表中有多少行：</p>
<p><span style="color:#BFBFBF;">hbase(main):019:0&gt;count'member'                                        </span></p>
<p><span style="color:#BFBFBF;">2 row(s) in 0.0160seconds</span></p>
<p> </p>
<p>8.给‘xiaofeng’这个id增加'info:age'字段，并使用counter实现递增</p>
<p><span style="color:#BFBFBF;">hbase(main):057:0*incr'member','xiaofeng','info:age'                    </span></p>
<p><span style="color:#BFBFBF;">COUNTER VALUE = 1</span></p>
<p><span style="color:#BFBFBF;"> </span></p>
<p><span style="color:#BFBFBF;">hbase(main):058:0&gt;get'member','xiaofeng','info:age' </span></p>
<p><span style="color:#BFBFBF;">COLUMN                                  CELL                                                                                                               </span></p>
<p><span style="color:#BFBFBF;"> info:age                               timestamp=1321590997648,value=\x00\x00\x00\x00\x00\x00\x00\x01                                                    </span></p>
<p><span style="color:#BFBFBF;">1 row(s) in 0.0140seconds</span></p>
<p><span style="color:#BFBFBF;"> </span></p>
<p><span style="color:#BFBFBF;">hbase(main):059:0&gt;incr 'member','xiaofeng','info:age'</span></p>
<p><span style="color:#BFBFBF;">COUNTER VALUE = 2</span></p>
<p><span style="color:#BFBFBF;"> </span></p>
<p><span style="color:#BFBFBF;">hbase(main):060:0&gt;get'member','xiaofeng','info:age' </span></p>
<p><span style="color:#BFBFBF;">COLUMN                                  CELL                                                                                                               </span></p>
<p><span style="color:#BFBFBF;"> info:age                               timestamp=1321591025110,value=\x00\x00\x00\x00\x00\x00\x00\x02                                                    </span></p>
<p><span style="color:#BFBFBF;">1 row(s) in 0.0160seconds</span></p>
<p><span style="color:#FF0000;"> </span><span style="color:#FF0000;">注：incr</span>的用法是先用incr ‘t1’, ‘r1’,‘c1’增加一列，</p>
<p><span style="color:#FF0000;">    </span><span style="color:#FF0000;">再用incr ‘t1’,’r1’,’c1’</span>，1 来对这一列进行值增加。</p>
<p>获取当前count的值</p>
<p><span style="color:#BFBFBF;">hbase(main):069:0&gt;get_counter'member','xiaofeng','info:age' </span></p>
<p><span style="color:#BFBFBF;">COUNTER VALUE = 2</span></p>
<p>  </p>
<p>9.将整张表清空：</p>
<p><span style="color:#BFBFBF;">hbase(main):035:0&gt;truncate 'member'</span></p>
<p><span style="color:#BFBFBF;">Truncating 'member'table (it may take a while):</span></p>
<p><span style="color:#BFBFBF;"> - Disabling table...</span></p>
<p><span style="color:#BFBFBF;"> - Dropping table...</span></p>
<p><span style="color:#BFBFBF;"> - Creating table...</span></p>
<p><span style="color:#BFBFBF;">0 row(s) in 4.3430seconds</span></p>
<p>可以看出，hbase是先将掉disable掉，然后drop掉后重建表来实现truncate的功能的。</p>
<p> </p>
<h2><strong>附：DDL、DML和DCL的理解</strong></h2>
<p><br>
 1、DDL</p>
<p>      1-1、DDL的概述</p>
<p>              DDL（Data Definition Language 数据定义语言）用于操作对象和对象的属性，这种对象包括<span style="color:#000000;"><a href="http://www.2cto.com/database/" rel="nofollow">数据库</a></span>本身，以及数据库对象，像：表、视图等等，DDL对这些对象和属性的管理和定义具体表现在Create、Drop和Alter上。特别注意：DDL操作的
 “对象”的概念，”对象“包括对象及对象的属性，而且对象最小也比记录大个层次。以表举例：Create创建数据表，Alter可以更改该表的字段，Drop可以删除这个表，从这里我们可以看到，DDL所站的高度，他不会对具体的数据进行操作。<br>
 </p>
<p>      1-2、DDL的主要语句（操作）</p>
<p>              Create语句：可以创建数据库和数据库的一些对象。</p>
<p>              Drop语句：可以删除数据表、索引、触发程序、条件约束以及数据表的权限等。</p>
<p>              Alter语句：修改数据表定义及属性。</p>
<p> </p>
<p>      1-3、DDL的操作对象（表）</p>
<p>              1-3-1、表的概念</p>
<p>              表的创建就是用来存放数据用的，由于我们存放的数据的不通，所以我们需要定义些数据类型，以方便管理。</p>
<p>              1-3-2、表的属性</p>
<p>     主键属性：主键就是主键约束，只不过起的名字不同了，主键的起名偏向于虚的（就是描述描述这件事），主键约束起名偏向于实得（就是描述操作的实施），描述的都是同一件事，主键约束就是表中的一个属性；在一个表中最多可以有一个主键；一个主键可以定义在一个或多个字段；主键使一个或多个字段的值必须唯一且不为空，这样做可以通过该字段或该组字段中的值唯一的代表一条记录。</p>
<p>      唯一属性：一个表中只能有一个主键属性，为了方表用户，提出唯一约束；唯一约束可以定义在一个或多个字段上；唯一约束使该字段或该组字段中的值唯一，可以为空，但是，不能重复。</p>
<p>       外键属性：又叫外键，又叫外键约束，跟主键和主键约束的关系是一样的；外键约束针对的两个表，如果表A的主关键字是表B中的字段，则该字段称为表B的外键，表A称为主表，表B称为从表，但要注意，必须要计算机要知道你是这种关系。<br>
      核查、Null和缺省属性：核查属性又叫核查约束，Null属性又叫Null约束，缺省属性又叫缺省约束；这些名称是描述一件事，描述一种情况，这件事或这张情况我们当然可以人为的那样特意做（输入数据是注意就行），但是，他们的本意是实现自动化，也就是让计算机做这件事。</p>
<p>      （你知道为什么建立主键和唯一约束的时候，会自动的创建索引吗？而且是唯一索引，想一想索引大多在那些字段上用，以及索引的作用就会知道了。像主键约束、唯一约束、非空约束、外键约束、核查约束和缺省约束这些操作都是使表具有某些特性，所以在这里我认为他们都是表的属性。）</p>
<p> </p>
<p>2、DML</p>
<p>      2-1、DML的概述</p>
<p>              DML（Data Manipulation Language 数据操控语言）用于操作数据库对象中包含的数据，也就是说操作的单位是记录。</p>
<p>      2-2、DML的主要语句（操作）</p>
<p>              Insert语句：向数据表张插入一条记录。</p>
<p>              Delete语句：删除数据表中的一条或多条记录，也可以删除数据表中的所有记录，但是，它的操作对象仍是记录。</p>
<p>              Update语句：用于修改已存在表中的记录的内容。</p>
<p>      2-3、DML的操作对象——记录</p>
<p>              2-3-1、注意</p>
<p>                          当我们对记录进行Insert、Delete和Update操作的时候，一定要注意，一定要清楚DDL对其的一些操作。</p>
<p> </p>
<p>3、DCL</p>
<p>       3-1、DCL的概述</p>
<p>               DCL（Data Control Language 数据控制语句）的操作是数据库对象的权限，这些操作的确定使数据更加的安全。</p>
<p>       3-2、DCL的主要语句（操作）</p>
<p>               Grant语句：允许对象的创建者给某用户或某组或所有用户（PUBLIC）某些特定的权限。</p>
<p>               Revoke语句：可以废除某用户或某组或所有用户访问权限</p>
<p>       3-3、DCL的操作对象（用户）</p>
<p>               此时的用户指的是数据库用户。</p>
<p> </p>
            </div>
                </div>