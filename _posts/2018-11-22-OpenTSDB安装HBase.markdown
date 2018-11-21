---
layout:     post
title:      OpenTSDB安装HBase
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <div id="sina_keyword_ad_area2" class="articalContent">
			<p><br></p>
<h2>Setup Hbase</h2>
<p><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">安装</span></font></font>HBase</p>
<p>In order to use OpenTSDB, you need to have <a href="http://hbase.org/" rel="nofollow">HBase</a> up and running. This page will help
you get started with a simple, single-node HBase setup, which is
good enough to <span style="background:#ffff00;">evaluate
OpenTSDB</span> or monitor small installations. If you need
scalability and reliability, you will need to setup a full HBase
cluster.</p>
<p><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">要使用</span></font></font>OpenTSDB<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">，你需要去让</span></font></font>HBase<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">跑起来。这个页面将帮助你以一种简单的，单结点的方式安装</span></font></font>HBase<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">，这是足够好的去支撑</span></font></font>OpenTSDB<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">或监控小装置。如果你需要可扩展性和可靠性，你将需要去安装一个完整的</span></font></font>HBase<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">群。</span></font></font></p>
<p>You can copy-paste all the following instructions directly into
a terminal.</p>
<p><a name="singlenode"></a><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">以下所有指令均可直接复制粘贴到终端下。</span></font></font></p>
<h3>Setup a single-node HBase instance</h3>
<p><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">安装一个单结点的</span></font></font>HBase<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">例子</span></font></font></p>
<p>If you already have an HBase cluster, <a href="http://opentsdb.net/getting-started.html" rel="nofollow">skip this step</a>. If
you're gonna be using less than 5-10 nodes, stick to a single node.
Deploying HBase on a single node is easy and can help get you
started with OpenTSDB quickly. You can always scale to a real
cluster and migrate your data later.</p>
<p><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">如果你想直接安装一个</span></font></font>HBase<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">群，跳过这一步。如果你要使用低于</span></font></font>5<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">至</span></font></font>10<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">个节点，那么坚持使用单结点。在单结点上部署</span></font></font>HBase<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">很简单，并且能帮你快速开启</span></font></font>OpenTSDB<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">。你总能扩展到一个真正的集群，然后迁移你的数据。</span></font></font></p>
<p style="margin-left:1.24cm;">wget
http://www.apache.org/dist/hbase/hbase-0.92.1/hbase-0.92.1.tar.gz</p>
<p style="margin-left:1.24cm;">tar xfz
hbase-0.92.1.tar.gz</p>
<p style="margin-left:1.24cm;">cd
hbase-0.92.1</p>
<p>At this point, you are ready to start
HBase (without HDFS) on a single node. But before starting it, I
recommend using the following configuration:</p>
<p><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">此时，你将准备在单个结点上开启</span></font></font>HBase(<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">没有</span></font></font>HDFS) <font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">。不过在开始之前，我推荐先用以下配置：</span></font></font></p>
<p style="margin-left:1.24cm;">
hbase_rootdir=${TMPDIR-'/tmp'}/tsdhbase</p>
<p style="margin-left:1.24cm;">iface=lo`uname |
sed -n s/Darwin/0/p`</p>
<p style="margin-left:1.24cm;">cat
&gt;conf/hbase-site.xml &lt;&lt;EOF</p>
<p style="margin-left:1.24cm;"> </p>
<p style="margin-left:1.24cm;">
</p>
<p style="margin-left:1.24cm;"> </p>
<p style="margin-left:1.24cm;"> </p>
<p style="margin-left:1.24cm;">
hbase.rootdir</p>
<p style="margin-left:1.24cm;">
file:///$hbase_rootdir/hbase-\${user.name}/hbase</p>
<p style="margin-left:1.24cm;"> </p>
<p style="margin-left:1.24cm;"> </p>
<p style="margin-left:1.24cm;">
hbase.zookeeper.dns.interface</p>
<p style="margin-left:1.24cm;">$iface</p>
<p style="margin-left:1.24cm;"> </p>
<p style="margin-left:1.24cm;"> </p>
<p style="margin-left:1.24cm;">
hbase.regionserver.dns.interface</p>
<p style="margin-left:1.24cm;">$iface</p>
<p style="margin-left:1.24cm;"> </p>
<p style="margin-left:1.24cm;"> </p>
<p style="margin-left:1.24cm;">
hbase.master.dns.interface</p>
<p style="margin-left:1.24cm;">$iface</p>
<p style="margin-left:1.24cm;"> </p>
<p style="margin-left:1.24cm;"> </p>
<p style="margin-left:1.24cm;">EOF</p>
<p>Make sure to adjust the value of <code>hbase_rootdir</code> if you want HBase to store its data
in somewhere more durable than a temporary directory. The default
is to use <code>/tmp</code>, which means you'll
lose all your data whenever your server reboots. The remaining
settings are less important and simply force HBase to<span style="background:#ffff00;">stick to the loopback interface</span>
(<code>lo0</code> on Mac OS X, or just <code>lo</code> on Linux), which simplifies things when you're
just testing HBase on a single node.</p>
<p><a name="result_box"></a><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">如果你希望</span></font></font><span lang="zh-cn" xml:lang="zh-cn">HBase</span><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">将其数据存储在一个比临时目录更耐用的地方，那么认真设置</span></font></font><span lang="zh-cn" xml:lang="zh-cn">hbase_rootdir</span><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">的值。默认使用</span></font></font>/tmp<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">，意味着无论何时你重启你的系统，你将丢失你所有的数据。随后的设置不是很重要，只是简单的使</span></font></font>HBase<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">持续监控端口（</span></font></font> Mac OS X<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">是</span></font></font>lo0<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">，</span></font></font>Linux<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">是</span></font></font>lo)<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">，当你只是想在单个节点上测试</span></font></font>HBase<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">时简化了操作。</span></font></font></p>
<p><br><br></p>
<p>Now start HBase:</p>
<p><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">现在开启</span></font></font>HBase:</p>
<p style="margin-left:1.36cm;">./bin/start-hbase.sh</p>
<h3>Using LZO</h3>
<p><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">使用</span></font></font>LZO</p>
<p>There is no reason to not use LZO with HBase. Except in rare
cases, the CPU cycles spent on doing LZO compression /
decompression pay for themselves by saving you time wasted doing
more I/O. This is certainly true for OpenTSDB where LZO can easily
compress OpenTSDB's binary data by 3 to 4x. Installing LZO is
simple and is done as follows.</p>
<p><a name="result_box1"></a><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">使用</span></font></font>HBase<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">，没有理由不使用</span></font></font><span lang="zh-cn" xml:lang="zh-cn">LZO</span><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">。除了极少数情况下，</span></font></font><span lang="zh-cn" xml:lang="zh-cn">CPU</span><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">周期为它们自己花时间做</span></font></font><span lang="zh-cn" xml:lang="zh-cn">LZO</span><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">压缩</span></font></font><span lang="zh-cn" xml:lang="zh-cn">/</span><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">解压缩，以节省您浪费在做更多的</span></font></font><span lang="zh-cn" xml:lang="zh-cn">I / O</span><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">的时间。对于</span></font></font><span lang="zh-cn" xml:lang="zh-cn">OpenTSDB</span><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">，可以肯定的是</span></font></font><span lang="zh-cn" xml:lang="zh-cn">LZO</span><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">可以很容易地压缩</span></font></font><span lang="zh-cn" xml:lang="zh-cn">OpenTSDB</span><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">的二进制数据</span></font></font><span lang="zh-cn" xml:lang="zh-cn">3</span><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">至</span></font></font><span lang="zh-cn" xml:lang="zh-cn">4</span><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">倍。安装</span></font></font><span lang="zh-cn" xml:lang="zh-cn">LZO</span><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">是简单的，如下：</span></font></font></p>
<h4>Pre-requisites</h4>
<p><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">先决条件</span></font></font></p>
<p>In order to build <code>hadoop-lzo</code>, you need to have Ant installed as well
as liblzo2 with development headers:</p>
<p><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">要连接</span></font></font>hadoop-lzo<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">，你需要安装</span></font></font>Ant<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">和</span></font></font>libzo2<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">的开发头文件：</span></font></font></p>
<p style="margin-left:1.22cm;">apt-get install ant liblzo2-dev #
Debian/Ubuntu</p>
<p style="margin-left:1.22cm;">yum install ant ant-nodeps
lzo-devel.x86_64 # RedHat/CentOS/Fedora</p>
<p style="margin-left:1.22cm;">brew install lzo # Mac OS X</p>
<h4>Compile &amp; Deploy</h4>
<p><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">编译和配置</span></font></font></p>
<p>Thanks to our friends at Cloudera for
maintaining the Hadoop-LZO package:</p>
<p><br></p>
<p style="margin-left:1.24cm;">git clone
git://github.com/cloudera/hadoop-lzo.git</p>
<p style="margin-left:1.24cm;">cd
hadoop-lzo</p>
<p style="margin-left:1.24cm;">
CLASSPATH=<i>path/to</i>/hadoop-core-1.0.3.jar CFLAGS=-m64
CXXFLAGS=-m64 ant compile-native tar</p>
<p style="margin-left:1.24cm;">
hbasedir=<i>path/to/hbase</i></p>
<p style="margin-left:1.24cm;">mkdir -p
$hbasedir/lib/native</p>
<p style="margin-left:1.24cm;">cp
build/hadoop-lzo-0.4.14/hadoop-lzo-0.4.14.jar $hbasedir/lib</p>
<p style="margin-left:1.24cm;">cp -a
build/hadoop-lzo-0.4.14/lib/native/* $hbasedir/lib/native</p>
<p>Restart HBase and make sure you create your tables with
<code>COMPRESSION =&gt; 'LZO'</code></p>
<p><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">重启</span></font></font>HBase<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">，确认你用</span></font></font><code>COMPRESSION
=&gt; 'LZO'</code><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn"><code>创建了表。</code></span></font></font></p>
<p>Common gotchas:</p>
<p><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">常见的错误：</span></font></font></p>
<ul><li>
<p>Where to find <code>hadoop-core-1.0.3.jar</code>? On a normal, production
HBase install, it will be under HBase's <code>lib/</code> directory. In your development environment it
may be stashed under HBase's <code>target/</code>
directory, use <code>find</code> to locate it.</p>
<p><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">去哪儿找到</span></font></font><code>hadoop-core-1.0.3.jar</code>?<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">通常，</span></font></font>HBase<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">安装后，它会放在</span></font></font>HBase<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">的</span></font></font>lib/<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">目录下。在你的开发环境下，它可能被放置在</span></font></font>HBase<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">的</span></font></font>target/<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">目录下，用</span></font></font>find<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">命令去定位它吧。</span></font></font></p>
</li>
<li>
<p>On Mac OS X, you may get <code>error: Native java headers not found. Is $JAVA_HOME set
correctly?</code> when <code>configure</code> is
looking for <code>jni.h</code>, in which case you
need to insert <code>CPPFLAGS=-I/System/Library/Frameworks/JavaVM.framework/Versions/Current/Headers</code>
before <code>CLASSPATH</code> on the 3rd command
above (the one that invokes <code>ant</code>).</p>
<p> </p>
<p><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">在</span></font></font> Mac OS X<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">，你可能会得到</span></font></font><code>error:Native java headers not found. Is $JAVA_HOME set
correctly?</code><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn"><code>当</code></span></font></font>
<code>configure</code><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn"><code>正在查找</code></span></font></font> <code>jni.h</code><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn"><code>的情况下，你需要在</code></span></font></font><code>CLASSPAT</code><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn"><code>之前第三个命令（调用</code></span></font></font><code>ant</code><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn"><code>的命令）插入</code></span></font></font><code>correctly? when configure is looking for jni.h, in which
case you need to insert
CPPFLAGS=-I/System/Library/Frameworks/JavaVM.framework/Versions/Current/Headers</code>
<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn"><code>。</code></span></font></font></p>
</li>
<li>
<p>On RedHat/CentOS/Fedora you may have
to specify where Java is, by adding <code>JAVA_HOME=/usr/lib/jvm/java-1.6.0-openjdk.x86_64</code>
(or similar) to the <code>ant</code> command-line,
before the <code>CLASSPATH</code>.</p>
</li>
</ul><p><br></p>
<p><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">在</span></font></font> RedHat/CentOS/Fedora<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">你可能需要去确定</span></font></font>java<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">在哪，可以在</span></font></font>ant<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">命令行，</span></font></font>CLASSPATH<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">之</span></font></font> <font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">前加入：</span></font></font> <code>JAVA_HOME=/usr/lib/jvm/java-1.6.0-openjdk.x86_64</code><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn"><code>（或相似</code></span></font></font>
<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn"><code>的）。</code></span></font></font></p>
<ul><li>
<p>On RedHat/CentOS/Fedora, if you get the weird error message that
"Cause: the class org.apache.tools.ant.taskdefs.optional.Javah was
not found." then you need to install the <code>ant-nodeps</code> package.</p>
<p><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">在</span></font></font> RedHat/CentOS/Fedora<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">，如果你得到了奇怪的错误信息：</span></font></font>Cause: the class
org.apache.tools.ant.taskdefs.optional.Javah was not
found<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">，那你需要去安装</span></font></font> <code>ant-nodeps</code><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn"><code>包。</code></span></font></font></p>
</li>
</ul><h3><a name="hbasescaleup"></a>Migrating to a real HBase cluster</h3>
<p><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">迁移至一个真正的</span></font></font>HBase<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">群</span></font></font></p>
<p>TBD. In short:</p>
<p><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">简要</span></font></font>TBD<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">：</span></font></font></p>
<ul><li>
<p>Shut down all your TSDs.</p>
</li>
<li>
<p><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">关闭所有的</span></font></font>TSD</p>
</li>
<li>
<p>Shut down your single-node HBase
cluster.</p>
</li>
<li>
<p><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">关闭你单结点的</span></font></font>HBase<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">群</span></font></font></p>
</li>
<li>
<p>Copy the directories named
<code>tsdb</code> and <code>tsdb-uid</code> from your local filesystem to the HDFS
cluster backing up your real HBase cluster.</p>
</li>
<li>
<p><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">从你的本地主机文件系统复制</span></font></font>tsdb<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">和</span></font></font>tsdb<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">－</span></font></font>uid<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">目录至</span></font></font>HDFS<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">群，以备份你的真实</span></font></font>HBase<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">群。</span></font></font></p>
</li>
<li>
<p>Run <code>./bin/hbase
org.jruby.Main ./bin/add_table.rb</code> <code><i>/hdfs/path/to/hbase/</i></code><code><i><b>tsdb</b></i></code> and again for the <code>tsdb-uid</code> directory.</p>
</li>
<li>
<p><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn"><code>跑命令：</code></span></font></font><code>./bin/hbase org.jruby.Main ./bin/add_table.rb</code>
<code><i>/hdfs/path/to/hbase/</i></code><code><i><b>tsdb</b></i></code>
<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">然后相同的方式跑</span></font></font>tsdb-uid<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">。</span></font></font></p>
</li>
<li>
<p>Restart your real HBase cluster
(sorry).</p>
</li>
<li>
<p><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">重启你的真实</span></font></font>HBase<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">群。</span></font></font></p>
</li>
<li>
<p>Restart your TSDs after making sure they now use your real HBase
cluster.</p>
</li>
<li>
<p><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">确认</span></font></font>TSD<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">正在使用你的真实</span></font></font>HBase<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">群后，重启</span></font></font>TSD<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">。</span></font></font></p>
</li>
</ul><h3><a name="hbaseprod"></a>Putting
HBase in production</h3>
<p><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">将</span></font></font>HBase<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">付诸生产</span></font></font></p>
<p>TBD. In short:</p>
<p><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">简要</span></font></font>TBD<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">：</span></font></font></p>
<ul><li>
<p>Stay on a single node unless you can
deploy HBase on at least 5 machines, preferably at least 10.</p>
<p><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">保持一个单结点除非你能在至少</span></font></font>5<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">台机器，最好至少</span></font></font>10<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">台机器上部署</span></font></font>HBase<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">。</span></font></font></p>
</li>
<li>
<p>Make sure you have <a href="http://wiki.apache.org/hadoop/UsingLzoCompression" rel="nofollow">LZO
installed</a> and make sure it's enabled for the tables used by
OpenTSDB.</p>
<p><font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">确认你已经安装</span></font></font>LZO<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">，并且确认它已经能够用于被</span></font></font>OpenTSDB<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">使用的表。</span></font></font></p>
</li>
<li>
<p>TBD...</p>
<p>TBD<font face="WenQuanYi Zen Hei"><font size="2" style="font-size:10pt;"><span lang="zh-cn" xml:lang="zh-cn">。。。</span></font></font></p>
</li>
</ul><p><br></p>							
		</div>            </div>
                </div>