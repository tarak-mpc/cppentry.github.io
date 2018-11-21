---
layout:     post
title:      编写hive udf和使用hive udf：hue的hive界面中使用hive udf函数、oozie使用hive udf函数、hive命令行使用udf函数
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：尊重原创，转载请标明"本文转自:https://blog.csdn.net/high2011"					https://blog.csdn.net/high2011/article/details/52425430				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><strong><span style="font-size:24px;background-color:rgb(51,102,255);">开发环境：jdk1.7+idea 16+hive-1.1.0</span></strong></p>
<p><strong><span style="font-size:24px;background-color:rgb(51,102,255);">使用udf的生产环境：cdh5.8.0+hive-1.1.0</span></strong></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;">1、<span style="font-family:'宋体';">导入</span>hive<span style="font-family:'宋体';">的所有相关</span><span style="font-family:Calibri;">jar</span><span style="font-family:'宋体';">包</span></span></p>
<p><span style="font-family:'宋体';"><img src="https://img-blog.csdn.net/20160903210857322?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p><span style="font-family:'宋体';"><br></span></p>
<p><span style="font-family:'宋体';"><strong><span style="font-size:18px;">或者使用maven引入CDH相关包：</span></strong></span></p>
<p><span style="font-family:'宋体';"></span></p>
<div><pre><code class="language-html">&lt;?xml version="1.0" encoding="UTF-8"?&gt;
4.0.0com.ennhive-udf1.0-SNAPSHOTUTF-8UTF-81.71.1.0-cdh5.8.0<!--add  maven release-->1.71.7UTF-8<!--配置依赖库地址（用于加载CDH依赖的jar包） -->clouderahttps://repository.cloudera.com/artifactory/cloudera-repos/<!--cdh的hive依赖-->org.apache.hivehive-accumulo-handler${cdh.version}org.apache.hivehive-ant${cdh.version}org.apache.hivehive-beeline${cdh.version}org.apache.hivehive-cli${cdh.version}org.apache.hivehive-common${cdh.version}org.apache.hivehive-contrib${cdh.version}org.apache.hivehive-exec${cdh.version}hive-udf<!-- 编译插件 -->org.apache.maven.pluginsmaven-compiler-plugin3.11.71.7UTF-8<!-- 打包 -->maven-assembly-pluginjar-with-dependenciesmake-assemblypackagesingle</code></pre></div>
<br><p></p>
<p><span style="font-family:'宋体';"><br></span></p>
<p><span style="font-family:'宋体';"></span></p>
<p><strong><span style="font-size:18px;">2、<span style="font-family:'宋体';">编写类</span>WeekTransform.java</span></strong></p>
<img src="https://img-blog.csdn.net/20160903211045569?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><p><span style="font-family:'宋体';"><br></span></p>
<p><span style="font-family:'宋体';"></span></p>
<p>内容如下：</p>
<p></p>
<div><pre><code class="language-java">import org.apache.hadoop.hive.ql.exec.UDF;
import org.apache.hadoop.io.Text;
import java.text.SimpleDateFormat;
import java.util.Calendar;
import java.util.Date;
/**
 * Document:本类作用----&gt;hive日期转换函数---返回日期所在的周
 * User: yangjf
 * Date: 2016/9/1  8:8
 */
public class WeekTransform extends UDF {

