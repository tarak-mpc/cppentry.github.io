---
layout:     post
title:      利用ycsb测试hbase性能
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
java 、maven、ycsb 的安装及配置见这篇博客：<br><a href="http://blog.csdn.net/hs794502825/article/details/17309845" rel="nofollow">http://blog.csdn.net/hs794502825/article/details/17309845</a><br><br>
本篇博客主要介绍 hbase 的安装，以及利用 ycsb 对 hbase 进行基本的测试<br><br>
步骤一：<br>
在<a href="http://mirrors.hust.edu.cn/apache/hbase/" rel="nofollow">http://mirrors.hust.edu.cn/apache/hbase/</a>下载hbase稳定版：<br>
hbase-0.94.14.tar.gz 存放于 /home/hs/program 目录下<br>
cd /home/hs/program<br>
tar -zxvf hbase-0.94.14.tar.gz<br><br><br>
步骤二：<br>
修改/home/hs/program/YCSB/hbase目录下的pom.xml文件（根据你所使用的hbase版本和hadoop版本，注意红色部分）：<br>
&lt;?xml version="1.0" encoding="UTF-8"?&gt;<br>
&lt;project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd"&gt;<br>
  &lt;modelVersion&gt;4.0.0&lt;/modelVersion&gt;<br>
  &lt;parent&gt;<br>
    &lt;groupId&gt;com.yahoo.ycsb&lt;/groupId&gt;<br>
    &lt;artifactId&gt;root&lt;/artifactId&gt;<br>
    &lt;version&gt;0.1.4&lt;/version&gt;<br>
  &lt;/parent&gt;<br><br><br>
  &lt;artifactId&gt;hbase-binding&lt;/artifactId&gt;<br>
  &lt;name&gt;HBase DB Binding&lt;/name&gt;<br><br><br>
  &lt;dependencies&gt;<br>
    &lt;dependency&gt;<br>
      &lt;groupId&gt;org.apache.hbase&lt;/groupId&gt;<br>
      &lt;artifactId&gt;hbase&lt;/artifactId&gt;<br>
      <strong><span style="color:#ff0000;">&lt;!--&lt;version&gt;${hbase.version}&lt;/version&gt;--&gt;<br>
      &lt;version&gt;0.94.14&lt;/version&gt;</span></strong><br>
    &lt;/dependency&gt;<br>
    &lt;dependency&gt;<br>
      &lt;groupId&gt;org.apache.hadoop&lt;/groupId&gt;<br>
      &lt;artifactId&gt;hadoop-core&lt;/artifactId&gt;<br>
      <strong><span style="color:#ff0000;">&lt;!--&lt;version&gt;1.0.0&lt;/version&gt;--&gt;<br>
      &lt;version&gt;1.0.4&lt;/version&gt;</span></strong><br>
    &lt;/dependency&gt;<br>
    &lt;dependency&gt;<br>
      &lt;groupId&gt;com.yahoo.ycsb&lt;/groupId&gt;<br>
      &lt;artifactId&gt;core&lt;/artifactId&gt;<br>
      &lt;version&gt;${project.version}&lt;/version&gt;<br>
    &lt;/dependency&gt;<br>
  &lt;/dependencies&gt;<br><br><br>
  &lt;build&gt;<br>
    &lt;plugins&gt;<br>
      &lt;plugin&gt;<br>
        &lt;groupId&gt;org.apache.maven.plugins&lt;/groupId&gt;<br>
        &lt;artifactId&gt;maven-assembly-plugin&lt;/artifactId&gt;<br>
        &lt;version&gt;${maven.assembly.version}&lt;/version&gt;<br>
        &lt;configuration&gt;<br>
          &lt;descriptorRefs&gt;<br>
            &lt;descriptorRef&gt;jar-with-dependencies&lt;/descriptorRef&gt;<br>
          &lt;/descriptorRefs&gt;<br>
          &lt;appendAssemblyId&gt;false&lt;/appendAssemblyId&gt;<br>
        &lt;/configuration&gt;<br>
        &lt;executions&gt;<br>
          &lt;execution&gt;<br>
            &lt;phase&gt;package&lt;/phase&gt;<br>
            &lt;goals&gt;<br>
              &lt;goal&gt;single&lt;/goal&gt;<br>
            &lt;/goals&gt;<br>
          &lt;/execution&gt;<br>
        &lt;/executions&gt;<br>
      &lt;/plugin&gt;<br>
    &lt;/plugins&gt;<br>
  &lt;/build&gt;<br><br><br>
