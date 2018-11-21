---
layout:     post
title:      windows搭建hadoop环境
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/engerla/article/details/83550322				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>1、配置java环境：<a href="https://blog.csdn.net/engerla/article/details/83550450" rel="nofollow">java环境配置教程</a><br>
2、去官网下载hadoop：<a href="https://hadoop.apache.org/releases.html" rel="nofollow">hadoop官网下载</a><br>
3、将下载的hadoop解压到某个目录（自己随便），我这里解压到D盘<br>
<img src="https://img-blog.csdnimg.cn/20181030224856740.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2VuZ2VybGE=,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"><br>
4、配置环境变量<br>
1)新建环境变量HADOOP_HOME，值为hadoop的解压路径<br>
<img src="https://img-blog.csdnimg.cn/20181030225105396.png" alt="在这里插入图片描述"><br>
2)	在环境变量path中添加hadoop的bin目录<br>
<img src="https://img-blog.csdnimg.cn/2018103022531868.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2VuZ2VybGE=,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"><br>
5、	解压hadoop的依赖文件，并替换hadoop目录下的bin目录（注意版本）<br>
<img src="https://img-blog.csdnimg.cn/20181030225435723.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2VuZ2VybGE=,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"><br>
6、	再将hadoop依赖文件中的hadoop.dll复制到C:\Windows\System32，解决依赖性问题<br>
<img src="https://img-blog.csdnimg.cn/20181030225509575.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2VuZ2VybGE=,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"><br>
7、	修改D:\hadoop-2.7.5\etc\hadoop \hadoop-env.cmd配置文件（你的文件在哪就去哪找）<br>
<img src="https://img-blog.csdnimg.cn/2018103022553942.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2VuZ2VybGE=,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"><br>
8、	打开cmd命令窗口，输入hadoop version，若返回版本信息则hadoop环境搭建成功<br>
<img src="https://img-blog.csdnimg.cn/20181030225607242.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2VuZ2VybGE=,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>