    public Integer evaluate(final Text str) {
        Integer weekDay=null;
        try{
            Date nowDate= new SimpleDateFormat("yyyy-MM-dd").parse(str.toString());
            Calendar calendar = Calendar.getInstance();
            if(nowDate != null){
                calendar.setTime(nowDate);
            }
            weekDay= calendar.get(Calendar.DAY_OF_WEEK) - 1;
            if (weekDay &lt; 0){
                weekDay= 1;
            }
        }catch (Exception e){
        }
        return weekDay+1;
    }
//    public static void main(String[] args) {
//        System.out.println(new WeekTransform().evaluate("2016-08-31"));
//    }
}</code></pre></div>
<br><p></p>
<p><span style="font-size:18px;">3<span style="font-family:'宋体';">、使用idea编译、打包生成</span><span style="color:rgb(0,0,255);">hiveUdf.jar</span></span></p>
<br><p><span style="font-family:'宋体';"></span></p>
<p><span style="background-color:rgb(51,204,255);"><span style="font-size:24px;"><strong><span style="font-family:'宋体';">第一种使用方法：在</span>xshell<span style="font-family:'宋体';">中使用</span><span style="font-family:Calibri;">udf</span><span style="font-family:'宋体';">函数</span></strong></span></span></p>
<p><span style="font-size:18px;">1、<span style="font-family:'宋体';">打开</span>xshell<span style="font-family:'宋体';">、输入</span><span style="font-family:Calibri;">hive</span><span style="font-family:'宋体';">进入命令行</span></span></p>
<p><span style="font-size:18px;">2、<span style="font-family:'宋体';">上传</span>hiveUdf.jar<span style="font-family:'宋体';">到当前用户目录</span></span></p>
<p><span style="font-size:18px;">3、<span style="font-family:'宋体';">将</span>linux<span style="font-family:'宋体';">目录上的</span><span style="color:rgb(0,0,255);">hiveUdf.jar</span>上传<span style="font-family:'宋体';">到</span>hdfs用户目录/user/hive/test/<span style="font-family:'宋体';">上</span></span></p>
<p><span style="font-size:18px;">   hdfs dfs -put  /user/e_lvbin/test/hiveudf.jar   /user/hive/test/</span></p>
<p><span style="font-size:18px;">4<span style="font-family:'宋体';">、添加</span><span style="font-family:Calibri;">jar</span><span style="font-family:'宋体';">到内存中</span></span></p>
<p><span style="font-size:18px;">   (1)<span style="font-family:'宋体';">第一种方法</span><span style="font-family:Verdana;">-----&gt;</span>add jar hdfs://<span style="color:rgb(255,0,0);">localhost:9000</span>/user/hive/test/<span style="color:rgb(0,0,255);">hiveUdf.jar</span>;</span></p>
<p><span style="font-size:18px;">   (2)<span style="font-family:'宋体';">第二种方法</span><span style="font-family:Verdana;">-----&gt;</span>add jar hdfs://<span style="color:rgb(255,0,0);">nameservice</span>/user/hive/test/<span style="color:rgb(0,0,255);">hiveUdf.jar</span>;</span></p>
<p><span style="font-size:18px;">注意：<span style="color:rgb(255,0,0);">nameservice</span><span style="color:rgb(255,0,0);"><span style="font-family:'宋体';">是</span>core-site.xml<span style="font-family:'宋体';">中，入口的别名，用于</span><span style="font-family:Verdana;">namenode</span><span style="font-family:'宋体';">的</span><span style="font-family:Verdana;">HA</span></span></span></p>
<p><span style="font-size:18px;">5<span style="font-family:'宋体';">、创建临时函数</span><span style="font-family:Verdana;">:</span><span style="color:rgb(0,0,255);">weektransform(<span style="font-family:'宋体';">可以改其他别名</span><span style="font-family:Verdana;">)</span></span></span></p>
<p><span style="font-size:18px;">create temporary function <span style="color:rgb(0,0,255);">
weektransform</span><span style="color:rgb(0,0,255);"> </span><span style="color:rgb(0,0,255);"> </span>as  ‘<span style="color:rgb(0,0,255);background:rgb(228,228,255);">WeekTransform</span><span style="color:rgb(0,0,255);">’</span>;</span></p>
<p><span style="font-size:18px;">注意：’<span style="color:rgb(0,0,255);background:rgb(228,228,255);">WeekTransform</span><span style="color:rgb(0,0,255);">’</span><span style="color:rgb(0,0,255);"><span style="font-family:'宋体';">是刚才编写的</span>java<span style="font-family:'宋体';">类，如果有包名称，则应该是全路径</span></span></span></p>
<p><span style="font-size:18px;">6、创建成功后可以使用命令查看到</span></p>
<p><span style="color:rgb(255,0,0);"><span style="font-size:18px;">&gt;show functions;</span></span></p>
<p><span style="font-size:18px;"><span style="font-family:'宋体';">会看见</span>----<span style="font-family:'宋体';">》</span><span style="font-family:Calibri;">default.</span><span style="color:rgb(0,0,255);">weektransform</span></span></p>
<p><span style="font-size:18px;"><span style="color:rgb(0,0,255);">7、</span><span style="color:rgb(0,0,255);">使用该函数</span></span></p>
<p><span style="font-size:18px;"><span style="color:rgb(0,0,255);">&gt;select weektransform(</span><span style="color:rgb(0,0,255);">‘</span><span style="color:rgb(0,0,255);">2016-08-31</span><span style="color:rgb(0,0,255);">’</span><span style="color:rgb(0,0,255);">);</span></span></p>
<p><span style="font-size:18px;"><span style="color:rgb(0,0,255);">返回</span><span style="color:rgb(0,0,255);">’</span><span style="color:rgb(0,0,255);">4</span><span style="color:rgb(0,0,255);">’</span><span style="color:rgb(0,0,255);"><span style="font-family:'宋体';">，代表是周三，因为周日是</span>1<span style="font-family:'宋体';">，依次类推，周三是</span><span style="font-family:Verdana;">4</span></span></span></p>
<br><p><span style="font-family:'宋体';"><br></span></p>
<p><span style="font-family:'宋体';"></span></p>
<p><span style="background-color:rgb(51,204,255);"><span style="font-size:24px;"><strong><span style="font-family:'宋体';">第二种使用方法：在</span>HUE<span style="font-family:'宋体';">中使用</span><span style="font-family:Calibri;">Hive</span><span style="font-family:'宋体';">自定义函数</span></strong></span></span></p>
<p><span style="font-size:18px;">1、<span style="font-family:'宋体';">打开</span>hue<span style="font-family:'宋体';">窗口，进入</span><span style="font-family:Calibri;">hive</span><span style="font-family:'宋体';">界面</span></span></p>
<p><span style="font-size:18px;">2、点击’session’,<span style="font-family:'宋体';">即图片中的红框处</span></span></p>
<img src="https://img-blog.csdn.net/20160903211336086?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><p><span style="font-family:'宋体';"><br></span></p>
<p><span style="font-family:'宋体';"></span></p>
<p><br></p>
<p><span style="font-size:18px;">3<span style="font-family:'宋体';">、选择上传到</span><span style="font-family:Calibri;">hdfs</span><span style="font-family:'宋体';">的</span><span style="color:rgb(0,0,255);">hiveUdf.jar<span style="font-family:'宋体';">包、输入自定义函数的名称</span><span style="font-family:Calibri;">(</span><span style="font-family:'宋体';">可以该其他名称</span><span style="font-family:Calibri;">)</span><span style="font-family:'宋体';">、添加</span><span style="font-family:Calibri;">jar</span><span style="font-family:'宋体';">包中的</span><span style="font-family:Calibri;">udf</span><span style="font-family:'宋体';">函数类的全路径</span></span></span></p>
<p><span style="color:rgb(0,0,255);"><span style="font-family:'宋体';"><img src="https://img-blog.csdn.net/20160903211409336?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></span></p>
<p><span style="color:rgb(0,0,255);"><span style="font-family:'宋体';"><br></span></span></p>
<p><span style="color:rgb(0,0,255);"><span style="font-family:'宋体';"><br></span></span></p>
<p><span style="color:rgb(0,0,255);"><span style="font-family:'宋体';"><br></span></span></p>
<p><span style="font-family:'宋体';"></span></p>
<p><span style="font-size:18px;color:#333333;">4<span style="font-family:'宋体';">、使用</span><span style="font-family:Calibri;">weektransform</span><span style="font-family:'宋体';">函数</span></span></p>
<br><p><span style="color:rgb(0,0,255);"><span style="font-family:'宋体';"><img src="https://img-blog.csdn.net/20160903211431258?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></span></p>
<p><span style="color:rgb(0,0,255);"><span style="font-family:'宋体';"><br></span></span></p>
<p><span style="font-family:'宋体';"></span></p>
<p style="color:rgb(0,0,255);"><span style="background-color:rgb(51,204,255);"><strong><span style="font-size:24px;"><span style="font-family:'宋体';">第三种使用方法：在</span>oozie<span style="font-family:'宋体';">调度中使用</span><span style="font-family:Calibri;">udf</span><span style="font-family:'宋体';">函数</span></span></strong></span></p>
<p><span style="font-size:18px;color:#333333;">1<span style="font-family:'宋体';">、上传</span><span style="font-family:Calibri;">hiveUdf.jar</span><span style="font-family:'宋体';">包到</span><span style="font-family:Calibri;">oozie</span><span style="font-family:'宋体';">目录下</span></span></p>
<p><span style="font-size:18px;color:#333333;">2<span style="font-family:'宋体';">、创建一个</span><span style="font-family:Calibri;">workflow</span><span style="font-family:'宋体';">、添加</span><span style="font-family:Calibri;">2</span><span style="font-family:'宋体';">个</span><span style="font-family:Calibri;">hive</span><span style="font-family:'宋体';">流程</span><span style="font-family:Calibri;">(</span><span style="font-family:'宋体';">一个是</span><span style="font-family:Calibri;">hiveudf.txt</span><span style="font-family:'宋体';">脚本、一个是</span><span style="font-family:Calibri;">ykt_modelToApp_predict.txt)</span></span></p>
<p><span style="font-size:18px;color:#333333;">3<span style="font-family:'宋体';">、编写</span><span style="font-family:Calibri;">hiveudf.txt</span><span style="font-family:'宋体';">脚本，内容如下</span><span style="font-family:Calibri;">:</span></span></p>
<p><span style="font-size:18px;color:#333333;">use origin_ennenergy_test;</span></p>
<p><span style="font-size:18px;color:#333333;">add jar  hdfs://nameservice/user/hive/test/hiveUdf.jar;</span></p>
<p><span style="font-size:18px;color:#333333;"> </span></p>
<p><span style="font-size:18px;color:#333333;">4、<span style="font-family:'宋体';">编写另一个脚本</span>:ykt_modelToApp_predict.txt<span style="font-family:'宋体';">脚本，内容如下</span><span style="font-family:Calibri;">:</span></span></p>
<p><span style="font-size:18px;color:#333333;">use origin_ennenergy_test;</span></p>
<p><span style="font-size:18px;color:#333333;">create table  t_test_weektransform as select weektransform(‘2016-08-31’);</span></p>
<p><span style="font-size:18px;color:#333333;"><br></span></p>
<p><span style="font-size:18px;color:#333333;">5<span style="font-family:'宋体';">、编写</span><span style="font-family:Calibri;">hive-site.xml</span><span style="font-family:'宋体';">文件(备注一)</span></span></p>
<p><span style="font-size:18px;color:#333333;">6<span style="font-family:'宋体';">、先添加</span><span style="font-family:Calibri;">hiveudf.txt</span><span style="font-family:'宋体';">，再添加</span><span style="font-family:Calibri;">ykt_modelToApp_predict.txt</span></span></p>
<p><span style="font-size:18px;color:#ff0000;">注意：</span></p>
<p><span style="font-size:18px;color:#333333;">（1）<span style="font-family:'宋体';">需要编写</span>hive-site.xml<span style="font-family:'宋体';">脚本</span></span></p>
<p><span style="font-size:18px;color:#333333;">（2）<span style="font-family:'宋体';">每个流程都需要添加</span>hiveUdf.jar</span></p>
<p><span style="font-size:18px;color:#333333;">（3）txt<span style="font-family:'宋体';">文件编码格式应该是</span><span style="font-family:Calibri;">:UTF-8</span><span style="font-family:'宋体';">无</span><span style="font-family:Calibri;">BOM</span><span style="font-family:'宋体';">格式编码</span></span></p>
<p><span style="font-size:18px;color:#333333;"><span style="font-family:'宋体';">（4）首次用于使用udf函数时需要创建：</span></span></p>
<p><span style="font-size:18px;color:#333333;"><span style="font-family:'宋体';">     <span style="font-size:18px;">create temporary function </span><span style="font-size:18px;color:rgb(0,0,255);">weektransform</span><span style="font-size:18px;color:rgb(0,0,255);"> </span><span style="font-size:18px;color:rgb(0,0,255);"> </span><span style="font-size:18px;">as
  ‘</span><span style="font-size:18px;color:rgb(0,0,255);background:rgb(228,228,255);">WeekTransform</span><span style="font-size:18px;color:rgb(0,0,255);">’</span><span style="font-size:18px;">;</span></span></span></p>
