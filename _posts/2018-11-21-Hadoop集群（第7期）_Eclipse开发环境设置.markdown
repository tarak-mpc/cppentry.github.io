---
layout:     post
title:      Hadoop集群（第7期）_Eclipse开发环境设置
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                原文地址为：<a href="http://www.itdaan.com/blog/2012/05/20/e568c6336628bea90fa1ab365a0ffcb2.html" rel="nofollow">Hadoop集群（第7期）_Eclipse开发环境设置</a><br><br><h2>1、Hadoop开发环境简介</h2>
<h3>1.1 Hadoop<a href="http://www.javaxxz.com/thread-343628-1-1.html" rel="nofollow">集群</a>简介</h3>
<p>　　Java版本：jdk-<span style="color:#3366ff;">6u31</span>-linux-i586.bin</p>
<p>　　Linux系统：CentOS6.0</p>
<p>　　Hadoop版本：<span style="color:#FF0000;">hadoop-1.0.0.tar.gz </span></p>
<h3>1.2 Windows开发简介</h3>
<p>　　Java版本：jdk-<span style="color:#3366ff;">6u31</span>-windows-i586.exe</p>
<p>　　Win系统：Windows 7 旗舰版</p>
<p>　　<a href="http://www.javaxxz.com/thread-348973-1-1.html" rel="nofollow">Eclipse</a>软件：eclipse-jee-<span style="color:#FF0000;"><strong>indigo</strong></span>-SR1-win32.zip | eclipse-jee-<span style="color:#0000FF;"><strong>helios</strong></span>-SR2-win32.zip</p>
<p>　　Hadoop软件：hadoop-1.0.0.tar.gz</p>
<p>　　Hadoop Eclipse 插件：hadoop-eclipse-plugin-1.0.0.jar</p>
<p>　　下载地址：<a href="http://download.csdn.net/detail/xia520pi/4113746" rel="nofollow">http://download.csdn.net/detail/xia520pi/4113746</a></p>
<p><span style="color:#FF0000;"><strong>　　备注：</strong></span>下面是网上收集的收集的"hadoop-eclipse-plugin-1.0.0.jar"，除"版本2.0"是根据"V1.0"按照"常见问题FAQ_<span style="color:#FF0000;"><strong>1</strong></span>"改的之外，剩余的"V3.0"、"V4.0"和"V5.0"和"V2.0"一样是别人已经弄好的，而且我已经都测试过，没有任何问题，可以放心使用。我们这里选择第"V5.0"使用。记得在使用时<span style="color:#FF0000;"><strong>重新命名</strong></span>为"<span style="color:#3366ff;"><strong>hadoop-eclipse-plugin-1.0.0.jar</strong></span>"。</p>
<p> </p>
<p style="text-align:center;"><img src="http://www.itdaan.com/imgs/8/2/7/0/35/a3187fb51aa0cfa6a2a17c43181a8a67.jpe" alt=""></p>
<h2>2、Hadoop Eclipse简介和使用</h2>
<h3>2.1 Eclipse插件介绍</h3>
<p>　　Hadoop是一个强大的并行框架，它允许任务在其分布式集群上并行处理。但是编写、调试Hadoop程序都有很大难度。正因为如此，Hadoop的开发者开发出了Hadoop Eclipse插件，它在Hadoop的开发环境中嵌入了Eclipse，从而实现了开发环境的图形化，降低了编程难度。在安装插件，配置Hadoop的相关信息之后，如果用户创建Hadoop程序，插件会自动导入Hadoop编程接口的JAR文件，这样用户就可以在Eclipse的图形化界面中编写、调试、运行Hadoop程序（包括单机程序和分布式程序），也可以在其中查看自己程序的实时状态、错误信息和运行结果，还可以查看、管理HDFS以及文件。总地来说，Hadoop Eclipse插件安装简单，使用方便，功能强大，尤其是在Hadoop编程方面，是Hadoop入门和Hadoop编程必不可少的工具。</p>
<h3>2.2 Hadoop工作目录简介</h3>
<p>　　为了以后方便开发，我们按照下面把开发中用到的软件安装在此目录中，JDK安装除外，我这里把JDK安装在C盘的默认安装路径下，下面是我的工作目录：</p>
<p> </p>
<blockquote>
 <p>    系统磁盘（E：）</p>
 <p>        |---HadoopWorkPlat</p>
 <p>            |--- eclipse</p>
 <p>            |--- hadoop-1.0.0</p>
 <p>            |--- workplace</p>
 <p>            |---<span style="color:#FF0000;"><strong>…… </strong></span></p>
