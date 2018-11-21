---
layout:     post
title:      flume sqoop kafka使用小结
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>1.flume<br>
  flume是高可用的，高可靠的，分布式的海量日志采集、聚合和传输的系统。flume不仅可以采集数据，还可以对数据进行简单的处理。<br>
  flume的文件采集source工具只能识别文本数据。<br>
2.flume-ng<br>
  flume-ng中，flume的集群只有一种角色agent<br>
  agent中有三种组件：source、channel、sink<br>
  source作用是采集数据，它可以是主动的去监听、抓取，可以可以被动的接收和等待<br>
  channel作用是做数据的缓存，当采集数据并发高的时候，有可能source采集数据的速度比sink消费数据的速度要快，因此需要channel来做一个中间缓存，解决这种速度不一致问题<br>
  sink作用是消费channel中的数据，它的消费会保证消费成功只有保证消费成功以后，它才会把这个数据从channel中删除，否则还存在于channel中。<br>
  <br>
  一个agent中可以由多个source、channel、sink<br>
  一个source可以对接多个channel<br>
  一个sink只可以对接一个channel<br>
  <br>
  在source上有两种组件<br>
  选择器（selector）：当1个source对应多个channel时，可以使用选择器来分配source中什么样的数据到达什么样的channel<br>
  拦截器（interceptor）：数据由source到达channel的过程中对数据进行拦截并可以进行处理</p>

<p>3.flume agent的程序开发<br>
  flume采集过程程序的开发其实就是定义配置文件，在配置文件定义好source、channel、sink、selector、interceptor等组件，以及他们之间的关系。<br>
  flume程序是properties格式配置文件，范式如下：<br>
  # list the sources, sinks and channels for the agent<br>
  &lt;Agent&gt;.sources = &lt;Source&gt;<br>
  &lt;Agent&gt;.sinks = &lt;Sink&gt;<br>
  &lt;Agent&gt;.channels = &lt;Channel1&gt; &lt;Channel2&gt;</p>

<p>  # set channel for source<br>
  &lt;Agent&gt;.sources.&lt;Source&gt;.channels = &lt;Channel1&gt; &lt;Channel2&gt; ...</p>

<p>  # set channel for sink<br>
  &lt;Agent&gt;.sinks.&lt;Sink&gt;.channel = &lt;Channel1&gt;<br>
多个之间用空格来间隔<br>
4.flume agent的启动<br>
  bin/flume-ng agent --conf conf --conf-file example.conf --name a1 -Dflume.root.logger=INFO,console<br>
  bin/flume-ng agent -c conf -f example.conf -n a1 -Dflume.root.logger=INFO,console<br>
5.测试常用的source和sink<br>
  avro source被用来做测试，需要flume客户端程序把数据发送过来。<br>
  logger sink直接把获取到的数据打印在控制台<br>
  <br>
6.flume的客户端程序开发<br>
  项目中添加flume依赖<br>
  &lt;dependency&gt;<br>
    &lt;groupId&gt;org.apache.flume&lt;/groupId&gt;<br>
    &lt;artifactId&gt;flume-ng-core&lt;/artifactId&gt;<br>
    &lt;version&gt;1.8.0&lt;/version&gt;<br>
  &lt;/dependency&gt;<br>
  然后<br>
  构建RpcClient，使用RpcClientFactory的方法来构建。<br>
  构建Event对象，使用EventBuilder<br>
  然后调用client的append方法把数据发送给avrosource<br>
7.Event<br>
  event是flume传递数据的最小单元<br>
  event里面包括两部分：header和body<br>
  header里面是一组kv数据，kv的类型都是String，header里面kv的内容可以被selector、interceptor或sink所使用<br>
  body里面是要传递具体的数据内容</p>

<p>写一个flume的客户端程序发送数据到一个flume agent<br>
agent采集到数据之后把数据存储在hdfs上，以文本的方式存储<br>
客户端发送的event的header里面写一个kv，k是timestamp，vlaue是当前时间戳值<br>
8.客户端类型<br>
  Failover Client：解决单个flume节点挂掉，可以选择发送到集群中其他flume节点。<br>
                   给一个client配置多个flume节点，在某个时间点只有一个节点来接受这个clien的数据，当这个节点挂掉之后，客户端会选择其他的节点来进行传输数据</p>

