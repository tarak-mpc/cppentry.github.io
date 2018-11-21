---
layout:     post
title:      Hbase Shell常用命令--附实例
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h3 style="margin-left:0in;"><span style="color:#f33b45;">1.进入Hbase shell命令</span></h3>

<p style="margin-left:0in;">       $&gt;hbase shell</p>

<h3 style="margin-left:0in;"><span style="color:#f33b45;">2.建议参照“help”命令</span></h3>

<h3 style="margin-left:0in;"><span style="color:#f33b45;">3.shell命令</span></h3>

<h3 style="margin-left:0in;">       create创建表指令：</h3>

<p style="margin-left:0in;">           hbase(main):021:0&gt; create 'customer','baseinfo','address'</p>

<h3 style="margin-left:0in;">      put插入数据：</h3>

<p style="margin-left:0in;">           hbase(main):021:0&gt; put 'customer','row-1','baseinfo:name','zhangsan'</p>

<p style="margin-left:0in;">           hbase(main):021:0&gt; put 'customer','row-1','baseinfo:age',12</p>

<p style="margin-left:0in;">           hbase(main):021:0&gt; put 'customer','row-1','baseinfo:sex','男'</p>

<p style="margin-left:0in;">           hbase(main):021:0&gt; put 'customer','row-1','address:city','长春'</p>

<h3 style="margin-left:0in;">       scan扫描表：</h3>

<p style="margin-left:0in;">            hbase(main):021:0&gt; scan 'customer'</p>

<p style="margin-left:0in;"> ROW                                 COLUMN+CELL                                                                                           </p>

<p style="margin-left:0in;"> row-1                              column=address:city, timestamp=1533057164343, value=changchun                                         </p>

<p style="margin-left:0in;"> row-1                              column=baseinfo:age, timestamp=1533057275350, value=23                                                </p>

<p style="margin-left:0in;"> row-1                              column=baseinfo:name, timestamp=1533056802314, value=zhangsan                                         </p>

<p style="margin-left:0in;"> row-1                              column=baseinfo:sex, timestamp=1533057120030, value=\xE7\x94\xB7\x0A                                  </p>

<p style="margin-left:0in;">1 row(s) in 0.1030 seconds</p>

<p style="margin-left:0in;">          说明：当前显示的为一条记录；</p>

<h3 style="margin-left:0in;">       get获取指令：</h3>

<p style="margin-left:0in;">            hbase(main):021:0&gt; get 'customer','row-1'</p>

<p style="margin-left:0in;">COLUMN                              CELL                                                                                                  </p>

<p style="margin-left:0in;"> address:city                       timestamp=1533057164343, value=changchun                                                              </p>

<p style="margin-left:0in;"> baseinfo:age                       timestamp=1533057275350, value=23                                                                     </p>

<p style="margin-left:0in;"> baseinfo:name                      timestamp=1533056802314, value=zhangsan                                                               </p>

<p style="margin-left:0in;"> baseinfo:sex                       timestamp=1533057120030, value=\xE7\x94\xB7\x0A                                                       </p>

<p style="margin-left:0in;">1 row(s) in 0.1500 seconds</p>

<h3 style="margin-left:0in;">       delete删除记录指令：</h3>

<p style="margin-left:0in;">            hbase(main):072:0&gt; delete 'customer','row-2','baseinfo:name'</p>

<p style="margin-left:0in;">       describe描述指令检索结构：</p>

<p style="margin-left:0in;">    hbase(main):084:0&gt; desc 'customer'</p>

<p style="margin-left:0in;">Table customer is ENABLED                                                                                                                 </p>

<p style="margin-left:0in;">customer                                                                                                                                  </p>

<p style="margin-left:0in;">COLUMN FAMILIES DESCRIPTION                                                                                                               </p>

