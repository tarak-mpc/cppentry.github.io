---
layout:     post
title:      自定义Flume拦截器，并将收集的日志存储到Kafka中（案例）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/toto1297488504/article/details/73716124				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h3 id="1引入pom文件">1.引入POM文件</h3>

<p>如果想调用Flume，需要引入flume相关的jar包依赖，jar包依赖如下：</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-pi">&lt;?xml version="1.0" encoding="UTF-8"?&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">project</span> <span class="hljs-attribute">xmlns</span>=<span class="hljs-value">"http://maven.apache.org/POM/4.0.0"</span>
         <span class="hljs-attribute">xmlns:xsi</span>=<span class="hljs-value">"http://www.w3.org/2001/XMLSchema-instance"</span>
         <span class="hljs-attribute">xsi:schemaLocation</span>=<span class="hljs-value">"http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">parent</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>cn.com.toto.stormlogPro<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>stormlogPro<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.0-SNAPSHOT<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">parent</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">modelVersion</span>&gt;</span>4.0.0<span class="hljs-tag">&lt;/<span class="hljs-title">modelVersion</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>cn.com.toto.flume<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">dependencies</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.flume<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>flume-ng-core<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.6.0<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
            <span class="hljs-comment">&lt;!-- 设置打包的时候，剔除依赖--&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">scope</span>&gt;</span>provided<span class="hljs-tag">&lt;/<span class="hljs-title">scope</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>log4j<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>log4j<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.2.17<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">dependencies</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">build</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">plugins</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">plugin</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>maven-assembly-plugin<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
                    <span class="hljs-tag">&lt;<span class="hljs-title">descriptorRefs</span>&gt;</span>
                        <span class="hljs-tag">&lt;<span class="hljs-title">descriptorRef</span>&gt;</span>jar-with-dependencies<span class="hljs-tag">&lt;/<span class="hljs-title">descriptorRef</span>&gt;</span>
                    <span class="hljs-tag">&lt;/<span class="hljs-title">descriptorRefs</span>&gt;</span>
                    <span class="hljs-tag">&lt;<span class="hljs-title">archive</span>&gt;</span>
                        <span class="hljs-tag">&lt;<span class="hljs-title">manifest</span>&gt;</span>
                            <span class="hljs-tag">&lt;<span class="hljs-title">mainClass</span>&gt;</span>cn.com.toto.stromlogpro.log4j.LogInfoBuilder<span class="hljs-tag">&lt;/<span class="hljs-title">mainClass</span>&gt;</span>
                        <span class="hljs-tag">&lt;/<span class="hljs-title">manifest</span>&gt;</span>
                    <span class="hljs-tag">&lt;/<span class="hljs-title">archive</span>&gt;</span>
                <span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">executions</span>&gt;</span>
                    <span class="hljs-tag">&lt;<span class="hljs-title">execution</span>&gt;</span>
                        <span class="hljs-tag">&lt;<span class="hljs-title">id</span>&gt;</span>make-assembly<span class="hljs-tag">&lt;/<span class="hljs-title">id</span>&gt;</span>
                        <span class="hljs-tag">&lt;<span class="hljs-title">phase</span>&gt;</span>package<span class="hljs-tag">&lt;/<span class="hljs-title">phase</span>&gt;</span>
                        <span class="hljs-tag">&lt;<span class="hljs-title">goals</span>&gt;</span>
                            <span class="hljs-tag">&lt;<span class="hljs-title">goal</span>&gt;</span>single<span class="hljs-tag">&lt;/<span class="hljs-title">goal</span>&gt;</span>
                        <span class="hljs-tag">&lt;/<span class="hljs-title">goals</span>&gt;</span>
                    <span class="hljs-tag">&lt;/<span class="hljs-title">execution</span>&gt;</span>
                <span class="hljs-tag">&lt;/<span class="hljs-title">executions</span>&gt;</span>
            <span class="hljs-tag">&lt;/<span class="hljs-title">plugin</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">plugin</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.maven.plugins<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>maven-compiler-plugin<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
                    <span class="hljs-tag">&lt;<span class="hljs-title">source</span>&gt;</span>1.7<span class="hljs-tag">&lt;/<span class="hljs-title">source</span>&gt;</span>
                    <span class="hljs-tag">&lt;<span class="hljs-title">target</span>&gt;</span>1.7<span class="hljs-tag">&lt;/<span class="hljs-title">target</span>&gt;</span>
                <span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span>
            <span class="hljs-tag">&lt;/<span class="hljs-title">plugin</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">plugins</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">build</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">project</span>&gt;</span></code></pre>



