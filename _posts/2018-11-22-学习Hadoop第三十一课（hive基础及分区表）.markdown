---
layout:     post
title:      学习Hadoop第三十一课（hive基础及分区表）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u012453843/article/details/53053111				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>        上节课我们一起学习了Hive的简单入门，这节课我们一起来学习Hive基础及分区表。</p>
<p><strong><span style="color:#3333ff;">第一部分：hive基础</span></strong></p>
<p>         首先我们在itcast03上启动hive并创建一张表，如下所示。</p>
<p><img src="https://img-blog.csdn.net/20161106100830179?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>       接下来我们到hive数据库去查看一下我们创建的表的信息存储情况，我使用的mysql连接工具是SQLyog，我们连接Mysql，连上后可以看到有一个hive数据库，这是hive帮我们自动生成的，我们创建的表名存在于TBLS当中，如下图所示，可以看到有两张表，分别是people表和student表。第一列TBL_ID是表的ID。</p>
<p><img src="https://img-blog.csdn.net/20161106111856694?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>        我们再来看看表的字段存储的位置，字段存储在COLUMNS_V2中，如下所示，可以看到字段有id、name，那么怎么判断字段属于哪张表呢？其实是通过索引来判断的，我们看到第一列是CD_ID，这列的值是指对应表的ID，我们在TBLS中看到两张表的ID分别是1和6，现在我们看到的字段所对应的索引也正好是1和6，从而明确了表与字段之间的关系。</p>
<p><img src="https://img-blog.csdn.net/20161106112848544?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>       我们再来看看数据存储的位置在哪里可以看到，我们点击"SDS"，然后就会看到如下图所示的界面，我们可以看到SDS表的LOCATION列存储着我们所建的表在HDFS上对应的位置。</p>
<p><img src="https://img-blog.csdn.net/20161106113304815?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>        接下来我们向student表中插入一些数据，插入数据的方式是先建一个student.txt文件，在文件里面输入我们要插入的数据，如下所示，<strong>我们共插入四行内容</strong>，前三行我们输入的有内容，第四行我们什么也不输入。</p>
<p></p>
<div>[root@itcast03 ~]# <strong><span style="color:#3333ff;">vim student.txt</span></strong><br>
1       张三<br>
2       李四<br>
3       王五</div>
<div><br></div>
<div>~</div>
<div>        接下来在itcast03上，我们把student.txt上传到student表当中，注意要在hive视图下上传。</div>
<div>hive&gt; <strong><span style="color:#3333ff;">load data local inpath '/root/student.txt' into table student;</span></strong><br>
Copying data from file:/root/student.txt<br>
Copying file: file:/root/student.txt<br>
Loading data to table default.student<br>
Table default.student stats: [numFiles=1, numRows=0, totalSize=28, rawDataSize=0]<br>
OK<br>
Time taken: 1.824 seconds<br>
hive&gt;</div>
<p></p>
<p>        上传完之后，我们到HDFS查看我们上传的文件，如下图所示，可以看到确实有我们上传的student.txt文件了。</p>
<p><img src="https://img-blog.csdn.net/20161106124235621?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>         接下来我们在itcast03的hive视图下进行一些查询操作。</p>
<p>        <strong>首先我们来查询student表所有的数据，可以看到我们查询出了4行数据，前三行有值，第四行是NULL，这是因为我们没给第四行写任何内容。从执行过程来看，Hive并没有启用MapReduce，这是因为查询所有数据不涉及计算，只需查询出来就行了，因此不需要启动MapReduce。</strong></p>
<p>hive&gt; <strong><span style="color:#3333ff;">select * from student;</span></strong><br>
OK<br>
1       张三<br>
2       李四<br>
3       王五<br>
NULL    NULL<br>
Time taken: 1.071 seconds, Fetched: 4 row(s)</p>
<p>        <strong>接着我们来查询前两条记录，由于查询前两条记录也不涉及到计算，只是把前两条记录查询出来就可以了。因此也不用启动MapReduce。</strong><br>
hive&gt; <strong><span style="color:#3333ff;">select * from student limit 2;</span></strong><br>
OK<br>
1       张三<br>
2       李四<br>
Time taken: 0.075 seconds, Fetched: 2 row(s)</p>
<p>        <strong>接着我们来查询所有id的和，明显，这条SQL语句涉及到了计算，因此Hive会自动帮我们启动MapReduce进行计算。执行的结果是6，可以看到它自动忽略了NULL。</strong></p>
<p>hive&gt; <strong><span style="color:#3333ff;">select sum(id) from student;</span></strong><br>
Total jobs = 1<br>
Launching Job 1 out of 1<br>
Number of reduce tasks determined at compile time: 1<br>
In order to change the average load for a reducer (in bytes):<br>
  set hive.exec.reducers.bytes.per.reducer=&lt;number&gt;<br>
