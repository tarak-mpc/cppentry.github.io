---
layout:     post
title:      Kafka（二）Kafka Connector与Debezium
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/lengyan110/article/details/72792571				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:32px;">Kafka Connector与Debezium</span></p>
<p><span style="font-size:24px;">1.介绍</span></p>
<p><span style="font-size:14px;">kafka connector 是连接kafka集群和其他数据库、集群等系统的连接器。kafka connector可以进行多种系统类型与kafka的连接，主要的任务包括从kafka读（sink），向kafka写（Source），所以连接器也可以分为两种：Source Connector、Sink Connector。</span></p>
<p><span style="font-size:14px;">而kafka本身自带的Connector只有三个：file connector、Sqlite connector、hdfs connector. 像连接类似于Mysql、Mongodb等数据库需要有对应的第三方库以Plugins的形式注册到kafka connector 上。在这里，我使用了debezium 来提供mysql connector 和mongodb connector的jar包。</span></p>
<p><span style="font-size:24px;">2.Quickstart</span></p>
<p><span style="font-size:14px;">在发起一个连接的过程中我们，不仅需要指明connector的配置属性，同样也需要指明kafka的schema属性，以表明以何种方式去存贮或者读取数据记录。</span></p>
<p><span style="font-size:14px;">命令格式：</span></p>
<p><span style="font-size:14px;"></span></p>
<pre style="background-color:rgb(255,255,255);font-size:14px;font-family:Consolas, 'Andale Mono WT', 'Andale Mono', 'Lucida Console', 'Lucida Sans Typewriter', 'DejaVu Sans Mono', 'Bitstream Vera Sans Mono', 'Liberation Mono', 'Nimbus Mono L', Monaco, 'Courier New', Courier, monospace;line-height:normal;overflow:auto;"><span style="color:rgb(64,64,64);">path/to/kafka/bin/[connector-standalone/connector-distributed] [schema-properties] [connector-properties]
</span>
</pre>
<br><p></p>
<p><span style="font-size:14px;"><span style="font-size:14px;">举个例子：</span><br></span></p>
<p><span style="font-size:14px;"></span></p>
<pre style="font-family:Consolas, 'Andale Mono WT', 'Andale Mono', 'Lucida Console', 'Lucida Sans Typewriter', 'DejaVu Sans Mono', 'Bitstream Vera Sans Mono', 'Liberation Mono', 'Nimbus Mono L', Monaco, 'Courier New', Courier, monospace;line-height:normal;overflow:auto;color:rgb(64,64,64);background-color:rgb(255,255,255);">./bin/connect-standalone ./etc/schema-registry/connect-avro-standalone.properties <span class="se" style="color:rgb(221,17,68);">\</span>
      ./etc/kafka/connect-file-source.properties</pre>
