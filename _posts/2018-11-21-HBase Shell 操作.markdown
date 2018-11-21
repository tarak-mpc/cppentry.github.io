---
layout:     post
title:      HBase Shell 操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/Luomingkui1109/article/details/82740379				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>1.基本操作</p>

<p>(1) 进入HBase客户端命令行</p>

<p>    [luomk@hadoop102 hbase]$ bin/hbase shell</p>

<p>(2) 查看帮助命令</p>

<p>    hbase(main):001:0&gt; help</p>

<p>(3) 查看当前数据库中有哪些表</p>

<p>    hbase(main):002:0&gt; list</p>

<p> </p>

<p>2.表的操作</p>

<p>(1) 创建表</p>

<p>    hbase(main):002:0&gt; create 'student','info'</p>

<p>(2) 插入数据到表</p>

<p>    hbase(main):003:0&gt; put 'student','1001','info:sex','male'</p>

<p>    hbase(main):004:0&gt; put 'student','1001','info:age','18'</p>

<p>    hbase(main):005:0&gt; put 'student','1002','info:name','Janna'</p>

<p>    hbase(main):006:0&gt; put 'student','1002','info:sex','female'</p>

<p>    hbase(main):007:0&gt; put 'student','1002','info:age',’20'</p>

<p>    查看效果：</p>

<pre class="has">
<code>hbase(main):015:0&gt; scan 'student'

ROW                                                      COLUMN+CELL

1001                                                    column=info:age, timestamp=1537168179371, value=18

1001                                                    column=info:sex, timestamp=1537168171354, value=male

1002                                                    column=info:age, timestamp=1537168195932, value=20

1002                                                    column=info:name, timestamp=1537168185919, value=Janna

1002                                                    column=info:sex, timestamp=1537168191050, value=female

2 row(s) in 0.0140 seconds</code></pre>

<p> </p>

<p>(3) 扫描查看表数据</p>

<p>    hbase(main):008:0&gt; scan 'student'</p>

<p>    hbase(main):009:0&gt; scan 'student',{STARTROW =&gt; '1001', STOPROW  =&gt; '1001'}</p>

<p>    hbase(main):010:0&gt; scan 'student',{STARTROW =&gt; '1001'}</p>

<p>(4) 查看表结构</p>

<p>    hbase(main):011:0&gt; describe 'student’</p>

<p>(5) 更新指定字段的数据</p>

<p>    hbase(main):012:0&gt; put 'student','1001','info:name','Nick'</p>

<p>    hbase(main):013:0&gt; put 'student','1001','info:age','100'</p>

<p>(6) 查看“指定行”或“指定列族:列”的数据</p>

<p>    hbase(main):014:0&gt; get 'student','1001'</p>

<p>    hbase(main):015:0&gt; get 'student','1001','info:name'</p>

<p>(7) 统计表数据行数</p>

<p>    hbase(main):021:0&gt; count 'student'</p>

<p>(8) 删除数据</p>

<p>    删除某rowkey的全部数据：</p>

<p>    hbase(main):016:0&gt; deleteall 'student','1001'</p>

<p>    删除某rowkey的某一列数据：</p>

<p>    hbase(main):017:0&gt; delete 'student','1002','info:sex'</p>

<p>(9) 清空表数据</p>

<p>    hbase(main):018:0&gt; truncate 'student'</p>

<p>    提示：清空表的操作顺序为先disable，然后再truncate。</p>

<p>(10) 删除表</p>

<p>    首先需要先让该表为disable状态：</p>

<p>    hbase(main):019:0&gt; disable 'student'</p>

<p>    然后才能drop这个表：</p>

<p>    hbase(main):020:0&gt; drop 'student'</p>

<p>    提示：如果直接drop表，会报错：ERROR: Table student is enabled. Disable it first.</p>

<p>(11) 变更表信息</p>

<p>    将info列族中的数据存放3个版本：</p>

<p>    hbase(main):022:0&gt; alter 'student',{NAME=&gt;'info',VERSIONS=&gt;3}</p>

<p> </p>

<p>3.命名空间的操作</p>

<p>(1) 介绍</p>

<p>    在HBase中，namespace命名空间指对一组表的逻辑分组，类似RDBMS中的database，方便对表在业务上划分。Apache HBase从0.98.0, 0.95.2两个版本开始支持namespace级别的授权操作，HBase全局管理员可以创建、修改和回收namespace的授权。</p>

<p>(2) namespace</p>

<p>    HBase系统默认定义了两个缺省的namespace</p>

<p>    hbase：系统内建表，包括namespace和meta表</p>

<p>    default：用户建表时未指定namespace的表都创建在此</p>

<pre class="has">
<code>创建namespace

hbase&gt;create_namespace 'ai_ns'

删除namespace

hbase&gt;drop_namespace 'ai_ns'

查看namespace

hbase&gt;describe_namespace 'ai_ns'

列出所有namespace

hbase&gt;list_namespace

在namespace下创建表

hbase&gt;create 'ai_ns:testtable', 'fm1'

查看namespace下的表

hbase&gt;list_namespace_tables 'ai_ns'</code></pre>

<p> </p>

<p> </p>            </div>
                </div>