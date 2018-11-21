---
layout:     post
title:      用 Hadoop 进行分布式并行编程（四）Java远程调用Hadoop服务
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>    前面几篇都是在Hadoop环境中，使用Hadoop工具进行MapReduce计算。本篇介绍在Java应用中如何利用Hadoop服务进行MapReduce计算。<br></p>
<p><span style="font-size:14px;"><strong>一、安装配置Hadoop</strong></span></p>
<p>1、解压Hadoop</p>
<p></p>
<pre><code class="language-plain">$tar zxvf hadoop-1.2.1-bin.tar.gz -C /usr/local/app/hadoop</code></pre>
<p></p>
<p>2、配置Hadoop环境</p>
<p>修改/etc/profile信息：</p>
<p></p>
<pre><code class="language-plain">export JAVA_HOME=/usr/local/app/jdk1.6.0_45
export JRE_HOME=$JAVA_HOME/jre
export HADOOP_HOME=/usr/local/app/hadoop/hadoop-1.2.1
export PATH=$JAVA_HOME/bin:$HADOOP_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar</code></pre>更新系统环境配置：
<p></p>
<p></p>
<pre><code class="language-plain">$source  /etc/profile</code></pre>
<p></p>
<p>3、配置Hadoop服务</p>
<p>配置$HADOOP_HOME/conf/core-site.xml</p>
<p></p>
<pre><code class="language-html">&lt;configuration&gt;
  &lt;property&gt;
    &lt;name&gt;fs.default.name&lt;/name&gt;
    &lt;value&gt;hdfs://192.168.242.128:9000&lt;/value&gt;//建议配置成机器域名或ip
    &lt;final&gt;true&lt;/final&gt;
  &lt;/property&gt;&lt;pre name="code" class="html"&gt;  &lt;property&gt;
    &lt;name&gt;hadoop.tmp.dir&lt;/name&gt;
    &lt;value&gt;/home/guzicheng/hadoop/tmp&lt;/value&gt;
  &lt;/property&gt;
&lt;/configuration&gt;</code></pre>
<p></p>
<pre></pre>
配置$HADOOP_HOME/conf/hdfs-site.xml
<p></p>
<p></p>
<pre><code class="language-html">&lt;configuration&gt;&lt;pre name="code" class="html"&gt;&lt;pre name="code" class="html"&gt;  &lt;property&gt;
    &lt;name&gt;dfs.name.dir&lt;/name&gt;
    &lt;value&gt;/home/guzicheng/hadoop/hdfs/name&lt;/value&gt;
    &lt;final&gt;true&lt;/final&gt;
  &lt;/property&gt;</code></pre>
<p></p>
<pre></pre>
<pre><code class="language-html">&lt;pre name="code" class="html"&gt;  &lt;property&gt;
    &lt;name&gt;dfs.data.dir&lt;/name&gt;
    &lt;value&gt;/home/guzicheng/hadoop/hdfs/data&lt;/value&gt;
    &lt;final&gt;true&lt;/final&gt;
  &lt;/property&gt;&lt;pre name="code" class="html"&gt;&lt;pre name="code" class="html"&gt;  &lt;property&gt;
    &lt;name&gt;dfs.replication&lt;/name&gt;
    &lt;value&gt;3&lt;/value&gt;
  &lt;/property&gt;
&lt;/configuration&gt;</code></pre>
<pre></pre>
<pre></pre>
<pre></pre>
<pre></pre>
配置$HADOOP_HOME/conf/mapred-site.xml<pre><code class="language-html">&lt;configuration&gt;&lt;pre name="code" class="html"&gt;&lt;pre name="code" class="html"&gt;  &lt;property&gt;
    &lt;name&gt;mapred.job.tracker&lt;/name&gt;
    &lt;value&gt;192.168.242.128:9001&lt;/value&gt;
    &lt;final&gt;true&lt;/final&gt;
  &lt;/property&gt;
&lt;/configuration&gt;</code></pre>
<pre></pre>
<pre></pre>
<p></p>
<p>4、启动Hadoop服务</p>
<p>进入$HADOOP_HOME/bin目录</p>
<p></p>
<pre><code class="language-plain">$./start-all.sh</code></pre>启动过程中会提示输入系统用户的密码，输入密码回车即可。
<p></p>
<p>启动成功后会出现NameNode、SecondaryNameNode、DataNode、JobTracker、TaskTracker这5个进程，可以输入如下命令查看：</p>
<p></p>
<pre><code class="language-plain">$jps</code></pre><br><p></p>
<p><span style="font-size:14px;"><strong>二、编写Java客户端</strong></span></p>
<p><strong>1、Maven配置</strong></p>
<p></p>
<pre><code class="language-html">&lt;dependency&gt;
    &lt;groupId&gt;org.apache.hadoop&lt;/groupId&gt;
    &lt;artifactId&gt;hadoop-core&lt;/artifactId&gt;
    &lt;version&gt;1.2.1&lt;/version&gt;
&lt;/dependency&gt;</code></pre><br><p></p>
<p><strong>2、Java代码</strong></p>
<p></p>
<pre><code class="language-java">@Service
public class WordCountServiceImpl implements WordCountService
{
  //测试代码
  public static void main(String[] args)
  {
    try {
        new WordCountServiceImpl().wordCount();
    } catch (Exception e){
       e.printStackTrace();
    }
  }

