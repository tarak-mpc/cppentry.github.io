---
layout:     post
title:      hadoop + hbase架构和源码分析
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/lizhitao/article/details/52499668				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<ol>
<li><a href="http://blog.csdn.net/woshiwanxin102213/article/details/17584043" rel="nofollow">Hbase原理、基本概念、基本架构</a> </li>
<li><a href="http://www.blogjava.net/DLevin/archive/2015/08/22/426877.html" rel="nofollow">深入HBase架构解析（一）</a></li>
<li><a href="http://www.blogjava.net/DLevin/archive/2015/08/22/426950.html" rel="nofollow">深入HBase架构解析（二）</a></li>
<li><a href="http://www.cnblogs.com/JemBai/archive/2012/07/21/2602432.html" rel="nofollow">HBase 系统架构</a> </li>
<li><a href="http://www.linuxidc.com/Linux/2012-01/52242.htm" rel="nofollow">HBase之数据模型(DataModel)</a></li>
<li><a href="http://www.shangxueba.com/jingyan/753494.html" rel="nofollow">hbase数据模型</a></li>
<li><a href="http://www.oschina.net/question/195301_41176" rel="nofollow">HBase 在淘宝的应用和优化小结</a></li>
<li><a href="http://blog.csdn.net/dbanote/article/details/34953353" rel="nofollow">[Hadoop] 如何为你的企业选择正确的Hadoop版本</a></li>
<li><a href="http://www.2cto.com/database/201305/209355.html" rel="nofollow">[HBase]数据模型（逻辑结构）</a></li>
<li><a href="http://blog.csdn.net/dbanote/article/details/9151475" rel="nofollow">[Hadoop] 实际应用场景之 - 阿里</a></li>
<li><a href="http://blog.jobbole.com/83614/" rel="nofollow">HBase的特征和优点</a></li>
<li><a href="http://gao-xianglong.iteye.com/blog/2031543" rel="nofollow">笔者带你剖析大数据存储HBase特性</a></li>
<li><a href="http://blog.csdn.net/dbanote/article/details/8897599" rel="nofollow">[HBase] LSM树 VS B+树</a></li>
<li><a href="http://cloud.51cto.com/art/201505/476391.htm" rel="nofollow">基于HBase的海量微博数据高效存储解析</a></li>
<li><a href="http://blog.csdn.net/u010270403/article/details/51648462" rel="nofollow">HBase详细概述</a></li>
<li><a href="http://blog.csdn.net/lifuxiangcaohui/article/details/39894265" rel="nofollow">Hbase总结（五）-hbase常识及habse适合什么场景</a></li>
<li><a href="http://blog.csdn.net/lifuxiangcaohui/article/details/39961643" rel="nofollow">Hbase总结（六）hbase37个笔试题</a></li>
<li><a href="http://blog.csdn.net/lifuxiangcaohui/article/details/39894265" rel="nofollow">Hbase总结（五）-hbase常识及habse适合什么场景</a></li>
<li><a href="http://www.aboutyun.com/thread-7804-1-1.html" rel="nofollow">图解Nosql（hbase）与传统数据库的区别</a></li>
<li><a href="http://blog.csdn.net/dbanote/article/details/9055569" rel="nofollow">[Hadoop] HDFS架构设计</a></li>
<li><a href="http://blog.csdn.net/dbanote/article/details/9055569" rel="nofollow">[Hadoop] HDFS架构设计</a></li>
<li><a href="http://blog.csdn.net/dbanote/article/details/9056129" rel="nofollow">[Hadoop] MapReduce架构设计</a></li>
<li><a href="http://blog.csdn.net/lifuxiangcaohui/article/details/39889653" rel="nofollow">Hbase总结（三）-Hbase与Hive的区别与联系</a></li>
<li><a href="http://blog.csdn.net/lifuxiangcaohui/article/details/39891099" rel="nofollow">Hbase总结（四）- Hbase与传统数据库的区别</a></li>
<li><a href="http://blog.csdn.net/dbanote/article/details/9151475" rel="nofollow">[Hadoop] 实际应用场景之 - 阿里</a></li>
<li><a href="http://blog.csdn.net/dbanote/article/details/8929025" rel="nofollow">[OceanBase] 架构设计</a></li>
<li><a href="http://blog.csdn.net/dbanote/article/details/8902746" rel="nofollow">[HBase] 体系架构（物理模型）</a></li>
<li><a href="http://www.open-open.com/lib/view/open1427792081037.html" rel="nofollow">Hbase基本原理、及存储知识</a></li>
<li><a href="http://www.cnblogs.com/sharpxiajun/p/5585613.html" rel="nofollow">HBase笔记：对HBase原理的简单理解</a></li>
<li><a href="http://www.cnblogs.com/zhenjing/p/hbase_example.html" rel="nofollow">HBase Java简单示例</a></li>
<li><a href="http://www.cnblogs.com/nexiyi/p/hbase_intro_94.html" rel="nofollow">HBase简介</a></li>
<li><a href="http://jiajun.iteye.com/blog/899632/" rel="nofollow">HBase简介（很好的梳理资料）</a></li>
<li><a href="http://www.open-open.com/lib/view/open1427792081037.html" rel="nofollow">Hbase基本原理、及存储知识</a></li>
<li><a href="http://www.open-open.com/lib/view/open1346821084631.html" rel="nofollow">Hbase系统架构及数据结构</a></li>
<li><a href="http://www.open-open.com/news/view/478ecf" rel="nofollow">Spark 是否真的比 MapReduce 技高一筹</a></li>
<li><a href="http://www.open-open.com/news/view/8983f7" rel="nofollow">陈超：Spark这一年，从开源到火爆</a></li>
<li><a href="http://www.open-open.com/news/view/1d12d74" rel="nofollow">Apache Kylin在百度地图的实践</a></li>
<li><a href="http://www.open-open.com/news/view/15e2eca" rel="nofollow">事实上，Spark是一项非常值得学习的技术</a></li>
<li><a href="http://www.open-open.com/doc/view/d950bb9b1923448cbd6106ce724d92df" rel="nofollow">HBase 介绍.pptx</a></li>
<li><a href="http://www.open-open.com/lib/view/open1328755780186.html" rel="nofollow">HBase技术详细介绍</a></li>
<li><a href="http://www.open-open.com/lib/view/open1448618635079.html" rel="nofollow">基于HBase做Storm 实时计算指标存储</a></li>
<li><a href="http://www.open-open.com/lib/view/open1449301491561.html" rel="nofollow">HBase优化实战</a></li>
<li><a href="http://blog.csdn.net/wuwenxiang91322/article/details/45626271" rel="nofollow">HBase RegionServer详解</a></li>
<li><a href="http://blog.csdn.net/wuwenxiang91322/article/details/51595771" rel="nofollow">Hbase内存磁盘大致关系</a></li>
<li><a href="http://www.oschina.net/question/12_26495" rel="nofollow">关系型数据库到HBase的数据储存方式变迁</a></li>
<li><a href="http://www.cnblogs.com/hark0623/p/5571193.html" rel="nofollow">HBase原理和设计</a></li>
<li><a href="http://blog.csdn.net/lxf310/article/details/22398655" rel="nofollow">HBase Region分裂</a></li>
<li><a href="http://wenku.baidu.com/view/7f3a6708aeaad1f347933fca.html?re=view" rel="nofollow">Module 02 HDFS架构原理</a></li>
<li><a href="http://wenku.baidu.com/view/54254a03cf84b9d528ea7a83.html?re=view" rel="nofollow">HDFS 原理分析</a></li>
<li><a href="https://my.oschina.net/mkh/blog/349866" rel="nofollow">Hbase存储详解</a></li>
<li><a href="https://www.zhihu.com/question/26872988" rel="nofollow">region HFile DataNode 三者的区别与关系</a></li>
<li><a href="http://www.thebigdata.cn/HBase/15222.html" rel="nofollow">HBase vs. MongoDB vs. MySQL vs. Oracle vs. Redis，三大主流开源 NoSQL 数据库的 PK 两大主流传</a>…</li>
<li><a href="http://www.thebigdata.cn/HBase/12372.html" rel="nofollow">深入理解HBase</a></li>
<li><a href="http://zy19982004.iteye.com/blog/2087608" rel="nofollow">Hadoop学习四十一：HBase基础</a></li>
<li><a href="http://blog.csdn.net/frankiewang008/article/details/41965543" rel="nofollow">HBase 超详细介绍</a></li>
<li><a href="http://itindex.net/detail/49632-hbase-%E6%80%A7%E8%83%BD%E8%B0%83%E4%BC%98" rel="nofollow">hbase性能调优 | IT瘾</a></li>
<li><a href="http://www.thebigdata.cn/HBase/11925.html" rel="nofollow">HDFS架构</a></li>
<li><a href="http://www.thebigdata.cn/HBase/29725.html" rel="nofollow">HBase – RegionServer宕机案件侦查</a></li>
<li><a href="http://www.thebigdata.cn/HBase/8241.html" rel="nofollow">HBase在京东的完善与创新</a></li>
<li><a href="http://www.thebigdata.cn/HBase/9654.html" rel="nofollow">Cassandra与HBase的大数据对决 谁是胜者？</a></li>
<li><a href="http://www.thebigdata.cn/HBase/10055.html" rel="nofollow">分布式在线存储系统——HBase</a></li>
<li><a href="http://www.jianshu.com/p/7f25bbc3cd7f" rel="nofollow">hdfs架构与高可用性</a></li>
<li><a href="https://yq.aliyun.com/articles/60406?spm=5176.100239.blogcont60910.17.sexCK4" rel="nofollow">hdfs平衡分布</a></li>
<li><a href="http://wangneng-168.iteye.com/blog/2067741" rel="nofollow">hbase性能调优</a></li>
<li><a href="http://www.searchtb.com/2011/01/understanding-hbase.html" rel="nofollow">HBase技术介绍</a></li>
<li><a href="http://www.cnblogs.com/shitouer/archive/2012/06/04/2533518.html" rel="nofollow">HBase 系统架构</a></li>
<li><a href="http://blog.xiaoxiaomo.com/2016/06/05/HBase-%E8%AF%BB%E5%86%99%E6%95%B0%E6%8D%AE/" rel="nofollow">HBase–读写数据</a></li>
<li><a href="http://blog.xiaoxiaomo.com/2016/08/06/Hive-%E6%89%A7%E8%A1%8C%E6%B5%81%E7%A8%8B%E5%92%8C%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90/" rel="nofollow">Hive–执行流程和源码解析</a></li>
<li><a href="http://pangjiuzala.github.io/2015/09/26/%E6%B7%B1%E5%85%A5HBase%E6%9E%B6%E6%9E%84%E8%A7%A3%E6%9E%90-1/" rel="nofollow">深入HBase架构解析(1)</a></li>
<li><a href="http://pangjiuzala.github.io/2015/09/28/%E6%B7%B1%E5%85%A5HBase%E6%9E%B6%E6%9E%84%E8%A7%A3%E6%9E%90-2/" rel="nofollow">深入HBase架构解析(2)</a></li>
<li><a href="http://blog.csdn.net/kwu_ganymede/article/details/51227834" rel="nofollow">HBase读与写数据流程</a></li>
<li><a href="http://hbasefly.com/2016/03/23/hbase-memstore-flush/" rel="nofollow">HBase – Memstore Flush深度解析</a></li>
<li><a href="http://hbasefly.com/2016/09/26/hbase-mutiltenant-1/" rel="nofollow">HBase最佳实践 – 多租户机制(上)</a></li>
<li><a href="http://hbasefly.com/2016/09/08/hbase-rit/" rel="nofollow">HBase运维实践－聊聊RIT的那点事</a></li>
<li><a href="http://hbasefly.com/2016/08/22/hbase-practise-cluster-planning/" rel="nofollow">HBase最佳实践－集群规划</a></li>
<li>HBase最佳实践－CMS GC调优</li>
<li>HBase Compaction的前生今世－改造之路</li>
<li>HBase Compaction的前生今世－身世之旅</li>
<li>HBase最佳实践－列族设计优化</li>
<li>HBase最佳实践－内存规划</li>
<li>HBase最佳实践－客户端超时机制</li>
<li><a href="http://hbasefly.com/2016/06/04/hbase-client-1/" rel="nofollow">HBase最佳实践 – 客户端重试机制</a></li>
<li><a href="http://www.jiagou4.com/2015/09/765.html" rel="nofollow">HBase技术介绍</a></li>
<li><a href="http://www.jianshu.com/p/b596411c5a48" rel="nofollow">HBase存储架构</a></li>
<li><a href="http://blog.csdn.net/kwu_ganymede/article/details/49097437" rel="nofollow">从关系库导入数据到hive-hbase表中</a></li>
<li><a href="http://blog.csdn.net/kwu_ganymede/article/details/49097391" rel="nofollow">创建hive整合hbase的表总结</a></li>
<li>【解决】<a href="http://blog.csdn.net/kwu_ganymede/article/details/49097287" rel="nofollow">hive与hbase表结合级联查询的问题</a></li>
<li><a href="http://blog.csdn.net/kwu_ganymede/article/details/51227665" rel="nofollow">HBase Coprocessor 剖析与编程实践</a></li>
<li><a href="https://hadoop.apache.org/docs/r1.0.4/cn/hdfs_design.html" rel="nofollow">Hadoop分布式文件系统：架构和设计</a></li>
<li><a href="http://blog.csdn.net/suifeng3051/article/details/48548341" rel="nofollow">Hadoop核心之HDFS 架构设计</a></li>
<li><a href="http://www.cnblogs.com/wuyudong/p/4404242.html" rel="nofollow">HDFS主要特性和体系结构</a></li>
<li><a href="http://dataunion.org/8798.html" rel="nofollow">分布式计算框架Hadoop原理及架构全解</a></li>
<li><a href="http://itindex.net/detail/53596-hdfs-%E6%9E%B6%E6%9E%84" rel="nofollow">HDFS架构</a></li>
<li><a href="https://my.oschina.net/leejun2005/blog/151872" rel="nofollow">HDFS 原理、架构与特性介绍</a></li>
<li><a href="http://blog.jobbole.com/34244/" rel="nofollow">HDFS的架构</a></li>
<li><a href="http://www.superwu.cn/2015/05/25/2353/" rel="nofollow">HDFS结构详解</a></li>
<li><a href="http://www.infoq.com/cn/articles/hadoop-intro" rel="nofollow">分布式计算开源框架Hadoop介绍</a></li>
<li><a href="http://www.aboutyun.com/thread-8121-1-1.html" rel="nofollow">淘宝网图片存储系统架构</a></li>
<li><a href="http://blog.csdn.net/yangning5850/article/details/9244683" rel="nofollow">Hadoop学习——HDFS系统架构</a></li>
<li><a href="http://www.csdn.net/article/1970-01-01/282725" rel="nofollow">Hadoop分布式文件系统：架构和设计要点</a></li>
<li><a href="http://blog.xiaoxiaomo.com/2016/07/03/Hadoop-MapReduce%E8%AF%A6%E8%A7%A3/" rel="nofollow">Hadoop–MapReduce详解</a></li>
<li><a href="http://blog.xiaoxiaomo.com/2016/06/26/Hadoop-HDFS%E4%B9%8B%E8%AF%BB%E5%86%99%E6%B5%81%E7%A8%8B/" rel="nofollow">Hadoop–HDFS之读写流程</a></li>
<li><a href="http://wangneng-168.iteye.com/blog/2067746" rel="nofollow">hbase client访问的超时时间、重试次数、重试间隔时间的配置</a></li>
<li><a href="http://wangneng-168.iteye.com/blog/2067741" rel="nofollow">hbase性能调优</a></li>
<li><a href="http://wangneng-168.iteye.com/blog/2067734" rel="nofollow">Split Region详细解读</a></li>
<li><a href="http://wangneng-168.iteye.com/blog/2067870" rel="nofollow">处理master不断assign未成功分配region的问题</a></li>
<li><a href="http://abloz.com/hbase/book.html" rel="nofollow">HBase 官方文档中文版</a></li>
<li><a href="http://wangneng-168.iteye.com/blog/1983657" rel="nofollow">hbase锁机制</a></li>
<li><a href="http://wangneng-168.iteye.com/blog/1982434" rel="nofollow">看懂-ROOT-表和.META.表</a></li>
<li><a href="http://wangneng-168.iteye.com/blog/1982592" rel="nofollow">hbase写数据过程</a></li>
<li><a href="http://wangneng-168.iteye.com/blog/2068808" rel="nofollow">导致Hbase挂掉的场景</a></li>
<li><a href="http://wangneng-168.iteye.com/blog/2087324" rel="nofollow">迁移namenode</a></li>
<li><a href="http://www.cnblogs.com/shitouer/archive/2012/07/09/2583095.html" rel="nofollow">Hadoop压缩-SNAPPY算法安装</a></li>
<li><a href="http://www.cnblogs.com/shitouer/archive/2012/05/29/2522860.html" rel="nofollow">Windows 下配置 Eclipse 连接 Hadoop 开发环境</a></li>
<li><a href="http://pangjiuzala.github.io/2015/12/06/Hadoop%E7%9A%84HA%E6%9C%BA%E5%88%B6/" rel="nofollow">Hadoop的HA机制</a></li>
<li><a href="http://pangjiuzala.github.io/2015/12/01/%E4%B8%80%E4%B8%AA%E5%AE%8C%E6%95%B4%E7%9A%84MapReduce%E7%A8%8B%E5%BA%8F/" rel="nofollow">一个完整的MapReduce程序</a></li>
<li><a href="http://itindex.net/detail/49700-hadoop-hdfs" rel="nofollow">Hadoop剖析之HDFS</a></li>
<li><a href="http://www.superwu.cn/2016/07/13/3172/" rel="nofollow">HDFS 架构</a></li>
<li><a href="http://www.itweet.cn/2016/01/25/Hadoop-Disk-Planning/" rel="nofollow">Hadoop平台架构–存储篇</a></li>
<li><a href="http://www.thebigdata.cn/HBase/11925.html" rel="nofollow">HDFS架构</a></li>
<li><a href="http://www.jianshu.com/p/7f25bbc3cd7f" rel="nofollow">hdfs架构与高可用性</a></li>
<li><a href="http://blog.chinaunix.net/uid-22312037-id-3993018.html" rel="nofollow">Hadoop初探之HDFS系统架构</a> </li>
<li><a href="http://www.imooc.com/article/9007" rel="nofollow">Hadoop大数据平台架构与实践总结</a></li>
<li><a href="https://yq.aliyun.com/articles/32240" rel="nofollow">Hadoop 基本架构</a></li>
<li><a href="https://yq.aliyun.com/articles/32239?spm=5176.100239.blogcont32240.7.jkeqo2" rel="nofollow">hadoop源代码组织结构与阅读技巧</a></li>
<li><a href="https://yq.aliyun.com/articles/60910?spm=5176.100239.blogcont32240.9.jkeqo2" rel="nofollow">专访HDFS committer Intel 研发经理郑锴：EC之后，HDFS下一步新思考</a></li>
<li><a href="https://yq.aliyun.com/articles/32344?spm=5176.100239.blogrightarea32240.15.jkeqo2" rel="nofollow">HBase数据模型剖析</a></li>
<li><a href="https://yq.aliyun.com/articles/32343?spm=5176.100239.blogrightarea32240.16.jkeqo2" rel="nofollow">HBase读写路径的工作机制</a></li>
<li><a href="http://blog.csdn.net/lifuxiangcaohui/article/details/40077753" rel="nofollow">HBase总结（十六）</a></li>
<li><a href="http://blog.csdn.net/lifuxiangcaohui/article/details/41925867" rel="nofollow">Hadoop架构和设计要点</a></li>
<li><a href="http://blog.csdn.net/lifuxiangcaohui/article/details/39962921" rel="nofollow">HBase总结（七）LSM理解</a></li>
<li><a href="http://blog.csdn.net/lifuxiangcaohui/article/details/7284311" rel="nofollow">HBase总体介绍</a></li>
<li><a href="http://blog.csdn.net/lifuxiangcaohui/article/details/40077753" rel="nofollow">HBase总结（十六）HBase原理讲解</a></li>
<li><a href="http://blog.csdn.net/hljlzc2007/article/details/10963425" rel="nofollow">Region Server 宕机后,处理过程</a></li>
<li><a href="http://blog.csdn.net/hljlzc2007/article/details/10961591" rel="nofollow">HBase性能优化方法总结</a></li>
<li><a href="http://blog.csdn.net/hljlzc2007/article/details/11744221" rel="nofollow">提升HBase写性能</a></li>
<li><a href="http://blog.csdn.net/xuguokun1986/article/details/50588428" rel="nofollow">HBase 数据库检索性能优化策略</a></li>
<li><a href="http://blog.csdn.net/hljlzc2007/article/details/11003545" rel="nofollow">HBase Flush操作流程以及对读写服务的影响</a></li>
<li><a href="http://blog.csdn.net/hljlzc2007/article/details/42526271" rel="nofollow">HBase宕机的多种场景</a></li>
<li><a href="http://blog.csdn.net/hljlzc2007/article/details/11475269" rel="nofollow">HBase .META. Region启动不成功</a></li>
<li><a href="http://blog.csdn.net/hljlzc2007/article/details/11764381" rel="nofollow">HBase向多个column写入的性能分析</a></li>
<li><a href="http://blog.csdn.net/hljlzc2007/article/details/11207383" rel="nofollow">HBase 维护–查看HLog和HFile</a></li>
<li><a href="http://mp.weixin.qq.com/s?__biz=MzI4NTA1MDEwNg==&amp;mid=2650755596&amp;idx=1&amp;sn=fee470d4093387faf5f851c0c2ef3dcb&amp;scene=21#wechat_redirect" rel="nofollow">海量吞吐的实时NoSQL：HBase的七剑和双11圣战(数据脱敏版)</a></li>
<li><a href="https://www.mapr.com/blog/in-depth-look-hbase-architecture" rel="nofollow">An In-Depth Look at the HBase Architecture</a></li>
<li><a href="https://mp.weixin.qq.com/s?__biz=MzI5MzMzNjQyNQ==&amp;mid=2247483930&amp;idx=1&amp;sn=b638697eedd02419bf1e7be5b49657a4&amp;chksm=ec72e61cdb056f0a817064173cf0320c2a7d7425a8a6afce3ca50d5f9ca5d1a8a212435dbcf7&amp;mpshare=1&amp;scene=23&amp;srcid=1221TNPqTA64qGmA8PtI7olD#rd" rel="nofollow">HBase架构设计与数据模型</a></li>
</ol>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>