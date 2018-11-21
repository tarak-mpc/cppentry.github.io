---
layout:     post
title:      influxdb测量平台建设中，编写HTTPInfluxLineSource及HTTPSourceInfluxHandler
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-github-gist">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>flume 是公司公用的平台，所有数据都经过flume，flume也是团队最主要的数据采集工具。 <br>
因此好多平台要和flume进行对接，比如我负责的测量平台。为此要开发相应的flume插件，本篇分享 flume source插件，用来接收 使用influxdb官方sdk产生的metrics数据。</p>



<h4 id="httpinfluxlinesource">HTTPInfluxLineSource</h4>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-comment">/*
 * Licensed to the Apache Software Foundation (ASF) under one
 * or more contributor license agreements.  See the NOTICE file
 * distributed with this work for additional information
 * regarding copyright ownership.  The ASF licenses this file
 * to you under the Apache License, Version 2.0 (the
 * "License"); you may not use this file except in compliance
 * with the License.  You may obtain a copy of the License at
 *
 *     http://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed on an "AS IS" BASIS,
 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 * See the License for the specific language governing permissions and
 * limitations under the License.
 */</span>
package org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.source</span><span class="hljs-preprocessor">.influxhttp</span><span class="hljs-comment">;</span>

import <span class="hljs-keyword">com</span><span class="hljs-preprocessor">.google</span><span class="hljs-preprocessor">.common</span><span class="hljs-preprocessor">.base</span><span class="hljs-preprocessor">.Preconditions</span><span class="hljs-comment">;</span>
import <span class="hljs-keyword">com</span><span class="hljs-preprocessor">.google</span><span class="hljs-preprocessor">.common</span><span class="hljs-preprocessor">.base</span><span class="hljs-preprocessor">.Throwables</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.ChannelException</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.Context</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.Event</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.EventDrivenSource</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.conf</span><span class="hljs-preprocessor">.Configurable</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.instrumentation</span><span class="hljs-preprocessor">.SourceCounter</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.source</span><span class="hljs-preprocessor">.AbstractSource</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.source</span><span class="hljs-preprocessor">.http</span><span class="hljs-preprocessor">.HTTPBadRequestException</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.source</span><span class="hljs-preprocessor">.http</span><span class="hljs-preprocessor">.HTTPSourceConfigurationConstants</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.source</span><span class="hljs-preprocessor">.http</span><span class="hljs-preprocessor">.HTTPSourceHandler</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.source</span><span class="hljs-preprocessor">.http</span><span class="hljs-preprocessor">.JSONHandler</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.tools</span><span class="hljs-preprocessor">.HTTPServerConstraintUtil</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.mortbay</span><span class="hljs-preprocessor">.jetty</span><span class="hljs-preprocessor">.Connector</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.mortbay</span><span class="hljs-preprocessor">.jetty</span><span class="hljs-preprocessor">.Server</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.mortbay</span><span class="hljs-preprocessor">.jetty</span><span class="hljs-preprocessor">.nio</span><span class="hljs-preprocessor">.SelectChannelConnector</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.mortbay</span><span class="hljs-preprocessor">.jetty</span><span class="hljs-preprocessor">.security</span><span class="hljs-preprocessor">.SslSocketConnector</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.mortbay</span><span class="hljs-preprocessor">.jetty</span><span class="hljs-preprocessor">.servlet</span><span class="hljs-preprocessor">.ServletHolder</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.slf</span>4j<span class="hljs-preprocessor">.Logger</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.slf</span>4j<span class="hljs-preprocessor">.LoggerFactory</span><span class="hljs-comment">;</span>

