---
layout:     post
title:      Hadoop学习笔记-Hadoop在Windows下安装
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div class="article_title" style="font-size:20px;line-height:30px;font-family:'Microsoft YaHei';">
<h3 style="display:inline;font-weight:normal;font-size:20px;vertical-align:middle;">
<span class="link_title"><a href="http://blog.csdn.net/jdream314/article/details/8242617" rel="nofollow" style="color:rgb(0,0,0);">Hadoop学习笔记-Hadoop在Windows下安装</a></span></h3>
</div>
<div class="article_manage" style="color:rgb(153,153,153);font-size:11.818181991577148px;line-height:24px;font-family:Arial;text-align:right;">
<span class="link_categories">分类： <a href="http://blog.csdn.net/JDream314/article/category/1289537" rel="nofollow" style="color:rgb(202,0,0);">Hadoop学习笔记</a></span><span class="link_postdate">2012-11-30
 11:19</span> <span class="link_view" title="阅读次数">124人阅读</span> <span class="link_comments" title="评论次数"><a href="http://blog.csdn.net/jdream314/article/details/8242617#comments" rel="nofollow" style="color:rgb(202,0,0);">评论</a>(0)</span> <span class="link_collect"><a href="" rel="nofollow" title="收藏" style="color:rgb(202,0,0);">收藏</a></span> <span class="link_report"><a href="http://blog.csdn.net/jdream314/article/details/8242617#report" rel="nofollow" title="举报" style="color:rgb(202,0,0);">举报</a></span></div>
<div class="tag2box" style="font-family:Arial, Console, Verdana, 'Courier New';font-size:11.818181991577148px;">
<a href="http://blog.csdn.net/tag/details.html?tag=%E5%AD%98%E5%82%A8" rel="nofollow" style="color:rgb(202,0,0);display:inline-block;line-height:11.818181991577148px;border:1px solid rgb(238,238,238);background-color:rgb(238,238,238);">存储</a><a href="http://blog.csdn.net/tag/details.html?tag=%E6%95%B0%E6%8D%AE%E5%BA%93" rel="nofollow" style="color:rgb(202,0,0);display:inline-block;line-height:11.818181991577148px;border:1px solid rgb(238,238,238);background-color:rgb(238,238,238);">数据库</a><a href="http://blog.csdn.net/tag/details.html?tag=java" rel="nofollow" style="color:rgb(202,0,0);display:inline-block;line-height:11.818181991577148px;border:1px solid rgb(238,238,238);background-color:rgb(238,238,238);">java</a></div>
<div style="font-family:Arial, Console, Verdana, 'Courier New';font-size:11.818181991577148px;clear:both;">
</div>
<div style="font-family:Arial, Console, Verdana, 'Courier New';font-size:11.818181991577148px;border:1px solid rgb(204,204,204);background-color:rgb(238,238,238);min-width:200px;">
<p style="text-align:right;">
<span>目录<a href="http://blog.csdn.net/jdream314/article/details/8242617#" rel="nofollow" title="系统根据文章中H1到H6标签自动生成文章目录" style="color:rgb(202,0,0);">(?)</a></span><a href="http://blog.csdn.net/jdream314/article/details/8242617#" rel="nofollow" title="展开" style="color:rgb(202,0,0);">[+]</a></p>
</div>
<div style="font-family:Arial, Console, Verdana, 'Courier New';font-size:11.818181991577148px;clear:both;">
</div>
<div id="article_content" class="article_content" style="font-size:14px;line-height:26px;font-family:Arial;">
<h1 align="center"><a name="t0" style="color:rgb(202,0,0);"></a>Hadoop学习笔记-Hadoop在Windows下安装</h1>
<p>
<br></p>
<p>
</p>
<p>
前言</p>
<p>
         Hadoop的生产环境是Linux或者是Unix，要在Windows下搭建Hadoop需要借助Cygwin来搭建模拟Linux环境。那么首要任务就是在Windows下安装Cygwin。本次安装只是搭建一个Hadoop的学习平台，并没有任何的实践的意义，由于本人也只是菜鸟，所以只提供和我一样新接触Hadoop的童鞋参考。</p>
<p>
</p>
<h2><a name="t1" style="color:rgb(202,0,0);"></a>安装Cygwin</h2>
        Cygwin的下载地址百度一下一大把，我这里给一个官方最新版本的下载地址：<a href="http://cygwin.com/setup.exe" rel="nofollow" style="color:rgb(202,0,0);">http://cygwin.com/setup.exe</a>。下载下来之后双击安装就可以了。
<p>
</p>
<p align="center">
<img src="http://img.my.csdn.net/uploads/201211/30/1354245243_9934.jpg" alt="" style="border:none;"></p>
<p align="left">
</p>
<p align="left">
         此处有三个选项，第一个是从网上下载安装文件，第二个是下载安装文件，但不进行安装，第三个是直接从本地目录获取安装文件信息（这种情况是你以前安装过Cygwin，然后再你本地有临时目录，如果你以前没有安装过，那么就不用选择这个选项）。</p>
