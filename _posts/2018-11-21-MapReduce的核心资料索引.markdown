---
layout:     post
title:      MapReduce的核心资料索引
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<a title="展开" href="http://blog.csdn.net/hero_hegang/article/details/9357315#" rel="nofollow">[+]</a>
<ol style="line-height:160%;margin-left:14px;"><li><a href="http://blog.csdn.net/hero_hegang/article/details/9357315#t0" rel="nofollow">名字起源</a></li><li><a href="http://blog.csdn.net/hero_hegang/article/details/9357315#t1" rel="nofollow">起源</a></li><li><a href="http://blog.csdn.net/hero_hegang/article/details/9357315#t2" rel="nofollow">诸多优点</a></li><li><a href="http://blog.csdn.net/hero_hegang/article/details/9357315#t3" rel="nofollow">架构</a>
<ol><li><a href="http://blog.csdn.net/hero_hegang/article/details/9357315#t4" rel="nofollow">HDFS</a></li><li><a href="http://blog.csdn.net/hero_hegang/article/details/9357315#t5" rel="nofollow">NameNode</a></li><li><a href="http://blog.csdn.net/hero_hegang/article/details/9357315#t6" rel="nofollow">DataNode</a></li><li><a href="http://blog.csdn.net/hero_hegang/article/details/9357315#t7" rel="nofollow">文件操作</a></li><li><a href="http://blog.csdn.net/hero_hegang/article/details/9357315#t8" rel="nofollow">Linux 集群</a></li></ol></li><li><a href="http://blog.csdn.net/hero_hegang/article/details/9357315#t9" rel="nofollow">集群系统</a></li><li><a href="http://blog.csdn.net/hero_hegang/article/details/9357315#t10" rel="nofollow">应用程序</a>
<ol><li><a href="http://blog.csdn.net/hero_hegang/article/details/9357315#t11" rel="nofollow">Hadoop系统安装于配置</a></li><li><a href="http://blog.csdn.net/hero_hegang/article/details/9357315#t12" rel="nofollow">海量数据处理平台架构介绍</a></li><li><a href="http://blog.csdn.net/hero_hegang/article/details/9357315#t13" rel="nofollow">Hadoop能解决哪些问题</a></li><li><a href="http://blog.csdn.net/hero_hegang/article/details/9357315#t14" rel="nofollow">Hadoop在国内的情景</a></li><li><a href="http://blog.csdn.net/hero_hegang/article/details/9357315#t15" rel="nofollow">Hadoop简介</a></li><li><a href="http://blog.csdn.net/hero_hegang/article/details/9357315#t16" rel="nofollow">Hadoop生态系统介绍</a></li><li><a href="http://blog.csdn.net/hero_hegang/article/details/9357315#t17" rel="nofollow">HDFS简介</a></li><li><a href="http://blog.csdn.net/hero_hegang/article/details/9357315#t18" rel="nofollow">HDFS设计原则</a></li><li><a href="http://blog.csdn.net/hero_hegang/article/details/9357315#t19" rel="nofollow">HDFS系统结构</a></li><li><a href="http://blog.csdn.net/hero_hegang/article/details/9357315#t20" rel="nofollow">HDFS文件权限</a></li><li><a href="http://blog.csdn.net/hero_hegang/article/details/9357315#t21" rel="nofollow">HDFS文件读取</a></li><li><a href="http://blog.csdn.net/hero_hegang/article/details/9357315#t22" rel="nofollow">HDFS文件写入</a></li><li><a href="http://blog.csdn.net/hero_hegang/article/details/9357315#t23" rel="nofollow">HDFS文件存储</a></li><li><a href="http://blog.csdn.net/hero_hegang/article/details/9357315#t24" rel="nofollow">HDFS文件存储结构</a></li><li><a href="http://blog.csdn.net/hero_hegang/article/details/9357315#t25" rel="nofollow">HDFS开发常用命令</a></li><li><a href="http://blog.csdn.net/hero_hegang/article/details/9357315#t26" rel="nofollow">Hadoop管理员常用命令</a></li><li><a href="http://blog.csdn.net/hero_hegang/article/details/9357315#t27" rel="nofollow">HDFS API简介</a></li><li><a href="http://blog.csdn.net/hero_hegang/article/details/9357315#t28" rel="nofollow">用Java对HDFS编程</a></li><li><a href="http://blog.csdn.net/hero_hegang/article/details/9357315#t29" rel="nofollow">Mapreduce简介</a></li><li><a href="http://blog.csdn.net/hero_hegang/article/details/9357315#t30" rel="nofollow">编写MapReduce程序的步骤</a></li><li><a href="http://blog.csdn.net/hero_hegang/article/details/9357315#t31" rel="nofollow">MapReduce模型</a></li><li><a href="http://blog.csdn.net/hero_hegang/article/details/9357315#t32" rel="nofollow">MapReduce运行步骤</a></li><li><a href="http://blog.csdn.net/hero_hegang/article/details/9357315#t33" rel="nofollow">MapReduce执行流程</a></li><li><a href="http://blog.csdn.net/hero_hegang/article/details/9357315#t34" rel="nofollow">MapReduce基本流程</a></li><li><a href="http://blog.csdn.net/hero_hegang/article/details/9357315#t35" rel="nofollow">JobTrackerJT和TaskTrackerTT简介</a></li><li><a href="http://blog.csdn.net/hero_hegang/article/details/9357315#t36" rel="nofollow">Mapreduce原理</a></li><li><a href="http://blog.csdn.net/hero_hegang/article/details/9357315#t37" rel="nofollow">使用ZooKeeper来协作JobTracker</a></li><li><a href="http://blog.csdn.net/hero_hegang/article/details/9357315#t38" rel="nofollow">Hadoop Job Scheduler</a></li><li><a href="http://blog.csdn.net/hero_hegang/article/details/9357315#t39" rel="nofollow">mapreduce的类型与格式</a></li><li><a href="http://blog.csdn.net/hero_hegang/article/details/9357315#t40" rel="nofollow">mapreduce的数据类型与java类型对应关系</a></li><li><a href="http://blog.csdn.net/hero_hegang/article/details/9357315#t41" rel="nofollow">Writable接口</a></li><li><a href="http://blog.csdn.net/hero_hegang/article/details/9357315#t42" rel="nofollow">实现自定义的mapreduce类型</a></li><li><a href="http://blog.csdn.net/hero_hegang/article/details/9357315#t43" rel="nofollow">mapreduce驱动默认的设置</a></li><li><a href="http://blog.csdn.net/hero_hegang/article/details/9357315#t44" rel="nofollow">Combiners和Partitioner编程</a></li></ol></li></ol><div style="clear:both;"></div>
<div id="article_content" class="article_content">
<div class="postTitle"><a id="ArchiveDay1_SingleDay_DayList_TitleUrl_0" class="postTitle2" href="http://www.cnblogs.com/jie465831735/archive/2013/03/06/2946994.html" rel="nofollow">MapReduce的核心资料索引</a>
</div>
<p>转自<a href="http://prinx.blog.163.com/blog/static/190115275201211128513868/" rel="nofollow">http://prinx.blog.163.com/blog/static/190115275201211128513868/</a>和<a href="http://www.cnblogs.com/jie465831735/archive/2013/03/06.html" rel="nofollow">http://www.cnblogs.com/jie465831735/archive/2013/03/06.html</a></p>
<p>按如下顺序看效果最佳: </p>
<p>1.       MapReduce Simplied Data Processing on Large Clusters</p>
<p>2.       Hadoop环境的安装 By 徐伟</p>
<p>3.       Parallel K-Means Clustering Based on MapReduce</p>
<p>4.       《Hadoop权威指南》的第一章和第二章</p>
<p>5.       迭代式MapReduce框架介绍   董的博客</p>
<p>6.       HaLoop: Efficient Iterative Data Processing on Large Clusters</p>
<p>7.       Twister: A Runtime for Iterative MapReduce</p>
<p>8.       迭代式MapReduce解决方案（一）</p>
<p>9.       迭代式MapReduce解决方案（二）</p>
<p>10.   迭代式MapReduce解决方案（三）</p>
<p>11.   Granules: A Lightweight, Streaming Runtime for Cloud Computing With Support for Map-Reduce</p>
<p>12.   On the Performance of Distributed Data Clustering Algorithms in File and Streaming Processing Systems</p>
<p>13.   Spark: Cluster Computing with Working Set</p>
<p>14.   iMapReduce: A Distributed Computing Framework for Iterative Computation</p>
<p>15.   《Hadoop权威指南》的第三章到第十章</p>
<p>16.   Map-Reduce-Merge: Simplified Relational Data Processing on Large Clusters</p>
<p>17.   Clustering Very Large Multi-dimensional Datasets with MapReduce</p>
<p>18.   HBase环境的安装 By 徐伟 + HBase 测试程序</p>
<p> </p>
<p>Ps：简单讲解一下上面的流程，MapReduce计算模型就是Google在(1)中提出来的，一定要仔细看这篇论文，我当初因为看的不够仔细走了很多的弯路。Hadoop是一个开源的MapReduce计算模型实现，按照(2)来安装，以及跑一遍Word Count程序，基本上就算是入门了。(3)这篇文章价值不大，但是可以通过其看一下K-Means算法是如何MapReduce化的，以后就可以举一反三了。(4)的作用就是加深对(1-3)的理解。从(5)开始就可以进入迭代MapReduce的子领域了，董是这方面的大牛。(6)(7)是(5)中提到的两篇论文，(5-7)都要仔细的看，把迭代MapReduce的基础打牢。(8-10)也是董的文章，加深一下对迭代MapReduce问题的理解。(11)(12)是Jaliya
 Ekanayake、Shrideep Pallickara合作的文章，他们是国外迭代MapReduce领域的发文章最多的两个人。(13)是伯克利大学的迭代MapReduce的文章，Spark是所有实验室产品中唯一已经商用推广的，赞！(14)这篇文章，我看的不是很细致，但是Collector的灵感就是来源于这篇文章。这个时候估计你已经有自己的解决方案了，要编程实现自己的设计了，需要仔细的看(15)了。(16) Map-Reduce-Merge咱们实验室曾经做过的一个问题。(17)这篇文章+Canopy算法，可以得出一些关于用MapReduce实现高质量数据抽样的思路。(18)如果需要使用HBase，可以参考这篇文章。</p>
