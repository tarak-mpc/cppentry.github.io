---
layout:     post
title:      CDH 的Kerberos认证配置
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <div class="iteye-blog-content-contain">
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"><span style="color:#666666;font-family:'宋体', Arial;font-size:medium;"><span style="line-height:26px;">http://xubo8118.blog.163.com/blog/static/1855523322013918103857226/</span></span></div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">关于：</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">
<a style="color:#565656;" href="http://dongxicheng.org/mapreduce/hadoop-security/" rel="nofollow">hadoop的安全机制</a> </div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"><a style="color:#565656;" href="http://dongxicheng.org/mapreduce/hadoop-kerberos-introduction/" rel="nofollow">hadoop kerberos的安全机制</a></div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"> </div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">参考Cloudera官方文档：<a style="color:#0074c0;font-family:'Lucida Grande CY', 'Lucida Grande', 'Lucida Sans', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:#fafafa;" title="Configuring Hadoop Security in CDH3" href="https://ccp.cloudera.com/display/CDHDOC/Configuring+Hadoop+Security+in+CDH3" rel="nofollow">Configuring Hadoop Security in CDH3</a>
</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"> </div>
<p style="font-size:14px;"><span style="font-family:'宋体', Arial;font-size:16px;line-height:26px;color:#008080;"><strong>一、部署无kerberos认证的Hadoop环境</strong></span></p>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">参考另一篇笔记：<a style="color:#565656;" href="http://blog.chinaunix.net/uid-1838361-id-287231.html" rel="nofollow">hadoop集群部署</a>
</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">或者按照Cloudera的官方文档：<a style="color:#0074c0;font-family:'Lucida Grande CY', 'Lucida Grande', 'Lucida Sans', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:#fafafa;" title="CDH3 Installation Guide" href="https://ccp.cloudera.com/display/CDHDOC/CDH3+Installation+Guide" rel="nofollow">CDH3 Installation Guide</a><span style="color:#505050;font-family:'Lucida Grande CY', 'Lucida Grande', 'Lucida Sans', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:#fafafa;">.</span>
</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"> </div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"><span style="color:#008080;font-size:large;"><strong>二、环境说明</strong></span></div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">1、主机名</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">之前部署hadoop集群时，没有使用节点的hostname，而是在hosts文件里添加了ip要域名的解析，部署后的hadoop没有问题，但是在为集群添加kerberos认证时因为这一点，遇到很多的问题。所以，建议还是使用节点的hostname来做解析。</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"> </div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">集群中包含一个NameNode/JobTracker，两个DataNode/TaskTracker。</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"> </div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">hosts文件</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">
<div id="codeText" class="codeText" style="border:1px solid #dddddd;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;"><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;">172.18.6.152 nn.hadoop.local</li>
<li style="margin-left:0px;">172.18.6.143 dn143.hadoop.local</li>
<li style="margin-left:0px;">172.18.6.145 dn145.hadoop.local</li>
</ol></div>
注意：hosts文件中不要包含127.0.0.1的解析。</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"> </div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">2、hadoop安装部署相关</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">hadoop 和kerberos的部署需要hadoop-sbin和hadoop-native。</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">如果使用的是rpm部署的hadoop，需要安装上面的两个rpm包。</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">我的集群使用的是tar包部署的，所以默认是包含这两部分文件的，可以检查一下：</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">hadoop-sbin对应的文件是：</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">/usr/local/hadoop/sbin/Linux-amd64-64</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">文件夹中包含两个文件：jsvc、task-controller</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"> </div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">hadoop-native对应的目录是：</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">/usr/local/hadoop/lib/native</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"> </div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">3、AES-256加密</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">我的系统使用的是centos6.2和centos5.7，对于使用centos5.6及以上的系统，默认使用AES-256来加密的。这就需要集群中的<span style="color:#f00000;">所有节点</span>和hadoop user machine上安装<span style="color:#505050;font-family:'Lucida Grande CY', 'Lucida Grande', 'Lucida Sans', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:#fafafa;"> </span><a class="external-link" style="color:#0074c0;font-family:'Lucida Grande CY', 'Lucida Grande', 'Lucida Sans', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:#fafafa;" href="http://www.oracle.com/technetwork/java/javase/downloads/index.html" rel="nofollow">Java Cryptography Extension (JCE) Unlimited Strength Jurisdiction Policy File</a>
</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">打开上面的链接，在页面的下方，下载jdk对应的文件，jdk1.6.0_22下载下面的文件：</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"><span style="color:#f00000;">注：如果后面出现login failed的错误，应先检查是否是从官方网站下载的JCE。</span></div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"><a style="color:#565656;" href="http://blog.chinaunix.net/attachment/201206/14/1838361_1339657797576w.jpg" rel="nofollow"><img src="http://blog.chinaunix.net/attachment/201206/14/1838361_1339657797576w.jpg" alt="" width="548" height="83" border="0"></a></div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">下载的文件是一个zip包，解开后，将里面的两个文件放到下面的目录中：</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">/usr/java/jdk1.6.0_22/jre/lib/security</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">注：也可以不使用AED-256加密，方法见官方文档对应的部分。</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"> </div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"><span style="color:#008080;font-size:large;"><strong>三、部署KDC</strong></span></div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">1、安装kdc server</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">只需要在kdc中安装</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">yum install krb5-server.x86_64  krb5-devel.x86_64</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"> </div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">2、配置文件</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">kdc服务器涉及到三个配置文件：</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">/etc/krb5.conf、</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">/var/kerberos/krb5kdc/kdc.conf、</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">
<span style="font-size:10.5pt;font-family:Calibri;">/var/kerberos/krb5kdc</span><span style="font-size:10.5pt;font-family:'宋体';">/</span><span style="font-size:10.5pt;font-family:Calibri;">kadm5.acl</span>
</div>
<p class="p0" style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"> </p>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"> </div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">hadoop集群中其他服务器涉及到的kerberos配置文件：/etc/krb5.conf。</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"><span style="color:#f00000;">将kdc中的/etc/krb5.conf拷贝到集群中其他服务器即可。</span></div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">集群如果开启selinux了，拷贝后可能需要执行restorecon -R -v /etc/krb5.conf</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"> </div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">/etc/krb5.conf</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">
<div class="codeText" style="border:1px solid #dddddd;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;"><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;">[logging]</li>
<li style="margin-left:0px;">default = FILE:/var/log/krb5libs.log</li>
<li style="margin-left:0px;">kdc = FILE:/var/log/krb5kdc.log</li>
<li style="margin-left:0px;">admin_server = FILE:/var/log/kadmind.log</li>
<li style="margin-left:0px;">[libdefaults]</li>
<li style="margin-left:0px;">default_realm = for_hadoop</li>
<li style="margin-left:0px;">dns_lookup_realm = false</li>
<li style="margin-left:0px;">dns_lookup_kdc = false</li>
<li style="margin-left:0px;">ticket_lifetime = 24h</li>
<li style="margin-left:0px;">renew_lifetime = 2d</li>
<li style="margin-left:0px;">forwardable = true</li>
<li style="margin-left:0px;">renewable = true</li>
<li style="margin-left:0px;">[realms]</li>
<li style="margin-left:0px;">for_hadoop = {</li>
<li style="margin-left:0px;">kdc = 172.18.6.152:88</li>
<li style="margin-left:0px;">admin_server = 172.18.6.152:749</li>
<li style="margin-left:0px;">}</li>
<li style="margin-left:0px;">[domain_realm]</li>
<li style="margin-left:0px;">[kdc]</li>
<li style="margin-left:0px;">profile=/var/kerberos/krb5kdc/kdc.conf</li>
</ol></div>
/var/kerberos/krb5kdc/kdc.conf</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">
<div class="codeText" style="border:1px solid #dddddd;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;"><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;">[kdcdefaults]</li>
<li style="margin-left:0px;">kdc_ports = 88</li>
<li style="margin-left:0px;">kdc_tcp_ports = 88</li>
<li style="margin-left:0px;">[realms]</li>
<li style="margin-left:0px;">for_hadoop = {</li>
<li style="margin-left:0px;">master_key_type = aes256-cts</li>
<li style="margin-left:0px;"><span style="color:#f00000;">max_life = 25h</span></li>
<li style="margin-left:0px;"><span style="color:#f00000;font-size:large;"><strong>max_renewable_life = 4w</strong></span></li>
<li style="margin-left:0px;">acl_file = /var/kerberos/krb5kdc/kadm5.acl</li>
<li style="margin-left:0px;">dict_file = /usr/share/dict/words</li>
<li style="margin-left:0px;">admin_keytab = /var/kerberos/krb5kdc/kadm5.keytab</li>
<li style="margin-left:0px;">supported_enctypes = aes256-cts:normal aes128-cts:normal des3-hmac-sha1:normal arcfour-hmac:normal des-hmac-sha1:normal des-cbc-md</li>
<li style="margin-left:0px;">5:normal des-cbc-crc:normal</li>
<li style="margin-left:0px;">}</li>
</ol></div>
<p class="p0"><span style="font-size:10.5pt;font-family:Calibri;">/var/kerberos/krb5kdc</span><span style="font-size:10.5pt;font-family:'宋体';">/</span><span style="font-size:10.5pt;font-family:Calibri;">kadm5.acl</span></p>
<p class="p0"> </p>
<div class="codeText" style="border:1px solid #dddddd;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;"><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;">*/admin@for_hadoop *</li>
</ol></div>
<p class="p0"><span style="font-size:10.5pt;">3、创建数据库</span></p>
<p class="p0"> </p>
<div class="codeText" style="border:1px solid #dddddd;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;"><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;">#kdb5_util create -r for_hadoop -s</li>
</ol></div>
<p> </p>
</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">该命令会在/var/kerberos/krb5kdc/目录下创建principal数据库。</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"> </div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">4、关于kerberos的管理</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">
<div><span style="color:#f00000;">可以使用kadmin.local或kadmin,至于使用哪个，取决于账户和访问权限：</span></div>
<div>kadmin.local（on the KDC machine）or kadmin （from any machine）</div>
<div>如果有访问kdc服务器的root权限，但是没有kerberos admin账户，使用kadmin.local</div>
<div>如果没有访问kdc服务器的root权限，但是用kerberos admin账户，使用kadmin</div>
</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"> </div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">5、创建远程管理的管理员</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">
<div class="codeText" style="border:1px solid #dddddd;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;"><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;">#kadmin.local</li>
<li style="margin-left:0px;">addprinc root/admin@for_hadoop</li>
</ol></div>
</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">密码不能为空，且需妥善保存。</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"> </div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">6、创建测试用户</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">
<div class="codeText" style="border:1px solid #dddddd;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;"><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;">#kadmin.local</li>
<li style="margin-left:0px;">addprinc test</li>
</ol></div>
</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"> </div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">7、常用kerberos管理命令</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">
<div class="codeText" style="border:1px solid #dddddd;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;"><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;">#kadmin.local</li>
<li style="margin-left:0px;">列出所有用户 listprincs</li>
<li style="margin-left:0px;">查看某个用户属性，如 getprinc hdfs/nn.hadoop.local@for_hadoop</li>
<li style="margin-left:0px;">注意，是getprinc，没有's'</li>
<li style="margin-left:0px;">添加用户 addprinc</li>
<li style="margin-left:0px;">更多，查看帮助</li>
</ol></div>
</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">8、添加kerberos自启动及重启服务</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">
<div class="codeText" style="border:1px solid #dddddd;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;"><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;">chkconfig --level 35 krb5kdc on</li>
<li style="margin-left:0px;">chkconfig --level 35 kadmin on</li>
<li style="margin-left:0px;">service krb5kdc restart</li>
<li style="margin-left:0px;">service kadmin restart</li>
</ol></div>
</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">9、测试</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">使用之前创建的test用户</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">
<div class="codeText" style="border:1px solid #dddddd;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;"><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;"># kinit test</li>
<li style="margin-left:0px;">Password for test@for_hadoop:</li>
<li style="margin-left:0px;">#</li>
</ol></div>
输入密码后，没有报错即可。</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">
<br><div class="codeText" style="border:1px solid #dddddd;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;"><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;"><span style="color:#f00000;"># klist -e</span></li>
<li style="margin-left:0px;">Ticket cache: FILE:/tmp/krb5cc_0</li>
<li style="margin-left:0px;"><span style="color:#f00000;">Default principal: test@for_hadoop</span></li>
<li style="margin-left:0px;">Valid starting Expires Service principal</li>
<li style="margin-left:0px;">06/14/12 15:42:33 06/15/12 15:42:33 krbtgt/for_hadoop@for_hadoop</li>
<li style="margin-left:0px;">renew until 06/21/12 15:42:33, Etype (skey, tkt): AES-256 CTS mode with 96-bit SHA-1 HMAC, AES-256 CTS mode with 96-bit SHA-1 HMAC</li>
<li style="margin-left:0px;">Kerberos 4 ticket cache: /tmp/tkt0</li>
<li style="margin-left:0px;">klist: You have no tickets cached</li>
</ol></div>
可以看到，已经以test@for_hadoop登陆成功。</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"> </div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"><span style="color:#008080;font-size:large;"><strong>四、为hadoop创建认证规则（Principals）和keytab</strong></span></div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">
<div>1、一些概念</div>
<div>Kerberos principal用于在kerberos加密系统中标记一个唯一的身份。</div>
<div>kerberos为kerberos principal分配tickets使其可以访问由kerberos加密的hadoop服务。</div>
<div>对于hadoop，principals的格式为username/fully.qualified.domain.name@YOUR-REALM.COM.</div>
</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"> </div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">
<div>keytab是包含principals和加密principal key的文件。</div>
<div>keytab文件对于每个host是唯一的，因为key中包含hostname。keytab文件用于不需要人工交互和保存纯文本密码，实现到kerberos上验证一个主机上的principal。</div>
<div>因为服务器上可以访问keytab文件即可以以principal的身份通过kerberos的认证，所以，keytab文件应该被妥善保存，应该只有少数的用户可以访问。</div>
</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"> </div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">按照Cloudrea的文档，我们也使用两个用户hdfs和mapred，之前已经在linux上创建了相应的用户。</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"> </div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">2、为集群中每个服务器节点添加三个principals，分别是hdfs、mapred和host。</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">
<div class="codeText" style="border:1px solid #dddddd;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;"><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;">创建hdfs principal</li>
<li style="margin-left:0px;">kadmin: addprinc -randkey hdfs/<span>nn.hadoop.local</span>@for_hadoop</li>
<li style="margin-left:0px;">
<span>kadmin: addprinc -randkey hdfs/dn143</span><span>.hadoop.local</span><span>@for_hadoop</span>
</li>
<li style="margin-left:0px;"><span><span>kadmin: addprinc -randkey hdfs/dn145</span><span>.hadoop.local</span><span>@for_hadoop</span></span></li>
<li style="margin-left:0px;">创建mapred principal</li>
<li style="margin-left:0px;">kadmin: addprinc -randkey mapred/<span>nn.hadoop.local</span><span>@for_hadoop</span>
</li>
<li style="margin-left:0px;">
<span>kadmin: addprinc -randkey mapred/</span><span>dn143</span><span>.hadoop.local</span><span>@for_hadoop</span>
</li>
<li style="margin-left:0px;">
<span>kadmin: addprinc -randkey mapred/</span><span>dn145</span><span>.hadoop.local</span><span>@for_hadoop</span>
</li>
<li style="margin-left:0px;">创建host principal</li>
<li style="margin-left:0px;">kadmin: addprinc -randkey host/<span>nn.hadoop.local</span><span>@for_hadoop</span>
</li>
<li style="margin-left:0px;"><span><span>kadmin: addprinc -randkey host/dn143</span><span>.hadoop.local</span><span>@for_hadoop</span></span></li>
<li style="margin-left:0px;"><span><span><span>kadmin: addprinc -randkey host/dn145</span><span>.hadoop.local</span><span>@for_hadoop</span></span></span></li>
<li style="margin-left:0px;">创建完成后，查看：</li>
<li style="margin-left:0px;">kadmin: listprincs</li>
</ol></div>
</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">3、创建keytab文件</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">创建包含hdfs principal和host principal的hdfs keytab</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">
<div>
<div class="codeText" style="border:1px solid #dddddd;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;"><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;">kadmin: xst -norandkey -k hdfs.keytab hdfs/fully.qualified.domain.name host/fully.qualified.domain.name</li>
</ol></div>
</div>
<div>创建包含mapred principal和host principal的mapred keytab</div>
<div>
<div class="codeText" style="border:1px solid #dddddd;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;"><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;">kadmin: xst -norandkey -k mapred.keytab mapred/fully.qualified.domain.name host/fully.qualified.domain.name</li>
</ol></div>
</div>
</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"><span style="font-size:medium;"> </span></div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">
<div><span style="font-size:small;"> </span></div>
<div><span style="font-size:small;">注：上面的方法使用了xst的<span style="color:#f00000;">norandkey</span>参数，有些kerberos不支持该参数，我在Centos6.2上即不支持该参数。</span></div>
<div><span style="font-size:small;">当不支持该参数时有这样的提示：<span style="color:#f00000;">Principal -norandkey does not exist.</span></span></div>
<div><span style="font-size:small;">需要使用下面的方法来生成keytab文件。</span></div>
<div><span style="font-size:small;"> </span></div>
<div><span style="font-size:small;">生成独立key</span></div>
<div style="font-size:medium;">
<div class="codeText" style="border:1px solid #dddddd;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;">
<ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;"># <span style="color:#000000;font-family:Simsun;line-height:18px;font-size:medium;">cd /var/kerberos/krb5kdc</span>
</li>
<li style="margin-left:0px;">#kadmin</li>
<li style="margin-left:0px;">kadmin: xst -k hdfs-unmerged.keytab hdfs/<span>nn.hadoop.local</span><span>@for_hadoop</span>
</li>
<li style="margin-left:0px;"><span><span>kadmin: xst -k hdfs-unmerged.keytab hdfs/d</span><span>n143.hadoop.local</span><span>@for_hadoop</span></span></li>
<li style="margin-left:0px;"><span><span><span>kadmin: xst -k hdfs-unmerged.keytab hdfs/d</span><span>n145.hadoop.local</span><span>@for_hadoop</span></span></span></li>
<li style="margin-left:0px;"><span><span><span> </span></span></span></li>
<li style="margin-left:0px;">kadmin: xst -k mapred-unmerged.keytab mapred/<span>nn.hadoop.local</span><span>@for_hadoop</span>
</li>
<li style="margin-left:0px;"><span><span>kadmin: xst -k mapred-unmerged.keytab mapred/dn143</span><span>.hadoop.local</span><span>@for_hadoop</span></span></li>
<li style="margin-left:0px;"><span><span><span>kadmin: xst -k mapred-unmerged.keytab mapred/dn145</span><span>.hadoop.local</span><span>@for_hadoop</span></span></span></li>
</ol><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;">
<span>kadmin: xst -k host.keytab host/</span><span>nn.hadoop.local</span><span>@for_hadoop</span>
</li>
<li style="margin-left:0px;"><span><span>kadmin: xst -k host.keytab host/d</span><span>n143.hadoop.local</span><span>@for_hadoop</span></span></li>
<li style="margin-left:0px;"><span><span><span>kadmin: xst -k host.keytab host/d</span><span>n145.hadoop.local</span><span>@for_hadoop</span></span></span></li>
</ol></div>
合并key</div>
<div style="font-size:medium;">
<div>使用ktutil 合并前面创建的keytab</div>
<div>
<div class="codeText" style="border:1px solid #dddddd;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;"><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;"># <span style="color:#000000;font-family:Simsun;line-height:18px;font-size:medium;">cd /var/kerberos/krb5kdc</span>
</li>
<li style="margin-left:0px;">#ktutil</li>
<li style="margin-left:0px;">ktutil: rkt hdfs-unmerged.keytab</li>
<li style="margin-left:0px;">ktutil: rkt host.keytab</li>
<li style="margin-left:0px;">ktutil: wkt hdfs.keytab</li>
<li style="margin-left:0px;">ktutil: clear</li>
<li style="margin-left:0px;">ktutil: rkt mapred-unmerged.keytab</li>
<li style="margin-left:0px;">ktutil: rkt host.keytab</li>
<li style="margin-left:0px;">ktutil: wkt mapred.keytab</li>
</ol></div>
</div>
</div>
<div style="font-size:medium;"> </div>
<div style="font-size:medium;">
<div>这个过程创建了两个文件，hdfs.keytab和mapred.keytab，分别包含hdfs和host的principals，mapred和host的principals。</div>
<div> </div>
<div>使用klist显示keytab文件列表，一个正确的hdfs keytab文件看起来类似于：</div>
<div>
<br><div class="codeText" style="border:1px solid #dddddd;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;"><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;">#cd /var/kerberos/krb5kdc</li>
<li style="margin-left:0px;">#klist -e -k -t hdfs.keytab</li>
<li style="margin-left:0px;">Keytab name: WRFILE:hdfs.keytab</li>
<li style="margin-left:0px;">slot KVNO Principal</li>
<li style="margin-left:0px;">---- ---- ---------------------------------------------------------------------</li>
<li style="margin-left:0px;">1 7 host/fully.qualified.domain.name@YOUR-REALM.COM (DES cbc mode with CRC-32)</li>
<li style="margin-left:0px;">2 7 host/fully.qualified.domain.name@YOUR-REALM.COM (Triple DES cbc mode with HMAC/sha1)</li>
<li style="margin-left:0px;">3 7 hdfs/fully.qualified.domain.name@YOUR-REALM.COM (DES cbc mode with CRC-32)</li>
<li style="margin-left:0px;">4 7 hdfs/fully.qualified.domain.name@YOUR-REALM.COM (Triple DES cbc mode with HMAC/sha1)</li>
</ol></div>
</div>
<div>验证是否正确合并了key，使用合并后的keytab，分别使用hdfs和host principals来获取证书。</div>
<div>
<br><div class="codeText" style="border:1px solid #dddddd;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;"><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;"># kinit -k -t hdfs.keytab hdfs/fully.qualified.domain.name@YOUR-REALM.COM</li>
<li style="margin-left:0px;"># kinit -k -t hdfs.keytab host/fully.qualified.domain.name@YOUR-REALM.COM</li>
</ol></div>
</div>
<div>如果出现错误：</div>
<div> "kinit: Key table entry not found while getting initial credentials",</div>
<div>则上面的合并有问题，重新执行前面的操作。</div>
</div>
</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"> </div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">4、部署kerberos keytab文件</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">
<div>在集群中所有节点，执行下面的操作来部署hdfs.keytab和mapred.keytab文件</div>
<div> </div>
<div>拷贝hdfs.keytab和mapred.keytab文件到hadoop可以访问的目录。</div>
<div>
<div class="codeText" style="border:1px solid #dddddd;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;"><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;">scp hdfs.keytab mapred.keytab host:/usr/local/hadoop/conf</li>
</ol></div>
</div>
<div><span style="color:#f00000;"><strong>确保hdfs.keytab对hdfs用户可读</strong></span></div>
<div><span style="color:#f00000;"><strong>确报mapred.keytab对mapred用户可读</strong></span></div>
</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">后面经常会遇到使用keytab login失败的问题，首先需要检查的就是文件的权限。</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"> </div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"><span style="color:#008080;font-size:large;"><strong>五、停止hadoop集群</strong></span></div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"> </div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"><strong><span style="color:#008080;font-size:large;">六、Enable Hadoop Security</span></strong></div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">在集群中所有节点的<span style="font-size:large;">core-site.xml</span>文件中添加下面的配置</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">
<div class="codeText" style="border:1px solid #dddddd;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;"><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>name<span style="color:#0000cc;">&gt;</span>hadoop<span style="color:#0000cc;">.</span>security<span style="color:#0000cc;">.</span>authentication<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>name<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>value<span style="color:#0000cc;">&gt;</span>kerberos<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>value<span style="color:#0000cc;">&gt;</span> <span style="color:#0000cc;">&lt;</span>!-- A value of <span style="color:#ff00ff;">"simple"</span> would disable security<span style="color:#0000cc;">.</span> --<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;"> </li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>name<span style="color:#0000cc;">&gt;</span>hadoop<span style="color:#0000cc;">.</span>security<span style="color:#0000cc;">.</span>authorization<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>name<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>value<span style="color:#0000cc;">&gt;</span>true<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>value<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>property<span style="color:#0000cc;">&gt;</span>
</li>
</ol></div>
<strong><span style="color:#008080;font-size:large;">七、Configure Secure HDFS</span></strong>
</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">1、在集群中所有节点的<span style="font-size:large;">hdfs-site.xml</span>文件中添加下面的配置 <br><div class="codeText" style="border:1px solid #dddddd;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;"><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span>!-- General HDFS security config --<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>name<span style="color:#0000cc;">&gt;</span>dfs<span style="color:#0000cc;">.</span>block<span style="color:#0000cc;">.</span>access<span style="color:#0000cc;">.</span>token<span style="color:#0000cc;">.</span>enable<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>name<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>value<span style="color:#0000cc;">&gt;</span>true<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>value<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;"> </li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span>!-- NameNode security config --<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>name<span style="color:#0000cc;">&gt;</span>dfs<span style="color:#0000cc;">.</span>https<span style="color:#0000cc;">.</span>address<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>name<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>value<span style="color:#0000cc;">&gt;</span><span style="color:#0000cc;">&lt;</span>fully qualified domain name of NN<span style="color:#0000cc;">&gt;</span><span style="color:#0000cc;">:</span>50470<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>value<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>name<span style="color:#0000cc;">&gt;</span>dfs<span style="color:#0000cc;">.</span>https<span style="color:#0000cc;">.</span>port<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>name<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>value<span style="color:#0000cc;">&gt;</span>50470<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>value<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>name<span style="color:#0000cc;">&gt;</span>dfs<span style="color:#0000cc;">.</span>namenode<span style="color:#0000cc;">.</span>keytab<span style="color:#0000cc;">.</span>file<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>name<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>value<span style="color:#0000cc;">&gt;</span><span style="color:#0000cc;">/</span>usr/local<span style="color:#0000cc;">/</span>hadoop<span style="color:#0000cc;">/</span>conf<span style="color:#0000cc;">/</span>hdfs<span style="color:#0000cc;">.</span>keytab<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>value<span style="color:#0000cc;">&gt;</span> <span style="color:#0000cc;">&lt;</span>!-- path to the HDFS keytab --<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>name<span style="color:#0000cc;">&gt;</span>dfs<span style="color:#0000cc;">.</span>namenode<span style="color:#0000cc;">.</span>kerberos<span style="color:#0000cc;">.</span>principal<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>name<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>value<span style="color:#0000cc;">&gt;</span>hdfs<span style="color:#0000cc;">/</span>_HOST@YOUR-REALM<span style="color:#0000cc;">.</span>COM<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>value<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>name<span style="color:#0000cc;">&gt;</span>dfs<span style="color:#0000cc;">.</span>namenode<span style="color:#0000cc;">.</span>kerberos<span style="color:#0000cc;">.</span>https<span style="color:#0000cc;">.</span>principal<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>name<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>value<span style="color:#0000cc;">&gt;</span>host<span style="color:#0000cc;">/</span>_HOST@YOUR-REALM<span style="color:#0000cc;">.</span>COM<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>value<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;"> </li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span>!-- Secondary NameNode security config --<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>name<span style="color:#0000cc;">&gt;</span>dfs<span style="color:#0000cc;">.</span>secondary<span style="color:#0000cc;">.</span>https<span style="color:#0000cc;">.</span>address<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>name<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>value<span style="color:#0000cc;">&gt;</span><span style="color:#0000cc;">&lt;</span>fully qualified domain name of 2NN<span style="color:#0000cc;">&gt;</span><span style="color:#0000cc;">:</span>50495<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>value<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>name<span style="color:#0000cc;">&gt;</span>dfs<span style="color:#0000cc;">.</span>secondary<span style="color:#0000cc;">.</span>https<span style="color:#0000cc;">.</span>port<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>name<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>value<span style="color:#0000cc;">&gt;</span>50495<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>value<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>name<span style="color:#0000cc;">&gt;</span>dfs<span style="color:#0000cc;">.</span>secondary<span style="color:#0000cc;">.</span>namenode<span style="color:#0000cc;">.</span>keytab<span style="color:#0000cc;">.</span>file<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>name<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>value<span style="color:#0000cc;">&gt;</span><span style="color:#0000cc;"><span>/</span><span style="color:#5c5c5c;">usr/local</span>/</span>hadoop<span style="color:#0000cc;">/</span>conf<span style="color:#0000cc;">/</span>hdfs<span style="color:#0000cc;">.</span>keytab<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>value<span style="color:#0000cc;">&gt;</span> <span style="color:#0000cc;">&lt;</span>!-- path to the HDFS keytab --<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>name<span style="color:#0000cc;">&gt;</span>dfs<span style="color:#0000cc;">.</span>secondary<span style="color:#0000cc;">.</span>namenode<span style="color:#0000cc;">.</span>kerberos<span style="color:#0000cc;">.</span>principal<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>name<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>value<span style="color:#0000cc;">&gt;</span>hdfs<span style="color:#0000cc;">/</span>_HOST@YOUR-REALM<span style="color:#0000cc;">.</span>COM<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>value<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>name<span style="color:#0000cc;">&gt;</span>dfs<span style="color:#0000cc;">.</span>secondary<span style="color:#0000cc;">.</span>namenode<span style="color:#0000cc;">.</span>kerberos<span style="color:#0000cc;">.</span>https<span style="color:#0000cc;">.</span>principal<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>name<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>value<span style="color:#0000cc;">&gt;</span>host<span style="color:#0000cc;">/</span>_HOST@YOUR-REALM<span style="color:#0000cc;">.</span>COM<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>value<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;"> </li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span>!-- DataNode security config --<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>name<span style="color:#0000cc;">&gt;</span>dfs<span style="color:#0000cc;">.</span>datanode<span style="color:#0000cc;">.</span>data<span style="color:#0000cc;">.</span>dir<span style="color:#0000cc;">.</span>perm<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>name<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>value<span style="color:#0000cc;">&gt;</span>700<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>value<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>name<span style="color:#0000cc;">&gt;</span>dfs<span style="color:#0000cc;">.</span>datanode<span style="color:#0000cc;">.</span>address<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>name<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>value<span style="color:#0000cc;">&gt;</span>0<span style="color:#0000cc;">.</span>0<span style="color:#0000cc;">.</span>0<span style="color:#0000cc;">.</span>0:1004<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>value<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>name<span style="color:#0000cc;">&gt;</span>dfs<span style="color:#0000cc;">.</span>datanode<span style="color:#0000cc;">.</span>http<span style="color:#0000cc;">.</span>address<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>name<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>value<span style="color:#0000cc;">&gt;</span>0<span style="color:#0000cc;">.</span>0<span style="color:#0000cc;">.</span>0<span style="color:#0000cc;">.</span>0:1006<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>value<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>name<span style="color:#0000cc;">&gt;</span>dfs<span style="color:#0000cc;">.</span>datanode<span style="color:#0000cc;">.</span>keytab<span style="color:#0000cc;">.</span>file<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>name<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>value<span style="color:#0000cc;">&gt;</span><span style="color:#0000cc;"><span>/</span><span style="color:#5c5c5c;">usr/local</span>/</span>hadoop<span style="color:#0000cc;">/</span>conf<span style="color:#0000cc;">/</span>hdfs<span style="color:#0000cc;">.</span>keytab<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>value<span style="color:#0000cc;">&gt;</span> <span style="color:#0000cc;">&lt;</span>!-- path to the HDFS keytab --<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>name<span style="color:#0000cc;">&gt;</span>dfs<span style="color:#0000cc;">.</span>datanode<span style="color:#0000cc;">.</span>kerberos<span style="color:#0000cc;">.</span>principal<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>name<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>value<span style="color:#0000cc;">&gt;</span>hdfs<span style="color:#0000cc;">/</span>_HOST@YOUR-REALM<span style="color:#0000cc;">.</span>COM<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>value<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>name<span style="color:#0000cc;">&gt;</span>dfs<span style="color:#0000cc;">.</span>datanode<span style="color:#0000cc;">.</span>kerberos<span style="color:#0000cc;">.</span>https<span style="color:#0000cc;">.</span>principal<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>name<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>value<span style="color:#0000cc;">&gt;</span>host<span style="color:#0000cc;">/</span>_HOST@YOUR-REALM<span style="color:#0000cc;">.</span>COM<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>value<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>property<span style="color:#0000cc;">&gt;</span>
</li>
</ol></div>
2、启动namenode</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">
<div class="codeText" style="border:1px solid #dddddd;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;"><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;"># <span style="color:#000000;font-family:Simsun;line-height:18px;"><span style="color:#008080;font-size:large;">sudo -u hdfs /usr/local/hadoop/bin/hadoop namenode</span></span>
</li>
</ol></div>
启动后可以看到下面的信息</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">
<br><div class="codeText" style="border:1px solid #dddddd;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;"><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;">10/10/25 17:01:46 INFO security.UserGroupInformation:</li>
<li style="margin-left:0px;">Login successful for user hdfs/fully.qualified.domain.name@YOUR-REALM.COM using keytab file /etc/hadoop/hdfs.keytab</li>
</ol></div>
<div class="codeText" style="border:1px solid #dddddd;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;"><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;">10/10/25 17:01:52 INFO security.UserGroupInformation: Login successful for user host/fully.qualified.domain.name@YOUR-REALM.COM using keytab file /etc/hadoop/hdfs.keytab</li>
<li style="margin-left:0px;">10/10/25 17:01:52 INFO http.HttpServer: Added global filtersafety (class=org.apache.hadoop.http.HttpServer$QuotingInputFilter)</li>
<li style="margin-left:0px;">10/10/25 17:01:57 INFO http.HttpServer: Adding Kerberos filter to getDelegationToken</li>
<li style="margin-left:0px;">10/10/25 17:01:57 INFO http.HttpServer: Adding Kerberos filter to renewDelegationToken</li>
<li style="margin-left:0px;">10/10/25 17:01:57 INFO http.HttpServer: Adding Kerberos filter to cancelDelegationToken</li>
<li style="margin-left:0px;">10/10/25 17:01:57 INFO http.HttpServer: Adding Kerberos filter to fsck</li>
<li style="margin-left:0px;">10/10/25 17:01:57 INFO http.HttpServer: Adding Kerberos filter to getimage</li>
</ol></div>
关于错误：</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">
<div class="codeText" style="border:1px solid #dddddd;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;"><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;">12/06/13 13:24:43 WARN ipc.Server: Auth failed for 127.0.0.1:63202:null</li>
<li style="margin-left:0px;">12/06/13 13:24:43 WARN ipc.Server: Auth failed for 127.0.0.1:63202:null</li>
<li style="margin-left:0px;">12/06/13 13:24:43 INFO ipc.Server: IPC Server listener on 9000: readAndProcess threw exception javax.security.sasl.SaslException: GSS initiate failed [Caused by GS***ception: Failure unspecified at GSS-API level (Mechanism level: Encryption type AES256 CTS mode with HMAC SHA1-96 is not supported/enabled)] from client 127.0.0.1. Count of bytes read: 0</li>
<li style="margin-left:0px;">javax.security.sasl.SaslException: GSS initiate failed [Caused by GS***ception: Failure unspecified at GSS-API level (Mechanism level: Encryption type AES256 CTS mode with HMAC SHA1-96 is not supported/enabled)]</li>
</ol></div>
<div>
<div class="codeText" style="border:1px solid #dddddd;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;"><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;">12/06/13 13:23:21 WARN security.UserGroupInformation: Not attempting to re-login since the last re-login was attempted less than 600 seconds before.</li>
</ol></div>
</div>
这两个错误与前面提到的JCE jar包有关，确保已经下载并替换了相应的jar包。</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"> </div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">如果出现Login failed，应首先使用kinit的方式登陆，如果可以登陆，检查是否使用了正确的JCE jar包。然后就是检查keytab的路径及权限。</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"> </div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">另外，第二个错误，也有可能与SELINUX有关，在所有配置不变的情况下，关闭selinux可以解决问题。但是在/var/log/audit/audit.log里没有看到相关的错误。之后不知何故，开启selinux也不会造成上面的那个问题了。</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"> </div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">3、验证namenode是否正确启动</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">两种方法：</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">（1）访问http://machine:50070</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">（2）</div>
<div class="codeText" style="border:1px solid #dddddd;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;color:#666666;"><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;">#hadoop fs -ls</li>
</ol></div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">注：如果在你的凭据缓存中没有有效的kerberos ticket，执行hadoop fs -ls将会失败。</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">可以使用klist来查看是否有有有效的ticket。</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">可以通过kinit来获取ticket.</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">kinit -k -t /usr/local/hadoop/conf/hdfs.ketab hdfs/nn.hadoop.local@for_hadoop</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">如果没有有效的ticket，将会出现下面的错误：</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">
<div class="codeText" style="border:1px solid #dddddd;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;"><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;">11/01/04 12:08:12 WARN ipc.Client: Exception encountered while connecting to the server : javax.security.sasl.SaslException:</li>
<li style="margin-left:0px;">GSS initiate failed [Caused by GS***ception: No valid credentials provided (Mechanism level: Failed to find any Kerberos tgt)]</li>
<li style="margin-left:0px;">Bad connection to FS. command aborted. exception: Call to nn-host/10.0.0.2:8020 failed on local exception: java.io.IOException:</li>
<li style="margin-left:0px;">javax.security.sasl.SaslException: GSS initiate failed [Caused by GS***ception: No valid credentials provided (Mechanism level: Failed to find any Kerberos tgt)]</li>
</ol></div>
注：如果使用的MIT kerberos 1.8.1或更高版本，ORACLE JDK6 UPDATE 26和更早的版本存在一个bug：</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">即使成功的使用kinit获取了ticket，java仍然无法读取kerberos 票据缓存。</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">解决的办法是在使用kinit获取ticket之后使用kinit -R 来renew ticket。这样，将重写票据缓存中的ticket为java可读的格式。</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">但是，在使用kinit -R 时遇到一个问题，就是无法renew ticket</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">
<br><div class="codeText" style="border:1px solid #dddddd;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;"><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;">kinit: Ticket expired while renewing credentials</li>
</ol></div>
在官方文档中也有描述：<a style="color:#0074c0;font-family:'Lucida Grande CY', 'Lucida Grande', 'Lucida Sans', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:#fafafa;" href="https://ccp.cloudera.com/display/CDHDOC/Appendix+A+-+Troubleshooting#AppendixA-Troubleshooting-Problem2%3AJavaisunabletoreadtheKerberoscredentialscachecreatedbyversionsofMITKerberos1.8.1orhigher." rel="nofollow">Java is unable to read the Kerberos credentials cache created by versions of MIT Kerberos 1.8.1 or higher.</a>
</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">关于是否以获取renew的ticket，取决于KDC的设置。</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">是否是可以获取renew的ticket，可以通过klist来查看：</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"><span style="color:#f00000;"><strong>如果不可以获取renw的ticket<span style="font-family:'Lucida Grande CY', 'Lucida Grande', 'Lucida Sans', Helvetica, Arial, sans-serif;font-size:small;"><span style="line-height:19px;">，”</span></span><span style="font-family:'Lucida Grande CY', 'Lucida Grande', 'Lucida Sans', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:#ffffdd;">valid starting" and "renew until"的值</span><span style="line-height:19px;font-family:'Lucida Grande CY', 'Lucida Grande', 'Lucida Sans', Helvetica, Arial, sans-serif;font-size:small;">是相同的时间。</span></strong></span></div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">我为了获取renw的ticket，做了以下的尝试：</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">&lt;1&gt;、在kdc.conf中添加默认flag</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"><span style="font-family:Consolas, monospace;font-size:12px;line-height:15px;">default_principal_flags = +forwardable,+renewable</span></div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"><span style="font-family:Consolas, monospace;"><span style="font-size:12px;line-height:15px;">但是实际没有起作用，因为查看资料，默认的principal_flags就包含了renewable，所以问题不是出在这里。</span></span></div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">另外需要说明一点，<span style="font-family:Consolas, monospace;font-size:12px;line-height:15px;">default_principal_flags</span><span style="font-family:Consolas, monospace;font-size:12px;line-height:15px;"> 只对这个flags生效以后创建的principal生效，之前创建的不生效，需要使用modprinc来使之前的principal生效。</span>
</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"><span style="font-family:Consolas, monospace;font-size:12px;line-height:15px;"> </span></div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">&lt;2&gt;、在kdc.conf中添加：</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">
<div class="codeText" style="border:1px solid #dddddd;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;"><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;"><span style="color:#f00000;font-size:large;"><strong>max_renewable_life = 10d</strong></span></li>
</ol></div>
</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">重启kdc， 重新kinit -k -t .....，重新执行kinit -R可以正常renw了。</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">再次验证，修改为：</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">
<div class="codeText" style="border:1px solid #dddddd;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;"><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;">max_renewable_life = 0s</li>
</ol></div>
重启kdc，重新kinit -k -t ......，重新执行 kinit -R在此不能renew ticket了。</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">所以，是否可以获取renew的ticket是这样设置的：</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"><span style="color:#f00000;font-size:large;"><strong>默认是可以获取renew的ticket的，但是，可以renw的最长时间是0s，所以造成无法renew，解决的办法是在kdc.conf中增大该参数。</strong></span></div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"> </div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">另外关于krb5.conf中的renew_lifetime = 7d参数，该参数设置该服务器上的使用kinit -R时renew的时间。</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"> </div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">另外，也可以通过modprinc来修改<span style="color:#5c5c5c;font-family:Consolas, monospace;font-size:12px;line-height:15px;">max_renewable_life的值，使用modprinc修改的值比kdc.conf中的配置有更高的优先级，例如，使用modprinc设置了为7天，kdc.conf中设置了为10天，使用getprinc可以看出，实际生效的是7天。</span><span style="color:#5c5c5c;font-family:Consolas, monospace;font-size:12px;line-height:15px;">需要注意的是，即要修改</span><span style="font-family:Consolas, monospace;"><span style="line-height:15px;"><span style="color:#f00000;font-size:large;"><strong>krbtgt/for_hadoop</strong></span><span style="font-size:12px;color:#5c5c5c;">@for_hadoop，也要修改类似于hdfs/dn145.hadoop.local@for_hadoop这样的prinicials，通过klist可以看出来：</span></span></span>
</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">
<br><div class="codeText" style="border:1px solid #dddddd;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;"><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;"># klist</li>
<li style="margin-left:0px;">Ticket cache: FILE:/tmp/krb5cc_0</li>
<li style="margin-left:0px;">Default principal: <span style="color:#f00000;">hdfs/dn145.hadoop.local@for_hadoop</span>
</li>
<li style="margin-left:0px;">Valid starting Expires Service principal</li>
<li style="margin-left:0px;">06/14/12 17:15:05 06/15/12 17:15:05 <span style="color:#f00000;">krbtgt/for_hadoop@for_hadoop</span>
</li>
<li style="margin-left:0px;">renew until 06/21/12 17:15:04</li>
<li style="margin-left:0px;">Kerberos 4 ticket cache: /tmp/tkt0</li>
<li style="margin-left:0px;">klist: You have no tickets cached</li>
</ol></div>
如何使用modprinc来修改<span style="color:#5c5c5c;font-family:Consolas, monospace;font-size:12px;line-height:15px;">max_renewable_life</span>
</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">
<div class="codeText" style="border:1px solid #dddddd;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;"><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;">#kadmin.local</li>
<li style="margin-left:0px;">modprinc -maxrenewlife 7days krbtgt/for_hadoop@for_hadoop</li>
<li style="margin-left:0px;">getprinc krbtgt/for_hadoop@for_hadoop</li>
<li style="margin-left:0px;">Principal: krbtgt/for_hadoop@for_hadoop</li>
<li style="margin-left:0px;">Expiration date: [never]</li>
<li style="margin-left:0px;">Last password change: [never]</li>
<li style="margin-left:0px;">Password expiration date: [none]</li>
<li style="margin-left:0px;">Maximum ticket life: 1 day 00:00:00</li>
<li style="margin-left:0px;"><span style="color:#f00000;">Maximum renewable life: 7 days 00:00:00</span></li>
<li style="margin-left:0px;">Last modified: Thu Jun 14 11:25:15 CST 2012 (hdfs/admin@for_hadoop)</li>
<li style="margin-left:0px;">Last successful authentication: [never]</li>
<li style="margin-left:0px;">Last failed authentication: [never]</li>
<li style="margin-left:0px;">Failed password attempts: 0</li>
<li style="margin-left:0px;">Number of keys: 7</li>
<li style="margin-left:0px;">Key: vno 1, aes256-cts-hmac-sha1-96, no salt</li>
<li style="margin-left:0px;">Key: vno 1, aes128-cts-hmac-sha1-96, no salt</li>
<li style="margin-left:0px;">Key: vno 1, des3-cbc-sha1, no salt</li>
<li style="margin-left:0px;">Key: vno 1, arcfour-hmac, no salt</li>
<li style="margin-left:0px;">Key: vno 1, des-hmac-sha1, no salt</li>
<li style="margin-left:0px;">Key: vno 1, des-cbc-md5, no salt</li>
<li style="margin-left:0px;">Key: vno 1, des-cbc-crc, no salt</li>
</ol></div>
到这里，kinit -R的问题解决，可以成功的执行hadoop fs -ls了。</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"> </div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">4、启动datanode</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">
<span style="color:#f00000;">正确的启动方法</span>应该是使用<span style="color:#f00000;font-size:large;"><strong>root</strong></span>账号</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">
<div class="codeText" style="border:1px solid #dddddd;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;"><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;"><span style="font-size:large;"><span style="color:#008080;">HADOOP_DATANODE_USER=</span><span style="color:#f00000;">hdfs</span><span style="color:#008080;"> sudo -E /usr/local/hadoop/bin/hadoop datanode</span></span></li>
</ol></div>
如果使用其他用户，直接执行hadoop datanode，则会报错：</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">
<div class="codeText" style="border:1px solid #dddddd;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;"><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;">11/03/21 12:46:57 ERROR datanode.DataNode: java.lang.RuntimeException: Cannot start secure cluster without privileged resources. In a secure cluster, the DataNode must</li>
<li style="margin-left:0px;">be started from within jsvc. If using Cloudera packages, please install the hadoop-0.20-sbin package.</li>
<li style="margin-left:0px;">For development purposes ONLY you may override this check by setting dfs.datanode.require.secure.ports to false. *** THIS WILL OPEN A SECURITY HOLE AND MUST NOT BE</li>
<li style="margin-left:0px;">USED FOR A REAL CLUSTER ***.</li>
<li style="margin-left:0px;">at org.apache.hadoop.hdfs.server.datanode.DataNode.startDataNode(DataNode.java:306)</li>
<li style="margin-left:0px;">at org.apache.hadoop.hdfs.server.datanode.DataNode.&lt;init&gt;(DataNode.java:280)</li>
<li style="margin-left:0px;">at org.apache.hadoop.hdfs.server.datanode.DataNode.makeInstance(DataNode.java:1533)</li>
<li style="margin-left:0px;">at org.apache.hadoop.hdfs.server.datanode.DataNode.instantiateDataNode(DataNode.java:1473)</li>
<li style="margin-left:0px;">at org.apache.hadoop.hdfs.server.datanode.DataNode.createDataNode(DataNode.java:1491)</li>
<li style="margin-left:0px;">at org.apache.hadoop.hdfs.server.datanode.DataNode.secureMain(DataNode.java:1616)</li>
<li style="margin-left:0px;">at org.apache.hadoop.hdfs.server.datanode.DataNode.main(DataNode.java:1626)</li>
</ol></div>
官方文档中提到了这个问题：</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"><a style="color:#0074c0;font-family:'Lucida Grande CY', 'Lucida Grande', 'Lucida Sans', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:#fafafa;" href="https://ccp.cloudera.com/display/CDHDOC/Appendix+A+-+Troubleshooting#AppendixA-Troubleshooting-Problem5%3ACannotstartsecureclusterwithoutprivilegedresources." rel="nofollow">Cannot start secure cluster without privileged resources.</a></div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">官方的解释是和jsvc有关，确实，与jsvc有关.</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">（1）、有可能没有安装hadoop-sbin。</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"> （2）、<span style="color:#f00000;"><strong>确保jsv对于<span style="font-family:Consolas, monospace;font-size:12px;line-height:15px;">HADOOP_DATANODE_USER=</span><span style="font-family:Consolas, monospace;font-size:12px;line-height:15px;">hdfs有可执行的权限。</span></strong></span>
</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">（3）、通过查看hadoop这个启动脚本，可以看到这样的代码：</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">
<div class="codeText" style="border:1px solid #dddddd;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;"><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;">if [ "<span style="color:#f00000;">$EUID</span>" = "0" ] ; then</li>
<li style="margin-left:0px;">if [ "$COMMAND" == "datanode" ] &amp;&amp; [ -x "$_JSVC_PATH" ]; then</li>
<li style="margin-left:0px;">_HADOOP_RUN_MODE="jsvc"</li>
<li style="margin-left:0px;">elif [ -x /bin/su ]; then</li>
<li style="margin-left:0px;">_HADOOP_RUN_MODE="su"</li>
<li style="margin-left:0px;">else</li>
</ol></div>
</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">检查执行hadoop命令的用户的EUID是否为0，即root，只有root用户才去执行jsvc相关的命令。</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">
<span style="color:#444444;font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;">关于EUID：linux系统中每个进程都有2个ID，分别为用户ID（uid）和有效用户ID（euid），UID一般表示进程的创建者（属于哪个用户创建），而EUID表示进程对于文件和资源的访问权限（具备等同于哪个用户的权限）。</span><span style="color:#444444;font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;">一般情况下2个ID是相同的。</span>
</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"><span style="color:#444444;font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;"> </span></div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">
<span style="color:#444444;font-family:Tahoma, 'Microsoft Yahei', Simsun;"><span style="line-height:21px;">5、</span></span> Set the Sticky Bit on HDFS Directories.</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">可以针对hdfs上的目录设置sticky bit，用于防止除superuser，owner以外的用户删除文件夹中的文件。对一个文件设置sticky bit是无效的。</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"> </div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"><strong><span style="color:#008080;font-size:large;">八、Start up the Secondary NameNode</span></strong></div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">跳过</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"> </div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"><span style="color:#008080;"><strong>九、Configure Secure MapReduce</strong></span></div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">在mapred-site.xml中添加</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">
<br><div class="codeText" style="border:1px solid #dddddd;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;"><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span>!-- JobTracker security configs --<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>name<span style="color:#0000cc;">&gt;</span>mapreduce<span style="color:#0000cc;">.</span>jobtracker<span style="color:#0000cc;">.</span>kerberos<span style="color:#0000cc;">.</span>principal<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>name<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>value<span style="color:#0000cc;">&gt;</span>mapred<span style="color:#0000cc;">/</span>_HOST@YOUR-REALM<span style="color:#0000cc;">.</span>COM<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>value<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>name<span style="color:#0000cc;">&gt;</span>mapreduce<span style="color:#0000cc;">.</span>jobtracker<span style="color:#0000cc;">.</span>kerberos<span style="color:#0000cc;">.</span>https<span style="color:#0000cc;">.</span>principal<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>name<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>value<span style="color:#0000cc;">&gt;</span>host<span style="color:#0000cc;">/</span>_HOST@YOUR-REALM<span style="color:#0000cc;">.</span>COM<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>value<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>name<span style="color:#0000cc;">&gt;</span>mapreduce<span style="color:#0000cc;">.</span>jobtracker<span style="color:#0000cc;">.</span>keytab<span style="color:#0000cc;">.</span>file<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>name<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>value<span style="color:#0000cc;">&gt;</span><span style="color:#0000cc;">/</span>usr/local<span style="color:#0000cc;">/</span>hadoop<span style="color:#0000cc;">/</span>conf<span style="color:#0000cc;">/</span>mapred<span style="color:#0000cc;">.</span>keytab<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>value<span style="color:#0000cc;">&gt;</span> <span style="color:#0000cc;">&lt;</span>!-- path to the MapReduce keytab --<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;"> </li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span>!-- TaskTracker security configs --<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>name<span style="color:#0000cc;">&gt;</span>mapreduce<span style="color:#0000cc;">.</span>tasktracker<span style="color:#0000cc;">.</span>kerberos<span style="color:#0000cc;">.</span>principal<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>name<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>value<span style="color:#0000cc;">&gt;</span>mapred<span style="color:#0000cc;">/</span>_HOST@YOUR-REALM<span style="color:#0000cc;">.</span>COM<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>value<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>name<span style="color:#0000cc;">&gt;</span>mapreduce<span style="color:#0000cc;">.</span>tasktracker<span style="color:#0000cc;">.</span>kerberos<span style="color:#0000cc;">.</span>https<span style="color:#0000cc;">.</span>principal<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>name<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>value<span style="color:#0000cc;">&gt;</span>host<span style="color:#0000cc;">/</span>_HOST@YOUR-REALM<span style="color:#0000cc;">.</span>COM<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>value<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>name<span style="color:#0000cc;">&gt;</span>mapreduce<span style="color:#0000cc;">.</span>tasktracker<span style="color:#0000cc;">.</span>keytab<span style="color:#0000cc;">.</span>file<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>name<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>value<span style="color:#0000cc;">&gt;</span><span style="color:#0000cc;">/</span>usr/local<span style="color:#0000cc;">/</span>hadoop<span style="color:#0000cc;">/</span>conf<span style="color:#0000cc;">/</span>mapred<span style="color:#0000cc;">.</span>keytab<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>value<span style="color:#0000cc;">&gt;</span> <span style="color:#0000cc;">&lt;</span>!-- path to the MapReduce keytab --<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;"> </li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span>!-- TaskController settings --<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>name<span style="color:#0000cc;">&gt;</span>mapred<span style="color:#0000cc;">.</span>task<span style="color:#0000cc;">.</span>tracker<span style="color:#0000cc;">.</span>task-controller<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>name<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>value<span style="color:#0000cc;">&gt;</span>org<span style="color:#0000cc;">.</span>apache<span style="color:#0000cc;">.</span>hadoop<span style="color:#0000cc;">.</span>mapred<span style="color:#0000cc;">.</span>LinuxTaskController<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>value<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span>property<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>name<span style="color:#0000cc;">&gt;</span>mapreduce<span style="color:#0000cc;">.</span>tasktracker<span style="color:#0000cc;">.</span>group<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>name<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">  <span style="color:#0000cc;">&lt;</span>value<span style="color:#0000cc;">&gt;</span>mapred<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>value<span style="color:#0000cc;">&gt;</span>
</li>
<li style="margin-left:0px;">
<span style="color:#0000cc;">&lt;</span><span style="color:#0000cc;">/</span>property<span style="color:#0000cc;">&gt;</span>
</li>
</ol></div>
<div> </div>
<div>创建一个taskcontroller.cfg文件，路径为</div>
<div>&lt;path of task-controller binary&gt;/../../conf/taskcontroller.cfg</div>
<div>即/usr/local/hadoop/sbin/Linux-amd64-64/../../conf/taskcontroller.cfg</div>
<div>即conf目录，和site文件相同的目录</div>
<div>
<div class="codeText" style="border:1px solid #dddddd;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;"><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;">mapred.local.dir=<span style="color:#f00000;font-size:large;">/hadoop_data/tmp/mapred/local</span>
</li>
<li style="margin-left:0px;">hadoop.log.dir=/usr/local/hadoop/logs</li>
<li style="margin-left:0px;">mapreduce.tasktracker.group=hadoop</li>
<li style="margin-left:0px;">banned.users=hadoop,hdfs,bin</li>
<li style="margin-left:0px;">min.user.id=500</li>
</ol></div>
其中：</div>
</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">mapred.local.dir需要和mapred-site.xml中指定的相同，否则<a style="color:#0074c0;font-family:'Lucida Grande CY', 'Lucida Grande', 'Lucida Sans', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:#fafafa;" href="https://ccp.cloudera.com/display/CDHDOC/Appendix+A+-+Troubleshooting#AppendixA-Troubleshooting-mapreddirectoryerror" rel="nofollow">this error message</a><span style="color:#505050;font-family:'Lucida Grande CY', 'Lucida Grande', 'Lucida Sans', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:#fafafa;"> </span>
</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">hadoop.log.dir要和hadoop所使用的目录相同，可以在core-site.xml中指定，不同的话会报错：<a style="color:#0074c0;font-family:'Lucida Grande CY', 'Lucida Grande', 'Lucida Sans', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:#fafafa;" href="https://ccp.cloudera.com/display/CDHDOC/Appendix+A+-+Troubleshooting#AppendixA-Troubleshooting-hadooplogsdirectoryerror" rel="nofollow">this error message</a>
</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">另外mapred.local.dir的属主为mapred用户:</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">
<div class="codeText" style="border:1px solid #dddddd;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;"><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;">chown -R mapred.mapred  <span style="color:#f00000;line-height:23px;font-size:large;">/hadoop_data/tmp/mapred/local</span>
</li>
</ol></div>
<div>
<strong style="color:#505050;font-family:'Lucida Grande CY', 'Lucida Grande', 'Lucida Sans', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:#ffffdd;">Note</strong><br style="color:#505050;font-family:'Lucida Grande CY', 'Lucida Grande', 'Lucida Sans', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:#ffffdd;"><span style="color:#505050;font-family:'Lucida Grande CY', 'Lucida Grande', 'Lucida Sans', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:#ffffdd;">In the </span>taskcontroller.cfg<span style="color:#505050;font-family:'Lucida Grande CY', 'Lucida Grande', 'Lucida Sans', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:#ffffdd;"> file, the default setting for the </span>banned.users<span style="color:#505050;font-family:'Lucida Grande CY', 'Lucida Grande', 'Lucida Sans', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:#ffffdd;"> property is </span>mapred<span style="color:#505050;font-family:'Lucida Grande CY', 'Lucida Grande', 'Lucida Sans', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:#ffffdd;">, </span>hdfs<span style="color:#505050;font-family:'Lucida Grande CY', 'Lucida Grande', 'Lucida Sans', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:#ffffdd;">, and </span>bin<span style="color:#505050;font-family:'Lucida Grande CY', 'Lucida Grande', 'Lucida Sans', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:#ffffdd;"> to prevent jobs from being submitted via those user accounts. The default setting for the</span>min.user.id<span style="color:#505050;font-family:'Lucida Grande CY', 'Lucida Grande', 'Lucida Sans', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:#ffffdd;"> property is </span>1000<span style="color:#505050;font-family:'Lucida Grande CY', 'Lucida Grande', 'Lucida Sans', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:#ffffdd;"> to prevent jobs from being submitted with a user ID less than 1000, which are conventionally Unix super users. Note that some operating systems such as CentOS 5 use a default value of 500 and above for user IDs, not 1000. If this is the case on your system, change the default setting for the </span>min.user.id<span style="color:#505050;font-family:'Lucida Grande CY', 'Lucida Grande', 'Lucida Sans', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:#ffffdd;"> property to </span>500<span style="color:#505050;font-family:'Lucida Grande CY', 'Lucida Grande', 'Lucida Sans', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:#ffffdd;">. If there are user accounts on your cluster that have a user ID less than the value specified for the </span>min.user.id<span style="color:#505050;font-family:'Lucida Grande CY', 'Lucida Grande', 'Lucida Sans', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:#ffffdd;"> property, the TaskTracker returns an error code of 255.</span>
</div>
<div> </div>
修改task-controller文件的权限：</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">
<a style="color:#0074c0;font-family:'Lucida Grande CY', 'Lucida Grande', 'Lucida Sans', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:#fafafa;" title="Appendix B - More Information about the hadoop-0.20-sbin Binary Programs" href="https://ccp.cloudera.com/display/CDHDOC/Appendix+B+-+More+Information+about+the+hadoop-0.20-sbin+Binary+Programs" rel="nofollow">More Information about the hadoop-0.20-sbin Binary Programs</a><span style="color:#505050;font-family:'Lucida Grande CY', 'Lucida Grande', 'Lucida Sans', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:#fafafa;">.</span>
</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">
<br><div class="codeText" style="border:1px solid #dddddd;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;"><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;">chown root:mapred /usr/local/hadoop/sbin/Linux-amd64-64/task-controller</li>
<li style="margin-left:0px;">chmod 4754 /usr/local/hadoop/sbin/Linux-amd64-64/task-controller</li>
</ol></div>
</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"> </div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"><span style="color:#008080;"><strong>启动JOBTRACKER</strong></span></div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">
<div class="codeText" style="border:1px solid #dddddd;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;"><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;"><span style="color:#008080;font-size:large;">sudo -u mapred /usr/local/hadoop/bin/hadoop jobtracker</span></li>
</ol></div>
错误：</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">
<div>
<div class="codeText" style="border:1px solid #dddddd;width:645px;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;"><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;">FATAL mapred.JobTracker: org.apache.hadoop.security.AccessControlException: The systemdir hdfs://nn.hadoop.local:9000/hadoop_data/tmp/mapred/system is not owned by mapred</li>
</ol></div>
</div>
</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">修改hdfs上对应目录的属性</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">
<div>
<div class="codeText" style="border:1px solid #dddddd;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;"><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;">hadoop fs -chown -R mapred /hadoop_data/tmp/mapred</li>
</ol></div>
</div>
<div>注意，是mapred而不是mapred.mapred，否则会变成 mapred.mapred supergroup          0 2012-06-08 11:41 /hadoop_data/tmp/mapred/system</div>
</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"> </div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">重新启动JobTracker。</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"> </div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">到这里JobTracker启动完成，最后一步，<span style="color:#008080;font-size:large;">启动TaskTracker</span>
</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">修改taskcontroller.cfg文件属性，启动tasktracker时会检查（jobtracker不需要？待验证）</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">
<div class="codeText" style="border:1px solid #dddddd;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;"><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;">chown root.mapred taskcontroller.cfg</li>
<li style="margin-left:0px;">chmod 600 taskcontroller.cfg</li>
</ol></div>
同样的，也需要修改task-controler的属性</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">
<div class="codeText" style="border:1px solid #dddddd;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;"><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;">chown root:mapred  <span>/usr/local/hadoop/sbin/Linux-amd64-64/</span>task-controller</li>
<li style="margin-left:0px;">chmod 4754 /usr/local/hadoop/sbin/Linux-amd64-64/task-controller</li>
</ol></div>
启动<br><div class="codeText" style="border:1px solid #dddddd;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;"><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;"><span style="color:#008080;font-size:large;">sudo -u mapred /usr/local/hadoop/bin/hadoop tasktracker</span></li>
</ol></div>
错误：<br><div class="codeText" style="border:1px solid #dddddd;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;"><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;">ERROR mapred.TaskTracker: Can not start task tracker because java.io.IOException: Login failure for mapred/srv143.madeforchina.co@for_hadoop from keytab /usr/local/hadoop/mapred.keytab</li>
</ol></div>
使用kinit可以登陆？确保key对于mapred用户可读。</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"> </div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">另外，可以还需要修改log目录的权限</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">
<div class="codeText" style="border:1px solid #dddddd;letter-spacing:.1px;font-size:12px;line-height:normal;font-family:Consolas, monospace;"><ol class="dp-css" style="color:#5c5c5c;line-height:1.3;" start="1"><li style="margin-left:0px;">chown -R mapred.hadoop /usr/local/hadoop/logs/</li>
</ol></div>
</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">到这里，hadoop + kerberos基本完后。</div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;"> </div>
<div style="color:#666666;font-family:'宋体', Arial;font-size:16px;line-height:26px;">后面需要做的工作包括修改启动hadoop的脚本，部署kerberos slave服务器。</div>
<p style="font-size:14px;"> </p>
<p style="font-size:14px;"> </p>
</div>            </div>
                </div>