---
layout:     post
title:      初识Marp(二)——MapR standbox for Hadoop
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1 id="MapRSandboxforHadoop-Overview" style="font-size:24px;font-weight:normal;line-height:1.25;color:rgb(51,51,51);border-bottom-color:rgb(3,8,13);font-family:Arial, sans-serif;">
<span>概观</span></h1>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;line-height:20px;">
该MAPR sandbox Hadoop的是，轻轻地引入业务分析，目前，有抱负的Hadoop开发人员和管理员（数据库，系统和Hadoop），以Hadoop和其生态系统的大数据的承诺一个全功能的单节点群集。使用沙盒尝试使用MAPR控制系统（MCS）和色调的Hadoop技术。</p>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;line-height:20px;">
MAPR的 <span style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;line-height:20px;">
sandbox </span>Hadoop的包括一些教程，帮助您了解通过MCS和色调工具的力量和Hadoop的能力。Hadoop的管理员可以启动MCS和使用教程来配置，监控和管理集群。  <span style="color:rgb(0,0,0);">Hadoop的开发者或分析师，希望获得Hadoop和MAPR的了解，可以启动色调的界面和使用教程使用包括应用程序来执行任务在顺化。Ë</span> Xplore数据库的解决方案，你的用例，并在MAPR <span style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;line-height:20px;">sandbox </span>Hadoop的数据运行的作业。</p>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;line-height:20px;">
要使用MAPR沙盒Hadoop的，请执行以下任务：</p>
<ul style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;line-height:20px;"><li>验证主机系统满足的先决条件。</li><li>安装MAPR沙箱Hadoop的。</li><li>启动色相或MAPR控制系统。</li></ul><h1 id="MapRSandboxforHadoop-Prerequisites" style="font-size:24px;font-weight:normal;line-height:1.25;color:rgb(51,51,51);border-bottom-color:rgb(3,8,13);font-family:Arial, sans-serif;">
先决条件</h1>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;line-height:20px;">
该MAPR沙箱Hadoop的运行在VMware Player和VirtualBox的，您可以使用在Windows，Mac或Linux的PC上运行虚拟机的免费桌面应用程序。在您安装了MAPR沙箱Hadoop的，请验证主机系统满足以下前提条件：</p>
<ul style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;line-height:20px;"><li>的VMware Player或者VirtualBox的安装。</li><li>至少20 GB可用硬盘空间和6 GB的RAM可用。性能与增加更多的内存和硬盘空间。</li><li>
<p>使用下面的64位x86架构之一：</p>
<ul style="list-style-type:disc;"><li>1.3 GHz或更快的AMD的CPU在长时间模式的段限制支持</li><li>1.3 GHz或更快的Intel CPU搭配VT-X支持</li></ul></li><li>如果你有一个英特尔CPU采用的VT-X的支持，确认VT-X支持在主机系统BIOS中启用。必须启用VT-X支持的BIOS设置，具体取决于系统供应商。请参见<br>
VMware知识库文章在  <a class="external-link" href="http://kb.vmware.com/kb/1003944" rel="nofollow" style="color:rgb(59,115,175);text-decoration:none;">http://kb.vmware.com/kb/1003944</a>  关于如何确定的VT-X支持启用信息。</li></ul><h4 id="MapRSandboxforHadoop-Downloads" style="font-size:14px;line-height:20px;color:rgb(51,51,51);font-family:Arial, sans-serif;">
下载</h4>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;line-height:20px;">
下面的列表提供了虚拟机下载位置的各种操作系统： </p>
<ul style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;line-height:20px;"><li>对于Linux和Windows，请下载免费  <a class="external-link" href="https://my.vmware.com/web/vmware/free#desktop_end_user_computing/vmware_player/6_0" rel="nofollow" style="color:rgb(59,115,175);text-decoration:none;">的VMware Player</a>，  或者，你可以购买  <a class="external-link" href="http://www.vmware.com/products/fusion/" rel="nofollow" style="color:rgb(59,115,175);text-decoration:none;">的VMware
 Fusion </a> 的Mac。</li><li>
