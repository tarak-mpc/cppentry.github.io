---
layout:     post
title:      Flume NG源码分析（一）基于静态properties文件的配置模块
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>日志收集是互联网公司的一个重要服务，Flume NG是Apache的顶级项目，是分布式日志收集服务的一个开源实现，具有良好的扩展性，与其他很多开源组件可以无缝集成。搜了一圈发现介绍Flume NG的文章有不少，但是深入分析Flume NG源代码的却没有。准备写一个系列分析一下Flume NG的源码。先从基础的配置模块说起。</p>
<p><br></p>
<p>Flume NG支持两种配置模式，一种是基于properties文件的静态配置，并且只加载一次。另一种是基于Guava EventBus发布订阅模式的动态配置，可运行时加载修改的配置。这篇先说说基于properties文件的静态配置。<br></p>
<p><br></p>
<p>下面这个是flume-conf.properties的一个常见配置<br></p>
<p>1. producers是agent的名字，一个agent表示一个Flume-NG的进程</p>
<p>2. producer.sources指定了这个agent监控的几个日志源，可以配置多个source</p>
<p>3. producer.channels, sinks指定了channel和sink，这些概念后面会说</p>
<p>4. producer.sources.sX.XXX指定了日志源获取的方式，对于从本地日志文件收集的方式来说，实际使用的是tail -F的命令来监控日志文件的尾部</p>
<p></p>
<pre><code class="language-html">producer.sources = s1 s2 s3
producer.channels = c
producer.sinks = r


producer.sources.s1.type = exec
producer.sources.s1.channels = c
producer.sources.s1.command = tail -F /data/logs/s1.log

producer.sources.s2.type = exec
producer.sources.s2.channels = c
producer.sources.s2.command = tail -F /data/logs/s2.log

producer.sources.s3.type = exec
producer.sources.s3.channels = c
producer.sources.s3.command = tail -F /data/logs/s3.log

producer.sinks.r.type = org.apache.flume.plugins.KafkaSink
producer.sinks.r.metadata.broker.list=server1:9092,server2:9092,server3:9092
producer.sinks.r.zk.connect=server1:2181,server2:2181,server3:2181,server4:2181,server5:2181
producer.sinks.r.partition.key=0
producer.sinks.r.partitioner.class=org.apache.flume.plugins.SinglePartition
producer.sinks.r.serializer.class=kafka.serializer.StringEncoder
producer.sinks.r.request.required.acks=0
producer.sinks.r.max.message.size=1000000
producer.sinks.r.producer.type=sync
producer.sinks.r.custom.encoding=UTF-8
producer.sinks.r.custom.topic.name=topic.xxx

#Specify the channel the sink should use
producer.sinks.r.channel = c