<p>  LoadBalancing RPC client：解决单个节点上数据吞吐量过大达到瓶颈，可以使用LoadBalancing RPC client来进行负载均衡。<br>
  <br>
  在代码实现上需要对client进行配置<br>
  Failover<br>
  写配置文件：clientconfig.properties<br>
  client.type = default_failover #配置客户端类型为default_failover。<br>
  hosts = h1 h2 h3 #给client配置多个flume节点<br>
  #每个节点的hostname和port可以独立设置<br>
  hosts.h1 = host1.example.org:41414<br>
  hosts.h2 = host2.example.org:41414<br>
  hosts.h3 = host3.example.org:41414<br>
  max-attempts = 3 #每个节点传输失败三次被判断为节点失败，开始尝试下一个节点<br>
  <br>
  配置有了之后再实例化client对象的时候就可以使用<br>
  RpcClientFactory.getInstance(File propertiesFile)<br>
  其中参数propertiesFile去加载clientconfig.properties文件<br>
  </p>

<p>  对于配置信息可以写在自定义的配置文件中，也可以直接在代码里构建Properties对象，然后把上述配置参数写在Properties对象里面，然后使用RpcClientFactory.getInstance(Properties properties)方法来构造client对象</p>

<p>  发送数据和普通的client没什么不同</p>

<p>  写一个程序实现Failover类型的客户端，然后启动两个flume agent，达到客户端程序启动后往一个agent中发数据，结束这个agent的进程后，client会自动把数据发送到第二个agent</p>

<p>  LoadBalancing RPC<br>
  client.type = default_loadbalance<br>
  hosts = h1 h2 h3 #给client添加多个flume节点，解决单节点处理流量达到瓶颈问题<br>
  hosts.h1 = host1.example.org:41414<br>
  hosts.h2 = host2.example.org:41414<br>
  hosts.h3 = host3.example.org:41414</p>

<p>  backoff = false <br>
  maxBackoff = 0  # 当多个节点中有一个节点发生故障，这两个参数决定client是否重发已往失败节点上发送的数据，重发多少条</p>

<p>  host-selector = round_robin # 这个决定着客户端以什么标准均衡的把数据发送到多个节点上去。round_robin，random，FQCN（自定义类型）<br>
  </p>

<p>  写一个LoadBalancing RPC客户端，配置两个agent，启动两个agent，客户端均衡的把数据发送到两个agent中<br>
  运行程序，并核对客户端发送的记录条数和两个agent各自收到的记录条数之和  是否一致<br>
  agent把数据以文本的方式保存到hdfs上<br>
9.selector<br>
  每个flume的source都有selector<br>
  selector的类型有两种<br>
  replicating（默认）  ：副本  每个event从source出去会以副本的形式发送到多个channel中，也就是数每个channel中的数据都是全量的<br>
  multiplexing         ：多路  一个event从soucce中出去会选择进入某个channel，也就是说每个channel中的数据是部分，多个channel接受的数据加起来才是source中发送数据的全量。<br>
  <br>
  多路需要有选择条件，这个选择条件需要存放在event的header中，K：条件名称，V：判断值<br>
  a1.sources = r1<br>
  a1.channels = c1 c2 c3 c4<br>
  a1.sources.r1.selector.type = multiplexing<br>
  a1.sources.r1.selector.header = state<br>
  a1.sources.r1.selector.mapping.CZ = c1<br>
  a1.sources.r1.selector.mapping.US = c2 c3<br>
  a1.sources.r1.selector.default = c4<br>
  上述flume的采集过程中，event的header中state key对应的<br>
  value是CZ的会被发送到c1 channel中<br>
  value是US的会被同时发送到c2和c3两个channel中<br>
  value是其余值的会被发送到c4 channel中</p>

<p>  写一个客户端发送的数据event的header加上一个k:province ,v:henan、shandong、hebei、shanghai等<br>
  写一个agent，声明三个channel和三个sink，<br>
  用select把v是henan的数全部保存在hdfs上的一个/flume/henan目录下<br>
          把v是shanghai的数全部保存在hdfs上的一个/flume/shanghai目录下<br>
          v是其他值的全部都保存在/flume/other目录下<br>
