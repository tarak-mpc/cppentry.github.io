---
layout:     post
title:      Hadoop Job的提交
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div class="BlogAnchor" style="background-color:rgb(244,247,249);line-height:23px;font-family:Verdana, sans-serif, '宋体';font-size:13px;">
<div class="AnchorContent" id="AnchorContent">

<a href="http://my.oschina.net/zhzhenqin/blog/163158#OSC_h2_1" rel="nofollow" style="color:rgb(68,102,187);text-decoration:none;">一个最基本的Hadoo任务</a>
<a href="http://my.oschina.net/zhzhenqin/blog/163158#OSC_h2_2" rel="nofollow" style="color:rgb(68,102,187);text-decoration:none;">Hadoop任务提交</a>
<a href="http://my.oschina.net/zhzhenqin/blog/163158#OSC_h3_3" rel="nofollow" style="color:rgb(68,102,187);text-decoration:none;">传统的Hadoop任务提交</a>
<a href="http://my.oschina.net/zhzhenqin/blog/163158#OSC_h3_4" rel="nofollow" style="color:rgb(68,102,187);text-decoration:none;">Eclipse的hadoop插件的Hadoop任务提交</a>
<a href="http://my.oschina.net/zhzhenqin/blog/163158#OSC_h3_5" rel="nofollow" style="color:rgb(68,102,187);text-decoration:none;">在Eclipse中当做Java Application运行为什么不可以?</a>
<a href="http://my.oschina.net/zhzhenqin/blog/163158#OSC_h2_6" rel="nofollow" style="color:rgb(68,102,187);text-decoration:none;">背景</a>
<a href="http://my.oschina.net/zhzhenqin/blog/163158#OSC_h2_7" rel="nofollow" style="color:rgb(68,102,187);text-decoration:none;">用JVisualVM监视Eclipse hadoop插件的Hadoop任务提交</a>
<a href="http://my.oschina.net/zhzhenqin/blog/163158#OSC_h2_8" rel="nofollow" style="color:rgb(68,102,187);text-decoration:none;">使用Hadoop Api提交Job,完美解决方案</a>
<a href="http://my.oschina.net/zhzhenqin/blog/163158#OSC_h2_9" rel="nofollow" style="color:rgb(68,102,187);text-decoration:none;">哪些是在Client执行的?哪些是在Hadoop集群中执行?</a>
<a href="http://my.oschina.net/zhzhenqin/blog/163158#OSC_h2_10" rel="nofollow" style="color:rgb(68,102,187);text-decoration:none;">Hadoop分布式缓存</a></div>
</div>
<div class="BlogContent" style="font-size:13px;overflow:hidden;color:rgb(51,51,51);line-height:23px;font-family:Verdana, sans-serif, '宋体';">
<span id="OSC_h2_1"></span>
<h2 style="line-height:36px;font-size:20px;">一个最基本的Hadoo任务</h2>
<p style="letter-spacing:.5px;font-size:12.5px;">
WordCountMapper:</p>
<pre><code class="language-java">private final static IntWritable one = new IntWritable(1);
@Override
public void map(LongWritable key, Text value, 
        Context context) throws IOException, InterruptedException {
    StringTokenizer tokenizer = new StringTokenizer(value.toString());
    while (tokenizer.hasMoreTokens()) {
        String str = tokenizer.nextToken();
        context.write(new Text(StringUtils.trim(str.replaceAll("\\W", ""))), one);
    }
}</code></pre>
<p style="letter-spacing:.5px;font-size:12.5px;">
WordCountReduce:</p>
<pre><code class="language-java">@Override
public void reduce(Text key, Iterable

 

 
  
 
  
  values,
        Context context) throws IOException, InterruptedException {
    int sum = 0;
    for (IntWritable val : values) {
            sum += val.get();
    }
    context.write(key, new IntWritable(sum));
    }


 

 </code></pre>
<p style="letter-spacing:.5px;font-size:12.5px;">
WordCount:</p>
<pre><code class="language-java">final Configuration conf = new Configuration();
Job job = new Job(conf);
job.setJobName("wordcount");
job.setJarByClass(WordCount.class);

job.setOutputKeyClass(Text.class);
job.setOutputValueClass(IntWritable.class);

job.setMapperClass(WordCountMapper.class);
job.setReducerClass(WordCountReduce.class);

job.setInputFormatClass(TextInputFormat.class);
job.setOutputFormatClass(TextOutputFormat.class);

FileInputFormat.setInputPaths(job, new Path(args[0]));
FileOutputFormat.setOutputPath(job, new Path(args[1]));

