---
layout:     post
title:      Hadoop上路_06-在Ubuntu中使用eclipse操作HDFS
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div class="BlogAnchor" style="background-color:rgb(244,247,249);line-height:23px;font-family:Verdana, sans-serif, '宋体';font-size:13px;">
<p style="font-size:18px;color:rgb(21,162,48);">
<span class="corner" id="AnchorContentToggle" title="点击收起目录" style="font-size:13px;color:rgb(255,255,255);display:inline-block;line-height:20px;background-color:rgb(0,114,187);">目录[-]</span></p>
<div class="AnchorContent" id="AnchorContent">

<a href="http://my.oschina.net/vigiles/blog/132278#OSC_h2_1" rel="nofollow" style="color:rgb(0,114,187);text-decoration:none;">1.解压eclipse：</a>
<a href="http://my.oschina.net/vigiles/blog/132278#OSC_h2_2" rel="nofollow" style="color:rgb(0,114,187);text-decoration:none;">2.安装hadoop开发插件： </a>
<a href="http://my.oschina.net/vigiles/blog/132278#OSC_h2_3" rel="nofollow" style="color:rgb(0,114,187);text-decoration:none;">3.执行快捷方式： </a>
<a href="http://my.oschina.net/vigiles/blog/132278#OSC_h2_4" rel="nofollow" style="color:rgb(0,114,187);text-decoration:none;">4.配置Hadoop视图：</a>
<a href="http://my.oschina.net/vigiles/blog/132278#OSC_h3_5" rel="nofollow" style="color:rgb(0,114,187);text-decoration:none;">        1）window -&gt; preferences -&gt; Hadoop Map/Reduce，              </a>
<a href="http://my.oschina.net/vigiles/blog/132278#OSC_h3_6" rel="nofollow" style="color:rgb(0,114,187);text-decoration:none;">        2）打开Hadoop视图，      </a>
<a href="http://my.oschina.net/vigiles/blog/132278#OSC_h3_7" rel="nofollow" style="color:rgb(0,114,187);text-decoration:none;">        3）新建Hadoop Location连接，             </a>
<a href="http://my.oschina.net/vigiles/blog/132278#OSC_h3_8" rel="nofollow" style="color:rgb(0,114,187);text-decoration:none;">        4）配置主机、端口，          </a>
<a href="http://my.oschina.net/vigiles/blog/132278#OSC_h3_9" rel="nofollow" style="color:rgb(0,114,187);text-decoration:none;">        5）完成：          </a>
<a href="http://my.oschina.net/vigiles/blog/132278#OSC_h2_10" rel="nofollow" style="color:rgb(0,114,187);text-decoration:none;">5.创建项目：</a></div>
</div>
<div class="BlogContent" style="font-size:13px;overflow:hidden;color:rgb(51,51,51);line-height:23px;font-family:Verdana, sans-serif, '宋体';">
<span id="OSC_h2_1"></span>
<h2 style="line-height:36px;font-size:20px;">1.<span>解压</span><span>eclipse</span><span>：</span></h2>
<p style="letter-spacing:.5px;font-size:12.5px;">
<span>         eclipse-SDK-4.2-linux-gtk.tar.gz</span><br>
        （<span>1</span><span>）解压程序文件：<br></span><span style="line-height:1.5;font-size:10pt;"></span></p>