</blockquote>
<p> </p>
<p>　　按照上面目录把Eclipse和Hadoop解压到"E:\HadoopWorkPlat"下面，并创建"workplace"作为Eclipse的工作空间。</p>
<p> </p>
<p style="text-align:center;"><img src="http://www.itdaan.com/imgs/1/1/1/6/5/3313bc6917e2d9a05661160a7b39bae9.jpe" alt=""></p>
<p> </p>
<p><span style="color:#FF0000;"><strong>　　备注：</strong></span>大家可以按照自己的情况，不一定按照我的结构来设计。</p>
<h3>2.3 修改系统管理员名字</h3>
<p>　　经过两天多次探索，为了使Eclipse能正常对Hadoop集群的HDFS上的文件能进行修改和删除，所以修改你工作时所用的Win7系统管理员名字，默认一般为"<span style="color:#FF0000;"><strong>Administrator</strong></span>"，把它修改为"<span style="color:#0000FF;"><strong>hadoop</strong></span>"，此用户名与Hadoop集群普通用户一致，大家应该记得我们Hadoop集群中所有的机器都有一个普通用户——hadoop，而且Hadoop运行也是用这个用户进行的。为了不至于为权限苦恼，我们可以修改Win7上系统管理员的姓名，这样就避免出现该用户在Hadoop集群上没有权限等都疼问题，会导致在Eclipse中对Hadoop集群的HDFS创建和删除文件受影响。</p>
<p>　　你可以做一下实验，查看Master.Hadoop机器上"/usr/hadoop/logs"下面的日志。发现权限不够，不能进行"Write"操作，网上有几种解决方案，但是对Hadoop1.0不起作用，详情见"常见问题FAQ_<span style="color:#FF0000;"><strong>2</strong></span>"。下面我们进行修改管理员名字。</p>
<p>　　首先"<span style="color:#FF0000;"><strong>右击</strong></span>"桌面上图标"<span style="color:#FF0000;"><strong>我的电脑</strong></span>"，选择"<span style="color:#FF0000;"><strong>管理</strong></span>"，弹出界面如下：</p>
<p> </p>
<p style="text-align:center;"><img src="http://www.itdaan.com/imgs/6/5/1/7/25/fe5c7a2de6935f31023d77560d373c53.jpe" alt=""></p>
<p style="text-align:center;"> </p>
<p>　　接着选择"<span style="color:#FF0000;"><strong>本地用户和组</strong></span>"，展开"<span style="color:#FF0000;"><strong>用户</strong></span>"，找到系统管理员"<span style="color:#FF0000;"><strong>Administrator</strong></span>"，修改其为"<span style="color:#0000FF;"><strong>hadoop</strong></span>"，操作结果如下图：</p>
<p> </p>
<p style="text-align:center;"><img src="http://www.itdaan.com/imgs/6/9/7/8/94/7cfab3637ad59d14984e791e6b7ca129.jpe" alt=""></p>
<p> </p>
<p>　　最后，把电脑进行"<span style="color:#FF0000;"><strong>注销</strong></span>"或者"<span style="color:#FF0000;"><strong>重启电脑</strong></span>"，这样才能使管理员<span style="color:#0000FF;"><strong>才能用</strong></span>这个名字。</p>
<h3>2.4 Eclipse插件开发配置</h3>
<p><span style="color:#0000FF;"><strong>　　第一步</strong></span>：把我们的"hadoop-eclipse-plugin-1.0.0.jar"放到Eclipse的目录的"<span style="color:#FF0000;"><strong>plugins</strong></span>"中，然后重新Eclipse即可生效。</p>
<p> </p>
<blockquote>
 <p>    系统磁盘（E：）</p>
 <p>        |---HadoopWorkPlat</p>
 <p>            |--- eclipse</p>
 <p>                |---<span style="color:#FF0000;"><strong> plugins </strong></span></p>
 <p>                    |--- <span style="color:#0000FF;"><strong>hadoop-eclipse-plugin-1.0.0.jar </strong></span></p>
