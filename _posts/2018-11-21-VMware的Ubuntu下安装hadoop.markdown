---
layout:     post
title:      VMware的Ubuntu下安装hadoop
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:24px;">一共四大步：</span></p>
<p><strong>一.在Ubuntu下创建hadoop组合hadoop用户，并用hadoop用户登录系统</strong></p>
<p><strong>增加hadoop用户组，同时在该组里增加hadoop用户<span style="font-family:'宋体';">，</span>后续在涉及到hadoop操作时，我们使用<span style="font-family:'宋体';">该</span>用户<span style="font-family:'宋体';">。</span></strong></p>
<p><strong></strong></p>
<h3><span style="font-family:'宋体';">1、创建</span>hadoop<span style="font-family:'宋体';">用户组</span></h3>
<img src="https://img-blog.csdn.net/20151114233423594?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><p></p>
<p><strong>2、创建hadoop用户</strong></p>
<p><strong>sudo adduser -ingroup hadoop hadoop</strong><br></p>
<p><strong><strong>回车后会提示输入新的UNIX密码，这是新建用户hadoop的密码</strong></strong></p>
<p><strong><strong>最后确认信息是否正确，如果没问题，输入 Y，回车即可。</strong><br></strong></p>
<p><strong><img src="https://img-blog.csdn.net/20151114233722897?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><br>
3、为hadoop用户添加权限<br><br>
输入：sudo gedit /etc/sudoers<br></strong></p>
<p><strong><img src="https://img-blog.csdn.net/20151114234028307?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></strong></p>
<p><strong><strong>回车，打开sudoers文件</strong></strong></p>
<p><strong><strong><strong>给hadoop用户赋予和root用户同样的权限</strong><br></strong></strong></p>
<p><strong><img src="https://img-blog.csdn.net/20151114234214786?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></strong></p>
<p><strong>4.用创建的hadoop用户登录Ubuntu系统<br></strong></p>
<p><strong>二.安装ssh</strong></p>
<p><strong>sudo apt-get install openssh-server</strong></p>
<p><strong><img src="https://img-blog.csdn.net/20151114234437748?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></strong></p>
<p><strong>安装完成后，启动ssh服务</strong></p>
<p><strong>sudo /etc/init.d/ssh start</strong></p>
<p><br></p>
<p><strong>查看服务是否正确启动：ps -e | grep ssh</strong></p>
<p><strong><img src="https://img-blog.csdn.net/20151114234812243?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></strong></p>
<p><strong>注意（有的Ubuntu系统下查看服务的信息如下图）</strong></p>
<p><strong><img src="https://img-blog.csdn.net/20151114234949363?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></strong></p>
<p><br><strong></strong></p>
<p>设置免密码登录，生成私钥和公钥</p>
<p>ssh-keygen -t rsa -P ""</p>
<img src="https://img-blog.csdn.net/20151114235105474?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><p><br><strong></strong></p>
<p>此时会在／home／hadoop/.ssh下生成两个文件：id_rsa和id_rsa.pub，前者为私钥，后者为公钥。</p>
<p>下面我们将公钥追加到authorized_keys中，它用户保存所有允许以当前用户身份登录到ssh客户端用户的公钥内容。</p>
<p>cat ~/.ssh/id_rsa.pub &gt;&gt; ~/.ssh/authorized_keys</p>
<img src="https://img-blog.csdn.net/20151114235155045?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><p><br><strong></strong></p>
<p>登录ssh</p>
<p>ssh localhost</p>
<img src="https://img-blog.csdn.net/20151114235246878?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><p><strong></strong></p>
<p>退出</p>
<p>exit</p>
<br><p><strong>三.安装java环境</strong></p>
<p><strong>sudo apt-get install openjdk-7-jdk</strong></p>
<p><strong><img src="https://img-blog.csdn.net/20151114235400463?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></strong></p>
<p><strong>查看安装结果，输入命令：java -version，结果如下表示安装成功。</strong></p>
<p><strong><img src="https://img-blog.csdn.net/20151114235525195?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></strong></p>
<p><strong><br></strong></p>
<p><strong>四.安装hadoop2.6.0</strong></p>
<p><br><strong></strong></p>
<h3>1<span style="font-family:'宋体';">、官网下载</span><span style="text-decoration:underline;">http://mirror.bit.edu.cn/apache/hadoop/common/</span></h3>
<p></p>
<p> 注意：下载的是<strong>hadoop-2.6.0.tar.gz</strong>查看后缀小心下错</p>
<h3>2<span style="font-family:'宋体';">、安装</span></h3>
<strong>1. </strong>把hadoop解压到/usr/local下:<span><span></span></span><span><span><br></span></span>
<p><span><span>          sudo tar -zxvf hadoop-2.6.0.tar.gz  </span></span><span><br></span></p>
<p><span>           sudo mv hadoop-2.6.0 /usr/local/hadoop  </span><span><br></span></p>
<p><span>           sudo chmod -R 775 /usr/local/hadoop  </span><span><br></span></p>
<p><span>           sudo chown -R hadoop:hadoop /usr/local/hadoop</span></p>
<span><br></span>
<p><strong>2.配置~/.bashrc</strong></p>
<p><strong></strong><span style="font-family:'宋体';">配置</span>该文件前需要知道Java的安装路径，用来设置JAVA_HOME环境变量，可以使用下面命令行查看安装路径<br><strong></strong></p>
<p>        update-alternatives - -config java</p>
<p><img src="https://img-blog.csdn.net/20151115000311914?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p></p>
<p>           完整的路径为      /usr/lib/jvm/java-7-openjdk-amd64/jre/bin/java</p>
<p>           我们只取前面的部分     /usr/lib/jvm/java-7-openjdk-amd64</p>
<p>3.配置.bashrc文件</p>
<p>            sudo gedit ~/.bashrc</p>
<p>            该命令会打开该文件的编辑窗口，在文件末尾追加下面内容，然后保存，关闭编辑窗口。</p>
<p></p>
<p style="margin-left:44pt;">#HADOOP VARIABLES START</p>
<p style="margin-left:44pt;">export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64</p>
<p style="margin-left:44pt;">export HADOOP_INSTALL=/usr/local/hadoop</p>
<p style="margin-left:44pt;">export PATH=$PATH:$HADOOP_INSTALL/bin</p>
<p style="margin-left:44pt;">export PATH=$PATH:$HADOOP_INSTALL/sbin</p>
<p style="margin-left:44pt;">export HADOOP_MAPRED_HOME=$HADOOP_INSTALL</p>
<p style="margin-left:44pt;">export HADOOP_COMMON_HOME=$HADOOP_INSTALL</p>
<p style="margin-left:44pt;">export HADOOP_HDFS_HOME=$HADOOP_INSTALL</p>
<p style="margin-left:44pt;">export YARN_HOME=$HADOOP_INSTALL</p>
<p style="margin-left:44pt;">export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_INSTALL/lib/native</p>
<p style="margin-left:44pt;">export HADOOP_OPTS="-Djava.library.path=$HADOOP_INSTALL/lib"</p>
<p style="margin-left:44pt;">#HADOOP VARIABLES END</p>
            最终结果如下图：
