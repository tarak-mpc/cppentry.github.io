---
layout:     post
title:      hadoop安装和hadoop pipes编程说明
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文可以转载，转载时，须注明原文出处。					https://blog.csdn.net/syx19930206/article/details/46999555				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p><em>本篇文章主要是对hadoop pipes编程的一些问题的备注，对于网上常见的问题，并未完全写入。</em></p>



<h1 id="安装">安装</h1>

<p><em>基础环境：3台基于centos7的虚拟机（1个master，2个slave：slave1、slave2）、hadoop-2.6.0</em> <br>
1. hadoop安装主要参考的网址是：<a href="http://www.aboutyun.com/thread-7781-1-1.html" rel="nofollow">hadoop参考安装</a> <br>
2. linux配置ssh免密码登录，具体参考的是：<a href="http://www.jb51.net/os/RedHat/118711.html" rel="nofollow">centos ssh免密码登录</a> <br>
<strong>tips：</strong> <br>
1. <strong>三个虚拟机的一定要用一样的帐号，即用户名。</strong>如果不一样的话，通过ssh进行免密码登录时会出现问题。比如master登录用户为hadoop，slave1登录用户为hadoop1，master通过ssh进行登录slave1时，默认的用户名是当前用户hadoop，而slave1的可登录用户是hadoop1，导致登录不成功，当然可以通过显示指定用户名，比如：ssh hadoop1@slave1的ip的方式进行登录。不过hadoop默认ssh登录时应该是采用当前默认用户名，所以就算ssh配置成功了，机器的用户名不一样，hadoop启动时照样会需要输入相应密码。 <br>
2. <strong>ssh追加密钥文件时，只需要配置master和slave即可，slave之间不需要再配置</strong>。即只需要配置：master与slave1、master与slave2。slave1和slave2之间不需要再配置。 <br>
3. 配置hadoop时，最好更换下core-site.xml中的“hadoop.tmp.dir”的路径，其默认路径是“/tmp” ，因为“/tmp”很容易因为权限和被系统自动清理出现问题。 <br>
4. hadoop配置完以后，可以通过远程复制命令：“<strong>scp</strong>”，将hadoop分发到其他机器。</p>



<h1 id="启动">启动</h1>

<ol>
<li>简单启动所有的hadoop程序，先<em>切换到hadoop的安装目录下</em> <br>
<ul><li>第一步先格式化文件系统：bin/hdfs namenode -format</li>
<li>然后一键启动所有程序：sbin/start-all.sh</li></ul></li>
<li><em>关于启动的所有命令和其他命令可以参考官方文档</em>  <a href="http://hadoop.apache.org/docs/r2.6.0/hadoop-project-dist/hadoop-common/ClusterSetup.html#Hadoop_Startup" rel="nofollow">hadoop启动命令</a> &amp; <a href="http://hadoop.apache.org/docs/r2.6.0/" rel="nofollow">hadoop-2.6.0官方介绍</a></li>
</ol>

<p><strong>tips：</strong> <br>
1. <strong>启动前需要关闭各个机器的防火墙</strong>。临时关闭防火前命令：(非root用户，即普通用户)“sudo service firewalld stop”或者(root用户)“service firewalld stop”。查看防火墙状态的命令：“service firewalld status” <br>
2. <strong>如果你对hadoop进行多次格式化，在格式化之前需要删除hdfs-site.xml文件中“dfs.namenode.name.dir”、“dfs.datanode.data.dir”和core-site.xml中的“hadoop.tmp.dir”对应路径下的文件</strong>，最好是每个机器都删除。如果没有显示赋值，可以默认路径一般对应分别是：”/tmp/dfs/name”、”/tmp/dfs/data”和”/tmp”，这个主要是为了避免下次启动时namenodeID不一致的问题，感觉只删除”dfs.namenode.name.dir”路径下的文件就可以了，具体没有验证。 <br>
3. <strong>关于各个程序的启动情况，如果出现错误，可以查看相应的启动日志</strong>。路径是：“hadoop的安装目录/logs” <br>
4. <strong>单独启动hadoop的各个应用程序</strong>。具体参考：<a href="http://hadoop.apache.org/docs/r2.6.0/hadoop-project-dist/hadoop-common/ClusterSetup.html#Hadoop_Startup" rel="nofollow">hadoop启动命令</a> &amp;<a href="http://www.cnblogs.com/likehua/p/3999560.html" rel="nofollow">单个命令使用说明</a> <br>
5. <strong>“sbin/hadoop-daemon.sh start datanode”和”sbin/hadoop-daemons.sh start datanode”的区别：前者是启动master的datanode，后者是启动所有slave节点上的datanode</strong>。停止命令，把start改为stop即可。 <br>
6. <strong>通过web查看hadoop集群的运行情况</strong>。参考官方文档 <a href="http://hadoop.apache.org/docs/r2.6.0/hadoop-project-dist/hadoop-common/ClusterSetup.html#Web_Interfaces" rel="nofollow">web查看hadoop情况</a> <br>
7. <strong>hadoop自带相应的说明文档docs</strong>。路径是：hadoop安装目录/share/doc/hadoop，index.html是文档说明的首页，点击即可进行查看。</p>

