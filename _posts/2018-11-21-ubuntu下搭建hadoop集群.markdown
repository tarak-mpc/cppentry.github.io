---
layout:     post
title:      ubuntu下搭建hadoop集群
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h2>                                         Hadoop的伪分布式安装</h2>

<p> </p>

<h2 id="一准备工作">一、准备</h2>

<h3 id="11创建hadoop用户"><a name="t1"></a>1.1创建hadoop用户</h3>

<pre>
$ sudo useradd -m hadoop -s /bin/bash  #创建hadoop用户，并使用/bin/bash作为shell
$ sudo passwd hadoop                   #为hadoop用户设置密码，之后需要连续输入两次密码
$ sudo adduser hadoop sudo             #为hadoop用户增加管理员权限
$ su - hadoop                          #切换当前用户为用户hadoop
$ sudo apt-get update                  #更新hadoop用户的apt,方便后面的安装
</pre>

<h3 id="12安装ssh并配置ssh无密码登陆">1.2安装SSH,设置SSH无密码登陆</h3>

<pre>
$ sudo apt-get install openssh-server   #安装SSH server
$ ssh localhost                         #登陆SSH，第一次登陆输入yes
$ exit                                  #退出登录的ssh localhost
$ cd ~/.ssh/                            #如果没法进入该目录，执行一次ssh localhost
$ ssh-keygen -t rsa　　</pre>

<p>输入完  $ ssh-keygen -t rsa　语句以后，需要连续敲击三次回车，如下图：</p>

<p><img alt="" class="has" src="https://images2017.cnblogs.com/blog/1139343/201709/1139343-20170928110119965-299294660.png"></p>

<p>其中，第一次回车是让KEY存于默认位置，以方便后续的命令输入。第二次和第三次是确定passphrase，相关性不大。两次回车输入完毕以后，如果出现类似于下图所示的输出，即成功：</p>

<p><img alt="" class="has" src="https://images2017.cnblogs.com/blog/1139343/201709/1139343-20170928110536840-777008126.jpg"></p>

<p>之后再输入：</p>

<pre>
$ cat ./id_rsa.pub &gt;&gt; ./authorized_keys #加入授权
$ ssh localhost                         #此时已不需密码即可登录localhost，并可见下图。如果失败则可以搜索SSH免密码登录来寻求答案</pre>

<p><img alt="" class="has" src="https://images2017.cnblogs.com/blog/1139343/201709/1139343-20170928110719637-31277406.png"></p>

<h2 id="二安装jdk17">二、安装jdk1.8</h2>

<p>首先在<a href="http://www.oracle.com/technetwork/java/javase/downloads/index.html%20" rel="nofollow">oracle官网</a>下载jdk1.8 <a href="http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html" rel="nofollow">http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html</a>接下来进行安装与环境变量配置，根据个人电脑系统位数选择对应版本，64位就选择x64。</p>

<pre>
$ mkdir /usr/lib/jvm                           #创建jvm文件夹
$ sudo tar zxvf jdk-7u80-linux-x64.tar.gz  -C /usr/lib #/ 解压到/usr/lib/jvm目录下
$ cd /usr/lib/jvm                                 #进入该目录
$ mv  jdk1.7.0_80 java                         #重命名为java
$ vi ~/.bashrc                                 #给JDK配置环境变量</pre>

<p>注：其中如果权限不够，无法在相关目录下创建jvm文件夹，那么可以使用 $ sudo -i  语句进入root账户来创建文件夹。</p>

<p>另外推荐使用vim来编辑环境变量，即最后一句使用指令</p>

<pre>
$ vim ~/.bashrc </pre>

<p>如果没有vim,可以使用：</p>

<pre>
$sudo apt-get install vim</pre>

<p>来进行下载。</p>

<p> </p>

<pre>

 </pre>

<p><img alt="" class="has" src="https://images2017.cnblogs.com/blog/1139343/201709/1139343-20170928111356262-1434564476.png"></p>

<p> </p>

<p> 在.bashrc文件添加如下指令：</p>