</blockquote>
<p> </p>
<p>　　上面是我的"hadoop-eclipse-plugin"插件放置的地方。重启Eclipse如下图：</p>
<p> </p>
<p style="text-align:center;"><img src="http://www.itdaan.com/imgs/1/6/2/0/71/262c804116a97593926cf81b93c75b9c.jpe" alt=""></p>
<p> </p>
<p>　　细心的你从上图中左侧"Project Explorer"下面发现"DFS Locations"，说明Eclipse已经识别刚才放入的Hadoop Eclipse插件了。</p>
<p> </p>
<p><span style="color:#FF0000;"><strong>　　第二步</strong></span>：选择"<span style="color:#0000FF;"><strong>Window</strong></span>"菜单下的"<span style="color:#0000FF;"><strong>Preference</strong></span>"，然后弹出一个窗体，在窗体的左侧，有一列选项，里面会多出"<span style="color:#FF0000;"><strong>Hadoop Map/Reduce</strong></span>"选项，点击此选项，选择Hadoop的安装目录（如我的Hadoop目录：E:\HadoopWorkPlat\hadoop-1.0.0）。结果如下图：</p>
<p> </p>
<p style="text-align:center;"><img src="http://www.itdaan.com/imgs/5/9/6/3/52/fbd64e094eb89d19d74bc669d21bac76.jpe" alt=""></p>
<p> </p>
<p><span style="color:#FF0000;"><strong>　　第三步</strong></span>：切换"Map/Reduce"工作目录，有两种方法：</p>
<p>　　1）选择"Window"菜单下选择"Open Perspective"，弹出一个窗体，从中选择"Map/Reduce"选项即可进行切换。</p>
<p> </p>
<p style="text-align:center;"><img src="http://www.itdaan.com/imgs/4/5/9/5/67/c653f56f6322899943d7ad9af2b309cf.jpe" alt=""></p>
<p style="text-align:center;"> </p>
<p>　　2）在Eclipse软件的<span style="color:#FF0000;"><strong>右上角</strong></span>，点击图标"<img src="http://www.itdaan.com/imgs/0/5/8/8/32/739fdb2a0cea9a9baf732aa96ec9078e.jpe" alt="">"中的<span style="color:#FF0000;"><strong>"<img src="http://www.itdaan.com/imgs/3/6/1/0/80/788a93936df98b8a7a73dcd32d8355c9.jpe" alt="">"</strong></span>，点击"Other"选项，也可以弹出上图，从中选择"Map/Reduce"，然后点击"OK"即可确定。</p>
<p>　　切换到"Map/Reduce"工作目录下的界面如下图所示。</p>
<p> </p>
<p style="text-align:center;"><img src="http://www.itdaan.com/imgs/2/9/7/3/75/2741407b9a21378d80a24c2935635c61.jpe" alt=""></p>
<p> </p>
<p><span style="color:#FF0000;"><strong>　　第四步</strong></span>：建立与Hadoop集群的连接，在Eclipse软件下面的"<span style="color:#FF0000;"><strong>Map/Reduce Locations</strong></span>"进行<span style="color:#FF0000;"><strong>右击</strong></span>，弹出一个选项，选择"<span style="color:#FF0000;"><strong>New Hadoop Location</strong></span>"，然后弹出一个窗体。</p>
<p> </p>
<p style="text-align:center;"><img src="http://www.itdaan.com/imgs/4/3/6/7/98/02d5b3438ab38be9ae5ebfa7666d53e1.jpe" alt=""></p>
<p> </p>
<p style="text-align:center;"><img src="http://www.itdaan.com/imgs/1/4/0/6/33/c87a14a0fc1a122a44906b17d2e6285d.jpe" alt=""></p>
<p style="text-align:center;"> </p>
<p>　　注意上图中的红色标注的地方，是需要我们关注的地方。</p>
<ul style="margin-left:42pt;"><li>
  <div style="text-align:justify;">
   Location Name：可以任意其，标识一个"Map/Reduce Location"
  </div></li>
 <li>
  <div style="text-align:justify;">
   Map/Reduce Master
   <br>Host：192.168.1.2（
   <span style="color:#FF0000;"><strong>Master.Hadoop</strong></span>的IP地址）
   <br>Port：9001 
  </div></li>
</ul><ul style="margin-left:42pt;"><li>
  <div style="text-align:justify;">
   DFS Master 
   <br>Use M/R Master host：前面的
   <span style="color:#FF0000;"><strong>勾上</strong></span>。（因为我们的NameNode和JobTracker都在一个机器上。）
   <br>Port：9000 
  </div></li>
</ul><ul style="margin-left:42pt;"><li>
  <div style="text-align:justify;">
   User name：hadoop（默认为Win系统管理员名字，因为我们之前改了所以这里就变成了hadoop。） 
  </div></li>
