---
layout:     post
title:      HBase Shell 操作命令&&使用Sqoop将数据导入HBase
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，欢迎诸位分享交流					https://blog.csdn.net/xiaoshunzi111/article/details/49252665				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:18px;color:#000000;">一、</span><span style="font-size:18px;color:#000000;">HBase Shell <span style="font-family:'黑体';">操作命令实验</span></span></p>
<p><span style="font-size:18px;">要求：</span></p>
<p><span style="font-size:18px;">HBase<span style="font-family:'宋体';">集群正常启动，且可以运行正常</span></span></p>
<p><span style="font-size:18px;">进入客户端</span></p>
<p><span style="font-size:18px;color:#333333;">[zkpk@master ~]$ cd /home/zkpk/hbase-0.98.7-hadoop2</span></p>
<p><span style="font-size:18px;color:#333333;">[zkpk@master hbase-0.98.7-hadoop2]$ bin/hbase shell</span></p>
<p><span style="font-size:18px;">查看列表</span></p>
<p><span style="font-size:18px;color:#333333;">hbase(main):001:0&gt; list</span></p>
<p><span style="font-size:18px;">创建表<span style="font-family:'Times New Roman';">test</span></span></p>
<p><span style="font-size:18px;color:#333333;">hbase(main):002:0&gt; create 'test', {NAME =&gt; 'f1', VERSIONS =&gt; 5}</span></p>
<p><span style="font-size:18px;">再次查看列表对比</span></p>
<p><span style="font-size:18px;color:#333333;">hbase(main):003:0&gt; list</span></p>
<p><span style="font-size:18px;">插入数据</span></p>
<p><span style="font-size:18px;color:#333333;">hbase(main):001:0&gt; put 'test', 'aid001', 'f1:uid', '001'</span></p>
<p><span style="font-size:18px;">扫描查询数据</span></p>
<p><span style="font-size:18px;color:#333333;">hbase(main):001:0&gt; scan 'test'</span></p>
<p><span style="font-size:18px;">单条查询数据</span></p>
<p><span style="font-size:18px;color:#333333;">hbase(main):002:0&gt; get 'test','aid001'</span></p>
<p><span style="font-size:18px;">查看表结构</span></p>
<p><span style="font-size:18px;color:#333333;">hbase(main):003:0&gt; describe 'test'</span></p>
<p><span style="font-size:18px;">修改表</span></p>
<p><span style="font-size:18px;color:#333333;">hbase(main):004:0&gt; disable 'test'</span></p>
<p><span style="font-size:18px;color:#333333;">hbase(main):004:0&gt; alter 'test', NAME =&gt; 'f1', VERSIONS =&gt; 3</span></p>
<p><span style="font-size:18px;color:#333333;">hbase(main):004:0&gt; enable 'test'</span></p>
<p><span style="font-size:18px;color:#333333;background:rgb(240,247,254);"> </span></p>
<p><span style="font-size:18px;">再次查看表结构对比</span></p>
<p><span style="font-size:18px;color:#333333;">[zkpk@master ~]$ cd /home/zkpk/hbase-0.98.7-hadoop2</span></p>
<p><span style="font-size:18px;color:#333333;">hbase(main):004:0&gt; describe 'test'</span></p>
<p><span style="font-size:18px;">清空表</span></p>
<p><span style="font-size:18px;color:#333333;">hbase(main):004:0&gt; truncate 'test'</span></p>
<p><span style="font-size:18px;">扫描表</span></p>
<p><span style="font-size:18px;color:#333333;">hbase(main):004:0&gt; scan 'test'</span></p>
<p><span style="font-size:18px;">删除表</span></p>
<p><span style="font-size:18px;color:#333333;">hbase(main):004:0&gt; disable 'test'</span></p>
<p><span style="font-size:18px;color:#333333;">hbase(main):004:0&gt; drop 'test'</span></p>
<p><span style="font-size:18px;color:#333333;">hbase(main):004:0&gt; list</span></p>
<p><span style="font-size:18px;">创建新表<span style="font-family:'Times New Roman';">uid_cnt</span></span></p>
<p><span style="font-size:18px;color:#333333;">hbase(main):004:0&gt; create 'uid_cnt', {NAME =&gt; 'f1', VERSIONS =&gt; 5}</span></p>
<p><span style="font-size:18px;color:#000000;">二、</span><span style="font-size:18px;color:#000000;">使用<span style="font-family:'Times New Roman';">Sqoop</span><span style="font-family:'黑体';">将数据导入</span><span style="font-family:'Times New Roman';">HBase</span></span></p>
<p><span style="font-size:18px;">要求：</span></p>
<p><span style="font-size:18px;">MySQL<span style="font-family:'宋体';">服务启动且运行正常</span></span></p>
<p><span style="font-size:18px;">HBase<span style="font-family:'宋体';">集群启动且运行正常</span></span></p>
<p><span style="font-size:18px;">进入<span style="font-family:'Times New Roman';">sqoop</span><span style="font-family:'宋体';">安装主目录</span></span></p>
<p><span style="font-size:18px;color:#333333;"> [zkpk@master ~]$ cd /home/zkpk/sqoop-1.4.5.bin__hadoop-2.0.4-alpha</span></p>
<p><span style="font-size:18px;">执行导入命令：</span></p>
<p><span style="font-size:18px;color:#333333;"> [zkpk@master sqoop-1.4.5.bin__hadoop-2.0.4-alpha]$ bin/sqoop import --connect jdbc:mysql://</span><span style="font-size:18px;">192.168.190.147</span><span style="font-size:18px;color:#333333;">:3306/test --username hadoop --password hadoop --table uid_cnt --hbase-table uid_cnt --column-family f1 --hbase-row-key uid --hbase-create-table -m 1</span></p>
<p><span style="font-size:18px;">其中红色主机<span style="font-family:'Times New Roman';">ip</span><span style="font-family:'宋体';">需要改为本机的</span><span style="font-family:'Times New Roman';">ip</span><span style="font-family:'宋体';">地址</span></span></p>
<p><span style="font-size:18px;">字段解释：</span></p>
<p><span style="font-size:18px;color:#333333;">bin/sqoop import --connect jdbc:mysql://192.168.190.147:3306/test --username hadoop --password hadoop --table uid_cnt </span></p>
<p><span style="font-size:18px;color:#333333;">--hbase-table uid_cnt  HBase<span style="font-family:'楷体';">中表名称</span></span></p>
<p><span style="font-size:18px;color:#333333;">--column-family f1  <span style="font-family:'楷体';">列簇名称</span></span></p>
<p><span style="font-size:18px;color:#333333;">--hbase-row-key uid  HBase<span style="font-family:'楷体';">行键</span></span></p>
<p><span style="font-size:18px;color:#333333;">--hbase-create-table  <span style="font-family:'楷体';">是否在不存在情况下创建表</span></span></p>
<p><span style="font-size:18px;color:#333333;">-m 1 <span style="font-family:'楷体';">启动</span><span style="font-family:'Courier New';">Map</span><span style="font-family:'楷体';">数量</span></span></p>
            </div>
                </div>