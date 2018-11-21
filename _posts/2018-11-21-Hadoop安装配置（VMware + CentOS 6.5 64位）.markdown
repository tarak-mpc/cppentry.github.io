---
layout:     post
title:      Hadoop安装配置（VMware + CentOS 6.5 64位）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1 id="Hadoop安装配置-环境" align="left" style="font-size:24px;font-weight:normal;line-height:1.25;border-bottom-color:rgb(204,204,204);">
<span style="font-size:18px;"><strong><span style="font-family:'Microsoft YaHei';">环境</span></strong></span></h1>
<p align="left" style="font-size:14px;line-height:20px;">
<span style="font-family:'Microsoft YaHei';font-size:18px;">系统环境：VMware + <strong>CentOS 6.5 64位</strong>。</span></p>
<p align="left" style="font-size:14px;line-height:20px;">
<span style="font-family:'Microsoft YaHei';font-size:18px;"><span class="replace_word" style="font-weight:bold;">Hadoop</span>版本：基于原生 Hadoop 2，可适合任何 Hadoop 2.x.y 版本，本文以<strong>Hadoop 2.6.0 (stable)</strong> 为例。</span></p>
<h2 id="Hadoop安装配置-创建hadoop用户" align="left" style="font-size:20px;font-weight:normal;line-height:1.5;border-bottom-color:rgb(204,204,204);">
<span style="font-size:18px;"><a name="t1"></a><span style="font-family:'Microsoft YaHei';">创建hadoop用户</span></span></h2>
<p align="left" style="font-size:14px;line-height:20px;">
<span style="font-family:'Microsoft YaHei';font-size:18px;">创建新用户hadoop（推荐） :</span></p>
<div class="table-wrap" align="left" style="font-size:14px;line-height:20px;">
<table class="confluenceTable" style="border-collapse:collapse;"><tbody><tr><th class="confluenceTh" style="border:1px solid rgb(221,221,221);vertical-align:top;background-color:rgb(240,240,240);">
<span style="font-family:'Microsoft YaHei';font-size:18px;">sudo useradd -m hadoop -s /bin/bash</span></th>
</tr></tbody></table></div>
<p align="left" style="font-size:14px;line-height:20px;">
<span style="font-family:'Microsoft YaHei';font-size:18px;">为 hadoop 用户增加管理员权限，方便部署：</span></p>
<div class="table-wrap" align="left" style="font-size:14px;line-height:20px;">
<table class="confluenceTable" style="border-collapse:collapse;"><tbody><tr><th class="confluenceTh" style="border:1px solid rgb(221,221,221);vertical-align:top;background-color:rgb(240,240,240);">
<ol style="text-align:justify;"><li>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;">切换root用户</span></p>
<p>
<span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';">su</span></span></p>
</li><li>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;">编辑修改/etc/sudoers配置文件</span></p>
<p>
<span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';">visudo</span></span></p>
</li><li>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;">在“root  ALL=(ALL)   ALL”这一行下面，再加入一行：</span></p>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;">hadoop  ALL=(ALL)     ALL</span></p>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;">其中，hadoop为你当前使用的用户名，也就是普通用户的用户名</span></p>
</li><li>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;">执行命令</span></p>
<p>
<span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';">sudo adduser hadoop sudo</span></span></p>
</li></ol></th>
</tr></tbody></table></div>
<h2 id="Hadoop安装配置-更新软件" align="left" style="font-size:20px;font-weight:normal;line-height:1.5;border-bottom-color:rgb(204,204,204);">
<span style="font-size:18px;"><a name="t2"></a><span style="font-family:'Microsoft YaHei';">更新软件</span></span></h2>
<div class="table-wrap" align="left" style="font-size:14px;line-height:20px;">
<table class="confluenceTable" style="border-collapse:collapse;"><tbody><tr><th class="confluenceTh" style="border:1px solid rgb(221,221,221);vertical-align:top;background-color:rgb(240,240,240);">
<span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';">yum update</span></span></th>
</tr></tbody></table></div>
<h2 id="Hadoop安装配置-安装SSHserver" align="left" style="font-size:20px;font-weight:normal;line-height:1.5;border-bottom-color:rgb(204,204,204);">
<span style="font-size:18px;"><a name="t3"></a><span style="font-family:'Microsoft YaHei';">安装SSH server</span></span></h2>
<p align="left" style="font-size:14px;line-height:20px;">
<span style="font-family:'Microsoft YaHei';font-size:18px;">集群、单节点模式都需要用到SSH登陆：</span></p>
<div class="table-wrap" align="left" style="font-size:14px;line-height:20px;">
<table class="confluenceTable" style="border-collapse:collapse;"><tbody><tr><th class="confluenceTh" style="border:1px solid rgb(221,221,221);vertical-align:top;background-color:rgb(240,240,240);">
<p style="text-align:left;">
<span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';">rpm -qa |grep ssh 检查是否装了SSH包</span></span></p>
<p style="text-align:left;">
<span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';">yum install openssh-server 没有的话安装ssh</span></span></p>
<p style="text-align:left;">
<span style="font-family:'Microsoft YaHei';font-size:18px;"> chkconfig --list sshd 检查SSHD是否在本运行级别下设置为开机启动</span></p>
<p style="text-align:left;">
<span style="font-family:'Microsoft YaHei';font-size:18px;"> chkconfig --level 2345 sshd on  如果没设置启动就设置</span></p>
<p style="text-align:left;">
<span style="font-family:'Microsoft YaHei';font-size:18px;"> service sshd restart  重新启动</span></p>
<p style="text-align:left;">
<span style="font-family:'Microsoft YaHei';font-size:18px;"> netstat -antp |grep sshd  确认是否启动了22端口</span></p>
<p style="text-align:left;">
<span style="font-family:'Microsoft YaHei';font-size:18px;"> iptables -nL  是否放行了22端口</span></p>
<p style="text-align:left;">
<span style="font-family:'Microsoft YaHei';font-size:18px;"> setup----&gt;防火墙设置   如果没放行就设置放行.</span></p>
</th>
</tr></tbody></table></div>
<p align="left" style="font-size:14px;line-height:20px;">
<span style="font-family:'Microsoft YaHei';font-size:18px;"> </span></p>
<p align="left" style="font-size:14px;line-height:20px;">
<span style="font-family:'Microsoft YaHei';font-size:18px;">安装后，登陆本机：</span></p>
<div class="table-wrap" align="left" style="font-size:14px;line-height:20px;">
<table class="confluenceTable" style="border-collapse:collapse;"><tbody><tr><th class="confluenceTh" style="border:1px solid rgb(221,221,221);vertical-align:top;background-color:rgb(240,240,240);">
<span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';">ssh localhost</span></span></th>
</tr></tbody></table></div>
<p align="left" style="font-size:14px;line-height:20px;">
<span style="font-family:'Microsoft YaHei';font-size:18px;">出现SSH首次登陆提示，输入 yes 。然后按提示输入密码，这样就登陆到本机了。</span></p>
<p align="left" style="font-size:14px;line-height:20px;">
<span style="font-family:'Microsoft YaHei';font-size:18px;"> </span></p>
<h2 id="Hadoop安装配置-配置SSH无密码登陆" align="left" style="font-size:20px;font-weight:normal;line-height:1.5;border-bottom-color:rgb(204,204,204);">
<span style="font-size:18px;"><a name="t4"></a><span style="font-family:'Microsoft YaHei';">配置SSH无密码登陆</span></span></h2>
<p align="left" style="font-size:14px;line-height:20px;">
<span style="font-family:'Microsoft YaHei';font-size:18px;">登陆是需要每次输入密码的，我们需要配置成SSH无密码登陆比较方便。</span></p>
<p align="left" style="font-size:14px;line-height:20px;">
<span style="font-family:'Microsoft YaHei';font-size:18px;">首先退出刚才的 ssh，就回到了我们原先的终端窗口，然后利用 ssh-keygen 生成密钥，并将密钥加入到授权中：</span></p>
<p align="left" style="font-size:14px;line-height:20px;">
<span style="font-family:'Microsoft YaHei';font-size:18px;">准备工作</span></p>
<div class="table-wrap" align="left" style="font-size:14px;line-height:20px;">
<table class="confluenceTable" style="border-collapse:collapse;"><tbody><tr><th class="confluenceTh" style="border:1px solid rgb(221,221,221);vertical-align:top;background-color:rgb(240,240,240);">
<div style="text-align:left;"><span style="font-family:'Microsoft YaHei';font-size:18px;">    1、确认本机sshd的配置文件（需要root权限）</span></div>
<div style="text-align:left;"><span style="font-family:'Microsoft YaHei';font-size:18px;">　　$ gedit /etc/ssh/sshd_config</span></div>
<div style="text-align:left;"><span style="font-family:'Microsoft YaHei';font-size:18px;">　　找到以下内容，并去掉注释符”#“</span></div>
<div style="text-align:left;"><span style="font-family:'Microsoft YaHei';font-size:18px;">　　RSAAuthentication yes</span></div>
<div style="text-align:left;"><span style="font-family:'Microsoft YaHei';font-size:18px;">　　PubkeyAuthentication yes</span></div>
<div style="text-align:left;"><span style="font-family:'Microsoft YaHei';font-size:18px;">　　AuthorizedKeysFile      .ssh/authorized_keys</span></div>
<div style="text-align:left;"><span style="font-family:'Microsoft YaHei';font-size:18px;">　  2、如果修改了配置文件需要重启sshd服务 （需要root权限）</span></div>
<div style="text-align:left;"><span style="font-family:'Microsoft YaHei';font-size:18px;">　　$ /sbin/service sshd restart</span></div>
</th>
</tr></tbody></table></div>
<p align="left" style="font-size:14px;line-height:20px;">
<span style="font-family:'Microsoft YaHei';font-size:18px;"><br></span></p>
<p align="left" style="font-size:14px;line-height:20px;">
<span style="font-family:'Microsoft YaHei';font-size:18px;">配置SSH无密码登录：</span></p>
<div class="table-wrap" align="left" style="font-size:14px;line-height:20px;">
<table class="confluenceTable" style="border-collapse:collapse;"><tbody><tr><th class="confluenceTh" style="border:1px solid rgb(221,221,221);vertical-align:top;background-color:rgb(240,240,240);">
<div style="text-align:left;"><span style="font-family:'Microsoft YaHei';font-size:18px;">1.生成公钥和私钥</span></div>
<div style="text-align:left;"><span style="font-family:'Microsoft YaHei';font-size:18px;">  ssh-keygen -t rsa</span></div>
<div style="text-align:left;"><span style="font-family:'Microsoft YaHei';font-size:18px;"> </span></div>
<div style="text-align:left;"><span style="font-family:'Microsoft YaHei';font-size:18px;">   默认在 ~/.ssh目录生成两个文件：</span></div>
<div style="text-align:left;"><span style="font-family:'Microsoft YaHei';font-size:18px;">    id_rsa      ：私钥</span></div>
<div style="text-align:left;"><span style="font-family:'Microsoft YaHei';font-size:18px;">    id_rsa.pub  ：公钥</span></div>
<div style="text-align:left;"><span style="font-family:'Microsoft YaHei';font-size:18px;">2.导入公钥到认证文件,更改权限</span></div>
<div style="text-align:left;"><span style="font-family:'Microsoft YaHei';font-size:18px;">    2.1 导入本机</span></div>
<div style="text-align:left;"><span style="font-family:'Microsoft YaHei';font-size:18px;">        cat ~/.ssh/id_rsa.pub &gt;&gt; ~/.ssh/authorized_keys  </span></div>
<div>
<div style="text-align:left;"><span style="font-family:'Microsoft YaHei';font-size:18px;">2.2 更改权限（对root用户和hadoop（普通用户）都要做这一步）</span></div>
<div style="text-align:left;"><span style="font-family:'Microsoft YaHei';font-size:18px;">       chmod 700 ~/.ssh</span></div>
<div style="text-align:left;"><span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';">       chmod 600 ~/.ssh/authorized_keys  </span></span></div>
</div>
</th>
</tr></tbody></table></div>
<div align="left" style="font-size:14px;line-height:20px;">
<span style="font-family:'Microsoft YaHei';font-size:18px;"><br></span>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;">此时再用 <code>ssh localhost</code> 命令，无需输入密码就可以直接登陆了，如下所示：</span></p>
<div class="table-wrap">
<table class="confluenceTable" style="border-collapse:collapse;"><tbody><tr><td class="confluenceTd" style="border:1px solid rgb(221,221,221);vertical-align:top;">
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">[<span class="external-link" style="text-decoration:none;">hadoop@localhost</span> ~]$ ssh localhost<br>
Last login: Fri Jun 19 01:17:09 2015 from localhost</span></div>
</td>
</tr></tbody></table></div>
<h2 id="Hadoop安装配置-安装Java环境" style="font-size:20px;font-weight:normal;line-height:1.5;border-bottom-color:rgb(204,204,204);">
<span style="font-size:18px;"><a name="t5"></a><span style="font-family:'Microsoft YaHei';">安装Java环境</span></span></h2>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;"><span class="replace_word" style="font-weight:bold;">Java</span>环境可选择<span class="replace_word" style="font-weight:bold;">Oracle</span> 的 JDK，或是 OpenJDK，这里安装原版JDK：</span></p>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">1. 拷贝JDK文件到本机/usr/etc/java目录下</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">2. 设置环境变量： <br>
 </span>
