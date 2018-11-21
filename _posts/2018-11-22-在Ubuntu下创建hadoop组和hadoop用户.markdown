---
layout:     post
title:      在Ubuntu下创建hadoop组和hadoop用户
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h2 style="line-height:1.8;font-family:verdana, Arial, Helvetica, sans-serif;font-size:21px;">
<span style="font-family:'宋体';line-height:1.8;">一、在</span>Ubuntu<span style="font-family:'宋体';line-height:1.8;">下创建</span>hadoop<span style="font-family:'宋体';line-height:1.8;">组和</span>hadoop<span style="font-family:'宋体';line-height:1.8;">用户</span></h2>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
    增加hadoop用户组，同时在该组里增加hadoop用户<span style="font-family:'宋体';line-height:1.8;">，</span>后续在涉及到hadoop操作时，我们使用<span style="font-family:'宋体';line-height:1.8;">该</span>用户<span style="font-family:'宋体';line-height:1.8;">。</span></p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<h3 style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:16px;">
<span style="font-family:'宋体';line-height:1.8;">1、创建</span>hadoop<span style="font-family:'宋体';line-height:1.8;">用户组</span></h3>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<img align="left" alt="" src="http://images.cnitblog.com/blog/12097/201406/181325537236804.png" style="border:0px;">        </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<h3 style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:16px;">
    2<span style="font-family:'宋体';line-height:1.8;">、创建</span>hadoop<span style="font-family:'宋体';line-height:1.8;">用户</span></h3>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
    </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
    sudo adduser -ingroup hadoop hadoop</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
    回车后会提示输入新的UNIX密码，这是新建用户hadoop的密码<span style="font-family:'宋体';line-height:1.8;">，</span>输入回车即可。</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
    如果不输入密码，回车后会重新提示输入密码，即密码不能为空。</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
    最后确认信息是否正确，如果没问题，输入 Y，回车即可。</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<img align="left" alt="" src="http://images.cnitblog.com/blog/12097/201406/181325547071504.png" style="border:0px;">    </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<h3 style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:16px;">
    3<span style="font-family:'宋体';line-height:1.8;">、为</span>hadoop<span style="font-family:'宋体';line-height:1.8;">用户添加权限</span></h3>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
     输入：sudo gedit /etc/sudoers</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
     回车，打开sudoers文件</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
     给hadoop用户赋予和root用户同样的权限</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<img align="left" alt="" src="http://images.cnitblog.com/blog/12097/201406/181325553952348.png" style="border:0px;"></p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
    <img align="left" alt="" src="http://images.cnitblog.com/blog/12097/201406/181325561927991.png" style="border:0px;"></p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<h2 style="line-height:1.8;font-family:verdana, Arial, Helvetica, sans-serif;font-size:21px;">
二、用新增加的hadoop用户登录Ubuntu系统</h2>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<h2 style="line-height:1.8;font-family:verdana, Arial, Helvetica, sans-serif;font-size:21px;">
三、安装ssh</h2>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
sudo apt-get install openssh-server</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<img align="left" alt="" src="http://images.cnitblog.com/blog/12097/201406/181325569737863.png" style="border:0px;"></p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
安装完成后，启动服务</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
sudo /etc/init.d/ssh start</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
查看服务是否正确启动：ps -e | grep ssh</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<img align="left" alt="" src="http://images.cnitblog.com/blog/12097/201406/181325580364821.png" style="border:0px;"></p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
设置免密码登录，生成私钥和公钥</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
ssh-keygen -t rsa -P ""</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<img align="left" alt="" src="http://images.cnitblog.com/blog/12097/201406/181325587709679.png" style="border:0px;"></p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
此时会在／home／hadoop/.ssh下生成两个文件：id_rsa和id_rsa.pub，前者为私钥，后者为公钥。</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
下面我们将公钥追加到authorized_keys中，它用户保存所有允许以当前用户身份登录到ssh客户端用户的公钥内容。</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
cat ~/.ssh/id_rsa.pub &gt;&gt; ~/.ssh/authorized_keys</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<img align="left" alt="" src="http://images.cnitblog.com/blog/12097/201406/181325593481023.png" style="border:0px;"></p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
登录ssh</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
ssh localhost</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<img align="left" alt="" src="http://images.cnitblog.com/blog/12097/201406/181325598014095.png" style="border:0px;"></p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
      </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
