---
layout:     post
title:      ubuntu 上搭建hdfs单机模式
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/DaBingShui/article/details/79160340				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="color:rgb(69,69,69);background-color:rgb(255,255,255);">1.<span style="font-family:SimSun;font-size:18px;">为了 方便以后对hadoop的管理，我们<span style="color:rgb(51,51,51);text-align:justify;background-color:rgb(250,250,252);">需要增加一个hadoop用户，</span></span></span><span style="color:rgb(51,51,51);font-family:SimSun;font-size:18px;text-indent:36px;"> </span></p>
<p><span style="color:rgb(51,51,51);font-family:SimSun;font-size:18px;text-indent:36px;">addgroup hadoop</span></p>
<p><span style="background-color:rgb(250,250,252);color:rgb(51,51,51);font-family:SimSun;font-size:18px;text-indent:36px;">adduser  -ingroup hadoop hadoop</span></p>
<p><span style="color:rgb(69,69,69);background-color:rgb(255,255,255);"><span style="font-family:SimSun;font-size:18px;"><span style="color:rgb(51,51,51);text-align:justify;background-color:rgb(250,250,252);"><img src="https://img-blog.csdn.net/20180125144248750?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRGFCaW5nU2h1aQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></span></span></span></p>
<p><span style="color:rgb(69,69,69);background-color:rgb(255,255,255);"><span style="font-family:SimSun;font-size:18px;"><span style="color:rgb(51,51,51);text-align:justify;background-color:rgb(250,250,252);"><br></span></span></span></p>
<p><span style="color:rgb(69,69,69);background-color:rgb(255,255,255);"><span style="font-family:SimSun;font-size:18px;"><span style="color:rgb(51,51,51);text-align:justify;background-color:rgb(250,250,252);">给hadoop赋于root权限</span></span></span></p>
<p><span style="color:rgb(69,69,69);background-color:rgb(255,255,255);"><span style="font-family:SimSun;font-size:18px;"><span style="color:rgb(51,51,51);text-align:justify;background-color:rgb(250,250,252);">sudo adduser hadoop sudo<br></span></span></span></p>
<p><span style="color:rgb(69,69,69);background-color:rgb(255,255,255);"><span style="font-family:SimSun;font-size:18px;"><span style="color:rgb(51,51,51);text-align:justify;background-color:rgb(250,250,252);"><br></span></span></span></p>
<p><span style="color:rgb(69,69,69);background-color:rgb(255,255,255);"><span style="font-family:SimSun;font-size:18px;"><span style="color:rgb(51,51,51);text-align:justify;background-color:rgb(250,250,252);"><img src="https://img-blog.csdn.net/20180125125039349?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRGFCaW5nU2h1aQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></span></span></span></p>
<p><span style="color:rgb(69,69,69);background-color:rgb(255,255,255);"><span style="font-family:SimSun;font-size:18px;"><span style="color:rgb(51,51,51);text-align:justify;background-color:rgb(250,250,252);">2 </span></span></span><span style="font-size:18px;background-color:rgb(250,250,252);color:rgb(51,51,51);font-family:SimSun;">安装SSH
 server、配置SSH无密码登陆</span></p>