10.interceptor<br>
  interceptor是event从source之后和channel之前的一个中间过程，它可以对source发往channel的event进行拦截，并且可以对event进行处理，也可以对event进行过滤。<br>
  在代码上interceptor是添加在source上的：<br>
  a1.sources = r1<br>
  a1.sinks = k1<br>
  a1.channels = c1<br>
  a1.sources.r1.interceptors = i1 i2<br>
  a1.sources.r1.interceptors.i1.type = org.apache.flume.interceptor.HostInterceptor$Builder<br>
  a1.sources.r1.interceptors.i1.preserveExisting = false<br>
  a1.sources.r1.interceptors.i1.hostHeader = hostname<br>
  a1.sources.r1.interceptors.i2.type = org.apache.flume.interceptor.TimestampInterceptor$Builder<br>
  a1.sinks.k1.filePrefix = FlumeData.%{CollectorHost}.%Y-%m-%d<br>
  a1.sinks.k1.channel = c1<br>
i1和i2都是interceptor，它被添加在source r1上，使用a1.sources.r1.interceptors=i1 i2的形式来声明和添加<br>
然后通过<br>
a1.sources.r1.interceptors.i1.params=value 的形式来具体配置</p>

<p>   interceptor常用类型：<br>
   Timestamp Interceptor ：给event的header上添加一个kv，k:timestamp(可修改)，v:当前节点的时间戳<br>
   Host Interceptor      ：给event的header上添加一个kv，k:host(可修改),v:当前节点的ip地址或者hostname<br>
   Static Interceptor    ：给event的header上添加一个kv，k:自己指定，v:自己指定<br>
   Remove Header Interceptor：用来删除event的header上的一个或者多个kv，根据k的值<br>
   UUID Interceptor      ：给event的header上添加一个kv，k:id(可修改)，v:随机产生的uuid<br>
   <br>
   Search and Replace Interceptor：在event的body内容的字符串里，查找某个值，然后替换成指定的值。查找的值可以使用正则表达式匹配，替换的值可以是静态值（自己指定），也可以是匹配值 例如：<br>
   正则表达式：The quick brown ([a-z]+) jumped over the lazy ([a-z]+)<br>
   匹配值中可以使用：$1代表正则中的第一个小括号，$2代表正则表达式中的第二个小括号。<br>
   <br>
   Regex Filtering Interceptor  ： 正则表达式过滤拦截器，先匹配，然后选择匹配上的要被过滤掉还是匹配不上的被过滤掉<br>
   Regex Extractor Interceptor  ： 正则表达式抽取拦截器，先拦截，在匹配，再抽取，抽取的结果更新到原event的body中</p>

<p>   写一个client随机发送模拟的电话号码：一个11位数字的 字符串<br>
   写一个flume，对body中的电话号码进行脱敏处理，如：13592381243   ---》 135xxxx1243<br>
11.自定义拦截器步骤<br>
   1创建项目添加flume依赖：<br>
   &lt;dependency&gt;<br>
        &lt;groupId&gt;org.apache.flume&lt;/groupId&gt;<br>
        &lt;artifactId&gt;flume-ng-core&lt;/artifactId&gt;<br>
        &lt;version&gt;1.7.0&lt;/version&gt;<br>
   &lt;/dependency&gt;<br>
   2创建类型实现org.apache.flume.interceptor.Interceptor接口<br>
   3在实现类中实现<br>
    public Event intercept(Event event)<br>
    public List&lt;Event&gt; intercept(List&lt;Event&gt; events)<br>
    方法的拦截处理逻辑<br>
   4在实现类中定义一个名称为Builder的内部类实现Interceptor.Builder接口<br>
    实现<br>
    public void configure(Context context)<br>
    方法用来接收flume agent配置程序中传来的参数<br>
    实现<br>
    public Interceptor build()<br>
    方法用来完成对自定义的interceptor类的实例化<br>
   5项目打包<br>
    把项目放到flume安装目录的的lib子目录下<br>
   6在flume的agent配置程序中<br>
    interceptor的type参数为自定义类型的全名<br>
    参数结合着Builder类的configure方法来配置。</p>

