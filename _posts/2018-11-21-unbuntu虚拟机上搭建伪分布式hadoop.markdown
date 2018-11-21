---
layout:     post
title:      unbuntu虚拟机上搭建伪分布式hadoop
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/nuisthou/article/details/48518231				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;">一. 你要安装Ubuntu这一步省略；</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;">二. 在Ubuntu下创建hadoop用户组和用户;</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;">1. 创建hadoop用户组：</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;"> sudo addgroup hadoop</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;"><br>
2. 创建hadoop用户：</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;"> sudo adduser -ingroup hadoop hadoop</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;"><br>
3. 给hadoop用户添加权限，打开/etc/sudoers文件：</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;"> sudo gedit /etc/sudoers</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;"><br>
按回车键后就会打开/etc/sudoers文件了，给hadoop用户赋予root用户同样的权限。</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;">在root   ALL=(ALL:ALL)   ALL下添加：hadoop   ALL=(ALL:ALL)  ALL，</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;"><br>
三. 在Ubuntu下安装JDK</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;">1、这里使用的是jdk-6u37-linux-i586.bin(可以直接去官网下载：<a href="http://www.oracle.com/technetwork/java/javase/downloads/jdk6u37-downloads-1859587.html" rel="nofollow" style="text-decoration:none;color:rgb(82,102,115);">http://www.oracle.com/technetwork/java/javase/downloads/jdk6u37-downloads-1859587.html</a>)<br>
2、在/usr/下新建java目录：sudo mkdir /usr/java<br>
3、拷贝文件至该新建目录：sudo cp /home/administrator/jdk-6u37-linux-i586.bin /usr/java  （注意：我是用U盘拷到administratior的）<br>
4、更改文件权限，使之可以运行：sudo chmod u+x jdk-6u37-linux-i586.bin<br>
5、运行该文件：sudo ./jdk-6u37-linux-i586.bin 。至此，usr/java/目录下面有一个bin文件包jdk1.6.0_37，以及解压后的同名文件夹。<br>
6.然后要配置环境变量<br>
(1)<br>
sudo gedit /etc/environment <br>
在文件中添加<br>
gedit代码<br>
export JAVA_HOME=/usr/java/jdk1.6.0_37<br>
export JRE_Home=/usr/java/jdk1.6.0_37/jre<br>
export CLASSPATH=$CLASSPATH:$JAVA_HOME/lib:$JAVA_HOME/jre/lib</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;">(2)然后修改第二个文件<br>
sudo gedit /etc/profile <br>
在文件中最后一行的前面添加<br>
export JAVA_HOME=/usr/java/jdk1.6.0_37<br>
export JRE_HOME=/usr/java/jdk1.6.0_37/jre<br>
export CLASSPATH=$CLASSPATH:$JAVA_HOME/lib:$JAVA_HOME/jre/lib <br>
export PATH=$JAVA_HOME/bin:$JAVA_HOME/jre/bin:$PATH:$HOME/bin</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;">(3)由于ubuntu中一般安装有默认的jdk，如openjdk，所以，为了使默认使用的是我们安装的jdk，还要进行如下工作。<br>
sudo update-alternatives --install /usr/bin/java java /usr/java/jdk1.6.0_37/bin/java 300<br>
sudo update-alternatives --install /usr/bin/javac javac /usr/java/jdk1.6.0_37/bin/javac 300</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;">(4)通过这一步将我们安装的jdk加入java选单。<br>
sudo update-alternatives --config java<br>
通过这一步选择系统默认的jdk</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;">(5)终端输入：<br>
java -version<br>
显示:<br>
java version "jdk1.6.0_37"<br>
Java(TM) SE Runtime Environment (jdk1.6.0_37-b06)<br>
Java HotSpot(TM) Server VM (build 20.12-b01, mixed mode)<br>
即配置成功</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;">四. 修改机器名</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;">每当ubuntu安装成功时，我们的机器名都默认为：ubuntu ，但为了以后集群中能够容易分辨各台服务器，需要给每台机器取个不同的名字。机器名由 /etc/hostname文件决定。</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;">1. 打开/etc/hostname文件：</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;"> sudo gedit /etc/hostname</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;"><br>
2. 将/etc/hostname文件中的ubuntu改为你想取的机器名。这里我取"yu-ubuntu"。 重启系统后才会生效。</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;"><br>
五. 安装ssh服务</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;">这里的ssh和三大框架:spring,struts,hibernate没有什么关系，ssh可以实现远程登录和管理，具体可以参考其他相关资料。</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;">安装openssh-server，</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;">sudo apt-get install ssh openssh-server (在这一步可能由于权限不够而进行不了，可以sudo su - 进入root权限)</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;"><br>
六、 建立ssh无密码登录本机</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;">首先要转换成hadoop用户，执行以下命令：</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;">su - hadoop</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;"><br>
ssh生成密钥有rsa和dsa两种生成方式，默认情况下采用rsa方式。</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;">1. 创建ssh-key，，这里我们采用rsa方式：</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;"> ssh-keygen -t rsa -P ""</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;">（注：回车后会在~/.ssh/下生成两个文件：id_rsa和id_rsa.pub这两个文件是成对出现的）</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;">2. 进入~/.ssh/目录下，将id_rsa.pub追加到authorized_keys授权文件中，开始是没有authorized_keys文件的：</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;">cd ~/.ssh<br>
cat id_rsa.pub &gt;&gt; authorized_keys</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;">（完成后就可以无密码登录本机了。）</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;">3.登录localhost：</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;">ssh localhost</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;">( 注：当ssh远程登录到其它机器后，现在你控制的是远程的机器，需要执行退出命令才能重新控制本地主机。)</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;">4. 执行退出命令：</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;">exit</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;"><br>
七. 安装hadoop（必须在ssh安装好后才可以安装hadoop）</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;">这里采用的hadoop版本是：hadoop-0.20.203（<a href="http://www.apache.org/dyn/closer.cgi/hadoop/common/" rel="nofollow" style="text-decoration:none;color:rgb(82,102,115);">http://www.apache.org/dyn/closer.cgi/hadoop/common/</a>），因为该版本比较稳定。</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;">1. 假设hadoop-0.20.203.tar.gz在桌面，将它复制到安装目录 /usr/local/下：</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;"> sudo cp hadoop-0.20.203.0rc1.tar.gz /usr/local/</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;"><br>
2. 解压hadoop-0.20.203.tar.gz：</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;"> cd /usr/local<br>
sudo tar -zxf hadoop-0.20.203.0rc1.tar.gz</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;"><br>
3. 将解压出的文件放到hadoop文件夹下：</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;"> sudo mv hadoop-0.20.203.0 hadoop</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;"><br>
4. 将该hadoop文件夹的属主用户设为hadoop：</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;"> sudo chown -R hadoop:hadoop hadoop</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;"><br></span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;">下面三个文件的配置信息，请参考《实战hadoop》这本书<br>
5. 打开hadoop/conf/hadoop-env.sh文件：</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;"> sudo gedit hadoop/conf/hadoop-env.sh</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;"><br>
6. 配置conf/hadoop-env.sh（找到#export JAVA_HOME=...,去掉#，然后加上本机jdk的路径）：</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;"> export JAVA_HOME=/usr/java/jdk1.6.0_37</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;">7. 打开conf/core-site.xml文件：</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;"> sudo gedit hadoop/conf/core-site.xml</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;"><br>
编辑如下：<br>
                                                        <br><br>
         <br>
       fs.default.name     <br>
       hdfs://localhost:9000     <br>
       <br></span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;">8. 打开conf/mapred-site.xml文件：</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;"> sudo gedit hadoop/conf/mapred-site.xml</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;">编辑如下：<br>
                                                   <br>
        <br>
               <br>
         mapred.job.tracker         <br>
         localhost:9001         <br>
            <br></span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;">9. 打开conf/hdfs-site.xml文件：</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;"> sudo gedit hadoop/conf/hdfs-site.xml</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;"><br>