<p style="margin-left:0in;">{NAME =&gt; 'address', BLOOMFILTER =&gt; 'ROW', VERSIONS =&gt; '1', IN_MEMORY =&gt; 'false', KEEP_DELETED_CELLS =&gt; 'FALSE', DATA_BLOCK_ENCODING =&gt; 'NONE', TTL =&gt; 'FOREVER', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', BLOCKCACHE =&gt; 'true', BLOCKSIZE =&gt; '65536', REPLICATION_SCOPE =&gt; '0'}   </p>

<p style="margin-left:0in;">{NAME =&gt; 'baseinfo', BLOOMFILTER =&gt; 'ROW', VERSIONS =&gt; '1', IN_MEMORY =&gt; 'false', KEEP_DELETED_CELLS =&gt; 'FALSE', DATA_BLOCK_ENCODING =&gt; 'NONE', TTL =&gt; 'FOREVER', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', BLOCKCACHE =&gt; 'true', BLOCKSIZE =&gt; '65536', REPLICATION_SCOPE =&gt; '0'}  </p>

<p style="margin-left:0in;">2 row(s) in 0.5850 seconds</p>

<p style="margin-left:0in;">       VERSION版本，默认version为1，按timestamp时间戳进行降序：</p>

<p style="margin-left:0in;">           修改版本号：hbase(main):086:0&gt; alter 'customer',{NAME=&gt;'baseinfo',VERSIONS=&gt;5}</p>

<p style="margin-left:0in;">                       hbase(main):084:0&gt; desc 'customer'</p>

<p style="margin-left:0in;">   通过scan '表名',{VERSIONS=&gt;*}指令，检索当前表的数据版本；</p>

<p style="margin-left:0in;">   执行delete删除命令时，如需按照ts(timestamp)时间戳进行删除操作时，之前版本一并删除掉；</p>

<p style="margin-left:0in;">       drop删除table指令：</p>

<p style="margin-left:0in;">           hbase(main):084:0&gt; disable 'ns1:tablename'    //说明：禁用table</p>

<p style="margin-left:0in;">   hbase(main):084:0&gt; drop 'ns1:tablename'       //说明：删除table</p>

<p style="margin-left:0in;">       get_table指令：将table映射成相对应的变量值(table实例)，通过table实例对表进行相关操作</p>

<p style="margin-left:0in;">           hbase(main):117:0&gt; t1 = get_table 'customer'</p>

<p style="margin-left:0in;">   hbase(main):118:0&gt; t1.scan</p>

<p style="margin-left:0in;">   hbase(main):119:0&gt; t1.put 'row-2','baseinfo:name','zhangsan'</p>

<p style="margin-left:0in;">           hbase(main):120:0&gt; t1.get 'row-2'</p>

<p style="margin-left:0in;">       namespace名字空间,相当于传统数据库的表空间：</p>

<p style="margin-left:0in;">           hbase(main):003:0&gt; create_namespace 'ns1',{'author'=&gt;'zhangyuejiu','data'=&gt;'2018-07-31'}</p>

<p style="margin-left:0in;">           hbase(main):005:0&gt; describe_namespace 'ns1'</p>

<p style="margin-left:0in;">DESCRIPTION                                                                                                                               </p>

<p style="margin-left:0in;">{NAME =&gt; 'ns1', author =&gt; 'zhangyuejiu', data =&gt; '2018-07-31'}                                                                            </p>

<p style="margin-left:0in;">1 row(s) in 0.0620 seconds</p>

<p style="margin-left:0in;">       balance负载均衡指令，需使用开关模式，原因负载均衡比较耗费资源：</p>

<p style="margin-left:0in;">           hbase(main):005:0&gt; balance_switch true    //开启负载均衡</p>

<p style="margin-left:0in;">   hbase(main):005:0&gt; balancer               //执行负载均衡</p>

<p style="margin-left:0in;">           hbase(main):005:0&gt; balance_switch false   //关闭负载均衡</p>

<p style="margin-left:0in;">   hbase(main):005:0&gt; balancer "force"       //强制负载均衡</p>

<h3 style="margin-left:0in;">    4.测试，使用Ruby语法添加数据</h3>

<p style="margin-left:0in;">   hbase(main):003:0&gt; create_namespace 'ns1'</p>

<p style="margin-left:0in;">           hbase(main):003:0&gt; create 'ns1:student','info'</p>

<p style="margin-left:0in;">           hbase(main):003:0&gt; for i in 'a'..'z' do   for j in 'a'..'z' do \</p>

<p style="margin-left:0in;">           hbase(main):009:2* put 'ns1:student',"row-#{i}#{j}","info:#{j}","#{j}"  end end</p>            </div>
                </div>