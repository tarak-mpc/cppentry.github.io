---
layout:     post
title:      VMware  ubuntu12.10上伪分布式安装hadoop0.20.2
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p align="left"><span style="font-size:14px;">写在前面的： </span></p>
<p align="left"><span style="font-size:14px;">hadoop分为三种安装模式</span></p>
<p align="left"><span style="font-size:14px;">1）单机模式安装</span></p>
<p align="left"><span style="font-size:14px;">2）伪分布式安装</span></p>
<p align="left"><span style="font-size:14px;">3）完全分布式安装</span></p>
<p align="left"><span style="font-size:14px;">使用的linux系统的用户需要放到用户组里</span></p>
<p align="left">$ sudo addgroup hadoop<br>
$ sudo adduser --ingroup hadoop hadoop</p>
<p align="left">更改hadoop安装文件所属的用户以及组</p>
<p align="left"><span style="color:#FF0000;">$ sudo chown -R hadoop:hadoop hadoop</span></p>
<p align="left">所有的操作都是已hadoop来完成的（以防文件权限的问题）</p>
<p align="left"><span style="color:#FF0000;">注意虚拟机最好有一个静态的ip</span>这样方便与windows下的开发环境连接hadoop 以及 hbase</p>
<p align="left"><span style="font-size:14px;">使用者可以根据目的选择不同的安装模式</span></p>
<p align="left"><span style="font-size:14px;">抱着学习的目的我选择了伪分布式安装</span></p>
<p align="left"><span style="font-size:14px;">下面是一些安装的步骤：</span></p>
<p align="left"><span style="font-size:14px;">1.</span><span style="font-size:14px;">安装</span><span style="font-size:14px;">JDK<br>
　　1）下载安装JDK：确保计算机联网之后命令行输入下面命令安装</span><span style="font-size:14px;">JDK<br>
　　      sudo apt-get install sun-java6-jdk</span></p>
<p align="left"><span style="font-size:14px;">或者使用下载好的安装包进行安装</span></p>
<p align="left"><span style="font-size:14px;">eg：</span></p>
<p align="left"><span style="font-size:14px;">下载的安装包</span></p>
<p align="left"><span style="font-size:14px;"><img src="https://img-blog.csdn.net/20140507151004421?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3hsb3ZleXVu/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p align="left"><span style="font-size:14px;">解压缩</span></p>
<p align="left"><span style="font-size:14px;"><img src="https://img-blog.csdn.net/20140507151156968?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3hsb3ZleXVu/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p align="left"><span style="font-size:14px;">　　2）配置计算机Java环境：打开/etc/profile，在文件最后输入下面内容</span></p>
<p align="left"><span style="font-size:14px;">　　 export JAVA_HOME = （Jdk安装目录）</span><br><span style="font-size:14px;">        export CLASSPATH =".:$JAVA_HOME/lib:$CLASSPATH"
<br>
　　 export PATH = "$JAVA_HOME/:PATH"</span></p>
<p align="left">           <img src="https://img-blog.csdn.net/20140507151306968?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3hsb3ZleXVu/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><span style="font-size:14px;">　　3）验证Java是否安装成功</span><br><span style="font-size:14px;">　　 输入 java -version ，输出Java版本信息即为安装成功。</span></p>
<p align="left"><span style="font-size:14px;">2.安装配置SSH</span></p>
<p align="left"><span style="font-size:14px;">　　1）下载安装SSH：同样在命令行输入下面命令安装</span><span style="font-size:14px;">SSH<br>
　　     </span><span style="font-size:14px;"> sudo apt-get install ssh<br>
　　2）配置无密码登录本机：在命令行输入下面两条命令</span><br><span style="font-size:14px;">             $ ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa<br>
             直接回车，完成后会在~/.ssh/生成两个文件：id_rsa和id_rsa.pub;这两个成对出现，类似钥匙和锁。</span><br><span style="font-size:14px;">             再把id_rsa.pub追加到授权key里面（当前并没有authorized_keys文件）</span><br><span style="font-size:14px;">             $ cat ~/.ssh/id_rsa.pub &gt;&gt; ~/.ssh/authorized_keys<br>
3</span><span style="font-size:14px;">）验证SSH是否安装成功</span></p>
<p align="left"><span style="font-size:14px;">      输入 ssh localhost 。如果显示本机登录成功就表明安装成功。</span></p>
<p align="left"><span style="font-size:14px;">3.hadoop0.20.2安装</span></p>
<p align="left"><span style="font-size:14px;">1）下载安装包</span></p>
<p align="left"><img src="https://img-blog.csdn.net/20140507151458265?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3hsb3ZleXVu/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p align="left"><span style="font-size:14px;">2）解压安装文件</span></p>
<p align="left"><img src="https://img-blog.csdn.net/20140507151458265?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3hsb3ZleXVu/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p align="left"><span style="font-size:14px;">3）配置计算机hadoop环境：打开/etc/profile，在文件最后输入下面内容</span></p>
<p align="left"><img src="https://img-blog.csdn.net/20140507151615843?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3hsb3ZleXVu/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p align="left"><span style="font-size:14px;">4）配置core-site.xml</span></p>
<p align="left"><img src="https://img-blog.csdn.net/20140507151645890?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3hsb3ZleXVu/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p align="left"><span style="font-size:14px;">5）配置hdfs-site.xml</span></p>
<p align="left"><img src="https://img-blog.csdn.net/20140507151716953?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3hsb3ZleXVu/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p align="left"><span style="font-size:14px;">6）配置hadoop-env.sh</span></p>
<p align="left"><img src="https://img-blog.csdn.net/20140507151745437?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3hsb3ZleXVu/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p align="left"><span style="font-size:14px;">7）配置mapred-site.xml</span></p>
<p align="left"><img src="https://img-blog.csdn.net/20140507151816500?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3hsb3ZleXVu/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p align="left"><span style="font-size:14px;">8）masters，slaves都填写成机器名称</span></p>
<p align="left"><span style="font-size:14px;">9）格式化文件系统</span></p>
<p align="left"><span style="font-size:14px;">       bin/hadoop namenode -format</span></p>
<p align="left"><span style="font-size:14px;">10）启动文件系统</span></p>
<p align="left"><span style="font-size:14px;">       bin/start-all.sh</span></p>
<p align="left"><span style="font-size:14px;">11）文件系统启动成功后访问 http://ubuntu:50070/【ubuntu机器名称可以用ip代替】</span></p>
<p align="left"><img src="https://img-blog.csdn.net/20140507151935421?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3hsb3ZleXVu/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p align="left"><span style="font-size:14px;">12）访问<a href="http://ubuntu:50030/" rel="nofollow"><span style="color:#0000FF;">http://ubuntu:50030</span></a></span></p>
<p align="left"><img src="https://img-blog.csdn.net/20140507152020250?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3hsb3ZleXVu/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p align="left"><span style="font-size:14px;"> </span></p>
<p align="left"><span style="font-size:14px;"> </span></p>
<p align="left"><span style="font-size:14px;">写在结束:</span></p>
<p align="left"><span style="font-size:14px;">上述安装是hadoop的最基本的安装，在生产环境下还需要各方面的配置，需要了解hadoop更多的知识。</span></p>
<p><span style="font-family:Calibri;font-size:14px;"> </span></p>
            </div>
                </div>