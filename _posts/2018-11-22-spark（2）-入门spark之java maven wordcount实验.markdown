---
layout:     post
title:      spark（2）-入门spark之java maven wordcount实验
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/answer100answer/article/details/78697470				</div>
								            <div id="content_views" class="markdown_views prism-atelier-sulphurpool-light">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p></p><div class="toc"><h3>文章目录</h3><ul><ul><li><a href="#1__java__spark_wordcount_4" rel="nofollow">1.  java 实现 spark wordcount实验</a></li></ul></ul></div><p></p>
<blockquote>
<p>在上一篇<a href="http://blog.csdn.net/answer100answer/article/details/78688756" rel="nofollow">spark（1）-入门spark之scala sbt wordcount实验</a>的基础上，继续学习java版本wordcount实验。</p>
</blockquote>
<h2><a id="1__java__spark_wordcount_4"></a>1.  java 实现 spark wordcount实验</h2>
<hr>
<p><strong>1</strong> （eclipse）新建maven项目，quick-start</p>
<pre><code>Group Id：cn.whbing.spark
Atifacts Id：SparkApps
</code></pre>
<p><strong>2</strong> <code>JRE（SystemLibrary）</code>改成<code>workSpaceDefaultJRE（java8）</code></p>
<p><strong>3</strong> 新建包并建WordCount类：</p>
<p><img src="https://img-blog.csdn.net/20171202192242066?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYW5zd2VyMTAwYW5zd2Vy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述"></p>
<p><strong>4</strong> pom.xml</p>
<pre><code class="prism language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>project</span> <span class="token attr-name">xmlns</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://maven.apache.org/POM/4.0.0<span class="token punctuation">"</span></span> <span class="token attr-name"><span class="token namespace">xmlns:</span>xsi</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.w3.org/2001/XMLSchema-instance<span class="token punctuation">"</span></span>
  <span class="token attr-name"><span class="token namespace">xsi:</span>schemaLocation</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>modelVersion</span><span class="token punctuation">&gt;</span></span>4.0.0<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>modelVersion</span><span class="token punctuation">&gt;</span></span>

  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>groupId</span><span class="token punctuation">&gt;</span></span>cn.whbing.spark<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>groupId</span><span class="token punctuation">&gt;</span></span>
  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>artifactId</span><span class="token punctuation">&gt;</span></span>SparkApps<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>artifactId</span><span class="token punctuation">&gt;</span></span>
  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>version</span><span class="token punctuation">&gt;</span></span>0.0.1-SNAPSHOT<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>version</span><span class="token punctuation">&gt;</span></span>
  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>packaging</span><span class="token punctuation">&gt;</span></span>jar<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>packaging</span><span class="token punctuation">&gt;</span></span>

  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>name</span><span class="token punctuation">&gt;</span></span>SparkApps<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>name</span><span class="token punctuation">&gt;</span></span>
  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>url</span><span class="token punctuation">&gt;</span></span>http://maven.apache.org<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>url</span><span class="token punctuation">&gt;</span></span>

  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>properties</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>project.build.sourceEncoding</span><span class="token punctuation">&gt;</span></span>UTF-8<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>project.build.sourceEncoding</span><span class="token punctuation">&gt;</span></span>
  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>properties</span><span class="token punctuation">&gt;</span></span>

  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>dependencies</span><span class="token punctuation">&gt;</span></span>
  
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>dependency</span><span class="token punctuation">&gt;</span></span>
      <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>groupId</span><span class="token punctuation">&gt;</span></span>junit<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>groupId</span><span class="token punctuation">&gt;</span></span>
      <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>artifactId</span><span class="token punctuation">&gt;</span></span>junit<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>artifactId</span><span class="token punctuation">&gt;</span></span>
      <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>version</span><span class="token punctuation">&gt;</span></span>3.8.1<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>version</span><span class="token punctuation">&gt;</span></span>
      <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>scope</span><span class="token punctuation">&gt;</span></span>test<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>scope</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>dependency</span><span class="token punctuation">&gt;</span></span>
    

