---
layout:     post
title:      [大数据]连载No19 Hbase Shell和API的增删改查+与MapperReducer读写操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>本次总结如下<br><span style="font-size:12px;color:#009900;">1、Hbase Shell的常用命令<br>2、Java APi 对hbase的增删改查<br></span><span style="color:rgb(0,153,0);font-size:12px;">3、Mapper Reducer从hbase读写数数据，计算单词数量，并写回hbase</span></p><p>登录hbase Shell<br></p><p class="p1"><span class="s1">[root@master ~]#<span>  </span>/home/softs/hbase-0.98.12.1-hadoop2/bin/hbase shell<br><br></span></p>1、表操作<br><p><span style="font-size:12px;">创建表user    create 'test', 'cf'     # test表明  cf列族<br>查询表user    scan  'test'</span></p>2、增删改查操作<br><span style="font-size:12px;">插入数据  put 'test', 'row1', 'cf:username', 'value1'    # row行唯一标识符   username列名<br>查询数据  list 'test'<br>id查询    get 'test', 'row1'<br>删除表    disable 'test' 然后 drop 'test'    #删除前要先禁用掉<br></span><p><br></p><p class="p1"></p><pre style="background-color:#2b2b2b;color:#a9b7c6;font-family:Menlo;font-size:9pt;">hbase(main):001:0&gt; scan 'user'
SLF4J: Class path contains multiple SLF4J bindings.
SLF4J: Found binding in [jar:file:/home/softs/hbase-0.98.12.1-hadoop2/lib/slf4j-log4j12-1.6.4.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/home/softs/hadoop-2.5.0/share/hadoop/common/lib/slf4j-log4j12-1.7.5.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
2018-06-29 04:03:54,274 WARN  [main] util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
ROW                                 COLUMN+CELL                                                                                            
 2                                  column=col1:count, timestamp=1530214622282, value=1                                                    
 userId1                            column=col1:age, timestamp=1530201359347, value=2                                                      
 userId1                            column=col1:name, timestamp=1530203162657, value=xiaohong                                              
 userId1                            column=col2:age, timestamp=1530203197562, value=33                                                     
 userId1                            column=col2:name, timestamp=1530201359347, value=\xE5\xB0\x8F\xE7\xBA\xA2                              
2 row(s) in 0.3550 seconds

hbase(main):002:0&gt; </pre><br>Hbase WebUI查看集群信息<br><img src="https://img-blog.csdn.net/20180630162436850?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTM0ODc1NDg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br><br><p><br></p><p>java操作hbase Api<br></p><pre style="background-color:#2b2b2b;color:#a9b7c6;font-family:Menlo;font-size:9pt;"><span style="color:#cc7832;">public class </span>HbaseCrub {

    HBaseAdmin <span style="color:#9876aa;">hbase</span><span style="color:#cc7832;">;
</span><span style="color:#cc7832;">    </span>HTable <span style="color:#9876aa;">table</span><span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">    </span>String <span style="color:#9876aa;">user </span>= <span style="color:#6a8759;">"user"</span><span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">    </span>String <span style="color:#9876aa;">col1</span>=<span style="color:#6a8759;">"col1"</span><span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">    </span>String <span style="color:#9876aa;">col2</span>=<span style="color:#6a8759;">"col2"</span><span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">    </span><span style="color:#bbb529;">@Before
</span><span style="color:#bbb529;">    </span><span style="color:#cc7832;">public void </span><span style="color:#ffc66d;">before</span>() <span style="color:#cc7832;">throws  </span>Exception{
        Configuration configuration=<span style="color:#cc7832;">new </span>Configuration()<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">        </span><span style="color:#629755;"><em>/**指定zookeeper集群，找到配置文件*/
</em></span><span style="color:#629755;"><em>        </em></span>configuration.set(<span style="color:#6a8759;">"hbase.zookeeper.quorum"</span><span style="color:#cc7832;">,</span><span style="color:#6a8759;">"master,node1,node2"</span>)<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">        </span><span style="color:#629755;"><em>/**数据库连接**/
</em></span><span style="color:#629755;"><em>        </em></span><span style="color:#9876aa;">hbase </span>=<span style="color:#cc7832;">new </span>HBaseAdmin(configuration)<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">        </span><span style="color:#9876aa;">table</span>=<span style="color:#cc7832;">new </span>HTable(configuration<span style="color:#cc7832;">,</span><span style="color:#9876aa;">user</span>.getBytes())<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">    </span>}

    <span style="color:#bbb529;">@After
</span><span style="color:#bbb529;">    </span><span style="color:#cc7832;">public void </span><span style="color:#ffc66d;">end</span>() <span style="color:#cc7832;">throws  </span>Exception{

        <span style="color:#cc7832;">if</span>(<span style="color:#9876aa;">hbase </span>!= <span style="color:#cc7832;">null</span>) {
            <span style="color:#9876aa;">hbase</span>.close()<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">        </span>}
        <span style="color:#cc7832;">if</span>(<span style="color:#9876aa;">table </span>!= <span style="color:#cc7832;">null</span>) {
            <span style="color:#9876aa;">table</span>.close()<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">        </span>}
    }


    <span style="color:#bbb529;">@Test
</span><span style="color:#bbb529;">    </span><span style="color:#cc7832;">public void </span><span style="color:#ffc66d;">createTable</span>() <span style="color:#cc7832;">throws  </span>Exception{
        <span style="color:#cc7832;">if</span>(<span style="color:#9876aa;">hbase</span>.tableExists(<span style="color:#9876aa;">user</span>.getBytes())){

            <span style="color:#808080;">/*禁用，删除**/
</span><span style="color:#808080;">            </span><span style="color:#9876aa;">hbase</span>.disableTable(<span style="color:#9876aa;">user</span>.getBytes())<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">            </span><span style="color:#9876aa;">hbase</span>.deleteTable(<span style="color:#9876aa;">user</span>.getBytes())<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">        </span>}

        HTableDescriptor descriptor =<span style="color:#cc7832;">new </span>HTableDescriptor(TableName.<span style="font-style:italic;">valueOf</span>(<span style="color:#9876aa;">user</span>))<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">        </span><span style="color:#629755;"><em>/**要先指定列族*/
</em></span><span style="color:#629755;"><em>        </em></span>HColumnDescriptor columnDescriptor=<span style="color:#cc7832;">new </span>HColumnDescriptor(<span style="color:#9876aa;">col1</span>.getBytes())<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">        </span><span style="color:#629755;"><em>/**内存缓存*/
</em></span><span style="color:#629755;"><em>        </em></span>columnDescriptor.setInMemory(<span style="color:#cc7832;">true</span>)<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">        </span>descriptor.addFamily(columnDescriptor)<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">        </span>HColumnDescriptor columnDescriptor2=<span style="color:#cc7832;">new </span>HColumnDescriptor(<span style="color:#9876aa;">col2</span>.getBytes())<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">        </span><span style="color:#629755;"><em>/**使用数据使用内存先存放*/
</em></span><span style="color:#629755;"><em>        </em></span>columnDescriptor2.setInMemory(<span style="color:#cc7832;">false</span>)<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">        </span>descriptor.addFamily(columnDescriptor2)<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">        </span><span style="color:#9876aa;">hbase</span>.createTable(descriptor)<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">    </span>}


    <span style="color:#bbb529;">@Test
</span><span style="color:#bbb529;">    </span><span style="color:#cc7832;">public void </span><span style="color:#ffc66d;">insertUser</span>() <span style="color:#cc7832;">throws  </span>Exception{
        <span style="color:#629755;"><em>/**指定rowkey*/
</em></span><span style="color:#629755;"><em>        </em></span>String  rowKey =<span style="color:#6a8759;">"userId1"</span><span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">        </span>Put put =<span style="color:#cc7832;">new </span>Put(rowKey.getBytes())<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">        </span>put.add(<span style="color:#9876aa;">col1</span>.getBytes()<span style="color:#cc7832;">,</span><span style="color:#6a8759;">"name"</span>.getBytes()<span style="color:#cc7832;">,</span><span style="color:#6a8759;">"小石头"</span>.getBytes())<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">        </span>put.add(<span style="color:#9876aa;">col1</span>.getBytes()<span style="color:#cc7832;">,</span><span style="color:#6a8759;">"age"</span>.getBytes()<span style="color:#cc7832;">,</span><span style="color:#6a8759;">"2"</span>.getBytes())<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">        </span>put.add(<span style="color:#9876aa;">col2</span>.getBytes()<span style="color:#cc7832;">,</span><span style="color:#6a8759;">"name"</span>.getBytes()<span style="color:#cc7832;">,</span><span style="color:#6a8759;">"小红"</span>.getBytes())<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">        </span><span style="color:#9876aa;">table</span>.put(put)<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">    </span>}

    <span style="color:#bbb529;">@Test
</span><span style="color:#bbb529;">    </span><span style="color:#cc7832;">public void </span><span style="color:#ffc66d;">deleteUser</span>() <span style="color:#cc7832;">throws  </span>Exception{

        <span style="color:#629755;"><em>/**指定rowkey*/
</em></span><span style="color:#629755;"><em>        </em></span>Delete delete =<span style="color:#cc7832;">new </span>Delete(<span style="color:#6a8759;">"userId1"</span>.getBytes())<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">        </span>delete.deleteColumn(<span style="color:#9876aa;">col1</span>.getBytes()<span style="color:#cc7832;">,</span><span style="color:#6a8759;">"name"</span>.getBytes())<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">        </span><span style="color:#9876aa;">table</span>.delete(delete)<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">    </span>}


    <span style="color:#bbb529;">@Test
</span><span style="color:#bbb529;">    </span><span style="color:#cc7832;">public void </span><span style="color:#ffc66d;">getByUserId</span>() <span style="color:#cc7832;">throws  </span>Exception{

        <span style="color:#629755;"><em>/**指定rowkey*/
</em></span><span style="color:#629755;"><em>        </em></span>Get get =<span style="color:#cc7832;">new </span>Get(<span style="color:#6a8759;">"userId1"</span>.getBytes())<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">        </span><span style="color:#629755;"><em>/**指定返回的列*/
</em></span><span style="color:#629755;"><em>        </em></span>get.addColumn(<span style="color:#9876aa;">col1</span>.getBytes()<span style="color:#cc7832;">,</span><span style="color:#6a8759;">"age"</span>.getBytes())<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">        </span>Result result= <span style="color:#9876aa;">table</span>.get(get)<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">        </span><span style="color:#808080;">//单行记录
</span><span style="color:#808080;">        </span>Cell cell=result.getColumnLatestCell(<span style="color:#9876aa;">col1</span>.getBytes()<span style="color:#cc7832;">,</span><span style="color:#6a8759;">"age"</span>.getBytes())<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">        </span>System.<span style="color:#9876aa;"><em>out</em></span>.println(<span style="color:#cc7832;">new </span>String(CellUtil.<span style="font-style:italic;">cloneValue</span>(cell)))<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">    </span>}


    <span style="color:#bbb529;">@Test
</span><span style="color:#bbb529;">    </span><span style="color:#cc7832;">public void </span><span style="color:#ffc66d;">listUsers</span>() <span style="color:#cc7832;">throws  </span>Exception{
        <span style="color:#629755;"><em>/**
</em></span><span style="color:#629755;"><em>         * Scan 查询 返回多行数据
</em></span><span style="color:#629755;"><em>         * 尽量不要用全表扫描
</em></span><span style="color:#629755;"><em>         * 1、范围查找  起始rowkey  结束rowkey
</em></span><span style="color:#629755;"><em>         * 2、过滤器 filter  慎重！！
</em></span><span style="color:#629755;"><em>         * </em></span><span style="color:#629755;"><strong><em>@throws </em></strong></span><span style="color:#629755;"><em>Exception
</em></span><span style="color:#629755;"><em>         */
</em></span><span style="color:#629755;"><em>
</em></span><span style="color:#629755;"><em>       </em></span>Scan scan =<span style="color:#cc7832;">new </span>Scan()<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">       </span>scan.setStartRow(<span style="color:#6a8759;">"userId0"</span>.getBytes())<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">        </span>scan.setStopRow(<span style="color:#6a8759;">"userId3"</span>.getBytes())<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">        </span><span style="color:#808080;">// 添加查询条件
</span><span style="color:#808080;">        </span>SingleColumnValueFilter filter1 = <span style="color:#cc7832;">new </span>SingleColumnValueFilter(
                <span style="color:#9876aa;">col1</span>.getBytes()<span style="color:#cc7832;">, </span><span style="color:#6a8759;">"age"</span>.getBytes()<span style="color:#cc7832;">, </span>CompareFilter.CompareOp.<span style="color:#9876aa;"><em>EQUAL</em></span><span style="color:#cc7832;">, </span><span style="color:#6a8759;">"2"</span>.getBytes())<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">        </span>scan.setFilter(filter1)<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">        </span>ResultScanner results= <span style="color:#9876aa;">table</span>.getScanner(scan)<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">        </span>results.forEach(result -&gt; {
            System.<span style="color:#9876aa;"><em>out</em></span>.print(<span style="color:#cc7832;">new </span>String(result.getValue(<span style="color:#9876aa;">col1</span>.getBytes()<span style="color:#cc7832;">,</span><span style="color:#6a8759;">"name"</span>.getBytes()))+<span style="color:#6a8759;">"</span><span style="color:#cc7832;">\t</span><span style="color:#6a8759;">"</span>)<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">            </span>System.<span style="color:#9876aa;"><em>out</em></span>.println(<span style="color:#cc7832;">new </span>String(result.getValue(<span style="color:#9876aa;">col1</span>.getBytes()<span style="color:#cc7832;">,</span><span style="color:#6a8759;">"age"</span>.getBytes())))<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">        </span>})<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">    </span>}

}</pre><p><br></p><p></p><p>hbase与mapperReduce整合,读行数据，统计单词数量</p><p>job类<br></p><pre style="background-color:#2b2b2b;color:#a9b7c6;font-family:Menlo;font-size:9pt;"><span style="color:#cc7832;">public static void </span><span style="color:#ffc66d;">main</span>(String []args) <span style="color:#cc7832;">throws  </span>Exception{
    Configuration conf =<span style="color:#cc7832;">new </span>Configuration()<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">    </span><span style="color:#629755;"><em>/**本地运行*/
</em></span><span style="color:#629755;"><em>    </em></span>conf.set(<span style="color:#6a8759;">"fs.defaultFS"</span><span style="color:#cc7832;">,</span><span style="color:#6a8759;">"hdfs://master:8020"</span>)<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">    </span>conf.set(<span style="color:#6a8759;">"hbase.zookeeper.quorum"</span><span style="color:#cc7832;">, </span><span style="color:#6a8759;">"master,node1,node2"</span>)<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">    </span>Job job =Job.<span style="font-style:italic;">getInstance</span>(conf)<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">    </span>job.setJarByClass(WCJob.<span style="color:#cc7832;">class</span>)<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">    </span><span style="color:#629755;"><em>/**从hbase读取数据设置查询条件*/
</em></span><span style="color:#629755;"><em>    </em></span>Scan scan =<span style="color:#cc7832;">new </span>Scan()<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">    </span>TableMapReduceUtil.<span style="font-style:italic;">initTableMapperJob</span>(<span style="color:#6a8759;">"user"</span><span style="color:#cc7832;">,</span>scan<span style="color:#cc7832;">,</span>WCMapper.<span style="color:#cc7832;">class, </span>Text.<span style="color:#cc7832;">class, </span>IntWritable.<span style="color:#cc7832;">class,</span>job<span style="color:#cc7832;">,false</span>)<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">    </span><span style="color:#629755;"><em>/**
</em></span><span style="color:#629755;"><em>     * 最后一个参数指定为false,因为是本地运行,需要注意
</em></span><span style="color:#629755;"><em>     * */
</em></span><span style="color:#629755;"><em>    </em></span>TableMapReduceUtil.<span style="font-style:italic;">initTableReducerJob</span>(<span style="color:#6a8759;">"user"</span><span style="color:#cc7832;">,</span>WCReducer.<span style="color:#cc7832;">class,</span>job<span style="color:#cc7832;">,null,null,null,null,false</span>)<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">    </span>job.waitForCompletion(<span style="color:#cc7832;">true</span>)<span style="color:#cc7832;">;
</span>}</pre><p>Mapper类，统计单词数量，输出<br></p><pre style="background-color:#2b2b2b;color:#a9b7c6;font-family:Menlo;font-size:9pt;"><span style="color:#cc7832;">public class </span>WCMapper  <span style="color:#cc7832;">extends </span>TableMapper&lt;Text<span style="color:#cc7832;">,</span>IntWritable&gt;{

    <span style="color:#629755;"><em>/**
</em></span><span style="color:#629755;"><em>     * 每次读一行调用一次map方法
</em></span><span style="color:#629755;"><em>     * */
</em></span><span style="color:#629755;"><em>    </em></span><span style="color:#bbb529;">@Override
</span><span style="color:#bbb529;">    </span><span style="color:#cc7832;">protected void </span><span style="color:#ffc66d;">map</span>(ImmutableBytesWritable key<span style="color:#cc7832;">, </span>Result value<span style="color:#cc7832;">, </span>Context context) <span style="color:#cc7832;">throws </span>IOException<span style="color:#cc7832;">, </span>InterruptedException {

        String age =<span style="color:#cc7832;">new </span>String(value.getValue(<span style="color:#6a8759;">"col1"</span>.getBytes()<span style="color:#cc7832;">,</span><span style="color:#6a8759;">"age"</span>.getBytes()))<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">        </span>context.write(<span style="color:#cc7832;">new </span>Text(age)<span style="color:#cc7832;">,new </span>IntWritable(<span style="color:#6897bb;">1</span>))<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">    </span>}
}</pre>reducer类，计算结果，并写入到hbase<br><pre style="background-color:#2b2b2b;color:#a9b7c6;font-family:Menlo;font-size:9pt;"><span style="color:#629755;"><em>/**
</em></span><span style="color:#629755;"><em> * Text,IntWritable 和mapper数据的数据类型一直
</em></span><span style="color:#629755;"><em> * */
</em></span><span style="color:#cc7832;">public class </span>WCReducer <span style="color:#cc7832;">extends </span>TableReducer&lt;Text<span style="color:#cc7832;">,</span>IntWritable<span style="color:#cc7832;">,</span>ImmutableBytesWritable&gt; {

    <span style="color:#bbb529;">@Override
</span><span style="color:#bbb529;">    </span><span style="color:#cc7832;">protected void </span><span style="color:#ffc66d;">reduce</span>(Text key<span style="color:#cc7832;">, </span>Iterable&lt;IntWritable&gt; values<span style="color:#cc7832;">, </span>Context context) <span style="color:#cc7832;">throws </span>IOException<span style="color:#cc7832;">, </span>InterruptedException {
        <span style="color:#cc7832;">int </span>num =<span style="color:#6897bb;">0</span><span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">        for</span>(IntWritable in : values){
            num ++<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">        </span>}

        <span style="color:#629755;"><em>/**以年级为rowkey,写入到hbase**/
</em></span><span style="color:#629755;"><em>
</em></span><span style="color:#629755;"><em>        </em></span>Put put =<span style="color:#cc7832;">new </span>Put(key.getBytes())<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">        </span>put.add(<span style="color:#6a8759;">"col1"</span>.getBytes()<span style="color:#cc7832;">,</span><span style="color:#6a8759;">"count"</span>.getBytes()<span style="color:#cc7832;">,</span>(num+<span style="color:#6a8759;">""</span>).getBytes())<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">        </span>context.write(<span style="color:#cc7832;">null,</span>put)<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">    </span>}
}
</pre><p>结果查看，正确<br><img src="https://img-blog.csdn.net/20180630163930786?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTM0ODc1NDg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></p><br><br>            </div>
                </div>