<p>12.常用source和sink<br>
  source ：Avro Source，Spooling Directory Source，Kafka Source<br>
  sink   ：HDFS Sink，Kafka Sink，Hive Sink，HBaseSinks，ElasticSearchSink，HTTP Sink</p>

<p><br>
  写一个flume的客户端往agent发送数据，数据内容包括人员信息：id、name、age<br>
  在agent中用HBaseSink 把数据保存到hbase的 flume_user表中，表有1个列簇i，列成员对应name、age，id作为rowkey</p>

<p># 如果需要使用正则处理value可以使用以下的serializer<br>
agent.sinks.hbaseSink.serializer= org.apache.flume.sink.hbase.RegexHbaseEventSerializer<br>
# 指定某一列来当主键，而不是用随机生成的key<br>
# agent.sinks.hbaseSink.serializer.rowKeyIndex = 0<br>
# 指定正则表达式,这里用的正则是匹配逗号分隔的字符串<br>
agent.sinks.hbaseSink.serializer.regex=^([^,]+),([^,]+),([^,]+),([^,]+)$<br>
# 指定在列族中对应的的colName<br>
agent.sinks.hbaseSink.serializer.colNames=c1,c2,c3,c4</p>

<p>13.sqoop<br>
  sqoop是hadoop体系下把数据从关系型数据库与大数据体系。存储系统之间相互导入导出的etl工具。它的底层原理，是把指令解析编译成mr，然后发送到yarn下去分布式执行，从而完成把数据在rdbms和hadoop体系下的迁移。<br>
  <br>
  sqoop目前维护的版本：sqoop1和sqoop2<br>
  sqoop1:1.4.6以下的版本<br>
  sqoop2:1.99.x以上的版本<br>
  <br>
14.元数据<br>
   hdfs的metadata存储在namenode的本地文件系统的目录里面，包含fsimage和edits文件<br>
   hive的metadata是可以由用户来配置，存储在类似mysql的关系型数据库里面<br>
   hbase的metadata包含两部分：meta表和root表，其meta表存储在hbase数据库中，root表存储在zookeeper中。<br>
   关系型数据库的metadata，在数据库系统本身的数据字典表中<br>
   sqoop2的metadata是存储在本身配置的关系型数据库中。默认的是derby数据库。<br>
   kafka的metadata存储在zookeeper中<br>
   <br>
15.sqoop1<br>
   $ sqoop tool-name [tool-arguments]</p>

<p>16.使用sqoop1把mysql中的hive数据库下的的TBLS表中的数据导入到hdfs的/sqoop1/hive/tbls目录中<br>
   sqoop import .....(generic-args) (import-args)<br>
   sqoop-import .....(generic-args) (import-args)</p>

<p>sqoop import --connect jdbc:mysql://centos1:3306/hive \<br>
--driver com.mysql.jdbc.Driver \<br>
--username root \<br>
--password sa \<br>
--verbose \<br>
--as-textfile \<br>
--table TBLS \<br>
--target-dir /sqoop1/hive/tbls \<br>
--delete-target-dir</p>

<p>如果是以table的形式导入数据的话，sqoop会默认按照table的primary key在作为分割的条件，把数据根据primary key来划分成若干个区间，每个区间启动一个map任务来抓取数据，并导入到hdfs上。</p>

<p>17.使用sql从mysql中抽取数据导入到hdfs上<br>
sqoop import --connect jdbc:mysql://centos1:3306/hive \<br>
--driver com.mysql.jdbc.Driver \<br>
--username root \<br>
--password sa \<br>
--verbose \<br>
--as-textfile \<br>
--query 'select * from TBLS WHERE $CONDITIONS' \<br>
--split-by TBL_ID \<br>
--target-dir /sqoop1/hive/tbls1 \<br>
--delete-target-dir</p>

<p>使用sql的方式导入数据，对sql上必须要有where，where后面必须要有$CONDITIONS，必须指定split-by，根据什么字段来分割table数据。</p>

