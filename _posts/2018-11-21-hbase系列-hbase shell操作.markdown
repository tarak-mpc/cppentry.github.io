---
layout:     post
title:      hbase系列-hbase shell操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创，转载请声明出处。					https://blog.csdn.net/weixin_41279060/article/details/78799049				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div style="line-height:1.75;font-size:14px;"><span style="font-size:22px;"><strong>hbase shell操作</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:18px;color:rgb(223,64,42);"><strong>1、启动hbase shell</strong></span></div>
<div style="line-height:1.75;font-size:14px;">./bin/hbase shell</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:18px;color:rgb(223,64,42);"><strong>2、HBase常用命令status, version, table_help和whoami</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="color:rgb(223,64,42);"><strong>status</strong></span></div>
<div style="line-height:1.75;font-size:14px;">命令返回包括在系统上运行的服务器的细节和系统的状态。</div>
<div style="line-height:1.75;font-size:14px;">hbase(main):009:0&gt; status</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="color:rgb(223,64,42);"><strong>version</strong></span></div>
<div style="line-height:1.75;font-size:14px;">该命令返回HBase系统使用的版本。</div>
<div style="line-height:1.75;font-size:14px;">hbase(main):009:0&gt; version</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="color:rgb(223,64,42);"><strong>table_help</strong></span></div>
<div style="line-height:1.75;font-size:14px;">此命令将引导如何使用表引用的命令。</div>
<div style="line-height:1.75;font-size:14px;">hbase(main):02:0&gt; table_help</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="color:rgb(223,64,42);"><strong>whoami</strong></span></div>
<div style="line-height:1.75;font-size:14px;">该命令返回HBase用户详细信息。如果执行这个命令，返回当前HBase用户</div>
<div style="line-height:1.75;font-size:14px;">hbase(main):008:0&gt; whoami</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:18px;color:rgb(223,64,42);"><strong>3、创建表</strong></span></div>
<div style="line-height:1.75;font-size:14px;">可以使用命令创建一个表，在这里必须指定表名和列族名。在HBase shell中创建表的语法如下所示。</div>
<div style="line-height:1.75;font-size:14px;">create ‘&lt;table name&gt;’,’&lt;column family&gt;’</div>
<div style="line-height:1.75;font-size:14px;">下面给出的是一个表名为emp的样本模式。它有两个列族：“personal data”和“professional data”</div>
<div style="line-height:1.75;font-size:14px;">在HBase shell创建该表如下所示。</div>
<div style="line-height:1.75;font-size:14px;">hbase(main):002:0&gt; create 'emp', 'personal data', ’professional data’</div>
<div style="line-height:1.75;font-size:14px;"><span style="color:rgb(223,64,42);">注：列族可以理解为分区，一个表有多个分区，分区的设置有利于加快hbase的查询速度</span></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:18px;color:rgb(223,64,42);background-color:rgb(245,245,245);"><strong>4、</strong></span><span style="font-size:18px;color:rgb(223,64,42);"><strong>HBase列出表</strong></span></div>
<div style="line-height:1.75;font-size:14px;">list 是用来列出HBase中所有表的命令。</div>
<div style="line-height:1.75;font-size:14px;">hbase(main):001:0 &gt; list</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:18px;color:rgb(223,64,42);"><strong>5、HBase禁用表</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="color:rgb(223,64,42);"><strong>disable</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:15px;font-family:Arial;color:rgb(51,51,68);">要删除表或改变其设置，首先需要使用 disable 命令关闭表。使用 enable 命令，可以重新启用它。下面给出的语法是用来禁用一个表：</span></div>
<div style="line-height:1.33333;font-size:14px;">hbase(main):025:0&gt; disable 'emp'</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;">禁用表之后，仍然可以通过 list 和exists命令查看到。无法扫描到它存在，它会给下面的错误。</div>
<div style="line-height:1.75;font-size:14px;">hbase(main):028:0&gt; scan 'emp'</div>
<div style="line-height:1.75;font-size:14px;">ROW COLUMN+CELL</div>
<div style="line-height:1.75;font-size:14px;">ERROR: emp is disabled.</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="color:rgb(223,64,42);"><strong>is_disabled</strong></span></div>
<div style="line-height:1.75;font-size:14px;">这个命令是用来查看表是否被禁用。</div>
<div style="line-height:1.75;font-size:14px;">下面的例子验证表名为emp是否被禁用。如果禁用，它会返回true，如果没有，它会返回false。</div>
<div style="line-height:1.75;font-size:14px;">hbase(main):031:0&gt; is_disabled 'emp'</div>
<div style="line-height:1.75;font-size:14px;">true</div>
<div style="line-height:1.75;font-size:14px;">0 row(s) in 0.0440 seconds</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="color:rgb(223,64,42);"><strong>disable_all</strong></span></div>
<div style="line-height:1.75;font-size:14px;">此命令用于禁用所有匹配给定正则表达式的表。</div>
<div style="line-height:1.75;font-size:14px;">假设有5个表在HBase，即raja, rajani, rajendra, rajesh 和 raju。下面的代码将禁用所有以 raj 开始的表。</div>
<div style="line-height:1.75;font-size:14px;">hbase(main):002:0&gt; disable_all 'raj.*'rajarajanirajendrarajeshrajuDisable the above 5 tables (y/n)?y5 tables successfully disabled</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:18px;color:rgb(223,64,42);"><strong>6、启用表</strong></span></div>
<div style="line-height:1.75;font-size:14px;">启用表的语法：</div>
<div style="line-height:1.75;font-size:14px;">hbase(main):005:0&gt; enable 'emp'</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:18px;color:rgb(223,64,42);"><strong>7、HBase表描述</strong></span></div>
<div style="line-height:1.75;font-size:14px;">该命令返回表的说明。</div>
<div style="line-height:1.75;font-size:14px;">hbase(main):006:0&gt; describe 'emp'</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:18px;color:rgb(223,64,42);"><strong>8、修改表</strong></span></div>
<div style="line-height:1.75;font-size:14px;">alter用于更改现有表的命令。使用此命令可以更改列族的单元，设定最大数量和删除表范围运算符，并从表中删除列家族。</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="color:rgb(223,64,42);"><strong>更改列族单元格的最大数目</strong></span></div>
<div style="line-height:1.75;font-size:14px;">下面给出的语法来改变列家族单元的最大数目。</div>
<div style="line-height:1.75;font-size:14px;">hbase&gt; alter 't1', NAME =&gt; 'f1', VERSIONS =&gt; 5</div>
<div style="line-height:1.75;font-size:14px;">在下面的例子中，单元的最大数目设置为5。</div>
<div style="line-height:1.75;font-size:14px;">hbase(main):003:0&gt; alter 'emp', NAME =&gt; 'personal data', VERSIONS =&gt; 5</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="color:rgb(223,64,42);"><strong>表范围运算符</strong></span></div>
<div style="line-height:1.75;font-size:14px;">使用alter，可以设置和删除表范围，运算符，如MAX_FILESIZE，READONLY，MEMSTORE_FLUSHSIZE，DEFERRED_LOG_FLUSH等。</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="color:rgb(223,64,42);"><strong>设置只读</strong></span></div>
<div style="line-height:1.75;font-size:14px;">下面给出的是语法，是用以设置表为只读。</div>
<div style="line-height:1.75;font-size:14px;">hbase&gt;alter 't1', READONLY(option)</div>
<div style="line-height:1.75;font-size:14px;">在下面的例子中，我们已经设置表emp为只读。</div>
<div style="line-height:1.75;font-size:14px;">hbase(main):006:0&gt; alter 'emp', READONLY</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="color:rgb(223,64,42);"><strong>删除表范围运算符</strong></span></div>
<div style="line-height:1.75;font-size:14px;">也可以删除表范围运算。下面给出的是语法，从emp表中删除“MAX_FILESIZE”。</div>
<div style="line-height:1.75;font-size:14px;">hbase&gt; alter 't1', METHOD =&gt; 'table_att_unset', NAME =&gt; 'MAX_FILESIZE'</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="color:rgb(223,64,42);"><strong>删除列族</strong></span></div>
<div style="line-height:1.75;font-size:14px;">使用alter，也可以删除列族。下面给出的是使用alter删除列族的语法。</div>
<div style="line-height:1.75;font-size:14px;">hbase&gt; alter ‘ table name ’, ‘delete’ =&gt; ‘ column family ’</div>
<div style="line-height:1.75;font-size:14px;">下面给出的是一个例子，从“emp”表中删除列族。</div>
<div style="line-height:1.75;font-size:14px;">假设在HBase中有一个employee表。它包含以下数据：</div>
<div style="line-height:1.75;font-size:14px;">hbase(main):006:0&gt; scan 'employee'</div>
<div style="line-height:1.75;font-size:14px;">ROW COLUMN+CELL</div>
<div style="line-height:1.75;font-size:14px;">row1 column=personal:city, timestamp=1418193767, value=hyderabad</div>
<div style="line-height:1.75;font-size:14px;">row1 column=personal:name, timestamp=1418193806767, value=raju</div>
<div style="line-height:1.75;font-size:14px;">row1 column=professional:designation, timestamp=1418193767, value=manager</div>
<div style="line-height:1.75;font-size:14px;">row1 column=professional:salary, timestamp=1418193806767, value=50000</div>
<div style="line-height:1.75;font-size:14px;">1 row(s) in 0.0160 seconds</div>
<div style="line-height:1.75;font-size:14px;">现在使用alter命令删除指定的 professional 列族。</div>
<div style="line-height:1.75;font-size:14px;">hbase(main):007:0&gt; alter 'employee','delete'=&gt;'professional'</div>
<div style="line-height:1.75;font-size:14px;">现在验证该表中变更后的数据。观察列族“professional”也没有了，因为前面已经被删除了。</div>
<div style="line-height:1.75;font-size:14px;">hbase(main):003:0&gt; scan 'employee'</div>
<div style="line-height:1.75;font-size:14px;">ROW COLUMN+CELL</div>
<div style="line-height:1.75;font-size:14px;">row1 column=personal:city, timestamp=14181936767, value=hyderabad</div>
<div style="line-height:1.75;font-size:14px;">row1 column=personal:name, timestamp=1418193806767, value=raju</div>
<div style="line-height:1.75;font-size:14px;">1 row(s) in 0.0830 seconds</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:18px;color:rgb(223,64,42);"><strong>9、Exists</strong></span></div>
<div style="line-height:1.75;font-size:14px;">可以使用exists命令验证表的存在。下面的示例演示了如何使用这个命令。</div>
<div style="line-height:1.75;font-size:14px;">hbase(main):024:0&gt; exists 'emp'</div>
<div style="line-height:1.75;font-size:14px;">Table emp does exist</div>
<div style="line-height:1.75;font-size:14px;">0 row(s) in 0.0750 seconds</div>
<div style="line-height:1.75;font-size:14px;">==================================================================</div>
<div style="line-height:1.75;font-size:14px;">hbase(main):015:0&gt; exists 'student'</div>
<div style="line-height:1.75;font-size:14px;">Table student does not exist</div>
<div style="line-height:1.75;font-size:14px;">0 row(s) in 0.0480 seconds</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:18px;color:rgb(223,64,42);"><strong>10、删除表</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="color:rgb(223,64,42);"><strong>drop</strong></span></div>
<div style="line-height:1.75;font-size:14px;">用drop命令可以删除表。在删除一个表之前必须先将其禁用。</div>
<div style="line-height:1.75;font-size:14px;">hbase(main):018:0&gt; disable 'emp'</div>
<div style="line-height:1.75;font-size:14px;">0 row(s) in 1.4580 seconds</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;">hbase(main):019:0&gt; drop 'emp'</div>
<div style="line-height:1.75;font-size:14px;">0 row(s) in 0.3060 seconds</div>
<div style="line-height:1.75;font-size:14px;">使用exists 命令验证表是否被删除。</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;">hbase(main):020:0&gt; exists 'emp'</div>
<div style="line-height:1.75;font-size:14px;">Table emp does not exist</div>
<div style="line-height:1.75;font-size:14px;">0 row(s) in 0.0730 seconds</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="color:rgb(223,64,42);"><strong>drop_all</strong></span></div>
<div style="line-height:1.75;font-size:14px;">这个命令是用来在给出删除匹配“regex”表。它的语法如下：</div>
<div style="line-height:1.75;font-size:14px;">hbase&gt; drop_all ‘t.*’</div>
<div style="line-height:1.75;font-size:14px;">注意：要删除表，则必须先将其禁用。</div>
<div style="line-height:1.75;font-size:14px;">示例</div>
<div style="line-height:1.75;font-size:14px;">假设有一些表的名称为raja, rajani, rajendra</div>
<div style="line-height:1.75;font-size:14px;">所有这些表以字母raj开始。首先使用disable_all命令禁用所有这些表如下所示。</div>
<div style="line-height:1.75;font-size:14px;">hbase(main):002:0&gt; disable_all 'raj.*'</div>
<div style="line-height:1.75;font-size:14px;">raja</div>
<div style="line-height:1.75;font-size:14px;">rajani</div>
<div style="line-height:1.75;font-size:14px;">rajendra</div>
<div style="line-height:1.75;font-size:14px;">Disable the above 5 tables (y/n)?</div>
<div style="line-height:1.75;font-size:14px;">y</div>
<div style="line-height:1.75;font-size:14px;">3 tables successfully disabled</div>
<div style="line-height:1.75;font-size:14px;">现在，可以使用 drop_all 命令删除它们，如下所示。</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;">hbase(main):018:0&gt; drop_all 'raj.*'</div>
<div style="line-height:1.75;font-size:14px;">raja</div>
<div style="line-height:1.75;font-size:14px;">rajani</div>
<div style="line-height:1.75;font-size:14px;">rajendra</div>
<div style="line-height:1.75;font-size:14px;">Drop the above 5 tables (y/n)?</div>
<div style="line-height:1.75;font-size:14px;">y</div>
<div style="line-height:1.75;font-size:14px;">3 tables successfully dropped</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:18px;color:rgb(223,64,42);"><strong>11、退出和关闭</strong></span></div>
<div style="line-height:1.75;font-size:14px;">可以通过键入exit命令退出shell。</div>
<div style="line-height:1.75;font-size:14px;">hbase(main):021:0&gt; exit</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;">要停止HBase，浏览进入到HBase主文件夹，然后键入以下命令。</div>
<div style="line-height:1.75;font-size:14px;">./bin/stop-hbase.sh</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:18px;color:rgb(223,64,42);"><strong>12、创建数据</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:15px;font-family:Arial;color:rgb(51,51,68);">使用put命令，可以插入行到一个表。它的语法如下：</span></div>
<div style="line-height:1.33333;font-size:14px;"><span style="font-size:12px;background-color:rgb(245,245,245);">put</span><span style="font-size:12px;color:rgb(102,102,0);background-color:rgb(245,245,245);">’&lt;</span><span style="font-size:12px;background-color:rgb(245,245,245);">table
 name</span><span style="font-size:12px;color:rgb(102,102,0);background-color:rgb(245,245,245);">&gt;’,’</span><span style="font-size:12px;background-color:rgb(245,245,245);">row1</span><span style="font-size:12px;color:rgb(102,102,0);background-color:rgb(245,245,245);">’,’&lt;</span><span style="font-size:12px;background-color:rgb(245,245,245);">colfamily</span><span style="font-size:12px;color:rgb(102,102,0);background-color:rgb(245,245,245);">:</span><span style="font-size:12px;background-color:rgb(245,245,245);">colname</span><span style="font-size:12px;color:rgb(102,102,0);background-color:rgb(245,245,245);">&gt;’,’&lt;</span><span style="font-size:12px;background-color:rgb(245,245,245);">value</span><span style="font-size:12px;color:rgb(102,102,0);background-color:rgb(245,245,245);">&gt;’</span></div>
