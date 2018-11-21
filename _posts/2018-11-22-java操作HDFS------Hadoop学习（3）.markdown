---
layout:     post
title:      java操作HDFS------Hadoop学习（3）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/liuyunshengsir/article/details/52847376				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1><span style="font-family:'Microsoft YaHei', 'Segoe UI', Tahoma, Arial, Helvetica, sans-serif;"><span style="font-family:'comic sans ms', sans-serif;"><span style="font-size:18px;">1.HDFS中常用到的命令</span></span></span></h1>
<div><span style="font-family:'Microsoft YaHei', 'Segoe UI', Tahoma, Arial, Helvetica, sans-serif;"><span style="font-family:'comic sans ms', sans-serif;"><span style="font-size:18px;"></span></span></span><pre><code class="language-html">hadoop fs -ls /
hadoop fs -lsr
hadoop fs -mkdir /user/hadoop
hadoop fs -put a.txt /user/hadoop/
hadoop fs -get /user/hadoop/a.txt /
hadoop fs -cp src dst
hadoop fs -mv src dst
hadoop fs -cat /user/hadoop/a.txt
hadoop fs -rm /user/hadoop/a.txt
hadoop fs -rmr /user/hadoop/a.txt
hadoop fs -text /user/hadoop/a.txt
hadoop fs -copyFromLocal localsrc dst 与hadoop fs -put功能类似。
hadoop fs -moveFromLocal localsrc dst 将本地文件上传到hdfs，同时删除本地文件。</code></pre><br><br></div>
<h1><span style="font-family:'Microsoft YaHei', 'Segoe UI', Tahoma, Arial, Helvetica, sans-serif;"><span style="font-family:'comic sans ms', sans-serif;"><span style="font-size:18px;">2.Java操作HDFS</span></span></span></h1>
<div><span style="font-family:'Microsoft YaHei', 'Segoe UI', Tahoma, Arial, Helvetica, sans-serif;"><span style="font-family:'comic sans ms', sans-serif;"><span style="font-size:18px;"></span></span></span><pre><code class="language-html">public static void main(String[] args) throws IOException, URISyntaxException, InterruptedException {
		//文件配置项
		FileSystem fs = FileSystem.get(new URI("hdfs://192.168.3.145:9000"), new Configuration(),"root");
		
		//文件夹创建
		fs.mkdirs(new Path("/test"));
		System.out.println("文件夹创建成功");
		
		//文件上传
		FileInputStream in = new FileInputStream(new File("D:/hadoopWorkSpace/HDFSSpace/windows.txt"));
		FSDataOutputStream out = fs.create(new Path("/test/windows.txt"));
		IOUtils.copyBytes(in, out, 4096, true);
		System.out.println("上传文件成功");
		
		//文件夹删除
		fs.delete(new Path("/test"), true);
		fs.delete(new Path("/test"), true);
		System.out.println("文件夹删除成功");			

	}</code></pre><br><br></div>
<div><span style="font-family:'Microsoft YaHei', 'Segoe UI', Tahoma, Arial, Helvetica, sans-serif;"><span style="font-family:'comic sans ms', sans-serif;"><span style="font-size:18px;"><br></span></span></span></div>
<div><span style="font-family:'Microsoft YaHei', 'Segoe UI', Tahoma, Arial, Helvetica, sans-serif;"><span style="font-family:'comic sans ms', sans-serif;"><span style="font-size:18px;"><br></span></span></span></div>
<div><span style="font-family:'Microsoft YaHei', 'Segoe UI', Tahoma, Arial, Helvetica, sans-serif;"><span style="font-family:'comic sans ms', sans-serif;"><span style="font-size:18px;"><br></span></span></span></div>
<div><span style="font-family:'Microsoft YaHei', 'Segoe UI', Tahoma, Arial, Helvetica, sans-serif;"><span style="font-family:'comic sans ms', sans-serif;"><span style="font-size:18px;"><br></span></span></span></div>
<div><span style="font-family:'Microsoft YaHei', 'Segoe UI', Tahoma, Arial, Helvetica, sans-serif;"><span style="font-family:'comic sans ms', sans-serif;"><span style="font-size:18px;"><br></span></span></span></div>
<div><span style="font-family:'Microsoft YaHei', 'Segoe UI', Tahoma, Arial, Helvetica, sans-serif;"><span style="font-family:'comic sans ms', sans-serif;"><span style="font-size:24px;"><br></span></span></span></div>
            </div>
                </div>