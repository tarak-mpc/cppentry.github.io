---
layout:     post
title:      Kafka监控系统Kafka Eagle剖析
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h2>2.1 背景</h2>

<p>在使用Kafka Eagle监控系统之前，笔者简单的介绍一下这款工具的用途。Kafka Eagle监控系统是一款用来监控Kafka集群的工具，目前更新的版本是v1.2.3，支持管理多个Kafka集群、管理Kafka主题（包含查看、删除、创建等）、消费者组合消费者实例监控、消息阻塞告警、Kafka集群健康状态查看等。目前Kafka Eagle v1.2.3整个系统所包含的功能，这里笔者给绘制成了一个图，结果如下图所示：</p>

<p><img alt="" class="has" src="https://images2018.cnblogs.com/blog/666745/201807/666745-20180726155027971-1664616298.png"></p>

<h2>2.2 安装</h2>

<p>接下来，我们开始安装Kafka Eagle系统，安装之前，我们需要准备好Kafka Eagle安装包。这里有2种方式：</p>

<ul><li>下载编译好的安装包</li>
	<li>下载源代码，然后自行编译安装</li>
</ul><p>下面分别介绍这2种方式。</p>

<h3>2.2.1 直接下载安装包</h3>

<p> 可以直接访问Kafka Eagle安装包下载地址：<a href="http://download.smartloli.org/" rel="nofollow">http://download.smartloli.org/</a>,然后点击下载按钮，等待下载完成即可。下载界面如下图所示：</p>

<p><img alt="" class="has" src="https://images2018.cnblogs.com/blog/666745/201807/666745-20180726234757083-1491281273.png"></p>

<p> </p>

<h3> 2.2.2 下载源代码，自行编译安装</h3>

<p>Kafka Eagle系统的源代码托管在Github上，大家可以访问<a href="https://github.com/smartloli/kafka-eagle" rel="nofollow">https://github.com/smartloli/kafka-eagle</a>来获取源代码。Kafka Eagle源代码是由Maven工程来管理的，所以，在编译Kafka Eagle源代码之前，需要在本地开发环境中准备好你的Maven环境。</p>

<p>Maven安装比较简单，这里给大家介绍安装Maven的步骤：</p>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<pre>
# 步骤1：下载Maven安装包
wget http://mirrors.tuna.tsinghua.edu.cn/apache/maven/maven-3/3.5.4/binaries/apache-maven-3.5.4-bin.tar.gz

# 步骤2：解压Maven安装包
tar -zxvf apache-maven-3.5.4-bin.tar.gz

# 步骤3：重命名并移动到指定位置
mv apache-maven-3.5.4 /usr/local/maven

# 步骤4：配置Maven环境编辑
vi ~/.bash_profile

export M2_HOME=/usr/local/maven
export PATH=$PATH:$M2_HOME/bin

# 步骤5：执行source命令让环境变量立即生效
source ~/.bash_profile

# 步骤6：验证Maven是否安装成功
mvn -v

# 如果能打印Maven版本信息，则安装成功</pre>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<p>然后，进入到Kafka Eagle源代码根目录，在根目录中有一个build.sh的脚本，执行该脚本进行源代码编译。编译成功后，控制台会打印相关信息，如下图所示：</p>

<p><img alt="" class="has" src="https://images2018.cnblogs.com/blog/666745/201807/666745-20180727000107937-1283479031.png"></p>

<h3>2.2.3 配置Kafka Eagle</h3>

<p>准备好Kafka Eagle安装包后，接下来我们就可以进行安装了。其实，Kafka Eagle的安装是很简单的，当初设计这个系统就是遵循简单、易用的原则来的。但是，很多同学在安装的过程当中却遇到了很多各式各样的问题。其实，在官方使用手册的安装一节中，介绍的也很详细。官方使用手册地址：<a href="http://ke.smartloli.org/" rel="nofollow">http://ke.smartloli.org/</a> 文档托管在Gitbook，这里需要注意的是，可能有些同学反馈说访问不了，如果网络有波动，偶尔可能需要使用代理来访问。</p>

<p>接下来，我们就开始配置Kafka Eagle系统，步骤如下：</p>

<p>1. 配置JAVA_HOME和KE_HOME</p>

<p>由于源代码核心实现采用的是Java语言，所以需要配置JDK环境，建议采用JDK8以上。配置内容如下：</p>

<pre>
vi ~/.bash_profile
export JAVA_HOME=/usr/local/jdk8
export KE_HOME=/data/soft/new/kafka-eagle

export PATH=$PATH:$JAVA_HOME/bin:$KE_HOME/bin</pre>

<p>然后，执行source ~/.bash_profile命令让命令立即生效。如果不配置环境变量，可能在启动Kafka Eagle脚本ke.sh时抛出如下异常，异常信息如下：</p>

<pre>
[2018-07-26 18:41:51] Error: The KE_HOME environment variable is not defined correctly.
[2018-07-26 18:41:51] Error: This environment variable is needed to run this program.