<p><span style="font-size:18px;color:#333333;"><span style="font-family:'宋体';"><span style="font-size:18px;">     如果已经在当前用户中已经创建<span style="font-family:'宋体';font-size:18px;color:rgb(0,0,255);">weektransform临时函数，那么<span style="color:rgb(51,51,51);font-family:Calibri;font-size:18px;">ykt_modelToApp_predict.txt脚本中不用添加</span></span></span></span></span></p>
<p><span style="font-size:18px;color:#333333;"><span style="font-family:'宋体';"><span style="font-size:18px;"><span style="font-family:'宋体';font-size:18px;color:rgb(0,0,255);"><span style="color:rgb(51,51,51);font-family:Calibri;font-size:18px;">           如果集群重启，或者hive重启，那么需要在</span></span></span></span></span></p>
<p><span style="font-size:18px;color:#333333;"><span style="font-family:'宋体';"><span style="font-size:18px;"><span style="font-family:'宋体';font-size:18px;color:rgb(0,0,255);"><span style="color:rgb(51,51,51);font-family:Calibri;font-size:18px;">          <span style="color:rgb(51,51,51);font-size:18px;">add
 jar  hdfs://nameservice/user/hive/test/hiveUdf.jar;    后执行一次</span></span></span></span></span></span></p>