<p>对于Linux和Mac，下载免费  <a class="external-link" href="https://www.virtualbox.org/wiki/Downloads" rel="nofollow" style="color:rgb(59,115,175);text-decoration:none;">的VirtualBox</a>。 </p>
</li></ul><div class="aui-message warning shadowed information-macro"><span class="aui-icon icon-warning" style="border:none;display:inline-block;text-indent:-999em;vertical-align:text-bottom;width:16px;">图标</span>
<div class="message-content">
<p>MAPR不支持VirtualBox的Windows上。</p>
</div>
</div>
<h4 id="MapRSandboxforHadoop-Installation" style="font-size:14px;line-height:20px;color:rgb(51,51,51);font-family:Arial, sans-serif;">
安装</h4>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;line-height:20px;">
下面的列表提供了指向虚拟机安装说明：</p>
<ul style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;line-height:20px;"><li>要安装的VMware Player，请参见  <a class="external-link" href="http://www.vmware.com/support/pubs/player_pubs.html" rel="nofollow" style="color:rgb(59,115,175);text-decoration:none;">VMware文档</a>。  使用的VMware Player是受到玩家的VMware最终用户许可条款。VMware不提供的VMware
 Player的支持。对于自助资源，看到  <a class="external-link" href="http://www.vmware.com/products/player/faqs.html" rel="nofollow" style="color:rgb(59,115,175);text-decoration:none;">的VMware Player常见问题</a>。</li><li>要安装VirtualBox的，看到  <a class="external-link" href="http://dlc.sun.com.edgesuite.net/virtualbox/4.3.4/UserManual.pdf" rel="nofollow" style="color:rgb(59,115,175);text-decoration:none;">的Oracle VM VirtualBox的用户手册</a>。  <span style="color:rgb(0,0,0);">通过下载的VirtualBox，您同意各自许可的条款和条件。 </span></li></ul><div class="aui-message warning shadowed information-macro"><span class="aui-icon icon-warning" style="border:none;display:inline-block;text-indent:-999em;vertical-align:text-bottom;width:16px;">图标</span>
