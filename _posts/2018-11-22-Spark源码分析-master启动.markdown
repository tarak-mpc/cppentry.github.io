---
layout:     post
title:      Spark源码分析-master启动
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div>
<div style="font-family:'微软雅黑';font-size:14px;">1：背景介绍</div>
<div style="font-family:'微软雅黑';font-size:14px;">     接触spark也已经有三四个月了，刚刚接触spark的时候，spark的版本是0.8.1，经过几个月的发展，终于到了1.0版本了，这对spark来说是一个重大的里程碑。在接触spark的过程中，写过一些spark的应用程序，也看过很多对spark源代码的分析，如果一直只满足于使用scala编写spark应用程序，总感觉对很多东西理解的很模糊，所以也尝试着自己开始写一些对spark源码的分析文章，这样也能够帮助自己和其他人更加深入的理解spark。
     </div>
<div style="font-family:'微软雅黑';font-size:14px;">     刚接触spark源代码，也是千头万绪，不知道从哪个地方下手，最终决定还是从master的启动开始吧。</div>
<div style="font-family:'微软雅黑';font-size:14px;">2：启动顺序</div>
<div style="font-family:'微软雅黑';font-size:14px;">     在部署spark的过程中，在修改完配置文件后，开始启动spark，启动spark在sbin目录下运行 ./start-master.sh脚本启动master，运行./start-slaves.sh脚本启动slaves，或者直接运行./start-all.sh，但其实该脚本也是分别调用./start-master.sh和./start-slaves.sh脚本。打开./start-master.sh脚本,找到其中的</div>
<div style="font-family:'微软雅黑';font-size:14px;">     "$sbin"/spark-daemon.sh start org.apache.spark.deploy.master.Master 1 --ip $SPARK_MASTER_IP --port $SPARK_MASTER_PORT --webui-port $SPARK_MASTER_WEBUI_PORT</div>
<div style="font-family:'微软雅黑';font-size:14px;">      因此master启动的时候运行的是org.apache.spark.deploy.master.Master类，下面我们分析的也是这个类。</div>
<div style="font-family:'微软雅黑';font-size:14px;">      下载spark1.0的源码，根据包名找到object Master.</div>
</div>
<div style="font-family:'微软雅黑';font-size:14px;">
<div>
<div style="font-family:'微软雅黑';font-size:14px;"><img src="https://img-blog.csdn.net/20140606163558937?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaHVqdW44NjEw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<div style="font-family:'微软雅黑';font-size:14px;">      object Master代码并不长，在main中主要调用startSystemAndActor方法，该方法返回(ActorSystem,Int,Int)的三元组，在该方法内部，除了创建一个SecurityManager对象（根据spark的文档，该类主要是实现Spark currently supports authentication via a shared
 secret的功能，对安全不是很了解，有兴趣的可以自己去了解相关的知识），另外就是调用AKKaUtils.createActorSystem方法初始化actorSystem。然后actorSystem调用actorOf方法生成一个actor对象，该对象将负责与其他进程之间的通信。</div>
<div style="font-family:'微软雅黑';font-size:14px;">     打开AKKaUtils.scala文件，类AKKaUtils做了很多工作，主要分为两个部分，初始化akkaConf以及调用ActorSystem(name,akkaConf)方法获得该方法的实例，然后将actorSystem以及端口号返回。</div>
<div style="font-family:'微软雅黑';font-size:14px;"></div>
<div style="font-family:'微软雅黑';font-size:14px;"><br></div>
<div style="font-family:'微软雅黑';font-size:14px;">3：难点解析</div>
<div style="font-family:'微软雅黑';font-size:14px;">     由上述过程不难发现，master真正的启动依赖于AKKA以及actor。下面将简单的介绍scala的actor以及AKKA，这样可能有助于理解Spark各个组件的进程之间通信。</div>
<div style="font-family:'微软雅黑';font-size:14px;">     1、actor简介：</div>
<div style="font-family:'微软雅黑';font-size:14px;">     大家知道，java平台自带了内建的基于共享数据和锁的线程模型，每个对象都关联了一个逻辑监视器，用于控制对数据的多线程访问。Java的多线程技术就是基于此的，但是在实际应用中，编写与测试java多线程应用程序是很困难的，这其中很重要的一点是我们必须推断出哪些正在修改或者访问的数据会被其他线程修改和访问，这在大型程序中是一件很困难的事情。scala的actor包从根本上解决了这个问题，提供了另外一种不共享任何数据，依赖消息传递的模型，使用这种模型设计并发软件要容易的多。这就是actor出现的意义。</div>
<div style="font-family:'微软雅黑';font-size:14px;">     actor可以理解为一个类似线程的实体，它有一个用于接收消息的邮箱，实现actor的一个简单方法是继承scala.actor.Actor并完成其act方法（类似于在java中集成Thread时需要重写run方法），然后调用该类实例的start方法启动actor即可。代码如下所示：</div>
<div style="font-family:'微软雅黑';font-size:14px;">
<table border="1" cellpadding="2" cellspacing="0" width="100%"><tbody><tr><td valign="top">import actors._<br><br>
object SeriousActor extends Actor{<br>
   def act(){<br>
     for(i &lt;- 1 to 5){<br>
       println("test"+i)<br>
       Thread.sleep(1000)<br>
     }<br>
   }<br>
def main (args: Array[String]) {<br>
    SeriousActor.start();<br>
    Thread.sleep(3000)<br>
    println("In the main thread")<br>
  }<br>
}</td>
</tr></tbody></table><div>程序的运行结果如下所示</div>
<div><img src="https://img-blog.csdn.net/20140606163659546?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaHVqdW44NjEw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<div>
<div>
<div style="font-family:'微软雅黑';font-size:14px;">可以看到main函数中的Thread.sleep函数并没有阻塞actor。不过上述的actor只是自己带输出，还无法与其他的actor进行通信。如果希望actor相互之间进行通信完成具体的任务，就必须在act中使用receive或者react来接收消息。实际上关于actor还有很多内容需要注意的，大家如果比较感兴趣的话，可以参考《scala编程》，里面有详细的介绍。</div>
<div style="font-family:'微软雅黑';font-size:14px;">   </div>
<div style="font-family:'微软雅黑';font-size:14px;">     2、AKKA简介</div>
<div style="font-family:'微软雅黑';font-size:14px;">     AKKA是scala开源项目中非常有名的一个，但是网上关于它的介绍的英文资料非常少，而且大部分还都是翻译过来。但是了解AKKA对理解spark的体系结构有非常大的帮助，理解AKKA后，可以利用该框架自己编写一些简单的分布式应用程序。但是本人对这一快了解的非常少。后面会抽出时间专门写一篇关于akka的文章。</div>
<div style="font-family:'微软雅黑';font-size:14px;"><br></div>
<div style="font-family:'微软雅黑';font-size:14px;">4：结束语</div>
<div style="font-family:'微软雅黑';font-size:14px;">    这是本人的第一篇关于spark的文章，有很多地方写的太好，请大家见谅。</div>
</div>
<br></div>
</div>
</div>
<br></div>
            </div>
                </div>