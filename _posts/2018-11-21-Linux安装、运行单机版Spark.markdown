---
layout:     post
title:      Linux安装、运行单机版Spark
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>一. 安装JDK，可参考：<a href="https://blog.csdn.net/yzh_1346983557/article/details/81509329" rel="nofollow">Linux（CentOs7）安装OpenJDK1.8</a>。</p>

<p>二. 安装spark。</p>

<p>      说明：安装单机版spark只需要安装jdk和spark就可以了，因为spark内置的有scala，可不单独安装spark依赖的scala。这是网上的说法，我因为安装了scala就没有再去卸载。</p>

<p>2.1 到spark官网下载Spark压缩包及解压：</p>

<p>官网下载地址：<a href="http://spark.apache.org/downloads.html" rel="nofollow">http://spark.apache.org/downloads.html</a></p>

<p><img alt="" class="has" height="370" src="https://img-blog.csdn.net/20180813101046649?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l6aF8xMzQ2OTgzNTU3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="796"></p>

<p>我下载的是最新的2.3.1版本的。</p>

<p>下载完成后使用工具上传到Linux服务器下，然后解压压缩包。</p>

<p>解压的Linux命令：tar   -zxvf  spark-2.3.1-bin-hadoop2.7.tgz</p>

<p>为了操作方便，我将解压得到的spark-2.3.1-bin-hadoop2.7文件夹改名为spark-2.3.1了：mv spark-2.3.1-bin-hadoop2.7 spark-2.3.1</p>

<p>2.2 配置环境变量：</p>

<p>2.2.1 配置系统的环境变量：</p>

<p>          进入/etc/profile目录下：vim  /etc/profile，会提示文件已存在，输入“e”进入编辑模式即可，</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180813102127514?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l6aF8xMzQ2OTgzNTU3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p>然后在文件的最后加上如下配置：</p>

<p>#Spark<br>
export SPARK_HOME=/opt/spark-2.3.1<br>
export PATH=$PATH:$SPARK_HOME/bin</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180813102654368?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l6aF8xMzQ2OTgzNTU3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p>然后键盘按下“Esc”，输入“：”冒号加“wq”保存退出vim编辑模式，最后：source  /etc/profile 使文件修改生效。</p>

<p>2.2.2 配置spark的配置文件：</p>

<p>2.2.2.1 新建spark-env.sh配置文件（spark环境配置文件）：</p>

<p>            进入spark的conf目录下：cd  /opt/spark-2.3.1/conf/，</p>

<p>           copy一份spark-env.sh：cp  spark-env.sh.template spark-env.sh</p>

<p>          然后进入vim编辑模式：vim  spark-env.sh，会提示文件已存在，输入“e”进入编辑模式即可。</p>

<p>         在文件的最后加上如下配置：</p>

<p>#export SCALA_HOME=/opt/scala-2.13.0<br>
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.181-3.b13.el7_5.x86_64  #这里是你jdk的安装路径<br>
export SPARK_HOME=/opt/spark-2.3.1<br>
export SPARK_MASTER_IP=XXX.XX.XX.XXX  #将这里的xxx改为自己的Linux的ip地址<br>
#export SPARK_EXECUTOR_MEMORY=512M<br>
#export SPARK_WORKER_MEMORY=1G<br>
#export master=spark://XXX.XX.XX.XXX:7070</p>

<p>        最后“Esc”，输入“：”加“wq”保存退出vim编辑模式，最后source spark-env.sh使修改生效。</p>

<p>2.2.2.2 新建slaves配置文件（子节点配置文件）：</p>

<p>            进入spark的conf目录下：cd  /opt/spark-2.3.1/conf/，</p>

<p>           copy一份slaves：cp  slaves.template slaves</p>

<p>          然后进入vim编辑模式：vim  slaves，会提示文件已存在，输入“e”进入编辑模式即可。</p>

<p>         在文件的最后加上如下配置：</p>

<p>localhost</p>

<p>         最后“Esc”，输入“：”加“wq”保存退出vim编辑模式，最后source slaves使修改生效。</p>

<p>三. 启动spark。</p>

<p>      进入spark的sbin目录：cd  sbin/</p>

<p>     启动spark集群（单机版）： ./start-all.sh</p>

<p><img alt="" class="has" height="222" src="https://img-blog.csdn.net/2018081310541549?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l6aF8xMzQ2OTgzNTU3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="928"></p>

<p>会要求你输入linux的登录密码，使用jps命令可看到运行的master和worker进程，至此，单机版spark的搭建完成。</p>

<p>start-all.sh命令的作用可查看文件的内容：cat start-all.sh</p>

<p>if [ -z "${SPARK_HOME}" ]; then<br>
  export SPARK_HOME="$(cd "`dirname "$0"`"/..; pwd)"<br>
fi</p>

<p># Load the Spark configuration<br>
. "${SPARK_HOME}/sbin/spark-config.sh"</p>

<p># Start Master<br>
"${SPARK_HOME}/sbin"/start-master.sh</p>

<p># Start Workers<br>
"${SPARK_HOME}/sbin"/start-slaves.sh<br><br><img alt="" class="has" src="https://img-blog.csdn.net/20180813110128503?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l6aF8xMzQ2OTgzNTU3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p>可以发现它加载了spark-config.sh配置文件，启动了集群master主节点和slaves子节点。</p>

<p>参考博客：<a href="https://blog.csdn.net/pucao_cug/article/details/72377219" rel="nofollow">https://blog.csdn.net/pucao_cug/article/details/72377219</a></p>

<p>2.3.1的官方文档：<a href="http://spark.apache.org/docs/latest/spark-standalone.html" rel="nofollow">http://spark.apache.org/docs/latest/spark-standalone.html</a></p>

<p><br>
 </p>

<p>         <br>
 </p>

<p> </p>

<p> </p>

<p>          </p>

<p>         </p>

<p> </p>

<p> </p>            </div>
                </div>