---
layout:     post
title:      HDFS简介及用C语言访问HDFS接口操作实践
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，对文章内容有任何意见或建议，欢迎与作者单独交流，作者QQ(微信)：245924426。					https://blog.csdn.net/zhouzxi/article/details/47448227				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p><strong>一、概述</strong> <br>
近年来，大数据技术如火如荼，如何存储海量数据也成了当今的热点和难点问题，而HDFS分布式文件系统作为Hadoop项目的分布式存储基础，也为HBASE提供数据持久化功能，它在大数据项目中有非常广泛的应用。 <br>
Hadoop分布式文件系统(Hadoop Distributed File System，HDFS)被设计成适合运行在通用硬件(commodity hardware)上的分布式文件系统。HDFS是Hadoop项目的核心子项目，是一种具有高容错性、高可靠性、高可扩展性、高吞吐量等特征的分布式文件系统，可用于云计算或其它大数据应用中海量数据的存储(主要为大文件的存储)。 <br>
    本文结合作者本人及同事对HDFS的学习和实践的理解，首先介绍HDFS的特点和重要SHELL命令(hadoop和hdfs命令)的使用，接着介绍HDFS提供的C访问接口LIB HDFS及其跟普通文件系统的C API的异同，然后介绍如何利用LIB HDFS接口实现简单的HDFS客户端并列举相关应用实例，最后针对编写HDFS客户端中遇到的问题进行描述和分析。</p>

<p><strong>二、HDFS简介</strong> <br>
HDFS是Hadoop项目的核心子项目，是一种具有高容错性、高可靠性、高可扩展性、高吞吐量等特征的分布式文件系统。 <br>
<strong>1.HDFS特点</strong> <br>
    HDFS作为一种分布式文件系统，主要有以下特点： <br>
1)主要用于存储和管理大数据文件(由于HDFS默认数据块为128M，所以它主要适合于存储百M级别及以上大小的文件)。 <br>
2)其数据节点可横向扩展，且可选择廉价的商业硬件。 <br>
3)设计理念为“一次写，多次读”。 <br>
4)当前不支持在文件任意位置修改文件内容，只能在文件尾部执行append操作。 <br>
5)不适合低延迟(几十毫秒)数据访问应用(低延迟应用可以考虑HBASE分布式数据库或者ES+分布式文件系统的架构)。</p>

<p><strong>2.HDFS常用SHELL命令简介</strong> <br>
    hadoop有两个非常重要的SHELL命令：hadoop和hdfs。对于管理HDFS文件系统而言，hadoop和hdfs脚本功能有很大的重复性，下面以分别对这两个命令进行介绍。 <br>
1)hadoop命令使用 <br>
hadoop几乎所有的管理命令都被整合到了一个SHELL脚本中，即bin/hadoop脚本，通过执行带参数的hadoop脚本，就可以实现hadoop模块的管理工作。由于本文主要介绍HDFS文件系统，所以这里就主要介绍如何使用hadoop脚本操作HDFS文件系统，相关命令及参数见图1所示。 <br>
<img src="https://img-blog.csdn.net/20150812173652373" alt="这里写图片描述" title=""> <br>
图1 hadoop fs命令及参数描述 <br>
下面举一个具体的例子。例如，要在HDFS文件系统中查看根目录包含的所有目录或文件，再创建test目录，并赋予777权限，最后删除该目录。实现以上操作的主要命令如下：</p>



<pre class="prettyprint"><code class=" hljs perl">hadoop fs –ls /
hadoop fs -<span class="hljs-keyword">mkdir</span> /test
hadoop fs –<span class="hljs-keyword">chmod</span> –R <span class="hljs-number">777</span> /test
hadoop fs –<span class="hljs-keyword">rmdir</span> /test</code></pre>

<p>hadoop fs命令能够实现用户组及权限的管理、目录和文件和管理、文件的上传和下载等功能，但对于HDFS文件系统的检查(包括坏块的清理)、节点管理、快照管理和格式化等深层次管理工作就无能为力了，这里就需要用到hdfs命令了。</p>

