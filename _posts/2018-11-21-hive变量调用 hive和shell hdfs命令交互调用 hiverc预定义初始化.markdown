---
layout:     post
title:      hive变量调用 hive和shell hdfs命令交互调用 hiverc预定义初始化
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/leprovision/article/details/72627345				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<span style="font-family:Tahoma;font-size:14px;"> hive变量调用 hive和shell hdfs命令交互调用 hiverc预定义初始化</span><br style="font-family:Tahoma;font-size:14px;"><div class="iteye-blog-content-contain" style="font-family:Tahoma;font-size:14px;">
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
<span>1 hive使用外部变量： 分为4种情况，</span></p>
<p>
 </p>
<pre><code class="language-java">hive中引用外部变量：  在代码中引用时使用${...};

方式1：
[root@h2single bin]# hive -d name=username;      随便定义变量 后在hive中使用此变量  
hive&gt; create table t1(${name} string);
OK
Time taken: 3.499 seconds
hive&gt; describe t1;
OK
username                string   





方式2：

hiveconf： 覆盖hive-site hive-default的值:

[root@h2single bin]# pwd
/usr/local/hive/bin
[root@h2single bin]# hive --hiveconf hive.cli.print.current.db=true;  显示库名称

hive (default)&gt;   展示库名称为default

[root@h2single bin]# hive --hiveconf hive.cli.print.header=true;    查表记录时，展示字段名称

hive&gt; select * from t4;
OK
t4.root    展示字段




方式3：system 读取java 变量 从java定义的配置属性，如system:user.name

hive&gt; create table t3(${system:user.name} string)
    &gt; ;
OK
Time taken: 1.278 seconds
hive&gt; describe t3;
OK
root                    string                                      
Time taken: 0.894 seconds, Fetche


方式4：env 读取shell环境变量

hive&gt; create table t4(${env:USER} string);
OK
Time taken: 2.891 seconds
hive&gt; describe t4;
OK
root                    string  
</code></pre>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
<span>2 启动时 指定hive目录：</span><br>
#hive --hiveconf hive.metastore.warehouse.dir=/hive/$USER;</p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
<span>3 linux环境下执行hive语句：</span></p>
<p>
 </p>
<p>
<br>
 </p>
<pre><code class="language-java">如下命令均在linux下执行：
[root@h2single bin]# hive -e "show tables";
 hive -e "show tables"    linux shell下将此语句发送到shell执行 后将结果打印在linux shell下
 hive -e "show tables"&gt;tablename  将结果写到当前linux目录的tablename文件中
 hive -S -e "show tables"&gt;tablename  将和hive交互过程信息 比如耗时等 不在控制台中显示出来





[root@h2single bin]# vi test.hql
show tables



[root@h2single bin]# hive -f test.hql &gt;&gt; a.txt

  将外部文件a.hql内容发送到hive中执行并将返回结果 追加写到当前shell目录下的 a.txt中

[root@h2single bin]# cat a.txt 
t1
t3
t4


hive -f 操作hdfs文件写法：



hive -f hdfs://&lt;namenode&gt;:&lt;port&gt;/hive-script.sql
</code></pre>
<p>
 </p>
<p>
 </p>
<p>
<span>4 hive 环境下执行linux语句：   hive环境下访问hdfs内容：</span><br>
 </p>
<p>
 </p>
<pre><code class="language-java">hive&gt; dfs -ls /
    &gt; ;
Found 4 items
drwxr-xr-x   - root supergroup          0 2015-03-06 02:35 /flume
drwxr-xr-x   - root supergroup          0 2015-03-12 17:31 /hbase
drwx-wx-wx   - root supergroup          0 2015-03-13 19:09 /tmp
drwx------   - root supergroup          0 2015-03-13 19:17 /user
hive&gt; 

hive&gt;dfs -rm -r /root/t5


hive环境下访问shell内容：

hive&gt;!mkdri /usr/local/test;
 hive&gt; ! mkdir 111;
hive&gt; ! ls;
111
a.txt
ext
hive
hive-config.sh
test.hql
hive&gt; 

</code></pre>
<p>
 </p>
<p>
 </p>
<p>
<span> 5  hiverc 文件 做初始化操作：</span></p>
<p>
 </p>
<p>
 </p>
<pre><code class="language-java">[root@h2single bin]# ls -a ~


[root@h2single bin]# vi ~/.hiverc  创建此文件 里面添加如下内容

set  hive.cli.print.current.db=true;
set  hive.cli.print.header=true;



此时在进入hive&gt; 就会看到 显示数据库名称 查询表也会展示表字段名称</code></pre></div>
            </div>
                </div>