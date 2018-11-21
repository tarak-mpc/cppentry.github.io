---
layout:     post
title:      Hadoop HDFS分布式环境搭建
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div class="articalTitle" style="clear:both;line-height:20px;color:rgb(73,73,73);font-family:Verdana, '宋体', sans-serif;background-color:rgb(246,246,246);">
<h2 id="t_7060fb5a0101cson" class="titName SG_txta" style="border:0px;list-style:none;color:rgb(82,102,115);font-size:18px;font-family:'微软雅黑', '黑体';font-weight:300;display:inline;">
Hadoop HDFS分布式环境搭建</h2>
 <span class="time SG_txtc" style="color:rgb(135,135,135);font-family:Arial;font-size:10px;margin-left:5px;">(2013-12-05 14:12:24)</span>
<div class="turnBoxzz"><a class="SG_aBtn SG_aBtn_ico SG_turn" style="overflow:hidden;display:inline-block;letter-spacing:5px;width:86px;"><cite style="font-style:normal;line-height:23px;min-width:1px;width:34px;display:inline-block !important;"><img class="SG_icon SG_icon111" src="http://simg.sinajs.cn/blog7style/images/common/sg_trans.gif" width="15" height="15" alt="" style="border:0px;list-style:none;">转载<span class="arrow" style="font-size:7px;">▼</span></cite></a></div>
</div>
<div class="articalTag" id="sina_keyword_ad_area" style="width:690px;clear:both;line-height:20px;color:rgb(73,73,73);font-family:Verdana, '宋体', sans-serif;background-color:rgb(246,246,246);">
<table><tbody><tr><td class="blog_tag" style="font-family:'宋体';vertical-align:top;">
<span class="SG_txtb" style="color:rgb(135,135,135);">标签：</span> 
<h3 style="border:0px;list-style:none;display:inline;font-size:12px;font-weight:normal;">
<a href="http://search.sina.com.cn/?c=blog&amp;q=hadoop&amp;by=tag" rel="nofollow" style="text-decoration:none;color:rgb(82,102,115);">hadoop</a></h3>
 
<h3 style="border:0px;list-style:none;display:inline;font-size:12px;font-weight:normal;">
<a href="http://search.sina.com.cn/?c=blog&amp;q=java&amp;by=tag" rel="nofollow" style="text-decoration:none;color:rgb(82,102,115);">java</a></h3>
 
<h3 style="border:0px;list-style:none;display:inline;font-size:12px;font-weight:normal;">
<a href="http://search.sina.com.cn/?c=blog&amp;q=opensource&amp;by=tag" rel="nofollow" style="text-decoration:none;color:rgb(82,102,115);">opensource</a></h3>
</td>
<td class="blog_class" style="font-family:'宋体';vertical-align:top;width:220px;">
<span class="SG_txtb" style="color:rgb(135,135,135);">分类：</span> <a href="http://blog.sina.com.cn/s/articlelist_1885403994_14_1.html" rel="nofollow" style="text-decoration:none;color:rgb(82,102,115);">java</a></td>
</tr></tbody></table></div>
<div id="sina_keyword_ad_area2" class="articalContent" style="width:690px;clear:both;font-size:14px;overflow:hidden;font-family:simsun;line-height:21px;color:rgb(73,73,73);background-color:rgb(246,246,246);">
<h1 style="border:0px;list-style:none;"><span style="line-height:42px;">Hadoop HDFS</span><span style="line-height:42px;font-family:'宋体';">分布式环境搭建</span></h1>
<p style="border:0px;list-style:none;">
<span style="font-family:'宋体';">最近选择给大家介绍</span><span>Hadoop HDFS</span><span style="font-family:'宋体';">系统，因此研究了一下如何在</span><span>Linux</span> <span style="font-family:'宋体';">下配置一个</span><span>HDFS
 Clust</span><span style="font-family:'宋体';">。小记一下，以备将来进一步研究和记忆。</span></p>
<h2 style="border:0px;list-style:none;"><span style="line-height:31.5px;">HDFS</span><span style="line-height:31.5px;font-family:'宋体';">简介</span></h2>
<p style="border:0px;list-style:none;">
<span style="font-family:'宋体';">全称</span> <span>Hadoop Distributed File System, Hadoop</span><span style="font-family:'宋体';">分布式文件系统。</span> <span style="font-family:'宋体';">根据</span><span>Google</span><span style="font-family:'宋体';">的</span><span>GFS</span><span style="font-family:'宋体';">论文，由</span><span>Doug
 Cutting</span><span style="font-family:'宋体';">使用</span><span>JAVA</span><span style="font-family:'宋体';">开发的开源项目。</span><span>HDFS</span><span style="font-family:'宋体';">是</span><span>Hadoop</span><span style="font-family:'宋体';">项目的一部分。为</span><span>Hadoop</span><span style="font-family:'宋体';">提供底层的数据存储，满足上次各种实际应用使用（如</span><span>Map/Reduce</span><span style="font-family:'宋体';">）。</span><span>HDFS</span><span style="font-family:'宋体';">是典型的的</span><span>Master/Slave</span><span style="font-family:'宋体';">集群架构，由一个</span><span>NameNode</span><span style="font-family:'宋体';">和多个</span><span>DateNode</span><span style="font-family:'宋体';">组成，</span><span>NameName</span><span style="font-family:'宋体';">只能有一个，扮演</span><span>Master</span><span style="font-family:'宋体';">角色，负责对具体的存储块的元数据进行保存，比如控制某个存储块具体保存在哪个</span><span>DataNode</span><span style="font-family:'宋体';">上；</span><span>DataNode</span><span style="font-family:'宋体';">可以为多个，扮演着</span><span>Slave</span><span style="font-family:'宋体';">的角色，负责对具体的存储块进行保存，一个相同的存储块根据配置可以保存到多个</span><span>DataNode</span><span style="font-family:'宋体';">上，以保持数据的高可用性。</span></p>
