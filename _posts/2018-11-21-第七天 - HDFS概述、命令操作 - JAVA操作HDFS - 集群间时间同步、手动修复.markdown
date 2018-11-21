---
layout:     post
title:      第七天 - HDFS概述、命令操作 - JAVA操作HDFS - 集群间时间同步、手动修复
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="第七天-hdfs概述命令操作-java操作hdfs-集群间时间同步手动修复">第七天 - HDFS概述、命令操作 - JAVA操作HDFS - 集群间时间同步、手动修复</h1>

<p></p><div class="toc">
<ul>
<li><a href="#%E7%AC%AC%E4%B8%83%E5%A4%A9-hdfs%E6%A6%82%E8%BF%B0%E5%91%BD%E4%BB%A4%E6%93%8D%E4%BD%9C-java%E6%93%8D%E4%BD%9Chdfs-%E9%9B%86%E7%BE%A4%E9%97%B4%E6%97%B6%E9%97%B4%E5%90%8C%E6%AD%A5%E6%89%8B%E5%8A%A8%E4%BF%AE%E5%A4%8D" rel="nofollow">第七天 - HDFS概述、命令操作 - JAVA操作HDFS - 集群间时间同步、手动修复</a><ul>
<li><ul>
<li><a href="#%E4%B8%80hdfs%E6%A6%82%E8%BF%B0" rel="nofollow">一、HDFS概述</a><ul>
<li><ul>
<li><a href="#hdfs%E6%A6%82%E8%BF%B0" rel="nofollow">HDFS概述</a></li>
<li><a href="#hdfs%E4%BC%98%E7%BC%BA%E7%82%B9" rel="nofollow">HDFS优缺点</a></li>
<li><a href="#hdfs%E6%A0%B8%E5%BF%83%E6%80%9D%E6%83%B3%E5%8F%8A%E4%BD%9C%E7%94%A8" rel="nofollow">HDFS核心思想及作用</a></li>
<li><a href="#%E9%87%8D%E8%A6%81%E7%89%B9%E6%80%A7" rel="nofollow">重要特性</a></li>
</ul>
</li>
</ul>
</li>
<li><a href="#%E4%BA%8Chdfs%E5%B7%A5%E4%BD%9C%E5%8E%9F%E7%90%86" rel="nofollow">二、HDFS工作原理</a><ul>
<li><ul>
<li><a href="#%E6%A6%82%E8%BF%B0" rel="nofollow">概述</a></li>
<li><a href="#%E6%95%B0%E6%8D%AE%E8%AF%BB%E5%8F%96%E8%BF%87%E7%A8%8B" rel="nofollow">数据读取过程</a></li>
<li><a href="#%E6%95%B0%E6%8D%AE%E5%86%99%E5%85%A5%E8%BF%87%E7%A8%8B" rel="nofollow">数据写入过程</a></li>
<li><a href="#hadoop%E5%A2%9E%E5%88%A0%E8%8A%82%E7%82%B9" rel="nofollow">Hadoop增删节点</a></li>
<li><a href="#namenode%E8%AF%A6%E8%A7%A3" rel="nofollow">NameNode详解</a></li>
</ul>
</li>
</ul>
</li>
<li><a href="#%E4%B8%89hdfs%E5%91%BD%E4%BB%A4%E6%93%8D%E4%BD%9C" rel="nofollow">三、HDFS命令操作</a><ul>
<li><ul>
<li><a href="#web%E7%9B%91%E6%8E%A7" rel="nofollow">Web监控</a></li>
<li><a href="#%E4%BD%BF%E7%94%A8%E5%91%BD%E4%BB%A4%E6%93%8D%E4%BD%9Chdfs" rel="nofollow">使用命令操作HDFS</a></li>
</ul>
</li>
</ul>
</li>
<li><a href="#%E5%9B%9Bjava%E6%93%8D%E4%BD%9Chdfs" rel="nofollow">四、JAVA操作HDFS</a><ul>
<li><ul>
<li><a href="#junit%E5%8D%95%E5%85%83%E6%B5%8B%E8%AF%95%E5%B7%A5%E5%85%B7" rel="nofollow">JUnit单元测试工具</a></li>
<li><a href="#%E5%87%86%E5%A4%87" rel="nofollow">准备</a></li>
<li><a href="#eclipse%E7%BC%96%E5%86%99%E4%BB%A3%E7%A0%81" rel="nofollow">eclipse编写代码</a><ul>
<li><a href="#%E8%A7%A3%E5%86%B3%E7%8E%AF%E5%A2%83%E9%97%AE%E9%A2%98" rel="nofollow">解决环境问题</a></li>
<li><a href="#%E7%BC%96%E5%86%99%E4%BB%A3%E7%A0%81%E5%89%8D%E7%9A%84%E5%87%86%E5%A4%87" rel="nofollow">编写代码前的准备</a></li>
<li><a href="#%E7%BC%96%E5%86%99%E7%AC%AC%E4%B8%80%E4%B8%AA%E6%B5%8B%E8%AF%95%E4%BB%A3%E7%A0%81%E6%B5%8B%E8%AF%95%E8%BF%9E%E6%8E%A5%E6%98%AF%E5%90%A6%E6%88%90%E5%8A%9F" rel="nofollow">编写第一个测试代码，测试连接是否成功</a></li>
<li><a href="#%E5%88%9B%E5%BB%BA%E5%A4%9A%E7%BA%A7%E7%9B%AE%E5%BD%95" rel="nofollow">创建多级目录</a></li>
<li><a href="#%E4%B8%8A%E4%BC%A0%E6%9C%AC%E5%9C%B0%E6%96%87%E4%BB%B6%E8%87%B3hdfs" rel="nofollow">上传本地文件至hdfs</a></li>
<li><a href="#%E4%BB%8Ehdfs%E4%B8%8B%E8%BD%BD%E6%96%87%E4%BB%B6%E8%87%B3%E6%9C%AC%E5%9C%B0" rel="nofollow">从hdfs下载文件至本地</a></li>
<li><a href="#hdfsutil%E5%B7%A5%E5%85%B7%E7%B1%BB%E7%9A%84%E5%B0%81%E8%A3%85" rel="nofollow">HDFSUtil工具类的封装</a></li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
<li><a href="#%E4%BA%94%E8%BF%98%E5%8E%9F%E5%BF%AB%E7%85%A7%E5%90%8E%E7%9A%84%E5%A4%84%E7%90%86" rel="nofollow">五、还原快照后的处理</a></li>
</ul>
</li>
</ul>
</li>
</ul>
</div>




