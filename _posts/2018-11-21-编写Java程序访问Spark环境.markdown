---
layout:     post
title:      编写Java程序访问Spark环境
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>Spark本身是使用scala语言编写的，但是Spark提供了访问Spark环境的Java API，使得可以从Java程序中访问Spark环境。</p>
<h2>准备环境</h2>
<p>在互联的5台机器上同时部署Spark环境和Hadoop环境，之所以部署Hadoop环境，是为了让Spark可以从HDFS加载数据。Spark的版本是为CDH4预编译的0.8.0，Hadoop的版本为CDH4（好像是CDH4.3）。</p>
<h2>创建Maven项目</h2>
<p>创建Maven项目，在POM文件中添加如下依赖：</p>
<pre><code class="language-html">&lt;dependencies&gt;
	&lt;dependency&gt;
		&lt;groupId&gt;org.apache.spark&lt;/groupId&gt;
		&lt;artifactId&gt;spark-core_2.9.3&lt;/artifactId&gt;
		&lt;version&gt;0.8.0-incubating&lt;/version&gt;
	&lt;/dependency&gt;
	&lt;dependency&gt;
		&lt;groupId&gt;org.apache.hadoop&lt;/groupId&gt;
		&lt;artifactId&gt;hadoop-client&lt;/artifactId&gt;
		&lt;version&gt;2.0.0-mr1-cdh4.2.0&lt;/version&gt;
	&lt;/dependency&gt;
&lt;/dependencies&gt;</code></pre>
<p></p>
<h2>编写Java程序</h2>
直接贴Demo程序的代码和注释。
<p></p>
<p></p>
<pre><code class="language-java">package xxx;

import java.util.HashMap;
import java.util.Map;

import org.apache.spark.api.java.JavaRDD;
import org.apache.spark.api.java.JavaSparkContext;

public class Demo {

	static final String USER = "wyc";

	public static void main(String[] args) throws Exception {
		System.setProperty("user.name", USER); // 设置访问Spark使用的用户名
		System.setProperty("HADOOP_USER_NAME", USER); // 设置访问Hadoop使用的用户名
		Map&lt;String, String&gt; envs = new HashMap&lt;String, String&gt;();
		envs.put("HADOOP_USER_NAME", USER); // 为Spark环境中服务于本App的各个Executor程序设置访问Hadoop使用的用户名

		System.setProperty("spark.executor.memory", "512m"); // 为Spark环境中服务于本App的各个Executor程序设置使用内存量的上限

		// 以下构造sc对象的构造方法各参数意义依次为：
		//   Spark Master的地址；
		//   App的名称；
		//   Spark Worker的部署位置；
		//   需要提供给本App的各个Executor程序下载的jar包的路径列表，这些jar包将出现在Executor程序的类路径中；
		//   传递给本App的各个Executor程序的环境信息。

		JavaSparkContext sc = new JavaSparkContext("spark://node01:7077", "Spark App 0", "/home/wyc/spark", new String[0], envs);

		String file = "hdfs://node01:8020/user/wyc/a.txt";
		JavaRDD&lt;String&gt; data = sc.textFile(file, 4).cache();
		System.out.println(data.count());
	}

}</code></pre><br><br><p></p>
<p></p>
            </div>
                </div>