---
layout:     post
title:      Hadoop: the definitive guide 第三版 拾遗 第四章 之CompressionCodec
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>第四章中提到了通过CompressionCodec对streams进行压缩和解压缩，并提供了示例程序：</p>
<h2>输入：标准输入流</h2>
<h2>输出：压缩后的标准输出流</h2>
<h2>原示例程序：</h2>
<p></p>
<pre><code class="language-java">// cc StreamCompressor A program to compress data read from standard input and write it to standard output
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.io.IOUtils;
import org.apache.hadoop.io.compress.CompressionCodec;
import org.apache.hadoop.io.compress.CompressionOutputStream;
import org.apache.hadoop.util.ReflectionUtils;

// vv StreamCompressor
public class StreamCompressor {

  public static void main(String[] args) throws Exception {
    String codecClassname = args[0];
    Class&lt;?&gt; codecClass = Class.forName(codecClassname);
    Configuration conf = new Configuration();
    CompressionCodec codec = (CompressionCodec)
      ReflectionUtils.newInstance(codecClass, conf);
    
    CompressionOutputStream out = codec.createOutputStream(System.out);
    IOUtils.copyBytes(System.in, out, 4096, false);
    out.finish();
  }
}
// ^^ StreamCompressor
</code></pre>
<br><p><span style="font-family:'WenQuanYi Micro Hei Mono', 'WenQuanYi Micro Hei', 'Microsoft Yahei Mono', 'Microsoft Yahei', sans-serif;font-size:14px;line-height:24px;text-indent:30px;">该实例程序通过<span style="font-family:'WenQuanYi Micro Hei Mono', 'WenQuanYi Micro Hei', 'Microsoft Yahei Mono', 'Microsoft Yahei', sans-serif;line-height:18px;">CompressionCodec的</span>createOutputStream(OutputStream
 out)方法获得CompressionOutputStream对象。</span></p>
<p><span style="font-family:'WenQuanYi Micro Hei Mono', 'WenQuanYi Micro Hei', 'Microsoft Yahei Mono', 'Microsoft Yahei', sans-serif;font-size:14px;line-height:24px;text-indent:30px;">第12行因参数固定,可以直接写成String codecClassname="org.apache.hadoop.io.compress.GzipCodec";</span></p>
<p><span style="font-family:'WenQuanYi Micro Hei Mono', 'WenQuanYi Micro Hei', 'Microsoft Yahei Mono', 'Microsoft Yahei', sans-serif;font-size:14px;line-height:24px;text-indent:30px;"><span style="font-family:'WenQuanYi Micro Hei Mono', 'WenQuanYi Micro Hei', 'Microsoft Yahei Mono', 'Microsoft Yahei', sans-serif;font-size:14px;line-height:24px;text-indent:30px;">即从命令行接受一个CompressionCodec实现类的参数，然后通过ReflectionUtils把实例化这个类，调用</span><span style="font-family:arial, '宋体', sans-serif;font-size:14px;line-height:24px;text-indent:30px;">CompressionCodec的接口方法对标准输出流进行封装，封装成一个压缩流，通过IOUtils类的copyBytes方法把标准输入流拷贝到压缩流中，最后调用CompressionCodec的finish方法，完成压缩。</span><br></span></p>
<p><span style="font-family:'WenQuanYi Micro Hei Mono', 'WenQuanYi Micro Hei', 'Microsoft Yahei Mono', 'Microsoft Yahei', sans-serif;font-size:14px;line-height:24px;text-indent:30px;"><span style="font-family:arial, '宋体', sans-serif;font-size:14px;line-height:24px;text-indent:30px;">在hadoop集群的hadoop根目录下使用如下命令验证该程序（通过linux的gunzip完成解压缩）：</span></span></p>
<p><span style="font-family:'WenQuanYi Micro Hei Mono', 'WenQuanYi Micro Hei', 'Microsoft Yahei Mono', 'Microsoft Yahei', sans-serif;font-size:14px;line-height:24px;text-indent:30px;"><span style="font-family:arial, '宋体', sans-serif;font-size:14px;line-height:24px;text-indent:30px;"></span></span></p>
<pre><code class="language-java">$echo "Hello world" |  hadoop jar xxxx.jar com.tht.hadoopIO.StreamCompressor  org.apache.hadoop.io.compress.GzipCodec | gunzip -</code></pre>
<p></p>
<p>下面对改程序做进一步修改：</p>
<h1>一、更改输出路径：即标准输入流压缩后的存放位置。</h1>
<h2>输入：标准输入流</h2>
<h2>输出：压缩后的文件存放到HDFS上</h2>
<h2>示例代码如下：</h2>
<p></p>
<pre><code class="language-java">package com.tht.hadoopIO;

