---
layout:     post
title:      Mac上第一个eclipse编译的Hadoop程序
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：原创博客,微信:18518215883					https://blog.csdn.net/G290095142/article/details/78231337				</div>
								            <div id="content_views" class="markdown_views prism-tomorrow-night">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="参考博客"><strong>参考博客</strong></h2>

<p><a href="http://www.cnblogs.com/landed/p/6831758.html" rel="nofollow" target="_blank">手把手教你安装mac版hadoop2.7.3教程</a> <br>
<a href="http://www.cnblogs.com/micrari/p/5716851.html" rel="nofollow" target="_blank">Mac Hadoop的安装与配置</a> <br>
<a href="http://blog.csdn.net/keda8997110/article/details/8432161" rel="nofollow" target="_blank">Win下Eclipse提交hadoop程序出错：org.apache.hadoop.security.AccessControlException: Permission denied:</a> <br>
<a href="http://blog.csdn.net/shuaidan19920412/article/details/53000801" rel="nofollow" target="_blank">Mac上的第一个hadoop小demo</a> <br>
<a href="http://www.cnblogs.com/davidgu/p/6179191.html" rel="nofollow" target="_blank">Mac OSX下配置和启动hadoop以及常见错误解决</a> <br>
 <a href="http://blog.csdn.net/Ruidu_Doer/article/details/50781144" rel="nofollow" target="_blank">hadoop学习之二:mac下hadoop+eclipse环境搭建</a></p>



<h2 id="目录"><strong>目录</strong></h2>

<ul>
<li>Hadoop的下载安装</li>
<li>SSH免密登陆</li>
<li>eclipse下Hadoop插件的安装</li>
<li>编写第一个Hadoop程序</li>
</ul>

<p><strong><em>Hadoop的下载安装</em></strong> <br>
安装java环境，配置环境变量 <br>
使用brew下载安装</p>

<blockquote>
  <p>brew install hadoop</p>
</blockquote>

<p>默认安装目录 <br>
/usr/local/Cellar/hadoop <br>
安装完成后的目录 <br>
<img src="https://img-blog.csdn.net/20171013234731932?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRzI5MDA5NTE0Mg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
定位到Hadoop的安装目录下 <br>
修改/etc/hadoop/hadoop-env.sh <br>
export JAVA_HOME=jdk的安装目录 <br>
修改配置文件</p>

<ul>
<li>core-site.xml </li>
<li>hadfs-site.xml </li>
<li>mapred-site.xml </li>
<li>yarn-site.xml</li>
</ul>

<p>core-site.xml</p>



<pre class="prettyprint"><code class=" hljs xml"> <span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span> 
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span> 
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>fs.defaultFS<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span> 
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>hdfs://localhost:9000<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span> 
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span> 
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>

<p>hdfs-site.xml</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span> <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span> <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.replication<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span> <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>1<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span> <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span> <span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>

<p>mapred-site.xml <br>
如果不存在则创建一个 (可以复制一下mapred-site.xml.template文件再进行修改)</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span> <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span> <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>mapreduce.framework.name<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span> <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>yarn<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span> <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span> <span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>

<p>yarn-site.xml</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.nodemanager.aux-services<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>mapreduce_shuffle<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>

<p><strong><em>配置免密登陆</em></strong> <br>
定位到当前用户目录下，我的是koukouken <br>
<img src="https://img-blog.csdn.net/20171013235958308?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRzI5MDA5NTE0Mg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
用dsa密钥认证来生成一对公钥和私钥：</p>

<blockquote>
  <p>ssh-keygen -t dsa -P ” -f ~/.ssh/id_dsa</p>
</blockquote>

<p>将生成的公钥加入到用于认证的公钥文件中：</p>

<blockquote>
  <p>cat ~/.ssh/id_dsa.pub &gt;&gt; ~/.ssh/authorized_keys</p>
</blockquote>

<p>ssh localhost判读是否登陆成功 <br>
<img src="https://img-blog.csdn.net/20171014000522238?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRzI5MDA5NTE0Mg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>如果所示则登陆成功</p>

<p>Hadoop的启动</p>

<ul>
<li>进入Hadoop的目录，以mac系统为例目录为</li>
</ul>

<blockquote>
  <p>/usr/local/Cellar/hadoop/2.7.2/libexec <br>
  格式化文件系统 <br>
  hdfs namenode -format</p>
