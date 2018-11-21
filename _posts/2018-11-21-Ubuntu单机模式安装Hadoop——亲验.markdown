---
layout:     post
title:      Ubuntu单机模式安装Hadoop——亲验
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h2 style="line-height:1.8;font-size:21px;font-family:verdana, Arial, Helvetica, sans-serif;">
<span style="line-height:1.8;font-family:'宋体';">一、在</span>Ubuntu<span style="line-height:1.8;font-family:'宋体';">下创建</span>hadoop<span style="line-height:1.8;font-family:'宋体';">组和</span>hadoop<span style="line-height:1.8;font-family:'宋体';">用户</span></h2>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
    增加hadoop用户组，同时在该组里增加hadoop用户<span style="line-height:1.8;font-family:'宋体';">，</span>后续在涉及到hadoop操作时，我们使用<span style="line-height:1.8;font-family:'宋体';">该</span>用户<span style="line-height:1.8;font-family:'宋体';">。</span></p>
<h3 style="font-size:16px;font-family:verdana, Arial, Helvetica, sans-serif;">
<span style="line-height:1.8;font-family:'宋体';">1、创建</span>hadoop<span style="line-height:1.8;font-family:'宋体';">用户组</span><span style="color:rgb(51,51,51);font-size:14px;line-height:25.2000007629395px;"> </span></h3>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
<img src="http://images.cnitblog.com/blog/12097/201406/181325537236804.png" alt="" align="left" style="border:0px;">        </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<h3 style="font-size:16px;font-family:verdana, Arial, Helvetica, sans-serif;">
    2<span style="line-height:1.8;font-family:'宋体';">、创建</span>hadoop<span style="line-height:1.8;font-family:'宋体';">用户</span></h3>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
    sudo adduser -ingroup hadoop hadoop</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
    回车后会提示输入新的UNIX密码，这是新建用户hadoop的密码<span style="line-height:1.8;font-family:'宋体';">，</span>输入回车即可。</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
    如果不输入密码，回车后会重新提示输入密码，即密码不能为空。</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
    最后确认信息是否正确，如果没问题，输入 Y，回车即可。</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
<img src="http://images.cnitblog.com/blog/12097/201406/181325547071504.png" alt="" align="left" style="border:0px;">    </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<h3 style="font-size:16px;font-family:verdana, Arial, Helvetica, sans-serif;">
    3<span style="line-height:1.8;font-family:'宋体';">、为</span>hadoop<span style="line-height:1.8;font-family:'宋体';">用户添加权限</span></h3>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
     输入：sudo gedit /etc/sudoers</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
     回车，打开sudoers文件</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
     给hadoop用户赋予和root用户同样的权限</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
<img src="http://images.cnitblog.com/blog/12097/201406/181325553952348.png" alt="" align="left" style="border:0px;"></p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
    <img src="http://images.cnitblog.com/blog/12097/201406/181325561927991.png" alt="" align="left" style="border:0px;"></p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<h2 style="line-height:1.8;font-size:21px;font-family:verdana, Arial, Helvetica, sans-serif;">