<p>2)hdfs命令使用 <br>
hdfs所有管理命令都被整合到了一个SHELL脚本中，即bin/hdfs脚本，通过执行带参数的hdfs脚本，就可以实现对HDFS文件系统的管理工作，包括基本的文件、目录、权限和属组操作以及数据块管理和格式化等功能。 <br>
    hdfs脚本实现的功能非常强大，hdfs dfs命令跟hadoop fs命令功能完全一致，所以我们可以利用hdfs dfs命令并携带图1中的参数实现hadoop fs的所有功能。下面主要介绍下HDFS文件系统格式化和数据块管理操作。 <br>
    格式化一个HDFS文件系统，使用如下命令： <br>
hdfs namenode -format <br>
    删除HDFS文件系统中存在的坏块及相应已损坏的文件，使用如下命令： <br>
hdfs fsck -delete -files /</p>

<p><strong>三、LIB HDFS接口简介</strong> <br>
    Hadoop FileSystem APIs是JAVA CLIENT API，HDFS并没有提供原生的C语言访问接口。但HDFS提供了基于JNI的C调用接口LIB HDFS，为C语言访问HDFS提供了很大的便利。 <br>
    LIB HDFS接口的头文件和库文件已包含在Hadoop发行版本中，可以直接使用。它的头文件hdfs.h一般位于<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-1-Frame" role="textbox" aria-readonly="true" style=""><nobr><span class="math" id="MathJax-Span-1" style="width: 33.499em; display: inline-block;"><span style="display: inline-block; position: relative; width: 27.211em; height: 0px; font-size: 123%;"><span style="position: absolute; clip: rect(1.304em 1000.003em 2.713em -0.431em); top: -2.274em; left: 0.003em;"><span class="mrow" id="MathJax-Span-2"><span class="texatom" id="MathJax-Span-3"><span class="mrow" id="MathJax-Span-4"><span class="mi" id="MathJax-Span-5" style="font-family: MathJax_Math; font-style: italic;">H<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.057em;"></span></span><span class="mi" id="MathJax-Span-6" style="font-family: MathJax_Math; font-style: italic;">A</span><span class="mi" id="MathJax-Span-7" style="font-family: MathJax_Math; font-style: italic;">D</span><span class="mi" id="MathJax-Span-8" style="font-family: MathJax_Math; font-style: italic;">O</span><span class="mi" id="MathJax-Span-9" style="font-family: MathJax_Math; font-style: italic;">O</span><span class="msubsup" id="MathJax-Span-10"><span style="display: inline-block; position: relative; width: 1.412em; height: 0px;"><span style="position: absolute; clip: rect(1.412em 1000.003em 2.442em -0.431em); top: -2.274em; left: 0.003em;"><span class="mi" id="MathJax-Span-11" style="font-family: MathJax_Math; font-style: italic;">P<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.111em;"></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.111em; left: 0.707em;"><span class="mi" id="MathJax-Span-12" style="font-size: 70.7%; font-family: MathJax_Math; font-style: italic;">H<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.057em;"></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span><span class="mi" id="MathJax-Span-13" style="font-family: MathJax_Math; font-style: italic;">O</span><span class="mi" id="MathJax-Span-14" style="font-family: MathJax_Math; font-style: italic;">M<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.057em;"></span></span><span class="mi" id="MathJax-Span-15" style="font-family: MathJax_Math; font-style: italic;">E<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span></span></span><span class="texatom" id="MathJax-Span-16"><span class="mrow" id="MathJax-Span-17"><span class="mo" id="MathJax-Span-18" style="font-family: MathJax_Main;">/</span></span></span><span class="mi" id="MathJax-Span-19" style="font-family: MathJax_Math; font-style: italic;">i</span><span class="mi" id="MathJax-Span-20" style="font-family: MathJax_Math; font-style: italic;">n</span><span class="mi" id="MathJax-Span-21" style="font-family: MathJax_Math; font-style: italic;">c</span><span class="mi" id="MathJax-Span-22" style="font-family: MathJax_Math; font-style: italic;">l</span><span class="mi" id="MathJax-Span-23" style="font-family: MathJax_Math; font-style: italic;">u</span><span class="mi" id="MathJax-Span-24" style="font-family: MathJax_Math; font-style: italic;">d<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span class="mi" id="MathJax-Span-25" style="font-family: MathJax_Math; font-style: italic;">e</span><span class="texatom" id="MathJax-Span-26"><span class="mrow" id="MathJax-Span-27"><span class="mo" id="MathJax-Span-28"><span style="font-family: STIXGeneral, 'Arial Unicode MS', serif; font-size: 81%; font-style: normal; font-weight: normal;">目</span></span></span></span><span class="texatom" id="MathJax-Span-29"><span class="mrow" id="MathJax-Span-30"><span class="mo" id="MathJax-Span-31"><span style="font-family: STIXGeneral, 'Arial Unicode MS', serif; font-size: 81%; font-style: normal; font-weight: normal;">录</span></span></span></span><span class="texatom" id="MathJax-Span-32"><span class="mrow" id="MathJax-Span-33"><span class="mo" id="MathJax-Span-34"><span style="font-family: STIXGeneral, 'Arial Unicode MS', serif; font-size: 81%; font-style: normal; font-weight: normal;">中</span></span></span></span><span class="texatom" id="MathJax-Span-35"><span class="mrow" id="MathJax-Span-36"><span class="mo" id="MathJax-Span-37"><span style="font-family: STIXGeneral, 'Arial Unicode MS', serif; font-size: 81%; font-style: normal; font-weight: normal;">，</span></span></span></span><span class="texatom" id="MathJax-Span-38"><span class="mrow" id="MathJax-Span-39"><span class="mo" id="MathJax-Span-40"><span style="font-family: STIXGeneral, 'Arial Unicode MS', serif; font-size: 81%; font-style: normal; font-weight: normal;">而</span></span></span></span><span class="texatom" id="MathJax-Span-41"><span class="mrow" id="MathJax-Span-42"><span class="mo" id="MathJax-Span-43"><span style="font-family: STIXGeneral, 'Arial Unicode MS', serif; font-size: 81%; font-style: normal; font-weight: normal;">其</span></span></span></span><span class="texatom" id="MathJax-Span-44"><span class="mrow" id="MathJax-Span-45"><span class="mo" id="MathJax-Span-46"><span style="font-family: STIXGeneral, 'Arial Unicode MS', serif; font-size: 81%; font-style: normal; font-weight: normal;">库</span></span></span></span><span class="texatom" id="MathJax-Span-47"><span class="mrow" id="MathJax-Span-48"><span class="mo" id="MathJax-Span-49"><span style="font-family: STIXGeneral, 'Arial Unicode MS', serif; font-size: 81%; font-style: normal; font-weight: normal;">文</span></span></span></span><span class="texatom" id="MathJax-Span-50"><span class="mrow" id="MathJax-Span-51"><span class="mo" id="MathJax-Span-52"><span style="font-family: STIXGeneral, 'Arial Unicode MS', serif; font-size: 81%; font-style: normal; font-weight: normal;">件</span></span></span></span><span class="mi" id="MathJax-Span-53" style="font-family: MathJax_Math; font-style: italic;">l</span><span class="mi" id="MathJax-Span-54" style="font-family: MathJax_Math; font-style: italic;">i</span><span class="mi" id="MathJax-Span-55" style="font-family: MathJax_Math; font-style: italic;">b</span><span class="mi" id="MathJax-Span-56" style="font-family: MathJax_Math; font-style: italic;">h</span><span class="mi" id="MathJax-Span-57" style="font-family: MathJax_Math; font-style: italic;">d<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span class="mi" id="MathJax-Span-58" style="font-family: MathJax_Math; font-style: italic;">f<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.057em;"></span></span><span class="mi" id="MathJax-Span-59" style="font-family: MathJax_Math; font-style: italic;">s</span><span class="mo" id="MathJax-Span-60" style="font-family: MathJax_Main;">.</span><span class="mi" id="MathJax-Span-61" style="font-family: MathJax_Math; font-style: italic; padding-left: 0.165em;">s</span><span class="mi" id="MathJax-Span-62" style="font-family: MathJax_Math; font-style: italic;">o</span><span class="texatom" id="MathJax-Span-63"><span class="mrow" id="MathJax-Span-64"><span class="mo" id="MathJax-Span-65"><span style="font-family: STIXGeneral, 'Arial Unicode MS', serif; font-size: 81%; font-style: normal; font-weight: normal;">通</span></span></span></span><span class="texatom" id="MathJax-Span-66"><span class="mrow" id="MathJax-Span-67"><span class="mo" id="MathJax-Span-68"><span style="font-family: STIXGeneral, 'Arial Unicode MS', serif; font-size: 81%; font-style: normal; font-weight: normal;">常</span></span></span></span><span class="texatom" id="MathJax-Span-69"><span class="mrow" id="MathJax-Span-70"><span class="mo" id="MathJax-Span-71"><span style="font-family: STIXGeneral, 'Arial Unicode MS', serif; font-size: 81%; font-style: normal; font-weight: normal;">则</span></span></span></span><span class="texatom" id="MathJax-Span-72"><span class="mrow" id="MathJax-Span-73"><span class="mo" id="MathJax-Span-74"><span style="font-family: STIXGeneral, 'Arial Unicode MS', serif; font-size: 81%; font-style: normal; font-weight: normal;">位</span></span></span></span><span class="texatom" id="MathJax-Span-75"><span class="mrow" id="MathJax-Span-76"><span class="mo" id="MathJax-Span-77"><span style="font-family: STIXGeneral, 'Arial Unicode MS', serif; font-size: 81%; font-style: normal; font-weight: normal;">于</span></span></span></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span><span style="border-left-width: 0.003em; border-left-style: solid; display: inline-block; overflow: hidden; width: 0px; height: 1.403em; vertical-align: -0.397em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-1">{HADOOP_HOME}/include目录中，而其库文件libhdfs.so通常则位于</script>{HADOOP_HOME}/lib/native目录中。因为Hadoop版本一直在更新中，所以不同版本的LIB HDFS接口功能通常不太一样，主要表现为功能递增的现象。 <br>
    通过LIB HDFS访问HDFS文件系统与使用C语言API访问普通操作系统的文件系统类似，但还存在一些不足的地方，具体如下所示： <br>
