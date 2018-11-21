---
layout:     post
title:      hadoop05-HDFS的api操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="环境配置">环境配置</h2>



<h4 id="eclipse配置hdfs的环境">eclipse配置hdfs的环境</h4>

<ol>
<li>hadoop的插件hadoop-eclipse-plugin，放在eclipse的安装目录的plugins下</li>
</ol>



<h4 id="配置本地hadoop的环境">配置本地hadoop的环境</h4>

<ol>
<li>解压hadoop安装包</li>
<li>配置windows下hadoop的环境变量 <br>
<ul><li>新建：HADOOP_HOME=hadoop的解压路径，我的是：HADOOP_HOME=C:\soft\hadoop-2.7.6</li>
<li>在path下加上%HADOOP_HOME%bin;%HADOOP_HOME%sbin</li></ul></li>
<li>配置windows下hadoop的相关插件 <br>
<ul><li>hadoop.dll放在C:\Windows\System32下</li>
<li>winutils.exe放在hadoop的安装目录的bin目录下</li></ul></li>
<li>重启eclipse</li>
<li>配置eclipse可视化界面： <br>
<ul><li>启动eclipse；</li>
<li>windows下搜索hadoop，把hadoop的安装目录导过来，点击确定，应用</li>
<li>windows下showview选择other搜索mapreduce的location（配置之前保证hadoop集群是启动的）</li>
<li>右键—new hadoop location —</li>
<li>location name = 可以任意指定</li>
<li>左边指的是yarn的master，我的是：host：dadoop04,port:9001（ 注意,需要提前在路径C:\Windows\System32\drivers\etc\hosts下配置好主机映射）</li>
<li>右边是hdfs的主节点dfsmaster：dfsmaster的host：hadoop02，port：9000</li>
<li>username是登陆集群的用户名，我的是:hadoop</li>
<li>eclipse的可视化界面配置完成之后会出现一个DFS location</li></ul></li>
</ol>



<h3 id="java项目中添加依赖">java项目中添加依赖：</h3>

<ol>
<li>项目下面建包：lib下的buildpath。 <br>
<ul><li>好处：代码移动的时候比较方便，联通依赖包一起移动</li>
<li>缺点：会造成工程很臃肿，jar包冲突</li></ul></li>
<li>maven添加依赖 <br>
<ul><li>好处：代码比较轻便，自动解决jar包冲突的问题</li>
<li>缺点：代码移动的时候，需要重新构建</li></ul></li>
<li>创建本地用户依赖库</li>
</ol>



<h1 id="代码">代码</h1>



<h2 id="1测试hdfs的链接是否成功1">1.测试hdfs的链接是否成功1</h2>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">import</span> java.io.IOException;
<span class="hljs-keyword">import</span> org.apache.hadoop.conf.Configuration;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.FileSystem;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.Path;

<span class="hljs-javadoc">/**
 * 测试hdfs的链接是否成功
 *<span class="hljs-javadoctag"> @author</span> dai
 *
 */</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">testHdfs</span> {</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) <span class="hljs-keyword">throws</span> IOException {
        <span class="hljs-comment">//hdfs连接有两个重要的配置文件对象</span>
        <span class="hljs-comment">//对象01：配置文件对象  思考：作用？   配置文件从哪里来的？</span>
        Configuration conf=<span class="hljs-keyword">new</span> Configuration();
        <span class="hljs-comment">//对象02：文件系统对象 FileSystem 是分布式文件系统的对象。  文件系统的句柄</span>
        <span class="hljs-comment">//在这里fs就是hdfs文件系统的抽象</span>
        FileSystem fs=FileSystem.get(conf);
        System.out.println(fs);


        <span class="hljs-comment">//文件上传</span>
        <span class="hljs-javadoc">/**
         * 参数1：本地的文件路径
         * 参数2：
         */</span>
        <span class="hljs-comment">// path对象是hdfs的内置路径对象，类似于windows下的file</span>
        Path src=<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"C:\\soft\\hadoop-2.7.6\\LICENSE.txt"</span>);
        <span class="hljs-comment">//文件上传   这种方式传到哪里了=====还是本地。 默认情况下上传到工程所在的盘符下</span>
        <span class="hljs-comment">//上传成功之后生成两个文件：   testxxxx_tmp上传的原始文件，    还有一个testxxxx_tmp.crc文件，他是一个校验文件 </span>
        Path dst=<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/testxxxx_tmp"</span>);
        fs.copyFromLocalFile(src, dst);

    }

}
</code></pre>