<div>
<div id="highlighter_150343" class="syntaxhighlighter shell" style="overflow:auto !important;font-size:1em !important;">
<div class="toolbar" style="background-color:rgb(108,226,108) !important;border:none !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;font-size:10px !important;min-height: !important;z-index:10 !important;color:#FFFFFF !important;">
<span><a href="http://my.oschina.net/vigiles/blog/132278#" rel="nofollow" class="toolbar_item command_help help" style="color:#FFFFFF !important;font-size:1em !important;border:0px !important;line-height:1.1em !important;overflow:visible !important;text-align:center !important;vertical-align:baseline !important;min-height: !important;display:block !important;text-decoration:none !important;">?</a></span></div>
<table border="0" cellpadding="0" cellspacing="0" style="font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;"><tbody style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;"><tr style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;"><td class="gutter" style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;font-size:1em !important;min-height: !important;color:rgb(175,175,175) !important;">
<div class="line number1 index0 alt2" style="border-width:0px 3px 0px 0px !important;border-right-style:solid !important;border-right-color:rgb(108,226,108) !important;line-height:1.1em !important;overflow:visible !important;text-align:right !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;">
1</div>
</td>
<td class="code" style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;font-size:1em !important;min-height: !important;">
<div style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;">
<div class="line number1 index0 alt2" style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;">
<code class="shell plain" style="font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;">hm@hm-ubuntu:</code><code class="shell plain" style="font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;">/usr</code><code class="shell plain" style="font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;">$
</code><code class="shell functions" style="font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;color:rgb(255,20,147) !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;">sudo</code>
<code class="shell functions" style="font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;color:rgb(255,20,147) !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;">
tar</code> <code class="shell plain" style="font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;">
eclipse.</code><code class="shell functions" style="font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;color:rgb(255,20,147) !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;">tar</code><code class="shell plain" style="font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;">.gz</code></div>
</div>
</td>
</tr></tbody></table></div>
</div>
<p style="letter-spacing:.5px;font-size:12.5px;">
        <a href="http://static.oschina.net/uploads/space/2013/0520/224520_w48W_256028.png" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><img src="http://static.oschina.net/uploads/space/2013/0520/224520_w48W_256028.png" alt="" style="border:1px solid rgb(221,221,221);background-color:rgb(244,247,249);"></a>  </p>
<p style="letter-spacing:.5px;font-size:12.5px;">
        <a href="http://static.oschina.net/uploads/space/2013/0520/224724_4Amp_256028.png" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><img src="http://static.oschina.net/uploads/space/2013/0520/224724_4Amp_256028.png" alt="" style="border:1px solid rgb(221,221,221);background-color:rgb(244,247,249);"></a> 
  </p>
<p style="letter-spacing:.5px;font-size:12.5px;">
        （<span>2</span><span>）在</span><span>Ubuntu</span><span>桌面点鼠标右键，</span>创建启动器：   </p>
<p style="letter-spacing:.5px;font-size:12.5px;">
        <a href="http://static.oschina.net/uploads/space/2013/0520/224735_nzn1_256028.png" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><img src="http://static.oschina.net/uploads/space/2013/0520/224735_nzn1_256028.png" alt="" style="border:1px solid rgb(221,221,221);background-color:rgb(244,247,249);"></a> 
  <a href="http://static.oschina.net/uploads/space/2013/0520/224747_ec3V_256028.png" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><img src="http://static.oschina.net/uploads/space/2013/0520/224747_ec3V_256028.png" alt="" style="border:1px solid rgb(221,221,221);background-color:rgb(244,247,249);"></a>  </p>
<p style="letter-spacing:.5px;font-size:12.5px;">
        （<span>3</span><span>）在“命令”选择</span><span>eclipse</span><span>执行文件：</span></p>
<p style="letter-spacing:.5px;font-size:12.5px;">
        <a href="http://static.oschina.net/uploads/space/2013/0520/224848_4oel_256028.png" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><img src="http://static.oschina.net/uploads/space/2013/0520/224848_4oel_256028.png" alt="" style="border:1px solid rgb(221,221,221);background-color:rgb(244,247,249);"></a>  </p>
<p style="letter-spacing:.5px;font-size:12.5px;">
        <a href="http://static.oschina.net/uploads/space/2013/0520/224940_vHvF_256028.png" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><img src="http://static.oschina.net/uploads/space/2013/0520/224940_vHvF_256028.png" alt="" style="border:1px solid rgb(221,221,221);background-color:rgb(244,247,249);"></a>  </p>