a)LIB HDFS接口实现的功能只是JAVA CLIENT API功能的一个子集，且跟JAVA CLIENT API相比可能还存在不少BUG未被发现或修复，如多线程、多进程访问文件系统时句柄资源释放的问题。 <br>
b)另外由于是使用JNI方式调用JAVA CLASS，所以应用程序占用内存较多，而且该接口运行可能会产生大量异常日志，怎么管理这些日志是个问题。另外，当操作HDFS文件系统出错时，errno不一定会有正确的提示，也会增加排查问题的难度。 <br>
c)目前LIB HDFS可参考用例较少，而利用多线程等方式通过LIB HDFS大数据量读写HDFS文件系统的用例更是少之又少。 <br>
d)目前LIB HDFS不支持在任意位置修改文件内容，只能在文件末尾执行append操作，或对整个文件执行truncate操作，这个主要跟HDFS文件系统设计有关，大数据存储一般都缺乏更新功能的支持，这点我们只能通过业务层面来规避了。 <br>
e)由低到高Hadoop发行版本携带的LIB HDFS功能可能呈现递增的情况，所以每当Hadoop版本更新了，都需要重新编译我们的应用程序，增加升级难度。 <br>
    目前虽然LIB HDFS接口还存在一些不足的地方，但相信未来随着该接口版本的不断更新，其功能和稳定性都会大大提高。</p>