import java.net.URI;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IOUtils;
import org.apache.hadoop.io.compress.CompressionCodec;
import org.apache.hadoop.io.compress.CompressionOutputStream;
import org.apache.hadoop.util.ReflectionUtils;

//vv StreamCompressor
public class StreamCompressor {

	public static void main(String[] args) throws Exception {
		String codecClassname = "org.apache.hadoop.io.compress.GzipCodec";
		String outputUri = "hdfs://master:9000/in/test.gz";
		
		Class&lt;?&gt; codecClass = Class.forName(codecClassname);
		Configuration conf = new Configuration();
		FileSystem fs = FileSystem.get(URI.create(outputUri), conf);
		
		CompressionCodec codec = (CompressionCodec) ReflectionUtils
				.newInstance(codecClass, conf);	</code></pre>
<pre><code class="language-java">		CompressionOutputStream out = codec.createOutputStream(fs.create(new Path(outputUri)));
		IOUtils.copyBytes(System.in, out, 4096, false);
		out.finish();
	}
}
// ^^ StreamCompressor</code></pre>
<br>
当然，在此路径outputUri：,,,/test.gz是指压缩后的文件存放位置和文件名及扩展名，如果改为...../test.txt.gz则指以.txt格式的压缩文件，后缀名是gz。
<h1>二、更改输入文件，即将输入文件路径定为HDFS上的文件。</h1>
<h2>输入：HDFS上存放文件</h2>
<p></p>
<h2>输出：压缩后的文件存放至HDFS上</h2>
<h2>示例代码如下：</h2>
<p></p>
<p></p>
<pre><code class="language-java">import java.io.InputStream;
import java.net.URI;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IOUtils;
import org.apache.hadoop.io.compress.CompressionCodec;
import org.apache.hadoop.io.compress.CompressionOutputStream;
import org.apache.hadoop.util.ReflectionUtils;

//vv StreamCompressor
public class StreamCompressor {

	public static void main(String[] args) throws Exception {
		String codecClassname = "org.apache.hadoop.io.compress.GzipCodec";
		String uri = "hdfs://master:9000/in/test.txt";
		String outputUri = "hdfs://master:9000/in/test.txt.gz";
		
		Class&lt;?&gt; codecClass = Class.forName(codecClassname);
		Configuration conf = new Configuration();
		FileSystem fs1 = FileSystem.get(URI.create(uri), conf);
		FileSystem fs2 = FileSystem.get(URI.create(outputUri), conf);
		
		CompressionCodec codec = (CompressionCodec) ReflectionUtils
				.newInstance(codecClass, conf);

		InputStream in =fs1.open(new Path(uri));	
		CompressionOutputStream out = codec.createOutputStream(fs2.create(new Path(outputUri)));
		
		IOUtils.copyBytes(in, out, 4096, false);
		in.close();
		out.close();
	}
}
// ^^ StreamCompressor</code></pre>
<br><p>当然了，输入输出都可以以参数形式存在。即String uri = arg[0];String outputUri =arg[1];则在执行时须加入两个路径参数。在windows eclipse下开发时会出现警告：</p>
<p></p>
<pre><code class="language-java">13/08/10 00:03:49 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable</code></pre>
<p></p>
<p>但是，只要在hadoop集群中配置好了hadoop本地库，在此就没有什么影响，照常压缩。</p>
<h1>解压缩：</h1>
<p></p>
<div style="font-family:'WenQuanYi Micro Hei Mono', 'WenQuanYi Micro Hei', 'Microsoft Yahei Mono', 'Microsoft Yahei', sans-serif;font-size:14px;line-height:26px;text-indent:30px;">
<span style="font-family:arial, '宋体', sans-serif;"><span style="font-family:'WenQuanYi Micro Hei Mono', 'WenQuanYi Micro Hei', 'Microsoft Yahei Mono', 'Microsoft Yahei', sans-serif !important;line-height:24px;">如果你想读取一个被压缩的文件的话，首先你得先通过扩展名判断该用哪种codec，可以查找压缩对应关系，这样做起来比较麻烦。</span></span></div>
<div style="font-family:'WenQuanYi Micro Hei Mono', 'WenQuanYi Micro Hei', 'Microsoft Yahei Mono', 'Microsoft Yahei', sans-serif;font-size:14px;line-height:26px;text-indent:30px;">
<span style="font-family:arial, '宋体', sans-serif;"><span style="font-family:'WenQuanYi Micro Hei Mono', 'WenQuanYi Micro Hei', 'Microsoft Yahei Mono', 'Microsoft Yahei', sans-serif !important;line-height:24px;">hadoop可以通过</span></span><a class="header" href="" rel="nofollow" style="line-height:24px;">CompressionCodecFactory</a><span style="line-height:24px;">.getCodec(</span><span style="line-height:24px;"></span><a class="header" href="" rel="nofollow" style="line-height:24px;">Path</a><span style="line-height:24px;">
 arg0)</span><span style="font-family:arial, '宋体', sans-serif;"><span style="font-family:'WenQuanYi Micro Hei Mono', 'WenQuanYi Micro Hei', 'Microsoft Yahei Mono', 'Microsoft Yahei', sans-serif !important;line-height:24px;">，传入一个Path即可获得相应得codec，示例代码如下：</span></span></div>
