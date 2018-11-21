---
layout:     post
title:      hadoop CLASSNAME的执行
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-family:SimSun;"><span></span>官方帮助中提示，如果需要执行java类，只需要执行命令：hadoop CLASSNAME(如：hadoop org.apache.hadoop.test.HelloWorld)即可，但是，实际上真正运行，需要配置$HADOOP_CLASSPATH变量。举例说明：</span></p>
<p><span style="font-family:SimSun;"><span></span>export /hadoop/hadoop/*.jar:$APP_HOME/bin</span></p>
<p><span><span style="font-family:SimSun;">hadoop org.apache.hadoop.test.HelloWorld</span></span></p>
<p><span><span style="font-family:SimSun;"><span></span>其中，/hadoop/hadoop/*.jar是hadoop的一些基本jar包； $APP_HOME/bin是指编译文件的路径。</span></span></p>
<p><span><span style="font-family:SimSun;"><br></span></span></p>
<p><span style="font-family:SimSun;">执行后会发现，仍然存在问题，提示map和reducer找不到。而且会有一个警告： WARN mapred.JobClient: No job jar file set.  User classes may not be found. See JobConf(Class) or JobConf#setJar(String)。查看代码，命名存在 job.setJarByClass（HelloWorld.class）。经过一番纠结，终于发现，原来使用class执行时，我们的job
 jar包在程序中才打包的，而HelloWorld.class的类加载器是AppClassLoader，运行后我们无法改变它的搜索路径，所以使用setJarByClass时无法设置作业jar包的。我们必须使用JobConf里面的setJar来直接设置作业jar包</span></p>
<p><span><span style="font-family:SimSun;"><br></span></span></p>
            </div>
                </div>