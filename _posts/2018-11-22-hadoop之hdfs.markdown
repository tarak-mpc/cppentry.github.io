---
layout:     post
title:      hadoop之hdfs
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/kaikai_sk/article/details/77623377				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>1.对hdfs的操作方式：hadoop fs xxx <br>
  hadoop fs -ls  /  查看hdfs的根目录下的内容的 <br>
  hadoop fs -lsr /  递归查看hdfs的根目录下的内容的 <br>
  hadoop fs -mkdir /d1  在hdfs上创建文件夹d1 <br>
  hadoop fs -put   把数据从linux上传到hdfs的特定路径中 <br>
  hadoop fs -get   把数据从hdfs下载到linux的特定路径下 <br>
  hadoop fs -text hdfs文件    查看hdfs中的文件 <br>
  hadoop fs -rmr    删除hdfs中的文件夹</p>

<p>hadoop fs -ls hdfs://hadoop0:9000/</p>

<p>2.HDFS的datanode在存储数据时，如果原始文件大小&gt;64MB,按照64MB大小切分；如果&lt;64MB，只有一个block，占用磁盘空间是源文件实际大小。</p>

<p>3.RPC(remote procedure call) <br>
  不同java进程间的对象方法的调用。 <br>
  一方称作服务端(server)，一方称作客户端(client)。 <br>
  server端提供对象，供客户端调用的，被调用的对象的方法的执行发生在server端。</p>

<p>RPC是hadoop框架运行的基础。 <br>
4.通过rpc小例子获得的认识？</p>

<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">package</span> rpc;

<span class="hljs-keyword">import</span> java.io.IOException;

<span class="hljs-keyword">import</span> org.apache.hadoop.conf.Configuration;
<span class="hljs-keyword">import</span> org.apache.hadoop.ipc.RPC;
<span class="hljs-keyword">import</span> org.apache.hadoop.ipc.RPC.Server;

<span class="hljs-keyword">import</span> com.sun.org.apache.bcel.internal.generic.NEW;

<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">MyServer</span>
{</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">final</span> String SERVER_ADDRESS=<span class="hljs-string">"localhost"</span>;
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">final</span> <span class="hljs-keyword">int</span> SERVER_PORT=<span class="hljs-number">12345</span>;

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) <span class="hljs-keyword">throws</span> Exception
    {
          <span class="hljs-javadoc">/** Construct an RPC server.
         *<span class="hljs-javadoctag"> @param</span> instance the instance whose methods will be called
         *<span class="hljs-javadoctag"> @param</span> bindAddress the address to bind on to listen for connection
         *<span class="hljs-javadoctag"> @param</span> port the port to listen for connections on
         *<span class="hljs-javadoctag"> @param</span> conf the configuration to use
         */</span>
        Server server = RPC.getServer(<span class="hljs-keyword">new</span> MyBiz(), SERVER_ADDRESS, SERVER_PORT, <span class="hljs-keyword">new</span> Configuration());
        server.start();

    }
}
</code></pre>



<pre class="prettyprint"><code class=" hljs avrasm">package rpc<span class="hljs-comment">;</span>

import java<span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.IOException</span><span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.net</span><span class="hljs-preprocessor">.InetSocketAddress</span><span class="hljs-comment">;</span>

import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.conf</span><span class="hljs-preprocessor">.Configuration</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.ipc</span><span class="hljs-preprocessor">.RPC</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.ipc</span><span class="hljs-preprocessor">.VersionedProtocol</span><span class="hljs-comment">;</span>

public class MyClient
{
    public static void main(String[] args) throws Exception
    {
        MyBizable proxy = (MyBizable)RPC<span class="hljs-preprocessor">.waitForProxy</span>(
                MyBizable<span class="hljs-preprocessor">.class</span>,
                MyBizable<span class="hljs-preprocessor">.VERSION</span>, 
                new InetSocketAddress(MyServer<span class="hljs-preprocessor">.SERVER</span>_ADDRESS, MyServer<span class="hljs-preprocessor">.SERVER</span>_PORT), 
                new Configuration())<span class="hljs-comment">;</span>
        String str = proxy<span class="hljs-preprocessor">.hello</span>(<span class="hljs-string">"world"</span>)<span class="hljs-comment">;</span>
        System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"客户端调用结果："</span>+str)<span class="hljs-comment">;</span>
        RPC<span class="hljs-preprocessor">.stopProxy</span>(proxy)<span class="hljs-comment">;</span>
    }
}
</code></pre>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">package</span> rpc;

<span class="hljs-keyword">import</span> java.io.IOException;

<span class="hljs-keyword">import</span> org.apache.hadoop.ipc.VersionedProtocol;

<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">MyBiz</span> <span class="hljs-keyword">implements</span> <span class="hljs-title">VersionedProtocol</span>, <span class="hljs-title">MyBizable</span>
{</span>
    <span class="hljs-comment">/* (non-Javadoc)
     * @see rpc.MyBizable#hello(java.lang.String)
     */</span>
    <span class="hljs-annotation">@Override</span>
    <span class="hljs-keyword">public</span> String <span class="hljs-title">hello</span>(String name)
    {
        System.out.println(<span class="hljs-string">"我被调用了"</span>);
        <span class="hljs-keyword">return</span> <span class="hljs-string">"hello "</span>+name;
    }

    <span class="hljs-annotation">@Override</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">long</span> <span class="hljs-title">getProtocolVersion</span>(String protocol, <span class="hljs-keyword">long</span> clientVersion) <span class="hljs-keyword">throws</span> IOException
    {
        <span class="hljs-keyword">return</span> MyBizable.VERSION;
    }
}</code></pre>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">package</span> rpc;

<span class="hljs-keyword">import</span> org.apache.hadoop.ipc.VersionedProtocol;

<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">interface</span> <span class="hljs-title">MyBizable</span> <span class="hljs-keyword">extends</span> <span class="hljs-title">VersionedProtocol</span>
{</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">long</span> VERSION=<span class="hljs-number">12345</span>L;

    String hello(String name);
}</code></pre>

<p>4.1 服务端提供的对象必须是一个接口，接口extends VersioinedProtocal <br>
4.2 客户端能够的对象中的方法必须位于对象的接口中。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>