<h3 id="一hdfs概述">一、HDFS概述</h3>



<h5 id="hdfs概述">HDFS概述</h5>

<p>hdfs源于Google的GFS论文，是GFS的克隆版译为易于扩展的分布式文件系统，可以运行在大量的普通廉价机器上。hadoop，离线计算框架，数据很少会发生变化。当数据发生变化时，通过操作hdfs的方式进行数据同步，在hadoop中提供数据存储服务，具备数据管理功能/数据不丢失(冗余存储)。</p>

<p>hadoop进行数据运算时是以分布式的方式进行：任务分发 -&gt; 资源调配 -&gt; 结果同步，所以在对小数据量的处理时，从主观来看会感觉使用分布式计算反而比单机计算更慢，这是因为时间花费在了任务分发、结果同步上，；然而，当数据量足够大时，就能体现出分布式计算的优势了。</p>

<p>hdfs可以使得在数据计算时，数据的读取变得高效，提高计算的效率，同时可以作为多个数据计算框架数据存储支持，例如hive、hbase等。</p>



<h5 id="hdfs优缺点">HDFS优缺点</h5>

<ul>
<li>优点 <br>
<ul><li>高可靠性:存储和处理数据的能力 </li>
<li>高扩展性:可以在可用的计算机集群间分配数据来完成计算任务，并可以扩展到数以千计的节点中 </li>
<li>高效性:能够在各个节点之间动态移动数据，保证各个节点的动态平衡以提升速度 </li>
<li>高容错性:能够自动保存数据的多个副本，并能够将失败的任务重新分配</li></ul></li>
<li>缺点 <br>
<ul><li>不适合低延迟的数据访问</li>
<li>无法高效存储大量的小文件</li>
<li>不支持多用户写入及修改文件</li></ul></li>
</ul>



<h5 id="hdfs核心思想及作用">HDFS核心思想及作用</h5>

<ul>
<li>分而治之:将大文件、大批量文件，分布式存放在大量服务器上，可以快速高效的对海量数据进行运算分析</li>
<li>可以为各类的分布式运算框架(MapReduce，Spark等)提供数据存储服务</li>
<li>本质上是一个分布式文件系统，用于存储和管理文件，由多台服务器联合起来实现分布式的功能，在每个服务器上可以负责不同的工作内容</li>
</ul>

<p>如果一个集群中有100台节点：40台做数据存储，60台做数据运算</p>

<p>40台用于数据存储提升数据稳定性，例如使用商用固态硬盘/弹性块存储等高性能存储设备</p>

<p>60台应用于数据运算，需要提升提升CPU、内存性能</p>

<p>这100台节点都需要保证足够的带宽，否则带宽将会是这个集群分布式计算的瓶颈</p>



<h5 id="重要特性">重要特性</h5>

<ul>
<li>HDFS在物理上是分块存储，块的大小可以通过配置文件来指定，在2.x版本中是128M</li>
<li>HDFS文件系统会给客户端提供一个统一的抽象目录树，通过hdfs协议的路径来访问文件或文件夹</li>
<li>由namenode节点来管理目录结构及文件分块信息(元数据)，也是集群的主节点，负责维护整个文件系统的目录树以及每一个路径所对应的block信息</li>
<li>由datanode节点来管理文件的block存储，也是集群的从(子)节点，每一个block都可以在多个datanode上存储多个副本</li>
</ul>



<h3 id="二hdfs工作原理">二、HDFS工作原理</h3>



<h5 id="概述">概述</h5>

