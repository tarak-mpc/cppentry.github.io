---
layout:     post
title:      Hadoop HDFS
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/freeworkman/article/details/38400401				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>1、HDFS相关概念</p>
<p>2、HDFS配置</p>
<p>3、HDFS使用</p>
<p>4、HDFShadoop 应用、HBase</p>
<p>本文希望通过最简洁的方式让您快了了解Hadoop HDFS:</p>
<p>HDFS是Hadoop中的文件系统，既然是文件系统那其目的就是为了提供数据存储服务，它应该提供一般文件系统最常用的功能。Hadoop系统非常庞大，本文将主要介绍HDFS，</p>
<p><br></p>
<p><strong>2、HDFS文件系统配置</strong></p>
<p>作为分布式文件系统只有集群下的HDFS才能真正的发挥作用，下面将描述如何搭建HDFS集群环境：</p>
<p>HDFS系统的三个组成部分：</p>
<blockquote style="border:none;">
<p><strong>NameNode</strong>：master（管理者）管理集群下的DataNode节点，namenode管理整个文件系统的命名空间，记录个块所在的数据节点新，维护整个文件系统中的文件和目录树，这些信息已两个文件的形式保持在磁盘上，分别为</p>
</blockquote>
<p><span></span>命名空间镜像文件：/tmp/hadoop-${user}/dfs/name/current/fsimage （默认文件位置）</p>
<p><span></span>编辑日志：/tmp/hadoop-${user}/dfs/name/current/edits（默认文件位置）</p>
<blockquote style="border:none;">
<p><strong>DataNode</strong>：slave（）文件系统工作节点。根据需要存储或检索数据，定期向namenode发送块的列表信息。</p>
</blockquote>
<blockquote style="border:none;">
<p><strong>Client</strong>：客户端用户访问和使用HDFS文件系统的工具</p>
</blockquote>
<strong>2.1 单机HDFS环境（伪分布式）</strong><br><blockquote style="border:none;">
<p> 相关配置文件说明（0.20version）：rpm安装配置文件在/etc/hadoop下，tar解压在conf文件夹下；</p>
<p>hadoop-env.xml：hadoop综合环境配置，包括JAVA_HOM和一些启动参数；</p>
<p>core-site.xml：核心配置文件，fd.default.name等参数配置；</p>
<p>hdfs-site.xml：hdfs-相关配置。</p>
</blockquote>
<strong>2.1.1 环境检查和配置</strong><br><p></p>
<blockquote style="border:none;">
<p>1、配置JAVA环境，确定JAVA_HOME设置成功，检查hadoop-env.xml中JAVA_HOME参数是否与机器的实际JAVA_HOME一致；</p>
<p>2、向core-site.xml中添加如下代码：</p>
<pre><code class="language-html"> &lt;property&gt;
    &lt;name&gt;fs.default.name&lt;/name&gt;
    &lt;value&gt;hdfs://localhost:9000&lt;/value&gt;
  &lt;/property&gt;</code></pre>  向hdfs-site.xml中添加如下代码：指定文件系统中块的副本数；<pre><code class="language-html"> &lt;property&gt;
    &lt;name&gt;dfs.replication&lt;/name&gt;
    &lt;value&gt;1&lt;/value&gt;
  &lt;/property&gt;</code></pre>3、ssh配置：hadoop启动默认要ssh认证说以这里要配置能够通过ssh访问本机<pre><code class="language-html">$ ssh-keygen -t dsa -P '' -f ~/.ssh/id_dsa 
