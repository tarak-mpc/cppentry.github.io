---
layout:     post
title:      hadoop04--hdfs启动以及读取流程, java操作hdfs, 安全模式
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，转载请标明出处。https://blog.csdn.net/kXYOnA63Ag9zqtXx0/article/details/82954503					https://blog.csdn.net/forever428/article/details/83419391				</div>
								            <div id="content_views" class="markdown_views prism-github-gist">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p></p><div class="toc"><h3>文章目录</h3><ul><ul><ul><li><a href="#HDFS_1" rel="nofollow">HDFS启动过程</a></li><li><a href="#_7" rel="nofollow">安全模式</a></li><ul><li><a href="#_10" rel="nofollow">安全模式的管理命令</a></li><ul><li><a href="#_14" rel="nofollow">常用参数</a></li><li><a href="#HDFS_shell_20" rel="nofollow">HDFS shell命令</a></li></ul></ul><li><a href="#HDFS_125" rel="nofollow">HDFS读取数据流程</a></li><li><a href="#JAVAHDFS_132" rel="nofollow">JAVA操作HDFS</a></li><ul><li><a href="#_191" rel="nofollow">配置文件的优先级</a></li><li><a href="#APIHDFS_208" rel="nofollow">通过API下载HDFS上的文件</a></li></ul></ul></ul></ul></div><p></p>
<h3><a id="HDFS_1"></a>HDFS启动过程</h3>
<ul>
<li>第一次启动<br>
第一次启动需要格式化namenode, 创建fsimage和edits, 第一次启动只需要加载fsimage</li>
<li>非第一次启动<br>
如果不是第一次启动, 直接加载edits, fsimage镜像文件,合并成一个新的fsimage文件, 再创建edits 文件记录新的操作行为</li>
</ul>
<h3><a id="_7"></a>安全模式</h3>
<p>在启动的过程中, 会存在30秒的等待时间, 这个等待的时间就是安全模式</p>
<h4><a id="_10"></a>安全模式的管理命令</h4>
<p>$HADOOP_HOME/bin/hdfs dfsadmin -safemode<br>
进入安全模式的管理<br>
[-safemode &lt;enter | leave | get | wait&gt;]</p>
<h5><a id="_14"></a>常用参数</h5>
<pre><code>	get : 获得安全模式的状态
	enter : 进入安全模式, 在安全模式中只允许查询操作,  datanode要向 namenode汇报,块信息,以及数据的完整性
	leave : 离开安全模式
</code></pre>
<h5><a id="HDFS_shell_20"></a>HDFS shell命令</h5>
<pre><code>		[-appendToFile &lt;localsrc&gt; ... &lt;dst&gt;]
        [-cat [-ignoreCrc] &lt;src&gt; ...]
        [-checksum &lt;src&gt; ...]
        [-chgrp [-R] GROUP PATH...]
        [-chmod [-R] &lt;MODE[,MODE]... | OCTALMODE&gt; PATH...]
        [-chown [-R] [OWNER][:[GROUP]] PATH...]
        [-copyFromLocal [-f] [-p] [-l] &lt;localsrc&gt; ... &lt;dst&gt;]
        [-copyToLocal [-p] [-ignoreCrc] [-crc] &lt;src&gt; ... &lt;localdst&gt;]
        [-count [-q] [-h] &lt;path&gt; ...]
        [-cp [-f] [-p | -p[topax]] &lt;src&gt; ... &lt;dst&gt;]
        [-createSnapshot &lt;snapshotDir&gt; [&lt;snapshotName&gt;]]
        [-deleteSnapshot &lt;snapshotDir&gt; &lt;snapshotName&gt;]
        [-df [-h] [&lt;path&gt; ...]]
        [-du [-s] [-h] &lt;path&gt; ...]
        [-expunge]
        [-find &lt;path&gt; ... &lt;expression&gt; ...]
        [-get [-p] [-ignoreCrc] [-crc] &lt;src&gt; ... &lt;localdst&gt;]
        [-getfacl [-R] &lt;path&gt;]
        [-getfattr [-R] {-n name | -d} [-e en] &lt;path&gt;]
        [-getmerge [-nl] &lt;src&gt; &lt;localdst&gt;]
        [-help [cmd ...]]
        [-ls [-d] [-h] [-R] [&lt;path&gt; ...]]
        [-mkdir [-p] &lt;path&gt; ...]
        [-moveFromLocal &lt;localsrc&gt; ... &lt;dst&gt;]
        [-moveToLocal &lt;src&gt; &lt;localdst&gt;]
        [-mv &lt;src&gt; ... &lt;dst&gt;]
        [-put [-f] [-p] [-l] &lt;localsrc&gt; ... &lt;dst&gt;]
        [-renameSnapshot &lt;snapshotDir&gt; &lt;oldName&gt; &lt;newName&gt;]
        [-rm [-f] [-r|-R] [-skipTrash] &lt;src&gt; ...]
        [-rmdir [--ignore-fail-on-non-empty] &lt;dir&gt; ...]
        [-setfacl [-R] [{-b|-k} {-m|-x &lt;acl_spec&gt;} &lt;path&gt;]|[--set &lt;acl_spec&gt; &lt;path&gt;]]
        [-setfattr {-n name [-v value] | -x name} &lt;path&gt;]
        [-setrep [-R] [-w] &lt;rep&gt; &lt;path&gt; ...]
        [-stat [format] &lt;path&gt; ...]
        [-tail [-f] &lt;file&gt;]
        [-test -[defsz] &lt;path&gt;]
        [-text [-ignoreCrc] &lt;src&gt; ...]
        [-touchz &lt;path&gt; ...]
        [-truncate [-w] &lt;length&gt; &lt;path&gt; ...]
        [-usage [cmd ...]]