import javax<span class="hljs-preprocessor">.net</span><span class="hljs-preprocessor">.ssl</span><span class="hljs-preprocessor">.SSLServerSocket</span><span class="hljs-comment">;</span>
import javax<span class="hljs-preprocessor">.servlet</span><span class="hljs-preprocessor">.http</span><span class="hljs-preprocessor">.HttpServlet</span><span class="hljs-comment">;</span>
import javax<span class="hljs-preprocessor">.servlet</span><span class="hljs-preprocessor">.http</span><span class="hljs-preprocessor">.HttpServletRequest</span><span class="hljs-comment">;</span>
import javax<span class="hljs-preprocessor">.servlet</span><span class="hljs-preprocessor">.http</span><span class="hljs-preprocessor">.HttpServletResponse</span><span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.IOException</span><span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.net</span><span class="hljs-preprocessor">.ServerSocket</span><span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.util</span>.*<span class="hljs-comment">;</span>

<span class="hljs-comment">/**
 * A source which accepts Flume Events by HTTP POST and GET. GET should be used
 * for experimentation only. HTTP requests are converted into flume events by a
 * pluggable "handler" which must implement the
 * {@linkplain HTTPSourceHandler} interface. This handler takes a
 * {@linkplain HttpServletRequest} and returns a list of flume events.
 *
 * The source accepts the following parameters: &lt;p&gt; &lt;tt&gt;port&lt;/tt&gt;: port to which
 * the server should bind. Mandatory &lt;p&gt; &lt;tt&gt;handler&lt;/tt&gt;: the class that
 * deserializes a HttpServletRequest into a list of flume events. This class
 * must implement HTTPSourceHandler. Default:
 * {@linkplain JSONHandler}. &lt;p&gt; &lt;tt&gt;handler.*&lt;/tt&gt; Any configuration
 * to be passed to the handler. &lt;p&gt;
 *
 * All events deserialized from one Http request are committed to the channel in
 * one transaction, thus allowing for increased efficiency on channels like the
 * file channel. If the handler throws an exception this source will return
 * a HTTP status of 400. If the channel is full, or the source is unable to
 * append events to the channel, the source will return a HTTP 503 - Temporarily
 * unavailable status.
 *
 * A JSON handler which converts JSON objects to Flume events is provided.
 *
 */</span>