<div style="font-family:'WenQuanYi Micro Hei Mono', 'WenQuanYi Micro Hei', 'Microsoft Yahei Mono', 'Microsoft Yahei', sans-serif;font-size:14px;line-height:26px;text-indent:30px;">
<span style="font-family:arial, '宋体', sans-serif;"><span style="font-family:'WenQuanYi Micro Hei Mono', 'WenQuanYi Micro Hei', 'Microsoft Yahei Mono', 'Microsoft Yahei', sans-serif !important;line-height:24px;"></span></span>
<pre><code class="language-java">//cc FileDecompressor A program to decompress a compressed file using a codec inferred from the file's extension
import java.io.InputStream;
import java.io.OutputStream;
import java.net.URI;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IOUtils;
import org.apache.hadoop.io.compress.CompressionCodec;
import org.apache.hadoop.io.compress.CompressionCodecFactory;

//vv FileDecompressor
public class FileDecompressor {

	public static void main(String[] args) throws Exception {
//		String uri = args[0];
		String uri = "hdfs://master:9000/in/word.txt.gz";
		Configuration conf = new Configuration();
		FileSystem fs = FileSystem.get(URI.create(uri), conf);

		Path inputPath = new Path(uri);
		CompressionCodecFactory factory = new CompressionCodecFactory(conf);
		CompressionCodec codec = factory.getCodec(inputPath);
		if (codec == null) {
			System.err.println("No codec found for " + uri);
			System.exit(1);
		}

		String outputUri = CompressionCodecFactory.removeSuffix(uri,
				codec.getDefaultExtension());

		InputStream in = null;
		OutputStream out = null;
		try {
			in = codec.createInputStream(fs.open(inputPath));
			out = fs.create(new Path(outputUri));
			IOUtils.copyBytes(in, out, conf);
		} finally {
			IOUtils.closeStream(in);
			IOUtils.closeStream(out);
		}
	}
}
// ^^ FileDecompressor
</code></pre>
<h1>报警：</h1>
同样的，如果实在window下的eclipse中运行，将会报警（不影响程序正常运行）：</div>
<div style="font-family:'WenQuanYi Micro Hei Mono', 'WenQuanYi Micro Hei', 'Microsoft Yahei Mono', 'Microsoft Yahei', sans-serif;font-size:14px;line-height:26px;text-indent:30px;">
<span style="font-family:arial, '宋体', sans-serif;"><span style="font-family:'WenQuanYi Micro Hei Mono', 'WenQuanYi Micro Hei', 'Microsoft Yahei Mono', 'Microsoft Yahei', sans-serif !important;line-height:24px;"></span></span>
<pre><code class="language-java">13/08/10 00:09:37 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
13/08/10 00:09:37 WARN snappy.LoadSnappy: Snappy native library not loaded</code></pre>
<span style="color:#ff0000;">注：hadoop本地库不支持windows系统。</span></div>
<div style="font-family:'WenQuanYi Micro Hei Mono', 'WenQuanYi Micro Hei', 'Microsoft Yahei Mono', 'Microsoft Yahei', sans-serif;font-size:14px;line-height:26px;text-indent:30px;">
<span style="font-family:arial, '宋体', sans-serif;"><span style="font-family:'WenQuanYi Micro Hei Mono', 'WenQuanYi Micro Hei', 'Microsoft Yahei Mono', 'Microsoft Yahei', sans-serif !important;line-height:24px;"><br></span></span></div>
<div style="font-family:'WenQuanYi Micro Hei Mono', 'WenQuanYi Micro Hei', 'Microsoft Yahei Mono', 'Microsoft Yahei', sans-serif;font-size:14px;line-height:26px;text-indent:30px;">
<span style="font-family:arial, '宋体', sans-serif;"><span style="font-family:'WenQuanYi Micro Hei Mono', 'WenQuanYi Micro Hei', 'Microsoft Yahei Mono', 'Microsoft Yahei', sans-serif !important;line-height:24px;"><br></span></span></div>
<p></p>
<p><br></p>
            </div>
                </div>