<p style="border:0px;list-style:none;">
<span> </span></p>
<h3 style="border:0px;list-style:none;"><span style="line-height:24.5699977874756px;font-family:'宋体';">环境介绍</span></h3>
<p style="border:0px;list-style:none;">
<span style="font-family:'宋体';">三台主机，一台</span><span>NameNode, 2</span><span style="font-family:'宋体';">台</span><span>DataNode</span></p>
<table border="1" cellspacing="0" cellpadding="0" style="border-collapse:collapse;border:none;"><tbody><tr><td width="118" valign="top" style="font-family:Verdana, '宋体', sans-serif;line-height:18px;width:88.8pt;border:1pt solid;">
<p style="border:0px;list-style:none;">
<span>Role</span></p>
</td>
<td width="122" valign="top" style="font-family:Verdana, '宋体', sans-serif;line-height:18px;width:91.25pt;border-style:solid solid solid none;border-top-width:1pt;border-right-width:1pt;border-bottom-width:1pt;">
<p style="border:0px;list-style:none;">
<span>IP</span></p>
</td>
<td width="138" valign="top" style="font-family:Verdana, '宋体', sans-serif;line-height:18px;width:103.2pt;border-style:solid solid solid none;border-top-width:1pt;border-right-width:1pt;border-bottom-width:1pt;">
<p style="border:0px;list-style:none;">
<span>Host</span></p>
</td>
<td width="79" valign="top" style="font-family:Verdana, '宋体', sans-serif;line-height:18px;width:58.95pt;border-style:solid solid solid none;border-top-width:1pt;border-right-width:1pt;border-bottom-width:1pt;">
<p style="border:0px;list-style:none;">
<span>Type</span></p>
</td>
<td width="97" valign="top" style="font-family:Verdana, '宋体', sans-serif;line-height:18px;width:72.6pt;border-style:solid solid solid none;border-top-width:1pt;border-right-width:1pt;border-bottom-width:1pt;">
<p style="border:0px;list-style:none;">
<span>Version</span></p>
</td>
</tr><tr><td width="118" valign="top" style="font-family:Verdana, '宋体', sans-serif;line-height:18px;width:88.8pt;border-style:none solid solid;border-right-width:1pt;border-bottom-width:1pt;border-left-width:1pt;">
<p style="border:0px;list-style:none;">
<span>NameNode</span></p>
</td>
<td width="122" valign="top" style="font-family:Verdana, '宋体', sans-serif;line-height:18px;width:91.25pt;border-style:none solid solid none;border-bottom-width:1pt;border-right-width:1pt;">
<p style="border:0px;list-style:none;">
<span>10.34.64.222</span></p>
</td>
<td width="138" valign="top" style="font-family:Verdana, '宋体', sans-serif;line-height:18px;width:103.2pt;border-style:none solid solid none;border-bottom-width:1pt;border-right-width:1pt;">
<p style="border:0px;list-style:none;">
<span>NC-VM-PL-DEV-26</span></p>
</td>
<td width="79" valign="top" style="font-family:Verdana, '宋体', sans-serif;line-height:18px;width:58.95pt;border-style:none solid solid none;border-bottom-width:1pt;border-right-width:1pt;">
<p style="border:0px;list-style:none;">
<span>Master</span></p>
</td>
<td width="97" valign="top" style="font-family:Verdana, '宋体', sans-serif;line-height:18px;width:72.6pt;border-style:none solid solid none;border-bottom-width:1pt;border-right-width:1pt;">
<p style="border:0px;list-style:none;">
<span>CentOS 6.4</span></p>
</td>
</tr><tr><td width="118" valign="top" style="font-family:Verdana, '宋体', sans-serif;line-height:18px;width:88.8pt;border-style:none solid solid;border-right-width:1pt;border-bottom-width:1pt;border-left-width:1pt;">
<p style="border:0px;list-style:none;">
<span>DataNode</span></p>
</td>
<td width="122" valign="top" style="font-family:Verdana, '宋体', sans-serif;line-height:18px;width:91.25pt;border-style:none solid solid none;border-bottom-width:1pt;border-right-width:1pt;">
<p style="border:0px;list-style:none;">
<span>10.34.64.223</span></p>
</td>
<td width="138" valign="top" style="font-family:Verdana, '宋体', sans-serif;line-height:18px;width:103.2pt;border-style:none solid solid none;border-bottom-width:1pt;border-right-width:1pt;">
<p style="border:0px;list-style:none;">
<span>NC-VM-PL-DEV-27</span></p>
</td>
<td width="79" valign="top" style="font-family:Verdana, '宋体', sans-serif;line-height:18px;width:58.95pt;border-style:none solid solid none;border-bottom-width:1pt;border-right-width:1pt;">
<p style="border:0px;list-style:none;">
<span>Slave</span></p>
</td>
<td width="97" valign="top" style="font-family:Verdana, '宋体', sans-serif;line-height:18px;width:72.6pt;border-style:none solid solid none;border-bottom-width:1pt;border-right-width:1pt;">
<p style="border:0px;list-style:none;">
<span>CentOS 6.4</span></p>
</td>
</tr><tr><td width="118" valign="top" style="font-family:Verdana, '宋体', sans-serif;line-height:18px;width:88.8pt;border-style:none solid solid;border-right-width:1pt;border-bottom-width:1pt;border-left-width:1pt;">
<p style="border:0px;list-style:none;">
<span>DataNode</span></p>
</td>
<td width="122" valign="top" style="font-family:Verdana, '宋体', sans-serif;line-height:18px;width:91.25pt;border-style:none solid solid none;border-bottom-width:1pt;border-right-width:1pt;">
<p style="border:0px;list-style:none;">
<span>10.34.64.224</span></p>
</td>
<td width="138" valign="top" style="font-family:Verdana, '宋体', sans-serif;line-height:18px;width:103.2pt;border-style:none solid solid none;border-bottom-width:1pt;border-right-width:1pt;">
<p style="border:0px;list-style:none;">
<span>NC-VM-PL-DEV-28</span></p>
</td>
<td width="79" valign="top" style="font-family:Verdana, '宋体', sans-serif;line-height:18px;width:58.95pt;border-style:none solid solid none;border-bottom-width:1pt;border-right-width:1pt;">
<p style="border:0px;list-style:none;">
<span>Slave</span></p>
</td>
<td width="97" valign="top" style="font-family:Verdana, '宋体', sans-serif;line-height:18px;width:72.6pt;border-style:none solid solid none;border-bottom-width:1pt;border-right-width:1pt;">
<p style="border:0px;list-style:none;">
<span>CentOS 6.4</span></p>
</td>
</tr></tbody></table><p style="border:0px;list-style:none;">
<span> </span></p>
<h3 style="border:0px;list-style:none;"><span style="line-height:24.5699977874756px;font-family:'宋体';">配置流程</span></h3>
<p style="border:0px;list-style:none;">
<span style="font-family:'宋体';">原则：三台机器的配置保持一致就可以，下面具体描述</span><span>NameNode</span><span style="font-family:'宋体';">主机的过程</span></p>
<p style="margin-left:18pt;border:0px;list-style:none;text-indent:-18pt;">
<strong><span>1.<span style="line-height:normal;font-weight:normal;font-size:7pt;font-family:'Times new roman';">      </span></span> <span style="font-family:'宋体';">安装</span><span>JDK1.6</span> <span style="font-family:'宋体';">（略）</span></strong></p>
<p style="margin-left:18pt;border:0px;list-style:none;text-indent:-18pt;">
<strong><span>2.<span style="line-height:normal;font-weight:normal;font-size:7pt;font-family:'Times new roman';">      </span></span> <span style="font-family:'宋体';">创建</span><span>HDFS</span><span style="font-family:'宋体';">账号，最好这样，便于管理</span></strong></p>
<p style="margin-left:18pt;border:0px;list-style:none;text-indent:0cm;">
<span>[root@NC-VM-PL-DEV-26 ~]# useradd hdfs</span></p>
<p style="margin-left:18pt;border:0px;list-style:none;text-indent:0cm;">
<span>[root@NC-VM-PL-DEV-26 ~]# passwd hdfs</span></p>
<p style="margin-left:18pt;border:0px;list-style:none;text-indent:-18pt;">
<strong><span>3.<span style="line-height:normal;font-weight:normal;font-size:7pt;font-family:'Times new roman';">      </span></span> <span style="font-family:'宋体';">配置免密码的</span><span>SSH</span><span style="font-family:'宋体';">访问机制</span></strong></p>
<p style="margin-left:18pt;border:0px;list-style:none;text-indent:0cm;">
<span style="font-family:'宋体';">通常</span><span>Centos</span><span style="font-family:'宋体';">系统都默认安装</span><span>SSH,</span> <span style="font-family:'宋体';">如果没有安装，请单独安装，</span><span>yum
 install ssh</span></p>