<div class="postDesc">posted @ 2013-03-06 21:36 南宫星海 阅读(25) 评论(0) <a href="http://www.cnblogs.com/jie465831735/admin/EditPosts.aspx?postid=2946994" rel="nofollow">
编辑</a></div>
<div class="postSeparator"></div>
<div class="postTitle"><a id="ArchiveDay1_SingleDay_DayList_TitleUrl_1" class="postTitle2" href="http://www.cnblogs.com/jie465831735/archive/2013/03/06/2946985.html" rel="nofollow">研究云计算与大数据分析处理领域建议看的学术论文列表</a>
</div>
<p>转自<a href="http://cloud.dlmu.edu.cn/cloudsite/index.php?action-viewnews-itemid-123-php-1" rel="nofollow">http://cloud.dlmu.edu.cn/cloudsite/index.php?action-viewnews-itemid-123-php-1</a></p>
<div id="article_body">[1] Zhou AY. Data intensive computing-challenges of data management techniques. Communications of CCF, 2009,5(7):50.53 (in Chinese with English abstract).<br>
[2] Cohen J, Dolan B, Dunlap M, Hellerstein JM, Welton C. MAD skills: New analysis practices for big data. PVLDB, 2009,2(2): 1481.1492.<br>
[3] Schroeder B, Gibson GA. Understanding failures in petascale computers. Journal of Physics: Conf. Series, 2007,78(1):1.11.<br>
[4] Dean J, Ghemawat S. MapReduce: Simplified data processing on large clusters. In: Brewer E, Chen P, eds. Proc. of the OSDI. California: USENIX Association, 2004. 137.150.<br>
[5] Pavlo A, Paulson E, Rasin A, Abadi DJ, Dewitt DJ, Madden S, Stonebraker M. A comparison of approaches to large-scale data analysis. In: Cetintemel U, Zdonik SB, Kossmann D, Tatbul N, eds. Proc. of the SIGMOD. Rhode Island: ACM Press, 2009. 165.178.<br>
[6] Chu CT, Kim SK, Lin YA, Yu YY, Bradski G, Ng AY, Olukotun K. Map-Reduce for machine learning on multicore. In: Scholkopf B, Platt JC, Hoffman T, eds. Proc. of the NIPS. Vancouver: MIT Press, 2006. 281.288.<br>
[7] Wang CK, Wang JM, Lin XM, Wang W, Wang HX, Li HS, Tian WP, Xu J, Li R. MapDupReducer: Detecting near duplicates over massive datasets. In: Elmagarmid AK, Agrawal D, eds. Proc. of the SIGMOD. Indiana: ACM Press, 2010. 1119.1122.<br>
[8] Liu C, Guo F, Faloutsos C. BBM: Bayesian browsing model from petabyte-scale data. In: Elder JF IV, Fogelman-Soulié F, Flach PA, Zaki MJ, eds. Proc. of the KDD. Paris: ACM Press, 2009. 537.546.<br>
[9] Panda B, Herbach JS, Basu S, Bayardo RJ. PLANET: Massively parallel learning of tree ensembles with MapReduce. PVLDB, 2009,2(2):1426.1437.<br>
[10] Lin J, Schatz M. Design patterns for efficient graph algorithms in MapReduce. In: Rao B, Krishnapuram B, Tomkins A, Yang Q, eds. Proc. of the KDD. Washington: ACM Press, 2010. 78.85.<br>
[11] Zhang CJ, Ma Q, Wang XL, Zhou AY. Distributed SLCA-based XML keyword search by Map-Reduce. In: Yoshikawa M, Meng XF, Yumoto T, Ma Q, Sun LF, Watanabe C, eds. Proc. of the DASFAA. Tsukuba: Springer-Verlag, 2010. 386.397.<br>
[12] Stupar A, Michel S, Schenkel R. RankReduce—Processing K-nearest neighbor queries on top of MapReduce. In: Crestani F, Marchand-Maillet S, Chen HH, Efthimiadis EN, Savoy J, eds. Proc. of the SIGIR. Geneva: ACM Press, 2010. 13.18.<br>
[13] Wang GZ, Salles MV, Sowell B, Wang X, Cao T, Demers A, Gehrke J, White W. Behavioral simulations in MapReduce. PVLDB, 2010,3(1-2):952.963.<br>
[14] Gunarathne T, Wu TL, Qiu J, Fox G. Cloud computing paradigms for pleasingly parallel biomedical applications. In: Hariri S, Keahey K, eds. Proc. of the HPDC. Chicago: ACM Press, 2010. 460−469.<br>
[15] Delmerico JA, Byrnesy NA, Brunoz AE, Jonesz MD, Galloz SM, Chaudhary V. Comparing the performance of clusters, hadoop, and active disks on microarray correlation computations. In: Yang YY, Parashar M, Muralidhar R, Prasanna VK, eds. Proc. of the HiPC.
 Kochi: IEEE Press, 2009. 378−387.<br>
[16] Das S, Sismanis Y, Beyer KS, Gemulla R, Haas PJ, McPherson J. Ricardo: Integrating R and hadoop. In: Elmagarmid AK, Agrawal D, eds. Proc. of the SIGMOD. Indiana: ACM Press, 2010. 987−998.<br>
[17] Wegener D, Mock M, Adranale D, Wrobel S. Toolkit-Based high-performance data mining of large data on MapReduce clusters. In: Saygin Y, Yu JX, Kargupta H, Wang W, Ranka S, Yu PS, Wu XD, eds. Proc. of the ICDM Workshop. Washington: IEEE Computer Society,
 2009. 296−301.<br>