<pre>
export JAVA_HOME=/usr/lib/jvm/java
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH</pre>

<p> </p>

<p>在文件修改完毕以后，输入代码：</p>

<pre>
$ source ~/.bashrc                       #使新配置的环境变量生效
$ java -version                          #检测是否安装成功，查看java版本</pre>

<p>如果出现如下图所示的内容，即为安装成功</p>

<p><img alt="" class="has" height="84" src="https://img-blog.csdn.net/20180721152305584?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L215bTA4MTg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="569"></p>

<p> </p>

<p>注：如果各位不想一个一个的敲击，可以复制黏贴，但因为vim不支持系统粘贴板，所以需要先下载相关插件vim-gnome</p>

<pre>
sudo apt-get install vim-gnome</pre>

<p>然后复制相关代码，光标移到指定位置，使用指令 "+p，即可复制，注意 " 也是需要敲击的内容，即一共有 " 、+、p 三个操作符需要敲入，如果你安装的是ubuntu桌面版就可以直接复制粘贴。</p>

<p> </p>

<h2 id="三安装hadoop-260">三、安装hadoop-2.6.0</h2>

<p>先下载hadoop-2.6.0.tar.gz，链接如下: <br>
http://mirrors.hust.edu.cn/apache/hadoop/common/</p>

<p>下面进行安装：</p>

<pre>
$ sudo tar -zxvf  hadoop-2.6.0.tar.gz -C /usr/local    #解压到/usr/local目录下
$ cd /usr/local
$ sudo mv  hadoop-2.6.0    hadoop                      #重命名为hadoop
$ sudo chown -R hadoop ./hadoop                        #修改文件权限</pre>

<p>给hadoop配置环境变量，将下面代码添加到.bashrc文件:</p>

<pre>
export HADOOP_HOME=/usr/local/hadoop
export CLASSPATH=$($HADOOP_HOME/bin/hadoop classpath):$CLASSPATH
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin</pre>

<p> </p>

<p> </p>

<p><img alt="" class="has" src="https://images2017.cnblogs.com/blog/1139343/201709/1139343-20170928113521434-2017853701.png"></p>

<p> </p>

<p>同样，执行source ~./bashrc使设置生效，并查看hadoop是否安装成功</p>

<p><img alt="这里写图片描述" class="has" src="https://img-blog.csdn.net/20170321124003176?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvTXJfS2tUaWFu/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast"></p>

<p> </p>

<h2 id="四伪分布式配置">四、伪分布式配置</h2>

<p>Hadoop 可以在单节点上以伪分布式的方式运行，Hadoop 进程以分离的 Java 进程来运行，节点既作为 NameNode 也作为 DataNode，同时，读取的是 HDFS 中的文件。Hadoop 的配置文件位于 /usr/local/hadoop/etc/hadoop/ 中，伪分布式需要修改2个配置文件 core-site.xml 和 hdfs-site.xml 。Hadoop的配置文件是 xml 格式，每个配置以声明 property 的 name 和 value 的方式来实现。首先将jdk1.7的路径添（export JAVA_HOME=/usr/lib/jvm/java ）加到hadoop-env.sh文件 </p>

<p> </p>

<p> <img alt="" class="has" src="https://images2017.cnblogs.com/blog/1139343/201709/1139343-20170928113620325-1212521989.png"></p>

<p>接下来修改core-site.xml文件：</p>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<pre>
&lt;configuration&gt;
        &lt;property&gt;
             &lt;name&gt;hadoop.tmp.dir&lt;/name&gt;
             &lt;value&gt;file:/usr/local/hadoop/tmp&lt;/value&gt;
             &lt;description&gt;Abase for other temporary directories.&lt;/description&gt;
        &lt;/property&gt;
        &lt;property&gt;
             &lt;name&gt;fs.defaultFS&lt;/name&gt;
             &lt;value&gt;hdfs://localhost:9000&lt;/value&gt;
        &lt;/property&gt;
&lt;/configuration&gt;</pre>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<p> </p>

<p> </p>