<p style="margin-left:42pt;border:0px;list-style:none;text-indent:-21pt;">
<span>a)<span style="line-height:normal;font-size:7pt;font-family:'Times new roman';">        </span></span> <span style="font-family:'宋体';">切换至</span><span>hdfs</span><span style="font-family:'宋体';">登录</span><span>.</span></p>
<p style="margin-left:42pt;border:0px;list-style:none;text-indent:-21pt;">
<span>b)<span style="line-height:normal;font-size:7pt;font-family:'Times new roman';">        </span></span> <span>[hdfs@NC-VM-PL-DEV-26
 ~]$ cd ~</span></p>
<p style="margin-left:42pt;border:0px;list-style:none;text-indent:-21pt;">
<span>c)<span style="line-height:normal;font-size:7pt;font-family:'Times new roman';">        </span></span> <span>[hdfs@NC-VM-PL-DEV-26
 ~]$ ls -a</span></p>
<p style="margin-left:42pt;border:0px;list-style:none;text-indent:-21pt;">
<span>d)<span style="line-height:normal;font-size:7pt;font-family:'Times new roman';">        </span></span> <span>[hdfs@NC-VM-PL-DEV-26
 ~]$ cd .ssh</span></p>
<p style="margin-left:42pt;border:0px;list-style:none;text-indent:-21pt;">
<span>e)<span style="line-height:normal;font-size:7pt;font-family:'Times new roman';">        </span></span> <span>[hdfs@NC-VM-PL-DEV-26
 ~]$ ssh-keygen –t rsa</span></p>
