---
layout:     post
title:      对kafka中zookeeper启动过程解析
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>首先kafka中zookeeper启动的命令是：</p>
<p><strong><span style="font-size:18px;">/usr/local/kafka/bin/zookeeper-server-start.sh /usr/local/kafka/config/zookeeper.properties &amp;</span></strong><br></p>
<p><strong><span style="font-size:18px;">查看<strong><span style="font-size:18px;">zookeeper-server-start.sh里面最后一行是：</span></strong></span></strong></p>
<p><strong><span style="font-size:18px;"><strong><span style="font-size:18px;">exec $base_dir/kafka-run-class.sh $EXTRA_ARGS org.apache.zookeeper.server.quorum.QuorumPeerMain $@<br></span></strong></span></strong></p>
<p><span style="font-weight:bold;"><span style="font-size:18px;"><span style="font-weight:bold;"><span style="font-size:18px;">它去执行了<strong><span style="font-size:18px;"><span style="font-size:18px;">org.apache.zookeeper.server.quorum.QuorumPeerMain 这个main方法，那我们就跑到这个main里面去看下究竟吧</span></span></strong></span></span></span></span></p>
<p><span style="font-weight:bold;"><span style="font-size:18px;"><span style="font-weight:bold;"><span style="font-size:18px;"><strong><span style="font-size:18px;"><span style="font-size:18px;"><br></span></span></strong></span></span></span></span></p>
<p><span style="font-weight:bold;"><span style="font-size:18px;"><span style="font-weight:bold;"><span style="font-size:18px;"><span style="font-weight:bold;"><span style="font-size:18px;"><span style="font-size:18px;"></span></span></span></span></span></span></span></p>
<pre><code class="language-java">    /**
     * To start the replicated server specify the configuration file name on
     * the command line.
     * @param args path to the configfile
     */
    public static void main(String[] args) {
        QuorumPeerMain main = new QuorumPeerMain();
        try {
            main.initializeAndRun(args);
        } catch (IllegalArgumentException e) {
            LOG.fatal("Invalid arguments, exiting abnormally", e);
            LOG.info(USAGE);
            System.err.println(USAGE);
            System.exit(2);
        } catch (ConfigException e) {
            LOG.fatal("Invalid config, exiting abnormally", e);
            System.err.println("Invalid config, exiting abnormally");
            System.exit(2);
        } catch (Exception e) {
            LOG.fatal("Unexpected exception, exiting abnormally", e);
            System.exit(1);
        }
        LOG.info("Exiting normally");
        System.exit(0);
    }</code></pre><br><p><br></p>
<p><br></p>
<p></p>
<pre><code class="language-java">    protected void initializeAndRun(String[] args)
        throws ConfigException, IOException
    {
        QuorumPeerConfig config = new QuorumPeerConfig();
        if (args.length == 1) {
            config.parse(args[0]);
        }

        if (args.length == 1 &amp;&amp; config.servers.size() &gt; 0) {
            runFromConfig(config);
        } else {
            LOG.warn("Either no config or no quorum defined in config, running "
                    + " in standalone mode");
            // there is only server in the quorum -- run as standalone
            ZooKeeperServerMain.main(args);
        }
    }</code></pre><strong><span style="font-size:18px;">从启动的log上看到:WARN Either no config or no quorum defined in config, running  in standalone mode (org.apache.zookeeper.server.quorum.QuorumPeerMain)<br></span></strong>
<p></p>
<p><strong><span style="font-size:18px;">接下来让我们去</span><span style="font-size:18px;">ZooKeeperServerMain.main(args)继续看吧</span></strong></p>
<p><span style="font-size:18px;"><strong></strong></span></p>
<pre><code class="language-java">    protected void initializeAndRun(String[] args)
        throws ConfigException, IOException
    {
        try {
            ManagedUtil.registerLog4jMBeans();
        } catch (JMException e) {
            LOG.warn("Unable to register log4j JMX control", e);
        }

        ServerConfig config = new ServerConfig();
        if (args.length == 1) {
            config.parse(args[0]);
        } else {
            config.parse(args);
        }

        runFromConfig(config);
    }</code></pre><br>
