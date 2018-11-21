---
layout:     post
title:      HDFS java 操作 HDFS
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qzqanzc/article/details/69218386				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<pre><code class="language-java">package com.qzq.lhy;

import java.io.FileNotFoundException;
import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.LocatedFileStatus;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.fs.RemoteIterator;
import org.junit.Before;
import org.junit.Test;

public class HdfsClientEasy {
private FileSystem fs = null;
	
	@Before
	public void getFs() throws IOException{
		Configuration conf = new Configuration();
		conf.set("fs.defaultFS", "hdfs://127.0.0.1:9000/");
		conf.set("dfs.replication", "1");
		
		// get a instance of HDFS FileSystem Client
		fs = FileSystem.get(conf);
	}
	
	@Test
	public void testUpload() throws IllegalArgumentException, IOException{
		fs.copyFromLocalFile(new Path("/home/hadoop/"), new Path("/test/hadoop"));
	}
	
	@Test
	public void testRmFile() throws Exception{
		boolean res = fs.delete(new Path("/test/hadoop"),true);
		System.out.println(res?"delete is successfully":"it is failed");
	}
	@Test
	public void testMkdir() throws IllegalArgumentException, IOException{
		
		fs.mkdirs(new Path("/ad/hadoop"));
	}
	
	@Test
	public void testRename() throws IllegalArgumentException, IOException{
		fs.rename(new Path("/jdk.tar"), new Path("/jdk.tar.rename"));
	}
	
	@Test
	public void testListFiles() throws FileNotFoundException, IllegalArgumentException, IOException{
		RemoteIterator&lt;LocatedFileStatus&gt; lists =  fs.listFiles(new Path("/"), true);
		while(lists.hasNext()){
			LocatedFileStatus file = lists.next();
			System.out.println(file.getPath().getName());
		}

	}
}
</code></pre><br>            </div>
                </div>