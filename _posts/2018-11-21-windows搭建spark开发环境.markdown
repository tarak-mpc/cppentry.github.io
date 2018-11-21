---
layout:     post
title:      windows搭建spark开发环境
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/feyman1120/article/details/70042333				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p></p>
<p>一、下载:</p>
<p>1, IDEA                                                      </p>
<p><a href="https://download.jetbrains.com/idea/ideaIC-14.1.7.exe" rel="nofollow">https://download.jetbrains.com/idea/ideaIC-14.1.7.exe</a></p>
<p>2、java </p>
<p><a href="http://download.oracle.com/otn-pub/java/jdk/8u131-b11/d54c1d3a095b4ff2b6607d096fa80163/jdk-8u131-windows-x64.exe" rel="nofollow">http://download.oracle.com/otn-pub/java/jdk/8u131-b11/d54c1d3a095b4ff2b6607d096fa80163/jdk-8u131-windows-x64.exe</a></p>
<p> </p>
<p> </p>
<p>3、scala</p>
<p><a href="https://downloads.lightbend.com/scala/2.11.8/scala-2.11.8.msi" rel="nofollow">https://downloads.lightbend.com/scala/2.11.8/scala-2.11.8.msi</a></p>
<p> </p>
<p>4、maven</p>
<p><a href="http://ftp.cuhk.edu.hk/pub/packages/apache.org/maven/maven-3/3.5.0/binaries/apache-maven-3.5.0-bin.zip" rel="nofollow">http://ftp.cuhk.edu.hk/pub/packages/apache.org/maven/maven-3/3.5.0/binaries/apache-maven-3.5.0-bin.zip</a></p>
<p> </p>
<p>5，spark2.0.0-hadoop2.7</p>
<p><a href="http://d3kbcqa49mib13.cloudfront.net/spark-2.0.0-bin-hadoop2.7.tgz" rel="nofollow">http://d3kbcqa49mib13.cloudfront.net/spark-2.0.0-bin-hadoop2.7.tgz</a></p>
<p> </p>
<p>6,Hadoop 2.7.3</p>
<p><a href="http://www.apache.org/dyn/closer.cgi/hadoop/common/hadoop-2.7.3/hadoop-2.7.3.tar.gz" rel="nofollow">http://www.apache.org/dyn/closer.cgi/hadoop/common/hadoop-2.7.3/hadoop-2.7.3.tar.gz</a></p>
<p> </p>
<p>7 anaconda2-4.3.1</p>
<p><a href="https://repo.continuum.io/archive/Anaconda2-4.3.1-Windows-x86_64.exe" rel="nofollow">https://repo.continuum.io/archive/Anaconda2-4.3.1-Windows-x86_64.exe</a></p>
<p> </p>
<p> </p>
<p>二、安装：</p>
<p>全部安装到D盘 DevEnv目录下，安装完如下图：</p>
<p>1、  安装JAVA</p>
<p></p>
<p>2、  安装scala</p>
<p></p>
<p>3、  安装IDEA，anaconda 解压maven spark,hadoop</p>
<p> </p>
<p>4、  Asdf</p>
<p> </p>
<p> </p>
<p>三、配置环境变量</p>
<p>1、配置JAVA_HOME</p>
<p></p>
<p> </p>
<p>2、配置HADOOP_HOME</p>
<p></p>
<p>3、配置MAVEN_HOME</p>
<p></p>
<p>4配置SPARK_HOME </p>
<p></p>
<p> </p>
<p>5 SCALA_HOME</p>
<p></p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p>6，在PATH后面添加</p>
<p>;%JAVA_HOME%\bin;%SCALA_HOME%\bin;%MAVEN_HOME%\bin;%SPARK_HOME%\bin</p>
<p> </p>
<p> </p>
<p> </p>
<p>四、配置IDEA+MAVEN+SAPRK</p>
<p> </p>
<p>1、新建MAVEN ,SDK 选择JAVA所在路径，勾选“createfrom archetype”,再选中”scal-…simple”</p>
<p> </p>
<p>                         </p>
<p> </p>
<p> </p>
<p> </p>
<p>设置groupID和artfid</p>
<p></p>
<p> </p>
<p>这里的seting里面可以设置本地仓库</p>
<p></p>
<p> </p>
<p></p>
<p></p>
<p>安装scala插件</p>
<p>“file”-“ setting”-“plugins”-“browseresopo”-</p>
<p></p>
<p> </p>
<p> </p>
<p></p>
<p> </p>
<p>等待maven初始化工程和下载依赖项，第一次花费的时间会比较长，如果配置私有境像就会很快。</p>
<p></p>
<p> </p>
<p>配置路径为：</p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p></p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p>重启，点击auto-enable初始化</p>
<p> </p>
<p></p>
<p>右键新建scala类</p>
<p></p>
<p> </p>
<p> </p>
<p>修改pom文件中的scala版本为2.11.8</p>
<p></p>
<p> </p>
<p>在依赖项里面添加spark依赖项</p>
<p> </p>
<p><a href="http://mvnrepository.com/artifact/org.apache.spark" rel="nofollow">http://mvnrepository.com/artifact/org.apache.spark</a></p>
<p> </p>
<p>选择  <a href="http://mvnrepository.com/artifact/org.apache.spark/spark-core_2.11" rel="nofollow"><span style="color:rgb(23,91,176);">spark-core_2.11</span></a>点进去后，再选择2.0.0</p>
<p></p>
<p> </p>
<p>将内容复杂以pom文件中</p>
<p>                                                              </p>
<p></p>
<p>会看到右下角的读条开始走动，说明已经在仓库下载spark相关依赖项到本地，</p>
<p> </p>
<p>编写如下代码</p>
<p></p>
<p> </p>
<p>在teminal里输入命令mvnclean install</p>
<p></p>
<p> </p>
<p>报错</p>
<p></p>
<p>把src下面和main并列的test目录删掉，重新打包</p>
<p></p>
<p> </p>
<p>进入target目录下面，提交JAR包，</p>
<p></p>
<p> </p>
<p>报错</p>
<p></p>
<p>去官网下：</p>
<p><a href="https://github.com/steveloughran/winutils/tree/master/hadoop-2.7.1/bin" rel="nofollow">https://github.com/steveloughran/winutils/tree/master/hadoop-2.7.1/bin</a></p>
<p>放到对应的目录下面，再重新打包，再跑，得到如下结果，说明成功。</p>
<p></p>
<p> </p>
<p>如果想直接在IDEA 里面运行程序，则需要做如下两步配置，</p>
<p>1、  conf需要指定master为本地变量</p>
<p> </p>
<p></p>
<p> </p>
<p>2、  file-project sruct-module-选中当前moudle</p>
<p>3、  </p>
<p>再在右边选中</p>
<p></p>
<p> </p>
<p>选择加号，再选择jars or dic…，选到SPARK_HOME/jars目录，</p>
<p></p>
<p> </p>
<p>等右下角加载完，再运行Run，，，得到如下结果：表示成功！</p>
<p></p>
<p> </p>
<p> </p>
<p> </p>
<p>Python </p>
<p> </p>
<p>Python就比较简单，不用打包，直接提交.py就可以跑，</p>
<p> </p>
<p>在setting – puglins 里面添加python</p>
<p></p>
<p>                                                                                                      
</p>
<p>下载完后重启IDEA</p>
<p>再new project的时候多了一个python选项</p>
<p></p>
<p> </p>
<p>SDK选择之前装的anaconda所在目录</p>
<p></p>
<p> </p>
<p></p>
<p> </p>
<p> </p>
<p>新建test_for_python.py文件，里面输入如下测试代码</p>
<p></p>
<p> </p>
<p>这个时候还不能跑，因为依赖的lib还没有加入到python环境下，</p>
<p> </p>
<p>将下面的pyspark </p>
<p></p>
<p> </p>
<p>复制添加到</p>
<p></p>
<p> </p>
<p>再运行，报错：</p>
<p></p>
<p> </p>
<p>直接在terminal下面用pip下载py4j(要在连网下)</p>
<p></p>
<p> </p>
<p></p>
<p> </p>
<p>再run,,,</p>
<p></p>
<p> </p>
<p>大功告成~</p>
<p> </p>
<p> </p>
<p>这已经是第四遍搭这个环境了，第一遍搭的非常辛苦，走了很多弯路，这次是在公司新领笔记本上搭建的，上面啥也没有，完全从0重新开始搭，搭一步写一步，每一步的截图都是真实情况复现，所以到最后肯定是可以成功的。</p>
<p> </p>
<p> </p>
<p> </p>
<p>如何在IDEA 配置git这个后面再整理。</p>
<p><br></p>
            </div>
                </div>