退出</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
exit</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<h2 style="line-height:1.8;font-family:verdana, Arial, Helvetica, sans-serif;font-size:21px;">
四、安装Java环境</h2>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
sudo apt-get install openjdk-7-jdk</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<img align="left" alt="" src="http://images.cnitblog.com/blog/12097/201406/181326008641052.png" style="border:0px;"></p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
查看安装结果，输入命令：java -version，结果如下表示安装成功。</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<img align="left" alt="" src="http://images.cnitblog.com/blog/12097/201406/181326019736724.png" style="border:0px;"></p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<h2 style="line-height:1.8;font-family:verdana, Arial, Helvetica, sans-serif;font-size:21px;">
<span style="font-family:'宋体';line-height:1.8;">五、安装</span>hadoop2.4.0</h2>
<h3 style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:16px;">
    1<span style="font-family:'宋体';line-height:1.8;">、官网下载</span><span style="line-height:1.8;text-decoration:underline;">http://mirror.bit.edu.cn/apache/hadoop/common/</span></h3>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<h3 style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:16px;">
    2<span style="font-family:'宋体';line-height:1.8;">、安装</span></h3>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
        解压</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
        sudo tar xzf hadoop-2.4.0.tar.gz        </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
        假如我们要把hadoop安装到/usr/local下</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
        拷贝到/usr/local/下，文件夹为hadoop</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
        sudo mv hadoop-2.4.0 /usr/local/hadoop        </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<img align="left" alt="" src="http://images.cnitblog.com/blog/12097/201406/181326023644310.png" style="border:0px;"></p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
        </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
赋予用户对该文件夹的读写权限</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
        sudo chmod 774 /usr/local/hadoop</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<img align="left" alt="" src="http://images.cnitblog.com/blog/12097/201406/181326027238353.png" style="border:0px;"></p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
    </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<h3 style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:16px;">
3、配置</h3>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
      </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
        1）<span style="font-family:'宋体';line-height:1.8;">配置</span>~/.bashrc</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
        </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span style="font-family:'宋体';line-height:1.8;">配置</span>该文件前需要知道Java的安装路径，用来设置JAVA_HOME环境变量，可以使用下面命令行查看安装路径</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
        update-alternatives - -config java</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
        执行结果如下：</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<img align="left" alt="" src="http://images.cnitblog.com/blog/12097/201406/181326030041140.png" style="border:0px;">        </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
    </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
完整的路径为</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
    /usr/lib/jvm/java-7-openjdk-amd64/jre/bin/java</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
    我们只取前面的部分 /usr/lib/jvm/java-7-openjdk-amd64</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
    配置.bashrc文件</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
    sudo gedit ~/.bashrc</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
    </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
    该命令会打开该文件的编辑窗口，在文件末尾追加下面内容，然后保存，关闭编辑窗口。</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
#HADOOP VARIABLES START</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
export HADOOP_INSTALL=/usr/local/hadoop</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
export PATH=$PATH:$HADOOP_INSTALL/bin</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
export PATH=$PATH:$HADOOP_INSTALL/sbin</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
export HADOOP_MAPRED_HOME=$HADOOP_INSTALL</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
export HADOOP_COMMON_HOME=$HADOOP_INSTALL</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
export HADOOP_HDFS_HOME=$HADOOP_INSTALL</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
export YARN_HOME=$HADOOP_INSTALL</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_INSTALL/lib/native</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
export HADOOP_OPTS="-Djava.library.path=$HADOOP_INSTALL/lib"</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
#HADOOP VARIABLES END</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
最终结果如下图：</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<img align="left" alt="" src="http://images.cnitblog.com/blog/12097/201406/181326035825782.png" style="border:0px;"></p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
执行下面命，使添加的环境变量生效：</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
        source ~/.bashrc</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
2）编辑/usr/local/hadoop/etc/hadoop/hadoop-env.sh</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
        执行下面命令<span style="font-family:'宋体';line-height:1.8;">，</span>打开该文件的编辑窗口</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
        sudo gedit /usr/local/hadoop/etc/hadoop/hadoop-env.sh</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
找到JAVA_HOME变量，修改此变量如下</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
        export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64    </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
        修改后的hadoop-env.sh文件如下所示<span style="font-family:'宋体';line-height:1.8;">：</span></p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
    <img align="left" alt="" src="http://images.cnitblog.com/blog/12097/201406/181326041767598.png" style="border:0px;"></p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<h2 style="line-height:1.8;font-family:verdana, Arial, Helvetica, sans-serif;font-size:21px;">
<span style="font-family:'宋体';line-height:1.8;">六、</span>WordCount测试</h2>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
单机模式安装完成，下面通过执行hadoop自带实例WordCount验证是否安装成功</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
    /usr/local/hadoop路径下创建input文件夹    </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
mkdir input</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
    拷贝README.txt到input    </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
cp README.txt input</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
    执行WordCount</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
    bin/hadoop jar share/hadoop/mapreduce/sources/hadoop-mapreduce-examples-2.4.0-sources.jar org.apache.hadoop.examples.WordCount input output</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<img align="left" alt="" src="http://images.cnitblog.com/blog/12097/201406/181326049269226.png" style="border:0px;"></p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
    </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
执行结果：</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<img align="left" alt="" src="http://images.cnitblog.com/blog/12097/201406/181326055049570.png" style="border:0px;"></p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
执行 cat output/*，查看字符统计结果</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<img align="left" alt="" src="http://images.cnitblog.com/blog/12097/201406/181326062072484.png" style="border:0px;"></p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 常见问题：</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
问题： 50070 hadoop 打不开 50075可以打开</p>
<p style="color:rgb(51,51,51);line-height:25px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
解决: hadoop namenode -format</p>
            </div>
                </div>