</ul><p> </p>
<p style="text-align:center;"><img src="http://www.itdaan.com/imgs/2/9/7/3/53/61c5e6966eced4fa80bcc1efe9df01c6.jpe" alt=""></p>
<p> </p>
<p><span style="color:#FF0000;"><strong>　　备注：</strong></span>这里面的Host、Port分别为你在mapred-site.xml、core-site.xml中配置的地址及端口。不清楚的可以参考"<span style="color:#FF0000;"><strong>Hadoop集群_第5期_Hadoop安装配置_V1.0</strong></span>"进行查看。 </p>
<p> 　　接着点击"Advanced parameters"从中找见"hadoop.tmp.dir"，修改成为我们Hadoop集群中<a href="http://www.javaxxz.com/thread-348966-1-1.html" rel="nofollow">设置</a>的地址，我们的Hadoop集群是"<span style="color:#FF0000;"><strong>/usr/hadoop/tmp</strong></span>"，这个参数在"core-site.xml"进行了配置。 </p>
<p> </p>
<p style="text-align:center;"><img src="http://www.itdaan.com/imgs/3/2/3/6/63/cc417790c4d10e9a3550ff1a598f2b0d.jpe" alt=""></p>
<p> </p>
<p>　　点击"finish"之后，会发现Eclipse软件下面的"Map/Reduce Locations"出现一条信息，就是我们刚才建立的"Map/Reduce Location"。 </p>
<p> </p>
<p style="text-align:center;"><img src="http://www.itdaan.com/imgs/4/9/4/3/47/94943300d78a3a3225a96f5592bab4e2.jpe" alt=""></p>
<p>     </p>
<p><span style="color:#0000FF;"><strong>　　第五步</strong></span>：查看HDFS文件系统，并尝试建立文件夹和上传文件。点击Eclipse软件左侧的"DFS Locations"下面的"Win7ToHadoop"，就会展示出HDFS上的文件结构。 </p>
<p> </p>
<p style="text-align:center;"><img src="http://www.itdaan.com/imgs/0/9/7/6/46/b256af7e31d1660a7bceaea1c0844f02.jpe" alt=""></p>
<p>     </p>
<p>　　右击"Win7ToHadoop<span style="font-family:wingdings;">à</span>user<span style="font-family:wingdings;">à</span><span style="color:#FF0000;"><strong>hadoop</strong></span>"可以尝试建立一个"文件夹--xiapi"，然后右击刷新就能查看我们刚才建立的文件夹。 </p>
<p> </p>
<p style="text-align:center;"><img src="http://www.itdaan.com/imgs/1/8/5/1/79/606f77224b303b56c59a316227136af1.jpe" alt=""></p>
<p style="text-align:center;"> </p>
<p>　　创建完之后，并刷新，显示结果如下： </p>
<p> </p>
<p style="text-align:center;"><img src="http://www.itdaan.com/imgs/2/2/3/7/86/7d60515669f964435fc49272e26968d8.jpe" alt=""></p>
<p> </p>
<p>　　用SecureCRT远程登录"Master.Hadoop"服务器，用下面命令查看是否已经建立一个"xiapi"的文件夹。 </p>
<p> </p>
<blockquote>
 <p>hadoop fs -ls </p>