ServerConfig去读取配置文件
<p></p>
<p><span style="font-size:18px;"><strong></strong></span></p>
<pre><code class="language-java">    public void parseProperties(Properties zkProp)
    throws IOException, ConfigException {
        int clientPort = 0;
        String clientPortAddress = null;
        for (Entry&lt;Object, Object&gt; entry : zkProp.entrySet()) {
            String key = entry.getKey().toString().trim();
            String value = entry.getValue().toString().trim();
            if (key.equals("dataDir")) {
                dataDir = value;
            } else if (key.equals("dataLogDir")) {
                dataLogDir = value;
            } else if (key.equals("clientPort")) {
                clientPort = Integer.parseInt(value);
            } else if (key.equals("clientPortAddress")) {
                clientPortAddress = value.trim();
            } else if (key.equals("tickTime")) {
                tickTime = Integer.parseInt(value);
            } else if (key.equals("maxClientCnxns")) {
                maxClientCnxns = Integer.parseInt(value);
            } else if (key.equals("minSessionTimeout")) {
                minSessionTimeout = Integer.parseInt(value);
            } else if (key.equals("maxSessionTimeout")) {
                maxSessionTimeout = Integer.parseInt(value);
            } else if (key.equals("initLimit")) {
                initLimit = Integer.parseInt(value);
            } else if (key.equals("syncLimit")) {
                syncLimit = Integer.parseInt(value);
            } else if (key.equals("electionAlg")) {
                electionAlg = Integer.parseInt(value);
            } else if (key.equals("peerType")) {
                if (value.toLowerCase().equals("observer")) {
                    peerType = LearnerType.OBSERVER;
                } else if (value.toLowerCase().equals("participant")) {
                    peerType = LearnerType.PARTICIPANT;
                } else
                {
                    throw new ConfigException("Unrecognised peertype: " + value);
                }
            } else if (key.startsWith("server.")) {
                int dot = key.indexOf('.');
                long sid = Long.parseLong(key.substring(dot + 1));
                String parts[] = value.split(":");
                if ((parts.length != 2) &amp;&amp; (parts.length != 3) &amp;&amp; (parts.length !=4)) {
                    LOG.error(value
                       + " does not have the form host:port or host:port:port " +
                       " or host:port:port:type");
                }
                InetSocketAddress addr = new InetSocketAddress(parts[0],
                        Integer.parseInt(parts[1]));
                if (parts.length == 2) {
                    servers.put(Long.valueOf(sid), new QuorumServer(sid, addr));
                } else if (parts.length == 3) {
                    InetSocketAddress electionAddr = new InetSocketAddress(
                            parts[0], Integer.parseInt(parts[2]));
                    servers.put(Long.valueOf(sid), new QuorumServer(sid, addr,
                            electionAddr));
                } else if (parts.length == 4) {
                    InetSocketAddress electionAddr = new InetSocketAddress(
                            parts[0], Integer.parseInt(parts[2]));
                    LearnerType type = LearnerType.PARTICIPANT;
                    if (parts[3].toLowerCase().equals("observer")) {
                        type = LearnerType.OBSERVER;
                        observers.put(Long.valueOf(sid), new QuorumServer(sid, addr,
                                electionAddr,type));
                    } else if (parts[3].toLowerCase().equals("participant")) {
                        type = LearnerType.PARTICIPANT;
                        servers.put(Long.valueOf(sid), new QuorumServer(sid, addr,
                                electionAddr,type));
                    } else {
                        throw new ConfigException("Unrecognised peertype: " + value);
                    }
                }
            } else if (key.startsWith("group")) {
                int dot = key.indexOf('.');
                long gid = Long.parseLong(key.substring(dot + 1));

                numGroups++;

                String parts[] = value.split(":");
                for(String s : parts){
                    long sid = Long.parseLong(s);
                    if(serverGroup.containsKey(sid))
                        throw new ConfigException("Server " + sid + "is in multiple groups");
                    else
                        serverGroup.put(sid, gid);
                }

            } else if(key.startsWith("weight")) {
                int dot = key.indexOf('.');
                long sid = Long.parseLong(key.substring(dot + 1));
                serverWeight.put(sid, Long.parseLong(value));
            } else {
                System.setProperty("zookeeper." + key, value);
            }
        }

        if (dataDir == null) {
            throw new IllegalArgumentException("dataDir is not set");
        }
        if (dataLogDir == null) {
            dataLogDir = dataDir;
        } else {
            if (!new File(dataLogDir).isDirectory()) {
                throw new IllegalArgumentException("dataLogDir " + dataLogDir
                        + " is missing.");
            }
        }
        if (clientPort == 0) {
            throw new IllegalArgumentException("clientPort is not set");
        }
        if (clientPortAddress != null) {
            this.clientPortAddress = new InetSocketAddress(
                    InetAddress.getByName(clientPortAddress), clientPort);
        } else {
            this.clientPortAddress = new InetSocketAddress(clientPort);
        }

        if (tickTime == 0) {
            throw new IllegalArgumentException("tickTime is not set");
        }
        if (minSessionTimeout &gt; maxSessionTimeout) {
            throw new IllegalArgumentException(
                    "minSessionTimeout must not be larger than maxSessionTimeout");
        }
        if (servers.size() == 0) {
            if (observers.size() &gt; 0) {
                throw new IllegalArgumentException("Observers w/o participants is an invalid configuration");
            }
            // Not a quorum configuration so return immediately - not an error
            // case (for b/w compatibility), server will default to standalone
            // mode.
            return;
        } else if (servers.size() == 1) {
            if (observers.size() &gt; 0) {
                throw new IllegalArgumentException("Observers w/o quorum is an invalid configuration");
            }

            // HBase currently adds a single server line to the config, for
            // b/w compatibility reasons we need to keep this here.
            LOG.error("Invalid configuration, only one server specified (ignoring)");
            servers.clear();
        } else if (servers.size() &gt; 1) {
            if (servers.size() == 2) {
                LOG.warn("No server failure will be tolerated. " +
                    "You need at least 3 servers.");
            } else if (servers.size() % 2 == 0) {
                LOG.warn("Non-optimial configuration, consider an odd number of servers.");
            }
            if (initLimit == 0) {
                throw new IllegalArgumentException("initLimit is not set");
            }
            if (syncLimit == 0) {
                throw new IllegalArgumentException("syncLimit is not set");
            }
            /*
             * If using FLE, then every server requires a separate election
             * port.
             */
            if (electionAlg != 0) {
                for (QuorumServer s : servers.values()) {
                    if (s.electionAddr == null)
                        throw new IllegalArgumentException(
                                "Missing election port for server: " + s.id);
                }
            }

            /*
             * Default of quorum config is majority
             */
            if(serverGroup.size() &gt; 0){
                if(servers.size() != serverGroup.size())
                    throw new ConfigException("Every server must be in exactly one group");
                /*
                 * The deafult weight of a server is 1
                 */
                for(QuorumServer s : servers.values()){
                    if(!serverWeight.containsKey(s.id))
                        serverWeight.put(s.id, (long) 1);
                }

                /*
                 * Set the quorumVerifier to be QuorumHierarchical
                 */
                quorumVerifier = new QuorumHierarchical(numGroups,
                        serverWeight, serverGroup);
            } else {
                /*
                 * The default QuorumVerifier is QuorumMaj
                 */

                LOG.info("Defaulting to majority quorums");
                quorumVerifier = new QuorumMaj(servers.size());
            }

            // Now add observers to servers, once the quorums have been
            // figured out
            servers.putAll(observers);
    
            File myIdFile = new File(dataDir, "myid");
            if (!myIdFile.exists()) {
                throw new IllegalArgumentException(myIdFile.toString()
                        + " file is missing");
            }
            BufferedReader br = new BufferedReader(new FileReader(myIdFile));
            String myIdString;
            try {
                myIdString = br.readLine();
            } finally {
                br.close();
            }
            try {
                serverId = Long.parseLong(myIdString);
            } catch (NumberFormatException e) {
                throw new IllegalArgumentException("serverid " + myIdString
                        + " is not a number");
            }
        }
    }</code></pre>这一大段都是关于解析配置文件，所以大家在配置文件的时候不要配错了。<br>
 <pre><code class="language-java">    /**
     * Run from a ServerConfig.
     * @param config ServerConfig to use.
     * @throws IOException
     */
    public void runFromConfig(ServerConfig config) throws IOException {
        LOG.info("Starting server");
        try {
            // Note that this thread isn't going to be doing anything else,
            // so rather than spawning another thread, we will just call
            // run() in this thread.
            // create a file logger url from the command line args
            ZooKeeperServer zkServer = new ZooKeeperServer();

            FileTxnSnapLog ftxn = new FileTxnSnapLog(new
                   File(config.dataLogDir), new File(config.dataDir));
            zkServer.setTxnLogFactory(ftxn);
            zkServer.setTickTime(config.tickTime);
            zkServer.setMinSessionTimeout(config.minSessionTimeout);
            zkServer.setMaxSessionTimeout(config.maxSessionTimeout);
            cnxnFactory = new NIOServerCnxn.Factory(config.getClientPortAddress(),
                    config.getMaxClientCnxns());
            cnxnFactory.startup(zkServer);
            cnxnFactory.join();
            if (zkServer.isRunning()) {
                zkServer.shutdown();
            }
        } catch (InterruptedException e) {
            // warn, but generally this is ok
            LOG.warn("Server interrupted", e);
        }
    }</code></pre><br>