<span id="OSC_h2_2"></span>
<h2 style="line-height:36px;font-size:20px;">2.<span>安装</span><span>hadoop</span><span>开发插件： </span></h2>
<p style="letter-spacing:.5px;font-size:12.5px;">
        hadoop-eclipse-plugin-*.jar<span>插件须符合此</span><span>eclipse</span><span>版本。    </span></p>
<p style="letter-spacing:.5px;font-size:12.5px;">
        （<span>1</span><span>）手动制作</span><span>hadoop-eclipse-plugin</span><span>插件  </span></p>
<p style="letter-spacing:.5px;font-size:12.5px;">
                见《Hadoop上路_01-手动制作hadoop-eclipse-plugin插件<span>》 </span></p>
<p style="letter-spacing:.5px;font-size:12.5px;">
        （<span>2</span><span>）把插件放入</span><span>%eclipse%/dropins/hadoop/plugins</span><span>目录  </span></p>
<p style="letter-spacing:.5px;font-size:12.5px;">
                首先在<span>%eclipse%/dropins/</span><span>中创建</span><span>hadoop/plugins</span><span>层级目录  </span></p>
<span id="OSC_h2_3"></span>
<h2 style="line-height:36px;font-size:20px;">3.<span>执行快捷方式： </span></h2>
<p style="letter-spacing:.5px;font-size:12.5px;">
        <a href="http://static.oschina.net/uploads/space/2013/0520/225237_k6XW_256028.jpg" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><img src="http://static.oschina.net/uploads/space/2013/0520/225237_k6XW_256028.jpg" alt="" style="border:1px solid rgb(221,221,221);background-color:rgb(244,247,249);"></a>  </p>
<p style="letter-spacing:.5px;font-size:12.5px;">
        <span><span style="font-size:16px;color:rgb(229,51,51);">错误：</span></span>   <br><span style="line-height:1.5;font-size:10pt;">               </span><span style="line-height:1.5;font-size:14px;color:rgb(229,51,51);">重新配置了环境变量中的</span><span style="line-height:1.5;font-size:14px;color:rgb(229,51,51);">jdk</span><span style="line-height:1.5;font-size:14px;color:rgb(229,51,51);">路径，</span><span style="line-height:1.5;font-size:14px;color:rgb(229,51,51);">eclipse</span><span style="line-height:1.5;font-size:14px;color:rgb(229,51,51);">不能检测到新路径</span><span style="line-height:1.5;font-size:10pt;"> </span></p>
<p style="letter-spacing:.5px;font-size:12.5px;">
              <a href="http://static.oschina.net/uploads/space/2013/0520/225300_SWgV_256028.png" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><img src="http://static.oschina.net/uploads/space/2013/0520/225300_SWgV_256028.png" alt="" style="border:1px solid rgb(221,221,221);background-color:rgb(244,247,249);"></a> 
  </p>
<p style="letter-spacing:.5px;font-size:12.5px;">
        <span><span style="color:rgb(0,153,0);font-size:16px;">解决：</span></span> <br><span style="line-height:1.5;font-size:10pt;">              <span style="color:rgb(0,153,0);font-size:14px;">更新环境变量后重启系统</span>    </span></p>
<p style="letter-spacing:.5px;font-size:12.5px;">
              <a href="http://static.oschina.net/uploads/space/2013/0520/225317_SlRA_256028.png" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><img src="http://static.oschina.net/uploads/space/2013/0520/225317_SlRA_256028.png" alt="" style="border:1px solid rgb(221,221,221);background-color:rgb(244,247,249);"></a><br>
             <span style="line-height:1.5;font-size:10pt;"> </span><a href="http://static.oschina.net/uploads/space/2013/0520/225331_mlXm_256028.png" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><img src="http://static.oschina.net/uploads/space/2013/0520/225331_mlXm_256028.png" alt="" style="border:1px solid rgb(221,221,221);background-color:rgb(244,247,249);"></a></p>