[18] Kovoor G, Singer J, Lujan M. Building a Java Map-Reduce framework for multi-core architectures. In: Ayguade E, Gioiosa R, Stenstrom P, Unsal O, eds. Proc. of the HiPEAC. Pisa: HiPEAC Endowment, 2010. 87−98.<br>
[19] De Kruijf M, Sankaralingam K. MapReduce for the cell broadband engine architecture. IBM Journal of Research and Development, 2009,53(5):1−12.<br>
[20] Becerra Y, Beltran V, Carrera D, Gonzalez M, Torres J, Ayguade E. Speeding up distributed MapReduce applications using hardware accelerators. In: Barolli L, Feng WC, eds. Proc. of the ICPP. Vienna: IEEE Computer Society, 2009. 42−49.<br>
[21] Ranger C, Raghuraman R, Penmetsa A, Bradski G, Kozyrakis C. Evaluating MapReduce for multi-core and multiprocessor systems. In: Dally WJ, ed. Proc. of the HPCA. Phoenix: IEEE Computer Society, 2007. 13−24.<br>
[22] Ma WJ, Agrawal G. A translation system for enabling data mining applications on GPUs. In: Zhou P, ed. Proc. of the Supercomputing (SC). New York: ACM Press, 2009. 400−409.<br>
[23] He BS, Fang WB, Govindaraju NK, Luo Q, Wang TY. Mars: A MapReduce framework on graphics processors. In: Moshovos A, Tarditi D, Olukotun K, eds. Proc. of the PACT. Ontario: ACM Press, 2008. 260−269.<br>
[24] Stuart JA, Chen CK, Ma KL, Owens JD. Multi-GPU volume rendering using MapReduce. In: Hariri S, Keahey K, eds. Proc. of the MapReduce Workshop (HPDC 2010). New York: ACM Press, 2010. 841−848.<br>
[25] Hong CT, Chen DH, Chen WG, Zheng WM, Lin HB. MapCG: Writing parallel program portable between CPU and GPU. In: Salapura V, Gschwind M, Knoop J, eds. Proc. of the PACT. Vienna: ACM Press, 2010. 217−226.<br>
[26] Jiang W, Ravi VT, Agrawal G. A Map-Reduce system with an alternate API for multi-core environments. In: Chiba T, ed. Proc. of the CCGRID. Melbourne: IEEE Press, 2010. 84−93.<br>
[27] Liao HJ, Han JZ, Fang JY. Multi-Dimensional index on hadoop distributed file system. In: Xu ZW, ed. Proc. of the Networking, Architecture, and Storage (NAS). Macau: IEEE Computer Society, 2010. 240−249.<br>
[28] Zou YQ, Liu J, Wang SC, Zha L, Xu ZW. CCIndex: A complemental clustering index on distributed ordered tables for multi- dimensional range queries. In: Ding C, Shao ZY, Zheng R, eds. Proc. of the NPC. Zhengzhou: Springer-Verlag, 2010. 247−261.<br>
[29] Zhang SB, Han JZ, Liu ZY, Wang K, Feng SZ. Accelerating MapReduce with distributed memory cache. In: Huang XX, ed. Proc. of the ICPADS. Shenzhen: IEEE Press, 2009. 472−478.<br>
[30] Dittrich J, Quian′e-Ruiz JA, Jindal A, Kargin Y, Setty V, Schad J. Hadoop++: Making a yellow elephant run like a cheetah (without it even noticing). PVLDB, 2010,3(1-2):518−529.<br>
[31] Chen ST. Cheetah: A high performance, custom data warehouse on top of MapReduce. PVLDB, 2010,3(1-2):1459−1468.<br>
[32] Iu MY, Zwaenepoel W. HadoopToSQL: A MapReduce query optimizer. In: Morin C, Muller G, eds. Proc. of the EuroSys. Paris: ACM Press, 2010. 251−264.<br>
[33] Blanas S, Patel JM, Ercegovac V, Rao J, Shekita EJ, Tian YY. A comparison of join algorithms for log processing in MapReduce. In: Elmagarmid AK, Agrawal D, eds. Proc. of the SIGMOD. Indiana: ACM Press, 2010. 975−986.<br>
[34] Zhou MQ, Zhang R, Zeng DD, Qian WN, Zhou AY. Join optimization in the MapReduce environment for column-wise data store. In: Fang YF, Huang ZX, eds. Proc. of the SKG. Ningbo: EEE Computer Society, 2010. 97−104.<br>
[35] Afrati FN, Ullman JD. Optimizing joins in a Map-Reduce environment. In: Manolescu I, Spaccapietra S, Teubner J, Kitsuregawa M, Léger A, Naumann F, Ailamaki A, Ozcan F, eds. Proc. of the EDBT. Lausanne: ACM Press, 2010. 99−110.<br>
[36] Sandholm T, Lai K. MapReduce optimization using regulated dynamic prioritization. In: Douceur JR, Greenberg AG, Bonald T, Nieh J, eds. Proc. of the SIGMETRICS. Seattle: ACM Press, 2009. 299.310.<br>
[37] Hoefler T, Lumsdaine A, Dongarra J. Towards efficient MapReduce using MPI. In: Oster P, ed. Proc. of the EuroPVM/MPI. Berlin: Springer-Verlag, 2009. 240.249.<br>
[38] Nykiel T, Potamias M, Mishra C, Kollios G, Koudas N. MRShare: Sharing across multiple queries in MapReduce. PVLDB, 2010, 3(1-2):494.505.<br>
[39] Kambatla K, Rapolu N, Jagannathan S, Grama A. Asynchronous algorithms in MapReduce. In: Moreira JE, Matsuoka S, Pakin S, Cortes T, eds. Proc. of the CLUSTER. Crete: IEEE Press, 2010. 245.254.<br>
[40] Polo J, Carrera D, Becerra Y, Torres J, Ayguade E, Steinder M, Whalley I. Performance-Driven task co-scheduling for MapReduce environments. In: Tonouchi T, Kim MS, eds. Proc. of the IEEE Network Operations and Management Symp. (NOMS). Osaka: IEEE Press,
 2010. 373.380.<br>
[41] Zaharia M, Konwinski A, Joseph AD, Katz R, Stoica I. Improving MapReduce performance in heterogeneous environments. In: Draves R, van Renesse R, eds. Proc. of the ODSI. Berkeley: USENIX Association, 2008. 29.42.<br>
[42] Xie J, Yin S, Ruan XJ, Ding ZY, Tian Y, Majors J, Manzanares A, Qin X. Improving MapReduce performance through data placement in heterogeneous hadoop clusters. In: Taufer M, Rünger G, Du ZH, eds. Proc. of the Workshop on Heterogeneity in Computing (IPDPS
 2010). Atlanta: IEEE Press, 2010. 1.9.<br>
[43] Polo J, Carrera D, Becerra Y, Beltran V, Torres J, Ayguade E. Performance management of accelerated MapReduce workloads in heterogeneous clusters. In: Qin F, Barolli L, Cho SY, eds. Proc. of the ICPP. San Diego: IEEE Press, 2010. 653.662.<br>
[44] Papagiannis A, Nikolopoulos DS. Rearchitecting MapReduce for heterogeneous multicore processors with explicitly managed memories. In: Qin F, Barolli L, Cho SY, eds. Proc. of the ICPP. San Diego: IEEE Press, 2010. 121.130.<br>
[45] Jiang DW, Ooi BC, Shi L, Wu S. The performance of MapReduce: An in-depth study. PVLDB, 2010,3(1-2):472.483.<br>
[46] Berthold J, Dieterle M, Loogen R. Implementing parallel Google Map-Reduce in Eden. In: Sips HJ, Epema DHJ, Lin HX, eds. Proc. of the Euro-Par. Delft: Springer-Verlag, 2009. 990.1002.<br>
[47] Verma A, Zea N, Cho B, Gupta I, Campbell RH. Breaking the MapReduce stage barrier. In: Moreira JE, Matsuoka S, Pakin S, Cortes T, eds. Proc. of the CLUSTER. Crete: IEEE Press, 2010. 235.244.<br>
[48] Yang HC, Dasdan A, Hsiao RL, Parker DS. Map-Reduce-Merge simplified relational data processing on large clusters. In: Chan CY, Ooi BC, Zhou AY, eds. Proc. of the SIGMOD. Beijing: ACM Press, 2007. 1029.1040.<br>
[49] Seo SW, Jang I, Woo KC, Kim I, Kim JS, Maeng S. HPMR: Prefetching and pre-shuffling in shared MapReduce computation environment. In: Rana O, Tang FL, Kosar T, eds. Proc. of the CLUSTER. New Orleans: IEEE Press, 2009. 1.8.<br>
[50] Babu S. Towards automatic optimization of MapReduce programs. In: Kansal A, ed. Proc. of the ACM Symp. on Cloud Computing (SoCC). New York: ACM Press, 2010. 137.142.<br>
[51] Olston C, Reed B, Srivastava U, Kumar R, Tomkins A. Pig Latin: A not-so-foreign language for data processing. In: Wang JTL, ed. Proc. of the SIGMOD. Vancouver: ACM Press, 2008. 1099.1110.<br>
[52] Isard M, Budiu M, Yu Y, Birrell A, Fetterly D. Dryad: Distributed data-parallel programs from sequential building blocks. ACM SIGOPS Operating Systems Review, 2007,41(3):59.72.<br>
[53] Isard M, Yu Y. Distributed data-parallel computing using a high-level programming language. In: Cetintemel U, Zdonik SB, Kossmann D, Tatbul N, eds. Proc. of the SIGMOD. Rhode Island: ACM Press, 2009. 987.994.<br>
[54] Chaiken R, Jenkins B, Larson P, Ramsey B, Shakib D, Weaver S, Zhou JR. SCOPE: Easy and efficient parallel processing of massive data sets. PVLDB, 2008,1(2):1265.1276.<br>
[55] Condie T, Conway N, Alvaro P, Hellerstein JM, Gerth J, Talbot J, Elmeleegy K, Sears R. Online aggregation and continuous query support in MapReduce. In: Elmagarmid AK, Agrawal D, eds. Proc. of the SIGMOD. Indianapolis: ACM Press, 2010. 1115.1118.<br>
[56] Thusoo A, Sarma JS, Jain N, Shao Z, Chakka P, Anthony S, Liu H, Wyckoff P, Murthy R. Hive a warehousing solution over a MapReduce framework. PVLDB, 2009,2(2):938.941.<br>
[57] Ghoting A, Pednault E. Hadoop-ML: An infrastructure for the rapid implementation of parallel reusable analytics. In: Culotta A, ed. Proc. of the Large-Scale Machine Learning: Parallelism and Massive Datasets Workshop (NIPS 2009). Vancouver: MIT Press,
 2009. 6.<br>
