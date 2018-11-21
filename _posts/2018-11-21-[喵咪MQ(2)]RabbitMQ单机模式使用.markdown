---
layout:     post
title:      [喵咪MQ(2)]RabbitMQ单机模式使用
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u011142688/article/details/78490020				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="喵咪mq2rabbitmq单机模式使用">[喵咪MQ(2)]RabbitMQ单机模式使用</h1>

<p><img src="http://i.imgur.com/vhdeDvX.png" alt="" title=""></p>

<p>哈喽!本周我们又见面了,今天呢我们接着说MQ软件RabbitMQ的故事,在上小节以及成功的安装了RabbitMQ也配置了开机启动,那么我们应该要开始的真正的接触并且使用它来管理使用解决业务问题,请紧张的往下看…</p>

<p>附上:</p>

<p>喵了个咪的博客:<a href="w-blog.cn" rel="nofollow">w-blog.cn</a></p>

<p>RabbitMQ官网 :<a href="http://www.rabbitmq.com/" rel="nofollow">http://www.rabbitmq.com/</a> </p>



<h2 id="1用户权限体系">1.用户权限体系</h2>

<p>在RabbitMQ中提供了一套账户体系,账户体系围绕着vhost进行展开,那么vhost是什么东西,一个比较相近的意思就是<strong>虚拟空间或命名空间</strong>,每个用户都可以分配一个vhost或多个vhost的操作权限,只有拥有了操作权限这个用户才能管理这个vhost,在不同的vhost里面队列名称可以重复,所以vhost可以用来区分开发测试环境,也就是说只要部署一套rabbitMQ就可以适用多个环境</p>

<p>接下来我们创建一个管理员账户让我们可以进行后续的web界面管理时用户登录,以及使用队列时的用户</p>



<pre class="prettyprint"><code class=" hljs vala"><span class="hljs-preprocessor">#admin为新建的用户，123456为密码</span>
rabbitmqctl add_user admin <span class="hljs-number">123456</span>  

<span class="hljs-preprocessor">#将用户设置为管理员角色(管理员角色才能管理用户哦)</span>
rabbitmqctl  set_user_tags admin administrator </code></pre>



<h2 id="2-后台管理界面">2. 后台管理界面</h2>

<p>rabbitMQ提供一套功能全面的管理后台程序,我们需要开启web服务即可通过http://主机IP:15672 进行访问(建立防火墙进行拦截,通过nginx限定IP访问)</p>



<pre class="prettyprint"><code class=" hljs bash">rabbitmq-plugins enable rabbitmq_management <span class="hljs-comment">#启用web插件</span>

rabbitmq-plugins disable rabbitmq_management <span class="hljs-comment">#关闭web插件</span></code></pre>

<p>通过管理员登录就可以看到如下界面了</p>

<p><img src="http://pic.w-blog.cn/6E6E8D3D-7EF6-4B0B-B069-BCB32A97E8D2.png" alt="" title=""></p>

<p>用户管理可以看到现在的用户列表,以及各个用户可以操作的vhost</p>

<p><img src="http://pic.w-blog.cn/CC2E3363-847C-4680-A48B-267DF089A5EA.png" alt="" title=""></p>

<p>点击一个用户可以控制权限分配新的vhost</p>

<p><img src="http://pic.w-blog.cn/FE49FAE2-FFC2-4E1D-BCBD-F98908D796B8.png" alt="" title=""></p>

<p>Vhost 可以在用户下面的菜单进行创建</p>

<p><img src="http://pic.w-blog.cn/FFBB2A14-F1E0-4BA2-8508-9D72391CC636.png" alt="" title=""></p>



<h2 id="3使用队列">3.使用队列</h2>

<p>当我们单机模式准备工作完成后就可以开始使用RabbitMQ了,当然我们需要使用一门语言来开发并且需要用上SDK,RabbitMq官方提供了各类语言的扩展:<a href="http://www.rabbitmq.com/devtools.html" rel="nofollow">http://www.rabbitmq.com/devtools.html</a> </p>

<p>我们以php-amqplib为例子,使用composer进行安装</p>



<pre class="prettyprint"><code class=" hljs php"><span class="hljs-keyword">require_once</span> <span class="hljs-keyword">__DIR__</span> . <span class="hljs-string">'/vendor/autoload.php'</span>;
<span class="hljs-keyword">use</span> <span class="hljs-title">PhpAmqpLib</span>\<span class="hljs-title">Connection</span>\<span class="hljs-title">AMQPStreamConnection</span>;
<span class="hljs-keyword">use</span> <span class="hljs-title">PhpAmqpLib</span>\<span class="hljs-title">Message</span>\<span class="hljs-title">AMQPMessage</span>;

