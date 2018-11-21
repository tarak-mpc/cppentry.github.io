---
layout:     post
title:      hbase rest 源码解析 对象与字符串的互转
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/lishuangzhe7047/article/details/78634570				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<pre><code>  最近用springcloud写了一个模拟的hbase rest server，供自动化测试使用，为了达到测试目的，hbase rest server除了跟原生的hbase rest的功能一样外，传入的参数，返回的结果对象，也都一样。
  hbase rest返回的结果都是字符串，在程序内部都是hbase相关的对象。第一步就是对象跟字符串的转换问题。
  原生的hbase rest支持两种字符串格式的转换：一种是json，一种是xml。
</code></pre>

<p>hbase rest源码中json字符串与对象的转换使用的是jackson，xml格式的字符串与对象的转化用的是JAXB。 <br>
我根据源码抽取了四个工具方法，分别如下：</p>

<ul>
<li><strong>对象转化为json字符串</strong></li>
</ul>

<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> String <span class="hljs-title">toJson</span>(Object object) {
        ObjectMapper mapper = <span class="hljs-keyword">new</span> JacksonJaxbJsonProvider().locateMapper(object.getClass(),
                MediaType.APPLICATION_JSON_TYPE);
        StringWriter writer = <span class="hljs-keyword">new</span> StringWriter();
        <span class="hljs-keyword">try</span> {
            mapper.writeValue(writer, object);
        } <span class="hljs-keyword">catch</span> (IOException e) {
            e.printStackTrace();
        }
        <span class="hljs-keyword">return</span> writer.toString();
    }</code></pre>

<ul>
<li><strong>json字符串转化为对象</strong></li>
</ul>

<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> &lt;T&gt; T <span class="hljs-title">fromJson</span>(String str, Class&lt;T&gt; c) {
        ObjectMapper mapper = <span class="hljs-keyword">new</span> JacksonJaxbJsonProvider().locateMapper(c,
                MediaType.APPLICATION_JSON_TYPE);
        T t = <span class="hljs-keyword">null</span>;
        <span class="hljs-keyword">try</span> {
            t = mapper.readValue(str, c);
        } <span class="hljs-keyword">catch</span> (IOException e) {
            <span class="hljs-keyword">throw</span> <span class="hljs-keyword">new</span> RuntimeException(e);
        }
        <span class="hljs-keyword">return</span> t;
    }</code></pre>

<ul>
<li><strong>对象转化为xml字符串</strong></li>
</ul>

<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> String <span class="hljs-title">toXML</span>(Object object) {
        <span class="hljs-keyword">try</span> {
            StringWriter writer = <span class="hljs-keyword">new</span> StringWriter();
            JAXBContext context = JAXBContext.newInstance(object.getClass());
            Marshaller marshal = context.createMarshaller();

            marshal.setProperty(Marshaller.JAXB_FORMATTED_OUTPUT, <span class="hljs-keyword">true</span>);
            marshal.setProperty(Marshaller.JAXB_ENCODING, <span class="hljs-string">"UTF-8"</span>);
            marshal.setProperty(Marshaller.JAXB_FRAGMENT, <span class="hljs-keyword">false</span>);
            marshal.setProperty(<span class="hljs-string">"jaxb.encoding"</span>, <span class="hljs-string">"utf-8"</span>);
            marshal.marshal(object, writer);

            <span class="hljs-keyword">return</span> <span class="hljs-keyword">new</span> String(writer.getBuffer());

        } <span class="hljs-keyword">catch</span> (Exception e) {
            e.printStackTrace();
            <span class="hljs-keyword">return</span> <span class="hljs-keyword">null</span>;
        }
    }</code></pre>

<ul>
<li><strong>xml字符串转化为对象</strong></li>
</ul>

<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> &lt;T&gt; T <span class="hljs-title">fromXML</span>(String xmlStr, Class&lt;T&gt; c) {
        <span class="hljs-keyword">try</span> {
            JAXBContext context = <span class="hljs-keyword">new</span> JAXBContextResolver().getContext(c);
            Unmarshaller unmarshaller = context.createUnmarshaller();
            T t = (T) unmarshaller.unmarshal(<span class="hljs-keyword">new</span> StringReader(xmlStr));
            <span class="hljs-keyword">return</span> t;
        } <span class="hljs-keyword">catch</span> (JAXBException e) {
            e.printStackTrace();
            <span class="hljs-keyword">return</span> <span class="hljs-keyword">null</span>;
        } <span class="hljs-keyword">catch</span> (Exception e) {
            e.printStackTrace();
            <span class="hljs-keyword">return</span> <span class="hljs-keyword">null</span>;
        }
    }</code></pre>

<p>添加的maven依赖为：</p>



<pre class="prettyprint"><code class="language-xml hljs "> <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.codehaus.jackson<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>jackson-mapper-asl<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.8.3<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.codehaus.jackson<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>jackson-jaxrs<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.8.3<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.codehaus.jackson<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>jackson-core-asl<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.8.3<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.codehaus.jackson<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>jackson-xc<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.8.3<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>javax.ws.rs<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>javax.ws.rs-api<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>2.0.1<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span></code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>