<span class="token comment">&lt;!--2_11.2.1.2版本的FlatMapFunction的实现方法有区别--&gt;</span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>dependency</span><span class="token punctuation">&gt;</span></span>
	    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>groupId</span><span class="token punctuation">&gt;</span></span>org.apache.spark<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>groupId</span><span class="token punctuation">&gt;</span></span>
	    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>artifactId</span><span class="token punctuation">&gt;</span></span>spark-core_2.11<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>artifactId</span><span class="token punctuation">&gt;</span></span>
	    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>version</span><span class="token punctuation">&gt;</span></span>2.1.2<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>version</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>dependency</span><span class="token punctuation">&gt;</span></span>

<span class="token comment">&lt;!--  以下依赖暂时不需要
    &lt;dependency&gt;
      &lt;groupId&gt;org.apache.spark&lt;/groupId&gt;
      &lt;artifactId&gt;spark-sql_2.11&lt;/artifactId&gt;
      &lt;version&gt;2.1.2&lt;/version&gt;
    &lt;/dependency&gt;
    
    &lt;dependency&gt;
      &lt;groupId&gt;org.apache.spark&lt;/groupId&gt;
      &lt;artifactId&gt;spark-hive_2.11&lt;/artifactId&gt;
      &lt;version&gt;2.1.2&lt;/version&gt;
    &lt;/dependency&gt;
    
    &lt;dependency&gt;
      &lt;groupId&gt;org.apache.spark&lt;/groupId&gt;
      &lt;artifactId&gt;spark-streaming_2.11&lt;/artifactId&gt;
      &lt;version&gt;2.1.2&lt;/version&gt;
    &lt;/dependency&gt;
    
    &lt;dependency&gt;
      &lt;groupId&gt;org.apache.hadoop&lt;/groupId&gt;
      &lt;artifactId&gt;hadoop-client&lt;/artifactId&gt;
      &lt;version&gt;2.7.1&lt;/version&gt;
    &lt;/dependency&gt;  
    
	&lt;dependency&gt;
      &lt;groupId&gt;org.apache.spark&lt;/groupId&gt;
      &lt;artifactId&gt;spark-streaming-kafka_2.11&lt;/artifactId&gt;
      &lt;version&gt;1.6.3&lt;/version&gt;
    &lt;/dependency&gt;  

    &lt;dependency&gt;
      &lt;groupId&gt;org.apache.spark&lt;/groupId&gt;
      &lt;artifactId&gt;spark-graphx_2.11&lt;/artifactId&gt;
      &lt;version&gt;2.1.2&lt;/version&gt;
    &lt;/dependency&gt;
