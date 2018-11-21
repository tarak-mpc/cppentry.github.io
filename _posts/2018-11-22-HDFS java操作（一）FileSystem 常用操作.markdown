---
layout:     post
title:      HDFS java操作（一）FileSystem 常用操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载					https://blog.csdn.net/T1DMzks/article/details/72758324				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="简介"><strong>简介:</strong></h1>

<p>本文主要讲解如何用java去操作hdfs，以下是我整理的常用的一些方法，本文主要介绍的是FileSystem，我把其集合到了一个工具类当中，下面的操作主要有 检查文件是否存在，创建文件，创建文件夹，复制（上传）本地文件到hdfs指定目录，复制（上传）本地文件夹到hdfs指定目录，从hdfs下载文件，移动hdfs上的文件或者文件夹，删除文件或者文件夹，HDFS 到 HDFS 的合并，列出所有DataNode的名字信息，检测是否是备用节点等操作 <br>
<img src="http://cdn.data-flair.training/blogs/wp-content/uploads/HDFS-Commands.jpg" alt="image" title=""></p>



<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-keyword">package</span> hdfs.tutorial;

<span class="hljs-keyword">import</span> org.apache.hadoop.conf.Configuration;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.*;
<span class="hljs-keyword">import</span> org.apache.hadoop.hdfs.DistributedFileSystem;
<span class="hljs-keyword">import</span> org.apache.hadoop.hdfs.protocol.DatanodeInfo;

<span class="hljs-keyword">import</span> java.io.File;
<span class="hljs-keyword">import</span> java.io.FileInputStream;
<span class="hljs-keyword">import</span> java.io.FileOutputStream;
<span class="hljs-keyword">import</span> java.io.IOException;
<span class="hljs-keyword">import</span> java.net.URI;
<span class="hljs-keyword">import</span> java.net.URISyntaxException;
<span class="hljs-keyword">import</span> java.util.Date;
<span class="hljs-keyword">import</span> java.util.List;

