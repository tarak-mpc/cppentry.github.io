---
layout:     post
title:      Hive集成HBase（一）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u010376788/article/details/50890622				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><a href="http://blog.csdn.net/u010376788/article/details/50890622" rel="nofollow">Hive集成HBase（一）</a></p>
<p><a href="http://blog.csdn.net/u010376788/article/details/50905476" rel="nofollow">Hive集成HBase（二）</a><br></p>
<p>在介绍Hive集成HBase的使用之前，我们先看看Hive和HBase之间通信用到的一个包，这个包至关重要，容不得有半点差池，否则就会集成失败。下面这个官网链接可供参考<a href="https://cwiki.apache.org/confluence/display/Hive/StorageHandlers" rel="nofollow">https://cwiki.apache.org/confluence/display/Hive/StorageHandlers</a>。因此，第一篇文章先讨论Hive和HBase的通信包，第二篇才开始介绍Hive集成HBase之后的使用。</p>
<p></p>
<h1>1.hive-hbase-handler.jar</h1>
在Hive继承HBase中，二者的通信包就是hive-hbase-handler.jar，极其重要。如果我们用$HIVE_HOME/lib目录下的hive-hbase-handler-x.y.z.jar，那么一般都不会集成成功，反而报错如：<br><pre><code class="language-plain">FAILED: Execution Error, return code 1 from org.apache.hadoop.hive.ql.exec.DDLTask. org.apache.hadoop.hbase.HTableDescriptor.addFamily(Lorg/apache/hadoop/hbase/HColumnDescriptor;)V</code></pre><br>
这说明Hive和HBase的版本不兼容。因此，我们必须根据自己安装的Hive和HBase的版本自己重新编译hive-hbase-handler.jar，而且还有修改hive-handler的源码（<span style="color:#ff0000;"><strong>没错，你绝对没有看错我们要修改源代码。不过，不要恐慌，我们只修改两三行代码而已）</strong></span>。<br><br><p></p>
<p></p>
<h1>2.编译hive-hbase-handler源码</h1>
hive-hbase-handler的源码在Hive源码中，因此，我们需要先在下载Hive源码，官网地址：<a href="http://mirror.bit.edu.cn/apache/hive/" rel="nofollow">http://mirror.bit.edu.cn/apache/hive/</a><br>
现在我们按照以下步骤来完成编译：<br><h2>1.把hive-hbase-handler源码导入到Eclipse中</h2>
首先，需要在Eclipse中新建一个Java项目，项目名可以随便起，如hivehbase。
<p></p>
<p style="text-align:center;"><img src="https://img-blog.csdn.net/20160314224909240?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>然后，我们在<strong><span style="font-size:18px;color:#ff0000;">src目录</span></strong>下导入hive-handler源码。<br></p>
<div style="text-align:center;"><img src="https://img-blog.csdn.net/20160314225008276?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></div>
<p></p>
<p>在Hive源码中找到hive-handler源码，<span style="font-size:18px;color:#ff0000;"><strong>切记现在hive-handler目录下的java目录，而不是把hive-handler导入</strong></span>。<br></p>
<p style="text-align:center;"><img src="https://img-blog.csdn.net/20160314225127074?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p style="text-align:center;"><img src="https://img-blog.csdn.net/20160314225136181?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p>接着，选择org目录即可。<br></p>
<p style="text-align:center;"><img src="https://img-blog.csdn.net/20160314225209822?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p></p>
<h2>2.下载依赖jar包</h2>
<p></p>
<p><span style="font-size:18px;color:#ff0000;"><strong>注意：不同版本之间的集成依赖的包的数量和种类不一样。</strong></span><br></p>
依赖的jar包有这么些（Hive1.0.1和HBase1.1.3集成）。<br><div style="text-align:center;"><img src="https://img-blog.csdn.net/20160314225243683?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></div>
<div style="text-align:left;">再看Hive1.2.1和HBase0.98的集成，需要的jar却是（其实，如果不修改源代码还需要high-scale-lib-x.y.z.jar，在下一节我会告知在哪里修改源代码）：</div>
<div style="text-align:center;"><img src="https://img-blog.csdn.net/20160315094821617?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<p>关于这些jar包在哪下载，我们可以去Maven中央仓库<a href="http://repo.maven.apache.org/maven2/" rel="nofollow">http://repo.maven.apache.org/maven2/</a>。但是，有一个jar包——high-scale-lib-x.y.z.jar在Maven中央仓库找不到，它的下载地址是<a href="http://www.java2s.com/Code/Jar/h/Downloadhighscalelib10jar.htm" rel="nofollow">http://www.java2s.com/Code/Jar/h/Downloadhighscalelib10jar.htm</a>。不过还是建议在$HIVE_HOME\lib和$HBASE_HOME\lib里面找相应的jar。关于high-scale-lib-x.y.z.jar，我们后面还要提到
 high-scale-lib-x.y.z.jar。然后，把上述jar包加入CLASSPATH就不会报错了。如果到此，你觉得已经没错了，可以编译了。那么，你就大错特错了（除非你用的是HBase1.0之前的版本）。因为你用此时编译的hive-hbase-handler.jar，当把Hive表存到HBase表中时，会报错：<br></p>