<h3 id="2自定义的拦截器的代码">2.自定义的拦截器的代码</h3>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">package</span> cn.com.toto.stromlogpro.flume;

<span class="hljs-keyword">import</span> org.apache.commons.lang.StringUtils;
<span class="hljs-keyword">import</span> org.apache.flume.Context;
<span class="hljs-keyword">import</span> org.apache.flume.Event;
<span class="hljs-keyword">import</span> org.apache.flume.interceptor.Interceptor;

<span class="hljs-keyword">import</span> java.io.UnsupportedEncodingException;
<span class="hljs-keyword">import</span> java.util.ArrayList;
<span class="hljs-keyword">import</span> java.util.List;

<span class="hljs-javadoc">/**
 * 自定义一个点击流收集的拦截器
 * 
 * 1、实现一个Interceptor.Builder接口。
 * 2、Interceptor.Builder中有个configuref方法,通过configure获取配置文件中的相应key。
 * 3、Interceptor.Builder中有个builder方法，通过builder创建一个自定义的AppInterceptor
 * 4、AppInterceptor中有两个方法，一个是批处理，一个单条处理，将批处理的逻辑转换为单条处理
 * 5、需要在单条数据中添加 appid，由于appid是变量。需要在AppInterceptor的构造器中传入一些参数。
 * 6、为自定义的AppInterceptor创建有参构造器，将需要的参数传入进来。
 *
 *<span class="hljs-javadoctag"> @author</span> tuzq
 *<span class="hljs-javadoctag"> @create</span> 2017-06-25 12:48
 */</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">AppInterceptor</span> <span class="hljs-keyword">implements</span> <span class="hljs-title">Interceptor</span>{</span>
    <span class="hljs-comment">//4.定义成员变量appId,用来接收从配置文件中读取的信息</span>
    <span class="hljs-keyword">private</span> String appId;

    <span class="hljs-keyword">public</span> <span class="hljs-title">AppInterceptor</span>(String appId) {
        <span class="hljs-keyword">this</span>.appId = appId;
    }

    <span class="hljs-javadoc">/**
     * 单条数据进行处理,通过这个方式为日志添加上系统id
     *<span class="hljs-javadoctag"> @param</span> event
     *<span class="hljs-javadoctag"> @return</span>
     */</span>
    <span class="hljs-annotation">@Override</span>
    <span class="hljs-keyword">public</span> Event <span class="hljs-title">intercept</span>(Event event) {
        String message = <span class="hljs-keyword">null</span>;
        <span class="hljs-keyword">try</span> {
            message = <span class="hljs-keyword">new</span> String(event.getBody(), <span class="hljs-string">"utf-8"</span>);
        } <span class="hljs-keyword">catch</span> (UnsupportedEncodingException e) {
            message = <span class="hljs-keyword">new</span> String(event.getBody());
        }
        <span class="hljs-comment">//处理逻辑</span>
        <span class="hljs-keyword">if</span> (StringUtils.isNotBlank(message)) {
            message = <span class="hljs-string">"aid:"</span>+appId+<span class="hljs-string">"||msg:"</span> +message;
            event.setBody(message.getBytes());
            <span class="hljs-comment">//正常逻辑应该执行到这里</span>
            <span class="hljs-keyword">return</span> event;
        }
        <span class="hljs-keyword">return</span> event;
    }

    <span class="hljs-javadoc">/**
     * 批量数据进行处理
     *<span class="hljs-javadoctag"> @param</span> list
     *<span class="hljs-javadoctag"> @return</span>
     */</span>
    <span class="hljs-annotation">@Override</span>
    <span class="hljs-keyword">public</span> List&lt;Event&gt; <span class="hljs-title">intercept</span>(List&lt;Event&gt; list) {
        List&lt;Event&gt; resultList = <span class="hljs-keyword">new</span> ArrayList&lt;Event&gt;();
        <span class="hljs-keyword">for</span> (Event event : list) {
            Event r = intercept(event);
            <span class="hljs-keyword">if</span> (r != <span class="hljs-keyword">null</span>) {
                resultList.add(r);
            }
        }
        <span class="hljs-keyword">return</span> resultList;
    }

    <span class="hljs-annotation">@Override</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">initialize</span>() {

    }

    <span class="hljs-annotation">@Override</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">close</span>() {

    }

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span>  <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">AppInterceptorBuilder</span> <span class="hljs-keyword">implements</span> <span class="hljs-title">Interceptor</span>.<span class="hljs-title">Builder</span>{</span>
        <span class="hljs-comment">//1、获取配置文件的appId</span>
        <span class="hljs-keyword">private</span> String appId;

        <span class="hljs-annotation">@Override</span>
        <span class="hljs-keyword">public</span> Interceptor <span class="hljs-title">build</span>() {
            <span class="hljs-comment">//3、构造拦截器</span>
            <span class="hljs-keyword">return</span> <span class="hljs-keyword">new</span> AppInterceptor(appId);
        }

        <span class="hljs-annotation">@Override</span>
        <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">configure</span>(Context context) {
            <span class="hljs-comment">//2、当出现default之后，就是点击流告警系统</span>
            <span class="hljs-keyword">this</span>.appId =  context.getString(<span class="hljs-string">"appId"</span>,<span class="hljs-string">"default"</span>);
            System.out.println(<span class="hljs-string">"appId:"</span>+appId);
        }
    }
}
</code></pre>

