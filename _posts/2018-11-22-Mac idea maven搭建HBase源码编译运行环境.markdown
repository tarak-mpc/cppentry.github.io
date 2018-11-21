---
layout:     post
title:      Mac idea maven搭建HBase源码编译运行环境
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u012365810/article/details/84201754				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1><a id="_0"></a>准备工作</h1>
<h2><a id="1HBase_1"></a>1.HBase源码下载</h2>
<p>地址：<a href="http://www.apache.org/dyn/closer.cgi/hbase/" rel="nofollow">http://www.apache.org/dyn/closer.cgi/hbase/</a><br>
<img src="https://img-blog.csdnimg.cn/20181118162000414.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTIzNjU4MTA=,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"><br>
常用的地址有<br>
<a href="http://mirror.bit.edu.cn/apache/hbase/" rel="nofollow">http://mirror.bit.edu.cn/apache/hbase/</a><br>
<a href="http://mirrors.hust.edu.cn/apache/hbase/" rel="nofollow">http://mirrors.hust.edu.cn/apache/hbase/</a><br>
<a href="http://mirrors.shu.edu.cn/apache/hbase/" rel="nofollow">http://mirrors.shu.edu.cn/apache/hbase/</a> 等<br>
选择合适的版本下载即可。</p>
<h2><a id="2maven_9"></a>2.maven</h2>
<h3><a id="_10"></a>安装和环境变量配置，网上很多，直接参考链接配：</h3>
<p>mac下安装Maven和配置环境变量<br>
<a href="https://www.cnblogs.com/assasion/p/8516445.html" rel="nofollow">https://www.cnblogs.com/assasion/p/8516445.html</a></p>
<h3><a id="_14"></a>阿里云镜像配置**【重要】**</h3>
<p>放到maven安装目录/conf/settings.xml文件里的<code>&lt;mirrors&gt;&lt;/mirrors&gt;</code>标签下：</p>
<pre><code>&lt;mirror&gt;
	&lt;id&gt;nexus-aliyun&lt;/id&gt;
	&lt;mirrorOf&gt;central&lt;/mirrorOf&gt;
	&lt;name&gt;Nexus aliyun&lt;/name&gt;
	&lt;url&gt;http://maven.aliyun.com/nexus/content/groups/public&lt;/url&gt;
&lt;/mirror&gt;
</code></pre>
<h3><a id="pomxmljarrepository_24"></a>屏蔽源码pom.xml文件里指定jar包的源repository：</h3>
<p>由于hbase源码的pom.xml文件里很多jar包都指定了repository，只是配置阿里云镜像仍然会导致编译时下载很慢或假死的现象。<br>
覆盖repository指定，全部用阿里云Maven镜像下载。将上述文件修改：</p>
<pre><code> &lt;mirror&gt;
    	&lt;id&gt;nexus-aliyun&lt;/id&gt;
    	&lt;mirrorOf&gt;*&lt;/mirrorOf&gt;
    	&lt;name&gt;Nexus aliyun&lt;/name&gt;
    	&lt;url&gt;http://maven.aliyun.com/nexus/content/groups/public&lt;/url&gt;
 &lt;/mirror&gt;
</code></pre>
<h2><a id="3_36"></a>3.编译源码</h2>
<p>进入源码根目录，执行：mvn clean install -DskipTests<br>
等待编译结束后，即可导入hbase到idea中：<br>
<img src="https://img-blog.csdnimg.cn/20181118154859569.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTIzNjU4MTA=,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<h2><a id="4idea_40"></a>4.idea导入源码</h2>
<p>使用idea将代码导入<br>
File -&gt; Open -&gt; pom.xml<br>
没有截图，一路点击确定即可。</p>
<h1><a id="HBase_Master_45"></a>启动HBase Master服务器端</h1>
<p>配置run/debug configuration<br>
启动时入口类：HMaster<br>
参数：start<br>
<img src="https://img-blog.csdnimg.cn/20181118145701411.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTIzNjU4MTA=,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"><br>
VM Options：日志文件<br>
<img src="https://img-blog.csdnimg.cn/20181118145637617.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTIzNjU4MTA=,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"><br>
修改run/debug configuration下的VM Options配置信息</p>
<pre><code>-Dlog4j.configuration=file:/Users/aaa/Downloads/SourceCodeAnalysis/hbase/hbase-1.1.13/conf/log4j.properties
</code></pre>
<p>报错：提示版本有问题，配置<code>hbase-default.xml</code>文件中的版本检查skip为true即可<br>
<img src="https://img-blog.csdnimg.cn/20181118154625845.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTIzNjU4MTA=,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<p>报错：提示zookeeper端口错误<br>
<img src="https://img-blog.csdnimg.cn/20181118155202377.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTIzNjU4MTA=,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"><br>
原因是本地已经安装运行zookeeper，hbase默认会再启动一个zookeeper进程，关于zookeeper不展开了。这里的解决办法是将本地zookeeper关闭，mac命令是：zkServer stop</p>
<p>再次启动项目可以看到master启动成功<br>
<img src="https://img-blog.csdnimg.cn/20181118155922298.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTIzNjU4MTA=,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"><br>
浏览器地址栏输入:<code>localhost:16010</code><br>
显示如下界面表示安装成功！<br>
<img src="https://img-blog.csdnimg.cn/2018111816014085.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTIzNjU4MTA=,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"><br>
<img src="https://img-blog.csdnimg.cn/20181118160335994.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTIzNjU4MTA=,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<h1><a id="HBase_shell_70"></a>启动HBase shell命令行</h1>
<p>参考上述配置和下面的链接中的配置，启动shell，输入<code>list</code>测试下：<br>
<img src="https://img-blog.csdnimg.cn/20181118161649227.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTIzNjU4MTA=,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<p>参考资料：<br>
《hbase本地调试环境搭建》<br>
<a href="https://www.cnblogs.com/superhedantou/p/5567787.html" rel="nofollow">https://www.cnblogs.com/superhedantou/p/5567787.html</a><br>
《HBase 在linux环境下本地编译及调试》<br>
<a href="https://blog.csdn.net/huoshanbaofa123/article/details/75008193" rel="nofollow">https://blog.csdn.net/huoshanbaofa123/article/details/75008193</a></p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>