<span class="hljs-variable">$config</span> = <span class="hljs-keyword">array</span>(
    <span class="hljs-string">'host'</span>     =&gt; <span class="hljs-string">"localhost"</span>,
    <span class="hljs-string">'port'</span>     =&gt; <span class="hljs-string">"5672"</span>,
    <span class="hljs-string">'user'</span>     =&gt; <span class="hljs-string">"admin"</span>,
    <span class="hljs-string">'password'</span> =&gt; <span class="hljs-string">"123456"</span>,
    <span class="hljs-string">'vhost'</span>    =&gt; <span class="hljs-string">"/"</span>
);

<span class="hljs-comment">// 链接RabbitMQ</span>
<span class="hljs-variable">$connection</span> = <span class="hljs-keyword">new</span> AMQPStreamConnection(<span class="hljs-variable">$config</span>[<span class="hljs-string">'host'</span>], <span class="hljs-variable">$config</span>[<span class="hljs-string">'port'</span>], <span class="hljs-variable">$config</span>[<span class="hljs-string">'user'</span>], <span class="hljs-variable">$config</span>[<span class="hljs-string">'password'</span>], <span class="hljs-variable">$config</span>[<span class="hljs-string">'vhost'</span>]);
<span class="hljs-comment">// 建立通道</span>
<span class="hljs-variable">$channel</span> = <span class="hljs-variable">$this</span>-&gt;connection-&gt;channel();

<span class="hljs-comment">// 声明操作的队列，不存在会去创建 如果写入一个不存在的队列会丢失结果(此操作耗时严重谨慎使用)</span>
<span class="hljs-variable">$this</span>-&gt;channel-&gt;queue_declare(<span class="hljs-string">"test_queue"</span>, <span class="hljs-keyword">false</span>, <span class="hljs-keyword">true</span>, <span class="hljs-keyword">false</span>, <span class="hljs-keyword">false</span>);

<span class="hljs-comment">// 消息转换 有持续交付 和 非持续交付两种</span>
<span class="hljs-variable">$message</span> = <span class="hljs-keyword">new</span> AMQPMessage(<span class="hljs-string">""</span>, <span class="hljs-keyword">array</span>(<span class="hljs-string">'delivery_mode'</span> =&gt; AMQPMessage::DELIVERY_MODE_PERSISTENT));
<span class="hljs-comment">// 写入一条消息进入队列</span>
<span class="hljs-variable">$channel</span>-&gt;basic_publish(<span class="hljs-variable">$message</span>, <span class="hljs-string">''</span>, <span class="hljs-string">"test_queue"</span>);

<span class="hljs-comment">// 读取消息处理 指定需要操作的队列以及处理方法</span>
<span class="hljs-variable">$channel</span>-&gt;basic_qos(<span class="hljs-keyword">null</span>, <span class="hljs-number">1</span>, <span class="hljs-keyword">null</span>);
<span class="hljs-variable">$channel</span>-&gt;basic_consume(<span class="hljs-string">"test_queue"</span>, <span class="hljs-string">''</span>, <span class="hljs-keyword">false</span>, <span class="hljs-keyword">false</span>, <span class="hljs-keyword">false</span>, <span class="hljs-keyword">false</span>, <span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-params">(<span class="hljs-variable">$msg</span>)</span> {</span>
    <span class="hljs-variable">$data</span> = <span class="hljs-variable">$msg</span>-&gt;body;
    <span class="hljs-keyword">echo</span> <span class="hljs-variable">$data</span>;
    <span class="hljs-comment">// 告知消息已经处理完成,只有告知消息处理成功这条消息才算被消费</span>
    <span class="hljs-variable">$msg</span>-&gt;delivery_info[<span class="hljs-string">'channel'</span>]-&gt;basic_ack(<span class="hljs-variable">$msg</span>-&gt;delivery_info[<span class="hljs-string">'delivery_tag'</span>]);
});

<span class="hljs-comment">// 循环处理</span>
<span class="hljs-keyword">while</span> (count(<span class="hljs-variable">$this</span>-&gt;channel-&gt;callbacks)) {
    <span class="hljs-variable">$this</span>-&gt;channel-&gt;wait();
}
</code></pre>



<h2 id="4-总结">4 总结</h2>

<p>本小节介绍了在单机场景下用户的配置,web管理界面的使用,以及怎么使用RabbitMQ来写入消息消费消息,但是在我们面前还有一个比较重要的问题,如果RabbitMQ不幸宕机了怎么办呢?业务不就不能正常使用下去了吗?不用担心RabbitMQ也有一套灵活的集群解决方案,在下一节我们将来一同学习有关集群的一些支持,那么今天的博文就到这里,多谢大家的支持!</p>

<p>注:笔者能力有限有说的不对的地方希望大家能够指出,也希望多多交流!</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>