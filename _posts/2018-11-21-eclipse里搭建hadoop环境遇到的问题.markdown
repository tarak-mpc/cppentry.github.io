---
layout:     post
title:      eclipse里搭建hadoop环境遇到的问题
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
0.依赖的jar包<br>
share\hadoop\common\hadoop-common-2.8.0.jar<br>
share\hadoop\common\lib\全部<br>
share\hadoop\hdfshadoop-hdfs-2.8.0.jar<br>
share\hadoop\hdfs\lib\全部<br>
重复的部分覆盖即可；<br><br>
1.Caused by: java.lang.IllegalArgumentException: Wrong FS: hdfs:/, expected: file:///  <br>
        at org.apache.hadoop.fs.FileSystem.checkPath(FileSystem.java:642)  <br><br>
解决：需要将core-site.xml和hdfs-site.xml文件拷贝到eclipse中工程的src目录下<br><br>
2.log4j:WARN See http://logging.apache.org/log4j/1.2/faq.html#noconfig for more info.<br>
Exception in thread "main" java.lang.IllegalArgumentException: java.net.UnknownHostException: hserver1<br><br>
解决：如果配置的是系统名称，如：hserver1,将配置文件中的系统名称改成ip，如：192.168.229.141<br><br>
3.Exception in thread "main" org.apache.hadoop.security.AccessControlException: Permission denied: user=luxuefeng, access=WRITE, inode="/":root:supergroup:drwxr-xr-x<br><span></span>at org.apache.hadoop.hdfs.server.namenode.FSPermissionChecker.check(FSPermissionChecker.java:310)<br><span></span><br>
解决：需要设置window用户访问hadoop目录的权限<br>
run as中选择run configration,配置Arguments参数,将-DHADOOP_USER_NAME设置为linux环境中配置时的用户<br>
-DHADOOP_USER_NAME=root<br>
但这样每个方法都要配置，<br>
终极解决：初始化时指定用户<br><span></span>public void init() throws IOException, InterruptedException, URISyntaxException{<br><span></span>Configuration conf =<span>
</span>new Configuration();<br><span></span>fs = FileSystem.get(new URI("hdfs://192.168.229.141:9000/"),conf,"root");<br><span></span>}<br><span></span><br>
4.java.lang.RuntimeException: java.io.FileNotFoundException: java.io.FileNotFoundException: HADOOP_HOME and hadoop.home.dir are unset. -see https://wiki.apache.org/hadoop/WindowsProblems<br><span></span>at org.apache.hadoop.util.Shell.getWinUtilsPath(Shell.java:716)<br><span></span>测试下载方法时遇到的，<br><span></span>/**<br><span></span>* 下载文件<br><span></span>* @throws IOException <br><span></span>* */<br><span></span>@Test<br><span></span>public void download() throws IOException{<br><span></span>//下载时，源路径和目标路径刚好相反<br><span></span>Path local = new Path("hdfs://192.168.229.141:9000/baidu_url.txt");<br><span></span>Path dst = new Path("G:/baidu_url3.txt");<br><span></span><br><span></span>fs.copyToLocalFile(local, dst);<br><span></span>}<br><span></span><br>
Problems running Hadoop on Windows<br>
Hadoop requires native libraries on Windows to work properly -that includes to access the file:// filesystem, where Hadoop uses some Windows APIs to implement posix-like file access permissions.<br>
This is implemented in HADOOP.DLL and WINUTILS.EXE.<br>
In particular, %HADOOP_HOME%\BIN\WINUTILS.EXE must be locatable.<br>
If it is not, Hadoop or an application built on top of Hadoop will fail.<br>
How to fix a missing WINUTILS.EXE<br>
You can fix this problem in two ways<br>
Install a full native windows Hadoop version. The ASF does not currently (September 2015) release such a version; releases are available externally.<br>
Or: get the WINUTILS.EXE binary from a Hadoop redistribution. There is a repository of this for some Hadoop versions on github.<br>
Then<br>
Set the environment variable %HADOOP_HOME% to point to the directory above the BIN dir containing WINUTILS.EXE.<br>
Or: run the Java process with the system property hadoop.home.dir set to the home directory.<br><br>
解决：将安装在cent os中的hadoop.tar.gz解压在windows一份。<br>
然后重点在这里，仅仅解压缩了这个hadoop的包还不够，还需要这俩东西：hadoop.dll和winutils.exe下载链接及其附属(hadoop.exp、hadoop.lib、hadoop.pdb、libwinutils.lib、winutils.pdb)。缺少会报错，我在报错前已经把这俩及其相关的东西都放上了，所以不知会报啥错。再说一点如果版本不一致会报java.lang.UnsatisfiedLinkError:org.apache.hadoop.io.nativeio.NativeIO$Windows.createDirectoryWithMode0(Ljava/lang/String;I)V错误 <br>
在你的环境变量中的系统变量中配置了HADOOP_HOME并且指向hadoop程序包的本目录即可(如我本机就是D:\hadoop-2.8.0)，并且在系统变量的path中附加了%HADOOP_HOME%\bin及%HADOOP_HOME%\sbin就能在你的windows上运行hadoop程序了，然后重启电脑。<br><span></span><br><br>            </div>
                </div>