</blockquote>

<ul>
<li>启动NameNode和DataNode的守护进程。</li>
</ul>

<blockquote>
  <p>sbin/start-dfs.sh</p>
</blockquote>

<ul>
<li>启动ResourceManager和NodeManager的守护进程</li>
</ul>

<blockquote>
  <p>sbin/start-yarn.sh</p>
</blockquote>

<ul>
<li>访问localhost:50070和localhost:8088测试是否正常</li>
</ul>

<p><img src="https://img-blog.csdn.net/20171014001108505?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRzI5MDA5NTE0Mg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<ul>
<li>创建hdfs目录:</li>
</ul>

<blockquote>
  <p>hdfs dfs -mkdir -p /user/robin/input</p>
</blockquote>

<ul>
<li>拷贝一些文件到input目录:</li>
</ul>

<blockquote>
  <p>hdfs dfs -put etc/hadoop input</p>
</blockquote>

<ul>
<li>运行样例: （下面命令中的hadoop版本号是2.8.1，根据你自己的hadoop版本修改版本号）</li>
</ul>

<blockquote>
  <p>bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.8.1.jar grep input output ‘dfs[a-z.]+’</p>
</blockquote>

<ul>
<li>在localhost:50070中的Utilities标签下找到/user/robin目录，下载part-r-00000文件，可以看到其中内容如下所示：</li>
</ul>

<blockquote>
  <p>这个地方我什么都没有显示 <br>
  4   dfs.class <br>
  4   dfs.audit.logger <br>
  3   dfs.server.namenode. <br>
  2   dfs.period <br>
  2   dfs.audit.log.maxfilesize <br>
  2   dfs.audit.log.maxbackupindex <br>
  1   dfsmetrics.log <br>
  1   dfsadmin <br>
  1   dfs.servers <br>
  1   dfs.replication <br>
  1   dfs.file</p>
</blockquote>

<p><strong><em>eclipse下Hadoop插件的安装</em></strong> <br>
<a href="http://download.csdn.net/download/g290095142/10020803" rel="nofollow">插件下载地址</a> <br>
将jar 拷贝到eclipse的plugins目录下,重启eclipse,定位Hadoop目录下： <br>
<img src="https://img-blog.csdn.net/20171014003356652?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRzI5MDA5NTE0Mg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
修改中间的install目录为Hadoop安装的目录</p>

<p><strong><em>编写第一个Hadoop程序</em></strong> <br>
新建一个Hadoop项目 <br>
<img src="https://img-blog.csdn.net/20171014003608368?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRzI5MDA5NTE0Mg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
配置 Map/Reduce Locations  <br>
Windows—show view—Other—— Map… <br>
新建一个Hadoop local,我的端口为9000，点击确定 <br>
<img src="https://img-blog.csdn.net/20171014003910244?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRzI5MDA5NTE0Mg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
在右侧DFS就会出现你创建的Hadoop local,点击就能看到你创建的文件目录 <br>
<img src="https://img-blog.csdn.net/20171014004155478?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRzI5MDA5NTE0Mg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
新建HdfsClientDemo.java <br>
中间的koukouken是我的用户名，需要改成你对应的用户名</p>

<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">package</span> testHadoop;

<span class="hljs-keyword">import</span> java.net.URI;
<span class="hljs-keyword">import</span> java.util.Iterator;
<span class="hljs-keyword">import</span> java.util.Map.Entry;