public class HTTPInfluxLineSource extends AbstractSource implements
        EventDrivenSource, Configurable {
  <span class="hljs-comment">/*
   * There are 2 ways of doing this:
   * a. Have a static server instance and use connectors in each source
   *    which binds to the port defined for that source.
   * b. Each source starts its own server instance, which binds to the source's
   *    port.
   *
   * b is more efficient than a because Jetty does not allow binding a
   * servlet to a connector. So each request will need to go through each
   * each of the handlers/servlet till the correct one is found.
   *
   */</span>

  private static final Logger LOG = LoggerFactory<span class="hljs-preprocessor">.getLogger</span>(org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.source</span><span class="hljs-preprocessor">.http</span><span class="hljs-preprocessor">.HTTPSource</span><span class="hljs-preprocessor">.class</span>)<span class="hljs-comment">;</span>
  private volatile Integer port<span class="hljs-comment">;</span>
  private volatile Server srv<span class="hljs-comment">;</span>
  private volatile String host<span class="hljs-comment">;</span>
  private HTTPSourceHandler handler<span class="hljs-comment">;</span>
  private SourceCounter sourceCounter<span class="hljs-comment">;</span>

  // SSL configuration variable
  private volatile String keyStorePath<span class="hljs-comment">;</span>
  private volatile String keyStorePassword<span class="hljs-comment">;</span>
  private volatile Boolean sslEnabled<span class="hljs-comment">;</span>
  private final List&lt;String&gt; excludedProtocols = new LinkedList&lt;String&gt;()<span class="hljs-comment">;</span>


  public void configure(Context context) {
    try {
      // SSL related config
      sslEnabled = context<span class="hljs-preprocessor">.getBoolean</span>(HTTPSourceConfigurationConstants<span class="hljs-preprocessor">.SSL</span>_ENABLED, false)<span class="hljs-comment">;</span>

      port = context<span class="hljs-preprocessor">.getInteger</span>(HTTPSourceConfigurationConstants<span class="hljs-preprocessor">.CONFIG</span>_PORT)<span class="hljs-comment">;</span>
      host = context<span class="hljs-preprocessor">.getString</span>(HTTPSourceConfigurationConstants<span class="hljs-preprocessor">.CONFIG</span>_BIND,
        HTTPSourceConfigurationConstants<span class="hljs-preprocessor">.DEFAULT</span>_BIND)<span class="hljs-comment">;</span>

      Preconditions<span class="hljs-preprocessor">.checkState</span>(host != null &amp;&amp; !host<span class="hljs-preprocessor">.isEmpty</span>(),
                <span class="hljs-string">"HTTPInfluxLineSource hostname specified is empty"</span>)<span class="hljs-comment">;</span>
      Preconditions<span class="hljs-preprocessor">.checkNotNull</span>(port, <span class="hljs-string">"HTTPInfluxLineSource requires a port number to be"</span>
        + <span class="hljs-string">" specified"</span>)<span class="hljs-comment">;</span>

      String handlerClassName = context<span class="hljs-preprocessor">.getString</span>(
              HTTPSourceConfigurationConstants<span class="hljs-preprocessor">.CONFIG</span>_HANDLER,
              HTTPSourceConfigurationConstants<span class="hljs-preprocessor">.DEFAULT</span>_HANDLER)<span class="hljs-preprocessor">.trim</span>()<span class="hljs-comment">;</span>

      if(sslEnabled) {
        LOG<span class="hljs-preprocessor">.debug</span>(<span class="hljs-string">"SSL configuration enabled"</span>)<span class="hljs-comment">;</span>
        keyStorePath = context<span class="hljs-preprocessor">.getString</span>(HTTPSourceConfigurationConstants<span class="hljs-preprocessor">.SSL</span>_KEYSTORE)<span class="hljs-comment">;</span>
        Preconditions<span class="hljs-preprocessor">.checkArgument</span>(keyStorePath != null &amp;&amp; !keyStorePath<span class="hljs-preprocessor">.isEmpty</span>(),
                                        <span class="hljs-string">"Keystore is required for SSL Conifguration"</span> )<span class="hljs-comment">;</span>
        keyStorePassword = context<span class="hljs-preprocessor">.getString</span>(HTTPSourceConfigurationConstants<span class="hljs-preprocessor">.SSL</span>_KEYSTORE_PASSWORD)<span class="hljs-comment">;</span>
        Preconditions<span class="hljs-preprocessor">.checkArgument</span>(keyStorePassword != null,
          <span class="hljs-string">"Keystore password is required for SSL Configuration"</span>)<span class="hljs-comment">;</span>
        String excludeProtocolsStr = context<span class="hljs-preprocessor">.getString</span>(HTTPSourceConfigurationConstants
          <span class="hljs-preprocessor">.EXCLUDE</span>_PROTOCOLS)<span class="hljs-comment">;</span>
        if (excludeProtocolsStr == null) {
          excludedProtocols<span class="hljs-preprocessor">.add</span>(<span class="hljs-string">"SSLv3"</span>)<span class="hljs-comment">;</span>
        } else {
          excludedProtocols<span class="hljs-preprocessor">.addAll</span>(Arrays<span class="hljs-preprocessor">.asList</span>(excludeProtocolsStr<span class="hljs-preprocessor">.split</span>(<span class="hljs-string">" "</span>)))<span class="hljs-comment">;</span>
          if (!excludedProtocols<span class="hljs-preprocessor">.contains</span>(<span class="hljs-string">"SSLv3"</span>)) {
            excludedProtocols<span class="hljs-preprocessor">.add</span>(<span class="hljs-string">"SSLv3"</span>)<span class="hljs-comment">;</span>
          }
        }
      }



      @SuppressWarnings(<span class="hljs-string">"unchecked"</span>)
      Class&lt;? extends HTTPSourceHandler&gt; clazz =
              (Class&lt;? extends HTTPSourceHandler&gt;)
              Class<span class="hljs-preprocessor">.forName</span>(handlerClassName)<span class="hljs-comment">;</span>
      handler = clazz<span class="hljs-preprocessor">.getDeclaredConstructor</span>()<span class="hljs-preprocessor">.newInstance</span>()<span class="hljs-comment">;</span>
      //ref: http://docs<span class="hljs-preprocessor">.codehaus</span><span class="hljs-preprocessor">.org</span>/display/JETTY/Embedding+Jetty
      //ref: http://jetty<span class="hljs-preprocessor">.codehaus</span><span class="hljs-preprocessor">.org</span>/jetty/jetty-<span class="hljs-number">6</span>/apidocs/org/mortbay/jetty/servlet/Context<span class="hljs-preprocessor">.html</span>
      Map&lt;String, String&gt; subProps =
              context<span class="hljs-preprocessor">.getSubProperties</span>(
              HTTPSourceConfigurationConstants<span class="hljs-preprocessor">.CONFIG</span>_HANDLER_PREFIX)<span class="hljs-comment">;</span>
      handler<span class="hljs-preprocessor">.configure</span>(new Context(subProps))<span class="hljs-comment">;</span>
    } catch (ClassNotFoundException ex) {
      LOG<span class="hljs-preprocessor">.error</span>(<span class="hljs-string">"Error while configuring HTTPInfluxLineSource. Exception follows."</span>, ex)<span class="hljs-comment">;</span>
      Throwables<span class="hljs-preprocessor">.propagate</span>(ex)<span class="hljs-comment">;</span>
    } catch (ClassCastException ex) {
      LOG<span class="hljs-preprocessor">.error</span>(<span class="hljs-string">"Deserializer is not an instance of HTTPSourceHandler."</span>
              + <span class="hljs-string">"Deserializer must implement HTTPSourceHandler."</span>)<span class="hljs-comment">;</span>
      Throwables<span class="hljs-preprocessor">.propagate</span>(ex)<span class="hljs-comment">;</span>
    } catch (Exception ex) {
      LOG<span class="hljs-preprocessor">.error</span>(<span class="hljs-string">"Error configuring HTTPInfluxLineSource!"</span>, ex)<span class="hljs-comment">;</span>
      Throwables<span class="hljs-preprocessor">.propagate</span>(ex)<span class="hljs-comment">;</span>
    }
    if (sourceCounter == null) {
      sourceCounter = new SourceCounter(getName())<span class="hljs-comment">;</span>
    }
  }

  private void checkHostAndPort() {
    Preconditions<span class="hljs-preprocessor">.checkState</span>(host != null &amp;&amp; !host<span class="hljs-preprocessor">.isEmpty</span>(),
      <span class="hljs-string">"HTTPInfluxLineSource hostname specified is empty"</span>)<span class="hljs-comment">;</span>
    Preconditions<span class="hljs-preprocessor">.checkNotNull</span>(port, <span class="hljs-string">"HTTPInfluxLineSource requires a port number to be"</span>
      + <span class="hljs-string">" specified"</span>)<span class="hljs-comment">;</span>
  }

  @Override
  public void start() {
    Preconditions<span class="hljs-preprocessor">.checkState</span>(srv == null,
            <span class="hljs-string">"Running HTTP Server found in source: "</span> + getName()
            + <span class="hljs-string">" before I started one."</span>
            + <span class="hljs-string">"Will not attempt to start."</span>)<span class="hljs-comment">;</span>
    srv = new Server()<span class="hljs-comment">;</span>

    // Connector Array
    Connector[] connectors = new Connector[<span class="hljs-number">1</span>]<span class="hljs-comment">;</span>


    if (sslEnabled) {
      SslSocketConnector sslSocketConnector = new HTTPSourceSocketConnector(excludedProtocols)<span class="hljs-comment">;</span>
      sslSocketConnector<span class="hljs-preprocessor">.setKeystore</span>(keyStorePath)<span class="hljs-comment">;</span>
      sslSocketConnector<span class="hljs-preprocessor">.setKeyPassword</span>(keyStorePassword)<span class="hljs-comment">;</span>
      sslSocketConnector<span class="hljs-preprocessor">.setReuseAddress</span>(true)<span class="hljs-comment">;</span>
      connectors[<span class="hljs-number">0</span>] = sslSocketConnector<span class="hljs-comment">;</span>
    } else {
      SelectChannelConnector connector = new SelectChannelConnector()<span class="hljs-comment">;</span>
      connector<span class="hljs-preprocessor">.setReuseAddress</span>(true)<span class="hljs-comment">;</span>
      connectors[<span class="hljs-number">0</span>] = connector<span class="hljs-comment">;</span>
    }

    connectors[<span class="hljs-number">0</span>]<span class="hljs-preprocessor">.setHost</span>(host)<span class="hljs-comment">;</span>
    connectors[<span class="hljs-number">0</span>]<span class="hljs-preprocessor">.setPort</span>(port)<span class="hljs-comment">;</span>
    srv<span class="hljs-preprocessor">.setConnectors</span>(connectors)<span class="hljs-comment">;</span>
    try {
      org<span class="hljs-preprocessor">.mortbay</span><span class="hljs-preprocessor">.jetty</span><span class="hljs-preprocessor">.servlet</span><span class="hljs-preprocessor">.Context</span> root =
        new org<span class="hljs-preprocessor">.mortbay</span><span class="hljs-preprocessor">.jetty</span><span class="hljs-preprocessor">.servlet</span><span class="hljs-preprocessor">.Context</span>(
          srv, <span class="hljs-string">"/"</span>, org<span class="hljs-preprocessor">.mortbay</span><span class="hljs-preprocessor">.jetty</span><span class="hljs-preprocessor">.servlet</span><span class="hljs-preprocessor">.Context</span><span class="hljs-preprocessor">.SESSIONS</span>)<span class="hljs-comment">;</span>
      root<span class="hljs-preprocessor">.addServlet</span>(new ServletHolder(new FlumeHTTPServlet()), <span class="hljs-string">"/"</span>)<span class="hljs-comment">;</span>
      HTTPServerConstraintUtil<span class="hljs-preprocessor">.enforceConstraints</span>(root)<span class="hljs-comment">;</span>
      srv<span class="hljs-preprocessor">.start</span>()<span class="hljs-comment">;</span>
      Preconditions<span class="hljs-preprocessor">.checkArgument</span>(srv<span class="hljs-preprocessor">.getHandler</span>()<span class="hljs-preprocessor">.equals</span>(root))<span class="hljs-comment">;</span>
    } catch (Exception ex) {
      LOG<span class="hljs-preprocessor">.error</span>(<span class="hljs-string">"Error while starting HTTPInfluxLineSource. Exception follows."</span>, ex)<span class="hljs-comment">;</span>
      Throwables<span class="hljs-preprocessor">.propagate</span>(ex)<span class="hljs-comment">;</span>
    }
    Preconditions<span class="hljs-preprocessor">.checkArgument</span>(srv<span class="hljs-preprocessor">.isRunning</span>())<span class="hljs-comment">;</span>
    sourceCounter<span class="hljs-preprocessor">.start</span>()<span class="hljs-comment">;</span>
    super<span class="hljs-preprocessor">.start</span>()<span class="hljs-comment">;</span>
  }

  @Override
  public void stop() {
    try {
      srv<span class="hljs-preprocessor">.stop</span>()<span class="hljs-comment">;</span>
      srv<span class="hljs-preprocessor">.join</span>()<span class="hljs-comment">;</span>
      srv = null<span class="hljs-comment">;</span>
    } catch (Exception ex) {
      LOG<span class="hljs-preprocessor">.error</span>(<span class="hljs-string">"Error while stopping HTTPInfluxLineSource. Exception follows."</span>, ex)<span class="hljs-comment">;</span>
    }
    sourceCounter<span class="hljs-preprocessor">.stop</span>()<span class="hljs-comment">;</span>
    LOG<span class="hljs-preprocessor">.info</span>(<span class="hljs-string">"Http source {} stopped. Metrics: {}"</span>, getName(), sourceCounter)<span class="hljs-comment">;</span>
  }

  private class FlumeHTTPServlet extends HttpServlet {

    private static final long serialVersionUID = <span class="hljs-number">4891924863218790344</span>L<span class="hljs-comment">;</span>

    @Override
    public void doPost(HttpServletRequest request, HttpServletResponse response)
            throws IOException {
      List&lt;Event&gt; events = Collections<span class="hljs-preprocessor">.emptyList</span>()<span class="hljs-comment">; //create empty list</span>
      try {
        events = handler<span class="hljs-preprocessor">.getEvents</span>(request)<span class="hljs-comment">;</span>
      } catch (HTTPBadRequestException ex) {
        LOG<span class="hljs-preprocessor">.warn</span>(<span class="hljs-string">"Received bad request from client. "</span>, ex)<span class="hljs-comment">;</span>
        response<span class="hljs-preprocessor">.sendError</span>(HttpServletResponse<span class="hljs-preprocessor">.SC</span>_BAD_REQUEST,
                <span class="hljs-string">"Bad request from client. "</span>
                + ex<span class="hljs-preprocessor">.getMessage</span>())<span class="hljs-comment">;</span>
        return<span class="hljs-comment">;</span>
      } catch (Exception ex) {
        LOG<span class="hljs-preprocessor">.warn</span>(<span class="hljs-string">"Deserializer threw unexpected exception. "</span>, ex)<span class="hljs-comment">;</span>
        response<span class="hljs-preprocessor">.sendError</span>(HttpServletResponse<span class="hljs-preprocessor">.SC</span>_INTERNAL_SERVER_ERROR,
                <span class="hljs-string">"Deserializer threw unexpected exception. "</span>
                + ex<span class="hljs-preprocessor">.getMessage</span>())<span class="hljs-comment">;</span>
        return<span class="hljs-comment">;</span>
      }
      sourceCounter<span class="hljs-preprocessor">.incrementAppendBatchReceivedCount</span>()<span class="hljs-comment">;</span>
      sourceCounter<span class="hljs-preprocessor">.addToEventReceivedCount</span>(events<span class="hljs-preprocessor">.size</span>())<span class="hljs-comment">;</span>
      try {
        getChannelProcessor()<span class="hljs-preprocessor">.processEventBatch</span>(events)<span class="hljs-comment">;</span>
      } catch (ChannelException ex) {
        LOG<span class="hljs-preprocessor">.warn</span>(<span class="hljs-string">"Error appending event to channel. "</span>
                + <span class="hljs-string">"Channel might be full. Consider increasing the channel "</span>
                + <span class="hljs-string">"capacity or make sure the sinks perform faster."</span>, ex)<span class="hljs-comment">;</span>
        response<span class="hljs-preprocessor">.sendError</span>(HttpServletResponse<span class="hljs-preprocessor">.SC</span>_SERVICE_UNAVAILABLE,
                <span class="hljs-string">"Error appending event to channel. Channel might be full."</span>
                + ex<span class="hljs-preprocessor">.getMessage</span>())<span class="hljs-comment">;</span>
        return<span class="hljs-comment">;</span>
      } catch (Exception ex) {
        LOG<span class="hljs-preprocessor">.warn</span>(<span class="hljs-string">"Unexpected error appending event to channel. "</span>, ex)<span class="hljs-comment">;</span>
        response<span class="hljs-preprocessor">.sendError</span>(HttpServletResponse<span class="hljs-preprocessor">.SC</span>_INTERNAL_SERVER_ERROR,
                <span class="hljs-string">"Unexpected error while appending event to channel. "</span>
                + ex<span class="hljs-preprocessor">.getMessage</span>())<span class="hljs-comment">;</span>
        return<span class="hljs-comment">;</span>
      }
      response<span class="hljs-preprocessor">.setCharacterEncoding</span>(request<span class="hljs-preprocessor">.getCharacterEncoding</span>())<span class="hljs-comment">;</span>
      response<span class="hljs-preprocessor">.setStatus</span>(HttpServletResponse<span class="hljs-preprocessor">.SC</span>_OK)<span class="hljs-comment">;</span>
      //TODO
      response<span class="hljs-preprocessor">.setContentType</span>(<span class="hljs-string">"application/json"</span>)<span class="hljs-comment">;</span>
      //TODO
      response<span class="hljs-preprocessor">.setHeader</span>(<span class="hljs-string">"X-Influxdb-Version"</span>,<span class="hljs-string">"1.0"</span>)<span class="hljs-comment">;</span>
      //TODO
      response<span class="hljs-preprocessor">.setDateHeader</span>(<span class="hljs-string">"Date"</span>,System<span class="hljs-preprocessor">.currentTimeMillis</span>())<span class="hljs-comment">;</span>
      //TODO
      response<span class="hljs-preprocessor">.setContentLength</span>(<span class="hljs-number">14</span>)<span class="hljs-comment">;</span>
      //TODO
      response<span class="hljs-preprocessor">.getWriter</span>()<span class="hljs-preprocessor">.write</span>(<span class="hljs-string">"{\"results\":[]}"</span>)<span class="hljs-comment">;</span>
      response<span class="hljs-preprocessor">.flushBuffer</span>()<span class="hljs-comment">;</span>
      sourceCounter<span class="hljs-preprocessor">.incrementAppendBatchAcceptedCount</span>()<span class="hljs-comment">;</span>
      sourceCounter<span class="hljs-preprocessor">.addToEventAcceptedCount</span>(events<span class="hljs-preprocessor">.size</span>())<span class="hljs-comment">;</span>
    }

    @Override
    public void doGet(HttpServletRequest request, HttpServletResponse response)
            throws IOException {
      doPost(request, response)<span class="hljs-comment">;</span>
    }
  }

  private static class HTTPSourceSocketConnector extends SslSocketConnector {

    private final List&lt;String&gt; excludedProtocols<span class="hljs-comment">;</span>
    HTTPSourceSocketConnector(List&lt;String&gt; excludedProtocols) {
      this<span class="hljs-preprocessor">.excludedProtocols</span> = excludedProtocols<span class="hljs-comment">;</span>
    }

    @Override
    public ServerSocket newServerSocket(String host, int port,
      int backlog) throws IOException {
      SSLServerSocket socket = (SSLServerSocket)super<span class="hljs-preprocessor">.newServerSocket</span>(host,
        port, backlog)<span class="hljs-comment">;</span>
      String[] protocols = socket<span class="hljs-preprocessor">.getEnabledProtocols</span>()<span class="hljs-comment">;</span>
      List&lt;String&gt; newProtocols = new ArrayList&lt;String&gt;(protocols<span class="hljs-preprocessor">.length</span>)<span class="hljs-comment">;</span>
      for(String protocol: protocols) {
        if (!excludedProtocols<span class="hljs-preprocessor">.contains</span>(protocol)) {
          newProtocols<span class="hljs-preprocessor">.add</span>(protocol)<span class="hljs-comment">;</span>
        }
      }
      socket<span class="hljs-preprocessor">.setEnabledProtocols</span>(
        newProtocols<span class="hljs-preprocessor">.toArray</span>(new String[newProtocols<span class="hljs-preprocessor">.size</span>()]))<span class="hljs-comment">;</span>
      return socket<span class="hljs-comment">;</span>
    }
  }
}
</code></pre>



