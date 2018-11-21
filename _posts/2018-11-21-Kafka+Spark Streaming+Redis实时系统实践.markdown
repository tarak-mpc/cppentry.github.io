---
layout:     post
title:      Kafka+Spark Streaming+Redis实时系统实践
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
基于<span class="wp_keywordlink_affiliate"><a target="_blank" title="" href="http://www.iteblog.com/archives/tag/spark" rel="nofollow">Spark</a></span>通用计算平台，可以很好地扩展各种计算类型的应用，尤其是<span class="wp_keywordlink_affiliate"><a target="_blank" title="" href="http://www.iteblog.com/archives/tag/spark" rel="nofollow">Spark</a></span>提供了内建的计算库支持，像<span class="wp_keywordlink_affiliate"><a target="_blank" title="" href="http://www.iteblog.com/archives/tag/spark" rel="nofollow">Spark</a></span>
 Streaming、Spark SQL、MLlib、GraphX，这些内建库都提供了高级抽象，可以用非常简洁的代码实现复杂的计算逻辑、这也得益于Scala编程语言的简洁性。这里，我们基于1.3.0版本的Spark搭建了计算平台，实现基于Spark Streaming的实时计算。
<p>　　我们的应用场景是分析用户使用手机App的行为，描述如下所示：</p>
<p>　　1、手机客户端会收集用户的行为事件（我们以点击事件为例），将数据发送到数据服务器，我们假设这里直接进入到<span class="wp_keywordlink_affiliate"><a target="_blank" title="" href="http://www.iteblog.com/archives/tag/kafka" rel="nofollow">Kafka</a></span>消息队列<br>
　　2、后端的实时服务会从<span class="wp_keywordlink_affiliate"><a target="_blank" title="" href="http://www.iteblog.com/archives/tag/kafka" rel="nofollow">Kafka</a></span>消费数据，将数据读出来并进行实时分析，这里选择Spark Streaming，因为Spark Streaming提供了与<span class="wp_keywordlink_affiliate"><a target="_blank" title="" href="http://www.iteblog.com/archives/tag/kafka" rel="nofollow">Kafka</a></span>整合的内置支持<br>
　　3、经过Spark Streaming实时计算程序分析，将结果写入<span class="wp_keywordlink_affiliate"><a target="_blank" title="" href="http://www.iteblog.com/archives/tag/redis" rel="nofollow">Redis</a></span>，可以实时获取用户的行为数据，并可以导出进行离线综合统计分析</p>
<h2>Kafka+Spark Streaming+<span class="wp_keywordlink_affiliate"><a target="_blank" title="" href="http://www.iteblog.com/archives/tag/redis" rel="nofollow">Redis</a></span>编程实践</h2>
<p>　　下面，我们根据上面提到的应用场景，来编程实现这个实时计算应用。首先，写了一个Kafka Producer模拟程序，用来模拟向Kafka实时写入用户行为的事件数据，数据是JSON格式，示例如下：</p>
<div id="highlighter_320800" class="syntaxhighlighter  bash">
<div class="bar">
<div class="toolbar"><a target="_blank" class="item viewSource" title="查看源代码" href="http://www.iteblog.com/archives/1378#viewSource" rel="nofollow" style="width:16px; height:16px">查看源代码</a>
<div class="item copyToClipboard"></div>
<a target="_blank" class="item printSource" title="打印" href="http://www.iteblog.com/archives/1378#printSource" rel="nofollow" style="width:16px; height:16px">打印</a><a target="_blank" class="item about" title="帮助" href="http://www.iteblog.com/archives/1378#about" rel="nofollow" style="width:16px; height:16px">帮助</a></div>
</div>
<div class="lines">
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>1</code></td>
<td class="content"><code class="bash plain">{ </code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>2</code></td>
<td class="content"><code class="spaces">    </code><code class="bash string">"uid"</code><code class="bash plain">:</code><code class="bash string">"068b746ed4620d25e26055a9f804385f"</code><code class="bash plain">, </code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>3</code></td>
<td class="content"><code class="spaces">    </code><code class="bash string">"event_time"</code><code class="bash plain">:</code><code class="bash string">"1430204612405"</code><code class="bash plain">, </code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>4</code></td>
<td class="content"><code class="spaces">    </code><code class="bash string">"os_type"</code><code class="bash plain">:</code><code class="bash string">"Android"</code><code class="bash plain">, 
</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>5</code></td>
<td class="content"><code class="spaces">    </code><code class="bash string">"click_count"</code><code class="bash plain">: 6</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>6</code></td>
<td class="content"><code class="bash plain">}</code></td>
</tr>
</tbody>
</table>
</div>
</div>
</div>
<p>一个事件包含4个字段：<br>
　　1、uid：用户编号<br>
　　2、event_time：事件发生时间戳<br>
　　3、os_type：手机App<span class="wp_keywordlink"><a target="_blank" title="" href="http://www.wypblog.com/archives/category/%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F" rel="nofollow">操作系统</a></span>类型<br>
　　4、click_count：点击次数<br>
下面是我们实现的代码，如下所示：</p>
<div id="highlighter_72521" class="syntaxhighlighter  scala">
<div class="bar">
<div class="toolbar"><a target="_blank" class="item viewSource" title="查看源代码" href="http://www.iteblog.com/archives/1378#viewSource" rel="nofollow" style="width:16px; height:16px">查看源代码</a>
<div class="item copyToClipboard"></div>
<a target="_blank" class="item printSource" title="打印" href="http://www.iteblog.com/archives/1378#printSource" rel="nofollow" style="width:16px; height:16px">打印</a><a target="_blank" class="item about" title="帮助" href="http://www.iteblog.com/archives/1378#about" rel="nofollow" style="width:16px; height:16px">帮助</a></div>
</div>
<div class="lines">
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>01</code></td>
<td class="content"><code class="scala keyword">package</code> <code class="scala plain">
com.iteblog.spark.streaming.utils </code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>02</code></td>
<td class="content"><code class="spaces"> </code> </td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>03</code></td>
<td class="content"><code class="scala keyword">import</code> <code class="scala plain">
java.util.Properties </code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>04</code></td>
<td class="content"><code class="scala keyword">import</code> <code class="scala plain">
scala.util.Properties </code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>05</code></td>
<td class="content"><code class="scala keyword">import</code> <code class="scala plain">
org.codehaus.jettison.json.JSONObject </code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>06</code></td>
<td class="content"><code class="scala keyword">import</code> <code class="scala plain">
kafka.javaapi.producer.Producer </code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>07</code></td>
<td class="content"><code class="scala keyword">import</code> <code class="scala plain">
kafka.producer.KeyedMessage </code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>08</code></td>
<td class="content"><code class="scala keyword">import</code> <code class="scala plain">
kafka.producer.KeyedMessage </code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>09</code></td>
<td class="content"><code class="scala keyword">import</code> <code class="scala plain">
kafka.producer.ProducerConfig </code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>10</code></td>
<td class="content"><code class="scala keyword">import</code> <code class="scala plain">
scala.util.Random </code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>11</code></td>
<td class="content"><code class="spaces"> </code> </td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>12</code></td>
<td class="content"><code class="scala keyword">object</code> <code class="scala plain">
KafkaEventProducer { </code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>13</code></td>
<td class="content"><code class="spaces">  </code> </td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>14</code></td>
<td class="content"><code class="spaces">  </code><code class="scala keyword">private</code><code class="scala keyword">val</code>
<code class="scala plain">users </code><code class="scala keyword">=</code><code class="scala plain">Array(
</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>15</code></td>
<td class="content"><code class="spaces">      </code><code class="scala string">"4A4D769EB9679C054DE81B973ED5D768"</code><code class="scala plain">,</code><code class="scala string">"8dfeb5aaafc027d89349ac9a20b3930f"</code><code class="scala plain">,</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>16</code></td>
<td class="content"><code class="spaces">      </code><code class="scala string">"011BBF43B89BFBF266C865DF0397AA71"</code><code class="scala plain">,</code><code class="scala string">"f2a8474bf7bd94f0aabbd4cdd2c06dcf"</code><code class="scala plain">,</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>17</code></td>
<td class="content"><code class="spaces">      </code><code class="scala string">"068b746ed4620d25e26055a9f804385f"</code><code class="scala plain">,</code><code class="scala string">"97edfc08311c70143401745a03a50706"</code><code class="scala plain">,</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>18</code></td>
<td class="content"><code class="spaces">      </code><code class="scala string">"d7f141563005d1b5d0d3dd30138f3f62"</code><code class="scala plain">,</code><code class="scala string">"c8ee90aade1671a21336c721512b817a"</code><code class="scala plain">,</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>19</code></td>
<td class="content"><code class="spaces">      </code><code class="scala string">"6b67c8c700427dee7552f81f3228c927"</code><code class="scala plain">,</code><code class="scala string">"a95f22eabc4fd4b580c011a3161a9d9d"</code><code class="scala plain">)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>20</code></td>
<td class="content"><code class="spaces">      </code> </td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>21</code></td>
<td class="content"><code class="spaces">  </code><code class="scala keyword">private</code><code class="scala keyword">val</code>
<code class="scala plain">random </code><code class="scala keyword">=</code><code class="scala keyword">new</code>
<code class="scala plain">Random() </code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>22</code></td>
<td class="content"><code class="spaces">      </code> </td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>23</code></td>
<td class="content"><code class="spaces">  </code><code class="scala keyword">private</code><code class="scala keyword">var</code>
<code class="scala plain">pointer </code><code class="scala keyword">=</code><code class="scala plain">-</code><code class="scala value">1</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>24</code></td>
<td class="content"><code class="spaces">  </code> </td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>25</code></td>
<td class="content"><code class="spaces">  </code><code class="scala keyword">def</code><code class="scala plain">getUserID()
</code><code class="scala keyword">:</code><code class="scala plain">String </code>
<code class="scala keyword">=</code> <code class="scala plain">{ </code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>26</code></td>
<td class="content"><code class="spaces">       </code><code class="scala plain">pointer</code><code class="scala keyword">=</code>
<code class="scala plain">pointer + </code><code class="scala value">1</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>27</code></td>
<td class="content"><code class="spaces">    </code><code class="scala keyword">if</code><code class="scala plain">(pointer &gt;</code><code class="scala keyword">=</code><code class="scala plain">users.length) {
</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>28</code></td>
<td class="content"><code class="spaces">      </code><code class="scala plain">pointer</code><code class="scala keyword">=</code>
<code class="scala value">0</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>29</code></td>
<td class="content"><code class="spaces">      </code><code class="scala plain">users(pointer)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>30</code></td>
<td class="content"><code class="spaces">    </code><code class="scala plain">} </code>
<code class="scala keyword">else</code> <code class="scala plain">{ </code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>31</code></td>
<td class="content"><code class="spaces">      </code><code class="scala plain">users(pointer)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>32</code></td>
<td class="content"><code class="spaces">    </code><code class="scala plain">} </code>
</td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>33</code></td>
<td class="content"><code class="spaces">  </code><code class="scala plain">} </code>
</td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>34</code></td>
<td class="content"><code class="spaces">  </code> </td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>35</code></td>
<td class="content"><code class="spaces">  </code><code class="scala keyword">def</code><code class="scala plain">click()
</code><code class="scala keyword">:</code> <code class="scala plain">Double </code>
<code class="scala keyword">=</code> <code class="scala plain">{ </code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>36</code></td>
<td class="content"><code class="spaces">    </code><code class="scala plain">random.nextInt(</code><code class="scala value">10</code><code class="scala plain">)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>37</code></td>
<td class="content"><code class="spaces">  </code><code class="scala plain">} </code>
</td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>38</code></td>
<td class="content"><code class="spaces">  </code> </td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>39</code></td>
<td class="content"><code class="spaces">  </code><code class="scala comments">// bin/kafka-topics.sh --zookeeper zk1:2181,zk2:2181,zk3:2181/kafka --create --topic user_events --replication-factor 2 --partitions 2</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>40</code></td>
<td class="content"><code class="spaces">  </code><code class="scala comments">// bin/kafka-topics.sh --zookeeper zk1:2181,zk2:2181,zk3:2181/kafka --list</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>41</code></td>
<td class="content"><code class="spaces">  </code><code class="scala comments">// bin/kafka-topics.sh --zookeeper zk1:2181,zk2:2181,zk3:2181/kafka --describe user_events</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>42</code></td>
<td class="content"><code class="spaces">  </code><code class="scala comments">// bin/kafka-console-consumer.sh --zookeeper zk1:2181,zk2:2181,zk3:22181/kafka --topic test_json_basis_event --from-beginning</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>43</code></td>
<td class="content"><code class="spaces">  </code><code class="scala keyword">def</code><code class="scala plain">main(args</code><code class="scala keyword">:</code>
<code class="scala plain">Array[String])</code><code class="scala keyword">:</code>
<code class="scala plain">Unit </code><code class="scala keyword">=</code> <code class="scala plain">
{ </code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>44</code></td>
<td class="content"><code class="spaces">    </code><code class="scala keyword">val</code><code class="scala plain">topic
</code><code class="scala keyword">=</code> <code class="scala string">"user_events"</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>45</code></td>
<td class="content"><code class="spaces">    </code><code class="scala keyword">val</code><code class="scala plain">brokers
</code><code class="scala keyword">=</code> <code class="scala string">"10.10.4.126:9092,10.10.4.127:9092"</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>46</code></td>
<td class="content"><code class="spaces">    </code><code class="scala keyword">val</code><code class="scala plain">props
</code><code class="scala keyword">=</code> <code class="scala keyword">new</code>
<code class="scala plain">Properties() </code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>47</code></td>
<td class="content"><code class="spaces">    </code><code class="scala plain">props.put(</code><code class="scala string">"metadata.broker.list"</code><code class="scala plain">, brokers)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>48</code></td>
<td class="content"><code class="spaces">    </code><code class="scala plain">props.put(</code><code class="scala string">"serializer.class"</code><code class="scala plain">,</code><code class="scala string">"kafka.serializer.StringEncoder"</code><code class="scala plain">)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>49</code></td>
<td class="content"><code class="spaces">    </code> </td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>50</code></td>
<td class="content"><code class="spaces">    </code><code class="scala keyword">val</code><code class="scala plain">kafkaConfig
</code><code class="scala keyword">=</code><code class="scala keyword">new</code>
<code class="scala plain">ProducerConfig(props)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>51</code></td>
<td class="content"><code class="spaces">    </code><code class="scala keyword">val</code><code class="scala plain">producer
</code><code class="scala keyword">=</code> <code class="scala keyword">new</code>
<code class="scala plain">Producer[String, String](kafkaConfig) </code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>52</code></td>
<td class="content"><code class="spaces">    </code> </td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>53</code></td>
<td class="content"><code class="spaces">    </code><code class="scala keyword">while</code><code class="scala plain">(</code><code class="scala keyword">true</code><code class="scala plain">) {</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>54</code></td>
<td class="content"><code class="spaces">      </code><code class="scala comments">// prepare event data</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>55</code></td>
<td class="content"><code class="spaces">      </code><code class="scala keyword">val</code><code class="scala plain">event
</code><code class="scala keyword">=</code> <code class="scala keyword">new</code>
<code class="scala plain">JSONObject() </code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>56</code></td>
<td class="content"><code class="spaces">      </code><code class="scala plain">event</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>57</code></td>
<td class="content"><code class="spaces">        </code><code class="scala plain">.put(</code><code class="scala string">"uid"</code><code class="scala plain">, getUserID)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>58</code></td>
<td class="content"><code class="spaces">        </code><code class="scala plain">.put(</code><code class="scala string">"event_time"</code><code class="scala plain">, System.currentTimeMillis.toString)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>59</code></td>
<td class="content"><code class="spaces">        </code><code class="scala plain">.put(</code><code class="scala string">"os_type"</code><code class="scala plain">,</code><code class="scala string">"Android"</code><code class="scala plain">)
</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>60</code></td>
<td class="content"><code class="spaces">        </code><code class="scala plain">.put(</code><code class="scala string">"click_count"</code><code class="scala plain">, click)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>61</code></td>
<td class="content"><code class="spaces">      </code> </td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>62</code></td>
<td class="content"><code class="spaces">      </code><code class="scala comments">// produce event message</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>63</code></td>
<td class="content"><code class="spaces">      </code><code class="scala plain">producer.send(</code><code class="scala keyword">new</code><code class="scala plain">KeyedMessage[String, String](topic, event.toString))
</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>64</code></td>
<td class="content"><code class="spaces">      </code><code class="scala plain">println(</code><code class="scala string">"Message sent: "</code><code class="scala plain">+ event)
</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>65</code></td>
<td class="content"><code class="spaces">      </code> </td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>66</code></td>
<td class="content"><code class="spaces">      </code><code class="scala plain">Thread.sleep(</code><code class="scala value">200</code><code class="scala plain">)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>67</code></td>
<td class="content"><code class="spaces">    </code><code class="scala plain">} </code>
</td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>68</code></td>
<td class="content"><code class="spaces">  </code><code class="scala plain">}   </code>
</td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>69</code></td>
<td class="content"><code class="scala plain">}</code></td>
</tr>
</tbody>
</table>
</div>
</div>
</div>
<p>　　通过控制上面程序最后一行的时间间隔来控制模拟写入速度。下面我们来讨论实现实时统计每个用户的点击次数，它是按照用户分组进行累加次数，逻辑比较简单，关键是在实现过程中要注意一些问题，如对象序列化等。先看实现代码，稍后我们再详细讨论，代码实现如下所示：</p>
<div id="highlighter_665541" class="syntaxhighlighter  scala">
<div class="bar ">
<div class="toolbar"><a target="_blank" class="item viewSource" title="查看源代码" href="http://www.iteblog.com/archives/1378#viewSource" rel="nofollow" style="width:16px; height:16px">查看源代码</a>
<div class="item copyToClipboard"></div>
<a target="_blank" class="item printSource" title="打印" href="http://www.iteblog.com/archives/1378#printSource" rel="nofollow" style="width:16px; height:16px">打印</a><a target="_blank" class="item about" title="帮助" href="http://www.iteblog.com/archives/1378#about" rel="nofollow" style="width:16px; height:16px">帮助</a></div>
</div>
<div class="lines">
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>01</code></td>
<td class="content"><code class="scala keyword">object</code> <code class="scala plain">
UserClickCountAnalytics { </code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>02</code></td>
<td class="content"><code class="spaces"> </code> </td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>03</code></td>
<td class="content"><code class="spaces">  </code><code class="scala keyword">def</code><code class="scala plain">main(args</code><code class="scala keyword">:</code>
<code class="scala plain">Array[String])</code><code class="scala keyword">:</code>
<code class="scala plain">Unit </code><code class="scala keyword">=</code> <code class="scala plain">
{ </code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>04</code></td>
<td class="content"><code class="spaces">    </code><code class="scala keyword">var</code><code class="scala plain">masterUrl
</code><code class="scala keyword">=</code> <code class="scala string">"local[1]"</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>05</code></td>
<td class="content"><code class="spaces">    </code><code class="scala keyword">if</code><code class="scala plain">(args.length &gt;
</code><code class="scala value">0</code><code class="scala plain">) {</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>06</code></td>
<td class="content"><code class="spaces">      </code><code class="scala plain">masterUrl</code><code class="scala keyword">=</code>
<code class="scala plain">args(</code><code class="scala value">0</code><code class="scala plain">)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>07</code></td>
<td class="content"><code class="spaces">    </code><code class="scala plain">} </code>
</td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>08</code></td>
<td class="content"><code class="spaces"> </code> </td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>09</code></td>
<td class="content"><code class="spaces">    </code><code class="scala comments">// Create a StreamingContext with the given master URL</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>10</code></td>
<td class="content"><code class="spaces">    </code><code class="scala keyword">val</code><code class="scala plain">conf
</code><code class="scala keyword">=</code> <code class="scala keyword">new</code>
<code class="scala plain">SparkConf().setMaster(masterUrl).setAppName(</code><code class="scala string">"UserClickCountStat"</code><code class="scala plain">)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>11</code></td>
<td class="content"><code class="spaces">    </code><code class="scala keyword">val</code><code class="scala plain">ssc
</code><code class="scala keyword">=</code> <code class="scala keyword">new</code>
<code class="scala plain">StreamingContext(conf, Seconds(</code><code class="scala value">5</code><code class="scala plain">))</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>12</code></td>
<td class="content"><code class="spaces"> </code> </td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>13</code></td>
<td class="content"><code class="spaces">    </code><code class="scala comments">// Kafka configurations</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>14</code></td>
<td class="content"><code class="spaces">    </code><code class="scala keyword">val</code><code class="scala plain">topics
</code><code class="scala keyword">=</code> <code class="scala plain">Set(</code><code class="scala string">"user_events"</code><code class="scala plain">)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>15</code></td>
<td class="content"><code class="spaces">    </code><code class="scala keyword">val</code><code class="scala plain">brokers
</code><code class="scala keyword">=</code> <code class="scala string">"10.10.4.126:9092,10.10.4.127:9092"</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>16</code></td>
<td class="content"><code class="spaces">    </code><code class="scala keyword">val</code><code class="scala plain">kafkaParams
</code><code class="scala keyword">=</code><code class="scala plain">Map[String, String](
</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>17</code></td>
<td class="content"><code class="spaces">      </code><code class="scala string">"metadata.broker.list"</code><code class="scala plain">-&gt; brokers,
</code><code class="scala string">"serializer.class"</code><code class="scala plain">-&gt;
</code><code class="scala string">"kafka.serializer.StringEncoder"</code><code class="scala plain">)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>18</code></td>
<td class="content"><code class="spaces"> </code> </td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>19</code></td>
<td class="content"><code class="spaces">    </code><code class="scala keyword">val</code><code class="scala plain">dbIndex
</code><code class="scala keyword">=</code> <code class="scala value">1</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>20</code></td>
<td class="content"><code class="spaces">    </code><code class="scala keyword">val</code><code class="scala plain">clickHashKey
</code><code class="scala keyword">=</code><code class="scala string">"app::users::click"</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>21</code></td>
<td class="content"><code class="spaces"> </code> </td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>22</code></td>
<td class="content"><code class="spaces">    </code><code class="scala comments">// Create a direct stream</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>23</code></td>
<td class="content"><code class="spaces">    </code><code class="scala keyword">val</code><code class="scala plain">kafkaStream
</code><code class="scala keyword">=</code><code class="scala plain">KafkaUtils.createDirectStream[String, String, StringDecoder, StringDecoder](ssc, kafkaParams, topics)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>24</code></td>
<td class="content"><code class="spaces"> </code> </td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>25</code></td>
<td class="content"><code class="spaces">    </code><code class="scala keyword">val</code><code class="scala plain">events
</code><code class="scala keyword">=</code> <code class="scala plain">kafkaStream.flatMap(line
</code><code class="scala keyword">=</code><code class="scala plain">&gt; {</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>26</code></td>
<td class="content"><code class="spaces">      </code><code class="scala keyword">val</code><code class="scala plain">data
</code><code class="scala keyword">=</code> <code class="scala plain">JSONObject.fromObject(line.</code><code class="scala keyword">_</code><code class="scala value">2</code><code class="scala plain">)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>27</code></td>
<td class="content"><code class="spaces">      </code><code class="scala plain">Some(data)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>28</code></td>
<td class="content"><code class="spaces">    </code><code class="scala plain">}) </code>
</td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>29</code></td>
<td class="content"><code class="spaces"> </code> </td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>30</code></td>
<td class="content"><code class="spaces">    </code><code class="scala comments">// Compute user click times</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>31</code></td>
<td class="content"><code class="spaces">    </code><code class="scala keyword">val</code><code class="scala plain">userClicks
</code><code class="scala keyword">=</code> <code class="scala plain">events.map(x
</code><code class="scala keyword">=</code><code class="scala plain">&gt; (x.getString(</code><code class="scala string">"uid"</code><code class="scala plain">), x.getInt(</code><code class="scala string">"click_count"</code><code class="scala plain">))).reduceByKey(</code><code class="scala keyword">_</code><code class="scala plain">+
</code><code class="scala keyword">_</code><code class="scala plain">)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>32</code></td>
<td class="content"><code class="spaces">    </code><code class="scala plain">userClicks.foreachRDD(rdd</code><code class="scala keyword">=</code><code class="scala plain">&gt; {
</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>33</code></td>
<td class="content"><code class="spaces">      </code><code class="scala plain">rdd.foreachPartition(partitionOfRecords</code><code class="scala keyword">=</code><code class="scala plain">&gt; {
</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>34</code></td>
<td class="content"><code class="spaces">        </code><code class="scala plain">partitionOfRecords.foreach(pair</code><code class="scala keyword">=</code><code class="scala plain">&gt; {
</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>35</code></td>
<td class="content"><code class="spaces">          </code><code class="scala keyword">val</code><code class="scala plain">uid
</code><code class="scala keyword">=</code> <code class="scala plain">pair.</code><code class="scala keyword">_</code><code class="scala value">1</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>36</code></td>
<td class="content"><code class="spaces">          </code><code class="scala keyword">val</code><code class="scala plain">clickCount
</code><code class="scala keyword">=</code> <code class="scala plain">pair.</code><code class="scala keyword">_</code><code class="scala value">2</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>37</code></td>
<td class="content"><code class="spaces">          </code><code class="scala keyword">val</code><code class="scala plain">jedis
</code><code class="scala keyword">=</code> <code class="scala keyword">&lt;</code><code class="scala plain">SPAN
</code><code class="scala keyword">class</code><code class="scala keyword">=</code><code class="scala plain">wp</code><code class="scala keyword">_</code><code class="scala plain">keywordlink</code><code class="scala keyword">_</code><code class="scala plain">affiliate</code><code class="scala keyword">&gt;&lt;</code><code class="scala plain">A
 title</code><code class="scala keyword">=</code><code class="scala string">""</code><code class="scala plain">href</code><code class="scala keyword">=</code><code class="scala string">"<a target="_blank" href="http://www.iteblog.com/archives/tag/redis" rel="nofollow">http://www.iteblog.com/archives/tag/redis</a>"</code><code class="scala plain">target</code><code class="scala keyword">=_</code><code class="scala plain">blank
 data-original-title</code><code class="scala keyword">=</code><code class="scala string">"View all posts in Redis"</code><code class="scala plain">jQuery</code><code class="scala value">1830668587673401759</code><code class="scala keyword">=</code><code class="scala string">"50"</code><code class="scala keyword">&gt;</code><code class="scala plain">Redis</code><code class="scala keyword">&lt;</code><code class="scala plain">/A</code><code class="scala keyword">&gt;&lt;</code><code class="scala plain">/SPAN</code><code class="scala keyword">&gt;</code><code class="scala plain">Client.pool.getResource</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>38</code></td>
<td class="content"><code class="spaces">          </code><code class="scala plain">jedis.select(dbIndex)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>39</code></td>
<td class="content"><code class="spaces">          </code><code class="scala plain">jedis.hincrBy(clickHashKey, uid, clickCount)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>40</code></td>
<td class="content"><code class="spaces">          </code><code class="scala plain">RedisClient.pool.returnResource(jedis)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>41</code></td>
<td class="content"><code class="spaces">        </code><code class="scala plain">})</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>42</code></td>
<td class="content"><code class="spaces">      </code><code class="scala plain">})</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>43</code></td>
<td class="content"><code class="spaces">    </code><code class="scala plain">}) </code>
</td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>44</code></td>
<td class="content"><code class="spaces"> </code> </td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>45</code></td>
<td class="content"><code class="spaces">    </code><code class="scala plain">ssc.start()</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>46</code></td>
<td class="content"><code class="spaces">    </code><code class="scala plain">ssc.awaitTermination()</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>47</code></td>
<td class="content"><code class="spaces"> </code> </td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>48</code></td>
<td class="content"><code class="spaces">  </code><code class="scala plain">} </code>
</td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>49</code></td>
<td class="content"><code class="scala plain">}</code></td>
</tr>
</tbody>
</table>
</div>
</div>
</div>
<p>　　上面代码使用了Jedis客户端来操作Redis，将分组计数结果数据累加写入Redis存储，如果其他系统需要实时获取该数据，直接从Redis实时读取即可。RedisClient实现代码如下所示：</p>
<div id="highlighter_593978" class="syntaxhighlighter  scala">
<div class="bar ">
<div class="toolbar"><a target="_blank" class="item viewSource" title="查看源代码" href="http://www.iteblog.com/archives/1378#viewSource" rel="nofollow" style="width:16px; height:16px">查看源代码</a>
<div class="item copyToClipboard"></div>
<a target="_blank" class="item printSource" title="打印" href="http://www.iteblog.com/archives/1378#printSource" rel="nofollow" style="width:16px; height:16px">打印</a><a target="_blank" class="item about" title="帮助" href="http://www.iteblog.com/archives/1378#about" rel="nofollow" style="width:16px; height:16px">帮助</a></div>
</div>
<div class="lines">
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>01</code></td>
<td class="content"><code class="scala keyword">object</code> <code class="scala plain">
RedisClient </code><code class="scala keyword">extends</code> <code class="scala plain">
Serializable { </code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>02</code></td>
<td class="content"><code class="spaces">  </code><code class="scala keyword">val</code><code class="scala plain">redisHost
</code><code class="scala keyword">=</code> <code class="scala string">"10.10.4.130"</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>03</code></td>
<td class="content"><code class="spaces">  </code><code class="scala keyword">val</code><code class="scala plain">redisPort
</code><code class="scala keyword">=</code> <code class="scala value">6379</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>04</code></td>
<td class="content"><code class="spaces">  </code><code class="scala keyword">val</code><code class="scala plain">redisTimeout
</code><code class="scala keyword">=</code><code class="scala value">30000</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>05</code></td>
<td class="content"><code class="spaces">  </code><code class="scala keyword">lazy</code><code class="scala keyword">val</code>
<code class="scala plain">pool </code><code class="scala keyword">=</code><code class="scala keyword">new</code>
<code class="scala plain">JedisPool(</code><code class="scala keyword">new</code><code class="scala plain">GenericObjectPoolConfig(), redisHost, redisPort, redisTimeout)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>06</code></td>
<td class="content"><code class="spaces"> </code> </td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>07</code></td>
<td class="content"><code class="spaces">  </code><code class="scala keyword">lazy</code><code class="scala keyword">val</code>
<code class="scala plain">hook </code><code class="scala keyword">=</code><code class="scala keyword">new</code>
<code class="scala plain">Thread { </code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>08</code></td>
<td class="content"><code class="spaces">    </code><code class="scala keyword">override</code><code class="scala keyword">def</code>
<code class="scala plain">run </code><code class="scala keyword">=</code><code class="scala plain">{
</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>09</code></td>
<td class="content"><code class="spaces">      </code><code class="scala plain">println(</code><code class="scala string">"Execute hook thread: "</code><code class="scala plain">+
</code><code class="scala keyword">this</code><code class="scala plain">)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>10</code></td>
<td class="content"><code class="spaces">      </code><code class="scala plain">pool.destroy()</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>11</code></td>
<td class="content"><code class="spaces">    </code><code class="scala plain">} </code>
</td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>12</code></td>
<td class="content"><code class="spaces">  </code><code class="scala plain">} </code>
</td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>13</code></td>
<td class="content"><code class="spaces">  </code><code class="scala plain">sys.addShutdownHook(hook.run)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>14</code></td>
<td class="content"><code class="scala plain">}</code></td>
</tr>
</tbody>
</table>
</div>
</div>
</div>
<p>　　上面代码我们分别在local[K]和Spark Standalone集群模式下运行通过。</p>
<p>　　如果我们是在开发环境进行调试的时候，也就是使用local[K]部署模式，在本地启动K个Worker线程来计算，这K个Worker在同一个JVM实例里，上面的代码默认情况是，如果没有传参数则是local[K]模式，所以如果使用这种方式在创建Redis连接池或连接的时候，可能非常容易调试通过，但是在使用Spark Standalone、YARN Client（YARN Cluster）或Mesos集群部署模式的时候，就会报错，主要是由于在处理Redis连接池或连接的时候出错了。我们可以看一下Spark架构，如图所示（来自官网）：</p>
<div class="wp-caption aligncenter"><a target="_blank" title="" href="http://www.iteblog.com/pic/spark-cluster-overview.png" rel="nofollow"><img alt="" src="http://www.iteblog.com/pic/spark-cluster-overview.png"></a></div>
<p>　　无论是在本地模式、Standalone模式，还是在Mesos或YARN模式下，整个Spark集群的结构都可以用上图抽象表示，只是各个组件的运行环境不同，导致组件可能是分布式的，或本地的，或单个JVM实例的。如在本地模式，则上图表现为在同一节点上的单个进程之内的多个组件；而在YARN Client模式下，Driver程序是在YARN集群之外的一个节点上提交Spark Application，其他的组件都运行在YARN集群管理的节点上。</p>
<p>　　在Spark集群环境部署Application后，在进行计算的时候会将作用于RDD数据集上的函数（Functions）发送到集群中Worker上的Executor上（在Spark Streaming中是作用于DStream的操作），那么这些函数操作所作用的对象（Elements）必须是可序列化的，通过Scala也可以使用lazy引用来解决，否则这些对象（Elements）在跨节点序列化传输后，无法正确地执行反序列化重构成实际可用的对象。上面代码我们使用lazy引用（Lazy Reference）来实现的，代码如下所示：</p>
<div id="highlighter_991946" class="syntaxhighlighter  scala">
<div class="bar">
<div class="toolbar"><a target="_blank" class="item viewSource" title="查看源代码" href="http://www.iteblog.com/archives/1378#viewSource" rel="nofollow" style="width:16px; height:16px">查看源代码</a>
<div class="item copyToClipboard"></div>
<a target="_blank" class="item printSource" title="打印" href="http://www.iteblog.com/archives/1378#printSource" rel="nofollow" style="width:16px; height:16px">打印</a><a target="_blank" class="item about" title="帮助" href="http://www.iteblog.com/archives/1378#about" rel="nofollow" style="width:16px; height:16px">帮助</a></div>
</div>
<div class="lines">
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>01</code></td>
<td class="content"><code class="scala comments">// lazy pool reference </code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>02</code></td>
<td class="content"><code class="scala keyword">lazy</code> <code class="scala keyword">
val</code> <code class="scala plain">pool </code><code class="scala keyword">=</code><code class="scala keyword">new</code>
<code class="scala plain">JedisPool(</code><code class="scala keyword">new</code><code class="scala plain">GenericObjectPoolConfig(), redisHost, redisPort, redisTimeout)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>03</code></td>
<td class="content"><code class="scala plain">... </code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>04</code></td>
<td class="content"><code class="scala plain">partitionOfRecords.foreach(pair </code>
<code class="scala keyword">=</code><code class="scala plain">&gt; { </code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>05</code></td>
<td class="content"><code class="spaces">  </code><code class="scala keyword">val</code><code class="scala plain">uid
</code><code class="scala keyword">=</code> <code class="scala plain">pair.</code><code class="scala keyword">_</code><code class="scala value">1</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>06</code></td>
<td class="content"><code class="spaces">  </code><code class="scala keyword">val</code><code class="scala plain">clickCount
</code><code class="scala keyword">=</code> <code class="scala plain">pair.</code><code class="scala keyword">_</code><code class="scala value">2</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>07</code></td>
<td class="content"><code class="spaces">  </code><code class="scala keyword">val</code><code class="scala plain">jedis
</code><code class="scala keyword">=</code> <code class="scala plain">RedisClient.pool.getResource
</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>08</code></td>
<td class="content"><code class="spaces">  </code><code class="scala plain">jedis.select(dbIndex)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>09</code></td>
<td class="content"><code class="spaces">  </code><code class="scala plain">jedis.hincrBy(clickHashKey, uid, clickCount)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>10</code></td>
<td class="content"><code class="spaces">  </code><code class="scala plain">RedisClient.pool.returnResource(jedis)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>11</code></td>
<td class="content"><code class="scala plain">})</code></td>
</tr>
</tbody>
</table>
</div>
</div>
</div>
<p>　　另一种方式，我们将代码修改为，把对Redis连接的管理放在操作DStream的Output操作范围之内，因为我们知道它是在特定的Executor中进行初始化的，使用一个单例的对象来管理，如下所示：</p>
<div id="highlighter_204201" class="syntaxhighlighter  scala">
<div class="bar              ">
<div class="toolbar"><a target="_blank" class="item viewSource" title="查看源代码" href="http://www.iteblog.com/archives/1378#viewSource" rel="nofollow" style="width:16px; height:16px">查看源代码</a>
<div class="item copyToClipboard"></div>
<a target="_blank" class="item printSource" title="打印" href="http://www.iteblog.com/archives/1378#printSource" rel="nofollow" style="width:16px; height:16px">打印</a><a target="_blank" class="item about" title="帮助" href="http://www.iteblog.com/archives/1378#about" rel="nofollow" style="width:16px; height:16px">帮助</a></div>
</div>
<div class="lines">
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>001</code></td>
<td class="content"><code class="scala keyword">package</code> <code class="scala plain">
org.shirdrn.spark.streaming </code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>002</code></td>
<td class="content"><code class="spaces"> </code> </td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>003</code></td>
<td class="content"><code class="scala keyword">import</code> <code class="scala plain">
org.apache.commons.pool</code><code class="scala value">2</code><code class="scala plain">.impl.GenericObjectPoolConfig</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>004</code></td>
<td class="content"><code class="scala keyword">import</code> <code class="scala plain">
org.apache.spark.SparkConf </code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>005</code></td>
<td class="content"><code class="scala keyword">import</code> <code class="scala plain">
org.apache.spark.streaming.Seconds </code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>006</code></td>
<td class="content"><code class="scala keyword">import</code> <code class="scala plain">
org.apache.spark.streaming.StreamingContext </code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>007</code></td>
<td class="content"><code class="scala keyword">import</code> <code class="scala plain">
org.apache.spark.streaming.dstream.DStream.toPairDStreamFunctions </code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>008</code></td>
<td class="content"><code class="scala keyword">import</code> <code class="scala plain">
org.apache.spark.streaming.kafka.KafkaUtils </code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>009</code></td>
<td class="content"><code class="spaces"> </code> </td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>010</code></td>
<td class="content"><code class="scala keyword">import</code> <code class="scala plain">
kafka.serializer.StringDecoder </code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>011</code></td>
<td class="content"><code class="scala keyword">import</code> <code class="scala plain">
net.sf.json.JSONObject </code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>012</code></td>
<td class="content"><code class="scala keyword">import</code> <code class="scala plain">
redis.clients.jedis.JedisPool </code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>013</code></td>
<td class="content"><code class="spaces"> </code> </td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>014</code></td>
<td class="content"><code class="scala keyword">object</code> <code class="scala plain">
UserClickCountAnalytics { </code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>015</code></td>
<td class="content"><code class="spaces"> </code> </td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>016</code></td>
<td class="content"><code class="spaces">  </code><code class="scala keyword">def</code><code class="scala plain">main(args</code><code class="scala keyword">:</code>
<code class="scala plain">Array[String])</code><code class="scala keyword">:</code>
<code class="scala plain">Unit </code><code class="scala keyword">=</code> <code class="scala plain">
{ </code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>017</code></td>
<td class="content"><code class="spaces">    </code><code class="scala keyword">var</code><code class="scala plain">masterUrl
</code><code class="scala keyword">=</code> <code class="scala string">"local[1]"</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>018</code></td>
<td class="content"><code class="spaces">    </code><code class="scala keyword">if</code><code class="scala plain">(args.length &gt;
</code><code class="scala value">0</code><code class="scala plain">) {</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>019</code></td>
<td class="content"><code class="spaces">      </code><code class="scala plain">masterUrl</code><code class="scala keyword">=</code>
<code class="scala plain">args(</code><code class="scala value">0</code><code class="scala plain">)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>020</code></td>
<td class="content"><code class="spaces">    </code><code class="scala plain">} </code>
</td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>021</code></td>
<td class="content"><code class="spaces"> </code> </td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>022</code></td>
<td class="content"><code class="spaces">    </code><code class="scala comments">// Create a StreamingContext with the given master URL</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>023</code></td>
<td class="content"><code class="spaces">    </code><code class="scala keyword">val</code><code class="scala plain">conf
</code><code class="scala keyword">=</code> <code class="scala keyword">new</code>
<code class="scala plain">SparkConf().setMaster(masterUrl).setAppName(</code><code class="scala string">"UserClickCountStat"</code><code class="scala plain">)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>024</code></td>
<td class="content"><code class="spaces">    </code><code class="scala keyword">val</code><code class="scala plain">ssc
</code><code class="scala keyword">=</code> <code class="scala keyword">new</code>
<code class="scala plain">StreamingContext(conf, Seconds(</code><code class="scala value">5</code><code class="scala plain">))</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>025</code></td>
<td class="content"><code class="spaces"> </code> </td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>026</code></td>
<td class="content"><code class="spaces">    </code><code class="scala comments">// Kafka configurations</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>027</code></td>
<td class="content"><code class="spaces">    </code><code class="scala keyword">val</code><code class="scala plain">topics
</code><code class="scala keyword">=</code> <code class="scala plain">Set(</code><code class="scala string">"user_events"</code><code class="scala plain">)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>028</code></td>
<td class="content"><code class="spaces">    </code><code class="scala keyword">val</code><code class="scala plain">brokers
</code><code class="scala keyword">=</code> <code class="scala string">"10.10.4.126:9092,10.10.4.127:9092"</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>029</code></td>
<td class="content"><code class="spaces">    </code><code class="scala keyword">val</code><code class="scala plain">kafkaParams
</code><code class="scala keyword">=</code><code class="scala plain">Map[String, String](
</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>030</code></td>
<td class="content"><code class="spaces">      </code><code class="scala string">"metadata.broker.list"</code><code class="scala plain">-&gt; brokers,
</code><code class="scala string">"serializer.class"</code><code class="scala plain">-&gt;
</code><code class="scala string">"kafka.serializer.StringEncoder"</code><code class="scala plain">)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>031</code></td>
<td class="content"><code class="spaces"> </code> </td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>032</code></td>
<td class="content"><code class="spaces">    </code><code class="scala keyword">val</code><code class="scala plain">dbIndex
</code><code class="scala keyword">=</code> <code class="scala value">1</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>033</code></td>
<td class="content"><code class="spaces">    </code><code class="scala keyword">val</code><code class="scala plain">clickHashKey
</code><code class="scala keyword">=</code><code class="scala string">"app::users::click"</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>034</code></td>
<td class="content"><code class="spaces"> </code> </td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>035</code></td>
<td class="content"><code class="spaces">    </code><code class="scala comments">// Create a direct stream</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>036</code></td>
<td class="content"><code class="spaces">    </code><code class="scala keyword">val</code><code class="scala plain">kafkaStream
</code><code class="scala keyword">=</code><code class="scala plain">KafkaUtils.createDirectStream[String, String, StringDecoder, StringDecoder](ssc, kafkaParams, topics)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>037</code></td>
<td class="content"><code class="spaces"> </code> </td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>038</code></td>
<td class="content"><code class="spaces">    </code><code class="scala keyword">val</code><code class="scala plain">events
</code><code class="scala keyword">=</code> <code class="scala plain">kafkaStream.flatMap(line
</code><code class="scala keyword">=</code><code class="scala plain">&gt; {</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>039</code></td>
<td class="content"><code class="spaces">      </code><code class="scala keyword">val</code><code class="scala plain">data
</code><code class="scala keyword">=</code> <code class="scala plain">JSONObject.fromObject(line.</code><code class="scala keyword">_</code><code class="scala value">2</code><code class="scala plain">)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>040</code></td>
<td class="content"><code class="spaces">      </code><code class="scala plain">Some(data)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>041</code></td>
<td class="content"><code class="spaces">    </code><code class="scala plain">}) </code>
</td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>042</code></td>
<td class="content"><code class="spaces"> </code> </td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>043</code></td>
<td class="content"><code class="spaces">    </code><code class="scala comments">// Compute user click times</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>044</code></td>
<td class="content"><code class="spaces">    </code><code class="scala keyword">val</code><code class="scala plain">userClicks
</code><code class="scala keyword">=</code> <code class="scala plain">events.map(x
</code><code class="scala keyword">=</code><code class="scala plain">&gt; (x.getString(</code><code class="scala string">"uid"</code><code class="scala plain">), x.getInt(</code><code class="scala string">"click_count"</code><code class="scala plain">))).reduceByKey(</code><code class="scala keyword">_</code><code class="scala plain">+
</code><code class="scala keyword">_</code><code class="scala plain">)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>045</code></td>
<td class="content"><code class="spaces">    </code><code class="scala plain">userClicks.foreachRDD(rdd</code><code class="scala keyword">=</code><code class="scala plain">&gt; {
</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>046</code></td>
<td class="content"><code class="spaces">      </code><code class="scala plain">rdd.foreachPartition(partitionOfRecords</code><code class="scala keyword">=</code><code class="scala plain">&gt; {
</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>047</code></td>
<td class="content"><code class="spaces">        </code><code class="scala plain">partitionOfRecords.foreach(pair</code><code class="scala keyword">=</code><code class="scala plain">&gt; {
</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>048</code></td>
<td class="content"><code class="spaces">          </code> </td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>049</code></td>
<td class="content"><code class="spaces">          </code><code class="scala comments">/**</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>050</code></td>
<td class="content"><code class="spaces">           </code><code class="scala comments">* Internal Redis client for managing Redis connection {@link Jedis} based on {@link RedisPool}</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>051</code></td>
<td class="content"><code class="spaces">           </code><code class="scala comments">*/</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>052</code></td>
<td class="content"><code class="spaces">          </code><code class="scala keyword">object</code><code class="scala plain">InternalRedisClient
</code><code class="scala keyword">extends</code><code class="scala plain">Serializable {
</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>053</code></td>
<td class="content"><code class="spaces">            </code> </td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>054</code></td>
<td class="content"><code class="spaces">            </code><code class="scala keyword">@</code><code class="scala plain">transient</code><code class="scala keyword">private</code>
<code class="scala keyword">var</code><code class="scala plain">pool</code><code class="scala keyword">:</code>
<code class="scala plain">JedisPool </code><code class="scala keyword">=</code> <code class="scala keyword">
null</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>055</code></td>
<td class="content"><code class="spaces">            </code> </td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>056</code></td>
<td class="content"><code class="spaces">            </code><code class="scala keyword">def</code><code class="scala plain">makePool(redisHost</code><code class="scala keyword">:</code><code class="scala plain">String, redisPort</code><code class="scala keyword">:</code><code class="scala plain">Int,
 redisTimeout</code><code class="scala keyword">:</code><code class="scala plain">Int,
</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>057</code></td>
<td class="content"><code class="spaces">                </code><code class="scala plain">maxTotal</code><code class="scala keyword">:</code><code class="scala plain">Int, maxIdle</code><code class="scala keyword">:</code><code class="scala plain">Int, minIdle</code><code class="scala keyword">:</code><code class="scala plain">Int)</code><code class="scala keyword">:</code>
<code class="scala plain">Unit </code><code class="scala keyword">=</code> <code class="scala plain">
{ </code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>058</code></td>
<td class="content"><code class="spaces">              </code><code class="scala plain">makePool(redisHost, redisPort, redisTimeout, maxTotal, maxIdle, minIdle,</code><code class="scala keyword">true</code><code class="scala plain">,
</code><code class="scala keyword">false</code><code class="scala plain">, </code>
<code class="scala value">10000</code><code class="scala plain">)   </code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>059</code></td>
<td class="content"><code class="spaces">            </code><code class="scala plain">}</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>060</code></td>
<td class="content"><code class="spaces">            </code> </td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>061</code></td>
<td class="content"><code class="spaces">            </code><code class="scala keyword">def</code><code class="scala plain">makePool(redisHost</code><code class="scala keyword">:</code><code class="scala plain">String, redisPort</code><code class="scala keyword">:</code><code class="scala plain">Int,
 redisTimeout</code><code class="scala keyword">:</code><code class="scala plain">Int,
</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>062</code></td>
<td class="content"><code class="spaces">                </code><code class="scala plain">maxTotal</code><code class="scala keyword">:</code><code class="scala plain">Int, maxIdle</code><code class="scala keyword">:</code><code class="scala plain">Int, minIdle</code><code class="scala keyword">:</code><code class="scala plain">Int,
 testOnBorrow</code><code class="scala keyword">:</code><code class="scala plain">Boolean,
</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>063</code></td>
<td class="content"><code class="spaces">                </code><code class="scala plain">testOnReturn</code><code class="scala keyword">:</code><code class="scala plain">Boolean, maxWaitMillis</code><code class="scala keyword">:</code><code class="scala plain">Long)</code><code class="scala keyword">:</code>
<code class="scala plain">Unit </code><code class="scala keyword">=</code> <code class="scala plain">
{ </code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>064</code></td>
<td class="content"><code class="spaces">              </code><code class="scala keyword">if</code><code class="scala plain">(pool</code><code class="scala keyword">==</code>
<code class="scala keyword">null</code><code class="scala plain">) {</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>065</code></td>
<td class="content"><code class="spaces">                   </code><code class="scala keyword">val</code><code class="scala plain">poolConfig
</code><code class="scala keyword">=</code> <code class="scala keyword">new</code>
<code class="scala plain">GenericObjectPoolConfig() </code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>066</code></td>
<td class="content"><code class="spaces">                   </code><code class="scala plain">poolConfig.setMaxTotal(maxTotal)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>067</code></td>
<td class="content"><code class="spaces">                   </code><code class="scala plain">poolConfig.setMaxIdle(maxIdle)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>068</code></td>
<td class="content"><code class="spaces">                   </code><code class="scala plain">poolConfig.setMinIdle(minIdle)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>069</code></td>
<td class="content"><code class="spaces">                   </code><code class="scala plain">poolConfig.setTestOnBorrow(testOnBorrow)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>070</code></td>
<td class="content"><code class="spaces">                   </code><code class="scala plain">poolConfig.setTestOnReturn(testOnReturn)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>071</code></td>
<td class="content"><code class="spaces">                   </code><code class="scala plain">poolConfig.setMaxWaitMillis(maxWaitMillis)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>072</code></td>
<td class="content"><code class="spaces">                   </code><code class="scala plain">pool</code><code class="scala keyword">=</code>
<code class="scala keyword">new</code><code class="scala plain">JedisPool(poolConfig, redisHost, redisPort, redisTimeout)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>073</code></td>
<td class="content"><code class="spaces">                   </code> </td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>074</code></td>
<td class="content"><code class="spaces">                   </code><code class="scala keyword">val</code><code class="scala plain">hook
</code><code class="scala keyword">=</code> <code class="scala keyword">new</code>
<code class="scala plain">Thread{ </code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>075</code></td>
<td class="content"><code class="spaces">                        </code><code class="scala keyword">override</code><code class="scala keyword">def</code>
<code class="scala plain">run </code><code class="scala keyword">=</code><code class="scala plain">pool.destroy()
</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>076</code></td>
<td class="content"><code class="spaces">                   </code><code class="scala plain">}</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>077</code></td>
<td class="content"><code class="spaces">                   </code><code class="scala plain">sys.addShutdownHook(hook.run)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>078</code></td>
<td class="content"><code class="spaces">              </code><code class="scala plain">}</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>079</code></td>
<td class="content"><code class="spaces">            </code><code class="scala plain">}</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>080</code></td>
<td class="content"><code class="spaces">            </code> </td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>081</code></td>
<td class="content"><code class="spaces">            </code><code class="scala keyword">def</code><code class="scala plain">getPool</code><code class="scala keyword">:</code>
<code class="scala plain">JedisPool </code><code class="scala keyword">=</code> <code class="scala plain">
{</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>082</code></td>
<td class="content"><code class="spaces">              </code><code class="scala plain">assert(pool !</code><code class="scala keyword">=</code><code class="scala keyword">null</code><code class="scala plain">)
</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>083</code></td>
<td class="content"><code class="spaces">              </code><code class="scala plain">pool</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>084</code></td>
<td class="content"><code class="spaces">            </code><code class="scala plain">}</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>085</code></td>
<td class="content"><code class="spaces">          </code><code class="scala plain">}</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>086</code></td>
<td class="content"><code class="spaces">          </code> </td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>087</code></td>
<td class="content"><code class="spaces">          </code><code class="scala comments">// Redis configurations</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>088</code></td>
<td class="content"><code class="spaces">          </code><code class="scala keyword">val</code><code class="scala plain">maxTotal
</code><code class="scala keyword">=</code> <code class="scala value">10</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>089</code></td>
<td class="content"><code class="spaces">          </code><code class="scala keyword">val</code><code class="scala plain">maxIdle
</code><code class="scala keyword">=</code> <code class="scala value">10</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>090</code></td>
<td class="content"><code class="spaces">          </code><code class="scala keyword">val</code><code class="scala plain">minIdle
</code><code class="scala keyword">=</code> <code class="scala value">1</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>091</code></td>
<td class="content"><code class="spaces">          </code><code class="scala keyword">val</code><code class="scala plain">redisHost
</code><code class="scala keyword">=</code> <code class="scala string">"10.10.4.130"</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>092</code></td>
<td class="content"><code class="spaces">          </code><code class="scala keyword">val</code><code class="scala plain">redisPort
</code><code class="scala keyword">=</code> <code class="scala value">6379</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>093</code></td>
<td class="content"><code class="spaces">          </code><code class="scala keyword">val</code><code class="scala plain">redisTimeout
</code><code class="scala keyword">=</code><code class="scala value">30000</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>094</code></td>
<td class="content"><code class="spaces">          </code><code class="scala keyword">val</code><code class="scala plain">dbIndex
</code><code class="scala keyword">=</code> <code class="scala value">1</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>095</code></td>
<td class="content"><code class="spaces">          </code><code class="scala plain">InternalRedisClient.makePool(redisHost, redisPort, redisTimeout, maxTotal, maxIdle, minIdle)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>096</code></td>
<td class="content"><code class="spaces">          </code> </td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>097</code></td>
<td class="content"><code class="spaces">          </code><code class="scala keyword">val</code><code class="scala plain">uid
</code><code class="scala keyword">=</code> <code class="scala plain">pair.</code><code class="scala keyword">_</code><code class="scala value">1</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>098</code></td>
<td class="content"><code class="spaces">          </code><code class="scala keyword">val</code><code class="scala plain">clickCount
</code><code class="scala keyword">=</code> <code class="scala plain">pair.</code><code class="scala keyword">_</code><code class="scala value">2</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>099</code></td>
<td class="content"><code class="spaces">          </code><code class="scala keyword">val</code><code class="scala plain">jedis
</code><code class="scala keyword">=</code><code class="scala plain">InternalRedisClient.getPool.getResource</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>100</code></td>
<td class="content"><code class="spaces">          </code><code class="scala plain">jedis.select(dbIndex)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>101</code></td>
<td class="content"><code class="spaces">          </code><code class="scala plain">jedis.hincrBy(clickHashKey, uid, clickCount)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>102</code></td>
<td class="content"><code class="spaces">          </code><code class="scala plain">InternalRedisClient.getPool.returnResource(jedis)</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>103</code></td>
<td class="content"><code class="spaces">        </code><code class="scala plain">})</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>104</code></td>
<td class="content"><code class="spaces">      </code><code class="scala plain">})</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>105</code></td>
<td class="content"><code class="spaces">    </code><code class="scala plain">}) </code>
</td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>106</code></td>
<td class="content"><code class="spaces"> </code> </td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>107</code></td>
<td class="content"><code class="spaces">    </code><code class="scala plain">ssc.start()</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>108</code></td>
<td class="content"><code class="spaces">    </code><code class="scala plain">ssc.awaitTermination()</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>109</code></td>
<td class="content"><code class="spaces"> </code> </td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>110</code></td>
<td class="content"><code class="spaces">  </code><code class="scala plain">} </code>
</td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>111</code></td>
<td class="content"><code class="scala plain">}</code></td>
</tr>
</tbody>
</table>
</div>
</div>
</div>
<p>　　上面代码实现，得益于Scala语言的特性，可以在代码中任何位置进行class或object的定义，我们将用来管理Redis连接的代码放在了特定操作的内部，就避免了瞬态（Transient）对象跨节点序列化的问题。这样做还要求我们能够了解Spark内部是如何操作RDD数据集的，更多可以参考RDD或Spark相关文档。</p>
<p>　　在集群上，以Standalone模式运行，执行如下命令：</p>
<div id="highlighter_139631" class="syntaxhighlighter  bash">
<div class="bar">
<div class="toolbar"><a target="_blank" class="item viewSource" title="查看源代码" href="http://www.iteblog.com/archives/1378#viewSource" rel="nofollow" style="width:16px; height:16px">查看源代码</a>
<div class="item copyToClipboard"></div>
<a target="_blank" class="item printSource" title="打印" href="http://www.iteblog.com/archives/1378#printSource" rel="nofollow" style="width:16px; height:16px">打印</a><a target="_blank" class="item about" title="帮助" href="http://www.iteblog.com/archives/1378#about" rel="nofollow" style="width:16px; height:16px">帮助</a></div>
</div>
<div class="lines">
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>1</code></td>
<td class="content"><code class="bash functions">cd</code> <code class="bash plain">
/usr/</code><code class="bash functions">local</code><code class="bash plain">/spark</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>2</code></td>
<td class="content"><code class="bash plain">./bin/spark-submit --class org.shirdrn.spark.streaming.UserClickCountAnalytics </code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>3</code></td>
<td class="content"><code class="bash plain">　　　　　　　　    --master <a target="_blank" href="" rel="nofollow">
spark://hadoop1:7077 </a> </code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>4</code></td>
<td class="content"><code class="bash plain">　　　　　　　　    --executor-memory 1G  </code>
</td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>5</code></td>
<td class="content"><code class="bash plain">　　　　　　　　    --total-executor-cores 2 </code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>6</code></td>
<td class="content"><code class="bash plain">　　　　　　　　    ~/spark-0.0.SNAPSHOT.jar<a target="_blank" href="" rel="nofollow">spark://hadoop1:7077</a></code></td>
</tr>
</tbody>
</table>
</div>
</div>
</div>
<p>　　可以查看集群中各个Worker节点执行计算任务的状态，也可以非常方便地通过Web页面查看。</p>
<p>　　下面，看一下我们存储到Redis中的计算结果，如下所示：</p>
<div id="highlighter_332909" class="syntaxhighlighter  scala">
<div class="bar ">
<div class="toolbar"><a target="_blank" class="item viewSource" title="查看源代码" href="http://www.iteblog.com/archives/1378#viewSource" rel="nofollow" style="width:16px; height:16px">查看源代码</a>
<div class="item copyToClipboard"></div>
<a target="_blank" class="item printSource" title="打印" href="http://www.iteblog.com/archives/1378#printSource" rel="nofollow" style="width:16px; height:16px">打印</a><a target="_blank" class="item about" title="帮助" href="http://www.iteblog.com/archives/1378#about" rel="nofollow" style="width:16px; height:16px">帮助</a></div>
</div>
<div class="lines">
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>01</code></td>
<td class="content"><code class="scala value">127.0</code><code class="scala plain">.</code><code class="scala value">0.1</code><code class="scala keyword">:</code><code class="scala value">6379</code><code class="scala plain">[</code><code class="scala value">1</code><code class="scala plain">]&gt;
 HGETALL app</code><code class="scala keyword">::</code><code class="scala plain">users</code><code class="scala keyword">::</code><code class="scala plain">click</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>02</code></td>