<p style="margin-left:42pt;border:0px;list-style:none;text-indent:-21pt;">
<span>f)<span style="line-height:normal;font-size:7pt;font-family:'Times new roman';">         </span></span> <span>[hdfs@NC-VM-PL-DEV-26
 ~]$ cat id_rsa.pub &gt; authorized_keys</span></p>
<p style="margin-left:42pt;border:0px;list-style:none;text-indent:-21pt;">
<span>g)<span style="line-height:normal;font-size:7pt;font-family:'Times new roman';">        </span></span> <span style="font-family:'宋体';">至此可以测试一下</span><span>ssh</span> <span style="font-family:'宋体';">本机</span><span>IP</span><span style="font-family:'宋体';">是否需要密码，如果成功，说明本机配置成功。</span></p>
<p style="margin-left:42pt;border:0px;list-style:none;text-indent:-21pt;">
<span>h)<span style="line-height:normal;font-size:7pt;font-family:'Times new roman';">        </span></span> <span style="font-family:'宋体';">然后将</span><span>authorized_keys</span><span style="font-family:'宋体';">文件复制到所有</span><span>DataNode</span><span style="font-family:'宋体';">节点，</span><span>[hdfs@NC-VM-PL-DEV-26
 ~]$ scp authorized_keys <a href="mailto:hdfs@10.34.64.223:/home/hdfs/.ssh" rel="nofollow" style="text-decoration:none;color:rgb(82,102,115);">hdfs@10.34.64.223:/home/hdfs/.ssh</a></span></p>
<p style="margin-left:42pt;border:0px;list-style:none;text-indent:-21pt;">
<span>i)<span style="line-height:normal;font-size:7pt;font-family:'Times new roman';">          </span></span> <span style="font-family:'宋体';">给另一个</span><span>DataNode</span><span style="font-family:'宋体';">节点相同的操作，至此免密码登录配置完毕，可以通过本机</span><span>ssh</span> <span style="font-family:'宋体';">各个节点</span><span>IP</span><span style="font-family:'宋体';">来测试是否需要密码登录</span></p>
<p style="margin-left:18pt;border:0px;list-style:none;text-indent:-18pt;">
<strong><span>4.<span style="line-height:normal;font-weight:normal;font-size:7pt;font-family:'Times new roman';">      </span></span> <span style="font-family:'宋体';">下载</span><span>Hadoop</span><span style="font-family:'宋体';">软件包，官方下载地址</span></strong></p>
<p style="margin-left:42pt;border:0px;list-style:none;text-indent:-21pt;">
<span>a)<span style="line-height:normal;font-size:7pt;font-family:'Times new roman';">        </span></span> <span><a href="http://mirrors.cnnic.cn/apache/hadoop/common/stable/" rel="nofollow" style="text-decoration:none;color:rgb(82,102,115);">http://mirrors.cnnic.cn/apache/hadoop/common/stable/</a></span></p>
<p style="margin-left:42pt;border:0px;list-style:none;text-indent:-21pt;">
<span>b)<span style="line-height:normal;font-size:7pt;font-family:'Times new roman';">        </span></span> <span><a href="http://hadoop.apache.org/releases.html#Download" rel="nofollow" style="text-decoration:none;color:rgb(82,102,115);">http://hadoop.apache.org/releases.html#Download</a></span></p>
<p style="margin-left:21pt;border:0px;list-style:none;">
<span style="font-family:'宋体';">切换至</span><span>hdfs</span><span style="font-family:'宋体';">账号，解压至</span><span>/home/hdfs/hadoop</span></p>
<p style="margin-left:21pt;border:0px;list-style:none;">
<span>[hdfs@NC-VM-PL-DEV-26 ~]$ tar zxf hadoop-2.2.0.tar.gz</span></p>
<p style="margin-left:21pt;border:0px;list-style:none;">
<span>[hdfs@NC-VM-PL-DEV-26 ~]$ mv hadoop-2.2.0  hadoop</span></p>
<p style="margin-left:18pt;border:0px;list-style:none;text-indent:-18pt;">
<strong><span>5.<span style="line-height:normal;font-weight:normal;font-size:7pt;font-family:'Times new roman';">      </span></span> <span style="font-family:'宋体';">配置环境变量</span></strong></p>
<p style="margin-left:42pt;border:0px;list-style:none;text-indent:-21pt;">
<span>a)<span style="line-height:normal;font-size:7pt;font-family:'Times new roman';">        </span></span> <span>[hdfs@NC-VM-PL-DEV-26
 ~]$ vim ~/.bash_profile</span></p>