<div class="message-content">
<p>确认您的VMware播放器的网络设置被设置为  <strong>桥</strong>。您可以在访问这些设置  <strong>虚拟机&gt;虚拟机设置</strong>。选择  <strong>网络适配器</strong>  的设置对话框，然后<strong>桥接</strong>  的网络连接窗格。</p>
</div>
</div>
<h1 id="MapRSandboxforHadoop-InstalltheMapRSandboxforHadoop" style="font-size:24px;font-weight:normal;line-height:1.25;color:rgb(51,51,51);border-bottom-color:rgb(3,8,13);font-family:Arial, sans-serif;">
Install the Mapr Sandbox for Hadoop</h1>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;line-height:20px;">
下载并解压缩归档文件，然后使用你的虚拟机播放器打开并运行MAPR沙箱Hadoop的。</p>
<h4 id="MapRSandboxforHadoop-ForVMwarePlayerorVMwareFusion,completethefollowingstepstoinstalltheMapRSandboxforHadoop:" style="font-size:14px;line-height:20px;color:rgb(51,51,51);font-family:Arial, sans-serif;">
对于<strong> VMware Player or VMware Fusion</strong>，完成安装MAPR sandbox for Hadoop的步骤如下：</h4>
<ol style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;line-height:20px;"><li>下载MAPR沙箱Hadoop的归档文件：<br><ul style="list-style-type:disc;"><li><a class="external-link" href="http://archive.mapr.com/releases/v3.0.2/vmdemo/MapR-VM-3.0.2.23167GA-1-m7.tbz2" rel="nofollow" style="color:rgb(59,115,175);text-decoration:none;">http://archive.mapr.com/releases/v3.0.2/vmdemo/MapR-VM-3.0.2.23167GA-1-m7.tbz2</a></li></ul></li><li>解压缩。tbz2的存档文件到一个目录。 <br><ul style="list-style-type:disc;"><li><span style="line-height:1.4285715;">在Linux或Mac系统中，使用  </span><code style="line-height:1.4285715;">的tar </code><span style="line-height:1.4285715;"> 命令解压缩归档文件到你的主目录或您选择的其他目录：<br></span><span style="font-family:monospace;line-height:1.4285715;">tar -xvf MapR-VM-&lt;VERSION&gt;.tbz2</span></li></ul><ul style="list-style-type:disc;"><li>在Windows系统中，使用解压缩实用程序，如  <a class="external-link" href="http://www.7-zip.org/" rel="nofollow" style="color:rgb(59,115,175);text-decoration:none;">7-ZIP</a>  解压缩归档文件。</li></ul></li><li>提取得到的。tar归档文件的目录。本。tar归档文件中包含您从虚拟机播放器访问。VMX虚拟机文件。<br><ul style="list-style-type:disc;"><li>在Linux或Mac系统中，使用  <code>的tar</code>  命令解压缩归档文件到你的主目录或您选择的其他目录：<span style="font-family:monospace;">tar -xvf MapR-VM-&lt;VERSION&gt;.tar</span></li><li>在Windows系统中，使用解压缩实用程序，如  <a class="external-link" href="http://www.7-zip.org/" rel="nofollow" style="color:rgb(59,115,175);text-decoration:none;">7-ZIP</a>  解压缩归档文件。</li></ul></li><li>
<p>打开 virtual machine player，并选择  Open a Virtual Machine<strong> </strong>选项。 </p>
<div id="expander-323825092" class="expand-container">
<div id="expander-control-323825092" class="expand-control">
<span class="expand-control-icon icon" style="display:inline-block;min-width:16px;text-indent:-9999em;font-size:0px;vertical-align:text-bottom;width:20px;"> </span><span class="expand-control-text" style="vertical-align:top;color:rgb(59,115,175);">Tip
 for VMware Fusion</span></div>
