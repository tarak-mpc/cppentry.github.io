---
layout:     post
title:      Apache Flume – Architecture of Flume NG
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<span style="color:rgb(80,80,80);font-family:'Lucida Grande CY', 'Lucida Grande', 'Lucida Sans', Helvetica, Arial, sans-serif;font-size:14px;line-height:20px;"></span>
<p style="margin-left:0px;">
<span style="font-weight:inherit;"><em>This blog was originally posted on the Apache Blog:<a href="https://blogs.apache.org/flume/entry/flume_ng_architecture" rel="nofollow" style="text-decoration:none;color:rgb(1,126,197);">https://blogs.apache.org/flume/entry/flume_ng_architecture</a></em></span></p>
<p style="margin-left:0px;">
Apache Flume is a distributed, reliable, and available system for efficiently collecting, aggregating and moving large amounts of log data from many different sources to a centralized data store. Flume is currently undergoing incubation at The Apache Software
 Foundation. More information on this project can be found at<a href="http://incubator.apache.org/flume" rel="nofollow" style="text-decoration:none;color:rgb(1,126,197);">http://incubator.apache.org/flume</a>. <span style="font-weight:inherit;"><em>Flume NG</em></span> is
 work related to new major revision of Flume and is the subject of this post.</p>
<p style="margin-left:0px;">
Prior to entering the incubator, Flume saw incremental releases leading up to version 0.9.4. As Flume became adopted it became clear that certain design choices would need to be reworked in order to address problems reported in the field. The work necessary
 to make this change began a few months ago under the JIRA issue<a href="https://issues.apache.org/jira/browse/FLUME-728" rel="nofollow" style="text-decoration:none;color:rgb(1,126,197);">FLUME-728</a>. This work currently resides on a separate branch by the name flume-728,
 and is informally referred to as Flume NG. At the time of writing this post Flume NG had gone through two internal milestones – <span style="font-weight:inherit;"><em>NG Alpha 1</em></span>, and <span style="font-weight:inherit;"><em>NG Alpha 2</em></span> and
 a formal incubator release of Flume NG is in the works.</p>
<p style="margin-left:0px;">
At a high-level, Flume NG uses a single-hop message delivery guarantee semantics to provide end-to-end reliability for the system. To accomplish this, certain new concepts have been incorporated into its design, while certain other existing concepts have been
 either redefined, reused or dropped completely.</p>
<p style="margin-left:0px;">
In this blog post, I will describe the fundamental concepts incorporated in Flume NG and talk about it’s high-level architecture. This is a first in a series of blog posts by Flume team that will go into further details of it’s design and implementation.</p>
<h2 style="margin-left:0px;font-size:15px;font-weight:bold;line-height:17px;color:rgb(0,0,0);">
Core Concepts</h2>
<p style="margin-left:0px;">
The purpose of Flume is to provide a distributed, reliable, and available system for efficiently collecting, aggregating and moving large amounts of log data from many different sources to a centralized data store. The architecture of Flume NG is based on a
 few concepts that together help achieve this objective. Some of these concepts have existed in the past implementation, but have changed drastically. Here is a summary of concepts that Flume NG introduces, redefines, or reuses from earlier implementation:</p>
<ul style="margin-left:16px;"><li style="margin-left:0px;list-style-type:disc;">
<span style="font-style:inherit;color:rgb(47,43,44);"><strong>Event:</strong></span> A byte payload with optional string headers that represent the unit of data that Flume can transport from it’s point of origination to it’s final destination.</li><li style="margin-left:0px;list-style-type:disc;">
<span style="font-style:inherit;color:rgb(47,43,44);"><strong>Flow:</strong></span> Movement of events from the point of origin to their final destination is considered a data flow, or simply flow. This is not a rigorous definition and is used only at a high
 level for description purposes.</li><li style="margin-left:0px;list-style-type:disc;">
<span style="font-style:inherit;color:rgb(47,43,44);"><strong>Client:</strong></span> An interface implementation that operates at the point of origin of events and delivers them to a Flume agent. Clients typically operate in the process space of the application
 they are consuming data from. For example, Flume Log4j Appender is a client.</li><li style="margin-left:0px;list-style-type:disc;">
<span style="font-style:inherit;color:rgb(47,43,44);"><strong>Agent: </strong></span>An independent process that hosts flume components such as sources, channels and sinks, and thus has the ability to receive, store and forward events to their next-hop destination.</li><li style="margin-left:0px;list-style-type:disc;">
<span style="font-style:inherit;color:rgb(47,43,44);"><strong>Source:</strong></span> An interface implementation that can consume events delivered to it via a specific mechanism. For example, an Avro source is a source implementation that can be used to receive
 Avro events from clients or other agents in the flow. When a source receives an event, it hands it over to one or more channels.</li><li style="margin-left:0px;list-style-type:disc;">