<h2 id="2测试hdfs的链接是否成功2">2.测试hdfs的链接是否成功2</h2>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">import</span> java.io.IOException;
<span class="hljs-keyword">import</span> java.net.URI;
<span class="hljs-keyword">import</span> java.net.URISyntaxException;

<span class="hljs-keyword">import</span> org.apache.hadoop.conf.Configuration;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.FileSystem;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.Path;
<span class="hljs-keyword">import</span> org.apache.hadoop.hdfs.DistributedFileSystem;

<span class="hljs-javadoc">/**
 * 测试hdfs的链接是否成功
 *<span class="hljs-javadoctag"> @author</span> dai
 *
 */</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">testHdfs2</span> {</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) <span class="hljs-keyword">throws</span> IOException, InterruptedException, URISyntaxException {
        <span class="hljs-comment">//System.setProperty("HADOOP_USER_NAME", "hadoop");</span>
        <span class="hljs-javadoc">/**
         * 文件上传成功了，提前设定集群中的副本为2个，  通过这种方式上传的文件的副本还是3个，为什么？
         *因为这里加载的配置文件，不是集群的，而代码中jar包中的。
         * 这里的conf对象默认加载的是hadoop-hdfs-2.7.6.jar包下的hdfs-default.xml文件
         * conf对象就是加载以下配置文件
         *      hdfs-default.xml
         *      core-default.xml
         *      yarn-default.xml
         *      mapred-default.xml
         *      hdfs-site.xml
         *      core-site.xml
         *      yarn-site.xml
         *      mapred-site.xml
         * conf对象加载配置文件的时候，加载次序：
         * jar包下加载》》》》src下加载》》》》 hdfs-default.xml   hdfs-site.xml&gt;&gt;&gt;代码中的
         * 生效的顺序：
         *  代码中的》》》》src下的》》》》jar包中的
         * 
         */</span>
        Configuration conf=<span class="hljs-keyword">new</span> Configuration();
        <span class="hljs-comment">//conf.set("dfs.replication", "5");</span>
        <span class="hljs-comment">//强制加载配置文件  加载指定的配置文件</span>
        <span class="hljs-comment">//conf.addResource("config/hdfs-site.xml");</span>
        <span class="hljs-comment">//conf.set("HADOOP_USER_NAME", "hadoop");</span>

        <span class="hljs-javadoc">/**
         * 在配置文件中，两个参数是k-v形式存在的
         *  &lt;property&gt;
         &lt;name&gt;fs.defaultFS&lt;/name&gt;
         &lt;value&gt;hdfs://hadoop01:9000&lt;/value&gt;
         &lt;/property&gt;
         key：&lt;name&gt;fs.defaultFS&lt;/name&gt;
         value：&lt;value&gt;hdfs://hadoop01:9000&lt;/value&gt;
         设置分布式文件系统的入口
         */</span>
        <span class="hljs-comment">//conf.set("fs.defaultFS", "hdfs://hadoop01:9000");</span>
        <span class="hljs-comment">//FileSystem fs=FileSystem.get(conf);</span>


        <span class="hljs-javadoc">/**
         * 参数1：hdfs集群连接的主节点入口
         */</span>
        FileSystem fs=FileSystem.get(<span class="hljs-keyword">new</span> URI(<span class="hljs-string">"hdfs://hadoop01:9000"</span>), conf, <span class="hljs-string">"hadoop"</span>);
        System.out.println(fs <span class="hljs-keyword">instanceof</span> DistributedFileSystem);
        <span class="hljs-comment">//当前的src是否可以是hdfs路径，汇报权限问题</span>
        <span class="hljs-javadoc">/**
         * 修改权限的集中方式：
         *  1）代码提交的时候进行设置参数
         *      右键》》》》run as&gt;&gt;&gt;&gt;  run configurations
         *      arguments&gt;&gt;&gt;&gt;&gt;&gt;&gt;
         *      program arguments:代码中需要的参数   String[] args
         *      VM arguments: 代码运行中  jvm的参数
         *          jvm提交代码的时候的用户
         *          将用户名已添加上
         *      -DHADOOP_USER_NAME=hadoop
         * 2）在代码中添加：
         *  conf.set("HADOOP_USER_NAME", "hadoop");
         * 
         *  System.setProperty("HADOOP_USER_NAME", "hadoop");
         *  FileSystem fs=FileSystem.get(new URI("hdfs://hadoop01:9000"), conf, "hadoop");
         * 3) 在windows的环境变量中
         *  HADOOP_USER_NAME=hadoop
         *
         *如果代码中和运行的时候都指定了用户名：
         *先加载运行指定的参数  在加载代码中，代码中的参数会覆盖掉运行指定的参数
         *最后生效的是代码中的
         */</span>

        Path src=<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/cc/testcrc"</span>);
        Path dst=<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"C:\\test_crc_04"</span>);
        <span class="hljs-javadoc">/**
         * 文件上传成功之后会生成两个文件 ：原始文件、CRC文件
         *      linux下查看一下：
         *          blk_1073741825    原始文件
                    blk_1073741825_1001.meta    元数据文件----crc文件   校验  存储的是原始文件的偏移量
                    校验数据的完整性    验证数据是否被修改了或损坏了   校验的时候与文件上传的时候的数据进行校验
         *          1)块的末尾追加不会影响校验
         *          2）块的中间追加，会影响校验，产生校验和错误
         *          结论：crc校验，从原始文件头---原始文件末尾  的校验，如果这部分内容不发生改变，不会影响校验    这部分内容发生改变的修啊吧则会报校验和错误 。
         * 
         * Exception in thread "main" org.apache.hadoop.fs.ChecksumException: Checksum error: /cc/testcrc at 0 exp: 1139544428 got: -1322830295
         * 文件块损坏的异常
         */</span>
        <span class="hljs-comment">//fs.copyFromLocalFile(src, dst);</span>
        fs.copyToLocalFile(src, dst);
    }

}
</code></pre>