<p style="margin-left:42pt;border:0px;list-style:none;text-indent:-21pt;">
<span>b)<span style="line-height:normal;font-size:7pt;font-family:'Times new roman';">        </span></span> <span>HADOOP_HOME=/home/hdfs/hadoop</span></p>
<p style="margin-left:42pt;border:0px;list-style:none;text-indent:0cm;">
<span>export HADOOP_HOME</span></p>
<p style="margin-left:42pt;border:0px;list-style:none;text-indent:0cm;">
<span>export  JRE_HOME=$JAVA_HOME/jre</span></p>
<p style="margin-left:42pt;border:0px;list-style:none;text-indent:0cm;">
<span>export  CLASSPATH=.:$JAVA_HOME/lib:$JAVA_HOME/jre/lib</span></p>
<p style="margin-left:42pt;border:0px;list-style:none;text-indent:0cm;">
<span>export  PATH=$JAVA_HOME/bin:$JAVA_HOME/jre/bin:$PATH:$HADOOP_HOME/bin</span></p>
<p style="margin-left:18pt;border:0px;list-style:none;text-indent:-18pt;">
<strong><span>6.<span style="line-height:normal;font-weight:normal;font-size:7pt;font-family:'Times new roman';">      </span></span> <span style="font-family:'宋体';">在</span><span>NameNode</span><span style="font-family:'宋体';">上修改</span><span>hadoop</span><span style="font-family:'宋体';">配置文件</span></strong></p>
<p style="margin-left:18pt;border:0px;list-style:none;text-indent:0cm;">
<span style="font-family:'宋体';">主要修改</span><span>/home/hdfs/hadoop/etc/hadoop/</span><span style="font-family:'宋体';">目录下的配置文件</span></p>
<p style="margin-left:18pt;border:0px;list-style:none;text-indent:0cm;">
<span>Hadoop-env.sh:         Hadoop</span><span style="font-family:'宋体';">环境变量设置</span></p>
<p style="margin-left:18pt;border:0px;list-style:none;text-indent:0cm;">
<span>Core-site.xml:            NameNode  IP</span><span style="font-family:'宋体';">和端口设置</span></p>
<p style="margin-left:18pt;border:0px;list-style:none;text-indent:0cm;">
<span>Hdfs-site.xml:            HDFS</span><span style="font-family:'宋体';">数据块副本等参数设置</span></p>
<p style="margin-left:18pt;border:0px;list-style:none;text-indent:0cm;">
<span>Mapred-site.xml:      MapReduce</span> <span style="font-family:'宋体';">完成</span><span>JobTracker  IP</span><span style="font-family:'宋体';">和端口设置</span></p>
<p style="margin-left:18pt;border:0px;list-style:none;text-indent:0cm;">
<span>Slaves:                         </span> <span style="font-family:'宋体';">完成</span><span>datanode</span><span style="font-family:'宋体';">节点</span><span>IP</span><span style="font-family:'宋体';">设置</span></p>
<p style="margin-left:18pt;border:0px;list-style:none;text-indent:0cm;">
<span> </span></p>
<p style="margin-left:18pt;border:0px;list-style:none;text-indent:0cm;">
<span>6.1</span> <span style="font-family:'宋体';">配置</span><span>core-site.xml</span></p>
<p style="margin-left:18pt;border:0px;list-style:none;">
 </p>
<p style="margin-left:18pt;border:0px;list-style:none;">
<span> </span></p>
<p style="margin-left:18pt;border:0px;list-style:none;">
<span>    fs.default.name</span></p>
<p style="margin-left:18pt;border:0px;list-style:none;">
<span>    hdfs://10.34.64.222:9000/</span></p>
<p style="margin-left:18pt;border:0px;list-style:none;">
<span> </span></p>
<p style="margin-left:36pt;border:0px;list-style:none;text-indent:3pt;">
 </p>
<p style="margin-left:18pt;border:0px;list-style:none;text-indent:0cm;">
<span>6.2</span> <span style="font-family:'宋体';">配置</span> <span>hdfs-site.xml</span></p>
<p style="margin-left:18pt;border:0px;list-style:none;">
 </p>
