---
layout:     post
title:      hadoop系列之五JavaAPI操作HDFS文本系统
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/kgdxwy1/article/details/73429927				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1>JavaAPI操作HDFS文本系统</h1>
<div>
<blockquote style="border:none;">window下用到的hadoop版本:</blockquote>
<blockquote style="border:none;">链接：http://pan.baidu.com/s/1jI2yVgq 密码：yvet<br></blockquote>
</div>
<div>
<blockquote style="border:none;">
<h2>创建项目</h2>
</blockquote>
</div>
<blockquote style="border:none;">
<blockquote style="border:none;">
<div>使用idea创建的项目</div>
<div><img src="https://img-blog.csdn.net/20170618130820171?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQva2dkeHd5MQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<div><img src="https://img-blog.csdn.net/20170618130839030?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQva2dkeHd5MQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<div><img src="https://img-blog.csdn.net/20170618130901140?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQva2dkeHd5MQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<div><img src="https://img-blog.csdn.net/20170618130919671?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQva2dkeHd5MQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<div><img src="https://img-blog.csdn.net/20170618130939375?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQva2dkeHd5MQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<div><br></div>
<div><pre><code class="language-html">&lt;dependency&gt;
    &lt;groupId&gt;org.apache.hadoop&lt;/groupId&gt;
    &lt;artifactId&gt;hadoop-client&lt;/artifactId&gt;
    &lt;version&gt;2.6.4&lt;/version&gt;
&lt;/dependency&gt;</code></pre></div>
</blockquote>
<h2>操作JavaAPI</h2>
</blockquote>
<blockquote style="border:none;">
<blockquote style="border:none;">
<h3>1.初始化方式一操作</h3>
<div><span style="color:rgb(255,0,0);"><em>注意:出现错误请查看</em><strong><em>hadoop系统六错误解决方案</em></strong></span></div>
</blockquote>
</blockquote>
<blockquote style="border:none;">
<blockquote style="border:none;">
<div><pre><code class="language-java">private FileSystem fs;
private Configuration c;

@Before
public void init() throws Exception {
<span>	</span>c = new Configuration();
<span>	</span>//方式一 设置地址,传递配置,在vm options中设置-DHADOOP_USER_NAME=hadoop
<span>	</span>c.set("fs.defaultFS", "hdfs://mini06:9000");
<span>	</span>//拿到一个文件系统操作客户端实例对象
<span>	</span>fs = FileSystem.get(c);
}</code></pre>
<h3>2.初始化方式二操作 <span style="color:#ff0000;">推荐</span></h3>
<div><pre><code class="language-java">private FileSystem fs;
private Configuration c;

@Before
public void init() throws Exception {
	c = new Configuration();
	//方式二 传递用户与文件系统
	/**
         *  URI uri, hdfs的地址
         *  Configuration conf, 客户端配置对象
         *  String user  指定linux上运行的用户名称
         */
	fs = FileSystem.get(new URI("hdfs://mini06:9000"), c, "hadoop");
}</code></pre></div>
<h3>3.文件上传</h3>
</div>
<div><pre><code class="language-java">//从本地上传文件到hadoop hdfs文件系统的根目录下
@Test
public void upload() throws IOException {
	/**
	* 将文件上传到fdhs
	* 参数一:本地文件路径
	* 参数二:fdhs文件目录
	*/
	f.copyFromLocalFile(new Path("c:/1.png"),new Path("/"));
	f.close();
}</code></pre>
<h3>4.下载文件</h3>
</div>
<div><pre><code class="language-java">//从hadoop hdfs下载文件到本地
@Test
public void testDonwload() throws IOException {
	/**
	 * Path src, 下载的地址
	 * Path dst  存放下载文件的地址
	 */
	f.copyToLocalFile(new Path("/1.png"),new Path("d:/2.png"));
	f.close();
}</code></pre>
<h3>5.获取客户端所有配置信息并打印</h3>
</div>
<div><pre><code class="language-java">//获取客户端所有的配置信息
@Test
public void testGetConf(){
	Iterator&lt;Map.Entry&lt;String, String&gt;&gt; it = c.iterator();
	while(it.hasNext()){
	    Map.Entry&lt;String, String&gt; conf = it.next();
	    System.out.println(conf.getKey()+":"+conf.getValue());
	}
}</code></pre>
<h3>6.创建目录</h3>
</div>
<div><em>默认就是递归创建目录</em></div>
<div><pre><code class="language-java"> //创建目录
@Test
public void testMkdir() throws IOException {
	boolean mkdirs = f.mkdirs(new Path("/abc/def"));
	System.out.println("是否创建成功"+mkdirs);
}</code></pre>
<h3>7.删除文件</h3>
</div>
<div><pre><code class="language-java">//删除文件
@Test
public void testDelete() throws IOException {
	/**
	 * 参数一:删除的路径
	 * 参数二:要不要递归删除
	 * 返回:是否删除成功
	 */
	boolean delete = f.delete(new Path("/1.png"), false);
	System.out.println("是否删除成功:"+delete);
}</code></pre>
<h3>8.递归列出文件下文件与子文件</h3>
<pre><code class="language-java">//递归列出文件下文件与子文件
@Test
public void testTrss() throws IOException {
	/**
	 * 参数一:路径
	 * 参数二:是否递归查询
	 * 返回:返回迭代器
	 */
	RemoteIterator&lt;LocatedFileStatus&gt; listFiles = f.listFiles(new Path("/"),true);
	while(listFiles.hasNext()){
	    LocatedFileStatus fs = listFiles.next();
	    System.out.println("blockSize:" + fs.getBlockSize());
	    System.out.println("owner:" + fs.getOwner());
	    System.out.println("replication:" + fs.getReplication());
	    System.out.println("permission:" + fs.getPermission());
	    System.out.println("path:" + fs.getPath());
	    <span style="color:#ff0000;">//获取某一个文件的块信息</span>
	    BlockLocation[] blockLocations = fs.getBlockLocations();
	    for (BlockLocation b:blockLocations){
		<span style="background-color:rgb(255,255,255);"><span style="color:#ff0000;">//获取某一个文件分块后的起始位置</span></span>
		System.out.println("offset:"+b.getOffset());
		<span style="color:#ff0000;">//获取每一个块的大小</span>
		System.out.println("block length:"+b.getLength());
		String[] datanodes = b.getHosts();
		for (String node:datanodes){
		    System.out.println("datanodes:"+node);
		}
	    }
	    System.out.println("========================================================");
	}
}</code></pre><img src="https://img-blog.csdn.net/20170618135544664?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQva2dkeHd5MQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></div>
<div>
<h3>9.读取一级目录</h3>
<pre><code class="language-java">//读取一级目录
@Test
public void testLs2() throws IOException {
	FileStatus[] fileStatuses = fs.listStatus(new Path("/"));
	for (FileStatus f : fileStatuses) {
	    System.out.print(f.getPath().getName());
	    System.out.println(f.isFile() ? "====&gt;file:" : "====&gt;directory");
	}
}</code></pre></div>
</blockquote>
<blockquote style="border:none;">
<div><br></div>
<div><br></div>
<div><br></div>
</blockquote>
</blockquote>
            </div>
                </div>