--&gt;</span>
  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>dependencies</span><span class="token punctuation">&gt;</span></span>
  
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>build</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>sourceDirectory</span><span class="token punctuation">&gt;</span></span>src/main/java<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>sourceDirectory</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>testSourceDirectory</span><span class="token punctuation">&gt;</span></span>src/test/java<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>testSourceDirectory</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>plugins</span><span class="token punctuation">&gt;</span></span>
      <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>plugin</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>artifactId</span><span class="token punctuation">&gt;</span></span>maven-assembly-plugin<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>artifactId</span><span class="token punctuation">&gt;</span></span>
        
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>configuration</span><span class="token punctuation">&gt;</span></span>
         <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>descriptorRefs</span><span class="token punctuation">&gt;</span></span>
         	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>descriptorRef</span><span class="token punctuation">&gt;</span></span>jar-with-dependencies<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>descriptorRef</span><span class="token punctuation">&gt;</span></span>
       	  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>descriptorRefs</span><span class="token punctuation">&gt;</span></span>
       	  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>archive</span><span class="token punctuation">&gt;</span></span>
       	  	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>manifest</span><span class="token punctuation">&gt;</span></span>
       	  		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>mainClass</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>mainClass</span><span class="token punctuation">&gt;</span></span>
       	  	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>manifest</span><span class="token punctuation">&gt;</span></span>
       	  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>archive</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>configuration</span><span class="token punctuation">&gt;</span></span>
        
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>executions</span><span class="token punctuation">&gt;</span></span>
          <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>execution</span><span class="token punctuation">&gt;</span></span>
          	 <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>id</span><span class="token punctuation">&gt;</span></span>make-assembly<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>id</span><span class="token punctuation">&gt;</span></span>
          	 <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>phase</span><span class="token punctuation">&gt;</span></span>package<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>phase</span><span class="token punctuation">&gt;</span></span>
            <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>goals</span><span class="token punctuation">&gt;</span></span>
              <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>goal</span><span class="token punctuation">&gt;</span></span>single<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>goal</span><span class="token punctuation">&gt;</span></span>              
            <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>goals</span><span class="token punctuation">&gt;</span></span>
          <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>execution</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>executions</span><span class="token punctuation">&gt;</span></span>        
      <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>plugin</span><span class="token punctuation">&gt;</span></span>
      <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>plugin</span><span class="token punctuation">&gt;</span></span>
		 <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>groupId</span><span class="token punctuation">&gt;</span></span>org.codehaus.mojo<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>groupId</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>artifactId</span><span class="token punctuation">&gt;</span></span>exec-maven-plugin<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>artifactId</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>version</span><span class="token punctuation">&gt;</span></span>1.2.1<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>version</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>executions</span><span class="token punctuation">&gt;</span></span>
          <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>execution</span><span class="token punctuation">&gt;</span></span>
            <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>goals</span><span class="token punctuation">&gt;</span></span>
              <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>goal</span><span class="token punctuation">&gt;</span></span>exec<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>goal</span><span class="token punctuation">&gt;</span></span>              
            <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>goals</span><span class="token punctuation">&gt;</span></span>
          <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>execution</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>executions</span><span class="token punctuation">&gt;</span></span> 
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>configuration</span><span class="token punctuation">&gt;</span></span>
          <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>executable</span><span class="token punctuation">&gt;</span></span>java<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>executable</span><span class="token punctuation">&gt;</span></span>
          <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>includeProjectDependencies</span><span class="token punctuation">&gt;</span></span>false<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>includeProjectDependencies</span><span class="token punctuation">&gt;</span></span>
          <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>includePluginDependencies</span><span class="token punctuation">&gt;</span></span>false<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>includePluginDependencies</span><span class="token punctuation">&gt;</span></span>
          <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>classpathScope</span><span class="token punctuation">&gt;</span></span>compile<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>classpathScope</span><span class="token punctuation">&gt;</span></span>
          <span class="token comment">&lt;!--  &lt;mainClass&gt;cn.whbing.spark.App&lt;/mainClass&gt;--&gt;</span>
          <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>mainClass</span><span class="token punctuation">&gt;</span></span>cn.whbing.spark.SparkApps.WordCount2<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>mainClass</span><span class="token punctuation">&gt;</span></span>
          
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>configuration</span><span class="token punctuation">&gt;</span></span>
      <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>plugin</span><span class="token punctuation">&gt;</span></span>
      
      
      <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>plugin</span><span class="token punctuation">&gt;</span></span>
      	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>groupId</span><span class="token punctuation">&gt;</span></span>org.apache.maven.plugins<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>groupId</span><span class="token punctuation">&gt;</span></span>
      	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>artifactId</span><span class="token punctuation">&gt;</span></span>maven-compiler-plugin<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>artifactId</span><span class="token punctuation">&gt;</span></span>
      	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>configuration</span><span class="token punctuation">&gt;</span></span>
      		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>source</span><span class="token punctuation">&gt;</span></span>1.8<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>source</span><span class="token punctuation">&gt;</span></span>
      		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>target</span><span class="token punctuation">&gt;</span></span>1.8<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>target</span><span class="token punctuation">&gt;</span></span>
      	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>configuration</span><span class="token punctuation">&gt;</span></span>
      <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>plugin</span><span class="token punctuation">&gt;</span></span>
      
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>plugins</span><span class="token punctuation">&gt;</span></span>
  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>build</span><span class="token punctuation">&gt;</span></span>
  
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>project</span><span class="token punctuation">&gt;</span></span>

