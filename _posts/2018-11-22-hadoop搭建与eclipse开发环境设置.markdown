---
layout:     post
title:      hadoop搭建与eclipse开发环境设置
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div>
<h1><span>hadoop</span><span>搭建与</span><span>eclipse</span><span>开发环境设置</span></h1>
<p><span>                                                   
</span><span>  </span></p>
<h2>1.    <span>Windows</span><span>下</span><span>eclipse</span><span>开发环境配置</span></h2>
<h4><span>1.1 </span><span style="color:rgb(51,51,51);">安装开发</span><span style="color:rgb(51,51,51);">hadoop</span><span style="color:rgb(51,51,51);">插件</span></h4>
<p><span>将</span><span>hadoop</span><span>安装包</span><span>hadoop\contrib\eclipse-plugin\hadoop-0.20.2-eclipse-plugin.jar</span><span>拷贝到</span><span>eclipse</span><span>的插件目录</span><span>plugins</span><span>下。</span></p>
<p><span>　　需要注意的是插件版本（及后面开发导入的所有</span><span>jar</span><span>包）与运行的</span><span>hadoop</span><span>一致，否则可能会出现</span><span>EOFException</span><span>异常。</span></p>
<p><span> </span></p>
<p><span>重启</span><span>eclipse</span><span>，打开</span><span>windows-&gt;open perspective-&gt;other-&gt;map/reduce
</span><span>可以看到</span><span>map/reduce</span><span>开发视图。</span></p>
<p><span> </span></p>
<h4><span>1.2 </span><span>设置</span><span style="color:rgb(51,51,51);">连接参数</span></h4>
<p><span>    </span><span>打开</span><span>windows-&gt;show view-&gt;other-&gt; map/reduce Locations</span><span>视图，</span><span style="color:rgb(51,51,51);">在点击大象后弹出的对话框（</span><span style="color:rgb(51,51,51);">General
 tab</span><span style="color:rgb(51,51,51);">）进行参数的添加：</span></p>
<p><span> </span></p>
<p><span>参数说明如下：</span></p>
<p><span>Location name:</span><span>任意</span></p>
<p><span style="color:rgb(51,51,51);">map/reduce master</span><span style="color:rgb(51,51,51);">：与</span><span style="color:rgb(51,51,51);">mapred-site.xml</span><span style="color:rgb(51,51,51);">里面</span><span style="color:rgb(51,51,51);">mapred.job.tracker</span><span style="color:rgb(51,51,51);">设置一致。</span></p>
<p><span style="color:rgb(51,51,51);">DFS master</span><span style="color:rgb(51,51,51);">：与</span><span style="color:rgb(51,51,51);">core-site.xml</span><span style="color:rgb(51,51,51);">里</span><span style="color:rgb(51,51,51);">fs.default.name</span><span style="color:rgb(51,51,51);">设置一致。</span></p>
<p><span style="color:rgb(51,51,51);">User name: </span><span style="color:rgb(51,51,51);">服务器上运行</span><span style="color:rgb(51,51,51);">hadoop</span><span style="color:rgb(51,51,51);">服务的用户名。</span></p>
<p><span style="color:rgb(51,51,51);"> </span></p>
<p><span style="color:rgb(51,51,51);">然后是打开“</span><span style="color:rgb(51,51,51);">Advanced parameters</span><span style="color:rgb(51,51,51);">”设置面板，修改相应参数。上面的参数填写以后，也会反映到这里相应的参数：</span></p>
<p><span style="color:rgb(51,51,51);">主要关注下面几个参数：</span></p>
<p><span style="color:rgb(51,51,51);">fs.defualt.name</span><span style="color:rgb(51,51,51);">：与</span><span style="color:rgb(51,51,51);">core-site.xml</span><span style="color:rgb(51,51,51);">里</span><span style="color:rgb(51,51,51);">fs.default.name</span><span style="color:rgb(51,51,51);">设置一致。</span></p>
<p><span style="color:rgb(51,51,51);">mapred.job.tracker</span><span style="color:rgb(51,51,51);">：与</span><span style="color:rgb(51,51,51);">mapred-site.xml</span><span style="color:rgb(51,51,51);">里面</span><span style="color:rgb(51,51,51);">mapred.job.tracker</span><span style="color:rgb(51,51,51);">设置一致。</span></p>
<p><span style="color:rgb(51,51,51);">dfs.replication</span><span style="color:rgb(51,51,51);">：与</span><span style="color:rgb(51,51,51);">hdfs-site.xml</span><span style="color:rgb(51,51,51);">里面的</span><span style="color:rgb(51,51,51);">dfs.replication</span><span style="color:rgb(51,51,51);">一致。</span></p>
<p><span style="color:rgb(51,51,51);">hadoop.tmp.dir</span><span style="color:rgb(51,51,51);">：与</span><span style="color:rgb(51,51,51);">core-site.xml</span><span style="color:rgb(51,51,51);">里</span><span style="color:rgb(51,51,51);">hadoop.tmp.dir</span><span style="color:rgb(51,51,51);">设置一致。</span></p>
<p><span style="color:rgb(51,51,51);">hadoop.job.ugi</span><span style="color:rgb(51,51,51);">：并不是设置用户名与密码。是用户与组名，所以这里填写</span><span style="color:rgb(51,51,51);">hadoop,hadoop</span><span style="color:rgb(51,51,51);">。</span></p>
<p><span style="color:rgb(51,51,51);">说明：第一次设置的时候可能是没有</span><span style="color:rgb(51,51,51);">hadoop.job.ugi</span><span style="color:rgb(51,51,51);">和</span><span style="color:rgb(51,51,51);">dfs.replication</span><span style="color:rgb(51,51,51);">参数的，不要紧，确认保存。打开</span><span style="color:rgb(51,51,51);">Project
 Explorer</span><span style="color:rgb(51,51,51);">中</span><span style="color:rgb(51,51,51);">DFS</span><span style="color:rgb(51,51,51);">Locations</span><span style="color:rgb(51,51,51);">目录，应该可以年看到文件系统中的结构了。但是在</span><span style="color:rgb(51,51,51);">/hadoop/mapred/system</span><span style="color:rgb(51,51,51);">下却没有查看权限，如下图：</span></p>
