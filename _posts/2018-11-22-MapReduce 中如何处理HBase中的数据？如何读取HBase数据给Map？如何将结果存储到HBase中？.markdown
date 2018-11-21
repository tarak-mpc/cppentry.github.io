---
layout:     post
title:      MapReduce 中如何处理HBase中的数据？如何读取HBase数据给Map？如何将结果存储到HBase中？
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div class="article_title" style="font-size:20px;line-height:30px;font-family:'Microsoft YaHei';">
<h3 style="display:inline;font-weight:normal;font-size:20px;vertical-align:middle;">
<span class="link_title"><a href="http://blog.csdn.net/yanmingming1989/article/details/7011928" rel="nofollow" style="color:rgb(0,0,0);text-decoration:none;">MapReduce 中如何处理HBase中的数据？如何读取HBase数据给Map？如何将结果存储到HBase中？</a></span></h3>
</div>
<br><div class="tag2box" style="font-family:Arial, Console, Verdana, 'Courier New';font-size:13.333333969116211px;">
<br></div>
<div id="article_content" class="article_content" style="font-size:14px;line-height:26px;font-family:Arial;">
   MapReduce 中如何处理HBase中的数据？如何读取HBase数据给Map？如何将结果存储到HBase中？<br><br><br>
Mapper类：包括一个内部类(Context)和四个方法(setup,map,cleanup,run)；<br>
          setup,cleanup用于管理Mapper生命周期中的资源。setup -&gt; map -&gt; cleanup ， run方法执行了这个过程；<br>
          map方法用于对一次输入的key/value对进行map动作，对应HBase操作也就是一行的处理；<br><br><br>
job的配置：<br>
    1.  TableInputFormat完成了什么功能？<br>
         (1)通过设置conf.set(TableInputFormat.INPUT_TABLE,"udc_sell");设定HBase的输入表；<br>
                设置conf.set(TableInputFormat.SCAN, TableMRUtil.convertScanToString(scan));设定对HBase输入表的scan方式；<br>
               <img src="http://hi.csdn.net/attachment/201111/25/3737329_1322202435Dzf5.jpg" alt="" style="border:none;"><br>
                <br>
         (2)通过TableInputFormat.setConf(Configration conf)方法初始化scan对象；<br>
            scan对象是从job中设置的对象，以字符串的形式传给TableInputFormat，在TableInputFormat内部将scan字符创转换为scan对象<br>
             <img src="http://hi.csdn.net/attachment/201111/25/3737329_1322202443FOkW.jpg" alt="" style="border:none;">       <br>
           * TableMapReduceUtily有两个方法：convertScanToString和convertStringToScan作用？<br>
            将scan实例转换为Base64字符串  和将Base64字符串还原为scan实例；<br>
             Q:为什么不直接穿Scan对象而是费尽周折地转换来转换去呢？<br>
             A:<br>
         (3)TableInputFormat继承了TableInputFormatBase实现了InputFormat抽象类的两个抽象方法：<br>
            getSplits()和createRecordReader()方法：<br>
            A:getSplits()断定输入对象的切分原则：对于TableInputFormatBase，<span style="color:#ff0000;">会遍历HBase相应表的所有HRegion</span>，每一个HRegion都会被分成一个split，所以切分的块数是 与表中HRegion的数目是相同的；<br>
                InputSplit split = new TableSplit(table.getTableName(),splitStart, splitStop, regionLocation);  在split中只会记载HRegion的其实rowkey和终止rowkey，具体的去读取 这片区域的数据是createRecordReader()实现的。<br>
             计算出来的每一个分块都将被作为一个map Task的输入；http://<br>
                 Q:但是分出的块分给那台机器的那个task去执行Map，即jobTracker如何调度任务给taskTracker？<br>
                 A:  需要进一步了解Map的本地化运行机制和jobTracker的调度算法；（可能是就近原则）<br>
                      对于一个map任务，jobtracker会考虑tasktracker的网络位置，并选取一个距离其输入分片文件最近的tasktracker。在最理想 的情况下，任务是数据本地化的(data-                       local),也就是任务运行在输入分片所在的节点上。同样，任务也可能是机器本地化的：任务和输入分片在同一个机架，但不在同 一个节点上。<br>
                      reduce任务，jobtracker简单滴从待运行的reduce任务列表中选取下一个来运行，用不着考虑数据段饿本地化。<br>
            B:createRecordReader()按照必然格式读取响应数据：<br>
                 接收split块，返回读取记录的结果；  <br>
                 public RecordReader&lt;ImmutableBytesWritable, Result&gt; createRecordReader(InputSplit split, TaskAttemptContext context){<br>
                  <img src="http://hi.csdn.net/attachment/201111/25/3737329_1322202449J98Q.jpg" alt="" style="border:none;"><br>
                }<br>
                trr.init()返回的是这个分块的起始rowkey的记录；<img src="http://hi.csdn.net/attachment/201111/25/3737329_1322202753r0Or.jpg" alt="" style="border:none;"><br>
           RecordReader将一个split解析成&lt;key,value&gt;对的形式提供给map函数，key就是rowkey，value就是对应的一行数据；<br>
           RecordReader用于在划分中读取&lt;Key,Value&gt;对。RecordReader有五个虚方法，分别是：<br>
                                          initialize：初始化，输入参数包括该Reader工作的数据划分InputSplit和Job的上下文context；<br>
                                          nextKey：得到输入的下一个Key，如果数据划分已经没有新的记录，返回空；<br>
                                          nextValue：得到Key对应的Value，必须在调用nextKey后调用；<br>
                                          getProgress：得到现在的进度；<br>
                                          close：来自java.io的Closeable接口，用于清理RecordReader。<br>
   2.   job.setInputFormatClass(TableInputFormat.class);         <br>
                                       <br>
   3.   TableMapReduceUtil.initTableReducerJob("daily_result", DailyReduce.class, job); <br>
        使用了该方法就不需要再单独定义 <br>
        initTableReducerJob()方法完成了一系列操作：<br>
                (1). job.setOutputFormatClass(TableOutputFormat.class); 设置输出格式；<br>
                (2). conf.set(TableOutputFormat.OUTPUT_TABLE, table); 设置输出表；<br>
                (3). 初始化partition</div>
            </div>
                </div>