  public int wordCount() throws Exception {
    Configuration conf = new Configuration();
    conf.set("fs.default.name", "hdfs://192.168.242.128:9000");//设置hadoop服务地址

    Job job = new Job(conf, "word count");
    job.setJarByClass(WordCount.class);
    job.setMapperClass(TokenizerMapper.class);
    job.setCombinerClass(IntSumReducer.class);
    job.setReducerClass(IntSumReducer.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(IntWritable.class);
    FileInputFormat.addInputPath(job, new Path("input"));//设置hdfs输入路径，hdfs://192.168.242.128:9000/user/guzicheng/input
    FileOutputFormat.setOutputPath(job, new Path("output"));//设置hdfs输出路径，hdfs://192.168.242.128:9000/user/guzicheng/output
    return job.waitForCompletion(true) ? 0 : 1;

  }

  public static class IntSumReducer extends Reducer&lt;Text, IntWritable, Text, IntWritable&gt;
  {
    private IntWritable result = new IntWritable();

    public void reduce(Text key, Iterable&lt;IntWritable&gt; values, Reducer&lt;Text, IntWritable, Text, IntWritable&gt;.Context context)
      throws IOException, InterruptedException
    {
      int sum = 0;
      for (IntWritable val : values) {
        sum += val.get();
      }
      this.result.set(sum);
      context.write(key, this.result);
    }
  }

  public static class TokenizerMapper extends Mapper&lt;Object, Text, Text, IntWritable&gt;
  {
    private static final IntWritable one = new IntWritable(1);
    private Text word = new Text();

    public void map(Object key, Text value, Mapper&lt;Object, Text, Text, IntWritable&gt;.Context context) throws IOException, InterruptedException
    {
      StringTokenizer itr = new StringTokenizer(value.toString());
      while (itr.hasMoreTokens()) {
        this.word.set(itr.nextToken());
        context.write(this.word, one);
      }
    }
  }
}</code></pre>
<p></p>
<p><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:14px;"><strong>三、问题汇总</strong></span></p>
<p><strong>1、连接失败</strong></p>
<p>    异常信息：</p>
<p></p>
<pre><code class="language-plain">java.net.ConnectException: Call to 192.168.242.128/192.168.242.128:9000 failed on connection exception: java.net.ConnectException: Connection refused: no further information
        at org.apache.hadoop.ipc.Client.wrapException(Client.java:1142)
        ……</code></pre>
<p></p>
<p>   问题原因：</p>
<p>    1）Hadoop服务配置问题</p>
<p>         配置Hadoop服务的时候，指定的服务地址为本机域名或地址（localhost或127.0.0.1），如：</p>
<p></p>
<pre><code class="language-html">  &lt;property&gt;
    &lt;name&gt;fs.default.name&lt;/name&gt;
    &lt;value&gt;hdfs://127.0.0.1:9000&lt;/value&gt;
    &lt;final&gt;true&lt;/final&gt;
  &lt;/property&gt;</code></pre>
<p></p>
<p>    2）服务器防火墙</p>
<p>        Hadoop服务端口被防火墙阻挡。</p>
<p><br></p>
<p>   解决方法：</p>
<p>    1）修改Hadoop服务配置</p>
<p></p>
<pre><code class="language-html">  &lt;property&gt;
    &lt;name&gt;fs.default.name&lt;/name&gt;
    &lt;value&gt;hdfs://192.168.242.128:9000&lt;/value&gt;
    &lt;final&gt;true&lt;/final&gt;
  &lt;/property&gt;</code></pre>
<p></p>
    2）服务器防火墙
<p>        打开Linux端口9000（namenode rpc交互端口）、50010（datanode交互端口）、50030（job tracker交互端口）。</p>
<p>        修改/etc/sysconfig/iptables</p>
<p></p>
<pre><code class="language-plain">&lt;pre name="code" class="plain"&gt;-A INPUT -m state --state NEW -m tcp -p tcp --dport 9000 -j ACCEPT</code></pre>-A INPUT -m state --state NEW -m tcp -p tcp --dport 50010 -j ACCEPT<br><pre><code class="language-plain">-A INPUT -m state --state NEW -m tcp -p tcp --dport 50030 -j ACCEPT</code></pre>
<p></p>
<pre></pre>
       重启防火墙
<p></p>
<p></p>
<pre><code class="language-plain">$service iptables restart</code></pre><br><p></p>
<p><strong>2、文件权限问题</strong></p>
<p>    环境：java客户端运行环境为windows，hadoop服务运行环境为linux<strong><br></strong></p>
<p>    异常信息：<br></p>
<pre><code class="language-plain">java.io.IOException: Failed to set permissions of path: \tmp\……
        at org.apache.hadoop.fs.FileUtil.checkReturnValue(FileUtil.java:691)
        ……
</code></pre>
<p>   问题原因：Windows下的文件权限问题导致，在Linux下不存在</p>
<p>   解决方法：注释掉FileUtil.checkReturnValue()方法，如下</p>
<p></p>
<pre><code class="language-java">  ……
  private static void checkReturnValue(boolean rv, File p, FsPermission permission)
    throws IOException
  {
    /**
    if (!rv)
      throw new IOException(new StringBuilder().append("Failed to set permissions of path: ").append(p).append(" to ").
        append(String.format("%04o", new Object[] { Short.valueOf(permission.toShort()) })).toString());
    **/
 }
……
</code></pre>   重新编译打包的<a href="http://download.csdn.net/detail/guzicheng/8199739" rel="nofollow">hadoop-1.2.1.jar</a><br><p></p>
<p><br></p>
<p>参考：</p>
<p><a href="http://www.cnblogs.com/ggjucheng/archive/2012/04/17/2454590.html" rel="nofollow">《hadoop集群默认配置和常用配置》</a><br></p>
<p><br></p>
            </div>
                </div>