<p><img alt="" class="has" src="https://images2017.cnblogs.com/blog/1139343/201709/1139343-20170928113648528-107334336.png"></p>

<p> </p>

<p>接下来修改配置文件 hdfs-site.xml</p>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<pre>
&lt;configuration&gt;
        &lt;property&gt;
             &lt;name&gt;dfs.replication&lt;/name&gt;
             &lt;value&gt;1&lt;/value&gt;
        &lt;/property&gt;
        &lt;property&gt;
             &lt;name&gt;dfs.namenode.name.dir&lt;/name&gt;
             &lt;value&gt;file:/usr/local/hadoop/tmp/dfs/name&lt;/value&gt;
        &lt;/property&gt;
        &lt;property&gt;
             &lt;name&gt;dfs.datanode.data.dir&lt;/name&gt;
             &lt;value&gt;file:/usr/local/hadoop/tmp/dfs/data&lt;/value&gt;
        &lt;/property&gt;
&lt;/configuration&gt;</pre>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<p><img alt="" class="has" src="https://images2017.cnblogs.com/blog/1139343/201709/1139343-20170928113708481-69399513.png"></p>

<p> </p>

<p>Hadoop 的运行方式是由配置文件决定的（运行 Hadoop 时会读取配置文件），因此如果需要从伪分布式模式切换回非分布式模式，需要删除 core-site.xml 中的配置项。此外，伪分布式虽然只需要配置 fs.defaultFS 和 dfs.replication 就可以运行（可参考官方教程），不过若没有配置 hadoop.tmp.dir 参数，则默认使用的临时目录为 /tmp/hadoo-hadoop，而这个目录在重启时有可能被系统清理掉，导致必须重新执行 format 才行。所以我们进行了设置，同时也指定 dfs.namenode.name.dir 和 dfs.datanode.data.dir，否则在接下来的步骤中可能会出错。</p>

<p>配置完成后，执行 NameNode 的格式化</p>

<pre>

 </pre>

<pre>
$ ./bin/hdfs namenode -format</pre>

<p>启动namenode和datanode进程，并查看启动结果</p>

<pre>

 </pre>

<pre>
$ ./sbin/start-dfs.sh
$ jps</pre>

<p>启动完成后，可以通过命令 jps 来判断是否成功启动，若成功启动则会列出如下进程: “NameNode”、”DataNode” 和 “SecondaryNameNode” </p>

<p> <img alt="" class="has" src="https://images2017.cnblogs.com/blog/1139343/201709/1139343-20170928113759184-1736102726.png"></p>

<p> </p>

<p>此时也有可能出现要求输入localhost密码的情况 ，如果此时明明输入的是正确的密码却仍无法登入，其原因是由于如果不输入用户名的时候默认的是root用户，但是安全期间ssh服务默认没有开root用户的ssh权限</p>

<p>输入代码：</p>

<pre>
$vim /etc/ssh/sshd_config</pre>

<p>检查PermitRootLogin 后面是否为yes，如果不是，则将该行代码 中PermitRootLogin 后面的内容删除，改为yes，保存。之后输入下列代码重启SSH服务：</p>

<pre>
$ /etc/init.d/sshd restart</pre>

<p>即可正常登入（免密码登录参考第一章）</p>

<p> </p>

<p> </p>

<p>成功启动后，可以访问 Web 界面 <a href="http://localhost:50070/" rel="nofollow">http://localhost:50070</a> 查看 NameNode 和 Datanode 信息，还可以在线查看 HDFS 中的文件。 </p>

<p><img alt="" class="has" src="https://images2017.cnblogs.com/blog/1139343/201709/1139343-20170928113820059-1133518394.png"></p>

<p> </p>

<p>至此，hadoop的安装就已经完成啦！enjoy it！</p>

<p> </p>

<p>这是原文：<a href="https://www.cnblogs.com/87hbteo/p/7606012.html" rel="nofollow">https://www.cnblogs.com/87hbteo/p/7606012.html</a></p>

<p> </p>            </div>
                </div>