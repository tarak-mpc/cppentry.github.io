---
layout:     post
title:      KAFKA 与 FLUME 集成配置测试
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="kafka-与-flume-集成配置测试">KAFKA 与 FLUME 集成配置测试</h1>

<p>标签（空格分隔）： 未分类</p>



<h3 id="flume-安装配置">FLUME 安装配置</h3>

<pre><code>指导链接： https://blog.csdn.net/a123demi/article/details/72637847

安装FLUME 设置其支持kafka

上传并压缩FLUME 并修改名字为flume...
</code></pre>

<p><img src="http://static.zybuluo.com/825973286/6lft0kwarpt290dqz1esdxhj/image_1ck7sfjla1ecdpsb1u9jq6jht79.png" alt="image_1ck7sfjla1ecdpsb1u9jq6jht79.png-22kB" title="">    </p>

<pre><code>修改文件
flume-env.sh.template -&gt; flume-env.sh
并设置jdk目录和hadoop目录
</code></pre>

<p><img src="http://static.zybuluo.com/825973286/2aqf6gxc53og5ll2i9dq71f1/image_1ck7shgf01djh1jsb1a2o1pam10tim.png" alt="image_1ck7shgf01djh1jsb1a2o1pam10tim.png-13.5kB" title="">    </p>

<pre><code>修改conf配置
flume-conf.properties  去掉 template
</code></pre>

<p><img src="http://static.zybuluo.com/825973286/lkt57nfdgg3k38d5vya4xl59/image_1ck7slbemnfds2e1bqqmfu15ef13.png" alt="image_1ck7slbemnfds2e1bqqmfu15ef13.png-30kB" title=""> <br>
<img src="http://static.zybuluo.com/825973286/4lusn4xj7lzx21qkit2pecgf/image_1ck7slnnv1pjf19rr7ld1ajaffr1g.png" alt="image_1ck7slnnv1pjf19rr7ld1ajaffr1g.png-22.4kB" title=""> </p>



<h3 id="测试">测试</h3>

<p><img src="http://static.zybuluo.com/825973286/v0yu7nljnf5fywm7hp8je6ul/image_1ck7tderq1gpqhlj1250121i17c02a.png" alt="image_1ck7tderq1gpqhlj1250121i17c02a.png-19.3kB" title=""> <br>
    kafka 启动一个消息接收端 <br>
<img src="http://static.zybuluo.com/825973286/3cwofs95beqi163l7d442kvh/image_1ck7tlruu6u0132q1sid1610o392n.png" alt="image_1ck7tlruu6u0132q1sid1610o392n.png-11.4kB" title=""> <br>
<img src="http://static.zybuluo.com/825973286/z2beiblwzjv4q2wofdf4jccw/image_1ck7svp9r1ta0bcp1c9nf1cmgn1t.png" alt="image_1ck7svp9r1ta0bcp1c9nf1cmgn1t.png-21kB" title=""></p>

<pre><code>启动flume 读取模拟日志数据
</code></pre>

<p><img src="http://static.zybuluo.com/825973286/0w67vdqk6byidlhc88ik3v11/image_1ck7uptdtpv31lkd8coein1p4v3u.png" alt="image_1ck7uptdtpv31lkd8coein1p4v3u.png-7.5kB" title="">    </p>

<p><img src="http://static.zybuluo.com/825973286/98mkybjp30zr1vtmgicgp864/image_1ck7topckrabufi1c0cl9248b34.png" alt="image_1ck7topckrabufi1c0cl9248b34.png-9.7kB" title="">    </p>

<pre><code>kafka接收端
在 /opt/datas/  下创建一个sh脚本  模拟产生数据
</code></pre>

<p><img src="http://static.zybuluo.com/825973286/iffzfs74lqhu1h7p9u8cz6jp/image_1ck7urkogmkf29b1ir41frs1bd94b.png" alt="image_1ck7urkogmkf29b1ir41frs1bd94b.png-48.3kB" title=""> <br>
<img src="http://static.zybuluo.com/825973286/19uo7a5fixvymp68246023yn/image_1ck7ut3t519rofvi1l0eig9101l58.png" alt="image_1ck7ut3t519rofvi1l0eig9101l58.png-25.7kB" title=""></p>

<pre><code>kafka消息接收者读取到消息
</code></pre>

<p><img src="http://static.zybuluo.com/825973286/d3nv7zqpeyum55bar54amqsx/image_1ck7un0tn1q29ffulob1g1lsp53h.png" alt="image_1ck7un0tn1q29ffulob1g1lsp53h.png-15.3kB" title=""></p>

<pre><code>flume读取到消息
</code></pre>

<p><img src="http://static.zybuluo.com/825973286/bbtg04j76487ls0wwplo6q01/image_1ck7uv7vr2k926k1hft12cg1u655l.png" alt="image_1ck7uv7vr2k926k1hft12cg1u655l.png-86.1kB" title="">    </p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>