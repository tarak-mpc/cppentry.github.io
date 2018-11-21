---
layout:     post
title:      mac 安装kafaka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h2 id="toc_0">brew 安装kafka</h2>

<p>Last login: Fri Aug 11 08:57:04 on console<br>
helendeMacBook-Pro:~ helen$ brew install kafka<br>
Updating Homebrew...<br><sup>C==&gt;</sup> Installing dependencies for kafka: zookeeper<br>
==&gt; Installing kafka dependency: zookeeper<br>
==&gt; Downloading <a href="https://homebrew.bintray.com/bottles/zookeeper-3.4.10.sierra.bot" rel="nofollow">https://homebrew.bintray.com/bottles/zookeeper-3.4.10.sierra.bot</a><br>
curl: (35) Server aborted the SSL handshake<br>
Error: Failed to download resource "zookeeper"<br>
Download failed: <a href="https://homebrew.bintray.com/bottles/zookeeper-3.4.10.sierra.bottle.tar.gz" rel="nofollow">https://homebrew.bintray.com/bottles/zookeeper-3.4.10.sierra.bottle.tar.gz</a><br>
Warning: Bottle installation failed: building from source.<br>
==&gt; Using the sandbox<br>
==&gt; Downloading <a href="https://www.apache.org/dyn/closer.cgi?path=zookeeper/zookeeper-3" rel="nofollow">https://www.apache.org/dyn/closer.cgi?path=zookeeper/zookeeper-3</a><br>
==&gt; Best Mirror <a href="http://mirrors.tuna.tsinghua.edu.cn/apache/zookeeper/zookeeper-3" rel="nofollow">http://mirrors.tuna.tsinghua.edu.cn/apache/zookeeper/zookeeper-3</a><br>
100.0%<br>
==&gt; ./configure --prefix=/usr/local/Cellar/zookeeper/3.4.10 --without-cppunit<br>
==&gt; make install<br>
==&gt; Caveats<br>
To have launchd start zookeeper now and restart at login:<br>
  brew services start zookeeper<br>
Or, if you don't want/need a background service you can just run:<br>
 ** zkServer start**<br>
==&gt; Summary<br>
/usr/local/Cellar/zookeeper/3.4.10: 237 files, 31.4MB, built in 49 seconds<br>
==&gt; Installing kafka<br>
==&gt; Downloading <a href="https://homebrew.bintray.com/bottles/kafka-0.11.0.0.sierra.bottle.tar.gz" rel="nofollow">https://homebrew.bintray.com/bottles/kafka-0.11.0.0.sierra.bottle.tar.gz</a><br>
100.0%<br>
==&gt; Pouring kafka-0.11.0.0.sierra.bottle.tar.gz<br>
==&gt; Caveats<br>
To have launchd start kafka now and restart at login:<br>
  brew services start kafka<br>
Or, if you don't want/need a background service you can just run:<br>
  zookeeper-server-start /usr/local/etc/kafka/zookeeper.properties &amp; kafka-server-start /usr/local/etc/kafka/server.properties<br>
==&gt; Summary<br>
 /usr/local/Cellar/kafka/0.11.0.0: 149 files, 35.4MB</p>

<hr><h2 id="toc_1">启动服务</h2>

<p>helendeMacBook-Pro:~ helen\( zkServer start<br>
ZooKeeper JMX enabled by default<br>
Using config: /usr/local/etc/zookeeper/zoo.cfg<br>
Starting zookeeper ... STARTED<br>
helendeMacBook-Pro:~ helen\) ps -ef|grep zookeeper<br>
  501 18353     1   0 11:09下午 ttys000    0:00.40 java -Dzookeeper.log.dir=. -Dzookeeper.root.logger=INFO,CONSOLE -cp /usr/local/Cellar/zookeeper/3.4.10/libexec/bin/../build/classes:/usr/local/Cellar/zookeeper/3.4.10/libexec/bin/../build/lib/<em>.jar:/usr/local/Cellar/zookeeper/3.4.10/libexec/bin/../lib/slf4j-log4j12-1.6.1.jar:/usr/local/Cellar/zookeeper/3.4.10/libexec/bin/../lib/slf4j-api-1.6.1.jar:/usr/local/Cellar/zookeeper/3.4.10/libexec/bin/../lib/netty-3.10.5.Final.jar:/usr/local/Cellar/zookeeper/3.4.10/libexec/bin/../lib/log4j-1.2.16.jar:/usr/local/Cellar/zookeeper/3.4.10/libexec/bin/../lib/jline-0.9.94.jar:/usr/local/Cellar/zookeeper/3.4.10/libexec/bin/../zookeeper-3.4.10.jar:/usr/local/Cellar/zookeeper/3.4.10/libexec/bin/../src/java/lib/</em>.jar:/usr/local/etc/zookeeper: -Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.local.only=false org.apache.zookeeper.server.quorum.QuorumPeerMain /usr/local/etc/zookeeper/zoo.cfg<br>
  501 18358 14185   0 11:09下午 ttys000    0:00.00 grep zookeeper<br>
