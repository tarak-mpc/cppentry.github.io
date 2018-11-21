---
layout:     post
title:      大数据之hadoop【hdfs】
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="目录">目录</h1>

<pre><code>    1、HDFS体系结构
    2、HDFS Shell操作
    3、HDFS Java API
    4、HDFS和RPC
    5、HDFS High Availability
    6、HDFS数据回收和简单运维
</code></pre>

<p>==============================================================</p>

<p>Hadoop <br>
   HDFS <br>
      Namenode <br>
        管理datanode， <br>
        接收client的操作 <br>
      Datanode <br>
        只负责存储数据 <br>
   Yarn <br>
     MapReduce <br>
     …storm spark flink <br>
    ResourceManager <br>
        从整体上对资源的一个管理和调度 <br>
    NodeManager <br>
  具体的管理和调度</p>

<p>=================================</p>



<h2 id="hdfs">HDFS</h2>

<pre><code>HDFS特点
    分布式
    数据量越来越多，在一个操作系统管辖的范围存不下了，那么就分配到更多的操作系统管理的磁盘中，但是不方便管理和维护，因此迫切需要一种系统来管理多台机器上的文件，这就是分布式文件管理系统 。
    高可用
        是一种允许文件通过网络在多台主机上分享的文件系统，可让多机器上的多用户分享文件和存储空间。
    通透性
        实际上是通过网络来访问文件的动作，由程序与用户看来，就像是访问本地的磁盘一般。
    容错
        即使系统中有某些节点脱机，整体来说系统仍然可以持续运作而不会有数据损失。
        分布式文件管理系统很多，hdfs只是其中一种，不合适小文件。
java中操作大文件的一个包：java.nio包
</code></pre>

<p>====================================================</p>

<p>NameNode <br>
        是整个文件系统的管理节点。它维护着整个文件系统的文件目录树，文件/目录的 <br>
        元信息和每个文件对应的数据块列表。接收用户的操作请求。 <br>
        文件包括： <br>
            1)fsimage:元数据镜像文件。存储某一时段NameNode内存元数据信息。 <br>
            2)edits:操作日志文件。 <br>
            3)fstime:保存最近一次checkpoint的时间 <br>
        查看fsimage元数据信息可以通过命令将其转化为普通xml文件来进行查看 <br>
            bin/hdfs oiv -p XML -i inputfile[具体的文件路径] -o  outputfile <br>
            eg. <br>
            [root@master current]# pwd <br>
                /opt/hadoop-repo/name/current <br>
            current]# hdfs oiv -p XML -i fsimage_0000000000000000116 -o fsimage.xml <br>
        查看edits操作日志 <br>
            bin/hdfs oev -p XML -i inputfile[具体的文件路径] -o  outputfile <br>
            eg. <br>
            [root@master current]# pwd <br>
                /opt/hadoop-repo/name/current <br>
            current]# hdfs oiv -p XML -i  edits_inprogress_0000000000000000119 -o edits.xml <br>
    DateNode <br>
        说明:datanode中的VERSION中的clusterID必须要和namenode中的VERSION中的clusterID保持一致。 <br>
            不一致的原因：多次格式化造成(没有清空 name|data|secondary|tmp目录中的数据) <br>
        提供真实文件数据的存储服务。 <br>
        文件块（block）：最基本的存储单位。对于文件内容而言，一个文件的长度大小是size，那么从文件的０偏移开始，按照固定的大小，顺序对文件进行划分并编号，划分好的每一个块称一个Block。HDFS默认Block大小是128MB，以一个256MB文件，共有256/128=2个Block。 <br>
        不同于普通文件系统的是，HDFS中，如果一个文件小于一个数据块的大小，并不占用整个数据块存储空间 <br>
        Replication。多复本。默认是三个。</p>