</code></pre>
<ol>
<li>cat和text的区别:<br>
cat 读取文本内容, text 也是读取文本内容,连个命令都可以读取, cat 读取文本内容, text 也是读取文本内容,连个命令都可以读取, 但是text既可以读取文本文件,也可以读取二进制文件, cat只能读取文本文件</li>
<li>mkdir 创建目录<br>
如果想要创建多级目录 使用<strong>mkdir -p  路径</strong> 在HDFS存在/ 和非/ 的情况<br>
如果在创建目录或者文件的时候,前面加  <b>/</b>  代表在hdfs根路径上存储</li>
<li>从本地剪切到hdfs上</li>
</ol>
<pre><code>moveFromLocal &lt;localsrc&gt; ... &lt;dst&gt;

bin/hdfs dfs -moveFromLocal hello.txt /hello3.txt
</code></pre>
<ol start="4">
<li>追加文件</li>
</ol>
<pre><code> -appendToFile &lt;localsrc&gt; ... &lt;dst&gt;
</code></pre>
<ol start="5">
<li>更改文件权限</li>
</ol>
<pre><code>[-chgrp [-R] GROUP PATH...]
 [-chmod [-R] &lt;MODE[,MODE]... | OCTALMODE&gt; PATH...]
 [-chown [-R] [OWNER][:[GROUP]] PATH...]
</code></pre>
<p>文件权限</p>

<table>
<thead>
<tr>
<th>读</th>
<th>写</th>
<th>执行</th>
</tr>
</thead>
<tbody>
<tr>
<td>R</td>
<td>w</td>
<td>x</td>
</tr>
<tr>
<td>4</td>
<td>2</td>
<td>1</td>
</tr>
</tbody>
</table><ol start="5">
<li>从本地复制数据到hdfs,本地的数据不删除(同put)</li>
</ol>
<pre><code> -copyFromLocal [-f] [-p] [-l] &lt;localsrc&gt; ... &lt;dst&gt;
 bin/hdfs dfs -copyFromLocal c.txt /c.txt
