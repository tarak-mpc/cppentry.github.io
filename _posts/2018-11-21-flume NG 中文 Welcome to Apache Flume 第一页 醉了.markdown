---
layout:     post
title:      flume NG 中文 Welcome to Apache Flume 第一页 醉了
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>第一次写博客手有点抖 ，因为项目需要做日志搜集和分析系统 ，在网上看了一下，相对 flume更简单试用一些 ，所以就登陆了官网，一看全是英文 ，傻了。早知道上学的时候要把汉语拼音学好 。没办法一首拿着词典 ，一边看着 flume 的首页 进行了2个小时 ，才把首页翻译了一遍 。下面把 Welcome to Apache Flume第一页 看了一遍 ，说实话不是太懂 但是 了解了 flume ng 中涉及到生词的一些含义 ，总结了一下 希望对于刚刚开始flume 的人有一些帮助 。</p>
<p><br></p>
<p>在 flume 的首页中涉及到了很多开源框架 一个个看了下，简单的描述一下吧 。都是在 welcome to apache flume中提到的 </p>
<p><br></p>
<p>   </p>
<p>Apache BigTop  ：   是 apache 基金推出的一个hadoop 以及 周边生态进行打包，分发 和测试的工具 <br></p>
<p>Avro                     ：    是hadoop 中的一个字项目，也是apache中一个独立的项目 ，avro 是一个基于二进制数据传输的高性能的中间件，在hadoop的其他HBASE（REF）和                                                 Hive（ref）的client段与服务的数据传输采用的工具 ，Avro是一个 数据序列化系统，Avro 可以将数据结构或对象转化为便于存储或传输的格式，Avro 涉及指        
                                 出就是支持防护据密集型的应用，适合于远程和本地大规模的存储数据的存储和交换<br></p>
<p>ElasticSearch   :        ElasticSearch是一个基于Lucene构建的开源，分布式，RESTful搜索引擎。设计用于云计算中，能够达到实时搜索，稳定，可靠，快速，安装使用方便。<br>
                                      支持通过HTTP使用JSON进行数据索引</p>
<p>solr 　　　　　：     是一个基于Lucene的Java搜索引擎服务器。Solr 提供了层面搜索、命中醒目显示并且支持多种输出格式（包括 XML/XSLT 和 JSON 格式）。它易于安装和                                    配置，而且附带了一个基于 HTTP 的管理界面。Solr已经在众多大型的网站中使用，较为成熟和稳定。Solr 包装并扩展了 Lucene，所以Solr的基本上沿用                                     了Lucene的相关术语。更重要的是，Solr
 创建的索引与 Lucene 搜索引擎库完全兼容。通过对Solr 进行适当的配置，某些情况下可能需要进行编码，Solr 可                                     以阅读和使用构建到其他 Lucene 应用程序中的索引。此外，很多 Lucene 工具（如Nutch、 Luke）也可以使用Solr 创建的索引。</p>
<p>thrift                   ：　Thrift源于大名鼎鼎的facebook之手，在2007年facebook提交Apache基金会将Thrift作为一个开源项目，对于当时的facebook来说创造thrift是为了解决　　　　　　　　　　　facebook系统中各系统间大数据量的传 输通信以及系统之间语言环境不同需要跨平台的特性。所以thrift可以支持多种程序语言，例如:  C++, C#, Cocoa, 　　　　　　　　　　Erlang, Haskell, Java, Ocami,
 Perl, PHP, Python, Ruby,<br></p>
<p>kite                   ：　　kite 是一个开发工具 ，kite is 高层次的开发hadoop 的提供了 api ，你可以配置 kite 存储数据到你的hadoop ，来代替构建和维护自己的基础设施（我也有                                      点醉了）</p>
<p>上面的词都是在网上找的，但是都在Welcome to Apache Flume 中出现了 ，这个了这些基本上能读Welcome to Apache Flume 页了，对了还有一些专业的生词 </p>
<p><br></p>
<p><img src="" alt=""><br></p>
<p><img src="" alt=""><br></p>
<p><br></p>
<p>welcome to apache flume zsj 欢迎来到爱你的apache flume 的世界 <br><br><br>
Flume is a distributed ,reliable ,and available service for efficiently collect ,aggregating ,and moving large amounts of log data .<br>
 (flume 是 分布是，高可靠的，高效率的服务。 flume 能够做到高效的收集，集成和移动大规模的日志数据)<br>
 It has a simple and flexible architecture based on straming data flows ,<br>
 对于数据流的处理，因为flume 是基于streaming 流实现的，所以在架构上flume  保持着简单性和可伸缩性。）<br>
 It uses a simple  extensible data model that allows for online analytic application <br>
 flume 使用简单的扩展数据模型能够支持在线的应用分析。<br>
 <br>
 November 18, 2014 - Apache Flume 1.5.2 Released   2014年11月18日 更新到了1.5.2  版本<br>
 <br>
 The Apache Flume team is pleased to announce the release of Flume 1.5.2    apache  flume 小组很荣幸的通知大家， 1.5.2  版本的发布<br><br><br>