<p>==================================================== <br>
HDFS shell <br>
        data]# hdfs dfs -appendToFile append.txt /hello 向/hello中追加内容append.txt <br>
        data]# hdfs dfs -cp /hello /hello1 将/hello拷贝到/hello1 <br>
    HDFS JAVA</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">HDFStest</span> {</span>
    <span class="hljs-comment">/*列出目录的内容 listStatus
      读取文件 open
      创建目 mkdirs
      创建文件 create
      显示文件存储位置getFileBlockLocations
     删除文件或目录 delete
    */</span>
    <span class="hljs-comment">//Permission    Owner   Group   Size    Replication Block Size  Name</span>
    FileSystem fileSystem;
    Configuration configuration;
    <span class="hljs-annotation">@Before</span>
    <span class="hljs-keyword">public</span>  <span class="hljs-keyword">void</span> <span class="hljs-title">setUp</span>() <span class="hljs-keyword">throws</span> Exception{
        URI uri=<span class="hljs-keyword">new</span> URI(<span class="hljs-string">"hdfs://master:9000/"</span>);
        configuration=<span class="hljs-keyword">new</span> Configuration();
        fileSystem=FileSystem.get(uri,configuration);
<span class="hljs-comment">//        System.out.println(fileSystem);</span>
    }
    <span class="hljs-comment">//获取文件列表状态</span>
    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span>  <span class="hljs-keyword">void</span>  <span class="hljs-title">testListStatus</span>()<span class="hljs-keyword">throws</span> IOException{
<span class="hljs-comment">//        根路径</span>
        Path path=<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/"</span>);
        FileStatus[] fileStatuses = fileSystem.listStatus(path);
        <span class="hljs-keyword">for</span> (FileStatus f: fileStatuses) {
            FsPermission permission = f.getPermission();
            String fp_prefix = <span class="hljs-string">"-"</span>;
            <span class="hljs-keyword">if</span> (f.isDirectory()){
                fp_prefix = <span class="hljs-string">"d"</span>;
            }
            FsAction userAction=permission.getUserAction();
            FsAction groupAction = permission.getGroupAction();
            FsAction otherAction = permission.getOtherAction();
            String acl = fp_prefix+userAction.SYMBOL+groupAction.SYMBOL+otherAction.SYMBOL;
            String owner = f.getOwner();
            String group= f.getGroup();
            <span class="hljs-keyword">long</span> size=f.getLen();
            <span class="hljs-keyword">short</span> replication =f.getReplication();
            <span class="hljs-keyword">long</span> blockSize = f.getBlockSize();
            String name = f.getPath().getName();
            String mTime = <span class="hljs-keyword">new</span> SimpleDateFormat(<span class="hljs-string">"yyyy-MM-dd hh:mm"</span>).format(<span class="hljs-keyword">new</span> Date(f.getModificationTime()));
            System.out.println(acl + <span class="hljs-string">" "</span> + replication + <span class="hljs-string">" "</span> + owner + <span class="hljs-string">" "</span> +
                    group + <span class="hljs-string">" "</span> + size + <span class="hljs-string">" "</span> + mTime + <span class="hljs-string">" "</span> + name);
        }
    }
    <span class="hljs-comment">//读取文件 open</span>
    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">testRead</span>() <span class="hljs-keyword">throws</span> IOException {
        Path path = <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/hello"</span>);
        FSDataInputStream fis = fileSystem.open(path);
<span class="hljs-comment">//        BufferedReader br=new BufferedReader(new InputStreamReader(fis));</span>
<span class="hljs-comment">//        String  line=null;</span>
<span class="hljs-comment">//        while ((line = br.readLine())!=null)</span>
<span class="hljs-comment">//        {</span>
<span class="hljs-comment">//            System.out.println(line);</span>
<span class="hljs-comment">//        }</span>
<span class="hljs-comment">//        br.close();</span>
<span class="hljs-comment">//        第二种读取方法</span>
        IOUtils.copyBytes(fis,System.out,<span class="hljs-number">1024</span>,<span class="hljs-keyword">false</span>);
        IOUtils.closeStream(fis);
    }