# Each channel's type is defined.
producer.channels.c.type = memory
producer.channels.c.capacity = 1000
</code></pre><br>
再看看如何指定的producer这个agent名字以及指定采用哪个配置文件，下面是Flume NG的启动命令，-f指定了配置文件的路径，-n指定了agent的名字，也就是flume-conf.properties里面每项配置的前缀名
<p></p>
<p></p>
<pre><code class="language-html">/flume-ng  agent -c conf -f ../conf/flume-conf.properties -n producer -Dflume.root.logger=INFO,console &gt; flume-ng.log 2&gt;&amp;1 &amp;</code></pre><br>
来看看Flume-NG是如何来获取命令行参数，以及如何把flume-conf.properties的配置转化成它内部的数据结构的。
<p></p>
<p></p>
<p class="p1">org.apache.flume.node.Application类是Flume NG的启动类，看一下它的main方法</p>
<p class="p1">1. 使用了commons-cli.jar提供的解析命令行参数的能力来解析命令行参数，把-n, -f/--conf-file, --no-reload-conf这几个配置信息读到变量</p>
<p class="p1">2. 打开由-f参数指定的配置文件，如果指定了no-reload-conf = false，也就是要运行时加载配置，就创建一个EventBus来发布和注册配置文件修改的事件，创建一个</p>
<p class="p1">PollingPropertiesFileConfigurationProvider 来轮询properties配置文件是否修改，如果修改就重新加载</p>
<p class="p1">3. no-reload-conf默认是true，也就是说默认是静态配置，只在启动时加载一次，只需要创建一个PropertiesFileConfigurationProvider来读取properties配置文件即可</p>
<p class="p1"></p>
<pre><code class="language-java"> public static void main(String[] args) {

    try {

      Options options = new Options();

      Option option = new Option("n", "name", true, "the name of this agent");
      option.setRequired(true);
      options.addOption(option);

      option = new Option("f", "conf-file", true, "specify a conf file");
      option.setRequired(true);
      options.addOption(option);

      option = new Option(null, "no-reload-conf", false, "do not reload " +
        "conf file if changed");
      options.addOption(option);

      option = new Option("h", "help", false, "display help text");
      options.addOption(option);

      CommandLineParser parser = new GnuParser();
      CommandLine commandLine = parser.parse(options, args);

      File configurationFile = new File(commandLine.getOptionValue('f'));
      String agentName = commandLine.getOptionValue('n');
      boolean reload = !commandLine.hasOption("no-reload-conf");

      if (commandLine.hasOption('h')) {
        new HelpFormatter().printHelp("flume-ng agent", options, true);
        return;
      }
      /*
       * The following is to ensure that by default the agent
       * will fail on startup if the file does not exist.
       */
      if (!configurationFile.exists()) {
        // If command line invocation, then need to fail fast
        if (System.getProperty(Constants.SYSPROP_CALLED_FROM_SERVICE) == null) {
          String path = configurationFile.getPath();
          try {
            path = configurationFile.getCanonicalPath();
          } catch (IOException ex) {
            logger.error("Failed to read canonical path for file: " + path, ex);
          }
          throw new ParseException(
              "The specified configuration file does not exist: " + path);
        }
      }
      List&lt;LifecycleAware&gt; components = Lists.newArrayList();
      Application application;
      if(reload) {
        EventBus eventBus = new EventBus(agentName + "-event-bus");
        PollingPropertiesFileConfigurationProvider configurationProvider =
            new PollingPropertiesFileConfigurationProvider(agentName,
                configurationFile, eventBus, 30);
        components.add(configurationProvider);
        application = new Application(components);
        eventBus.register(application);
      } else {
        PropertiesFileConfigurationProvider configurationProvider =
            new PropertiesFileConfigurationProvider(agentName,
                configurationFile);
        application = new Application();
        application.handleConfigurationEvent(configurationProvider.getConfiguration());
      }
      application.start();

      final Application appReference = application;
      Runtime.getRuntime().addShutdownHook(new Thread("agent-shutdown-hook") {
        @Override
        public void run() {
          appReference.stop();
        }
      });

    } catch (Exception e) {
      logger.error("A fatal error occurred while running. Exception follows.",
          e);
    }
  }</code></pre><br>
