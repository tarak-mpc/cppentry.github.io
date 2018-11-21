---
layout:     post
title:      大数据技术之HBase第4章 HBase Shell操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h2 style="margin-left:0pt;"><strong><strong><strong>4.1 基本操作</strong></strong></strong></h2>

<p style="margin-left:0pt;">1） 进入HBase客户端命令行</p>

<p style="margin-left:0pt;"><span style="color:#7030a0;">[atguigu@hadoop10</span><span style="color:#7030a0;">2</span><span style="color:#7030a0;"> h</span><span style="color:#7030a0;">base</span><span style="color:#7030a0;">]$ bin/hbase shell</span></p>

<p style="margin-left:0pt;">2）查看帮助命令</p>

<p style="margin-left:0pt;"><span style="color:#7030a0;">hbase(main)</span><span style="color:#7030a0;">:001:0</span><span style="color:#7030a0;">&gt; help</span></p>

<p style="margin-left:0pt;">3）查看当前数据库中有哪些表</p>

<p style="margin-left:0pt;"><span style="color:#7030a0;">hbase(main)</span><span style="color:#7030a0;">:002:0</span><span style="color:#7030a0;">&gt; list</span></p>

<h2 style="margin-left:0pt;"><strong><strong><strong>4.2 表的操作</strong></strong></strong></h2>

<p style="margin-left:0pt;">1）创建表</p>

<p style="margin-left:0pt;"><span style="color:#7030a0;">hbase(main)</span><span style="color:#7030a0;">:002:0</span><span style="color:#7030a0;">&gt; create 'student','info'</span></p>

<p style="margin-left:0pt;">2）插入数据到表<br><span style="color:#7030a0;">hbase(main)</span><span style="color:#7030a0;">:003:0</span><span style="color:#7030a0;">&gt; put 'student','1001','info:sex','male'</span></p>

<p style="margin-left:0pt;"><span style="color:#7030a0;">hbase(main)</span><span style="color:#7030a0;">:004:0</span><span style="color:#7030a0;">&gt; put 'student','1001','info:age','18'</span></p>

<p style="margin-left:0pt;"><span style="color:#7030a0;">hbase(main)</span><span style="color:#7030a0;">:005:0</span><span style="color:#7030a0;">&gt; put 'student','1002','info:name','Janna'</span></p>

<p style="margin-left:0pt;"><span style="color:#7030a0;">hbase(main)</span><span style="color:#7030a0;">:006:0</span><span style="color:#7030a0;">&gt; put 'student','1002','info:sex','female'</span></p>

<p style="margin-left:0pt;"><span style="color:#7030a0;">hbase(main)</span><span style="color:#7030a0;">:007:0</span><span style="color:#7030a0;">&gt; put 'student','1002','info:age','20'</span></p>

<p style="margin-left:0pt;">3）扫描查看表数据</p>

<p style="margin-left:0pt;"><span style="color:#7030a0;">hbase(main)</span><span style="color:#7030a0;">:008:0</span><span style="color:#7030a0;">&gt; scan 'student'</span></p>

<p style="margin-left:0pt;"><span style="color:#7030a0;">hbase(main)</span><span style="color:#7030a0;">:009:0</span><span style="color:#7030a0;">&gt; scan 'student',{STARTROW =&gt; '1001', STOPROW  =&gt; '1001'}</span></p>

<p style="margin-left:0pt;"><span style="color:#7030a0;">hbase(main)</span><span style="color:#7030a0;">:010:0</span><span style="color:#7030a0;">&gt; scan 'student',{STARTROW =&gt; '1001'}</span></p>

<p style="margin-left:0pt;">4）查看表结构</p>

<p style="margin-left:0pt;"><span style="color:#7030a0;">hbase(main):01</span><span style="color:#7030a0;">1</span><span style="color:#7030a0;">:0&gt; describe ‘student’</span></p>