[58] Yang C, Yen C, Tan C, Madden S. Osprey: Implementing MapReduce-style fault tolerance in a shared-nothing distributed database. In: Li FF, Moro MM, Ghandeharizadeh S, Haritsa JR, Weikum G, Carey MJ, Casati F, Chang EY, Manolescu I, Mehrotra S, Dayal U,
 Tsotras VJ, eds. Proc. of the ICDE. Long Beach: IEEE Press, 2010. 657.668.<br>
[59] Abouzeid A, Bajda-Pawlikowski K, Abadi D, Silberschatz A, Rasin A. HadoopDB: An architectural hybrid of MapReduce and DBMS technologes for analytical workloads. PVLDB, 2009,2(1):922.933.<br>
[60] Abouzied A, Bajda-Pawlikowski K, Huang JW, Abadi DJ, Silberschatz A. HadoopDB in action: Building real world applications. In: Elmagarmid AK, Agrawal D, eds. Proc. of the SIGMOD. Indiana: ACM Press, 2010. 1111.1114.<br>
[61] Friedman E, Pawlowski P, Cieslewicz J. SQL/MapReduce: A practical approach to self describing, polymorphic, and parallelizable user defined functions. PVLDB, 2009,2(2):1402.1413.<br>
[62] Stonebraker M, Abadi D, DeWitt DJ, Maden S, Paulson E, Pavlo A, Rasin A. MapReduce and parallel DBMSs: Friends or foes? Communications of the ACM, 2010,53(1):64.71.<br>
[63] Dean J, Ghemawat S. MapReduce: A flexible data processing tool. Communications of ACM, 2010,53(1):72.77.<br>
[64] Xu Y, Kostamaa P, Gao LK. Integrating hadoop and parallel DBMS. In: Elmagarmid AK, Agrawal D, eds. Proc. of the SIGMOD. Indianapolis: ACM Press, 2010. 969.974.<br>
[65] Thusoo A, Shao Z, Anthony S, Borthakur D, Jain N, Sarma JS, Murthy R, Liu H. Data warehousing and analytics infrastructure at facebook. In: Elmagarmid AK, Agrawal D, eds. Proc. of the SIGMOD. Indianapolis: ACM Press, 2010. 1013.1020.<br>
[66] Mcnabb AW, Monson CK, Seppi KD. MRPSO: MapReduce particle swarm optimization. In: Ryan C, Keijzer M, eds. Proc. of the GECCO. Atlanta: ACM Press, 2007. 177.185.<br>
[67] Kang U, Tsourakakis CE, Faloutsos C. PEGASUS: A peta-scale graph mining system—Implementation and observations. In: Wang W, Kargupta H, Ranka S, Yu PS, Wu XD, eds. Proc. of the ICDM. Miami: IEEE Computer Society, 2009. 229.238.<br>
[68] Kang S, Bader DA. Large scale complex network analysis using the hybrid combination of a MapReduce cluster and a highly multithreaded system. In: Taufer M, Rünger G, Du ZH, eds. Proc. of the Workshops and Phd Forum (IPDPS 2010). Atlanta: IEEE Presss, 2010.
 11.19.<br>
[69] Logothetis D, Yocum K. AdHoc data processing in the cloud. PVLDB, 2008,1(1):1472.1475.<br>
[70] Olston C, Bortnikov E, Elmeleegy K, Junqueira F, Reed B. Interactive analysis of WebScale data. In: DeWitt D, ed. Proc. of the CIDR. Asilomar: Online
<a href="http://www.crdrdb.org/" rel="nofollow">www.crdrdb.org</a>, 2009.<br>
[71] Bose JH, Andrzejak A, Hogqvist M. Beyond online aggregation: Parallel and incremental data mining with online Map-Reduce. In: Tanaka K, Zhou XF, Zhang M, Jatowt A, eds. Proc. of the Workshop on Massive Data Analytics on the Cloud (WWW 2010). Raleigh: ACM
 Press, 2010. 3.<br>