<div style="line-height:1.33333;font-size:14px;">插入第一行</div>
<div style="line-height:1.33333;font-size:14px;">hbase(main):005:0&gt; put 'emp','1','personal data:name','raju'</div>
<div style="line-height:1.33333;font-size:14px;"><br></div>
<div style="line-height:1.33333;font-size:14px;"><span style="font-size:18px;color:rgb(223,64,42);"><strong>13、更新数据</strong></span></div>
<div style="line-height:1.33333;font-size:14px;">可以使用put命令更新现有的单元格值。按照下面的语法，并注明新值，如下图所示。</div>
<div style="line-height:1.75;font-size:14px;">put ‘table name’,’rowkey’,'Column family:column name',’new value’</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;">假设HBase中有一个表emp拥有下列数据</div>
<div style="line-height:1.75;font-size:14px;">hbase(main):003:0&gt; scan 'emp'</div>
<div style="line-height:1.75;font-size:14px;">ROW COLUMN+CELL</div>
<div style="line-height:1.75;font-size:14px;">row1 column=personal:name, timestamp=1418051555, value=raju</div>
<div style="line-height:1.75;font-size:14px;">row1 column=personal:city, timestamp=1418275907, value=Hyderabad</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;">以下命令将更新名为“Raju'员工的城市值为'Delhi'。</div>
<div style="line-height:1.75;font-size:14px;">hbase(main):002:0&gt; put 'emp','row1','personal:city','Delhi'</div>
<div style="line-height:1.75;font-size:14px;">更新后的表如下所示，观察这个城市Raju的值已更改为“Delhi”。</div>
<div style="line-height:1.75;font-size:14px;">hbase(main):003:0&gt; scan 'emp'</div>
<div style="line-height:1.75;font-size:14px;">ROW COLUMN+CELL</div>
<div style="line-height:1.75;font-size:14px;">row1 column=personal:name, timestamp=1418035791555, value=raju</div>
<div style="line-height:1.75;font-size:14px;">row1 column=personal:city, timestamp=1418274645907, value=Delhi</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:18px;color:rgb(223,64,42);"><strong>14、读取数据</strong></span></div>
<div style="line-height:1.75;font-size:14px;">使用 get 命令，可以同时获取一行数据。它的语法如下：</div>
<div style="line-height:1.75;font-size:14px;">get ’&lt;table name&gt;’,’rowkey’</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:18px;color:rgb(223,64,42);"><strong>15、删除数据</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="color:rgb(223,64,42);"><strong>从表删除特定单元格</strong></span></div>
<div style="line-height:1.75;font-size:14px;">使用 delete 命令，可以在一个表中删除特定单元格。 delete 命令的语法如下：</div>
<div style="line-height:1.75;font-size:14px;">delete ‘&lt;table name&gt;’, ‘&lt;rowkey&gt;’, ‘&lt;column name &gt;’, ‘&lt;time stamp&gt;’</div>
<div style="line-height:1.75;font-size:14px;">下面是一个删除特定单元格和例子。在这里，我们删除salary</div>
<div style="line-height:1.75;font-size:14px;">hbase(main):006:0&gt; delete 'emp', '1', 'personal data:city',1417521848375</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="color:rgb(223,64,42);"><strong>删除表的所有单元格</strong></span></div>
<div style="line-height:1.75;font-size:14px;">使用“deleteall”命令，可以删除一行中所有单元格。下面给出是 deleteall 命令的语法。</div>
<div style="line-height:1.75;font-size:14px;">deleteall ‘&lt;table name&gt;’, ‘&lt;row&gt;’,</div>
<div style="line-height:1.75;font-size:14px;">这里是使用“deleteall”命令删去 emp 表 row1 的所有单元的一个例子。</div>
<div style="line-height:1.75;font-size:14px;">hbase(main):007:0&gt; deleteall 'emp','1'</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:18px;color:rgb(223,64,42);"><strong>16、扫描</strong></span></div>
<div style="line-height:1.75;font-size:14px;">使用 scan 命令可以得到表中的数据。它的语法如下：</div>
<div style="line-height:1.75;font-size:14px;">scan ‘&lt;table name&gt;’</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:18px;color:rgb(223,64,42);"><strong>17、计数和清空表</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="color:rgb(223,64,42);"><strong>count</strong></span></div>
<div style="line-height:1.75;font-size:14px;">可以使用count命令计算表的行数量。它的语法如下：</div>
<div style="line-height:1.75;font-size:14px;">count ‘&lt;table name&gt;’</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="color:rgb(223,64,42);"><strong>truncate</strong></span></div>
<div style="line-height:1.75;font-size:14px;">此命令将禁止删除并重新创建一个表。truncate 的语法如下：</div>
<div style="line-height:1.75;font-size:14px;">hbase&gt; truncate 'table name'</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:18px;color:rgb(223,64,42);"><strong>18、权限</strong></span></div>
<div style="line-height:1.75;font-size:14px;">我们可以授予和撤销HBase用户的权限。也有出于安全目的，三个命令：grant, revoke 和 user_permission.。</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="color:rgb(223,64,42);"><strong>grant</strong></span></div>
<div style="line-height:1.75;font-size:14px;">grant命令授予特定的权限，如读，写，执行和管理表给定一个特定的用户。 grant命令的语法如下：</div>
<div style="line-height:1.75;font-size:14px;">hbase&gt; grant &lt;user&gt; &lt;permissions&gt; [&lt;table&gt; [&lt;column family&gt; [&lt;column; qualifier&gt;]]</div>
<div style="line-height:1.75;font-size:14px;">我们可以从RWXCA组，其中给予零个或多个特权给用户</div>
<div style="line-height:1.75;font-size:14px;">R - 代表读取权限</div>
<div style="line-height:1.75;font-size:14px;">W - 代表写权限</div>
<div style="line-height:1.75;font-size:14px;">X - 代表执行权限</div>
<div style="line-height:1.75;font-size:14px;">C - 代表创建权限</div>
<div style="line-height:1.75;font-size:14px;">A - 代表管理权限</div>
<div style="line-height:1.75;font-size:14px;">下面给出是为用户“Tutorialspoint'授予所有权限的例子。</div>
<div style="line-height:1.75;font-size:14px;">hbase(main):018:0&gt; grant 'Tutorialspoint', 'RWXCA'</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="color:rgb(223,64,42);"><strong>revoke</strong></span></div>
<div style="line-height:1.75;font-size:14px;">revoke命令用于撤销用户访问表的权限。它的语法如下：</div>
<div style="line-height:1.75;font-size:14px;">hbase&gt; revoke &lt;user&gt;</div>
<div style="line-height:1.75;font-size:14px;">下面的代码撤消名为“Tutorialspoint”用户的所有权限。</div>
<div style="line-height:1.75;font-size:14px;">hbase(main):006:0&gt; revoke 'Tutorialspoint'</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="color:rgb(223,64,42);"><strong>user_permission</strong></span></div>
<div style="line-height:1.75;font-size:14px;">此命令用于列出特定表的所有权限。 user_permission的语法如下：</div>
<div style="line-height:1.75;font-size:14px;">hbase&gt;user_permission ‘tablename’</div>
<div style="line-height:1.75;font-size:14px;">下面的代码列出了“emp”表的所有用户权限。</div>
<div style="line-height:1.75;font-size:14px;">hbase(main):013:0&gt; user_permission 'emp'</div>
<div style="line-height:1.33333;font-size:14px;"><br></div>
<div style="line-height:1.33333;font-size:14px;"></div>
            </div>
                </div>