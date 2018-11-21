---
layout:     post
title:      Kafka The Definitive Guide
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:24px;"><img src="https://img-blog.csdn.net/20171213175834205" alt=""><br></span></p>
<p><span style="font-size:24px;">生产者</span></p>
<br><p><span style="font-size:18px;">生产者有大量的配置参数;大多数都在ApacheKafka文档中有记录，许多都有合理的默认值，所以没有理由去修改每一个参数。但是，一些参数对生产者的记忆使用，性能和可靠性有重大影响。我们会在这里回顾一下。</span></p>
<p><span style="font-size:18px;">acks<br><br>
该ACK的参数控制许多分区副本必须如何接收记录之前，生产者可以考虑写成功。这个选项对邮件丢失的可能性有很大的影响。acks 参数有三个允许的值：<br>
If acks = 0 ，<br>
生产者在假设消息发送成功之前不会等待代理的回复。这意味着如果出了问题，<br>
经纪人没有收到消息，生产者不知道，消息将会丢失。但是，由于生产者不等待服务器的任何响应，所以它可以以网络支持的速度发送消息，所以这个设置可以用来实现非常高的吞吐量。<br>
If ack = 1 ，生产者将接收来自代理中的领导者副本接收到的消息的时刻的成功响应。如果消息不能写入领导（例如，如果领导崩溃，并且还没有选出新领导），则生产者将收到错误响应并且可以重试发送消息，避免潜在的数据丢失。如果领导人崩溃，并且没有这个消息的副本被选为新领导者（通过不洁的领导者选举），消息仍然可能会丢失。在这种情况下，吞吐量取决于我们是同步还是异步发送消息。如果我们的客户端代码等待来自服务器的回复（通过调用发送消息时返回的Future 对象的get（）方法），显然会显着增加延迟（至少通过网络往返）。如果客户端使用回调，则潜伏时间将被隐藏，但是吞吐量将受到正在进行的消息数量的限制（即在从服务器接收回复之前生产者将发送多少消息）。<br>
If acks = all ，则一旦所有同步副本收到消息，生产者都将收到来自代理的成功响应。这是最安全的模式，因为您可以确保多个经纪人都有消息，即使发生崩溃，消息也能存活（有关第5章的更多信息）。然而，我们在acks = 1 案例中讨论的延迟会更高，因为我们将等待不止一个经纪人接收信息。<br><br><br>
buffer.memory<br>
这将设置生产者将用于缓冲等待发送给代理的消息的内存量。如果应用程序发送的消息比发送给服务器的消息快，那么生产者可能会用尽空间，并且额外的send（）调用将根据block.on.buffer.full 参数阻塞或抛出异常（替换为0.9.0.0版本中的 max.block.ms ，允许阻塞一段时间，然后抛出一个异常）。<br><br><br>
compression.type<br>
默认情况下，消息是未压缩发送的。该参数可以被设置为活泼的，gzip的，或LZ4 ，在这种情况下相应的压缩算法将被用来将它发送到代理之前对数据进行压缩。敏捷的压缩技术是Google发明的，它可以提供良好的压缩比，CPU占用率低，性能好，所以建议在兼顾性能和带宽的情况下。Gzip压缩通常会使用更多的CPU和时间，但会产生更好的压缩比，所以建议在网络带宽更受限制的情况下使用。通过启用压缩功能，可以减少网络利用率和存储量，这往往是向Kafka发送消息时的瓶颈。<br><br><br>
retries<br>
当生产者从服务器收到错误消息时，错误可能是暂时的（例如，缺少分区的领导）。在这种情况下，retries 参数的值将控制生产者在放弃并通知客户端问题之前将重试发送消息的次数。默认情况下，生产者将在重试之间等待100ms，但是您可以使用retry.backoff.ms 参数来控制此操作。我们建议测试从崩溃的代理恢复需要多长时间（即，直到所有分区获得新的领导者的时间），并设置它们之间的重试次数和延迟时间，以使重试的总时间长于时间需要Kafka集群从崩溃中恢复，否则生产者会放弃过早。不是所有的错误都会被生产者重新尝试。有些错误不是暂时的，不会导致重试（例如，“消息太大”的错误）。一般来说，因为生产者为你处理重试，在你自己的应用程序逻辑中处理重试没有意义。您将需要集中精力处理不可恢复的错误或重试尝试耗尽的情况。<br><br><br>
batch.size<br>
当多个记录发送到同一个分区时，生产者将把它们批处理在一起。此参数控制将用于每个批次的内存字节数（不是消息！）。批次满时，批量中的所有消息都将被发送。但是，这并不意味着生产者会等待批次变满。制片人将发送半满批次，甚至批量，只有一条消息。因此，批量设置过大不会导致邮件发送延迟; 它只会使用更多的内存批量。设置批量太小会增加一些开销，因为生产者需要更频繁地发送消息。<br><br><br>
linger.ms<br>
linger.ms 控制发送当前批次之前等待附加消息的时间。KafkaProducer 在当前批次已满或达到 linger.ms 限制时发送一批消息。默认情况下，即使批处理中只有一条消息，生产者也会在发送者线程可用时立即发送消息。通过将linger.ms设置为高于0，我们指示生产者等待几毫秒，然后将其添加到批处理中，然后再发送给代理。这增加了延迟，但也增加了吞吐量（因为我们一次发送更多消息，每条消息的开销更少）。<br><br><br>
client.id<br>
这可以是任何字符串，并且由经纪人用来识别从客户端发送的消息。它用于日志记录和指标以及配额。<br><br><br>
max.in.flight.requests.per.connection<br>
这将控制生产者在没有收到响应的情况下将发送到服务器的消息数量。将此设置为最高会增加内存使用量，同时提高吞吐量，但将其设置得过高可能会降低吞吐量，因为批量降低效率。将其设置为1将保证消息将按照发送顺序写入代理，即使发生重试也是如此。<br><br><br>
timeout.ms，request.timeout.ms和metadata.fetch.timeout.ms<br>
这些参数控制生产者在发送数据（request.timeout.ms ）和请求元数据（如正在写入的分区的当前领导）（metadata.fetch.timeout.ms ）时等待服务器答复的时间。。如果达到超时没有回复，生产者将重试发送或响应错误（通过异常或发送回调）。timeout.ms 控制代理等待同步副本确认消息以满足确认配置的时间 - 如果没有必要的确认，代理将返回错误。<br><br><br>
max.block.ms<br>
此参数控制调用send（）时以及通过partitionsFor（）显式请求元数据时生产者将阻塞的时间。这些方法在生产者的发送缓冲区已满或元数据不可用时阻塞。当达到 max.block.ms 时，会引发超时异常。<br><br><br>
max.request.size<br>
此设置控制生产者发送的产品请求的大小。它既限制了可发送的最大消息的大小，也限制了生产者可以在一个请求中发送的消息的数量。例如，默认的最大请求大小为1 MB，则可以发送的最大消息为1 MB，或者生产者可以将1,000个大小为1 K的消息分批发送到一个请求中。另外，代理对它将接受的最大消息的大小有限制（message.max.bytes ）。使这些配置匹配通常是一个好主意，所以生产者不会尝试发送将被代理拒绝的大小的消息。<br><br><br>
receive.buffer.bytes and send.buffer.byteu<br>
这些是在写入和读取数据时由套接字使用的TCP发送和接收缓冲区的大小。如果这些设置为-1，则将使用OS默认值。当生产者或消费者与不同的数据中心中的经纪人进行通信时，增加这些数据是个好主意，因为这些网络链路通常具有更高的延迟和更低的带宽。</span><br></p>
<p><span style="font-size:18px;"><br></span></p>
<p><br><br><span style="font-size:24px;">消费者</span><br><br><br><span style="font-size:18px;">ApacheKafka文档中记录了所有用户配置。大多数参数都有合理的默认值，不需要修改，但有些参数会影响消费者的性能和可用性。</span><br><br><br><span style="font-size:18px;">fetch.min.bytes</span><br><br><span style="font-size:18px;">此属性允许用户指定在获取记录时从代理接收的最小数据量。如果代理收到来自消费者的记录请求，但新记录的字节数少于min.fetch.bytes ，则代理将等待更多消息可用，然后再将记录发回消费者。这减少了消费者和经纪人的负担，因为在话题没有太多新活动（或者对于一天中的较低活动时间）的情况下，他们必须处理较少的来回消息。如果消费者在没有太多数据可用的情况下使用太多的CPU，则需要将此参数设置为高于默认值，或者在拥有大量消费者时减少代理的负载。</span><br><br><br><span style="font-size:18px;">fetch.max.wait.ms</span><br><span style="font-size:18px;">通过设置fetch.min.bytes ，您可以告诉Kafka等到有足够的数据发送给用户之后再发送。fetch.max.wait.ms 让你控制等待多久。默认情况下，Kafka将等待500毫秒。如果没有足够的数据流向Kafka主题以满足返回的最小数据量，则会导致高达500 ms的额外延迟。如果您想限制潜在的延迟（通常是由于SLA控制应用程序的最大延迟），则可以将fetch.max.wait.ms 设置为较低的值。如果将fetch.max.wait.ms设置为100
 ms，并将fetch.min.bytes设置为1 MB，则Kafka将接收来自使用者的提取请求，并在数据有1 MB数据返回时或100毫秒，以先发生者为准。</span><br><br><br><span style="font-size:18px;">max.partition.fetch.bytes</span><br><span style="font-size:18px;">此属性控制服务器每个分区返回的最大字节数。默认值是1 MB，这意味着当KafkaConsumer.poll（）返回ConsumerRecords时，记录对象将最多使用分配给使用者的每个分区的 max.partition.fetch.bytes 。因此，如果一个主题有20个分区，并且有5个消费者，则每个消费者需要有4 MB的内存用于消费者记录。实际上，如果组中的其他使用者发生故障，您将需要分配更多的内存，因为每个使用者需要处理更多的分区。最大。partition.fetch.bytes
 必须大于代理将接受的最大消息（由代理配置中的max.message.size 属性确定），或者代理可能具有消费者将无法使用的消息，在这种情况下消费者会试图阅读它们。设置max.partition.fetch.bytes的另一个重要考虑因素是消费者处理数据所花费的时间。您记得，消费者必须经常调用poll（）以避免会话超时和随后的重新平衡。如果单个poll（）返回的数据量非常大，则消费者可能需要更长的时间来处理，这意味着它不会及时到达轮询循环的下一个迭代以避免会话超时。如果发生这种情况，两个选项要么降低最大值。partition.fetch.bytes
 或增加会话超时。</span><br><br><br><span style="font-size:18px;">session.timeout.ms</span><br><span style="font-size:18px;">消费者在仍被认为活着的情况下可以脱离与经纪人联系的时间量默认为3秒。如果超过session.timeout.ms 而没有消费者向组协调器发送心跳信号，则认为它已经死亡，组协调器将触发消费者组的重新平衡，以便将死亡消费者的分区分配给组中的其他消费者。此属性与heartbeat.interval.ms密切相关。heartbeat.interval.ms 控制KafkaConsumer poll（）方法向组协调器发送心跳的频率，而session.timeout.ms
 控制消费者在不发送心跳的情况下可以走多久。因此，这两个属性通常一起修改 - heatbeat.interval.ms 必须低于session.timeout.ms ，通常设置为超时值的三分之一。所以如果session.timeout.ms 是3秒钟，heartbeat.interval.ms 应该是1秒。将session.timeout.ms设置为低于默认值将允许用户组更快地检测并从故障中恢复，但是由于用户花费更长时间来完成轮询循环或垃圾回收，还可能导致不必要的重新平衡。将session.timeout.ms设置得更高会降低意外重新平衡的机会，但也意味着检测到真正的故障需要更长的时间。</span><br><br><br><span style="font-size:18px;">auto.offset.reset</span><br><span style="font-size:18px;">当它开始读取一个没有提交偏移量的分区，或者提交的偏移量是无效的时，这个属性控制着消费者的行为（通常是因为消费者关闭的时间太长，已经超出了经纪人的年龄）。缺省值是“latest”，这意味着缺少有效的偏移量，消费者将开始从最新的记录（在消费者开始运行之后编写的记录）读取。替代方法是“最早的”，这意味着缺少有效的偏移量，消费者将从头开始读取分区中的所有数据。</span><br><br><br><span style="font-size:18px;">enable.auto.commit</span><br><span style="font-size:18px;">我们在本章前面讨论了抵销的不同选择。此参数控制使用者是否自动提交偏移量，并且默认为true 。如果您希望控制何时提交偏移量，则将其设置为false ，这是减少重复项并避免丢失数据所必需的。如果将enable.auto.commit设置为true ，则可能还需要使用auto.commit.interval.ms来控制偏移提交的频率。</span><br><br><br><span style="font-size:18px;">partition.assignment.strategy</span><br><span style="font-size:18px;">我们了解到，分区被分配给消费者群体中的消费者。一个PartitionAssignor 则认为，鉴于消费者和主题，他们订阅类，决定哪些分区将被分配给消费者。默认情况下，Kafka有两个分配策略：</span><br><span style="font-size:18px;">RANGE</span><br><span style="font-size:18px;">为每个使用者分配来自其订阅的每个主题的连续分区子集。因此，如果消费者C1和C2订购了两个主题T1和T2，并且每个主题具有三个分区，则C1将被分配来自主题T1和T2的分区0和1，而C2将从这些主题被分配分区2 。因为每个主题都有不平衡的分区数量和分配为每个主题独立完成，第一个消费者比第二个消费者分区更多。无论何时使用范围分配，消费者数量不会整齐划分每个主题中的分区数量，就会发生这种情况。</span><br><br><br><span style="font-size:18px;">ROUNDROBIN</span><br><span style="font-size:18px;">从所有订阅的主题中获取所有分区，并逐个分配给消费者。如果之前描述的C1和C2使用RoundRobin分配，则C1将具有来自主题T1的分区0和2以及来自主题T2的分区1。C2将具有来自主题T1的分区1和来自主题T2的分区0和2。一般来说，如果所有消费者都订购了相同的主题（一个非常常见的情况），RoundRobin分配将最终与所有消费者具有相同数量的分区（或至多1个分区差异）。</span><br><br><br><span style="font-size:18px;">partition.assignment.strategy 允许您选择一个分区分配策略。缺省值是org.apache.kafka.clients.consumer.RangeAssignor ，它实现了上述的Range策略。你可以用org.apache.kafka.clients.consumer.RoundRobinAssignor替换它。更高级的选项是实现你自己的分配策略，在这种情况下，partition.assignment.strategy 应该指向你的类的名字。</span><br><br><br><span style="font-size:18px;">client.id</span><br><span style="font-size:18px;">这可以是任何字符串，并且由经纪人用来识别从客户端发送的消息。它用于日志记录和指标以及配额。</span><br><br><br><span style="font-size:18px;">max.poll.records</span><br><span style="font-size:18px;">这将控制一次调用poll（）将返回的最大记录数。这有助于控制应用程序在轮询循环中需要处理的数据量。</span><br><br><br><span style="font-size:18px;">receive.buffer.bytes and send.buffer.byteu</span><br><span style="font-size:18px;">这些是在写入和读取数据时由套接字使用的TCP发送和接收缓冲区的大小。如果这些设置为-1，则将使用OS默认值。当生产者或消费者与不同的数据中心中的经纪人进行通信时，增加这些可能是一个好主意，因为这些网络链路通常具有更高的延迟和更低的带宽。</span><br></p>
<p><span style="font-size:18px;"><br></span></p>
            </div>
                </div>