<h2 id="3用流的方式进行文件上传和下载">3.用流的方式进行文件上传和下载</h2>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">import</span> java.io.FileInputStream;
<span class="hljs-keyword">import</span> java.io.FileOutputStream;
<span class="hljs-keyword">import</span> java.io.IOException;
<span class="hljs-keyword">import</span> java.net.URI;
<span class="hljs-keyword">import</span> java.net.URISyntaxException;

<span class="hljs-keyword">import</span> org.apache.hadoop.conf.Configuration;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.FSDataInputStream;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.FSDataOutputStream;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.FileSystem;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.Path;
<span class="hljs-keyword">import</span> org.apache.hadoop.io.IOUtils;

<span class="hljs-javadoc">/**
 * 用流的方式进行文件上传和下载
 *<span class="hljs-javadoctag"> @author</span> dai
 *
 */</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">testHdfsStream</span> {</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) <span class="hljs-keyword">throws</span> IOException, InterruptedException, URISyntaxException {
        Configuration conf=<span class="hljs-keyword">new</span> Configuration();
        FileSystem fs=FileSystem.get(<span class="hljs-keyword">new</span> URI(<span class="hljs-string">"hdfs://hadoop01:9000"</span>), conf, <span class="hljs-string">"hadoop"</span>);

        <span class="hljs-comment">//文件上传</span>
        <span class="hljs-comment">/*//本地流，输入流
        FileInputStream in=new FileInputStream("C:\\testcrc.txt");
        //hdfs的输出流   create()   创建输出流
        FSDataOutputStream out = fs.create(new Path("/testcrc01"));
        //参数4：是否关闭流  true  关闭    false  不关闭
        IOUtils.copyBytes(in, out, 4096,true);*/</span>

        <span class="hljs-comment">//文件下载     hdfs（hdfs的输入流</span>
        FSDataInputStream in01 = fs.open(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/testcrc01"</span>));
        in01.seek(<span class="hljs-number">5</span>);
        <span class="hljs-comment">//本地的输出流</span>
        FileOutputStream out01=<span class="hljs-keyword">new</span> FileOutputStream(<span class="hljs-string">"C:\\testcrc03"</span>);
        IOUtils.copyBytes(in01, out01, <span class="hljs-number">4096</span>, <span class="hljs-keyword">true</span>);
        fs.close();


    }

}
</code></pre>



<h2 id="4创建删除上传下载文件等api操作">4.创建，删除，上传，下载文件等api操作</h2>



<pre class="prettyprint"><code class=" hljs avrasm">import java<span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.IOException</span><span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.net</span><span class="hljs-preprocessor">.URI</span><span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.net</span><span class="hljs-preprocessor">.URISyntaxException</span><span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.Arrays</span><span class="hljs-comment">;</span>

import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.conf</span><span class="hljs-preprocessor">.Configuration</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.fs</span><span class="hljs-preprocessor">.BlockLocation</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.fs</span><span class="hljs-preprocessor">.FileStatus</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.fs</span><span class="hljs-preprocessor">.FileSystem</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.fs</span><span class="hljs-preprocessor">.LocatedFileStatus</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.fs</span><span class="hljs-preprocessor">.Path</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.fs</span><span class="hljs-preprocessor">.RemoteIterator</span><span class="hljs-comment">;</span>