&lt;/project&gt;<br><br><br>
步骤三：<br>
因为更改了步骤二中的文件，所以需要重新编译YCSB项目<br>
cd /home/hs/program/YCSB<br>
mvn clean package | tee mvn-clean-package.dat //将输出同时输出到屏幕和文件<br>
显示与下类似信息，则说明编译成功：<br>
[INFO] Reactor Summary:<br>
[INFO] ------------------------------------------------------------------------<br>
[INFO] YCSB Root ............................................. SUCCESS [7.540s]<br>
[INFO] Core YCSB ............................................. SUCCESS [13.293s]<br>
[INFO] Cassandra DB Binding .................................. SUCCESS [15.861s]<br>
[INFO] HBase DB Binding ...................................... SUCCESS [2:21.346s]<br>
[INFO] Hypertable DB Binding ................................. SUCCESS [5.296s]<br>
[INFO] DynamoDB DB Binding ................................... SUCCESS [7.347s]<br>
[INFO] ElasticSearch Binding ................................. SUCCESS [18.140s]<br>
[INFO] JDBC DB Binding ....................................... SUCCESS [5.495s]<br>
[INFO] Mapkeeper DB Binding .................................. SUCCESS [4.054s]<br>
[INFO] Mongo DB Binding ...................................... SUCCESS [3.368s]<br>
[INFO] OrientDB Binding ...................................... SUCCESS [4.199s]<br>
[INFO] Redis DB Binding ...................................... SUCCESS [3.261s]<br>
[INFO] Voldemort DB Binding .................................. SUCCESS [4.113s]<br>
[INFO] YCSB Release Distribution Builder ..................... SUCCESS [13.764s]<br>
[INFO] ------------------------------------------------------------------------<br>
[INFO] ------------------------------------------------------------------------<br>
[INFO] BUILD SUCCESSFUL<br>
[INFO] ------------------------------------------------------------------------<br>
[INFO] Total time: 4 minutes 8 seconds<br>
[INFO] Finished at: Mon Dec 16 12:43:12 CST 2013<br>
[INFO] Final Memory: 94M/227M<br><br>
步骤四：<br>
成功编译YCSB之后，在/home/hs/program/YCSB/distribution/target目录下会有ycsb-0.1.4.tar.gz<br>
将该文件解压到/home/hs/program目录下：<br>
tar -zxvf /home/hs/program/YCSB/distribution/target -C /home/hs/program<br><br>
步骤五：<br>
将/home/hs/program/hbase-0.94.14/lib中的以下文件：<br>
slf4j-api-*.jar<br>
slf4j-log4j12-*.jar<br>
zookeeper-*.jar<br>
拷贝到/home/hs/program/ycsb-0.1.4/hbase-binding/lib中<br><br>
步骤六：<br>
使用/home/hs/program/hbase-0.94.14/conf中的hbase-site.xml<br>
替换掉/home/hs/program/ycsb-0.1.4/hbase-binding/conf中的hbase-site.xml<br><br>
步骤七：<br>
执行测试<br>
终端1：<br>
进入hbase的安装目录：<br>
执行：./bin/start-hbase.sh<br>
显示：<br>
starting master, logging to /home/hs/program/hbase-0.94.14/bin/../logs/hbase-hs-master-hs-virtual-machine.out<br><br>
接着执行：<br>
./bin/hbase shell<br>
create 'usertable','cf'<br><br>
终端2中：<br>
cd /home/hs/program/ycsb-0.1.4<br><br>
hs@hs-virtual-machine:~/program/ycsb-0.1.4$ ./bin/ycsb load hbase -P workloads/workloada -p hosts=localhost -p columnfamily=cf &gt; ./my-results/load-hbase-a<br><br>
hs@hs-virtual-machine:~/program/ycsb-0.1.4$ ./bin/ycsb run hbase -P workloads/workloada -p hosts=localhost -p columnfamily=cf &gt; ./my-results/run-hbase-a<br><br>
得到如下结果：<br>
YCSB Client 0.1<br>
Command line: -db com.yahoo.ycsb.db.HBaseClient -P workloads/workloada -p hosts=localhost -p columnfamily=cf -t<br>
[OVERALL], RunTime(ms), 5886.0<br>
[OVERALL], Throughput(ops/sec), 169.89466530750934<br>
[UPDATE], Operations, 487<br>
[UPDATE], AverageLatency(us), 9366.180698151951<br>
[UPDATE], MinLatency(us), 33<br>
[UPDATE], MaxLatency(us), 4459758<br>
[UPDATE], 95thPercentileLatency(ms), 0<br>
[UPDATE], 99thPercentileLatency(ms), 0<br>
[UPDATE], Return=0, 486<br>
......<br>
[READ], Operations, 514<br>
[READ], AverageLatency(us), 2482.817120622568<br>
[READ], MinLatency(us), 707<br>
[READ], MaxLatency(us), 176125<br>
[READ], 95thPercentileLatency(ms), 2<br>
[READ], 99thPercentileLatency(ms), 29<br>
[READ], Return=0, 514
            </div>
                </div>