<span id="OSC_h2_4"></span>
<h2 style="line-height:36px;font-size:20px;">4.<span>配置</span><span>Hadoop</span><span>视图：</span></h2>
<p style="letter-spacing:.5px;font-size:12.5px;">
        <a href="http://static.oschina.net/uploads/space/2013/0520/225810_lImY_256028.png" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><img src="http://static.oschina.net/uploads/space/2013/0520/225810_lImY_256028.png" alt="" style="border:1px solid rgb(221,221,221);background-color:rgb(244,247,249);"></a><br>
        <a href="http://static.oschina.net/uploads/space/2013/0520/225829_ixDo_256028.png" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><img src="http://static.oschina.net/uploads/space/2013/0520/225829_ixDo_256028.png" alt="" style="border:1px solid rgb(221,221,221);background-color:rgb(244,247,249);"></a></p>
<span id="OSC_h3_5"></span>
<h3 style="line-height:28px;font-size:16px;">        1<span>）</span><span>window -&gt; preferences -&gt; Hadoop Map/Reduce</span><span>，              </span></h3>
<p style="letter-spacing:.5px;font-size:12.5px;">
                配置任意<span>hadoop</span><span>解压目录（仅在创建</span><span>Map/Reduce Project</span><span>项目时供自动加载</span><span>jar</span><span>包用；如果创建</span><span>Java Project</span><span>可以置空）：   </span></p>
<p style="letter-spacing:.5px;font-size:12.5px;">
                <a href="http://static.oschina.net/uploads/space/2013/0520/225845_cVQA_256028.png" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><img src="http://static.oschina.net/uploads/space/2013/0520/225845_cVQA_256028.png" alt="" style="border:1px solid rgb(221,221,221);background-color:rgb(244,247,249);"></a>  </p>
<p style="letter-spacing:.5px;font-size:12.5px;">
                 <span><span style="color:rgb(229,51,51);font-size:16px;">错误：</span></span> <br>
                        <a href="http://blog.csdn.net/witsmakemen/article/details/7832617" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><span><span style="color:rgb(229,51,51);font-size:14px;">jdk</span><span style="color:rgb(229,51,51);font-size:14px;">版本错误</span></span></a><span style="line-height:1.5;font-size:10pt;">：<br></span><span style="line-height:1.5;font-size:10pt;">                        <span style="color:rgb(229,51,51);font-size:14px;">当前使用的hadoop-eclipse-plugin-1.1.2插件是基于JDK1.7制作的，而系统中安装的是JDK1.6</span>。<br><br></span><span style="line-height:1.5;font-size:10pt;">eclipse.buildId=M20130204-1200<br></span><span style="line-height:1.5;font-size:10pt;">java.version=1.6.0_24<br></span><span style="line-height:1.5;font-size:10pt;">java.vendor=Sun Microsystems Inc.<br></span><span style="line-height:1.5;font-size:10pt;">BootLoader constants: OS=linux, ARCH=x86, WS=gtk, NL=zh_CN<br></span><span style="line-height:1.5;font-size:10pt;">Command-line arguments:  -os linux -ws gtk -arch x86<br><br></span><span style="line-height:1.5;font-size:10pt;">Error<br></span><span style="line-height:1.5;font-size:10pt;">Sun May 19 09:10:21 CST 2013<br></span><span style="line-height:1.5;font-size:10pt;">Unable to create the selected preference page.<br><br></span><span style="line-height:1.5;font-size:10pt;"><span><span style="color:rgb(229,51,51);">java.lang.UnsupportedClassVersionError: org/apache/hadoop/eclipse/preferences/MapReducePreferencePage : Unsupported major.minor version 51.0</span></span>   <br><br></span><span style="line-height:1.5;font-size:10pt;"><span style="color:rgb(229,51,51);">at java.lang.ClassLoader.defineClass1(Native Method)</span><br></span><span style="line-height:1.5;font-size:10pt;"><span></span><span style="color:rgb(229,51,51);">at java.lang.ClassLoader.defineClassCond(ClassLoader.java:632)</span><br></span><span style="line-height:1.5;font-size:10pt;"><span style="color:rgb(229,51,51);">at java.lang.ClassLoader.defineClass(ClassLoader.java:616)</span><br></span><span style="line-height:1.5;font-size:10pt;"><span style="color:rgb(229,51,51);">at org.eclipse.osgi.internal.baseadaptor.DefaultClassLoader.defineClass(DefaultClassLoader.java:188)</span><br></span><span style="line-height:1.5;font-size:10pt;"><span style="color:rgb(229,51,51);">at org.eclipse.osgi.baseadaptor.loader.ClasspathManager.defineClassHoldingLock(ClasspathManager.java:632)</span><br></span><span style="line-height:1.5;font-size:10pt;"><span style="color:rgb(229,51,51);">... ... </span><br></span><span style="line-height:1.5;font-size:10pt;"><span style="color:rgb(229,51,51);">at org.eclipse.equinox.launcher.Main.run(Main.java:1438)</span>   <br><br>
                  </span><a href="http://static.oschina.net/uploads/space/2013/0520/230012_tJ4a_256028.png" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><img src="http://static.oschina.net/uploads/space/2013/0520/230012_tJ4a_256028.png" alt="" style="border:1px solid rgb(221,221,221);background-color:rgb(244,247,249);"></a><span style="line-height:1.5;font-size:10pt;">  </span></p>
