---
layout:     post
title:      hadoop的安装，主从设置及结合php的应用
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
最近又机会接触hadoop，顺便分享一下同事的记录</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
<br></div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
一、主服务器设置</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
1.创建用户</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
#useradd hadoop</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
2.设置密码</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
#passwd hadoop</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
3.切换hadoop登录</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
#su - hadoop</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
4.解压hadoop压缩包</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
#tar zxvf hadoop-1.0.3.tar.gz</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
5.设置目录权限</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
#chown -R hadoop.hadoop hadoop-1.0.3</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
6.编辑环境变量</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
#vim hadoop-1.0.3/conf/hadoop-env.sh</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
编辑“JAVA_HOME”路径：</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
export JAVA_HOME=/usr/local/jdk1.7.0_05</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
增加一行（取消过时警告）：</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
export HADOOP_HOME_WARN_SUPPRESS=1</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
7.编辑系统环境变量(使用root用户)</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
#vim /etc/profile</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
追加如下内容：</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
export JAVA_HOME=/usr/local/jdk1.7.0_05</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
export PATH=$JAVA_HOME/bin:$ANT_HOME/bin:$PATH</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
export HADOOP_HOME=/home/hadoop/hadoop-1.0.3</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
export PATH=$PATH:$HADOOP_HOME/bin</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
8.执行环境设置</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
#source /etc/profile</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
9.设置主IP</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
#vim hadoop-1.0.3/conf/masters</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
把localhost替换成192.168.1.247</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
10.设置从IP</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
#vim hadoop-1.0.3/conf/slaves</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
把localhost替换成192.168.1.248</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
11.设置hdfs</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
#vim hadoop-1.0.3/conf/hdfs-site.xml</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
增加如下内容：</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
&lt;configuration&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
    &lt;property&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
        &lt;name&gt;dfs.name.dir&lt;/name&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
        &lt;value&gt;/home/hadoop/hadoop-1.0.3/name&lt;/value&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
    &lt;/property&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
     &lt;property&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
        &lt;name&gt;dfs.data.dir&lt;/name&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
        &lt;value&gt;/home/hadoop/hadoop-1.0.3/data&lt;/value&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
    &lt;/property&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
     &lt;property&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
        &lt;name&gt;dfs.replication&lt;/name&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
        &lt;value&gt;3&lt;/value&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
    &lt;/property&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
&lt;/configuration&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
12.配置任务</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
#vim hadoop-1.0.3/conf/mapred-site.xml</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
增加如下内容：</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
&lt;configuration&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
    &lt;property&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
        &lt;name&gt;mapred.job.tracker&lt;/name&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
        &lt;value&gt;<a href="http://192.168.1.247:9001/" rel="nofollow" style="color:rgb(0,0,139);">192.168.1.247:9001</a>&lt;/value&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
    &lt;/property&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
    &lt;property&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
        &lt;name&gt;mapred.local.dir&lt;/name&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
        &lt;value&gt;/home/hadoop/hadoop-1.0.3/var&lt;/value&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
    &lt;/property&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
&lt;/configuration&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
13.配置核心文件</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
#vim hadoop-1.0.3/conf/core-site.xml</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
&lt;configuration&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
    &lt;property&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
        &lt;name&gt;<a href="http://fs.default.name/" rel="nofollow" style="color:rgb(0,0,139);">fs.default.name</a>&lt;/name&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
        &lt;value&gt;hdfs://<a href="http://192.168.1.247:9000/" rel="nofollow" style="color:rgb(0,0,139);">192.168.1.247:9000</a>&lt;/value&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
    &lt;/property&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
    &lt;property&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
        &lt;name&gt;fs.checkpoint.period&lt;/name&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
        &lt;value&gt;3600&lt;/value&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
    &lt;/property&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
    &lt;property&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
        &lt;name&gt;fs.checkpoint.size&lt;/name&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
        &lt;value&gt;<span id="OBJ_PREFIX_DWT9163_com_zimbra_phone"><a style="color:rgb(0,0,139);">67108864</a></span>&lt;/value&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
    &lt;/property&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
    &lt;property&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
        &lt;name&gt;hadoop.tmp.dir&lt;/name&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
        &lt;value&gt;/home/hadoop/hadoop-1.0.3/tmp&lt;/value&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
    &lt;/property&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