<p>如果是带条件的导入：<br>
导入TBLS 的 TBL_TYPE='MANAGED_TABLE'<br>
sqoop import --connect jdbc:mysql://centos1:3306/hive \<br>
--driver com.mysql.jdbc.Driver \<br>
--username root \<br>
--password sa \<br>
--verbose \<br>
--as-textfile \<br>
--query "select * from TBLS WHERE \$CONDITIONS and TBL_TYPE='MANAGED_TABLE'" \<br>
--split-by TBL_ID \<br>
--target-dir /sqoop1/hive/tbls2 \<br>
--delete-target-dir<br>
在使用双引号来括中sql语句的时候$CONDITIONS的$符号前面要加一个转义字符：\$CONDITIONS</p>

<p>18.增量导入<br>
  增量导入，就是对于同一数据按照批次导入的时候，每个批次导入的数据都最新的数据，每个批次导入的数据都是不同条件下的数据，所有批次的数据累计起来就是全量数据。<br>
  1.根据数据特征字段，把这些字段作为条件来完成导入过程<br>
    2018-01-15导入的数据，取数规则上使用：select * from tableA where op_date='2018-01-15'<br>
    2018-01-16导入的数据，取数规则上使用：select * from tableA where op_date='2018-01-16'<br>
  2.设定检查字段<br>
    --check-column 设置检查字段，每次执行导入的时候会自动在导入数据上加条件检查这个字段的值  <br>
    --incremental  增量模式：append、lastmodified。当要导的数据表的检查字段，是递增的，也就是说新的数据记录进入数据库这个字段的值只可能变大不可能变小这种场景可以使用append，结合--check-column和--last-value使用。如果数据不仅仅是新增记录，还有数据改变的情况，这种情况下就不能直接使用append的方式增量导入了，可以在导入的数据上添加一个字段last-modified，每当被更新时，这个字段值需要修改成当前的时间戳，在检查导入的时候，--last-value设置的时间戳和被导入的数据的last-modified进行比较，凡是符合last-modified&gt;last-value的数据会被选中并导入。<br>
    </p>

<p>    事实表一般使用append<br>
    维度表一般使用last-modified</p>

<p>19.sqoop1把数据从mysql导入到hive中<br>
sqoop import --connect jdbc:mysql://centos1:3306/hive \<br>
--driver com.mysql.jdbc.Driver \<br>
--username root \<br>
--password sa \<br>
--verbose \<br>
--as-textfile \<br>
--table TBLS \<br>
--hive-import \<br>
--hive-overwrite \<br>
--create-hive-table \<br>
--hive-table from_sqoop1</p>

<p>20.sqoop1把数据从mysql导入到hbase中<br>
sqoop import --connect jdbc:mysql://centos1:3306/hive \<br>
--driver com.mysql.jdbc.Driver \<br>
--username root \<br>
--password sa \<br>
--verbose \<br>
--as-textfile \<br>
--table TBLS \<br>
--column-family i \<br>
--hbase-row-key TBL_ID \<br>
--hbase-table from_sqoop1</p>

<p>因为版本问题，如果自动在hbase中创建表，会报错：<br>
--hbase-create-table \<br>
21.sqoop1把hdfs上的数据导入到mysql<br>
导出的时候要求rdbms上要已经存在目标表:<br>
create table test.wc(<br>
word varchar(30)<br>
,count int<br>
,primary key(word)<br>
);<br>
sqoop export \<br>
--connect jdbc:mysql://centos1:3306/test \<br>
--driver com.mysql.jdbc.Driver \<br>
--username root \<br>
--password sa \<br>
--verbose \<br>
--export-dir /wctestoutput/part-r-00000 \<br>
--table wc \<br>
--input-fields-terminated-by '\t'</p>

<p>把数据从大数据平台导出到关系型数据库中需要注意update-mode和update-key<br>
--update-mode &lt;mode&gt;<br>
--update-key &lt;col-name&gt;<br>
update-mode是用来指定当数据插入或更新到rdbms数据库时的模式：updateonly和allowinsert<br>
update-key用来指定当sqoop是使用update语句去写rdbms是的where后的限定条件。</p>

<p><br>
22.sqoop2是服务型的架构<br>
  使用sqoop2的时候需要启动sqoop服务<br>
  sqoop2-server start<br>
  <br>
  使用sqoop2-shell连接服务进行操作和查看sqoop2</p>

