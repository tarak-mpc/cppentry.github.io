---
layout:     post
title:      hadoop学习之使用命令行以及javaAPI管理 hdfs中的数据
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/leixingbang1989/article/details/47999687				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p></p>
<p><strong>Hdoop使用命令来操纵HDFS</strong></p>
<p>查询hadoop fs下所有的命令：hadoop fs 回车，当某一命令不会使用，如rm命令不会用可以使用命令：hadoop fs<strong><span style="color:#FF0000;"> –help</span></strong> rm 来查看rm命令如何用</p>
<p>Hadoop fs -rm  /d1 删除文件</p>
<p>Hadoop fs -rmr 删除的是目录</p>
<p>Hadoop fs -ls /</p>
<p>Hadoop fs -lsr / 对根目录下的内容进行递归地查看。</p>
<p>Hadoop fs -mkdir d1 创建文件夹d1</p>
<p>也可以直接通过URL来进行访问,例如</p>
<p>Hadoop fs –ls hdfs:master:9000/</p>
<p>将本地的文件上传到hdfs文件系统的根目录下的d1文件夹下，</p>
<p>ll  linux中查看所有文件的详细信息</p>
<p>hadoop fs -put  /root/install.log  /d1   上传本地文件 第一个参数表示文件的路径 第二个参数表示要上传到的目的路径</p>
<p>hadoop fs -get &lt;hdfs source&gt;&lt;linux destinatio&gt;把数据从hdfs下载到Linux的特定路径下。</p>
<p>Hadoop fs -text &lt;hdfs文件&gt;             查看目录下的文件</p>
<p><strong>Hadoop使用javaAPI来操纵HDFS</strong></p>
<pre><code class="language-java">package hdfs;

import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;
import java.net.URI;
import java.net.URISyntaxException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.FSDataInputStream;
import org.apache.hadoop.fs.FSDataOutputStream;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IOUtils;

public class app2 {
	private static final String HDFS_PATH = "hdfs://master:9000/" ;
	private static final String newPath = "/d100";
	private static final String newFile = "/d100/abc";// 在d100下创建abc文件，并将文件内容写入

	public static void main(String[] args) throws IOException,
			URISyntaxException {
		final FileSystem fs = FileSystem.get(new URI(HDFS_PATH),
				new Configuration());
		//createDir(fs);//创建文件夹
		//deleteFile(fs);
		uploadFile(fs);
	
	
	}
	public static void deleteFile(FileSystem fs) throws IOException
	{
		String mypath="/d2/derby.log";
		fs.delete(new Path(mypath), true);//hadoop fs -rm 删除文件
	}
   public static void downloadFile(FileSystem fs) throws IOException
   {
		//下载文件
		final FSDataInputStream myIn=fs.open(new Path(newFile));
		IOUtils.copyBytes(myIn, System.out, 1024,true);
   }
	public static void createDir(FileSystem fs) {
		// 创建文件夹
		try {
			fs.mkdirs(new Path(newPath));
		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}

	public static void uploadFile(FileSystem fs) throws IOException {
		final FSDataOutputStream out = fs.create(new Path(newFile));
		FileInputStream in;
		in = new FileInputStream("c:/fff.txt");//通过流的形式读取本地文件 并将文件的内容通过流的形式上传
		IOUtils.copyBytes(in, out, 1024, true);
	}

}
</code></pre><br><p></p>
            </div>
                </div>