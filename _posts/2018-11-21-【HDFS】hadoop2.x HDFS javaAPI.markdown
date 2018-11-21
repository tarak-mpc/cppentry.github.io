---
layout:     post
title:      【HDFS】hadoop2.x HDFS javaAPI
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>Configuration conf = <strong><span style="color:rgb(127,0,85);">new</span></strong> Configuration();</p>
<p>conf.set(<span style="color:rgb(42,0,255);">"fs.defaultFS"</span>, <span style="color:rgb(42,0,255);">"hdfs://h6:9000"</span>);</p>
<p>FileSystem fileSystem = FileSystem.<em>get</em>(conf);</p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p>1.创建文件夹:</p>
<p>判断是否存在</p>
<p>不存在再创建</p>
<p><strong><span style="color:rgb(127,0,85);">if</span></strong> (!fileSystem.exists(<strong><span style="color:rgb(127,0,85);">new</span></strong> Path(<span style="color:rgb(42,0,255);">"/weir01"</span>))) {</p>
<p>        fileSystem.mkdirs(<strong><span style="color:rgb(127,0,85);">new</span></strong> Path(<span style="color:rgb(42,0,255);">"/weir01"</span>));</p>
<p>      }</p>
<p> </p>
<p>2.创建文件：</p>
<p>in - InputStream to read from 原文件路径</p>
<p>out - OutputStream to write to  hdfs 目录</p>
<p>the size of the buffer  缓冲大小</p>
<p>close - whether or not close the InputStream and OutputStream at the end. The streams are closed in the finally clause. 是否关闭流</p>
<p> </p>
<p> </p>
<p> </p>
<p>FSDataOutputStream out =fileSystem.create(<strong><span style="color:rgb(127,0,85);">new</span></strong> Path(<span style="color:rgb(42,0,255);">"/d1"</span>));</p>
<p>      FileInputStream in = <strong><span style="color:rgb(127,0,85);">new</span></strong> FileInputStream(<span style="color:rgb(42,0,255);">"f:/hadoop.zip"</span>);</p>
<p>      IOUtils.<em>copyBytes</em>(in, out, 1024, <strong><span style="color:rgb(127,0,85);">true</span></strong>);</p>
<p> </p>
<p> </p>
<p>3上传本地文件</p>
<p> </p>
<p>delSrc - whether to delete the src是否删除源文件</p>
<p>overwrite - whether to overwrite an existing file是否覆盖已存在的文件</p>
<p>srcs - array of paths which are source 可以上传多个文件数组方式</p>
<p>dst – path 目标路径</p>
<p> </p>
<p>fileSystem.copyFromLocalFile(src, dst);</p>
<p>      fileSystem.copyFromLocalFile(delSrc, src, dst);</p>
<p>      fileSystem.copyFromLocalFile(delSrc, overwrite, src, dst);</p>
<p>   fileSystem.copyFromLocalFile(delSrc, overwrite, srcs, dst);</p>
<p> </p>
<p> </p>
<p>4 重命名HDFS文件</p>
<p> </p>
<p>fileSystem.rename(src, dst);</p>
<p> </p>
<p>5.删除文件</p>
<p> </p>
<p>True 表示递归删除</p>
<p>fileSystem.delete(<strong><span style="color:rgb(127,0,85);">new</span></strong> Path(<span style="color:rgb(42,0,255);">"/d1"</span>), <strong><span style="color:rgb(127,0,85);">true</span></strong>);</p>
<p> </p>
<p>6.查看目录及文件信息</p>
<p> </p>
<p>FileStatus[] fs = fileSystem.listStatus(<strong><span style="color:rgb(127,0,85);">new</span></strong> Path(<span style="color:rgb(42,0,255);">"/"</span>));</p>
<p>      <strong><span style="color:rgb(127,0,85);">for</span></strong> (FileStatus f : fs) {</p>
<p>        String dir = f.isDirectory() ? <span style="color:rgb(42,0,255);">"</span><span style="color:rgb(42,0,255);">目录</span><span style="color:rgb(42,0,255);">"</span>:<span style="color:rgb(42,0,255);">"</span><span style="color:rgb(42,0,255);">文件</span><span style="color:rgb(42,0,255);">"</span>;</p>
<p>        String name = f.getPath().getName();</p>
<p>        String path = f.getPath().toString();</p>
<p>        System.<em><span style="color:rgb(0,0,192);">out</span></em>.println(dir+<span style="color:rgb(42,0,255);">"----"</span>+name+<span style="color:rgb(42,0,255);">"  path:"</span>+path);</p>
<p>        System.<em><span style="color:rgb(0,0,192);">out</span></em>.println(f.getAccessTime());</p>
<p>        System.<em><span style="color:rgb(0,0,192);">out</span></em>.println(f.getBlockSize());</p>
<p>        System.<em><span style="color:rgb(0,0,192);">out</span></em>.println(f.getGroup());</p>
<p>        System.<em><span style="color:rgb(0,0,192);">out</span></em>.println(f.getLen());</p>
<p>        System.<em><span style="color:rgb(0,0,192);">out</span></em>.println(f.getModificationTime());</p>
<p>        System.<em><span style="color:rgb(0,0,192);">out</span></em>.println(f.getOwner());</p>
<p>        System.<em><span style="color:rgb(0,0,192);">out</span></em>.println(f.getPermission());</p>
<p>        System.<em><span style="color:rgb(0,0,192);">out</span></em>.println(f.getReplication());</p>
<p>        System.<em><span style="color:rgb(0,0,192);">out</span></em>.println(f.getSymlink());</p>
<p>      }</p>
<p> </p>
<p> </p>
<p>7.查找某个文件在HDFS集群的位置</p>
<p> </p>
<p>FileStatus fs = fileSystem.getFileStatus(<strong><span style="color:rgb(127,0,85);">new</span></strong> Path(<span style="color:rgb(42,0,255);">"/data"</span>));</p>
<p>      BlockLocation[] bls=fileSystem.getFileBlockLocations(fs, 0, fs.getLen());</p>
<p>      <strong><span style="color:rgb(127,0,85);">for</span></strong> (<strong><span style="color:rgb(127,0,85);">int</span></strong> i = 0,h=bls.<span style="color:rgb(0,0,192);">length</span>; i &lt; h; i++) {</p>
<p>        String[] hosts= bls[i].getHosts();</p>
<p>        System.<em><span style="color:rgb(0,0,192);">out</span></em>.println(<span style="color:rgb(42,0,255);">"block_"</span>+i+<span style="color:rgb(42,0,255);">"_location:  "</span>+hosts[0]);</p>
<p>      }</p>
<p> </p>
<p> </p>
<p>8.获取HDFS集群上所有节点名称信息</p>
<p> </p>
<p> </p>
<p>DistributedFileSystem hdfs = (DistributedFileSystem) fileSystem;</p>
<p>      DatanodeInfo[] dns=hdfs.getDataNodeStats();</p>
<p>      <strong><span style="color:rgb(127,0,85);">for</span></strong> (<strong><span style="color:rgb(127,0,85);">int</span></strong> i = 0,h=dns.<span style="color:rgb(0,0,192);">length</span>; i &lt; h; i++) {</p>
<p>        System.<em><span style="color:rgb(0,0,192);">out</span></em>.println(<span style="color:rgb(42,0,255);">"datanode_"</span>+i+<span style="color:rgb(42,0,255);">"_name:  "</span>+dns[i].getHostName());</p>
<p>      }</p>
<p> </p>
            </div>
                </div>