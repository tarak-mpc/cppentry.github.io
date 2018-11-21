---
layout:     post
title:      JSP访问Hadoop 图片存储服务
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
使用hadoop的hdfs来存放图片文件.以下是整个架构思路:
<p>  使用hadoop作为分布式文件系统，hadoop是一个<span style="font-size:10px;">实现了HDFS文件系统和MapReduce的开源项目，我们这里只是</span></p>
<p><span style="font-size:10px;">使用了它的hdfs.首先从web页面上上传的文件直接调用hadoop接口将图片文件存入hadoop系统中，hadoop可以设定备份</span></p>
<p><span style="font-size:10px;">数，这样在hadoop系统中某个datanode死掉并不会造成图片不可能，系统会从其他datanode上拿到数据。以下我们编写的一个hadoop的java的访问封装类:</span></p>
<p> </p>
<p>import java.io.File;<br>
import java.io.IOException;<br>
import java.io.InputStream;</p>
<p>import org.apache.hadoop.conf.Configuration;<br>
import org.apache.hadoop.fs.FSDataInputStream;<br>
import org.apache.hadoop.fs.FSDataOutputStream;<br>
import org.apache.hadoop.fs.FileSystem;<br>
import org.apache.hadoop.fs.FileUtil;<br>
import org.apache.hadoop.fs.Path;<br>
import org.apache.log4j.Logger;</p>
<p> </p>
<p>public class HadoopFileUtil {<br>
 static Logger logger = Logger.getLogger(HadoopFileUtil.class);<br>
 /**<br>
  * @param args<br>
  */<br>
 public static void main(String[] args) {<br>
  <br>
   String src=args[0];<br>
   String dst=args[1];<br>
   String tag=args[2];<br>
   HadoopFileUtil util=new HadoopFileUtil();<br>
   if(tag!=null&amp;&amp;tag.equals("1")){<br>
    System.out.println(util.createFile(src, dst));<br>
   }<br>
   else{<br>
    util.deleteFile(dst);<br>
   }</p>
<p> }</p>
<p> /**<br>
  * 拷贝一个本地文件到hadoop里面<br>
  * @param localFile 本地文件和路径名<br>
  * @param hadoopFile hadoop文件和路径名<br>
  * @return<br>
  */<br>
 public  boolean createFile(String localFile,String hadoopFile){<br>
  try {<br>
   Configuration conf=new Configuration();<br>
   FileSystem src=FileSystem.getLocal(conf);<br>
   FileSystem dst= FileSystem.get(conf);<br>
   Path srcpath = new Path(localFile);<br>
   Path dstpath = new Path(hadoopFile);<br>
   FileUtil.copy(src, srcpath, dst, dstpath,false,conf);<br>
  } catch (Exception e) {<br>
   e.printStackTrace();<br>
   return false;<br>
  }   </p>
<p>  return true;<br>
 }<br>
 <br>
 <br>
 /**将一个流作为输入，生成一个hadoop里面的文件<br>
  * @param inStream 输入流<br>
  * @param hadoopFile hadoop路径及文件名字<br>
  * @return<br>
  */<br>
 public boolean createFileByInputStream(InputStream inStream,String hadoopFile){<br>
  try {<br>
   Configuration conf=new Configuration();<br>
   FileSystem dst= FileSystem.get(conf);<br>
   Path dstpath = new Path(hadoopFile);<br>
   FSDataOutputStream oStream=dst.create(dstpath);<br>
   byte[] buffer = new byte[400];<br>
   int length = 0;<br>
   while((length = inStream.read(buffer))&gt;0){<br>
    oStream.write(buffer,0,length);<br>
   }<br>
   oStream.flush();<br>
   oStream.close();<br>
   inStream.close();<br>
  } catch (Exception e) {<br>
   e.printStackTrace();<br>
   return false;<br>
  }   <br>
  return true;<br>
 }<br>
 /**<br>
  * 删除hadoop里面的一个文件<br>
  * @param hadoopFile<br>
  * @return<br>
  */<br>
 public  boolean deleteFile(String hadoopFile){<br>
  try {<br>
   Configuration conf=new Configuration();<br>
   FileSystem dst= FileSystem.get(conf);<br>
   FileUtil.fullyDelete(dst,new Path(hadoopFile));<br>
  } catch (Exception e) {<br>
   e.printStackTrace();<br>
   return false;<br>
  }<br>
  <br>
  return true;<br>
 }<br>
 /**<br>
  * 从hadoop中读取一个文件流<br>
  * @param hadoopFile<br>
  * @return<br>
  */<br>
 public FSDataInputStream getInputStream(String hadoopFile){<br>
  FSDataInputStream iStream=null;<br>
  try {<br>
   Configuration conf=new Configuration();<br>
   FileSystem dst= FileSystem.get(conf);<br>
   Path p=new Path(hadoopFile);<br>
   iStream=dst.open(p);<br>
  } catch (Exception e) {<br>
   e.printStackTrace();<br>
   logger.error("getInputStream error:", e);<br>
  }<br>
  return iStream;<br>
 }</p>
<p>}<br>
通过调用这个类可以将图片存入hadoop 系统.</p>
<p>当需要访问某个图片时，先访问jsp服务器(如:tomcat)的一个servlet，这个servlet从hadoop里面读出图片，并</p>
<p>返回给浏览器.以下是我们的servlet:<br>
import java.io.IOException;<br>
import java.io.OutputStream;<br>
import java.io.PrintWriter;</p>
<p>import javax.servlet.ServletException;<br>
import javax.servlet.http.HttpServletRequest;<br>
import javax.servlet.http.HttpServletResponse;</p>
<p>import org.apache.hadoop.fs.FSDataInputStream;<br>
import org.apache.hadoop.io.IOUtils;<br>
import org.apache.log4j.Logger;</p>
<p>import com.tixa.dfs.hadoop.util.HadoopFileUtil;</p>
<p>public class HadoopServlet extends javax.servlet.http.HttpServlet implements javax.servlet.Servlet {<br>
 static Logger logger = Logger.getLogger(HadoopServlet.class);<br>
 <br>
 public void doGet(HttpServletRequest req, HttpServletResponse res) throws ServletException, IOException{</p>
<p>  PrintWriter out=res.getWriter();<br>
  res.setContentType("image/jpeg");<br>
  java.util.Date date = new java.util.Date();   <br>
  res.setDateHeader("Expires",date.getTime()+1000*60*60*24);   <br>
  String path=req.getPathInfo();<br>
  path=path.substring(1,path.length());<br>
  HadoopFileUtil hUtil=new HadoopFileUtil();<br>
  FSDataInputStream inputStream=hUtil.getInputStream(path);<br>
  OutputStream os = res.getOutputStream();<br>
 <br>
  byte[] buffer = new byte[400];<br>
  int length = 0;<br>
  while((length = inputStream.read(buffer))&gt;0){<br>
     os.write(buffer,0,length);<br>
  }<br>
  os.flush();<br>
  os.close();<br>
  inputStream.close();<br>
 }<br>
    <br>
}<br>
另外，为了避免对hadoop的频繁读取，可以再jsp服务器前放一个squid进行对图片的缓存。</p>
<p>这就是我们图片服务器的架构.</p>
<br>            </div>
                </div>