Flume is a distributed, reliable, and available service for efficiently collecting, aggregating, and moving large amounts of streaming event data.<br>
Flume 软件是分布式，高可靠性，高可用性的 服务，对于搜集，集成和应用大量的日志数据来说能够通过事件流的方式高效完成。<br><br><br>
Version 1.5.2 is the eighth Flume release as an Apache top-level project. Flume 1.5.2 is stable, production-ready software, <br>
and is backwards-compatible with previous versions of the Flume 1.x codeline.<br><br><br>
作为apache 中高级别的功能，1.5.2 的版本是flume 发布的第8个版本，Flume 1.5.2 这个版本是稳定的，可投入生产的软件产品，<br><br><br>
1.5.2 这个版本也是之前的机遇flume1.x 代码进行编译而成的<br>
Apache Flume 1.5.2 is a security and maintenance release that disables SSLv3 on all components in Flume that support SSL/TLS. All users are encouraged to update to this release as soon as possible.<br>
flume 1.5.2 对于SSL/TLS的支持上来说，flume 是安全的和可维护的，在1.5.2 中废除了了对SSLv3 的支持。希望所有的用户更新到这个版本。<br><br><br>
The full change log and documentation are available on the Flume 1.5.2 release page.<br>
对于全部的变更日志和文档都是有效的可用的，在1.5.2的发布清单中<br><br><br>
This release can be downloaded from the Flume Download page.<br>
可以从flume 下载中进行下载<br><br><br>
Your contributions, feedback, help and support make Flume better! For more information on how to report problems or contribute, please visit our Get Involved page.<br>
您的贡献,反馈和帮助以及支持都能够使flume 越做越好，对于在支持上和贡献上的疑问的更多消息，请访问我们的 解决问题清单。 <br>
The Apache Flume Team<br>
来自apach项目组。<br></p>
<p><br><br>
Flume is a distributed, reliable, and available service for efficiently collecting, aggregating, and moving large amounts of streaming event data.<br>
 (flume 是 分布是，高可靠的，高效率的服务。 flume 能够做到高效的收集，集成和移动大规模的日志数据)<br><br><br>
Version 1.5.0.1 is the sixth Flume release as an Apache top-level project. Flume 1.5.0.1 is stable, production-ready software,<br>
 and is backwards-compatible with previous versions of the Flume 1.x codeline.<br><br><br>
Apache Flume 1.5.0.1 is a maintenance release primarily meant to add support to build against Apache HBase 0.98.x. This release <br>
adds a new build profile that builds Flume against HBase 0.98.2.<br>
Flume 1.5.0.1 是可维护的版本，这个版本首要增加了基于apacheHbase0.9.8X构建的支持，这个版本也增加了新的构建描述基于HBASE0.98.x<br><br><br>
Apache BigTop 0.8.0 release will ship Flume binaries built against HBase 0.98.x.<br>
apache BIGTOP 0.8.0 版本将用于 基于hbase 0。98 的版flume的构建<br></p>
<p><br></p>
<p>Several months of active development went into this release: 123 patches were committed since 1.4.0,<br>
 representing many features, enhancements, and bug fixes. While the full change log can be found on the 1.5.0 release page (link below), <br>
 here are a few new feature highlights:<br>
 经过几个月的积极的开发，我们完成了3个分支的提交到了1.4.0 ,<br>
 完善，加强了一些功能的特性，也修改了很多bug ，可以在1.5.0 的链接中发现 全部的变更列表<br>
 下面是一些功能的概要性说明和强调的点<br><br><br>
New in-memory channel that can spill to disk   新的内存channel 使磁盘溢出的bug<br>
A new dataset sink that use Kite API to write data to HDFS and HBase 新的数据及的sink ，可以使用KITE API 写数据到HDFS和HBASE 中<br>
Support for Elastic Search HTTP API in Elastic Search Sink<br>
在可伸缩的查询sink中，增加了可伸缩的http查询的支持。 <br><br><br><br><br>
Much faster replay in the File Channel.<br>
更快的重新在文件channel 组件中<br></p>
<p><br></p>
<p><br><br>
New JMS Source   新功能 JMS source 的支持<br>
New Solr Sink with ETL capabilities 和ETL工具兼容的基于lucence 的sink 支持<br>
Updated ElasticSearch sink to support ES version 0.90 支持ESversion 0.90  的 可伸缩的搜索sink<br>
Support for secure SSL transport over Avro-RPC clients, sources &amp; sinks<br>
支持安全的ssl 全书，通过 AVRO-RPC的客户端，得source 和sinks 进行日志的搜集<br>
Support for Thrift-RPC as a transport mechanism<br>
支持thrift-rpc 作为数据传输机制 <br>
Support for embedding a Flume agent within applications<br>
在应用中支持嵌入式设备的flume代理<br>
Support for a new plugins.d directory structure for managing Flume addons<br>
支持plugins.d 目录方式，来管理flume 插件的功能<br><br><br>
Support for reading Avro files via the Spooling Directory source<br>
可以支持读写avro 文件变化的目录池的<br>
Support for writing Avro files with arbitrary schemas via the HDFS sink<br>
支持谢AVRO类型文件用二进制的编码的，写入变化hdfssink <br>
Support for ingesting Avro-serializable objects via the log4j API<br>
支持获取log4jj 的api 获取avro 序列化后的对象<br>
Improvements to the file channel to keep a backup checkpoint to avoid replays<br>
对于文件channel 听哦保持回退键差点，在做无效的重复的时候<br>
Performance improvements to the file channel, including group commit<br>
对于文件channel 性能上的提升，也包括批量的组的提交<br>
New file channel consistency check tool<br>
新的文件channle 中一致性的检查工具<br></p>
<p><br></p>
<p><br><br>
New HTTP Post Source  新的http 端口source <br>
New Spool Directory Source 新的目录池源<br>
New Multi-port Syslog Source 新的多对于syslog ，多端口<br>
New Elastic Search Sink 新的ESsink 支持<br>
New Regex Extractor Interceptor 新的规则执行器拦截<br>
File Channel Encryption 文件 文件channel 的尝试<br></p>
            </div>
                </div>