&lt;/configuration&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
14.实现主从自动登录（单方向）</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
创建公钥:</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
#ssh-keygen -t rsa</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
自己可以ssh：</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
#cat .ssh/id_rsa.pub &gt;&gt; .ssh/authorized_keys</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
设置文件权限：</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
#chmod 700 .ssh/authorized_keys</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
测试一下，不需要密码就表示成功</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
#ssh localhost</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
#exit</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
二、从服务器设置</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
1.创建用户</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
#useradd hadoop</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
2.设置密码</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
#passwd hadoop</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
3.编辑系统环境变量(使用root用户)</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
#vim /etc/profile</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
追加如下内容：</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
export JAVA_HOME=/usr/local/jdk1.7.0_05</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
export PATH=$JAVA_HOME/bin:$ANT_HOME/bin:$PATH</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
export HADOOP_HOME=/home/hadoop/hadoop-1.0.3</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
export PATH=$PATH:$HADOOP_HOME/bin</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
4.执行环境设置</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
#source /etc/profile</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
5.解压缩hadoop压缩包</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
#tar zxvf hadoop-1.0.3.tar.gz</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
6.设置目录权限</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
#chown -R hadoop.hadoop hadoop-1.0.3</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
 </div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
三、回到主服务器</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
1.切换hadoop用户</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
#su - hadoop</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
2.复制公私到从服务器</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
#ssh-copy-id -i .ssh/id_rsa.pub <a href="mailto:hadoop@192.168.1.248" rel="nofollow" style="color:rgb(0,0,139);">hadoop@192.168.1.248</a></div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
3.测试</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
#ssh 192.168.1.248</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
配置正确的话是不需要密码就能登录的</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
4.向从服务器发送配置文件</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
#scp -r hadoop-1.0.3/conf hadoop@192.168.1.248:/home/hadoop/hadoop-1.0.3</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
5.格式化分布式文件系统</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
#hadoop-1.0.3/bin/hadoop namenode -format</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
6.启动hadoop服务</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
#hadoop-1.0.3/bin/start-dfs.sh</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
#hadoop-1.0.3/bin/start-mapred.sh</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
7.查看运行情况</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
web查询界面：<a href="http://192.168.1.247:50030/" rel="nofollow" style="color:rgb(0,0,139);">http://192.168.1.247:50030</a></div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
 </div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
四、安装hive(主服务器)</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
1.压缩包装包(hadoop用户)</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
#tar zxvf hive-0.9.0.tar.gz</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
#mv hive-0.9.0 hadoop-1.0.3</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
2.配置hive环境变量</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
#cp hadoop-1.0.3/hive-0.9.0/conf/hive-env.sh.template hadoop-1.0.3/hive-0.9.0/conf/hive-env.sh</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
#vim hadoop-1.0.3/hive-0.9.0/conf/hive-env.sh</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
增加一行：</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
HADOOP_HOME=$HADOOP_HOME</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
3.配置hive元数据保存到mysql</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
建立数据数用户，数据库使用latin1字符集：</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
mysql&gt;CREATE DATABASE hive CHARACTER SET latin1;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
mysql&gt;GRANT ALL PRIVILEGES ON hive.* TO 'hive'@'%' IDENTIFIED BY 'hivepasswd';</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
修改配置文件：</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
#cp hadoop-1.0.3/hive-0.9.0/conf/hive-default.xml.template hadoop-1.0.3/hive-0.9.0/conf/hive-site.xml</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
#vim hadoop-1.0.3/hive-0.9.0/conf/hive-site.xml</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
修改四个地方：</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
&lt;property&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
  &lt;name&gt;javax.jdo.option.ConnectionURL&lt;/name&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
  &lt;value&gt;jdbc:mysql://<a href="http://192.168.1.247:3306/hive?createDatabaseIfNotExist=true" rel="nofollow" style="color:rgb(0,0,139);">192.168.1.247:3306/hive?createDatabaseIfNotExist=true</a>&lt;/value&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
  &lt;description&gt;JDBC connect string for a JDBC metastore&lt;/description&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