<p><strong>四、C语言访问HDFS应用实践</strong> <br>
<strong>1.编译和运行环境搭建</strong> <br>
为了成功编译C语言客户端程序，我们需要预先安装7.0及以上版本的JAVA JDK和Hadoop发行版，前者提供libjvm.so等库，后者则提供LIB HDFS连接所需要的库。 <br>
为了成功运行C语言客户端程序，除了预先安装上面提到的程序外，我们还需要正确地设置几个关键环境变量，包括LD_LIBRARY_PATH和CLASSPATH的设置。 <br>
关于LD_LIBRARY_PATH环境变量，主要是需要添加libjvm.so和libhdfs.so库所在路径；而针对CLASSPATH则需要囊括Hadoop提供的所有jar包的全路径信息(具体可通过find+awk组合命令来实现)，否则C语言客户端程序总会报缺少某个类而无法运行的错误。</p>

<p><strong>2.LIB HDFS接口简单应用实践</strong> <br>
这里主要介绍部分API的使用示例。 <br>
1)获取HDFS文件系统的容量和已使用空间大小信息如GetHdfsInfo函数所示：</p>



<pre class="prettyprint"><code class=" hljs objectivec"><span class="hljs-keyword">void</span> GetHdfsInfo(<span class="hljs-keyword">void</span>)
{
    hdfsFS pfs = <span class="hljs-literal">NULL</span>;
    <span class="hljs-keyword">int</span> iRet = <span class="hljs-number">0</span>;
tOffset iTmp = <span class="hljs-number">0</span>;

    pfs = hdfsConnect(<span class="hljs-string">"hdfs://127.0.0.1:9000/"</span>, <span class="hljs-number">0</span>);              <span class="hljs-comment">// 与HDFS文件系统建立连接</span>
    <span class="hljs-keyword">if</span> (<span class="hljs-literal">NULL</span> == pfs)
    {
        WRITELOGEX(LOG_ERROR, (<span class="hljs-string">"GetHdfsInfo(): hdfsConnect failed! errno=%d."</span>, errno));
        <span class="hljs-keyword">return</span>;
    }
WRITELOG(LOG_INFO, <span class="hljs-string">"GetHdfsInfo(): hdfsConnect success!"</span>);

    iTmp = hdfsGetCapacity(pfs);                     <span class="hljs-comment">// 获取HDFS文件系统容量</span>
    <span class="hljs-keyword">if</span> (-<span class="hljs-number">1</span> == iTmp)
    {
        WRITELOGEX(LOG_ERROR, (<span class="hljs-string">"GetHdfsInfo(): hdfsGetCapacity failed! errno=%d."</span>, errno));
        hdfsDisconnect(pfs);
        pfs = <span class="hljs-literal">NULL</span>;
        <span class="hljs-keyword">return</span>;
    }
WRITELOGEX(LOG_INFO, (<span class="hljs-string">"GetHdfsInfo(): hdfsGetCapacity success! offset=%ld."</span>, iTmp));

    iTmp = hdfsGetUsed(pfs);         <span class="hljs-comment">// 获取HDFS文件系统中所有文件占用空间大小，即已使用量</span>
    <span class="hljs-keyword">if</span> (-<span class="hljs-number">1</span> == iTmp)
    {
        WRITELOGEX(LOG_ERROR, (<span class="hljs-string">"GetHdfsInfo(): hdfsGetUsed failed! errno=%d."</span>, errno));
        hdfsDisconnect(pfs);
        pfs = <span class="hljs-literal">NULL</span>;
        <span class="hljs-keyword">return</span>;
    }
    WRITELOGEX(LOG_INFO, (<span class="hljs-string">"GetHdfsInfo(): hdfsGetUsed success! offset=%ld."</span>, iTmp));

    iRet = hdfsDisconnect(pfs);                  <span class="hljs-comment">// 关闭与HDFS文件系统的连接</span>
    <span class="hljs-keyword">if</span> (-<span class="hljs-number">1</span> == iRet)
    {
        WRITELOGEX(LOG_ERROR, (<span class="hljs-string">"GetHdfsInfo(): hdfsDisconnect failed! errno=%d."</span>,  errno));
        <span class="hljs-keyword">return</span>;
    }
    WRITELOGEX(LOG_INFO, (<span class="hljs-string">"GetHdfsInfo(): hdfsDisconnect success! ret=%d."</span>, iRet));
pfs = <span class="hljs-literal">NULL</span>;
    <span class="hljs-keyword">return</span>;
}
</code></pre>