<p><img src="https://day07-1253629415.cos.ap-guangzhou.myqcloud.com/1536043721824.png" alt="1536043721824" title=""></p>

<ul>
<li>NameNode负责管理整个文件系统的元数据</li>
<li>DataNode负责管理用户存储文件的数据块</li>
<li>文件会按照预先指定的块大小被切成若干块后分布式存储在若干台DataNode上</li>
<li>每一个文件快可以有多个副本，并存放在不同的DataNode上</li>
<li>DataNode会定期向NameNode汇报自身保存的文件block信息，NameNode负责保持文件的副本数量</li>
<li>在用户使用时不需要关心HDFS的内部工作步骤，直接通过一个地址进行操作</li>
</ul>

<p>使用流修改文件内容时，首先打开一个流，不断的读取文件信息，定位修改，通常不会直接修改</p>

<p>通常是以文件导入的方式将数据导入HDFS，将计算得到的数据(小数据量)进行读取，结果的展示</p>



<h5 id="数据读取过程">数据读取过程</h5>

<p><img src="https://day07-1253629415.cos.ap-guangzhou.myqcloud.com/1536043936017.png" alt="1536043936017" title=""></p>

<ol>
<li>客户端通过命令或调用FileSystem对象的open方法打开需要读取的文件</li>
<li>NameNode接收指令后，确定文件的block的基本信息</li>
<li>DataNode进行排序，如果客户端就是在一台DataNode上执行则直接从本地读取数据。</li>
<li>确定数据节点后会返回一个FSDataInputStream对象，从中读取数据</li>
<li>使用read方法以流的方式从文件中读取数据</li>
<li>当达到block文件末尾时，FSDataInputStream会关闭当前连接，继续读取下一个block文件</li>
<li>循环读取过程，直至读取整个文件，关闭FSDataInputStream</li>
</ol>



<h5 id="数据写入过程">数据写入过程</h5>

<p><img src="https://day07-1253629415.cos.ap-guangzhou.myqcloud.com/1536044027682.png" alt="1536044027682" title=""></p>

<ol>
<li>客户端通过调用create()方法来请求创建文件</li>
<li>NameNode会对该操作进行校验，包括文件系统中是否已经存在该文件，以及是否有相应的权限进行创建</li>
<li>如果校验通过，NameNode会记录该文件信息，返回一个FSDataOutputStream对象，用于数据写入</li>
<li>FSDataOutputStream会把要写入的数据分成包的形式，写入到中间队列</li>
<li>DataStreamer用于将数据包中的数据分别写入到各个DataNode中</li>
<li>在FSDataOutputStream中维护了一个packets队列，其中存放了等待被每个DataNode确认的packets信息</li>
<li>一个packets信息被移出本队列当且仅当所有的DataNode都确认无误</li>
<li>当数据完成后会调用close方法，会flush残留的packets，通知NameNode等待确认信息</li>
</ol>



<h5 id="hadoop增删节点">Hadoop增删节点</h5>

<ul>
<li><p>静态添加</p>

<blockquote>
  <p>停止集群，修改节点配置文件(slaves)，启动集群</p>
</blockquote>

<ul><li>优点：操作简单，改动较少</li>
<li>缺点：去要重启集群</li>
<li>应用场景：每天有固定的重启时间</li></ul></li>
<li><p>动态添加</p>

<blockquote>
  <p>在集群运行过程中进行变更，在新机器上配置好环境，同步各集群的hosts文件和slaves文件</p>
</blockquote>

<p>在新节点中使用如下命令进行启动</p>

<blockquote>
  <p>sbin/hadoop-daemon.sh start datanode</p>
  
  <p>sbin/yarn-daemon.sh start nodemanager</p>
</blockquote>

<ul><li>优点：不需要重启集群</li>
<li>缺点：改动量较大时比较繁琐</li>
<li>应用场景：需要持续不断的提供服务</li></ul></li>
</ul>



<h5 id="namenode详解">NameNode详解</h5>

<blockquote>
  <p>负责处理客户端的请求，元数据的查询和修改</p>
</blockquote>

<ul>
<li><p>启动过程</p>

<ul><li>首先将fsimage(镜像载入内存)，并执行和editlog相关的各项操作</li>
<li>在内存中建立元数据映射，则创建一个新的fsimage文件和一个空的editlog</li>
<li>在安全模式下，DataNode会向NameNode发送块列表的最新情况</li>
<li>在安全模式中，文件系统对于客户端是只读的</li>
<li>完成以上操作后开启监听RPC和HTTP请求，并退出安全模式</li></ul>

<p><img src="https://day07-1253629415.cos.ap-guangzhou.myqcloud.com/1536045831654.png" alt="1536045831654" title=""></p></li>
<li><p>元数据管理</p>

<ul><li>NameNode的两个重要文件:fsimage-元数据镜像文件，edits-元数据操作日志</li>
<li>元数据镜像:内存中为最新的=fsimage+edits</li>
<li>定期合并fsimage和edits:由SecondaryNameNode，保证稳定运行以及NameNode重启速度</li></ul>