<td class="content"><code class="scala value">1</code><code class="scala plain">)</code><code class="scala string">"4A4D769EB9679C054DE81B973ED5D768"</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>03</code></td>
<td class="content"><code class="scala value">2</code><code class="scala plain">)</code><code class="scala string">"7037"</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>04</code></td>
<td class="content"><code class="scala value">3</code><code class="scala plain">)</code><code class="scala string">"8dfeb5aaafc027d89349ac9a20b3930f"</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>05</code></td>
<td class="content"><code class="scala value">4</code><code class="scala plain">)</code><code class="scala string">"6992"</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>06</code></td>
<td class="content"><code class="scala value">5</code><code class="scala plain">)</code><code class="scala string">"011BBF43B89BFBF266C865DF0397AA71"</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>07</code></td>
<td class="content"><code class="scala value">6</code><code class="scala plain">)</code><code class="scala string">"7021"</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>08</code></td>
<td class="content"><code class="scala value">7</code><code class="scala plain">)</code><code class="scala string">"97edfc08311c70143401745a03a50706"</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>09</code></td>
<td class="content"><code class="scala value">8</code><code class="scala plain">)</code><code class="scala string">"6874"</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>10</code></td>
<td class="content"><code class="scala value">9</code><code class="scala plain">)</code><code class="scala string">"d7f141563005d1b5d0d3dd30138f3f62"</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>11</code></td>
<td class="content"><code class="scala value">10</code><code class="scala plain">)</code><code class="scala string">"7057"</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>12</code></td>
<td class="content"><code class="scala value">11</code><code class="scala plain">)</code><code class="scala string">"a95f22eabc4fd4b580c011a3161a9d9d"</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>13</code></td>
<td class="content"><code class="scala value">12</code><code class="scala plain">)</code><code class="scala string">"7092"</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>14</code></td>
<td class="content"><code class="scala value">13</code><code class="scala plain">)</code><code class="scala string">"6b67c8c700427dee7552f81f3228c927"</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>15</code></td>
<td class="content"><code class="scala value">14</code><code class="scala plain">)</code><code class="scala string">"7266"</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>16</code></td>
<td class="content"><code class="scala value">15</code><code class="scala plain">)</code><code class="scala string">"f2a8474bf7bd94f0aabbd4cdd2c06dcf"</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>17</code></td>
<td class="content"><code class="scala value">16</code><code class="scala plain">)</code><code class="scala string">"7188"</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>18</code></td>
<td class="content"><code class="scala value">17</code><code class="scala plain">)</code><code class="scala string">"c8ee90aade1671a21336c721512b817a"</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>19</code></td>
<td class="content"><code class="scala value">18</code><code class="scala plain">)</code><code class="scala string">"6950"</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>20</code></td>
<td class="content"><code class="scala value">19</code><code class="scala plain">)</code><code class="scala string">"068b746ed4620d25e26055a9f804385f"</code></td>
</tr>
</tbody>
</table>
</div>
</div>
</div>
<h2>pom文件及相关依赖</h2>
<p>　　这里，附上前面开发的应用所对应的依赖，以及打包Spark Streaming应用程序的Maven配置，以供参考。如果使用maven-shade-plugin插件，配置有问题的话，打包后在Spark集群上提交Application时候可能会报错Invalid signature file digest for Manifest main attributes。参考的Maven配置，如下所示：</p>
<div id="highlighter_823416" class="syntaxhighlighter  scala">
<div class="bar             ">
<div class="toolbar"><a target="_blank" class="item viewSource" title="查看源代码" href="http://www.iteblog.com/archives/1378#viewSource" rel="nofollow" style="width:16px; height:16px">查看源代码</a>
<div class="item copyToClipboard"></div>
<a target="_blank" class="item printSource" title="打印" href="http://www.iteblog.com/archives/1378#printSource" rel="nofollow" style="width:16px; height:16px">打印</a><a target="_blank" class="item about" title="帮助" href="http://www.iteblog.com/archives/1378#about" rel="nofollow" style="width:16px; height:16px">帮助</a></div>
</div>
<div class="lines">
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>001</code></td>
<td class="content"><code class="scala plain">&lt;project xmlns</code><code class="scala keyword">=</code><code class="scala string">"<a target="_blank" href="http://maven.apache.org/POM/4.0.0" rel="nofollow">http://maven.apache.org/POM/4.0.0</a>"</code><code class="scala plain">xmlns</code><code class="scala keyword">:</code><code class="scala plain">xsi</code><code class="scala keyword">=</code><code class="scala string">"<a target="_blank" href="http://www.w3.org/2001/XMLSchema-instance" rel="nofollow">http://www.w3.org/2001/XMLSchema-instance</a>"</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>002</code></td>
<td class="content"><code class="spaces">     </code><code class="scala plain">xsi</code><code class="scala keyword">:</code><code class="scala plain">schemaLocation</code><code class="scala keyword">=</code><code class="scala string">"<a target="_blank" href="http://maven.apache.org/POM/4.0.0" rel="nofollow">http://maven.apache.org/POM/4.0.0</a><a target="_blank" href="http://maven.apache.org/xsd/maven-4.0.0.xsd" rel="nofollow">http://maven.apache.org/xsd/maven-4.0.0.xsd</a>"</code><code class="scala plain">&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>003</code></td>
<td class="content"><code class="spaces">     </code><code class="scala plain">&lt;modelVersion&gt;</code><code class="scala value">4.0</code><code class="scala plain">.</code><code class="scala value">0</code><code class="scala plain">&lt;/modelVersion&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>004</code></td>
<td class="content"><code class="spaces">     </code><code class="scala plain">&lt;groupId&gt;org.shirdrn.spark&lt;/groupId&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>005</code></td>
<td class="content"><code class="spaces">     </code><code class="scala plain">&lt;artifactId&gt;spark&lt;/artifactId&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>006</code></td>
<td class="content"><code class="spaces">     </code><code class="scala plain">&lt;version&gt;</code><code class="scala value">0.0</code><code class="scala plain">.</code><code class="scala value">1</code><code class="scala plain">-SNAPSHOT&lt;/version&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>007</code></td>
<td class="content"><code class="spaces"> </code> </td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>008</code></td>
<td class="content"><code class="spaces">     </code><code class="scala plain">&lt;dependencies&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>009</code></td>
<td class="content"><code class="spaces">          </code><code class="scala plain">&lt;dependency&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>010</code></td>
<td class="content"><code class="spaces">               </code><code class="scala plain">&lt;groupId&gt;org.apache.spark&lt;/groupId&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>011</code></td>
<td class="content"><code class="spaces">               </code><code class="scala plain">&lt;artifactId&gt;spark-core</code><code class="scala keyword">_</code><code class="scala value">2.10</code><code class="scala plain">&lt;/artifactId&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>012</code></td>
<td class="content"><code class="spaces">               </code><code class="scala plain">&lt;version&gt;</code><code class="scala value">1.3</code><code class="scala plain">.</code><code class="scala value">0</code><code class="scala plain">&lt;/version&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>013</code></td>
<td class="content"><code class="spaces">          </code><code class="scala plain">&lt;/dependency&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>014</code></td>
<td class="content"><code class="spaces">          </code><code class="scala plain">&lt;dependency&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>015</code></td>
<td class="content"><code class="spaces">               </code><code class="scala plain">&lt;groupId&gt;org.apache.spark&lt;/groupId&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>016</code></td>
<td class="content"><code class="spaces">               </code><code class="scala plain">&lt;artifactId&gt;spark-streaming</code><code class="scala keyword">_</code><code class="scala value">2.10</code><code class="scala plain">&lt;/artifactId&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>017</code></td>
<td class="content"><code class="spaces">               </code><code class="scala plain">&lt;version&gt;</code><code class="scala value">1.3</code><code class="scala plain">.</code><code class="scala value">0</code><code class="scala plain">&lt;/version&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>018</code></td>
<td class="content"><code class="spaces">          </code><code class="scala plain">&lt;/dependency&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>019</code></td>
<td class="content"><code class="spaces">          </code><code class="scala plain">&lt;dependency&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>020</code></td>
<td class="content"><code class="spaces">               </code><code class="scala plain">&lt;groupId&gt;net.sf.json-lib&lt;/groupId&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>021</code></td>
<td class="content"><code class="spaces">               </code><code class="scala plain">&lt;artifactId&gt;json-lib&lt;/artifactId&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>022</code></td>
<td class="content"><code class="spaces">               </code><code class="scala plain">&lt;version&gt;</code><code class="scala value">2.3</code><code class="scala plain">&lt;/version&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>023</code></td>
<td class="content"><code class="spaces">          </code><code class="scala plain">&lt;/dependency&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>024</code></td>
<td class="content"><code class="spaces">          </code><code class="scala plain">&lt;dependency&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>025</code></td>
<td class="content"><code class="spaces">               </code><code class="scala plain">&lt;groupId&gt;org.apache.spark&lt;/groupId&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>026</code></td>
<td class="content"><code class="spaces">               </code><code class="scala plain">&lt;artifactId&gt;spark-streaming-kafka</code><code class="scala keyword">_</code><code class="scala value">2.10</code><code class="scala plain">&lt;/artifactId&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>027</code></td>
<td class="content"><code class="spaces">               </code><code class="scala plain">&lt;version&gt;</code><code class="scala value">1.3</code><code class="scala plain">.</code><code class="scala value">0</code><code class="scala plain">&lt;/version&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>028</code></td>
<td class="content"><code class="spaces">          </code><code class="scala plain">&lt;/dependency&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>029</code></td>
<td class="content"><code class="spaces">          </code><code class="scala plain">&lt;dependency&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>030</code></td>
<td class="content"><code class="spaces">               </code><code class="scala plain">&lt;groupId&gt;redis.clients&lt;/groupId&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>031</code></td>
<td class="content"><code class="spaces">               </code><code class="scala plain">&lt;artifactId&gt;jedis&lt;/artifactId&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>032</code></td>
<td class="content"><code class="spaces">               </code><code class="scala plain">&lt;version&gt;</code><code class="scala value">2.5</code><code class="scala plain">.</code><code class="scala value">2</code><code class="scala plain">&lt;/version&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>033</code></td>
<td class="content"><code class="spaces">          </code><code class="scala plain">&lt;/dependency&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>034</code></td>
<td class="content"><code class="spaces">          </code><code class="scala plain">&lt;dependency&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>035</code></td>
<td class="content"><code class="spaces">               </code><code class="scala plain">&lt;groupId&gt;org.apache.commons&lt;/groupId&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>036</code></td>
<td class="content"><code class="spaces">               </code><code class="scala plain">&lt;artifactId&gt;commons-pool</code><code class="scala value">2</code><code class="scala plain">&lt;/artifactId&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>037</code></td>
<td class="content"><code class="spaces">               </code><code class="scala plain">&lt;version&gt;</code><code class="scala value">2.2</code><code class="scala plain">&lt;/version&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>038</code></td>
<td class="content"><code class="spaces">          </code><code class="scala plain">&lt;/dependency&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>039</code></td>
<td class="content"><code class="spaces">     </code><code class="scala plain">&lt;/dependencies&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>040</code></td>
<td class="content"><code class="spaces"> </code> </td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>041</code></td>
<td class="content"><code class="spaces">     </code><code class="scala plain">&lt;build&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>042</code></td>
<td class="content"><code class="spaces">          </code><code class="scala plain">&lt;sourceDirectory&gt;${basedir}/src/main/scala&lt;/sourceDirectory&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>043</code></td>
<td class="content"><code class="spaces">          </code><code class="scala plain">&lt;testSourceDirectory&gt;${basedir}/src/test/scala&lt;/testSourceDirectory&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>044</code></td>
<td class="content"><code class="spaces">          </code><code class="scala plain">&lt;resources&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>045</code></td>
<td class="content"><code class="spaces">               </code><code class="scala plain">&lt;resource&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>046</code></td>
<td class="content"><code class="spaces">                    </code><code class="scala plain">&lt;directory&gt;${basedir}/src/main/resources&lt;/directory&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>047</code></td>
<td class="content"><code class="spaces">               </code><code class="scala plain">&lt;/resource&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>048</code></td>
<td class="content"><code class="spaces">          </code><code class="scala plain">&lt;/resources&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>049</code></td>
<td class="content"><code class="spaces">          </code><code class="scala plain">&lt;testResources&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>050</code></td>
<td class="content"><code class="spaces">               </code><code class="scala plain">&lt;testResource&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>051</code></td>
<td class="content"><code class="spaces">                    </code><code class="scala plain">&lt;directory&gt;${basedir}/src/test/resources&lt;/directory&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>052</code></td>
<td class="content"><code class="spaces">               </code><code class="scala plain">&lt;/testResource&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>053</code></td>
<td class="content"><code class="spaces">          </code><code class="scala plain">&lt;/testResources&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>054</code></td>
<td class="content"><code class="spaces">          </code><code class="scala plain">&lt;plugins&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>055</code></td>
<td class="content"><code class="spaces">               </code><code class="scala plain">&lt;plugin&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>056</code></td>
<td class="content"><code class="spaces">                    </code><code class="scala plain">&lt;artifactId&gt;maven-compiler-plugin&lt;/artifactId&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>057</code></td>
<td class="content"><code class="spaces">                    </code><code class="scala plain">&lt;version&gt;</code><code class="scala value">3.1</code><code class="scala plain">&lt;/version&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>058</code></td>
<td class="content"><code class="spaces">                    </code><code class="scala plain">&lt;configuration&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>059</code></td>
<td class="content"><code class="spaces">                         </code><code class="scala plain">&lt;source&gt;</code><code class="scala value">1.6</code><code class="scala plain">&lt;/source&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>060</code></td>
<td class="content"><code class="spaces">                         </code><code class="scala plain">&lt;target&gt;</code><code class="scala value">1.6</code><code class="scala plain">&lt;/target&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>061</code></td>
<td class="content"><code class="spaces">                    </code><code class="scala plain">&lt;/configuration&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>062</code></td>
<td class="content"><code class="spaces">               </code><code class="scala plain">&lt;/plugin&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>063</code></td>
<td class="content"><code class="spaces">               </code><code class="scala plain">&lt;plugin&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>064</code></td>
<td class="content"><code class="spaces">                    </code><code class="scala plain">&lt;groupId&gt;org.apache.maven.plugins&lt;/groupId&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>065</code></td>
<td class="content"><code class="spaces">                    </code><code class="scala plain">&lt;artifactId&gt;maven-shade-plugin&lt;/artifactId&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>066</code></td>
<td class="content"><code class="spaces">                    </code><code class="scala plain">&lt;version&gt;</code><code class="scala value">2.2</code><code class="scala plain">&lt;/version&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>067</code></td>
<td class="content"><code class="spaces">                    </code><code class="scala plain">&lt;configuration&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>068</code></td>
<td class="content"><code class="spaces">                         </code><code class="scala plain">&lt;createDependencyReducedPom&gt;</code><code class="scala keyword">true</code><code class="scala plain">&lt;/createDependencyReducedPom&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>069</code></td>
<td class="content"><code class="spaces">                    </code><code class="scala plain">&lt;/configuration&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>070</code></td>
<td class="content"><code class="spaces">                    </code><code class="scala plain">&lt;executions&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>071</code></td>
<td class="content"><code class="spaces">                         </code><code class="scala plain">&lt;execution&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>072</code></td>
<td class="content"><code class="spaces">                              </code><code class="scala plain">&lt;phase&gt;</code><code class="scala keyword">package</code><code class="scala plain">&lt;/phase&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>073</code></td>
<td class="content"><code class="spaces">                              </code><code class="scala plain">&lt;goals&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>074</code></td>
<td class="content"><code class="spaces">                                   </code><code class="scala plain">&lt;goal&gt;shade&lt;/goal&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>075</code></td>
<td class="content"><code class="spaces">                              </code><code class="scala plain">&lt;/goals&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>076</code></td>
<td class="content"><code class="spaces">                              </code><code class="scala plain">&lt;configuration&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>077</code></td>
<td class="content"><code class="spaces">                                   </code><code class="scala plain">&lt;artifactSet&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>078</code></td>
<td class="content"><code class="spaces">                                        </code><code class="scala plain">&lt;includes&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>079</code></td>
<td class="content"><code class="spaces">                                             </code><code class="scala plain">&lt;include&gt;*</code><code class="scala keyword">:</code><code class="scala plain">*&lt;/include&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>080</code></td>
<td class="content"><code class="spaces">                                        </code><code class="scala plain">&lt;/includes&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>081</code></td>
<td class="content"><code class="spaces">                                   </code><code class="scala plain">&lt;/artifactSet&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>082</code></td>
<td class="content"><code class="spaces">                                   </code><code class="scala plain">&lt;filters&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>083</code></td>
<td class="content"><code class="spaces">                                        </code><code class="scala plain">&lt;filter&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>084</code></td>
<td class="content"><code class="spaces">                                             </code><code class="scala plain">&lt;artifact&gt;*</code><code class="scala keyword">:</code><code class="scala plain">*&lt;/artifact&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>085</code></td>
<td class="content"><code class="spaces">                                             </code><code class="scala plain">&lt;excludes&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>086</code></td>
<td class="content"><code class="spaces">                                                  </code><code class="scala plain">&lt;exclude&gt;META-INF/*.SF&lt;/exclude&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>087</code></td>
<td class="content"><code class="spaces">                                                  </code><code class="scala plain">&lt;exclude&gt;META-INF/*.DSA&lt;/exclude&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>088</code></td>
<td class="content"><code class="spaces">                                                  </code><code class="scala plain">&lt;exclude&gt;META-INF/*.RSA&lt;/exclude&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>089</code></td>
<td class="content"><code class="spaces">                                             </code><code class="scala plain">&lt;/excludes&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>090</code></td>
<td class="content"><code class="spaces">                                        </code><code class="scala plain">&lt;/filter&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>091</code></td>
<td class="content"><code class="spaces">                                   </code><code class="scala plain">&lt;/filters&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>092</code></td>
<td class="content"><code class="spaces">                                   </code><code class="scala plain">&lt;transformers&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>093</code></td>
<td class="content"><code class="spaces">                                        </code><code class="scala plain">&lt;transformer</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>094</code></td>
<td class="content"><code class="spaces">                                             </code><code class="scala plain">implementation</code><code class="scala keyword">=</code><code class="scala string">"org.apache.maven.plugins.shade.resource.ServicesResourceTransformer"</code><code class="scala plain">/&gt;
</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>095</code></td>
<td class="content"><code class="spaces">                                        </code><code class="scala plain">&lt;transformer</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>096</code></td>
<td class="content"><code class="spaces">                                             </code><code class="scala plain">implementation</code><code class="scala keyword">=</code><code class="scala string">"org.apache.maven.plugins.shade.resource.AppendingTransformer"</code><code class="scala plain">&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>097</code></td>
<td class="content"><code class="spaces">                                             </code><code class="scala plain">&lt;resource&gt;reference.conf&lt;/resource&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>098</code></td>
<td class="content"><code class="spaces">                                        </code><code class="scala plain">&lt;/transformer&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>099</code></td>
<td class="content"><code class="spaces">                                        </code><code class="scala plain">&lt;transformer</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>100</code></td>
<td class="content"><code class="spaces">                                             </code><code class="scala plain">implementation</code><code class="scala keyword">=</code><code class="scala string">"org.apache.maven.plugins.shade.resource.DontIncludeResourceTransformer"</code><code class="scala plain">&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>101</code></td>
<td class="content"><code class="spaces">                                             </code><code class="scala plain">&lt;resource&gt;log</code><code class="scala value">4</code><code class="scala plain">j.properties&lt;/resource&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>102</code></td>
<td class="content"><code class="spaces">                                        </code><code class="scala plain">&lt;/transformer&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>103</code></td>
<td class="content"><code class="spaces">                                   </code><code class="scala plain">&lt;/transformers&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>104</code></td>
<td class="content"><code class="spaces">                              </code><code class="scala plain">&lt;/configuration&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>105</code></td>
<td class="content"><code class="spaces">                         </code><code class="scala plain">&lt;/execution&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>106</code></td>
<td class="content"><code class="spaces">                    </code><code class="scala plain">&lt;/executions&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>107</code></td>
<td class="content"><code class="spaces">               </code><code class="scala plain">&lt;/plugin&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>108</code></td>
<td class="content"><code class="spaces">          </code><code class="scala plain">&lt;/plugins&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt1">
<table>
<tbody>
<tr>
<td class="number"><code>109</code></td>
<td class="content"><code class="spaces">     </code><code class="scala plain">&lt;/build&gt;</code></td>
</tr>
</tbody>
</table>
</div>
<div class="line alt2">
<table>
<tbody>
<tr>
<td class="number"><code>110</code></td>
<td class="content"><code class="scala plain">&lt;/project&gt;</code></td>
</tr>
</tbody>
</table>
</div>
</div>
</div>
            </div>
                </div>