<p><span style="color:rgb(51,51,51);"> </span></p>
<p><span style="color:rgb(51,51,51);">而且删除文件的时候也会报错：</span></p>
<p><span style="color:rgb(51,51,51);">这个原因是我使用地本用户</span><span style="color:rgb(51,51,51);">Administrator</span><span style="color:rgb(51,51,51);">（我是用管理员用户登陆来地</span><span style="color:rgb(51,51,51);">windows</span><span style="color:rgb(51,51,51);">系统的）进行远程</span><span style="color:rgb(51,51,51);">hadoop</span><span style="color:rgb(51,51,51);">系统操作，没有权限。</span></p>
<p><span style="color:rgb(51,51,51);"> </span></p>
<p><span style="color:rgb(51,51,51);">此时再打开“</span><span style="color:rgb(51,51,51);">Advanced parameters</span><span style="color:rgb(51,51,51);">”设置面板，应该可以看到</span><span style="color:rgb(51,51,51);">hadoop.job.ugi</span><span style="color:rgb(51,51,51);">了，这个参数默认是本地操作系统的用户名，如果不幸与远程</span><span style="color:rgb(51,51,51);">hadoop</span><span style="color:rgb(51,51,51);">用户不一致，那就要改过来了，将</span><span style="color:rgb(51,51,51);">hadoop</span><span style="color:rgb(51,51,51);">加在第一个，并用逗号分隔。如：</span></p>
<p><span style="color:rgb(51,51,51);"> </span><span style="color:rgb(51,51,51);">　　保存配置后，重新启动</span><span style="color:rgb(51,51,51);">eclipse</span><span style="color:rgb(51,51,51);">。</span><span style="color:rgb(51,51,51);">/hadoop/mapred/system</span><span style="color:rgb(51,51,51);">下就一目了然了，删除文件也</span><span style="color:rgb(51,51,51);">OK</span><span style="color:rgb(51,51,51);">。</span></p>
<h4><span>1.3 </span><span>运行</span><span>hadoop</span><span>程序</span></h4>
<p><span>首先将</span><span>hadoop</span><span>安装包下面的所有</span><span>jar</span><span>包都导到</span><span>eclipse</span><span>工程里。</span></p>
<p><span>然后建立一个类：</span><span>DFSOperator.java</span><span>，该类写了四个基本方法：创建文件，删除文件，把文件内容读为字符串，将字符串写入文件。同时有个</span><span>main</span><span>函数，可以修改测试</span><span>:</span></p>
<p> </p>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top" style="background:#D9D9D9;">
<p><span>package com.kingdee.hadoop;</span></p>
<p><span> </span></p>
<p><span>import java.io.BufferedReader;</span></p>
<p><span>import java.io.IOException;</span></p>
<p><span>import java.io.InputStream;</span></p>
<p><span>import java.io.InputStreamReader;</span></p>
<p><span> </span></p>
<p><span>import org.apache.hadoop.conf.Configuration;</span></p>
<p><span>import org.apache.hadoop.fs.FSDataOutputStream;</span></p>
<p><span>import org.apache.hadoop.fs.FileSystem;</span></p>
<p><span>import org.apache.hadoop.fs.Path;</span></p>
<p><span> </span></p>
<p><span>/**</span></p>
<p><span> * </span></p>
<p><span> * The utilities to operate file on hadoop hdfs.</span></p>
<p><span> * </span></p>
<p><span> * @author luolihui 2011-07-18</span></p>
<p><span> *</span></p>
<p><span> */</span></p>
<p><span>public class DFSOperator {</span></p>
<p><span>private static final String ROOT_PATH = "hdfs:///";</span></p>
<p><span>private static final int BUFFER_SIZE = 4096;</span></p>
<p><span>/**</span></p>
<p><span>* construct.</span></p>
<p><span>*/</span></p>
<p><span>public DFSOperator(){}</span></p>
<p><span> </span></p>
<p><span>     /**</span></p>
<p><span>     * Create a file on hdfs.The root path is /.&lt;br&gt;</span></p>
<p><span>     * for example: DFSOperator.createFile("/lory/test1.txt", true);</span></p>
<p><span>* @param path  the file name to open</span></p>
<p><span>* @param overwrite if a file with this name already exists, then if true, the file will be
</span></p>
<p><span>* @return true if delete is successful else IOException.</span></p>
<p><span>* @throws IOException</span></p>
<p><span>*/</span></p>
<p><span>public static boolean createFile(String path, boolean overwrite) throws IOException</span></p>
<p><span>{</span></p>
<p><span>//String uri = "hdfs://192.168.1.100:9000"; </span>
</p>
<p><span>//FileSystem fs1 = FileSystem.get(URI.create(uri), conf); 
</span></p>
<p><span> </span></p>
<p><span>Configuration conf = new Configuration();</span></p>
<p><span>FileSystem fs = FileSystem.get(conf);</span></p>
<p><span>Path f = new Path(ROOT_PATH + path); </span></p>
<p><span>fs.create(f, overwrite);</span></p>
<p><span>fs.close();</span></p>
<p><span>return true;</span></p>
<p><span>}</span></p>
<p><span>    /**</span></p>
<p><span>     * Delete a file on hdfs.The root path is /. &lt;br&gt;</span></p>
<p><span>     * for example: DFSOperator.deleteFile("/user/hadoop/output", true);</span></p>
<p><span>     * @param path the path to delete</span></p>
<p><span>     * @param recursive  if path is a directory and set to true, the directory is deleted else throws an exception. In case of a file the recursive can be set to either true or false.
</span></p>
<p><span>     * @return true if delete is successful else IOException.</span></p>
<p><span>     * @throws IOException</span></p>
<p><span>     */</span></p>
<p><span>public static boolean deleteFile(String path, boolean recursive) throws IOException</span></p>
<p><span>{</span></p>
<p><span>//String uri = "hdfs://192.168.1.100:9000"; </span>
</p>
<p><span>//FileSystem fs1 = FileSystem.get(URI.create(uri), conf); 
</span></p>
<p><span>Configuration conf = new Configuration();</span></p>
<p><span>FileSystem fs = FileSystem.get(conf);</span></p>
<p><span>Path f = new Path(ROOT_PATH + path); </span></p>
<p><span>fs.delete(f, recursive);</span></p>
<p><span>fs.close();</span></p>
<p><span>return true;</span></p>
<p><span>}</span></p>
<p><span>/**</span></p>
<p><span>* Read a file to string on hadoop hdfs. From stream to string. &lt;br&gt;</span></p>
<p><span>* for example: System.out.println(DFSOperator.readDFSFileToString("/user/hadoop/input/test3.txt"));</span></p>
<p><span>* @param path the path to read</span></p>
<p><span>* @return true if read is successful else IOException.</span></p>
<p><span>* @throws IOException</span></p>
<p><span>*/</span></p>
<p><span>public static String readDFSFileToString(String path) throws IOException</span></p>
<p><span>{</span></p>
<p><span>Configuration conf = new Configuration();</span></p>
<p><span>FileSystem fs = FileSystem.get(conf);</span></p>
<p><span>Path f = new Path(ROOT_PATH + path);</span></p>
<p><span>InputStream in = null;</span></p>
<p><span>String str = null;</span></p>
<p><span>StringBuilder sb = new StringBuilder(BUFFER_SIZE);</span></p>
<p><span>if (fs.exists(f))</span></p>
<p><span>{</span></p>
<p><span>in = fs.open(f);</span></p>
<p><span>BufferedReader bf = new BufferedReader(new InputStreamReader(in));
</span></p>
<p><span>while ((str = bf.readLine()) != null) </span></p>
<p><span>{</span></p>
<p><span>sb.append(str);</span></p>
<p><span>sb.append("\n");</span></p>
<p><span>}</span></p>
<p><span>in.close();</span></p>
<p><span>bf.close();</span></p>
<p><span>fs.close();</span></p>
<p><span>return sb.toString();</span></p>
<p><span>}</span></p>
<p><span>else</span></p>
<p><span>{</span></p>
<p><span>return null;</span></p>
<p><span>}</span></p>
<p><span>}</span></p>
<p><span>/**</span></p>
<p><span>* Write string to a hadoop hdfs file. &lt;br&gt;</span></p>
<p><span>* for example: DFSOperator.writeStringToDFSFile("/lory/test1.txt", "You are a bad man.\nReally!\n");</span></p>
<p><span>* @param path the file where the string to write in.</span></p>
<p><span>* @param string the context to write in a file.</span></p>
<p><span>* @return true if write is successful else IOException.</span></p>
<p><span>* @throws IOException</span></p>
<p><span>*/</span></p>
<p><span>public static boolean writeStringToDFSFile(String path, String string) throws IOException</span></p>
<p><span>{</span></p>
<p><span>Configuration conf = new Configuration();</span></p>
<p><span>FileSystem fs = FileSystem.get(conf);</span></p>
<p><span>FSDataOutputStream os = null;</span></p>
<p><span>Path f = new Path(ROOT_PATH + path);</span></p>
<p><span>os = fs.create(f,true);</span></p>
<p><span>os.writeBytes(string);</span></p>
<p><span>os.close();</span></p>
<p><span>fs.close();</span></p>
<p><span>return true;</span></p>
<p><span>}</span></p>
<p><span> </span></p>
<p><span>public static void main(String[] args) </span></p>
<p><span>{</span></p>
<p><span>try {</span></p>
<p><span>DFSOperator.createFile("/lory/test1.txt", true);</span></p>
<p><span>DFSOperator.deleteFile("/dfs_operator.txt", true);</span></p>
<p><span>DFSOperator.writeStringToDFSFile("/lory/test1.txt", "You are a bad man.\nReally?\n");</span></p>
<p><span>System.out.println(DFSOperator.readDFSFileToString("/lory/test1.txt"));</span></p>
<p><span>} catch (IOException e) {</span></p>
<p><span>// TODO Auto-generated catch block</span></p>
<p><span>e.printStackTrace();</span></p>
<p><span>}</span></p>
<p><span>System.out.println("===end===");</span></p>
<p><span>}</span></p>
<p><span>}</span></p>
</td>
</tr></tbody></table><p><span> </span></p>
<p><span>然后</span><span>Run As</span><span>à</span><span>Run on Hadoop</span><span>à</span><span>Choose an
 exitsing server from the list below</span><span>à</span><span>finish.</span></p>