编辑如下：</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;"><br>
    <br>
         dfs.replication<br>
         1<br>
    <br></span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;">10. 打开conf/masters文件，添加作为secondarynamenode的主机名，作为单机版环境，这里只需填写 localhost 就Ok了。</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;"> sudo gedit hadoop/conf/masters</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;"><br>
11. 打开conf/slaves文件，添加作为slave的主机名，一行一个。作为单机版，这里也只需填写 localhost就Ok了。</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;"> sudo gedit hadoop/conf/slaves</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;"><br>
八. 在单机上运行hadoop</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;">1. 进入hadoop目录下，格式化hdfs文件系统，初次运行hadoop时一定要有该操作，</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;">cd /usr/local/hadoop/</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;">bin/hadoop namenode -format</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;"><br>
2. hdfs文件系统格式化成功</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;">下面一步，必须在用ssh localhost 之后才可以启动。</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;"> </span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;">3. 启动bin/start-all.sh：（<span style="color:#FF0000;">注意在这一步可能会由于权限不够而不能执行，我们需要将解压的java文件夹的属主改为hadoop用户</span>）</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;">bin/start-all.sh</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;"><br>
4. 检测hadoop是否启动成功：</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;">jps</span></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(73,73,73);font-family:simsun;background-color:rgb(226,226,226);">
<span style="font-size:18px;"><br>
如果有Namenode，SecondaryNameNode，TaskTracker，DataNode，JobTracker五个进程，就说明你的hadoop单机版环境配置好了！</span></p>
            </div>
                </div>