<p style="margin-left:0pt;">5）更新指定字段的数据</p>

<p style="margin-left:0pt;"><span style="color:#7030a0;">hbase(main):01</span><span style="color:#7030a0;">2</span><span style="color:#7030a0;">:0&gt; put 'student','1001','info:name','Nick'</span></p>

<p style="margin-left:0pt;"><span style="color:#7030a0;">hbase(main):01</span><span style="color:#7030a0;">3</span><span style="color:#7030a0;">:0&gt; put 'student','1001','info:age','100'</span></p>

<p style="margin-left:0pt;">6）查看“指定行”或“指定列族:列”的数据</p>

<p style="margin-left:0pt;"><span style="color:#7030a0;">hbase(main):01</span><span style="color:#7030a0;">4</span><span style="color:#7030a0;">:0&gt; get 'student','1001'</span></p>

<p style="margin-left:0pt;"><span style="color:#7030a0;">hbase(main):01</span><span style="color:#7030a0;">5</span><span style="color:#7030a0;">:0&gt; get 'student','1001','info:name'</span></p>

<p style="margin-left:0pt;">7）统计表数据行数</p>

<p style="margin-left:0pt;"><span style="color:#7030a0;">hbase(main):0</span><span style="color:#7030a0;">21</span><span style="color:#7030a0;">:0&gt; count 'student'</span></p>

<p style="margin-left:0pt;">8）删除数据</p>

<p style="margin-left:0pt;">删除某rowkey的全部数据：</p>

<p style="margin-left:0pt;"><span style="color:#7030a0;">hbase(main):01</span><span style="color:#7030a0;">6</span><span style="color:#7030a0;">:0&gt; deleteall 'student','1001'</span></p>

<p style="margin-left:0pt;">删除某rowkey的某一列数据：</p>

<p style="margin-left:0pt;"><span style="color:#7030a0;">hbase(main):01</span><span style="color:#7030a0;">7</span><span style="color:#7030a0;">:0&gt; delete 'student','1002','info:sex'</span></p>

<p style="margin-left:0pt;">9）清空表数据</p>

<p style="margin-left:0pt;"><span style="color:#7030a0;">hbase(main):01</span><span style="color:#7030a0;">8</span><span style="color:#7030a0;">:0&gt; truncate 'student'</span></p>

<p style="margin-left:0pt;"><span style="color:#ff0000;">提示：</span>清空表的操作顺序为先disable，然后再truncate。</p>

<p style="margin-left:0pt;">10）删除表</p>

<p style="margin-left:0pt;">首先需要先让该表为disable状态：</p>

<p style="margin-left:0pt;"><span style="color:#7030a0;">hbase(main):01</span><span style="color:#7030a0;">9</span><span style="color:#7030a0;">:0&gt; disable 'student'</span></p>

<p style="margin-left:0pt;">然后才能drop这个表：</p>

<p style="margin-left:0pt;"><span style="color:#7030a0;">hbase(main):0</span><span style="color:#7030a0;">20</span><span style="color:#7030a0;">:0&gt; drop 'student'</span></p>

<p style="margin-left:0pt;"><span style="color:#ff0000;">提示：</span>如果直接drop表，会报错：Drop the named table. Table must first be disabled</p>

<p style="margin-left:0pt;">ERROR: Table student is enabled. Disable it first.</p>

<p style="margin-left:0pt;">11）变更表信息</p>

<p style="margin-left:0pt;">将info列族中的数据存放3个版本：</p>

<p style="margin-left:0pt;"><span style="color:#7030a0;">hbase(main):0</span><span style="color:#7030a0;">22</span><span style="color:#7030a0;">:0&gt; alter 'student',{NAME=&gt;'info',VERSIONS=&gt;3}</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><span style="color:#7030a0;">本教程由尚硅谷教育大数据研究院出品，如需转载请注明来源。</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"> </p>            </div>
                </div>