boolean success = job.waitForCompletion(true);</code></pre>
<p style="letter-spacing:.5px;font-size:12.5px;">
如你所见, 这是个Hadoop基础的入门例子, 如果你了解Hadoop, 你已经对这些代码熟记于心了.这篇文章我想说明Hadoop提交Job到底提交了那些东西,提交了哪些类,不需要提交那些东西.</p>
<span id="OSC_h2_2"></span>
<h2 style="line-height:36px;font-size:20px;">Hadoop任务提交</h2>
<span id="OSC_h3_3"></span>
<h3 style="line-height:28px;font-size:16px;">传统的Hadoop任务提交</h3>
<p style="letter-spacing:.5px;font-size:12.5px;">
把上面的代码<code style="font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier;border:1px solid rgb(238,238,238);background-color:rgb(252,252,252);color:rgb(64,170,83);"> WordCountMapper,
 WordCountReduce, WordCount</code>打包成jar,放到hadoop目录下, 使用<code style="font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier;border:1px solid rgb(238,238,238);background-color:rgb(252,252,252);color:rgb(64,170,83);">hadoop
 jar wordcount.jar WordCount</code>运行任务. 这样方式我称为传统的方式,也是《hadoop权威指南》上一贯的方法.</p>
<span id="OSC_h3_4"></span>
<h3 style="line-height:28px;font-size:16px;">Eclipse的hadoop插件的Hadoop任务提交</h3>
<p style="letter-spacing:.5px;font-size:12.5px;">
如果你开发过Hadoop的Job, 那么对这个应该很熟悉.大多数开发测试都是用这个提交任务的,如果每次都是打包成jar, 再用hadoop jar <span></span>这还不把人搞疯.</p>
<p style="letter-spacing:.5px;font-size:12.5px;">
如果你还细心,你会发现,你选好Hadoop的jobtracker,提交任务的前一刻,Eclipse会弹出一个浮动窗口,上面跳动着显示很多jar名.为什么会这样?它做了什么?</p>
<p style="letter-spacing:.5px;font-size:12.5px;">
<a href="http://static.oschina.net/uploads/space/2013/0922/192826_SM01_259382.png" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><img src="http://static.oschina.net/uploads/space/2013/0922/192826_SM01_259382.png" alt="在此输入图片描述" style="border:1px solid rgb(221,221,221);background-color:rgb(244,247,249);"></a></p>
<span id="OSC_h3_5"></span>
<h3 style="line-height:28px;font-size:16px;">在Eclipse中当做Java Application运行为什么不可以?</h3>
<p style="letter-spacing:.5px;font-size:12.5px;">
Hadoop的job项目都有main方法,这个是符合JavaApplication运行条件的,那么我们是不是可以使用Eclipse中直接运行呢?当我们尝试运行的时候,程序是可以运行的,但总当运行一会儿(几秒钟)后抛出WordCountMapper <code style="font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier;border:1px solid rgb(238,238,238);background-color:rgb(252,252,252);color:rgb(64,170,83);">ClassNotFount</code>的错误.</p>
<p style="letter-spacing:.5px;font-size:12.5px;">
那么为什么程序不是直接抛出错误而是过了一会儿才抛出?为什么用Eclipse Hadoop插件运行不会发生这个错误.</p>
<span id="OSC_h2_6"></span>
<h2 style="line-height:36px;font-size:20px;">背景</h2>
<p style="letter-spacing:.5px;font-size:12.5px;">
写这篇文章前,我们已经正在开发一个Hadoop的任务调度的系统. 也就是一个项目中提前写好很多个Hadoop Job绑定到里边,如果想要运行哪个Job,我们就从前台配置好参数,并把这个Job提交到Hadoop集群. 并从前台不断的得到Job的运行任务信息,取得Job的执行进度. 不关心进度的话直接去喝茶就可以,完了来看结果.</p>
<p style="letter-spacing:.5px;font-size:12.5px;">
如果每次都是<code style="font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier;border:1px solid rgb(238,238,238);background-color:rgb(252,252,252);color:rgb(64,170,83);">hadoop
 jar wordcount.jar WordCount</code>未免太弱智. 如果每次从Eclispe中运行,那专业性太强,也不可取.是不是有更好的方法提交任务?</p>
