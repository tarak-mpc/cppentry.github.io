---
layout:     post
title:      HBase学习（一）hbase安装(单机模式)和javaapi客户端访问hbase例子
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>平时习惯了window下开发，而Hbase只能在linux环境下运行，所以决定把hbase安装一台虚拟机上，在windows下运行eclipse连接虚拟机里的hbase</p>
<p>因为对linux命令了解不多，所以很多时候都是编码baidu边安装的。</p>
<p><span style="font-size:24px;"><strong>安装Hbase单机模式</strong></span></p>
<p>（虚拟机安装的是CentOS）<br></p>
<p>1、下载Hbase版本</p>
<p>在http://apache.claz.org/hbase/选择一个需要下载的hbase版本，我选择的是hbase-0.94.24</p>
<p>建立一个hbase目录，并下载hbase.</p>
<pre><code class="language-html">[xxxx@localhost ~]$ mkdir hbase
[xxxx@localhost ~]$ cd hbase
[xxxx@localhost hbase]$ wget http://apache.claz.org/hbase/hbase-0.94.24/hbase-0.94.24.tar.gz
</code></pre><br><p></p>
<p>2、解压tar.gz文件，得到如下结果</p>
<p></p>
<pre><code class="language-html">[xxxx@localhost hbase]$ ll
总计 57900
drwxr-xr-x 10 daidai daidai     4096 09-30 08:07 hbase-0.94.24
-rw-rw-r--  1 daidai daidai 59210066 10-30 08:08 hbase-0.94.24.tar.gz</code></pre><br><p>3、启动Hbase</p>
<p></p><pre><code class="language-html">[xxxx@localhost hbase]$ ./hbase-0.94.24/bin/start-hbase.sh 
starting master, logging to /home/daidai/hbase/hbase-0.94.24/bin/../logs/hbase-daidai-master-localhost.localdomain.out
[xxxx@localhost hbase]$ </code></pre><br>
4、启动Hbase Shell 操作Hbase<br><p></p><pre><code class="language-html">[xxxx@localhost hbase]$ ./hbase-0.94.24/bin/hbase shell
HBase Shell; enter 'help&lt;RETURN&gt;' for list of supported commands.
Type "exit&lt;RETURN&gt;" to leave the HBase Shell
Version 0.94.24, r68ed793a1d89226f8fb1ee1ee618c1e7779d46cd, Mon Sep 29 23:09:31 UTC 2014

hbase(main):001:0&gt; list
TABLE                                                                                                                               
mxb                                                                                                                                 
mytable                                                                                                                             
2 row(s) in 0.5220 seconds

hbase(main):002:0&gt; </code></pre>
<p>数据库中已经存在表mxb和mytable,通过Hbase Shell删除表mxb</p>
<p></p><pre><code class="language-html">hbase(main):007:0&gt; disable 'mxb'
0 row(s) in 1.3730 seconds

hbase(main):008:0&gt; drop 'mxb'
0 row(s) in 0.0950 seconds

hbase(main):009:0&gt; list
TABLE                                                                                                                               
mytable                                                                                                                             
1 row(s) in 0.0320 seconds

hbase(main):010:0&gt; </code></pre><br>
5、可以通过网页查询HBase动行状态
<p>http://ip:60010  <br></p>
<p>如果网页无法打开，查看linuex防火墙是否关闭</p>
<p></p><pre><code class="language-html">[xxxx@localhost hbase]$ su -
口令：
[root@localhost ~]# service iptables stop
清除防火墙规则：[确定]
把 chains 设置为 ACCEPT 策略：filter [确定]
正在卸载 Iiptables 模块：[确定]
[root@localhost ~]# </code></pre><br><img src="https://img-blog.csdn.net/20141216194422489?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMzQ4NTUzMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><p><br></p>
<p><span style="font-size:24px;">使用Hbase JavaAPI 访问HBase</span><br><br></p>
<p>1）新建一个eclipse工程，并导入Hbase中的jar包。</p>
<p>需要导入的jar包：hbase-0.94.24.jar</p>
<p>hbase-0.94.24\lib下的所有jar包</p>
<p>2）创建表的代码如下</p>
<p></p><pre><code class="language-html">public static void main(String[] args) {
		String createTableName = "mytable2";
		Configuration configuration = HBaseConfiguration.create();;
		configuration.set("hbase.zookeeper.quorum", "10.10.2.66");
		//configuration.set("hbase.master", "10.10.2.66:600000");
		System.out.println("start create table ......");
		try {
			HBaseAdmin hBaseAdmin = new HBaseAdmin(configuration);
			HTableDescriptor tableDescriptor = new HTableDescriptor(createTableName);
			tableDescriptor.addFamily(new HColumnDescriptor("column1"));
			tableDescriptor.addFamily(new HColumnDescriptor("column2"));
			tableDescriptor.addFamily(new HColumnDescriptor("column3"));
			hBaseAdmin.createTable(tableDescriptor);
			hBaseAdmin.close();
		} catch (MasterNotRunningException e) {
			e.printStackTrace();
		} catch (ZooKeeperConnectionException e) {
			e.printStackTrace();
		} catch (IOException e) {
			e.printStackTrace();
		}
		System.out.println("end create table ......");
	}</code></pre><br>
如果出现下面错误
<p></p><pre><code class="language-html">14/12/16 19:52:22 INFO zookeeper.ClientCnxn: Session establishment complete on server localhost.localdomain/10.10.2.66:2181, sessionid = 0x14a54972f20000f, negotiated timeout = 40000
14/12/16 19:52:23 INFO client.HConnectionManager$HConnectionImplementation: getMaster attempt 0 of 14 failed; retrying after sleep of 1001
java.net.ConnectException: Connection refused: no further information</code></pre><br>
需要修改linux的host地址，去掉原127.0.0.1,重启Hbase<br><p></p><pre><code class="language-html">10.10.2.66      localhost.localdomain localhost
#127.0.0.1      localhost.localdomain localhost
</code></pre><br>
修改本地host地址
<p></p><pre><code class="language-html">10.10.2.66	localhost.localdomain</code></pre><br>
3)通过Hbase shell 验证表是否创建成功
<p></p><pre><code class="language-html">[xxxx@localhost ~]$ ./hbase/hbase-0.94.24/bin/hbase shell
HBase Shell; enter 'help&lt;RETURN&gt;' for list of supported commands.
Type "exit&lt;RETURN&gt;" to leave the HBase Shell
Version 0.94.24, r68ed793a1d89226f8fb1ee1ee618c1e7779d46cd, Mon Sep 29 23:09:31 UTC 2014

hbase(main):001:0&gt; list
TABLE                                                                                                                               
mytable                                                                                                                             
mytable2                                                                                                                            
2 row(s) in 0.4340 seconds

hbase(main):002:0&gt; </code></pre><br><br><p></p>
            </div>
                </div>