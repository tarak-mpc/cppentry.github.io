---
layout:     post
title:      hbase常用的shell命令例子
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，转载请注明本文链接。文章内容如有错误望能指正，以免误导更多人。					https://blog.csdn.net/gnail_oug/article/details/46997997				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p></p>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><span style="background-color:inherit;"><strong>1、hbase shell    进入hbase</strong></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">[<a href="mailto:hadoop@mdw" rel="nofollow" style="background-color:inherit;">hadoop@mdw</a> ~]$ <span style="background-color:inherit;"><span style="color:#ff0000;">hbase shell</span></span><br style="background-color:inherit;">
HBase Shell; enter 'help&lt;RETURN&gt;' for list of supported commands.<br style="background-color:inherit;">
Type "exit&lt;RETURN&gt;" to leave the HBase Shell<br style="background-color:inherit;">
Version 0.94.16, r1557241, Fri Jan 10 20:10:24 UTC 2014<br></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><span style="background-color:inherit;"><strong>2、whoami    查看当前访问hbase的用户</strong></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">hbase(main):001:0&gt; <span style="background-color:inherit;"><span style="color:#ff0000;">whoami</span></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">hadoop</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><span style="background-color:inherit;"><strong>3、version    查看当前hbase的版本信息</strong></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">hbase(main):002:0&gt; <span style="background-color:inherit;"><span style="color:#ff0000;">version</span></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">0.94.16, r1557241, Fri Jan 10 20:10:24 UTC 2014</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><span style="background-color:inherit;"><strong>4、status    查看当前hbase的状态</strong></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">hbase(main):003:0&gt; <span style="background-color:inherit;"><span style="color:#ff0000;">status</span></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">2 servers, 0 dead, 1.0000 average load</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">也可以通过增加参数查看，status 'simple' 查看简单的信息、status 'summary' 查看概要信息、status 'detailed' 查看详细信息</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><span style="background-color:inherit;"><strong>5、create    创建表</strong></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">hbase(main):007:0&gt;<span style="background-color:inherit;"> <span style="color:#ff0000;">create 'member','member_id','address','info'</span></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">0 row(s) in 1.1590 seconds</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><span style="background-color:inherit;"><strong>6、list    查看数据库中的表</strong></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">hbase(main):008:0&gt; <span style="background-color:inherit;"><span style="color:#ff0000;">list</span></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">TABLE                                                                                                    </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">member                                                                                                  </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">1 row(s) in 0.0280 seconds</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><span style="background-color:inherit;"><strong>7、describe 表名    查看表的信息</strong></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">hbase(main):009:0&gt;<span style="color:#ff0000;">describe member</span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">NameError: undefined local variable or method `member' for #&lt;Object:0x453c47&gt;</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">hbase(main):010:0&gt; describe 'member'</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">DESCRIPTION                                                                                                                  ENABLED                            </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> 'member', {NAME =&gt; '<span style="color:#ff0000;background-color:inherit;">address</span>', DATA_BLOCK_ENCODING =&gt; 'NONE', BLOOMF    true                                </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> ILTER =&gt; 'NONE', REPLICATION_SCOPE =&gt; '0', VERSIONS =&gt; '3', COMPRES                                    </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> SION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', TTL =&gt; '2147483647', KEEP_DELE                                    </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> TED_CELLS =&gt; 'false', BLOCKSIZE =&gt; '65536', IN_MEMORY =&gt; 'false', E                                    </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> NCODE_ON_DISK =&gt; 'true', BLOCKCACHE =&gt; 'true'}, {NAME =&gt; '<span style="color:#ff0000;background-color:inherit;">info</span>', DA                                    </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> TA_BLOCK_ENCODING =&gt; 'NONE', BLOOMFILTER =&gt; 'NONE', REPLICATION_SCO                                    </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> PE =&gt; '0', VERSIONS =&gt; '3', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt;                                      </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> '0', TTL =&gt; '2147483647', KEEP_DELETED_CELLS =&gt; 'false', BLOCKSIZE                                      </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> =&gt; '65536', IN_MEMORY =&gt; 'false', ENCODE_ON_DISK =&gt; 'true', BLOCKCA                                    </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> CHE =&gt; 'true'}, {NAME =&gt; '<span style="color:#ff0000;background-color:inherit;">member_id</span>', DATA_BLOCK_ENCODING =&gt; 'NONE'                                    </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> , BLOOMFILTER =&gt; 'NONE', REPLICATION_SCOPE =&gt; '0', VERSIONS =&gt; '3',                                    </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">  COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', TTL =&gt; '2147483647', K                                    </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> EEP_DELETED_CELLS =&gt; 'false', BLOCKSIZE =&gt; '65536', IN_MEMORY =&gt; 'f                                    </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> alse', ENCODE_ON_DISK =&gt; 'true', BLOCKCACHE =&gt; 'true'}                                                  </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">1 row(s) in 0.0450 seconds</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">hbase(main):011:0&gt;</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><span style="background-color:inherit;"><strong>8、删除列族</strong></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">删除列族<span style="color:#ff0000;">需要先禁用表</span>，否则会报以下错误：</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">hbase(main):006:0&gt; <span style="background-color:inherit;">alter 'member',NAME=&gt;'member_id',METHOD=&gt;'delete'</span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">ERROR: org.apache.hadoop.hbase.TableNotDisabledException: org.apache.hadoop.hbase.TableNotDisabledException: member</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">        at org.apache.hadoop.hbase.master.HMaster.checkTableModifiable(HMaster.java:1525)</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">        at org.apache.hadoop.hbase.master.handler.TableEventHandler.&lt;init&gt;(TableEventHandler.java:72)</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">        at org.apache.hadoop.hbase.master.handler.TableDeleteFamilyHandler.&lt;init&gt;(TableDeleteFamilyHandler.java:41)</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">        at org.apache.hadoop.hbase.master.HMaster.deleteColumn(HMaster.java:1430)</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:39)</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:25)</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">        at java.lang.reflect.Method.invoke(Method.java:597)</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">        at org.apache.hadoop.hbase.ipc.WritableRpcEngine$Server.call(WritableRpcEngine.java:323)</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">        at org.apache.hadoop.hbase.ipc.HBaseServer$Handler.run(HBaseServer.java:1426)</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">删除列族的正确方法：</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">先禁用表：</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">hbase(main):007:0&gt; <span style="background-color:inherit;"><span style="color:#ff0000;">disable 'member'</span></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">0 row(s) in 1.1690 seconds</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">然后删除列族(注意NAME和METHOD要大写)：</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">hbase(main):008:0&gt; <span style="background-color:inherit;"><span style="color:#ff0000;">alter 'member',NAME=&gt;'member_id',METHOD=&gt;'delete'</span></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">Updating all regions with the new schema...</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">1/1 regions updated.</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">Done.</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">0 row(s) in 1.1640 seconds</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">删除列族之后再启用表：</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">hbase(main):009:0&gt; <span style="background-color:inherit;"><span style="color:#ff0000;">enable 'member'</span></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">0 row(s) in 1.2250 seconds</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">再次查看表信息，便可以发现member_id已经被删除</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">hbase(main):010:0&gt;<span style="color:#ff0000;">describe 'member'</span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">DESCRIPTION                                                                                                                                                                                      
      ENABLED                                                    </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> 'member', {NAME =&gt; '<span style="color:#ff0000;background-color:inherit;">address</span>', DATA_BLOCK_ENCODING =&gt; 'NONE', BLOOMFILTER =&gt; 'NONE', REPLICATION_SCOPE =&gt; '     true                
                                       </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> 0', VERSIONS =&gt; '3', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', TTL =&gt; '2147483647', KEEP_DELETED_CELLS =                                                           </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> &gt; 'false', BLOCKSIZE =&gt; '65536', IN_MEMORY =&gt; 'false', ENCODE_ON_DISK =&gt; 'true', BLOCKCACHE =&gt; 'true'}, {N                                                           </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> AME =&gt; '<span style="color:#ff0000;background-color:inherit;">info</span>', DATA_BLOCK_ENCODING =&gt; 'NONE', BLOOMFILTER =&gt; 'NONE', REPLICATION_SCOPE =&gt; '0', VERSIONS =&gt;                        
                                    </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">  '3', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', TTL =&gt; '2147483647', KEEP_DELETED_CELLS =&gt; 'false', BLOC                                                           </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> KSIZE =&gt; '65536', IN_MEMORY =&gt; 'false', ENCODE_ON_DISK =&gt; 'true', BLOCKCACHE =&gt; 'true'}                                                                              </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">1 row(s) in 0.0420 seconds</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><span style="background-color:inherit;"><strong>9、drop 表名    删除表</strong></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">删除表时也需要先将表disable，否则会报错，为方便先创建个临时表(user表)，然后将user表删除</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><span style="background-color:inherit;line-height:1.5;">hbase(main):019:0&gt; <span style="background-color:inherit;"><span style="color:#ff0000;">create 'user','name','age' </span></span></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">0 row(s) in 1.0900 seconds</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">没有禁用user表，直接删除，报以下错误：</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">hbase(main):020:0&gt; <span style="background-color:inherit;"><span style="color:#ff0000;">drop 'user'</span></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">ERROR: Table user is enabled. Disable it first.'</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">Here is some help for this command:</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">Drop the named table. Table must first be disabled: e.g. "hbase&gt; drop 't1'"</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">正确删除方法如下：</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">先禁用表：</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">hbase(main):021:0&gt; <span style="background-color:inherit;"><span style="color:#ff0000;">disable 'user'</span></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">0 row(s) in 1.1660 seconds</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">然后删除表：</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">hbase(main):022:0&gt; <span style="background-color:inherit;"><span style="color:#ff0000;">drop 'user'</span></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">0 row(s) in 1.0710 seconds</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">hbase(main):023:0&gt; </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><span style="background-color:inherit;"><strong>10、exists 表名    查看一个表是否存在</strong></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<div><span style="background-color:inherit;">hbase(main):023:0&gt; </span><span style="background-color:inherit;"><span style="color:#ff0000;">exists 'user'</span></span></div>
<div><span style="background-color:inherit;">Table user </span><span style="color:#ff0000;background-color:inherit;">does not exist  </span>                                                                              </div>
<div>0 row(s) in 0.0230 seconds</div>
<div><br style="background-color:inherit;"></div>
<div><span style="background-color:inherit;">hbase(main):024:0&gt; </span><span style="background-color:inherit;"><span style="color:#ff0000;">exists 'member'</span></span></div>
<div><span style="background-color:inherit;">Table member </span><span style="color:#ff0000;background-color:inherit;">does exist</span>                                                                                  </div>
<div>0 row(s) in 0.0750 seconds</div>
<br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><span style="background-color:inherit;"><strong>11、is_enabled    判断表是否enable</strong></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<div><span style="background-color:inherit;">hbase(main):025:0&gt; </span><span style="background-color:inherit;"><span style="color:#ff0000;">is_enabled 'member'</span></span></div>
<div>true                                                                                                    </div>
<div>0 row(s) in 0.0160 seconds</div>
</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><span style="background-color:inherit;"><strong>12、is_disabled    判断表是否disable</strong></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<div><span style="background-color:inherit;">hbase(main):026:0&gt;<span style="color:#ff0000;"> </span></span><span style="background-color:inherit;"><span style="color:#ff0000;">is_disabled 'member'</span></span></div>
<div>false                                                                                                    </div>
<div>0 row(s) in 0.0220 seconds</div>
<br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><span style="background-color:inherit;"><strong>13、put    插入数据</strong></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">hbase(main):076:0&gt; <span style="background-color:inherit;"><span style="color:#ff0000;">put 'member','xueba','info:age','25'</span></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">0 row(s) in 0.0170 seconds</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">为了方便使用下面命令的，这里多插入一些数据：</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<p style="background-color:inherit;"><span style="background-color:inherit;">put 'member','xueba','info:age','25'</span></p>
<p style="background-color:inherit;"><span style="background-color:inherit;">put 'member','xueba','info:birthday','1989-06-19'</span></p>
<p style="background-color:inherit;"><span style="background-color:inherit;">put '</span><span style="background-color:inherit;">member','xueba','info:company','tecent</span><span style="background-color:inherit;">'</span></p>
<p style="background-color:inherit;"><span style="background-color:inherit;">put '</span><span style="background-color:inherit;">member','xueba','address:contry','china</span><span style="background-color:inherit;">'</span></p>
<p style="background-color:inherit;"><span style="background-color:inherit;">put '</span><span style="background-color:inherit;">member','xueba','address:province','guangdong</span><span style="background-color:inherit;">'</span></p>
<p style="background-color:inherit;"><span style="background-color:inherit;">put '</span><span style="background-color:inherit;">member','xueba','address:city','shenzhen</span><span style="background-color:inherit;">'</span></p>
<p style="background-color:inherit;"><span style="background-color:inherit;">put 'member','xiaoming','info:age','24'</span></p>
<p style="background-color:inherit;"><span style="background-color:inherit;">put 'member','xiaoming','info:birthday','1990-03-22'</span></p>
<p style="background-color:inherit;"><span style="background-color:inherit;">put '</span><span style="background-color:inherit;">member','xiaoming','info:company','tecent</span><span style="background-color:inherit;">'</span></p>
<p style="background-color:inherit;"><span style="background-color:inherit;">put '</span><span style="background-color:inherit;">member','xiaoming','info:favorite','movie</span><span style="background-color:inherit;">'</span></p>
<p style="background-color:inherit;"><span style="background-color:inherit;">put '</span><span style="background-color:inherit;">member','xiaoming','address:contry','china</span><span style="background-color:inherit;">'</span></p>
<p style="background-color:inherit;"><span style="background-color:inherit;">put '</span><span style="background-color:inherit;">member','xiaoming','address:province','guangdong</span><span style="background-color:inherit;">'</span></p>
<p style="background-color:inherit;"><span style="background-color:inherit;">put '</span><span style="background-color:inherit;">member','xiaoming','address:city','guangzhou</span><span style="background-color:inherit;">'</span></p>
<br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><span style="background-color:inherit;"><strong>14、get    获取表数据</strong></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">获取xueba的信息：</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">hbase(main):090:0&gt; <span style="background-color:inherit;"><span style="color:#ff0000;">get 'member','xueba'</span></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">COLUMN                      CELL                                                                        </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> address:city               timestamp=1436932081705, value=shenzhen                                      </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> address:contry             timestamp=1436932081662, value=china                                        </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> address:province           timestamp=1436932081686, value=guangdong                                    </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> info:age                   timestamp=1436932081590, value=25                                            </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> info:birthday              timestamp=1436932081619, value=1989-06-19                                    </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> info:company               timestamp=1436932081642, value=tecent                                        </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">6 row(s) in 0.0080 seconds</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">获取xiaoming的info信息：</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">hbase(main):091:0&gt; <span style="background-color:inherit;"><span style="color:#ff0000;">get 'member','xiaoming','info'</span></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">COLUMN                      CELL                                                                        </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> info:age                   timestamp=1436932081724, value=24                                            </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> info:birthday              timestamp=1436932081745, value=1990-03-22                                    </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> info:company               timestamp=1436932081764, value=tecent                                        </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> info:favorite              timestamp=1436932081784, value=movie                                        </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">4 row(s) in 0.0560 seconds</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">获取xiaoming的age信息</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">hbase(main):092:0&gt;<span style="background-color:inherit;"> <span style="color:#ff0000;">get 'member','xiaoming','info:age'</span></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">COLUMN                      CELL                                                                        </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> info:age                   timestamp=1436932081724, value=24                                            </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">1 row(s) in 0.0060 seconds</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><span style="background-color:inherit;"><strong>15、put    更新记录</strong></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">将xueba的age改为26：</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">hbase(main):093:0&gt; <span style="background-color:inherit;"><span style="color:#ff0000;">put 'member','xueba','info:age','26'</span></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">0 row(s) in 0.0120 seconds</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">查看修改结果：</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">hbase(main):094:0&gt; <span style="background-color:inherit;"><span style="color:#ff0000;">get 'member','xueba','info:age'</span></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">COLUMN                      CELL                                                                        </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> info:age                   timestamp=1436932900139, value=26                                            </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">1 row(s) in 0.0070 seconds</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><span style="background-color:inherit;"><strong>16、通过timestamp获取数据</strong></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">hbase(main):099:0&gt; <span style="background-color:inherit;"><span style="color:#ff0000;">get 'member','xueba',{COLUMN=&gt;'info:age',TIMESTAMP=&gt;1436936294957}</span></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">COLUMN                      CELL                                                                        </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> info:age                   timestamp=1436936294957, value=26                                            </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">1 row(s) in 0.0100 seconds</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><span style="background-color:inherit;"><strong>17、scan 表名    全表扫描</strong></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">hbase(main):101:0&gt; <span style="background-color:inherit;"><span style="color:#ff0000;">scan 'member'</span></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">ROW                         COLUMN+CELL                                                                  </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> xiaoming                   column=address:city, timestamp=1436932131672, value=guangzhou                </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> xiaoming                   column=address:contry, timestamp=1436932081802, value=china                  </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> xiaoming                   column=address:province, timestamp=1436932081821, value=guangdong            </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> xiaoming                   column=info:age, timestamp=1436932081724, value=24                          </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> xiaoming                   column=info:birthday, timestamp=1436932081745, value=1990-03-22              </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> xiaoming                   column=info:company, timestamp=1436932081764, value=tecent                  </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> xiaoming                   column=info:favorite, timestamp=1436932081784, value=movie                  </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> xueba                      column=address:city, timestamp=1436932081705, value=shenzhen                </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> xueba                      column=address:contry, timestamp=1436932081662, value=china                  </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> xueba                      column=address:province, timestamp=1436932081686, value=guangdong            </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> xueba                      column=info:age, timestamp=1436936294957, value=26                          </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> xueba                      column=info:birthday, timestamp=1436932081619, value=1989-06-19              </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> xueba                      column=info:company, timestamp=1436932081642, value=tecent                  </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><span style="color:#ff0000;background-color:inherit;">2 row(s)</span> in 0.0270 seconds</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">这里数据是2行，分别是xiaoming、xueba</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><span style="background-color:inherit;"><strong>18、delete    删除数据</strong></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">删除xueba的age数据</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">hbase(main):102:0&gt; <span style="background-color:inherit;"><span style="color:#ff0000;">delete 'member','xueba','info:age'</span></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">0 row(s) in 0.0400 seconds</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">删除后查看xueba信息，已没有age数据</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">hbase(main):103:0&gt; <span style="background-color:inherit;"><span style="color:#ff0000;">get 'member','xueba'</span></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">COLUMN                      CELL                                                                        </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> address:city               timestamp=1436932081705, value=shenzhen                                      </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> address:contry             timestamp=1436932081662, value=china                                        </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> address:province           timestamp=1436932081686, value=guangdong                                    </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> info:birthday              timestamp=1436932081619, value=1989-06-19                                    </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> info:company               timestamp=1436932081642, value=tecent                                        </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">5 row(s) in 0.0160 seconds</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><span style="background-color:inherit;"><strong>19、deleteall    删除整行</strong></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">删除xiaoming的数据：</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">hbase(main):104:0&gt; <span style="background-color:inherit;"><span style="color:#ff0000;">deleteall 'member','xiaoming'</span></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">0 row(s) in 0.0100 seconds</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">查看删除情况，可以看出已没有xiaoming的数据</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">hbase(main):105:0&gt; <span style="background-color:inherit;"><span style="color:#ff0000;">get 'member','xiaoming'</span></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">COLUMN                      CELL                                                                        </div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">0 row(s) in 0.0050 seconds</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><span style="background-color:inherit;"><strong>20、count 表名    查询表中总共有多少行数据</strong></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">hbase(main):106:0&gt;<span style="color:#ff0000;">count 'member'</span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">1 row(s) in 0.0200 seconds</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">总共插入两行数据，上面将xiaoming的数据全部删除了，所以只剩一行xueba的数据</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><span style="background-color:inherit;"><strong>21、truncate 表名    清空表</strong></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">hbase(main):107:0&gt; <span style="background-color:inherit;"><span style="color:#ff0000;">truncate 'member'</span></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">Truncating 'member' table (it may take a while):</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> - Disabling table...</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> - Dropping table...</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> - Creating table...</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">0 row(s) in 3.2460 seconds</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">通过上面信息可以看出，清空表的操作是先禁用表，然后删除表，再重新创建表</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br style="background-color:inherit;"></div>
<br><p></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
            </div>
                </div>