<span class="hljs-keyword">import</span> org.apache.hadoop.conf.Configuration;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.FileStatus;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.FileSystem;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.LocatedFileStatus;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.Path;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.RemoteIterator;
<span class="hljs-keyword">import</span> org.junit.Before;
<span class="hljs-keyword">import</span> org.junit.Test;
<span class="hljs-javadoc">/**
 * 
 * 客户端去操作hdfs时，是有一个用户身份的
 * 默认情况下，hdfs客户端api会从jvm中获取一个参数来作为自己的用户身份：-DHADOOP_USER_NAME=koukouken
 * 
 * 也可以在构造客户端fs对象时，通过参数传递进去
 *<span class="hljs-javadoctag"> @author</span>
 *
 */</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">HdfsClientDemo</span> {</span>
    FileSystem fs = <span class="hljs-keyword">null</span>;
    Configuration conf = <span class="hljs-keyword">null</span>;
    <span class="hljs-annotation">@Before</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">init</span>() <span class="hljs-keyword">throws</span> Exception{

        conf = <span class="hljs-keyword">new</span> Configuration();
        conf.set(<span class="hljs-string">"fs.defaultFS"</span>, <span class="hljs-string">"hdfs://localhost:9000"</span>);

        <span class="hljs-comment">//拿到一个文件系统操作的客户端实例对象</span>
        <span class="hljs-comment">/*fs = FileSystem.get(conf);*/</span>
        <span class="hljs-comment">//可以直接传入 uri和用户身份</span>
        fs = FileSystem.get(<span class="hljs-keyword">new</span> URI(<span class="hljs-string">"hdfs://localhost:9000"</span>),conf,<span class="hljs-string">"koukouken"</span>); <span class="hljs-comment">//最后一个参数为用户名</span>
    }

    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">testUpload</span>() <span class="hljs-keyword">throws</span> Exception {

        Thread.sleep(<span class="hljs-number">2000</span>);
        fs.copyFromLocalFile(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/Users/koukouken/hadoop/access.log"</span>), <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/access.log.copy"</span>));
        fs.close();
    }


    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">testDownload</span>() <span class="hljs-keyword">throws</span> Exception {

        fs.copyToLocalFile(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/access.log.copy"</span>), <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/Users/koukouken/hadoop"</span>));
        fs.close();
    }

    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">testConf</span>(){
        Iterator&lt;Entry&lt;String, String&gt;&gt; iterator = conf.iterator();
        <span class="hljs-keyword">while</span> (iterator.hasNext()) {
            Entry&lt;String, String&gt; entry = iterator.next();
            System.out.println(entry.getValue() + <span class="hljs-string">"--"</span> + entry.getValue());<span class="hljs-comment">//conf加载的内容</span>
        }
    }

    <span class="hljs-javadoc">/**
     * 创建目录
     */</span>
    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">makdirTest</span>() <span class="hljs-keyword">throws</span> Exception {
        <span class="hljs-keyword">boolean</span> mkdirs = fs.mkdirs(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/user/aaa/bbb"</span>));
        System.out.println(mkdirs);
    }

    <span class="hljs-javadoc">/**
     * 删除
     */</span>
    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">deleteTest</span>() <span class="hljs-keyword">throws</span> Exception{
        <span class="hljs-keyword">boolean</span> delete = fs.delete(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/user/aaa"</span>), <span class="hljs-keyword">true</span>);<span class="hljs-comment">//true， 递归删除</span>
        System.out.println(delete);
    }

    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">listTest</span>() <span class="hljs-keyword">throws</span> Exception{

        FileStatus[] listStatus = fs.listStatus(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/"</span>));
        <span class="hljs-keyword">for</span> (FileStatus fileStatus : listStatus) {
            System.err.println(fileStatus.getPath()+<span class="hljs-string">"================="</span>+fileStatus.toString());
        }
        <span class="hljs-comment">//会递归找到所有的文件</span>
        RemoteIterator&lt;LocatedFileStatus&gt; listFiles = fs.listFiles(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/"</span>), <span class="hljs-keyword">true</span>);
        <span class="hljs-keyword">while</span>(listFiles.hasNext()){
            LocatedFileStatus next = listFiles.next();
            String name = next.getPath().getName();
            Path path = next.getPath();
            System.out.println(name + <span class="hljs-string">"---"</span> + path.toString());
        }
    }

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) <span class="hljs-keyword">throws</span> Exception {
        Configuration conf = <span class="hljs-keyword">new</span> Configuration();
        conf.set(<span class="hljs-string">"fs.defaultFS"</span>, <span class="hljs-string">"hdfs://localhost:9000"</span>);
        <span class="hljs-comment">//拿到一个文件系统操作的客户端实例对象</span>
        FileSystem fs = FileSystem.get(conf);

        fs.copyFromLocalFile(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/Users/koukouken/hadoop/access.log"</span>), <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/access.log.copy"</span>));
        fs.close();
    }


}
</code></pre>

<p>执行完中间的测试代码，点击如图所示Disconnect即可看到文件是否创建成功 <br>
<img src="https://img-blog.csdn.net/20171014004506687?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRzI5MDA5NTE0Mg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
可能出现一些权限的错误，Hadoop修改权限的方式</p>

<blockquote>
  <p>hadoop fs -chmod 777 /user/koukouken</p>
</blockquote>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>