<p>LogInfoBuilder的代码如下：</p>



<pre class="prettyprint"><code class=" hljs php">package cn.com.toto.stromlogpro.log4j;

import java.util.ArrayList;
import java.util.<span class="hljs-keyword">List</span>;
import java.util.Random;
import java.util.logging.Logger;

<span class="hljs-comment">/**
 * 通过这个工程模拟创建日志内容
 *
 *<span class="hljs-phpdoc"> @author</span> tuzq
 *<span class="hljs-phpdoc"> @create</span> 2017-06-25 13:51
 */</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">LogInfoBuilder</span> {</span>

    <span class="hljs-keyword">private</span> <span class="hljs-keyword">final</span> <span class="hljs-keyword">static</span> Logger logger = Logger.getLogger(<span class="hljs-string">"msg"</span>);

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> void main(String[] args) {
        Random random = <span class="hljs-keyword">new</span> Random();
        <span class="hljs-keyword">List</span>&lt;String&gt; <span class="hljs-keyword">list</span> = logInfoList();
        <span class="hljs-keyword">while</span>(<span class="hljs-keyword">true</span>) {
            logger.info(<span class="hljs-keyword">list</span>.get(random.nextInt(<span class="hljs-keyword">list</span>.size())));
        }
    }

    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">List</span>&lt;String&gt; logInfoList() {
        <span class="hljs-keyword">List</span> <span class="hljs-keyword">list</span> = <span class="hljs-keyword">new</span> ArrayList&lt;String&gt;();
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"aid:1||msg:error: Caused by: java.lang.NoClassDefFoundError: com/starit/gejie/dao/SysNameDao"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"java.sql.SQLException: You have an error in your SQL syntax;"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error Unable to connect to any of the specified MySQL hosts."</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error:Servlet.service() for servlet action threw exception java.lang.NullPointerException"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error:Exception in thread main java.lang.ArrayIndexOutOfBoundsException: 2"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error:NoSuchMethodError: com/starit/."</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error:java.lang.NoClassDefFoundError: org/coffeesweet/test01/Test01"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error:java.lang.NoClassDefFoundError: org/coffeesweet/test01/Test01"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error:Java.lang.IllegalStateException"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error:Java.lang.IllegalMonitorStateException"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error:Java.lang.NegativeArraySizeException"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error:java.sql.SQLException: You have an error in your SQL syntax;"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error:Java.lang.TypeNotPresentException "</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error:Java.lang.UnsupprotedOperationException "</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error Java.lang.IndexOutOfBoundsException"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error Java.lang.ClassNotFoundException"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error java.lang.ExceptionInInitializerError "</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error:java.lang.IncompatibleClassChangeError "</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error:java.lang.LinkageError "</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error:java.lang.OutOfMemoryError "</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error java.lang.StackOverflowError"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error: java.lang.UnsupportedClassVersionError"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error java.lang.ClassCastException"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error: java.lang.CloneNotSupportedException"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error: java.lang.EnumConstantNotPresentException "</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error java.lang.IllegalMonitorStateException "</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error java.lang.IllegalStateException "</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error java.lang.IndexOutOfBoundsException "</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error java.lang.NumberFormatException "</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error java.lang.RuntimeException "</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error java.lang.TypeNotPresentException "</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error MetaSpout.java:9: variable i might not have been initialized"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error MyEvaluator.java:1: class Test1 is public, should be declared in a file named Test1.java "</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error Main.java:5: cannot find symbol "</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error NoClassDefFoundError: asa wrong name: ASA "</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error Test1.java:54: 'void' type not allowed here"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error Test5.java:8: missing return statement"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error:Next.java:66: cannot find symbol "</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error symbol  : method createTempFile(java.lang.String,java.lang.String,java.lang.String) "</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error invalid method declaration; return type required"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error array required, but java.lang.String found"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error Exception in thread main java.lang.NumberFormatException: null 20. ."</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error non-static method cannot be referenced from a static context"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error Main.java:5: non-static method fun1() cannot be referenced from a static context"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error continue outside of  loop"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error MyAbstract.java:6: missing method body, or declare abstract"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error Main.java:6: Myabstract is abstract; cannot be instantiated"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error MyInterface.java:2: interface methods cannot have body "</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error Myabstract is abstract; cannot be instantiated"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error asa.java:3: modifier static not allowed here"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error possible loss of precision  found: long required:byte  var=varlong"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error  java.lang.NegativeArraySizeException "</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error java.lang.ArithmeticException:  by zero"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error java.lang.ArithmeticException"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error java.lang.ArrayIndexOutOfBoundsException"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error java.lang.ClassNotFoundException"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error java.lang.IllegalArgumentException"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error fatal error C1010: unexpected end of file while looking for precompiled header directive"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error fatal error C1083: Cannot open include file: R…….h: No such file or directory"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error C2011:C……clas type redefinition"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error C2018: unknown character 0xa3"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error C2057: expected constant expression"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error C2065: IDD_MYDIALOG : undeclared identifier IDD_MYDIALOG"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error C2082: redefinition of formal parameter bReset"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error C2143: syntax error: missing : before  "</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error C2146: syntax error : missing ';' before identifier dc"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error C2196: case value '69' already used"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error C2509: 'OnTimer' : member function not declared in 'CHelloView'"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error C2555: 'B::f1': overriding virtual function differs from 'A::f1' only by return type or calling convention"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error C2511: 'reset': overloaded member function 'void (int)' not found in 'B'"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error C2660: 'SetTimer' : function does not take 2 parameters"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error warning C4035: 'f……': no return value"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error warning C4553: '= =' : operator has no effect; did you intend '='"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error C4716: 'CMyApp::InitInstance' : must return a value"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error LINK : fatal error LNK1168: cannot open Debug/P1.exe for writing"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error LNK2001: unresolved external symbol public: virtual _ _thiscall C (void)"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error java.lang.IllegalArgumentException: Path index.jsp does not start with"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error org.apache.struts.action.ActionServlet.process(ActionServlet.java:148"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error org.apache.jasper.JasperException: Exception in JSP"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error The server encountered an internal error () that prevented it from fulfilling this request"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error org.apache.jasper.servlet.JspServletWrapper.handleJspException(JspServletWrapper.java:467"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error javax.servlet.http.HttpServlet.service(HttpServlet.java:803)"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error javax.servlet.jsp.JspException: Cannot find message resources under key org.apache.struts.action.MESSAGE"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error Stacktrace:  org.apache.jasper.servlet.JspServletWrapper.handleJspException(JspServletWrapper.java:467)"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error javax.servlet.ServletException: Cannot find bean org.apache.struts.taglib.html.BEAN in any scope"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error no data found"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error exception in thread main org.hibernate.MappingException: Unknown entity:."</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error using namespace std;"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error C2065: 'cout' : undeclared identifier"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error main already defined in aaa.obj"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error syntax error : missing ';' before '}'"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error cout : undeclared identifier"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error weblogic.servlet.internal.WebAppServletContext$ServletInvocationAction.run(WebAp "</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error Caused by: java.lang.reflect.InvocationTargetException"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error Caused by: java.lang.NoClassDefFoundError: com/starit/gejie/dao/SysNameDao"</span>);
        <span class="hljs-keyword">list</span>.add(<span class="hljs-string">"error at com.starit.gejie.Util.Trans.BL_getSysNamesByType(Trans.java:220)"</span>);
        <span class="hljs-keyword">return</span> <span class="hljs-keyword">list</span>;
    }
}
</code></pre>