</code></pre>
<p><strong>说明</strong>：<strong>（maven的一些问题及解决）</strong></p>
<p><strong>A）</strong> eclipse使用maven时，在<code>windows</code>=&gt;<code>preferences</code>中配置maven <code>setting.xml</code>及<code>repo</code>位置。**（这里指定的setting.xml可以不是maven conf中的xml，但是maven conf中的xml是CLI使用的xml）**下载依赖如使用第三方仓库可能会出问题，直接科学上网+自带中央仓库（注释掉mirror即可）即可。出现问题，请查看中央仓库是否有对应的版本。</p>
<p>**B）**如中途停止了下载，则不会再二次下载，需要到repo目录下删除对应的组件目录，再更新即可。</p>
<p>pom.xml如下（可能有很多组件第一个实验中未用到，但这里还是一并给出）</p>
<p>**C）**关于maven插件出问题，如mvn clean，assembly，compile等出问题，找到对应的文件夹<code>repo\org\apache\maven\plugins</code>删掉</p>
<p><strong>D）</strong> mvn打包跳过测试</p>
<pre><code>mvn package -Dmaven.test.skip=true   
</code></pre>
<br>
### <font color="red">1.1 spark官网 WordCount Example实验
---
</font><p><strong>5</strong> spark官网示例</p>
<p>url：<a href="http://spark.apache.org/examples.html" rel="nofollow">http://spark.apache.org/examples.html</a></p>
<p><img src="https://img-blog.csdn.net/20171205123556441?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYW5zd2VyMTAwYW5zd2Vy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述"></p>
<p>WordCountExample.java</p>
<pre><code class="prism language-java"><span class="token keyword">package</span> cn<span class="token punctuation">.</span>whbing<span class="token punctuation">.</span>spark<span class="token punctuation">.</span>SparkApps<span class="token punctuation">.</span>cores<span class="token punctuation">;</span>

<span class="token keyword">import</span> java<span class="token punctuation">.</span>util<span class="token punctuation">.</span>Arrays<span class="token punctuation">;</span>

<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>spark<span class="token punctuation">.</span>SparkConf<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>spark<span class="token punctuation">.</span>api<span class="token punctuation">.</span>java<span class="token punctuation">.</span>JavaPairRDD<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>spark<span class="token punctuation">.</span>api<span class="token punctuation">.</span>java<span class="token punctuation">.</span>JavaRDD<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>spark<span class="token punctuation">.</span>api<span class="token punctuation">.</span>java<span class="token punctuation">.</span>JavaSparkContext<span class="token punctuation">;</span>

<span class="token comment">/**
 * 官方示例版本
 */</span>
