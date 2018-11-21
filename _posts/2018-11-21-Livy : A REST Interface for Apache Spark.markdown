---
layout:     post
title:      Livy : A REST Interface for Apache Spark
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p style="margin-left:0pt;">官网：http://livy.incubator.apache.org/</p>

<p style="margin-left:0pt;">Github：<a href="https://github.com/apache/incubator-livy" rel="nofollow"><u><span style="color:#000000;"><u>https://github.com/apache/incubator-livy</u></span></u></a></p>

<p style="margin-left:0pt;"><strong>概述：</strong></p>

<p style="margin-left:0pt;">    当前spark上的管控平台有spark job server，zeppelin，由于spark job server和zeppelin都存在一些缺陷，比如spark job server不支持提交sql，zeppelin不支持jar包方式提交，并且它们都不支持yarn cluster模式，只能用client的模式运行，这会严重影响扩展性。针对这些问题，cloudera研发了Livy，Livy结合了spark job server和Zeppelin的优点，并解决了spark job server和Zeppelin的缺点，下面是他们的对比。</p>

<table border="1" cellspacing="0" style="width:426.1pt;"><tbody><tr><td style="vertical-align:top;width:120.8pt;">
			<p style="margin-left:0pt;"> </p>
			</td>
			<td style="vertical-align:top;width:92.2pt;">
			<p style="margin-left:0pt;">Spark job server</p>
			</td>
			<td style="vertical-align:top;width:106.55pt;">
			<p style="margin-left:0pt;">Zeppelin</p>
			</td>
			<td style="vertical-align:top;width:106.55pt;">
			<p style="margin-left:0pt;">Livy</p>
			</td>
		</tr><tr><td style="vertical-align:top;width:120.8pt;">
			<p style="margin-left:0pt;">是否支持jar包提交</p>
			</td>
			<td style="vertical-align:top;width:92.2pt;">
			<p style="margin-left:0pt;">支持</p>
			</td>
			<td style="vertical-align:top;width:106.55pt;">
			<p style="margin-left:0pt;">不支持</p>
			</td>
			<td style="vertical-align:top;width:106.55pt;">
			<p style="margin-left:0pt;">支持</p>
			</td>
		</tr><tr><td style="vertical-align:top;width:120.8pt;">
			<p style="margin-left:0pt;">是否支持代码段提交</p>
			</td>
			<td style="vertical-align:top;width:92.2pt;">
			<p style="margin-left:0pt;">不支持</p>
			</td>
			<td style="vertical-align:top;width:106.55pt;">
			<p style="margin-left:0pt;">支持</p>
			</td>
			<td style="vertical-align:top;width:106.55pt;">
			<p style="margin-left:0pt;">支持</p>
			</td>
		</tr><tr><td style="vertical-align:top;width:120.8pt;">
			<p style="margin-left:0pt;">是否支持SparkContext重用和管理</p>
			</td>
			<td style="vertical-align:top;width:92.2pt;">
			<p style="margin-left:0pt;">支持</p>
			</td>
			<td style="vertical-align:top;width:106.55pt;">
			<p style="margin-left:0pt;">支持重用，不能stop</p>
			</td>
			<td style="vertical-align:top;width:106.55pt;">
			<p style="margin-left:0pt;">支持</p>
			</td>
		</tr><tr><td style="vertical-align:top;width:120.8pt;">
			<p style="margin-left:0pt;">运行模式</p>
			</td>
			<td style="vertical-align:top;width:92.2pt;">
			<p style="margin-left:0pt;">Client</p>
			</td>
			<td style="vertical-align:top;width:106.55pt;">
			<p style="margin-left:0pt;">Client</p>
			</td>
			<td style="vertical-align:top;width:106.55pt;">
			<p style="margin-left:0pt;">Client/Cluster</p>
			</td>
		</tr><tr><td style="vertical-align:top;width:120.8pt;">
			<p style="margin-left:0pt;">多个SparkContext</p>

			<p style="margin-left:0pt;">运行</p>
			</td>
			<td style="vertical-align:top;width:92.2pt;">
			<p style="margin-left:0pt;">在同一个JVM，可能存在问题</p>
			</td>
			<td style="vertical-align:top;width:106.55pt;">
			<p style="margin-left:0pt;">运行在不同JVM</p>
			</td>
			<td style="vertical-align:top;width:106.55pt;">
			<p style="margin-left:0pt;">运行在不同JVM</p>
			</td>
		</tr><tr><td style="vertical-align:top;width:120.8pt;">
			<p style="margin-left:0pt;">接口</p>
			</td>
			<td style="vertical-align:top;width:92.2pt;">
			<p style="margin-left:0pt;">RESTFul</p>
			</td>
			<td style="vertical-align:top;width:106.55pt;">
			<p style="margin-left:0pt;">RESTFul/WebSocket</p>
			</td>
			<td style="vertical-align:top;width:106.55pt;">
			<p style="margin-left:0pt;">RESTFul</p>
			</td>
		</tr><tr><td style="vertical-align:top;width:120.8pt;">
			<p style="margin-left:0pt;">SQL是否支持</p>
			</td>
			<td style="vertical-align:top;width:92.2pt;">
			<p style="margin-left:0pt;">不支持</p>
			</td>
			<td style="vertical-align:top;width:106.55pt;">
			<p style="margin-left:0pt;">支持</p>
			</td>
			<td style="vertical-align:top;width:106.55pt;">
			<p style="margin-left:0pt;">支持</p>
			</td>
		</tr></tbody></table><p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">下面是Livy架构图</p>