[72] Kumar V, Andrade H, Gedik B, Wu KL. DEDUCE: At the intersection of MapReduce and stream processing. In: Manolescu I, Spaccapietra S, Teubner J, Kitsuregawa M, Léger A, Naumann F, Ailamaki A, Ozcan F, eds. Proc. of the EDBT. Lausanne: ACM Press, 2010. 657.662.<br>
[73] Abramson D, Dinh MN, Kurniawan D, Moench B, DeRose L. Data centric highly parallel debugging. In: Hariri S, Keahey K, eds. Proc. of the HPDC. Chicago: ACM Press, 2010. 119.129.<br>
[74] Morton K, Friesen A, Balazinska M, Grossman D. Estimating the progress of MapReduce pipelines. In: Li FF, Moro MM, Ghandeharizadeh S, et al., eds. Proc. of the ICDE. Long Beach: IEEE Press, 2010. 681.684.<br>
[75] Morton K, Balazinska M, Grossman D. ParaTimer: A progress indicator for MapReduce DAGs. In: Elmagarmid AK, Agrawal D, eds. Proc. of the SIGMOD. Indianapolis: ACM Press, 2010. 507.518.<br>
[76] Lang W, Patel JM. Energy management for MapReduce clusters. PVLDB, 2010,3(1-2):129.139.<br>
[77] Wieder A, Bhatotia P, Post A, Rodrigues R. Brief announcement: Modeling MapReduce for optimal execution in the cloud. In: Richa AW, Guerraoui R, eds. Proc. of the PODC. Zurich: ACM Press, 2010. 408.409.<br>
[78] Zheng Q. Improving MapReduce fault tolerance in the cloud. In: Taufer M, Rünger G, Du ZH, eds. Proc. of the Workshops and Phd Forum (IPDPS 2010). Atlanta: IEEE Presss, 2010. 1.6.<br>
[79] Groot S. Jumbo: Beyond MapReduce for workload balancing. In: Mylopoulos J, Zhou LZ, Zhou XF, eds. Proc. of the PhD Workshop (VLDB 2010). Singapore: VLDB Endowment, 2010. 7.12.<br>
[80] Chatziantoniou D, Tzortzakakis E. ASSET queries: A declarative alternative to MapReduce. SIGMOD Record, 2009,38(2):35.41.<br>
[81] Bu YY, Howe B, Balazinska M, Ernst MD. HaLoop: Efficient iterative data processing on large clusters. PVLDB, 2010,3(1-2): 285−296.<br>
[82] Wang HJ, Qin XP, Zhang YS, Wang S, Wang ZW. LinearDB: A relational approach to make data warehouse scale like MapReduce. In: Yu JX, Kim MH, Unland R, eds. Proc. of the DASFAA. Hong Kong: Springer-Verlag, 2011. 306−320</div>
<div class="postDesc">posted @ 2013-03-06 21:30 南宫星海 阅读(17) 评论(0) <a href="http://www.cnblogs.com/jie465831735/admin/EditPosts.aspx?postid=2946985" rel="nofollow">
编辑</a></div>
<div class="postSeparator"></div>
<div class="postTitle"><a id="ArchiveDay1_SingleDay_DayList_TitleUrl_2" class="postTitle2" href="http://www.cnblogs.com/jie465831735/archive/2013/03/06/2946982.html" rel="nofollow">云计算核心论文 .</a>
</div>
<p>转自 <a href="http://blog.csdn.net/zhaomirong/article/details/7832215" rel="nofollow">
http://blog.csdn.net/zhaomirong/article/details/7832215</a></p>
<div class="article_content"><span style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25px;"><span><span>Google <br>
1. nosqldbs-NOSQL Introduction and Overview <br>
2. system and method for data distribution(2009) <br>
3. System and method for large-scale data processing using an application-independent framework(2010) <br>
4. MapReduce: Simplified Data Processing on Large Clusters; <br>
5. MapReduce-- a flexible data processing tool(2010) <br>
6. Map-Reduce-Merge: Simplified Relational Data Processing on Large Clusters <br>
7. MapReduce and Parallel DBMSs--Friends or Foes(2010) <br>
8. Presentation:MapReduce and Parallel DBMSs:Together at Last (2010) <br>
9. Twister: A Runtime for Iterative MapReduce(2010) <br>
10. MapReduce Online(2009) <br>
11. Megastore: Providing Scalable, Highly Available Storage for Interactive Services (2011,CIDR) <br>
12. Interpreting the Data:Parallel Analysis with Sawzall <br>
13. Dapper, a Large-Scale Distributed Systems Tracing Infrastructure (technical  report 2010) <br>
14. Large-scale Incremental Processing Using Distributed Transactions and Notifications(2010) <br>
15. Improving MapReduce Performance in Heterogeneous Environments <br>
16. Dremel: Interactive Analysis of WebScale Datasets(2011) <br>
17. Large-scale Incremental Processing Using Distributed Transactions and Notifications <br>
18. Chukwa: a scalable cloud monitoring System (presentation) <br>
19. The Chubby lock service for loosely-coupled distributed systems <br>
20. Paxos Made Simple(2001,Lamport) <br>
21. Fast Paxos(2006) <br>
22. Paxos Made Live - An Engineering Perspective(2007) <br>
23. Classic Paxos vs. Fast Paxos: Caveat Emptor <br>
24. On the Coordinator’s Rule for Fast Paxos(2005) <br>
25. Paxos  made code:Implementing a high throughput Atomic Broadcast (2009) <br>
26. Bigtable: A Distributed Storage System for Structured Data(2006) <br>
27. The Google File System <br><br>
Google patent papers <br>
1. Data processing system and method for financial debt instruments(1999) <br>
2. Data processing system and method to enforce payment of royalties when copying softcopy books(1996) <br>
3. Data processing systems and methods(2005) <br>
4. Large-scale data processing in a distributed and parallel processing environment(2010) <br>
5. METHODS AND SYSTEMS FOR MANAGEMENT OF DATA() <br>
6. SEARCH OVER STRUCTURED DATA(2011) <br>
7. System and method for maintaining replicated data coherency in a data processing system(1995) <br>
8. System and method of using data mining prediction methodology(2006) <br>
9. System and Methodology for Data Processing Combining Stream Processing and spreadsheet computation(2011) <br>
10. Patent Factor index report of system and method of using data mining prediction methodology <br>
11. Pregel: A System for Large-Scale Graph Processing(2010) <br><br>
Hadoop <br>
1. A simple totally ordered broadcast protocol <br>
2. ZooKeeper: Wait-free coordination for Internet-scale systems <br>
3. Zab: High-performance broadcast for primary-backup systems(2011) <br>
4. wait-free syschronization(1991) <br>
5. ON SELF-STABILIZING WAIT-FREE CLOCK SYNCHRONIZATION(1997) <br>
6. Wait-free clock synchronization(ps format) <br>
7. Programming with ZooKeeper - A basic tutorial <br>
8. Hive – A Petabyte Scale Data Warehouse Using Hadoop <br>
9. Thrift: Scalable Cross-Language Services Implementation(Facebook) <br>
10. Hive other files: HiveMetaStore class picture, Chinese docs <br>
11. Scaling out data preprocessing with Hive (2011) <br>
12. HBase The Definitive Guide - 2011 <br>
13. Nova: Continuous Pig/Hadoop Workflows(yahoo,2011) <br>
14. Pig Latin: A Not-So-Foreign Language for Data Processing(2008) <br>
15. Analyzing Massive Astrophysical Datasets: Can Pig/Hadoop or a Relational DBMS Help?(2009) <br>
a. Some docs about HStreaming,Zebra <br>
16. HIPI: A Hadoop Image Processing Interface for Image-based MapReduce Tasks <br>
17. System Anomaly Detection in Distributed Systems through MapReduce-Based Log Analysis(2010) <br>
18. Benchmarking Cloud Serving Systems with YCSB(2010) <br>
19. Low-Latency, High-Throughput Access to Static Global Resources within the Hadoop Framework (2009) <br><br>
SmallFile Combine in hadoop world <br>
1. TidyFS: A Simple and Small Distributed File System(Microsoft) <br>
2. Improving the storage efficiency of small files in cloud storage(chinese,2011) <br>
3. Comparing Hadoop and Fat-Btree Based Access Method for Small File I/O Applications(2010) <br>
4. RCFile: A Fast and Space-efficient Data Placement Structure in MapReduce-based Warehouse Systems(Facebook) <br>
5. A Novel Approach to Improving the Efficiency of Storing and Accessing Small Files on Hadoop: a Case Study by PowerPoint Files(IBM,2010) <br><br>
Job schedule <br>
1. Job Scheduling for Multi-User MapReduce Clusters(Facebook) <br>
2. MapReduce Scheduler Using Classifiers for Heterogeneous Workloads(2011) <br>
3. Performance-Driven Task Co-Scheduling for MapReduce Environments <br>
4. Towards a Resource Aware Scheduler in Hadoop(2009) <br>
5. Delay Scheduling: A Simple Technique for Achieving <br>
6. Locality and Fairness in Cluster Scheduling(yahoo,2010) <br>
7. Dynamic Proportional Share Scheduling in Hadoop(HP) <br>
8. Adaptive Task Scheduling for MultiJob MapReduce Environments(2010) <br>
9. A Dynamic MapReduce Scheduler for Heterogeneous Workloads(2009) <br><br>
HStreaming <br>
1. HStreaming Cloud Documentation <br>
2. S4: Distributed Stream Computing Platform(yahoo,2010) <br>
3. Complex Event Processing(2009) <br>
4. Hstreaming : http://www.hstreaming.com/resources/manuals/ <br>
5. StreamBase: http://streambase.com/developers-docs-pdfindex.htm <br>
6. Twitter storm: http://www.infoq.com/cn/news/2011/09/twitter-storm-real-time-hadoop <br>
7. Bulk Synchronous Parallel(BSP) computing <br>
8. MPI <br><br>
SQL/Mapreduce <br>
1. Aster Data whilepaper:Deriving Deep Insights from Large Datasets with SQL-MapReduce (2004) <br>
2. SQL/MapReduce: A practical approach to self-describing,polymorphic, and parallelizable user-defined functions(2009,aster) <br>
3. HadoopDB: An Architectural Hybrid of MapReduce and DBMS Technologies for Analytical Workloads(2009) <br>
4. HadoopDB in Action: Building Real World Applications(2010) <br>
5. Aster Data presentation: Making Advanced Analytics on Big Data Fast and Easy(2010) <br>
6. A Scalable, Predictable Join Operator for <br>
7. Highly Concurrent Data Warehouses(2009) <br>
8. Cheetah: A High Performance, Custom Data Warehouse on Top of MapReduce(2010) <br>
9. Greenplum whilepaper:A Unified Engine for RDBMS and MapReduce(2004) <br>
10. A Comparison of Approaches to Large-Scale Data Analysis(2009) <br>
11. MAD Skills: New Analysis Practices for Big Data (2009) <br>
12. C Store A Column oriented DBMS(2005) <br>
13. Distributed Aggregation for Data-Parallel Computing: Interfaces and Implementations(Microsoft) <br><br>
Microsoft <br>
1. Dryad: Distributed Data-Parallel Programs from Sequential Building Blocks (2007) <br><br>
Amazon <br>
1. Dynamo: Amazon’s Highly Available Key-value Store(2007) <br>
2. Efficient Reconciliation and Flow Control for Anti-Entropy Protocols <br>
3. The Eucalyptus Open-source Cloud-computing System <br>
4. Eucalyptus: An Open-source Infrastructure for Cloud Computing(presentation) <br>
5. Eucalyptus : A Technical Report on an Elastic Utility Computing Archietcture Linking Your Programs to Useful Systems (2008) <br>
6. Zephyr: Live Migration in Shared Nothing Databases for Elastic Cloud Platforms(2011) <br>
7. Database-Agnostic Transaction Support for Cloud Infrastructures <br>
8. CloudScale: Elastic Resource Scaling for Multi-Tenant Cloud Systems(2011) <br>
9. ELT: Efficient Log-based Troubleshooting System for Cloud Computing Infrastructures <br><br>
Books <br>
1. Distributed Systems Concepts and Design (5th Edition) <br>
2. Principles of Computer Systems (7-11) <br>
3. Distributed system(chapter) <br>
4. Data-Intensive Text Processing with MapReduce (2010) <br>
5. Hadoop in Action <br>
6. 21 Recipes for Mining Twitter <br>
7. Hadoop.The.Definitive.Guide.2nd.Edition <br>
8. Pro hadoop <br><br>
Other papers about Distributed system <br>
1. Flexible Update Propagation for Weakly Consistent Replication(1997) <br>
2. Providing High Availability Using Lazy Replication(1992) <br>
3. Managing Update Conflicts in Bayou,a Weakly Connected Replicated Storage System(1995) <br>
4. XMIDDLE: A Data-Sharing Middleware for Mobile Computing(2002) <br>
5. design and implementation of sun network filesystem <br>
6. Chord: A Scalable Peertopeer Lookup Service for Internet Applications(2001) <br>
7. A Survey and Comparison of Peer-to-Peer Overlay Network Schemes(2004) <br>
8. Tapestry: An Infrastructure for Fault-tolerant Wide-area Location and Routing(2001) <br><br>
BI <br>
1. 21 Recipes for Mining Twitter(Book) <br>
2. Web Data Mining(Book) <br>
3. Web Mining and Social Networking(Book) <br>
4. mining the social web(book) <br>
5. TEXTUAL BUSINESS INTELLIGENCE (Inmon) <br>
6. Social Network Analysis and Mining for Business Applications(yahoo,2011) <br>
7. Data Mining in Social Networks(2002) <br>
8. Natural Language Processing with Python(book) <br>
9. data_mining-10_methods(Chinese editation) <br>
10. Mahout in Action(Book) <br>
11. Text Mining Infrastructure in R(2008) <br>
12. Text Mining Handbook(2010) <br><br>
Web search engine <br>
1. Building Efficient Multi-Threaded Search Nodes(Yahoo,2010) <br>
2. The Anatomy of a Large-Scale Hypertextual Web Search Engine(google) </span></span></span>
</div>
<div class="postDesc">posted @ 2013-03-06 21:29 南宫星海 阅读(17) 评论(0) <a href="http://www.cnblogs.com/jie465831735/admin/EditPosts.aspx?postid=2946982" rel="nofollow">
编辑</a></div>
<div class="postSeparator"></div>
<div class="postTitle"><a id="ArchiveDay1_SingleDay_DayList_TitleUrl_3" class="postTitle2" href="http://www.cnblogs.com/jie465831735/archive/2013/03/06/2946964.html" rel="nofollow">Hadoop（初步）</a>
</div>
<p><span style="font-size:14px;"><span style="font-family:'microsoft yahei';">Hadoop</span></span></p>
<p> </p>
<div>
<div>
<div>
<p><span style="font-size:14px;"><span style="font-family:'microsoft yahei';">一个分布式系统基础架构，由Apache基金会开发。用户可以在不了解分布式底层细节的情况下，开发分布式程序。充分利用集群的威力高速运算和存储。Hadoop实现了一个分布式文件系统（Hadoop Distributed File System），简称HDFS。HDFS有着高容错性的特点，并且设计用来部署在低廉的（low-cost）硬件上。而且它提供高传输率（high
 throughput）来访问应用程序的数据，适合那些有着超大数据集（large data set）的应用程序。HDFS放宽了（relax）POSIX的要求（requirements）这样可以流的形式访问（streaming access）文件系统中的数据。</span></span></p>