<p></p>
<pre><code class="language-plain">java.lang.NoClassDefFoundError: org/cliffc/high_scale_lib/Counter。</code></pre><br><p></p>
<h2>3.修改hive-handler源代码</h2>
前面让你留意了high-scale-lib-x.y.z.jar，因为正是high-scale-lib-x.y.z.jar，才使得我们需要修改hive-handler源码。我们找到org.apache.hadoop.hive.hbase.HBaseStorageHandler，然后定位到472行（仅对于Hive1.0.1），可以看到org.cliffc.high_scale_lib.Counter.class。<br>
我们可以找到源码：<br><pre><code class="language-java">TableMapReduceUtil.addDependencyJars(
          jobConf, HBaseStorageHandler.class, TableInputFormatBase.class,
          org.cliffc.high_scale_lib.Counter.class); // this will be removed for HBase 1.0
</code></pre><br>
看到后面的注释，所以我们把这三行代码修改为：<pre><code class="language-java">TableMapReduceUtil.addDependencyJars(
          jobConf, HBaseStorageHandler.class, TableInputFormatBase.class);
</code></pre>
<p><br></p>
<p>对于Hive1.2.1的源码又不一样了，找到org.apache.hadoop.hive.hbase.HBaseStorageHandler，定位到源码：</p>
<p></p>
<pre><code class="language-java">  private static Class counterClass = null;
  static {
    try {
      counterClass = Class.forName("org.cliffc.high_scale_lib.Counter");
    } catch (ClassNotFoundException cnfe) {
      // this dependency is removed for HBase 1.0
    }
  }
  @Override
  public void configureJobConf(TableDesc tableDesc, JobConf jobConf) {
    try {
      HBaseSerDe.configureJobConf(tableDesc, jobConf);
      /*
       * HIVE-6356
       * The following code change is only needed for hbase-0.96.0 due to HBASE-9165, and
       * will not be required once Hive bumps up its hbase version). At that time , we will
       * only need TableMapReduceUtil.addDependencyJars(jobConf) here.
       */
      if (counterClass != null) {
        TableMapReduceUtil.addDependencyJars(
          jobConf, HBaseStorageHandler.class, TableInputFormatBase.class, counterClass);
         
      } </code></pre><br>
严格来讲应该修改成：
<p></p>
<p></p>
<pre><code class="language-java">//  private static Class counterClass = null;
//  static {
//    try {
//      counterClass = Class.forName("org.cliffc.high_scale_lib.Counter");
//    } catch (ClassNotFoundException cnfe) {
//      // this dependency is removed for HBase 1.0
//    }
//  }
  @Override
  public void configureJobConf(TableDesc tableDesc, JobConf jobConf) {
    try {
      HBaseSerDe.configureJobConf(tableDesc, jobConf);
      /*
       * HIVE-6356
       * The following code change is only needed for hbase-0.96.0 due to HBASE-9165, and
       * will not be required once Hive bumps up its hbase version). At that time , we will
       * only need TableMapReduceUtil.addDependencyJars(jobConf) here.
       */
      if (counterClass != null) {
        TableMapReduceUtil.addDependencyJars(
//          jobConf, HBaseStorageHandler.class, TableInputFormatBase.class, counterClass);
          jobConf, HBaseStorageHandler.class, TableInputFormatBase.class);
      } </code></pre><br>
但是，也可以只修改成这样即可：
<p></p>
<p></p>
<pre><code class="language-java">    if (counterClass != null) {
        TableMapReduceUtil.addDependencyJars(
//          jobConf, HBaseStorageHandler.class, TableInputFormatBase.class, counterClass);
          jobConf, HBaseStorageHandler.class, TableInputFormatBase.class);
      }</code></pre>
<p></p>
<p><br></p>
<h2>4.打包</h2>
现在我们终于可以打包了。这个jar是不可执行的。<br><div style="text-align:center;"><img src="https://img-blog.csdn.net/20160314225728814?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></div>
<p></p>
<p><br></p>
<p><br></p>
            </div>
                </div>