public class testHdfsDemo01 {
    public static void main(String[] args) throws InterruptedException, URISyntaxException, IOException {
        Configuration conf=new Configuration()<span class="hljs-comment">;</span>
        FileSystem fs=FileSystem<span class="hljs-preprocessor">.get</span>(new URI(<span class="hljs-string">"hdfs://hadoop01:9000"</span>),conf,<span class="hljs-string">"hadoop"</span>)<span class="hljs-comment">;</span>
        //创建文件夹：级联创建,相当于hadoop fs  -mkdir -p
        <span class="hljs-comment">/*boolean ifmk=fs.mkdirs(new Path("/xxxx/aa/bb/cc"));
        System.out.println(ifmk);*/</span>
        //删除文件夹：文件   空目录  非空目录
        //fs<span class="hljs-preprocessor">.delete</span>(new Path(<span class="hljs-string">"/cc"</span>))<span class="hljs-comment">;</span>
        //参数<span class="hljs-number">1</span>：删除的路径   参数<span class="hljs-number">2</span>：是否递归删除  true  需要递归  false：不递归
        //fs<span class="hljs-preprocessor">.delete</span>(new Path(<span class="hljs-string">"/xxxx"</span>), true)<span class="hljs-comment">;</span>
        <span class="hljs-comment">/*fs.copyFromLocalFile(src, dst);//上传
        fs.copyToLocalFile(src, dst);//下载
*/</span>      

        //查看指定目录下的文件信息，相当于hadoop fs -ls /
        RemoteIterator&lt;LocatedFileStatus&gt; listFiles = fs<span class="hljs-preprocessor">.listFiles</span>(new Path(<span class="hljs-string">"/"</span>), false)<span class="hljs-comment">;</span>
        //循环遍历输出
        while(listFiles<span class="hljs-preprocessor">.hasNext</span>()){
            //System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(listFiles<span class="hljs-preprocessor">.next</span>())<span class="hljs-comment">;</span>
            //LocatedFileStatus   描述hdfs上的文件或目录信息的对象
            LocatedFileStatus next = listFiles<span class="hljs-preprocessor">.next</span>()<span class="hljs-comment">;</span>
            System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(next<span class="hljs-preprocessor">.getPath</span>())<span class="hljs-comment">;</span>
            System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(next<span class="hljs-preprocessor">.getLen</span>())<span class="hljs-comment">;//获取数据的真实长度</span>
            System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(next<span class="hljs-preprocessor">.getBlockSize</span>())<span class="hljs-comment">;//获取块的大小</span>

            <span class="hljs-comment">/*//获取的是块存储信息
            */</span><span class="hljs-comment">/**
             * BlockLocation   封装的是数据块的详细信息
             * 数据块的偏移量+数据块的存储位置（副本）
             * 0（数据块的其实偏移量）,119978（块的实际大小）,hadoop02,hadoop01（存储位置）
             * 
             */</span>
            BlockLocation[] blockLocations = next<span class="hljs-preprocessor">.getBlockLocations</span>()<span class="hljs-comment">;</span>
            System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"999999999999999999999999999999"</span>)<span class="hljs-comment">;</span>
            for(BlockLocation bl:blockLocations){
                System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(bl<span class="hljs-preprocessor">.getOffset</span>())<span class="hljs-comment">;//获取块的起始偏移量</span>
                System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(bl<span class="hljs-preprocessor">.getLength</span>())<span class="hljs-comment">;//获取块的实际的大小</span>
                System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(Arrays<span class="hljs-preprocessor">.toString</span>(bl<span class="hljs-preprocessor">.getHosts</span>()))<span class="hljs-comment">;//获取存储的主机   数组</span>
            }

        }

        //获取状态信息  返回的是状态信息对象   FileStatus  封装的是一个路径的信息
        //这种方式只能返回文件或目录的状态信息   不能返回块信息   获取文件或目录的信息都可以
    <span class="hljs-comment">/*  FileStatus[] listStatus = fs.listStatus(new Path("/"));

        for(FileStatus ff:listStatus){
            System.out.println("----------------------");
            System.out.println(ff.getPath());
            System.out.println(ff.getLen());
            System.out.println(ff.getReplication());
            System.out.println(ff.getBlockSize());
        }*/</span>
        fs<span class="hljs-preprocessor">.close</span>()<span class="hljs-comment">;</span>
    }

}
</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>