<p><img src="https://day07-1253629415.cos.ap-guangzhou.myqcloud.com/1536045885009.png" alt="1536045885009" title=""></p></li>
<li><p>安全模式</p>

<blockquote>
  <p>一个新创建的集群不会进入安全模式，重新启动一个已有的集群，通常会有短暂的安全模式时间，用于完成合并和初始化的操作</p>
</blockquote>

<ul><li>查询当前是否处于安全模式</li></ul>

<blockquote>
  <p>hadoop dfsadmin -safemode get</p>
</blockquote>

<ul><li>等待安全模式关闭</li></ul>

<blockquote>
  <p>hadoop dfsadmin -safemode wait</p>
</blockquote>

<ul><li>退出安全模式</li></ul>

<blockquote>
  <p>hadoop dfsadmin -safemode leave</p>
</blockquote>

<ul><li>启用安全模式</li></ul>

<blockquote>
  <p>hadoop dfsadmin -safemode enter</p>
</blockquote></li>
</ul>



<h3 id="三hdfs命令操作">三、HDFS命令操作</h3>



<h5 id="web监控">Web监控</h5>

<p>浏览器中输入地址SZ01:50070</p>

<p><img src="https://day07-1253629415.cos.ap-guangzhou.myqcloud.com/1536046367579.png" alt="1536046367579" title=""></p>

<p>可以在这个网页中查看集群信息等，也可以在上图的位置找到查看hdfs文件系统的页面，可以将hdfs中的文件下载至Windows本地；</p>

<p>在浏览器中直接下载文件时，传输数据的datanode是随机的，通常需要对每一台每一台机器配置hosts映射(Windows下)，当机器数量较多时，可以手动修改主机名为IP，再重新发送请求。</p>

<p><img src="https://day07-1253629415.cos.ap-guangzhou.myqcloud.com/1536031988636.png" alt="1536031988636" title=""></p>



<h5 id="使用命令操作hdfs">使用命令操作HDFS</h5>

<p>两种命令格式：</p>

<p>hadoop fs -command path</p>

<p>hdfs dfs -command path</p>

<blockquote>
  <p>command为操作命令，上传、下载、删除、查看、移动、复制文件等，命令与Linux下的命令几乎相同</p>
  
  <p>path为操作路径，hdfs根路径为hdfs://host:8020/，使用时可直接简写为/</p>
  
  <p>hdfs也有绝对路径和相对路径，但一般使用时使用绝对路径，即从/开始</p>
</blockquote>

<ul>
<li><p>查看hdfs系统中存在的文件的命令：ls</p>

<p>hdfs dfs -ls path：查看path路径下的文件</p>

<p>hdfs dfs -lsr path = hdfs dfs -ls -R path：递归查看path路径下的文件</p></li>
<li><p>上传文件至hdfs命令：put(copyFromLocal)</p>

<p>hdfs dfs -put src(Linux文件系统) dest(HDFS文件系统)</p>

<blockquote>
  <p>hdfs dfs -put .bash_profile /</p>
</blockquote>

<p><img src="https://day07-1253629415.cos.ap-guangzhou.myqcloud.com/1536031895401.png" alt="1536031895401" title=""></p>

<p><img src="https://day07-1253629415.cos.ap-guangzhou.myqcloud.com/1536031906227.png" alt="1536031906227" title=""></p></li>
<li><p>查看hdfs中文件内容：cat</p>

<p>hdfs dfs -cat file</p>

<blockquote>
  <p>hdfs dfs -cat /.bash_profile</p>
</blockquote>

<p><img src="https://day07-1253629415.cos.ap-guangzhou.myqcloud.com/1536032184637.png" alt="1536032184637" title=""></p></li>
<li><p>从hdfs下载文件命令：get(copyToLocal)</p>

<p>hdfs dfs -get src(HDFS文件系统) dest(Linux文件系统)</p>

<blockquote>
  <p>hdfs dfs -get /.bash_profile /tmp</p>
</blockquote>

<p><img src="https://day07-1253629415.cos.ap-guangzhou.myqcloud.com/1536032724810.png" alt="1536032724810" title=""></p>

<p><img src="https://day07-1253629415.cos.ap-guangzhou.myqcloud.com/1536032740739.png" alt="1536032740739" title=""></p></li>
<li><p>删除hdfs中文件、文件夹命令：rm  rmdir</p>

<p>hdfs dfs -rm file：删除文件</p>

<p>hdfs dfs -rmdir emptyDir：删除空文件夹</p>

<p>hdfs dfs -rm -r notEmptyDir：删除非空文件夹</p>

<p>在hdfs-site.xml中配置了删除文件时并不是立即删除，而是现将文件移至某一文件夹，60分钟后彻底删除</p>

<blockquote>
  <p>hdfs dfs -rm /.bash_profile</p>
</blockquote>

<p><img src="https://day07-1253629415.cos.ap-guangzhou.myqcloud.com/1536041268231.png" alt="1536041268231" title=""></p>

<p><img src="https://day07-1253629415.cos.ap-guangzhou.myqcloud.com/1536041341464.png" alt="1536041341464" title=""></p></li>
<li><p>创建文件夹：mkdir</p>