<h1 id="启动样例程序">启动样例程序</h1>

<p>运行样例程序需要上传相应的文件和创建目录，这要求hadoop需要将safemode设置为off状态。命令是：”bin/hdfs dfsadmin -safemode leave”</p>



<h2 id="启动java版wordcount">启动java版wordcount</h2>

<p>启动java，是执行自带的jar，通过hadoop jar命令，类似如下，先切换到hadoop安装目录： <br>
bin/hadoop jar ./share/hadoop/mapreduce/hadoop-mapreduce-examples-2.6.0.jar WordCount /wordcount/input /wordcount/output <br>
具体可参考 <a href="http://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html#Usage" rel="nofollow">官网介绍WordCount</a> &amp; <a href="http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CommandsManual.html#jar" rel="nofollow">hadoop jar命令说明</a></p>



<h2 id="启动c版wordcount">启动C++版wordcount</h2>

<p>执行C++版wordcount，需要下载源码，对其进行编译。官方提供了两种编译方式：maven的mvn和make命令。 <br>
下载的是<a href="http://mirror.bit.edu.cn/apache/hadoop/common/hadoop-2.6.0/hadoop-2.6.0-src.tar.gz" rel="nofollow">hadoop-2.6.0-src</a> <br>
wordcount的源码路径：“hadoop-2.6.0-src/hadoop-tools/hadoop-pipes”</p>



<h3 id="mvn编译方式">mvn编译方式</h3>

<p>根据“hadoop-2.6.0-src/hadoop-tools/hadoop-pipes/src/main/native/examples/README.txt”的说明进行编译。复制其内容如下： <br>
1. To run the examples, first compile them:（mvn运行需要解析pom.xml配置文件，所以执行这个命令，需要先切换到“hadoop-2.6.0-src/hadoop-tools/hadoop-pipes/”目录下） <br>
% mvn install -Pdist,native <br>
2. and then copy the binaries to dfs:（mvn生成的可执行文件在target/native/examples/wordcount-simple文件夹下，需要先将其上传到hdfs中） <br>
% hdfs dfs -put target/native/examples/wordcount-simple /examples/bin/ <br>
3. create an input directory with text files: <br>
% hdfs dfs -put my-data in-dir <br>
4. and run the word count example:（运行文件需要相应的配置信息，同时注意输入路径in-dir默认是在hdfs文件系统上的user文件夹下（未验证），建议以“/in-dir”这种方式指定输入路径，输出路径也一样） <br>
% mapred pipes -conf src/main/native/examples/conf/word.xml -input in-dir -output out-dir</p>

<p><strong>tips：</strong> <br>
1. <strong>通过mapred pipes命令执行执行程序，master是将可执行文件分发到各个slave以后再执行，即访问main函数，所以，如果你在mapreduce程序的main函数中有什么读取文件的操作，他们读取的都是slave的，同时他们运行的环境也是基于slave的，比如配置底层需要的jar包，不能只配置master，重点是配置实际运行程序的slave机器。</strong> <br>
2. 第4步使用word.xml，主要是为了设置类似recordreader和recordwriter之类的处理是否是java类，所以也可以不用word.xml文件，直接执行如下命令： <br>
mapred pipes -D hadoop.pipes.java.recordreader=true -D hadoop.pipes.java.recordwriter=true -input /input/sample.txt -output /output -program /examples/bin/wordcount <br>
3. 如果通过<a href="https://github.com/winghc/hadoop2x-eclipse-plugin" rel="nofollow">hadoop-eclipse-plugin</a>编写mapreduce程序时，在linux中，通过插件设置“Edit Hadoop Location…&gt;Advanced parameters”时，会显示空白，如果等待一段时间（3分钟左右）还不显示，你可以通过上下左右拉大窗口来刷新窗口来显示，如果还是不行，可能需要根据说明进行重新编译一遍插件。 <br>
4. “Location name” 为任意字符串，Host是master的ip，不要用字符串“master”，显示用ip，“User name”为master主机的登录账号 <br>
 <img src="https://img-blog.csdn.net/20150722171225089" alt="hadoop-eclipse-plugin" title=""></p>