二、用新增加的hadoop用户登录Ubuntu系统</h2>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
<span style="color:rgb(51,51,51);"> （</span><span style="color:#ff0000;">自我感觉前两步没什么重要性，只不过是添加的新的用户，保留原创</span><span style="color:#333333;">）</span></p>
<h2 style="line-height:1.8;font-size:21px;font-family:verdana, Arial, Helvetica, sans-serif;">
三、安装ssh</h2>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
sudo apt-get install openssh-server</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
<img src="http://images.cnitblog.com/blog/12097/201406/181325569737863.png" alt="" align="left" style="border:0px;"></p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
安装完成后，启动服务</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
sudo /etc/init.d/ssh start</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
查看服务是否正确启动：ps -e | grep ssh</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
<img src="http://images.cnitblog.com/blog/12097/201406/181325580364821.png" alt="" align="left" style="border:0px;"></p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
设置免密码登录，生成私钥和公钥</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
ssh-keygen -t rsa -P ""</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
<img src="http://images.cnitblog.com/blog/12097/201406/181325587709679.png" alt="" align="left" style="border:0px;"></p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
此时会在／home／hadoop/.ssh下生成两个文件：id_rsa和id_rsa.pub，前者为私钥，后者为公钥。</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
下面我们将公钥追加到authorized_keys中，它用户保存所有允许以当前用户身份登录到ssh客户端用户的公钥内容。</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
cat ~/.ssh/id_rsa.pub &gt;&gt; ~/.ssh/authorized_keys</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
<img src="http://images.cnitblog.com/blog/12097/201406/181325593481023.png" alt="" align="left" style="border:0px;"></p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
登录ssh</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
ssh localhost</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
<img src="http://images.cnitblog.com/blog/12097/201406/181325598014095.png" alt="" align="left" style="border:0px;"></p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
退出</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
exit</p>
<h2 style="line-height:1.8;font-size:21px;font-family:verdana, Arial, Helvetica, sans-serif;">
四、安装Java环境</h2>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
sudo apt-get install openjdk-7-jdk</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
<img src="http://images.cnitblog.com/blog/12097/201406/181326008641052.png" alt="" align="left" style="border:0px;"></p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
查看安装结果，输入命令：java -version，结果如下表示安装成功。</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
<img src="http://images.cnitblog.com/blog/12097/201406/181326019736724.png" alt="" align="left" style="border:0px;"></p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<h2 style="line-height:1.8;font-size:21px;font-family:verdana, Arial, Helvetica, sans-serif;">
<span style="line-height:1.8;font-family:'宋体';">五、安装</span>hadoop2.4.0（<span style="color:#ff0000;">此处版本没有什么限制，我用的2.7.0</span>）</h2>
<h3 style="font-size:16px;font-family:verdana, Arial, Helvetica, sans-serif;">
    1<span style="line-height:1.8;font-family:'宋体';">、官网下载</span><span style="line-height:1.8;text-decoration:underline;">http://mirror.bit.edu.cn/apache/hadoop/common/</span></h3>
<h3 style="font-size:16px;font-family:verdana, Arial, Helvetica, sans-serif;">
    2<span style="line-height:1.8;font-family:'宋体';">、安装</span></h3>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
        解压</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
        sudo tar xzf hadoop-2.4.0.tar.gz        </p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
<span style="color:rgb(51,51,51);">（</span><span style="color:#ff0000;">此处解压可能会遇到问题，必须在压缩文件所在的路径下进行解压，不然会找不到文件</span><span style="color:#333333;">）</span></p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
        假如我们要把hadoop安装到/usr/local下</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
        拷贝到/usr/local/下，文件夹为hadoop</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
        sudo mv hadoop-2.4.0 /usr/local/hadoop        </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
<img src="http://images.cnitblog.com/blog/12097/201406/181326023644310.png" alt="" align="left" style="border:0px;"></p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
        </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
赋予用户对该文件夹的读写权限</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
        sudo chmod 774 /usr/local/hadoop</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
<img src="http://images.cnitblog.com/blog/12097/201406/181326027238353.png" alt="" align="left" style="border:0px;"></p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
    </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
<span style="color:#333333;">（</span><span style="color:rgb(255,0,0);font-family:'宋体';line-height:28px;">sudo chmod -（代表类型）×××（所有者）×××（组用户）×××（其他用户） </span></p>
<div style="border-width:0px;list-style:none;font-family:'宋体';font-size:14px;line-height:28px;">
<span style="color:#ff0000;">三位数的每一位都表示一个用户类型的权限设置。取值是0～7，即二进制的[000]~[111]。 </span></div>
<div style="border-width:0px;list-style:none;font-family:'宋体';font-size:14px;line-height:28px;">
<span style="color:#ff0000;">这个三位的二进制数的每一位分别表示读、写、执行权限。 </span></div>
<div style="border-width:0px;list-style:none;font-family:'宋体';font-size:14px;line-height:28px;">
<span style="color:#ff0000;">如000表示三项权限均无，而100表示只读。这样，我们就有了下面的对应： </span></div>
<div style="border-width:0px;list-style:none;font-family:'宋体';font-size:14px;line-height:28px;">
<span style="color:#ff0000;">0 [000] 无任何权限 </span></div>
<div style="border-width:0px;list-style:none;font-family:'宋体';font-size:14px;line-height:28px;">
<span style="color:#ff0000;">4 [100] 只读权限 </span></div>
<div style="border-width:0px;list-style:none;font-family:'宋体';font-size:14px;line-height:28px;">
<span style="color:#ff0000;">6 [110] 读写权限 </span></div>
<div style="border-width:0px;list-style:none;font-family:'宋体';font-size:14px;line-height:28px;">
<span style="color:#ff0000;">7 [111] 读写执行权限</span><span style="color:rgb(51,51,51);"> </span></div>
<span style="color:#333333;">）</span><span style="color:#ff0000;">在这里我用的777，用774不能打开文件夹</span>
<p></p>
<h3 style="font-size:16px;font-family:verdana, Arial, Helvetica, sans-serif;">
3、配置</h3>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
        1）<span style="line-height:1.8;font-family:'宋体';">配置</span>~/.bashrc</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