&lt;/property&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
&lt;property&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
  &lt;name&gt;javax.jdo.option.ConnectionDriverName&lt;/name&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
  &lt;value&gt;com.mysql.jdbc.Driver&lt;/value&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
  &lt;description&gt;Driver class name for a JDBC metastore&lt;/description&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
&lt;/property&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
&lt;property&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
  &lt;name&gt;javax.jdo.option.ConnectionUserName&lt;/name&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
  &lt;value&gt;hive&lt;/value&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
  &lt;description&gt;username to use against metastore database&lt;/description&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
&lt;/property&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
&lt;property&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
  &lt;name&gt;javax.jdo.option.ConnectionPassword&lt;/name&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
  &lt;value&gt;hivepasswd&lt;/value&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
  &lt;description&gt;password to use against metastore database&lt;/description&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
&lt;/property&gt;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
5.复制mysql连接库包</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
#cp mysql-connector-java-5.1.11-bin.jar hadoop-1.0.3/hive-0.9.0/lib</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
4.启动hive</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
#hadoop-1.0.3/hive-0.9.0/bin/hive</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
5.去掉log4j过期警告</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
#cp hadoop-1.0.3/hive-0.9.0/conf/hive-log4j.properties.template hadoop-1.0.3/hive-0.9.0/conf/hive-log4j.properties</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
#vim hadoop-1.0.3/hive-0.9.0/conf/hive-log4j.properties</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
找到“log4j.appender.EventCounter”将他的值变更为：“org.apache.hadoop.log.metrics.EventCounter”</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
 </div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
五、安装php访问hive数据库插件</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
1.解压缩thrift包</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
#tar zxvf thrift-0.8.0.tar.gz</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
2.不要ruby支持否则会报错</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
#cd thrift-0.8.0</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
#./configure --without-ruby</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
#make &amp;&amp; make install</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
3.后台启动hive</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
#hadoop-1.0.3/hive-0.9.0/bin/hive --service hiveserver&gt;/dev/null 2&gt;&amp;1 &amp;</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
4.准备Thrifht库包放在/home/hadoop目录下，这个包可以在网上下载</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
5.编写php程序</div>
<div style="color:rgb(51,51,51);line-height:26px;font-family:Helvetica, Arial, sans-serif;font-size:16px;">
<pre><code class="language-html">&lt;/pre&gt;&lt;pre name="code" class="php"&gt;&lt;?
//php连接hive thrift依赖包路径
$GLOBALS['THRIFT_ROOT'] = '/home/hadoop/Thrift/';
//load the required files for connecting to Hive
require_once $GLOBALS['THRIFT_ROOT'] . 'packages/hive_service/ThriftHive.php';
require_once $GLOBALS['THRIFT_ROOT'] . 'transport/TSocket.php';
require_once $GLOBALS['THRIFT_ROOT'] . 'protocol/TBinaryProtocol.php';
//Set up the transport/protocol/client
$transport = new TSocket('192.168.1.247', 10000);
$protocol = new TBinaryProtocol($transport);
$client = new ThriftHiveClient($protocol);
$transport-&gt;open();
//run queries, metadata calls etc
$client-&gt;execute('show tables');
var_dump($client-&gt;fetchAll());
$transport-&gt;close();
?&gt;</code></pre><br><br><br></div>
            </div>
                </div>