<p>hdfs dfs -mkdir dirPathName：指定路径和名字创建文件夹</p>

<blockquote>
  <p>hdfs dfs -mkdir /input</p>
  
  <p>hdfs dfs -mkdir /output</p>
  
  <p>hdfs dfs -mkdir test</p>
</blockquote>

<p><img src="https://day07-1253629415.cos.ap-guangzhou.myqcloud.com/1536041440710.png" alt="1536041440710" title=""></p>

<p><img src="https://day07-1253629415.cos.ap-guangzhou.myqcloud.com/1536041471272.png" alt="1536041471272" title=""></p></li>
<li><p>移动文件(夹)：mv</p>

<p>hdfs dfs -mv src(源) dest(目标)</p>

<blockquote>
  <p>hdfs dfs -mv /test.txt /s1/</p>
</blockquote>

<p><img src="https://day07-1253629415.cos.ap-guangzhou.myqcloud.com/1536053365475.png" alt="1536053365475" title=""></p></li>
<li><p>复制文件(夹)：cp</p>

<p>hdfs dfs -cp src(源) dest(目标)</p>

<blockquote>
  <p>hdfs dfs -cp /s1/test.txt /s1/s2/</p>
</blockquote>

<p><img src="https://day07-1253629415.cos.ap-guangzhou.myqcloud.com/1536053502927.png" alt="1536053502927" title=""></p></li>
<li><p>更改文件(夹)权限：chmod</p>

<p>hdfs dfs -chmod [-R] xxx file(dir)</p>

<blockquote>
  <p>hdfs dfs -chmod 777 /s1/test.txt</p>
</blockquote>

<p><img src="https://day07-1253629415.cos.ap-guangzhou.myqcloud.com/1536053621122.png" alt="1536053621122" title=""></p></li>
</ul>



<h3 id="四java操作hdfs">四、JAVA操作HDFS</h3>

<p>在Windows下通过java操作服务器中的hdfs时，需要进行安装hadoop，配置环境变量，在项目中引入所需jar包，解决环境问题等操作</p>



<h5 id="junit单元测试工具">JUnit单元测试工具</h5>

<p>此工具可以直接执行某一个方法：不能有参数，不能有返回值</p>

<p>原理：调用无参的构造方法，再去调用声明测试的方法</p>

<p>使用方法：添加库，在要测试的方法前引入@Test注释，右键运行时选择JUnit Test</p>

<p>通常测试时只会指定一个方法进行测试</p>

<p>使用步骤：右键项目，build Path，configure Build Path，切换至Library，add Library，Junit</p>

<p><img src="https://day07-1253629415.cos.ap-guangzhou.myqcloud.com/1536047495447.png" alt="1536047495447" title=""></p>

<ul>
<li>检验JUnit原理测试代码：</li>
</ul>

<p><img src="https://day07-1253629415.cos.ap-guangzhou.myqcloud.com/1536047648327.png" alt="1536047648327" title=""></p>



<pre class="prettyprint"><code class=" hljs cs">@Test
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">getHomeDir</span>() {
    <span class="hljs-comment">//输出当前家目录</span>
        System.<span class="hljs-keyword">out</span>.println(fs.getHomeDirectory());
    }</code></pre>

<p><img src="https://day07-1253629415.cos.ap-guangzhou.myqcloud.com/1536047666276.png" alt="1536047666276" title=""></p>



<h5 id="准备">准备</h5>

<ul>
<li><p>Windows下配置hadoop</p>

<ol><li><p>将hadoop-2.7.2.tar.gz通过解压缩软件解压至目录(一般解压至软件安装目录)</p></li>
<li><p>右键此电脑 -&gt; 高级系统设置 -&gt; 环境变量，在系统变量一栏选择新建</p>

<p><img src="https://day07-1253629415.cos.ap-guangzhou.myqcloud.com/1536054044335.png" alt="1536054044335" title=""></p></li>
<li><p>变量名为HADOOP_HOME，变量值为第1步中解压的路径</p>

<p><img src="https://day07-1253629415.cos.ap-guangzhou.myqcloud.com/1536054068038.png" alt="1536054068038" title=""></p></li></ol></li>
<li><p>解决运行MapReduce时因环境而报错(为之后编写MapReduce代码做准备)</p>

<ol><li><p>新建包org.apache.hadoop.io.nativeio</p></li>
<li><p>将修改后的NativeIO.java粘贴到包内</p>

<p><img src="https://day07-1253629415.cos.ap-guangzhou.myqcloud.com/1536058841992.png" alt="1536058841992" title=""></p></li>
<li><p>此时import有报错，原因是eclipse默认设置将禁止的引用进行报错，需要进行处理</p>

<p><img src="https://day07-1253629415.cos.ap-guangzhou.myqcloud.com/1536050096457.png" alt="1536050096457" title=""></p></li>
<li><p>将禁止的引用改成警告，如下图进行设置：</p>