</div>
</div>
</div>
<p> </p>
<h2><a name="t0"></a><span style="font-size:14px;"><span style="font-family:'microsoft yahei';"><a name="sub908354_1"></a><span>名字起源</span></span></span></h2>
<p> </p>
<div><span style="font-size:14px;"><span style="font-family:'microsoft yahei';">Hadoop<sup><span style="font-size:12px;">[1]</span></sup><a name="ref_%5B1%5D_908354"></a>这 个名字不是一个缩写，它是一个虚构的名字。该项目的创建者，Doug Cutting如此解释Hadoop的得名：“这个名字是我孩子给一个棕黄色的大象样子的填充玩具命名的。我的命名标准就是简短，容易发音和拼写，没有太
 多的意义，并且不会被用于别处。小孩子是这方面的高手。”[Hadoop: The Definitive Guide]</span></span></div>
<p> </p>
<h2><a name="t1"></a><span style="font-size:14px;"><span style="font-family:'microsoft yahei';"><span>起源</span></span></span></h2>
<p> </p>
<div><span style="font-size:14px;"><span style="font-family:'microsoft yahei';">Hadoop 由 Apache Software Foundation 公司于 2005 年秋天作为 Lucene的子</span></span><br><div style="width:250px;">
<p><span style="font-size:14px;"><span style="font-family:'microsoft yahei';">  Hadoop logo</span></span></p>
</div>
<span style="font-size:14px;"><span style="font-family:'microsoft yahei';">项目 Nutch的一部分正式引入。它受到最先由 Google Lab 开发的 Map/Reduce 和 Google File System(GFS) 的启发。2006 年 3 月份，Map/Reduce 和 Nutch Distributed File System (NDFS) 分别被纳入称为 Hadoop 的项目中。</span></span></div>
<p> </p>
<div><span style="font-size:14px;"><span style="font-family:'microsoft yahei';">Hadoop 是最受欢迎的在 Internet 上对搜索关键字进行内容分类的工具，但它也可以解决许多要求极大伸缩性的问题。例如，如果您要 grep 一个 10TB 的巨型文件，会出现什么情况？在传统的系统上，这将需要很长的时间。但是 Hadoop 在设计时就考虑到这些问题，采用并行执行机制，因此能大大提高效率。</span></span></div>
<p> </p>
<h2><a name="t2"></a><span style="font-size:14px;"><span style="font-family:'microsoft yahei';"><span>诸多优点</span></span></span></h2>
<p> </p>
<div><span style="font-size:14px;"><span style="font-family:'microsoft yahei';">Hadoop 是一个能够对大量数据进行分布式处理的软件框架。但是 Hadoop 是以一种可靠、高效、可伸缩的方式进行处理的。Hadoop 是可靠的，因为它假设计算元素和存储会失败，因此它维护多个工作数据副本，确保能够针对失败的节点重新分布处理。Hadoop 是高效的，因为它以并行的方式工作，通过并行处理加快处理速度。Hadoop 还是可伸缩的，能够处理 PB
 级数据。此外，Hadoop 依赖于社区服务器，因此它的成本比较低，任何人都可以使用。</span></span></div>
<p> </p>
<div><span style="font-size:14px;"><span style="font-family:'microsoft yahei';">Hadoop是一个能够让用户轻松架构和使用的分布式计算平台。用户可以轻松地在Hadoop上开发和运行处理海量数据的应用程序。它主要有以下几个优点：</span></span></div>
<p> </p>
<div><span style="font-size:14px;"><span style="font-family:'microsoft yahei';">⒈高可靠性。Hadoop按位存储和处理数据的能力值得人们信赖。</span></span></div>
<p> </p>
<div><span style="font-size:14px;"><span style="font-family:'microsoft yahei';">⒉高扩展性。Hadoop是在可用的计算机集簇间分配数据并完成计算任务的，这些集簇可以方便地扩展到数以千计的节点中。</span></span></div>
<p> </p>
<div><span style="font-size:14px;"><span style="font-family:'microsoft yahei';">⒊高效性。Hadoop能够在节点之间动态地移动数据，并保证各个节点的动态平衡，因此处理速度非常快。</span></span></div>
<p> </p>
<div><span style="font-size:14px;"><span style="font-family:'microsoft yahei';">⒋高容错性。Hadoop能够自动保存数据的多个副本，并且能够自动将失败的任务重新分配。</span></span></div>
<p> </p>
<div><span style="font-size:14px;"><span style="font-family:'microsoft yahei';">Hadoop带有用 Java 语言编写的框架，因此运行在 Linux 生产平台上是非常理想的。Hadoop 上的应用程序也可以使用其他语言编写，比如 C++。</span></span></div>
<p> </p>
<h2><a name="t3"></a><span style="font-size:14px;"><span style="font-family:'microsoft yahei';"><span>架构</span></span></span></h2>
<p> </p>
<div><span style="font-size:14px;"><span style="font-family:'microsoft yahei';">Hadoop 有许多元素构成。其最底部是 Hadoop Distributed File Syst</span></span><span style="font-size:14px;"><span style="font-family:'microsoft yahei';">em<sup><span style="font-size:12px;">[2]</span></sup>（HDFS），它存储
 Hadoop 集群中所有存储节点上的文件。HDFS（对于本文）的上一层是 MapReduce 引擎，该引擎由 JobTrackers 和 TaskTrackers 组成。</span></span></div>
<p> </p>
<h3><a name="t4"></a><span style="font-size:14px;"><span style="font-family:'microsoft yahei';"><span>HDFS</span></span></span></h3>
<p> </p>
<div><span style="font-size:14px;"><span style="font-family:'microsoft yahei';">对外部客户机而言，HDFS 就像一个传统的分级文件系统。可以创建、删除、移动或重命名文 件，等等。但是 HDFS 的架构是基于一组特定的节点构建的（参见图 1），这是由它自身的特点决定的。这些节点包括 NameNode（仅一个），它在 HDFS 内部提供元数据服务；DataNode，它为 HDFS 提供存储块。由于仅存在一个 NameNode，因此这是 HDFS
 的一个缺点（单点失败）。</span></span></div>