</blockquote>
<p> </p>
<p style="text-align:center;"><img src="http://www.itdaan.com/imgs/1/1/7/9/10/3e34edb1adc3e755e4758c97fde52617.jpe" alt=""></p>
<p>     </p>
<p>　　到此为止，我们的Hadoop Eclipse开发环境已经配置完毕，不尽兴的同学可以上传点本地文件到HDFS分布式文件上，可以互相对比意见文件是否已经上传成功。 </p>
<h2>3、Eclipse运行WordCount程序 </h2>
<h3>3.1 配置Eclipse的JDK </h3>
<p>　　如果电脑上不仅仅安装的JDK6.0，那么要确定一下Eclipse的平台的默认JDK是否6.0。从"Window"菜单下选择"<span style="color:#0000FF;"><strong>Preference</strong></span>"，弹出一个窗体，从窗体的左侧找见"Java"，选择"Installed JREs"，然后添加JDK6.0。下面是我的默认选择JRE。 </p>
<p> </p>
<p style="text-align:center;"><img src="http://www.itdaan.com/imgs/7/1/7/7/74/d7abe496613fe899785ed3d6d3fc55d6.jpe" alt=""></p>
<p style="text-align:center;"> </p>
<p>　　下面是没有添加之前的设置如下： </p>
<p> </p>
<p style="text-align:center;"><img src="http://www.itdaan.com/imgs/0/5/8/2/16/cbea44cabe79481951fd862701558d0d.jpe" alt=""></p>
<p> </p>
<p>　　下面是添加完JDK6.0之后结果如下： </p>
<p> </p>
<p style="text-align:center;"><img src="http://www.itdaan.com/imgs/2/7/6/4/59/a2f270e0111895037eb95e3f5bd06fd5.jpe" alt=""></p>
<p> </p>
<p>　　接着设置Complier。 </p>
<p> </p>
<p style="text-align:center;"><img src="http://www.itdaan.com/imgs/1/1/6/9/80/73ceb6786ad904be54e35f3a14195730.jpe" alt=""></p>
<p style="text-align:center;"> </p>
<h3>3.2 设置Eclipse的编码为UTF-8 </h3>
<p> </p>
<p style="text-align:center;"><img src="http://www.itdaan.com/imgs/7/6/5/3/52/29128ade48f3268e2852557f64ae5d2c.jpe" alt=""></p>
<p style="text-align:center;"> </p>
<h3>3.3 创建MapReduce项目 </h3>
<p> 　　从"File"菜单，选择"Other"，找到"<span style="color:#FF0000;"><strong>Map/Reduce Project</strong></span>"，然后选择它。 </p>
<p> </p>
<p style="text-align:center;"><img src="http://www.itdaan.com/imgs/5/2/1/0/21/9a8ddd5ee9f18bba501bb070cf8e5bd5.jpe" alt=""></p>
<p>     </p>
<p>　　接着，填写MapReduce工程的名字为"WordCountProject"，点击"finish"完成。 </p>
<p> </p>
<p style="text-align:center;"><img src="http://www.itdaan.com/imgs/8/3/9/3/2/2192dbd5c9be15e1eabc76e080c6f5b3.jpe" alt=""></p>
<p> </p>
<p>　　目前为止我们已经成功创建了MapReduce项目，我们发现在Eclipse软件的左侧多了我们的刚才建立的项目。 </p>
<p> </p>
<p style="text-align:center;"><img src="http://www.itdaan.com/imgs/4/8/3/1/55/77a6919d7e3ac4a036bab402e7f7b82b.jpe" alt=""></p>
<p style="text-align:center;"> </p>
<h3>3.4 创建WordCount类 </h3>
<p>　　选择"WordCountProject"工程，右击弹出菜单，然后选择"New"，接着选择"Class"，然后填写如下信息： </p>
<p> </p>
<p style="text-align:center;"><img src="http://www.itdaan.com/imgs/4/0/9/8/40/2ad7449df89935709bd1f18eb2b7a299.jpe" alt=""></p>
<p style="text-align:center;"> </p>
<p>　　因为我们直接用Hadoop1.0.0自带的WordCount程序，所以报名需要和代码中的一致为"org.apache.hadoop.examples"，类名也必须一致为"WordCount"。这个代码放在如下的结构中。 </p>
<p> </p>
<blockquote>
 <p>    hadoop-1.0.0 </p>
 <p>        |---src </p>
 <p>            |---examples </p>
 <p>                |---org </p>
 <p>                    |---apache </p>
 <p>                        |---hadoop </p>
 <p>                            |---examples </p>