<p>  sqoop2的元数据：connector、job、link、role等信息，在启动sqoop2-server的目录下会生成一个@BASEDIR@和一个@LOGDIR@，其中@BASEDIR@是元数据（derby的数据库文件），@LOGDIR@是sqoop2-server运行的日志输出。</p>

<p>  sqoop2的组件：connector：代表着sqoop可以连接操作的数据存储系统类型（有FROM和TO）<br>
                link     ：代表一个具体的数据库链接，比方说虚拟机集群下的hdfs连接，或者centos1上的mysql连接<br>
                job      ：代表一个具体的数据导入导出的过程，比方说把TBLS表从hive中导入到hdfs上<br>
  <br>
  sqoop2的开发方式：<br>
    1在shell中 通过create指令创建link<br>
              根据link创建job，job中需要指定 from link1 to link2<br>
              使用start job来启动job完成数据导入过程的执行<br>
    link和job都有自己的名称和id，在执行指令时通过name或id来制定要操作或使用的具体的link或job</p>

<p>    <br>
    2使用api来连接sqoop2的服务，然后通过api方法远程调用sqoop2的rpc服务完成link的创建，job的创建和job的启动，来完成sqoop2数据迁移的执行过程。</p>

<p>23.sqoop2的配置参数<br>
   http://sqoop.apache.org/docs/1.99.7/user.html<br>
   不同的connector对应的link和job（from、to）都有不同的配置参数<br>
   Link Configuration<br>
   FROM Job Configuration<br>
   TO Job Configuration<br>
   <br>
   创建link的时候参照Link Configuration<br>
   创建from job的时候，比方说有一个job的from link是generic-jdbc-connector类型的，就需要查看generic-jdbc-connector的FROM Job Configuration<br>
   创建to job，的时候，比方说，这个job是把数据导出到jdbc数据库中，就需要查看generic-jdbc-connector的TO Job Configuration</p>

<p>24.sqoop2 java api开发过程<br>
   创建项目，添加sqoop依赖：<br>
   &lt;dependency&gt;<br>
       &lt;groupId&gt;org.apache.sqoop&lt;/groupId&gt;<br>
       &lt;artifactId&gt;sqoop-client&lt;/artifactId&gt;<br>
       &lt;version&gt;${requestedVersion}&lt;/version&gt;<br>
   &lt;/dependency&gt;<br>
   1构建sqoop的客户端对象SqoopClient类型，参数sqoop2-server的url，如："http://centos1:12000/sqoop/"<br>
   2通过client.createLink创建link对象，使用client.saveLink方法来保存link到sqoop2-server中<br>
   3通过client.createJob方法创建job对象，对job配置完成后，使用client.saveJob来把job保存到sqoop2-server中<br>
   4通过client.startJob方法来启动sqoop2-server中已有的job，执行sqoop2的数据迁移构成<br>
注：<br>
在java api的MJob、MLink的配置参数文档中是没有的，可以使用api的<br>
MLinkConfig linkConfig = link.getConnectorLinkConfig();<br>
MFromConfig fromJobConfig = job.getFromJobConfig();<br>
MToConfig toJobConfig = job.getToJobConfig();<br>
方法来获取link、from job、to job的配置参数<br>
遍历这三个对象获取到MInput，每一个MInput代表一个配置参数，通过对配置参数的设置，完成对job和link的配置。</p>

<p>  1.把mysql中的TBLS使用sqoop2 shell指令的方式导入的hdfs /sqoop2/tbls<br>
  2.把hdfs上的wordcount结果迁移到mysql test数据库下的wc2表中，使用java api的方式来完成<br>
25.sqoop2在运行job时需要hadoop启动jobhistory server<br>
  sqoop2需要调用jobhistory server来判断每个job的执行任务和状态的<br>
  如果不启动jobhistory server，在sqoop2里已启动的job不能被终结，也不能删除，不能重新启动</p>

<p>26.kafka架构<br>
   broker，一个kafka的服务节点被称作一个broker，分布式下的kafka是由多个broker动态的形成的一个消息队列集群。<br>
   broker节点是一个逻辑的概念，一个broker节点其实就是一个java进程，因此一个物理服务器上可以运行多个broker节点。<br>
   多个broker之间，通过zookeeper来形成一个集群对外提供统一的消息队列服务。<br>
