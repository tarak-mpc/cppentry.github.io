---
layout:     post
title:      Flink入门示例——wordCount(二)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h3 style="font-size:1.5em;font-weight:600;line-height:1.25;color:rgb(36,41,46);font-family:'-apple-system', BlinkMacSystemFont, '微软雅黑', 'PingFang SC', Helvetica, Tahoma, Arial, 'Hiragino Sans GB', 'Microsoft YaHei', SimSun, '宋体', Heiti, '黑体', sans-serif;font-style:normal;letter-spacing:normal;text-indent:0px;text-transform:none;word-spacing:0px;background-color:rgb(255,255,255);">说明（批处理）</h3><ul style="color:rgb(36,41,46);font-family:'-apple-system', BlinkMacSystemFont, '微软雅黑', 'PingFang SC', Helvetica, Tahoma, Arial, 'Hiragino Sans GB', 'Microsoft YaHei', SimSun, '宋体', Heiti, '黑体', sans-serif;font-size:14px;font-style:normal;font-weight:normal;letter-spacing:normal;text-indent:0px;text-transform:none;word-spacing:0px;background-color:rgb(255,255,255);"><li>读取HDFS数据</li><li>将处理结果写入HDFS</li></ul><h3 style="font-size:1.5em;font-weight:600;line-height:1.25;color:rgb(36,41,46);font-family:'-apple-system', BlinkMacSystemFont, '微软雅黑', 'PingFang SC', Helvetica, Tahoma, Arial, 'Hiragino Sans GB', 'Microsoft YaHei', SimSun, '宋体', Heiti, '黑体', sans-serif;font-style:normal;letter-spacing:normal;text-indent:0px;text-transform:none;word-spacing:0px;background-color:rgb(255,255,255);">代码示例</h3><pre style="font-family:'SFMono-Regular', Consolas, 'Liberation Mono', Menlo, Courier, monospace;font-size:11.9px;font-style:normal;font-variant:normal;font-weight:normal;line-height:1.45;background-color:rgb(246,248,250);color:rgb(36,41,46);letter-spacing:normal;text-indent:0px;text-transform:none;word-spacing:0px;"><code class="hljs" style="color:rgb(51,51,51);background:transparent;font-family:'SFMono-Regular', Consolas, 'Liberation Mono', Menlo, Courier, monospace, sans-serif;font-size:11.9px;border:0px;line-height:inherit;"><span class="hljs-keyword" style="color:#333333;font-weight:bold;">package</span> com.test

<span class="hljs-keyword" style="color:#333333;font-weight:bold;">import</span> org.apache.flink.api.scala._
<span class="hljs-keyword" style="color:#333333;font-weight:bold;">import</span> org.apache.flink.core.fs.<span class="hljs-type" style="color:#445588;font-weight:bold;">FileSystem</span>.<span class="hljs-type" style="color:#445588;font-weight:bold;">WriteMode</span>

<span class="hljs-comment" style="color:#999988;font-style:italic;">/**
  * <span class="zh-hans" style="font-family:'微软雅黑', SimSun, sans-serif;">读取</span>hdfs<span class="zh-hans" style="font-family:'微软雅黑', SimSun, sans-serif;">数据，把处理结果结果再写入</span>HDFS
  */</span>
<span class="hljs-class"><span class="hljs-keyword" style="color:#333333;font-weight:bold;">object</span> <span class="hljs-title" style="color:#445588;font-weight:bold;">WordCountBatch</span></span>{

  <span class="hljs-comment" style="color:#999988;font-style:italic;">/**
    * main<span class="zh-hans" style="font-family:'微软雅黑', SimSun, sans-serif;">函数传递的参数</span>
    * hdfs://artemis-02:9000/tmp/lvxw/tmp/logs  hdfs://artemis-02:9000//tmp/lvxw/tmp/out
    * @param args
    */</span>
  <span class="hljs-function"><span class="hljs-keyword" style="color:#333333;font-weight:bold;">def</span> <span class="hljs-title" style="font-weight:bold;">main</span></span>(args: <span class="hljs-type" style="color:#445588;font-weight:bold;">Array</span>[<span class="hljs-type" style="color:#445588;font-weight:bold;">String</span>]): <span class="hljs-type" style="color:#445588;font-weight:bold;">Unit</span> = {
    <span class="hljs-keyword" style="color:#333333;font-weight:bold;">if</span>(args.length!=<span class="hljs-number">2</span>){
      println(<span class="hljs-string" style="color:#dd1144;">s"<span class="hljs-subst" style="color:#333333;font-weight:normal;">${this.getClass.getSimpleName}</span> must be two param:inputDir outputDir"</span> )
      <span class="hljs-type" style="color:#445588;font-weight:bold;">System</span>.exit(<span class="hljs-number">1</span>)
    }

    <span class="hljs-comment" style="color:#999988;font-style:italic;">// <span class="zh-hans" style="font-family:'微软雅黑', SimSun, sans-serif;">在</span>window<span class="zh-hans" style="font-family:'微软雅黑', SimSun, sans-serif;">环境下，以</span>hadoop<span class="zh-hans" style="font-family:'微软雅黑', SimSun, sans-serif;">身份远程放完</span>HDFS</span>
    <span class="hljs-type" style="color:#445588;font-weight:bold;">System</span>.setProperty(<span class="hljs-string" style="color:#dd1144;">"HADOOP_USER_NAME"</span>,<span class="hljs-string" style="color:#dd1144;">"hadoop"</span>)
    <span class="hljs-keyword" style="color:#333333;font-weight:bold;">val</span> <span class="hljs-type" style="color:#445588;font-weight:bold;">Array</span>(inputDir,outputDir) = args

    <span class="hljs-keyword" style="color:#333333;font-weight:bold;">val</span> env = <span class="hljs-type" style="color:#445588;font-weight:bold;">ExecutionEnvironment</span>.getExecutionEnvironment

    <span class="hljs-keyword" style="color:#333333;font-weight:bold;">val</span> text = env.readTextFile(inputDir)

    <span class="hljs-keyword" style="color:#333333;font-weight:bold;">val</span> result = text.flatMap ( _.split(<span class="hljs-string" style="color:#dd1144;">"\\s"</span>))
      .map ((_, <span class="hljs-number">1</span>))
      .groupBy(<span class="hljs-number">0</span>)
      .sum(<span class="hljs-number">1</span>)

    result.setParallelism(<span class="hljs-number">2</span>).writeAsCsv(outputDir,<span class="hljs-string" style="color:#dd1144;">"\n"</span>,<span class="hljs-string" style="color:#dd1144;">","</span>,<span class="hljs-type" style="color:#445588;font-weight:bold;">WriteMode</span>.<span class="hljs-type" style="color:#445588;font-weight:bold;">OVERWRITE</span>)
    env.execute(<span class="hljs-keyword" style="color:#333333;font-weight:bold;">this</span>.getClass.getSimpleName)

  }
}
</code></pre><br>            </div>
                </div>