[2018-07-26 18:41:51] Error: The JAVA_HOME environment variable is not defined correctly.
[2018-07-26 18:41:51] Error: This environment variable is needed to run this program.</pre>

<p>2. 配置system-config.properties文件</p>

<p>该文件在$KE_HOME/conf/目录，配置内容如下：</p>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<pre>
######################################
# 配置多个Kafka集群所对应的Zookeeper
######################################
kafka.eagle.zk.cluster.alias=cluster1,cluster2
cluster1.zk.list=dn1:2181,dn2:2181,dn3:2181
cluster2.zk.list=tdn1:2181,tdn2:2181,tdn3:2181

######################################
# 设置Zookeeper线程数
######################################
kafka.zk.limit.size=25

######################################
# 设置Kafka Eagle浏览器访问端口
######################################
kafka.eagle.webui.port=8048

######################################
# 如果你的offsets存储在Kafka中，这里就配置
# 属性值为kafka，如果是在Zookeeper中，可以
# 注释该属性。一般情况下，Offsets的也和你消
# 费者API有关系，如果你使用的Kafka版本为0.10.x
# 以后的版本，但是，你的消费API使用的是0.8.2.x
# 时的API，此时消费者依然是在Zookeeper中
######################################
kafka.eagle.offset.storage=kafka

######################################
# 是否启动监控图表，默认是不启动的
######################################
kafka.eagle.metrics.charts=false

######################################
# 在使用Kafka SQL查询主题时，如果遇到错误，
# 可以尝试开启这个属性，默认情况下，不开启
######################################
kafka.eagle.sql.fix.error=false

######################################
# 邮件服务器设置，用来告警
######################################
kafka.eagle.mail.enable=false
kafka.eagle.mail.sa=
kafka.eagle.mail.username=
kafka.eagle.mail.password=
kafka.eagle.mail.server.host=
kafka.eagle.mail.server.port=

######################################
# 设置告警用户，多个用户以英文逗号分隔
######################################
kafka.eagle.alert.users=smartloli.org@gmail.com


######################################
# 超级管理员删除主题的Token
######################################
kafka.eagle.topic.token=keadmin

######################################
# 如果启动Kafka SASL协议，开启该属性
######################################
kafka.eagle.sasl.enable=false
kafka.eagle.sasl.protocol=SASL_PLAINTEXT
kafka.eagle.sasl.mechanism=PLAIN

######################################
# Kafka Eagle默认存储在Sqlite中，如果要使用
# MySQL可以替换驱动、用户名、密码、连接地址
######################################
#kafka.eagle.driver=com.mysql.jdbc.Driver
#kafka.eagle.url=jdbc:mysql://127.0.0.1:3306/ke?useUnicode=true&amp;characterEncoding=UTF-8&amp;zeroDateTimeBehavior=convertToNull
#kafka.eagle.username=root
#kafka.eagle.password=123456

kafka.eagle.driver=org.sqlite.JDBC
kafka.eagle.url=jdbc:sqlite:/Users/dengjie/workspace/kafka-egale/db/ke.db
kafka.eagle.username=root
kafka.eagle.password=root</pre>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<p>3. 启动Kafka Eagle</p>

<p>配置完成后，可以执行Kafka Eagle脚本ke.sh。如果首次执行，需要给该脚本赋予执行权限，命令如下：</p>

<pre>
chmod +x $KE_HOME/bin/ke.sh</pre>

<p>在ke.sh脚本中，支持以下命令：</p>

<table border="0"><tbody><tr><td>命令</td>
			<td>说明</td>
		</tr><tr><td>ke.sh start</td>
			<td>启动Kafka Eagle系统</td>
		</tr><tr><td>ke.sh stop</td>
			<td>停止Kafka Eagle系统</td>
		</tr><tr><td>ke.sh restart</td>
			<td>重启Kafka Eagle系统</td>
		</tr><tr><td>ke.sh status</td>
			<td>查看Kafka Eagle系统运行状态</td>
		</tr><tr><td>ke.sh stats</td>
			<td>统计Kafka Eagle系统占用Linux资源情况</td>
		</tr><tr><td>ke.sh find [ClassName]</td>
			<td>查看Kafka Eagle系统中的类是否存在</td>
		</tr></tbody></table><h1>3.预览</h1>

<p>在反馈出现频率最多的问题，就是Consumer模块没有数据展示、趋势监控图没有数据、Kafka SQL查询Topic没有数据。下面围绕这3个问题来给大家演示，以及解释什么情况下出现这种情况。</p>

<h2>3.1 Consumer模块展示</h2>

<p>启动一个消费者程序，然后进入到Consumer模块，截图如下：</p>

<p><img alt="" class="has" src="https://images2018.cnblogs.com/blog/666745/201807/666745-20180727003501567-792685122.png"></p>

<p><img alt="" class="has" src="https://images2018.cnblogs.com/blog/666745/201807/666745-20180727003557494-955457367.png"></p>