</div>
</li><li>
<p><span style="line-height:1.4285715;">导航到您解压缩档案的目录，并选择  </span><code style="line-height:1.4285715;">MapR-VM-Base-&lt;VERSION&gt;.VMX </code><span style="line-height:1.4285715;">。</span></p>
</li><li>
<p>点击  <strong>Play virtual machine</strong>。这可能需要几分钟的时间MAPR服务启动。经过MAPR服务启动和安装完成后，出现以下详细：<br><img src="https://img-blog.csdn.net/20140101142056500?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbWFveGlhb19qc2Q=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<div class="aui-message warning shadowed information-macro"><span class="aui-icon icon-warning" style="border:none;display:inline-block;text-indent:-999em;vertical-align:text-bottom;width:16px;">图标</span>
<div class="message-content">
<p>如果虚拟机没有安装成功后，关闭电源并重新启动虚拟机或返回第1步，并开始安装过程了。</p>
</div>
</div>
</li><li>按照窗口中的说明来访问MAPR sandbox for Hadoop的。例如，浏览到提供的URL。</li></ol><h4 id="MapRSandboxforHadoop-ForVirtualBox,completethefollowingstepstoinstalltheMapRSandboxforHadoop:" style="font-size:14px;line-height:20px;color:rgb(51,51,51);font-family:Arial, sans-serif;">
<strong>对于VirtualBox的，完成安装MAPR沙箱Hadoop的步骤如下：</strong></h4>
<ol style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;line-height:20px;"><li>下载MAPR沙箱Hadoop的归档文件：
<ul style="list-style-type:disc;"><li><a class="external-link" href="http://archive.mapr.com/releases/v3.0.2/vmdemo/MapR-OVF-3.0.2.23167GA-1-m7.tbz2" rel="nofollow" style="color:rgb(59,115,175);text-decoration:none;">http://archive.mapr.com/releases/v3.0.2/vmdemo/MapR-OVF-3.0.2.23167GA-1-m7.tbz2</a></li></ul></li><li>使用  <code>焦油</code>  命令提取存档到你的主目录或您选择的其他目录：<br><ul style="list-style-type:disc;"><li><code>tar -xvf MapR-OVF-&lt;VERSION&gt;.tbz2  </code></li></ul></li><li>打开VirtualBox的应用。</li><li>选择  <strong>File&gt;Import Appliance</strong>。</li><li>导航到OVF文件的位置，然后单击  <strong>下一步</strong>。出现在设备设置窗口。</li><li>单击  <strong>Import .The Import Appliance imports the image</strong></li><li>导入完成后，导航回 ​​到VirtualBox和选择  <strong>VirtualBox&gt;Preferences</strong>。出现在VirtualBox的设置窗口。</li><li>选择  <strong>NetWork</strong>  并单击上  <strong>Host-only Networks</strong>  选项卡。如果没有出现适配器，单击绿色的  <strong>+</strong>  按钮来添加一个适配器。将出现vboxnet0适配器。</li><li>单击  <strong>OK（确定）</strong>  继续。</li><li>选择  <strong>VM</strong>，然后单击  <strong>Settings</strong>。出现在虚拟机设置窗口。</li><li>选择  <strong>Network</strong>。验证适配器1被激活，并且它被安装在主机仅适配器。主机仅适配器名称必须vmboxnet0。</li><li>单击  <strong>确定</strong>  以继续。</li><li>
<p>单击  <strong>Start</strong>。这可能需要几分钟的时间MAPR服务启动。经过MAPR服务启动和安装完成后，出现以下详细：<br><img src="https://img-blog.csdn.net/20140101143059156?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbWFveGlhb19qc2Q=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<div class="aui-message warning shadowed information-macro"><span class="aui-icon icon-warning" style="border:none;display:inline-block;text-indent:-999em;vertical-align:text-bottom;width:16px;">图标</span>
<div class="message-content">
<p>如果虚拟机没有安装成功后，关闭电源并重新启动虚拟机或返回第1步，并开始安装过程了。</p>
</div>
</div>
</li><li>按照窗口中的说明来访问MAPR沙箱Hadoop的。例如，浏览到提供的URL。</li></ol><h1 id="MapRSandboxforHadoop-LaunchHueortheMCS" style="font-size:24px;font-weight:normal;line-height:1.25;color:rgb(51,51,51);border-bottom-color:rgb(3,8,13);font-family:Arial, sans-serif;">
Launch Hue  or the MCS </h1>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;line-height:20px;">
当你浏览到提供的URL，您将看到以下MAPR sandbox for Hadoop的启动页：</p>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;line-height:20px;">
<img src="https://img-blog.csdn.net/20140101143408625?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbWFveGlhb19qc2Q=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<ol style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;line-height:20px;"><li>单击启动按钮来启动Hue or the MCS。  <br>
出现与右边的左边和应用程序窗口的教程窗格中新的一页。  <br>
您可以使用教程，在应用程序窗口执行一些基本任务。<br><img src="https://img-blog.csdn.net/20140101143610468?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbWFveGlhb19qc2Q=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></li><li>
<p>登录Hue or the MCS，用户名：root   密码：mapr。  <br>
如果你启动MCS，系统填入你的用户名和密码字段。单击  <strong>OK（确定）</strong>  继续。</p>
<div class="aui-message hint shadowed information-macro">
<p class="title" style="font-weight:bold;">Hue username:root      Hue password:mapr   MCS username:root    MCS password:mapr</p>
</div>
<p>您已经完成访问MAPR沙箱Hadoop的必要步骤。Have fun!</p>
</li></ol>            </div>
                </div>