<h4 id="httpsourcehandler">HTTPSourceHandler</h4>

<p>flume source 插件 要配合 handler 插件一起使用，样例代码sample如下</p>

<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">package</span> org.apache.flume.source.influxhttp;

<span class="hljs-keyword">import</span> org.apache.flume.Context;
<span class="hljs-keyword">import</span> org.apache.flume.Event;
<span class="hljs-keyword">import</span> org.apache.flume.event.EventBuilder;
<span class="hljs-keyword">import</span> org.apache.flume.source.http.HTTPSourceHandler;
<span class="hljs-keyword">import</span> org.slf4j.Logger;
<span class="hljs-keyword">import</span> org.slf4j.LoggerFactory;

<span class="hljs-keyword">import</span> javax.servlet.http.HttpServletRequest;
<span class="hljs-keyword">import</span> java.io.BufferedReader;
<span class="hljs-keyword">import</span> java.nio.charset.UnsupportedCharsetException;
<span class="hljs-keyword">import</span> java.util.*;

<span class="hljs-javadoc">/**
 * Created by shen.xiangxiang on 2017/3/13.
 */</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">HTTPSourceInfluxHandler</span> <span class="hljs-keyword">implements</span> <span class="hljs-title">HTTPSourceHandler</span> {</span>
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">final</span> Logger LOG = LoggerFactory.getLogger(HTTPSourceInfluxHandler.class);




    <span class="hljs-javadoc">/**
     * {@inheritDoc}
     */</span>
    <span class="hljs-keyword">public</span> List&lt;Event&gt; <span class="hljs-title">getEvents</span>(HttpServletRequest request) <span class="hljs-keyword">throws</span> Exception {
        BufferedReader reader = request.getReader();
        String charset = request.getCharacterEncoding();


        Enumeration iter = request.getHeaderNames();
        <span class="hljs-keyword">while</span> (iter.hasMoreElements()) {
            String name = (String) iter.nextElement();

            LOG.debug(name  + <span class="hljs-string">":"</span> + request.getHeader(name));

        }




        <span class="hljs-comment">//UTF-8 is default for JSON. If no charset is specified, UTF-8 is to</span>
        <span class="hljs-comment">//be assumed.</span>
        <span class="hljs-keyword">if</span> (charset == <span class="hljs-keyword">null</span>) {
            LOG.debug(<span class="hljs-string">"Charset is null, default charset of UTF-8 will be used."</span>);
            charset = <span class="hljs-string">"UTF-8"</span>;
        } <span class="hljs-keyword">else</span> <span class="hljs-keyword">if</span> (!(charset.equalsIgnoreCase(<span class="hljs-string">"utf-8"</span>)
                || charset.equalsIgnoreCase(<span class="hljs-string">"utf-16"</span>)
                || charset.equalsIgnoreCase(<span class="hljs-string">"utf-32"</span>))) {
            LOG.error(<span class="hljs-string">"Unsupported character set in request {}. "</span>
                    + <span class="hljs-string">"JSON handler supports UTF-8, "</span>
                    + <span class="hljs-string">"UTF-16 and UTF-32 only."</span>, charset);
            <span class="hljs-keyword">throw</span> <span class="hljs-keyword">new</span> UnsupportedCharsetException(<span class="hljs-string">"JSON handler supports UTF-8, "</span>
                    + <span class="hljs-string">"UTF-16 and UTF-32 only."</span>);
        }


        Map&lt;String, String&gt; eventHeader = <span class="hljs-keyword">new</span> HashMap&lt;String, String&gt;();
        eventHeader.put(<span class="hljs-string">"mt"</span>, <span class="hljs-string">"METL"</span>);
        eventHeader.put(<span class="hljs-string">"topic"</span>, <span class="hljs-string">"metrics_influxline"</span>);

        List&lt;Event&gt; eventList = <span class="hljs-keyword">new</span> ArrayList&lt;Event&gt;();

        String line;
        <span class="hljs-keyword">while</span> ((line = reader.readLine()) != <span class="hljs-keyword">null</span>) {
            LOG.debug(<span class="hljs-string">"line:"</span> + line);
            eventList.add(EventBuilder.withBody(line.getBytes(charset),eventHeader));

        }

        LOG.info(<span class="hljs-string">"eventList.size():"</span>  + <span class="hljs-string">":"</span> + eventList.size());
        <span class="hljs-keyword">return</span> eventList;
    }


    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">configure</span>(Context context) {
    }

}</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>