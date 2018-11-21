---
layout:     post
title:      安装hive出现的错误“hive: Permission denied”
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>今天试着去安装hive</p>
<p></p>
<p>Hive的安装</p>
<p><span style="color:#444444;background:#FFFFFF;">1:</span><span><span style="font-size:18px;"><span style="background-color:#FFFFFF;"> </span><span style="background-color:rgb(255,255,153);"> vi /etc/profile</span></span></span></p>
<p>在末尾加上exportHIVE_HOME=/usr/local/hive</p>
<p>         export HOVE_CONF_DIR=/usr/local/hive/conf</p>
<p>export PATH=$PATH:$HIVE_HOME/bin</p>
<p>export HIVE_LIB=$HOVE_HOME/lib</p>
<p>2. <span style="font-size:18px;background-color:rgb(255,255,153);">让profile文件立即生效</span></p>
<p>source /etc/profile</p>
<p>3. <span style="font-size:18px;background-color:rgb(255,255,153);"> 对hive配置文件调配</span></p>
<p>a)      切换到目录：cd /usr/local/hive/conf/</p>
<p>b)     ls会看到hive-env.sh.template这个文件（.template文件是模板文件，让用户参照其中的格式进行自定义的修改和优化）</p>
<p>c)      将其cp并命名为：hive-env.sh，然后编辑该文件</p>
<p>将export HADOOP_HEAPSIZE=1024前面的#去掉</p>
<p>将export HADOOP_HOME前面的#去掉，并让它指向安装hadoop的目录</p>
<p>将 export HIVE_CONF_DIR=/usr/local/hive/conf，并把#去掉</p>
<p>将 export HIVE_AUX_JARS_PATH=/usr/local/hive/lib</p>
<p>Esc键，退出保存:wq</p>
<p>source/hive-env.sh(生效文件)</p>
<p>d)     mkdir –p/usr/local/hive/warehouse</p>
<p>        mkdir –p /usr/local/hive/tmp</p>
<p>        mkdir –p /usr/local/hive/log</p>
<p>e)      cp hive-default.xml.templatehive-site.xml然后编辑该文件（vihive-site.xml）</p>
<p>       三个地方需要修改</p>
<p align="left">            1）&lt;property&gt;</p>
<p align="left">                     &lt;name&gt;hive.metastore.warehouse.dir&lt;/name&gt;</p>
<p align="left">                     &lt;value&gt;/usr/local/hive/warehouse&lt;/value&gt;</p>
<p align="left">                     &lt;description&gt;location ofdefault database for the warehouse&lt;/description&gt;</p>
<p align="left">                 &lt;/property&gt;</p>
<p align="left">           2）&lt;property&gt;</p>
<p align="left">                    &lt;name&gt;hive.exec.scratchdir&lt;/name&gt;</p>
<p align="left">                    &lt;value&gt;/usr/local/hive/tmp&lt;/value&gt;</p>
<p align="left">                    &lt;description&gt;Scratch space forHive jobs&lt;/description&gt;</p>
<p align="left">               &lt;/property&gt;</p>
<p align="left">          3）&lt;property&gt;</p>
<p align="left">                    &lt;name&gt;hive.querylog.location&lt;/name&gt;</p>
<p align="left">                     &lt;value&gt;/usr/local/hive/log&lt;/value&gt;</p>
<p align="left">               &lt;/property&gt;（注：这个在笔者的文件中没有可以自己添加，用于存放hive相关的日志文件）</p>
<p align="left">  f) cp hive-exec-log4j.properties.templatehive-log4j.properties并编辑此文件</p>
<p align="left">    修改：    log4j.appender.EventCounter=org.apache.hadoop.log.metrics.EventCounter</p>
<p align="left"><span style="font-size:18px;background-color:rgb(255,255,153);">最后大功告成，运行"hive"的时候出现了问题</span></p>
<p align="left"><img src="https://img-blog.csdn.net/20131218203414468" alt=""><br></p>
<p align="left">显然是权限出现了问题，不过网上查了半天终于找到解决办法了</p>
<p align="left">输入“ chmod -R 755 /usr/local/hive”搞定</p>
<p align="left"><img src="https://img-blog.csdn.net/20131218203432140" alt=""><br></p>
<br>            </div>
                </div>