<div class="table-wrap">
<table class="confluenceTable" style="border-collapse:collapse;"><tbody><tr><th class="confluenceTh" style="border:1px solid rgb(221,221,221);vertical-align:top;background-color:rgb(240,240,240);">
<div style="text-align:left;"><span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';">vi /etc/profile</span></span></div>
<div>
<p style="text-align:left;">
<span style="font-family:'Microsoft YaHei';font-size:18px;">     在文件末尾加上：</span></p>
<div class="table-wrap">
<table class="confluenceTable" style="border-collapse:collapse;"><tbody><tr><td class="confluenceTd" style="border:1px solid rgb(221,221,221);vertical-align:top;">
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">JAVA_HOME=/usr/java/jdk1.7.0_75</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">JRE_HOME=/usr/java/jdk1.7.0_75/jre</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">CLASSPATH=.:$JAVA_HOME/<span class="external-link" style="text-decoration:none;">lib:/dt.jar:$JAVA_HOME/lib/tools.jar</span></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">PATH=$PATH:$JAVA_HOME/bin</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">export  JAVA_HOME</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">export  JRE_HOME</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">export  CLASSPATH</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">export  PATH</span></div>
</td>
</tr></tbody></table></div>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;"> </span></p>
</div>
</th>
</tr></tbody></table></div>
<span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';"> </span></span></div>
<div>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;"> </span></p>
</div>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;">3. 让该环境变量生效：</span></p>
<div class="table-wrap">
<table class="confluenceTable" style="border-collapse:collapse;"><tbody><tr><th class="confluenceTh" style="border:1px solid rgb(221,221,221);vertical-align:top;background-color:rgb(240,240,240);">
<p style="text-align:left;">
<span style="font-family:'Microsoft YaHei';font-size:18px;"> source ~/.bashrc</span></p>
<p style="text-align:left;">
<span style="font-family:'Microsoft YaHei';font-size:18px;"> echo $JAVA_HOME # 检验是否设置正确</span></p>
</th>
</tr></tbody></table></div>
<h1 id="Hadoop安装配置-安装Hadoop2" style="font-size:24px;font-weight:normal;line-height:1.25;border-bottom-color:rgb(204,204,204);">
<span style="font-size:18px;"><a name="t6"></a><span style="font-family:'Microsoft YaHei';">安装 Hadoop 2</span></span></h1>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;">最新的 Hadoop 2 稳定版可以通过 <span class="external-link" style="text-decoration:none;">http://mirror.bit.edu.cn/apache/hadoop/common/stable2/</span> （推荐）或者<span class="external-link" style="text-decoration:none;">http://mirrors.cnnic.cn/apache/hadoop/common/stable/</span> 下载，下载 <strong>hadoop-2.x.y.tar.gz</strong> 这个格式的文件，这是编译好的，另一个包含
 src 的则是 Hadoop 源代码。</span></p>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;">强烈建议也下载 <strong>hadoop-2.x.y.tar.gz.mds</strong> 这个文件，该文件包含了检验值可用于检查 hadoop-2.x.y.tar.gz 的完整性，否则若文件发生了损坏或下载不完整，Hadoop 将无法正常运行。</span></p>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;"><strong>如果你用的不是 2.6.0 版本，则将所有命令中出现的 2.6.0 更改为你所使用的版本</strong>。</span></p>