</code></pre>
<ol start="6">
<li>从hdfs复制数据到本地,hdfs上的数据不删除(同get)</li>
</ol>
<pre><code> -copyToLocal [-p] [-ignoreCrc] [-crc] &lt;src&gt; ... &lt;localdst&gt;
</code></pre>
<ol start="7">
<li>从hdfs复制数据到hdfs</li>
</ol>
<pre><code>cp [-f] [-p | -p[topax]] &lt;src&gt; ... &lt;dst&gt;
</code></pre>
<ol start="8">
<li>合并下载多个文件,比如hdfs下面存在a.txt, b.txt</li>
</ol>
<pre><code>bin/hdfs dfs -getmerge /*.txt merge.txt
</code></pre>
<ol start="9">
<li>删除文件或文件夹</li>
</ol>
<pre><code>rm -r
</code></pre>
<ol start="10">
<li>删除空目录(相对于rm -r  更安全,可以防止误删)</li>
</ol>
<pre><code>rmdir
</code></pre>
<ol start="11">
<li>查看路径的大小信息</li>
</ol>
<pre><code>df [-h] [&lt;path&gt; ...]
bin/hdfs dfs -du -s -h /
</code></pre>
<ol start="12">
<li>查看hdfs系统可用的空间信息</li>
</ol>
<pre><code>[-du [-s] [-h] &lt;path&gt; ...]
bin/hdfs dfs -df -h /
</code></pre>
<h3><a id="HDFS_125"></a>HDFS读取数据流程</h3>
<ol>
<li>客户端想namenode请求下载文件, namenode收到请求之后查询元数据信息,如果没有,返回无,如果有, 返回datanode数据块的信息</li>
<li>客户端挑选一台最近的datanode , 进行请求数据</li>
<li>datanode开始传输数据给客户端, 传输数据是以 packet 为单位进行传输</li>
<li>客户端接收packet数据, 先在本地缓存, 由于数据量的庞大, packet一边缓存,一边写入到目标文件.<br>
<img src="https://img-blog.csdnimg.cn/20181026202438534.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZvcmV2ZXI0Mjg=,size_27,color_FFFFFF,t_70" alt="在这里插入图片描述"></li>
</ol>
<h3><a id="JAVAHDFS_132"></a>JAVA操作HDFS</h3>
<ol>
<li>配置链接信息, 并对文件进行上传</li>
</ol>
<pre><code>import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;

public class HDFSClient {

	public static void main(String[] args) throws IOException {
		
		// 第一步 获得与HDFS进行连接 
		
		 //获得文件系统
		Configuration conf=new Configuration();
		
		conf.set("fs.defaultFS", "hdfs://hadoop01:8020");
		FileSystem fs=FileSystem.get(conf);
		
		// 第二步 操作HDFS 文件系统 (上传文件)
		
		fs.copyFromLocalFile(new Path("c:/hello9.txt"), new Path("/hello9.txt"));
		
		
		// 第三步 关闭资源
		
		fs.close();
		
		System.out.println("上传成功");
	}
}
</code></pre>
<ol start="2">
<li>范例：在客户端可以给定操作HDFS的参数，接下来观察在客户端给定文件副本数</li>
</ol>
<pre><code>@Test
	public void testCopyFromLocal() throws IOException {
		
		// 第一步 获得与HDFS进行连接 
		
		 //获得文件系统
		Configuration conf=new Configuration();
		
		conf.set("fs.defaultFS", "hdfs://hadoop01:8020");
		conf.set("dfs.replication", "2");
		FileSystem fs=FileSystem.get(conf);
		
	   // 第二步 操作HDFS 文件系统 (上传文件)
		
		fs.copyFromLocalFile(new Path("c:/hello9.txt"), new Path("/hello10.txt"));
		
		// 第三步 关闭资源
		
		fs.close();
		
		System.out.println("上传成功");
	}
</code></pre>
<h4><a id="_191"></a>配置文件的优先级</h4>
<p>通过上面的代码发现,客户端修改的属性,优先级高于集群中配置文件的优先级, 在客户端可以通过配置文件来进行属性的配置, 优先级高于HDFS的集群, 如果说在客户端同时配置了配置文件, 以及程序中配置了相同的属性, 优先级最高的是程序中设置的属性<br>
优先级:<br>
程序设置的属性&gt;&gt;&gt;客户端的配置文件&gt;&gt;&gt;HDFS的自定义配置文件&gt;&gt;&gt;HDFS默认配置文件</p>
<ol start="3">
<li>在客户端进行HDFS链接的时候, 默认使用的是windows中的名称, 所以也可以在eclipse中模拟用户进行登录, 或者直接给定用户名进行操作<br>
(1) 在eclipse中模拟用户进行登录<br>
<img src="https://img-blog.csdnimg.cn/20181026184649586.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZvcmV2ZXI0Mjg=,size_27,color_FFFFFF,t_70" alt="在这里插入图片描述"></li>
</ol>
<p><img src="https://img-blog.csdnimg.cn/2018102618494424.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZvcmV2ZXI0Mjg=,size_27,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<p>(2) 通过建立链接的时候, 直接给定链接信息, 包括用户名</p>
<pre><code>FileSystemfs=FileSystem.get(new URI("hdfs://hadoop01:8020"),conf,"hadoop");
</code></pre>
<p>以上两种方式都是模拟用户操作的方法</p>
<h4><a id="APIHDFS_208"></a>通过API下载HDFS上的文件</h4>
<pre><code>@Test
	public void copyToLocal() throws IOException, InterruptedException, URISyntaxException {

		// 第一步 获得与HDFS进行连接

		// 获得文件系统
		Configuration conf = new Configuration();

		// conf.set("fs.defaultFS", "hdfs://hadoop01:8020");
		conf.set("dfs.replication", "2");
		FileSystem fs = FileSystem.get(new URI("hdfs://hadoop01:8020"), conf, "hadoop");
		
		
		/**
		 * delSrc 指定是否将源文件删除 HDFS 上的文件 boolean  true  删除  false 不删除
		 * 
		 * src 要下载的文件路径
		 * 
		 * dst 文件下载到的路径
		 * 
		 * useRawLocalFileSystem 是否开启文件校验
		 * 
		 */
		fs.copyToLocalFile(false, new Path("/hello15.txt"), new Path("c:/hello15.txt"), true);
		
		
		fs.close();
		
		
		System.out.println("下载完成");
	}
</code></pre>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>