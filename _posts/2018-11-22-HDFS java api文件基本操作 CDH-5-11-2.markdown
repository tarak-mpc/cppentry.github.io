---
layout:     post
title:      HDFS java api文件基本操作 CDH-5-11-2
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：未经同意请勿转载					https://blog.csdn.net/Thomas_an/article/details/82796136				</div>
								            <div id="content_views" class="markdown_views prism-dracula">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h3><a id="HDFS_java_api__0"></a>HDFS java api 文件基本操作</h3>
<pre><code>package hdfs;

import java.io.BufferedReader;
import java.io.ByteArrayInputStream;
import java.io.File;
import java.io.IOException;
import java.io.InputStreamReader;
import java.net.URI;
import java.util.Scanner;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.FSDataInputStream;
import org.apache.hadoop.fs.FSDataOutputStream;
import org.apache.hadoop.fs.FileStatus;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IOUtils;

public class HdfsOperation {

    public static final String BASE_URL = "hdfs://hadoop-master.wei.com:9000";
    public static Configuration conf;
    public static FileSystem fs;

    //静态代码块创建fs文件系统对象
    static {
        conf = new Configuration();
        try {
            fs = FileSystem.get(URI.create(BASE_URL), conf);
        } catch (IOException e) {
            System.out.println("initialize filesystem error");
            e.printStackTrace();
        }
    }

    /*** 查看文件内容 ***/
    public static void cat(String filePath) {
        Path path = new Path(filePath);
        FSDataInputStream inputStream = null;
        try {
            inputStream = fs.open(path);
            BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(inputStream, "UTF-8"));

            IOUtils.copyBytes(inputStream, System.out, 4096, false);
        } catch (IOException e) {

            e.printStackTrace();
        } finally {
            IOUtils.closeStream(inputStream);
        }

    }

    /*** 上传文件到hdfs ***/
    public static boolean put(boolean delSrcFile, boolean overwrite, String srcStr, String dstStr) throws IOException {
        Path srcPath = new Path(srcStr);
        Path dstPath = new Path(dstStr);

        // 判断源文件是否存在
        if (!(new File(srcStr)).exists()) {
            System.out.println("上传的源文件不存在！");
            return false;
        }

        // 判断上传目录是否存在
        if (!fs.exists(dstPath)) {
            System.out.println("上传路径不存在！");
            return false;
        }

        fs.copyFromLocalFile(delSrcFile, overwrite, srcPath, dstPath);

        System.out.println("上传到 :" + conf.get("fs.defaultFS.name") + dstStr);
        // 列出该目录下的文件
        System.out.println("-------list-----------");
        FileStatus[] listStatus = fs.listStatus(dstPath);

        for (FileStatus file : listStatus) {
            System.out.println(file.getPath());
        }
        return true;

    }

    /*** 从hdfs下载文件 ***/
    public static boolean get(boolean delSrc, String srcStr, String dstStr, boolean useRawLocalFileSystem)
            throws IOException {
        Path srcPath = new Path(srcStr);
        Path dstPath = new Path(dstStr);

        // 判断源文件是否存在
        if (!fs.exists(srcPath)) {
            System.out.println("源文件不存在");
            return false;
        }

        // 判断目标路径是否存在,不存则创建
        else if (!new File(dstStr).exists()) {
            new File(dstStr).mkdirs();
            System.out.println();
        } else {
            String fileName = srcPath.getName();
            // 判断同名文件是否存在
            if (new File(dstStr + "\\" + fileName).exists()) {
                System.out.println("本地文件已存在！");
                return false;
            } else {
                // 下载文件
                fs.copyToLocalFile(delSrc, srcPath, dstPath, useRawLocalFileSystem);
                System.out.println("download successful! At:" + dstStr);
                return true;

            }
        }
        return true;
    }

    /*** 删除文件 ***/
    public static boolean rmFile(String filePath) throws IOException {

        Path path = new Path(filePath);
        boolean deleteOnExit = fs.deleteOnExit(path);
        if (deleteOnExit) {
            System.out.println("删除成功");
            return deleteOnExit;
        } else {
            System.out.println("删除失败,文件不存在");
            return deleteOnExit;
        }

    }

    /*** 文件重命名 ***/
    public static boolean renameFile(String oldName, String newName) throws IOException {
        Path srcPath = new Path(oldName);
        Path newPath = new Path(newName);
        boolean isSuccess = fs.rename(srcPath, newPath);
        if (isSuccess) {
            System.out.println("重命名: " + oldName + " 为  " + newName);
        } else {
            System.out.println("重命名失败！");
        }
        return isSuccess;
    }

    /*** 创建文件,并且写入内容 ***/
    public static boolean touch(String filePath, String content) throws IOException {
        Path path = new Path(filePath);
        byte[] contens = content.getBytes();

        boolean overwrite;
        if (fs.exists(path)) {
            // 文件已经存在
            while (true) {
                System.out.println("文件已经存在,是否覆盖(y/n)?");
                Scanner sc = new Scanner(System.in);
                String input = sc.next();
                if ("y".equals(input.trim()) || "Y".equals(input.trim())) {
                    FSDataOutputStream out = fs.create(path, true);
                    out.write(contens);
                    out.close();
                    System.out.println("文件创建成功！");
                    return true;

                } else if ("n".equals(input) || "N".equals(input)) {
                    System.out.println("文件写入失败，文件已存在");
                    return false;
                } else {
                    continue;
                }
            }

        } else {
            // 文件不存在
            FSDataOutputStream out = fs.create(path);
            out.write(contens);
            out.close();
            System.out.println("文件创建成功！");
            return true;
        }

    }

    /*** 创建文件夹 ***/
    public static boolean mkdir(String dirPath) throws IOException {
        Path path = new Path(dirPath);
        boolean exists = fs.exists(path);
        if (exists) {
            System.out.println("目录已存在");
            return false;
        } else {
            if (fs.mkdirs(path)) {
                System.out.println("目录创建成功: " + dirPath);
                return true;
            }
            return false;
        }
    }

    /** 列出路径下的目录 **/
    public static void ls(String path) throws IOException {
        Path p = new Path(path);
        if (!fs.exists(p)) {
            System.out.println("路径：" + path + " 不存在！");
        } else {
            FileStatus[] listStatus = fs.listStatus(p);
            System.out.println("under path:" + path);
            for (FileStatus f : listStatus) {
                System.out.println(f.getPath());
            }
        }

    }

    /** 添加内容到文件 **/
    public static void append(String content, String filePath) throws IOException {
        Path path = new Path(filePath);
        // 将字符串转为输入流
        ByteArrayInputStream in = new ByteArrayInputStream(content.getBytes());
        // 获得添加 输出流
        FSDataOutputStream out = fs.append(path, 4096);
        IOUtils.copyBytes(in, out, 4096, true);

    }

}
</code></pre>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>