<p><span> </span></p>
<p><span>结果很简单（那个警告不管）：</span></p>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p><span style="color:rgb(255,0,0);">11/07/16 18:44:32 WARN conf.Configuration: DEPRECATED: hadoop-site.xml found in the classpath. Usage of hadoop-site.xml is deprecated. Instead use core-site.xml, mapred-site.xml and hdfs-site.xml to override properties of
 core-default.xml, mapred-default.xml and hdfs-default.xml respectively</span></p>
<p>You are a bad man.</p>
<p>Really?</p>
<p><span> </span></p>
<p>===end===</p>
</td>
</tr></tbody></table><p><span> </span></p>
<p><span>也可以运行</span><span>hadoop</span><span>自带的</span><span>WorkCount</span><span>程序，找到其源代码导进来，然后设置输入输出参数，然后同样“</span><span>Run
 on hadoop</span><span>”。具体步骤不再示范。</span></p>
<p><span>每“</span><span>Run on hadoop</span><span>”都会在</span><span>workspace\.metadata\.plugins\org.apache.hadoop.eclipse</span><span>下生成临时</span><span>jar</span><span>包。不过第一次需要</span><span>Run
 on hadoop</span><span>，以后只需要点击那运行的绿色按钮了。</span></p>
<p><span><br clear="all"></span></p>
<h2>2.    <span>错误及处理</span></h2>
<h3><span>2.1 </span><span>安全模式问题</span></h3>
<p><span>我在</span><span>eclipse</span><span>上删除</span><span>DFS</span><span>上的文件夹时，出现下面错误：</span></p>
<p><span>错误提示说得也比较明示，是</span><span>NameNode</span><span>在安全模式中，其解决方案也一并给出。</span></p>
<p><span>类似的运行</span><span>hadoop</span><span>程序时，有时候会报以下错误：</span></p>
<p><span>org.apache.hadoop.dfs.SafeModeException: Cannot delete /user/hadoop/input. Name node is in safe mode</span></p>
<p><span> </span></p>
<p><span>解除安全模式：</span></p>
<p><span>bin/hadoop dfsadmin -safemode leave </span></p>
<p><span> </span></p>
<p><span>用户可以通过</span><span>dfsadmin -safemode value  </span><span>来操作安全模式，参数</span><span>value</span><span>的说明如下：</span></p>
<p><span>enter - </span><span>进入安全模式</span></p>
<p><span>leave - </span><span>强制</span><span>NameNode</span><span>离开安全模式</span></p>
<p><span>get -  </span><span>返回安全模式是否开启的信息</span></p>
<p><span>wait - </span><span>等待，一直到安全模式结束。</span></p>
<p><span> </span></p>
<h3><span>2.2 </span><span>开发时报错</span><span>Permission denied</span></h3>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p><span style="color:rgb(0,0,128);">org.apache.hadoop.security.AccessControlException</span><span style="color:rgb(255,0,0);">:
</span><span style="color:rgb(0,0,128);">org.apache.hadoop.security.AccessControlException</span><span style="color:rgb(255,0,0);">: Permission denied: user=Administrator, access=WRITE, inode="test1.txt":hadoop:supergroup:rw-r--r--</span></p>
<p><span style="color:rgb(255,0,0);">at sun.reflect.NativeConstructorAccessorImpl.newInstance0(</span><span style="color:rgb(0,0,128);">Native Method</span><span style="color:rgb(255,0,0);">)</span></p>
<p><span style="color:rgb(255,0,0);">at sun.reflect.NativeConstructorAccessorImpl.newInstance(</span><span style="color:rgb(0,0,128);">NativeConstructorAccessorImpl.java:39</span><span style="color:rgb(255,0,0);">)</span></p>
<p><span style="color:rgb(255,0,0);">at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(</span><span style="color:rgb(0,0,128);">DelegatingConstructorAccessorImpl.java:27</span><span style="color:rgb(255,0,0);">)</span></p>
<p><span style="color:rgb(255,0,0);">at java.lang.reflect.Constructor.newInstance(</span><span style="color:rgb(0,0,128);">Constructor.java:513</span><span style="color:rgb(255,0,0);">)</span></p>
<p><span style="color:rgb(255,0,0);">at org.apache.hadoop.ipc.RemoteException.instantiateException(</span><span style="color:rgb(0,0,128);">RemoteException.java:96</span><span style="color:rgb(255,0,0);">)</span></p>
<p><span style="color:rgb(255,0,0);">at org.apache.hadoop.ipc.RemoteException.unwrapRemoteException(</span><span style="color:rgb(0,0,128);">RemoteException.java:58</span><span style="color:rgb(255,0,0);">)</span></p>
<p><span style="color:rgb(255,0,0);">at org.apache.hadoop.hdfs.DFSClient$DFSOutputStream.&lt;init&gt;(</span><span style="color:rgb(0,0,128);">DFSClient.java:2710</span><span style="color:rgb(255,0,0);">)</span></p>
<p><span style="color:rgb(255,0,0);">at org.apache.hadoop.hdfs.DFSClient.create(</span><span style="color:rgb(0,0,128);">DFSClient.java:492</span><span style="color:rgb(255,0,0);">)</span></p>
<p><span style="color:rgb(255,0,0);">at org.apache.hadoop.hdfs.DistributedFileSystem.create(</span><span style="color:rgb(0,0,128);">DistributedFileSystem.java:195</span><span style="color:rgb(255,0,0);">)</span></p>
<p><span style="color:rgb(255,0,0);">at org.apache.hadoop.fs.FileSystem.create(</span><span style="color:rgb(0,0,128);">FileSystem.java:484</span><span style="color:rgb(255,0,0);">)</span></p>
<p><span style="color:rgb(255,0,0);">at org.apache.hadoop.fs.FileSystem.create(</span><span style="color:rgb(0,0,128);">FileSystem.java:465</span><span style="color:rgb(255,0,0);">)</span></p>
<p><span style="color:rgb(255,0,0);">at org.apache.hadoop.fs.FileSystem.create(</span><span style="color:rgb(0,0,128);">FileSystem.java:372</span><span style="color:rgb(255,0,0);">)</span></p>
<p><span style="color:rgb(255,0,0);">at com.kingdee.hadoop.DFSOperator.createFile(</span><span style="color:rgb(0,0,128);">DFSOperator.java:46</span><span style="color:rgb(255,0,0);">)</span></p>
<p><span style="color:rgb(255,0,0);">at com.kingdee.hadoop.DFSOperator.main(</span><span style="color:rgb(0,0,128);">DFSOperator.java:134</span><span style="color:rgb(255,0,0);">)</span></p>
</td>
</tr></tbody></table><p><span> </span></p>
<p><span>解决方法是，在</span><span style="color:rgb(51,51,51);">“</span><span style="color:rgb(51,51,51);">Advanced parameters</span><span style="color:rgb(51,51,51);">”设置面板，设置</span><span style="color:rgb(51,51,51);">hadoop.job.ugi</span><span style="color:rgb(51,51,51);">参数，将</span><span style="color:rgb(51,51,51);">hadoop</span><span style="color:rgb(51,51,51);">用户加上去。</span></p>
<p><span>变为：</span></p>
<p><span> </span></p>
<p><span>然后重新在运行中</span><span>”Run on hadoop”</span><span>。</span></p>
<p><span>另一方法是改变要操作的文件的权限。</span></p>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top" style="background:#D9D9D9;">
<p><span style="color:rgb(255,0,0);">Permission denied: user=Administrator, access=WRITE, inode="test1.txt":hadoop:supergroup:rw-r--r--</span></p>
</td>
</tr></tbody></table><p><span>　　上面的意思是：</span><span>test1.txt</span><span>文件的访问权限是</span><span>rw-r--r--</span><span>，归属组是</span><span>supergroup</span><span>，归属用户是</span><span>hadoop</span><span>，现在使用</span><span>Administrator</span><span>用户对</span><span>test1.txt</span><span>文件进行</span><span>WRITE</span><span>方式访问，被拒绝了。</span></p>
<p><span>所以可以改变下</span><span>test1.txt</span><span>文件的访问权限：</span></p>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top" style="background:#D9D9D9;">
<p><span>$ hadoop fs –chmod 777 /lory/test1.txt</span></p>
<p><span>$ hadoop fs –chmod 777 /lory     #</span><span>或者上一级文件夹</span></p>
</td>
</tr></tbody></table><p><span>　　当然使用</span><span>-chown</span><span>命令也可以。</span></p>
</div>
            </div>
                </div>