helendeMacBook-Pro:~ helen\( zookeeper-server-start /usr/local/etc/kafka/zookeeper.properties &amp; kafka-server-start /usr/local/etc/kafka/server.properties<br>
[1] 18378<br>
[2017-08-13 23:11:15,449] INFO Reading configuration from: /usr/local/etc/kafka/zookeeper.properties (org.apache.zookeeper.server.quorum.QuorumPeerConfig)<br>
[2017-08-13 23:11:15,452] INFO autopurge.snapRetainCount set to 3 (org.apache.zookeeper.server.DatadirCleanupManager)<br>
[2017-08-13 23:11:15,452] INFO autopurge.purgeInterval set to 0 (org.apache.zookeeper.server.DatadirCleanupManager)<br>
[2017-08-13 23:11:15,452] INFO Purge task is not scheduled. (org.apache.zookeeper.server.DatadirCleanupManager)<br>
[2017-08-13 23:11:15,452] WARN Either no config or no quorum defined in config, running  in standalone mode (org.apache.zookeeper.server.quorum.QuorumPeerMain)<br>
[2017-08-13 23:11:15,466] INFO Reading configuration from: /usr/local/etc/kafka/zookeeper.properties (org.apache.zookeeper.server.quorum.QuorumPeerConfig)<br>
[2017-08-13 23:11:15,467] INFO Starting server (org.apache.zookeeper.server.ZooKeeperServerMain)<br>
[2017-08-13 23:11:15,476] INFO Server environment:zookeeper.version=3.4.10-39d3a4f269333c922ed3db283be479f9deacaa0f, built on 03/23/2017 10:13 GMT (org.apache.zookeeper.server.ZooKeeperServer)<br>
[2017-08-13 23:11:15,476] INFO Server environment:host.name=192.168.31.193 (org.apache.zookeeper.server.ZooKeeperServer)<br>
[2017-08-13 23:11:15,476] INFO Server environment:java.version=1.8.0_121 (org.apache.zookeeper.server.ZooKeeperServer)<br>
[2017-08-13 23:11:15,476] INFO Server environment:java.vendor=Oracle Corporation (org.apache.zookeeper.server.ZooKeeperServer)<br>
[2017-08-13 23:11:15,477] INFO Server environment:java.home=/Library/Java/JavaVirtualMachines/jdk1.8.0_121.jdk/Contents/Home/jre (org.apache.zookeeper.server.ZooKeeperServer)<br>
[2017-08-13 23:11:15,477] INFO Server environment:java.class.path=:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/aopalliance-repackaged-2.5.0-b05.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/argparse4j-0.7.0.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/commons-lang3-3.5.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/connect-api-0.11.0.0.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/connect-file-0.11.0.0.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/connect-json-0.11.0.0.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/connect-runtime-0.11.0.0.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/connect-transforms-0.11.0.0.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/guava-20.0.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/hk2-api-2.5.0-b05.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/hk2-locator-2.5.0-b05.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/hk2-utils-2.5.0-b05.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/jackson-annotations-2.8.5.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/jackson-core-2.8.5.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/jackson-databind-2.8.5.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/jackson-jaxrs-base-2.8.5.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/jackson-jaxrs-json-provider-2.8.5.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/jackson-module-jaxb-annotations-2.8.5.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/javassist-3.21.0-GA.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/javax.annotation-api-1.2.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/javax.inject-1.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/javax.inject-2.5.0-b05.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/javax.servlet-api-3.1.0.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/javax.ws.rs-api-2.0.1.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/jersey-client-2.24.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/jersey-common-2.24.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/jersey-container-servlet-2.24.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/jersey-container-servlet-core-2.24.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/jersey-guava-2.24.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/jersey-media-jaxb-2.24.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/jersey-server-2.24.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/jetty-continuation-9.2.15.v20160210.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/jetty-http-9.2.15.v20160210.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/jetty-io-9.2.15.v20160210.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/jetty-security-9.2.15.v20160210.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/jetty-server-9.2.15.v20160210.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/jetty-servlet-9.2.15.v20160210.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/jetty-servlets-9.2.15.v20160210.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/jetty-util-9.2.15.v20160210.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/jopt-simple-5.0.3.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/kafka-clients-0.11.0.0.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/kafka-log4j-appender-0.11.0.0.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/kafka-streams-0.11.0.0.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/kafka-streams-examples-0.11.0.0.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/kafka-tools-0.11.0.0.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/kafka_2.12-0.11.0.0-sources.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/kafka_2.12-0.11.0.0-test-sources.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/kafka_2.12-0.11.0.0.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/log4j-1.2.17.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/lz4-1.3.0.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/maven-artifact-3.5.0.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/metrics-core-2.2.0.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/osgi-resource-locator-1.0.1.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/plexus-utils-3.0.24.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/reflections-0.9.11.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/rocksdbjni-5.0.1.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/scala-library-2.12.2.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/scala-parser-combinators_2.12-1.0.4.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/slf4j-api-1.7.25.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/slf4j-log4j12-1.7.25.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/snappy-java-1.1.2.6.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/validation-api-1.1.0.Final.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/zkclient-0.10.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/zookeeper-3.4.10.jar (org.apache.zookeeper.server.ZooKeeperServer)<br>
[2017-08-13 23:11:15,478] INFO Server environment:java.library.path=/Users/helen/Library/Java/Extensions:/Library/Java/Extensions:/Network/Library/Java/Extensions:/System/Library/Java/Extensions:/usr/lib/java:. (org.apache.zookeeper.server.ZooKeeperServer)<br>
[2017-08-13 23:11:15,478] INFO Server environment:java.io.tmpdir=/var/folders/4v/r5z1scwx72s354x2bshfybwr0000gn/T/ (org.apache.zookeeper.server.ZooKeeperServer)<br>
[2017-08-13 23:11:15,478] INFO Server environment:java.compiler=&lt;NA&gt; (org.apache.zookeeper.server.ZooKeeperServer)<br>
[2017-08-13 23:11:15,478] INFO Server environment:os.name=Mac OS X (org.apache.zookeeper.server.ZooKeeperServer)<br>
[2017-08-13 23:11:15,478] INFO Server environment:os.arch=x86_64 (org.apache.zookeeper.server.ZooKeeperServer)<br>
[2017-08-13 23:11:15,478] INFO Server environment:os.version=10.12.3 (org.apache.zookeeper.server.ZooKeeperServer)<br>
[2017-08-13 23:11:15,478] INFO Server environment:user.name=helen (org.apache.zookeeper.server.ZooKeeperServer)<br>
[2017-08-13 23:11:15,478] INFO Server environment:user.home=/Users/helen (org.apache.zookeeper.server.ZooKeeperServer)<br>
[2017-08-13 23:11:15,478] INFO Server environment:user.dir=/Users/helen (org.apache.zookeeper.server.ZooKeeperServer)<br>
[2017-08-13 23:11:15,486] INFO tickTime set to 3000 (org.apache.zookeeper.server.ZooKeeperServer)<br>
[2017-08-13 23:11:15,486] INFO minSessionTimeout set to -1 (org.apache.zookeeper.server.ZooKeeperServer)<br>
[2017-08-13 23:11:15,487] INFO maxSessionTimeout set to -1 (org.apache.zookeeper.server.ZooKeeperServer)<br>
[2017-08-13 23:11:15,509] INFO binding to port 0.0.0.0/0.0.0.0:2181 (org.apache.zookeeper.server.NIOServerCnxnFactory)<br>
[2017-08-13 23:11:15,510] ERROR Unexpected exception, exiting abnormally (org.apache.zookeeper.server.ZooKeeperServerMain)<br>
java.net.BindException: Address already in use<br>
    at sun.nio.ch.Net.bind0(Native Method)<br>
    at sun.nio.ch.Net.bind(Net.java:433)<br>
    at sun.nio.ch.Net.bind(Net.java:425)<br>
    at sun.nio.ch.ServerSocketChannelImpl.bind(ServerSocketChannelImpl.java:223)<br>
    at sun.nio.ch.ServerSocketAdaptor.bind(ServerSocketAdaptor.java:74)<br>
    at sun.nio.ch.ServerSocketAdaptor.bind(ServerSocketAdaptor.java:67)<br>
    at org.apache.zookeeper.server.NIOServerCnxnFactory.configure(NIOServerCnxnFactory.java:90)<br>
    at org.apache.zookeeper.server.ZooKeeperServerMain.runFromConfig(ZooKeeperServerMain.java:117)<br>
    at org.apache.zookeeper.server.ZooKeeperServerMain.initializeAndRun(ZooKeeperServerMain.java:87)<br>
    at org.apache.zookeeper.server.ZooKeeperServerMain.main(ZooKeeperServerMain.java:53)<br>
    at org.apache.zookeeper.server.quorum.QuorumPeerMain.initializeAndRun(QuorumPeerMain.java:116)<br>
    at org.apache.zookeeper.server.quorum.QuorumPeerMain.main(QuorumPeerMain.java:78)<br>
[2017-08-13 23:11:15,956] INFO KafkaConfig values: <br>
    advertised.host.name = null<br>
    advertised.listeners = null<br>
    advertised.port = null<br>
    alter.config.policy.class.name = null<br>
    authorizer.class.name = <br>
    auto.create.topics.enable = true<br>
    auto.leader.rebalance.enable = true<br>
    background.threads = 10<br>
    broker.id = 0<br>
    broker.id.generation.enable = true<br>
    broker.rack = null<br>
    compression.type = producer<br>
    connections.max.idle.ms = 600000<br>
    controlled.shutdown.enable = true<br>
    controlled.shutdown.max.retries = 3<br>
    controlled.shutdown.retry.backoff.ms = 5000<br>
    controller.socket.timeout.ms = 30000<br>
    create.topic.policy.class.name = null<br>
    default.replication.factor = 1<br>
    delete.records.purgatory.purge.interval.requests = 1<br>
    delete.topic.enable = false<br>
    fetch.purgatory.purge.interval.requests = 1000<br>
    group.initial.rebalance.delay.ms = 0<br>
    group.max.session.timeout.ms = 300000<br>
    group.min.session.timeout.ms = 6000<br>
    host.name = <br>
    inter.broker.listener.name = null<br>
    inter.broker.protocol.version = 0.11.0-IV2<br>
    leader.imbalance.check.interval.seconds = 300<br>
    leader.imbalance.per.broker.percentage = 10<br>
    listener.security.protocol.map = SSL:SSL,SASL_PLAINTEXT:SASL_PLAINTEXT,TRACE:TRACE,SASL_SSL:SASL_SSL,PLAINTEXT:PLAINTEXT<br>
    listeners = null<br>
    log.cleaner.backoff.ms = 15000<br>
    log.cleaner.dedupe.buffer.size = 134217728<br>
    log.cleaner.delete.retention.ms = 86400000<br>
    log.cleaner.enable = true<br>
    log.cleaner.io.buffer.load.factor = 0.9<br>
    log.cleaner.io.buffer.size = 524288<br>
    log.cleaner.io.max.bytes.per.second = 1.7976931348623157E308<br>
    log.cleaner.min.cleanable.ratio = 0.5<br>
    log.cleaner.min.compaction.lag.ms = 0<br>
    log.cleaner.threads = 1<br>
    log.cleanup.policy = [delete]<br>
    log.dir = /tmp/kafka-logs<br>
    log.dirs = /usr/local/var/lib/kafka-logs<br>
    log.flush.interval.messages = 9223372036854775807<br>
    log.flush.interval.ms = null<br>
    log.flush.offset.checkpoint.interval.ms = 60000<br>
    log.flush.scheduler.interval.ms = 9223372036854775807<br>
    log.flush.start.offset.checkpoint.interval.ms = 60000<br>
    log.index.interval.bytes = 4096<br>
    log.index.size.max.bytes = 10485760<br>
    log.message.format.version = 0.11.0-IV2<br>
    log.message.timestamp.difference.max.ms = 9223372036854775807<br>
    log.message.timestamp.type = CreateTime<br>
    log.preallocate = false<br>
    log.retention.bytes = -1<br>
    log.retention.check.interval.ms = 300000<br>
    log.retention.hours = 168<br>
    log.retention.minutes = null<br>
    log.retention.ms = null<br>
    log.roll.hours = 168<br>
    log.roll.jitter.hours = 0<br>
    log.roll.jitter.ms = null<br>
    log.roll.ms = null<br>
    log.segment.bytes = 1073741824<br>
    log.segment.delete.delay.ms = 60000<br>
    max.connections.per.ip = 2147483647<br>
    max.connections.per.ip.overrides = <br>
    message.max.bytes = 1000012<br>
    metric.reporters = []<br>
    metrics.num.samples = 2<br>
    metrics.recording.level = INFO<br>
    metrics.sample.window.ms = 30000<br>
    min.insync.replicas = 1<br>
    num.io.threads = 8<br>
    num.network.threads = 3<br>
    num.partitions = 1<br>
    num.recovery.threads.per.data.dir = 1<br>
    num.replica.fetchers = 1<br>
    offset.metadata.max.bytes = 4096<br>
    offsets.commit.required.acks = -1<br>
    offsets.commit.timeout.ms = 5000<br>
    offsets.load.buffer.size = 5242880<br>
    offsets.retention.check.interval.ms = 600000<br>
    offsets.retention.minutes = 1440<br>
    offsets.topic.compression.codec = 0<br>
    offsets.topic.num.partitions = 50<br>
    offsets.topic.replication.factor = 1<br>
    offsets.topic.segment.bytes = 104857600<br>
    port = 9092<br>
    principal.builder.class = class org.apache.kafka.common.security.auth.DefaultPrincipalBuilder<br>
    producer.purgatory.purge.interval.requests = 1000<br>
    queued.max.requests = 500<br>
    quota.consumer.default = 9223372036854775807<br>
    quota.producer.default = 9223372036854775807<br>
    quota.window.num = 11<br>
    quota.window.size.seconds = 1<br>
    replica.fetch.backoff.ms = 1000<br>
    replica.fetch.max.bytes = 1048576<br>
    replica.fetch.min.bytes = 1<br>
    replica.fetch.response.max.bytes = 10485760<br>
    replica.fetch.wait.max.ms = 500<br>
    replica.high.watermark.checkpoint.interval.ms = 5000<br>
    replica.lag.time.max.ms = 10000<br>
    replica.socket.receive.buffer.bytes = 65536<br>
    replica.socket.timeout.ms = 30000<br>
    replication.quota.window.num = 11<br>
    replication.quota.window.size.seconds = 1<br>
    request.timeout.ms = 30000<br>
    reserved.broker.max.id = 1000<br>
    sasl.enabled.mechanisms = [GSSAPI]<br>
    sasl.kerberos.kinit.cmd = /usr/bin/kinit<br>
    sasl.kerberos.min.time.before.relogin = 60000<br>
    sasl.kerberos.principal.to.local.rules = [DEFAULT]<br>
    sasl.kerberos.service.name = null<br>
    sasl.kerberos.ticket.renew.jitter = 0.05<br>
    sasl.kerberos.ticket.renew.window.factor = 0.8<br>
    sasl.mechanism.inter.broker.protocol = GSSAPI<br>
    security.inter.broker.protocol = PLAINTEXT<br>
    socket.receive.buffer.bytes = 102400<br>
    socket.request.max.bytes = 104857600<br>
    socket.send.buffer.bytes = 102400<br>
    ssl.cipher.suites = null<br>
    ssl.client.auth = none<br>
    ssl.enabled.protocols = [TLSv1.2, TLSv1.1, TLSv1]<br>
    ssl.endpoint.identification.algorithm = null<br>
    ssl.key.password = null<br>
    ssl.keymanager.algorithm = SunX509<br>
    ssl.keystore.location = null<br>
    ssl.keystore.password = null<br>
    ssl.keystore.type = JKS<br>
    ssl.protocol = TLS<br>
    ssl.provider = null<br>
    ssl.secure.random.implementation = null<br>
    ssl.trustmanager.algorithm = PKIX<br>
    ssl.truststore.location = null<br>
    ssl.truststore.password = null<br>
    ssl.truststore.type = JKS<br>
    transaction.abort.timed.out.transaction.cleanup.interval.ms = 60000<br>
    transaction.max.timeout.ms = 900000<br>
    transaction.remove.expired.transaction.cleanup.interval.ms = 3600000<br>
    transaction.state.log.load.buffer.size = 5242880<br>
    transaction.state.log.min.isr = 1<br>
    transaction.state.log.num.partitions = 50<br>
    transaction.state.log.replication.factor = 1<br>
    transaction.state.log.segment.bytes = 104857600<br>
    transactional.id.expiration.ms = 604800000<br>
    unclean.leader.election.enable = false<br>
    zookeeper.connect = localhost:2181<br>
    zookeeper.connection.timeout.ms = 6000<br>
    zookeeper.session.timeout.ms = 6000<br>
    zookeeper.set.acl = false<br>
    zookeeper.sync.time.ms = 2000<br>
 (kafka.server.KafkaConfig)<br>
[2017-08-13 23:11:16,021] INFO starting (kafka.server.KafkaServer)<br>
[2017-08-13 23:11:16,023] INFO Connecting to zookeeper on localhost:2181 (kafka.server.KafkaServer)<br>
[2017-08-13 23:11:16,035] INFO Starting ZkClient event thread. (org.I0Itec.zkclient.ZkEventThread)<br>
[2017-08-13 23:11:16,040] INFO Client environment:zookeeper.version=3.4.10-39d3a4f269333c922ed3db283be479f9deacaa0f, built on 03/23/2017 10:13 GMT (org.apache.zookeeper.ZooKeeper)<br>
[2017-08-13 23:11:16,040] INFO Client environment:host.name=192.168.31.193 (org.apache.zookeeper.ZooKeeper)<br>
[2017-08-13 23:11:16,040] INFO Client environment:java.version=1.8.0_121 (org.apache.zookeeper.ZooKeeper)<br>
[2017-08-13 23:11:16,040] INFO Client environment:java.vendor=Oracle Corporation (org.apache.zookeeper.ZooKeeper)<br>
[2017-08-13 23:11:16,040] INFO Client environment:java.home=/Library/Java/JavaVirtualMachines/jdk1.8.0_121.jdk/Contents/Home/jre (org.apache.zookeeper.ZooKeeper)<br>
[2017-08-13 23:11:16,040] INFO Client environment:java.class.path=:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/aopalliance-repackaged-2.5.0-b05.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/argparse4j-0.7.0.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/commons-lang3-3.5.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/connect-api-0.11.0.0.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/connect-file-0.11.0.0.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/connect-json-0.11.0.0.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/connect-runtime-0.11.0.0.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/connect-transforms-0.11.0.0.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/guava-20.0.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/hk2-api-2.5.0-b05.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/hk2-locator-2.5.0-b05.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/hk2-utils-2.5.0-b05.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/jackson-annotations-2.8.5.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/jackson-core-2.8.5.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/jackson-databind-2.8.5.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/jackson-jaxrs-base-2.8.5.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/jackson-jaxrs-json-provider-2.8.5.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/jackson-module-jaxb-annotations-2.8.5.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/javassist-3.21.0-GA.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/javax.annotation-api-1.2.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/javax.inject-1.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/javax.inject-2.5.0-b05.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/javax.servlet-api-3.1.0.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/javax.ws.rs-api-2.0.1.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/jersey-client-2.24.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/jersey-common-2.24.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/jersey-container-servlet-2.24.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/jersey-container-servlet-core-2.24.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/jersey-guava-2.24.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/jersey-media-jaxb-2.24.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/jersey-server-2.24.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/jetty-continuation-9.2.15.v20160210.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/jetty-http-9.2.15.v20160210.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/jetty-io-9.2.15.v20160210.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/jetty-security-9.2.15.v20160210.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/jetty-server-9.2.15.v20160210.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/jetty-servlet-9.2.15.v20160210.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/jetty-servlets-9.2.15.v20160210.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/jetty-util-9.2.15.v20160210.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/jopt-simple-5.0.3.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/kafka-clients-0.11.0.0.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/kafka-log4j-appender-0.11.0.0.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/kafka-streams-0.11.0.0.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/kafka-streams-examples-0.11.0.0.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/kafka-tools-0.11.0.0.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/kafka_2.12-0.11.0.0-sources.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/kafka_2.12-0.11.0.0-test-sources.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/kafka_2.12-0.11.0.0.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/log4j-1.2.17.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/lz4-1.3.0.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/maven-artifact-3.5.0.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/metrics-core-2.2.0.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/osgi-resource-locator-1.0.1.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/plexus-utils-3.0.24.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/reflections-0.9.11.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/rocksdbjni-5.0.1.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/scala-library-2.12.2.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/scala-parser-combinators_2.12-1.0.4.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/slf4j-api-1.7.25.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/slf4j-log4j12-1.7.25.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/snappy-java-1.1.2.6.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/validation-api-1.1.0.Final.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/zkclient-0.10.jar:/usr/local/Cellar/kafka/0.11.0.0/libexec/bin/../libs/zookeeper-3.4.10.jar (org.apache.zookeeper.ZooKeeper)<br>
[2017-08-13 23:11:16,041] INFO Client environment:java.library.path=/Users/helen/Library/Java/Extensions:/Library/Java/Extensions:/Network/Library/Java/Extensions:/System/Library/Java/Extensions:/usr/lib/java:. (org.apache.zookeeper.ZooKeeper)<br>
[2017-08-13 23:11:16,041] INFO Client environment:java.io.tmpdir=/var/folders/4v/r5z1scwx72s354x2bshfybwr0000gn/T/ (org.apache.zookeeper.ZooKeeper)<br>
[2017-08-13 23:11:16,041] INFO Client environment:java.compiler=&lt;NA&gt; (org.apache.zookeeper.ZooKeeper)<br>
[2017-08-13 23:11:16,041] INFO Client environment:os.name=Mac OS X (org.apache.zookeeper.ZooKeeper)<br>
[2017-08-13 23:11:16,041] INFO Client environment:os.arch=x86_64 (org.apache.zookeeper.ZooKeeper)<br>
[2017-08-13 23:11:16,041] INFO Client environment:os.version=10.12.3 (org.apache.zookeeper.ZooKeeper)<br>
[2017-08-13 23:11:16,041] INFO Client environment:user.name=helen (org.apache.zookeeper.ZooKeeper)<br>
[2017-08-13 23:11:16,041] INFO Client environment:user.home=/Users/helen (org.apache.zookeeper.ZooKeeper)<br>
[2017-08-13 23:11:16,041] INFO Client environment:user.dir=/Users/helen (org.apache.zookeeper.ZooKeeper)<br>
[2017-08-13 23:11:16,042] INFO Initiating client connection, connectString=localhost:2181 sessionTimeout=6000 watcher=org.I0Itec.zkclient.ZkClient@2ea227af (org.apache.zookeeper.ZooKeeper)<br>
[2017-08-13 23:11:16,053] INFO Waiting for keeper state SyncConnected (org.I0Itec.zkclient.ZkClient)<br>
[2017-08-13 23:11:16,055] INFO Opening socket connection to server localhost/127.0.0.1:2181. Will not attempt to authenticate using SASL (unknown error) (org.apache.zookeeper.ClientCnxn)<br>
[2017-08-13 23:11:16,070] INFO Socket connection established to localhost/127.0.0.1:2181, initiating session (org.apache.zookeeper.ClientCnxn)<br>
[2017-08-13 23:11:16,100] INFO Session establishment complete on server localhost/127.0.0.1:2181, sessionid = 0x15ddc24810d0000, negotiated timeout = 6000 (org.apache.zookeeper.ClientCnxn)<br>
[2017-08-13 23:11:16,102] INFO zookeeper state changed (SyncConnected) (org.I0Itec.zkclient.ZkClient)<br>
[2017-08-13 23:11:16,188] INFO Cluster ID = BRq2gXYzSVCboWSMyZstcg (kafka.server.KafkaServer)<br>
[2017-08-13 23:11:16,190] WARN No meta.properties file under dir /usr/local/var/lib/kafka-logs/meta.properties (kafka.server.BrokerMetadataCheckpoint)<br>
[2017-08-13 23:11:16,230] INFO [ThrottledRequestReaper-Fetch]: Starting (kafka.server.ClientQuotaManager\)ThrottledRequestReaper)<br>
[2017-08-13 23:11:16,231] INFO [ThrottledRequestReaper-Produce]: Starting (kafka.server.ClientQuotaManager\(ThrottledRequestReaper)<br>
[2017-08-13 23:11:16,232] INFO [ThrottledRequestReaper-Request]: Starting (kafka.server.ClientQuotaManager\)ThrottledRequestReaper)<br>
[2017-08-13 23:11:16,263] INFO Log directory '/usr/local/var/lib/kafka-logs' not found, creating it. (kafka.log.LogManager)<br>
[2017-08-13 23:11:16,273] INFO Loading logs. (kafka.log.LogManager)<br>
[2017-08-13 23:11:16,282] INFO Logs loading complete in 9 ms. (kafka.log.LogManager)<br>
[2017-08-13 23:11:16,365] INFO Starting log cleanup with a period of 300000 ms. (kafka.log.LogManager)<br>
[2017-08-13 23:11:16,369] INFO Starting log flusher with a default period of 9223372036854775807 ms. (kafka.log.LogManager)<br>
[2017-08-13 23:11:16,414] INFO Awaiting socket connections on 0.0.0.0:9092. (kafka.network.Acceptor)<br>
[2017-08-13 23:11:16,416] INFO [Socket Server on Broker 0], Started 1 acceptor threads (kafka.network.SocketServer)<br>
[2017-08-13 23:11:16,435] INFO [ExpirationReaper-0-Produce]: Starting (kafka.server.DelayedOperationPurgatory\(ExpiredOperationReaper)<br>
[2017-08-13 23:11:16,436] INFO [ExpirationReaper-0-Fetch]: Starting (kafka.server.DelayedOperationPurgatory\)ExpiredOperationReaper)<br>
[2017-08-13 23:11:16,437] INFO [ExpirationReaper-0-DeleteRecords]: Starting (kafka.server.DelayedOperationPurgatory\(ExpiredOperationReaper)<br>
[2017-08-13 23:11:16,502] INFO [ExpirationReaper-0-topic]: Starting (kafka.server.DelayedOperationPurgatory\)ExpiredOperationReaper)<br>
[2017-08-13 23:11:16,506] INFO Creating /controller (is it secure? false) (kafka.utils.ZKCheckedEphemeral)<br>
[2017-08-13 23:11:16,506] INFO [ExpirationReaper-0-Heartbeat]: Starting (kafka.server.DelayedOperationPurgatory\(ExpiredOperationReaper)<br>
[2017-08-13 23:11:16,507] INFO [ExpirationReaper-0-Rebalance]: Starting (kafka.server.DelayedOperationPurgatory\)ExpiredOperationReaper)<br>
[2017-08-13 23:11:16,513] INFO Result of znode creation is: OK (kafka.utils.ZKCheckedEphemeral)<br>
[2017-08-13 23:11:16,524] INFO [GroupCoordinator 0]: Starting up. (kafka.coordinator.group.GroupCoordinator)<br>
[2017-08-13 23:11:16,525] INFO [GroupCoordinator 0]: Startup complete. (kafka.coordinator.group.GroupCoordinator)<br>
[2017-08-13 23:11:16,529] INFO [Group Metadata Manager on Broker 0]: Removed 0 expired offsets in 4 milliseconds. (kafka.coordinator.group.GroupMetadataManager)<br>
[2017-08-13 23:11:16,542] INFO [ProducerId Manager 0]: Acquired new producerId block (brokerId:0,blockStartProducerId:0,blockEndProducerId:999) by writing to Zk with path version 1 (kafka.coordinator.transaction.ProducerIdManager)<br>
[2017-08-13 23:11:16,567] INFO [Transaction Coordinator 0]: Starting up. (kafka.coordinator.transaction.TransactionCoordinator)<br>
[2017-08-13 23:11:16,568] INFO [Transaction Marker Channel Manager 0]: Starting (kafka.coordinator.transaction.TransactionMarkerChannelManager)<br>
[2017-08-13 23:11:16,568] INFO [Transaction Coordinator 0]: Startup complete. (kafka.coordinator.transaction.TransactionCoordinator)<br>
[2017-08-13 23:11:16,598] INFO Will not load MX4J, mx4j-tools.jar is not in the classpath (kafka.utils.Mx4jLoader$)<br>
[2017-08-13 23:11:16,620] INFO Creating /brokers/ids/0 (is it secure? false) (kafka.utils.ZKCheckedEphemeral)<br>
[2017-08-13 23:11:16,623] INFO Result of znode creation is: OK (kafka.utils.ZKCheckedEphemeral)<br>
[2017-08-13 23:11:16,624] INFO Registered broker 0 at path /brokers/ids/0 with addresses: EndPoint(192.168.31.193,9092,ListenerName(PLAINTEXT),PLAINTEXT) (kafka.utils.ZkUtils)<br>
[2017-08-13 23:11:16,624] WARN No meta.properties file under dir /usr/local/var/lib/kafka-logs/meta.properties (kafka.server.BrokerMetadataCheckpoint)<br>
[2017-08-13 23:11:16,634] INFO Kafka version : 0.11.0.0 (org.apache.kafka.common.utils.AppInfoParser)<br>
[2017-08-13 23:11:16,634] INFO Kafka commitId : cb8625948210849f (org.apache.kafka.common.utils.AppInfoParser)<br>
[2017-08-13 23:11:16,635] INFO [Kafka Server 0], started (kafka.server.KafkaServer)</p>

<hr><h2 id="toc_2">发布和订阅消息</h2>

<p><img src="https://img-blog.csdn.net/20170813234217564" alt="屏幕快照 2017-08-13 下午11.40.43">￼</p>
            </div>
                </div>