<h3 id="make直接编译">make直接编译</h3>

<p>根据源码中提供的CMakeLists.txt文件来生成make命令需要的Makefile文件，然后利用Makefile进行编译生成可执行文件。具体如下： <br>
1. 切换到具有CMakeLists.txt的文件夹下。即“hadoop-2.6.0-src/hadoop-tools/hadoop-pipes/src” <br>
2. 直接执行cmake，即“cmake .”注意，“.”表示当前目录，还有cmake可能需要先安装。cmake执行完后会在当前目录下生成Makefile文件。 <br>
3. 直接执行make命令，即“make”，不需要指定当前目录 <br>
4. 命令执行完成后会在当前目录生成相应的可执行文件，同样需要上传到hdfs和通过mapred pipes命令进行执行。</p>



<h3 id="通过eclipse编写hadoop-pipes">通过eclipse编写hadoop pipes</h3>

<ul>
<li>主要是通过org.apache.hadoop.mapred.pipes.Submitter类来控制C++版MapReduce多次迭代执行。即下一次的MapReduce需要等待上一次的MapReduce的任务执行完。即Chain MapReduce。这个需要借助Submitter类才能完成。</li>
<li>Submitter是通过执行已上传到hdfs的可执行文件来完成多次MapReduce。</li>
</ul>



<h4 id="c版mapreduce说明">C++版MapReduce说明</h4>

<ol>
<li>代码需要引用openssl的相应库文件，所以需要先安装openssl</li>
<li><p>需要包含的头文件在“hadoop安装目录/include”路径下。需要包含的库文件主要是：</p>

<ul><li>hadoop安装目录/lib/native/：libhadooputils.a（支持MR），libhadooppipes.a（支持MR），libhadoop.so，libhdfs.so（支持hdfs操作）。 <br>
<strong>tips：</strong> <br>
在eclipse中引用时，不需要写lib和.a、.so，它会自动不全。需要说明一下，<strong>libhadoop.so和libhdfs.so是链接文件，具体指到同一目录下带有具体版本号的真实.so文件，你可以通过他们的大小看出来，如果在windows下直接解压hadoop安装文件，会导致这个链接失效，必须重新建立链接，不然引用会失效，找不到库</strong></li>
<li>jdk安装目录/jre/lib/amd64/server：libjvm.so，如果是32位，可能路径会不同。</li>
<li>/usr/lib或者/usr/lib64：libcrypto.so，libpthread.so，这个目录是系统指定，可以直接包含命令，不需要添加路径</li>
<li>libssl.a或者libssl.so，具体忘记了</li></ul></li>
<li><p>编译时，设置的库目录，在程序具体运行时，会通过<strong>LD_LIBRARY_PATH</strong>查找相应文件，需要在/etc/profile，即设置系统环境变量的地方，设置查找库所需要的路径，比如： <br>
export LD_LIBRARY_PATH=<span>$</span>LD_LIBRARY_PATH:<span>$</span>JAVA_HOME/jre/lib/amd64/server:$HADOOP_HOME/lib/native</p></li>
<li><strong>如果你在MapReduce中进行了访问hdfs文件的操作，那么你必须设置程序运行需要的jar文件，即在mapred-site.xml文件中设置“mapreduce.application.classpath”的值，具体值，可以通过执行“hadoop classpath –glob”打印输出，注意引用的jar不能带通配符，即类似*之类的，同时也需要把系统环境变量中的classpath的值也添加进去。注意是逗号分割，具体参考<a href="http://hadoop.apache.org/docs/r2.6.0/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml" rel="nofollow">mapred-site.xml文件变量说明</a>。还有每个机器都需要设置，因为mapreduce是下发至每个slave具体执行的</strong>。</li>
<li>具体的Hadoop pipes（即C++ MapReduce）可以参考源代码带的例子，同样C++ Hdfs访问，主要是通过“hdfs.h”头文件中提供的接口进行访问的。</li>
</ol>



<h4 id="submitter类的使用样例">Submitter类的使用样例</h4>

<p>Submitter类的使用如下（Utils是自己写的包含一些功能函数的类，和具体使用Submitter无关）：</p>



