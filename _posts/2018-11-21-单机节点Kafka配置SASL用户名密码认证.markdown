---
layout:     post
title:      单机节点Kafka配置SASL用户名密码认证
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u012842205/article/details/73188534				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div>
<blockquote><strong>转载请注明出处：<a href="http://blog.csdn.net/u012842205/article/details/73188534" rel="nofollow">http://blog.csdn.net/u012842205/article/details/73188534</a></strong><br></blockquote>
伪分布式Kafka，只用一个节点，zookeeper也用集成在Kafka里的。首先得配置zookeeper的SASL，再配置Kafka broker的SASL。先启动zookeeper节点，再启动Kafka broker。最后测试consumer和读写。<br clear="none"></div>
<div><br clear="none"></div>
<div>Kafka版本2.11-0.10.0.0，集成的zookeeper为3.4.6。<br clear="none"></div>
<div><span style="font-size:18px;"><strong><br clear="none"></strong></span></div>
<div><span style="font-size:18px;"><strong>一、Zookeeper</strong></span><br clear="none"></div>
<div><span style="font-size:14px;"><strong>1、单节点zookeeper配置</strong></span><br clear="none"></div>
<div>注意，这里我只使用Kafka里集成的zookeeper。先配置zookeeper.properties配置文件，写入如下配置项：<br clear="none"></div>
<div><pre><code class="language-plain">authProvider.1=org.apache.zookeeper.server.auth.SASLAuthenticationProvider
requireClientAuthScheme=sasl
jaasLoginRenew=3600000</code></pre><br></div>
<div><span style="font-size:14px;"><strong>2、创建JAAS配置文件</strong></span><br clear="none"></div>
<div>JAAS配置文件指定身份认证插件，并提供登录到zookeeper的登录名与密码。我创建了一个配置文件kafka_zoo_jaas.conf ：<br clear="none"></div>
<div><pre><code class="language-plain">ZKServer {
  org.apache.kafka.common.security.plain.PlainLoginModule required
    username="admin"
    password="admin-secret"
    user_admin="admin-secret";
};</code></pre>这里我配置的应用名称为ZKServer，zookeeper默认使用的JAAS应用名称是Server。这个名称如何修改在后面解释。使用如user_&lt;username&gt;的格式定义用户，等号右边的值即是密码。我们使用user_admin配置了一个用户名为admin，密码为admin-secret。最后，再选择一个用户作为Zookeeper内部各个节点间的通信认证。只有一个amdin用户，所以填写username和password即可。当然，也可以定义多个用户，视自己的需求来定义。<br clear="none"></div>
<div><br clear="none"></div>
<div><span style="font-size:14px;"><strong>3、启动前设置环境变量</strong></span><br clear="none"></div>
<div>解释为何要设置：<br clear="none"></div>
<div>设置的环境变量其实只是传入到JVM的参数。我们设置的环境变量是KAFKA_OPTS。zookeeper的启动脚本调用如下：<br clear="none"></div>
<div>bin/zookeeper-server-start.sh<br clear="none"></div>
<div>bin/kafka-run-class.sh<br clear="none"></div>
<div>在最后这个脚本中，将会使用到KAFKA_OPTS这个变量，并传入JVM。所以这个变量设置成JVM能识别的格式即可。-Dkey=value<br clear="none"></div>
<div><br clear="none"></div>
<div>这里我配置两个变量：<br clear="none"></div>
<div><pre><code class="language-plain">-Djava.security.auth.login.config=$ROOT/config/kafka_zoo_jaas.conf -Dzookeeper.sasl.serverconfig=ZKServer</code></pre><br>
第一个是指定上一步骤中jaas配置文件的路径，第二个参数指定的就是JAAS应用名称，也就是zookeeper在jaas配置文件中寻找的配置节，上一步骤已经说了，默认是Server，这里修改成ZKServer。<br clear="none"></div>
<div><br clear="none"></div>
<div><strong><span style="font-size:14px;">4、启动zookeeper</span></strong><br clear="none"></div>
<div>使用启动脚本bin/zookeeper-server-start.sh，3、 4两步骤可以直接写成一个脚本：<br clear="none"></div>
<div><pre><code class="language-plain">#!/bin/bash
ROOT=`dirname $0`
export KAFKA_OPTS=" -Djava.security.auth.login.config=$ROOT/config/kafka_zoo_jaas.conf -Dzookeeper.sasl.serverconfig=ZKServer "
$ROOT/bin/zookeeper-server-start.sh $ROOT/config/zookeeper.properties &gt;&gt; zookeeper.log 2&gt;&amp;1 &amp;</code></pre><br clear="none"></div>
<div>执行这个脚本即可。<br clear="none"></div>
<div><br clear="none"></div>
<div>可以查看日志文件，成功执行没有抛异常即可。<br clear="none"></div>
<div><br clear="none"></div>
<div><strong><span style="font-size:18px;">二、Kafka Broker</span></strong><br clear="none"></div>
<div><strong><span style="font-size:14px;">1、伪分布式Kafka Broker配置</span></strong><br clear="none"></div>
<div>上一节配置了内嵌的zookeeper，确保启动成功。Kafka这部分配置稍多一些，先配置server.properties：<br clear="none"></div>
<pre><code class="language-plain"># 监听的地址，使用的schema是SASL_PLAINTEXT，即最简单的用户名密码认证，不需要加密。
listeners=SASL_PLAINTEXT://vubuntuez1:9092

# 使用的认证协议
security.inter.broker.protocol=SASL_PLAINTEXT  

# SASL机制
sasl.enabled.mechanisms=PLAIN 
sasl.mechanism.inter.broker.protocol=PLAIN  

# 完成身份验证的类
authorizer.class.name=kafka.security.auth.SimpleAclAuthorizer