<p style="margin-left:18pt;border:0px;list-style:none;">
<span> </span></p>
<p style="margin-left:18pt;border:0px;list-style:none;">
<span>     dfs.name.dir</span></p>
<p style="margin-left:18pt;border:0px;list-style:none;">
<span>     /home/hdfs/hadoop-2.2.0/data/dfs.name.dir</span></p>
<p style="margin-left:18pt;border:0px;list-style:none;">
<span> </span></p>
<p style="margin-left:18pt;border:0px;list-style:none;">
<span> </span></p>
<p style="margin-left:18pt;border:0px;list-style:none;">
<span>     dfs.data.dir</span></p>
<p style="margin-left:18pt;border:0px;list-style:none;">
<span>     /home/hdfs/hadoop-2.2.0/data/dfs.data.dir</span></p>
<p style="margin-left:18pt;border:0px;list-style:none;">
<span> </span></p>
<p style="margin-left:18pt;border:0px;list-style:none;">
<span> </span></p>
<p style="margin-left:18pt;border:0px;list-style:none;">
<span>     dfs.permissions</span></p>
<p style="margin-left:18pt;border:0px;list-style:none;">
<span>     false</span></p>
<p style="margin-left:18pt;border:0px;list-style:none;">
<span> </span></p>
<p style="margin-left:18pt;border:0px;list-style:none;">
<span> </span></p>
<p style="margin-left:18pt;border:0px;list-style:none;">
<span>    dfs.replication</span></p>
<p style="margin-left:18pt;border:0px;list-style:none;">
<span>    2</span></p>
<p style="margin-left:18pt;border:0px;list-style:none;">
<span> </span></p>
<p style="margin-left:36pt;border:0px;list-style:none;text-indent:3pt;">
 </p>
<p style="margin-left:18pt;border:0px;list-style:none;text-indent:0cm;">
<span>6.3</span> <span style="font-family:'宋体';">配置</span> <span>mapred-site.xml</span></p>
<p style="margin-left:18pt;border:0px;list-style:none;">
 </p>
<p style="margin-left:18pt;border:0px;list-style:none;">
<span> </span></p>
<p style="margin-left:18pt;border:0px;list-style:none;">
<span>     mapred.job.tracker</span></p>
<p style="margin-left:18pt;border:0px;list-style:none;">
<span>     Master:9001</span></p>
<p style="margin-left:18pt;border:0px;list-style:none;">
<span> </span></p>
<p style="margin-left:36pt;border:0px;list-style:none;text-indent:3pt;">
 </p>
<p style="margin-left:18pt;border:0px;list-style:none;text-indent:0cm;">
<span>6.4</span> <span style="font-family:'宋体';">配置</span> <span>hadoop-env.sh</span></p>
<p style="margin-left:18pt;border:0px;list-style:none;text-indent:0cm;">
<span>          </span> <span style="font-family:'宋体';">将</span><span>JAVA_HOME</span><span style="font-family:'宋体';">的值修改为真实有效的地址，如果不知道，请执行</span><span>echo
 $JAVA_HOME</span> <span style="font-family:'宋体';">查看。</span><span>export JAVA_HOME=/usr/java/jdk1.6.0_27</span></p>
