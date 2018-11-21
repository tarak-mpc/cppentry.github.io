---
layout:     post
title:      javaAPI操作hdfs文件系统环境准备
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/freefish_yzx/article/details/76474648				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1>解决javaAPI操作hdfs文件系统问题</h1>
<div><span style="font-family:Arial;font-size:14px;line-height:26px;">解决正常运行hadoop hdfs javaAPI</span><br></div>
<div>
<pre>java.lang.NullPointerException</pre>
<span></span>at java.lang.ProcessBuilder.start(ProcessBuilder.java:1012)<br><h1><span></span>at org.apache.hadoop.util.Shell.runCommand(Shell.java:482)</h1>
<span></span>at org.apache.hadoop.util.Shell.run(Shell.java:455)<br><span></span>at org.apache.hadoop.util.Shell$ShellCommandExecutor.execute(Shell.java:715)<br><span></span>at org.apache.hadoop.util.Shell.execCommand(Shell.java:808)<br><span></span>at org.apache.hadoop.util.Shell.execCommand(Shell.java:791)<br><span></span>at org.apache.hadoop.fs.RawLocalFileSystem.setPermission(RawLocalFileSystem.java:659)<br><span></span>at org.apache.hadoop.fs.FilterFileSystem.setPermission(FilterFileSystem.java:490)<br><span></span>at org.apache.hadoop.fs.ChecksumFileSystem.create(ChecksumFileSystem.java:462)<br><span></span>at org.apache.hadoop.fs.ChecksumFileSystem.create(ChecksumFileSystem.java:428)<br><span></span>at org.apache.hadoop.fs.FileSystem.create(FileSystem.java:908)<br><span></span>at org.apache.hadoop.fs.FileSystem.create(FileSystem.java:889)<br><span></span>at org.apache.hadoop.fs.FileSystem.create(FileSystem.java:786)<br><span></span>at org.apache.hadoop.fs.FileUtil.copy(FileUtil.java:365)<br><span></span>at org.apache.hadoop.fs.FileUtil.copy(FileUtil.java:338)<br><span></span>at org.apache.hadoop.fs.FileUtil.copy(FileUtil.java:289)<br><span></span>at org.apache.hadoop.fs.LocalFileSystem.copyFromLocalFile(LocalFileSystem.java:82)<br><span></span>at org.apache.hadoop.fs.FileSystem.copyFromLocalFile(FileSystem.java:1838)<br><span></span>at cn.yzx.bigdata.hdfs.HdfsClientDemo.testUpload(HdfsClientDemo.java:23)<br><span></span>at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)<br><span></span>at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)<br><span></span>at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)<br><span></span>at java.lang.reflect.Method.invoke(Method.java:497)<br><span></span>at org.junit.runners.model.FrameworkMethod$1.runReflectiveCall(FrameworkMethod.java:47)<br><span></span>at org.junit.internal.runners.model.ReflectiveCallable.run(ReflectiveCallable.java:12)<br><span></span>at org.junit.runners.model.FrameworkMethod.invokeExplosively(FrameworkMethod.java:44)<br><span></span>at org.junit.internal.runners.statements.InvokeMethod.evaluate(InvokeMethod.java:17)<br><span></span>at org.junit.internal.runners.statements.RunBefores.evaluate(RunBefores.java:26)<br><span></span>at org.junit.runners.ParentRunner.runLeaf(ParentRunner.java:271)<br><span></span>at org.junit.runners.BlockJUnit4ClassRunner.runChild(BlockJUnit4ClassRunner.java:70)<br><span></span>at org.junit.runners.BlockJUnit4ClassRunner.runChild(BlockJUnit4ClassRunner.java:50)<br><span></span>at org.junit.runners.ParentRunner$3.run(ParentRunner.java:238)<br><span></span>at org.junit.runners.ParentRunner$1.schedule(ParentRunner.java:63)<br><span></span>at org.junit.runners.ParentRunner.runChildren(ParentRunner.java:236)<br><span></span>at org.junit.runners.ParentRunner.access$000(ParentRunner.java:53)<br><span></span>at org.junit.runners.ParentRunner$2.evaluate(ParentRunner.java:229)<br><span></span>at org.junit.runners.ParentRunner.run(ParentRunner.java:309)<br><span></span>at org.eclipse.jdt.internal.junit4.runner.JUnit4TestReference.run(JUnit4TestReference.java:86)<br><span></span>at org.eclipse.jdt.internal.junit.runner.TestExecution.run(TestExecution.java:38)<br><span></span>at org.eclipse.jdt.internal.junit.runner.RemoteTestRunner.runTests(RemoteTestRunner.java:459)<br><span></span>at org.eclipse.jdt.internal.junit.runner.RemoteTestRunner.runTests(RemoteTestRunner.java:678)<br><span></span>at org.eclipse.jdt.internal.junit.runner.RemoteTestRunner.run(RemoteTestRunner.java:382)<br><span></span>at org.eclipse.jdt.internal.junit.runner.RemoteTestRunner.main(RemoteTestRunner.java:192)<br><br><br></div>
<h1>1.材料</h1>
<div>hadoop-2.6.5-centos-6.7.tar.gz<br></div>
<div>无jar版windows平台hadoop-2.6.1.zip<br></div>
<h1>2.new Java  Project</h1>
<div><img src="https://img-blog.csdn.net/20170801082339959?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZnJlZWZpc2hfeXp4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<h1>3.hdfsjar</h1>
<div>解压hadoop-2.6.5-centos-6.7.tar.gz  </div>
<div>hadoop-2.6.5\share\hadoop\hdfs  下把hadoop-hdfs-2.6.5.jar 和当前目录lib下所有都导入hdfsjar<br></div>
<div>hadoop-2.6.5\share\hadoop\common 下把hadoop-common-2.6.5.jar 和当前目录lib下所有都导入hdfsjar<br></div>
<h1>4. 代码</h1>
<div><img src="https://img-blog.csdn.net/20170801083537426?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZnJlZWZpc2hfeXp4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<div><br></div>
<h1>5.跑代码发现问题</h1>
<div><img src="https://img-blog.csdn.net/20170801083855568?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZnJlZWZpc2hfeXp4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<div><br></div>
<h1>6配置环境变量</h1>
<div><img src="https://img-blog.csdn.net/20170801085612067?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZnJlZWZpc2hfeXp4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<div><img src="https://img-blog.csdn.net/20170801085822161?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZnJlZWZpc2hfeXp4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><img src="https://img-blog.csdn.net/20170801090323635?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZnJlZWZpc2hfeXp4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><img src="https://img-blog.csdn.net/20170801090451709?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZnJlZWZpc2hfeXp4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><br></div>
<h1>7重启eclipse再跑代码</h1>
<div><img src="https://img-blog.csdn.net/20170801105618787?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZnJlZWZpc2hfeXp4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<h1>8.发现并没有成功</h1>
<div>
<h2 style="font-family:Arial;line-height:26px;"><span style="font-size:14px;">1.执行成功,但没有上传到hadoop hdfs文件下</span></h2>
<div><span style="font-size:14px;"></span>
<div style="font-family:Arial;font-size:14px;line-height:26px;"><span></span>解决方案:</div>
<div style="font-family:Arial;font-size:14px;line-height:26px;">
<p>
<span style="font-family:'宋体';"><span></span>配置</span><a href="http://lib.csdn.net/base/hadoop" rel="nofollow" class="replace_word" title="Hadoop知识库" style="color:rgb(223,52,52);text-decoration:none;font-weight:bold;">Hadoop</a><span style="font-family:'宋体';">的文件系统地址</span></p>
</div>
<br></div>
<span></span>添加代码</div>
<div><span><img src="https://img-blog.csdn.net/20170801114547040?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZnJlZWZpc2hfeXp4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></div>
<div><span></span>
<h2 style="font-family:Arial;line-height:26px;"><span style="font-size:14px;"><span></span>2.权限问题:用户名不能是Administrator</span></h2>
<div><span style="font-size:14px;"><span></span></span>
<div style="font-family:Arial;font-size:14px;line-height:26px;"><span></span>解决方案:</div>
<div style="font-family:Arial;font-size:14px;line-height:26px;"><span></span>设置hadoop用户名</div>
<div style="font-family:Arial;font-size:14px;line-height:26px;"><span><img src="https://img-blog.csdn.net/20170801114856586?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZnJlZWZpc2hfeXp4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></div>
<div style="font-family:Arial;font-size:14px;line-height:26px;"><span><span style="font-family:Arial;font-size:14px;line-height:26px;"><span></span>设置hadoop名称</span><br></span></div>
<div style="font-family:Arial;font-size:14px;line-height:26px;"><span><span style="font-family:Arial;font-size:14px;line-height:26px;"><span></span>点 run  Configuration</span></span></div>
<div style="font-family:Arial;font-size:14px;line-height:26px;"><span><span style="font-family:Arial;font-size:14px;line-height:26px;"><span><img src="https://img-blog.csdn.net/20170801115130339?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZnJlZWZpc2hfeXp4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></span></span></div>
<div style="font-family:Arial;font-size:14px;line-height:26px;"><span><span style="font-family:Arial;font-size:14px;line-height:26px;"><span><span style="font-family:Arial;font-size:14px;line-height:26px;">以上问题解决,就可以正常运行hadoop
 hdfs javaAPI了!!!</span><br></span></span></span></div>
<div style="font-family:Arial;font-size:14px;line-height:26px;"><span><span style="font-family:Arial;font-size:14px;line-height:26px;"><span><span></span></span></span></span></div>
<div style="font-family:Arial;font-size:14px;line-height:26px;"><span><span style="font-family:Arial;font-size:14px;line-height:26px;"><span></span></span></span></div>
</div>
</div>
<div><br></div>
<div><span></span></div>
            </div>
                </div>