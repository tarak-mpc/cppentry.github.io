---
layout:     post
title:      hbase(二)--常用hbase shell
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/chinabestchina/article/details/82861354				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p><strong>一、简介</strong><br>
这里介绍，在hbase shell中，常用的表操作及数据操作。<br><strong>二、执行shell的方式</strong><br>
1、交互式执行hbase shell, 即 bin/hbase shell<br>
直接进入hbase shell，进行shell交互<br>
2、以非交互方式执行hbase shell命令，格式如：echo " 命令 " | ${hbase_shell}<br>
3、参非交互方式执行hbase shell文件，格式如：bin/hbase shell 文件<br><strong>三、表操作</strong><br>
1、创建表<br>
包含表名，列簇名，列簇可多个,逗号分隔<br>
命令格式: ceate 'table' 'family','family'<br>
2、查看所有表<br>
命令格式: list<br>
3、查看指定表<br>
命令格式：desc 'table'<br>
4、删除表<br>
删除表,分两步：首先disable 表 ，再drop 表。<br>
disable 'table'<br>
drop 'table<br><strong>四、数据操作</strong><br>
1、插入或更新数据（已经存在则更新）<br>
命令格式: put 'table','rowkey','family:column','value'<br>
2、查询指定rowkey数据<br>
命令格式: get 'table','rowkey'<br>
命令格式: get 'table','rowkey','family:column'<br>
3、扫描表数据<br>
命令格式: scan 'stu'<br>
4、扫描表数据,LIMIT限制行数,COLUMNS限制显示列<br>
命令格式: scan 'table',{COLUMNS=&gt;['family:col'],LIMIT=&gt;rows}<br>
5、删除数据<br>
命令格式: delete 'table','rowkey','family:column'<br>
6、统计表中数据量<br>
命令格式：命令格式: count 'table'<br><strong>五、示例</strong><br>
请参数代码注释</p>

<pre>
#!/usr/bin/env bash

hbase_shell='/home/china/programs/hbase/bin/hbase shell'

#进入shell
bin/hbase shell

#以非交互方式执行命令
#命令格式: echo " 命令 " | ${hbase_shell}
echo " create 'school', 'info' " | ${hbase_shell}

#执行文件中的命令
#命令格式: bin/hbase shell 文件

########################## 表操作 #############################
#创建表stu,列簇info,列簇可多个,逗号分隔
#命令格式: ceate 'table' 'family','family'
echo " create 'stu', 'info' " | ${hbase_shell}
echo " create 'teacher', 'info','extra','log' " | ${hbase_shell}

#查看所有表
echo " list " | ${hbase_shell}

#查看指定表定义
#命令格式: desc 'table'
echo " desc 'stu' " | ${hbase_shell}
echo " desc 'teacher' " | ${hbase_shell}

#删除表,分两步：一,disable 表  二,drop 表
#命令格式: disable 'table'
echo " disable 'stu' " | ${hbase_shell}
#命令格式: drop 'table'
echo " drop 'stu' " | ${hbase_shell}
#####################################################################

############################ 数据操作 ################################
#插入或更新数据（已经存在则更新）
#命令格式: put 'table','rowkey','family:column','value'
echo " put 'stu','row1','info:name','apple1' " | ${hbase_shell}
echo " put 'stu','row1','info:age',18 " | ${hbase_shell}
echo " put 'stu','row2','info:name','apple2' " | ${hbase_shell}
echo " put 'stu','row2','info:age',18 " | ${hbase_shell}

#查询指定rowkey数据
#命令格式: get 'table','rowkey'
echo " get 'stu','row1' " | ${hbase_shell}
#命令格式: get 'table','rowkey','family:column'
echo " get 'stu','row1','info:name' " | ${hbase_shell}

#扫描表数据
#命令格式: scan 'stu'
echo " scan 'stu' " | ${hbase_shell}

#扫描表数据,LIMIT限制行数,COLUMNS限制显示列
#命令格式: scan 'table',{COLUMNS=&gt;['family:column'],LIMIT=&gt;rows}
echo " scan 'stu',{COLUMNS=&gt;['info:name'],LIMIT=&gt;1}  " | ${hbase_shell}

#删除数据
#命令格式: delete 'table','rowkey','family:column'
echo " delete 'stu','row1','info:age'  " | ${hbase_shell}

#统计表中数据量
#命令格式: count 'table'
echo " count 'stu'" | ${hbase_shell}
echo " count 'stu',{INTERVAL=&gt;1} " | ${hbase_shell}
#####################################################################


</pre>

<p> </p>

<p> </p>            </div>
                </div>