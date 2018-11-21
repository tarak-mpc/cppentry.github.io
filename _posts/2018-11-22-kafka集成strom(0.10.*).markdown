---
layout:     post
title:      kafka集成strom(0.10.*)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>    通过上一章节<a href="https://blog.csdn.net/smily_tk/article/details/80523377" rel="nofollow">初始kafka</a>，对kafka的基础简单认识，本章节的主要内容是：将使用storm作为消费者，消费kafka端的消息；</p><p>1. 配置消费者</p><p>    顾名思义，使用storm消费消息时，消息作为拓扑中的数据源，则承担着spout的角色，那么引发我们的猜想，会不会有一个类承担着消费kafka消息的spout？</p><pre><code class="language-java">        //1.配置kafka服务地址
        //1.1.获取kafka建造器
        //接收主题：topic-device, value类型： Object
        KafkaSpoutConfig.Builder&lt;String, Object&gt; kafkabuilder = new Builder&lt;String, Object&gt;(Constants.KAFKA_BOOTSTRAP_SERVER, StringDeserializer.class, BeanDeserializer.class, topics);
        
        //设置本次消费组Id，设置本次消费允许从哪里开始
        kafkabuilder.setGroupId(Constants.STORM_KAFKA_GROUP_ID)
                    .setFirstPollOffsetStrategy(FirstPollOffsetStrategy.LATEST);
                    //.setEmitNullTuples(true);
        
        //setFirstPollOffsetStrategy允许你设置从哪里开始消费数据. 这在故障恢复和第一次启动spout的情况下会被使用
        //    EARLIEST :无论之前的消费情况如何,spout会从每个kafka partition能找到的最早的offset开始的读取
        //    LATEST :无论之前的消费情况如何,spout会从每个kafka partition当前最新的offset开始的读取
        //    UNCOMMITTED_EARLIEST (默认值) :spout 会从每个partition的最后一次提交的offset开始读取. 如果offset不存在或者过期, 则会依照 EARLIEST进行读取.
        //    UNCOMMITTED_LATEST :spout 会从每个partition的最后一次提交的offset开始读取, 如果offset不存在或者过期, 则会依照 LATEST进行读取.
        
        
        //1.2.构建KafkaSpoutConfig
        KafkaSpoutConfig&lt;String, Object&gt; kafkaSpoConf = kafkabuilder.build();

        //2.构建KafkaSpout
        KafkaSpout&lt;String, Object&gt; kafkaSpout = new KafkaSpout&lt;String, Object&gt;(kafkaSpoConf);</code></pre><p>    在0.10.*的版本中，针对配置kafkaSpout的API进行了优化，这里建造出了Builder，用于构建指定键值的kafkaSpout对象。<br></p><p>2. 连接下游</p><p>    配置完毕kafkaSpout，将上游的数据源接收到，则进行其他处理<br></p><pre><code class="language-java">@Override
    public void execute(Tuple input) {
        //KafkaSpout在将数据解析的时候 
        //  默认封装5个元数据分别为：
        //    "topic": 主题；
        //    "partition": 分区；
        //    "offset": 偏移量
        //    "key": key
        //    "value"：value即为kafka中的数据
        //参考一下
        //values: [topic-ids, 2, 0, null, 527]
        
        //判断是否为定时器
        if(TupleUtils.isTick(input)){
            //提交
            collector.emit(Constants.BF_STREAM_DEVICE, new Values(ciMap, vendorMap, dMap, serialMap));
            return;
        }
        
        List&lt;Object&gt; values = input.getValues();
        int valueSize = values.size();
        Object topicObj = values.get(0);
        Object keyObject = values.get(valueSize - 2);
        Object valueObj = values.get(valueSize - 1);
        String topic = topicObj.toString();
        
        if("topic-devices".equals(topic)){
            //对应主题为： topic-devices 的内容
            Device device = (Device)valueObj;
            String deviceIp = device.getMgntIp();
            //筛选出同一CI设备管理IP
            String ci = device.getCiKey(), vendor = device.getVendorKey();
            Set&lt;String&gt; ciIps = ciMap.get(ci);
            if(ciIps == null){
                ciIps = new HashSet&lt;String&gt;();
                ciIps.add(deviceIp);
                ciMap.put(ci, ciIps);
            } else {
                if(!ciIps.contains(deviceIp)){
                    ciIps.add(deviceIp);
                    ciMap.put(ci, ciIps);
                }
            }
            //筛选出同一厂商设备管理IP
            Set&lt;String&gt; vendorIps = vendorMap.get(vendor);
            if(vendorIps == null){
                vendorIps = new HashSet&lt;String&gt;();
                vendorIps.add(deviceIp);
                vendorMap.put(vendor, vendorIps);
            } else {
                if(!vendorIps.contains(vendor)){
                    vendorIps.add(deviceIp);
                    vendorMap.put(vendor, vendorIps);
                }
            }
            dMap.put(Long.valueOf(keyObject.toString()), device);
            //对应主题为： topic-device 的内容
            log.info("topic-device: { key: {}, value: {} }", keyObject.toString(), device.toString());
        } 
        
    }</code></pre><br><p><br></p>            </div>
                </div>