<p><img src="https://day07-1253629415.cos.ap-guangzhou.myqcloud.com/1536050197655.png" alt="1536050149046" title=""></p></li></ol></li>
</ul>



<h5 id="eclipse编写代码">eclipse编写代码</h5>



<h6 id="解决环境问题">解决环境问题</h6>

<p>编写代码前，先将winutil.exe和hadoop.dll等文件拷贝至$HADOOP_HOME/bin目录下，并且将hadoop.dll拷贝至System32及SysWOW64目录下。双击运行winutil.exe，如果没报错即成功，如果报错，则需使用类库修复工具修复(具体可百度方法)，完成后重启电脑</p>

<p>压缩包可从<a href="https://download.csdn.net/download/osaymissyou0/9583264" rel="nofollow">点击此处</a>下载</p>

<p><img src="https://day07-1253629415.cos.ap-guangzhou.myqcloud.com/1536044658231.png" alt="1536044658231" title=""></p>



<h6 id="编写代码前的准备">编写代码前的准备</h6>

<ol>
<li><p>新建java项目，在项目下新建文件夹lib</p></li>
<li><p>在hadoop解压目录下拷贝以下jar包粘贴至项目的lib文件夹中</p>

<blockquote>
  <p><span>$</span>HADOOP_HOME/share/hadoop/common/hadoop-common-2.7.2.jar和hadoop-nfs-2.7.2.jar<img src="https://day07-1253629415.cos.ap-guangzhou.myqcloud.com/1536042465447.png" alt="1536042465447" title=""></p>
  
  <p><span>$</span>HADOOP_HOME/share/hadoop/common/lib中全部jar包<span>$</span>HADOOP_HOME/share/hadoop/hdfs/hadoop-hdfs-2.7.2.jar和hadoop-hdfs-nfs-2.7.2.jar<img src="https://day07-1253629415.cos.ap-guangzhou.myqcloud.com/1536042488968.png" alt="1536042488968" title="">$HADOOP_HOME/share/hadoop/hdfs/lib中全部jar包</p>
</blockquote></li>
<li><p>在eclipse中全选lib目录下的jar包，右键选择Build Path -&gt; Add to Build Path(添加至构建路径)</p></li>
<li><p>将$HADOOP_HOME/etc/hadoop/log4j.properties拷贝至src根目录下</p>

<p>将第18行中hadoop.root.logger=INFO改成DEBUG，这样在运行项目后输出的信息会以debug信息输出，否则有些报错信息未能输出，不利于调试</p>

<p><img src="https://day07-1253629415.cos.ap-guangzhou.myqcloud.com/1536045303877.png" alt="1536045303877" title=""></p></li>
</ol>



<h6 id="编写第一个测试代码测试连接是否成功">编写第一个测试代码，测试连接是否成功</h6>



<pre class="prettyprint"><code class=" hljs cs">import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.FileSystem;

<span class="hljs-keyword">public</span> <span class="hljs-keyword">class</span> Test01 {
    <span class="hljs-comment">// 初始化配置对象</span>
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> Configuration conf = <span class="hljs-keyword">new</span> Configuration();
    <span class="hljs-comment">// 声明需要访问的集群地址</span>
    <span class="hljs-keyword">private</span> final <span class="hljs-keyword">static</span> String URI = <span class="hljs-string">"hdfs://SZ01:8020/"</span>;
    <span class="hljs-comment">// 声明操作文件系统的类</span>
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> FileSystem fs;

    <span class="hljs-keyword">static</span> {
        <span class="hljs-keyword">try</span> {
            <span class="hljs-comment">// 从制定集群中读取配置</span>
            FileSystem.setDefaultUri(conf, URI);
            <span class="hljs-comment">// 使用读取到的配置实例化fs</span>
            fs = FileSystem.<span class="hljs-keyword">get</span>(conf);
        } <span class="hljs-keyword">catch</span> (Exception e) {
            e.printStackTrace();
        }
    }

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) {
        <span class="hljs-comment">// TODO 使用Java操作HDFS</span>
        <span class="hljs-comment">// 输出默认的block块大小</span>
        System.<span class="hljs-keyword">out</span>.println(fs.getDefaultBlockSize());
    }
}</code></pre>

<p>运行成功后输出结果为：</p>

<p><img src="https://day07-1253629415.cos.ap-guangzhou.myqcloud.com/1536043286129.png" alt="1536043286129" title=""></p>

<ul>
<li>下图中的设置为查看源码时使用反编译工具</li>
</ul>

<p><img src="https://day07-1253629415.cos.ap-guangzhou.myqcloud.com/1536047051652.png" alt="1536047051652" title=""></p>



<h6 id="创建多级目录">创建多级目录</h6>



<pre class="prettyprint"><code class=" hljs cs">    <span class="hljs-comment">// 同时创建多级目录</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">mkdirs</span>() {
        <span class="hljs-keyword">try</span> {
            fs.mkdirs(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"t1/t2"</span>));
            fs.mkdirs(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/s1/s2"</span>));
        } <span class="hljs-keyword">catch</span> (IllegalArgumentException e) {
            <span class="hljs-comment">// TODO 自动生成的 catch 块</span>
            e.printStackTrace();
        } <span class="hljs-keyword">catch</span> (IOException e) {
            <span class="hljs-comment">// TODO 自动生成的 catch 块</span>
            e.printStackTrace();
        }
    }</code></pre>