<p>这里需要注意的时，Kafka在0.10.x之后的版本和之前的版本底层设计有了变化，在之前的版本消费者信息是存储在Zookeeper中的，在0.10.x版本之后，默认存储到了Kafka内部主题中，只保留了元数据信息存储在Zookeeper中，例如：Kafka Broker地址、Topic名称、分区等信息。</p>

<p>是不是我使用的是Kafka 0.10.x之后的版本（如0.10.0、1.0.x、1.x等），然后配置属性kafka.eagle.offset.storage=kafka，启动消费者，就可以看到消费者信息呢？不一定的，还有一个关键因素决定Kafka Eagle系统是否可以展示你消费者程序信息，那就是消费者API的使用。</p>

<p>如果你使用的Kafka 0.10.x之后的版本，然后消费者API也是使用的最新的写法，那么自然你的消费者信息会被记录到Kafka内部主题中，那么此时你设置kafka.eagle.offset.storage=kafka这个属性，Kafka Eagle系统可以完美展示你的消费者使用情况。</p>

<p>但是，如果你虽然使用的是Kafka 0.10.x之后的版本，但是你使用的消费者API还是0.8.2.x或是0.9.x时的写法，此时的消费者信息是会被记录到Zookeeper中进行存储，那么此时你需要设置kafka.eagle.offset.storage=zookeeper或者注释掉该属性，在访问Kafka Eagle系统就可以查看到你的消费者详情了。</p>

<h2>3.2. 监控趋势图</h2>

<p> Kafka系统默认是没有开启JMX端口的，所以Kafka Eagle的监控趋势图默认采用不启用的方式，即kafka.eagle.metrics.charts=false。如果需要查看监控趋势图，需要开启Kafka系统的JMX端口，设置该端口在$KAFKA_HOME/bin/kafka-server-start.sh脚本中，设置内容如下：</p>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<pre>
vi kafka-server-start.sh

if [ "x$KAFKA_HEAP_OPTS" = "x" ]; then
    export KAFKA_HEAP_OPTS="-server -Xms2G -Xmx2G -XX:PermSize=128m -XX:+UseG1GC -XX:MaxGCPauseMillis=200 -XX:ParallelGCThreads=8 -XX:ConcGCThreads=5 -XX:InitiatingHeapOccupancyPercent=70"
    export JMX_PORT="9999"
    #export KAFKA_HEAP_OPTS="-Xmx1G -Xms1G"
fi</pre>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<p>需要注意的时，这里的端口不一定非要设置成9999，端口只有可用，均可。Kafka Eagle系统会自动获取这个JMX端口，采集结果如下：</p>

<p><img alt="" class="has" src="https://images2018.cnblogs.com/blog/666745/201807/666745-20180727004948478-1062696178.png"></p>

<h2>3.3 Kafka SQL查询Topic</h2>

<p>还有一部分同学在Kafka Eagle系统的SQL查询Topic时，会出现查询不到数据的情况。这里查询不到数据可能情况有多种，首先需要排除Kafka集群因素，确保Kafka集群运行正常，Topic能够正常访问，并且Topic中是有数据的。</p>

<p>在排除一些主观因素后，回到Kafka Eagle系统应用层面，如果出现这种问题，可以尝试开启属性kafka.eagle.sql.fix.error=true，这个属性默认是不开启的。正常情况下使用Kafka SQL查询Topic，返回结果如下图所示：</p>

<p><img alt="" class="has" src="https://images2018.cnblogs.com/blog/666745/201807/666745-20180727005710817-1885036162.png"></p>

<p> </p>

<p> <img alt="" class="has" src="https://images2018.cnblogs.com/blog/666745/201807/666745-20180727005735726-1157971488.png"></p>

<h1>4.总结</h1>

<p> 另外，如果在使用Kafka Eagle系统中遇到其他问题，可以查看$KE_HOME/logs/ke_console.out日志来分析具体的异常信息，一般都会提示具体的错误，大家可以根据错误提示来进行解决。</p>

<h3>作者：哥不是小萝莉 </h3>

<h3>出处：<a href="http://www.cnblogs.com/smartloli/" rel="nofollow">http://www.cnblogs.com/smartloli/</a></h3>

<p><a href="http://www.tjyy120.com/" rel="nofollow"><span style="color:#ffffff;">郑州妇科医院那个好</span> </a></p>

<p><a href="http://www.zzchanghong110.com/" rel="nofollow"><span style="color:#ffffff;">郑州专业男科医院</span> </a></p>

<p><a href="http://jbk.39.net/yiyuanzaixian/sysdfkyy/" rel="nofollow"><span style="color:#ffffff;">郑州妇科医院</span> </a></p>

<p><a href="http://yiyuan.120ask.com/zzfck/" rel="nofollow"><span style="color:#ffffff;">郑州较权威较专业排名较好的妇科医院</span></a></p>            </div>
                </div>