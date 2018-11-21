---
layout:     post
title:      Hadoop Linux 开发环境搭建
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许欢迎转载。					https://blog.csdn.net/LANGZI7758521/article/details/52514010				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>官网relsease版本：http://hadoop.apache.org/releases.html</p>
<p>Hadoop各个版下载地址</p>
<p>http://mirror.bit.edu.cn/apache/hadoop/common/</p>
<p>开发环境搭建：</p>
<p>1.安装Hadoop <br></p>
<p> wget  http://mirror.bit.edu.cn/apache/hadoop/common/hadoop-2.7.2/hadoop-2.7.2.tar.gz<br>
 tar xzvf hadoop-2.7.2.tar.gz<br></p>
<p>2.安装JDK （参考地址：http://blog.csdn.net/langzi7758521/article/details/51244741）</p>
<p>3.测试Hadoop</p>
<p>查看Hadoop版本号：<br></p>
<p>hadoop version <br></p>
<p>4.配置<br>
hadoop每一个组件都使用一个XML文件配置，核心属性在core-site.xml中，HDFS属性在hdfs-site.xml中，MapReduce属性在mapred-site.xml中。这些文件都在conf子目录下。<br>
hadoop有本地模式、伪分布模式和完全分布模式三种，默认配置为本地模式，没有守护进程，一切都运行在单个JVM上。<br></p>
            </div>
                </div>