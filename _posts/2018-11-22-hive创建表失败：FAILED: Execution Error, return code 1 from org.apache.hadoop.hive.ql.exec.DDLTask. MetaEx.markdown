---
layout:     post
title:      hive创建表失败：FAILED: Execution Error, return code 1 from org.apache.hadoop.hive.ql.exec.DDLTask. MetaEx
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>成功启动hive之后，想要创建表，但是确产生错误。</p>

<p><img alt="" class="has" height="134" src="https://img-blog.csdn.net/20180726142615394?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21vX2luZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1200"></p>

<p> </p>

<p>登录所安装的数据库，我这里是centos7自带的mariadb数据库，登进去之后，</p>

<p style="text-indent:50px;">1、删除hive，  drop database hive; </p>

<p style="text-indent:50px;">2、重新创建hive， create database hive;</p>

<p style="text-indent:50px;">3、将字符集UTF-8修改为 latin1， alter database hive character set latin1;</p>

<p style="text-indent:50px;"><img alt="" class="has" height="36" src="https://img-blog.csdn.net/20180726142845259?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21vX2luZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="496"></p>

<p style="text-indent:0;">备注：删除表失败也可以这样通过这一系列操作来解决。（前提各种配置文件都正确）</p>

<p> </p>            </div>
                </div>