---
layout:     post
title:      使用Dtree显示HBase目录
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>目标：将HBase文件目录通过web端树状显示</p>
<p>方法：先获取HBase目录，通过servlet传给jsp，在jsp中使用D-tree做显示</p>
<p>最后的dtree代码如下：</p>
<p>&lt;div class="dtree" &gt;<br></p>
<p>&lt;script type="text/javascript" src="../js/dtree.js"&gt;&lt;/script&gt;<br></p>
<p>HBase = new dTree('HBase');<br></p>
<p>var node = &lt;%=request.getAttribute("path_name")%&gt;;</p>
<p>HBase.add(0,-1, node);<br></p>
<p>var myobj= {id:"", pid:"",name:""}; </p>
                    HBase.add(0, -1, 'HBase');<br>
                    alert('HBase fa');<br>
                    for(var i=0;i&lt;eval(node).length;i++){<br>
                        myobj = node[i];<br>
                        alert(myobj.name);<br>
                    //    d.add(Number(myobj.id), Number(myobj.pid), myobj.name);<br>
                        HBase.add(parseInt(myobj.id), parseInt(myobj.pid), myobj.name);<br>
                    <br>
                    } <br><p>                    document.write(HBase);</p>
<p>&lt;/script&gt;</p>
<p>&lt;/div&gt;<br></p>
            </div>
                </div>