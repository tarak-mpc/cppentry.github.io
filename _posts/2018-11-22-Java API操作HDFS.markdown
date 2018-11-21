---
layout:     post
title:      Java API操作HDFS
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/lzm1340458776/article/details/37995345				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>HDFS是存储数据的分布式文件系统，对HDFS的操作，就是对文件系统的操作，除了用HDFS的shell命令对文件系统进行操作，我们也可以利用Java API对文件系统进行操作，比如文件的创建、删除、修改权限等等，还有文件夹的创建、删除、重命名等等。</p>
<p><strong><span style="color:#ff0000;">使用Java API对文件系统进行操作主要涉及以下几个类：</span></strong></p>
<p><strong><span style="color:#ff0000;">1.</span></strong>Configuration类：该类的对象封装了客户端或者服务端的配置。</p>
<p><strong><span style="color:#ff0000;">2.</span></strong>FileSystem类：该类的对象是一个文件系统对象，可以利用该对象的一些方法来对文件进行操作，FileSystem是一个抽象，不能通过new来获取对象，应该通过Filesystem的静态方法get()来获取：</p>
<p></p>
<pre><code class="language-java">//创建URI对象
			//PATH = "hdfs://liaozhongmin:9000/";
			URI uri = new URI(PATH);
			//获取文件系统
			FileSystem fileSystem = FileSystem.get(uri, new Configuration());</code></pre><br><strong><span style="color:#ff0000;">3.</span></strong>FSDataInputStream和FSDataOutputStream：这两个类是HDFS中的输入输出流。分别通过FileSystem的open()方法和create()方法获得。
