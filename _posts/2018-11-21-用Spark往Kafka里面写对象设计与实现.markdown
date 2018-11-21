---
layout:     post
title:      用Spark往Kafka里面写对象设计与实现
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<span class="wp_keywordlink_affiliate"><a title="" href="http://www.iteblog.com/archives/tag/spark" rel="nofollow">Spark</a></span>和<span class="wp_keywordlink_affiliate"><a title="" href="http://www.iteblog.com/archives/tag/kafka" rel="nofollow">Kafka</a></span>都是比较常用的两个大数据框架，<span class="wp_keywordlink_affiliate"><a title="" href="http://www.iteblog.com/archives/tag/spark" rel="nofollow">Spark</a></span>里面提供了对<span class="wp_keywordlink_affiliate"><a title="" href="http://www.iteblog.com/archives/tag/kafka" rel="nofollow">Kafka</a></span>读写的支持。默认情况下我们<span class="wp_keywordlink_affiliate"><a title="" href="http://www.iteblog.com/archives/tag/kafka" rel="nofollow">Kafka</a></span>只能写Byte数组到Topic里面，如果我们想往Topic里面读写String类型的消息，可以分别使用Kafka里面内置的StringEncoder编码类和StringDecoder解码类。那如果我们想往Kafka里面写对象怎么办？<br>
　　别担心，Kafka中的kafka.serializer里面有Decoder和Encoder两个trait，这两个trait就是Kafka Topic消息相关的解码类和编码类，内置的StringDecoder和StringEncoder类分别都是继承那两个trait的。直接将String对象用给定的编码转换成Byte数组。来看下Decoder和Encoder两个trait的实现：
<div id="highlighter_795422" class="syntaxhighlighter scala">
<div class="bar">
<div class="toolbar"><a class="item viewSource" title="查看源代码" href="http://www.iteblog.com/archives/1296#viewSource" rel="nofollow" style="width:16px;">查看源代码</a>
<div class="item copyToClipboard"></div>
<a class="item printSource" title="打印" href="http://www.iteblog.com/archives/1296#printSource" rel="nofollow" style="width:16px;">打印</a><a class="item about" title="帮助" href="http://www.iteblog.com/archives/1296#about" rel="nofollow" style="width:16px;">帮助</a></div>
</div>
<div class="lines no-wrap">
<div class="line alt1">
<table><tbody><tr><td class="number"><code>01</code></td>
<td class="content"><code class="scala comments">/** </code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>02</code></td>
<td class="content"><code class="spaces"> </code><code class="scala comments">* A decoder is a method of turning byte arrays into objects.
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>03</code></td>
<td class="content"><code class="spaces"> </code><code class="scala comments">* An implementation is required to provide a constructor that
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>04</code></td>
<td class="content"><code class="spaces"> </code><code class="scala comments">* takes a VerifiableProperties instance.
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>05</code></td>
<td class="content"><code class="spaces"> </code><code class="scala comments">*/</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>06</code></td>
<td class="content"><code class="scala keyword">trait</code> <code class="scala plain">
Decoder[T] { </code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>07</code></td>
<td class="content"><code class="spaces">  </code><code class="scala keyword">def</code>
<code class="scala plain">fromBytes(bytes</code><code class="scala keyword">:</code>
<code class="scala plain">Array[Byte])</code><code class="scala keyword">:</code>
<code class="scala plain">T </code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>08</code></td>
<td class="content"><code class="scala plain">} </code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>09</code></td>
<td class="content"><code class="spaces"> </code> </td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>10</code></td>
<td class="content"><code class="scala comments">/** </code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>11</code></td>
<td class="content"><code class="spaces"> </code><code class="scala comments">* An encoder is a method of turning objects into byte arrays.
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>12</code></td>
<td class="content"><code class="spaces"> </code><code class="scala comments">* An implementation is required to provide a constructor that
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>13</code></td>
<td class="content"><code class="spaces"> </code><code class="scala comments">* takes a VerifiableProperties instance.
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>14</code></td>
<td class="content"><code class="spaces"> </code><code class="scala comments">*/</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>15</code></td>
<td class="content"><code class="scala keyword">trait</code> <code class="scala plain">
Encoder[T] { </code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>16</code></td>
<td class="content"><code class="spaces">  </code><code class="scala keyword">def</code>
<code class="scala plain">toBytes(t</code><code class="scala keyword">:</code> <code class="scala plain">
T)</code><code class="scala keyword">:</code> <code class="scala plain">Array[Byte]
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>17</code></td>
<td class="content"><code class="scala plain">}</code></td>
</tr></tbody></table></div>
</div>
</div>
<p>　　也就是说，我们自定义的编码和解码类只需要分别实现toBytes和fromBytes函数即可。那我们如何将对象转换成Byte数组，并且如何将Byte数组转换回对象呢？记得Java中写对象的类没？我们可以用<code>ByteArrayOutputStream</code>并结合<code>ObjectOutputStream</code>类将对象转换成Byte数组；并用<code>ByteArrayInputStream</code>结合<code>ObjectInputStream</code>类将Byte数组转换回对象。这不就实现了吗？？废话不多说，来看看怎么实现：</p>
<div id="highlighter_686947" class="syntaxhighlighter scala">
<div class="bar">
<div class="toolbar"><a class="item viewSource" title="查看源代码" href="http://www.iteblog.com/archives/1296#viewSource" rel="nofollow" style="width:16px;">查看源代码</a>
<div class="item copyToClipboard"></div>
<a class="item printSource" title="打印" href="http://www.iteblog.com/archives/1296#printSource" rel="nofollow" style="width:16px;">打印</a><a class="item about" title="帮助" href="http://www.iteblog.com/archives/1296#about" rel="nofollow" style="width:16px;">帮助</a></div>
</div>
<div class="lines no-wrap">
<div class="line alt1">
<table><tbody><tr><td class="number"><code>01</code></td>
<td class="content"><code class="scala keyword">class</code> <code class="scala plain">
IteblogDecoder[T](props</code><code class="scala keyword">:</code> <code class="scala plain">
VerifiableProperties </code><code class="scala keyword">=</code> <code class="scala keyword">
null</code><code class="scala plain">) </code><code class="scala keyword">extends</code>
<code class="scala plain">Decoder[T] { </code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>02</code></td>
<td class="content"><code class="spaces"> </code> </td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>03</code></td>
<td class="content"><code class="spaces">    </code><code class="scala keyword">def</code>
<code class="scala plain">fromBytes(bytes</code><code class="scala keyword">:</code>
<code class="scala plain">Array[Byte])</code><code class="scala keyword">:</code>
<code class="scala plain">T </code><code class="scala keyword">=</code> <code class="scala plain">
{ </code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>04</code></td>
<td class="content"><code class="spaces">      </code><code class="scala keyword">var</code>
<code class="scala plain">t</code><code class="scala keyword">:</code> <code class="scala plain">
T </code><code class="scala keyword">=</code> <code class="scala keyword">null</code><code class="scala plain">.asInstanceOf[T]
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>05</code></td>
<td class="content"><code class="spaces">      </code><code class="scala keyword">var</code>
<code class="scala plain">bi</code><code class="scala keyword">:</code> <code class="scala plain">
ByteArrayInputStream </code><code class="scala keyword">=</code> <code class="scala keyword">
null</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>06</code></td>
<td class="content"><code class="spaces">      </code><code class="scala keyword">var</code>
<code class="scala plain">oi</code><code class="scala keyword">:</code> <code class="scala plain">
ObjectInputStream </code><code class="scala keyword">=</code> <code class="scala keyword">
null</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>07</code></td>
<td class="content"><code class="spaces">      </code><code class="scala keyword">try</code>
<code class="scala plain">{ </code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>08</code></td>
<td class="content"><code class="spaces">        </code><code class="scala plain">bi
</code><code class="scala keyword">=</code> <code class="scala keyword">new</code>
<code class="scala plain">ByteArrayInputStream(bytes) </code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>09</code></td>
<td class="content"><code class="spaces">        </code><code class="scala plain">oi
</code><code class="scala keyword">=</code> <code class="scala keyword">new</code>
<code class="scala plain">ObjectInputStream(bi) </code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>10</code></td>
<td class="content"><code class="spaces">        </code><code class="scala plain">t
</code><code class="scala keyword">=</code> <code class="scala plain">oi.readObject().asInstanceOf[T]
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>11</code></td>
<td class="content"><code class="spaces">      </code><code class="scala plain">}
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>12</code></td>
<td class="content"><code class="spaces">      </code><code class="scala keyword">catch</code>
<code class="scala plain">{ </code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>13</code></td>
<td class="content"><code class="spaces">        </code><code class="scala keyword">case</code>
<code class="scala plain">e</code><code class="scala keyword">:</code> <code class="scala plain">
Exception </code><code class="scala keyword">=</code><code class="scala plain">&gt; {
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>14</code></td>
<td class="content"><code class="spaces">          </code><code class="scala plain">e.printStackTrace();
</code><code class="scala keyword">null</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>15</code></td>
<td class="content"><code class="spaces">        </code><code class="scala plain">}
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>16</code></td>
<td class="content"><code class="spaces">      </code><code class="scala plain">}
</code><code class="scala keyword">finally</code> <code class="scala plain">{ </code>
</td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>17</code></td>
<td class="content"><code class="spaces">        </code><code class="scala plain">bi.close()
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>18</code></td>
<td class="content"><code class="spaces">        </code><code class="scala plain">oi.close()
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>19</code></td>
<td class="content"><code class="spaces">      </code><code class="scala plain">}
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>20</code></td>
<td class="content"><code class="spaces">      </code><code class="scala plain">t
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>21</code></td>
<td class="content"><code class="spaces">    </code><code class="scala plain">} </code>
</td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>22</code></td>
<td class="content"><code class="scala plain">} </code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>23</code></td>
<td class="content"><code class="spaces"> </code> </td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>24</code></td>
<td class="content"><code class="scala keyword">class</code> <code class="scala plain">
IteblogEncoder[T](props</code><code class="scala keyword">:</code> <code class="scala plain">
VerifiableProperties </code><code class="scala keyword">=</code> <code class="scala keyword">
null</code><code class="scala plain">) </code><code class="scala keyword">extends</code>
<code class="scala plain">Encoder[T] { </code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>25</code></td>
<td class="content"><code class="spaces"> </code> </td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>26</code></td>
<td class="content"><code class="spaces">    </code><code class="scala keyword">override</code>
<code class="scala keyword">def</code> <code class="scala plain">toBytes(t</code><code class="scala keyword">:</code>
<code class="scala plain">T)</code><code class="scala keyword">:</code> <code class="scala plain">
Array[Byte] </code><code class="scala keyword">=</code> <code class="scala plain">
{ </code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>27</code></td>
<td class="content"><code class="spaces">      </code><code class="scala keyword">if</code>
<code class="scala plain">(t </code><code class="scala keyword">==</code> <code class="scala keyword">
null</code><code class="scala plain">) </code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>28</code></td>
<td class="content"><code class="spaces">        </code><code class="scala keyword">null</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>29</code></td>
<td class="content"><code class="spaces">      </code><code class="scala keyword">else</code>
<code class="scala plain">{ </code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>30</code></td>
<td class="content"><code class="spaces">        </code><code class="scala keyword">var</code>
<code class="scala plain">bo</code><code class="scala keyword">:</code> <code class="scala plain">
ByteArrayOutputStream </code><code class="scala keyword">=</code> <code class="scala keyword">
null</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>31</code></td>
<td class="content"><code class="spaces">        </code><code class="scala keyword">var</code>
<code class="scala plain">oo</code><code class="scala keyword">:</code> <code class="scala plain">
ObjectOutputStream </code><code class="scala keyword">=</code> <code class="scala keyword">
null</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>32</code></td>
<td class="content"><code class="spaces">        </code><code class="scala keyword">var</code>
<code class="scala plain">byte</code><code class="scala keyword">:</code> <code class="scala plain">
Array[Byte] </code><code class="scala keyword">=</code> <code class="scala keyword">
null</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>33</code></td>
<td class="content"><code class="spaces">        </code><code class="scala keyword">try</code>
<code class="scala plain">{ </code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>34</code></td>
<td class="content"><code class="spaces">          </code><code class="scala plain">bo
</code><code class="scala keyword">=</code> <code class="scala keyword">new</code>
<code class="scala plain">ByteArrayOutputStream() </code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>35</code></td>
<td class="content"><code class="spaces">          </code><code class="scala plain">oo
</code><code class="scala keyword">=</code> <code class="scala keyword">new</code>
<code class="scala plain">ObjectOutputStream(bo) </code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>36</code></td>
<td class="content"><code class="spaces">          </code><code class="scala plain">oo.writeObject(t)
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>37</code></td>
<td class="content"><code class="spaces">          </code><code class="scala plain">byte
</code><code class="scala keyword">=</code> <code class="scala plain">bo.toByteArray
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>38</code></td>
<td class="content"><code class="spaces">        </code><code class="scala plain">}
</code><code class="scala keyword">catch</code> <code class="scala plain">{ </code>
</td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>39</code></td>
<td class="content"><code class="spaces">          </code><code class="scala keyword">case</code>
<code class="scala plain">ex</code><code class="scala keyword">:</code> <code class="scala plain">
Exception </code><code class="scala keyword">=</code><code class="scala plain">&gt; </code>
<code class="scala keyword">return</code> <code class="scala plain">byte </code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>40</code></td>
<td class="content"><code class="spaces">        </code><code class="scala plain">}
</code><code class="scala keyword">finally</code> <code class="scala plain">{ </code>
</td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>41</code></td>
<td class="content"><code class="spaces">          </code><code class="scala plain">bo.close()
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>42</code></td>
<td class="content"><code class="spaces">          </code><code class="scala plain">oo.close()
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>43</code></td>
<td class="content"><code class="spaces">        </code><code class="scala plain">}
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>44</code></td>
<td class="content"><code class="spaces">        </code><code class="scala plain">byte
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>45</code></td>
<td class="content"><code class="spaces">      </code><code class="scala plain">}
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>46</code></td>
<td class="content"><code class="spaces">    </code><code class="scala plain">} </code>
</td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>47</code></td>
<td class="content"><code class="scala plain">}</code></td>
</tr></tbody></table></div>
</div>
</div>
<p>　　这样我们就定义了自己的编码和解码器。那如何使用呢？？假设我们有一个Person类。如下：</p>
<div id="highlighter_416019" class="syntaxhighlighter scala">
<div class="bar">
<div class="toolbar"><a class="item viewSource" title="查看源代码" href="http://www.iteblog.com/archives/1296#viewSource" rel="nofollow" style="width:16px;">查看源代码</a>
<div class="item copyToClipboard"></div>
<a class="item printSource" title="打印" href="http://www.iteblog.com/archives/1296#printSource" rel="nofollow" style="width:16px;">打印</a><a class="item about" title="帮助" href="http://www.iteblog.com/archives/1296#about" rel="nofollow" style="width:16px;">帮助</a></div>
</div>
<div class="lines no-wrap">
<div class="line alt1">
<table><tbody><tr><td class="number"><code>1</code></td>
<td class="content"><code class="scala keyword">case</code> <code class="scala keyword">
class</code> <code class="scala plain">Person(</code><code class="scala keyword">var</code>
<code class="scala plain">name</code><code class="scala keyword">:</code> <code class="scala plain">
String, </code><code class="scala keyword">var</code> <code class="scala plain">age</code><code class="scala keyword">:</code>
<code class="scala plain">Int)</code></td>
</tr></tbody></table></div>
</div>
</div>
<p>　　我们可以在发送数据这么使用：</p>
<div id="highlighter_299229" class="syntaxhighlighter scala">
<div class="bar">
<div class="toolbar"><a class="item viewSource" title="查看源代码" href="http://www.iteblog.com/archives/1296#viewSource" rel="nofollow" style="width:16px;">查看源代码</a>
<div class="item copyToClipboard"></div>
<a class="item printSource" title="打印" href="http://www.iteblog.com/archives/1296#printSource" rel="nofollow" style="width:16px;">打印</a><a class="item about" title="帮助" href="http://www.iteblog.com/archives/1296#about" rel="nofollow" style="width:16px;">帮助</a></div>
</div>
<div class="lines no-wrap">
<div class="line alt1">
<table><tbody><tr><td class="number"><code>01</code></td>
<td class="content"><code class="scala keyword">def</code> <code class="scala plain">
getProducerConfig(brokerAddr</code><code class="scala keyword">:</code> <code class="scala plain">
String)</code><code class="scala keyword">:</code> <code class="scala plain">Properties
</code><code class="scala keyword">=</code> <code class="scala plain">{ </code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>02</code></td>
<td class="content"><code class="spaces">    </code><code class="scala keyword">val</code>
<code class="scala plain">props </code><code class="scala keyword">=</code> <code class="scala keyword">
new</code> <code class="scala plain">Properties() </code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>03</code></td>
<td class="content"><code class="spaces">    </code><code class="scala plain">props.put(</code><code class="scala string">"metadata.broker.list"</code><code class="scala plain">, brokerAddr)
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>04</code></td>
<td class="content"><code class="spaces">    </code><code class="scala plain">props.put(</code><code class="scala string">"serializer.class"</code><code class="scala plain">, classOf[IteblogEncoder[Person]].getName)
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>05</code></td>
<td class="content"><code class="spaces">    </code><code class="scala plain">props.put(</code><code class="scala string">"key.serializer.class"</code><code class="scala plain">, classOf[StringEncoder].getName)
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>06</code></td>
<td class="content"><code class="spaces">    </code><code class="scala plain">props
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>07</code></td>
<td class="content"><code class="spaces">  </code><code class="scala plain">} </code>
</td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>08</code></td>
<td class="content"><code class="spaces"> </code> </td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>09</code></td>
<td class="content"><code class="scala keyword">def</code> <code class="scala plain">
sendMessages(topic</code><code class="scala keyword">:</code> <code class="scala plain">
String, messages</code><code class="scala keyword">:</code> <code class="scala plain">
List[Person], brokerAddr</code><code class="scala keyword">:</code> <code class="scala plain">
String) { </code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>10</code></td>
<td class="content"><code class="spaces">    </code><code class="scala keyword">val</code>
<code class="scala plain">producer </code><code class="scala keyword">=</code> <code class="scala keyword">
new</code> <code class="scala plain">Producer[String, Person]( </code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>11</code></td>
<td class="content"><code class="scala plain">　　　　</code><code class="scala keyword">new</code>
<code class="scala plain">ProducerConfig(getProducerConfig(brokerAddr))) </code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>12</code></td>
<td class="content"><code class="spaces">    </code><code class="scala plain">producer.send(messages.map {
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>13</code></td>
<td class="content"><code class="spaces">      </code><code class="scala keyword">new</code>
<code class="scala plain">KeyedMessage[String, Person](topic, </code><code class="scala string">"Iteblog"</code><code class="scala plain">,
</code><code class="scala keyword">_</code><code class="scala plain">) </code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>14</code></td>
<td class="content"><code class="spaces">    </code><code class="scala plain">}</code><code class="scala keyword">:</code>
<code class="scala keyword">_</code><code class="scala plain">*) </code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>15</code></td>
<td class="content"><code class="spaces">    </code><code class="scala plain">producer.close()
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>16</code></td>
<td class="content"><code class="scala plain">} </code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>17</code></td>
<td class="content"><code class="spaces"> </code> </td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>18</code></td>
<td class="content"><code class="scala keyword">def</code> <code class="scala plain">
main(args</code><code class="scala keyword">:</code> <code class="scala plain">Array[String]) {
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>19</code></td>
<td class="content"><code class="spaces">    </code><code class="scala keyword">val</code>
<code class="scala plain">sparkConf </code><code class="scala keyword">=</code> <code class="scala keyword">
new</code> <code class="scala plain">S parkConf().setAppName(</code><code class="scala keyword">this</code><code class="scala plain">.getClass.getSimpleName)
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>20</code></td>
<td class="content"><code class="spaces">    </code><code class="scala keyword">val</code>
<code class="scala plain">ssc </code><code class="scala keyword">=</code> <code class="scala keyword">
new</code> <code class="scala plain">StreamingContext(sparkConf, Milliseconds(</code><code class="scala value">500</code><code class="scala plain">))
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>21</code></td>
<td class="content"><code class="spaces">    </code><code class="scala keyword">val</code>
<code class="scala plain">topic </code><code class="scala keyword">=</code> <code class="scala plain">
args(</code><code class="scala value">0</code><code class="scala plain">) </code>
</td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>22</code></td>
<td class="content"><code class="spaces">    </code><code class="scala keyword">val</code>
<code class="scala plain">brokerAddr </code><code class="scala keyword">=</code> <code class="scala string">
"<a href="http://www.iteblog.com:9092/" rel="nofollow">http://www.iteblog.com:9092</a>"</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>23</code></td>
<td class="content"><code class="spaces"> </code> </td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>24</code></td>
<td class="content"><code class="spaces">    </code><code class="scala keyword">val</code>
<code class="scala plain">data </code><code class="scala keyword">=</code> <code class="scala plain">
List(Person(</code><code class="scala string">"wyp"</code><code class="scala plain">,
</code><code class="scala value">23</code><code class="scala plain">), Person(</code><code class="scala string">"spark"</code><code class="scala plain">,
</code><code class="scala value">34</code><code class="scala plain">), Person(</code><code class="scala string">"kafka"</code><code class="scala plain">,
</code><code class="scala value">23</code><code class="scala plain">), </code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>25</code></td>
<td class="content"><code class="spaces">         </code><code class="scala plain">Person(</code><code class="scala string">"iteblog"</code><code class="scala plain">,
</code><code class="scala value">23</code><code class="scala plain">)) </code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>26</code></td>
<td class="content"><code class="spaces">    </code><code class="scala plain">sendMessages(topic, data, brokerAddr)
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>27</code></td>
<td class="content"><code class="scala plain">}</code></td>
</tr></tbody></table></div>
</div>
</div>
<p>　　在接收端可以这么使用</p>
<div id="highlighter_149670" class="syntaxhighlighter scala">
<div class="bar">
<div class="toolbar"><a class="item viewSource" title="查看源代码" href="http://www.iteblog.com/archives/1296#viewSource" rel="nofollow" style="width:16px;">查看源代码</a>
<div class="item copyToClipboard"></div>
<a class="item printSource" title="打印" href="http://www.iteblog.com/archives/1296#printSource" rel="nofollow" style="width:16px;">打印</a><a class="item about" title="帮助" href="http://www.iteblog.com/archives/1296#about" rel="nofollow" style="width:16px;">帮助</a></div>
</div>
<div class="lines no-wrap">
<div class="line alt1">
<table><tbody><tr><td class="number"><code>01</code></td>
<td class="content"><code class="scala keyword">val</code> <code class="scala plain">
sparkConf </code><code class="scala keyword">=</code> <code class="scala keyword">
new</code> <code class="scala plain">S parkConf().setAppName(</code><code class="scala keyword">this</code><code class="scala plain">.getClass.getSimpleName)
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>02</code></td>
<td class="content"><code class="scala keyword">val</code> <code class="scala plain">
ssc </code><code class="scala keyword">=</code> <code class="scala keyword">new</code>
<code class="scala plain">StreamingContext(sparkConf, Milliseconds(</code><code class="scala value">500</code><code class="scala plain">))
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>03</code></td>
<td class="content"><code class="scala keyword">val</code> <code class="scala plain">
(topic, groupId) </code><code class="scala keyword">=</code> <code class="scala plain">
(args(</code><code class="scala value">0</code><code class="scala plain">), args(</code><code class="scala value">1</code><code class="scala plain">))
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>04</code></td>
<td class="content"><code class="spaces"> </code> </td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>05</code></td>
<td class="content"><code class="scala keyword">val</code> <code class="scala plain">
kafkaParams </code><code class="scala keyword">=</code> <code class="scala plain">
Map(</code><code class="scala string">"zookeeper.connect"</code> <code class="scala plain">
-&gt; </code><code class="scala string">"<a href="http://www.iteblog.com:2181/" rel="nofollow">http://www.iteblog.com:2181</a>"</code><code class="scala plain">,
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>06</code></td>
<td class="content"><code class="spaces">      </code><code class="scala string">"group.id"</code>
<code class="scala plain">-&gt; groupId, </code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>07</code></td>
<td class="content"><code class="spaces">      </code><code class="scala string">"auto.offset.reset"</code>
<code class="scala plain">-&gt; </code><code class="scala string">"smallest"</code><code class="scala plain">)
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>08</code></td>
<td class="content"><code class="spaces"> </code> </td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>09</code></td>
<td class="content"><code class="scala keyword">val</code> <code class="scala plain">
stream </code><code class="scala keyword">=</code> <code class="scala plain">KafkaUtils.createStream[String, Person, StringDecoder,
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>10</code></td>
<td class="content"><code class="scala plain">　　 IteblogDecoder[Person]](ssc, kafkaParams, Map(topic -&gt;
</code><code class="scala value">1</code><code class="scala plain">),  </code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>11</code></td>
<td class="content"><code class="scala plain">　　StorageLevel.MEMORY</code><code class="scala keyword">_</code><code class="scala plain">ONLY)
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>12</code></td>
<td class="content"><code class="spaces">    </code><code class="scala plain">stream.foreachRDD(rdd
</code><code class="scala keyword">=</code><code class="scala plain">&gt; { </code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>13</code></td>
<td class="content"><code class="spaces">      </code><code class="scala keyword">if</code>
<code class="scala plain">(rdd.count() !</code><code class="scala keyword">=</code>
<code class="scala value">0</code><code class="scala plain">) { </code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>14</code></td>
<td class="content"><code class="spaces">        </code><code class="scala plain">rdd.foreach(item
</code><code class="scala keyword">=</code><code class="scala plain">&gt; </code><code class="scala keyword">if</code>
<code class="scala plain">(item !</code><code class="scala keyword">=</code> <code class="scala keyword">
null</code><code class="scala plain">) println(item)) </code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>15</code></td>
<td class="content"><code class="spaces">      </code><code class="scala plain">}
</code><code class="scala keyword">else</code> <code class="scala plain">{ </code>
</td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>16</code></td>
<td class="content"><code class="spaces">        </code><code class="scala plain">println(</code><code class="scala string">"Empty rdd!!"</code><code class="scala plain">)
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>17</code></td>
<td class="content"><code class="spaces">      </code><code class="scala plain">}
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>18</code></td>
<td class="content"><code class="spaces">    </code><code class="scala plain">}) </code>
</td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>19</code></td>
<td class="content"><code class="spaces">    </code><code class="scala plain">ssc.start()
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>20</code></td>
<td class="content"><code class="spaces">    </code><code class="scala plain">ssc.awaitTermination()
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>21</code></td>
<td class="content"><code class="spaces">    </code><code class="scala plain">ssc.stop()</code></td>
</tr></tbody></table></div>
</div>
</div>
<p>　　这样可以发送任意可序列化的对象了。下面是效果：</p>
<div id="highlighter_848733" class="syntaxhighlighter scala">
<div class="bar">
<div class="toolbar"><a class="item viewSource" title="查看源代码" href="http://www.iteblog.com/archives/1296#viewSource" rel="nofollow" style="width:16px;">查看源代码</a>
<div class="item copyToClipboard"></div>
<a class="item printSource" title="打印" href="http://www.iteblog.com/archives/1296#printSource" rel="nofollow" style="width:16px;">打印</a><a class="item about" title="帮助" href="http://www.iteblog.com/archives/1296#about" rel="nofollow" style="width:16px;">帮助</a></div>
</div>
<div class="lines no-wrap">
<div class="line alt1">
<table><tbody><tr><td class="number"><code>1</code></td>
<td class="content"><code class="scala plain">Empty rdd!! </code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>2</code></td>
<td class="content"><code class="scala plain">(Iteblog,Person(wyp,</code><code class="scala value">23</code><code class="scala plain">))
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>3</code></td>
<td class="content"><code class="scala plain">(Iteblog,Person(spark,</code><code class="scala value">34</code><code class="scala plain">))
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>4</code></td>
<td class="content"><code class="scala plain">(Iteblog,Person(kafka,</code><code class="scala value">23</code><code class="scala plain">))
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>5</code></td>
<td class="content"><code class="scala plain">(Iteblog,Person(iteblog,</code><code class="scala value">23</code><code class="scala plain">))
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>6</code></td>
<td class="content"><code class="scala plain">Empty rdd!! </code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>7</code></td>
<td class="content"><code class="scala plain">Empty rdd!! </code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>8</code></td>
<td class="content"><code class="scala plain">Empty rdd!! </code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>9</code></td>
<td class="content"><code class="scala plain">Empty rdd!!</code></td>
</tr></tbody></table></div>
</div>
</div>
<p>　　在例子中我们只是简单的将接收到的消息打印到控制台。如果没有接收到消息，则打印Empty rdd!!。</p>
            </div>
                </div>