<p align="left">
         完成上一步骤后点击下一步，会让你选择一个下载的镜像地址，此处可以随便选一个地址，之后会弹出一个警告框，点击确认即可。点击之后，会出现一个选择安装组件的窗口，此处需要选中Base下面的sed，eidtors下面的vim以及vim-common，还有net下面的openssl，还有Devel下面的subversion。选择完成后点击下一步，等待下载完成后安装完毕即可。记住，在安装完毕之后记得在环境变量的PATH后面加上%CYGWIN_HOME%/bin以及%CYGWIN_HOME%/usr/sbin。</p>
<p align="left">
</p>
<h2><a name="t2" style="color:rgb(202,0,0);"></a>Sshd安装</h2>
<p>
</p>
<p>
        注意：此处需要以管理员身份运行cygwin，因为下面可能的一些操作会受到权限的限制。</p>
<p>
         本人安装sshd尝尽了苦头，折腾了半天，百度了无数次，就在无意中找到了原因。按照网上的一般安装步骤之前请先安装cygserver。直接执行cygserver-config，会询问是否将cygserver作为一个服务，输入yes即可。然后执行net start cygserver，已启动cygserver服务。执行完之后下面就真正地安装sshd了。</p>
<p>
         执行ssh-host-config后会提示：<code>Should privilege separation be used?</code><code>记住此处输入yes，虽然网上很多说输入no，但是本人尝试过很多次，输入no安装不成功。接下来就会出现</code></p>
<p>
<span style="color:rgb(51,102,255);"><code>*** Query:Do you want to install sshd as a service?</code><br><code>*** Query: (Say"no" if it is already installed as a service) (yes/no)  </code></span></p>
<p>
<code>输入yes，表示你同意安祖行sshd作为一个服务。</code></p>
<p>
<code>接下来可能会提示</code></p>
<p>
<span style="color:rgb(51,102,255);"><code>*** Query:Enter the value of CYGWIN for the daemon: [ntsec]</code></span></p>
<p>
<code>此处输入ntsec即可。如果没有出现那就不用管它。接下来会提示：</code></p>
<p>
<span style="color:rgb(51,102,255);"><code>*** Info:'cyg_server' will only be used by registered services.</code><br><code>*** Query: Do you wantto use a different name? (yes/no)</code></span></p>
<code>因为sshd需要一个windows下的一个用户来运行，所以要创建一个windows下的用户，默认情况下这个用户名是cyg_server，所以这里就提示你是不是要用和这个不一样的用户名，输入yes表示你要用别的用户名，那么就要你输入一个用户名，输入no则采用这个用户名。接下来就是输入这个用户名的密码，输入你想要的密码即可。到此sshd的安装已基本完成，执行exit退出。然后打开cygwin执行cygrunsrv-S sshd，启动sshd服务，启动成功之后执行ssh-keygen一路回车即可。执行完之后在执行下图操作：</code>
<p align="center">
<img src="http://img.my.csdn.net/uploads/201211/30/1354245396_8138.jpg" alt="" style="border:none;"></p>
<p align="left">
</p>
<p align="left">
在执行exit。重新运行cygwin，在执行ssh localhost那么就可以成功登陆了。</p>
<div align="center"><img src="http://img.my.csdn.net/uploads/201211/30/1354245952_3998.jpg" alt="" style="border:none;"><br></div>
<p align="left">
到此就完成了sshd的安装，下面就剩下安装hadoop了。</p>
<p align="left">
</p>
<h2><a name="t3" style="color:rgb(202,0,0);"></a>Hadoop的安装</h2>
<p>
</p>
<p>
         到此，安装Hadoop是比较简单的，只是修改Hadoop的几个配置文档和环境变量。</p>
<p>
         本人安装的是Hadoop-0.22.0，下载地址是：<a href="http://labs.renren.com/apache-mirror/hadoop/core/hadoop-0.22.0/" rel="nofollow" style="color:rgb(202,0,0);">http://labs.renren.com/apache-mirror/hadoop/core/hadoop-0.22.0/</a>。我的安装过程是更具官方的相关文档来操作的，地址为：<a href="http://hadoop.apache.org/docs/r0.22.0/single_node_setup.html" rel="nofollow" style="color:rgb(202,0,0);">http://hadoop.apache.org/docs/r0.22.0/single_node_setup.html</a>。
 这个版本的Hadoop基本按照官方的文档来进行安装设置就可以了，我也就不赘述，最多也就是复制粘贴一下，所以觉得没必要，大家可以参考官方的文档即可。当然，在安装Hadoop之前，最好设置好JAVA_HOME环境变量，指向你的JDK的目录。当然，还有Hadoop的其他版本，经过本人尝试安装release版本的0.23.4，更具官方的文档安装一直不成功，还待本人以后继续了解之后再尝试一下。本文的目的是搭建一个简单的Hadoop，以方便以后的学习使用，而不是搭建一个生产环境为目的。后续会进入真正地Hadoop的具体内容学习。</p>
</div>
            </div>
                </div>