<pre class="prettyprint"><code class=" hljs avrasm">package test<span class="hljs-comment">;</span>

import java<span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.IOException</span><span class="hljs-comment">;</span>

import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.conf</span><span class="hljs-preprocessor">.Configuration</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.fs</span><span class="hljs-preprocessor">.Path</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.mapred</span><span class="hljs-preprocessor">.FileInputFormat</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.mapred</span><span class="hljs-preprocessor">.FileOutputFormat</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.mapred</span><span class="hljs-preprocessor">.JobConf</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.mapred</span><span class="hljs-preprocessor">.RunningJob</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.mapred</span><span class="hljs-preprocessor">.pipes</span><span class="hljs-preprocessor">.Submitter</span><span class="hljs-comment">;</span>
public class KMeans_Pipes {

    public static final String centerPath=<span class="hljs-string">"hdfs://192.168.1.104:9000/inputTemp/center/centers.txt"</span><span class="hljs-comment">;</span>
    public static final String inputPath=<span class="hljs-string">"hdfs://192.168.1.104:9000/inputTemp/data/wine.txt"</span><span class="hljs-comment">;</span>
    public static final String outputPath=<span class="hljs-string">"hdfs://192.168.1.104:9000/output/kmean"</span><span class="hljs-comment">;</span>
    public static void main(String[] args) {
        int i=<span class="hljs-number">0</span><span class="hljs-comment">;</span>
        while(true){
            try {
                runTask()<span class="hljs-comment">;</span>
                if(Utils<span class="hljs-preprocessor">.compareCenters</span>(centerPath, outputPath)){
                    Utils<span class="hljs-preprocessor">.output</span>()<span class="hljs-comment">;</span>
                    <span class="hljs-keyword">break</span><span class="hljs-comment">;</span>
                }
                System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"第"</span>+(++i)+<span class="hljs-string">"次运行结束"</span>)<span class="hljs-comment">;</span>
            } catch (IOException e) {
                e<span class="hljs-preprocessor">.printStackTrace</span>()<span class="hljs-comment">;</span>
            }
        }
    }

    static void runTask() throws IOException{

        JobConf jobConf = new JobConf(new Configuration())<span class="hljs-comment">;</span>
//      jobConf<span class="hljs-preprocessor">.setJobName</span>(<span class="hljs-string">"kmeans:"</span>+num)<span class="hljs-comment">;</span>
        FileInputFormat<span class="hljs-preprocessor">.setInputPaths</span>(jobConf, new Path(inputPath))<span class="hljs-comment">;</span>
        FileOutputFormat<span class="hljs-preprocessor">.setOutputPath</span>(jobConf, new Path(outputPath))<span class="hljs-comment">;</span>
        Submitter<span class="hljs-preprocessor">.setKeepCommandFile</span>(jobConf, true)<span class="hljs-comment">;</span>
        Submitter<span class="hljs-preprocessor">.setExecutable</span>(jobConf,<span class="hljs-string">"hdfs://192.168.1.104:9000/examples/bin/MR_KMeans"</span>)<span class="hljs-comment">;</span>
        Submitter<span class="hljs-preprocessor">.setIsJavaMapper</span>(jobConf, false)<span class="hljs-comment">;</span>
        Submitter<span class="hljs-preprocessor">.setIsJavaReducer</span>(jobConf, false)<span class="hljs-comment">;</span>
        Submitter<span class="hljs-preprocessor">.setIsJavaRecordReader</span>(jobConf, true)<span class="hljs-comment">;</span>
        Submitter<span class="hljs-preprocessor">.setIsJavaRecordWriter</span>(jobConf, true)<span class="hljs-comment">;</span>
        Utils<span class="hljs-preprocessor">.deletePath</span>(outputPath)<span class="hljs-comment">;</span>
        RunningJob rj = Submitter<span class="hljs-preprocessor">.runJob</span>(jobConf)<span class="hljs-comment">;</span>
//      RunningJob rj = Submitter<span class="hljs-preprocessor">.jobSubmit</span>(jobConf)<span class="hljs-comment">;</span>
//      rj<span class="hljs-preprocessor">.waitForCompletion</span>()<span class="hljs-comment">;</span>

        System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(rj<span class="hljs-preprocessor">.isComplete</span>())<span class="hljs-comment">;</span>
    }

}
</code></pre>