</blockquote>
<p> </p>
<p>　　从上面目录中找见"<span style="color:#FF0000;"><strong>WordCount.java</strong></span>"文件，用记事本打开，然后把代码复制到刚才建立的java文件中。当然源码有些变动，变动的红色已经标记出。 </p>
<p> </p>
<div class="cnblogs_Highlighter">
 <pre><code class="language-java">package org.apache.hadoop.examples;<br><br>import java.io.IOException;<br>import java.util.StringTokenizer;<br><br>import org.apache.hadoop.conf.Configuration;<br>import org.apache.hadoop.fs.Path;<br>import org.apache.hadoop.io.IntWritable;<br>import org.apache.hadoop.io.Text;<br>import org.apache.hadoop.mapreduce.Job;<br>import org.apache.hadoop.mapreduce.Mapper;<br>import org.apache.hadoop.mapreduce.Reducer;<br>import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;<br>import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;<br>import org.apache.hadoop.util.GenericOptionsParser;<br><br>public class WordCount {<br><br>  public static class TokenizerMapper <br>       extends Mapper&lt;Object, Text, Text, IntWritable&gt;{<br><br>    private final static IntWritable one = new IntWritable(1);<br>    private Text word = new Text();<br><br>    public void map(Object key, Text value, Context context<br>                    ) throws IOException, InterruptedException {<br>      StringTokenizer itr = new StringTokenizer(value.toString());<br>      while (itr.hasMoreTokens()) {<br>        word.set(itr.nextToken());<br>        context.write(word, one);      }<br>    }<br>  }<br><br>  public static class IntSumReducer <br>       extends Reducer&lt;Text,IntWritable,Text,IntWritable&gt; {<br>    private IntWritable result = new IntWritable();<br><br>    public void reduce(Text key, Iterable values, <br>                       Context context<br>                       ) throws IOException, InterruptedException {<br>      int sum = 0;<br>      for (IntWritable val : values) {<br>        sum += val.get();<br>      }<br>      result.set(sum);<br>      context.write(key, result);<br>    }<br>  }<br><br>  public static void main(String[] args) throws Exception {<br>    Configuration conf = new Configuration();<br>    conf.set("mapred.job.tracker", "192.168.1.2:9001");<br>    String[] ars=new String[]{"input","newout"};<br>    String[] otherArgs = new GenericOptionsParser(conf, ars).getRemainingArgs();<br>    if (otherArgs.length != 2) {<br>      System.err.println("Usage: wordcount  ");<br>      System.exit(2);<br>    }<br>    Job job = new Job(conf, "word count");<br>    job.setJarByClass(WordCount.class);<br>    job.setMapperClass(TokenizerMapper.class);<br>    job.setCombinerClass(IntSumReducer.class);<br>    job.setReducerClass(IntSumReducer.class);<br>    job.setOutputKeyClass(Text.class);<br>    job.setOutputValueClass(IntWritable.class);<br>    FileInputFormat.addInputPath(job, new Path(otherArgs[0]));<br>    FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));<br>    System.exit(job.waitForCompletion(true) ? 0 : 1);<br>  }<br>}</code></pre>