<pre style="font-family:Consolas, 'Andale Mono WT', 'Andale Mono', 'Lucida Console', 'Lucida Sans Typewriter', 'DejaVu Sans Mono', 'Bitstream Vera Sans Mono', 'Liberation Mono', 'Nimbus Mono L', Monaco, 'Courier New', Courier, monospace;line-height:normal;overflow:auto;color:rgb(64,64,64);background-color:rgb(255,255,255);"></pre>
在这种运行方式中，我们的kafka server存在本地，故可以直接运行对应的connect文件发起连接。不同properties的配置根据kafka connector的具体实现不同而不同。同样我们可以在kafka的Rest Api里做同样的事情，实际的生产活动中，我们也是这样做的，因为kafka server不可能部署在本地。
<p></p>
<p><span style="font-size:14px;">Rest Api命令格式：</span></p>
<p><span style="font-size:14px;">curl -X POST [data] http://[IP]:[PORT]</span></p>
<p><span style="font-size:14px;">默认情况下，kafka的端口为8083，我们可以通过REST Api去发起、终止和查看一个connect。这部分可以参考官方文档。</span></p>
<p></p>
<p><span style="font-size:24px;">3.Debezium的安装</span></p>
<p><span style="font-size:14px;">在Kafka中，debezium以Plugins的形式注册到Kafka Connector上面，具体的做法只需要到ClassPath中注册debezium的位置，并将jar包移动至/path/to/kafka/share/java/kafka-connect开头的自定义文件夹中。</span></p>
<p></p>
<p><span style="font-size:24px;">4.Kafka Connector工作原理及部分源码解析</span></p>
<p><span style="font-size:14px;">秉承“<span style="font-size:14px;">源码之前，毫无秘密</span>”的原则，现在我们来看Kafka Connector的启动过程，我们以这里作为切入点，来一览全局。</span></p>
<p><span style="font-size:14px;">首先在Rest Api中调用Kafka的StartConnector建立连接：</span></p>
<p><span style="font-size:14px;"></span></p>
<pre><code class="language-java">private boolean startConnector(String connectorName) {
        log.info("Starting connector {}", connectorName);
        final Map&lt;String, String&gt; configProps = configState.connectorConfig(connectorName);
        final ConnectorContext ctx = new HerderConnectorContext(this, connectorName);
        final TargetState initialState = configState.targetState(connectorName);
        boolean started = worker.startConnector(connectorName, configProps, ctx, this, initialState);

        // Immediately request configuration since this could be a brand new connector. However, also only update those
        // task configs if they are actually different from the existing ones to avoid unnecessary updates when this is
        // just restoring an existing connector.
        if (started &amp;&amp; initialState == TargetState.STARTED)
            reconfigureConnectorTasksWithRetry(connectorName);

        return started;
    }</code></pre><br>
在这段代码中，我们可以清晰的看到它做了这几件事：设置连接的属性，取得连接的名称和句柄，设置目标状态，开始一个Worker的连接，设置成功之后检查Connect的状态，然后设置Task。
<p></p>
<p><span style="font-size:14px;">这部分会对连接的属性进行简单赋值，我们展开讲下Connect的状态，Connect的状态分为STARTED和PAUSED。kafka connector内部通过状态机思想去控制每个connector；task亦然。</span></p>
<p><span style="font-size:14px;">在worker中的startConnector就较为复杂：</span></p>
<p><span style="font-size:14px;"></span></p>
<pre><code class="language-java">public boolean startConnector(
            String connName,
            Map&lt;String, String&gt; connProps,
            ConnectorContext ctx,
            ConnectorStatus.Listener statusListener,
            TargetState initialState
    ) {
        if (connectors.containsKey(connName))
            throw new ConnectException("Connector with name " + connName + " already exists");

        final WorkerConnector workerConnector;
        ClassLoader savedLoader = plugins.currentThreadLoader();
        try {
            final ConnectorConfig connConfig = new ConnectorConfig(plugins, connProps);
            final String connClass = connConfig.getString(ConnectorConfig.CONNECTOR_CLASS_CONFIG);
            log.info("Creating connector {} of type {}", connName, connClass);
            final Connector connector = plugins.newConnector(connClass);
            workerConnector = new WorkerConnector(connName, connector, ctx, statusListener);
            log.info("Instantiated connector {} with version {} of type {}", connName, connector.version(), connector.getClass());
            savedLoader = plugins.compareAndSwapLoaders(connector);
            workerConnector.initialize(connConfig);
            workerConnector.transitionTo(initialState);
            Plugins.compareAndSwapLoaders(savedLoader);
        } catch (Throwable t) {
            log.error("Failed to start connector {}", connName, t);
            // Can't be put in a finally block because it needs to be swapped before the call on
            // statusListener
            Plugins.compareAndSwapLoaders(savedLoader);
            statusListener.onFailure(connName, t);
            return false;
        }

        WorkerConnector existing = connectors.putIfAbsent(connName, workerConnector);
        if (existing != null)
            throw new ConnectException("Connector with name " + connName + " already exists");

        log.info("Finished creating connector {}", connName);
        return true;
    }</code></pre><br><p><span style="font-size:14px;">在StartConnector中，我们可以看到它做了这样的几件事情：读取注册的Plugins并创建对应的Connector操作类，在监听到Status状态发生改变的时候创建workerConnector，初始化workConnector，run workConnector。</span></p>
