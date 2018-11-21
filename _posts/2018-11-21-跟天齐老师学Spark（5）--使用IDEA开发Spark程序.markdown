---
layout:     post
title:      跟天齐老师学Spark（5）--使用IDEA开发Spark程序
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/xbs1019/article/details/54898143				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
使用IDEA开发spark程序:<br><br>
补全的快捷键："Ctrl+Alt+v"<br>
下面是提交spark程序到spark集群上运行的命令：<br><pre><code class="language-python">spark/bin/spark-submit \
--master spark://hadoop01:7077,spark://hadoop02:7077 \
--executor-memory 512m --total-executor-cores 7 \
--class cn.itcast.spark.WordCount \
/root/spark-1.0-SNAPSHOT.jar \
hdfs://hadoop01:9000/wc \
hdfs://hadoop01:9000/wc/out</code></pre><br>
后面会给大家介绍如何在IDEA中既可以编译java程序，又可以编译scala程序，这就需要两个插件。<br>
其实在打包的时候，不用在pom文件制定main方法的全类名，因为我们可能会写很多的程序，<br>
我们可以在它执行的时候，动态的告诉它调哪个main方法。<br><br>
---使用java来开发一个简单spark的wordcount程序----------------------------------------------------------------<br><pre><code class="language-plain">import org.apache.spark.SparkConf;
import org.apache.spark.api.java.JavaPairRDD;
import org.apache.spark.api.java.JavaRDD;
import org.apache.spark.api.java.JavaSparkContext;
import org.apache.spark.api.java.function.FlatMapFunction;
import org.apache.spark.api.java.function.Function2;
import org.apache.spark.api.java.function.PairFunction;
import scala.Int;
import scala.Tuple2;

import java.util.Arrays;

/**
 * Created by SYJ on 2016/10/22.
 */
public class JavaWordCount {
    public static void main(String[] args) {
        SparkConf sparkConf = new SparkConf().setAppName("JavaWordCount");
        JavaSparkContext context = new JavaSparkContext(sparkConf);
        JavaRDD&lt;String&gt; lines = context.textFile(args[0]);
        JavaRDD&lt;String&gt; words = lines.flatMap(new FlatMapFunction&lt;String, String&gt;() {
            @Override
            public Iterable&lt;String&gt; call(String line) throws Exception {
                return Arrays.asList(line.split(" "));
            }
        });
        JavaPairRDD&lt;String, Integer&gt; wordAndOne = words.mapToPair(new PairFunction&lt;String, String, Integer&gt;() {
            @Override
            public Tuple2&lt;String, Integer&gt; call(String word) throws Exception {
                return new Tuple2&lt;String, Integer&gt;(word, 1);
            }
        });

        JavaPairRDD&lt;String, Integer&gt; result = wordAndOne.reduceByKey(new Function2&lt;Integer, Integer, Integer&gt;() {
            @Override
            public Integer call(Integer i1, Integer i2) throws Exception {
                return i1 + i2;
            }
        });

        JavaPairRDD&lt;Integer, String&gt; swapedPair = result.mapToPair(new PairFunction&lt;Tuple2&lt;String, Integer&gt;, Integer, String&gt;() {
            @Override
            public Tuple2&lt;Integer, String&gt; call(Tuple2&lt;String, Integer&gt; tp) throws Exception {
                return new Tuple2&lt;Integer, String&gt;(tp._2, tp._1);
            }
        });

        JavaPairRDD&lt;String, Integer&gt; finalResult = swapedPair.sortByKey(false).mapToPair(new PairFunction&lt;Tuple2&lt;Integer, String&gt;, String, Integer&gt;() {
            @Override
            public Tuple2&lt;String, Integer&gt; call(Tuple2&lt;Integer, String&gt; tp) throws Exception {
                return tp.swap();
            }
        });

        finalResult.saveAsTextFile(args[1]);
        context.stop();
    }
}</code></pre><br><br>            </div>
                </div>