<p style="margin-left:18pt;border:0px;list-style:none;text-indent:0cm;">
<span>6.5</span> <span style="font-family:'宋体';">配置</span> <span>slaves</span></p>
<p style="margin-left:18pt;border:0px;list-style:none;">
<span>NC-VM-PL-DEV-27</span></p>
<p style="margin-left:36pt;border:0px;list-style:none;text-indent:5.25pt;">
<span>NC-VM-PL-DEV-28</span></p>
<p style="margin-left:18pt;border:0px;list-style:none;text-indent:-18pt;">
<strong><span>7.<span style="line-height:normal;font-weight:normal;font-size:7pt;font-family:'Times new roman';">      </span></span> <span style="font-family:'宋体';">复制</span><span>hadoop</span><span style="font-family:'宋体';">系统到所有</span><span>DataNode</span><span style="font-family:'宋体';">节点</span></strong></p>
<p style="margin-left:18pt;border:0px;list-style:none;text-indent:0cm;">
<span style="font-family:'宋体';">将主</span><span>NameNode</span><span style="font-family:'宋体';">节点安装好的</span><span>Hadoop</span><span style="font-family:'宋体';">系统目录复制到每一个从</span><span>DataNode</span><span style="font-family:'宋体';">节点上</span><span>.</span></p>
<p style="margin-left:18pt;border:0px;list-style:none;text-indent:0cm;">
<span>[hdfs@NC-VM-PL-DEV-26 hadoop]$ scp –r /home/hdfs/hadoop 10.34.64.223: /home/hdfs/hadoop</span></p>
<p style="margin-left:18pt;border:0px;list-style:none;text-indent:0cm;">
<span>[hdfs@NC-VM-PL-DEV-26 hadoop]$ scp –r /home/hdfs/hadoop 10.34.64.224: /home/hdfs/hadoop</span></p>
<p style="margin-left:18pt;border:0px;list-style:none;text-indent:-18pt;">
<strong><span>8.<span style="line-height:normal;font-weight:normal;font-size:7pt;font-family:'Times new roman';">      </span></span> <span style="font-family:'宋体';">在每一个主机上修改</span><span>Hosts</span><span style="font-family:'宋体';">文件，以便于主机名和</span><span>IP</span><span style="font-family:'宋体';">地址解析</span></strong></p>
<p style="margin-left:18pt;border:0px;list-style:none;text-indent:0cm;">
<span style="font-family:'宋体';">修改每台机器上的</span><span>/etc/hosts</span><span style="font-family:'宋体';">文件</span></p>
<p style="margin-left:42pt;border:0px;list-style:none;text-indent:-21pt;">
<span>a)<span style="line-height:normal;font-size:7pt;font-family:'Times new roman';">        </span></span> <span style="font-family:'宋体';">如果是</span><span>NameNode</span><span style="font-family:'宋体';">，需要在</span><span>hosts</span><span style="font-family:'宋体';">文件中添加集群中所有节点的</span><span>IP</span><span style="font-family:'宋体';">地址及对应的主机名。示例：</span></p>
<p style="margin-left:21pt;border:0px;list-style:none;text-indent:21pt;">
<span>10.34.64.224 NC-VM-PL-DEV-28</span></p>
<p style="margin-left:21pt;border:0px;list-style:none;text-indent:21pt;">
<span>10.34.64.223 NC-VM-PL-DEV-27</span></p>
<p style="margin-left:42pt;border:0px;list-style:none;text-indent:0cm;">
<span>10.34.64.222 NC-VM-PL-DEV-26</span></p>
<p style="border:0px;list-style:none;text-indent:21pt;">
<span>b)</span> <span style="font-family:'宋体';">如果是</span><span>DataNode</span><span style="font-family:'宋体';">，只需要在文件中添加本机和</span><span>NameNode</span><span style="font-family:'宋体';">的</span><span>Ip</span><span style="font-family:'宋体';">地址和主机名。</span></p>
<p style="border:0px;list-style:none;text-indent:21pt;">
<span style="font-family:'宋体';">为了方便都填上也挺好。</span></p>
<p style="margin-left:18pt;border:0px;list-style:none;text-indent:-18pt;">
<strong><span>9.<span style="line-height:normal;font-weight:normal;font-size:7pt;font-family:'Times new roman';">      </span></span> <span style="font-family:'宋体';">格式化</span><span>NameNode</span></strong></p>
<p style="margin-left:18pt;border:0px;list-style:none;text-indent:0cm;">
<span style="font-family:'宋体';">执行如下命令做格式化操作，每次如果有修改</span><span>conf</span><span style="font-family:'宋体';">，最好做一次格式化命令</span></p>
<p style="margin-left:18pt;border:0px;list-style:none;text-indent:0cm;">
<span>[hdfs@NC-VM-PL-DEV-26 ~]$ hadoop namenode –format</span></p>
<p style="border:0px;list-style:none;">
<span>        </span> <span style="font-family:'宋体';background-color:#FFFFFF;">如果格式化成功，会返回一堆有关</span><span style="font-family:'Times new roman', serif;background-color:#FFFFFF;">NameNode</span><span style="font-family:'宋体';background-color:#FFFFFF;">的启动信息，其中会有一句</span><span style="font-family:Arial, sans-serif;background-color:#FFFFFF;">“</span><span style="font-family:'Times new roman', serif;background-color:#FFFFFF;">….
 has been successfully formatted.”</span></p>
