---
layout:     post
title:      大数据密训第九课：IDEA下开发Spark程序
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:14px;">第一阶段（<span style="font-family:Calibri;">1-3</span><span style="font-family:'宋体';">月）：会从浅入深，基于大量案例实战，深度剖析和讲解</span><span style="font-family:Calibri;">Spark</span><span style="font-family:'宋体';">，并且会包含完全从企业真实复杂业务需求中抽取出的案例实战。课程会涵盖</span><span style="font-family:Calibri;">Scala</span><span style="font-family:'宋体';">编程详解、</span><span style="font-family:Calibri;">Spark</span><span style="font-family:'宋体';">核心编程、</span><span style="font-family:Calibri;">Spark SQL</span><span style="font-family:'宋体';">和</span><span style="font-family:Calibri;">Spark Streaming</span><span style="font-family:'宋体';">、</span><span style="font-family:Calibri;">Spark GraphX</span><span style="font-family:'宋体';">、</span><span style="font-family:Calibri;">SparkR</span><span style="font-family:'宋体';">、</span><span style="font-family:Calibri;">Machine Learning</span><span style="font-family:'宋体';">、</span><span style="font-family:Calibri;">Spark</span><span style="font-family:'宋体';">内核以及源码剖析、性能调优、企业级案例实战等部分</span></span></p>
<p><span style="font-size:14px;">第二阶段（<span style="font-family:Calibri;">Spark</span><span style="font-family:'宋体';">超大规模大数据案例实战）：使用了</span><span style="font-family:Calibri;">Spark</span><span style="font-family:'宋体';">技术生态栈中的</span><span style="font-family:Calibri;">Spark Core</span><span style="font-family:'宋体';">、</span><span style="font-family:Calibri;">Spark SQL</span><span style="font-family:'宋体';">、</span><span style="font-family:Calibri;">Spark Streaming</span><span style="font-family:'宋体';">、</span><span style="font-family:Calibri;">SparkR</span><span style="font-family:'宋体';">、</span><span style="font-family:Calibri;">Machine Learning</span><span style="font-family:'宋体';">，进行离线计算和实时计算业务模块的开发、数据的关联性分析、用户行为模式和特征的训练与应用、用户网络的社区发现、用户影响力、能量传播、标签传播、标签推理、人群划分、年龄段预测、商品交易时序跳转。</span></span></p>
<p><span style="font-size:14px;"><span style="font-family:'宋体';"></span></span></p>
<p>本期内容：</p>
<p>1 <span style="font-family:'宋体';">使用</span><span style="font-family:Calibri;">IDEA</span><span style="font-family:'宋体';">开发</span><span style="font-family:Calibri;">Spark</span><span style="font-family:'宋体';">分析</span></p>
<p>2 <span style="font-family:'宋体';">使用</span><span style="font-family:Calibri;">IDEA</span><span style="font-family:'宋体';">开发</span><span style="font-family:Calibri;">Spark</span><span style="font-family:'宋体';">实战</span></p>
<p>3<span style="font-family:'宋体';">，使用</span><span style="font-family:Calibri;">IDEA</span><span style="font-family:'宋体';">开发</span><span style="font-family:Calibri;">Spark</span><span style="font-family:'宋体';">的</span><span style="font-family:Calibri;">Local</span><span style="font-family:'宋体';">和</span><span style="font-family:Calibri;">Cluster</span></p>
<p>（1）在安装包 Windows下最新的ideaIC-15.0.2.exe</p>
<p>（2）本地Java 8 和Scala 2.10.4软件套件的安装</p>
<p>（3）为IDEA安装Scala 这个过程是IDEA自动化的插件管理，所以点击后会自动下载</p>
<img src="https://img-blog.csdn.net/20160111142224526?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><p><span style="font-size:14px;"><span style="font-family:'宋体';"></span></span></p>
<p>为IDEA安装Scala 这个过程是IDEA自动化的插件管理，所以点击后会自动下载</p>
<img src="https://img-blog.csdn.net/20160111142300943?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><p><span style="font-size:14px;"><span style="font-family:'宋体';"></span></span></p>
<p>4,指定JDK 1.8.x和Scala 2.10.4</p>
<p>点击finish后开始建立indexing</p>
<img src="https://img-blog.csdn.net/20160111142331954?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><p><span style="font-size:14px;"><span style="font-family:'宋体';"></span></span></p>
<p>（5）File-&gt; Project Stucture来设置工程的Libraries 核心是添加Spark的Jar</p>
<img src="https://img-blog.csdn.net/20160111142417140?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><p><span style="font-size:14px;"><span style="font-family:'宋体';">（6）</span></span><span style="font-family:'宋体';font-size:14px;">添加Spark的Jar依赖 spark-1.6.0-bin-hadoop2.6.tgz </span><span style="font-family:'宋体';font-size:14px;">spark-assembly-1.6.0-hadoop2.6.0.jar</span></p>
<p><span style="font-size:14px;"><span style="font-family:'宋体';"><img src="https://img-blog.csdn.net/20160111142450852?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></span></p>
<p><span style="font-size:14px;"><span style="font-family:'宋体';"></span></span></p>
<p>加入依赖后，创建包和Scala类开发第一个Spark程序WordCount</p>
<img src="https://img-blog.csdn.net/20160111142521595?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><p><span style="font-size:14px;"><span style="font-family:'宋体';"></span></span></p>
<p>注：为什么不能够在IDE集成开发环境中直接发布Spark程序到Spark集群中</p>
<p>第一点：内存和Cores的限制，默认情况下Spark程序的Driver会在提交Spark程序的机器上，所以如果在IDE中提交程序的话，那IDE机器就必须非常强大；</p>
<p>第二点：Driver要指挥Workers的运行并频繁的发生通信，如果开发环境IDE和Spark集群不在同样一个网络下，就会出现任务丢失，运行缓慢等多种不必要的问题；</p>
<p>第三点：安全性太差。</p>
<p>程序打包</p>
<p>打包的过程：File-Project Structure-Aartifacts-Jar-FromModuleWithDependencies-MainClass</p>
<img src="https://img-blog.csdn.net/20160111142607069?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><p><span style="font-size:14px;"><span style="font-family:'宋体';"></span></span></p>
<p>ＤＴ大数据梦工厂</p>
<p>新浪微博：<a href="http://www.weibo.com/ilovepains/" rel="nofollow">www.weibo.com/ilovepains/</a></p>
<p>微信公众号：<span style="font-family:Calibri;">DT_Spark</span></p>
<p>博客：<a href="/ilovepains" rel="nofollow">http://.blog.sina.com.cn/ilovepains</a></p>
<p>TEL:18610086859</p>
<p>Email:18610086859@vip.126.com</p>
<p> </p>
<p>监控台与打印的信息</p>
<br><p><span style="font-size:14px;"><span style="font-family:'宋体';"><br></span></span></p>
<p><span style="font-size:14px;"><span style="font-family:'宋体';"><br></span></span></p>
<p><span style="font-size:14px;"><span style="font-family:'宋体';"><br></span></span></p>
            </div>
                </div>