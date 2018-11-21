---
layout:     post
title:      新装的hadoop2版本无法运行mapreduce的解决方法
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，请尊重劳动成果，觉得不错就在文章下方顶一下呗，转载请标明原地址。					https://blog.csdn.net/m0_37739193/article/details/71132652				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                在hadoop用户下执行<span style="color:#3333ff;"><strong>hadoop classpath</strong></span>命令，我们可以得到运行 Hadoop 程序所需的全部 classpath 信息。<br>然后vi .bashrc（Debian版本，Redhat版本下是.bash_profile文件）添加：<br>export CLASSPATH=.:/home/hadoop/hadoop-2.6.0-cdh5.5.2/etc/hadoop:/home/hadoop/hadoop-2.6.0-cdh5.5.2/share/hadoop/common/lib/*:/home/hadoop/hadoop-2.6.0-cdh5.5.2/share/hadoop/common/*:/home/hadoop/hadoop-2.6.0-cdh5.5.2/share/hadoop/hdfs:/home/hadoop/hadoop-2.6.0-cdh5.5.2/share/hadoop/hdfs/lib/*:/home/hadoop/hadoop-2.6.0-cdh5.5.2/share/hadoop/hdfs/*:/home/hadoop/hadoop-2.6.0-cdh5.5.2/share/hadoop/yarn/lib/*:/home/hadoop/hadoop-2.6.0-cdh5.5.2/share/hadoop/yarn/*:/home/hadoop/hadoop-2.6.0-cdh5.5.2/share/hadoop/mapreduce/lib/*:/home/hadoop/hadoop-2.6.0-cdh5.5.2/share/hadoop/mapreduce/*:/home/hadoop/hadoop-2.6.0-cdh5.5.2/contrib/capacity-scheduler/*.jar<br><p>即可解决</p><p><br></p><p>使配置文件生效命令：</p>source .bashrc<br><br>运行一个简单的mr:<br>[hadoop@h40 ~]$ vi WordCount.java<br><pre><code class="language-java">import java.io.IOException;
import java.util.Iterator;
import java.util.StringTokenizer;

import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable; 
import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.Text; 
import org.apache.hadoop.mapred.FileInputFormat;
import org.apache.hadoop.mapred.FileOutputFormat;
import org.apache.hadoop.mapred.JobClient;
import org.apache.hadoop.mapred.JobConf; 
import org.apache.hadoop.mapred.MapReduceBase;
import org.apache.hadoop.mapred.Mapper;
import org.apache.hadoop.mapred.OutputCollector;
import org.apache.hadoop.mapred.Reducer;
import org.apache.hadoop.mapred.Reporter;
import org.apache.hadoop.mapred.TextInputFormat;
import org.apache.hadoop.mapred.TextOutputFormat;

public class WordCount {

    public static class Map extends MapReduceBase implements
            Mapper&lt;LongWritable, Text, Text, IntWritable&gt; {
        private final static IntWritable one = new IntWritable(1);
        private Text word = new Text();

        public void map(LongWritable key, Text value,
                OutputCollector&lt;Text, IntWritable&gt; output, Reporter reporter)
                throws IOException {
            String line = value.toString();
            StringTokenizer tokenizer = new StringTokenizer(line);
            while (tokenizer.hasMoreTokens()) {
                word.set(tokenizer.nextToken());
                output.collect(word, one);
            }
        }
    }

    public static class Reduce extends MapReduceBase implements
            Reducer&lt;Text, IntWritable, Text, IntWritable&gt; {
        public void reduce(Text key, Iterator&lt;IntWritable&gt; values,
                OutputCollector&lt;Text, IntWritable&gt; output, Reporter reporter)
                throws IOException {
            int sum = 0;
            while (values.hasNext()) {
                sum += values.next().get();
            }
            output.collect(key, new IntWritable(sum));
        }
    }

    public static void main(String[] args) throws Exception {
        JobConf conf = new JobConf(WordCount.class);
        conf.setJobName("wordcount");

        conf.setOutputKeyClass(Text.class);
        conf.setOutputValueClass(IntWritable.class);

        conf.setMapperClass(Map.class);
        conf.setCombinerClass(Reduce.class);
        conf.setReducerClass(Reduce.class);

        conf.setInputFormat(TextInputFormat.class);
        conf.setOutputFormat(TextOutputFormat.class);

        FileInputFormat.setInputPaths(conf, new Path(args[0]));
        FileOutputFormat.setOutputPath(conf, new Path(args[1]));

        JobClient.runJob(conf);
    }
}</code></pre><p><span style="color:#3333ff;">运行方法一：</span></p><p>[hadoop@h40 ~]$ /usr/jdk1.7.0_25/bin/javac WordCount.java </p>（还必须在执行完source .bash_profile的窗口或者在新的窗口执行上面的命令，如果在未执行source .bash_profile命令的窗口则编译会报错）<br><p>[hadoop@h40 ~]$ /usr/jdk1.7.0_25/bin/jar cvf xx.jar WordCount*class</p><p>added manifest<br>adding: WordCount.class(in = 1516) (out= 744)(deflated 50%)<br>adding: WordCount$Map.class(in = 1918) (out= 794)(deflated 58%)<br>adding: WordCount$Reduce.class(in = 1591) (out= 642)(deflated 59%)<br></p><p><span style="color:#3333ff;">运行方法二：</span></p><p>在myeclipse中创建该项目，项目右键导出为jar文件，命名为xx.jar。将xx.jar上传到虚拟机Master主机中，虚拟机安装了VMWare Tools可以直接拖拽进行复制。<br></p><p><br></p>[hadoop@h40 ~]$ vi he.txt<br>hello world<br>hello hadoop<br>hello hive<br>[hadoop@h40 ~]$ hadoop fs -mkdir /input<br>[hadoop@h40 ~]$ hadoop fs -put he.txt /input<br>[hadoop@h40 ~]$ hadoop jar xx.jar WordCount /input/he.txt /output（这个output目录不能存在）<br><p>[hadoop@h40 ~]$ hadoop fs -cat /output/part-00000</p><p>hadoop  1<br>hello   3<br>hive    1<br>world   1<br></p><p><br></p><p><span style="color:#330000;">在网页中查看Job的具体信息，hadoop2版本中使用MapReduce JobHistory Server，http://jhs_host:port/，端口号默认为19888，地址由参数mapreduce.jobhistory.webapp.address配置管理</span><br></p><p>[hadoop@h40 ~]$ mapred historyserver<br></p><p>（在CRT端启动，自己不会退出，按Ctrl+c退出则页面也就无法加载）</p><p><img src="https://img-blog.csdn.net/20170929112507699?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbTBfMzc3MzkxOTM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p><p><br></p><p><span style="color:#cc33cc;">补充：也可以运行Hadoop自带的Wordcount程序</span></p><pre><code class="language-plain">mkdir input
echo "Hello Docker" &gt;input/file2.txt
echo "Hello Hadoop" &gt;input/file1.txt

# create input directory on HDFS
hadoop fs -mkdir -p input

# put input files to HDFS
hdfs dfs -put ./input/* input

# run wordcount 
hadoop jar $HADOOP_HOME/share/hadoop/mapreduce/sources/hadoop-mapreduce-examples-2.6.0-sources.jar org.apache.hadoop.examples.WordCount input output

# print the input files
echo -e "\ninput file1.txt:"
hdfs dfs -cat input/file1.txt

echo -e "\ninput file2.txt:"
hdfs dfs -cat input/file2.txt

# print the output of wordcount
echo -e "\nwordcount output:"
hdfs dfs -cat output/part-r-00000</code></pre><br>            </div>
                </div>