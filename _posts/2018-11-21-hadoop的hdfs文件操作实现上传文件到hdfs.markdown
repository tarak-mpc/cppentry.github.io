---
layout:     post
title:      hadoop的hdfs文件操作实现上传文件到hdfs
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="font-size:14px;font-family:Tahoma, Helvetica, Arial, '宋体', sans-serif;line-height:25.2000007629395px;background-color:rgb(247,252,255);">
hdfs文件操作操作示例，包括上传文件到HDFS上、从HDFS上下载文件和删除HDFS上的文件，大家参考使用吧<br><br></p>
<div class="codetitle" style="border-left-color:rgb(0,153,204);border-left-width:1px;border-left-style:solid;width:640px;clear:both;font-size:14px;border-top-color:rgb(0,153,204);border-top-width:1px;border-top-style:solid;border-right-color:rgb(0,153,204);border-right-width:1px;border-right-style:solid;line-height:25.2000007629395px;font-family:Tahoma, Helvetica, Arial, '宋体', sans-serif;background:rgb(242,246,251);">
<span style="line-height:25.2000007629395px;"><a class="copybut" id="copybut79481" style="color:rgb(51,51,51);"><u>复制代码</u></a></span>代码如下:</div>
<div class="codebody" id="code79481" style="border:1px solid rgb(0,153,204);width:638px;clear:both;font-size:14px;line-height:25.2000007629395px;font-family:Tahoma, Helvetica, Arial, '宋体', sans-serif;background:rgb(221,237,251);">
<br>
import org.apache.hadoop.conf.Configuration;<br>
import org.apache.hadoop.fs.*;
<p>
</p>
<p>
import java.io.File;<br>
import java.io.IOException;<br>
public class HadoopFile {<br>
    private Configuration conf =null;</p>
<p>
    public HadoopFile(){<br>
        conf =new Configuration();<br>
        conf.addResource(new Path("/hadoop/etc/hadoop/core-site.xml"));<br>
    }</p>
<p>
    public HadoopFile(Configuration conf){<br>
        this.conf =conf;<br>
    }</p>
<p>
    public boolean sendFile(String path,String localfile){<br>
        File file=new File(localfile);<br>
        if (!file.isFile()) {<br>
            System.out.println(file.getName());<br>
            return false;<br>
        }<br>
        try {<br>
            FileSystem localFS =FileSystem.getLocal(conf);<br>
            FileSystem hadoopFS =FileSystem.get(conf);<br>
            Path hadPath=new Path(path);</p>
<p>
            FSDataOutputStream fsOut=hadoopFS.create(new Path(path+"/"+file.getName()));<br>
            FSDataInputStream fsIn=localFS.open(new Path(localfile));<br>
            byte[] buf =new byte[1024];<br>
            int readbytes=0;<br>
            while ((readbytes=fsIn.read(buf))&gt;0){<br>
                fsOut.write(buf,0,readbytes);<br>
            }<br>
            fsIn.close();<br>
            fsOut.close();</p>
<p>
            FileStatus[] hadfiles= hadoopFS.listStatus(hadPath);<br>
            for(FileStatus fs :hadfiles){<br>
                System.out.println(fs.toString());<br>
            }<br>
            return true;<br>
        } catch (IOException e) {<br>
            e.printStackTrace();<br>
        }<br>
        return false;<br>
    }</p>
<p>
    public boolean delFile(String hadfile){<br>
        try {</p>
<p>
            FileSystem hadoopFS =FileSystem.get(conf);<br>
            Path hadPath=new Path(hadfile);<br>
            Path p=hadPath.getParent();<br>
            boolean rtnval= hadoopFS.delete(hadPath, true);</p>
<p>
            FileStatus[] hadfiles= hadoopFS.listStatus(p);<br>
            for(FileStatus fs :hadfiles){<br>
                System.out.println(fs.toString());<br>
            }<br>
            return rtnval;<br>
        } catch (IOException e) {<br>
            e.printStackTrace();<br>
        }<br>
        return false;<br>
    }</p>
<p>
<br>
    public boolean downloadFile(String hadfile,String localPath){</p>
<p>
        try {<br>
            FileSystem localFS =FileSystem.getLocal(conf);<br>
            FileSystem hadoopFS =FileSystem.get(conf);<br>
            Path hadPath=new Path(hadfile);</p>
<p>
            FSDataOutputStream fsOut=localFS.create(new Path(localPath+"/"+hadPath.getName()));<br>
            FSDataInputStream fsIn=hadoopFS.open(hadPath);<br>
            byte[] buf =new byte[1024];<br>
            int readbytes=0;<br>
            while ((readbytes=fsIn.read(buf))&gt;0){<br>
                fsOut.write(buf,0,readbytes);<br>
            }<br>
            fsIn.close();<br>
            fsOut.close();</p>
<p>
            return true;<br>
        } catch (IOException e) {<br>
            e.printStackTrace();<br>
        }<br>
        return false;<br>
    }<br>
}</p>
</div>
            </div>
                </div>