接下来继续看<span style="font-size:18px;"></span><pre><code class="language-java">cnxnFactory.startup(zkServer);代码是要开始</code></pre><pre><code class="language-java"> * This class handles communication with clients using NIO. There is one per
 * client, but only one thread doing the communication.
</code></pre><pre><code class="language-java">从字面上的意思就是开启一个线程去监控客户端与zookeeper的socket的最大连接数，</code></pre><pre><code class="language-java">注：zookeeper的最大连接数是在zookeeper.properties的maxClientCnxns=150设置。</code></pre><pre><code class="language-java">若超出最大连接数将会导致socket close</code></pre><br><pre><code class="language-java">&lt;/pre&gt;&lt;p&gt;&lt;pre name="code" class="java"&gt;    public void deserialize(DataTree dt, Map&lt;Long, Integer&gt; sessions,
            InputArchive ia) throws IOException {
        FileHeader header = new FileHeader();
        header.deserialize(ia, "fileheader");
        if (header.getMagic() != SNAP_MAGIC) {
            throw new IOException("mismatching magic headers "
                    + header.getMagic() + 
                    " !=  " + FileSnap.SNAP_MAGIC);
        }
        SerializeUtils.deserializeSnapshot(dt,ia,sessions);
    }</code></pre>这一段代码是去读取本地文件，获取到上一次断掉的sessions点，是反序列话读取的，就是从最后一个读取并且用tree来排列
<p></p>
<p><br></p>
<p></p><pre><code class="language-java">    public void startup() {        
        createSessionTracker();
        setupRequestProcessors();

        registerJMX();

        synchronized (this) {
            running = true;
            notifyAll();
        }
    }</code></pre>这里将启动启动线程来管理session
<p>太多了，先到这里，有什么问题或者意见直接留言。</p>
<p>接下来有时间会继续进行优化和更新。</p>
<p>接下来将会去kafka本身是怎么利用zookeeper来实现管理topic数据的，并且还要继续了解kafka生产消息和消费消息的原理，<br><br></p>
<p></p>
            </div>
                </div>