<p><strong>MyDailyRollingFileAppender的代码如下：</strong></p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">package</span> cn.com.toto.stromlogpro.log4j;<span class="hljs-javadoc">/**
 * Created by toto on 2017/6/25.
 */</span>

<span class="hljs-keyword">import</span> org.apache.log4j.DailyRollingFileAppender;
<span class="hljs-keyword">import</span> org.apache.log4j.Priority;

<span class="hljs-javadoc">/**
 *<span class="hljs-javadoctag"> @author</span> tuzq
 *<span class="hljs-javadoctag"> @create</span> 2017-06-25 13:58
 */</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">MyDailyRollingFileAppender</span> <span class="hljs-keyword">extends</span> <span class="hljs-title">DailyRollingFileAppender</span> {</span>

    <span class="hljs-annotation">@Override</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">boolean</span> <span class="hljs-title">isAsSevereAsThreshold</span>(Priority priority) {
        <span class="hljs-keyword">return</span> getThreshold().equals(priority);
    }
}
</code></pre>

<p><strong>MyRollingFileAppender的代码如下：</strong></p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">package</span> cn.com.toto.stromlogpro.log4j;<span class="hljs-javadoc">/**
 * Created by toto on 2017/6/25.
 */</span>

<span class="hljs-keyword">import</span> org.apache.log4j.Priority;
<span class="hljs-keyword">import</span> org.apache.log4j.RollingFileAppender;

