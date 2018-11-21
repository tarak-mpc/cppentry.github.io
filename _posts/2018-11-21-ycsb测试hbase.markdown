---
layout:     post
title:      ycsb测试hbase
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/chen517611641/article/details/26056591				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>1.安装hbase和zookeeper，zookeeper不能使用hbase内置的</p>
<p>2.安装YCSB，直接下载官方二进制文件hbase会报错，not **host：port</p>
<p>   下载Git源代码项目，修改根目录下pom.xml和hbase-binding下pom.xml文件关于本机hadoop和hbase相关的信息</p>
<p>   编译hbase和core moudle          根目录下运行 mvn clean package</p>
<p>   将编译产生的hbase-binding.jar（在/yscb-master/hbase-binding/target）和core.jar（在/yscb-master/core/target）覆盖到先前下载好的二进制ycsb目录中</p>
<p>   将ZK_HOME/lib下边关于log4j和slf4j还有zookeeperx.x.x.jar 文件拷贝到YCSB_HOME/core/lib下，不然会报</p>
<p>   java.lang.NoSuchMethodError: org.slf4j.impl.StaticLoggerBinder.getSingleton()这个错误<br></p>
<p>3.hbase建一个表create 'usertable' , 'cf1'</p>
<p>   运行ycsb load hbase -P $yscb_home/workloads/workloada -threads 2 -p columnfamily=cf1 -s</p>
            </div>
                </div>