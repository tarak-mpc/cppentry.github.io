---
layout:     post
title:      centos7虚拟机（在系统和用户环境变量中）安装hadoop2.7.6分布式
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h2>NO.1安装配置hadoop系统环境变量</h2>

<p>只需要配置四个文件加一个路径，最后刷新一把</p>

<pre class="has">
<code>[root@localhost hadoop]# vim hadoop-env.sh
[root@localhost hadoop]# vim core-site.xml
[root@localhost hadoop]# vim hdfs-site.xml
[root@localhost hadoop]# vim mapred-site.xml.template
[root@localhost hadoop]# vim /etc/profile
[root@localhost hadoop]# source /etc/profile
</code></pre>

<p>第一个vim需要操作的内容</p>

<pre class="has">
<code>export JAVA_HOME=/home/jan/opt/jdk1.8.0_171    /在jdk所在文件进终端pwd找到此路径，复制过来就行
</code></pre>

<p><img alt="" class="has" height="73" src="https://img-blog.csdn.net/20180719104944996?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNjEyNjQ1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="810"></p>

<p>每个人jdk放的文件夹不同，路径也就不同，要具体问题具体分析，找自己的jdk所在位置就对了</p>

<p> </p>

<p>第二个vim需要操作的内容</p>

<pre class="has">
<code>&lt;configuration&gt;
&lt;property&gt;
&lt;name&gt;hadoop.tmp.dir&lt;/name&gt;
&lt;value&gt;/hadoop&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
&lt;name&gt;hadoop.name.dir&lt;/name&gt;
&lt;value&gt;/hadoop/name&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
&lt;name&gt;fs.default.name&lt;/name&gt;
&lt;value&gt;hdfs://localhost:9000&lt;/value&gt;               //localhost是我的主机名
&lt;/property&gt;
&lt;/configuration&gt;
</code></pre>

<p> </p>

<p>第三个vim需要操作的内容</p>

<pre class="has">
<code>&lt;configuration&gt;
&lt;property&gt;
&lt;name&gt;dfs.data.dir&lt;/name&gt;
&lt;value&gt;/hadoop/data&lt;/value&gt;
&lt;/property&gt;
&lt;/configuration&gt;
</code></pre>

<p>第四个vim需要操作的内容</p>

<pre class="has">
<code>&lt;configuration&gt;
&lt;property&gt;
&lt;name&gt;mapred.job.tracker&lt;/name&gt;
&lt;value&gt;localhost:9000&lt;/value&gt;              //localhost是我的主机名
&lt;/property&gt;
&lt;/configuration&gt;
</code></pre>

<p>路径需要配置的内容</p>

<pre class="has">
<code>export HADOOP_HOME=/home/jan/opt/hadoop-2.7.6        //hadoop所在的路径一定要对
export PATH=$HADOOP_HOME/bin:$PATH
</code></pre>

<p>最后刷新运行一下</p>

<pre class="has">
<code>[root@localhost hadoop]# source /etc/profile</code></pre>

<p>以上步骤都完成未报错的情况下，就可以开始格式化了，按照以下步骤进行就行</p>

<pre class="has">
<code>
1 ： cd $HADOOP_HOME

2 ： hdfs namenode -format

3 ： sbin/start-dfs.sh

4 ： sbin/start-yarn.sh</code></pre>

<p>以上四步完成后，检查一下网络是否连接，网络没问题的话，就在linux系统中额浏览器中输入以下内容，检验是否成功。 </p>

<pre class="has">
<code>localhost：50070       //localhost是我的主机名</code></pre>

<p> </p>

<h2>NO.2  安装配置hadoop用户环境变量</h2>

<p>前四个vim的操作同系统环境变量安装</p>

<p>路径有所不同</p>

<pre class="has">
<code>[jan@localhost opt]$ cd hadoop-2.7.6/
[jan@localhost hadoop-2.7.6]$ pwd
/home/jan/opt/hadoop-2.7.6
[jan@localhost hadoop-2.7.6]$ hadooppath=`pwd`
[jan@localhost hadoop-2.7.6]$ echo $hadooppath
/home/jan/opt/hadoop-2.7.6

[jan@localhost hadoop-2.7.6]$ echo export HADOOP_HOME=$hadooppath
export HADOOP_HOME=/home/jan/opt/hadoop-2.7.6
[jan@localhost hadoop-2.7.6]$ echo export HADOOP_HOME=$hadooppath &gt;&gt; ~/.bashrc
[jan@localhost hadoop-2.7.6]$ echo export PATH=\$HADOOP_HOME/bin:\$PATH
export PATH=$HADOOP_HOME/bin:$PATH
[jan@localhost hadoop-2.7.6]$ echo export PATH=\$HADOOP_HOME/bin:\$PATH &gt;&gt; ~/.bashrc

[jan@localhost hadoop-2.7.6]$ source ~/.bashrc
[jan@localhost hadoop-2.7.6]$ echo $HADOOP_HOME

[root@localhost hadoop]# cd $HADOOP_HOME
[root@localhost hadoop-2.7.6]# hdfs namenode -format
[root@localhost hadoop-2.7.6]# sbin/start-dfs.sh
[root@localhost hadoop-2.7.6]# sbin/start-yarn.sh



//最后同样在浏览器中  localhost：50070    检验一下是否可以连接到网络




</code></pre>

<p> </p>            </div>
                </div>