<span style="font-style:inherit;color:rgb(47,43,44);"><strong>Channel:</strong></span> A transient store for events, where events are delivered to the channel via sources operating within the agent. An event put in a channel stays in that channel until a sink
 removes it for further transport. An example of channel is the JDBC channel that uses a file-system backed embedded database to persist the events until they are removed by a sink. Channels play an important role in ensuring durability of the flows.</li><li style="margin-left:0px;list-style-type:disc;">
<span style="font-style:inherit;color:rgb(47,43,44);"><strong>Sink: </strong></span>An interface implementation that can remove events from a channel and transmit them to the next agent in the flow, or to the event’s final destination. Sinks that transmit the
 event to it’s final destination are also known as terminal sinks. The Flume HDFS sink is an example of a terminal sink. Whereas the Flume Avro sink is an example of a regular sink that can transmit messages to other agents that are running an Avro source.</li></ul><p style="margin-left:0px;">
These concepts help in simplifying the architecture, implementation, configuration and deployment of Flume.</p>
<h2 style="margin-left:0px;font-size:15px;font-weight:bold;line-height:17px;color:rgb(0,0,0);">
Flow Pipeline</h2>
<p style="margin-left:0px;">
A flow in Flume NG starts from the client. The client transmits the event to it’s next hop destination. This destination is an agent. More precisely, the destination is a source operating within the agent. The source receiving this event will then deliver it
 to one or more channels. The channels that receive the event are drained by one or more sinks operating within the same agent. If the sink is a regular sink, it will forward the event to it’s next-hop destination which will be another agent. If instead it
 is a terminal sink, it will forward the event to it’s final destination. Channels allow the decoupling of sources from sinks using the familiar producer-consumer model of data exchange. This allows sources and sinks to have different performance and runtime
 characteristics and yet be able to effectively use the physical resources available to the system.</p>
<p style="margin-left:0px;">
Figure 1 below shows how the various components interact with each other within a flow pipeline.</p>
<p style="margin-left:0px;text-align:center;">
<img class="aligncenter" src="https://blogs.apache.org/flume/mediaresource/ab0d50f6-a960-42cc-971e-3da38ba3adad" alt="Schematic showing logical components in a flow. The arrows represent the direction in which events travel across the system. This also illustrates how flows can fan-out by having one source write the event out to multiple=" style="border-top-width:0px;border-right-width:0px;border-bottom-width:0px;border-left-width:0px;border-top-style:none;border-right-style:none;border-bottom-style:none;border-left-style:none;"></p>
<p style="margin-left:0px;text-align:center;">
<span style="font-style:inherit;color:rgb(47,43,44);font-size:13px;"><strong><span style="font-weight:inherit;"><em>Figure 1:</em></span></strong></span><span style="font-weight:inherit;"><em> Schematic showing logical components in a flow. The arrows represent
 the direction in which events travel across the system. This also illustrates how flows can fan-out by having one source write the event out to multiple channels.</em></span></p>
<p style="margin-left:0px;">
By configuring a source to deliver the event to more than one channel, flows can fan-out to more than one destination. This is illustrated in Figure 1 where the source within the operating Agent writes the event out to two channels – Channel 1 and Channel 2.</p>
<p style="margin-left:0px;">
Conversely, flows can be converged by having multiple sources operating within the same agent write to the same channel. A example of the physical layout of a converging flow is show in Figure 2 below.</p>
<p style="margin-left:0px;text-align:center;">
<img class="aligncenter" src="https://blogs.apache.org/flume/mediaresource/268bf8db-43c7-497b-a0ef-63c482371eef" alt="A simple converging flow on Flume NG." width="500" height="343" style="border-top-width:0px;border-right-width:0px;border-bottom-width:0px;border-left-width:0px;border-top-style:none;border-right-style:none;border-bottom-style:none;border-left-style:none;"></p>
<p style="margin-left:0px;text-align:center;">
<span style="font-weight:inherit;"><em><span style="font-style:inherit;color:rgb(47,43,44);font-size:13px;"><strong>Figure 2:</strong></span> A simple converging flow on Flume NG.</em></span></p>
<h2 style="margin-left:0px;font-size:15px;font-weight:bold;line-height:17px;color:rgb(0,0,0);">
Reliability and Failure Handling</h2>
<p style="margin-left:0px;">
Flume NG uses channel-based transactions to guarantee reliable message delivery. When a message moves from one agent to another, two transactions are started, one on the agent that delivers the event and the other on the agent that receives the event. In order
 for the sending agent to commit it’s transaction, it must receive success indication from the receiving agent. The receiving agent only returns a success indication if it’s own transaction commits properly first. This ensures guaranteed delivery semantics
 between the hops that the flow makes. Figure 3 below shows a sequence diagram that illustrates the relative scope and duration of the transactions operating within the two interacting agents.</p>