27.kafka的安装配置<br>
   每一个broker都有一个server.properties配置文件，在启动broker的时候来指定配置文件的路径。<br>
   broker.id 每个broker节点在集群中拥有唯一的brokerid，集群中的每一个broker，id不能重复<br>
   listeners      broker是一个服务，它对外提供服务需要占用网络端口，同一个物理节点上运行的多个broker服务，端口不能重复<br>
   log.dirs   目录下保存的该broker存储的消息内容，同一个物理节点上的运行的多个broker服务，log目录不能重复<br>
   zookeeper.connect 默认是localhost:2181，可以配置整个zookeeper的各个节点的zookeeper服务端口 centos1:2181,centos2:2181,centos3:2181</p>

<p>   kafka把内存中的数据强制flush到磁盘上的触发条件<br>
   log.flush.interval.messages<br>
   log.flush.interval.ms<br>
   kafka会把消息以文件的形式保存在每个broker的log.dirs目录下面，但kafka并不是永久保存数据的，它里面可以通过参数配置来触发删除过程来删除过期的数据<br>
   log.retention.hours    时间触发参数<br>
   log.retention.bytes    log文件尺寸触发参数<br>
   log.retention.check.interval.ms 删除条件是否满足检查参数</p>

<p>   配置kafka中的topic是否可直接删除<br>
   delete.topic.enable<br>
28.kafka的数据模型<br>
   topic kafka数据分类的模式名称，类似于数据库中的表table的概念<br>
   partition 一个topic可以划分为多个partition，每个partition分布在不同的broker节点上。数据在写入topic的时候，可以严格保证单个paritition有序。<br>
   replication 每一个partition都有多个replication，kafka使用副本形式来维护它的容错功能。多个副本在某一个时间点只有一个是leader角色，而其他的是flower角色，对外提供读写服务的只有leader，flower会实时的根leader之间进行数据同步。当leader所在的节点故障，zookeeper会从flower中选举一个来接替leader继续对外提供读写服务。</p>

<p>   读写模型<br>
   producer 生产者，负责把消息传到kafka的topic上，默认会均衡的分配到多个parition上。producer还可以指定某个partition来把所有消息都发送到这个partition上。<br>
   consumer 消费者，可以从kafka的topic中读取消息来进行数据处理。一个consumer会从一个topic中的多个partition中顺序读取数据，consumer也可以指定某个partiton，只从这一个partition中消费消息记录。<br>
   consumer group 消费组，kafka支持分布式消费，同一个topic可以同时由多个consumer来进行并行消费，消费组可以保证一个组下多个consumer消费同一个topic中的数据时不会重复消费。<br>
   offset 在kafka的topic中数据被消费的时候，每一个topic的每一个partition对应的每一个消费组都会记录一个offset，kafka就是使用offset来保证一个组下的多个消费者数据不会消费重复。offset(topic,partition,groupname)。每当一条记录被消费者消费之后kafka会根据消费者的commit行为来更新这个offset。offset的数据是保存在kafka里面名称为__consumer_offsets的topic里面的。<br>
   kafka的消费者还可以指定起始的offset来进行消费。</p>

<p>   kafka的元数据信息，保存在zookeeper上。topic、topic的partition、partition的replication<br>
   <br>
29.常用指令<br>
   启动kafka<br>
   kafka-server-start.sh config/server.properties &amp;</p>

<p>   topic操作指令（创建，删除，查看，修改）<br>
   kafka-topics.sh<br>
   增<br>
   kafka-topics.sh --zookeeper centos1:2181 --create --partitions 2 --replication-factor 2 --topic bd19first<br>
   查<br>
   kafka-topics.sh --zookeeper centos1:2181 --list<br>
   kafka-topics.sh --zookeeper centos1:2181 --describe --topic bd19first<br>
   改（修改topic的partition数量，replication数量，和topic的configuration参数）<br>
   kafka-topics.sh --zookeeper centos1:2181 --alter --topic bd19first --partitions 3<br>
   删<br>
   kafka-topics.sh --zookeeper centos1:2181 --delete --topic bd19first</p>