</div>
<p> </p>
<p>　　备注：如果不加"<span style="color:#FF0000;"><strong>conf.set("mapred.job.tracker", "192.168.1.2:9001");</strong></span>"，将提示你的权限不够，其实照成这样的原因是刚才设置的"Map/Reduce Location"其中的配置<span style="color:#FF0000;"><strong>不是</strong></span>完全起作用，而是在本地的磁盘上建立了文件，并尝试运行，显然是不行的。我们要让Eclipse提交作业到Hadoop集群上，所以我们这里手动添加Job运行地址。详细参考"常见问题FAQ_<span style="color:#FF0000;"><strong>3</strong></span>"。</p>
<h3>3.5 运行WordCount程序</h3>
<p>　　选择"Wordcount.java"程序，<span style="color:#FF0000;"><strong>右击</strong></span>一次按照"Run AS<span style="font-family:wingdings;">à</span>Run on Hadoop"运行。然后会弹出如下图，按照下图进行操作。</p>
<p> </p>
<p style="text-align:center;"><img src="http://www.itdaan.com/imgs/4/1/7/7/52/18a9f662d96158c454574d2dac29a6c2.jpe" alt=""></p>
<p style="text-align:center;"> </p>
<p>　　运行结果如下：</p>
<p> </p>
<p style="text-align:center;"><img src="http://www.itdaan.com/imgs/4/6/3/1/12/1dc5fc1c57eb6d90672d863f60ef9bdf.jpe" alt=""></p>
<p> </p>
<p>　　从上图中我们得知我们的程序已经运行成功了。</p>
<h3>3.6 查看WordCount运行结果</h3>
<p>　　查看Eclipse软件左侧，右击"DFS Locations<span style="font-family:wingdings;">à</span>Win7ToHadoop<span style="font-family:wingdings;">à</span>user<span style="font-family:wingdings;">à</span>hadoop"，点击刷新按钮"Refresh"，我们刚才出现的文件夹"newoutput"会出现。记得"newoutput"文件夹是运行程序时自动创建的，如果已经存在相同的的文件夹，要么程序换个新的输出文件夹，要么删除HDFS上的那个重名文件夹，不然会出错。</p>
<p> </p>
<p style="text-align:center;"><img src="http://www.itdaan.com/imgs/9/2/3/4/66/6a1e3ed51353ce6137f886909c71f1e1.jpe" alt=""></p>
<p style="text-align:center;"> </p>
<p>　　打开"newoutput"文件夹，打开"<span style="color:#FF0000;"><strong>part-r-0000</strong></span>0"文件，可以看见执行后的结果。</p>
<p> </p>
<p style="text-align:center;"><img src="http://www.itdaan.com/imgs/3/5/5/5/14/ca7adf746cba8f43d1f014bbb28379a2.jpe" alt=""></p>
<p> </p>
<p>　　到此为止，Eclipse开发环境设置已经完毕，并且成功运行Wordcount程序，下一步我们真正开始Hadoop之旅。</p>
<h2>4、常见问题FAQ</h2>
<h3>4.1 "error: failure to login"问题</h3>
<p>　　下面以网上找的"hadoop-0.20.203.0"为例，我在使用"V1.0"时也出现这样的情况，原因就是那个"hadoop-eclipse-plugin-1.0.0_V1.0.jar"，是直接把源码编译而成，故而缺少相应的Jar包。具体情况如下</p>
<p>　　详细地址：<a href="http://blog.csdn.net/chengfei112233/article/details/7252404" rel="nofollow">http://blog.csdn.net/chengfei112233/article/details/7252404</a></p>
<p>　　在我实践尝试中，发现hadoop-0.20.203.0版本的该包如果直接复制到eclipse的插件目录中，在连接DFS时会出现错误，提示信息为： "error: failure to login"。</p>
<p>　　弹出的错误提示框内容为"<span style="color:#FF0000;"><strong>An internal error occurred during: "Connecting to DFS hadoop".org/apache/commons/configuration/Configuration</strong></span>". 经过察看Eclipse的log，发现是缺少jar包导致的。进一步查找资料后，发现直接复制hadoop-eclipse-plugin-0.20.203.0.jar，该包中lib目录下<span style="color:#FF0000;"><strong>缺少</strong></span>了jar包。</p>
<p>　　经过网上资料搜集，此处给出正确的安装方法：</p>
<p>　　首先要对hadoop-eclipse-plugin-0.20.203.0.jar进行修改。用归档管理器打开该包，发现只有commons-cli-1.2.jar 和hadoop-core.jar两个包。将hadoop/lib目录下的：</p>
<ul style="margin-left:42pt;"><li>
  <div style="text-align:justify;">
   commons-configuration-1.6.jar ,
  </div></li>
 <li>
  <div style="text-align:justify;">
   commons-httpclient-3.0.1.jar ,
  </div></li>
 <li>
  <div style="text-align:justify;">
   commons-lang-2.4.jar ,
  </div></li>
 <li>
  <div style="text-align:justify;">
   jackson-core-asl-1.0.1.jar
  </div></li>
 <li>
  <div style="text-align:justify;">
   jackson-mapper-asl-1.0.1.jar
  </div></li>
</ul><p style="margin-left:21pt;">一共5个包复制到hadoop-eclipse-plugin-0.20.203.0.jar的lib目录下，如下图：</p>
<p> </p>
<p style="text-align:center;"><img src="http://www.itdaan.com/imgs/5/8/8/9/48/16fbbd7098ee5c9d0a4b91e5195567dd.jpe" alt=""></p>
<p> </p>
<p>　　然后，修改该包META-INF目录下的<span style="color:#FF0000;"><strong>MANIFEST.MF</strong></span>，将classpath修改为一下内容：</p>
<p> </p>
<blockquote>
 <p>Bundle-ClassPath:classes/,lib/hadoop-core.jar,lib/commons-cli-1.2.jar,lib/commons-httpclient-3.0.1.jar,lib/jackson-core-asl-1.0.1.jar,lib/jackson-mapper-asl-1.0.1.jar,lib/commons-configuration-1.6.jar,lib/commons-lang-2.4.jar</p>