<p>2)在HDFS文件系统中新增文件并写入数据如HdfsWriteTest函数所示：</p>



<pre class="prettyprint"><code class=" hljs perl">void HdfsWriteTest(hdfsFS pfs)
{
    <span class="hljs-keyword">int</span> iRet = <span class="hljs-number">0</span>;
    hdfsFile pfile = NULL;
    char szTestFile[<span class="hljs-number">200</span>] = <span class="hljs-string">"/test/ write.test"</span>;

    <span class="hljs-keyword">if</span> (NULL == pfs)
    {
        WRITELOG(LOG_ERROR, <span class="hljs-string">"HdfsWriteTest():pfs is null."</span>);
        <span class="hljs-keyword">return</span>;
    }

    pfile = hdfsOpenFile(pfs, szTestFile, O_WRONLY || O_CREAT, <span class="hljs-number">0</span>, <span class="hljs-number">0</span>, <span class="hljs-number">0</span>);      <span class="hljs-regexp">//</span> 打开文件句柄
    <span class="hljs-keyword">if</span> (NULL == pfile)
    {
        WRITELOGEX(LOG_ERROR, (<span class="hljs-string">"HdfsWriteTest(): hdfsOpenFile failed! szFilePath=<span class="hljs-variable">%s</span>,errno=<span class="hljs-variable">%d</span>."</span>, szTestFile, errno));
        <span class="hljs-keyword">return</span>;
    }
    WRITELOGEX(LOG_INFO, (<span class="hljs-string">"HdfsWriteTest(): hdfsOpenFile success! szFilePath=<span class="hljs-variable">%s</span>."</span>, szTestFile));

    iRet = hdfsWrite(pfs, pfile, <span class="hljs-string">"hello world!"</span>, strlen(<span class="hljs-string">"hello world!"</span>));           <span class="hljs-regexp">//</span> 写入数据
    <span class="hljs-keyword">if</span> (-<span class="hljs-number">1</span> == iRet)
    {
        WRITELOGEX(LOG_ERROR, (<span class="hljs-string">"HdfsWriteTest(): hdfsWrite failed! ret=<span class="hljs-variable">%d</span>,errno=<span class="hljs-variable">%d</span>."</span>, iRet, errno));
hdfsCloseFile(pfs, pfile);
        pfile = NULL;
        <span class="hljs-keyword">return</span>;
    }
WRITELOGEX(LOG_INFO, (<span class="hljs-string">"HdfsWriteTest(): hdfsWrite success! ret=<span class="hljs-variable">%d</span>."</span>, iRet));

    iRet = hdfsHFlush(pfs, pfile);                           <span class="hljs-regexp">//</span> 将缓冲区中数据写入磁盘
    <span class="hljs-keyword">if</span> (-<span class="hljs-number">1</span> == iRet)
    {
        WRITELOGEX(LOG_ERROR, (<span class="hljs-string">"HdfsWriteTest(): hdfsFlush failed! ret=<span class="hljs-variable">%d</span>,errno=<span class="hljs-variable">%d</span>."</span>, iRet, errno));
hdfsCloseFile(pfs, pfile);
        pfile = NULL;
        <span class="hljs-keyword">return</span>;
}
    WRITELOGEX(LOG_INFO, (<span class="hljs-string">"HdfsWriteTest(): hdfsFlush success! ret=<span class="hljs-variable">%d</span>."</span>, iRet));
    iRet = hdfsCloseFile(pfs, pfile);                               <span class="hljs-regexp">//</span> 关闭文件句柄，释放资源
    <span class="hljs-keyword">if</span> (-<span class="hljs-number">1</span> == iRet)
    {
        WRITELOGEX(LOG_ERROR, (<span class="hljs-string">"HdfsWriteTest(): hdfsCloseFile failed! ret=<span class="hljs-variable">%d</span>,errno=<span class="hljs-variable">%d</span>."</span>, iRet, errno));
        <span class="hljs-keyword">return</span>;
    }
    WRITELOGEX(LOG_INFO, (<span class="hljs-string">"HdfsWriteTest(): hdfsCloseFile success! ret=<span class="hljs-variable">%d</span>."</span>, iRet));
pfile = NULL;

    <span class="hljs-keyword">return</span>;
}