<span class="token keyword">import</span> scala<span class="token punctuation">.</span>Tuple2<span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">WordCountExample</span> <span class="token punctuation">{</span>
<span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span>String<span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>		
		 
		SparkConf conf <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">SparkConf</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">setAppName</span><span class="token punctuation">(</span><span class="token string">"Spark WordCount written by java!"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

		JavaSparkContext sc <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">JavaSparkContext</span><span class="token punctuation">(</span>conf<span class="token punctuation">)</span><span class="token punctuation">;</span>
		

		JavaRDD<span class="token generics function"><span class="token punctuation">&lt;</span>String<span class="token punctuation">&gt;</span></span> textFile <span class="token operator">=</span> sc<span class="token punctuation">.</span><span class="token function">textFile</span><span class="token punctuation">(</span><span class="token string">"hdfs:///whbing/HelloSpark.txt"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

		JavaPairRDD<span class="token generics function"><span class="token punctuation">&lt;</span>String<span class="token punctuation">,</span> Integer<span class="token punctuation">&gt;</span></span> counts <span class="token operator">=</span> textFile
			    <span class="token punctuation">.</span><span class="token function">flatMap</span><span class="token punctuation">(</span>s <span class="token operator">-</span><span class="token operator">&gt;</span> Arrays<span class="token punctuation">.</span><span class="token function">asList</span><span class="token punctuation">(</span>s<span class="token punctuation">.</span><span class="token function">split</span><span class="token punctuation">(</span><span class="token string">" "</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">iterator</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
			    <span class="token punctuation">.</span><span class="token function">mapToPair</span><span class="token punctuation">(</span>word <span class="token operator">-</span><span class="token operator">&gt;</span> <span class="token keyword">new</span> <span class="token class-name">Tuple2</span><span class="token operator">&lt;</span><span class="token operator">&gt;</span><span class="token punctuation">(</span>word<span class="token punctuation">,</span> <span class="token number">1</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
			    <span class="token punctuation">.</span><span class="token function">reduceByKey</span><span class="token punctuation">(</span><span class="token punctuation">(</span>a<span class="token punctuation">,</span> b<span class="token punctuation">)</span> <span class="token operator">-</span><span class="token operator">&gt;</span> a <span class="token operator">+</span> b<span class="token punctuation">)</span><span class="token punctuation">;</span>
		
		counts<span class="token punctuation">.</span><span class="token function">saveAsTextFile</span><span class="token punctuation">(</span><span class="token string">"hdfs:///home/whbing/HelloSpark2"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		sc<span class="token punctuation">.</span><span class="token function">close</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span>

</code></pre>
<p><strong>6</strong> mvn打包上传。<br>
<strong>7</strong>  运行脚本：</p>
<pre><code>	#5.运行java wordcount 官网Example示例，输出保存到文件
	./bin/spark-submit --class cn.whbing.spark.SparkApps.cores.WordCountExample --master spark://master-1a:7077 /home/whbing/SparkApps-0.0.1-SNAPSHOT.jar

</code></pre>
<p><strong>8</strong> hdfs中查看结果（50070端口界面）</p>
<p><img src="https://img-blog.csdn.net/20171205124311955?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYW5zd2VyMTAwYW5zd2Vy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述"><br>
<img src="https://img-blog.csdn.net/20171205124654362?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYW5zd2VyMTAwYW5zd2Vy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述"></p>
<br>
### <font color="red">1.2 其他版本
---
**9** WourdCount2.java 输入作为参数
</font><pre><code class="prism language-java"><span class="token keyword">package</span> cn<span class="token punctuation">.</span>whbing<span class="token punctuation">.</span>spark<span class="token punctuation">.</span>SparkApps<span class="token punctuation">.</span>cores<span class="token punctuation">;</span>

<span class="token keyword">import</span> java<span class="token punctuation">.</span>util<span class="token punctuation">.</span>Arrays<span class="token punctuation">;</span>
<span class="token keyword">import</span> java<span class="token punctuation">.</span>util<span class="token punctuation">.</span>Iterator<span class="token punctuation">;</span>
<span class="token keyword">import</span> java<span class="token punctuation">.</span>util<span class="token punctuation">.</span>List<span class="token punctuation">;</span>
<span class="token keyword">import</span> java<span class="token punctuation">.</span>util<span class="token punctuation">.</span>regex<span class="token punctuation">.</span>Pattern<span class="token punctuation">;</span>

<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>spark<span class="token punctuation">.</span>SparkConf<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>spark<span class="token punctuation">.</span>api<span class="token punctuation">.</span>java<span class="token punctuation">.</span>JavaPairRDD<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>spark<span class="token punctuation">.</span>api<span class="token punctuation">.</span>java<span class="token punctuation">.</span>JavaRDD<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>spark<span class="token punctuation">.</span>api<span class="token punctuation">.</span>java<span class="token punctuation">.</span>JavaSparkContext<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>spark<span class="token punctuation">.</span>api<span class="token punctuation">.</span>java<span class="token punctuation">.</span>function<span class="token punctuation">.</span>FlatMapFunction<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>spark<span class="token punctuation">.</span>api<span class="token punctuation">.</span>java<span class="token punctuation">.</span>function<span class="token punctuation">.</span>Function2<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>spark<span class="token punctuation">.</span>api<span class="token punctuation">.</span>java<span class="token punctuation">.</span>function<span class="token punctuation">.</span>PairFunction<span class="token punctuation">;</span>

<span class="token keyword">import</span> scala<span class="token punctuation">.</span>Tuple2<span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">final</span> <span class="token keyword">class</span> <span class="token class-name">WordCount2</span> <span class="token punctuation">{</span>
    <span class="token keyword">private</span> <span class="token keyword">static</span> <span class="token keyword">final</span> Pattern SPACE <span class="token operator">=</span> Pattern<span class="token punctuation">.</span><span class="token function">compile</span><span class="token punctuation">(</span><span class="token string">" "</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span>String<span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token keyword">throws</span> Exception <span class="token punctuation">{</span>
    	
    	<span class="token comment">/**
    	 * 待读取的文件以参数的形式输入
    	 */</span>
    	
        <span class="token keyword">if</span> <span class="token punctuation">(</span>args<span class="token punctuation">.</span>length <span class="token operator">&lt;</span> <span class="token number">1</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
            System<span class="token punctuation">.</span>err<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"Usage: JavaWordCount &lt;file&gt;"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            System<span class="token punctuation">.</span><span class="token function">exit</span><span class="token punctuation">(</span><span class="token number">1</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
        SparkConf conf <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">SparkConf</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">setAppName</span><span class="token punctuation">(</span><span class="token string">"JavaWordCount"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        JavaSparkContext sc <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">JavaSparkContext</span><span class="token punctuation">(</span>conf<span class="token punctuation">)</span><span class="token punctuation">;</span>
        JavaRDD<span class="token generics function"><span class="token punctuation">&lt;</span>String<span class="token punctuation">&gt;</span></span> lines <span class="token operator">=</span> sc<span class="token punctuation">.</span><span class="token function">textFile</span><span class="token punctuation">(</span>args<span class="token punctuation">[</span><span class="token number">0</span><span class="token punctuation">]</span><span class="token punctuation">,</span><span class="token number">1</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        JavaRDD<span class="token generics function"><span class="token punctuation">&lt;</span>String<span class="token punctuation">&gt;</span></span> words <span class="token operator">=</span> lines<span class="token punctuation">.</span><span class="token function">flatMap</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">FlatMapFunction</span><span class="token generics function"><span class="token punctuation">&lt;</span>String<span class="token punctuation">,</span> String<span class="token punctuation">&gt;</span></span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>

            <span class="token keyword">private</span> <span class="token keyword">static</span> <span class="token keyword">final</span> <span class="token keyword">long</span> serialVersionUID <span class="token operator">=</span> <span class="token number">1</span>L<span class="token punctuation">;</span>

            <span class="token annotation punctuation">@Override</span>
            <span class="token keyword">public</span> Iterator<span class="token generics function"><span class="token punctuation">&lt;</span>String<span class="token punctuation">&gt;</span></span> <span class="token function">call</span><span class="token punctuation">(</span>String s<span class="token punctuation">)</span> <span class="token punctuation">{</span>
                <span class="token keyword">return</span> Arrays<span class="token punctuation">.</span><span class="token function">asList</span><span class="token punctuation">(</span>SPACE<span class="token punctuation">.</span><span class="token function">split</span><span class="token punctuation">(</span>s<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">iterator</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span>
        <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        JavaPairRDD<span class="token generics function"><span class="token punctuation">&lt;</span>String<span class="token punctuation">,</span> Integer<span class="token punctuation">&gt;</span></span> ones <span class="token operator">=</span> words<span class="token punctuation">.</span><span class="token function">mapToPair</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">PairFunction</span><span class="token generics function"><span class="token punctuation">&lt;</span>String<span class="token punctuation">,</span> String<span class="token punctuation">,</span> Integer<span class="token punctuation">&gt;</span></span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>

            <span class="token keyword">private</span> <span class="token keyword">static</span> <span class="token keyword">final</span> <span class="token keyword">long</span> serialVersionUID <span class="token operator">=</span> <span class="token number">1</span>L<span class="token punctuation">;</span>

            <span class="token annotation punctuation">@Override</span>
            <span class="token keyword">public</span> Tuple2<span class="token generics function"><span class="token punctuation">&lt;</span>String<span class="token punctuation">,</span> Integer<span class="token punctuation">&gt;</span></span> <span class="token function">call</span><span class="token punctuation">(</span>String s<span class="token punctuation">)</span> <span class="token punctuation">{</span>
                <span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">Tuple2</span><span class="token generics function"><span class="token punctuation">&lt;</span>String<span class="token punctuation">,</span> Integer<span class="token punctuation">&gt;</span></span><span class="token punctuation">(</span>s<span class="token punctuation">,</span> <span class="token number">1</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span>
        <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        JavaPairRDD<span class="token generics function"><span class="token punctuation">&lt;</span>String<span class="token punctuation">,</span> Integer<span class="token punctuation">&gt;</span></span> counts <span class="token operator">=</span> ones<span class="token punctuation">.</span><span class="token function">reduceByKey</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">Function2</span><span class="token generics function"><span class="token punctuation">&lt;</span>Integer<span class="token punctuation">,</span> Integer<span class="token punctuation">,</span> Integer<span class="token punctuation">&gt;</span></span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>

            <span class="token keyword">private</span> <span class="token keyword">static</span> <span class="token keyword">final</span> <span class="token keyword">long</span> serialVersionUID <span class="token operator">=</span> <span class="token number">1</span>L<span class="token punctuation">;</span>

            <span class="token annotation punctuation">@Override</span>
            <span class="token keyword">public</span> Integer <span class="token function">call</span><span class="token punctuation">(</span>Integer i1<span class="token punctuation">,</span> Integer i2<span class="token punctuation">)</span> <span class="token punctuation">{</span>
                <span class="token keyword">return</span> i1 <span class="token operator">+</span> i2<span class="token punctuation">;</span>
            <span class="token punctuation">}</span>
        <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        List<span class="token operator">&lt;</span>Tuple2<span class="token generics function"><span class="token punctuation">&lt;</span>String<span class="token punctuation">,</span> Integer<span class="token punctuation">&gt;</span></span><span class="token operator">&gt;</span> output <span class="token operator">=</span> counts<span class="token punctuation">.</span><span class="token function">collect</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token keyword">for</span> <span class="token punctuation">(</span>Tuple2<span class="token operator">&lt;</span><span class="token operator">?</span><span class="token punctuation">,</span> <span class="token operator">?</span><span class="token operator">&gt;</span> tuple <span class="token operator">:</span> output<span class="token punctuation">)</span> <span class="token punctuation">{</span>
            System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span>tuple<span class="token punctuation">.</span><span class="token function">_1</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">+</span> <span class="token string">": "</span> <span class="token operator">+</span> tuple<span class="token punctuation">.</span><span class="token function">_2</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>

        sc<span class="token punctuation">.</span><span class="token function">stop</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>
<p><strong>10</strong> 打包 mvn clean package并上传。<br>
<strong>11</strong> 脚本：</p>
<pre><code>#4.运行java wordcount示例，输入文件以参数输入
./bin/spark-submit --class cn.whbing.spark.SparkApps.cores.WordCount2 --master spark://master-1a:7077 /home/whbing/SparkApps-0.0.1-SNAPSHOT.jar hdfs:///whbing/HelloSpark.txt
</code></pre>
<p><strong>12</strong> 打印结果</p>
<pre><code>... ...
17/12/05 21:19:04 INFO DAGScheduler: Job 0 finished: collect at WordCount2.java:64, took 1.549826 s
spark: 1
whut: 1
hello: 3
world: 1
17/12/05 21:19:04 INFO ServerConnector: Stopped Spark@2f63dff2{HTTP/1.1}{0.0.0.0:4040}
... ....
</code></pre>
<p>至此，实验完成。</p>
<p><strong>13</strong> 附：流程</p>
<p><strong><code>step1</code>:</strong> 创建spark的配置对象SparkConf，配置Saprk运行时的配置信息。<br>
setMaster设置Spark集群的master URL。若为"local"表本地，一般可不配，提交命令时选择。</p>
<p><strong><code>step2</code>:</strong> 创建SparkContext对象<br>
SparkContext是spark程序的唯一入口。无论采用scala，java，python，R都必须有一个sparkContext</p>
<p><code>SparkContext核心作用</code>：初始化Spark程序运行时核心组件，包括DAGScheduler、TeskScheduler、SchedulerBackend同时还会负责Spark程序往master中注册程序</p>
<p>SparkContext是Spark程序中最为重要的</p>
<p>**<code>step3:</code>**根据具体的数据来源（HDFD、HBase、Local FS、DB、S3等）通过SparkContext来创建RDD<br>
RDD的创建基本有三种方式：外部数据来源（如HDFS）、根据scala集合、由其他的RDD操作<br>
数据会被RDD划分为一系列的partitions，分配到威哥partition的数据属于一个task的处理范畴</p>
<p>**<code>step4:</code>**对初始的javaRDD进行transformation级别的处理，例如map，filter等高级函数的编程</p>
<p>**<code>step4.1：</code>**将每一行字符串拆分成单个的单词<br>
如果是scala，可以SAM转化，直接val word =line.flatMap{line=&gt;line.split(" ")}</p>
<p>**<code>step4.2:</code>**在单词拆分的基础上对每个单词实例计数为1，也就是word=&gt;(word,1)</p>
<p>**<code>step4.3:</code>**在每个单词实例计数为1的基础上统计每个单词在文本中出现的总次数</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>