</blockquote>
<p> </p>
<p style="text-align:center;"><img src="http://www.itdaan.com/imgs/2/1/7/6/92/4e93cc6adce87b40e3bf16b8b65047f3.jpe" alt=""></p>
<p> </p>
<p>　　这样就完成了对hadoop-eclipse-plugin-0.20.203.0.jar的修改。</p>
<p>　　最后，将hadoop-eclipse-plugin-0.20.203.0.jar复制到Eclipse的plugins目录下。</p>
<p><span style="color:#FF0000;"><strong>　　备注：</strong></span>上面的操作对"hadoop-1.0.0"一样适用。</p>
<h3>4.2 "Permission denied"问题</h3>
<p>　　网上试了很多，有提到"hadoop fs -chmod 777 /user/hadoop "，有提到"dfs.permissions 的配置项，将value值改为 false"，有提到"hadoop.job.ugi"，但是通通没有效果。</p>
<p>　　参考文献：</p>
<p>        地址1：<a href="http://www.cnblogs.com/acmy/archive/2011/10/28/2227901.html" rel="nofollow">http://www.cnblogs.com/acmy/archive/2011/10/28/2227901.html</a></p>
<p>        地址2：<a href="http://sunjun041640.blog.163.com/blog/static/25626832201061751825292/" rel="nofollow">http://sunjun041640.blog.163.com/blog/static/25626832201061751825292/</a></p>
<p>　　  错误类型：<span style="color:#FF0000;">org.apache.hadoop.security.AccessControlException: org.apache.hadoop.security </span><span style="color:#FF0000;">.AccessControlException: Permission denied: user=*********, access=WRITE, inode="hadoop": </span><span style="color:#FF0000;">hadoop:supergroup:rwxr-xr-x </span></p>
<p>　　  解决方案：</p>
<p>　　　　我的解决方案直接把系统管理员的名字改成你的Hadoop集群运行hadoop的那个用户。</p>
<h3>4.3 "Failed to set permissions of path"问题</h3>
<p>　　　参考文献：<a href="https://issues.apache.org/jira/browse/HADOOP-8089" rel="nofollow">https://issues.apache.org/jira/browse/HADOOP-8089</a></p>
<p>　　　错误信息如下：</p>
<p><span style="color:#FF0000;">　　　　ERROR security.UserGroupInformation: PriviledgedActionException as: </span><span style="color:#FF0000;">hadoop cause:java.io.IOException Failed to set permissions of path:</span><span style="color:#FF0000;">\usr\hadoop\tmp\mapred\staging\hadoop753422487\.staging to 0700 </span><span style="color:#FF0000;">Exception in thread "main" java.io.IOException: Failed to set permissions of path: </span><span style="color:#FF0000;">\usr\hadoop\tmp \mapred\staging\hadoop753422487\.staging to 0700 </span></p>
<p>　　　解决方法：</p>
<p> </p>
<blockquote>
 <p>Configuration conf = new Configuration();</p>
 <p><span style="color:#FF0000;"><strong>conf.set("mapred.job.tracker", "[server]:9001"); </strong></span></p>
</blockquote>
<p> </p>
<p>　　 "<span style="color:#FF0000;"><strong>[server]:9001</strong></span>"中的"<span style="color:#FF0000;"><strong>[server]</strong></span>"为Hadoop集群Master的IP地址。</p>
<h3>4.4 "hadoop mapred执行目录文件权"限问题</h3>
<p>　　　参考文献：<a href="http://blog.csdn.net/azhao_dn/article/details/6921398" rel="nofollow">http://blog.csdn.net/azhao_dn/article/details/6921398</a></p>
<p>　　　错误信息如下：</p>
<p>　　　job Submission failed with exception 'java.io.IOException(<span style="color:#FF0000;">The ownership/permissions on the staging directory /tmp/hadoop-hadoop-user1/mapred/staging/hadoop-user1/.staging is not as expected. It is owned by hadoop-user1 and permissions are rwxrwxrwx. The directory must be owned by the submitter hadoop-user1 or by hadoop-user1 and permissions must be rwx------</span>)</p>
<p>　　　修改权限：</p>
<p> </p>
<p><img src="http://www.itdaan.com/imgs/5/7/4/8/4/7be537b9f5d3c32fb7d0bd1509926dde.jpe" alt=""></p>
<p> </p>
<p>　　这样就能解决问题。</p>
<p> </p>
<blockquote>
 <p>　　文章<span style="color:#ff0000;">下载</span>地址：<a href="http://files.cnblogs.com/xia520pi/HadoopCluster_Vol.7.rar" rel="nofollow">http://files.cnblogs.com/xia520pi/HadoopCluster_Vol.7.rar</a></p>
</blockquote>
<p> </p><br>转载请注明本文地址：<a href="http://www.itdaan.com/blog/2012/05/20/e568c6336628bea90fa1ab365a0ffcb2.html" rel="nofollow">Hadoop集群（第7期）_Eclipse开发环境设置</a><br>            </div>
                </div>