</code></pre>

<p><strong>3.遇到的主要问题描述与分析</strong> <br>
对于LIB HDFS接口的不足之处，在本文第三部分(LIB HDFS接口简介)已有大致描述。 <br>
在实际性能测试过程中，因LIB HDFS接口引起的问题主要包括：lease租约回收异常和程序句柄资源释放异常等两大类。 <br>
    我们换了多种测试模型，基本确认LIB HDFS接口在某些异常情况下(如频繁对同一个文件执行append操作)会产生上述问题。所以如果在项目中需要实际应用LIB HDFS接口，就需要我们改进客户端程序处理流程，尽量规避和减少上述问题的产生。可以采用如下方法： <br>
1)在客户端程序和HDFS文件系统间增加缓存的方式降低HDFS的读写密度； <br>
2)减少对HDFS文件系统的更新操作，例如文件写入完成后就不再执行append操作，只执行read操作。</p>

<p><strong>五、总结</strong> <br>
本文对HDFS和用C语言访问HDFS的操作进行了详细的介绍，可供相关项目的开发人员参考。 <br>
HDFS作为一种分布式文件系统，并不是万能的，例如并不适合于存储量太小或要求低访问延迟的应用场景，又或者需要频繁更新数据的系统。即使应用了HDFS文件系统，为了发挥HDFS文件系统的最大效率，仍可能需要通过我们修改业务分层或逻辑实现等手段来规避HDFS的一些缺点。</p>

<hr>

<p>本人微信公众号：zhouzxi，请扫描以下二维码： <br>
<img src="https://img-blog.csdn.net/20150812173903270" alt="这里写图片描述" title=""></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>