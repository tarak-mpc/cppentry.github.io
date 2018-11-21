---
layout:     post
title:      Hbase MapReduce例子 
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p><span style="font-size:12px;line-height:18px;font-family:Arial, sans-serif, Helvetica, Tahoma;"><span style="font-size:18px;line-height:27px;">Hbase Mapreduce例子</span></span></p>
<div id="sina_keyword_ad_area2" class="articalContent">
<div>
<p><span lang="en-us" xml:lang="en-us"><a style="color:#108ac6;text-decoration:underline;" href="http://hadoop.apache.org/hbase/docs/current/api/org/apache/hadoop/hbase/mapreduce/package-summary.html#package_description" rel="nofollow">http://hadoop.apache.org/hbase/docs/current/api/org/apache/hadoop/hbase/mapreduce/package-summary.html#package_description</a></span></p>
<p><span lang="en-us" xml:lang="en-us"><a style="color:#108ac6;text-decoration:underline;" href="http://wiki.apache.org/hadoop/Hbase/MapReduce" rel="nofollow"><span style="font-size:13px;font-family:Calibri;">http://wiki.apache.org/hadoop/Hbase/MapReduce</span></a><span style="font-size:13px;font-family:Calibri;"> （Deprecated）</span></span></p>
<p> </p>
<p>需要重启Hadoop的方式</p>
<p>所有机器都有修改配置</p>
<p><span lang="en-us" xml:lang="en-us"><span style="font-size:13px;font-family:Calibri;">1:修改<span style="font-family:'Courier new';">$HADOOP_HOME/conf/hadoop-env.sh</span> ，添加HBase类库引用</span></span></p>
<p><span lang="en-us" xml:lang="en-us"><span style="font-size:13px;font-family:Calibri;">export <span style="font-size:10px;font-family:Verdana;">HBASE_HOME=/home/iic/hbase-0.20.3</span></span></span></p>
<p><span lang="en-us" xml:lang="en-us">exportHADOOP_CLASSPATH=$HBASE_HOME/hbase-0.20.3.jar:$HBASE_HOME/hbase-0.20.3-test.jar:$HBASE_HOME/conf:${HBASE_HOME}/lib/zookeeper-3.3.0.jar</span></p>
<p> </p>
<p><span lang="en-us" xml:lang="en-us">不需要重启Hadoop的方式（把依赖类库打包进jar/lib目录下，同时代码中调用job.setJarByClass(XXX.class);）</span></p>
<p><span lang="en-us" xml:lang="en-us">Another possibility, if forexample you do not have access to hadoop-env.sh or are unable torestart the hadoop cluster, is bundling the hbase jars into amapreduce job jar adding it and its dependencies under the jobjar <code style="font-size:1em;font-family:'Courier new', Courier, monospace;">lib/</code> directoryand the hbase conf into the job jars top-leveldirectory.</span></p>
<p> </p>
<p>测试，出现异常：java.lang.OutOfMemoryError: Java heap space</p>
<p>bin/hadoop org.apache.hadoop.hbase.PerformanceEvaluationsequentialWrite 4 </p>
<h2 style="font-size:1.5em;color:#000000;line-height:1.5em;">HBase map reduce 2</h2>
<p>此例子，把表mrtest中的列contents的值，反转后，保存到列text里。</p>
<p>bin/hbase shell</p>
<p><span lang="en-us" xml:lang="en-us"><span style="font-family:Calibri;">create 'mrtest', 'contents','text'</span></span></p>
<p><span lang="en-us" xml:lang="en-us"><span style="font-family:Calibri;"><span lang="en-us" xml:lang="en-us"><span style="font-family:Calibri;">put 'mrtest', '1','contents:', 'content'</span></span></span></span></p>
<p><span lang="en-us" xml:lang="en-us"><span style="font-family:Calibri;"><span lang="en-us" xml:lang="en-us"><span style="font-family:Calibri;"><span lang="en-us" xml:lang="en-us"><span style="font-family:Calibri;"><span lang="en-us" xml:lang="en-us"><span style="font-family:Calibri;">put'mrtest', '1', 'text:','text'</span></span></span></span></span></span></span></span></p>
<p><span lang="en-us" xml:lang="en-us"><span style="font-family:Calibri;"><span lang="en-us" xml:lang="en-us"><span style="font-family:Calibri;"><span lang="en-us" xml:lang="en-us"><span style="font-family:Calibri;"><span lang="en-us" xml:lang="en-us"><span style="font-family:Calibri;">get'mrtest','1'</span></span></span></span></span></span></span></span></p>
<p> </p>
<p>类com.test.hadoop.hbase.HBaseTest 生成100W的测试数据。</p>
<p> </p>
<p><span lang="en-us" xml:lang="en-us"><span style="font-size:13px;font-family:Calibri;">/home/iic/hadoop-0.20.2/bin/hadoopjar <span> examples/</span>examples_1.jar <span> </span>examples.TestTableMapReduce</span></span></p>
<p> </p>
<h2 style="font-size:1.5em;color:#000000;line-height:1.5em;">HBase 自带例子</h2>
<p>hbase-0.20.3\src\test</p>
<h2 style="font-size:1.5em;color:#000000;line-height:1.5em;">计算表的总行数（org.apache.hadoop.hbase.mapreduce.RowCounter）</h2>
<p>bin/hadoop jar /home/iic/hbase-0.20.3/hbase-0.20.3.jar rowcounterscores grade</p>
<p>结果</p>
<p>10/04/12 17:08:05 INFOmapred.JobClient:    ROWS=2</p>
<p> </p>
<h2 style="font-size:1.5em;color:#000000;line-height:1.5em;">对HBase的列进行Lucene索引(examples.TestTableIndex)</h2>
<p>对表mrtest的列contents进行索引，使用lucene-core-2.2.0.jar，需把它加入类路径。<span lang="en-us" xml:lang="en-us"><span style="font-size:13px;font-family:Calibri;">把<span style="font-size:10px;font-family:Verdana;">lucene-core-2.2.0.jar加入到<span style="font-size:13px;"><span style="font-family:Calibri;">examples.zip/lib目录下，同时代码中必须指定</span></span></span></span></span><span lang="en-us" xml:lang="en-us">job.setJarByClass(TestTableIndex.class);不然lucene不识别</span></p>
<p> </p>
<p>bin/hadoop fs -rmr testindex</p>
<p><span lang="en-us" xml:lang="en-us"><span style="font-size:13px;font-family:Calibri;">bin/hadoopjar <span> </span>examples.zip <span> </span>examples.TestTableIndex</span></span></p>
<p> </p>
<p> </p>
<h2 style="font-size:1.5em;color:#000000;line-height:1.5em;">先从文件中产生适合HBase的HFiles文件，再倒入到Hbase中，加快导入速度</h2>
<p>examples.TestHFileOutputFormat</p>
<p>输入的数据，由例子自动生成,其中Key是前面补0的十位数“0000000001”。</p>
<p>输出数据目录：/user/iic/hbase-hfile-test</p>
<p>bin/hadoop fs -rmr hbase-hfile-test</p>
<p><span lang="en-us" xml:lang="en-us"><span style="font-size:13px;font-family:Calibri;">bin/hadoopjar <span> </span>examples.zip <span> </span>examples.TestHFileOutputFormat</span></span></p>
<p><span lang="en-us" xml:lang="en-us"><span style="font-size:13px;font-family:Calibri;">加载生成的数据到Hbae中（要先安装JRuby，才能执行）</span></span></p>
<p><span lang="en-us" xml:lang="en-us">exportPATH=$PATH:/home/iic/jruby-1.4.0/bin/</span></p>
<p><span lang="en-us" xml:lang="en-us">echo $PATH</span></p>
<p> </p>
<p><span lang="en-us" xml:lang="en-us">vi bin/loadtable.rb</span></p>
<p><span lang="en-us" xml:lang="en-us">require'/home/iic/hbase-0.20.3/hbase-0.20.3.jar'<br>require '/home/iic/hadoop-0.20.2/hadoop-0.20.2-core.jar'<br>require '/home/iic/hadoop-0.20.2/lib/log4j-1.2.15.jar'<br>require'/home/iic/hadoop-0.20.2/lib/commons-logging-1.0.4.jar'<br>require '/home/iic/hbase-0.20.3/lib/zookeeper-3.3.0.jar'<br>require'/home/iic/hbase-0.20.3/lib/commons-cli-2.0-SNAPSHOT.jar'</span></p>
<p><span lang="en-us" xml:lang="en-us">$CLASSPATH&lt;&lt;'/home/iic/hbase-0.20.3/conf';</span></p>
<p> </p>
<p>delete table "hbase-test" </p>
<p> </p>
<p><span lang="en-us" xml:lang="en-us"><span style="font-family:'Courier new';">jruby bin/loadtable.rb</span> hbase-test  /user/iic/hbase-hfile-test</span></p>
<p> </p>
<p><span lang="en-us" xml:lang="en-us">查看其使用方式</span></p>
<p><span lang="en-us" xml:lang="en-us">（bin/hbase org.jruby.Mainbin/loadtable.rb</span></p>
<p><span lang="en-us" xml:lang="en-us">Usage: loadtable.rb TABLENAMEHFILEOUTPUTFORMAT_OUTPUT_DIR</span></p>
<p><span lang="en-us" xml:lang="en-us">其使用JRuby</span></p>
<p><span lang="en-us" xml:lang="en-us">）</span></p>
<p> </p>
<p>注意：此种方式，必须解决几个问题</p>
<p>1：your MapReduce job ensures a total ordering among all keys ，bydefault distributes keys among reducers using a Partitioner thathashes on the map task output key。<span style="font-family:'Courier new';">(key.hashCode() &amp;Integer.MAX_VALUE) % numReduceTasks</span></p>
<p>默认MR在使用默认的default hash Partitioner分配Key给Reducer的时候，如果Key是0~4，有2个Task，则</p>
<p>reducer 0 would have get keys 0, 2 and 4 whereas reducer 1 wouldget keys 1 and 3 (in order).</p>
<p>则生成的Block里面的Start key 和 End Key次序讲混乱，</p>
<p> </p>
<p>System.out.println((newImmutableBytesWritable("0".getBytes())<br>    .hashCode()&amp; Integer.MAX_VALUE)</p>
<p> </p>
<p>所以需要实现自己的Hash Partitioner ，生成the keys need to be orderd so reducer0 gets keys 0-2 and reducer 1 gets keys 3-4 (See<strong style="font-weight:bold;"><span style="color:#ff0000;">TotalOrderPartitioner</span></strong> upin hadoop for more on what this means).</p>
<p> </p>
<p>验证导入的行数</p>
<p>bin/hadoop jar /home/iic/hbase-0.20.3/hbase-0.20.3.jarrowcounter <span style="font-size:10px;">hbase-test info</span> </p>
<p> </p>
<h2 style="font-size:1.5em;color:#000000;line-height:1.5em;">HFile生成例子2：</h2>
<p><span style="color:#ff0000;"><strong style="font-weight:bold;">此种例子，只适合第一次海量导入数据，因为<span style="font-size:10px;"><span style="font-family:'Courier new';">bin/loadtable.rb每次都替换所有的文件。</span></span></strong></span></p>
<p><span style="font-size:10px;color:#ff0000;font-family:'Courier new';"><strong style="font-weight:bold;">对于后续的数据操作，可以使用Map文本文件+HbaseTable直接操作Insert的功能。</strong></span></p>
<p><strong style="font-weight:bold;"><span style="font-size:10px;color:#ff0000;font-family:'Courier new';">或者保证新增加的Key跟原来没有冲突，按照bin/loadtable.rb的逻辑，添加新的Block。</span></strong></p>
<p> </p>
<p>生成1KW数据的test_1kw.log：0,content0,1271222976817</p>
<p><span style="font-size:13px;"><span style="font-size:10px;">/home/bmb/jdk1.6.0_16/bin/java -cp examples.zip examples.CreateLogFile test_1kw.log 10000000</span></span></p>
<p><span style="font-size:13px;"><span style="font-size:13px;"><span style="font-size:10px;">bin/hadoop fs-put test_1kw.log hadoop-performance-test</span></span></span></p>
<p><span style="font-size:13px;"><span style="font-size:13px;"><span style="font-size:10px;">只用1个ReduceTask，避免Total Order Key的问题</span></span></span></p>
<p><span style="font-size:13px;"><span style="font-size:10px;">bin/hadoop jar examples.zipexamples.TestCreateHFileMR hadoop-performance-test hadoop-hbase-hfile-test  1</span></span></p>
<p><span style="font-size:13px;"><span style="font-size:10px;">生成HbaseHfile文件才花了一点时间，比性能测试生成1KW的HBase数据快了N多。</span></span></p>
<p><span style="font-size:13px;"><span style="font-size:10px;">10/04/15 14:22:59--10/04/1514:25:22</span></span></p>
<p><span style="font-size:13px;"><span style="font-size:10px;">导入Hbase</span></span></p>
<p><span style="font-size:13px;"><span lang="en-us" xml:lang="en-us"><span style="font-size:10px;"><span style="font-family:'Courier new';">jruby bin/loadtable.rb</span> hbase-test2 hadoop-hbase-hfile-test</span></span></span></p>
<p> </p>
<p>验证导入的行数</p>
<p>bin/hadoop jar /home/iic/hbase-0.20.3/hbase-0.20.3.jarrowcounter <span style="font-size:10px;">hbase-test2 info</span></p>
<p> </p>
<p>其他HBase MapReduce例子<br><a style="color:#108ac6;text-decoration:underline;" href="http://www.hadoop.org.cn/mapreduce/hbase-mapreduce/" rel="nofollow">http://www.hadoop.org.cn/mapreduce/hbase-mapreduce/</a></p>
<p><span lang="en-us" xml:lang="en-us"><a style="color:#108ac6;text-decoration:underline;" href="http://www.spicylogic.com/allenday/blog/2008/08/28/hbase-bulk-load-import-example/" rel="nofollow"><span style="font-size:13px;font-family:Calibri;">http://www.spicylogic.com/allenday/blog/2008/08/28/hbase-bulk-load-import-example/</span></a></span></p>
</div>
</div>
<p> </p>            </div>
                </div>