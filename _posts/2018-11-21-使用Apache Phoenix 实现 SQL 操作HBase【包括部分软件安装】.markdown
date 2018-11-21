---
layout:     post
title:      使用Apache Phoenix 实现 SQL 操作HBase【包括部分软件安装】
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="font-size:16px;line-height:27.1875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
Apache Phoenix 相信大家并不陌生，它是HBase的SQL驱动，Phoenix 使得Hbase 支持通过JDBC的方式进行访问，并将你的SQL查询转换成Hbase的扫描和相应的动作。</p>
<p style="font-size:16px;line-height:27.1875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
兼容性：</p>
<ul style="list-style-type:none;font-size:16px;line-height:27.1875px;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);"><li style="line-height:1.7em;list-style-type:disc;">
<p style="line-height:1.7em;">Phoenix 2.x - HBase 0.94.x</p>
</li><li style="line-height:1.7em;list-style-type:disc;">
<p style="line-height:1.7em;">Phoenix 3.x - HBase 0.94.x</p>
</li><li style="line-height:1.7em;list-style-type:disc;">
<p style="line-height:1.7em;">Phoenix 4.x - HBase 0.98.1+</p>
</li></ul><p style="font-size:16px;line-height:27.1875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
Hbase和Phoenix的安装这里就不做过多阐述，都很简单，这里给大家简单介绍一下安装完成后如何通过SQL Client来连接到Phoenix：</p>
<p style="font-size:16px;line-height:27.1875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
第一步： 下载安装SQuirreL SQL Client : <a href="http://www.squirrelsql.org/" rel="nofollow"> http://www.squirrelsql.org/ </a></p>
<p style="font-size:16px;line-height:27.1875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
第二步：  拷贝phoenix-{version}-client.jar 和 phoenix-core-{version}.jar到SQuirreL 安装目录下的lib文件夹下，这两个jar可以在phoenix的安装目录下找到。</p>
<p style="font-size:16px;line-height:27.1875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
第三步：  打开SQuirreL ， 点击Drivers，新建一个driver：</p>
<p style="font-size:16px;line-height:27.1875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
name : 可以自定义一个名称</p>
<p style="font-size:16px;line-height:27.1875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
Example URL : 这个是为了让你新建一个连接的时候作为提示用的</p>
<p style="font-size:16px;line-height:27.1875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
Java Class Path : 这个需要选中之前上传的JAR</p>
<p style="font-size:16px;line-height:27.1875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
Class Name: org.apache.phoenix.jdbc.PhoenixDriver</p>
<p style="font-size:16px;line-height:27.1875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<img src="http://img1.tuicool.com/niEJra.png!web" class="alignCenter" alt="" style="vertical-align:middle;border:0px none;text-align:center;display:block;"></p>
<p style="font-size:16px;line-height:27.1875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
第四步：创建Aliases（就是创建一个hbase的connection）：</p>
<p style="font-size:16px;line-height:27.1875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
Name: 可以自定义</p>
<p style="font-size:16px;line-height:27.1875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
Driver： 选择之前配置好的phoenix driver</p>
<p style="font-size:16px;line-height:27.1875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
URL: 这里初始化显示的是之前配置的Example URL, 可以在这里做具体的修改，这里的‘your-zookeeer-servers’意思是在这里写上你的zookeeper集群中的server names。</p>
<p style="font-size:16px;line-height:27.1875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<img src="http://img0.tuicool.com/RfqimiQ.png!web" class="alignCenter" alt="" style="vertical-align:middle;border:0px none;text-align:center;display:block;"></p>
<p style="font-size:16px;line-height:27.1875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
配置好以后点击Test 测试连接:</p>
<p style="font-size:16px;line-height:27.1875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<img src="http://img1.tuicool.com/7vYbArN.png!web" class="alignCenter" alt="" style="vertical-align:middle;border:0px none;text-align:center;display:block;"></p>
<p style="font-size:16px;line-height:27.1875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
确认连接后的页面：</p>
<p style="font-size:16px;line-height:27.1875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<img src="http://img1.tuicool.com/7rYfiyu.png!web" class="alignCenter" alt="" style="vertical-align:middle;border:0px none;text-align:center;display:block;"></p>
<p style="font-size:16px;line-height:27.1875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
OK，到这里就说明已经连接上HBase了，下面大家就可以试着通过写SQL来操作HBase数据了。</p>
            </div>
                </div>