<span class="hljs-comment">//   创建目录 mkdirs</span>
     <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">testMkdir</span>() <span class="hljs-keyword">throws</span> IOException {
         Path path = <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/mutil-dir"</span>);
         <span class="hljs-keyword">boolean</span> ret = fileSystem.mkdirs(path);
         Assert.assertEquals(<span class="hljs-keyword">true</span>,ret);

     }
<span class="hljs-comment">//     创建文件 create</span>
    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">testCreateFile</span>() <span class="hljs-keyword">throws</span> IOException {
        Path path = <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/mutil-dir1/readme.txt"</span>);
<span class="hljs-comment">//        boolean ret = fileSystem.createNewFile(path);</span>
<span class="hljs-comment">//        Assert.assertEquals(true,ret);</span>
        FSDataOutputStream fos = fileSystem.create(path);
        <span class="hljs-keyword">byte</span>[] bytes= <span class="hljs-string">"你好三毛"</span>.getBytes();
        fos.write(bytes);
        fos.close();
        <span class="hljs-comment">//推荐使用第二种</span>
    }
    <span class="hljs-comment">//显示文件存储位置 getFileBlockLocations</span>
    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">testLocation</span>() <span class="hljs-keyword">throws</span> IOException {
        Path path = <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/mutil-dir1/readme.txt"</span>);
        FileStatus fs=fileSystem.getFileStatus(path);
        <span class="hljs-keyword">long</span> len = fs.getLen();
        BlockLocation[] locations = fileSystem.getFileBlockLocations(path,<span class="hljs-number">0</span>,len);
        <span class="hljs-keyword">for</span> (BlockLocation location: locations) {
            String[] hosts = location.getHosts();
            String[] names = location.getNames();
            <span class="hljs-keyword">long</span> length = location.getLength();
            System.out.println(Arrays.toString(hosts));
            System.out.println(Arrays.toString(names));
            System.out.println(<span class="hljs-string">"length "</span>+ length);
        }
    }
    <span class="hljs-comment">//删除文件或目录</span>
    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">testDelete</span>() <span class="hljs-keyword">throws</span> IOException {
        Path path = <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/mutil-dir1/"</span>);
        <span class="hljs-keyword">boolean</span> ret = fileSystem.delete(path,<span class="hljs-keyword">true</span>);
        Assert.assertEquals(<span class="hljs-keyword">true</span>,ret);
    }
</code></pre>

<p>=====================================================</p>



<h1 id="hdfs和rpc">HDFS和RPC</h1>

<blockquote>
  <p>Hadoop的整个体系结构就是构建在RPC之上的(org.apache.hadoop.ipc)。 <br>
      RPC(Remote Procedure Call)——远程过程调用协议，它是一种通过网络从远程计算机程序上请求服务，而不需要了解底层网络技术的协议。RPC协议假定某些传输协议的存在，如TCP或UDP，为了通信程序之间携带信息数据。在OSI网络通信模型中，RPC跨越了传输层和应用层。RPC使得包括网络分布式多程序在内的应用程序更加容易。 <br>
      RPC采用客户机/服务器模式。请求程序就是一个客户机，而服务提供程序就是一个服务器。首先，客户机调用进程发送一个带参数的调用信息到服务进程，然后等待响应信息。在服务器端，进程保持睡眠状态直到调用信息到达为止。当一个调用信息到达，服务器获得进程参数，计算结果，发送答复信息，然后等待下一个调用信息，最后，客户端调用进程接收答复信息，获取进程结果，最后调用执行。</p>
</blockquote>

<p><img src="https://img-blog.csdn.net/20161022142909473" alt="这里写图片描述" title=""></p>



<h2 id="rpc代码演示">RPC代码演示</h2>

<p>服务接口</p>

<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">package</span> com.sanmao.hadoop_02.rpc;

<span class="hljs-keyword">import</span> org.apache.hadoop.ipc.VersionedProtocol;


