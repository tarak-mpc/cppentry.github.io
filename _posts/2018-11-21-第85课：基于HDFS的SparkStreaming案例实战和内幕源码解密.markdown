---
layout:     post
title:      第85课：基于HDFS的SparkStreaming案例实战和内幕源码解密
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：王家林大咖2018年新书《SPARK大数据商业实战三部曲》清华大学出版，清华大学出版社官方旗舰店（天猫）https://qhdx.tmall.com/?spm=a220o.1000855.1997427721.d4918089.4b2a2e5dT6bUsM					https://blog.csdn.net/duan_zhihua/article/details/51225214				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p> </p>
<p>第85课：基于HDFS的SparkStreaming案例实战和内幕源码解密</p>
<p> </p>
<p><span style="font-family:Calibri;font-size:14px;">Hdfs</span></p>
<p><span style="font-family:Calibri;font-size:14px;">/library/SparkStreaming/CheckPoint_Data</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">/library/SparkStreaming/Data</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p align="left"><span style="font-family:Calibri;font-size:14px;">&lt;dependency&gt;</span></p>
<p align="left"><span style="font-family:Calibri;font-size:14px;">          &lt;groupId&gt;org.apache.spark&lt;/groupId&gt;</span></p>
<p align="left"><span style="font-family:Calibri;font-size:14px;">          &lt;artifactId&gt;spark-streaming-flume_2.10&lt;/artifactId&gt;</span></p>
<p align="left"><span style="font-family:Calibri;font-size:14px;">           &lt;version&gt;1.6.0&lt;/version&gt;</span></p>
<p align="left"><span style="font-family:Calibri;font-size:14px;">         &lt;/dependency&gt;</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">[root@master sbin]#hadoop dfs -mkdir -phdfs://master:9000//library/SparkStreaming/CheckPoint_Data</span></p>
<p><span style="font-family:Calibri;font-size:14px;">DEPRECATED: Use of this script to executehdfs command is deprecated.</span></p>
<p><span style="font-family:Calibri;font-size:14px;">Instead use the hdfs command for it.</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".</span></p>
<p><span style="font-family:Calibri;font-size:14px;">SLF4J: Defaulting to no-operation (NOP)logger implementation</span></p>
<p><span style="font-family:Calibri;font-size:14px;">SLF4J: Seehttp://www.slf4j.org/codes.html#StaticLoggerBinder for further details.</span></p>
<p><span style="font-family:Calibri;font-size:14px;">16/04/22 22:04:21 WARNutil.NativeCodeLoader: Unable to load native-hadoop library for yourplatform... using builtin-java classes where applicable</span></p>
<p><span style="font-family:Calibri;font-size:14px;">[root@master sbin]#hadoop dfs -mkdir -phdfs://master:9000/library/SparkStreaming/Data</span></p>
<p><span style="font-family:Calibri;font-size:14px;">DEPRECATED: Use of this script to executehdfs command is deprecated.</span></p>
<p><span style="font-family:Calibri;font-size:14px;">Instead use the hdfs command for it.</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">SLF4J: Failed to load class"org.slf4j.impl.StaticLoggerBinder".</span></p>
<p><span style="font-family:Calibri;font-size:14px;">SLF4J: Defaulting to no-operation (NOP)logger implementation</span></p>
<p><span style="font-family:Calibri;font-size:14px;">SLF4J: Seehttp://www.slf4j.org/codes.html#StaticLoggerBinder for further details.</span></p>
<p><span style="font-family:Calibri;font-size:14px;">16/04/22 22:06:14 WARN util.NativeCodeLoader:Unable to load native-hadoop library for your platform... using builtin-javaclasses where applicable</span></p>
<p><span style="font-family:Calibri;font-size:14px;">[root@master sbin]#</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">[root@master setup_tools]#mvSparkStreamingTest.jar /usr/local/IMFdatatest</span></p>
<p><span style="font-family:Calibri;font-size:14px;">[root@master setup_tools]#cd /usr/local/IMFdatatest</span></p>
<p><span style="font-family:Calibri;font-size:14px;">[root@master IMFdatatest]#ls</span></p>
<p><span style="font-family:Calibri;font-size:14px;">                        dataForAverage.txt    dataForMutipleSorting.bak HadoopAppsTest.jar  members.txt        SortedData.txt          URLLog.txt</span></p>
<p><span style="font-family:Calibri;font-size:14px;">address.txt              dataForChain.txt       dataForMutipleSorting.txt  HiveSogou           RhzfApacheUrl      SparkStreamingTest.jar  workers.txt</span></p>
<p><span style="font-family:Calibri;font-size:14px;">data2016.txt             dataForChain.txt.bak   data.txt                   HiveTBdata          SecondarySort.txt  TopN1.txt</span></p>
<p><span style="font-family:Calibri;font-size:14px;">dataBiggestsmallest.txt  dataForMutipleSorting  department.txt             invertedindex       selfjoin.txt       TopN2.txt</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">http://192.168.2.100:8080/</span></p>
<p><a href="http://192.168.2.100:50070/explorer.html#/library/SparkStreaming" rel="nofollow"><span style="font-family:Calibri;font-size:14px;color:#0000ff;">http://192.168.2.100:50070/explorer.html#/library/SparkStreaming</span></a></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="color:#FF0000;"><span style="font-family:Calibri;font-size:14px;">[root@masterbin]#spark-submit --class com.dt.spark.SparkApps.SparkStreaming.WordCountOnHDFS--master spark://192.168.2.100:7077 </span></span>/usr/local/IMFdatatest/SparkStreamingTest.jar</p>
<p><span style="font-family:Calibri;font-size:14px;">         </span></p>
<p><span style="font-family:Calibri;font-size:14px;">[root@master IMFdatatest]#hadoop dfs-put  data.txt   hdfs://master:9000/library/SparkStreaming/Data</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">[root@master IMFdatatest]#hadoop dfs -cat/library/SparkStreaming/Data/data.txt</span></p>
<p><span style="font-family:Calibri;font-size:14px;">DEPRECATED: Use of this script to executehdfs command is deprecated.</span></p>
<p><span style="font-family:Calibri;font-size:14px;">Instead use the hdfs command for it.</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".</span></p>
<p><span style="font-family:Calibri;font-size:14px;">SLF4J: Defaulting to no-operation (NOP)logger implementation</span></p>
<p><span style="font-family:Calibri;font-size:14px;">SLF4J: Seehttp://www.slf4j.org/codes.html#StaticLoggerBinder for further details.</span></p>
<p><span style="font-family:Calibri;font-size:14px;">16/04/22 22:29:27 WARNutil.NativeCodeLoader: Unable to load native-hadoop library for yourplatform... using builtin-java classes where applicable</span></p>
<p><span style="font-family:Calibri;font-size:14px;">0067011990999991950051507004888888889999999N9+00001+9999999999999999999999</span></p>
<p><span style="font-family:Calibri;font-size:14px;">0067011990999991950051512004888888889999999N9+00221+9999999999999999999999</span></p>
<p><span style="font-family:Calibri;font-size:14px;">0067011990999991950051518004888888889999999N9-00111+9999999999999999999999</span></p>
<p><span style="font-family:Calibri;font-size:14px;">0067011990999991949032412004888888889999999N9+01111+9999999999999999999999</span></p>
<p><span style="font-family:Calibri;font-size:14px;">0067011990999991950032418004888888880500001N9+00001+9999999999999999999999</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">org.apache.hadoop.mapreduce.lib.input.InvalidInputException:Input path does not exist:hdfs://master:9000/library/SparkStreaming/Data/data.txt._COPYING_</span></p>
<p><span style="font-family:Calibri;font-size:14px;">       atorg.apache.hadoop.mapreduce.lib.input.FileInputFormat.singleThreadedListStatus(FileInputFormat.java:321)</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">[root@master IMFdatatest]#ls</span></p>
<p><span style="font-family:Calibri;font-size:14px;">                        dataForAverage.txt     dataForMutipleSorting.bak  HadoopAppsTest.jar  invertedindex      selfjoin.txt            TopN2.txt</span></p>
<p><span style="font-family:Calibri;font-size:14px;">address.txt              dataForChain.txt       dataForMutipleSorting.txt  HiveSogou           members.txt        SortedData.txt          URLLog.txt</span></p>
<p><span style="font-family:Calibri;font-size:14px;">data2016.txt             dataForChain.txt.bak   data.txt                   HiveTBdata          RhzfApacheUrl      SparkStreamingTest.jar  workers.txt</span></p>
<p><span style="font-family:Calibri;font-size:14px;">dataBiggestsmallest.txt  dataForMutipleSorting  department.txt             IMFsparksubmit.sh   SecondarySort.txt  TopN1.txt</span></p>
<p><span style="font-family:Calibri;font-size:14px;">[root@master IMFdatatest]#chmod u+x  IMFsparksubmit.sh</span></p>
<p><span style="font-family:Calibri;font-size:14px;">[root@master IMFdatatest]#ls</span></p>
<p><span style="font-family:Calibri;font-size:14px;">                        dataForAverage.txt    dataForMutipleSorting.bak HadoopAppsTest.jar  invertedindex      selfjoin.txt            TopN2.txt</span></p>
<p><span style="font-family:Calibri;font-size:14px;">address.txt              dataForChain.txt       dataForMutipleSorting.txt  HiveSogou           members.txt        SortedData.txt          URLLog.txt</span></p>
<p><span style="font-family:Calibri;font-size:14px;">data2016.txt             dataForChain.txt.bak   data.txt                   HiveTBdata          RhzfApacheUrl      SparkStreamingTest.jar  workers.txt</span></p>
<p><span style="font-family:Calibri;font-size:14px;">dataBiggestsmallest.txt  dataForMutipleSorting  department.txt             IMFsparksubmit.sh   SecondarySort.txt  TopN1.txt</span></p>
<p><span style="font-family:Calibri;font-size:14px;">[root@master IMFdatatest]#catIMFsparksubmit.sh</span></p>
<p><span style="font-family:Calibri;font-size:14px;">/usr/local/spark-1.6.0-bin-hadoop2.6/bin/spark-submit--class com.dt.spark.SparkApps.SparkStreaming.WordCountOnHDFS --masterspark://192.168.2.100:7077 /usr/local/IMFdatatest/SparkStreamingTest.jar</span></p>
<p><span style="font-family:Calibri;font-size:14px;">[root@master IMFdatatest]#</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">[root@master bin]#hadoop dfs -rmr/library/SparkStreaming/CheckPoint_Data/</span></p>
<p><span style="font-family:Calibri;font-size:14px;">DEPRECATED: Use of this script to executehdfs command is deprecated.</span></p>
<p><span style="font-family:Calibri;font-size:14px;">Instead use the hdfs command for it.</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">rmr: DEPRECATED: Please use 'rm -r'instead.</span></p>
<p><span style="font-family:Calibri;font-size:14px;">SLF4J: Failed to load class"org.slf4j.impl.StaticLoggerBinder".</span></p>
<p><span style="font-family:Calibri;font-size:14px;">SLF4J: Defaulting to no-operation (NOP)logger implementation</span></p>
<p><span style="font-family:Calibri;font-size:14px;">SLF4J: Seehttp://www.slf4j.org/codes.html#StaticLoggerBinder for further details.</span></p>
<p><span style="font-family:Calibri;font-size:14px;">16/04/22 22:45:15 WARN util.NativeCodeLoader:Unable to load native-hadoop library for your platform... using builtin-javaclasses where applicable</span></p>
<p><span style="font-family:Calibri;font-size:14px;">16/04/22 22:45:17 INFOfs.TrashPolicyDefault: Namenode trash configuration: Deletion interval = 0minutes, Emptier interval = 0 minutes.</span></p>
<p><span style="font-family:Calibri;font-size:14px;">Deleted/library/SparkStreaming/CheckPoint_Data</span></p>
<p><span style="font-family:Calibri;font-size:14px;">[root@master bin]#</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">[root@master bin]#hadoop dfs -mkdir  /library/SparkStreaming/CheckPoint_Data</span></p>
<p><span style="font-family:Calibri;font-size:14px;">DEPRECATED: Use of this script to executehdfs command is deprecated.</span></p>
<p><span style="font-family:Calibri;font-size:14px;">Instead use the hdfs command for it.</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
<p><span style="font-family:Calibri;font-size:14px;">SLF4J: Failed to load class"org.slf4j.impl.StaticLoggerBinder".</span></p>
<p><span style="font-family:Calibri;font-size:14px;">SLF4J: Defaulting to no-operation (NOP)logger implementation</span></p>
<p><span style="font-family:Calibri;font-size:14px;">SLF4J: Seehttp://www.slf4j.org/codes.html#StaticLoggerBinder for further details.</span></p>
<p><span style="font-family:Calibri;font-size:14px;">16/04/22 22:46:08 WARNutil.NativeCodeLoader: Unable to load native-hadoop library for yourplatform... using builtin-java classes where applicable</span></p>
<p><span style="font-family:Calibri;font-size:14px;">[root@master bin]#</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> <img src="https://img-blog.csdn.net/20160423105454438?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p> </p>
<p><br></p>
<p><br></p>
<p><br></p>
<p>hadoop dfs -mkdir -p hdfs://master:9000//library/SparkStreaming/CheckPoint_Data<br><br><br>
hadoop dfs -mkdir -p hdfs://master:9000/library/SparkStreaming/Data<br><br><br><br><br>
IMFsparksubmit.sh<br><br><br>
 <br>
/usr/local/spark-1.6.0-bin-hadoop2.6/bin/spark-submit --class com.dt.spark.SparkApps.SparkStreaming.WordCountOnHDFS --master spark://192.168.189.1:7077  /usr/local/IMF_testdata/SparkStreamingTest.jar<br><br><br><br><br>
hadoop dfs -put README.md  hdfs://master:9000/library/SparkStreaming/Data<br></p>
<p><br></p>
<p><br></p>
<p><img src="https://img-blog.csdn.net/20160423163342542?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><br></p>
<p><img src="https://img-blog.csdn.net/20160423163430745?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><img src="https://img-blog.csdn.net/20160423163511280?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><img src="https://img-blog.csdn.net/20160423163551871?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><img src="https://img-blog.csdn.net/20160423163751309?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><img src="https://img-blog.csdn.net/20160423163819347?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><img src="https://img-blog.csdn.net/20160423163854208?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><br></p>
<p><br></p>
            </div>
                </div>