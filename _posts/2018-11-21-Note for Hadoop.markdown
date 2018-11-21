---
layout:     post
title:      Note for Hadoop
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
安装/bin/hadoop namenode -format<br>
查看进程 /usr/bin/jps<br>
拷贝到hdfs   bin/hadoop dfs -put ../../grid/input in<br>
从hdfs拷贝到linux  bin/hadoop dfs -get abc  ./xyz<br>
删除  bin/hadoop dfs -rmr abc<br>
查看文件内容  bin/hadoop dfs -cat ./in/abc<br>
查看目录 bin/hadoop dfs -ls ./in/*<br>
         bin/hadoop dfs -ls ./out<br>
查看统计信息 bin/hadoop dfsadmin -report<br>
         <br>
运行样例：bin/hadoop jar hadoop-0.20.2-examples.jar wordcount in out<br>
http查看namenod:50070端口    http://192.168.0.2:50070/dfshealth.jsp<br>
http查看jobtracker:50030端口  http://192.168.0.2:50030/jobtracker.jsp<br>
http查看日志    http://192.168.0.2:50030/logs   http://192.168.0.2:50070/logs<br>
http设置日志级别  http://192.168.0.2:50070/logLevel<br>
HDFS文件操作：<br>
命令方式：<br>
        启动：start-all.sh<br>
        停止：stop-all.sh<br>
        查看 bin/hadoop dfs -ls<br>
        存入 bin/hadoop dfs -put 源文件 目标文件<br>
        存入 bin/hadoop dfs -copyFromLocal 源文件 目标文件<br>
        取出 bin/hadoop dfs -get 源文件 目标文件<br>
        删除 bin/hadoop dfs -rmr 源文件夹<br>
        删除 bin/hadoop dfs -rm  源文件<br>
        查看 bin/hadoop dfs -cat 源文件<br>
        查看 bin/hadoop dfs -text 源文件<br>
        查看 bin/hadoop dfs -tail 源文件<br>
        创建目录 bin/hadoop dfs -mkdir /user/xxx<br>
        创建文件 bin/hadoop dfs -touchz /user/xxx<br>
        重命名 bin/hadoop dfs -mv /user/xxx user/xxx<br>
        合并文件 bin/hadoop dfs -getmerge /user/home/xxx<br>
        关闭作业 bin/hadoop job -kill [job-id]<br>
        查看整个系统报告   bin/hadoop dfsadmin -report  #检查HDFS块状态，包括DN信息<br>
        查看是否安全模式：bin/hadoop dfsadmin -safemode get<br>
        打开安全模式：bin/hadoop dfsadmin -safemode enter<br>
        关闭安全模式：bin/hadoop dfsadmin -safemode leave<br>
        启动节点上的datanode: hadoop-daemon.sh start datanode<br>
        启动节点上的tasktracker: hadoop-daemon.sh start tasktracker<br>
        进行数据负载均衡： start-balancer.sh<br>
        启动namenote(start dfs daemons):bin/start-dfs.sh --config $HADOOP_CONF_DIR<br>
        启动MapReduce(start mapred daemons):bin/start-mapred.sh --config $HADOOP_CONF_DIR<br>
        启动负载均衡：bin/star-balancer.sh  #平衡集群文件<br>
        并行拷贝：hadoop distcp hdfs://a:8020/xxx hdfs://b:8020/// <br>
        <br>
        列出正在运行的Job :hadoop job -list<br>
        kill job:hadoop job -kill &lt;job_id&gt;<br>
        检查HDFS块状态，是否损坏：hadoop fsck /<br>
        删除损坏块：hadoop fsck / -delete<br>
        并行copy:hadoop distcp hdfs://ZeroHostName:8020/xxx hdfs://OtherHostName:8020///<br>
        <br>
        <br>
                             <br>
API方式：http://hadoop.apache.org/docs/r0.23.6/api/index.html<br><span></span>package cn.forey.hadoop.hdsf<br><span></span>static FileSystem get(Configuration conf)<br><span></span>operator(){<br><span></span>//step 1<br><span></span>得到Configuration对象<br><span></span>//step 2<br><span></span>得到FileSystem对象<br><span></span>//step 3<br><span></span>进行文件操作<br><span></span>}<br><span></span>  hadoop jar /home/ZeroUserName/temp/hdfsmkdir.jar cn.forey.hadoop.hdfs.HdfsMkdir<span>
</span><br><span></span>hadoop-0.20.2-examples.jar是hadoop-0.20.2自带的一些案例：<br><span></span><span></span>1.aggregatewordcount 计算输入文件中文字个数的基于聚合的MapReduce程序；<br><span></span><span></span>2.aggregatewordlist  生成输入文件中文字个数的统计图的基于聚合的MapReduce程序；<br><span></span><span></span>3.grep 计算输入文件中匹配正则表达式的文字个数的MapReduce程序；<br><span></span><span></span>4.join 合并排序的平均分割的数据库的作业；<br><span></span><span></span>5.pentomino 解决五格拼板问题的分块分层的MapReduce程序；<br><span></span><span></span>6.pi 使用蒙地卡罗法计算pi的MapReduce程序；<br><span></span><span></span>7.Randomtextwriter 在一个节点上写10G随机文本的MapReduce程序；<br><span></span><span></span>8.randomwriter 在每个节点上写10G随机数据的MapReduce程序；<br><span></span><span></span>9.sleep 在每个Map和Reduce作业中休憩的程序；<br><span></span><span></span>10.sort 排序随机写入器生成的数据的MapReduce程序；<br><span></span><span></span>11.sudoku 一个九宫格游戏的解决方案；<br><span></span><span></span>12.wordcount 在输入文件中统计文字个的统计器。<span>
</span><br><span></span><br><span></span>写MapReduce程序的步骤：<br><span></span> 1、把问题转化为MapReduce模型<br><span></span> 2、设置运行的参数<br><span></span> 3、写map类<br><span></span> 4、写reduce类<br><span></span> <span> </span><br><br><br><br><br>
第三方工具：Ganglia     Chukwa       Openstack<br>
运行jar文件：bin/hadoop jar /home/.../xxx.jar  /home/../input /home/.../output<br><br><br>
---------------Hbase------------------<br>
此数据库适用场景：成熟的分析主题，已确立查询模式;高速插入，大量读取;操作简单 key-value<br>
            优势：天生面向时间戳查询;基于行键的查询异常快;分布式化解负荷。<br>
1、安装步骤：<br>
      单机模式：1&gt;下载解压Hbase安装包 mirror.bjtu.edu.cn/apache/hbase/hbase-0.90.5/<br>
                                      mirror.bit.edu.cn/apache/hbase/ <br>
                2&gt;修改conf/hbase-env.sh脚本，设置环境变量<br>
                3&gt;编辑hbase-site.xml进行配置  在hbase-env.sh加上export JAVA_HOME=/usr<br>
                                              在hbase-site.xml中加上<br>
                                              &lt;configuration&gt;<br>
                                              <span></span>&lt;property&gt;<br>
                                              <span></span>&lt;name&gt;hbase.rootdir&lt;/name&gt;<br>
                                              <span></span>&lt;value&gt;file:///home/hadoop/database&lt;/value&gt;<br>
                                              <span></span>&lt;/property&gt;<br>
                                              &lt;/configuration&gt;<br>
                4&gt;启动Hbase  bin/start-hbase.sh<br>
                5&gt;验证Hmaster已经启动  /usr/bin/jps<br>
                6&gt;进入shell  bin/hbase shell<br>
                <br>
        伪分布模式： 1&gt;在单点模式的基础上继续<br>
        <span></span>2&gt;编辑hbase-env.sh增加HBASE_CLASSPATH环境变量 export HBASE_CLASSPATH=/home/hadoop/hadoop-0.20.2/conf<br>
        <span></span>3&gt;编辑hbase-site.xml打开分布模式<br>
                                              &lt;configuration&gt;<br>
                                              <span></span>&lt;property&gt;<br>
                                              <span></span>&lt;name&gt;hbase.rootdir&lt;/name&gt;<br>
                                              <span></span>&lt;value&gt;file:///home/hadoop/database&lt;/value&gt;<br>
                                              <span></span>&lt;/property&gt;<br>
                                              <span></span>&lt;proerty&gt;<br>
                                              <span></span>&lt;name&gt;hbase.cluster.distributed&lt;/name&gt;<br>
                                              <span></span>&lt;value&gt;true&lt;/value&gt;<br>
                                              <span></span>&lt;/property&gt;<br>
                                              &lt;/configuration&gt;<br>
        <span></span>4&gt;覆盖hadoop核心jar包  主要目的是防止hbase和hadoop版本不同出现兼容性问题，造成hmaster启动异常。、<br>
        <span></span>     在hbase的lib目录中$mv hadoop-core-0.20-append-r1056497.jar hadoop-core-0.20-append-r1056497.sav<br>
        <span></span>                       $ls ../../hadoop-0.20.2/<br>
        <span></span>                       $cp ../../hadoop-0.20.2/hadoop-0.20.2-core.jar .<br>
        <span></span>                       $ls <br>
        <span></span>5&gt;启动hbase bin/start-hbase.sh<br>
        <span></span>6&gt;验证启动  /usr/bin/jps<br>
        <span></span> <br>
        <span></span>完全分布模式  1&gt;配置hosts,确保涉及的主机名均可以被解析为ip<br>
        <span></span>   2&gt;编辑hbase-env.xml<br>
        <span></span>   3&gt;编辑hbase-site.xml<br>
        <span></span>   4&gt;编辑regionservers文件<br>
        <span></span>   5&gt;把Hbase复制到其他节点<br>
        <span></span>   6&gt;启动Hbase<br>
        <span></span>   7&gt;验证启动<span>
</span> <br>
        <span></span> <br>
2、Web管理界面  192.168.0.2:60010/master.jsp<br><br><br>
3、操作数据库：1)HQL:$.\hbase shell<br>
                  A、帮助  &gt;help<br>
                  B、查询数据库状态 &gt;status<br>
                  C、查询数据库版本 &gt;version<br>
                  D、创建表 &gt;create 'member','member_id','address','info'<br>
                  E、查看表信息 &gt;list<br>
                                &gt;describe 'member'<br>
                  F、删除列族  &gt;disable 'member'  //首先需要离线该表<br>
                               &gt;alter 'member',{NAME=&gt;'member_id',METHOD=&gt;'delete'}<br>
                               &gt;enbale 'member'<br>
                  G、删除表   &gt;disable 'member'<br>
                              &gt;drop 'member'<br>
                  H、查询一个表是否存在  &gt;exists 'member'<br>
                  I、判断表是否在线 &gt;is_enabled 'member'<br>
                                 or &gt;is_disabled 'member'<br>
                  J、插入记录   &gt;put 'member','Jack','info:age','24'<br>
                                &gt;put 'member','Jack','info:birthday','1987-06-17'<br>
                                &gt;put 'member','Jack','info:company','alibaba'<br>
                                &gt;put 'member','Jack','address:country','China'<br>
                                &gt;put 'member','Jack','address:province','Zhejiang'<br>
                                &gt;put 'member','Jack','address:city','hangzhou'   <br>
                  K、查询，获取一个行键的所有数据        &gt;get 'member','Jack'<br>
                           获取行键、列族(列键)所有数据  &gt;get 'member','Jack','info'<br>
                  L、更新一条记录  &gt;put 'member','Jack','info:age','99'<br>
                                   &gt;get 'member','Jack','info:age'<br>
                  M、全表扫描      &gt;scan 'member'<br>
                  N、删除指定行键的字段  &gt;delete 'member','temp','info:age'<br>
                                         &gt;get 'member','temp'<br>
                  O、删除整行  &gt;deleteall 'member','temp'   <br>
                  P、查询表的行数  &gt;count 'member'               <br>
                  Q、清空表  &gt;truncate 'member'<br>
                                                                                <br><span></span>            2)API:<span>
</span>http://hbase.apache.org/apidocs/index.html<br><br><br>
4、模式设计  <br>
           A、场景：浏览商品记录<br>
               1)行键 userid<br>
               2)列族和列 book:bookid<br>
               3)为了充分利分布式，可以用reverse key、hash等技巧改造行键    <br>
           <br>
           B、场景：推荐商品<br>
              1)两个表：u-t,t-u<br>
              2)U-t:行键 userid,列族和列 thread:threadid<br>
              3)T-u:行键 threadid,列族和列 user:userid<br>
              4)查询，t-u:threadid--&gt;userid             <br>
                      u-t: userid--&gt;threadid,                      <br>
                      在程序中实现去重和统计功能。<span> </span><br>
                      <br>
           C、场景：学生表(学号，身份证号，姓名，性别，系，年龄)在学号或身份证事情上查询<br>
                   主表：行键学号，列族为学生，下面的列是身份证号，姓名，性别，系，年龄<br>
                   辅助(索引)表：行键为身份号，列族和列为学号。<br>
                   <br>
            D、复合行键设计：&lt;userid&gt;:&lt;colfam&gt;:&lt;messageid&gt;:&lt;timestamp&gt;:&lt;email-message&gt;<br>
                             &lt;userid&gt;-&lt;messageid&gt;:&lt;colfam&gt;:&lt;qualifief&gt;:&lt;timestamp&gt;:&lt;email-message&gt;<br>
                             好处：便于分布，便于多条件伸缩查询。<span> </span>
<br>
                             <br>
                             <br>
-------------------pig------------------<br>
1、可以看作hadoop的客户端软件，使用类似SQL的面向数据流的pig latin进行数据处理，<br>
   可以进行排序、过滤、求和、分组、关联等常用操作，可以自定义函数，<br>
   可以看作是pig lation到map-reduce的映射器,<br>
   pig latin面向数据分析处理的轻量级脚本语言。<br>
   <br>
2、安装pig   1)下载并解压pig安装包 http://pig.apache.org/  <br>
                                   http://mirror.bjtu.edu.cn/apache/pig/pig-0.9.2/<br>
             2)设置环境变量  用户的.bash_profile文件：<br>
             <span> </span>                             PATH=$PATH:$HOME/bin:/home/hadoop/pig-0.9.2/bin<br>
             <span> </span>                             JAVA_HOME=/usr<br>
             <span> </span>                             export JAVA_HOME           <br>
             3)进入grunt shell验证  pig -x local<br>
             <br>
3、pig工作模式   1)本地模式 所有文件和执行过程都在本地，一般用于测试程序<br>
                 2)Mapreduce模式：实际工作模式 <br>
                                 a、配置pig的map-reduce模式<br>
                                           设置PATH,增加指向hadoop/bin<br>
                                                PATH=$PATH:$HOME/bin:/home/hadoop/pig-0.9.2/bin:/home/hadoop/hadoop-0.20.2/bin<br>
                                                JAVA_HOME=/usr<br>
                                                PIG_CLASSPATH=/home/hadoop/hadoop-0.20.2/conf/<br>
                                                <br>
                                                export PIG_CLASSPATH<br>
                                                export JAVA_HOME<br>
                                                export PATH<br>
                                           设置PIG_CLASSPATH环境变量<br>
                                           修改hosts文件 vi /etc/hosts<br>
                                           启动pig<br><br><br>
4、pig的运行方法    <br>
      a.脚本<br>
      b.Grunt  <br>
          自动补全机制,只能对命令补全<br>
          autocomplete文件<br>
          eclipse插件PigPen<br>
          备注： 一般linux的命令可直接用，如cat ls cd <br>
                  copyeToLocal example2  ttt<br>
                  运行linux的命令：如 sh /usr/bin/jps<br>
                  Grunt shell命令：cat fs sh cd cp copyFromLocal copyToLocal dump<br>
                                   describe aliases explain help kill ls mv mkdir<br>
                                   pwd quit register rm rmf set illustrate run exec<br>
                                   scriptDone<br>
      c.嵌入式<br><br><br>
5.pig数据模型 <br>
    Bag:表<br>
    Tuple:行，记录<br>
    Field:属性<br>
    Pig:不要求同一个bag里面的各个tuple有相同数量或相同灰型的field<br><br><br>
6.pig lation常用语句<br>
    LOAD:载入数据的方法<br>
    FOREACH:逐行扫描进行某种处理<br>
    FILTER:过滤行<br>
    DUMP:把结果显示到屏幕<br>
    STORE:把结果保存到文件<br>
       grunt&gt;records = LOAD 'input/ncdc/micro-tab/sample.txt'<br>
               &gt;&gt;AS (year:chararray,temperature:int,quality:int);<br>
       grunt&gt;DUMP records;<br>
       grunt&gt;DESCRIBE records;<br>
       grunt&gt;filtered_records = FILTER records BY temperature != 9999 AND (quality == 0 OR quality == 1 OR quality == 4 OR quality == 5 OR quality == 9);<br>
       grunt&gt;DUMP filtered_records;<br>
       grunt&gt;grouped_records = GROUP  filtered_records BY year;<br>
       grunt&gt;DUMP grouped_records;<br>
       grunt&gt;max_temp = FOREACH grouped_records GENERATE group,<br>
             &gt;&gt;MAX(filtered_records.temperature);<br>
       grunt&gt;DUMP max_temp;<br>
       <br>
7.LOAD,FOREACH,STORE三部曲  <br>
        pig<br>
        A = LOAD 'csdn.txt' USING PigStorage('#') AS (id,pw,em);<br>
        B = FOREACH A GENERATE em;<br>
        STORE B INTO '/home/ZeroUserName/temp/email.txt' USING PigStorage();<br>
       <br>
      <br><br><br><br><br><br><br><br><br><br><br><br><br><br><br>
   <br>
-------------------hive-------------------<br>
     <br>
        <span></span>
            </div>
                </div>