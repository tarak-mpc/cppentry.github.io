---
layout:     post
title:      Hadoop分布式集群搭建（一）创建hadoop用户、配置ssh、安装jdk
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/l294265421/article/details/45642887				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:24px;">（集群中的每一台机器都需要执行下面的操作）<br></span></p>
<p><span style="font-size:24px;">1.添加组及在组中添加一个用户:</span></p>
<p><span style="font-size:24px;">addgroup hadoop</span></p>
<p><span style="font-size:24px;">adduser --ingroup hadoop hadoop(第一hadoop是组名，第二个hadoop是用户名)</span></p>
<p><span style="font-size:24px;">2.配置ssh</span></p>
<p><span style="font-size:24px;">ssh-keygen -t rsa(指定通过rsa算法创建公/私钥对；在要求输入保存key的文件路径时<span style="font-size:24px;">，直接点击“Enter”就行；</span>在要求输入密码时，也直接点击“Enter”就行，这样就可以无密码登陆，因为hadoop集群中各个节点进行交互的时候，是通过无密码的ssh进行登陆的）</span></p>
<p><span style="font-size:24px;"><span style="font-size:24px;">cp .ssh/id_rsa.pub <span style="font-size:24px;">
.ssh/</span>authorized_keys(把公钥复制到<span style="font-size:24px;">authorized_keys</span>,<span style="font-size:24px;"><span style="font-size:24px;"><span style="font-size:24px;">authorized_keys</span></span>用于保存其它主机的公钥</span>)</span><br></span></p>
<p><span style="font-size:24px;">通过</span></p>
<p><span style="font-size:24px;">ssh localhost</span></p>
<p><span style="font-size:24px;">测试现在是否可以无密码登陆到该机器。(</span></p>
<p><span style="font-size:24px;">如果报下面的错误：</span></p>
<p><span style="font-size:24px;">ssh:connect to host localhost port 22: Connection refused</span></p>
<p><span style="font-size:24px;">可以用下面的命令察看运行的ssh相关进程：</span></p>
<p><span style="font-size:24px;">ps -e|grep ssh</span></p>
<p><span style="font-size:24px;">如果只看到ssh-agent进程，说明需要安装服务器进程，用下面的命令进行安装：</span></p>
<p><span style="font-size:24px;">sudo apt-get install openssh-server<br></span></p>
<p><span style="font-size:24px;">)</span></p>
<p><span style="font-size:24px;">3.安装jdk</span></p>
<p><span style="font-size:24px;">下载jdk，我下载的是jdk-7u67-linux-x64.tar.gz，并把它放在/home/hadoop下；解压该文件，得到文件夹：jdk1.7.0_67；设置JAVA_HOME，在.profile文件的最后加上这句：</span></p>
<p><span style="font-size:24px;">export JAVA_HOME=/home/hadoop/<span style="font-size:24px;">jdk1.7.0_67</span></span></p>
<p><span style="font-size:24px;"><span style="font-size:24px;">(参考：nutch相关框架视频教程)</span><br></span></p>
            </div>
                </div>