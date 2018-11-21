---
layout:     post
title:      hbase shell 中的常用的一些命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>由于工作需要，最近这段时间刚开始接触hbase，在这里整理一些常用的hbase的命令：</p>

<p>1. 连接上服务器之后，直接输入hbase shell，进入到hbase shell console控制台，hbase的一些命令是在这个控制台中执行的。</p>

<p>2. list ： 查看hbase所有表</p>

<p>3. describe '表名' ： 查看表结构</p>

<p>4. list_namespace : 查看所有的表空间</p>

<p>5. 查看表中的前五条数据： scan '表名'，{LIMIT=&gt;5}</p>

<p>6. 查看表中所有的数据： scan '表名'</p>

<p>7. 查看指定软件包的安装目录：find / -name 软件包部分名称* -type d ,这是一个模糊查看的操作,</p>

<p>     比如要查看kafka的安装目录：find / -name kafka* -type d,  *表示不确定的版本号信息，但是kafka的安装目录包名肯定是对的。</p>

<p>     比如要查看ES的安装目录：find / -name elasticsearch* -type d</p>

<p>=======目前掌握和了解这些简单的hbase的相关命令，以后及时更新</p>            </div>
                </div>