<p style="margin-left:18pt;border:0px;list-style:none;text-indent:-18pt;">
<strong><span>10.<span style="line-height:normal;font-weight:normal;font-size:7pt;font-family:'Times new roman';">  </span></span> <span style="font-family:'宋体';">启动</span><span>HDFS</span></strong></p>
<p style="margin-left:18pt;border:0px;list-style:none;text-indent:0cm;">
<span>[hdfs@NC-VM-PL-DEV-26 ~]$.</span><span style="line-height:20px;font-size:10pt;background-color:#FFFFFF;">/home/hdfs/hadoop/bin/start-dfs.sh</span></p>
<p style="margin-left:18pt;border:0px;list-style:none;text-indent:0cm;">
<span style="line-height:20px;font-size:10pt;font-family:'宋体';background-color:#FFFFFF;">如果启动遇到问题，请对</span><span style="line-height:20px;font-size:10pt;background-color:#FFFFFF;">NameNode</span><span style="line-height:20px;font-size:10pt;font-family:'宋体';background-color:#FFFFFF;">或</span><span style="line-height:20px;font-size:10pt;background-color:#FFFFFF;">DataNode</span><span style="line-height:20px;font-size:10pt;font-family:'宋体';background-color:#FFFFFF;">的日志进行查看，路径为</span><span style="line-height:20px;font-size:10pt;background-color:#FFFFFF;">/home/hdfs/hadoop/logs</span><span style="line-height:20px;font-size:10pt;font-family:'宋体';background-color:#FFFFFF;">，看看是否有</span><span style="line-height:20px;font-size:10pt;background-color:#FFFFFF;">Exception</span><span style="line-height:20px;font-size:10pt;font-family:'宋体';background-color:#FFFFFF;">抛出。</span></p>
<p style="margin-left:18pt;border:0px;list-style:none;text-indent:-18pt;">
<strong><span>11.<span style="line-height:normal;font-weight:normal;font-size:7pt;font-family:'Times new roman';">  </span></span> <span style="font-family:'宋体';">简单对</span><span>HDFS</span><span style="font-family:'宋体';">进行试用</span></strong></p>
<p style="margin-left:18pt;border:0px;list-style:none;text-indent:0cm;">
<span>[hdfs@NC-VM-PL-DEV-26 ~]$ hadoop fs –mkdir /newdir</span></p>
<p style="margin-left:18pt;border:0px;list-style:none;text-indent:0cm;">
<span>[hdfs@NC-VM-PL-DEV-26 ~]$ hadoop fs –ls</span></p>
<p style="margin-left:18pt;border:0px;list-style:none;text-indent:0cm;">
<span style="font-family:'宋体';">用</span><span>jps</span><span style="font-family:'宋体';">命令检查一下是否正常启动：</span></p>
<p style="margin-left:18pt;border:0px;list-style:none;">
<span>[hdfs@NC-VM-PL-DEV-26 ~]$ jps</span></p>
<p style="margin-left:18pt;border:0px;list-style:none;">
<span>9193 SecondaryNameNode</span></p>
<p style="margin-left:18pt;border:0px;list-style:none;">
<span>8914 NameNode</span></p>
<p style="margin-left:36pt;border:0px;list-style:none;text-indent:3pt;">
<span>9615 Jps</span></p>
<p style="border:0px;list-style:none;">
<span>        </span> <span style="font-family:'宋体';">查看集群状态</span></p>
<p style="border:0px;list-style:none;">
<span>         [hdfs@NC-VM-PL-DEV-26 ~]$ hadoop dfsadmin -report</span></p>
<p style="margin-left:21pt;border:0px;list-style:none;">
<span>DEPRECATED: Use of this script to execute hdfs command is deprecated.</span></p>
<p style="margin-left:21pt;border:0px;list-style:none;">
<span>Instead use the hdfs command for it.</span></p>
<p style="margin-left:21pt;border:0px;list-style:none;">
<span> </span></p>
<p style="margin-left:21pt;border:0px;list-style:none;">
<span>13/12/04 15:46:20 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable</span></p>
<p style="margin-left:21pt;border:0px;list-style:none;">
<span>Configured Capacity: 0 (0 B)</span></p>
<p style="margin-left:21pt;border:0px;list-style:none;">
<span>Present Capacity: 0 (0 B)</span></p>
<p style="margin-left:21pt;border:0px;list-style:none;">
<span>DFS Remaining: 0 (0 B)</span></p>
<p style="margin-left:21pt;border:0px;list-style:none;">
<span>DFS Used: 0 (0 B)</span></p>
<p style="margin-left:21pt;border:0px;list-style:none;">
<span>DFS Used%: NaN%</span></p>
<p style="margin-left:21pt;border:0px;list-style:none;">
<span>Under replicated blocks: 0</span></p>
<p style="margin-left:21pt;border:0px;list-style:none;">
<span>Blocks with corrupt replicas: 0</span></p>
<p style="margin-left:21pt;border:0px;list-style:none;">
<span>Missing blocks: 0</span></p>
<p style="margin-left:21pt;border:0px;list-style:none;">
<span> </span></p>
<p style="margin-left:21pt;border:0px;list-style:none;">
<span>-------------------------------------------------</span></p>
<p style="margin-left:21pt;border:0px;list-style:none;">
<span>Datanodes available: 0 (0 total, 0 dead)</span></p>
<p style="border:0px;list-style:none;line-height:19.5pt;background:#FFFFFF;">
<strong><span style="line-height:21px;font-size:10.5pt;font-family:'宋体';">浏览器方式：在</span><span style="line-height:21px;font-size:10.5pt;">WEB</span><span style="line-height:21px;font-size:10.5pt;font-family:'宋体';">页面下查看</span><span style="line-height:21px;font-size:10.5pt;">Hadoop</span><span style="line-height:21px;font-size:10.5pt;font-family:'宋体';">工作情况</span></strong></p>
<p style="border:0px;list-style:none;line-height:19.5pt;background:#FFFFFF;">
<span style="line-height:21px;font-size:10.5pt;">输入部署</span><span style="line-height:21px;font-size:10.5pt;font-family:'Times new roman', serif;">Hadoop</span><span style="line-height:21px;font-size:10.5pt;">服务器的</span><strong><span style="line-height:21px;font-size:10.5pt;">IP</span><span style="line-height:21px;font-size:10.5pt;font-family:'宋体';">：</span><span style="line-height:21px;font-size:10.5pt;">http://IP:50070</span><span style="line-height:21px;font-size:10.5pt;font-family:'宋体';">；</span><span style="line-height:21px;font-size:10.5pt;font-family:Arial, sans-serif;"><a href="http://ip:50030/" rel="nofollow" style="text-decoration:none;color:rgb(82,102,115);">http://IP:50030</a></span><span style="line-height:21px;font-size:10.5pt;">.</span></strong></p>
</div>
            </div>
                </div>