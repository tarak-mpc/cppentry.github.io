---
layout:     post
title:      建立 HDFS 各类目录权限 及shell 脚本代码
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>Athor: F</p>
<p># 1、创建默认组及权限：</p>
<p></p>
<p>#!/bin/bash<br>
#<br>
## 初始化默认权限<br>
sudo -u hdfs hdfs dfs -chown -R root:root /hive<br>
sudo -u hdfs hdfs dfs -chmod -R 771 /hive<br><br>
sudo -u hdfs hdfs dfs -chown -R root:root /dw<br>
sudo -u hdfs hdfs dfs -chmod -R 771 /dw<br><br>
## 创建默认group <br>
groupadd default<br>
groupadd stage<br>
groupadd ods<br>
groupadd dms<br>
groupadd st<br>
groupadd dml<br><br>
## 分配默认group权限<br>
sudo -u hdfs hdfs dfs -chown -R root:default /hive<br>
sudo -u hdfs hdfs dfs -chown -R root:stage /dw/stage<br>
sudo -u hdfs hdfs dfs -chown -R root:ods /dw/ods<br>
sudo -u hdfs hdfs dfs -chown -R root:dms /dw/dms<br>
sudo -u hdfs hdfs dfs -chown -R root:st /dw/st<br>
sudo -u hdfs hdfs dfs -chown -R root:dml /dw/dml</p>
<br><p>#########################################################</p>
<p># 2、建立 HDFS 各类目录权限 :<br></p>
<p>#!/bin/bash<br>
#<br><br>
####################################################################################<br>
#创建组，权执行一次<br>
groupadd admin<br><br>
#创建组admin权限</p>
<p>sudo -u hdfs hdfs dfs -setfacl -R -m group:admin:rwx /<br><br>
#分配admin组用户<br>
usermod -a -G admin fu<br>
usermod -a -G admin root<br>
usermod -a -G admin wu<br><br>
####################################################################################<br>
#创建组，权执行一次<br>
groupadd bdc<br><br>
#创建组bdc权限<br>
sudo -u hdfs hdfs dfs -setfacl -R -m group:bdc:r-x /<br>
sudo -u hdfs hdfs dfs -setfacl -R -m group:bdc:rwx /hive<br>
sudo -u hdfs hdfs dfs -setfacl -R -m group:bdc:--- /dw/stage<br><br>
#分配bdc组用户<br>
usermod -a -G bdc fu<br>
usermod -a -G bdc guo<br><br>
####################################################################################<br>
#创建组，权执行一次<br>
groupadd etl<br><br>
#创建组etl权限<br>
sudo -u hdfs hdfs dfs -setfacl -R -m group:etl:rwx /dw/stage<br>
sudo -u hdfs hdfs dfs -setfacl -R -m group:etl:rwx /dw/ods<br>
sudo -u hdfs hdfs dfs -setfacl -R -m group:etl:rwx /hbase<br><br>
#分配etl组用户<br>
usermod -a -G etl etl<br><br>
####################################################################################<br>
#创建组，权执行一次<br>
groupadd bidw<br><br>
#创建组bidw权限<br>
sudo -u hdfs hdfs dfs -setfacl -R -m group:bidw:rwx /dw/stage<br>
sudo -u hdfs hdfs dfs -setfacl -R -m group:bidw:rwx /dw/ods<br>
sudo -u hdfs hdfs dfs -setfacl -R -m group:bidw:rwx /dw/dms<br>
sudo -u hdfs hdfs dfs -setfacl -R -m group:bidw:rwx /dw/st<br>
sudo -u hdfs hdfs dfs -setfacl -R -m group:bidw:rwx /dw/dml<br>
sudo -u hdfs hdfs dfs -setfacl -R -m group:bidw:rwx /hbase<br><br>
#分配bidw组用户<br>
usermod -a -G bidw guo<br>
usermod -a -G bidw zhou<br><br>
#############################################################<br>
#创建组，权执行一次<br>
groupadd dam<br><br>
#创建组dam权限<br>
sudo -u hdfs hdfs dfs -setfacl -R -m group:dam:rwx /dw/st<br>
sudo -u hdfs hdfs dfs -setfacl -R -m group:dam:rwx /dw/dml<br>
sudo -u hdfs hdfs dfs -setfacl -R -m group:bidw:rwx /hbase<br><br>
#分配dam组用户<br>
usermod -a -G dam zhou<br></p>
<p><br></p>
<p><br></p>
            </div>
                </div>