<p style="letter-spacing:.5px;font-size:12.5px;">
答案肯定是有的.</p>
<span id="OSC_h2_7"></span>
<h2 style="line-height:36px;font-size:20px;">用JVisualVM监视Eclipse hadoop插件的Hadoop任务提交</h2>
<p style="letter-spacing:.5px;font-size:12.5px;">
打开JVisualVM准备着,运行一个Job. 运行后立即就可以看到一个Java进程. 用JVisualVM打开这个进程查看,如图:</p>
<p style="letter-spacing:.5px;font-size:12.5px;">
<a href="http://static.oschina.net/uploads/space/2013/0922/192853_pJJ0_259382.png" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><img src="http://static.oschina.net/uploads/space/2013/0922/192853_pJJ0_259382.png" alt="在此输入图片描述" style="border:1px solid rgb(221,221,221);background-color:rgb(244,247,249);"></a></p>
<p style="letter-spacing:.5px;font-size:12.5px;">
<a href="http://static.oschina.net/uploads/space/2013/0922/192912_G7RP_259382.png" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><img src="http://static.oschina.net/uploads/space/2013/0922/192912_G7RP_259382.png" alt="在此输入图片描述" style="border:1px solid rgb(221,221,221);background-color:rgb(244,247,249);"></a></p>
<p style="letter-spacing:.5px;font-size:12.5px;">
我打开我电脑上的目录<code style="font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier;border:1px solid rgb(238,238,238);background-color:rgb(252,252,252);color:rgb(64,170,83);">F:\Eclipse\workspace.metadata.plugins\org.apache.hadoop.eclipse\hadoop-conf-1007657720166395816</code>并且查看了它的上级目录,顿时一些皆明朗了.Eclipse
 Hadoop插件竟然把我的项目下所有的类,资源文件打包成jar然后运行的.</p>