<p>   控制台生产者和消费者通常用于测试和调试<br>
   kafka-console-producer.sh --broker-list centos1:9092,centos2:9092,centos1:9093 --topic bd19first <br>
   kafka-console-consumer.sh --bootstrap-server centos1:9092,centos2:9092,centos3:9092 --topic bd19first<br>
   kafka-console-consumer.sh --bootstrap-server centos1:9092,centos2:9092,centos3:9092 --topic bd19first --partition 0 --offset 2</p>

<p>kafka-server-start.sh server.properties &amp;<br>
kafka-server-start.sh server1.properties &amp;</p>

<p>kafka-server-start.sh server2.properties &amp;<br>
kafka-server-start.sh server3.properties &amp;</p>

<p>kafka-server-start.sh server4.properties &amp;<br>
kafka-server-start.sh server5.properties &amp;</p>

<p>30.kafka的api使用<br>
  首先创建maven项目，添加kafka依赖<br>
  &lt;dependency&gt;<br>
    &lt;groupId&gt;org.apache.kafka&lt;/groupId&gt;<br>
    &lt;artifactId&gt;kafka-clients&lt;/artifactId&gt;<br>
    &lt;version&gt;0.10.1.0&lt;/version&gt;<br>
  &lt;/dependency&gt;<br>
  &lt;dependency&gt;<br>
    &lt;groupId&gt;org.slf4j&lt;/groupId&gt;<br>
    &lt;artifactId&gt;slf4j-log4j12&lt;/artifactId&gt;<br>
    &lt;version&gt;1.7.22&lt;/version&gt;<br>
  &lt;/dependency&gt;<br>
  然后根据producer的api文档和consumer的api文档来创建客户端程序</p>

<p>  kafka是一个消息队列系统，它传递的数据是封装在message里面的，message分成两部分，k和v<br>
  <br>
  producer<br>
  构建连接kafka服务的配置对象Properties后者Map<br>
  构建一个Producer对象，使用KafkaProducer&lt;K,V&gt;<br>
  partitionsFor(String topic)获取topic下面的partition信息<br>
  使用send(ProducerRecord&lt;K,V&gt; record)方法发送message到kafka服务端<br>
  message对象的类型是ProducerRecord&lt;K,V&gt;<br>
  flush()把producer客户端内存中的数据全部flush到kafka服务器上</p>

<p>  consumer<br>
  构建消费者的properties配置对象<br>
  构建一个Consumer对象，使用KafkaConsumer&lt;K,V&gt;类型<br>
  assign(Collection&lt;TopicPartition&gt; partitions)用来指定该consumer消费哪些topic的哪些partition<br>
  beginningOffsets(Collection&lt;TopicPartition&gt; partitions)获取本次消费给定参数中的partition上的offset值<br>
  <br>
  commitAsync()<br>
  commitAsync(Map&lt;TopicPartition,OffsetAndMetadata&gt; offsets, OffsetCommitCallback callback)<br>
  commitAsync(OffsetCommitCallback callback)<br>
  commitSync()<br>
  commitSync(Map&lt;TopicPartition,OffsetAndMetadata&gt; offsets)<br>
  提交offset，如果要使用上述五个方法手动提交offset的需要再properties里面设置enable.auto.commit为false。<br>
  <br>
  获取上一次提交的某partition下的offset值<br>
  committed(TopicPartition partition)</p>

<p>  手动指定消费的起始offset值：<br>
  seek(TopicPartition partition, long offset)<br>
  seekToBeginning(Collection&lt;TopicPartition&gt; partitions)<br>
  seekToEnd(Collection&lt;TopicPartition&gt; partitions)</p>

<p>  订阅topic<br>
  subscribe(Collection&lt;String&gt; topics)<br>
  取出要消费的数据<br>
  poll(long timeout)</p>

<p><br>
linux上新建一个/usr/tmp/spool的目录，每当有新的文本文件被拷贝到这个目录下时-------》flume spooldirsource 监听这个目录-------》使用kafkasink------》kafka topic：log-----》java consumer读取log topic中的数据并且控制台打印出来</p>

<p>sqoop import --connect jdbc:mysql://centos1:3306/hive --username root --password sa --table TBLS --target-dir /sqoop1test/aa --as-textfile</p>

<p> </p>            </div>
                </div>