<p><span style="font-size:18px;color:#333333;"><span style="font-family:'宋体';"><span style="font-size:18px;"><span style="font-family:'宋体';font-size:18px;color:rgb(0,0,255);"><span style="color:rgb(51,51,51);font-family:Calibri;font-size:18px;"><span style="color:rgb(51,51,51);font-size:18px;"> 
         <span style="color:rgb(51,51,51);font-family:'宋体';font-size:18px;">create temporary function </span><span style="font-family:'宋体';font-size:18px;color:rgb(0,0,255);">weektransform</span><span style="font-family:'宋体';font-size:18px;color:rgb(0,0,255);"> </span><span style="font-family:'宋体';font-size:18px;color:rgb(0,0,255);"> </span><span style="color:rgb(51,51,51);font-family:'宋体';font-size:18px;">as
  ‘</span><span style="font-family:'宋体';font-size:18px;color:rgb(0,0,255);background:rgb(228,228,255);">WeekTransform</span><span style="font-family:'宋体';font-size:18px;color:rgb(0,0,255);">’</span><span style="color:rgb(51,51,51);font-family:'宋体';font-size:18px;">;</span></span></span></span></span></span></span></p>
<p><span style="font-size:18px;color:#333333;"><span style="font-family:'宋体';">    原因：udf函数只针对当前用户有效！</span></span></p>
<p><span style="font-size:18px;color:#3333ff;"><span style="font-family:'宋体';"><strong>编码格式图：</strong></span></span></p>
<span style="color:#0000ff;"><img src="https://img-blog.csdn.net/20160903211513618?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span><br><p><span style="color:rgb(0,0,255);"><span style="font-family:'宋体';"><br></span></span></p>
<p><span style="color:rgb(0,0,255);"><span style="font-family:'宋体';"><br></span></span></p>
<p><span style="color:rgb(0,0,255);"><span style="font-family:'宋体';"><br></span></span></p>
<p><span style="color:rgb(0,0,255);"><span style="font-family:'宋体';"><strong><span style="font-size:18px;">流程图：</span></strong></span></span></p>
<p><span style="color:rgb(0,0,255);"><span style="font-family:'宋体';"><img src="https://img-blog.csdn.net/20160903211523930?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></span></p>
<p><span style="color:rgb(0,0,255);"><span style="font-family:'宋体';"><br></span></span></p>
<p>备注一：</p>
<p></p>
<div><pre><code class="language-html">&lt;?xml version="1.0" encoding="UTF-8"?&gt;

