---
layout:     post
title:      HBASE 常用语法和phoenix 配置
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/m0_37845836/article/details/77733673				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1>hadoop服务器监控网址</h1>
<p>    </p>
<h1><a name="_Toc484426947">hadoop</a>常用命令</h1>
<p><span style="color:rgb(51,51,51);">使用账号从</span><span style="color:rgb(51,51,51);">ssh</span><span style="color:rgb(51,51,51);">登录，进入</span><span style="color:rgb(51,51,51);">HADOOP_HOME</span><span style="color:rgb(51,51,51);">目录。</span></p>
<p><span style="color:rgb(51,51,51);">hadoop</span><span style="color:rgb(51,51,51);">安装目录：</span><span style="color:rgb(51,51,51);">/usr/lib/hadoop/</span></p>
<h2><a name="_Toc484426948"><span style="color:rgb(51,51,51);">启动</span></a><span style="color:rgb(51,51,51);">Hadoop</span><span style="color:rgb(51,51,51);">     
</span></h2>
<p align="left"><span style="color:rgb(51,51,51);">         </span><span style="color:rgb(51,51,51);">执行</span><span style="color:rgb(51,51,51);">sh bin/start-all.sh</span></p>
<p align="left"><span style="color:rgb(51,51,51);">         </span><span style="color:rgb(51,51,51);">或</span></p>
<p align="left"><span style="color:rgb(51,51,51);">        </span></p>
<h2><a name="_Toc484426949"><span style="color:rgb(51,51,51);">关闭</span></a><span style="color:rgb(51,51,51);">Hadoop</span></h2>
<p align="left">·        <span style="color:rgb(51,51,51);">执行sh bin/stop-all.sh</span></p>
<p align="left"><span style="color:rgb(51,51,51);"> </span></p>
<p><strong><span style="color:rgb(51,51,51);">1</span><span style="color:rgb(51,51,51);">、查看指定目录下内容</span></strong></p>
<p><span style="color:rgb(51,51,51);">hadoop dfs –ls [</span><span style="color:rgb(51,51,51);">文件目录</span><span style="color:rgb(51,51,51);">]</span></p>
<p><span style="color:rgb(51,51,51);">eg: hadoop dfs –ls /user/wangkai.pt</span></p>
<p><strong><span style="color:rgb(51,51,51);">2</span><span style="color:rgb(51,51,51);">、打开某个已存在文件</span></strong></p>
<p><span style="color:rgb(51,51,51);">hadoop dfs –cat [file_path]</span></p>
<p><span style="color:rgb(51,51,51);">eg:hadoop dfs -cat /user/wangkai.pt/data.txt</span></p>
<p><strong><span style="color:rgb(51,51,51);">3</span><span style="color:rgb(51,51,51);">、将本地文件存储至</span><span style="color:rgb(51,51,51);">hadoop</span></strong></p>
<p><span style="color:rgb(51,51,51);">hadoop fs –put [</span><span style="color:rgb(51,51,51);">本地地址</span><span style="color:rgb(51,51,51);">] [hadoop</span><span style="color:rgb(51,51,51);">目录</span><span style="color:rgb(51,51,51);">]</span></p>
<p><span style="color:rgb(51,51,51);">hadoop fs –put /home/t/file.txt /user/t   </span></p>
<p><span style="color:rgb(51,51,51);">(file.txt</span><span style="color:rgb(51,51,51);">是文件名</span><span style="color:rgb(51,51,51);">)</span></p>
<p><strong><span style="color:rgb(51,51,51);">4</span><span style="color:rgb(51,51,51);">、将本地文件夹存储至</span><span style="color:rgb(51,51,51);">hadoop</span></strong></p>
<p><span style="color:rgb(51,51,51);">hadoop fs –put [</span><span style="color:rgb(51,51,51);">本地目录</span><span style="color:rgb(51,51,51);">] [hadoop</span><span style="color:rgb(51,51,51);">目录</span><span style="color:rgb(51,51,51);">]</span><span style="color:rgb(51,51,51);"> <br></span><span style="color:rgb(51,51,51);">hadoop fs –put /home/t/dir_name /user/t</span></p>
<p><span style="color:rgb(51,51,51);">(dir_name</span><span style="color:rgb(51,51,51);">是文件夹名</span><span style="color:rgb(51,51,51);">)</span></p>
<p><strong><span style="color:rgb(51,51,51);">5</span><span style="color:rgb(51,51,51);">、将</span><span style="color:rgb(51,51,51);">hadoop</span><span style="color:rgb(51,51,51);">上某个文件</span><span style="color:rgb(51,51,51);">down</span><span style="color:rgb(51,51,51);">至本地已有目录下</span></strong></p>
<p><span style="color:rgb(51,51,51);">hadoop fs -get [</span><span style="color:rgb(51,51,51);">文件目录</span><span style="color:rgb(51,51,51);">] [</span><span style="color:rgb(51,51,51);">本地目录</span><span style="color:rgb(51,51,51);">]</span></p>
<p><span style="color:rgb(51,51,51);">hadoop fs –get /user/t/ok.txt /home/t</span></p>
<p><strong><span style="color:rgb(51,51,51);">6</span><span style="color:rgb(51,51,51);">、删除</span><span style="color:rgb(51,51,51);">hadoop</span><span style="color:rgb(51,51,51);">上指定文件</span></strong></p>
<p><span style="color:rgb(51,51,51);">hadoop fs –rm [</span><span style="color:rgb(51,51,51);">文件地址</span><span style="color:rgb(51,51,51);">]</span></p>
<p><span style="color:rgb(51,51,51);">hadoop fs –rm /user/t/ok.txt</span></p>
<p><strong><span style="color:rgb(51,51,51);">7</span><span style="color:rgb(51,51,51);">、删除</span><span style="color:rgb(51,51,51);">hadoop</span><span style="color:rgb(51,51,51);">上指定文件夹（包含子目录等）</span></strong></p>
<p><span style="color:rgb(51,51,51);">hadoop fs –rm [</span><span style="color:rgb(51,51,51);">目录地址</span><span style="color:rgb(51,51,51);">]</span></p>
<p><span style="color:rgb(51,51,51);">hadoop fs –rmr /user/t</span></p>
<p><strong><span style="color:rgb(51,51,51);">8</span><span style="color:rgb(51,51,51);">、在</span><span style="color:rgb(51,51,51);">hadoop</span><span style="color:rgb(51,51,51);">指定目录内创建新目录</span></strong></p>
<p><span style="color:rgb(51,51,51);">hadoop fs –mkdir /user/t</span></p>
<p><strong><span style="color:rgb(51,51,51);">9</span><span style="color:rgb(51,51,51);">、在</span><span style="color:rgb(51,51,51);">hadoop</span><span style="color:rgb(51,51,51);">指定目录下新建一个空文件</span></strong></p>
<p><span style="color:rgb(51,51,51);">使用</span><span style="color:rgb(51,51,51);">touchz</span><span style="color:rgb(51,51,51);">命令：</span></p>
<p><span style="color:rgb(51,51,51);">hadoop  fs  -touchz /user/new.txt</span></p>
<p><strong><span style="color:rgb(51,51,51);">10</span><span style="color:rgb(51,51,51);">、将</span><span style="color:rgb(51,51,51);">hadoop</span><span style="color:rgb(51,51,51);">上某个文件重命名</span></strong></p>
<p><span style="color:rgb(51,51,51);">使用</span><span style="color:rgb(51,51,51);">mv</span><span style="color:rgb(51,51,51);">命令：</span></p>
<p><span style="color:rgb(51,51,51);">hadoop  fs  –mv /user/test.txt  /user/ok.txt  
</span><span style="color:rgb(51,51,51);">（将</span><span style="color:rgb(51,51,51);">test.txt</span><span style="color:rgb(51,51,51);">重命名为</span><span style="color:rgb(51,51,51);">ok.txt</span><span style="color:rgb(51,51,51);">）</span></p>
<p><strong><span style="color:rgb(51,51,51);">11</span><span style="color:rgb(51,51,51);">、将</span><span style="color:rgb(51,51,51);">hadoop</span><span style="color:rgb(51,51,51);">指定目录下所有内容保存为一个文件，同时</span><span style="color:rgb(51,51,51);">down</span><span style="color:rgb(51,51,51);">至本地</span></strong></p>
<p><span style="color:rgb(51,51,51);">hadoop dfs –getmerge /user /home/t</span></p>
<p><strong><span style="color:rgb(51,51,51);">12</span><span style="color:rgb(51,51,51);">、将正在运行的</span><span style="color:rgb(51,51,51);">hadoop</span><span style="color:rgb(51,51,51);">作业</span><span style="color:rgb(51,51,51);">kill</span><span style="color:rgb(51,51,51);">掉</span></strong></p>
<p><span style="color:rgb(51,51,51);">hadoop job –kill  [job-id]</span></p>
<h1><a name="_Toc484426950">Zookeeper</a>常用命令</h1>
<h2><a name="_Toc484426951"><span style="color:rgb(51,51,51);">ZooKeeper</span></a><span style="color:rgb(51,51,51);">服务命令</span><span style="color:rgb(51,51,51);">:</span></h2>
<p><span style="color:rgb(51,51,51);">     </span><span style="color:rgb(51,51,51);">在准备好相应的配置之后，可以直接通过</span><span style="color:rgb(51,51,51);">zkServer.sh
</span><span style="color:rgb(51,51,51);">这个脚本进行服务的相关操作</span></p>
<ul type="disc"><li><span style="color:rgb(51,51,51);">1. </span><span style="color:rgb(51,51,51);">启动</span><span style="color:rgb(51,51,51);">ZK</span><span style="color:rgb(51,51,51);">服务</span><span style="color:rgb(51,51,51);">:       sh bin/zkServer.sh start</span>
</li><li><span style="color:rgb(51,51,51);">2. </span><span style="color:rgb(51,51,51);">查看</span><span style="color:rgb(51,51,51);">ZK</span><span style="color:rgb(51,51,51);">服务状态</span><span style="color:rgb(51,51,51);">: sh bin/zkServer.sh status</span>
</li><li><span style="color:rgb(51,51,51);">3. </span><span style="color:rgb(51,51,51);">停止</span><span style="color:rgb(51,51,51);">ZK</span><span style="color:rgb(51,51,51);">服务</span><span style="color:rgb(51,51,51);">:       sh bin/zkServer.sh stop</span>
</li><li><span style="color:rgb(51,51,51);">4. </span><span style="color:rgb(51,51,51);">重启</span><span style="color:rgb(51,51,51);">ZK</span><span style="color:rgb(51,51,51);">服务</span><span style="color:rgb(51,51,51);">:       sh bin/zkServer.sh restart</span></li></ul><p><span style="color:rgb(51,51,51);">ZooKeeper</span><span style="color:rgb(51,51,51);">进行访问，数据创建，数据修改等操作</span><span style="color:rgb(51,51,51);">. </span><span style="color:rgb(51,51,51);">使用</span><span style="color:rgb(51,51,51);"> zkCli.sh-server 127.0.0.1:2181
</span><span style="color:rgb(51,51,51);">连接到</span><span style="color:rgb(51,51,51);"> ZooKeeper
</span><span style="color:rgb(51,51,51);">服务，连接成功后，系统会输出</span><span style="color:rgb(51,51,51);"> ZooKeeper
</span><span style="color:rgb(51,51,51);">的相关环境以及配置信息。</span></p>
<p></p>
<p><strong><span style="color:rgb(51,51,51);">命令行工具的一些简单操作如下：</span></strong></p>
<ul type="disc"><li><span style="color:rgb(51,51,51);">1. </span><span style="color:rgb(51,51,51);">显示根目录下、文件：</span><span style="color:rgb(51,51,51);"> ls /
</span><span style="color:rgb(51,51,51);">使用</span><span style="color:rgb(51,51,51);"> ls
</span><span style="color:rgb(51,51,51);">命令来查看当前</span><span style="color:rgb(51,51,51);"> ZooKeeper
</span><span style="color:rgb(51,51,51);">中所包含的内容</span> </li><li><span style="color:rgb(51,51,51);">2. </span><span style="color:rgb(51,51,51);">显示根目录下、文件：</span><span style="color:rgb(51,51,51);"> ls2 /
</span><span style="color:rgb(51,51,51);">查看当前节点数据并能看到更新次数等数据</span> </li><li><span style="color:rgb(51,51,51);">3. </span><span style="color:rgb(51,51,51);">创建文件，并设置初始内容：</span><span style="color:rgb(51,51,51);"> create /zk "test"
</span><span style="color:rgb(51,51,51);">创建一个新的</span><span style="color:rgb(51,51,51);"> znode</span><span style="color:rgb(51,51,51);">节点</span><span style="color:rgb(51,51,51);">“ zk ”</span><span style="color:rgb(51,51,51);">以及与它关联的字符串</span>
</li><li><span style="color:rgb(51,51,51);">4. </span><span style="color:rgb(51,51,51);">获取文件内容：</span><span style="color:rgb(51,51,51);"> get /zk
</span><span style="color:rgb(51,51,51);">确认</span><span style="color:rgb(51,51,51);"> znode
</span><span style="color:rgb(51,51,51);">是否包含我们所创建的字符串</span> </li><li><span style="color:rgb(51,51,51);">5. </span><span style="color:rgb(51,51,51);">修改文件内容：</span><span style="color:rgb(51,51,51);"> set /zk "zkbak"
</span><span style="color:rgb(51,51,51);">对</span><span style="color:rgb(51,51,51);"> zk
</span><span style="color:rgb(51,51,51);">所关联的字符串进行设置</span> </li><li><span style="color:rgb(51,51,51);">6. </span><span style="color:rgb(51,51,51);">删除文件：</span><span style="color:rgb(51,51,51);"> delete /zk
</span><span style="color:rgb(51,51,51);">将刚才创建的</span><span style="color:rgb(51,51,51);"> znode
</span><span style="color:rgb(51,51,51);">删除</span> </li><li><span style="color:rgb(51,51,51);">7. </span><span style="color:rgb(51,51,51);">退出客户端：</span><span style="color:rgb(51,51,51);"> quit</span>
</li><li><span style="color:rgb(51,51,51);">8. </span><span style="color:rgb(51,51,51);">帮助命令：</span><span style="color:rgb(51,51,51);"> help</span></li></ul><h2><a name="_Toc484426952"><span style="color:rgb(51,51,51);">ZooKeeper
</span></a><span style="color:rgb(51,51,51);">常用四字命令</span></h2>
<p><span style="color:rgb(51,51,51);">      ZooKeeper </span><span style="color:rgb(51,51,51);">支持某些特定的四字命令字母与其的交互。它们大多是查询命令，用来获取</span><span style="color:rgb(51,51,51);"> ZooKeeper
</span><span style="color:rgb(51,51,51);">服务的当前状态及相关信息。用户在客户端可以通过</span><span style="color:rgb(51,51,51);"> telnet
</span><span style="color:rgb(51,51,51);">或</span><span style="color:rgb(51,51,51);"> nc
</span><span style="color:rgb(51,51,51);">向</span><span style="color:rgb(51,51,51);"> ZooKeeper
</span><span style="color:rgb(51,51,51);">提交相应的命令</span></p>
<ul type="disc"><li><span style="color:rgb(51,51,51);">1. </span><span style="color:rgb(51,51,51);">可以通过命令：</span><span style="color:rgb(51,51,51);">echo stat|nc 127.0.0.1 2181
</span><span style="color:rgb(51,51,51);">来查看哪个节点被选择作为</span><span style="color:rgb(51,51,51);">follower</span><span style="color:rgb(51,51,51);">或者</span><span style="color:rgb(51,51,51);">leader</span>
</li><li><span style="color:rgb(51,51,51);">2. </span><span style="color:rgb(51,51,51);">使用</span><span style="color:rgb(51,51,51);">echo ruok|nc 127.0.0.1 2181
</span><span style="color:rgb(51,51,51);">测试是否启动了该</span><span style="color:rgb(51,51,51);">Server</span><span style="color:rgb(51,51,51);">，若回复</span><span style="color:rgb(51,51,51);">imok</span><span style="color:rgb(51,51,51);">表示已经启动。</span>
</li><li><span style="color:rgb(51,51,51);">3. echo dump| nc 127.0.0.1 2181 ,</span><span style="color:rgb(51,51,51);">列出未经处理的会话和临时节点。</span>
</li><li><span style="color:rgb(51,51,51);">4. echo kill | nc 127.0.0.1 2181 ,</span><span style="color:rgb(51,51,51);">关掉</span><span style="color:rgb(51,51,51);">server</span>
</li><li><span style="color:rgb(51,51,51);">5. echo conf | nc 127.0.0.1 2181 ,</span><span style="color:rgb(51,51,51);">输出相关服务配置的详细信息。</span>
</li><li><span style="color:rgb(51,51,51);">6. echo cons | nc 127.0.0.1 2181 ,</span><span style="color:rgb(51,51,51);">列出所有连接到服务器的客户端的完全的连接</span><span style="color:rgb(51,51,51);"> /
</span><span style="color:rgb(51,51,51);">会话的详细信息。</span> </li><li><span style="color:rgb(51,51,51);">7. echo envi |nc 127.0.0.1 2181 ,</span><span style="color:rgb(51,51,51);">输出关于服务环境的详细信息（区别于</span><span style="color:rgb(51,51,51);"> conf
</span><span style="color:rgb(51,51,51);">命令）。</span> </li><li><span style="color:rgb(51,51,51);">8. echo reqs | nc 127.0.0.1 2181 ,</span><span style="color:rgb(51,51,51);">列出未经处理的请求。</span>
</li><li><span style="color:rgb(51,51,51);">9. echo wchs | nc 127.0.0.1 2181 ,</span><span style="color:rgb(51,51,51);">列出服务器</span><span style="color:rgb(51,51,51);"> watch
</span><span style="color:rgb(51,51,51);">的详细信息。</span> </li><li><span style="color:rgb(51,51,51);">10. echo wchc | nc 127.0.0.1 2181 ,</span><span style="color:rgb(51,51,51);">通过</span><span style="color:rgb(51,51,51);"> session
</span><span style="color:rgb(51,51,51);">列出服务器</span><span style="color:rgb(51,51,51);"> watch
</span><span style="color:rgb(51,51,51);">的详细信息，它的输出是一个与</span><span style="color:rgb(51,51,51);"> watch
</span><span style="color:rgb(51,51,51);">相关的会话的列表。</span> </li><li><span style="color:rgb(51,51,51);">11. echo wchp | nc 127.0.0.1 2181 ,</span><span style="color:rgb(51,51,51);">通过路径列出服务器</span><span style="color:rgb(51,51,51);"> watch
</span><span style="color:rgb(51,51,51);">的详细信息。它输出一个与</span><span style="color:rgb(51,51,51);"> session
</span><span style="color:rgb(51,51,51);">相关的路径。</span></li></ul><p align="left"> </p>
<p> </p>
<p> </p>
<h1><a name="_Toc484426953">HBASEshell </a>常用命令</h1>
<p> (1)建立一个表scores，有两个列族grad和courese  </p>
<pre style="background:#FFFFFF;">hbase(main):001:0&gt; create ‘scores','grade', ‘course</pre>
<p>   可以使用list命令来查看当前HBase里有哪些表。使用describe命令来查看表结构。（记得所有的表明、列名都需要加上引号） </p>
<p>(2)按设计的表结构插入值</p>
<pre style="background:#FFFFFF;">put ‘scores','Tom','grade:','5′</pre>
<pre style="background:#FFFFFF;">put ‘scores','Tom','course:math','97′</pre>
<pre style="background:#FFFFFF;">put ‘scores','Tom','course:art','87′</pre>
<pre style="background:#FFFFFF;">put ‘scores','Jim','grade','4′</pre>
<pre style="background:#FFFFFF;">put ‘scores','Jim','course:','89′</pre>
<pre style="background:#FFFFFF;">put ‘scores','Jim','course:','80′ </pre>
<p align="left">  这样表结构就起来了，其实比较自由，列族里边可以自由添加子列很方便。如果列族下没有子列，加不加冒号都是可以的。 <br>
put命令比较简单，只有这一种用法： <br>
hbase&gt; put ‘t1′, ‘r1′, ‘c1′, ‘value', ts1 <br>
t1指表，r1指行键名，c1指列名，value指单元格值。ts1指时间戳，一般都省略掉了。</p>
<p>(3)根据键值查询数据</p>
<pre style="background:#FFFFFF;">get ‘scores','Jim'</pre>
<pre style="background:#FFFFFF;">get ‘scores','Jim','grade' </pre>
<p align="left">可能你就发现规律了，HBase的shell操作，一个大概顺序就是操作关键词后跟表名，行名，列名这样的一个顺序，如果有其他条件再用花括号加上。 <br>
get有用法如下： </p>
<pre style="background:#FFFFFF;">hbase&gt; get ‘t1′, ‘r1′</pre>
<pre style="background:#FFFFFF;">hbase&gt; get ‘t1′, ‘r1′, {TIMERANGE =&gt; [ts1, ts2]}</pre>
<pre style="background:#FFFFFF;">hbase&gt; get ‘t1′, ‘r1′, {COLUMN =&gt; ‘c1′}</pre>
<pre style="background:#FFFFFF;">hbase&gt; get ‘t1′, ‘r1′, {COLUMN =&gt; ['c1', 'c2', 'c3']}</pre>
<pre style="background:#FFFFFF;">hbase&gt; get ‘t1′, ‘r1′, {COLUMN =&gt; ‘c1′, TIMESTAMP =&gt; ts1}</pre>
<pre style="background:#FFFFFF;">hbase&gt; get ‘t1′, ‘r1′, {COLUMN =&gt; ‘c1′, TIMERANGE =&gt; [ts1, ts2], VERSIONS =&gt; 4}</pre>
<pre style="background:#FFFFFF;">hbase&gt; get ‘t1′, ‘r1′, {COLUMN =&gt; ‘c1′, TIMESTAMP =&gt; ts1, VERSIONS =&gt; 4}</pre>
<pre style="background:#FFFFFF;">hbase&gt; get ‘t1′, ‘r1′, ‘c1′</pre>
<pre style="background:#FFFFFF;">hbase&gt; get ‘t1′, ‘r1′, ‘c1′, ‘c2′</pre>
<pre style="background:#FFFFFF;">hbase&gt; get ‘t1′, ‘r1′, ['c1', 'c2']</pre>
<p align="left">(4)扫描所有数据 <br>
     指定一些修饰词：TIMERANGE, FILTER, LIMIT,STARTROW, STOPROW, TIMESTAMP, MAXLENGTH,or COLUMNS。没任何修饰词，就是上边例句，就会显示所有数据行。 <br>
   代码如下: </p>