<p style="margin-left:0pt;"><img alt="" class="has" src="https://img-blog.csdnimg.cn/20181108161605330.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpYW9kYW9sdXl1bg==,size_16,color_FFFFFF,t_70"></p>

<p style="margin-left:0pt;">client将在初始化时创建一个远程Spark集群，并通过REST api提交作业。Livy服务器将打开、包装这个job，并通过RPC发送到远程SparkContext，代码也将在那里执行。同时，client被阻塞，等待此job运行的结果。</p>

<p style="margin-left:0pt;"><strong>Livy提供了三种模式去运行spark job：</strong></p>

<ol><li>Using the Programmatic API，通过程序接口提交作业，需要继承com.cloudera.livy.Job接口编程，通过LivyClient提交</li>
	<li>使用RESTAPI的session接口提交代码段方式运行</li>
	<li>使用TESTAPI的batch接口提交jar包方式运行</li>
</ol><p style="margin-left:0pt;"><strong>配置Livy：</strong></p>

<p style="margin-left:0pt;">  1.修改LIVY_HOME/conf/livy-env.sh,导入环境变量，cdh中的spark</p>

<p style="margin-left:0pt;"><span style="color:#c00000;">export HADOOP_CONF_DIR=/etc/hadoop/conf</span></p>

<p style="margin-left:0pt;"><span style="color:#c00000;">export SPARK_HOME=</span></p>

<p style="margin-left:0pt;"><span style="color:#c00000;">            /opt/cloudera/parcels/SPARK2-2.3.0.cloudera3-1.cdh5.13.3.p0.458809/lib/spark2/</span></p>

<p style="margin-left:0pt;"><span style="color:#c00000;">export SPARK_CONF_DIR=</span></p>

<p style="margin-left:0pt;"><span style="color:#c00000;">        /opt/cloudera/parcels/SPARK2-2.3.0.cloudera3-1.cdh5.13.3.p0.458809/lib/spark2/conf</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"> <strong>修改LIVY_HOME/conf/livy.conf</strong></p>

<p style="text-indent:0;">    <span style="color:#c00000;">livy.server.host = 10.18.0.11</span></p>

<p style="text-indent:0;"><span style="color:#c00000;">    livy.spark.master = yarn</span></p>

<p style="text-indent:0;"><span style="color:#c00000;">    livy.spark.deploy-mode = cluster</span></p>

<p style="margin-left:0pt;"><span style="color:#000000;">从架构图可以看出，当Livy服务器出现故障时，所有连接的Spark集群也会终止，这意味着所有的作业和数据都会立即消失。在Livy0.3.0引入了一个会话恢复机制，以确保在Livy server不可用时Spark集群仍然有效，并且在重新启动Livy server后可以连接到现有的会话，并回滚到失败之前的状态。所以还应该设置以下3种配置：</span></p>

<p style="margin-left:0pt;"><span style="color:#000000;"># 默认：off，Livy server挂掉，所有session都会stop，session状态也会丢失</span></p>

<p style="margin-left:0pt;"><span style="color:#000000;">#  recovery ：</span><span style="color:#2e3033;">Livy将会话信息持久化</span><span style="color:#2e3033;">到</span><span style="color:#2e3033;">state-store</span><span style="color:#2e3033;">。当Livy重新启动时，它会</span><span style="color:#2e3033;">从</span><span style="color:#2e3033;">state-store</span><span style="color:#2e3033;">恢复之前的会话。</span></p>

