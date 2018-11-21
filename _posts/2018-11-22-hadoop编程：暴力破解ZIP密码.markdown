---
layout:     post
title:      hadoop编程：暴力破解ZIP密码
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/kydkong/article/details/50087805				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p></p>
<p> </p>
<p align="center"><strong>“云计算技术与应用课程设计”总结报告</strong></p>
<p align="center"> </p>
<p align="left"> </p>
<h2>背景</h2>
<p>       互联网发展到今天，许多数据与文件的传输仍然要用到压缩技术。其中最有名的无疑是ZIP压缩文件，是无损压缩的杰出代表。</p>
<p>       但是，我们今天在上网的时候仍然会遇到这样尴尬的情况：下载了一个ZIP压缩包，但是解压这个包却需要密码，通常这些密码会写在帖子里，但是如果你找不到这个压缩包的出处或者联系到这个压缩包的作者的话，是不能解压出里面的内容的。于是，有了暴力破解这类压缩包文件的软件。</p>
<p>       但是，又出现一个问题，ZIP压缩包的密码是不定长的，里面可以是任意字符，也就是说一个8位密码，那它的可能次数为(26+10)^8=282429536481次，如果用一台计算机进行暴力破解可能需要一周或者更多时间。</p>
<p>       为了处理这个情况，这个项目就应运而生了。</p>
<p> </p>
<h2>介绍</h2>
<p>       GetZipKey是运行在Hadoop分布式集群上的软件，可以充分运用分布式集群的并行计算能力对ZIP压缩包进行暴力破解密码。</p>
<p> </p>
<h2>主要设计思想</h2>
<p>1.        获取输入的参数，对参数进行保存。</p>
<p>2.        通过自定义输入文件格式类InputFormat，根据输入的参数，生成可能的密码。</p>
<p>3.        密码由Hadoop分配给各个机器，由各个机器尝试着解压给定的ZIP压缩包。</p>
<p>4.        由Hadoop对尝试结果进行汇总，保存在输出文件内。</p>
<p> <img src="https://img-blog.csdn.net/20151128215030323?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p> </p>
<br clear="all"><p align="left"> </p>
<br clear="all"><h2>主要算法</h2>
<h4>ZIP压缩文件解压</h4>
<p>判断密码是否正确是对于ZIP解压的一种应用。</p>
<p>使用的是开源项目winzipaes提供的方法。</p>
<p>在导入winzipaes与其的依赖包后，解压ZIP文件变得简单了起来。</p>
<p> </p>
<p><span style="color:#FF0000;"> </span></p>
<table border="1" cellpadding="0" cellspacing="0"><tbody><tr><td valign="top">
<p><span style="color:#FF0000;">注意：在这篇文档中的代码全部为示例代码，经过删减，不能运行，只是提供一些参考。</span></p>
</td>
</tr><tr><td valign="top">
<p>解压ZIP压缩包的关键类<span style="color:#000000;">DecryptionZipUtil</span></p>
</td>
</tr><tr><td valign="top">
<p align="left"><strong><span style="color:#7F0055;">package</span></strong><span style="color:#000000;"> com.fieldsoft.winzipaes;</span></p>
<p align="left"><strong><span style="color:#7F0055;">import</span></strong><span style="color:#000000;"> java.io.File;</span></p>
<p align="left"><strong><span style="color:#7F0055;">+</span></strong></p>
<p align="left"> </p>
<p align="left"><span style="color:#3F5FBF;">/**</span></p>
<p align="left"><span style="color:#3F5FBF;"> </span>* <span style="color:#3F5FBF;">
压缩指定文件或目录为</span><span style="color:#3F5FBF;">ZIP</span><span style="color:#3F5FBF;">格式压缩文件</span></p>
<p align="left"><span style="color:#3F5FBF;"> </span>* <span style="color:#3F5FBF;">
支持中文</span><span style="color:#3F5FBF;">(</span><span style="color:#3F5FBF;">修改源码后</span><span style="color:#3F5FBF;">)</span></p>
<p align="left"><span style="color:#3F5FBF;"> </span>* <span style="color:#3F5FBF;">
支持密码</span><span style="color:#3F5FBF;">(</span><span style="color:#3F5FBF;">仅支持</span><span style="color:#3F5FBF;">256bit</span><span style="color:#3F5FBF;">的</span><span style="color:#3F5FBF;">AES</span><span style="color:#3F5FBF;">加密解密</span><span style="color:#3F5FBF;">)</span></p>
<p align="left"><span style="color:#3F5FBF;"> </span>* <span style="color:#3F5FBF;">
依赖</span><u><span style="color:#3F5FBF;">bcprov</span></u><span style="color:#3F5FBF;">项目</span><span style="color:#3F5FBF;">(<u>bcprov</u></span><span style="color:#7F7F9F;">-</span><span style="color:#3F5FBF;">jdk16</span><span style="color:#7F7F9F;">-</span><span style="color:#3F5FBF;">140.jar)</span></p>
<p align="left"><span style="color:#3F5FBF;"> </span>* </p>
<p align="left"><span style="color:#3F5FBF;"> </span>* <strong><span style="color:#7F9FBF;">@author</span></strong><span style="color:#3F5FBF;"><u>zyh</u></span></p>
<p align="left"><span style="color:#3F5FBF;"> </span>*/</p>
<p align="left"><strong><span style="color:#7F0055;">public</span><span style="color:#7F0055;">class</span></strong><span style="color:#000000;"> DecryptionZipUtil {</span></p>
<p align="left"><span style="color:#000000;">         </span></p>
<p align="left"><span style="color:#000000;">         </span><strong><span style="color:#7F0055;">public</span><span style="color:#7F0055;">static</span><span style="color:#7F0055;">void</span></strong><span style="color:#000000;"> zip(String</span><span style="color:#6A3E3E;">srcFile</span><span style="color:#000000;">,String</span><span style="color:#6A3E3E;">destPath</span><span style="color:#000000;">,String</span><span style="color:#6A3E3E;">passwd</span><span style="color:#000000;">);</span></p>
<p align="left"><span style="color:#000000;">         </span><strong><span style="color:#7F0055;">private</span><span style="color:#7F0055;">static</span><span style="color:#7F0055;">void</span></strong><span style="color:#000000;"> doZip(File</span><span style="color:#6A3E3E;">file</span><span style="color:#000000;">,
 AesZipFileEncrypter</span><span style="color:#6A3E3E;">encrypter</span><span style="color:#000000;">,</span></p>
<p align="left"><span style="color:#000000;">                            </span>String<span style="color:#6A3E3E;">pathForEntry</span><span style="color:#000000;">, String</span><span style="color:#6A3E3E;">passwd</span><span style="color:#000000;">)
</span><strong><span style="color:#7F0055;">throws</span></strong><span style="color:#000000;"> IOException ;</span></p>
<p align="left"><span style="color:#000000;">         </span></p>
<p align="left"><span style="color:#000000;">         </span><span style="color:#3F5FBF;">/**</span></p>
<p align="left"><span style="color:#3F5FBF;">         </span> * <span style="color:#3F5FBF;">
使用给定密码解压指定压缩文件到指定目录</span></p>
<p align="left"><span style="color:#3F5FBF;">         </span> * <strong><span style="color:#7F9FBF;">@param</span></strong><span style="color:#3F5FBF;"> inFile</span><span style="color:#3F5FBF;">指定</span><u><span style="color:#3F5FBF;">Zip</span></u><span style="color:#3F5FBF;">文件</span></p>
<p align="left"><span style="color:#3F5FBF;">         </span> * <strong><span style="color:#7F9FBF;">@param</span></strong><span style="color:#3F5FBF;"> outDir</span><span style="color:#3F5FBF;">解压目录</span></p>
<p align="left"><span style="color:#3F5FBF;">         </span> * <strong><span style="color:#7F9FBF;">@param</span></strong><span style="color:#3F5FBF;"> passwd</span><span style="color:#3F5FBF;">解压密码</span></p>
<p align="left"><span style="color:#3F5FBF;">         </span> * <strong><span style="color:#7F9FBF;">@return</span></strong><span style="color:#3F5FBF;"> 0:<u>pwd</u> err 1:<u>pwd</u> true</span><span style="color:#7F7F9F;">-</span><span style="color:#3F5FBF;">1:other
 err</span></p>
<p align="left"><span style="color:#3F5FBF;">         </span> */</p>
<p align="left"><span style="color:#000000;">         </span><strong><span style="color:#7F0055;">public</span><span style="color:#7F0055;">static</span><span style="color:#7F0055;">int</span></strong><span style="color:#000000;"> unzip(String</span><span style="color:#6A3E3E;">inFile</span><span style="color:#000000;">,
 String</span><span style="color:#6A3E3E;">outDir</span><span style="color:#000000;">, String</span><span style="color:#6A3E3E;">passwd</span><span style="color:#000000;">) {</span></p>
<p align="left"><span style="color:#000000;">                   </span>File <span style="color:#6A3E3E;">
outDirectory</span><span style="color:#000000;"> = </span><strong><span style="color:#7F0055;">new</span></strong><span style="color:#000000;"> File(</span><span style="color:#6A3E3E;">outDir</span><span style="color:#000000;">);</span></p>
<p align="left"><span style="color:#000000;">         </span>         <strong><span style="color:#7F0055;">if</span></strong><span style="color:#000000;"> (!</span><span style="color:#6A3E3E;">outDirectory</span><span style="color:#000000;">.exists()) {</span></p>
<p align="left"><span style="color:#000000;">                            </span><span style="color:#6A3E3E;">outDirectory</span><span style="color:#000000;">.mkdir();</span></p>
<p align="left"><span style="color:#000000;">                   </span>}</p>
<p align="left"><span style="color:#000000;">                   </span>AESDecrypter <span style="color:#6A3E3E;">
decrypter</span><span style="color:#000000;"> = </span><strong><span style="color:#7F0055;">new</span></strong><span style="color:#000000;"> AESDecrypterBC();</span></p>
<p align="left"><span style="color:#000000;">                   </span>AesZipFileDecrypter<span style="color:#6A3E3E;">zipDecrypter</span><span style="color:#000000;"> =
</span><strong><span style="color:#7F0055;">null</span></strong><span style="color:#000000;">;</span></p>
<p align="left"><span style="color:#000000;">                   </span><strong><span style="color:#7F0055;">try</span></strong><span style="color:#000000;"> {</span></p>
<p align="left"><span style="color:#000000;">                            </span><span style="color:#6A3E3E;">zipDecrypter</span><span style="color:#000000;"> =</span><strong><span style="color:#7F0055;">new</span></strong><span style="color:#000000;"> AesZipFileDecrypter(</span><strong><span style="color:#7F0055;">new</span></strong><span style="color:#000000;">
 File(</span><span style="color:#6A3E3E;">inFile</span><span style="color:#000000;">),</span><span style="color:#6A3E3E;">decrypter</span><span style="color:#000000;">);</span></p>
<p align="left"><span style="color:#000000;">                            </span>AesZipFileDecrypter.<em><span style="color:#0000C0;">charset</span></em><span style="color:#000000;"> =</span><span style="color:#2A00FF;">"utf-8"</span><span style="color:#000000;">;</span></p>
<p align="left"><span style="color:#000000;">                            </span><span style="color:#3F5FBF;">/**</span></p>
<p align="left"><span style="color:#3F5FBF;">                            </span> *<span style="color:#3F5FBF;">得到</span><span style="color:#3F5FBF;">ZIP</span><span style="color:#3F5FBF;">文件中所有</span><span style="color:#3F5FBF;">Entry,</span><span style="color:#3F5FBF;">但此处好像与</span><span style="color:#3F5FBF;">JDK</span><span style="color:#3F5FBF;">里不同</span><span style="color:#3F5FBF;">,</span><span style="color:#3F5FBF;">目录不视为</span><span style="color:#3F5FBF;">Entry</span></p>
<p align="left"><span style="color:#3F5FBF;">                            </span> *<span style="color:#3F5FBF;">需要创建文件夹</span><span style="color:#3F5FBF;">,entry.isDirectory()</span><span style="color:#3F5FBF;">方法同样不适用</span><span style="color:#3F5FBF;">,</span><span style="color:#3F5FBF;">不知道是不是自己使用错误</span></p>
<p align="left"><span style="color:#3F5FBF;">                            </span> *<span style="color:#3F5FBF;">处理文件夹问题处理可能不太好</span></p>
<p align="left"><span style="color:#3F5FBF;">                            </span> */</p>
<p align="left"><span style="color:#000000;">                            </span>List&lt;ExtZipEntry&gt;<span style="color:#6A3E3E;">entryList</span><span style="color:#000000;"> =
</span><span style="color:#6A3E3E;">zipDecrypter</span><span style="color:#000000;">.getEntryList();</span></p>
<p align="left"><span style="color:#000000;">                            </span><strong><span style="color:#7F0055;">for</span></strong><span style="color:#000000;">(ExtZipEntry</span><span style="color:#6A3E3E;">entry</span><span style="color:#000000;"> :
</span><span style="color:#6A3E3E;">entryList</span><span style="color:#000000;">) {</span></p>
<p align="left"><span style="color:#000000;">                                     </span>
String <span style="color:#6A3E3E;">eName</span><span style="color:#000000;"> = </span>
<span style="color:#6A3E3E;">entry</span><span style="color:#000000;">.getName();</span></p>
<p align="left"><span style="color:#000000;">                                     </span>
String <span style="color:#6A3E3E;">dir</span><span style="color:#000000;"> = </span>
<span style="color:#6A3E3E;">eName</span><span style="color:#000000;">.substring(0, </span>
<span style="color:#6A3E3E;">eName</span><span style="color:#000000;">.lastIndexOf(File.</span><strong><em><span style="color:#0000C0;">separator</span></em></strong><span style="color:#000000;">) + 1);</span></p>
<p align="left"><span style="color:#000000;">                                     </span>
File <span style="color:#6A3E3E;">extractDir</span><span style="color:#000000;"> = </span>
<strong><span style="color:#7F0055;">new</span></strong><span style="color:#000000;"> File(</span><span style="color:#6A3E3E;">outDir</span><span style="color:#000000;">,</span><span style="color:#6A3E3E;">dir</span><span style="color:#000000;">);</span></p>
<p align="left"><span style="color:#000000;">                                     </span>
<strong><span style="color:#7F0055;">if</span></strong><span style="color:#000000;"> (!</span><span style="color:#6A3E3E;">extractDir</span><span style="color:#000000;">.exists()) {</span></p>
<p align="left"><span style="color:#000000;">                                              </span>FileUtils.<em>forceMkdir</em>(<span style="color:#6A3E3E;">extractDir</span><span style="color:#000000;">);</span></p>
<p align="left"><span style="color:#000000;">                                     </span>
}</p>
<p align="left"><span style="color:#000000;">                                     </span>
<span style="color:#3F5FBF;">/**</span></p>
<p align="left"><span style="color:#3F5FBF;">                                     </span>
 * <span style="color:#3F5FBF;">抽出文件</span></p>
<p align="left"><span style="color:#3F5FBF;">                   </span>                    */</p>
<p align="left"><span style="color:#000000;">                                     </span>
File <span style="color:#6A3E3E;">extractFile</span><span style="color:#000000;"> = </span>
<strong><span style="color:#7F0055;">new</span></strong><span style="color:#000000;"> File(</span><span style="color:#6A3E3E;">outDir</span><span style="color:#000000;"> + File.</span><strong><em><span style="color:#0000C0;">separator</span></em></strong><span style="color:#000000;">
 + </span><span style="color:#6A3E3E;">eName</span><span style="color:#000000;">);</span></p>
<p align="left"><span style="color:#000000;">                                     </span>
<span style="color:#6A3E3E;">zipDecrypter</span><span style="color:#000000;">.extractEntry(</span><span style="color:#6A3E3E;">entry</span><span style="color:#000000;">,</span><span style="color:#6A3E3E;">extractFile</span><span style="color:#000000;">,
</span><span style="color:#6A3E3E;">passwd</span><span style="color:#000000;">);</span></p>
<p align="left"><span style="color:#000000;">                            </span>}</p>
<p align="left"><span style="color:#000000;">                            </span><strong><span style="color:#FF0000;background:#FFFF00;">return</span><span style="color:#FF0000;background:#FFFF00;"> 1;</span></strong></p>
<p align="left"><span style="color:#000000;">                   </span>} <strong><span style="color:#7F0055;">catch</span></strong><span style="color:#000000;"> (DataFormatException</span><span style="color:#6A3E3E;">e</span><span style="color:#000000;">) {</span></p>
<p align="left"><span style="color:#000000;">                            </span><span style="color:#6A3E3E;">e</span><span style="color:#000000;">.printStackTrace();</span></p>
<p align="left"><span style="color:#000000;">                   </span>}<strong><span style="color:#7F0055;">catch</span></strong><span style="color:#000000;"> (ZipException</span><span style="color:#6A3E3E;">e</span><span style="color:#000000;">){</span></p>
<p><span style="color:#000000;">                     </span><strong><span style="color:#FF0000;background:#FFFF00;">return 0;</span></strong></p>
<p align="left"><span style="color:#000000;">                   </span>} <strong><span style="color:#7F0055;">catch</span></strong><span style="color:#000000;"> (IOException</span><span style="color:#6A3E3E;">e</span><span style="color:#000000;">) {</span></p>
<p align="left"><span style="color:#000000;">                            </span><span style="color:#6A3E3E;">e</span><span style="color:#000000;">.printStackTrace();</span></p>
<p align="left"><span style="color:#000000;">                   </span>} <strong><span style="color:#7F0055;">finally</span></strong><span style="color:#000000;"> {</span></p>
<p align="left"><span style="color:#000000;">                            </span><strong><span style="color:#7F0055;">try</span></strong><span style="color:#000000;"> {</span></p>
<p align="left"><span style="color:#000000;">                                     </span>
<span style="color:#6A3E3E;">zipDecrypter</span><span style="color:#000000;">.close();</span></p>
<p align="left"><span style="color:#000000;">                            </span>} <strong>
<span style="color:#7F0055;">catch</span></strong><span style="color:#000000;"> (IOException</span><span style="color:#6A3E3E;">e</span><span style="color:#000000;">) {</span></p>
<p align="left"><span style="color:#000000;">                                     </span>
<span style="color:#6A3E3E;">e</span><span style="color:#000000;">.printStackTrace();</span></p>
<p align="left"><span style="color:#000000;">                            </span>}</p>
<p align="left"><span style="color:#000000;">                   </span>}</p>
<p align="left"><span style="color:#000000;">                   </span><strong><span style="color:#FF0000;background:#FFFF00;">return</span><span style="color:#FF0000;background:#FFFF00;"> -1;</span></strong></p>
<p align="left"><span style="color:#000000;">         </span>}</p>
<p align="left"> </p>
<p align="left"><span style="color:#000000;">         </span><span style="color:#3F5FBF;">/**</span></p>
<p align="left"><span style="color:#3F5FBF;">         </span> * <span style="color:#3F5FBF;">
测试</span></p>
<p align="left"><span style="color:#3F5FBF;">         </span> * <strong><span style="color:#7F9FBF;">@param</span></strong><span style="color:#3F5FBF;"> args</span></p>
<p align="left"><span style="color:#3F5FBF;">         </span> */</p>
<p align="left"><span style="color:#000000;">         </span><strong><span style="color:#7F0055;">public</span><span style="color:#7F0055;">static</span><span style="color:#7F0055;">void</span></strong><span style="color:#000000;"> main(String[]</span><span style="color:#6A3E3E;">args</span><span style="color:#000000;">)
 {</span></p>
<p align="left"><span style="color:#000000;">                   </span><span style="color:#3F5FBF;">/**</span></p>
<p align="left"><span style="color:#3F5FBF;">                   </span> * <span style="color:#3F5FBF;">
压缩测试</span></p>
<p align="left"><span style="color:#3F5FBF;">                   </span> * <span style="color:#3F5FBF;">
可以传文件或者目录</span></p>
<p align="left"><span style="color:#3F5FBF;">                   </span> */</p>
<p align="left"><span style="color:#3F7F5F;">//                </span><u>zip</u>("M:\\ZIP\\test\\<u>bb</u>\\a\\t.txt", "M:\\ZIP\\test\\temp1.<u>zip</u>", "<u>zyh</u>");</p>
<p align="left"><span style="color:#3F7F5F;">//                </span><u>zip</u>("M:\\ZIP\\test\\<u>bb</u>", "M:\\ZIP\\test\\temp2.<u>zip</u>", "<u>zyh</u>");</p>
<p align="left"><span style="color:#000000;">                   </span><span style="color:#3F7F5F;">//<u>zip</u>("/home/<u>hadoop</u>/abc.txt","/home/<u>hadoop</u>/test.zip","123456");</span></p>
<p align="left"><span style="color:#000000;">                   </span>System.<strong><em><span style="color:#0000C0;">out</span></em></strong><span style="color:#000000;">.println(<em>unzip</em>(</span><span style="color:#2A00FF;">"/home/hadoop/test.zip"</span><span style="color:#000000;">,</span><span style="color:#2A00FF;">"/home/hadoop/output"</span><span style="color:#000000;">,</span><span style="color:#2A00FF;">"123456"</span><span style="color:#000000;">));</span></p>
<p align="left"><span style="color:#000000;">                   </span><span style="color:#3F7F5F;">//<u>unzip</u>("M:\\ZIP\\test\\temp2.<u>zip</u>", "M:\\ZIP\\test\\<u>temp</u>", "<u>zyh</u>");</span></p>
<p align="left"><span style="color:#000000;">         </span>}</p>
<p align="left"><span style="color:#000000;">}</span></p>
<p> </p>
</td>
</tr></tbody></table><p>其中红字加粗为对其改动，可以捕获密码错误异常来判断密码是否正确。</p>
<p> </p>
<table border="1" cellpadding="0" cellspacing="0"><tbody><tr><td valign="top">
<p>Mapper中对于DecryptionZipUtil类中unzip方法的使用：</p>
</td>
</tr><tr><td valign="top">
<p><strong><span style="color:#7F0055;">if (DecryptionZipUtil.unzip("/home/hadoop/test.zip", "/home/hadoop/output", value.toString()) == 1) {</span></strong></p>
</td>
</tr></tbody></table><p> </p>
<h4>Hadoop自定义InputFormat</h4>
<table border="1" cellpadding="0" cellspacing="0"><tbody><tr><td valign="top">
<p><span style="color:#000000;">CustomInputFormat</span><span style="color:#000000;">类，继承</span><span style="color:#000000;">FileInputFormat</span></p>
</td>
</tr><tr><td valign="top">
<p align="left"><strong><span style="color:#7F0055;">package</span></strong><span style="color:#000000;"> com.fieldsoft.GetZipKey.inputformat;</span></p>
<p align="left"> </p>
<p align="left"><strong><span style="color:#7F0055;">import</span></strong><span style="color:#000000;"> java.io.IOException; </span></p>
<p align="left"><strong><span style="color:#7F0055;">import</span></strong><span style="color:#000000;"> org.apache.hadoop.io.Text; </span></p>
<p align="left"><strong><span style="color:#7F0055;">import</span></strong><span style="color:#000000;"> org.apache.hadoop.mapreduce.InputSplit; </span></p>
<p align="left"><strong><span style="color:#7F0055;">import</span></strong><span style="color:#000000;"> org.apache.hadoop.mapreduce.RecordReader; </span></p>
<p align="left"><strong><span style="color:#7F0055;">import</span></strong><span style="color:#000000;"> org.apache.hadoop.mapreduce.TaskAttemptContext; </span></p>
<p align="left"><strong><span style="color:#7F0055;">import</span></strong><span style="color:#000000;"> org.apache.hadoop.mapreduce.lib.input.FileInputFormat; </span></p>
<p align="left"> </p>
<p align="left"><strong><span style="color:#7F0055;">public</span><span style="color:#7F0055;">class</span></strong><span style="color:#000000;"> CustomInputFormat</span><strong><span style="color:#7F0055;">extends</span></strong><span style="color:#000000;"> FileInputFormat&lt;Text,
 Text&gt; { </span></p>
<p align="left"><span style="color:#000000;">         </span>  </p>
<p align="left"><span style="color:#000000;">    </span><span style="color:#646464;">@Override</span><span style="color:#000000;"> </span></p>
<p align="left"><span style="color:#000000;">    </span><strong><span style="color:#7F0055;">public</span></strong><span style="color:#000000;"> RecordReader&lt;Text, Text&gt; createRecordReader(InputSplit</span><span style="color:#6A3E3E;">split</span><span style="color:#000000;">, 
</span></p>
<p align="left"><span style="color:#000000;">            </span>TaskAttemptContext <span style="color:#6A3E3E;">
context</span><span style="color:#000000;">) </span><strong><span style="color:#7F0055;">throws</span></strong><span style="color:#000000;"> IOException, </span></p>
<p align="left"><span style="color:#000000;">            </span>InterruptedException { </p>
<p align="left"><span style="color:#000000;">        </span><span style="color:#3F7F5F;">//</span><strong><span style="color:#7F9FBF;">TODO</span></strong><span style="color:#3F7F5F;"> Auto-generated method stub </span></p>
<p align="left"><span style="color:#000000;">        </span><strong><span style="color:#7F0055;">return</span><span style="color:#7F0055;">new</span></strong><span style="color:#000000;"> CustomReader(); </span></p>
<p align="left"><span style="color:#000000;">    </span>}  </p>
<p align="left"><span style="color:#000000;">}  </span></p>
<p> </p>
</td>
</tr></tbody></table><p> </p>
<table border="1" cellpadding="0" cellspacing="0"><tbody><tr><td valign="top">
<p><span style="color:#000000;">CustomReader</span><span style="color:#000000;">类，继承</span><span style="color:#000000;">RecordReader</span></p>
</td>
</tr><tr><td valign="top">
<p align="left"><strong><span style="color:#7F0055;">package</span></strong><span style="color:#000000;"> com.fieldsoft.GetZipKey.inputformat;</span></p>
<p align="left"> </p>
<p align="left"><strong><span style="color:#7F0055;">import</span></strong><span style="color:#000000;"> java.io.IOException;</span></p>
<p align="left"><strong><span style="color:#7F0055;">+</span></strong></p>
<p align="left"> </p>
<p align="left"><strong><span style="color:#7F0055;">public</span><span style="color:#7F0055;">class</span></strong><span style="color:#000000;"> CustomReader</span><strong><span style="color:#7F0055;">extends</span></strong><span style="color:#000000;"> RecordReader&lt;Text,
 Text&gt; {</span></p>
<p align="left"><span style="color:#000000;">         </span><strong><span style="color:#7F0055;">private</span><span style="color:#7F0055;">int</span></strong><span style="color:#0000C0;">PassWordLengthMin</span><span style="color:#000000;">;</span></p>
<p align="left"><span style="color:#000000;">         </span><strong><span style="color:#7F0055;">private</span><span style="color:#7F0055;">int</span></strong><span style="color:#0000C0;">PassWordLengthMax</span><span style="color:#000000;">;</span></p>
<p align="left"><span style="color:#000000;">         </span><strong><span style="color:#7F0055;">private</span><span style="color:#7F0055;">int</span></strong><span style="color:#0000C0;">PassWordLength</span><span style="color:#000000;">;</span></p>
<p align="left"><span style="color:#000000;">         </span><strong><span style="color:#7F0055;">private</span><span style="color:#7F0055;">boolean</span></strong><span style="color:#0000C0;">EnableLowerLetter</span><span style="color:#000000;">;</span></p>
<p align="left"><span style="color:#000000;">         </span><strong><span style="color:#7F0055;">private</span><span style="color:#7F0055;">boolean</span></strong><span style="color:#0000C0;">EnableUpperLetter</span><span style="color:#000000;">;</span></p>
<p align="left"><span style="color:#000000;">         </span><strong><span style="color:#7F0055;">private</span><span style="color:#7F0055;">boolean</span></strong><span style="color:#0000C0;">EnableNum</span><span style="color:#000000;">;</span></p>
<p align="left"><span style="color:#000000;">         </span><strong><span style="color:#7F0055;">private</span></strong><span style="color:#000000;"> String</span><span style="color:#0000C0;">PassWordCurrent</span><span style="color:#000000;">;</span></p>
<p align="left"><span style="color:#000000;">         </span><strong><span style="color:#7F0055;">private</span></strong><span style="color:#000000;"> ArrayList&lt;String&gt;</span><span style="color:#0000C0;">CharPool</span><span style="color:#000000;"> =
</span><strong><span style="color:#7F0055;">new</span></strong><span style="color:#000000;"> ArrayList&lt;String&gt;();</span></p>
<p align="left"><span style="color:#000000;">         </span><strong><span style="color:#7F0055;">private</span><span style="color:#7F0055;">long</span></strong><span style="color:#0000C0;">PassWordTotal</span><span style="color:#000000;">;</span></p>
<p align="left"> </p>
<p align="left"><span style="color:#000000;">         </span><span style="color:#646464;">@Override</span></p>
<p align="left"><span style="color:#000000;">         </span><strong><span style="color:#7F0055;">public</span><span style="color:#7F0055;">void</span></strong><span style="color:#000000;"> initialize(InputSplit</span><span style="color:#6A3E3E;">inputSplit</span><span style="color:#000000;">,
 TaskAttemptContext</span><span style="color:#6A3E3E;">cxt</span><span style="color:#000000;">)
</span><strong><span style="color:#7F0055;">throws</span></strong><span style="color:#000000;"> IOException, InterruptedException {</span></p>
<p align="left"><span style="color:#000000;">                   </span><strong><span style="color:#7F0055;">this</span></strong><span style="color:#000000;">.</span><span style="color:#0000C0;">PassWordLengthMin</span><span style="color:#000000;"> = GetZipKeySystemValue.</span><em><span style="color:#0000C0;">PassWordLengthMin</span></em><span style="color:#000000;">;</span></p>
<p align="left"><span style="color:#000000;">                   </span><strong><span style="color:#7F0055;">this</span></strong><span style="color:#000000;">.</span><span style="color:#0000C0;">PassWordLengthMax</span><span style="color:#000000;"> = GetZipKeySystemValue.</span><em><span style="color:#0000C0;">PassWordLengthMax</span></em><span style="color:#000000;">;</span></p>
<p align="left"><span style="color:#000000;">                   </span><strong><span style="color:#7F0055;">this</span></strong><span style="color:#000000;">.</span><span style="color:#0000C0;">EnableLowerLetter</span><span style="color:#000000;"> = GetZipKeySystemValue.</span><em><span style="color:#0000C0;">EnableLowerLetter</span></em><span style="color:#000000;">;</span></p>
<p align="left"><span style="color:#000000;">                   </span><strong><span style="color:#7F0055;">this</span></strong><span style="color:#000000;">.</span><span style="color:#0000C0;">EnableUpperLetter</span><span style="color:#000000;"> = GetZipKeySystemValue.</span><em><span style="color:#0000C0;">EnableUpperLetter</span></em><span style="color:#000000;">;</span></p>
<p align="left"><span style="color:#000000;">                   </span><strong><span style="color:#7F0055;">this</span></strong><span style="color:#000000;">.</span><span style="color:#0000C0;">EnableNum</span><span style="color:#000000;"> = GetZipKeySystemValue.</span><em><span style="color:#0000C0;">EnableNum</span></em><span style="color:#000000;">;</span></p>
<p align="left"><span style="color:#000000;">                   </span><span style="color:#3F7F5F;">//<u>Init</u> CharPool</span></p>
<p align="left"><span style="color:#000000;">                   </span><strong><span style="color:#7F0055;">if</span></strong><span style="color:#000000;"> (</span><span style="color:#0000C0;">EnableNum</span><span style="color:#000000;">) {</span></p>
<p align="left"><span style="color:#000000;">                            </span><strong><span style="color:#7F0055;">for</span></strong><span style="color:#000000;"> (</span><strong><span style="color:#7F0055;">int</span></strong><span style="color:#6A3E3E;">i</span><span style="color:#000000;">
 = 0; </span><span style="color:#6A3E3E;">i</span><span style="color:#000000;"> &lt; 10;</span><span style="color:#6A3E3E;">i</span><span style="color:#000000;">++) {</span></p>
<p align="left"><span style="color:#000000;">                                     </span>
StringBuffer <span style="color:#6A3E3E;">s</span><span style="color:#000000;"> = </span>
<strong><span style="color:#7F0055;">new</span></strong><span style="color:#000000;"> StringBuffer();</span></p>
<p align="left"><span style="color:#000000;">                                     </span>
<span style="color:#6A3E3E;">s</span><span style="color:#000000;">.append((</span><strong><span style="color:#7F0055;">char</span></strong><span style="color:#000000;">) (</span><span style="color:#2A00FF;">'0'</span><span style="color:#000000;"> +</span><span style="color:#6A3E3E;">i</span><span style="color:#000000;">));</span></p>
<p align="left"><span style="color:#000000;">                                     </span>
<span style="color:#0000C0;">CharPool</span><span style="color:#000000;">.add(</span><span style="color:#6A3E3E;">s</span><span style="color:#000000;">.toString());</span></p>
<p align="left"><span style="color:#000000;">                            </span>}</p>
<p align="left"><span style="color:#000000;">                   </span>}</p>
<p align="left"><span style="color:#000000;">                   </span><strong><span style="color:#7F0055;">if</span></strong><span style="color:#000000;"> (</span><span style="color:#0000C0;">EnableUpperLetter</span><span style="color:#000000;">) {</span></p>
<p align="left"><span style="color:#000000;">                            </span><strong><span style="color:#7F0055;">for</span></strong><span style="color:#000000;"> (</span><strong><span style="color:#7F0055;">int</span></strong><span style="color:#6A3E3E;">i</span><span style="color:#000000;">
 = 0; </span><span style="color:#6A3E3E;">i</span><span style="color:#000000;"> &lt; 26;</span><span style="color:#6A3E3E;">i</span><span style="color:#000000;">++) {</span></p>
<p align="left"><span style="color:#000000;">                                     </span>
StringBuffer <span style="color:#6A3E3E;">s</span><span style="color:#000000;"> = </span>
<strong><span style="color:#7F0055;">new</span></strong><span style="color:#000000;"> StringBuffer();</span></p>
<p align="left"><span style="color:#000000;">                                     </span>
<span style="color:#6A3E3E;">s</span><span style="color:#000000;">.append((</span><strong><span style="color:#7F0055;">char</span></strong><span style="color:#000000;">) (</span><span style="color:#2A00FF;">'A'</span><span style="color:#000000;"> +</span><span style="color:#6A3E3E;">i</span><span style="color:#000000;">));</span></p>
<p align="left"><span style="color:#000000;">                                     </span>
<span style="color:#0000C0;">CharPool</span><span style="color:#000000;">.add(</span><span style="color:#6A3E3E;">s</span><span style="color:#000000;">.toString());</span></p>
<p align="left"><span style="color:#000000;">                            </span>}</p>
<p align="left"><span style="color:#000000;">                   </span>}</p>
<p align="left"><span style="color:#000000;">                   </span><strong><span style="color:#7F0055;">if</span></strong><span style="color:#000000;"> (</span><span style="color:#0000C0;">EnableLowerLetter</span><span style="color:#000000;">) {</span></p>
<p align="left"><span style="color:#000000;">                            </span><strong><span style="color:#7F0055;">for</span></strong><span style="color:#000000;"> (</span><strong><span style="color:#7F0055;">int</span></strong><span style="color:#6A3E3E;">i</span><span style="color:#000000;">
 = 0; </span><span style="color:#6A3E3E;">i</span><span style="color:#000000;"> &lt; 26;</span><span style="color:#6A3E3E;">i</span><span style="color:#000000;">++) {</span></p>
<p align="left"><span style="color:#000000;">                                     </span>
StringBuffer <span style="color:#6A3E3E;">s</span><span style="color:#000000;"> = </span>
<strong><span style="color:#7F0055;">new</span></strong><span style="color:#000000;"> StringBuffer();</span></p>
<p align="left"><span style="color:#000000;">                                     </span>
<span style="color:#6A3E3E;">s</span><span style="color:#000000;">.append((</span><strong><span style="color:#7F0055;">char</span></strong><span style="color:#000000;">) (</span><span style="color:#2A00FF;">'a'</span><span style="color:#000000;"> +</span><span style="color:#6A3E3E;">i</span><span style="color:#000000;">));</span></p>
<p align="left"><span style="color:#000000;">                                     </span>
<span style="color:#0000C0;">CharPool</span><span style="color:#000000;">.add(</span><span style="color:#6A3E3E;">s</span><span style="color:#000000;">.toString());</span></p>
<p align="left"><span style="color:#000000;">                            </span>}</p>
<p align="left"><span style="color:#000000;">                   </span>}</p>
<p align="left"><span style="color:#000000;">                   </span><span style="color:#3F7F5F;">//</span></p>
<p align="left"><span style="color:#000000;">                   </span><span style="color:#0000C0;">PassWordLength</span><span style="color:#000000;"> =</span><span style="color:#0000C0;">PassWordLengthMin</span><span style="color:#000000;">;</span></p>
<p align="left"><span style="color:#000000;">                   </span><strong><span style="color:#7F0055;">this</span></strong><span style="color:#000000;">.</span><span style="color:#0000C0;">PassWordCurrent</span><span style="color:#000000;"> = GetInitPassWord(</span><span style="color:#0000C0;">PassWordLength</span><span style="color:#000000;">);</span></p>
<p align="left"><span style="color:#000000;">                   </span><span style="color:#3F7F5F;">//<u>Cal</u> PassWordTotal</span></p>
<p align="left"><span style="color:#000000;">                   </span><strong><span style="color:#7F0055;">this</span></strong><span style="color:#000000;">.</span><span style="color:#0000C0;">PassWordTotal</span><span style="color:#000000;"> = 0;</span></p>
<p align="left"><span style="color:#000000;">                   </span><strong><span style="color:#7F0055;">for</span></strong><span style="color:#000000;"> (</span><strong><span style="color:#7F0055;">int</span></strong><span style="color:#6A3E3E;">i</span><span style="color:#000000;">
 = </span><strong><span style="color:#7F0055;">this</span></strong><span style="color:#000000;">.</span><span style="color:#0000C0;">PassWordLengthMin</span><span style="color:#000000;">;</span><span style="color:#6A3E3E;">i</span><span style="color:#000000;"> &lt;=</span><strong><span style="color:#7F0055;">this</span></strong><span style="color:#000000;">.</span><span style="color:#0000C0;">PassWordLengthMax</span><span style="color:#000000;">;</span><span style="color:#6A3E3E;">i</span><span style="color:#000000;">
 ++){</span></p>
<p align="left"><span style="color:#000000;">                            </span><strong><span style="color:#7F0055;">this</span></strong><span style="color:#000000;">.</span><span style="color:#0000C0;">PassWordTotal</span><span style="color:#000000;"> += Math.<em>pow</em>(</span><strong><span style="color:#7F0055;">this</span></strong><span style="color:#000000;">.</span><span style="color:#0000C0;">CharPool</span><span style="color:#000000;">.size(),</span><span style="color:#6A3E3E;">i</span><span style="color:#000000;">);</span></p>
<p align="left"><span style="color:#000000;">                   </span>}</p>
<p align="left"><span style="color:#000000;">                   </span>System.<strong><em><span style="color:#0000C0;">out</span></em></strong><span style="color:#000000;">.print(</span><strong><span style="color:#7F0055;">this</span></strong><span style="color:#000000;">.</span><span style="color:#0000C0;">PassWordTotal</span><span style="color:#000000;">);</span></p>
<p align="left"><span style="color:#000000;">         </span>}</p>
<p align="left"> </p>
<p align="left"><span style="color:#000000;">         </span><strong><span style="color:#7F0055;">private</span></strong><span style="color:#000000;"> String GetInitPassWord(</span><strong><span style="color:#7F0055;">int</span></strong><span style="color:#6A3E3E;">CurrLength</span><span style="color:#000000;">)
 {</span></p>
<p align="left"><span style="color:#000000;">                   </span><strong><span style="color:#7F0055;">char</span></strong><span style="color:#000000;">[]</span><span style="color:#6A3E3E;">TempCharArray</span><span style="color:#000000;"> =</span><strong><span style="color:#7F0055;">new</span><span style="color:#7F0055;">char</span></strong><span style="color:#000000;">[</span><span style="color:#6A3E3E;">CurrLength</span><span style="color:#000000;">];</span></p>
<p align="left"><span style="color:#000000;">                   </span><strong><span style="color:#7F0055;">for</span></strong><span style="color:#000000;"> (</span><strong><span style="color:#7F0055;">int</span></strong><span style="color:#6A3E3E;">i</span><span style="color:#000000;">
 = 0; </span><span style="color:#6A3E3E;">i</span><span style="color:#000000;"> &lt; </span>
<span style="color:#6A3E3E;">CurrLength</span><span style="color:#000000;">; </span><span style="color:#6A3E3E;">i</span><span style="color:#000000;">++) {</span></p>
<p align="left"><span style="color:#000000;">                            </span><span style="color:#6A3E3E;">TempCharArray</span><span style="color:#000000;">[</span><span style="color:#6A3E3E;">i</span><span style="color:#000000;">] =</span><span style="color:#0000C0;">CharPool</span><span style="color:#000000;">.get(0).charAt(0);</span></p>
<p align="left"><span style="color:#000000;">                   </span>}</p>
<p align="left"><span style="color:#000000;">                   </span><strong><span style="color:#7F0055;">return</span><span style="color:#7F0055;">new</span></strong><span style="color:#000000;"> String(</span><span style="color:#6A3E3E;">TempCharArray</span><span style="color:#000000;">);</span></p>
<p align="left"><span style="color:#000000;">         </span>}</p>
<p align="left"> </p>
<p align="left"><span style="color:#000000;">         </span><strong><span style="color:#7F0055;">private</span></strong><span style="color:#000000;"> String PassWordAdd() {</span></p>
<p align="left"><span style="color:#000000;">                   </span>String <span style="color:#6A3E3E;">
DesString</span><span style="color:#000000;"> = </span><strong><span style="color:#7F0055;">new</span></strong><span style="color:#000000;"> String(</span><strong><span style="color:#7F0055;">this</span></strong><span style="color:#000000;">.</span><span style="color:#0000C0;">PassWordCurrent</span><span style="color:#000000;">);</span></p>
<p align="left"><span style="color:#000000;">                   </span><strong><span style="color:#7F0055;">char</span></strong><span style="color:#000000;">[]</span><span style="color:#6A3E3E;">TempCharArray</span><span style="color:#000000;"> =</span><span style="color:#6A3E3E;">DesString</span><span style="color:#000000;">.toCharArray();</span></p>
<p align="left"><span style="color:#000000;">                   </span><strong><span style="color:#7F0055;">int</span></strong><span style="color:#6A3E3E;">c</span><span style="color:#000000;"> = 1;</span></p>
<p align="left"><span style="color:#000000;">                   </span><strong><span style="color:#7F0055;">for</span></strong><span style="color:#000000;"> (</span><strong><span style="color:#7F0055;">int</span></strong><span style="color:#6A3E3E;">i</span><span style="color:#000000;">
 = </span><span style="color:#6A3E3E;">DesString</span><span style="color:#000000;">.length() - 1;</span><span style="color:#6A3E3E;">i</span><span style="color:#000000;"> &gt;= 0 &amp;&amp;
</span><span style="color:#6A3E3E;">c</span><span style="color:#000000;"> &gt; 0; </span>
<span style="color:#6A3E3E;">i</span><span style="color:#000000;">--) {</span></p>
<p align="left"><span style="color:#000000;">                            </span><strong><span style="color:#7F0055;">int</span></strong><span style="color:#6A3E3E;">num</span><span style="color:#000000;"> = CharPoolFind(</span><span style="color:#6A3E3E;">TempCharArray</span><span style="color:#000000;">[</span><span style="color:#6A3E3E;">i</span><span style="color:#000000;">]);</span></p>
<p align="left"><span style="color:#000000;">                            </span><strong><span style="color:#7F0055;">if</span></strong><span style="color:#000000;"> (</span><span style="color:#6A3E3E;">num</span><span style="color:#000000;"> ==</span><span style="color:#0000C0;">CharPool</span><span style="color:#000000;">.size()
 - 1) {</span></p>
<p align="left"><span style="color:#000000;">                                     </span>
<span style="color:#6A3E3E;">TempCharArray</span><span style="color:#000000;">[</span><span style="color:#6A3E3E;">i</span><span style="color:#000000;">] =</span><span style="color:#0000C0;">CharPool</span><span style="color:#000000;">.get(0).charAt(0);</span></p>
<p align="left"><span style="color:#000000;">                                     </span>
<strong><span style="color:#7F0055;">continue</span></strong><span style="color:#000000;">;</span></p>
<p align="left"><span style="color:#000000;">                            </span>} <strong>
<span style="color:#7F0055;">else</span></strong><span style="color:#000000;"> {</span></p>
<p align="left"><span style="color:#000000;">                                     </span>
<span style="color:#6A3E3E;">TempCharArray</span><span style="color:#000000;">[</span><span style="color:#6A3E3E;">i</span><span style="color:#000000;">] =</span><span style="color:#0000C0;">CharPool</span><span style="color:#000000;">.get(</span><span style="color:#6A3E3E;">num</span><span style="color:#000000;">
 + 1).charAt(0);</span></p>
<p align="left"><span style="color:#000000;">                                     </span>
<span style="color:#6A3E3E;">c</span><span style="color:#000000;"> = 0;</span></p>
<p align="left"><span style="color:#000000;">                            </span>}</p>
<p align="left"><span style="color:#000000;">                   </span>}</p>
<p align="left"><span style="color:#000000;">                   </span><strong><span style="color:#7F0055;">if</span></strong><span style="color:#000000;"> (</span><span style="color:#6A3E3E;">c</span><span style="color:#000000;"> == 0) {</span></p>
<p align="left"><span style="color:#000000;">                            </span><span style="color:#6A3E3E;">DesString</span><span style="color:#000000;"> =</span><strong><span style="color:#7F0055;">new</span></strong><span style="color:#000000;"> String(</span><span style="color:#6A3E3E;">TempCharArray</span><span style="color:#000000;">);</span></p>
<p align="left"><span style="color:#000000;">                   </span>} <strong><span style="color:#7F0055;">else</span></strong><span style="color:#000000;"> {</span></p>
<p align="left"><span style="color:#000000;">                            </span><span style="color:#6A3E3E;">DesString</span><span style="color:#000000;"> =</span><strong><span style="color:#7F0055;">null</span></strong><span style="color:#000000;">;</span></p>
<p align="left"><span style="color:#000000;">                   </span>}</p>
<p align="left"><span style="color:#000000;">                   </span><strong><span style="color:#7F0055;">return</span></strong><span style="color:#6A3E3E;">DesString</span><span style="color:#000000;">;</span></p>
<p align="left"><span style="color:#000000;">         </span>}</p>
<p align="left"> </p>
<p align="left"><span style="color:#000000;">         </span><strong><span style="color:#7F0055;">private</span><span style="color:#7F0055;">int</span></strong><span style="color:#000000;"> CharPoolFind(</span><strong><span style="color:#7F0055;">char</span></strong><span style="color:#6A3E3E;">a</span><span style="color:#000000;">)
 {</span></p>
<p align="left"><span style="color:#000000;">                   </span><strong><span style="color:#7F0055;">for</span></strong><span style="color:#000000;"> (</span><strong><span style="color:#7F0055;">int</span></strong><span style="color:#6A3E3E;">i</span><span style="color:#000000;">
 = 0; </span><span style="color:#6A3E3E;">i</span><span style="color:#000000;"> &lt; </span>
<span style="color:#0000C0;">CharPool</span><span style="color:#000000;">.size(); </span>
<span style="color:#6A3E3E;">i</span><span style="color:#000000;">++) {</span></p>
<p align="left"><span style="color:#000000;">                            </span>StringBuffer<span style="color:#6A3E3E;">s</span><span style="color:#000000;"> =
</span><strong><span style="color:#7F0055;">new</span></strong><span style="color:#000000;"> StringBuffer();</span></p>
<p align="left"><span style="color:#000000;">                            </span><span style="color:#6A3E3E;">s</span><span style="color:#000000;">.append(</span><span style="color:#6A3E3E;">a</span><span style="color:#000000;">);</span></p>
<p align="left"><span style="color:#000000;">                            </span><strong><span style="color:#7F0055;">if</span></strong><span style="color:#000000;"> (</span><span style="color:#0000C0;">CharPool</span><span style="color:#000000;">.get(</span><span style="color:#6A3E3E;">i</span><span style="color:#000000;">).equals(</span><span style="color:#6A3E3E;">s</span><span style="color:#000000;">.toString()))
 {</span></p>
<p align="left"><span style="color:#000000;">                                     </span>
<strong><span style="color:#7F0055;">return</span></strong><span style="color:#6A3E3E;">i</span><span style="color:#000000;">;</span></p>
<p align="left"><span style="color:#000000;">                            </span>}</p>
<p align="left"><span style="color:#000000;">                   </span>}</p>
<p align="left"><span style="color:#000000;">                   </span><strong><span style="color:#7F0055;">return</span></strong><span style="color:#000000;"> -1;</span></p>
<p align="left"><span style="color:#000000;">         </span>}</p>
<p align="left"> </p>
<p align="left"><span style="color:#000000;">         </span><strong><span style="color:#7F0055;">public</span><span style="color:#7F0055;">void</span></strong><span style="color:#000000;"> test() {</span></p>
<p align="left"><span style="color:#000000;">                   </span><strong><span style="color:#7F0055;">try</span></strong><span style="color:#000000;"> {</span></p>
<p align="left"><span style="color:#000000;">                            </span><strong><span style="color:#7F0055;">this</span></strong><span style="color:#000000;">.initialize(</span><strong><span style="color:#7F0055;">null</span></strong><span style="color:#000000;">,</span><strong><span style="color:#7F0055;">null</span></strong><span style="color:#000000;">);</span></p>
<p align="left"><span style="color:#000000;">                   </span>} <strong><span style="color:#7F0055;">catch</span></strong><span style="color:#000000;"> (IOException | InterruptedException</span><span style="color:#6A3E3E;">e</span><span style="color:#000000;">)
 {</span></p>
<p align="left"><span style="color:#000000;">                            </span><span style="color:#3F7F5F;">//</span><strong><span style="color:#7F9FBF;">TODO</span></strong><span style="color:#3F7F5F;"> Auto-generated catch block</span></p>
<p align="left"><span style="color:#000000;">                            </span><span style="color:#6A3E3E;">e</span><span style="color:#000000;">.printStackTrace();</span></p>
<p align="left"><span style="color:#000000;">                   </span>}</p>
<p align="left"><span style="color:#000000;">         </span>}</p>
<p align="left"> </p>
<p align="left"><span style="color:#000000;">         </span><strong><span style="color:#7F0055;">public</span><span style="color:#7F0055;">static</span><span style="color:#7F0055;">void</span></strong><span style="color:#000000;"> main(String[]</span><span style="color:#6A3E3E;">args</span><span style="color:#000000;">)
 {</span></p>
<p align="left"><span style="color:#000000;">                   </span>CustomReader <u>
<span style="color:#6A3E3E;">a</span></u><span style="color:#000000;"> = </span><strong><span style="color:#7F0055;">new</span></strong><span style="color:#000000;"> CustomReader();</span></p>
<p align="left"><span style="color:#000000;">                   </span><span style="color:#6A3E3E;">a</span><span style="color:#000000;">.test();</span></p>
<p align="left"><span style="color:#000000;">         </span>}</p>
<p align="left"> </p>
<p align="left"><span style="color:#000000;">         </span><span style="color:#646464;">@Override</span></p>
<p align="left"><span style="color:#000000;">         </span><strong><span style="color:#7F0055;">public</span><span style="color:#7F0055;">boolean</span></strong><span style="color:#000000;"> nextKeyValue() {</span></p>
<p align="left"><span style="color:#000000;">                   </span><strong><span style="color:#7F0055;">if</span></strong><span style="color:#000000;"> (</span><strong><span style="color:#7F0055;">this</span></strong><span style="color:#000000;">.</span><span style="color:#0000C0;">PassWordLength</span><span style="color:#000000;">
 &gt; </span><strong><span style="color:#7F0055;">this</span></strong><span style="color:#000000;">.</span><span style="color:#0000C0;">PassWordLengthMax</span><span style="color:#000000;">) {</span></p>
<p align="left"><span style="color:#000000;">                            </span><strong><span style="color:#7F0055;">return</span><span style="color:#7F0055;">false</span></strong><span style="color:#000000;">;</span></p>
<p align="left"><span style="color:#000000;">                   </span>} <strong><span style="color:#7F0055;">else</span></strong><span style="color:#000000;"> {</span></p>
<p align="left"><span style="color:#000000;">                            </span>String<span style="color:#6A3E3E;">TempString</span><span style="color:#000000;"> = PassWordAdd();</span></p>
<p align="left"><span style="color:#000000;">                            </span><strong><span style="color:#7F0055;">if</span></strong><span style="color:#000000;"> (</span><span style="color:#6A3E3E;">TempString</span><span style="color:#000000;"> ==</span><strong><span style="color:#7F0055;">null</span></strong><span style="color:#000000;">)
 {</span></p>
<p align="left"><span style="color:#000000;">                                     </span>
<strong><span style="color:#7F0055;">this</span></strong><span style="color:#000000;">.</span><span style="color:#0000C0;">PassWordLength</span><span style="color:#000000;">++;</span></p>
<p align="left"><span style="color:#000000;">                                     </span>
<strong><span style="color:#7F0055;">if</span></strong><span style="color:#000000;"> (</span><strong><span style="color:#7F0055;">this</span></strong><span style="color:#000000;">.</span><span style="color:#0000C0;">PassWordLength</span><span style="color:#000000;"> &gt;</span><strong><span style="color:#7F0055;">this</span></strong><span style="color:#000000;">.</span><span style="color:#0000C0;">PassWordLengthMax</span><span style="color:#000000;">)
 {</span></p>
<p align="left"><span style="color:#000000;">                                              </span><strong><span style="color:#7F0055;">return</span><span style="color:#7F0055;">false</span></strong><span style="color:#000000;">;</span></p>
<p align="left"><span style="color:#000000;">                                     </span>
} <strong><span style="color:#7F0055;">else</span></strong><span style="color:#000000;"> {</span></p>
<p align="left"><span style="color:#000000;">                                              </span><strong><span style="color:#7F0055;">this</span></strong><span style="color:#000000;">.</span><span style="color:#0000C0;">PassWordCurrent</span><span style="color:#000000;">
 =</span><strong><span style="color:#7F0055;">this</span></strong><span style="color:#000000;">.GetInitPassWord(</span><span style="color:#0000C0;">PassWordLength</span><span style="color:#000000;">);</span></p>
<p align="left"><span style="color:#000000;">                                     </span>
}</p>
<p align="left"><span style="color:#000000;">                            </span>} <strong>
<span style="color:#7F0055;">else</span></strong><span style="color:#000000;"> {</span></p>
<p align="left"><span style="color:#000000;">                                     </span>
<strong><span style="color:#7F0055;">this</span></strong><span style="color:#000000;">.</span><span style="color:#0000C0;">PassWordCurrent</span><span style="color:#000000;"> =</span><span style="color:#6A3E3E;">TempString</span><span style="color:#000000;">;</span></p>
<p align="left"><span style="color:#000000;">                            </span>}</p>
<p align="left"><span style="color:#000000;">                   </span>}</p>
<p align="left"><span style="color:#000000;">                   </span><strong><span style="color:#7F0055;">return</span><span style="color:#7F0055;">true</span></strong><span style="color:#000000;">;</span></p>
<p align="left"><span style="color:#000000;">         </span>}</p>
<p align="left"> </p>
<p align="left"><span style="color:#000000;">         </span><span style="color:#646464;">@Override</span></p>
<p align="left"><span style="color:#000000;">         </span><strong><span style="color:#7F0055;">public</span></strong><span style="color:#000000;"> Text getCurrentKey() {</span></p>
<p align="left"><span style="color:#000000;">                   </span><strong><span style="color:#7F0055;">return</span><span style="color:#7F0055;">new</span></strong><span style="color:#000000;"> Text(</span><span style="color:#2A00FF;">"PassWord"</span><span style="color:#000000;">);</span></p>
<p align="left"><span style="color:#000000;">         </span>}</p>
<p align="left"> </p>
<p align="left"><span style="color:#000000;">         </span><span style="color:#646464;">@Override</span></p>
<p align="left"><span style="color:#000000;">         </span><strong><span style="color:#7F0055;">public</span></strong><span style="color:#000000;"> Text getCurrentValue() {</span></p>
<p align="left"><span style="color:#000000;">                   </span><strong><span style="color:#7F0055;">return</span><span style="color:#7F0055;">new</span></strong><span style="color:#000000;"> Text(</span><strong><span style="color:#7F0055;">this</span></strong><span style="color:#000000;">.</span><span style="color:#0000C0;">PassWordCurrent</span><span style="color:#000000;">);</span></p>
<p align="left"><span style="color:#000000;">         </span>}</p>
<p align="left"> </p>
<p align="left"><span style="color:#000000;">         </span><span style="color:#646464;">@Override</span></p>
<p align="left"><span style="color:#000000;">         </span><strong><span style="color:#7F0055;">public</span><span style="color:#7F0055;">float</span></strong><span style="color:#000000;"> getProgress() {</span></p>
<p align="left"><span style="color:#000000;">                   </span><strong><span style="color:#7F0055;">long</span></strong><span style="color:#6A3E3E;">CurrNum</span><span style="color:#000000;"> = 0;</span></p>
<p align="left"><span style="color:#000000;">                   </span><strong><span style="color:#7F0055;">for</span></strong><span style="color:#000000;"> (</span><strong><span style="color:#7F0055;">int</span></strong><span style="color:#6A3E3E;">i</span><span style="color:#000000;">
 = </span><strong><span style="color:#7F0055;">this</span></strong><span style="color:#000000;">.</span><span style="color:#0000C0;">PassWordLengthMin</span><span style="color:#000000;">;</span><span style="color:#6A3E3E;">i</span><span style="color:#000000;"> &lt;=</span><strong><span style="color:#7F0055;">this</span></strong><span style="color:#000000;">.</span><span style="color:#0000C0;">PassWordLength</span><span style="color:#000000;">;</span><span style="color:#6A3E3E;">i</span><span style="color:#000000;">
 ++){</span></p>
<p align="left"><span style="color:#000000;">                            </span><span style="color:#6A3E3E;">CurrNum</span><span style="color:#000000;"> += Math.<em>pow</em>(</span><strong><span style="color:#7F0055;">this</span></strong><span style="color:#000000;">.</span><span style="color:#0000C0;">CharPool</span><span style="color:#000000;">.size(),</span><span style="color:#6A3E3E;">i</span><span style="color:#000000;">);</span></p>
<p align="left"><span style="color:#000000;">                   </span>}</p>
<p align="left"><span style="color:#000000;">                   </span><strong><span style="color:#7F0055;">return</span></strong><span style="color:#000000;"> Math.<em>min</em>(1.0f, (</span><strong><span style="color:#7F0055;">float</span></strong><span style="color:#000000;">)</span><span style="color:#6A3E3E;">CurrNum</span><span style="color:#000000;">
 / (</span><strong><span style="color:#7F0055;">float</span></strong><span style="color:#000000;">)</span><strong><span style="color:#7F0055;">this</span></strong><span style="color:#000000;">.</span><span style="color:#0000C0;">PassWordTotal</span><span style="color:#000000;">);</span></p>
<p align="left"><span style="color:#000000;">         </span>}</p>
<p align="left"> </p>
<p align="left"><span style="color:#000000;">         </span><span style="color:#646464;">@Override</span></p>
<p align="left"><span style="color:#000000;">         </span><strong><span style="color:#7F0055;">public</span><span style="color:#7F0055;">void</span></strong><span style="color:#000000;"> close()</span><strong><span style="color:#7F0055;">throws</span></strong><span style="color:#000000;">
 IOException {</span></p>
<p align="left"><span style="color:#000000;">         </span>}</p>
<p align="left"> </p>
<p align="left"><span style="color:#000000;">}</span></p>
<p> </p>
</td>
</tr></tbody></table><p> </p>
<p><span style="color:#000000;">CustomReader</span><span style="color:#000000;">类，需要继承的方法有点多，大概流程是这样的：</span></p>
<p>1.        初始化，设置密码长度最小值，最大值，当前密码长度，当前密码值。读取密码可能的组成，生成密码元素池（CharPool）。</p>
<p>2.        <span style="color:#000000;">nextKeyValue</span><span style="color:#000000;">方法，更新密码。把当前密码</span><span style="color:#000000;">+1</span><span style="color:#000000;">，新值来自密码元素池。如果超过当前密码长度，则生成一条当前长度</span><span style="color:#000000;">+1</span><span style="color:#000000;">的新密码。</span></p>
<p> </p>
<h4>Mapper-Reduce</h4>
<table border="1" cellpadding="0" cellspacing="0"><tbody><tr><td valign="top">
<p>Mapper：把hadoop传递过来的密码一一试验</p>
</td>
</tr><tr><td valign="top">
<p align="left"><span style="color:#000000;">         </span><strong><span style="color:#7F0055;">public</span><span style="color:#7F0055;">static</span><span style="color:#7F0055;">class</span></strong><span style="color:#000000;"> TryMapper</span><strong><span style="color:#7F0055;">extends</span></strong><span style="color:#000000;">
 Mapper&lt;Text, Text, Text, Text&gt; {</span></p>
<p align="left"> </p>
<p align="left"><span style="color:#000000;">                   </span><strong><span style="color:#7F0055;">public</span><span style="color:#7F0055;">void</span></strong><span style="color:#000000;"> map(Text</span><span style="color:#6A3E3E;">key</span><span style="color:#000000;">,
 Text </span><span style="color:#6A3E3E;">value</span><span style="color:#000000;">, Context
</span><span style="color:#6A3E3E;">context</span><span style="color:#000000;">) </span>
<strong><span style="color:#7F0055;">throws</span></strong><span style="color:#000000;"> IOException, InterruptedException {</span></p>
<p align="left"><span style="color:#000000;">                   </span>         <strong>
<span style="color:#7F0055;">if</span></strong><span style="color:#000000;"> (DecryptionZipUtil.<em>unzip</em>(</span><span style="color:#2A00FF;">"/home/hadoop/test.zip"</span><span style="color:#000000;">,</span><span style="color:#2A00FF;">"/home/hadoop/output"</span><span style="color:#000000;">,</span><span style="color:#6A3E3E;">value</span><span style="color:#000000;">.toString())
 == 1) {</span></p>
<p align="left"><span style="color:#000000;">                                     </span>
<span style="color:#6A3E3E;">context</span><span style="color:#000000;">.write(</span><strong><span style="color:#7F0055;">new</span></strong><span style="color:#000000;"> Text(</span><span style="color:#2A00FF;">"PassWord"</span><span style="color:#000000;">),</span><span style="color:#6A3E3E;">value</span><span style="color:#000000;">);</span></p>
<p align="left"><span style="color:#000000;">                                     </span>
System.<strong><em><span style="color:#0000C0;">out</span></em></strong><span style="color:#000000;">.println(</span><span style="color:#6A3E3E;">value</span><span style="color:#000000;">.toString() +</span><span style="color:#2A00FF;">" is true"</span><span style="color:#000000;">);</span></p>
<p align="left"><span style="color:#000000;">                            </span>} <strong>
<span style="color:#7F0055;">else</span></strong><span style="color:#000000;"> {</span></p>
<p align="left"><span style="color:#000000;">                                     </span>
System.<strong><em><span style="color:#0000C0;">out</span></em></strong><span style="color:#000000;">.println(</span><span style="color:#6A3E3E;">value</span><span style="color:#000000;">.toString() +</span><span style="color:#2A00FF;">" is false"</span><span style="color:#000000;">);</span></p>
<p align="left"><span style="color:#000000;">                            </span>}</p>
<p align="left"><span style="color:#000000;">                   </span>}</p>
<p><span style="color:#000000;">         </span>}</p>
</td>
</tr></tbody></table><p> </p>
<table border="1" cellpadding="0" cellspacing="0"><tbody><tr><td valign="top">
<p>Reduce：把测试通过的密码输出</p>
</td>
</tr><tr><td valign="top">
<p align="left"><span style="color:#000000;">         </span><strong><span style="color:#7F0055;">public</span><span style="color:#7F0055;">static</span><span style="color:#7F0055;">class</span></strong><span style="color:#000000;"> KeyReducer</span><strong><span style="color:#7F0055;">extends</span></strong><span style="color:#000000;">
 Reducer&lt;Text, Text, Text, Text&gt; {</span></p>
<p align="left"><span style="color:#000000;">                   </span><strong><span style="color:#7F0055;">private</span></strong><span style="color:#000000;"> Text</span><span style="color:#0000C0;">TextPassWord</span><span style="color:#000000;"> =</span><strong><span style="color:#7F0055;">new</span></strong><span style="color:#000000;">
 Text(</span><span style="color:#2A00FF;">"PassWord"</span><span style="color:#000000;">);</span></p>
<p align="left"> </p>
<p align="left"><span style="color:#000000;">                   </span><strong><span style="color:#7F0055;">public</span><span style="color:#7F0055;">void</span></strong><span style="color:#000000;"> reduce(Text</span><span style="color:#6A3E3E;">key</span><span style="color:#000000;">,
 Iterable&lt;Text&gt;</span><span style="color:#6A3E3E;">values</span><span style="color:#000000;">, Context</span><span style="color:#6A3E3E;">context</span><span style="color:#000000;">)
</span><strong><span style="color:#7F0055;">throws</span></strong><span style="color:#000000;"> IOException, InterruptedException {</span></p>
<p align="left"><span style="color:#000000;">                            </span><strong><span style="color:#7F0055;">for</span></strong><span style="color:#000000;"> (Text</span><span style="color:#6A3E3E;">val</span><span style="color:#000000;"> :
</span><span style="color:#6A3E3E;">values</span><span style="color:#000000;">) {</span></p>
<p align="left"><span style="color:#000000;">                                     </span>
<strong><span style="color:#7F0055;">if</span></strong><span style="color:#000000;"> (</span><span style="color:#6A3E3E;">val</span><span style="color:#000000;"> !=</span><strong><span style="color:#7F0055;">null</span></strong><span style="color:#000000;">) {</span></p>
<p align="left"><span style="color:#000000;">                                              </span><span style="color:#6A3E3E;">context</span><span style="color:#000000;">.<span>write</span>(</span><span style="color:#0000C0;">TextPassWord</span><span style="color:#000000;">,</span><span style="color:#6A3E3E;">val</span><span style="color:#000000;">);</span></p>
<p align="left"><span style="color:#000000;">                                              </span><strong><span style="color:#7F0055;">return</span></strong><span style="color:#000000;">;</span></p>
<p align="left"><span style="color:#000000;">                                     </span>
}</p>
<p align="left"><span style="color:#000000;">                            </span>}</p>
<p align="left"><span style="color:#000000;">                   </span><span>}</span></p>
<p><span style="color:#000000;">         </span>}</p>
</td>
</tr></tbody></table><p> </p>
<br clear="all"><p align="left"><strong> </strong></p>
<h3>环境配置</h3>
<h4>测试条件</h4>
<p>       主机：Windows 10 专业版</p>
<p><img src="https://img-blog.csdn.net/20151128215136098?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p></p>
<p>       虚拟机：VMware Workstation 12 Pro</p>
<p><img src="https://img-blog.csdn.net/20151128215206229?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p></p>
<p>       系统：Ubuntu Kylin 14.04</p>
<p><img src="https://img-blog.csdn.net/20151128215235967?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p></p>
<p>       Java版本：OpenJDK 1.7.0_85</p>
<p><img src="https://img-blog.csdn.net/20151128215304179?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p></p>
<p>       Hadoop版本：2.7.1</p>
<p><img src="https://img-blog.csdn.net/20151128215325188?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p></p>
<p>       Eclipse版本：Mars.1 Release(4.5.1)</p>
<p><img src="https://img-blog.csdn.net/20151128215346545?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p></p>
<p>       EclipseHadoop插件版本：0.18</p>
<p><img src="https://img-blog.csdn.net/20151128215409518?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p> </p>
<h4>配置步骤：</h4>
<p>WorkStation安装：略</p>
<p>Ubuntu安装：略</p>
<p>Hadoop与JRE安装：</p>
<p>       单机模式</p>
<p><a href="http://www.cnblogs.com/kinglau/p/3794433.html" rel="nofollow">http://www.cnblogs.com/kinglau/p/3794433.html</a></p>
<p> </p>
<p>伪分布式模式</p>
<p><a href="http://www.cnblogs.com/kinglau/p/3796164.html" rel="nofollow">http://www.cnblogs.com/kinglau/p/3796164.html</a></p>
<p> </p>
<p>Eclipse环境搭建：</p>
<p>       <a href="http://www.cnblogs.com/kinglau/p/3802705.html" rel="nofollow">
http://www.cnblogs.com/kinglau/p/3802705.html</a></p>
<p>winzipaes：</p>
<p>       <a href="http://download.csdn.net/download/zhangyihui1986/4415937" rel="nofollow">
http://download.csdn.net/download/zhangyihui1986/4415937</a></p>
<p align="left">由于是Hadoop运行，所以要把这个压缩包内的第三方jar包（winszipaes_zh_CN_supported_20120416.jar、bcprov-jdk16-140.jar）放入hadoop目录下的share/hadoop/common/lib/文件夹下。</p>
<p> </p>
<p>需要把测试的test.zip拷贝到各个机器的统一路径/home/hadoop/test.zip下。</p>
<p> </p>
<h3>实验结果</h3>
<p>测试ZIP压缩包：</p>
<p>       test.zip</p>
<p>       只含一个abc.txt的文本文件，里面内容为一行k</p>
<p>       密码为123，256bit的AES加密</p>
<p> </p>
<p>实验过程：</p>
<p align="left">       1.在终端中输入hadoop jar test.jar com.fieldsoft.GetZipKey.GetZipKeyMain inputoutput 3 3 1</p>
<p align="left"><img src="https://img-blog.csdn.net/20151128215446465?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p align="left">       其中第一个3为最小密码长度，第二个3为最大密码长度，最后一个1为二进制数001，表示密码组成（小写字母关闭，大写字母关闭，数字开启）。</p>
<p align="left">2.程序运行过程：</p>
<p align="left">       <img src="https://img-blog.csdn.net/20151128215508898?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p align="left">       3.查看output文件夹下结果</p>
<p align="left">       <img src="https://img-blog.csdn.net/20151128215528116?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p align="left">       破解出密码：123</p>
<p> </p>
<h3>参考资料</h3>
<p>kinglau的博客hadoop相关：</p>
<p align="right"><a href="http://www.cnblogs.com/kinglau/tag/hadoop/" rel="nofollow">http://www.cnblogs.com/kinglau/tag/hadoop/</a></p>
<p> </p>
<p>winzipaes相关：</p>
<p align="right"><a href="http://download.csdn.net/download/zhangyihui1986/4415937" rel="nofollow">http://download.csdn.net/download/zhangyihui1986/4415937</a></p>
<p> </p>
<p>自定义InputFormat：</p>
<p align="right"><a href="http://blog.csdn.net/fansy1990/article/details/38039839?utm_source=tuicool&amp;utm_medium=referral" rel="nofollow">http://blog.csdn.net/fansy1990/article/details/38039839?utm_source=tuicool&amp;utm_medium=referral</a></p>
<p> </p>
<p>WordCount运行简介：</p>
<p align="right"><a href="http://www.cnblogs.com/xia520pi/archive/2012/05/16/2504205.html" rel="nofollow">http://www.cnblogs.com/xia520pi/archive/2012/05/16/2504205.html</a></p>
<p> </p>
<p> </p>
            </div>
                </div>