<pre style="background:#FFFFFF;">hbase&gt; scan ‘.META.'</pre>
<pre style="background:#FFFFFF;">hbase&gt; scan ‘.META.', {COLUMNS =&gt; ‘info:regioninfo'}</pre>
<pre style="background:#FFFFFF;">hbase&gt; scan ‘t1′, {COLUMNS =&gt; ['c1', 'c2'], LIMIT =&gt; 10, STARTROW =&gt; ‘xyz'}</pre>
<pre style="background:#FFFFFF;">hbase&gt; scan ‘t1′, {COLUMNS =&gt; ‘c1′, TIMERANGE =&gt; [1303668804, 1303668904]}</pre>
<pre style="background:#FFFFFF;">hbase&gt; scan ‘t1′, {FILTER =&gt; “(PrefixFilter (‘row2′) AND (QualifierFilter (&gt;=, ‘binary:xyz'))) AND (TimestampsFilter ( 123, 456))”}</pre>
<pre style="background:#FFFFFF;">hbase&gt; scan ‘t1′, {FILTER =&gt; org.apache.hadoop.hbase.filter.ColumnPaginationFilter.new(1, 0)}</pre>
<p align="left">(5)删除指定数据 </p>
<p align="left"><u>  </u>代码如下: </p>
<pre><span style="background:#FFFFFF;">delete </span><span style="background:#FFFFFF;">‘scores','Jim','grade'</span></pre>
<pre><span style="background:#FFFFFF;">delete </span><span style="background:#FFFFFF;">‘scores','Jim' </span></pre>
<p align="left">删除数据命令也没太多变化，只有一个： </p>
<p align="left">hbase&gt; delete ‘t1′, ‘r1′,‘c1′, ts1 <br>
另外有一个deleteall命令，可以进行整行的范围的删除操作，慎用！ <br>
如果需要进行全表删除操作，就使用truncate命令，其实没有直接的全表删除命令，这个命令也是disable，drop，create三个命令组合出来的。 <br>
(6)修改表结构 </p>
<p>  代码如下: </p>
<pre><span style="background:#FFFFFF;">disable </span><span style="background:#FFFFFF;">‘scores'</span></pre>
<pre><span style="background:#FFFFFF;">alter </span><span style="background:#FFFFFF;">‘scores',NAME=&gt;'info'</span></pre>
<pre><span style="background:#FFFFFF;">enable </span><span style="background:#FFFFFF;">‘scores' </span></pre>
<p align="left">alter命令使用如下（如果无法成功的版本，需要先通用表disable）： </p>
<p align="left">a、改变或添加一个列族： </p>
<pre style="background:#FFFFFF;">hbase&gt; alter ‘t1′, NAME =&gt; ‘f1′, VERSIONS =&gt; 5 </pre>
<p align="left">b、删除一个列族： </p>
<p align="left"><u>  </u>代码如下: </p>
<pre><span style="background:#FFFFFF;">hbase&gt; alter </span><span style="background:#FFFFFF;">‘t1</span>′, NAME =&gt; ‘f1′, METHOD =&gt; ‘delete'</pre>
<pre><span style="background:#FFFFFF;">hbase&gt; alter </span><span style="background:#FFFFFF;">‘t1</span>′, ‘delete' =&gt; ‘f1′ </pre>
<p align="left"> c、也可以修改表属性如MAX_FILESIZE </p>
<pre style="background:#FFFFFF;">MEMSTORE_FLUSHSIZE, READONLY,和 DEFERRED_LOG_FLUSH：</pre>
<pre style="background:#FFFFFF;">hbase&gt; alter ‘t1′, METHOD =&gt; ‘table_att', MAX_FILESIZE =&gt; '134217728′ </pre>
<p align="left">d、可以添加一个表协同处理器 </p>
<pre style="background:#FFFFFF;">hbase&gt; alter ‘t1′, METHOD =&gt; ‘table_att', ‘coprocessor'=&gt; ‘hdfs:///foo.jar|com.foo.FooRegionObserver|1001|arg1=1,arg2=2′ </pre>
<p align="left">一个表上可以配置多个协同处理器，一个序列会自动增长进行标识。加载协同处理器（可以说是过滤程序）需要符合以下规则： <br>
[coprocessor jar file location] | class name | [priority] | [arguments] <br>
e、移除coprocessor如下： <br>
hbase&gt; alter ‘t1′, METHOD =&gt; ‘table_att_unset', NAME =&gt;‘MAX_FILESIZE' <br>
hbase&gt; alter ‘t1′, METHOD =&gt; ‘table_att_unset', NAME =&gt;‘coprocessor$1′ <br>
f、可以一次执行多个alter命令： </p>
<pre style="background:#FFFFFF;">hbase&gt; alter ‘t1′, {NAME =&gt; ‘f1′}, {NAME =&gt; ‘f2′, METHOD =&gt; ‘delete'} </pre>
<p align="left">(7)统计行数： </p>
<p align="left"><u>  </u>代码如下: </p>
<pre><span style="background:#FFFFFF;">hbase&gt; count </span><span style="background:#FFFFFF;">‘t1</span>′</pre>
<pre><span style="background:#FFFFFF;">hbase&gt; count </span><span style="background:#FFFFFF;">‘t1</span>′, INTERVAL =&gt; 100000</pre>
<pre><span style="background:#FFFFFF;">hbase&gt; count </span><span style="background:#FFFFFF;">‘t1</span>′, CACHE =&gt; 1000</pre>
<pre><span style="background:#FFFFFF;">hbase&gt; count </span><span style="background:#FFFFFF;">‘t1</span>′, INTERVAL =&gt; 10, CACHE =&gt; 1000 </pre>
<p align="left">  count一般会比较耗时，使用mapreduce进行统计，统计结果会缓存，默认是10行。统计间隔默认的是1000行（INTERVAL）。 </p>
<p align="left">(8)disable 和 enable 操作 </p>
<p>  很多操作需要先暂停表的可用性，比如上边说的alter操作，删除表也需要这个操作。disable_all和enable_all能够操作更多的表。 <br><br>
(9)表的删除 <br>
  先停止表的可使用性，然后执行删除命令。 <br>
  drop ‘t1′ <br>
以上是一些常用命令详解，具体的所有hbase的shell命令如下，分了几个命令群，看英文是可以看出大概用处的，详细的用法使用help “cmd” 进行了解。 <br><strong>4. hbase shell脚本</strong> </p>
<p>既然是shell命令，当然也可以把所有的hbase shell命令写入到一个文件内，想linux shell脚本程序那样去顺序的执行所有命令。如同写linux shell，把所有hbase shell命令书写在一个文件内，然后执行如下命令即可： </p>
<p align="left"><u> </u>代码如下:</p>
<p align="left"><br>
$ hbase shell test.hbaseshell </p>
<p align="left">方便好用。 </p>
<p align="left"> </p>
<h1><a name="_Toc484426954">PHOENIX</a> 配置</h1>
<p align="left"> </p>
<p> </p>
<p align="left">配置PHOENIX步骤（本次hbase版本1.1.2）：1、下载hbase对应的安装包 2、tar -zxvf -C /usr/local/phoenix/ apache-phoenix-4.8.2-HBase-1.1-bin.tar.gz  3、配置环境变量vi /etc/profile 添加export PHOENIX_HOME=/usr/local/phoenix/apache-phoenix-4.8.2-HBase-1.1-bin export PHOENIX_CLASSPATH=$PHOENIX_HOMEb export PATH=$PATH:$PHOENIX_HOME/bin 4、拷贝jar包到hbase  cp phoenix-4.8.2-HBase-1.1-server.jar  /usr/local/hbase/hbase-1.1.10b 5、启动sqlline.py master,slave1,slave2:2181  sqlline.py + 主机名 + zookeeper端口号 2181</p>
<p align="left"> </p>
            </div>
                </div>