<p><span style="font-size:18px;background-color:rgb(250,250,252);color:rgb(51,51,51);font-family:SimSun;"></span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;background-color:rgb(250,250,252);">
Ubuntu默认安装了SSH client，还需要安装SSH server。</p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;background-color:rgb(250,250,252);">
</p>
<pre style="background-color:rgb(250,250,252);text-align:justify;color:rgb(51,51,51);"><code>sudo apt-get install openssh-server</code></pre>
<p></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;background-color:rgb(250,250,252);">
集群、单节点模式都需要用到SSH无密码登陆，首先设置SSH无密码登陆本机。</p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;background-color:rgb(250,250,252);">
输入命令</p>
<pre style="color:rgb(51,51,51);text-align:justify;background-color:rgb(250,250,252);"><code>ssh localhost
</code></pre>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;background-color:rgb(250,250,252);">
会有如下提示(SSH首次登陆提示)，输入yes。</p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;background-color:rgb(250,250,252);">
<img src="https://img-blog.csdn.net/20180125125458718?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRGFCaW5nU2h1aQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
SSH首次登陆提示,然后按提示输入密码hadoop，这样就登陆到本机了。但这样的登陆是需要密码的，需要配置成无密码登陆。<br><br>
先退出刚才的ssh，然后生成ssh证书：<br><br>
exit                           # 退出 ssh localhost<br>
cd ~/.ssh                      # 如果没有该目录，先执行一次ssh localhost<br>
ssh-keygen -t rsa              # 一直按回车就可以<br>
cp id_rsa.pub authorized_keys<br>
此时再用ssh localhost命令，就可以直接登陆了，如下图所示。<br><p><span style="font-size:18px;background-color:rgb(250,250,252);color:rgb(51,51,51);font-family:SimSun;"><img src="https://img-blog.csdn.net/20180125125527048?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRGFCaW5nU2h1aQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></span></p>
<p><br></p>
<p><span style="color:rgb(69,69,69);background-color:rgb(255,255,255);"><br></span></p>
<p><span style="color:rgb(69,69,69);background-color:rgb(255,255,255);">3.由于hadoop是java语言写的，需要使用java的运行环境，因此必须要先安装java，安装过程这里就不多说了。</span></p>
<p><span style="color:rgb(69,69,69);background-color:rgb(255,255,255);"><img src="https://img-blog.csdn.net/20180125122956613?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRGFCaW5nU2h1aQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></span></p>
<p><span style="color:rgb(69,69,69);background-color:rgb(255,255,255);"><br></span></p>
<p><span style="color:rgb(69,69,69);background-color:rgb(255,255,255);">4.<span style="color:rgb(69,69,69);background-color:rgb(255,255,255);">Ubuntu16.04默认安装后并不安装sshd。使用命令"sudo apt-get install openssh-server"安装。安装后使用"ps aux | grep sshd"命令查看sshd是否启动</span></span></p>
<p><span style="color:rgb(69,69,69);background-color:rgb(255,255,255);"><span style="color:rgb(69,69,69);background-color:rgb(255,255,255);"><img src="https://img-blog.csdn.net/20180125123024973?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRGFCaW5nU2h1aQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></span></span></p>
<p><span style="color:#454545;"><span style="background-color:rgb(255,255,255);">5.安装hadoop ，官网：http://hadoop.apache.org/releases.html，我下的是3.0.0的，下载完后，解压到/usr/local目录下，</span></span></p>
<p><span style="color:#454545;"><span style="background-color:rgb(255,255,255);">cd ~/Downloads/</span></span></p>
<p><span style="color:#454545;"><span style="background-color:rgb(255,255,255);">sudo tar -zxvf hadoop-3.0.0.tar.gz -C /usr/local/<br></span></span></p>
<p><span style="color:#454545;"><span style="background-color:rgb(255,255,255);">cd /usr/local/<br></span></span></p>
<p><span style="color:#454545;"><span style="background-color:rgb(255,255,255);"><img src="https://img-blog.csdn.net/20180125124151936?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRGFCaW5nU2h1aQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></span></span></p>
<p>为了防止以后权限问题，修改hadoop文件夹所有者为hadoop</p>
<p>sudo chown -R hadoop:hadoop hadoop-3.0.0/</p>
<p>切换hadoop用户</p>
<p>su hadoop</p>
<p><span style="color:#454545;"><span style="background-color:rgb(255,255,255);">进入hadoop目录，</span></span></p>
<p><span style="color:#454545;"><span style="background-color:rgb(255,255,255);">cd /usr/local/hadoop-3.0.0/<br></span></span></p>
<p><span style="color:#454545;"><span style="background-color:rgb(255,255,255);"><span style="color:rgb(69,69,69);background-color:rgb(255,255,255);">编辑文件"hadoop-env.sh"中定义的JAVA_HOME参数，先查看自己的java_home , </span></span></span></p>
<p><span style="color:#454545;"><span style="background-color:rgb(255,255,255);"><span style="color:rgb(69,69,69);background-color:rgb(255,255,255);">echo $JAVA_HOME  然后把输出的路径设置到<span style="color:rgb(69,69,69);text-align:justify;background-color:rgb(255,255,255);">hadoop-env.sh中</span><br></span></span></span></p>
<p><span style="color:#454545;"><span style="background-color:rgb(255,255,255);"><span style="color:rgb(69,69,69);background-color:rgb(255,255,255);"><span style="color:rgb(69,69,69);text-align:justify;background-color:rgb(255,255,255);">vi etc/hadoop/hadoop-env.sh</span><br></span></span></span></p>
<p><span style="color:#454545;"><span style="background-color:rgb(255,255,255);"><span style="color:rgb(69,69,69);background-color:rgb(255,255,255);"><span style="color:rgb(69,69,69);text-align:justify;background-color:rgb(255,255,255);"><img src="https://img-blog.csdn.net/20180125131345145?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRGFCaW5nU2h1aQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></span></span></span></span></p>
<p><span style="color:#454545;"><span style="background-color:rgb(255,255,255);"><span style="color:rgb(69,69,69);background-color:rgb(255,255,255);"><span style="color:rgb(69,69,69);text-align:justify;background-color:rgb(255,255,255);"><span style="color:rgb(69,69,69);background-color:rgb(255,255,255);">在使用命令"./bin/hadoop"后出现如下提示说明配置成功：</span><br></span></span></span></span></p>
<p><span style="color:#454545;"><span style="background-color:rgb(255,255,255);"><span style="color:rgb(69,69,69);background-color:rgb(255,255,255);"><span style="color:rgb(69,69,69);text-align:justify;background-color:rgb(255,255,255);"><span style="color:rgb(69,69,69);background-color:rgb(255,255,255);"><img src="https://img-blog.csdn.net/20180125131511706?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRGFCaW5nU2h1aQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></span></span></span></span></span></p>
<p><span style="color:#454545;"><span style="background-color:rgb(255,255,255);">现在前期配置已经好了，用hadoop自带的demo测试下，</span></span></p>
<p><span style="color:#454545;"><span style="background-color:rgb(255,255,255);"></span></span></p>
<p style="color:rgb(69,69,69);background-color:rgb(255,255,255);">
</p>
mkdir input<br>
cd input<br>
echo "hello world" &gt; test1.txt<br>
echo "hello hadoop" &gt; test2.txt<br>
cd ..<br>
./bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-3.0.0.jar wordcount input output
<p><span style="color:#454545;"><span style="background-color:rgb(255,255,255);">cat output/*</span></span></p>
<p><span style="color:#454545;">查看运行结果</span></p>
<p><span style="color:#454545;"><img src="https://img-blog.csdn.net/20180125133420283?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRGFCaW5nU2h1aQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br><span style="background-color:rgb(255,255,255);"></span></span></p>
<p style="color:rgb(69,69,69);background-color:rgb(255,255,255);">
</p>
<br><p style="text-align:center;"></p>
<p><span style="color:#454545;"><span style="background-color:rgb(255,255,255);">单机模式安装完成，下面会出伪分布式模式和分布式模式。</span></span></p>
<p><span style="color:#454545;"><span style="background-color:rgb(255,255,255);"><br></span></span></p>
<p><span style="color:#454545;"><span style="background-color:rgb(255,255,255);"><br></span></span></p>
<p><span style="color:#454545;"><span style="background-color:rgb(255,255,255);"><br></span></span></p>
<p><br></p>
            </div>
                </div>