<p style="margin-left:0pt;"><strong><span style="color:#c00000;"><strong>livy.server.recovery.mode = recovery</strong></span></strong></p>

<p style="margin-left:0pt;"><span style="color:#000000;"># Livy会话状态存储的地方</span></p>

<p style="margin-left:0pt;"><span style="color:#000000;">#默认：&lt;empty&gt;, 不存储</span></p>

<p style="margin-left:0pt;"><span style="color:#000000;"># filesystem：存储到本地文件系统</span></p>

<p style="margin-left:0pt;"><span style="color:#000000;"># zookeeper：存储到zookeeper中</span></p>

<p style="margin-left:0pt;"><strong><span style="color:#c00000;"><strong>livy.server.recovery.state-store = zookeeper</strong></span></strong></p>

<p style="margin-left:0pt;"><span style="color:#000000;">#文件系统路径或zookeeper的host</span></p>

<p style="margin-left:0pt;"><strong><span style="color:#c00000;"><strong>livy.server.recovery.state-store.url = zkHost1:2181,zkHost2:2181,zkHost3:2181</strong></span></strong></p>

<p style="margin-left:0pt;"><span style="color:#000000;">二、初始化资源：</span></p>

<p style="margin-left:0pt;"><span style="color:#000000;">在yarn中初始化一个spark应用，持久化的占有一定的资源。有一个sc（SparkContext）和spark（SparkSession），类似于spark-shell，在代码块中直接使用即可。</span></p>

<p style="margin-left:0pt;"><span style="color:#000000;">以下使用python语言，使用REST API方式，其他的方式可以看官网</span></p>

<pre class="has">
<code class="language-python">import requests, json, textwrap, pprint

"""
1.定义spark应用的资源情况
定义一个dict, 更多参数可以去官网或github上查看，类似于spark-submit
如果要导入自定义的模块，要放到hdfs上
"""
data = { "pyFiles":["hdfs://localhost:8020/***/lib.zip"], # list of string
         "executorMemory":"2G",
         "executorCores":2,
         "numExecutors":3
        }
"""
问题：使用pyFiles添加依赖，好像没用，还是报模块找不到。
解决：在代码块中使用 sc.addPyFile(path), 必须在import 依赖的包之前，见下方代码块
"""

#2.发送请求，创建spark应用

livy_host = "http://10.18.0.11:8998"
headers = {"Content-Type":"application/json"}

#创建一个spark应用  POST livy_host/sessions
rp = requests.post(url=livy_host+"/sessions",data=json.dumps(data),headers=headers)

#查看spark应用状态  GET livy_host/sessions/{sessionId}
session_url = livy_host+rp.headers['location']
rp_se = requests.get(url=session_url,headers=headers)
pprint.pprint(rp_se.json())

#3.向spark应用提交代码块

#定义提交的代码块 在代码块中直接使用sc（SparkContext）、spark（SparkSession）
py_code=textwrap.dedent("""
#导入自定义module
#sc.addPyFile(path)
#from lib import *

#官网的例子  计算圆的Pi
import random
    NUM_SAMPLES = 100000
    def sample(p):
      x, y = random.random(), random.random()
      return 1 if x*x + y*y &lt; 1 else 0

    count = sc.parallelize(xrange(0, NUM_SAMPLES)).map(sample).reduce(lambda a, b: a + b)
    print "Pi is roughly %f" % (4.0 * count / NUM_SAMPLES)
""")

body = {
        "kind":"pyspark",  #定义代码块中代码类型，spark、pyspark、sparkr，sql
        "code":py_code
       }

# POST livy_host/sessions/{sessionId}/statements
statements_url = session_url+"/statements"

#提交代码块
rp_st = requests.post(url=statements_url,data=json.dumps(body),headers=headers)

#查看程序运行状态   GET livy_host/sessions/{sessionId}/statements/{statementId}
result_url = livy_host+rp_st.headers['location']

flag = True
while flag:
        result = requests.get(url=result_url,headers=headers)
        result = result.json()
        if result['state']=="available":
            flag = False
        print(f"progress:{result['progress']}")
        time.sleep(1)
pprint.pprint(result)</code></pre>

<p> </p>            </div>
                </div>