<p><strong>tips：</strong> <br>
1. <strong>程序要打包成jar，通过hadoop jar命令进行执行，不能通过eclipse本地或者hadoop-eclipse-plugin插件执行，会出现问题</strong>。 <br>
2. 函数说明  <br>
RunningJob rj = Submitter.jobSubmit(jobConf); <br>
rj.waitForCompletion(); <br>
这两个可以配合使用，来实现等待任务完成后再往下执行。当然也可以只用一个函数，即： <br>
RunningJob rj = Submitter.runJob(jobConf); <br>
注意这个就不要再使用waitForCompletion();函数了，不然会出错误。</p>



<h3 id="通过hadoop-eclipse-plugin编写local-mapreduce">通过hadoop-eclipse-plugin编写local Mapreduce</h3>

<p>编写程序运行时，可能会遇到空指针问题。 <br>
1. 可参考<a href="http://www.sunchangming.com/blog/post/4131.html" rel="nofollow">local mapreduce遇到的问题</a> &amp; <a href="http://www.2cto.com/kf/201203/123070.html" rel="nofollow">local mapreduce问题</a> <br>
2. 主要是在当前工程中，添加进去“org.apache.hadoop.mapred.pipes”下的Application.java类，即在工程中新建package，名为“org.apache.hadoop.mapred.pipes”，然后把Application.java复制到该package下来进行修改代码，覆盖原先jar中的Application类。Application.java的位置在： <br>
hadoop源码目录下的：hadoop-mapreduce-project\hadoop-mapreduce-client\hadoop-mapreduce-client-core\src\main\java\org\apache\hadoop\mapred\pipes <br>
3. 修改说明如下：</p>

<ul>
<li>在104行左右 <br>
将<code>byte[]  password = jobToken.getPassword();</code>修改为：</li>
</ul>

<pre class="prettyprint"><code class=" hljs cs"><span class="hljs-keyword">byte</span>[] password = <span class="hljs-string">"no password"</span>.getBytes();
    <span class="hljs-keyword">if</span> (jobToken != <span class="hljs-keyword">null</span>) {
      password = jobToken.getPassword();
    }</code></pre>

<ul>
<li>在179行左右：将原先的权限代码修改为666，即：</li>
</ul>



<pre class="prettyprint"><code class=" hljs cs">FSDataOutputStream <span class="hljs-keyword">out</span> = FileSystem.create(localFs, localPath,
        <span class="hljs-keyword">new</span> FsPermission(<span class="hljs-string">"666"</span>));</code></pre>

<ul>
<li>运行时，可能会遇到类似“改变可执行文件权限出错，chmod无法访问指定文件”的错误，一般都是在路径前面，强制加了“file:”，比如file:/tmp/mapred，这个问题，自己感觉主要是uri.getScheme()导致的，hadoop的scheme感觉主要是和core-site.xml下的“fs.defaultFS”有关系，具体说明<a href="http://hadoop.apache.org/docs/r2.6.0/hadoop-project-dist/hadoop-common/core-default.xml" rel="nofollow">fs.defaultFS</a></li>
</ul>



<h3 id="hadoop-eclipse-plugin说明">hadoop-eclipse-plugin说明</h3>

<h1 id="windows配置环境变量和系统重启">windows配置环境变量和系统重启</h1>

<p>参考<a href="http://blog.163.com/guomaolin_gavin/blog/static/19961830720121114929321/" rel="nofollow">Windows下配置环境变量和需不需要重启问题</a> <br>
一个程序启动时，环境变量被复制到该程序所在的环境中，在该程序执行过程中不会被除该程序以外的其他程序所改变。也就是说，假设我们启动了一个cmd程序，然后通过控制面板修改了环境变量设置，但是已经启动了的cmd所拥有的环境变量并不会被改变。如果我们在修改环境变量之后启动cmd程序，则该程序将拥有新的环境变量。 <br>
那么结论就很明显了：修改环境变量之后，如果受影响的是应用程序，那么只要简单地重新启动此应用程序，环境变量的修改就会反映到该程序中，而不必重新启动计算机；但是，如果受影响的是系统服务，就必须重新启动才能将环境变量的修改反映到系统服务中（因为没有办法在不重启计算机的情况下重新启动系统服务管理器）。</p>



<h1 id="linux查看程序依赖的库">linux查看程序依赖的库</h1>

<p>参考<a href="http://os.51cto.com/art/201408/448424.htm" rel="nofollow">linux有问必答</a> &amp; <a href="http://wanwentao.blog.51cto.com/2406488/569291/" rel="nofollow">linux下查看程序依赖的库</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>