<span class="hljs-comment">//要继承VersionedProtocol 这个通信协议</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">interface</span> <span class="hljs-title">IHelloService</span> <span class="hljs-keyword">extends</span> <span class="hljs-title">VersionedProtocol</span>{</span>
    <span class="hljs-keyword">long</span> versionID=<span class="hljs-number">123456789</span>L;
    <span class="hljs-keyword">public</span>  String <span class="hljs-title">sayHello</span>(String name);
    <span class="hljs-keyword">public</span> String <span class="hljs-title">heartBeat</span>(String status);
}
</code></pre>

<p>具体服务类</p>

<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">package</span> com.sanmao.hadoop_02.rpc;

<span class="hljs-keyword">import</span> org.apache.hadoop.ipc.ProtocolSignature;

<span class="hljs-keyword">import</span> java.io.IOException;

<span class="hljs-javadoc">/**
 * Created by kkk on 2016/10/21.
 */</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">HelloServiceImpl</span> <span class="hljs-keyword">implements</span> <span class="hljs-title">IHelloService</span>{</span>
    <span class="hljs-keyword">public</span> String <span class="hljs-title">sayHello</span>(String name) {
        System.out.println(<span class="hljs-string">"hello 方法被调用了"</span>);
        <span class="hljs-keyword">return</span>  <span class="hljs-string">"hello "</span>+name;
    }

    <span class="hljs-keyword">public</span> String <span class="hljs-title">heartBeat</span>(String status) {
        System.out.println(<span class="hljs-string">"心跳检测"</span>);
        <span class="hljs-keyword">return</span> <span class="hljs-string">"心跳响应  "</span>+ status;
    }

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">long</span> <span class="hljs-title">getProtocolVersion</span>(String s, <span class="hljs-keyword">long</span> l) <span class="hljs-keyword">throws</span> IOException {
        <span class="hljs-keyword">return</span> IHelloService.versionID;
    }

    <span class="hljs-keyword">public</span> ProtocolSignature <span class="hljs-title">getProtocolSignature</span>(String s, <span class="hljs-keyword">long</span> l, <span class="hljs-keyword">int</span> i) <span class="hljs-keyword">throws</span> IOException {
        <span class="hljs-comment">//新建一个签名</span>
        <span class="hljs-keyword">return</span> <span class="hljs-keyword">new</span> ProtocolSignature();
    }
}
</code></pre>

<p>RPC 服务器</p>



<pre class="prettyprint"><code class=" hljs avrasm">package <span class="hljs-keyword">com</span><span class="hljs-preprocessor">.sanmao</span><span class="hljs-preprocessor">.hadoop</span>_02<span class="hljs-preprocessor">.rpc</span><span class="hljs-comment">;</span>

import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.conf</span><span class="hljs-preprocessor">.Configuration</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.ipc</span><span class="hljs-preprocessor">.RPC</span><span class="hljs-comment">;</span>

import java<span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.IOException</span><span class="hljs-comment">;</span>

public class RPCDriver {
    public static void main(String[] args) throws IOException {
        Configuration configuration = new Configuration()<span class="hljs-comment">;</span>
        RPC<span class="hljs-preprocessor">.Builder</span> builder = new RPC<span class="hljs-preprocessor">.Builder</span>(configuration)<span class="hljs-comment">;</span>
        RPC<span class="hljs-preprocessor">.Server</span> server = builder<span class="hljs-preprocessor">.setBindAddress</span>(<span class="hljs-string">"localhost"</span>)<span class="hljs-preprocessor">.setPort</span>(<span class="hljs-number">8888</span>)
                <span class="hljs-preprocessor">.setProtocol</span>(IHelloService<span class="hljs-preprocessor">.class</span>)
                <span class="hljs-preprocessor">.setInstance</span>(new HelloServiceImpl())<span class="hljs-preprocessor">.build</span>()<span class="hljs-comment">;</span>
        server<span class="hljs-preprocessor">.start</span>()<span class="hljs-comment">;</span>
        System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"RPC服务器开启"</span>)<span class="hljs-comment">;</span>
    }
}
</code></pre>