<div class="table-wrap">
<table class="confluenceTable" style="border-collapse:collapse;"><tbody><tr><th class="confluenceTh" style="border:1px solid rgb(221,221,221);vertical-align:top;background-color:rgb(240,240,240);">
<p style="text-align:left;">
<span style="font-family:'Microsoft YaHei';font-size:18px;">cd ~/Downloads  # 通过浏览器下载，默认保存在 “下载” 目录中</span></p>
<p style="text-align:left;">
<span style="font-family:'Microsoft YaHei';font-size:18px;">cat ./hadoop-2.6.0.tar.gz.mds | grep 'MD5' # 列出md5检验值</span></p>
<p style="text-align:left;">
<span style="font-family:'Microsoft YaHei';font-size:18px;">md5sum ./hadoop-2.6.0.tar.gz | tr "a-z" "A-Z" # 计算md5值，并转化为大写</span></p>
</th>
</tr></tbody></table></div>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;"> </span></p>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;">若文件不完整则这两个值一般差别很大，可以简单对比下前几个字符跟后几个字符是否相等即可，如下图所示，如果两个值不一样，请务必重新下载。</span></p>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;">选择将 Hadoop 安装至 /usr/local/ 中：</span></p>
<div class="table-wrap">
<table class="confluenceTable" style="border-collapse:collapse;"><tbody><tr><th class="confluenceTh" style="border:1px solid rgb(221,221,221);vertical-align:top;background-color:rgb(240,240,240);">
<p style="text-align:left;">
<span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';">cd ~/Downloads</span></span></p>
<p style="text-align:left;">
<span style="font-family:'Microsoft YaHei';font-size:18px;"> sudo tar -zxvf ./hadoop-2.6.0.tar.gz -C /usr/local # 解压到/usr/local中</span></p>
<p style="text-align:left;">
<span style="font-family:'Microsoft YaHei';font-size:18px;"> cd /usr/local/</span></p>
<p style="text-align:left;">
<span style="font-family:'Microsoft YaHei';font-size:18px;"> sudo mv ./hadoop-2.6.0/ ./hadoop # 将文件夹名改为hadoop</span></p>
<p style="text-align:left;">
<span style="font-family:'Microsoft YaHei';font-size:18px;"> sudo chown -R hadoop:hadoop ./hadoop # 修改文件权限</span></p>
</th>
</tr></tbody></table></div>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;"> </span></p>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;">Hadoop解压后即可使用。输入如下命令来检查 Hadoop 是否可用，成功则会显示命令用法：</span></p>
<div class="table-wrap">
<table class="confluenceTable" style="border-collapse:collapse;"><tbody><tr><th class="confluenceTh" style="border:1px solid rgb(221,221,221);vertical-align:top;background-color:rgb(240,240,240);">
<p>
<span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';">cd ./hadoop</span></span></p>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;"> ./bin/hadoop</span></p>
</th>
</tr></tbody></table></div>
<h1 id="Hadoop安装配置-Hadoop单机配置" style="font-size:24px;font-weight:normal;line-height:1.25;border-bottom-color:rgb(204,204,204);">
<span style="font-size:18px;"><a name="t7"></a><span style="font-family:'Microsoft YaHei';">Hadoop单机配置</span></span></h1>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;">Hadoop 默认配置是以非分布式模式运行，即单 Java 进程，方便进行调试。可以执行附带的例子 WordCount 来感受下 Hadoop 的运行。将 input 文件夹中的文件作为输入，统计当中符合正则表达式 <code>dfs[a-z.]+</code> 的单词出现的次数，并输出结果到 output 文件夹中。</span></p>
<div class="table-wrap">
<table class="confluenceTable" style="border-collapse:collapse;"><tbody><tr><th class="confluenceTh" style="border:1px solid rgb(221,221,221);vertical-align:top;background-color:rgb(240,240,240);">
<p style="text-align:left;">
<span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';">cd /usr/local/hadoop</span></span></p>
<p style="text-align:left;">
<span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';">mkdir input</span></span></p>
<p style="text-align:left;">
<span style="font-family:'Microsoft YaHei';font-size:18px;">cp ./etc/hadoop/*.xml input # 将配置文件作为输入文件</span></p>
<p style="text-align:left;">
<span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';"> ./bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-*.jar grep input output 'dfs[a-z.]+'</span></span></p>
<p style="text-align:left;">
<span style="font-family:'Microsoft YaHei';font-size:18px;"> cat ./output/* # 查看运行结果</span></p>
</th>
</tr></tbody></table></div>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;"> </span></p>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;">执行成功后如下所示，输出了作业的相关信息，输出的结果是符合正则的单词dfsadmin出现了1次</span></p>
<div class="table-wrap">
<table class="confluenceTable" style="border-collapse:collapse;"><tbody><tr><th class="confluenceTh" style="border:1px solid rgb(221,221,221);vertical-align:top;background-color:rgb(240,240,240);">
<span style="font-family:'Microsoft YaHei';font-size:18px;"><img class="confluence-embedded-image" src="http://192.168.1.178:8099/download/attachments/1081420/image2015-7-7%2016%3A28%3A45.png?version=1&amp;modificationDate=1436258122000&amp;api=v2" alt="" style="border:0px;"></span></th>
</tr></tbody></table></div>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;"> </span></p>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;">Hadoop 默认不会覆盖结果文件，因此再次运行上面实例会提示出错，需要将 <code>./output</code> 删除。</span></p>
<div class="table-wrap">
<table class="confluenceTable" style="border-collapse:collapse;"><tbody><tr><th class="confluenceTh" style="border:1px solid rgb(221,221,221);vertical-align:top;background-color:rgb(240,240,240);">
<span style="font-family:'Microsoft YaHei';font-size:18px;">rm -R ./output</span></th>
</tr></tbody></table></div>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;"> </span></p>
<h1 id="Hadoop安装配置-Hadoop伪分布式配置" style="font-size:24px;font-weight:normal;line-height:1.25;border-bottom-color:rgb(204,204,204);">
<span style="font-size:18px;"><a name="t8"></a><span style="font-family:'Microsoft YaHei';">Hadoop伪分布式配置</span></span></h1>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;">Hadoop 可以在单节点上以伪分布式的方式运行，Hadoop 进程以分离的 Java 进程来运行，节点既作为 NameNode 也作为 DataNode。</span></p>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;">Hadoop 的配置文件位于 <code>/usr/local/hadoop/etc/hadoop/</code> 中，伪分布式需要修改2个配置文件 <strong>core-site.xml</strong> 和 <strong>hdfs-site.xml</strong> 。Hadoop的配置文件是 xml 格式，每个配置以声明 property 的 name 和 value 的方式来实现。</span></p>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;">修改配置文件 <strong>core-site.xml</strong> (<code>vim /usr/local/hadoop/etc/hadoop/core-site.xml</code>)，将当中的</span></p>
<pre><span style="font-size:18px;"><code><span style="font-family:'Microsoft YaHei';">&lt;configuration&gt;
&lt;/configuration&gt;
</span></code></span></pre>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;">修改为下面配置：</span></p>
<div class="table-wrap">
<table class="confluenceTable" style="border-collapse:collapse;"><tbody><tr><th class="confluenceTh" style="border:1px solid rgb(221,221,221);vertical-align:top;background-color:rgb(240,240,240);">
<pre style="text-align:left;"></pre><div style="text-align:left;"><span style="font-size:18px;"><span style="line-height:20px;"><span style="font-family:'Microsoft YaHei';">&lt;configuration&gt;</span></span></span></div><span style="font-size:18px;"><code></code></span><div style="text-align:left;"><span style="font-size:18px;"><span style="line-height:20px;">    &lt;property&gt;</span></span></div><div style="text-align:left;"><span style="font-size:18px;"><span style="line-height:20px;">        &lt;name&gt;hadoop.tmp.dir&lt;/name&gt;</span></span></div><div style="text-align:left;"><span style="font-size:18px;"><span style="line-height:20px;">        &lt;value&gt;file:/usr/local/hadoop/tmp&lt;/value&gt;</span></span></div><div style="text-align:left;"><span style="font-size:18px;"><span style="line-height:20px;">        &lt;description&gt;Abase for other temporary directories.&lt;/description&gt;</span></span></div><div style="text-align:left;"><span style="font-size:18px;"><span style="line-height:20px;">    &lt;/property&gt;</span></span></div><span style="font-size:18px;">    &lt;property&gt;
</span><div style="text-align:left;"><span style="font-size:18px;"><span style="line-height:20px;">        &lt;name&gt;fs.defaultFS&lt;/name&gt;</span></span></div><span style="font-size:18px;">        &lt;value&gt;hdfs://localhost:9000&lt;/value&gt;
</span><div style="text-align:left;"><span style="font-size:18px;"><span style="line-height:20px;">&lt;/configuration&gt;</span></span></div><div style="text-align:left;"><span style="font-size:18px;"><span style="line-height:20px;">    &lt;/property&gt;</span></span></div>
</th>
</tr></tbody></table></div>
<pre><span style="font-size:18px;"><code><span style="font-family:'Microsoft YaHei';">
</span></code></span></pre>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;">同样的，修改配置文件 <strong>hdfs-site.xml</strong>：</span></p>
<div class="table-wrap">
<table class="confluenceTable" style="border-collapse:collapse;"><tbody><tr><th class="confluenceTh" style="border:1px solid rgb(221,221,221);vertical-align:top;background-color:rgb(240,240,240);">
<pre style="text-align:left;"></pre><div style="text-align:left;"><span style="font-size:18px;"><span style="line-height:20px;"><span style="font-family:'Microsoft YaHei';">&lt;configuration&gt;</span></span></span></div><span style="font-size:18px;"><code></code></span><div style="text-align:left;"><span style="font-size:18px;"><span style="line-height:20px;">    &lt;property&gt;</span></span></div><div style="text-align:left;"><span style="font-size:18px;"><span style="line-height:20px;">        &lt;name&gt;dfs.replication&lt;/name&gt;</span></span></div><div style="text-align:left;"><span style="font-size:18px;"><span style="line-height:20px;">        &lt;value&gt;1&lt;/value&gt;</span></span></div><span style="font-size:18px;">    &lt;/property&gt;
</span><div style="text-align:left;"><span style="font-size:18px;"><span style="line-height:20px;">        &lt;name&gt;dfs.namenode.name.dir&lt;/name&gt;</span></span></div><span style="font-size:18px;">    &lt;property&gt;
</span><div style="text-align:left;"><span style="font-size:18px;"><span style="line-height:20px;">        &lt;value&gt;file:/usr/local/hadoop/tmp/dfs/name&lt;/value&gt;</span></span></div><span style="font-size:18px;">    &lt;/property&gt;
    &lt;property&gt;
        &lt;name&gt;dfs.datanode.data.dir&lt;/name&gt;
</span><div style="text-align:left;"><span style="font-size:18px;"><span style="line-height:20px;">    &lt;/property&gt;</span></span></div><span style="font-size:18px;">        &lt;value&gt;file:/usr/local/hadoop/tmp/dfs/data&lt;/value&gt;
</span><div style="text-align:left;"><span style="font-size:18px;"><span style="line-height:20px;">&lt;/configuration&gt;</span></span></div>
</th>
</tr></tbody></table></div>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;"> </span></p>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">修改配置文件hadoop-env.sh：</span></div>
<div>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;"> </span></p>
<div class="table-wrap">
<table class="confluenceTable" style="border-collapse:collapse;"><tbody><tr><th class="confluenceTh" style="border:1px solid rgb(221,221,221);vertical-align:top;background-color:rgb(240,240,240);">
<div style="text-align:left;"><span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';">gedit /usr/local/hadoop/etc/hadoop/hadoop-env.sh </span></span></div>
<div style="text-align:left;"><span style="font-family:'Microsoft YaHei';font-size:18px;">添加：</span></div>
<div style="text-align:left;"><span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';">export  JAVA_HOME=/usr/java/jdk1.7.0_75</span></span></div>
</th>
</tr></tbody></table></div>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;"> </span></p>
</div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"><br></span></div>
<div><span style="font-size:18px;"><strong><span style="font-family:'Microsoft YaHei';">关于Hadoop配置项的一点说明</span></strong></span>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;">虽然只需要配置 fs.defaultFS 和 dfs.replication 就可以运行（官方教程如此），不过若没有配置 hadoop.tmp.dir 参数，则默认使用的临时目录为 /tmp/hadoo-hadoop，而这个目录在重启时有可能被系统清理掉，导致必须重新执行 format 才行。所以我们进行了设置，同时也指定 <span class="external-link" style="text-decoration:none;">dfs.namenode.name</span>.dir
 和 dfs.datanode.data.dir，否则在接下来的步骤中可能会出错。</span></p>
</div>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;">配置完成后，执行 namenode 的格式化:</span></p>
<div class="table-wrap">
<table class="confluenceTable" style="border-collapse:collapse;"><tbody><tr><th class="confluenceTh" style="border:1px solid rgb(221,221,221);vertical-align:top;background-color:rgb(240,240,240);">
<span style="font-family:'Microsoft YaHei';font-size:18px;">bin/hdfs namenode -format</span></th>
</tr></tbody></table></div>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;">成功的话，会看到 <code>successfully formatted</code> 的提示，且提示如下，<code>Exitting with status 0</code> 表示成功，若为 <code>Exitting with status 1</code> 则是出错。</span></p>
<div class="table-wrap">
<table class="confluenceTable" style="border-collapse:collapse;"><tbody><tr><th class="confluenceTh" style="border:1px solid rgb(221,221,221);vertical-align:top;background-color:rgb(240,240,240);">
<span style="font-family:'Microsoft YaHei';font-size:18px;"><img class="confluence-embedded-image" src="http://192.168.1.178:8099/download/attachments/1081420/image2015-7-7%2016%3A30%3A29.png?version=1&amp;modificationDate=1436258122000&amp;api=v2" alt="" style="border:0px;"></span></th>
</tr></tbody></table></div>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;"> </span></p>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;">接着开启 <code>NaneNode</code> 和 <code>DataNode</code> 守护进程。（以后要再启动hadoop，不需要再做format，直接做这一步就好了）</span></p>
<div class="table-wrap">
<table class="confluenceTable" style="border-collapse:collapse;"><tbody><tr><th class="confluenceTh" style="border:1px solid rgb(221,221,221);vertical-align:top;background-color:rgb(240,240,240);">
<span style="font-family:'Microsoft YaHei';font-size:18px;">sbin/start-dfs.sh</span></th>
</tr></tbody></table></div>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;"> </span></p>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;">若出现SSH提示，输入yes即可。</span></p>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;">启动完成后，可以通过命令 <code>jps</code> 来判断是否成功启动，若成功启动则会列出如下进程: <code>NameNode</code>、<code>DataNode</code>和<code>SecondaryNameNode</code>。（如果SecondaryNameNode没有启动，请运行 sbin/stop-dfs.sh 关闭进程，然后再次尝试启动尝试；如果 NameNode
 或 DataNode 没有启动，请仔细检查之前步骤）。</span></p>
<div>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;"> </span></p>
</div>
<h2 id="Hadoop安装配置-Hadoop伪分布式实例-WordCount" style="font-size:20px;font-weight:normal;line-height:1.5;border-bottom-color:rgb(204,204,204);">
<span style="font-size:18px;"><a name="t9"></a><span style="font-family:'Microsoft YaHei';">Hadoop伪分布式实例-WordCount</span></span></h2>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;">上面的单机模式，WordCount 读取的是本地数据，伪分布式读取的则是HDFS上的数据。要使用 HDFS，首先需要创建用户目录</span></p>
<div class="table-wrap">
<table class="confluenceTable" style="border-collapse:collapse;"><tbody><tr><th class="confluenceTh" style="border:1px solid rgb(221,221,221);vertical-align:top;background-color:rgb(240,240,240);">
<span style="font-family:'Microsoft YaHei';font-size:18px;">bin/hdfs dfs -mkdir -p /user/hadoop</span></th>
</tr></tbody></table></div>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;"> </span></p>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;">接着将 <code>etc/hadoop</code> 中的文件作为输入文件复制到分布式文件系统中，即将 <code>/usr/local/hadoop/etc/hadoop</code> 复制到分布式文件系统中的 <code>/user/hadoop/input</code> 中。上一步已创建了用户目录 /user/hadoop ，因此命令中就可以使用相对目录如 input，其对应的绝对路径就是
 /user/hadoop/input:</span></p>
<div class="table-wrap">
<table class="confluenceTable" style="border-collapse:collapse;"><tbody><tr><th class="confluenceTh" style="border:1px solid rgb(221,221,221);vertical-align:top;background-color:rgb(240,240,240);">
<p style="text-align:left;">
<span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';">bin/hdfs dfs -mkdir input</span></span></p>
<p style="text-align:left;">
<span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';">bin/hdfs dfs -put etc/hadoop/*.xml input</span></span></p>
</th>
</tr></tbody></table></div>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;"> </span></p>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;">复制完成后，可以通过如下命令查看文件列表：</span></p>
<div class="table-wrap">
<table class="confluenceTable" style="border-collapse:collapse;"><tbody><tr><th class="confluenceTh" style="border:1px solid rgb(221,221,221);vertical-align:top;background-color:rgb(240,240,240);">
<span style="font-family:'Microsoft YaHei';font-size:18px;">bin/hdfs dfs -ls input</span></th>
</tr></tbody></table></div>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;"> </span></p>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;">伪分布式运行MapReduce作业的方式跟单机模式相同，区别在于伪分布式读取的是HDFS中的文件（可以将单机步骤中创建的本地 input 文件夹删掉来验证这一点：rm -fr /usr/local/hadoop/input）。</span></p>
<div class="table-wrap">
<table class="confluenceTable" style="border-collapse:collapse;"><tbody><tr><th class="confluenceTh" style="border:1px solid rgb(221,221,221);vertical-align:top;background-color:rgb(240,240,240);">
<span style="font-family:'Microsoft YaHei';font-size:18px;">bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-*.jar grep input output 'dfs[a-z.]+'</span></th>
</tr></tbody></table></div>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;"> </span></p>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;">查看运行结果的命令（查看的是位于 HDFS 中的输出结果）：</span></p>
<div class="table-wrap">
<table class="confluenceTable" style="border-collapse:collapse;"><tbody><tr><th class="confluenceTh" style="border:1px solid rgb(221,221,221);vertical-align:top;background-color:rgb(240,240,240);">
<span style="font-family:'Microsoft YaHei';font-size:18px;">bin/hdfs dfs -cat output/*</span></th>
</tr></tbody></table></div>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;"> </span></p>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;">也可以将运行结果取回到本地：</span></p>
<div class="table-wrap">
<table class="confluenceTable" style="border-collapse:collapse;"><tbody><tr><th class="confluenceTh" style="border:1px solid rgb(221,221,221);vertical-align:top;background-color:rgb(240,240,240);">
<p style="text-align:left;">
<span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';">rm -R ./output</span></span></p>
<p style="text-align:left;">
<span style="font-family:'Microsoft YaHei';font-size:18px;">bin/hdfs dfs -get output output # 将 HDFS 上的 output 文件夹拷贝到本机</span></p>
<p style="text-align:left;">
<span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';">cat ./output/*</span></span></p>
</th>
</tr></tbody></table></div>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;"> </span></p>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;">Hadoop运行程序时，默认输出目录不能存在，因此再次运行需要执行如下命令删除 output文件夹:</span></p>
<div class="table-wrap">
<table class="confluenceTable" width="580" style="border-collapse:collapse;"><tbody><tr><th class="confluenceTh" style="border:1px solid rgb(221,221,221);vertical-align:top;background-color:rgb(240,240,240);">
<span style="font-family:'Microsoft YaHei';font-size:18px;">bin/hdfs dfs -rm -r /user/hadoop/output # 删除 output 文件夹</span></th>
</tr></tbody></table></div>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;"> </span></p>
<p>
<span style="font-family:'Microsoft YaHei';font-size:18px;">若要关闭 Hadoop，则运行</span></p>
<div class="table-wrap">
<table class="confluenceTable" style="border-collapse:collapse;"><tbody><tr><th class="confluenceTh" style="border:1px solid rgb(221,221,221);vertical-align:top;background-color:rgb(240,240,240);">
<span style="font-family:'Microsoft YaHei';font-size:18px;">sbin/stop-dfs.sh</span></th>
</tr></tbody></table></div>
</div>
<div align="left"><br></div>
            </div>
                </div>