<p><img src="https://day07-1253629415.cos.ap-guangzhou.myqcloud.com/1536048052209.png" alt="1536048052209" title=""></p>



<h6 id="上传本地文件至hdfs">上传本地文件至hdfs</h6>

<blockquote>
  <p>上传文件通常是客户端通过web应用进行上传操作，将文件上传到linux本地磁盘，再上传至hdfs(指定删除源文件，指定不覆盖目标文件)</p>
</blockquote>



<pre class="prettyprint"><code class=" hljs cs"><span class="hljs-comment">// 上传本地文件至hdfs</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">upLoad</span>() {
        <span class="hljs-keyword">try</span> {
            <span class="hljs-comment">// 两个参数：本地文件路径和hdfs目录</span>
            fs.copyFromLocalFile(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"G://test/test.txt"</span>), <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/"</span>));
        } <span class="hljs-keyword">catch</span> (IllegalArgumentException e) {
            <span class="hljs-comment">// TODO 自动生成的 catch 块</span>
            e.printStackTrace();
        } <span class="hljs-keyword">catch</span> (IOException e) {
            <span class="hljs-comment">// TODO 自动生成的 catch 块</span>
            e.printStackTrace();
        }
    }</code></pre>

<p><img src="https://day07-1253629415.cos.ap-guangzhou.myqcloud.com/1536048266786.png" alt="1536048266786" title=""></p>



<h6 id="从hdfs下载文件至本地">从hdfs下载文件至本地</h6>



<pre class="prettyprint"><code class=" hljs cs"><span class="hljs-comment">// 从hdfs下载文件至本地</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">downLoad</span>() {
        <span class="hljs-keyword">try</span> {

            fs.copyToLocalFile(<span class="hljs-keyword">false</span>, <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/text.txt"</span>), <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"E://"</span>), <span class="hljs-keyword">true</span>);
        } <span class="hljs-keyword">catch</span> (IllegalArgumentException e) {
            <span class="hljs-comment">// TODO 自动生成的 catch 块</span>
            e.printStackTrace();
        } <span class="hljs-keyword">catch</span> (IOException e) {
            <span class="hljs-comment">// TODO 自动生成的 catch 块</span>
            e.printStackTrace();
        }
    }</code></pre>

<p><img src="https://day07-1253629415.cos.ap-guangzhou.myqcloud.com/1536048839819.png" alt="1536048721556" title=""></p>



<h6 id="hdfsutil工具类的封装">HDFSUtil工具类的封装</h6>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">import</span> org.apache.hadoop.conf.Configuration;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.FileSystem;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.Path;

