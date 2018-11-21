---
layout:     post
title:      hive整合hbase
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h3 style="margin-left:0cm;">1.1 hive与hbase版本兼容性</h3>

<p style="margin-left:0cm;"><span style="color:#c00000;">Hive</span><span style="color:#c00000;">版本</span><span style="color:#c00000;"> hive-1.2.1</span><span style="color:#c00000;">、</span><span style="color:#c00000;">hbase</span><span style="color:#c00000;">的版本</span><span style="color:#c00000;">hbase-1.2.1</span></p>

<p style="margin-left:0cm;"><strong><span style="color:#444444;">hbase</span></strong><strong><span style="color:#444444;">与</span></strong><strong><span style="color:#444444;">hive</span></strong><strong><span style="color:#444444;">哪些版本兼容</span></strong><strong><span style="color:#444444;">？</span></strong></p>

<ul><li><span style="color:#444444;">hive0.90</span><span style="color:#444444;">与</span><span style="color:#444444;">hbase0.92</span><span style="color:#444444;">是兼容的</span><span style="color:#444444;">，</span><span style="color:#444444;">早期的</span><span style="color:#444444;">hive</span><span style="color:#444444;">版本与</span><span style="color:#444444;">hbase0.89/0.90</span><span style="color:#444444;">兼容</span><span style="color:#444444;">。</span></li>
	<li><span style="color:#444444;">hive1.x</span><span style="color:#444444;">与</span><span style="color:#444444;">hbase0.98.x</span><span style="color:#444444;">或则更低版本是兼容的。</span></li>
	<li><span style="color:#444444;">hive2.x</span><span style="color:#444444;">与</span><span style="color:#444444;">hbase1.x</span><span style="color:#444444;">及比</span><span style="color:#444444;">hbase1.x</span><span style="color:#444444;">更高版本兼容</span><span style="color:#444444;">。</span></li>
</ul><p style="margin-left:0cm;">如果当下没有合适的版本，就自己编译把。编译过程如下。</p>

<p style="margin-left:0cm;"><strong><span style="color:#444444;">编译：</span></strong></p>

<p style="margin-left:0cm;"><strong><span style="color:#444444;">创建项目</span></strong></p>

<p style="margin-left:0cm;"><span style="color:#444444;">在</span><span style="color:#444444;">eclipse</span><span style="color:#444444;">中创建一个项目。</span><span style="color:#444444;">Java project</span><span style="color:#444444;">即可。</span></p>

<p style="margin-left:0cm;"><span style="color:#444444;">随便起个名，</span><span style="color:#444444;">finish</span><span style="color:#444444;">即可。</span></p>

<p style="margin-left:0cm;"><strong><span style="color:#444444;">导入代码</span></strong></p>

<p style="margin-left:0cm;"><span style="color:#444444;">在创建好的项目上点击右键，选择</span><span style="color:#444444;">Import</span></p>

<p style="margin-left:0cm;"><span style="color:#444444;">选择</span><span style="color:#444444;">General</span><span style="color:#444444;">下的</span><span style="color:#444444;">FileSystem</span></p>

<p style="margin-left:0cm;"><span style="color:#444444;">找到</span><span style="color:#444444;">hive-1.2.1\src\hbase-handler\src\java</span><span style="color:#444444;">目录选择其中的</span><span style="color:#444444;">org</span><span style="color:#444444;">目录导入，这里的hive-1.2.1是源码，源码自行到官网下载。</span></p>

<p style="margin-left:0cm;"><strong><span style="color:#444444;">添加依赖包</span></strong></p>

<p style="margin-left:0cm;"><span style="color:#444444;">导入代码后可以看到很多的错误提示。这时由于没有引入依赖的</span><span style="color:#444444;">jar</span><span style="color:#444444;">包导致的。下面，我们引入</span><span style="color:#444444;">,</span><span style="color:#444444;">需要</span><span style="color:#444444;">hadoop</span><span style="color:#444444;">、</span><span style="color:#444444;">hive</span><span style="color:#444444;">、</span><span style="color:#444444;">hbase</span><span style="color:#444444;">下相关的</span><span style="color:#444444;">lib</span><span style="color:#444444;">包。依赖jar包到hadoop、hive、hbase安装目录下找。</span></p>

<p style="margin-left:0cm;"><span style="color:#444444;">新建</span><span style="color:#444444;">lib</span><span style="color:#444444;">目录，把对应的依赖包，导入</span></p>

<p style="margin-left:0cm;"><span style="color:#444444;">选择</span><span style="color:#444444;">Build Path</span><span style="color:#444444;">点击</span><span style="color:#444444;">Add to Bulid Path </span></p>

<p style="margin-left:0cm;"><span style="color:#444444;">至此可以导出我们需要的</span><span style="color:#444444;">jar</span><span style="color:#444444;">包了。在</span><span style="color:#444444;">org.apache.hadoop.hive.hbase</span><span style="color:#444444;">包上点击右键，选择</span><span style="color:#444444;">export</span></p>

<p> <span style="color:#444444;">选择</span><span style="color:#444444;">java</span><span style="color:#444444;">下的</span><span style="color:#444444;">JAR file</span></p>

<p><img alt="" class="has" height="694" src="https://img-blog.csdnimg.cn/20181029085556430.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MTIyODM2Mg==,size_27,color_FFFFFF,t_70" width="867"></p>

<p><strong>将hive-hbase-handlerjar包放入hive的lib目录下。</strong></p>

<h3>1.2 hive与hbase整合环境配置</h3>

<p><strong>修改hive-site.xml文件，添加配置属性（zookeeper的地址</strong></p>

<pre class="has">
<code>&lt;property&gt;      
&lt;name&gt;hbase.zookeeper.quorum&lt;/name&gt;
&lt;value&gt;itcast01:2181,itcast02:2181,itcast03:2181&lt;/value&gt;
&lt;/property&gt;
</code></pre>

<p style="margin-left:0cm;"><strong>引入hbase依赖包</strong></p>

<p style="margin-left:0cm;">将hbase安装目录下的lib文件夹下的包导入到hive的环境变量中</p>

<p style="margin-left:0cm;">在hive-env.sh 文件中添加</p>

<p style="margin-left:0cm;">export HIVE_CLASSPATH=$HIVE_CLASSPATH:/var/local/hbase/lib/*</p>

<p style="margin-left:0cm;">至此、hive与hbase整合环境准备完成。</p>            </div>
                </div>