<p><span style="font-size:14px;">接下来我们在回到start<span style="font-size:14px;">Connector中，看到它还执行了reconfigureConnectorTasksWithRetry,这是用于检查task在先前的历史中是否已在执行，如果是的话，会将task的属性、状态、上下文、通通载入：</span></span></p><pre><code class="language-java">    private void reconfigureConnector(final String connName, final Callback&lt;Void&gt; cb) {
        try {
            if (!worker.isRunning(connName)) {
                log.info("Skipping reconfiguration of connector {} since it is not running", connName);
                return;
            }

            Map&lt;String, String&gt; configs = configState.connectorConfig(connName);

            ConnectorConfig connConfig;
            List&lt;String&gt; sinkTopics = null;
            if (worker.isSinkConnector(connName)) {
                connConfig = new SinkConnectorConfig(plugins(), configs);
                sinkTopics = connConfig.getList(SinkConnectorConfig.TOPICS_CONFIG);
            } else {
                connConfig = new SourceConnectorConfig(plugins(), configs);
            }

            final List&lt;Map&lt;String, String&gt;&gt; taskProps
                    = worker.connectorTaskConfigs(connName, connConfig.getInt(ConnectorConfig.TASKS_MAX_CONFIG), sinkTopics);
            boolean changed = false;
            int currentNumTasks = configState.taskCount(connName);
            if (taskProps.size() != currentNumTasks) {
                log.debug("Change in connector task count from {} to {}, writing updated task configurations", currentNumTasks, taskProps.size());
                changed = true;
            } else {
                int index = 0;
                for (Map&lt;String, String&gt; taskConfig : taskProps) {
                    if (!taskConfig.equals(configState.taskConfig(new ConnectorTaskId(connName, index)))) {
                        log.debug("Change in task configurations, writing updated task configurations");
                        changed = true;
                        break;
                    }
                    index++;
                }
            }
            if (changed) {
                if (isLeader()) {
                    configBackingStore.putTaskConfigs(connName, taskProps);
                    cb.onCompletion(null, null);
                } else {
                    // We cannot forward the request on the same thread because this reconfiguration can happen as a result of connector
                    // addition or removal. If we blocked waiting for the response from leader, we may be kicked out of the worker group.
                    forwardRequestExecutor.submit(new Runnable() {
                        @Override
                        public void run() {
                            try {
                                String reconfigUrl = RestServer.urlJoin(leaderUrl(), "/connectors/" + connName + "/tasks");
                                RestServer.httpRequest(reconfigUrl, "POST", taskProps, null);
                                cb.onCompletion(null, null);
                            } catch (ConnectException e) {
                                log.error("Request to leader to reconfigure connector tasks failed", e);
                                cb.onCompletion(e, null);
                            }
                        }
                    });
                }
            }
        } catch (Throwable t) {
            cb.onCompletion(t, null);
        }
    }</code></pre>在这里面除了判断是否要恢复现场以及恢复现场的操作外，最重要的是将task的任务放到了configBackingStore的监听队列中，使得task在被监听后能够及时start，而如果这个broke不是我们选出的leader的话，它会将任务转发。
<p><span style="font-size:14px;"><span style="font-size:14px;"><br></span></span></p>
<p><span style="font-size:14px;"><span style="font-size:14px;"><span style="font-size:14px;">关于源码部分，这边我们先简单介绍了，以后有时间在补充。</span><br><br></span></span></p>
<p><span style="font-size:14px;"><br></span></p>
            </div>
                </div>