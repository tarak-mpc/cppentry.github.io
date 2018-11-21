---
layout:     post
title:      drill 查询hbase和hdfs数据的相关配置以及查询方法
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p align="justify"><span style="font-family:'宋体';">前提是在各个集群节点安装drill。</span></p>
<p align="justify"><span style="font-family:'宋体';">一．使用</span>drill<span style="font-family:'宋体';">查询</span><span style="font-family:Calibri;">hdfs</span><span style="font-family:'宋体';">配置步骤：</span></p>
<p align="justify">1.<span style="font-family:'宋体';">首先在</span><span style="font-family:Calibri;">hdfs</span><span style="font-family:'宋体';">中创建文件夹，用来存放上传到</span><span style="font-family:Calibri;">hdfs</span><span style="font-family:'宋体';">系统的文件</span></p>
<p align="justify"><span style="font-family:'宋体';">进入</span>hadoop<span style="font-family:'宋体';">的</span><span style="font-family:Calibri;">bin</span><span style="font-family:'宋体';">目录：</span><span style="font-family:Calibri;">hdfs dfs -mkdir /usr</span></p>
<p align="justify"><span style="font-family:'宋体';">查看文件夹是否创建成功：</span>hdfs dfs -ls /</p>
<p align="justify"><span style="font-family:'宋体';">修改文件夹的权限：</span>hadoop fs -chmod 777  /usr</p>
<p align="justify"><span style="font-family:'宋体';">然后打开浏览器，输入</span>http://192.168.1.71:8047/storage</p>
<p align="justify"><span style="font-family:'宋体';">点击</span>dfs<span style="font-family:'宋体';">中的</span><span style="font-family:Calibri;">update</span><span style="font-family:'宋体';">，配置相关属性，配置完毕后，进行“</span><span style="font-family:Calibri;">update</span><span style="font-family:'宋体';">”才开始生效，如图1所示：</span></p>
<p align="justify"><span style="font-family:'宋体';"><img src="https://img-blog.csdn.net/20160908131717249" alt=""><br></span></p>
<p align="justify"><span style="font-family:'宋体';">                                    图1</span></p>
<p align="center"></p>
<p align="justify">2.开始查询：</p>
<p align="justify"><span style="font-family:'宋体';">拷贝一个测试</span>.json<span style="font-family:'宋体';">文件到</span><span style="font-family:Calibri;">hdfs:  hdfs dfs -copyFromLocal /home/dm/opt/test.txt  /usr</span></p>
<p align="justify"><span style="font-family:'宋体';">前提是打开</span>drill<span style="font-family:'宋体';">进程：</span><span style="font-family:Calibri;">./drillbit.sh start</span></p>
<p align="justify"><span style="color:rgb(255,0,0);">备注：</span><span style="font-family:'宋体';">（由于</span>drill<span style="font-family:'宋体';">依赖</span><span style="font-family:Calibri;">zookeeper</span><span style="font-family:'宋体';">，故在启动</span><span style="font-family:Calibri;">drill</span><span style="font-family:'宋体';">进程之前，确保</span><span style="font-family:Calibri;">zookeeper</span><span style="font-family:'宋体';">已经启动，否则</span><span style="font-family:Calibri;">drill</span><span style="font-family:'宋体';">启动报错）</span></p>
<p align="justify">进入集群查询模式：</p>
<p align="justify">./sqlline -u jdbc:drill:zk=192.168.1.71:2181,192.168.1.72:2181,192.168.1.73:2181,192.168.1.75:2181,192.168.1.78:2181</p>
<p align="justify"><span style="font-family:'宋体';">使用</span>dfs<span style="font-family:'宋体';">查询：</span><span style="font-family:Calibri;">use dfs.root;(root</span><span style="font-family:'宋体';">是</span><span style="font-family:Calibri;">workspaces</span><span style="font-family:'宋体';">名称</span><span style="font-family:Calibri;">)</span></p>
<p align="justify">查询语句例子：</p>
<p align="justify"><span style="color:rgb(0,64,128);"> </span></p>
<p align="justify"><span style="font-family:'宋体';">切记：表名称一定要用中文输入法下的单引号</span>,<span style="font-family:'宋体';">而查询条件后的值用英文输入法的单引号</span></p>
<p align="justify">官方文档例子：</p>
<p align="justify">use  dfs.json_files;</p>
<p align="justify">select * from `donuts.json` where type=’frosted’ ;</p>
<p align="justify">二．<span style="font-family:'宋体';">使用</span>drill<span style="font-family:'宋体';">查询</span><span style="font-family:Calibri;">hbase</span></p>
<p align="justify">1.<span style="font-family:'宋体';">打开浏览器：然后打开浏览器，输入</span><span style="font-family:Calibri;">http://192.168.1.71:8047/storage</span><span style="font-family:'宋体';">，找到</span><span style="font-family:Calibri;">hbase</span><span style="font-family:'宋体';">，使其成为</span><span style="font-family:Calibri;">enable</span><span style="font-family:'宋体';">状态。</span></p>
<p align="justify"> </p>
<p align="justify">2.<span style="font-family:'宋体';">进入</span>drill<span style="font-family:'宋体';">查询：</span></p>
<p align="justify"><span style="font-family:'宋体';">切换到</span>drill<span style="font-family:'宋体';">的</span><span style="font-family:Calibri;">bin</span><span style="font-family:'宋体';">目录：</span></p>
<p align="justify">./sqlline -u jdbc:drill:zk=192.168.1.71:2181,192.168.1.72:2181,192.168.1.73:2181,192.168.1.75:2181,192.168.1.78:2181</p>
<p align="justify">3.开始查询：</p>
<p align="justify">use hbase;</p>
<p align="justify">Select * from ghdj;</p>
<p align="justify">以上查询会出现乱码，如果要进行数据格式转换，例子：</p>
<p align="justify">SELECT CONVERT_FROM(row_key, 'UTF8') AS studentid, </p>
<p align="justify">        CONVERT_FROM(students.account.name, 'UTF8') AS name, </p>
<p align="justify">        CONVERT_FROM(students.address.state, 'UTF8') AS state,
</p>
<p align="justify">        CONVERT_FROM(students.address.street, 'UTF8') AS street,
</p>
<p align="justify">        CONVERT_FROM(students.address.zipcode, 'UTF8') AS zipcode
</p>
<p align="justify"> FROM students;</p>
            </div>
                </div>