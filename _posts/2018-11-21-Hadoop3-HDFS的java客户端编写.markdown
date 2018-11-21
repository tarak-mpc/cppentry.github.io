---
layout:     post
title:      Hadoop3-HDFS的java客户端编写
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><br></p>
<p>以下载为例，HDFS的java客户端编写主要分为下面4个步骤</p>
<p>1. 通过抽象类FileSystem的get静态方法获取文件系统hdfs的实例</p>
<p>2. 使用fs的open方法，打开hdfs文件系统的输入流</p>
<p>3. 打开本地输出流</p>
<p>4. 使用IOUtils的copy方法将hdfs的输入流拷贝到本地文件系统的输出流</p>
<p><br></p>
<p>在开始写之前，需要先引入HDFS的依赖包，包括hdfs和commons，可以通过maven去引入。<br></p>
<p>需要注意的是，若不做配置，默认拿到的是本地文件系统的实例,可以通过如下两种方法配置</p>
<p>1. conf.set(name,value)<br></p>
<p>2. 将hadoop的配置文件拷贝到项目的classpath下<br></p>
<p>具体代码如下；</p>
<p></p><pre><code class="language-java">Confguration conf = new Confguration();
FileSystem fs = FileSystem.get(conf);

Path src = new Path("hdfs://hostname:9000/filename")
FSDataInputStream is = fs.open(src);

FileOutputStream os = new FileOutputStream("/path-to-save");

IOUtils.copy(is,os);</code></pre><br>
当然，也可以不用这样写，因为HDFS的API已经封装的很好了，可以直接调用：
<p> fs.copyToLocalFile(src, dst);</p>
<p><br></p>
<p>如果是在windows下编写的话，可能会遇到权限问题，原因是当前用户无权限操作，可以这样解决：</p>
<p>1. 添加虚拟机运行参数 -DHADOOP_USER_NAME=hadoop<br></p>
<p>2. 在获取fs实例时，选择重载的get（URI，conf，User）方法<br></p>
<p><br></p>
<p><br></p>
            </div>
                </div>