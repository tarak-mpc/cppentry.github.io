---
layout:     post
title:      SparkCore编程（一）-分组取topN
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_41851454/article/details/80341383				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h5>1、文件数据</h5><pre><code class="language-java">Spark,100
Hadoop,62
Flink,77
Kafka,91
Hadoop,93
Spark,78
Hadoop,69
Spark,98
Hadoop,62
Spark,99
Hadoop,61
Spark,70
Hadoop,75
Spark,88
Hadoop,68
Spark,90
Hadoop,61</code></pre><h5>2、Scala代码：</h5><pre><code class="language-java">package topN

import org.apache.spark.rdd.RDD
import org.apache.spark.{SparkConf, SparkContext}

object GroupTop {
  def main(args: Array[String]): Unit = {

    /**
      * 判断参数的多少
      */

    if (args.length &lt; 2){
      println(
        """
          |topN.GroupTop&lt;inputPath&gt;&lt;outputPath&gt;
          |&lt;inputPath&gt; 文件输入目录
          |&lt;outputPath&gt; 输出目录
        """.stripMargin
      )
      System.exit(0)
    }
    /**
      * 接收参数
      */


    val Array(inputPath,outputPath) = args
    /**
      * 初始化程序入口
      */
    val conf = new SparkConf()
    conf.setAppName(s"${this.getClass.getSimpleName}")
    conf.setMaster("local")
    conf.set("spark.serializer","org.apache.spark.serializer.KryoSerializer")

    /**
      * 计算topN
      */
    val sc = new SparkContext(conf)
    val lines: RDD[String] = sc.textFile(inputPath)
    //拆分为Tuple2
    val tupleRDD: RDD[(String, Int)] = lines.map(line =&gt; {
      (line.split(",")(0), line.split(",")(1).toInt)
    })
    //分组
    val groutRDD: RDD[(String, Iterable[Int])] = tupleRDD.groupByKey()
    //针对分组对value排序,返回Tuple2
    val groupSort: RDD[(String, List[Int])] = groutRDD.map(grouped =&gt; {
      (grouped._1, grouped._2.toList.sortWith(_ &gt; _).take(5))//升序，取Top3
    })
    //遍历输出
    groupSort.sortByKey().collect().foreach(pair =&gt; {
      println(pair._1+":")
      pair._2.foreach(s =&gt; println(s + "\t"))
    })

    sc.stop()
  }
}</code></pre><p>运行结果：</p><pre><code class="language-plain">Flink:
77	
Hadoop:
93	
75	
69	
68	
62	
Kafka:
91	
Spark:
100	
99	
98	
90	
88	</code></pre><h5>3、Java代码：</h5><pre><code class="language-java">package topN;

import org.apache.spark.SparkConf;
import org.apache.spark.api.java.JavaPairRDD;
import org.apache.spark.api.java.JavaRDD;
import org.apache.spark.api.java.JavaSparkContext;
import org.apache.spark.api.java.function.PairFunction;
import org.apache.spark.api.java.function.VoidFunction;
import org.apache.spark.rdd.RDD;
import scala.Tuple2;

import java.util.Comparator;
import java.util.Iterator;


public class GroupTopN {
    public static void main(String[] args) {

        /**
         * 判断参数的个数
         */
        if (args.length &gt; 2){
            System.out.println("topN.GroupTopN" +
                    "need two parameter &lt;inputPath&gt;&lt;outputPath&gt; \n" +
                    "&lt;inputPath&gt; 输入路径\n" +
                    "&lt;outputPath&gt; 输出路径");
            System.exit(0);
        }
        /**
         * 接收参数
         */
        String inputPath = args[0];
        String outputPath = args[1];
        SparkConf conf = new SparkConf().setAppName("topN.GroupTopN").setMaster("local");;
        JavaSparkContext sc = new JavaSparkContext(conf);
        JavaRDD&lt;String&gt; lines = sc.textFile(inputPath);
        
        //拆分为JavaPairRDD代码 匿名内部类
        JavaPairRDD&lt;String, Integer&gt; cs = lines.mapToPair(new PairFunction&lt;String, String, Integer&gt;() {

            @Override
            public Tuple2&lt;String, Integer&gt; call(String s) throws Exception {
                return new Tuple2(s.split(",")[0],Integer.valueOf(s.split(",")[1]));
            }
        });
        //拆分为JavaPairRDD代码 用lamda表达式
//        JavaPairRDD cs = lines.mapToPair((PairFunction) s -&gt; new Tuple2(s.toString().split(",")[0],Integer.valueOf(s.toString().split(",")[1])));

        //根据Key分组
        JavaPairRDD&lt;String, Iterable&lt;Integer&gt;&gt; csPairsRDD = cs.groupByKey();
        //根据Key排序，降序
        JavaPairRDD&lt;String, Iterable&lt;Integer&gt;&gt; sortbykey = csPairsRDD.sortByKey();

        //遍历取出Top3
        sortbykey.foreach(new VoidFunction&lt;Tuple2&lt;String, Iterable&lt;Integer&gt;&gt;&gt;() {
            @Override
            public void call(Tuple2&lt;String, Iterable&lt;Integer&gt;&gt; csPair) throws Exception {
                String name = csPair._1();
                Iterator&lt;Integer&gt; ite = csPair._2().iterator();
                Integer[] res = new Integer[3];
                //排序，取出Top3
                while (ite.hasNext()){
                    Integer score = ite.next();
                    for (int i = 0; i &lt; 3; i++){
                        if (res[i] == null){
                            res[i] = score;
                            break;
                        } else if (res[i] &lt; score){
                            for (int j= 2 ; j &gt; i; j--){
                                res[i] = res[j - 1];
                            }
                            res[i] = score;
                            break;
                        }
                    }
                }
                System.out.println(name+":");
                for (int i = 0; i &lt; res.length; i++){
                    System.out.println(res[i] + "\t");
                }
                System.out.println();
            }


        });
        sc.close();
    }

}</code></pre><p>运行结果：</p><pre><code class="language-java">Flink:
77	
null	
null	

Hadoop:
93	
75	
68	

Kafka:
91	
null	
null	

Spark:
100	
99	
90	</code></pre><p><br></p>            </div>
                </div>