<p style="letter-spacing:.5px;font-size:12.5px;">
<a href="http://static.oschina.net/uploads/space/2013/0922/192934_sNAa_259382.png" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><img src="http://static.oschina.net/uploads/space/2013/0922/192934_sNAa_259382.png" alt="在此输入图片描述" style="border:1px solid rgb(221,221,221);background-color:rgb(244,247,249);"></a></p>
<p style="letter-spacing:.5px;font-size:12.5px;">
<a href="http://static.oschina.net/uploads/space/2013/0922/192944_N3yQ_259382.png" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><img src="http://static.oschina.net/uploads/space/2013/0922/192944_N3yQ_259382.png" alt="在此输入图片描述" style="border:1px solid rgb(221,221,221);background-color:rgb(244,247,249);"></a></p>
<span id="OSC_h2_8"></span>
<h2 style="line-height:36px;font-size:20px;">使用Hadoop Api提交Job,完美解决方案</h2>
<p style="letter-spacing:.5px;font-size:12.5px;">
其实我也是从这篇文章(http://luliangy.iteye.com/blog/1401453)中找到灵感的.为什么把项目打包,以Java Application的方式就正常运行了.</p>
<pre><code class="language-java">((JobConf) job.getConfiguration()).setJar("wordcount.jar");  
job.setJarByClass(WordCount.class);</code></pre>
<p style="letter-spacing:.5px;font-size:12.5px;">
冲着这股劲我看了很多Hadoop API,终于找到为什么.</p>
<p style="letter-spacing:.5px;font-size:12.5px;">
通过<code style="font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier;border:1px solid rgb(238,238,238);background-color:rgb(252,252,252);color:rgb(64,170,83);">job.setJarByClass(WordCount.class); </code>这条路查看源码,
 你会找到如下的两个方法.</p>
<pre><code class="language-java">private static String findContainingJar(Class my_class) {
    ClassLoader loader = my_class.getClassLoader();
    String class_file = my_class.getName().replaceAll("\\.", "/") + ".class";
    try {
      for(Enumeration itr = loader.getResources(class_file);
          itr.hasMoreElements();) {
        URL url = (URL) itr.nextElement();
        if ("jar".equals(url.getProtocol())) {
          String toReturn = url.getPath();
          if (toReturn.startsWith("file:")) {
            toReturn = toReturn.substring("file:".length());
          }
          toReturn = URLDecoder.decode(toReturn, "UTF-8");
          return toReturn.replaceAll("!.*$", "");
        }
      }
    } catch (IOException e) {
      throw new RuntimeException(e);
    }
    return null;
  }

public void setJarByClass(Class cls) {
    String jar = findContainingJar(cls);
    if (jar != null) {
      setJar(jar);
    }   
}</code></pre>
<p style="letter-spacing:.5px;font-size:12.5px;">
我说说<code style="font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier;border:1px solid rgb(238,238,238);background-color:rgb(252,252,252);color:rgb(64,170,83);">findContainingJar</code>有什么作用?
 当使用<code style="font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier;border:1px solid rgb(238,238,238);background-color:rgb(252,252,252);color:rgb(64,170,83);">job.setJarByClass(WordCount.class);</code>设置类的时候,
 Hadoop Client能从你的classpath中取得<code style="font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier;border:1px solid rgb(238,238,238);background-color:rgb(252,252,252);color:rgb(64,170,83);">WordCount.class</code>所在的jar包的jar
 File绝对路径.如果找不到jar, <code style="font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier;border:1px solid rgb(238,238,238);background-color:rgb(252,252,252);color:rgb(64,170,83);">setJar(jar);</code>方法没有执行,jar肯定是个空值.</p>
<p style="letter-spacing:.5px;font-size:12.5px;">
我们在Eclipse中直接以Java Application运行的时候,classpath是一个本地文件夹, findContainingJar肯定找不到项目的jar.也就是Mapper和Reduce所在的jar. 这样在提交任务时候Configuration中<code style="font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier;border:1px solid rgb(238,238,238);background-color:rgb(252,252,252);color:rgb(64,170,83);">mapper.jar</code>属性是一个空值.这也就解释了为什么在Eclipse中当做Java
 Application运行时总是过一段时间后才发生ClassNotFound的错误原因.</p>
<p style="letter-spacing:.5px;font-size:12.5px;">
其实到这里Hadoop提交了什么也好解释了.</p>
<p style="letter-spacing:.5px;font-size:12.5px;">
Hadoop向集群中提交了一个xml和一个携带Mapper/Reduce的jar. xml就是Configuration对象序列化的结果.</p>
<p style="letter-spacing:.5px;font-size:12.5px;">
说到这里也许你已经发现,这是一个开发上的架构问题.既然Hadoop Job需要Map/Reduce的jar.我们应该把所有的Map/Reduce单独在一个项目中开发.然后打包放入调度系统项目的ClassPath就好了.然后在调度系统中构造Job,并把<code style="font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier;border:1px solid rgb(238,238,238);background-color:rgb(252,252,252);color:rgb(64,170,83);">job.setJarByClass(class);</code>中的class设置为该Job的map
 clas或者reduce class就行了.</p>
<span id="OSC_h2_9"></span>
<h2 style="line-height:36px;font-size:20px;">哪些是在Client执行的?哪些是在Hadoop集群中执行?</h2>
<p style="letter-spacing:.5px;font-size:12.5px;">
一个Hadoop 任务一般都有3个类(Map/Reduce/Job).<code style="font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier;border:1px solid rgb(238,238,238);background-color:rgb(252,252,252);color:rgb(64,170,83);">WordCountMapper,
 WordCountReduce, WordCount</code>你认为这三个类都会提交到集群中执行吗?</p>
<p style="letter-spacing:.5px;font-size:12.5px;">
不是! 只有Mapper和Reduce这2个类会提交到Hadoop集群, MapReduce执行也是这2个类. WordCount只是充当一个配置Job的客户端,并且提交任务,之后又定时轮询Job的运行状态输出简单的日志,直到任务完成,WordCount的这个进程会自动退出.</p>
<span id="OSC_h2_10"></span>
<h2 style="line-height:36px;font-size:20px;">Hadoop分布式缓存</h2>
<p style="letter-spacing:.5px;font-size:12.5px;">
讲到这里你也许能顺利的实现一个和我相同思路的系统了.</p>
<p style="letter-spacing:.5px;font-size:12.5px;">
但是我还是想说一个常见错误. 不是Mapper Class NotFound,而是Mapper中使用的Class NotFound. 而你又不想往hadoop集群中添加jar包,也不想重启Hadoop集群. 你可以使用Hadoop提供的一个类:<code style="font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier;border:1px solid rgb(238,238,238);background-color:rgb(252,252,252);color:rgb(64,170,83);">DistributedCache</code></p>
<ol><li>DistributedCache.addArchiveToClassPath() 添加hdfs上的jar到MapReduce的classpath</li><li>DistributedCache.addCacheFile(new URI(“/myapp/lookup.dat#lookup.dat”), job);</li><li>DistributedCache.addCacheArchive(new URI(“/myapp/map.zip”, job);</li><li>DistributedCache.addFileToClassPath(new Path(“/myapp/mylib.jar”), job);</li><li>DistributedCache.addCacheArchive(new URI(“/myapp/mytar.tar”, job);</li><li>DistributedCache.addCacheArchive(new URI(“/myapp/mytgz.tgz”, job);</li><li>DistributedCache.addCacheArchive(new URI(“/myapp/mytargz.tar.gz”, job);</li></ol></div>
            </div>
                </div>