<p></p>
<p><br></p>
<p><strong>在使用Java API 操作HDFS之前，首先要保证hadoop是正常启动的，可以通过jps命令来查看hadoop的进程是否全部启动，如下图：</strong></p>
<p><img src="https://img-blog.csdn.net/20140720192132078" alt=""><br></p>
<p><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><span style="color:rgb(255,0,0);">注：如上图所示，使用jps命令可以看到Hadoop的五个进程即NameNode、DataNode、SecondaryNameNode、TaskTrackers、JobTracker启动了，就表示hadoop启动成功了。</span></span></p>
<p><br></p>
<p><strong><span style="color:#006600;">下面是关于Java API操作HDFS文件系统的常用工具类：</span></strong></p>
<p></p><pre><code class="language-java">public class FileSystemUtil {

	//定义HDFS的路径
	private static final String PATH = "hdfs://liaozhongmin:9000/";
	
	/**
	 * 获取文件系统FileSystem
	 * @return
	 */
	public static FileSystem getFileSystem(){
		
		try {
			//创建URI对象
			//PATH = "hdfs://liaozhongmin:9000/";
			URI uri = new URI(PATH);
			//获取文件系统
			FileSystem fileSystem = FileSystem.get(uri, new Configuration());
			return fileSystem;
		} catch (Exception e) {
			e.printStackTrace();
		}
		
		return null;
	}
	
	/**
	 * 创建文件夹
	 * @param fileSystem
	 */
	public static void mkdir(FileSystem fileSystem,String path){
		
		try {
			//创建Path对象
			Path srcPath = new Path(path);
			//通过文件系统对象创建文件夹
			fileSystem.mkdirs(srcPath);
		} catch (IOException e) {
			e.printStackTrace();
		} finally{
			try {
				fileSystem.close();
			} catch (IOException e) {
				e.printStackTrace();
			}
		}
	}
	
	/**
	 * 创建新文件
	 * @param dst
	 * @param contents
	 */
	public static void createFile(String dst,byte[] contents){
		
		//定义文件系统
		FileSystem fileSystem = null;
		//定义输出流
		FSDataOutputStream outputStream = null;
		try {
			//获取文件系统
			fileSystem = FileSystemUtil.getFileSystem();
			//创建一个目标路径
			Path path = new Path(dst);
			//创建文件
			outputStream = fileSystem.create(path);
			//写数据
			outputStream.write(contents);
			System.out.println("文件创建成功!");
		} catch (IOException e) {
			e.printStackTrace();
		} finally{
			//关闭
			try {
				fileSystem.close();
				outputStream.close();
			} catch (IOException e) {
				e.printStackTrace();
			}
			
		}
		
	}
	/**
	 * 向文件系统中上传本地数据
	 * @param fileSystem
	 */
	public static void putData(FileSystem fileSystem,String src,String dst){
		
		try {
			//创建原路径Path对象
			Path srcPath = new Path(src);
			//创建目标路径Path对象
			Path dstPath = new Path(dst);
			//调用文件系统的文件爱你复制函数，前面的参数是指是否删除源文件，true为删除，否则不删除
			fileSystem.copyFromLocalFile(false, srcPath, dstPath);
			
		} catch (IOException e) {
			e.printStackTrace();
		} finally{
			//关闭文件系统
			try {
				fileSystem.close();
			} catch (IOException e) {
				e.printStackTrace();
			}
		}
	}
	
	/**
	 * 文件重命名
	 * @param oldName 旧名字
	 * @param newName 新名字
	 */
	public static void rename(FileSystem fileSystem,String oldName,String newName){
		
		try {
			//创建旧文件的Path对象
			Path oldPath = new Path(oldName);
			Path newPath = new Path(newName);
			//重命名
			fileSystem.rename(oldPath, newPath);
		} catch (Exception e) {
			e.printStackTrace();
		} finally{
			try {
				fileSystem.close();
			} catch (IOException e) {
				e.printStackTrace();
			}
		}
	}
	/**
	 * 下载文件
	 * @param fileSystem
	 * @param fileName
	 */
	public static void getData(FileSystem fileSystem,String src,String dst){
		
		
		try {
			//创建Path对象
			Path path = new Path(src);
			//通过path构建文件系统的输入流
			FSDataInputStream in = fileSystem.open(path);
			//构建文件
			File file = new File(dst);
			
			if (!file.exists()){
				file.createNewFile();
			}
			//构建输出流
			FileOutputStream fileOutputStream = new FileOutputStream(file);
			//下载
			IOUtils.copyBytes(in, fileOutputStream, 1024, true);
		} catch (IOException e) {
			
			e.printStackTrace();
		}
	}
	
	/**
	 * 遍历文件系统中的某个目录
	 * @param fileSystem
	 */
	public static void listFile(FileSystem fileSystem,String path){
		
		try {
			//调用listStatus()方法获取一个文件数组
			FileStatus[] listStatus = fileSystem.listStatus(new Path(path));
			//循环遍历
			for (FileStatus fileStatus : listStatus){
				//判断是否为目录
				String isDir = fileStatus.isDir()?"是文件夹":"是文件";
				//获取文件的权限
				String permission = fileStatus.getPermission().toString();
				//获取备份
				short replication = fileStatus.getReplication();
				//获取数组的长度
				long len = fileStatus.getLen();
				//获取文件的路径
				String filePath = fileStatus.getPath().toString();
				//打印数据
				System.out.println("isDir:" +isDir + "\npermission:" + permission + "\nreplication:" + replication+ "\nlen:" + len + "\nfilepath:" + filePath);
			}
		} catch (IOException e) {
			
			e.printStackTrace();
		}
	}
	
	/**
	 * 删除文件
	 * @param fileSystem
	 */
	public static void remove(FileSystem fileSystem,String filePath){
		
		try {
			//创建path对象
			Path path = new Path(filePath);
			//通过文件系统进行删除,第二个参数设置为true时才可以删除为目录的文件，否则的话如果是目录而不是文件就会报错的
			fileSystem.delete(path, true);
		} catch (IOException e) {
			e.printStackTrace();
		}
	}
	
	/**
	 * 工具类的测试
	 * @param args
	 */
	public static void main(String[] args) {
		
		//创建文件系统
		FileSystem fileSystem = getFileSystem();
		
		//创建文件夹
		//FileSystemUtil.mkdir(fileSystem, "/myDir");
		
		//创建文件并写入内容
		//byte[] contents = "我爱你".getBytes();
		//FileSystemUtil.createFile("/myDir/liao.txt", contents);
		
		//文件重命名
		//FileSystemUtil.rename(fileSystem, "/myDir/liao.txt", "/myDir/liao_bak");
		
		//上传文件
		//FileSystemUtil.putData(fileSystem, "D:\\Android\\test.txt", "/myDir");
		
		//下载文件
		//FileSystemUtil.getData(fileSystem, "/myDir/test.txt", "D:\\test.txt");
		
		//遍历文件系统的某个目录
		//FileSystemUtil.listFile(fileSystem, "/");
		
		//删除文件
		FileSystemUtil.remove(fileSystem, "/dir/liaozhongmin.txt");
	}
}
</code></pre><br><strong><span style="color:#ff0000;">注：远程文件系统的路径，请自行在hadoop/conf/core-site.xml文件中配置：</span></strong><pre><code class="language-html">&lt;?xml version="1.0"?&gt;
&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt;

&lt;!-- Put site-specific property overrides in this file. --&gt;

&lt;configuration&gt;
	 &lt;property&gt;
	         &lt;name&gt;fs.default.name&lt;/name&gt;
		&lt;!--文件系统的路径--&gt;
	         &lt;value&gt;hdfs://liaozhongmin:9000&lt;/value&gt;
	  &lt;/property&gt;

	  &lt;property&gt;
	          &lt;name&gt;hadoop.tmp.dir&lt;/name&gt;
		 &lt;!--文件保存的位置(上传后以块的形式保存)--&gt;
	          &lt;value&gt;/usr/local/hadoop/tmp&lt;/value&gt;
	  &lt;/property&gt;  

&lt;/configuration&gt;</code></pre><br><br><p></p>
            </div>
                </div>