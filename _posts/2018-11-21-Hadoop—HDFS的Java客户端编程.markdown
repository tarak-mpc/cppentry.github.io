---
layout:     post
title:      Hadoop—HDFS的Java客户端编程
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/Xw_Classmate/article/details/50637108				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:18px;">    可以使用HDFS Shell命令来完成对HDFS的操作，当然也可以使用Java编程语言来对HDFS实现操作。以下是一些Unit测试代码</span></p>
<p><span style="font-size:18px;"></span></p><pre><code class="language-java">package cn.nuc.hadoop.hdfs;

import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;
import java.net.URI;
import java.net.URISyntaxException;

import org.apache.commons.compress.utils.IOUtils;
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.FSDataOutputStream;
import org.apache.hadoop.fs.FileStatus;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.LocatedFileStatus;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.fs.RemoteIterator;
import org.junit.Before;
import org.junit.Test;

public class HdfsUtil {

	FileSystem fs = null;

	@Before
	public void init() throws IOException, InterruptedException,
			URISyntaxException {
		Configuration conf = new Configuration();// 读取配置文件

		// 设置fs.defaultFS参数，如果没有设置，会出现java.lang.IllegalArgumentException:
		// Wrong FS:hdfs://master:9000/user,expected: file:///
		// 当然了，也可以将hadoop集群中的core-site.xml配置文件拷贝到该工程项目下,这样，在读取配置文件时就能够识别hdfs文件系统
		conf.set("fs.defaultFS", "hdfs://master:9000/");

		fs = FileSystem.get(new URI("hdfs://master:9000/"), conf, "hadoop");// 获取hdfs实例
	}

	// upload a loacl file to HDFS
	@Test
	public void upload() throws IOException {

		Path dest = new Path("hdfs://master:9000/user/artifacts.xml");

		FSDataOutputStream fsOut = fs.create(dest);

		FileInputStream localIn = new FileInputStream(
				"D:\\eclipse\\artifacts.xml");

		IOUtils.copy(localIn, fsOut);
	}

	@Test
	public void upload2() throws IllegalArgumentException, IOException {
		fs.copyFromLocalFile(new Path("D:\\eclipse\\artifacts.xml"), new Path(
				"hdfs://master:9000/user/artifacts2.xml"));
	}

	// download a file to local file
	@Test
	public void download() throws IllegalArgumentException, IOException {
		fs.copyToLocalFile(new Path("hdfs://master:9000/user/artifacts2.xml"),
				new Path("D:\\a.xml"));
	}

	// list HDFS file
	@Test
	public void listFiles() throws FileNotFoundException, IllegalArgumentException, IOException {
		// listFiles列出的是文件信息，而且提供递归遍历
		RemoteIterator&lt;LocatedFileStatus&gt; files = fs.listFiles(new Path("/"),
				true);

		while (files.hasNext()) {

			LocatedFileStatus file = files.next();
			Path filePath = file.getPath();
			String fileName = filePath.getName();
			System.out.println(fileName);

		}

		System.out.println("---------------------------------");

		// listStatus 可以列出文件和文件夹的信息，但是不提供自带的递归遍历
		FileStatus[] listStatus = fs.listStatus(new Path("/"));
		for (FileStatus status : listStatus) {

			String name = status.getPath().getName();
			System.out.println(name
					+ (status.isDirectory() ? " is dir" : " is file"));

		}
	}

	// create HDFS folder
	@Test
	public void mkdir() throws IllegalArgumentException, IOException {
		fs.mkdirs(new Path("hdfs://master:9000/aaa/bbb/ccc"));
	}

	// remove HDFS folder or file
	@Test
	public void remove() throws IllegalArgumentException, IOException {
		fs.delete(new Path("hdfs://master:9000/aaa"), true);
	}
}
</code></pre><br><br><p><span style="font-size:18px;"><br></span></p>
            </div>
                </div>