<p style="margin-left:0px;text-align:center;">
<img class="aligncenter" src="https://blogs.apache.org/flume/mediaresource/a15d9347-da9e-4824-b45f-6c00f0720590" alt="Transactional exchange of events between agents." width="500" height="329" style="border-top-width:0px;border-right-width:0px;border-bottom-width:0px;border-left-width:0px;border-top-style:none;border-right-style:none;border-bottom-style:none;border-left-style:none;"></p>
<p style="margin-left:0px;text-align:center;">
<span style="font-weight:inherit;"><em><span style="font-style:inherit;color:rgb(47,43,44);font-size:13px;"><strong>Figure 3:</strong></span> Transactional exchange of events between agents.</em></span></p>
<p style="margin-left:0px;">
This mechanism also forms the basis for failure handling in Flume NG. When a flow that passes through many different agents encounters a communication failure on any leg of the flow, the affected events start getting buffered at the last unaffected agent in
 the flow. If the failure is not resolved on time, this may lead to the failure of the last unaffected agent, which then would force the agent before it to start buffering the events. Eventually if the failure occurs when the client transmits the event to its
 first-hop destination, the failure will be reported back to the client which can then allow the application generating the events to take appropriate action.</p>
<p style="margin-left:0px;">
On the other hand, if the failure is resolved before the first-hop agent fails, the buffered events in various agents downstream will start draining towards their destination. Eventually the flow will be restored to its original characteristic throughput levels.
 Figure 4 below illustrates a scenario where a flow comprising of two intermediary agents between the client and the central store go through a transient failure. The failure occurs between agent 2 and the central store, resulting in the events getting buffered
 at the agent 2 itself. Once the failing link has been restored to normal, the buffered events drain out to the central store and the flow is restored to its original throughput characteristics.</p>
<p style="margin-left:0px;text-align:center;">
<img class="aligncenter" src="https://blogs.apache.org/flume/mediaresource/ac9d1c83-1089-4730-9546-fe8de509b34c" alt="Failure handling in flows." width="500" height="352" style="border-top-width:0px;border-right-width:0px;border-bottom-width:0px;border-left-width:0px;border-top-style:none;border-right-style:none;border-bottom-style:none;border-left-style:none;"></p>
<p style="margin-left:0px;text-align:center;">
<span style="font-weight:inherit;"><em><span style="font-style:inherit;color:rgb(47,43,44);font-size:13px;"><strong>Figure 4: </strong></span>Failure handling in flows. In (a) the flow is normal and events can travel from the client to the central store. In
 (b) a communication failure occurs between Agent 2 and the event store resulting in events being buffered on Agent 2. In (c) the cause of failure was addressed and the flow was restored and any events buffered in Agent 2 were drained to the store.</em></span></p>
<h2 style="margin-left:0px;font-size:15px;font-weight:bold;line-height:17px;color:rgb(0,0,0);">
Wrapping up</h2>
<p style="margin-left:0px;">
In this post I described the various concepts that are a part of Flume NG and its high-level architecture. This is first of a series of posts from the Flume team that will highlight the design and implementation of this system. In the meantime, if you need
 anymore information, please feel free to drop an email on the project’s user or developer lists, or alternatively file the appropriate JIRA issues. Your contribution in any form is welcome on the project.</p>
<h2 style="margin-left:0px;font-size:15px;font-weight:bold;line-height:17px;color:rgb(0,0,0);">
Links:</h2>
<p style="margin-left:0px;">
Project Website: <a href="http://incubator.apache.org/flume/" rel="nofollow" style="text-decoration:none;color:rgb(1,126,197);">http://incubator.apache.org/flume/</a></p>
<p style="margin-left:0px;">
Flume NG Getting Started Guide: <a href="https://cwiki.apache.org/confluence/display/FLUME/Getting+Started" rel="nofollow" style="text-decoration:none;color:rgb(1,126,197);">https://cwiki.apache.org/confluence/display/FLUME/Getting+Started</a></p>
<p style="margin-left:0px;">
Mailing Lists: <a href="http://incubator.apache.org/flume/mail-lists.html" rel="nofollow" style="text-decoration:none;color:rgb(1,126,197);">http://incubator.apache.org/flume/mail-lists.html</a></p>
<p style="margin-left:0px;">
Issue Tracking: <a href="https://issues.apache.org/jira/browse/FLUME" rel="nofollow" style="text-decoration:none;color:rgb(1,126,197);">https://issues.apache.org/jira/browse/FLUME</a></p>
<p style="margin-left:0px;">
IRC Channel: #flume on irc.freenode.net</p>
            </div>
                </div>