<p> </p>
<div><span style="font-size:14px;"><span style="font-family:'microsoft yahei';">存储在 HDFS 中的文件被分成块，然后将这些块复制到多个计算机中（DataNode）。这与传统的 RAID 架构大不相同。块的大小（通常为 64MB）和复制的块数量在创建文件时由客户机决定。NameNode 可以控制所有文件操作。HDFS 内部的所有通信都基于标准的 TCP/IP 协议。</span></span></div>
<p> </p>
<h3><a name="t5"></a><span style="font-size:14px;"><span style="font-family:'microsoft yahei';"><span>NameNode</span></span></span></h3>
<p> </p>
<div><span style="font-size:14px;"><span style="font-family:'microsoft yahei';">NameNode 是一个通常在 HDFS 实例中的单独机器上运行的软件。它负责管理文件系统名称空间和控制外部客户机的访问。NameNode 决定是否将文件映射到 DataNode 上的复制块上。对于最常见的 3 个复制块，第一个复制块存储在同一机架的不同节点上，最后一个复制块存储在不同机架的某个节点上。注意，这里需要您了解集群架构。</span></span></div>
<p> </p>
<div><span style="font-size:14px;"><span style="font-family:'microsoft yahei';">实际的 I/O 事务并 没有经过 NameNode，只有表示 DataNode 和块的文件映射的元数据经过 NameNode。当外部客户机发送请求要求创建文件时，NameNode 会以块标识和该块的第一个副本的 DataNode IP 地址作为响应。这个 NameNode 还会通知其他将要接收该块的副本的 DataNode。</span></span></div>
<p> </p>
<div><span style="font-size:14px;"><span style="font-family:'microsoft yahei';">NameNode 在一个称为 FsImage 的文件中存储所有关于文件系统名称空间的信息。这个文件和一个包含所有事务的记录文件（这里是 EditLog）将存储在 NameNode 的本地文件系统上。FsImage 和 EditLog 文件也需要复制副本，以防文件损坏或 NameNode 系统丢失。</span></span></div>
<p> </p>
<h3><a name="t6"></a><span style="font-size:14px;"><span style="font-family:'microsoft yahei';"><span>DataNode</span></span></span></h3>
<p> </p>
<div><span style="font-size:14px;"><span style="font-family:'microsoft yahei';">DataNode 也是一个通常在 HDFS 实例中的单独机器上运行的软件。Hadoop 集群包含一个 NameNode 和大量 DataNode。DataNode 通常以机架的形式组织，机架通过一个交换机将所有系统连接起来。Hadoop 的一个假设是：机架内部节点之间的传输速度快于机架间节点的传输速度。</span></span></div>
<p> </p>
<div><span style="font-size:14px;"><span style="font-family:'microsoft yahei';">DataNode 响应来自 HDFS 客户机的读写请求。它们还响应来自 NameNode 的创建、删除和复制块的命令。NameNode 依赖来自每个 DataNode 的定期心跳（heartbeat）消息。每条消息都包含一个块报告，NameNode 可以根据这个报告验证块映射和其他文件系统元数据。如果 DataNode 不能发送心跳消息，NameNode 将采取修复措施，重新复制在该节点上丢失的块。</span></span></div>
<p> </p>
<h3><a name="t7"></a><span style="font-size:14px;"><span style="font-family:'microsoft yahei';"><a name="4_4"></a><a name="sub908354_4_4"></a><span>文件操作</span></span></span></h3>
<p> </p>
<div><span style="font-size:14px;"><span style="font-family:'microsoft yahei';">可 见，HDFS 并不是一个万能的文件系统。它的主要目的是支持以流的形式访问写入的大型文件。如果客户机想将文件写到 HDFS 上，首先需要将该文件缓存到本地的临时存储。如果缓存的数据大于所需的 HDFS 块大小，创建文件的请求将发送给 NameNode。NameNode 将以 DataNode 标识和目标块响应客户机。同时也通知将要保存文件块副本的 DataNode。当客户机开始将临时文件发送给第一个
 DataNode 时，将立即通过管道方式将块内容转发给副本 DataNode。客户机也负责创建保存在相同 HDFS 名称空间中的校验和（checksum）文件。在最后的文件块发送之后，NameNode 将文件创建提交到它的持久化元数据存储（在 EditLog 和 FsImage 文件）。</span></span></div>
<p> </p>
<h3><a name="t8"></a><span style="font-size:14px;"><span style="font-family:'microsoft yahei';"><a name="4_5"></a><a name="sub908354_4_5"></a><span>Linux 集群</span></span></span></h3>
<p> </p>
<div><span style="font-size:14px;"><span style="font-family:'microsoft yahei';">Hadoop 框架可在单一的 Linux 平台上使用（开发和调试时），但是使用存放在机架上的商业服务器才能发挥它的力量。这些机架组成一个 Hadoop 集群。它通过集群拓扑知识决定如何在整个集群中分配作业和文件。Hadoop 假定节点可能失败，因此采用本机方法处理单个计算机甚至所有机架的失败。</span></span></div>
<p> </p>
<h2><a name="t9"></a><span style="font-size:14px;"><span style="font-family:'microsoft yahei';"><span>集群系统</span></span></span></h2>
<p> </p>
<div><span style="font-size:14px;"><span style="font-family:'microsoft yahei';">Google的数据中心使用廉价的Linux PC机组成集群，在上面运行各种应用。即使是分布式开发的新手也可以迅速使用Google的基础设施。核心组件是3个：</span></span></div>
<p> </p>
<div><span style="font-size:14px;"><span style="font-family:'microsoft yahei';">⒈GFS（Google File System）。一个分布式文件系统，隐藏下层负载均衡，冗余复制等细节，对上层程序提供一个统一的文件系统API接口。Google根据自己的需求对它进行了特别优化，包括：超大文件的访问，读操作比例远超过写操作，PC机极易发生故障造成节点失效等。GFS把文件分成64MB的块，分布在集群的机器上，使用Linux的文件系统存放。同时每块文件至少有3份以上的冗余。中心是一个Master节点，根据文件索引，找寻文件块。详见Google的工程师发布的GFS论文。</span></span></div>
<p> </p>
<div><span style="font-size:14px;"><span style="font-family:'microsoft yahei';">⒉MapReduce。Google发现大多数分布式运算可以抽象为MapReduce操作。Map是把输入Input分解成中间的Key/Value对，Reduce把Key/Value合成最终输出Output。这两个函数由程序员提供给系统，下层设施把Map和Reduce操作分布在集群上运行，并把结果存储在GFS上。</span></span></div>
<p> </p>
<div><span style="font-size:14px;"><span style="font-family:'microsoft yahei';">⒊BigTable。一个大型的分布式数据库，这个数据库不是关系式的数据库。像它的名字一样，就是一个巨大的表格，用来存储结构化的数据。</span></span></div>
<p> </p>
<div><span style="font-size:14px;"><span style="font-family:'microsoft yahei';">以上三个设施Google均有论文发表。</span></span></div>
<p> </p>
<h2><a name="t10"></a><span style="font-size:14px;"><span style="font-family:'microsoft yahei';"><span>应用程序</span></span></span></h2>
<p> </p>
<div><span style="font-size:14px;"><span style="font-family:'microsoft yahei';">Hadoop 的最常见用法之一是 Web 搜索。虽然它不是惟一的软件框架应用程序，但作为一个并行数据处理引擎，它的表现非常突出。Hadoop 最有趣的方面之一是 Map and Reduce 流程，它受到 Google开发的启发。这个流程称为创建索引，它将 Web 爬行器检索到的文本 Web 页面作为输入，并且将这些页面上的单词的频率报告作为结果。然后可以在整个
 Web 搜索过程中使用这个结果从已定义的搜索参数中识别内容。</span></span></div>