In order to limit the maximum number of reducers:<br>
  set hive.exec.reducers.max=&lt;number&gt;<br>
In order to set a constant number of reducers:<br>
  set mapreduce.job.reduces=&lt;number&gt;<br>
Starting Job = job_1478396540774_0003, Tracking URL = http://itcast03:8088/proxy/application_1478396540774_0003/<br>
Kill Command = /itcast/hadoop-2.2.0/bin/hadoop job  -kill job_1478396540774_0003<br>
Hadoop job information for Stage-1: number of mappers: 1; number of reducers: 1<br>
2016-11-06 13:00:23,663 Stage-1 map = 0%,  reduce = 0%<br>
2016-11-06 13:00:30,983 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.95 sec<br>
2016-11-06 13:00:37,254 Stage-1 map = 100%,  reduce = 100%, Cumulative CPU 3.02 sec<br>
MapReduce Total cumulative CPU time: 3 seconds 20 msec<br>
Ended Job = job_1478396540774_0003<br>
MapReduce Jobs Launched: <br>
Job 0: Map: 1  Reduce: 1   Cumulative CPU: 3.02 sec   HDFS Read: 232 HDFS Write: 2 SUCCESS<br>
Total MapReduce CPU Time Spent: 3 seconds 20 msec<br>
OK<br><strong>6</strong><br>
Time taken: 20.897 seconds, Fetched: 1 row(s)<br>
hive&gt;<br></p>
<p>       上面我们进行了一些简单的操作，按照上面的操作，我们新建的表的类型是"MANAGED_TABLE"，即管理Table。如下图所示。</p>
<p><img src="https://img-blog.csdn.net/20161106130916849?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>       下面我们来点高级一点的，我们前面都是先建表然后向表中存数据，现在我们先向某个目录下存数据，然后再创建表指向我们存储的数据。</p>
<p>        值得一说的是，hive的作者在设计的时候考虑到了大家频繁更换hive视图和普通视图的不方便之处，在hive中加入了操作HDFS的命令，只是语法稍有不同。我们先在HDFS的根目录下创建一个data目录。如下所示，大家看到了吧，我们是在hive&gt;视图下执行的命令。</p>
<p>hive&gt; <strong><span style="color:#3333ff;">dfs -mkdir /data;</span></strong><span style="color:#3333ff;">
</span></p>
<p>        接着我们向data目录下上传两个文件，名字分别是a.txt和b.txt，内容是一样的。<br>
hive&gt; <strong><span style="color:#3333ff;">dfs -put /root/student.txt /data/a.txt;</span></strong><br>
hive&gt; <strong><span style="color:#3333ff;">dfs -put /root/student.txt /data/b.txt;</span></strong><br>
hive&gt;</p>
<p>        下面我们到HDFS查看data目录及我们上传的两个文件，如下所示。</p>
<p><img src="https://img-blog.csdn.net/20161106142840438?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>       数据已经上传完了，现在我们创建一个外部表，让它指向/data目录，如下所示，其中，external意思是外部表，location意思是指定新建的外部表指向的HDFS的目录。</p>
<p>hive&gt; <strong><span style="color:#3333ff;">create external table ext_student (id bigint,name string) row format delimited fields terminated by '\t' location '/data';</span></strong><br>
OK<br>
Time taken: 0.769 seconds</p>
<p>       现在我们来查询一下我们刚才创建的ext_student表的所有数据，如下所示，发现查询出来8条数据，这是正确的，因为ext_student下面有两个文件分别是a.txt和b.txt，这两个文件内容一样，文件中的内容是4行，因此我们一共查询出来8行内容是正确的。Hive中也有主键只不过比较弱化。<br>
hive&gt; <strong><span style="color:#3333ff;">select * from ext_student;</span></strong><br>
OK<br>
1       张三<br>
2       李四<br>
3       王五<br>
NULL    NULL<br>
1       张三<br>
2       李四<br>
3       王五<br>
NULL    NULL<br>
Time taken: 0.27 seconds, Fetched: 8 row(s)<br>
hive&gt;</p>
<p>       我们再创建一个文件，并在里面存储两行内容，如下所示。</p>
<p>[root@itcast03 ~]# <strong><span style="color:#3333ff;">vim pep.txt</span></strong><br>
100     aaa<br>
101     bbb</p>
<p>        接着我们把pep.txt文件上传到HDFS的/data目录下，如下所示。</p>
<p>[root@itcast03 ~]# <strong><span style="color:#3333ff;">hadoop fs -put /root/pep.txt /data</span></strong><br>
[root@itcast03 ~]#</p>
<p>        然后我们再到hive视图下查询ext_student表所有数据，如下所示，发现我们查询出来10行内容，刚才我们上传到/data目录下的pep.txt文件中的内容也被我们查询出来了。</p>
<p><strong>hive&gt; <span style="color:#3333ff;">select * from ext_student;</span></strong><br>
OK<br>
1       张三<br>
2       李四<br>
3       王五<br>
NULL    NULL<br>
1       张三<br>
2       李四<br>
3       王五<br>
NULL    NULL<br><strong>100     aaa<br>
101     bbb</strong><br>
Time taken: 0.836 seconds, Fetched: 10 row(s)<br>
hive&gt;</p>
<p>        可能有的童鞋会有疑问，会不会只有外部表有这个特性，内部表是不是也是这样呢？那我们现在就向内部表student目录下上传我们刚才建的pep.txt文件然后再查询student表。</p>
<p>[root@itcast03 bin]# <strong><span style="color:#3333ff;">hadoop fs -put /root/pep.txt /user/hive/warehouse/student</span></strong><br>
[root@itcast03 bin]#</p>
<p>         上传完之后我们可以看到HDFS的student目录下已经有pep.txt这个文件了。</p>
<p><img src="https://img-blog.csdn.net/20161106150154826?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>        在hive视图下查询student表的所有数据，发现也查询出了pep.txt中的内容，这说明，无论是外部表还是内部表，只要把某个文件放到表的目录下，就会被扫描并被查询出来。查询的执行过程是先通过TBLS表找到student表，然后根据表id到COLUMNS_V2表查找这张表都有哪些字段，然后再根据表id到SDS表中查找应该到HDFS的哪个目录下去查找数据。</p>
<p>hive&gt; <strong><span style="color:#3333ff;">select * from student;</span></strong><br>
OK<br>
100     aaa<br>
101     bbb<br>
1       张三<br>
2       李四<br>
3       王五<br>
NULL    NULL<br>
Time taken: 0.848 seconds, Fetched: 6 row(s)<br>
hive&gt;</p>
<p>--------------------------------------------------------------------------------------------------------------------------------------</p>
<p><strong><span style="color:#3333ff;">第二部分：hive分区表</span></strong></p>
<p>       首先说一下为什么要分区，其实分区最大的好处是查询非常快速，举个例子，数据库中存放着我们好几年的数据，假如我们没有创建分区，那么如果我们要查询某个月的数据的话，我们需要全表扫描并找到我们要查询的月份的数据，这无疑是非常耗时的，假如我们给表创建了分区，按月存放，这样想查某个月的数据直接到那个分区表中去查询，查询效率将大大提高。</p>
<p>       下面我们就创建一个分区表，如下所示，我们创建的表叫beauties(美女表)，美女按国籍进行分区，列于列之间用'\t'来分割，在HDFS根目录下创建beauty。</p>
<p>hive&gt; <strong><span style="color:#3333ff;">create external table beauties (id bigint,name string,age int) partitioned by (nation string) row format delimited fields terminated by '\t' location '/beauty';</span></strong><br>
OK<br>
Time taken: 0.776 seconds<br>
hive&gt;   </p>
<p>       创建完之后我们到HDFS根目录下查看一下，发现确实已经有beauty目录了，如下图所示。</p>
<p><img src="https://img-blog.csdn.net/20161106162045135?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>      下面我们创建两个文件，分别叫b.c和b.j，b.c存放的是中国的美女，b.j存放的是日本的美女。</p>
<p>[root@itcast03 ~]# <strong><span style="color:#3333ff;">vim b.c</span></strong><br>
1       赵薇    30<br>
2       赵丽颖  29<br>
3       赵雅芝  62</p>
<p>[root@itcast03 ~]# <strong><span style="color:#3333ff;">vim b.j</span></strong><br>
1       福原爱  30<br>
2       井上航平        22<br>
3       酒井法子        45</p>
<p>        下面我们先把b.c放到HDFS的/beauty目录下</p>
<p>[root@itcast03 ~]# hadoop fs -put /root/b.c /beauty<br>
[root@itcast03 ~]#</p>
<p>        上传完b.c之后我们到HDFS的beauty目录下查看我们刚才上传的b.c文件，发现确实存在了。</p>
<p><img src="https://img-blog.csdn.net/20161106163403784?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>      那么，我们这时查询beauties表的话能不能查询到数据呢？我们试试，如下所示，发现我们没有查询出来任何数据，这是为什么？？？其实是因为我们建表的时候设置了分区，分区其实就是子目录，现在/beauty目录下没有任何子目录，所以我们没有查询到任何数据。</p>
<p>hive&gt; <strong><span style="color:#3333ff;">select * from beauties;</span></strong><br>
OK<br>
Time taken: 0.839 seconds<br>
hive&gt;</p>
<p>       现在我们把/root/目录下的b.c文件添加到beauty的分区子目录当中</p>
<p>hive&gt; <strong><span style="color:#3333ff;">load data local inpath '/root/b.c' into table beauties partition (nation='China') ;</span></strong><br>
Copying data from file:/root/b.c<br>
Copying file: file:/root/b.c<br>
Loading data to table default.beauties partition (nation=China)<br>
Partition default.beauties{nation=China} stats: [numFiles=1, numRows=0, totalSize=42, rawDataSize=0]<br>
OK<br>
Time taken: 1.534 seconds</p>
<p>      然后我们再来查询beauties，发现查询出来了中国的美女列表，而且自动增加了一列国籍。<br>
hive&gt; <strong><span style="color:#3333ff;">select * from beauties;</span></strong><br>
OK<br>
1       赵薇    30      China<br>
2       赵丽颖  29      China<br>
3       赵雅芝  62      China<br>
Time taken: 0.428 seconds, Fetched: 3 row(s)<br>
hive&gt;</p>
<p>       之所以我们这时查询到了数据，是因为这时在beauty目录下已经有一个分区的子目录了，而且子目录下有b.c文件（我们查询的数据是nation=China目录下的b.c文件中的数据而不是与nation=China同级的那个b.c文件中的数据），如下图所示。   </p>
<p><img src="https://img-blog.csdn.net/20161106164858392?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>       接下来我们手动在beauty目录下创建一个nation=Japan的分区目录，然后把b.j文件放到它里面，如下所示。</p>
<p>hive&gt; <strong><span style="color:#3333ff;">dfs -mkdir /beauty/nation=Japan;</span></strong><br>
hive&gt; <strong><span style="color:#3333ff;">dfs -put /root/b.j /beauty/nation=Japan;</span></strong></p>
<p><img src="https://img-blog.csdn.net/20161106165605872?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>         我们此时查询beauties表的话，会查询出来几条呢？我们试一下，可以发现我们只查询出了中国的美女，没有查询出日本的美女。这是为什么？因为元数据库中没有记录Japan这个分区。</p>
<p>hive&gt; <strong><span style="color:#3333ff;">select * from beauties;</span></strong><br>
OK<br>
1       赵薇    30      China<br>
2       赵丽颖  29      China<br>
3       赵雅芝  62      China<br>
Time taken: 0.782 seconds, Fetched: 3 row(s)<br>
hive&gt;</p>
<p>      从下图可以看到并没有nation=Japan的目录，因此我们查询不到日本的美女。</p>
<p><img src="https://img-blog.csdn.net/20161106170421438?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>       知道了原因，我们便来修改beauties表并增加Japan分区</p>
<p>hive&gt; <strong><span style="color:#3333ff;">alter table beauties add partition (nation='Japan') location "/beauty/nation=Japan";</span></strong><br>
OK<br>
Time taken: 0.837 seconds<br>
hive&gt;</p>
<p>       建好分区后，我们来看看SDS表现在有没有Japan的分区目录，如下图所示，发现已经有Japan分区目录了。</p>
<p><img src="https://img-blog.csdn.net/20161106171056645?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>     这时我们再查询beauties表，可以看到我们现在把中国和日本的所有美女都查询出来了。</p>
<p>hive&gt; <strong><span style="color:#3333ff;">select * from beauties;</span></strong><br>
OK<br>
1       赵薇    30      China<br>
2       赵丽颖  29      China<br>
3       赵雅芝  62      China<br>
1       福原爱  30      Japan<br>
2       井上航平        22      Japan<br>
3       酒井法子        45      Japan<br>
Time taken: 0.923 seconds, Fetched: 6 row(s)<br>
hive&gt;</p>
<p>        下面我们便运用分区做为查询条件来查询，如下所示，我们查询出来的是中国的美女！有了分区做为查询条件，我们的查询效率将会大大提高。</p>
<p>hive&gt; <strong><span style="color:#3333ff;">select * from beauties where nation='China';</span></strong><br>
OK<br>
1       赵薇    30      China<br>
2       赵丽颖  29      China<br>
3       赵雅芝  62      China<br>
Time taken: 0.993 seconds, Fetched: 3 row(s)<br>
hive&gt;</p>
<p>         好了，本小节课我们便一起学习到这里。</p>
            </div>
                </div>