<span style="line-height:1.8;font-family:'宋体';">配置</span><span style="line-height:25.2000007629395px;">该文件前需要知道Java的安装路径，用来设置JAVA_HOME环境变量，可以使用下面命令行查看安装路径</span></p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
<span style="line-height:25.2000007629395px;">update-alternatives - -config java</span></p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
        执行结果如下：</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
<img src="http://images.cnitblog.com/blog/12097/201406/181326030041140.png" alt="" align="left" style="border:0px;">        </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
    </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
完整的路径为</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
    /usr/lib/jvm/java-7-openjdk-amd64/jre/bin/java</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
    我们只取前面的部分 /usr/lib/jvm/java-7-openjdk-amd64</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
配置.bashrc文件</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
    sudo gedit ~/.bashrc<span style="line-height:25.2000007629395px;"> </span></p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
    该命令会打开该文件的编辑窗口，在文件末尾追加下面内容，然后保存，关闭编辑窗口。</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
#HADOOP VARIABLES START</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
export HADOOP_INSTALL=/usr/local/hadoop</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
export PATH=$PATH:$HADOOP_INSTALL/bin</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
export PATH=$PATH:$HADOOP_INSTALL/sbin</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
export HADOOP_MAPRED_HOME=$HADOOP_INSTALL</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
export HADOOP_COMMON_HOME=$HADOOP_INSTALL</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
export HADOOP_HDFS_HOME=$HADOOP_INSTALL</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
export YARN_HOME=$HADOOP_INSTALL</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_INSTALL/lib/native</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
export HADOOP_OPTS="-Djava.library.path=$HADOOP_INSTALL/lib"</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
#HADOOP VARIABLES END</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
最终结果如下图：</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
<img src="http://images.cnitblog.com/blog/12097/201406/181326035825782.png" alt="" align="left" style="border:0px;"></p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 <span style="line-height:25.2000007629395px;">执行下面命，使添加的环境变量生效：</span></p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 source ~/.bashrc</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
2）编辑/usr/local/hadoop/etc/hadoop/hadoop-env.sh</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
        执行下面命令<span style="line-height:1.8;font-family:'宋体';">，</span>打开该文件的编辑窗口</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
        sudo gedit /usr/local/hadoop/etc/hadoop/hadoop-env.sh</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
<span style="line-height:25.2000007629395px;">     找到JAVA_HOME变量，修改此变量如下</span></p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
     export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64    </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
     修改后的hadoop-env.sh文件如下所示<span style="line-height:1.8;font-family:'宋体';">：</span></p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
    <img src="http://images.cnitblog.com/blog/12097/201406/181326041767598.png" alt="" align="left" style="border:0px;"></p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<h2 style="line-height:1.8;font-size:21px;font-family:verdana, Arial, Helvetica, sans-serif;">
<span style="line-height:1.8;font-family:'宋体';">六、</span>WordCount测试</h2>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
单机模式安装完成，下面通过执行hadoop自带实例WordCount验证是否安装成功</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
/usr/local/hadoop路径下创建input文件夹    </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
mkdir input</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
    拷贝README.txt到input    </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
cp README.txt input</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
    执行WordCount</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
    bin/hadoop jar share/hadoop/mapreduce/sources/hadoop-mapreduce-examples-2.4.0-sources.jar org.apache.hadoop.examples.WordCount input output</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
<img src="http://images.cnitblog.com/blog/12097/201406/181326049269226.png" alt="" align="left" style="border:0px;"></p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
    </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
执行结果：</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
<img src="http://images.cnitblog.com/blog/12097/201406/181326055049570.png" alt="" align="left" style="border:0px;"></p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
执行 cat output/*，查看字符统计结果</p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
<img src="http://images.cnitblog.com/blog/12097/201406/181326062072484.png" alt="" align="left" style="border:0px;"></p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
 </p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
<span style="color:#ff0000;">一次安装成功，整个人都好的不得了了</span></p>
            </div>
                </div>