<p><img src="https://img-blog.csdn.net/20151115000650615?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<br>
          执行下面命，使添加的环境变量生效：
<p><strong>           source ~/.bashrc</strong></p>
<p><strong>4.编辑/usr/local/hadoop/etc/hadoop/hadoop-env.sh</strong></p>
<p><strong>          执行下面命令<span style="font-family:'宋体';">，</span>打开该文件的编辑窗口</strong></p>
<p>          sudo gedit /usr/local/hadoop/etc/hadoop/hadoop-env.sh</p>
<p style="margin-left:36pt;">找到JAVA_HOME变量，修改此变量如下</p>
<p>        export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64    </p>
<p>        修改后的hadoop-env.sh文件如下所示<span style="font-family:'宋体';">：</span></p>
<img src="https://img-blog.csdn.net/20151115001248134?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><p></p>
<p><strong><br></strong></p>
<p><strong>五.WortCount测试</strong></p>
<p><strong>          单机模式安装完成，下面通过执行hadoop自带实例WordCount验证是否安装成功</strong></p>
<p><strong>           /usr/local/hadoop路径下创建input文件夹    </strong></p>
<p>              mkdir input</p>
<p>     <br></p>
<p>          拷贝README.txt到input    </p>
<p>              cp README.txt input</p>
<p>          执行WordCount</p>
<p>               bin/hadoop jar share/hadoop/mapreduce/sources/hadoop-mapreduce-examples-2.4.0-sources.jar org.apache.hadoop.examples.WordCount input output</p>
<p><img src="https://img-blog.csdn.net/20151115001510298?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>           执行结果：</p>
<p><img src="https://img-blog.csdn.net/20151115001648865?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>             执行 cat output/*，查看字符统计结果</p>
<p><img src="https://img-blog.csdn.net/20151115001804843?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<br>            </div>
                </div>