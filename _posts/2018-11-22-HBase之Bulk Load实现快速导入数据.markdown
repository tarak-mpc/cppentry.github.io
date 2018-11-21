---
layout:     post
title:      HBase之Bulk Load实现快速导入数据
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><img src="https://img-blog.csdn.net/20151019155021393" alt=""></p>
<h2><span style="font-family:SimHei;">  <span style="background-color:rgb(255,255,102);">
目录：</span></span></h2>
<div><span style="font-family:SimHei;">    <span style="font-size:14px;"> 【1】传统的HTableOutputFormat写HBase有什么问题？</span></span></div>
<div><span style="font-family:SimHei;"><span style="font-size:14px;">    【2】BulkLoad的流程与实现？</span></span></div>
<div><span style="font-family:SimHei;"><span style="font-size:14px;">    【3】说明</span></span></div>
<div><span style="font-family:SimHei;"><span style="font-size:14px;"><span style="font-family:SimHei;font-size:14px;"> 【1】传统的HTableOutputFormat写HBase有什么问题？</span><br></span></span></div>
<div><span style="font-family:SimHei;"><span style="font-size:14px;"><span style="font-family:SimHei;font-size:14px;">通常的Mapreduce在写入HBase时使用的是TableOutputFormat方式，在<span style="color:#ff0000;"><strong>Reduce</strong></span>中直接生成Put对象写入到HBase。</span></span></span></div>
<div><span style="font-family:SimHei;"><span style="font-size:14px;"><span style="font-family:SimHei;font-size:14px;">该方式的不足：在大数据量些入时效率低下，因为HBase会block写入，频繁进行flush，split和compact等大量IO操作），并对HBase节点的稳定性造成一定的影响（GC时间过长，响应变慢，导致节点超时退出，并引起一系列连锁反应）。</span></span></span></div>
<div><span style="font-family:SimHei;"><span style="font-size:14px;"><span style="font-family:SimHei;font-size:14px;">BulkLoad入库方式。</span></span></span></div>
<div><span style="font-family:SimHei;"><span style="font-size:14px;"><span style="font-family:SimHei;font-size:14px;">原理：利用HBase的数据信息按照特定的格式存储在 hdfs内，直接在HDFS中生成持久化的HFile数据格式文件，然后上传至合适的位置。</span></span></span></div>
<div><span style="font-family:SimHei;font-size:14px;">优点：配合mapreduce高效便捷，不占用region资源，增添负载，大数据量写入时效率高，降低了对HBase节点的写入压力。</span></div>
<div><span style="font-family:SimHei;font-size:14px;">相比较：</span></div>
<div><span style="font-family:SimHei;font-size:14px;">（1）消除了对Hbase集群的插入压力</span></div>
<div><span style="font-family:SimHei;font-size:14px;">（2）提高了Job的运行速度，降低了Job的执行时间</span></div>
<div><span style="font-family:SimHei;font-size:14px;">不足：目前BulkLoad只适用于只有一个列族的情况，在新版本的HBase中，但列族的限制会消除。</span></div>
<div><span style="font-family:SimHei;font-size:14px;"><span style="font-family:SimHei;font-size:14px;">【2】BulkLoad的流程与实现？</span><br></span></div>
<div><span style="font-family:SimHei;font-size:14px;"><span style="font-family:SimHei;font-size:14px;">需要两个Job配合完成：</span></span></div>
<div><span style="font-family:SimHei;font-size:14px;"><span style="font-family:SimHei;font-size:14px;">（1）第一个Job运行原来业务处理逻辑，处理的结果不直接调用HTableOutputFormat写入到HBase，而是写入到HDFS上面。</span></span></div>
<div><span style="font-family:SimHei;font-size:14px;"><span style="font-family:SimHei;font-size:14px;">（2）第二个Job以第一个Job写入到HDFS上的数据作为输入，然后将其格式化为Hbase的底层存储文件HFile。</span></span></div>
<div><span style="font-family:SimHei;font-size:14px;"><span style="font-family:SimHei;font-size:14px;">（3）调用BulkLoad将第二个Job生成的HFile导入到相应的HBase表中。</span></span></div>
<div><span style="font-family:SimHei;font-size:14px;"><span style="font-family:SimHei;font-size:14px;"><img src="" alt=""><br></span></span></div>
<div><span style="font-family:SimHei;font-size:14px;"><span style="font-family:SimHei;font-size:14px;">代码示例：</span></span></div>
<div><span style="font-family:SimHei;font-size:14px;"></span>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>001</code></td>
<td><code>import</code> <code>java.io.IOException;</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>002</code></td>
<td> </td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>003</code></td>
<td><code>import</code> <code>org.apache.hadoop.conf.Configuration;</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>004</code></td>
<td><code>import</code> <code>org.apache.hadoop.fs.Path;</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>005</code></td>
<td><code>import</code> <code>org.apache.hadoop.hbase.HBaseConfiguration;</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>006</code></td>
<td><code>import</code> <code>org.apache.hadoop.hbase.KeyValue;</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>007</code></td>
<td><code>import</code> <code>org.apache.hadoop.hbase.client.HTable;</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>008</code></td>
<td><code>import</code> <code>org.apache.hadoop.hbase.client.Put;</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>009</code></td>
<td><code>import</code> <code>org.apache.hadoop.hbase.io.ImmutableBytesWritable;</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>010</code></td>
<td><code>import</code> <code>org.apache.hadoop.hbase.mapreduce.HFileOutputFormat;</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>011</code></td>
<td><code>import</code> <code>org.apache.hadoop.hbase.mapreduce.KeyValueSortReducer;</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>012</code></td>
<td><code>import</code> <code>org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles;</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>013</code></td>
<td><code>import</code> <code>org.apache.hadoop.hbase.util.Bytes;</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>014</code></td>
<td><code>import</code> <code>org.apache.hadoop.io.IntWritable;</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>015</code></td>
<td><code>import</code> <code>org.apache.hadoop.io.LongWritable;</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>016</code></td>
<td><code>import</code> <code>org.apache.hadoop.io.Text;</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>017</code></td>
<td><code>import</code> <code>org.apache.hadoop.mapreduce.Job;</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>018</code></td>
<td><code>import</code> <code>org.apache.hadoop.mapreduce.Mapper;</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>019</code></td>
<td><code>import</code> <code>org.apache.hadoop.mapreduce.Reducer;</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>020</code></td>
<td><code>import</code> <code>org.apache.hadoop.mapreduce.lib.input.FileInputFormat;</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>021</code></td>
<td><code>import</code> <code>org.apache.hadoop.mapreduce.lib.input.TextInputFormat;</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>022</code></td>
<td><code>import</code> <code>org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>023</code></td>
<td><code>import</code> <code>org.apache.hadoop.mapreduce.lib.output.TextOutputFormat;</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>024</code></td>
<td><code>import</code> <code>org.apache.hadoop.util.GenericOptionsParser;</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>026</code></td>
<td><code>public</code> <code>class</code> <code>GeneratePutHFileAndBulkLoadToHBase {</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>028</code></td>
<td><code>    </code><code>public</code> <code>static</code> <code>class</code> <code>WordCountMapper </code><code>extends</code> <code>Mapper&lt;LongWritable, Text, Text, IntWritable&gt;</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>029</code></td>
<td><code>    </code><code>{</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>031</code></td>
<td><code>        </code><code>private</code> <code>Text wordText=</code><code>new</code> <code>Text();</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>032</code></td>
<td><code>        </code><code>private</code> <code>IntWritable one=</code><code>new</code> <code>IntWritable(</code><code>1</code><code>);</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>033</code></td>
<td><code>        </code><code>@Override</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>034</code></td>
<td><code>        </code><code>protected</code> <code>void</code> <code>map(LongWritable key, Text value, Context context)</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>035</code></td>
<td><code>                </code><code>throws</code> <code>IOException, InterruptedException {</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>036</code></td>
<td><code>            </code><code>// TODO Auto-generated method stub</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>037</code></td>
<td><code>            </code><code>String line=value.toString();</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>038</code></td>
<td><code>            </code><code>String[] wordArray=line.split(</code><code>" "</code><code>);</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>039</code></td>
<td><code>            </code><code>for</code><code>(String word:wordArray)</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>040</code></td>
<td><code>            </code><code>{</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>041</code></td>
<td><code>                </code><code>wordText.set(word);</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>042</code></td>
<td><code>                </code><code>context.write(wordText, one);</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>043</code></td>
<td><code>            </code><code>}</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>044</code></td>
<td><code>            </code> </td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>045</code></td>
<td><code>        </code><code>}</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>046</code></td>
<td><code>    </code><code>}</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>048</code></td>
<td><code>    </code><code>public</code> <code>static</code> <code>class</code> <code>WordCountReducer </code><code>extends</code> <code>Reducer&lt;Text, IntWritable, Text, IntWritable&gt;</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>049</code></td>
<td><code>    </code><code>{</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>051</code></td>
<td><code>        </code><code>private</code> <code>IntWritable result=</code><code>new</code> <code>IntWritable();</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>052</code></td>
<td><code>        </code><code>protected</code> <code>void</code> <code>reduce(Text key, Iterable&lt;IntWritable&gt; valueList,</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>053</code></td>
<td><code>                </code><code>Context context)</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>054</code></td>
<td><code>                </code><code>throws</code> <code>IOException, InterruptedException {</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>055</code></td>
<td><code>            </code><code>// TODO Auto-generated method stub</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>056</code></td>
<td><code>            </code><code>int</code> <code>sum=</code><code>0</code><code>;</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>057</code></td>
<td><code>            </code><code>for</code><code>(IntWritable value:valueList)</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>058</code></td>
<td><code>            </code><code>{</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>059</code></td>
<td><code>                </code><code>sum+=value.get();</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>060</code></td>
<td><code>            </code><code>}</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>061</code></td>
<td><code>            </code><code>result.set(sum);</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>062</code></td>
<td><code>            </code><code>context.write(key, result);</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>063</code></td>
<td><code>        </code><code>}</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>065</code></td>
<td><code>    </code><code>}</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>067</code></td>
<td><code>    </code><code>public</code> <code>static</code> <code>class</code> <code>ConvertWordCountOutToHFileMapper </code><code>extends</code> <code>Mapper&lt;LongWritable, Text, ImmutableBytesWritable, Put&gt;</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;">
<table style="font-family:Arial;"><tbody><tr><td><code>068</code></td>
<td><code>            </code><code>{</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>070</code></td>
<td><code>        </code><code>@Override</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>071</code></td>
<td><code>        </code><code>protected</code> <code>void</code> <code>map(LongWritable key, Text value, Context context)</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>072</code></td>
<td><code>                </code><code>throws</code> <code>IOException, InterruptedException {</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>073</code></td>
<td><code>            </code><code>// TODO Auto-generated method stub</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>074</code></td>
<td><code>            </code><code>String wordCountStr=value.toString();</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>075</code></td>
<td><code>            </code><code>String[] wordCountArray=wordCountStr.split(</code><code>"\t"</code><code>);</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>076</code></td>
<td><code>            </code><code>String word=wordCountArray[</code><code>0</code><code>];</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>077</code></td>
<td><code>            </code><code>int</code> <code>count=Integer.valueOf(wordCountArray[</code><code>1</code><code>]);</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>079</code></td>
<td><code>            </code><code>//创建HBase中的RowKey</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>080</code></td>
<td><code>            </code><code>byte</code><code>[] rowKey=Bytes.toBytes(word);</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>081</code></td>
<td><code>            </code><code>ImmutableBytesWritable rowKeyWritable=</code><code>new</code> <code>ImmutableBytesWritable(rowKey);</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>082</code></td>
<td><code>            </code><code>byte</code><code>[] family=Bytes.toBytes(</code><code>"cf"</code><code>);</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>083</code></td>
<td><code>            </code><code>byte</code><code>[] qualifier=Bytes.toBytes(</code><code>"count"</code><code>);</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>084</code></td>
<td><code>            </code><code>byte</code><code>[] hbaseValue=Bytes.toBytes(count);</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>085</code></td>
<td><code>            </code><code>// Put 用于列簇下的多列提交，若只有一个列，则可以使用 KeyValue 格式</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>086</code></td>
<td><code>            </code><code>// KeyValue keyValue = new KeyValue(rowKey, family, qualifier, hbaseValue);</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>087</code></td>
<td><code>            </code><code>Put put=</code><code>new</code> <code>Put(rowKey);</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>088</code></td>
<td><code>            </code><code>put.add(family, qualifier, hbaseValue);</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>089</code></td>
<td><code>            </code><code>context.write(rowKeyWritable, put);</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>091</code></td>
<td><code>        </code><code>}</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>093</code></td>
<td><code>    </code><code>}</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>095</code></td>
<td><code>    </code><code>public</code> <code>static</code> <code>void</code> <code>main(String[] args) </code><code>throws</code> <code>Exception {</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>096</code></td>
<td><code>        </code><code>// TODO Auto-generated method stub</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>097</code></td>
<td><code>        </code><code>Configuration hadoopConfiguration=</code><code>new</code> <code>Configuration();</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>098</code></td>
<td><code>        </code><code>String[] dfsArgs = </code><code>new</code> <code>GenericOptionsParser(hadoopConfiguration, args).getRemainingArgs();</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>100</code></td>
<td><code>        </code><code>//第一个Job就是普通MR，输出到指定的目录</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>101</code></td>
<td><code>        </code><code>Job job=</code><code>new</code> <code>Job(hadoopConfiguration, </code><code>"wordCountJob"</code><code>);</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>102</code></td>
<td><code>        </code><code>job.setJarByClass(GeneratePutHFileAndBulkLoadToHBase.</code><code>class</code><code>);</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>103</code></td>
<td><code>        </code><code>job.setMapperClass(WordCountMapper.</code><code>class</code><code>);</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>104</code></td>
<td><code>        </code><code>job.setReducerClass(WordCountReducer.</code><code>class</code><code>);</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>105</code></td>
<td><code>        </code><code>job.setOutputKeyClass(Text.</code><code>class</code><code>);</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>106</code></td>
<td><code>        </code><code>job.setOutputValueClass(IntWritable.</code><code>class</code><code>);</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>107</code></td>
<td><code>        </code><code>FileInputFormat.setInputPaths(job, </code><code>new</code> <code>Path(dfsArgs[</code><code>0</code><code>]));</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>108</code></td>
<td><code>        </code><code>FileOutputFormat.setOutputPath(job, </code><code>new</code> <code>Path(dfsArgs[</code><code>1</code><code>]));</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>109</code></td>
<td><code>        </code><code>//提交第一个Job</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>110</code></td>
<td><code>        </code><code>int</code> <code>wordCountJobResult=job.waitForCompletion(</code><code>true</code><code>)?</code><code>0</code><code>:</code><code>1</code><code>;</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>112</code></td>
<td><code>        </code><code>//第二个Job以第一个Job的输出做为输入，只需要编写Mapper类，在Mapper类中对一个job的输出进行分析，并转换为HBase需要的KeyValue的方式。</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>113</code></td>
<td><code>        </code><code>Job convertWordCountJobOutputToHFileJob=</code><code>new</code> <code>Job(hadoopConfiguration, </code><code>"wordCount_bulkload"</code><code>);</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>115</code></td>
<td><code>        </code><code>convertWordCountJobOutputToHFileJob.setJarByClass(GeneratePutHFileAndBulkLoadToHBase.</code><code>class</code><code>);</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>116</code></td>
<td><code>        </code><code>convertWordCountJobOutputToHFileJob.setMapperClass(ConvertWordCountOutToHFileMapper.</code><code>class</code><code>);</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>117</code></td>
<td><code>        </code><code>//ReducerClass 无需指定，框架会自行根据 MapOutputValueClass 来决定是使用 KeyValueSortReducer 还是 PutSortReducer</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>118</code></td>
<td><code>        </code><code>//convertWordCountJobOutputToHFileJob.setReducerClass(KeyValueSortReducer.class);</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>119</code></td>
<td><code>        </code><code>convertWordCountJobOutputToHFileJob.setMapOutputKeyClass(ImmutableBytesWritable.</code><code>class</code><code>);</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>120</code></td>
<td><code>        </code><code>convertWordCountJobOutputToHFileJob.setMapOutputValueClass(Put.</code><code>class</code><code>);</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>122</code></td>
<td><code>        </code><code>//以第一个Job的输出做为第二个Job的输入</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>123</code></td>
<td><code>        </code><code>FileInputFormat.addInputPath(convertWordCountJobOutputToHFileJob, </code><code>new</code> <code>Path(dfsArgs[</code><code>1</code><code>]));</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>124</code></td>
<td><code>        </code><code>FileOutputFormat.setOutputPath(convertWordCountJobOutputToHFileJob, </code><code>new</code> <code>Path(dfsArgs[</code><code>2</code><code>]));</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>125</code></td>
<td><code>        </code><code>//创建HBase的配置对象</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>126</code></td>
<td><code>        </code><code>Configuration hbaseConfiguration=HBaseConfiguration.create();</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>127</code></td>
<td><code>        </code><code>//创建目标表对象</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>128</code></td>
<td><code>        </code><code>HTable wordCountTable =</code><code>new</code> <code>HTable(hbaseConfiguration, </code><code>"word_count"</code><code>);</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>129</code></td>
<td><code>        </code><code>HFileOutputFormat.configureIncrementalLoad(convertWordCountJobOutputToHFileJob,wordCountTable);</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>130</code></td>
<td><code>       </code> </td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>131</code></td>
<td><code>        </code><code>//提交第二个job</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>132</code></td>
<td><code>        </code><code>int</code> <code>convertWordCountJobOutputToHFileJobResult=convertWordCountJobOutputToHFileJob.waitForCompletion(</code><code>true</code><code>)?</code><code>0</code><code>:</code><code>1</code><code>;</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>134</code></td>
<td><code>        </code><code>//当第二个job结束之后，调用BulkLoad方式来将MR结果批量入库</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>135</code></td>
<td><code>        </code><code>LoadIncrementalHFiles loader = </code><code>new</code> <code>LoadIncrementalHFiles(hbaseConfiguration);</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>136</code></td>
<td><code>        </code><code>//第一个参数为第二个Job的输出目录即保存HFile的目录，第二个参数为目标表</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>137</code></td>
<td><code>        </code><code>loader.doBulkLoad(</code><code>new</code> <code>Path(dfsArgs[</code><code>2</code><code>]), wordCountTable);</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>139</code></td>
<td><code>        </code><code>//最后调用System.exit进行退出</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>140</code></td>
<td><code>        </code><code>System.exit(convertWordCountJobOutputToHFileJobResult);</code></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">
<table><tbody><tr><td><code>142</code></td>
<td><code>    </code><code>}</code></td>
</tr></tbody></table></div>
<div style="color:rgb(51,51,51);line-height:26px;">
<table style="font-family:Arial;font-size:14px;"><tbody><tr><td><code>144</code></td>
<td><code>}<br></code></td>
</tr></tbody></table><p style="color:rgb(51,51,51);line-height:26px;">比如原始的输入数据的目录为：/rawdata/test/wordcount/20131212 </p>
<span style="color:rgb(51,51,51);line-height:26px;">中间结果数据保存的目录为：/middata/test/wordcount/20131212 </span><br style="color:rgb(51,51,51);line-height:26px;"><span style="color:rgb(51,51,51);line-height:26px;">最终生成的HFile保存的目录为：/resultdata/test/wordcount/20131212 </span><br style="color:rgb(51,51,51);line-height:26px;"><span style="color:rgb(51,51,51);line-height:26px;">运行上面的Job的方式如下： </span><br style="color:rgb(51,51,51);line-height:26px;"><span style="color:rgb(51,51,51);line-height:26px;">hadoop jar test.jar /rawdata/test/wordcount/20131212 /middata/test/wordcount/20131212 /resultdata/test/wordcount/20131212 </span><br></div>
</div>
<div><span style="font-family:SimHei;font-size:14px;"><span style="font-family:SimHei;font-size:14px;">【3】说明</span></span></div>
<div><span style="font-family:SimHei;font-size:14px;"><span style="font-family:SimHei;font-size:14px;">（1）HFile方式在所有的加载方案里面是最快的，前提是：表是空的。如果表中已经有了数据，HFile再导入到HBase的表中会触发Split操作。</span></span></div>
<div><span style="font-family:SimHei;font-size:14px;"><span style="font-family:SimHei;font-size:14px;">（2）最终的输出结果。</span></span></div>
<div><span style="font-family:SimHei;font-size:14px;"><span style="font-family:SimHei;font-size:14px;">无论是map还是reduce，输出部分key和value的类型是：&lt;ImmutableBytesWritable,keyvalue&gt;或者&lt;ImmutableBytesWritable,Put&gt;.</span></span></div>
<div><span style="font-size:14px;"><span style="color:rgb(51,51,51);line-height:26px;"><span style="font-family:SimHei;">（3）最终输出部分，Value类型是KeyValue 或Put，对应的Sorter分别是KeyValueSortReducer或PutSortReducer，这个 SorterReducer 可以不指定，因为源码中已经做了判断</span></span><br></span></div>
<div><span style="font-family:SimHei;font-size:14px;">（4)MR例子中Job.setOutputFormatClass（HFileOutputFormat.class)；<span style="font-family:SimHei;font-size:14px;">H</span><span style="font-family:SimHei;font-size:14px;">FileOutputFormat只适合一次对但列族组织成HFile文件，多列簇需要多个Job，不过新版本的HBase已经解决了这个限制。</span></span></div>
<div><span style="font-family:SimHei;font-size:14px;"><span style="font-family:SimHei;font-size:14px;">（5）MR例子中最后生成HFile存储在HDFS<span style="font-family:SimHei;font-size:14px;">上，输出路径下的子目录是各个列族。如果对HFile进行入库HBase，相当于move HFile到HBase的Region中，HFile子目录的列族内容就没有了。</span></span></span></div>
<div><span style="font-family:SimHei;font-size:14px;"><span style="font-family:SimHei;font-size:14px;"><span style="font-family:SimHei;font-size:14px;">（6）最后一个Reduce没有setNumReduceTasks是因为，该设置由框架根据region个数自动配置。</span></span></span></div>
<div><span style="font-family:SimHei;font-size:14px;"><span style="font-family:SimHei;font-size:14px;"><br></span></span></div>
<div><span style="font-family:SimHei;font-size:14px;"><span style="font-family:SimHei;font-size:14px;"><br></span></span></div>
<div><span style="font-family:SimHei;font-size:14px;"><span style="font-family:SimHei;font-size:14px;"><br></span></span></div>
<div><span style="font-family:SimHei;font-size:14px;"><span style="font-family:SimHei;font-size:14px;"><br></span></span></div>
<div><span style="font-family:SimHei;font-size:14px;"><span style="font-family:SimHei;font-size:14px;"><br></span></span></div>
            </div>
                </div>