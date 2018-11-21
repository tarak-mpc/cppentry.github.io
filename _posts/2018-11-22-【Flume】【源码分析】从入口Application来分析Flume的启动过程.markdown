---
layout:     post
title:      【Flume】【源码分析】从入口Application来分析Flume的启动过程
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/chiweitree/article/details/42970373				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h3>大家在启动flume的时候，输入的命令就可以看出flume的启动入口了</h3>
<p></p>
<pre><code class="language-plain">[root@com21 apache-flume-1.5.2-bin]# sh bin/flume-ng agent -c conf -f conf/server.conf -n a1
Info: Sourcing environment configuration script /home/flume/apache-flume-1.5.2-bin/conf/flume-env.sh
+ exec /home/flume/jdk1.7.0_71/bin/java -server -Xms2048m -Xmx2048m -Xss256K -XX:PermSize=32M -XX:MaxPermSize=512M -XX:+UseConcMarkSweepGC -XX:+DisableExplicitGC -XX:+UseParNewGC -XX:+CMSClassUnloadingEnabled -XX:+CMSParallelRemarkEnabled -XX:+UseCMSCompactAtFullCollection -XX:+UseFastAccessorMethods -XX:+UseCMSInitiatingOccupancyOnly -XX:+UseCompressedOops -XX:CMSInitiatingOccupancyFraction=70 -XX:+HeapDumpOnOutOfMemoryError -XX:SurvivorRatio=8 -cp '/home/flume/apache-flume-1.5.2-bin/conf:/home/flume/apache-flume-1.5.2-bin/lib/*' -Djava.library.path= org.apache.flume.node.Application -f conf/server.conf -n a1</code></pre>
<p></p>
<p>从这里可以看出flume的启动入口是：org.apache.flume.node.Application</p>
<p>下面我们就来看该入口程序是如何来运行的</p>
<h3>找到main函数</h3>
<p>附：flume每次启动都会先判断有没有与当前配置的三大组件同名的组件存在，存在的话<strong><span style="color:#ff0000;">先停掉该组件，顺序为source，sink，channel</span></strong></p>
<p>其次是启动所有当前配置的组件，<span style="color:#ff0000;"><strong>启动顺序为channel，sink，source</strong></span></p>
<p>以上启动顺序来源如下：</p>
<p></p><pre><code class="language-java">public synchronized void handleConfigurationEvent(MaterializedConfiguration conf) {
    stopAllComponents();
    startAllComponents(conf);
  }</code></pre>
<p>通过这个启动停止的顺序可以看出flume也是对数据一致性做了保证的。<br></p>
<h3>1、读取启动脚本中的参数，这一步很简单，看源码就能了解，这就不多说了</h3>
<h3>2、如果启动参数含有no-reload-conf，则每隔30秒检查一次conf配置文件</h3>
<p>这里是通过事件总线的机制来启动的</p>
<pre><code class="language-java">if(reload) {
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
      }</code></pre>
<p>PollingPropertiesFileConfigurationProvider该类是一个轮询操作，每隔30秒会去检查conf配置文件<br></p>
<p>start方法里</p>
<p></p><pre><code class="language-java"> FileWatcherRunnable fileWatcherRunnable =
        new FileWatcherRunnable(file, counterGroup);

    executorService.scheduleWithFixedDelay(fileWatcherRunnable, 0, interval,
        TimeUnit.SECONDS);</code></pre>FileWatcherRunnable是一个线程，看下它的run方法的逻辑
<p></p><pre><code class="language-java"> public void run() {
      LOGGER.debug("Checking file:{} for changes", file);

      counterGroup.incrementAndGet("file.checks");

      long lastModified = file.lastModified();

      if (lastModified &gt; lastChange) {
        LOGGER.info("Reloading configuration file:{}", file);

        counterGroup.incrementAndGet("file.loads");

        lastChange = lastModified;

        try {
          eventBus.post(getConfiguration());
        } catch (Exception e) {
          LOGGER.error("Failed to load configuration data. Exception follows.",
              e);
        } catch (NoClassDefFoundError e) {
          LOGGER.error("Failed to start agent because dependencies were not " +
              "found in classpath. Error follows.", e);
        } catch (Throwable t) {
          // caught because the caller does not handle or log Throwables
          LOGGER.error("Unhandled error", t);
        }
      }
    }</code></pre>这里通过文件修改时间来判断是否配置文件被修改了，然后通过事件总线的post调用EventHandler，也就是被@Subscribe注解的方法
<p></p><pre><code class="language-java">@Subscribe
  public synchronized void handleConfigurationEvent(MaterializedConfiguration conf) {
    stopAllComponents();
    startAllComponents(conf);
  }</code></pre>这里会重新停掉所有组件，然后重新启动所有组件
<p>else分支和if是一样的道理，我就不说了</p>
<h3>3、再看if else后面的是application.start()方法</h3>
<p></p><pre><code class="language-java">public synchronized void start() {
    for(LifecycleAware component : components) {
      supervisor.supervise(component,
          new SupervisorPolicy.AlwaysRestartPolicy(), LifecycleState.START);
    }
  }</code></pre>这是对所有组件进行监督supervise，只有在flume启动或者配置发生更改的时候会调用此监督方法