<!--Autogenerated by Cloudera Manager-->
hive.metastore.uristhrift://host12.master.cluster.enn.cn:9083,thrift://host13.master.cluster.enn.cn:9083hive.metastore.client.socket.timeout300hive.metastore.warehouse.dir/user/hive/warehousehive.warehouse.subdir.inherit.permstrue<!--'hive.enable.spark.execution.engine', originally set to 'false' (non-final), is overridden below by a safety valve-->hive.conf.restricted.listhive.enable.spark.execution.enginehive.auto.convert.jointruehive.auto.convert.join.noconditionaltask.size20971520hive.optimize.bucketmapjoin.sortedmergefalsehive.smbjoin.cache.rows10000mapred.reduce.tasks-1hive.exec.reducers.bytes.per.reducer67108864hive.exec.copyfile.maxsize33554432<!--'hive.exec.reducers.max', originally set to '1099' (non-final), is overridden below by a safety valve-->hive.vectorized.groupby.checkinterval4096hive.vectorized.groupby.flush.percent0.1hive.compute.query.using.statsfalse<!--
    当一个表存在过多小文件（小于10M）时，不仅会对namenode造成很大压力，也会降低执行效率，甚至任务无法执行，
    因此，当发现表出现很多小文件时，请添加如下参数：
    hive.mergejob.maponly = true;
    hive.merge.mapfiles = true;
    hive.merge.mapredfiles = true;
    mapred.max.split.size=256000000;
    mapred.min.split.size.per.node=256000000;
    mapred.min.split.size.per.rack=256000000;
    hive.merge.size.per.task = 256000000;
    hive.merge.smallfiles.avgsize=100000000;
    hive.input.format=org.apache.hadoop.hive.ql.io.CombineHiveInputFormat;
    小文件合并的规则、算法需要讨论定制。
    可参考如下链接：http://blog.itpub.net/26086233/viewspace-1119964/
    http://www.linuxidc.com/Linux/2015-06/118391.htm
  -->hive.vectorized.execution.enabledtruehive.vectorized.execution.reduce.enabledfalsehive.merge.mapfilestruehive.merge.mapredfilesfalsehive.cbo.enablefalsehive.fetch.task.conversionminimalhive.fetch.task.conversion.threshold268435456hive.limit.pushdown.memory.usage0.1hive.merge.sparkfilestruehive.merge.smallfiles.avgsize16777216hive.merge.size.per.task268435456hive.optimize.reducededuplicationtruehive.optimize.reducededuplication.min.reducer4hive.map.aggrtruehive.map.aggr.hash.percentmemory0.5hive.optimize.sort.dynamic.partitionfalsespark.executor.memory4294967296spark.driver.memory2147483648spark.executor.cores1spark.yarn.driver.memoryOverhead512spark.yarn.executor.memoryOverhead1024spark.dynamicAllocation.enabledtruespark.dynamicAllocation.initialExecutors1spark.dynamicAllocation.minExecutors1spark.dynamicAllocation.maxExecutors2147483647hive.metastore.execute.setugitruehive.support.concurrencytruehive.zookeeper.quorumhost19.slave.cluster.enn.cn,host16.slave.cluster.enn.cn,host14.slave.cluster.enn.cn,host15.slave.cluster.enn.cn,host17.slave.cluster.enn.cnhive.zookeeper.client.port2181hive.zookeeper.namespacehive_zookeeper_namespace_hivehbase.zookeeper.quorumhost19.slave.cluster.enn.cn,host16.slave.cluster.enn.cn,host14.slave.cluster.enn.cn,host15.slave.cluster.enn.cn,host17.slave.cluster.enn.cnhbase.zookeeper.property.clientPort2181hive.cluster.delegation.token.store.classorg.apache.hadoop.hive.thrift.MemoryTokenStorehive.server2.enable.doAstruehive.server2.use.SSLfalsespark.shuffle.service.enabledtruehive.cli.print.current.dbtruehive.exec.reducers.max60hive.enable.spark.execution.enginetrue</code></pre></div>
<br><p></p>
<p><strong><span style="font-size:24px;">     以上已经测试通过，可以根据用户需要做改变。</span></strong></p>
<p><strong><span style="font-size:24px;">     如有疑问、请留言！不足之处，请指正！谢谢!</span></strong></p>
<p><br></p>
<p><br></p>
<p><span style="font-family:'宋体';"><br></span></p>
            </div>
                </div>