<p> </p>
<div><span style="font-size:14px;"><span style="font-family:'microsoft yahei';"><strong>MapReduce</strong></span></span></div>
<p> </p>
<div><span style="font-size:14px;"><span style="font-family:'microsoft yahei';">最简单的 MapReduce 应用程序至少包含 3 个部分：一个 Map 函数、一个 Reduce 函数和一个 main 函数。main 函数将作业控制和文件输入/输出结合起来。在这点上，Hadoop 提供了大量的接口和抽象类，从而为 Hadoop 应用程序开发人员提供许多工具，可用于调试和性能度量等。</span></span></div>
<p> </p>
<div><span style="font-size:14px;"><span style="font-family:'microsoft yahei';">MapReduce 本身就是用于并行处理大数据集的软件框 架。MapReduce 的根源是函数性编程中的 map 和 reduce 函数。它由两个可能包含有许多实例（许多 Map 和 Reduce）的操作组成。Map 函数接受一组数据并将其转换为一个键/值对列表，输入域中的每个元素对应一个键/值对。Reduce 函数接受 Map 函数生成的列表，然后根据它们的键（为每个键生成一个键/值对）缩小键/值对列表。</span></span></div>
<p> </p>
<div><span style="font-size:14px;"><span style="font-family:'microsoft yahei';">这里提供一个示例，帮助您理解它。假设输入域是 one small step for man,one giant leap for mankind。在这个域上运行 Map 函数将得出以下的键/值对列表：</span></span></div>
<p> </p>
<div><span style="font-size:14px;"><span style="font-family:'microsoft yahei';">（one,1） (small,1） (step,1） (for,1） (man,1）</span></span>
<div style="width:181px;">
<p><span style="font-size:14px;"><span style="font-family:'microsoft yahei';"><a title="查看图片">  </a>MapReduce 流程的概念流</span></span></p>
</div>
<span style="font-size:14px;"><span style="font-family:'microsoft yahei';">(one,1） (giant,1） (leap,1） (for,1） (mankind,1）</span></span></div>
<p> </p>
<div><span style="font-size:14px;"><span style="font-family:'microsoft yahei';">如果对这个键/值对列表应用 Reduce 函数，将得到以下一组键/值对：</span></span></div>
<p> </p>
<div><span style="font-size:14px;"><span style="font-family:'microsoft yahei';">（one,2） (small,1） (step,1） (for,2） (man,1）（giant,1） (leap,1） (mankind,1）</span></span></div>
<p> </p>
<div><span style="font-size:14px;"><span style="font-family:'microsoft yahei';">结果是对输入域中的单词进行计数，这无疑对处理索引十分有用。但是，现在假</span></span><span style="font-size:14px;"><span style="font-family:'microsoft yahei';">设 有两个输入域，第一个是 one small step for man，第二个是 one giant leap for
 mankind。您可以在每个域上执行 Map 函数和 Reduce 函数，然后将这两个键/值对列表应用到另一个 Reduce 函数，这时得到与前面一样的结果。换句话说，可以在输入域并行使用相同的操作，得到的结果是一样的，但速度更快。这便是 MapReduce 的威力；它的并行功能可在任意数量的系统上使用。图 2 以区段和迭代的形式演示这种思想。</span></span></div>
<p> </p>
<div><span style="font-size:14px;"><span style="font-family:'microsoft yahei';">现在回到 Hadoop 上，它是如何实现这个功能的？一个代表客户机在单个主系统上启动的 MapReduce 应用程序称为 JobTracker。类似于 NameNode，它是 Hadoop 集群中惟一负责控制 MapReduce 应用程序的系统。在应用程序提交之后，将提供包含在 HDFS 中的输入和输出目录。JobTracker 使用文件块信息（物理量和位置）确定如何创建其他
 TaskTracker 从属任务。MapReduce 应用程序被复制到每个出现输入文件块的节点。将为特定节点上的每个文件块创建一个惟一的从属任务。每个 TaskTracker 将状态和完成信息报告给 JobTracker。图 3 显示一个示例集群中的工作分布。</span></span></div>
<p> </p>
<div><span style="font-size:14px;"><span style="font-family:'microsoft yahei';">Hadoop 的这个特点非常重要，因为它并没有将存储移动到某个位置以供处理，而是将处理移动到存储。这通过根据集群中的节点数调节处理，因此支持高效的数据处理。</span></span></div>
<p> </p>
<p> </p>
<h5><a name="t11"></a><span style="font-size:18px;"><span style="font-family:'microsoft yahei';">Hadoop系统安装于配置</span></span></h5>
<p> </p>
<h5><a name="t12"></a><span style="font-size:18px;"><span style="font-family:'microsoft yahei';">海量数据处理平台架构介绍</span></span></h5>
<p> </p>
<h5><a name="t13"></a><span style="font-size:18px;"><span style="font-family:'microsoft yahei';">Hadoop能解决哪些问题</span></span></h5>
<p> </p>
<h5><a name="t14"></a><span style="font-size:18px;"><span style="font-family:'microsoft yahei';">Hadoop在国内的情景</span></span></h5>
<p> </p>
<h5><a name="t15"></a><span style="font-size:18px;"><span style="font-family:'microsoft yahei';">Hadoop简介</span></span></h5>
<p> </p>
<h5><a name="t16"></a><span style="font-size:18px;"><span style="font-family:'microsoft yahei';">Hadoop生态系统介绍</span></span></h5>
<p> </p>
<h5><a name="t17"></a><span style="font-size:18px;"><span style="font-family:'microsoft yahei';">HDFS简介</span></span></h5>
<p> </p>
<h5><a name="t18"></a><span style="font-size:18px;"><span style="font-family:'microsoft yahei';">HDFS设计原则</span></span></h5>
<p> </p>
<h5><a name="t19"></a><span style="font-size:18px;"><span style="font-family:'microsoft yahei';">HDFS系统结构</span></span></h5>
<p> </p>
<h5><a name="t20"></a><span style="font-size:18px;"><span style="font-family:'microsoft yahei';">HDFS文件权限</span></span></h5>
<p> </p>
<h5><a name="t21"></a><span style="font-size:18px;"><span style="font-family:'microsoft yahei';">HDFS文件读取</span></span></h5>
<p> </p>
<h5><a name="t22"></a><span style="font-size:18px;"><span style="font-family:'microsoft yahei';">HDFS文件写入</span></span></h5>
<p> </p>
<h5><a name="t23"></a><span style="font-size:18px;"><span style="font-family:'microsoft yahei';">HDFS文件存储</span></span></h5>
<p> </p>
<h5><a name="t24"></a><span style="font-size:18px;"><span style="font-family:'microsoft yahei';">HDFS文件存储结构</span></span></h5>
<p> </p>
<h5><a name="t25"></a><span style="font-size:18px;"><span style="font-family:'microsoft yahei';">HDFS开发常用命令</span></span></h5>
<p> </p>
<h5><a name="t26"></a><span style="font-size:18px;"><span style="font-family:'microsoft yahei';">Hadoop管理员常用命令</span></span></h5>
<p> </p>
<h5><a name="t27"></a><span style="font-size:18px;"><span style="font-family:'microsoft yahei';">HDFS API简介</span></span></h5>
<p> </p>
<h5><a name="t28"></a><span style="font-size:18px;"><span style="font-family:'microsoft yahei';">用Java对HDFS编程</span></span></h5>
<p> </p>
<h5><a name="t29"></a><span style="font-size:18px;"><span style="font-family:'microsoft yahei';">Mapreduce简介</span></span></h5>
<p> </p>
<h5><a name="t30"></a><span style="font-size:18px;"><span style="font-family:'microsoft yahei';">编写MapReduce程序的步骤</span></span></h5>
<p> </p>
<h5><a name="t31"></a><span style="font-size:18px;"><span style="font-family:'microsoft yahei';">MapReduce模型</span></span></h5>
<p> </p>
<h5><a name="t32"></a><span style="font-size:18px;"><span style="font-family:'microsoft yahei';">MapReduce运行步骤</span></span></h5>
<p> </p>
<h5><a name="t33"></a><span style="font-size:18px;"><span style="font-family:'microsoft yahei';">MapReduce执行流程</span></span></h5>
<p> </p>
<h5><a name="t34"></a><span style="font-size:18px;"><span style="font-family:'microsoft yahei';">MapReduce基本流程</span></span></h5>
<p> </p>
<h5><a name="t35"></a><span style="font-size:18px;"><span style="font-family:'microsoft yahei';">JobTracker(JT)和TaskTracker(TT)简介</span></span></h5>
<p> </p>
<h5><a name="t36"></a><span style="font-size:18px;"><span style="font-family:'microsoft yahei';">Mapreduce原理</span></span></h5>
<p> </p>
<h5><a name="t37"></a><span style="font-size:18px;"><span style="font-family:'microsoft yahei';">使用ZooKeeper来协作JobTracker</span></span></h5>
<p> </p>
<h5><a name="t38"></a><span style="font-size:18px;"><span style="font-family:'microsoft yahei';">Hadoop Job Scheduler</span></span></h5>
<p> </p>
<h5><a name="t39"></a><span style="font-size:18px;"><span style="font-family:'microsoft yahei';">mapreduce的类型与格式</span></span></h5>
<p> </p>
<h5><a name="t40"></a><span style="font-size:18px;"><span style="font-family:'microsoft yahei';">mapreduce的数据类型与java类型对应关系</span></span></h5>
<p> </p>
<h5><a name="t41"></a><span style="font-size:18px;"><span style="font-family:'microsoft yahei';">Writable接口</span></span></h5>
<p> </p>
<h5><a name="t42"></a><span style="font-size:18px;"><span style="font-family:'microsoft yahei';">实现自定义的mapreduce类型</span></span></h5>
<p> </p>
<h5><a name="t43"></a><span style="font-size:18px;"><span style="font-family:'microsoft yahei';">mapreduce驱动默认的设置</span></span></h5>
<p> </p>
<h5><a name="t44"></a><span style="font-size:18px;"><span style="font-family:'microsoft yahei';">Combiners和Partitioner编程</span></span></h5>
</div>
            </div>
                </div>