<p style="letter-spacing:.5px;font-size:12.5px;">
                <span><span style="color:rgb(0,153,0);font-size:16px;">解决：</span></span><br><span style="line-height:1.5;font-size:10pt;">                        <span style="font-size:14px;color:rgb(0,153,0);">升级当前系统的</span></span><span style="line-height:1.5;font-size:14px;color:rgb(0,153,0);">JDK</span><span style="line-height:1.5;font-size:14px;color:rgb(0,153,0);">版本到插件使用的</span><span style="line-height:1.5;font-size:14px;color:rgb(0,153,0);">1.7</span><span style="line-height:1.5;font-size:10pt;">。
    </span></p>
<span id="OSC_h3_6"></span>
<h3 style="line-height:28px;font-size:16px;">        2<span>）打开</span><span>Hadoop</span><span>视图，      </span></h3>
<p style="letter-spacing:.5px;font-size:12.5px;">
                <a href="http://static.oschina.net/uploads/space/2013/0520/230105_TJ7N_256028.png" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><img src="http://static.oschina.net/uploads/space/2013/0520/230105_TJ7N_256028.png" alt="" style="border:1px solid rgb(221,221,221);background-color:rgb(244,247,249);"></a>  </p>
<span id="OSC_h3_7"></span>
<h3 style="line-height:28px;font-size:16px;">        3<span>）新建</span><span>Hadoop Location</span><span>连接，             </span></h3>
<p style="letter-spacing:.5px;font-size:12.5px;">
                <a href="http://static.oschina.net/uploads/space/2013/0520/230118_ChP9_256028.png" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><img src="http://static.oschina.net/uploads/space/2013/0520/230118_ChP9_256028.png" alt="" style="border:1px solid rgb(221,221,221);background-color:rgb(244,247,249);"></a></p>
<span id="OSC_h3_8"></span>
<h3 style="line-height:28px;font-size:16px;">        4<span>）配置主机、端口，          </span></h3>
<p style="letter-spacing:.5px;font-size:12.5px;">
                <a href="http://static.oschina.net/uploads/space/2013/0520/230219_bMuw_256028.png" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><img src="http://static.oschina.net/uploads/space/2013/0520/230219_bMuw_256028.png" alt="" style="border:1px solid rgb(221,221,221);background-color:rgb(244,247,249);"></a> 
  </p>