Flume NG配置相关的接口和类的结构如下
<p></p>
<p class="p1">1. ConfigurationProvider顶层接口定义了 MaterializedConfiguration getConfiguration() 方法</p>
<p class="p1">2. MaterializedConfiguration接口表示具体化的配置，也就是把flume-conf.properties配置文件里定义的配置实例化成具体的对象。SimpleMaterializedConfiguration提供了实现，维护了实际运行时的配置数据结构</p>
<p class="p1">3. AbstractConfigurationProvider实现了ConfigurationProvider接口，并定义了<span class="s1">abstract</span> FlumeConfiguration getFlumeConfiguration()抽象方法</p>
<p class="p1">4. FlumeConfiguration, AgentConfiguration, SourceConfiguration, ChannelConfiguration, SinkConfiguration这几个类用来辅助解析flume-conf.properties配置文件，保存配置定义的字段</p>
<p class="p1">5. PropertiesFileConfigurationProvider从-f/--conf指定的配置文件中读取配置信息，只在读取一次</p>
<p class="p1">6. PollingPropertiesFileConfigurationProvider 采用轮询的方式从配置文件中读取配置信息，并支持动态修改配置</p>
<p class="p1"><img src="https://img-blog.csdn.net/20150608165205146?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSVRlcl9aQw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<br><p></p>
<p class="p1">PropertiesFileConfigurationProvider的实现很简单</p>
<p class="p1">1. 首先是getFlumeConfiguration方法读取properties文件，然后转化成FlumeConfiguration结构的对象</p>
<p class="p1">2. 在 父类AbstractConfigurationProvider的getConfiguration方法生成MaterializedConfiguration实例，也就是创建实际运行时的Channel, SourceRunner, SinkRunner对象，它会从FlumeConfiguration中去读取各个对象的字段</p>
<p class="p1"></p>
<pre><code class="language-java">public FlumeConfiguration getFlumeConfiguration() {
    BufferedReader reader = null;
    try {
      reader = new BufferedReader(new FileReader(file));
      Properties properties = new Properties();
      properties.load(reader);
      return new FlumeConfiguration(toMap(properties));
    } catch (IOException ex) {
      LOGGER.error("Unable to load file:" + file
          + " (I/O failure) - Exception follows.", ex);
    } finally {
      if (reader != null) {
        try {
          reader.close();
        } catch (IOException ex) {
          LOGGER.warn(
              "Unable to close file reader for file: " + file, ex);
        }
      }
    }
    return new FlumeConfiguration(new HashMap&lt;String, String&gt;());
  }

public MaterializedConfiguration getConfiguration() {
    MaterializedConfiguration conf = new SimpleMaterializedConfiguration();
    FlumeConfiguration fconfig = getFlumeConfiguration();
    AgentConfiguration agentConf = fconfig.getConfigurationFor(getAgentName());
    if (agentConf != null) {
      Map&lt;String, ChannelComponent&gt; channelComponentMap = Maps.newHashMap();
      Map&lt;String, SourceRunner&gt; sourceRunnerMap = Maps.newHashMap();
      Map&lt;String, SinkRunner&gt; sinkRunnerMap = Maps.newHashMap();
      try {
        loadChannels(agentConf, channelComponentMap);
        loadSources(agentConf, channelComponentMap, sourceRunnerMap);
        loadSinks(agentConf, channelComponentMap, sinkRunnerMap);
        Set&lt;String&gt; channelNames =
            new HashSet&lt;String&gt;(channelComponentMap.keySet());
        for(String channelName : channelNames) {
          ChannelComponent channelComponent = channelComponentMap.
              get(channelName);
          if(channelComponent.components.isEmpty()) {
            LOGGER.warn(String.format("Channel %s has no components connected" +
                " and has been removed.", channelName));
            channelComponentMap.remove(channelName);
            Map&lt;String, Channel&gt; nameChannelMap = channelCache.
                get(channelComponent.channel.getClass());
            if(nameChannelMap != null) {
              nameChannelMap.remove(channelName);
            }
          } else {
            LOGGER.info(String.format("Channel %s connected to %s",
                channelName, channelComponent.components.toString()));
            conf.addChannel(channelName, channelComponent.channel);
          }
        }
        for(Map.Entry&lt;String, SourceRunner&gt; entry : sourceRunnerMap.entrySet()) {
          conf.addSourceRunner(entry.getKey(), entry.getValue());
        }
        for(Map.Entry&lt;String, SinkRunner&gt; entry : sinkRunnerMap.entrySet()) {
          conf.addSinkRunner(entry.getKey(), entry.getValue());
        }
      } catch (InstantiationException ex) {
        LOGGER.error("Failed to instantiate component", ex);
      } finally {
        channelComponentMap.clear();
        sourceRunnerMap.clear();
        sinkRunnerMap.clear();
      }
    } else {
      LOGGER.warn("No configuration found for this host:{}", getAgentName());
    }
    return conf;
  }
</code></pre><pre><code class="language-java">&lt;/pre&gt;&lt;pre name="code" class="java"&gt;</code></pre><br><div id="xunlei_com_thunder_helper_plugin_d462f475-c18e-46be-bd10-327458d045bd"></div>
<div></div>
<div></div>
            </div>
                </div>