<span class="hljs-javadoc">/**
 *<span class="hljs-javadoctag"> @author</span> tuzq
 *<span class="hljs-javadoctag"> @create</span> 2017-06-25 14:01
 */</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">MyRollingFileAppender</span> <span class="hljs-keyword">extends</span> <span class="hljs-title">RollingFileAppender</span> {</span>

    <span class="hljs-annotation">@Override</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">boolean</span> <span class="hljs-title">isAsSevereAsThreshold</span>(Priority priority) {
        <span class="hljs-keyword">return</span> getThreshold().equals(priority);
    }
}
</code></pre>



<h3 id="3在flume中的conf配置文件并将收集的日志下沉到kafka中">3.在Flume中的conf配置文件,并将收集的日志下沉到kafka中</h3>

<pre class="prettyprint"><code class=" hljs avrasm">a1<span class="hljs-preprocessor">.sources</span> = <span class="hljs-built_in">r1</span>
a1<span class="hljs-preprocessor">.channels</span> = c1
a1<span class="hljs-preprocessor">.sinks</span> = k1

a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.type</span> = exec
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.command</span> = tail -F /export/data/flume_sources/click_log/info<span class="hljs-preprocessor">.log</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.channels</span> = c1
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.interceptors</span> = i1
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.interceptors</span><span class="hljs-preprocessor">.i</span>1<span class="hljs-preprocessor">.type</span> = cn<span class="hljs-preprocessor">.com</span><span class="hljs-preprocessor">.toto</span><span class="hljs-preprocessor">.stromlogpro</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.AppInterceptor</span>$AppInterceptorBuilder
<span class="hljs-preprocessor">#通过这个参数向自定义的Flume拦截器中传递参数（即系统编号）</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.interceptors</span><span class="hljs-preprocessor">.i</span>1<span class="hljs-preprocessor">.appId</span> = <span class="hljs-number">1</span>

a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.type</span>=memory
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.capacity</span>=<span class="hljs-number">10000</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.transactionCapacity</span>=<span class="hljs-number">100</span>

a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.type</span> = org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.KafkaSink</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.topic</span> = log_monitor
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.brokerList</span> = hadoop1:<span class="hljs-number">9092</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.requiredAcks</span> = <span class="hljs-number">1</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.batchSize</span> = <span class="hljs-number">20</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.channel</span> = c1</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>