<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">HDFSUtil</span> {</span>

    <span class="hljs-javadoc">/**
     * 初始化工具类，指定需要操作的集群
     *<span class="hljs-javadoctag"> @param</span> hostName
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-title">HDFSUtil</span>(String hostName) {
        URI = <span class="hljs-string">"hdfs://"</span> + hostName + <span class="hljs-string">":8020"</span>;
        <span class="hljs-keyword">try</span> {
            <span class="hljs-comment">// 将URI信息记录到配置项中</span>
            FileSystem.setDefaultUri(conf, URI);
            <span class="hljs-comment">// 使用读取到的配置实例化fs</span>
            fs = FileSystem.get(conf);
        } <span class="hljs-keyword">catch</span> (Exception e) {
            e.printStackTrace();
        }
    }

    <span class="hljs-comment">// 初始化配置对象</span>
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> Configuration conf = <span class="hljs-keyword">new</span> Configuration();
    <span class="hljs-comment">// 声明需要访问的集群地址</span>
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> String URI = <span class="hljs-string">""</span>;
    <span class="hljs-comment">// 声明操作文件系统的类</span>
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> FileSystem fs;

    <span class="hljs-javadoc">/**
     * 返回当前用户的家目录
     *<span class="hljs-javadoctag"> @return</span>
     */</span>
    <span class="hljs-keyword">public</span> String <span class="hljs-title">getHomeDir</span>() {
        <span class="hljs-comment">// 当前用户的家目录</span>
        <span class="hljs-keyword">return</span> fs.getHomeDirectory().toString();
    }

    <span class="hljs-javadoc">/**
     * 创建文件夹
     *<span class="hljs-javadoctag"> @param</span> path 完整路径，不需要添加斜杠
     *<span class="hljs-javadoctag"> @param</span> useHomeDir 是否在用户家目录中创建
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">mkdirs</span>(String path, <span class="hljs-keyword">boolean</span> useHomeDir) {
        <span class="hljs-comment">// 同时创建多级目录</span>
        <span class="hljs-keyword">try</span> {
            <span class="hljs-keyword">if</span> (useHomeDir) {
                fs.mkdirs(<span class="hljs-keyword">new</span> Path(path));
            } <span class="hljs-keyword">else</span> {
                fs.mkdirs(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/"</span> + path));
            }
        } <span class="hljs-keyword">catch</span> (Exception e) {
            e.printStackTrace();
        }
    }

    <span class="hljs-javadoc">/**
     * 文件上传
     *<span class="hljs-javadoctag"> @param</span> delSrc 是否删除源文件
     *<span class="hljs-javadoctag"> @param</span> overwrite 是否覆盖目标文件
     *<span class="hljs-javadoctag"> @param</span> srcs 源文件路径，可以指定多个路径
     *<span class="hljs-javadoctag"> @param</span> dest 目标路径
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">upLoad</span>(<span class="hljs-keyword">boolean</span> delSrc, <span class="hljs-keyword">boolean</span> overwrite, String[] srcs, String dest) {
        <span class="hljs-keyword">try</span> {
            <span class="hljs-comment">// fs.copyFromLocalFile(false, src, dst);</span>
            <span class="hljs-comment">// 四个参数，是否删除源文件，是否覆盖目标文件，源路径，目标路径，声明不覆盖时抛出异常(不会发生覆盖)</span>
            <span class="hljs-comment">// fs.copyFromLocalFile(false, false, new Path("E://test.txt"), new Path("/"));</span>
            <span class="hljs-comment">// 上传单个文件时，直接上传</span>
            <span class="hljs-keyword">if</span> (srcs.length == <span class="hljs-number">1</span>) {
                fs.copyFromLocalFile(delSrc, overwrite, <span class="hljs-keyword">new</span> Path(srcs[<span class="hljs-number">0</span>]), <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/"</span>));
            } <span class="hljs-keyword">else</span> {
                <span class="hljs-comment">// 上传多个文件时，生成所需的Path数组</span>
                Path[] paths = <span class="hljs-keyword">new</span> Path[srcs.length];
                <span class="hljs-keyword">for</span> (<span class="hljs-keyword">int</span> i = <span class="hljs-number">0</span>; i &lt; srcs.length; i++) {
                    paths[i] = <span class="hljs-keyword">new</span> Path(srcs[i]);
                }
                fs.copyFromLocalFile(delSrc, overwrite, paths, <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/"</span>));
            }

        } <span class="hljs-keyword">catch</span> (Exception e) {
            e.printStackTrace();
        }
    }

    <span class="hljs-javadoc">/**
     * 文件下载
     *<span class="hljs-javadoctag"> @param</span> delSrc 是否删除源文件
     *<span class="hljs-javadoctag"> @param</span> src 源文件路径
     *<span class="hljs-javadoctag"> @param</span> dest 目标路径
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">downLoad</span>(<span class="hljs-keyword">boolean</span> delSrc,String src,String dest) {
        <span class="hljs-keyword">try</span> {
            <span class="hljs-comment">// 四个参数，是否删除源文件，源路径，目标路径，是否使用原生的系统文件系统</span>
            fs.copyToLocalFile(delSrc, <span class="hljs-keyword">new</span> Path(src), <span class="hljs-keyword">new</span> Path(dest), <span class="hljs-keyword">true</span>);
        } <span class="hljs-keyword">catch</span> (Exception e) {
            e.printStackTrace();
        }
    }
}
</code></pre>



<h3 id="五还原快照后的处理">五、还原快照后的处理</h3>

<p>在集群中单台机器出现了误操作后导致系统不能正常运行，此时需要还原快照，如果没有快照则需重装系统。</p>

<p>还原快照后需要对三台机器的时间进行同步，否则hadoop集群无法进行正常工作。</p>

<p>同步时间的步骤：</p>

<ol>
<li><p>所有机器切换至root用户</p>

<blockquote>
  <p>su - root</p>
</blockquote></li>
<li><p>打开Xshell的发送键输入到所有会话的功能，输入以下命令</p>

<blockquote>
  <p>date -s ‘201x-xx-xx xx:xx:xx’</p>
</blockquote>

<p>时间同步完成后，重启hadoop进程，即执行以下两步命令</p></li>
</ol>

<blockquote>
  <p>stop-all.sh</p>
  
  <p>start-all.sh</p>
</blockquote>

<p>重启hadoop进程成功后，hadoop会对恢复快照的机器尝试自动检测和修复。</p>

<p>如果hadoop自动修复失败，即恢复快照的机器访问hdfs文件系统时跟其他机器访问hdfs所得到的结果不同，则需进行手动修复。</p>

<p>手动修复步骤：</p>

<ol>
<li><p>损坏机器与正常机器都进入配置的datanode文件夹</p>

<blockquote>
  <p>cd hadoop/data/dfs/dn/current/BP-1387956734-192.168.128.121-1535966538843/current/finalized/subdir0/subdir0/</p>
</blockquote></li>
<li><p>对比损坏机器与正常机器该目录下的文件情况，将正常机器多出来的文件复制到损坏的机器上，复制方式可以通过scp发送，或者Xftp先下载至本地再上传</p></li>
</ol>

<p>手动修复后需要重启hadoop进程，此时修复完成。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>