<span class="hljs-javadoc">/**
 * Created by Administrator on 2017/5/25.
 */</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">HdfsOper</span> {</span>
    <span class="hljs-keyword">private</span> FileSystem hdfs;

    <span class="hljs-javadoc">/**
     *<span class="hljs-javadoctag"> @return</span> 得到hdfs的连接 FileSystem类
     *<span class="hljs-javadoctag"> @throws</span> URISyntaxException
     *<span class="hljs-javadoctag"> @throws</span> IOException
     *<span class="hljs-javadoctag"> @throws</span> InterruptedException
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> FileSystem <span class="hljs-title">getFileSystem</span>() <span class="hljs-keyword">throws</span> URISyntaxException, IOException, InterruptedException {
        <span class="hljs-comment">//获取FileSystem类的方法有很多种，这里只写一种</span>
        Configuration config = <span class="hljs-keyword">new</span> Configuration();
        URI uri = <span class="hljs-keyword">new</span> URI(<span class="hljs-string">"hdfs://192.xxx.x.xxx:xxx"</span>);
        <span class="hljs-keyword">return</span> FileSystem.get(uri,config,<span class="hljs-string">"your user"</span>);<span class="hljs-comment">// 第一位为uri，第二位为config，第三位是登录的用户</span>
    }

    <span class="hljs-javadoc">/**
     * 检查文件或者文件夹是否存在
     *<span class="hljs-javadoctag"> @param</span> filename
     *<span class="hljs-javadoctag"> @return</span>
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">boolean</span> <span class="hljs-title">checkFileExist</span>(String filename)
    {
        <span class="hljs-keyword">try</span>
        {
            Path f = <span class="hljs-keyword">new</span> Path(filename);
            <span class="hljs-keyword">return</span> hdfs.exists(f);
        }
        <span class="hljs-keyword">catch</span> (Exception e)
        {
            e.printStackTrace();
        }
        <span class="hljs-keyword">return</span> <span class="hljs-keyword">false</span>;
    }

    <span class="hljs-javadoc">/**
     * 创建文件夹
     *<span class="hljs-javadoctag"> @param</span> dirName
     *<span class="hljs-javadoctag"> @return</span>
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">boolean</span> <span class="hljs-title">mkdir</span>(String dirName)
    {
        <span class="hljs-keyword">if</span> (checkFileExist(dirName))
            <span class="hljs-keyword">return</span> <span class="hljs-keyword">true</span>;
        <span class="hljs-keyword">try</span>
        {
            Path f = <span class="hljs-keyword">new</span> Path(dirName);
            System.out.println(<span class="hljs-string">"Create and Write :"</span> + f.getName() + <span class="hljs-string">" to hdfs"</span>);
            <span class="hljs-keyword">return</span> hdfs.mkdirs(f);
        }
        <span class="hljs-keyword">catch</span> (Exception e)
        {
            e.printStackTrace();
        }

        <span class="hljs-keyword">return</span> <span class="hljs-keyword">false</span>;
    }

    <span class="hljs-javadoc">/**
     * 创建一个空文件
     *<span class="hljs-javadoctag"> @param</span> filePath  文件的完整路径名称
     *<span class="hljs-javadoctag"> @return</span>
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">boolean</span> <span class="hljs-title">mkfile</span>(String filePath)
    {
        <span class="hljs-keyword">try</span>
        {
            Path f = <span class="hljs-keyword">new</span> Path(filePath);
            FSDataOutputStream os = hdfs.create(f, <span class="hljs-keyword">true</span>);
            os.close();
            <span class="hljs-keyword">return</span> <span class="hljs-keyword">true</span>;
        }
        <span class="hljs-keyword">catch</span> (IllegalArgumentException e)
        {
            e.printStackTrace();
        }
        <span class="hljs-keyword">catch</span> (IOException e)
        {
            e.printStackTrace();
        }
        <span class="hljs-keyword">return</span> <span class="hljs-keyword">false</span>;
    }

    <span class="hljs-javadoc">/**
     * 复制文件到指定目录
     *<span class="hljs-javadoctag"> @param</span> srcfile  复制的文件路径
     *<span class="hljs-javadoctag"> @param</span> desfile  粘贴的路径
     *<span class="hljs-javadoctag"> @return</span>
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">boolean</span> <span class="hljs-title">hdfsCopyUtils</span>(String srcfile, String desfile) {
        Configuration conf = <span class="hljs-keyword">new</span> Configuration();
        Path src = <span class="hljs-keyword">new</span> Path(srcfile);
        Path dst = <span class="hljs-keyword">new</span> Path(desfile);
        <span class="hljs-keyword">try</span> {
            FileUtil.copy(src.getFileSystem(conf), src,
                    dst.getFileSystem(conf), dst, <span class="hljs-keyword">false</span>, conf);
        } <span class="hljs-keyword">catch</span> (IOException e) {
            <span class="hljs-keyword">return</span> <span class="hljs-keyword">false</span>;
        }
        <span class="hljs-keyword">return</span> <span class="hljs-keyword">true</span>;
    }

    <span class="hljs-javadoc">/**
     * 移动文件或者文件夹
     *<span class="hljs-javadoctag"> @param</span> src  初始路径
     *<span class="hljs-javadoctag"> @param</span> dst   移动结束路径
     *<span class="hljs-javadoctag"> @throws</span> Exception
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">movefile</span>(String src, String dst) <span class="hljs-keyword">throws</span> Exception
    {
        Path p1 = <span class="hljs-keyword">new</span> Path(src);
        Path p2 = <span class="hljs-keyword">new</span> Path(dst);
        hdfs.rename(p1, p2);
    }

    <span class="hljs-javadoc">/**
     * 删除文件或者文件夹
     *<span class="hljs-javadoctag"> @param</span> src
     *<span class="hljs-javadoctag"> @throws</span> Exception
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">delete</span>(String src) <span class="hljs-keyword">throws</span> Exception
    {
        Path p1 = <span class="hljs-keyword">new</span> Path(src);
        <span class="hljs-keyword">if</span> (hdfs.isDirectory(p1))
        {
            hdfs.delete(p1, <span class="hljs-keyword">true</span>);
            System.out.println(<span class="hljs-string">"删除文件夹成功: "</span> + src);
        }
        <span class="hljs-keyword">else</span> <span class="hljs-keyword">if</span> (hdfs.isFile(p1))
        {
            hdfs.delete(p1, <span class="hljs-keyword">false</span>);
            System.out.println(<span class="hljs-string">"删除文件成功: "</span> + src);
        }
    }

    <span class="hljs-javadoc">/**
     * 读取本地文件到HDFS系统, 保证文件格式是utf-8
     *<span class="hljs-javadoctag"> @param</span> localFilename
     *<span class="hljs-javadoctag"> @param</span> hdfsPath
     *<span class="hljs-javadoctag"> @return</span>
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">boolean</span> <span class="hljs-title">copyLocalFileToHDFS</span>(String localFilename, String hdfsPath)
    {
        <span class="hljs-keyword">try</span>
        {
            <span class="hljs-comment">// 如果路径不存在就创建文件夹</span>
            mkdir(hdfsPath);

            File file = <span class="hljs-keyword">new</span> File(localFilename);
            FileInputStream is = <span class="hljs-keyword">new</span> FileInputStream(file);

            <span class="hljs-comment">// 如果hdfs上已经存在文件，那么先删除该文件</span>
            <span class="hljs-keyword">if</span> (<span class="hljs-keyword">this</span>.checkFileExist(hdfsPath + <span class="hljs-string">"/"</span> + file.getName()))
            {
                delete(hdfsPath + <span class="hljs-string">"/"</span> + file.getName());
            }

            Path f = <span class="hljs-keyword">new</span> Path(hdfsPath + <span class="hljs-string">"/"</span> + file.getName());

            FSDataOutputStream os = hdfs.create(f, <span class="hljs-keyword">true</span>);
            <span class="hljs-keyword">byte</span>[] buffer = <span class="hljs-keyword">new</span> <span class="hljs-keyword">byte</span>[<span class="hljs-number">10240000</span>];
            <span class="hljs-keyword">int</span> nCount = <span class="hljs-number">0</span>;

            <span class="hljs-keyword">while</span> (<span class="hljs-keyword">true</span>)
            {
                <span class="hljs-keyword">int</span> bytesRead = is.read(buffer);
                <span class="hljs-keyword">if</span> (bytesRead &lt;= <span class="hljs-number">0</span>)
                {
                    <span class="hljs-keyword">break</span>;
                }

                os.write(buffer, <span class="hljs-number">0</span>, bytesRead);
                nCount++;
                <span class="hljs-keyword">if</span> (nCount % (<span class="hljs-number">100</span>) == <span class="hljs-number">0</span>)
                    System.out.println((<span class="hljs-keyword">new</span> Date()).toLocaleString() + <span class="hljs-string">": Have move "</span> + nCount + <span class="hljs-string">" blocks"</span>);
            }

            is.close();
            os.close();
            System.out.println((<span class="hljs-keyword">new</span> Date()).toLocaleString() + <span class="hljs-string">": Write content of file "</span> + file.getName()
                    + <span class="hljs-string">" to hdfs file "</span> + f.getName() + <span class="hljs-string">" success"</span>);
            <span class="hljs-keyword">return</span> <span class="hljs-keyword">true</span>;
        }
        <span class="hljs-keyword">catch</span> (Exception e)
        {
            e.printStackTrace();
        }
        <span class="hljs-keyword">return</span> <span class="hljs-keyword">false</span>;
    }

    <span class="hljs-javadoc">/**
     * 复制本地文件夹到hdfs的文件
     *<span class="hljs-javadoctag"> @param</span> localPath
     *<span class="hljs-javadoctag"> @param</span> hdfsPath
     *<span class="hljs-javadoctag"> @return</span>
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">boolean</span> <span class="hljs-title">CopyLocalDirTohdfs</span>(String localPath, String hdfsPath)
    {
        <span class="hljs-keyword">try</span>
        {
            File root = <span class="hljs-keyword">new</span> File(localPath);
            File[] files = root.listFiles();

            <span class="hljs-keyword">for</span> (File file : files)
            {
                <span class="hljs-keyword">if</span> (file.isFile())
                {
                    copyLocalFileToHDFS(file.getPath().toString(), hdfsPath);

                }
                <span class="hljs-keyword">else</span> <span class="hljs-keyword">if</span>(file.isDirectory())
                {
                    CopyLocalDirTohdfs(localPath+<span class="hljs-string">"/"</span>+file.getName(), hdfsPath+<span class="hljs-string">"/"</span>+file.getName());
                }
            }
            <span class="hljs-keyword">return</span> <span class="hljs-keyword">true</span>;
        }
        <span class="hljs-keyword">catch</span> (Exception e)
        {
            e.printStackTrace();
        }
        <span class="hljs-keyword">return</span> <span class="hljs-keyword">false</span>;
    }


    <span class="hljs-javadoc">/**
     * 从hdfs下载
     *<span class="hljs-javadoctag"> @param</span> hdfsFilename
     *<span class="hljs-javadoctag"> @param</span> localPath
     *<span class="hljs-javadoctag"> @return</span>
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">boolean</span> <span class="hljs-title">downloadFileFromHdfs</span>(String hdfsFilename, String localPath)
    {
        <span class="hljs-keyword">try</span>
        {
            Path f = <span class="hljs-keyword">new</span> Path(hdfsFilename);

            FSDataInputStream dis = hdfs.open(f);
            File file = <span class="hljs-keyword">new</span> File(localPath + <span class="hljs-string">"/"</span> + f.getName());
            FileOutputStream os = <span class="hljs-keyword">new</span> FileOutputStream(file);

            <span class="hljs-keyword">byte</span>[] buffer = <span class="hljs-keyword">new</span> <span class="hljs-keyword">byte</span>[<span class="hljs-number">1024000</span>];
            <span class="hljs-keyword">int</span> length = <span class="hljs-number">0</span>;
            <span class="hljs-keyword">while</span> ((length = dis.read(buffer)) &gt; <span class="hljs-number">0</span>)
            {
                os.write(buffer, <span class="hljs-number">0</span>, length);
            }

            os.close();
            dis.close();

            <span class="hljs-keyword">return</span> <span class="hljs-keyword">true</span>;
        }
        <span class="hljs-keyword">catch</span> (Exception e)
        {
            e.printStackTrace();
        }
        <span class="hljs-keyword">return</span> <span class="hljs-keyword">false</span>;
    }

    <span class="hljs-javadoc">/**
     * HDFS 到 HDFS 的合并
     * hdfs提供了一种FileUtil.copyMerge（）的方法， 注意下面的 false 这个，如果改为true，就会删除这个目录
     *<span class="hljs-javadoctag"> @param</span> folder 需要合并的目录
     *<span class="hljs-javadoctag"> @param</span> file  要合并成的文件，完整路径名称
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">copyMerge</span>(String folder, String file) {
        Configuration conf = <span class="hljs-keyword">new</span> Configuration();
        Path src = <span class="hljs-keyword">new</span> Path(folder);
        Path dst = <span class="hljs-keyword">new</span> Path(file);

        <span class="hljs-keyword">try</span> {
            FileUtil.copyMerge(src.getFileSystem(conf), src,
                    dst.getFileSystem(conf), dst, <span class="hljs-keyword">false</span>, conf, <span class="hljs-keyword">null</span>);
        } <span class="hljs-keyword">catch</span> (IOException e) {
            <span class="hljs-comment">// TODO Auto-generated catch block</span>
            e.printStackTrace();
        }
    }


    <span class="hljs-javadoc">/**
     * 列出所有DataNode的名字信息
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">listDataNodeInfo</span>()
    {
        <span class="hljs-keyword">try</span>
        {
            DistributedFileSystem fs =<span class="hljs-keyword">null</span>;
            fs = (DistributedFileSystem) hdfs;
            DatanodeInfo[] dataNodeStats = fs.getDataNodeStats();
            String[] names = <span class="hljs-keyword">new</span> String[dataNodeStats.length];
            System.out.println(<span class="hljs-string">"List of all the datanode in the HDFS cluster:"</span>);

            <span class="hljs-keyword">for</span> (<span class="hljs-keyword">int</span> i = <span class="hljs-number">0</span>; i &lt; names.length; i++)
            {
                names[i] = dataNodeStats[i].getHostName();
                System.out.println(names[i]);
            }
            System.out.println(hdfs.getUri().toString());
        }
        <span class="hljs-keyword">catch</span> (Exception e)
        {
            e.printStackTrace();
        }
    }

    <span class="hljs-javadoc">/**
     * 检测是否是备用节点
     *<span class="hljs-javadoctag"> @throws</span> Exception
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">boolean</span> <span class="hljs-title">checkStandbyException</span>(String filename)
    {
        <span class="hljs-keyword">try</span> {
            Path f = <span class="hljs-keyword">new</span> Path(filename);
            hdfs.exists(f);
        } <span class="hljs-keyword">catch</span> (org.apache.hadoop.ipc.RemoteException e) {
            <span class="hljs-keyword">if</span>(e.getClassName().equals(<span class="hljs-string">"org.apache.hadoop.ipc.StandbyException"</span>))
            {
                <span class="hljs-keyword">return</span> <span class="hljs-keyword">true</span>;
            }
        } <span class="hljs-keyword">catch</span> (Exception e) {

        }
        <span class="hljs-keyword">return</span> <span class="hljs-keyword">false</span>;
    }


    <span class="hljs-keyword">public</span> <span class="hljs-keyword">boolean</span> <span class="hljs-title">mergeDirFiles</span>(List&lt;FileStatus&gt; fileList, String tarPath, String rowTerminateFlag)
    {
        <span class="hljs-comment">//rowTerminateFlag   \n</span>
        FSDataOutputStream tarFileOutputStream = <span class="hljs-keyword">null</span>;
        FSDataInputStream srcFileInputStream = <span class="hljs-keyword">null</span>;

        <span class="hljs-keyword">try</span>
        {
            Path tarFile = <span class="hljs-keyword">new</span> Path(tarPath);
            tarFileOutputStream = hdfs.create(tarFile, <span class="hljs-keyword">true</span>);

            <span class="hljs-keyword">byte</span>[] buffer = <span class="hljs-keyword">new</span> <span class="hljs-keyword">byte</span>[<span class="hljs-number">1024000</span>];
            <span class="hljs-keyword">int</span> length = <span class="hljs-number">0</span>;
            <span class="hljs-keyword">long</span> nTotalLength = <span class="hljs-number">0</span>;
            <span class="hljs-keyword">int</span> nCount = <span class="hljs-number">0</span>;
            <span class="hljs-keyword">boolean</span> bfirst = <span class="hljs-keyword">true</span>;
            <span class="hljs-keyword">for</span>(FileStatus file : fileList)
            {
                <span class="hljs-keyword">if</span>(file.getPath().equals(tarFile))
                {
                    <span class="hljs-keyword">continue</span>;
                }
                System.out.println(<span class="hljs-string">" merging file from  "</span> + file.getPath() + <span class="hljs-string">" to "</span> + tarPath);

                <span class="hljs-keyword">if</span>(!bfirst)
                {
                    <span class="hljs-comment">//添加换行符</span>
                    tarFileOutputStream.write(rowTerminateFlag.getBytes(), <span class="hljs-number">0</span>, rowTerminateFlag.length());
                }

                srcFileInputStream = hdfs.open(file.getPath(), buffer.length);
                <span class="hljs-keyword">while</span> ((length = srcFileInputStream.read(buffer)) &gt; <span class="hljs-number">0</span>)
                {
                    nCount++;
                    tarFileOutputStream.write(buffer, <span class="hljs-number">0</span>, length);
                    nTotalLength += length;
                    <span class="hljs-comment">//System.out.println(" file length " + file.getLen() + " read " + length);</span>
                    <span class="hljs-keyword">if</span> (nCount % <span class="hljs-number">1000</span> == <span class="hljs-number">0</span>)
                    {
                        tarFileOutputStream.flush();
                        System.out.println((<span class="hljs-keyword">new</span> Date()).toLocaleString() + <span class="hljs-string">": Have move "</span> + (nTotalLength / <span class="hljs-number">1024000</span>) + <span class="hljs-string">" MB"</span>);
                    }

                }

                srcFileInputStream.close();

                bfirst = <span class="hljs-keyword">false</span>;
            }

        }
        <span class="hljs-keyword">catch</span> (Exception e)
        {
            e.printStackTrace();
            <span class="hljs-keyword">try</span>
            {
                delete(tarPath);
            }
            <span class="hljs-keyword">catch</span> (Exception e2)
            {
                <span class="hljs-comment">// TODO: handle exception</span>
            }
            <span class="hljs-keyword">return</span> <span class="hljs-keyword">false</span>;
        }
        <span class="hljs-keyword">finally</span>
        {
            <span class="hljs-keyword">try</span>
            {
                <span class="hljs-keyword">if</span>(tarFileOutputStream != <span class="hljs-keyword">null</span>)
                {
                    tarFileOutputStream.flush();
                    tarFileOutputStream.close();
                    srcFileInputStream.close();
                }
            }
            <span class="hljs-keyword">catch</span> (Exception e2)
            {
                <span class="hljs-comment">// TODO: handle exception</span>
            }
        }
        <span class="hljs-keyword">return</span> <span class="hljs-keyword">true</span>;
    }


}

    <span class="hljs-javadoc">/**
     * 将一个字符串写入某个路径
     *
     *<span class="hljs-javadoctag"> @param</span> text 要保存的字符串
     *<span class="hljs-javadoctag"> @param</span> path 要保存的路径
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">writerString</span>(String text,String path){

        <span class="hljs-keyword">try</span>
        {
            Path f = <span class="hljs-keyword">new</span> Path(path);
            FSDataOutputStream os = fs.create(f, <span class="hljs-keyword">true</span>);
            BufferedWriter  writer = <span class="hljs-keyword">new</span> BufferedWriter(<span class="hljs-keyword">new</span> OutputStreamWriter(os, <span class="hljs-string">"utf-8"</span>));<span class="hljs-comment">// 以UTF-8格式写入文件，不乱码</span>
            writer.write(text);
            writer.close();
            os.close();
        }
        <span class="hljs-keyword">catch</span> (Exception e)
        {
            e.printStackTrace();

        }

    }

    <span class="hljs-javadoc">/**
     * 按行读取文件内容，并且防止乱码
     *<span class="hljs-javadoctag"> @param</span> hdfsFilename
     *<span class="hljs-javadoctag"> @return</span>
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">boolean</span> <span class="hljs-title">readByLine</span>(String hdfsFilename)
    {
        <span class="hljs-keyword">try</span>
        {
            Path f = <span class="hljs-keyword">new</span> Path(hdfsFilename);

            FSDataInputStream dis = hdfs.open(f);

            BufferedReader bf=<span class="hljs-keyword">new</span> BufferedReader(<span class="hljs-keyword">new</span> InputStreamReader(dis));<span class="hljs-comment">//防止中文乱码</span>
            String line = <span class="hljs-keyword">null</span>;
            <span class="hljs-keyword">while</span> ((line=bf.readLine())!=<span class="hljs-keyword">null</span>)
            {
                System.out.println(<span class="hljs-keyword">new</span> String(line.getBytes(),<span class="hljs-string">"utf-8"</span>));
            }

            dis.close();
            <span class="hljs-keyword">return</span> <span class="hljs-keyword">true</span>;
        }
        <span class="hljs-keyword">catch</span> (Exception e)
        {
            e.printStackTrace();
        }
        <span class="hljs-keyword">return</span> <span class="hljs-keyword">false</span>;
    }

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">reNameExistsPath</span>(String srcPath, String tarPath) <span class="hljs-keyword">throws</span> Exception
    {
        <span class="hljs-comment">//检测输出目录是否存在，存在就改名</span>
        <span class="hljs-keyword">if</span>(checkFileExist(srcPath))
        {
            tarPath = srcPath.trim();
            <span class="hljs-keyword">while</span>(tarPath.charAt(tarPath.length()-<span class="hljs-number">1</span>) == <span class="hljs-string">'/'</span>)
            {
                tarPath = tarPath.substring(<span class="hljs-number">0</span>, tarPath.length()-<span class="hljs-number">1</span>);
            }
            Date now = <span class="hljs-keyword">new</span> Date(); 
            SimpleDateFormat dateFormat = <span class="hljs-keyword">new</span> SimpleDateFormat(<span class="hljs-string">"yyMMddHHmmss"</span>);
            String nowStr = dateFormat.format(now); 
            tarPath += <span class="hljs-string">"_"</span> + nowStr;
            movefile(srcPath, tarPath);
        }
        <span class="hljs-keyword">else</span> 
        {
            tarPath = srcPath;
        }
    }</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>