---
layout:     post
title:      HDFS基本操作 javaApi
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p></p>
<p><strong><span style="color:rgb(0,0,255);">上传文件到</span><span style="color:rgb(0,0,255);">HDFS<span style="font-family:'宋体';">：（对</span><span style="font-family:Calibri;">HDFS</span><span style="font-family:'宋体';">的操作，注意</span><span style="font-family:Calibri;">config</span><span style="font-family:'宋体';">的配置）</span></span></strong></p>
<p><strong>import java.net.URI;</strong></p>
<p><strong>import org.apache.hadoop.conf.Configuration;</strong></p>
<p><strong>import org.apache.hadoop.fs.FileSystem;</strong></p>
<p><strong>import org.apache.hadoop.fs.Path;</strong></p>
<p><strong>public class trytry {</strong></p>
<p><strong>    public static void main(String[] args) {</strong></p>
<p><strong>        FileSystem hdfs = null;</strong></p>
<p><strong>        try {</strong></p>
<p><strong>            //<span style="font-family:'宋体';">目标文件习，在</span><span style="font-family:Calibri;">HDFS</span><span style="font-family:'宋体';">根目录下新建一个文件夹</span><span style="font-family:Calibri;">/hadoop/temp</span></strong></p>
<p><strong>            String destfile = "/hadoop/tmp/" + System.currentTimeMillis();</strong></p>
<p><strong>            //<span style="font-family:'宋体';">源文件</span></strong></p>
<p><strong>            String fsrc = "/usr/hahahah.txt";</strong></p>
<p><strong>            Configuration config = new Configuration();</strong></p>
<p><strong>            // <span style="font-family:'宋体';">程序配置</span></strong></p>
<p><strong>            config.set("fs.default.name", "hdfs://192.168.146.130:9000");</strong></p>
<p><strong>            hdfs = FileSystem</strong></p>
<p><strong>                    .get(new URI("hdfs://192.168.146.130:9000"), config, "hadoop");</strong></p>
<p><strong>            //hadoop<span style="font-family:'宋体';">是用户名</span></strong></p>
<p><strong>            Path srcPath = new Path(fsrc);</strong></p>
<p><strong>            Path destPath = new Path(destfile);</strong></p>
<p><strong>            boolean delSrc = true;</strong></p>
<p><strong>            hdfs.copyFromLocalFile(delSrc, srcPath, destPath);</strong></p>
<p><strong>            System.out.println("It is over : " + destfile);</strong></p>
<p><strong>        } catch (Exception e) {</strong></p>
<p><strong>            e.printStackTrace();</strong></p>
<p><strong>        } finally {</strong></p>
<p><strong>            if (hdfs != null) {</strong></p>
<p><strong>                try {</strong></p>
<p><strong>                    hdfs.closeAll();</strong></p>
<p><strong>                } catch (Exception e) {</strong></p>
<p><strong>                    e.printStackTrace();</strong></p>
<p><strong>                }</strong></p>
<p><strong>            }</strong></p>
<p><strong>        }</strong></p>
<p><strong>    }</strong></p>
<p><strong>}</strong></p>
<p><strong><span style="color:rgb(0,0,255);"><span style="font-family:'宋体';">在</span>HDFS<span style="font-family:'宋体';">之中创建文件</span></span></strong></p>
<p><strong>import java.net.URI;</strong></p>
<p><strong>import org.apache.hadoop.conf.Configuration;</strong></p>
<p><strong>import org.apache.hadoop.fs.FSDataOutputStream;</strong></p>
<p><strong>import org.apache.hadoop.fs.FileSystem;</strong></p>
<p><strong>import org.apache.hadoop.fs.Path;</strong></p>
<p><strong>public class CreatFile {</strong></p>
<p><strong>public static void main(String args[]) throws Exception{</strong></p>
<p><strong>Configuration cof=new Configuration();</strong></p>
<p><strong>byte[] buff="hello word!".getBytes();</strong></p>
<p><strong>FileSystem hdfs=null;</strong></p>
<p><strong>  cof.set("fs.default.name", "hdfs://192.168.146.130:9000");</strong></p>
<p><strong>         hdfs = FileSystem</strong></p>
<p><strong>                  .get(new URI("hdfs://192.168.146.130:9000"), cof, "hadoop");</strong></p>
<p><strong>Path dfs=new Path("/lilan1");</strong></p>
<p><strong>FSDataOutputStream outputStream=hdfs.create(dfs);</strong></p>
<p><strong>outputStream.write(buff, 0, buff.length);</strong></p>
<p><strong>System.out.println("cdhngegb");</strong></p>
<p><strong>}</strong></p>
<p><strong>}</strong></p>
<p><strong><span style="color:rgb(0,0,255);">重命名</span><span style="color:rgb(0,0,255);">HDFS<span style="font-family:'宋体';">文件的文件名：</span></span></strong></p>
<p><strong>import java.net.URI;</strong></p>
<p><strong> </strong></p>
<p><strong>import org.apache.hadoop.conf.Configuration;</strong></p>
<p><strong>import org.apache.hadoop.fs.FileSystem;</strong></p>
<p><strong>import org.apache.hadoop.fs.Path;</strong></p>
<p><strong>public class rename {</strong></p>
<p><strong>public static void main(String args[]) throws Exception{</strong></p>
<p><strong>Configuration cof=new Configuration();</strong></p>
<p><strong>FileSystem hdfs=null;</strong></p>
<p><strong>  cof.set("fs.default.name", "hdfs://192.168.146.130:9000");</strong></p>
<p><strong>          hdfs = FileSystem</strong></p>
<p><strong>                  .get(new URI("hdfs://192.168.146.130:9000"), cof, "hadoop1");</strong></p>
<p><strong>Path sname=new Path("/lilan1");</strong></p>
<p><strong>Path dname=new Path("/lilan2");</strong></p>
<p><strong>hdfs.rename(sname, dname);</strong></p>
<p><strong>}</strong></p>
<p><strong>}</strong></p>
<p><strong><span style="color:rgb(0,0,255);">查看</span><span style="color:rgb(0,0,255);">HDFS<span style="font-family:'宋体';">中的文件的最后修改时间：</span></span></strong></p>
<p><strong>import java.net.URI;</strong></p>
<p><strong>import org.apache.hadoop.conf.Configuration;</strong></p>
<p><strong>import org.apache.hadoop.fs.FileStatus;</strong></p>
<p><strong>import org.apache.hadoop.fs.FileSystem;</strong></p>
<p><strong>import org.apache.hadoop.fs.Path;</strong></p>
<p><strong>public class finalEditTime {</strong></p>
<p><strong>public static void main(String args[]) throws Exception{</strong></p>
<p><strong>Configuration cof=new Configuration();</strong></p>
<p><strong>FileSystem hdfs=null;</strong></p>
<p><strong>  cof.set("fs.default.name", "hdfs://192.168.146.130:9000");</strong></p>
<p><strong>          hdfs = FileSystem</strong></p>
<p><strong>                  .get(new URI("hdfs://192.168.146.130:9000"), cof, "hadoop1");</strong></p>
<p><strong>Path pa=new Path("/lilan2");</strong></p>
<p><strong>FileStatus filestatus=hdfs.getFileStatus(pa);</strong></p>
<p><strong>long time=filestatus.getModificationTime();</strong></p>
<p><strong>System.out.println("The last Edit time is:"+time);</strong></p>
<p><strong>}</strong></p>
<p><strong>}</strong></p>
<br>            </div>
                </div>