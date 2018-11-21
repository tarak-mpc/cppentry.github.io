---
layout:     post
title:      大数据之五 hadoop HDFS HA集群客户端+eclipse（java API）配置
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>搭建好HDFS HA集群后，我们来配置客户端，用来操作HDFS HA集群<br>
搭建HDFS HA集群参看 <a href="https://blog.csdn.net/xingyao231/article/details/83929060" rel="nofollow">大数据之四 hadoop HDFS HA 高可用的完全分布式</a></p>
<h1><a id="eclipsejava_API_3"></a>客户端+eclipse（java API）配置</h1>
<ol>
<li>首先我们选择一台客户机，任意选择，只要能与集群通信即可，这里就使用真机</li>
<li>将集群中配置好的 hadoop 安装包拷贝到真机上</li>
<li>配置 hadoop 的环境变量<br>
HADOOP_HOME：hadoop安装包的位置<br>
PATH：%HADOOP_HOME%\bin<br>
HADOOP_USER_NAME：登录集群的用户名称，只要是可以登录集群的用户名就可以，这里配置为root<br>
<img src="https://img-blog.csdn.net/20181013105132764?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpbmd5YW8yMzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="HADOOP_HOME"><br>
<img src="https://img-blog.csdn.net/20181013105232789?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpbmd5YW8yMzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="HADOOP_USER_NAME"></li>
<li>将hadoop的插件放到eclipse的dropins\plugins\ 目录下，如果没有 plugins目录则手动创建。插件可到github下载对应版本。<br>
如果是按照<a href="https://blog.csdn.net/xingyao231/article/details/83929060" rel="nofollow">大数据之四 hadoop HDFS HA 高可用的完全分布式</a>搭建的集群环境，则插件使用 2.6.0 版本<img src="https://img-blog.csdn.net/20181013115159565?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpbmd5YW8yMzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"><br>
Github地址：<a href="https://github.com/winghc/hadoop2x-eclipse-plugin" rel="nofollow">https://github.com/winghc/hadoop2x-eclipse-plugin</a></li>
<li>启动eclipse，点击菜单栏的window–&gt;Preferences 进入Preferences选项窗口，此时会有一个Hadoop Map/Reduce，点击如下图，选择hadoop安装包的位置，点击Apply–&gt;OK<br>
<img src="https://img-blog.csdn.net/2018101311104382?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpbmd5YW8yMzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="hadoop插件配置"></li>
<li>点击菜单栏的window–&gt;Show View–&gt;other进入Show View 窗口如下。在搜索栏输入Map/Reduce，选中黄色小象Map/Reduce Location，点击OK<br>
<img src="https://img-blog.csdn.net/20181013111558332?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpbmd5YW8yMzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="show view"></li>
<li>此时下方控制台窗口便会出现Map/Reduce Location的窗口，在空白处右击，选择 New Hadoop location<br>
<img src="https://img-blog.csdn.net/20181013112032866?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpbmd5YW8yMzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="Map/Reduce Location"></li>
<li>按下图进行配置，点击finish<br>
<img src="https://img-blog.csdn.net/2018101311283282?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpbmd5YW8yMzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="config"></li>
<li>配置完成，project窗口中显示如下，连接成功<br>
<img src="https://img-blog.csdn.net/20181013113120417?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpbmd5YW8yMzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="DFS"></li>
<li>如果要在项目中操作HDFS集群，则需要导入依赖jar包和配置文件。<br>
将hadoop安装包中share\hadoop目录下 common,hdfs,tools三个文件夹中的jar包及其下lib文件夹中的jar包整合并导入项目。<br>
<img src="https://img-blog.csdn.net/20181013115800477?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpbmd5YW8yMzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"><br>
<img src="https://img-blog.csdn.net/20181013120127946?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpbmd5YW8yMzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></li>
<li>将加入的jar包加入classpath，通过build path。<br>
<img src="https://img-blog.csdn.net/20181013120732401?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpbmd5YW8yMzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></li>
<li>创建Source Folder文件，将hadoop安装包中的hdfs-site.xml和core-site.xml拷入其中，然后就可以在项目中操作HDFS集群了<br>
<img src="https://img-blog.csdn.net/20181020091959752?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpbmd5YW8yMzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="source folder"><br>
<img src="https://img-blog.csdn.net/20181020092231683?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpbmd5YW8yMzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="source"></li>
</ol>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>