# 如果没有找到ACL（访问控制列表）配置，则允许任何操作。
allow.everyone.if.no.acl.found=true</code></pre><br><br><div><strong><span style="font-size:14px;">2、创建JAAS配置文件</span></strong><br clear="none"></div>
<div>我们需要设置KafkaServer节配置和Client节配置，其中KafkaServer节定义其他链接Broker的客户端及客户端之间相互通信所使用的的用户名密码。Client节用于与zookeeper通信时进行认证，用户名密码在上一步配置zookeeper时的zookeeper JAAS配置文件中定义了。<br clear="none"></div>
<div><br clear="none"></div>
<div>以下是我的kafka_server_jaas.conf配置文件：<br clear="none"></div>
<div><pre><code class="language-plain">KafkaServer {
    org.apache.kafka.common.security.plain.PlainLoginModule required
        username="geting"
        password="geting"
        user_geting="geting"
        user_alice="geting";
};

Client {
    org.apache.kafka.common.security.plain.PlainLoginModule required
        username="admin"
        password="admin-secret";
};</code></pre><br>
KafkaServer中定义了两个用户，一个geting，一个alice，两个用户都可以给客户端程序使用（生产者、消费者程序）。我们选择geting这个用户作为Kafka broker之间的内部认证。username和password用于broker之间通信。<br clear="none"></div>
<div>Client没什么不同的，就是broker与zookeeper通信时登录zookeeper的用户名密码，上面配置zookeeper JAAS时只定义了一个admin用户，只能填写这个。<br clear="none"></div>
<div><br clear="none"></div>
<div><strong><span style="font-size:14px;">3、配置KAFKA_OPTS shell变量</span></strong><br clear="none"></div>
<div>上面说的kafka_server_jaas.conf配置文件也同zookeeper一样，要在启动kafka broker时传递给JVM，同样可以设置KAFKA_OPTS，下面是kafkabroker启动脚本：<br clear="none"></div>
<div><pre><code class="language-plain">#!/bin/bash
ROOT=`dirname $0`
export KAFKA_OPTS=" -Djava.security.auth.login.config=$ROOT/config/kafka_server_jaas.conf"
$ROOT/bin/kafka-server-start.sh $ROOT/config/server.properties &gt;&gt; kafka.log 2&gt;&amp;1 &amp;</code></pre><strong><span style="font-size:14px;"><br>
4、启动broker</span></strong><br clear="none"></div>
<div>调用上一步编写的启动脚本，启动broker。查看日志，没有什么异常则成功。<br clear="none"></div>
<div><strong><span style="font-size:18px;"><br clear="none"></span></strong></div>
<div><strong><span style="font-size:18px;">三、简单的Producer编写</span></strong><br clear="none">
0.9以后的kafka版本生产者API没有问题，可以直接使用，0.9前的生产者API貌似不支持安全，这个问题具体查看官方文档，更具体的我不了解。<br clear="none"></div>
<div><br clear="none"></div>
<div>0.10后的kafka，生产者代码我不写了，关键说说需要增加的配置操作。<br clear="none"></div>
<div><strong><span style="font-size:14px;">1、添加配置</span></strong><br clear="none">
JAVA程序的KafkaProducer初始化需要传入Properties，向Properties实例（假设是props）中加入如下配置项：<br clear="none"></div>
<pre><code class="language-java">props.setProperty ("security.protocol", "SASL_PLAINTEXT");
props.setProperty ("sasl.mechanism", "PLAIN");</code></pre>
<p><strong><span style="font-size:14px;"><br></span></strong></p>
<p><strong><span style="font-size:14px;">2、编写JAAS配置文件</span></strong></p>
<div>这里我们用kafka_client_jaas.conf<br clear="none"></div>
<div><pre><code class="language-plain">KafkaClient {
    org.apache.kafka.common.security.plain.PlainLoginModule required
            username="alice"
            password="geting";
};</code></pre><br>
配置节KafkaClient，默认的是这个，这个名称也可以修改，自行查官方文档。我们使用alice用户。高版本的kafka，其生产者消费者只需要直接链接kafka broker即可，所以不用配置zookeeper。<br clear="none"></div>
<div><br clear="none"></div>
<div><strong><span style="font-size:14px;">3、指定JAAS配置文件</span></strong><br clear="none"></div>
<div>同理，通过变量java.security.auth.login.config传递给JVM。Kafka的client组件会调用System.getProperty()这个JDK函数获取到JAAS配置文件路径，所以我们直接使用System.setProperty()设置这个变量即可。<br clear="none"></div>
<div><br clear="none"></div>
<div><strong><span style="font-size:14px;">4、启动生产者</span></strong><br clear="none"></div>
<div>提议最好设置一个log4j配置文件，并且设置日志级别为DEBUG，这样就能看见是否正常写入数据。<br clear="none"></div>
<div><br clear="none"></div>
<div><span style="font-size:18px;"><strong>四、简单的Consumer编写</strong></span><br clear="none"></div>
<div>步骤同producer。<br clear="none"></div>
<div><br clear="none"></div>
<div>最后说说，JAAS配置文件的语法必须写对，否则总会爆出一些奇奇怪怪的错误，具体格式可以上oracle jdk官方API查看：<br clear="none"></div>
<div><strong><a href="http://docs.oracle.com/javase/8/docs/api/javax/security/auth/login/Configuration.html" rel="nofollow">http://docs.oracle.com/javase/8/docs/api/javax/security/auth/login/Configuration.html</a></strong><br clear="none"></div>
<div><strong><br clear="none"></strong></div>
<div><strong><br clear="none"></strong></div>
<div><br clear="none"></div>
<div><br clear="none"></div>
<div><br clear="none"></div>
<div><br clear="none"></div>
            </div>
                </div>