<p>RPC  客户端</p>



<pre class="prettyprint"><code class=" hljs avrasm">package <span class="hljs-keyword">com</span><span class="hljs-preprocessor">.sanmao</span><span class="hljs-preprocessor">.hadoop</span>_02<span class="hljs-preprocessor">.rpc</span><span class="hljs-comment">;</span>

import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.conf</span><span class="hljs-preprocessor">.Configuration</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.ipc</span><span class="hljs-preprocessor">.RPC</span><span class="hljs-comment">;</span>

import java<span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.IOException</span><span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.net</span><span class="hljs-preprocessor">.InetSocketAddress</span><span class="hljs-comment">;</span>


public class RPCClient {
     <span class="hljs-comment">/*Class&lt;T&gt; protocol,
        long clientVersion,
        InetSocketAddress addr,
        Configuration conf*/</span>
    //RPC底层采取的就是TPC/IP协议
     public static void main(String[] args) throws IOException, InterruptedException {
         Configuration configuration= new Configuration()<span class="hljs-comment">;</span>
         InetSocketAddress inetSocketAddress=new InetSocketAddress(<span class="hljs-string">"localhost"</span>,<span class="hljs-number">8888</span>)<span class="hljs-comment">;</span>
         IHelloService proxy = RPC<span class="hljs-preprocessor">.getProxy</span>(IHelloService<span class="hljs-preprocessor">.class</span>, IHelloService<span class="hljs-preprocessor">.versionID</span>, inetSocketAddress, configuration)<span class="hljs-comment">;</span>
         //SOAP协议
         String result = proxy<span class="hljs-preprocessor">.sayHello</span>(<span class="hljs-string">"三毛"</span>)<span class="hljs-comment">;</span>
         System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(result)<span class="hljs-comment">;</span>
         while (true){
             String <span class="hljs-keyword">ret</span> = proxy<span class="hljs-preprocessor">.heartBeat</span>(System<span class="hljs-preprocessor">.currentTimeMillis</span>() + <span class="hljs-string">""</span>)<span class="hljs-comment">;</span>
             System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-keyword">ret</span>)<span class="hljs-comment">;</span>
             Thread<span class="hljs-preprocessor">.sleep</span>(<span class="hljs-number">3000</span>)<span class="hljs-comment">;</span>
         }
     }
}
</code></pre>



<h2 id="hdfs调用之数据存储读文件解析">HDFS调用之数据存储读文件解析</h2>

<p><img src="https://img-blog.csdn.net/20161022143457257" alt="这里写图片描述" title=""></p>

<h2 id="hdfs调用之数据存储写文件解析">HDFS调用之数据存储写文件解析</h2>

<p><img src="https://img-blog.csdn.net/20161022143707026" alt="这里写图片描述" title=""> <br>
三个关键的接口 <br>
ClientProtocol <br>
  是客户端(FileSystem)与NameNode通信的接口。 <br>
DatanodeProtocol <br>
  是DataNode与NameNode通信的接口。 <br>
NamenodeProtocol <br>
  是SecondaryNameNode与NameNode通信的接口</p>



<h2 id="常见的hdfs运维">常见的HDFS运维</h2>

<pre><code>    修改回收站清空数据的时间
       在每个节点(不仅仅是主节点)上添加配置 core-site.xml,增加如下内容
       &lt;property&gt;
          &lt;name&gt;fs.trash.interval&lt;/name&gt;
          &lt;value&gt;1440&lt;/value&gt;
          &lt;description&gt;单位是分钟&lt;/description&gt;
       &lt;/property&gt;
    hdfs dfsadmin -safemode leave ——&gt;hdfs离开受保护模式
    查看磁盘内容
        df -lh /
        查看具体目录下面文件大小
        du -lh --max-depth=1 path
    查看hdfs
        hdfs dfs -du -lh hdfs_path
        hdfs dfs -df hdfs_path
    查看磁盘健康状况
        hdfs fsck -blocks hdfs_path
</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>