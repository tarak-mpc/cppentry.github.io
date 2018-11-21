---
layout:     post
title:      Hadoop之Hbase------>Hbase常用命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/hongchenlingtian/article/details/53991452				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<span style="font-size:14px;">首先进入hbase命令行（在../hbase/bin目录下操作)<br>
./hbase shell<br>
             <br>
显示hbase中的表<br>
list<br><br>
帮助<br>
help ‘create’<br>
创建user表，包含info、data两个列族<br>
create 'user', 'info1', 'data1'<br>
create 'user', {NAME =&gt; 'info', VERSIONS =&gt; '3'},{NAME =&gt; 'data', VERSIONS =&gt; '2'}<br><br>
退格键<br>
ctorl + Backspace<br><br>
查看表<br>
describe 'user'<br><br>
向user表中插入信息，row key为rk0001，列族info中添加name列标示符，值为zhangsan<br>
put 'user', 'rk0001', 'info:name', 'zhangsan'<br><br>
向user表中插入信息，row key为rk0001，列族info中添加gender列标示符，值为female<br>
put 'user', 'rk0001', 'info:gender', 'female'<br><br>
向user表中插入信息，row key为rk0001，列族info中添加age列标示符，值为20<br>
put 'user', 'rk0001', 'info:age', 20<br><br>
向user表中插入信息，row key为rk0001，列族data中添加pic列标示符，值为picture<br>
put 'user', 'rk0001', 'data:pic', 'picture'<br><br>
获取user表中row key为rk0001的所有信息<br>
get 'user', 'rk0001'<br><br>
获取user表中row key为rk0001，info列族的所有信息<br>
get 'user', 'rk0001', 'info'<br><br>
获取user表中row key为rk0001，info列族的name、age列标示符的信息<br>
get 'user', 'rk0001', 'info:name', 'info:age'<br><br>
获取user表中row key为rk0001，info、data列族的信息<br>
get 'user', 'rk0001', 'info', 'data'<br>
get 'user', 'rk0001', {COLUMN =&gt; ['info', 'data']}<br><br>
get 'user', 'rk0001', {COLUMN =&gt; ['info:name', 'data:pic']}<br><br>
获取user表中row key为rk0001，列族为info，版本号最新5个的信息<br>
get 'user', 'rk0001', {COLUMN =&gt; 'info', VERSIONS =&gt; 2}<br>
get 'user', 'rk0001', {COLUMN =&gt; 'info:name', VERSIONS =&gt; 5}<br>
get 'user', 'rk0001', {COLUMN =&gt; 'info:name', VERSIONS =&gt; 5, TIMERANGE =&gt; [1392368783980, 1392380169184]}<br><br>
获取user表中row key为rk0001，cell的值为zhangsan的信息<br>
get 'people', 'rk0001', {FILTER =&gt; "ValueFilter(=, 'binary:图片')"}<br><br>
获取user表中row key为rk0001，列标示符中含有a的信息<br>
get 'people', 'rk0001', {FILTER =&gt; "(QualifierFilter(=,'substring:a'))"}<br><br>
put 'user', 'rk0002', 'info:name', 'fanbingbing'<br>
put 'user', 'rk0002', 'info:gender', 'female'<br>
put 'user', 'rk0002', 'info:nationality', '中国'<br>
get 'user', 'rk0002', {FILTER =&gt; "ValueFilter(=, 'binary:中国')"}<br><br><br>
查询user表中的所有信息<br>
scan 'user'<br><br>
查询user表中列族为info的信息<br>
scan 'user', {COLUMNS =&gt; 'info'}<br>
scan 'user', {COLUMNS =&gt; 'info', RAW =&gt; true, VERSIONS =&gt; 5}<br>
scan 'persion', {COLUMNS =&gt; 'info', RAW =&gt; true, VERSIONS =&gt; 3}<br>
查询user表中列族为info和data的信息<br>
scan 'user', {COLUMNS =&gt; ['info', 'data']}<br>
scan 'user', {COLUMNS =&gt; ['info:name', 'data:pic']}<br><br><br>
查询user表中列族为info、列标示符为name的信息<br>
scan 'user', {COLUMNS =&gt; 'info:name'}<br><br>
查询覆盖的信息（内存没有刷新时才可查）<br>
scan 'user', {RAW =&gt;true VERSIONS =&gt; 10}<br><br>
查询user表中列族为info、列标示符为name的信息,并且版本最新的5个<br>
scan 'user', {COLUMNS =&gt; 'info:name', VERSIONS =&gt; 5}<br><br>
查询user表中列族为info和data且列标示符中含有a字符的信息<br>
scan 'user', {COLUMNS =&gt; ['info', 'data'], FILTER =&gt; "(QualifierFilter(=,'substring:a'))"}<br><br>
查询user表中列族为info，rk范围是[rk0001, rk0003)的数据<br>
scan 'people', {COLUMNS =&gt; 'info', STARTROW =&gt; 'rk0001', ENDROW =&gt; 'rk0003'}<br><br>
查询user表中row key以rk字符开头的<br>
scan 'user',{FILTER=&gt;"PrefixFilter('rk')"}<br><br>
查询user表中指定范围的数据<br>
scan 'user', {TIMERANGE =&gt; [1392368783980, 1392380169184]}<br><br>
删除数据<br>
删除user表row key为rk0001，列标示符为info:name的数据<br>
delete 'people', 'rk0001', 'info:name'<br>
删除user表row key为rk0001，列标示符为info:name，timestamp为1392383705316的数据<br>
delete 'user', 'rk0001', 'info:name', 1392383705316<br><br>
清空user表中的数据<br>
truncate 'people'<br><br>
修改表结构<br>
首先停用user表（新版本不用）<br>
disable 'user'<br><br>
添加两个列族f1和f2<br>
alter 'people', NAME =&gt; 'f1'<br>
alter 'user', NAME =&gt; 'f2'<br>
启用表<br>
enable 'user'<br><br>
###disable 'user'(新版本不用)<br>
删除一个列族：<br>
alter 'user', NAME =&gt; 'f1', METHOD =&gt; 'delete' 或 alter 'user', 'delete' =&gt; 'f1'<br><br>
添加列族f1同时删除列族f2<br>
alter 'user', {NAME =&gt; 'f1'}, {NAME =&gt; 'f2', METHOD =&gt; 'delete'}<br><br>
将user表的f1列族版本号改为5<br>
alter 'people', NAME =&gt; 'info', VERSIONS =&gt; 5<br>
启用表<br>
enable 'user'<br><br>
删除表<br>
disable 'user'<br>
drop 'user'<br></span><br>            </div>
                </div>