<p></p><pre><code class="language-java">MonitorRunnable monitorRunnable = new MonitorRunnable();
    monitorRunnable.lifecycleAware = lifecycleAware;
    monitorRunnable.supervisoree = process;
    monitorRunnable.monitorService = monitorService;

    supervisedProcesses.put(lifecycleAware, process);

    ScheduledFuture&lt;?&gt; future = monitorService.scheduleWithFixedDelay(
        monitorRunnable, 0, 3, TimeUnit.SECONDS);
    monitorFutures.put(lifecycleAware, future);</code></pre>方法里将每个组件纳入了生命周期的管理中，每隔3秒会执行以下方法【在停止组件的时候，会调用unsupervisor方法，会给各个组件状态赋值】：
<p></p><pre><code class="language-java">public void run() {
      logger.debug("checking process:{} supervisoree:{}", lifecycleAware,
          supervisoree);

      long now = System.currentTimeMillis();

      try {
        if (supervisoree.status.firstSeen == null) {
          logger.debug("first time seeing {}", lifecycleAware);

          supervisoree.status.firstSeen = now;
        }

        supervisoree.status.lastSeen = now;
        synchronized (lifecycleAware) {
          if (supervisoree.status.discard) {
            // Unsupervise has already been called on this.
            logger.info("Component has already been stopped {}", lifecycleAware);
            return;
          } else if (supervisoree.status.error) {
            logger.info("Component {} is in error state, and Flume will not"
                + "attempt to change its state", lifecycleAware);
            return;
          }

          supervisoree.status.lastSeenState = lifecycleAware.getLifecycleState();

          if (!lifecycleAware.getLifecycleState().equals(
              supervisoree.status.desiredState)) {

            logger.debug("Want to transition {} from {} to {} (failures:{})",
                new Object[] { lifecycleAware, supervisoree.status.lastSeenState,
                    supervisoree.status.desiredState,
                    supervisoree.status.failures });

            switch (supervisoree.status.desiredState) {
              case START:
                try {
                  lifecycleAware.start();
                } catch (Throwable e) {
                  logger.error("Unable to start " + lifecycleAware
                      + " - Exception follows.", e);
                  if (e instanceof Error) {
                    // This component can never recover, shut it down.
                    supervisoree.status.desiredState = LifecycleState.STOP;
                    try {
                      lifecycleAware.stop();
                      logger.warn("Component {} stopped, since it could not be"
                          + "successfully started due to missing dependencies",
                          lifecycleAware);
                    } catch (Throwable e1) {
                      logger.error("Unsuccessful attempt to "
                          + "shutdown component: {} due to missing dependencies."
                          + " Please shutdown the agent"
                          + "or disable this component, or the agent will be"
                          + "in an undefined state.", e1);
                      supervisoree.status.error = true;
                      if (e1 instanceof Error) {
                        throw (Error) e1;
                      }
                      // Set the state to stop, so that the conf poller can
                      // proceed.
                    }
                  }
                  supervisoree.status.failures++;
                }
                break;
              case STOP:
                try {
                  lifecycleAware.stop();
                } catch (Throwable e) {
                  logger.error("Unable to stop " + lifecycleAware
                      + " - Exception follows.", e);
                  if (e instanceof Error) {
                    throw (Error) e;
                  }
                  supervisoree.status.failures++;
                }
                break;
              default:
                logger.warn("I refuse to acknowledge {} as a desired state",
                    supervisoree.status.desiredState);
            }

            if (!supervisoree.policy.isValid(lifecycleAware, supervisoree.status)) {
              logger.error(
                  "Policy {} of {} has been violated - supervisor should exit!",
                  supervisoree.policy, lifecycleAware);
            }
          }
        }
      } catch(Throwable t) {
        logger.error("Unexpected error", t);
      }
      logger.debug("Status check complete");
    }</code></pre>
<h4>1、判断组件状态</h4>
<p></p>
<h4>2、如果组件当前状态不是组件预期的状态，那么就要对预期状态按照switch分支来执行相应的逻辑</h4>
<p>比如start执行了，ExecSource的start方法</p>
<p></p><pre><code class="language-java"> public void start() {
    logger.info("Exec source starting with command:{}", command);

    executor = Executors.newSingleThreadExecutor();

    runner = new ExecRunnable(shell, command, getChannelProcessor(), sourceCounter,
        restart, restartThrottle, logStderr, bufferCount, batchTimeout, charset);

    // FIXME: Use a callback-like executor / future to signal us upon failure.
    runnerFuture = executor.submit(runner);

    /*
     * NB: This comes at the end rather than the beginning of the method because
     * it sets our state to running. We want to make sure the executor is alive
     * and well first.
     */
    sourceCounter.start();
    super.start();

    logger.debug("Exec source started");
  }</code></pre>这样就将一个Source的组件启动了，其它组件类似<br><br>
注意：
<p><strong><span style="color:#ff0000;">组件生命周期的监督，只会在flume启动所有组件的时候随之启动一次</span></strong><br><br></p>
<p></p>
<p><br></p>
<p>至此，flume中三大组件的交互以及交互频率就说完了，望各位网友不吝指教！！</p>
<p><br></p>
            </div>
                </div>