$ cat ~/.ssh/id_dsa.pub &gt;&gt; ~/.ssh/authorized_keys</code></pre>
<p>配置完成后通过ssh localhost 登录本机，如果未要求输入密码登录成功则ssh配置完成。</p>
</blockquote>
<strong>2.1.2 启动验证HDFS系统</strong><br><p></p>
<blockquote style="border:none;">
<p>1、格式化namenode：格式化后会在/tmp/hadoop-${user}/dfs/name找到namenode相关的文件；</p>
<pre><code class="language-html">$ hadoop namenode -format</code></pre></blockquote>
<blockquote style="border:none;">
<p>2、启动hdfs：默认启动日志可以/val/log/hadoop/${user}目录下</p>
<pre><code class="language-html">$ start-dfs.sh</code></pre>
<p>3、验证是否启动成功：启动会通过netstat  -nap | grep java可以查看到相关的进程。</p>
</blockquote>
<blockquote style="border:none;">
<p> 可以通过浏览器访问<span style="font-family:monospace;line-height:15.600000381469727px;">http://namenode-name:50070/查看问下系统信息。</span></p>
<p>4、使用stop-dfs.sh关闭HDFS</p>
</blockquote>
<p><strong>2.2 集群环境（分布式）</strong></p>
<p><strong><strong>2.2.1 环境检查和配置</strong><br></strong></p>
<blockquote style="border:none;">
<p>本实验采用1个namenode和2个datanode的架构进行测试<br></p>
<p>1、配置允许namenode和datanode之间的无密码访问</p>
<pre><code class="language-python">$ ssh-keygen -t dsa -P '' -f ~/.ssh/id_dsa </code></pre>通过ssh-keygen 工具产生用于无密码认证的密匙对，将公钥id_dsa.pub内容追加到被登录服务器的~/.ssh/authorized_keys（ssh默认认证文件）的文件中，实现无密码登录，更多关于ssh可以在网上查看更多详细资料，此节要实现的是namenode可以无密码登陆datanote，datanode可以无密码登录namenode，datanode之间无需配置。<br><p></p>
<p>2、同步集群时间<br></p>
<p></p>
<p>ntpdate dateServer ：同步集群内所有服务器的时间（注意hwclock -w）</p>
<p>3、namenode配置</p>
<br><p>a、配置如2中配置基本一致，hdfs-site.xml中的配置可以却掉，使用hdfs默认3个块副本的配置，core-site.xml中fs.default.name要使用真实的主机名或IP这个很重要。<br>
b、在conf/slaves（/etc/hadoop/slaves）中添加datanode主机名或IP，以行分割。<br></p>
<p>4、datanode配置<br></p>
<p>在core-site.xml中指定HDFS的位置，同namenode的core-site.xml配置相同。<br></p>
</blockquote>
<p><strong>2.2.2、启动方式和停止方式</strong></p>
<p></p>
<blockquote style="border:none;">
<p>在namenode节点使用start-dfs.sh启动HDFS集群，<br></p>
<p>可以在namenode和datanode上使用netstat -nap | grep java 查看相关进程是否启动，可以从/val/log/hadoop/${user}中查看启动信息</p>
<p>如果新添加datenode可以配置好datanote后在datanode上使用hadoop datanode命令启动datanode节点，记得在namenode的slaves配置文件中加入此datanode。<br>
ADDT：实际环境可以考虑加入冗余的DNS进行解析。<br>
在浏览器中通过http://namenode:50070访问集群接口。<br></p>
</blockquote>
<p><strong>3、访问文件系统</strong></p>
<p></p>
<blockquote style="border:none;">
<p>hadoop提供了相应的工具来访问文件系统，最基本的就是shell工具</p>
<p>shell工具：</p>
<p>hadoop fs -ls /：查看HDFS文件系统(此命令将默认显示core-site.xml中fs.default.name指定的文件系统)</p>
<p>hadoop fsck  / -files -blocks：</p>
<p>hadoop dfsadmin -report：查看问下系统状态信息。</p>
</blockquote>
<p></p>
<p><strong>4、通过API访问文件系统（</strong>本例源自Hadoop in Action第3章，示例为主要流程未考虑异常问题<strong>）</strong></p>
<p></p>
<pre><code class="language-java">import java.io.IOException;
import java.util.Scanner;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.FSDataInputStream;
import org.apache.hadoop.fs.FSDataOutputStream;
import org.apache.hadoop.fs.FileStatus;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;

public class MergeFile {
	public static void main(String[]args) throws IOException{
		//便于在Eclipse中调试
		Scanner scan = new Scanner(System.in);
		System.out.println("input.dir output.file");
		args = scan.nextLine().split(" ");
		
		//配置文件
		Configuration conf = new Configuration();
		FileSystem local = FileSystem.getLocal(conf);
		conf.set("fs.default.name", "hdfs://localhost");
		FileSystem hdfs = FileSystem.get(conf); 
		
		//活动输入目录和输出文件
		Path inputDir = new Path(args[0]);
		Path hdfsFile = new Path(args[1]);

		//执行文件合并拷贝
		FileStatus[] inputFiles  = local.listStatus(inputDir);
		FSDataInputStream rs = null;
		FSDataOutputStream os = hdfs.create(hdfsFile);
		for(FileStatus fss :inputFiles){
			System.out.println(fss.getPath().getName());
			int readSize = -1;
			byte[] buffer = new byte[1024];
			rs = local.open(fss.getPath());
			while((readSize=rs.read(buffer))&gt;0){
				os.write(buffer,0,readSize);
			}
			rs.close();
		}
		os.close();
		
	}
}
</code></pre><br><p><strong>5、hadoop文件系统</strong><br></p>
<blockquote style="border:none;">
<p>对于hadoop文件系统这里不再描述，可以查看《hadoop权威指南》或官方文档进行详细了解。很重要的原因是以笔者现在的水平并不认为能提供比这些更优秀的内容，、没必要重复相同的内容。以后会随着了解和经验的积累补充一下经验性的东西。</p>
<p>重要概念：</p>
<p>文件块：默认64M（v0.20），之所以大是尽量减少寻址时间。</p>
<p>namenode的冗余机制：1、在多个元数据上保存元数据的持久状态（例同时写入本地和nfs）；2、运行辅助的namenode从编辑日志中合并命名空间镜像，辅助namenode保存的状态总是滞后主节点（更多了解文件系统镜像和编辑日志）。</p>
</blockquote>
<p><span style="font-weight:bold;"></span></p>
<p><span></span></p>
<p><span></span></p>
<p><span></span></p>
<br><p></p>
<p><strong><br></strong></p>
            </div>
                </div>