<span id="OSC_h3_9"></span>
<h3 style="line-height:28px;font-size:16px;">        5）完成：          </h3>
<p style="letter-spacing:.5px;font-size:12.5px;">
                <a href="http://static.oschina.net/uploads/space/2013/0520/230229_lFNQ_256028.png" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><img src="http://static.oschina.net/uploads/space/2013/0520/230229_lFNQ_256028.png" alt="" style="border:1px solid rgb(221,221,221);background-color:rgb(244,247,249);"></a> 
   </p>
<p style="letter-spacing:.5px;font-size:12.5px;">
                “local”下的目录即HDFS<span>中的目录。</span>可以直接在上面进行下载、创建、上传、删除等操作：</p>
<p style="letter-spacing:.5px;font-size:12.5px;">
                <a href="http://static.oschina.net/uploads/space/2013/0520/230622_NyHP_256028.png" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><img src="http://static.oschina.net/uploads/space/2013/0520/230622_NyHP_256028.png" alt="" style="border:1px solid rgb(221,221,221);background-color:rgb(244,247,249);"></a> 
  </p>
<p style="letter-spacing:.5px;font-size:12.5px;">
                <span><span style="color:rgb(229,51,51);font-size:16px;">错误：</span></span><br><span style="line-height:1.5;font-size:10pt;">                        <span style="color:rgb(229,51,51);font-size:14px;">hadoop-eclipse-plugin-*.jar</span></span><span style="line-height:1.5;font-size:14px;color:rgb(229,51,51);">插件中</span><span style="line-height:1.5;font-size:14px;color:rgb(229,51,51);">jar</span><span style="line-height:1.5;font-size:14px;color:rgb(229,51,51);">包引用错误</span><span style="line-height:1.5;font-size:10pt;">  </span></p>
<p style="letter-spacing:.5px;font-size:12.5px;">
                       <a href="http://static.oschina.net/uploads/space/2013/0520/230706_mHiJ_256028.png" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><img src="http://static.oschina.net/uploads/space/2013/0520/230706_mHiJ_256028.png" alt="" style="border:1px solid rgb(221,221,221);background-color:rgb(244,247,249);"></a> 
  <br><span style="line-height:1.5;font-size:10pt;"><br>
An internal error occurred during: "Map/Reduce location status updater".  <br></span><span style="line-height:1.5;font-size:10pt;">org/codehaus/jackson/map/JsonMappingException            <br></span><span style="line-height:1.5;font-size:10pt;"><br>
              <span style="color:rgb(0,153,0);font-size:16px;"><span>解决：</span></span> <br></span><span style="line-height:1.5;font-size:10pt;">                     <span style="color:rgb(0,153,0);font-size:14px;">在制作插件时</span></span><span style="line-height:1.5;font-size:14px;color:rgb(0,153,0);">%hadoop%/</span><span style="line-height:1.5;font-size:14px;color:rgb(0,153,0);">src/contrib/eclipse-plugin/META-INF/MANIFEST.MF中的Bundle-ClassPath</span><span style="line-height:1.5;font-size:14px;color:rgb(0,153,0);">内</span><span style="line-height:1.5;font-size:14px;color:rgb(0,153,0);">jar</span><span style="line-height:1.5;font-size:14px;color:rgb(0,153,0);">的路径写错导致的，修改正确，重新制作</span><span style="line-height:1.5;font-size:10pt;">。
  </span></p>
<span id="OSC_h2_10"></span>
<h2 style="line-height:36px;font-size:20px;">5.<span>创建项目：</span></h2>
<p style="letter-spacing:.5px;font-size:12.5px;">
        在<span>Map/Reduce</span><span>视图中创建</span>java project： </p>
<p style="letter-spacing:.5px;font-size:12.5px;">
        <a href="http://static.oschina.net/uploads/space/2013/0520/230725_jZRc_256028.png" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><img src="http://static.oschina.net/uploads/space/2013/0520/230725_jZRc_256028.png" alt="" style="border:1px solid rgb(221,221,221);background-color:rgb(244,247,249);"></a></p>
</div>
            </div>
                </div>