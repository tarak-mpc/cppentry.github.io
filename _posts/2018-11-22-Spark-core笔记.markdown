---
layout:     post
title:      Spark-core笔记
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="spark-core笔记">Spark-core笔记</h1>

<hr>



<h2 id="spark框架简介">Spark框架简介</h2>

<pre><code>spark 配置
</code></pre>

<p><img src="http://static.zybuluo.com/825973286/sfckp635mijq26d8thnghguv/image_1citvkvnc11jk136b9hn172t1rn69.png" alt="image_1citvkvnc11jk136b9hn172t1rn69.png-19.8kB" title="">    </p>

<pre><code>shell启动
</code></pre>

<p><img src="http://static.zybuluo.com/825973286/npwt02cltiagyynkrutvulqc/image_1cj0md5av1sna1a9l1f08aftr3g34.png" alt="image_1cj0md5av1sna1a9l1f08aftr3g34.png-22.1kB" title=""> <br>
<img src="http://static.zybuluo.com/825973286/lreemmw32bkbxff6r8kzm8us/image_1citvt0gfdh517gk1efbk3nk272d.png" alt="image_1citvt0gfdh517gk1efbk3nk272d.png-24.7kB" title=""></p>

<p>local[2] 启动模式 <br>
<img src="http://static.zybuluo.com/825973286/o8qgr9552fr7ygagpc9m4320/image_1citvvhbhikd1c7m1a53vp6up72q.png" alt="image_1citvvhbhikd1c7m1a53vp6up72q.png-1.9kB" title=""></p>

<pre><code>spark分类
</code></pre>

<p><img src="http://static.zybuluo.com/825973286/eplyitlumxach4pudscwm6zu/image_1citvpg3t1rmq111gt641q4m54j13.png" alt="image_1citvpg3t1rmq111gt641q4m54j13.png-258.8kB" title=""></p>

<pre><code>简介
</code></pre>

<p><img src="http://static.zybuluo.com/825973286/vt02hyrdabxtmrrk4cgdckjv/image_1citvie811grd4dq1btf18k0ql9.png" alt="image_1citvie811grd4dq1btf18k0ql9.png-31kB" title=""> <br>
<img src="http://static.zybuluo.com/825973286/ijymke339hwq7sakwa2vumqq/image_1citvj1i8db124i16pgel06rim.png" alt="image_1citvj1i8db124i16pgel06rim.png-35.5kB" title=""></p>

<pre><code>spark运行模式
</code></pre>

<p><img src="http://static.zybuluo.com/825973286/0pdrjn5snsfhohctdvk7aaht/image_1citvmlovt69teo1kng1afom86m.png" alt="image_1citvmlovt69teo1kng1afom86m.png-12.9kB" title=""></p>

<pre><code>spark shell
</code></pre>

<p><img src="http://static.zybuluo.com/825973286/a0vsdk2o3eisjbzsshmpfzq8/image_1citvq6ab14roslctjqovo1p971g.png" alt="image_1citvq6ab14roslctjqovo1p971g.png-122.6kB" title=""></p>



<h3 id="结构图运行模式解析">结构图、运行模式解析</h3>

<p><img src="http://static.zybuluo.com/825973286/xsqgn9di0i90zbqjnjx78i6q/image_1cj0n8qsn1e2b1v4o14jh60c1hbh9k.png" alt="image_1cj0n8qsn1e2b1v4o14jh60c1hbh9k.png-428.7kB" title=""></p>

<p><img src="http://static.zybuluo.com/825973286/nqzjnmzg017lwu5tdgisfwfx/image_1cj0ncm42iuf183iceno46aqaa1.png" alt="image_1cj0ncm42iuf183iceno46aqaa1.png-137.9kB" title=""> <br>
<img src="http://static.zybuluo.com/825973286/m6rw295np7t929yrmg5riivb/image_1cj0mv7di1vbsnmn5kdm5d1d7n7j.png" alt="image_1cj0mv7di1vbsnmn5kdm5d1d7n7j.png-190.4kB" title=""> <br>
<img src="http://static.zybuluo.com/825973286/b3orllhpn31ohe84ir7y61rx/image_1cj0n0kbq6np1chk61h1v0k5dd80.png" alt="image_1cj0n0kbq6np1chk61h1v0k5dd80.png-273.3kB" title=""> <br>
<img src="http://static.zybuluo.com/825973286/qdeg5eq093jss3tl2xweyaxs/image_1cj0n1fvdb4u1n7ttb91b2nfgt8d.png" alt="image_1cj0n1fvdb4u1n7ttb91b2nfgt8d.png-258.9kB" title=""> <br>
<img src="http://static.zybuluo.com/825973286/qhz0ei60d0734sqhq0omphdm/image_1cj0n362kv9f1g1ok7e3p61v4n8q.png" alt="image_1cj0n362kv9f1g1ok7e3p61v4n8q.png-256.2kB" title=""></p>



<h3 id="rdd">RDD</h3>

<pre><code>五大特点
</code></pre>

<p><img src="http://static.zybuluo.com/825973286/30lo4hd212yybpqkk5a2y7mh/image_1cj0n5hau1c1ke3g44qjdjmbr97.png" alt="image_1cj0n5hau1c1ke3g44qjdjmbr97.png-175.5kB" title=""></p>

<p><img src="http://static.zybuluo.com/825973286/gy34ikzh8016iudoavfpzkgw/image_1cj0m4a4v1hm0k837j62gu15a19.png" alt="image_1cj0m4a4v1hm0k837j62gu15a19.png-40kB" title=""> <br>
<img src="http://static.zybuluo.com/825973286/e082a3wjg3doqkboq0wri10c/image_1cj0m4ss01lan7i1jldoo5qbfm.png" alt="image_1cj0m4ss01lan7i1jldoo5qbfm.png-14.1kB" title=""> <br>
<img src="http://static.zybuluo.com/825973286/nm8zebxsz63w16r0srqfqnvr/image_1cj0m6h8pqt1rqf1dab1s3t1v0313.png" alt="image_1cj0m6h8pqt1rqf1dab1s3t1v0313.png-24.4kB" title=""> <br>
<img src="http://static.zybuluo.com/825973286/udkbf3xz8bvaiibgbd46d11x/image_1cj0m74kkqj75qu8gnf5u5o51g.png" alt="image_1cj0m74kkqj75qu8gnf5u5o51g.png-23.9kB" title=""> <br>
<img src="http://static.zybuluo.com/825973286/tq7fb3ohy361yg7wx72h1u5i/image_1cj0m7n9tee6bmj1o4e192i12rg1t.png" alt="image_1cj0m7n9tee6bmj1o4e192i12rg1t.png-20.5kB" title=""> <br>
<img src="http://static.zybuluo.com/825973286/teeteiglgr871vjf8ikvaafr/image_1cj0m84ng1pjq1m5a6n2155ofjl2a.png" alt="image_1cj0m84ng1pjq1m5a6n2155ofjl2a.png-29.8kB" title=""></p>



<h3 id="运行模式与配置">运行模式与配置</h3>

<p><img src="http://static.zybuluo.com/825973286/esi6swqboqcvyqd6mdk6mykt/image_1cj0m9h1l17g21ipaefhif1l4v2n.png" alt="image_1cj0m9h1l17g21ipaefhif1l4v2n.png-31.9kB" title=""></p>



<h2 id="spark程序">Spark程序</h2>



<h3 id="提交程序">提交程序</h3>

<p><img src="http://static.zybuluo.com/825973286/2l6014vxpylag45j6bra0hr8/image_1cj0mi4n143d1ng81oj1jph1aqm3h.png" alt="image_1cj0mi4n143d1ng81oj1jph1aqm3h.png-9.2kB" title=""></p>

<pre><code>自定义目录和 运行模式
</code></pre>

<p><img src="http://static.zybuluo.com/825973286/oajvgngpyqw8h3cssii68eee/image_1cj0mivetfbf1g3h1rf1e0d1p873u.png" alt="image_1cj0mivetfbf1g3h1rf1e0d1p873u.png-9.3kB" title=""></p>

<pre><code>运行在yarn上 并设置内存 线程
</code></pre>

<p><img src="http://static.zybuluo.com/825973286/zns4qngq1izgvdrkomveluvr/image_1cj0mk4jeodgddrkpq1bdj1shs4b.png" alt="image_1cj0mk4jeodgddrkpq1bdj1shs4b.png-13.1kB" title=""></p>



<h3 id="spark开发模板">Spark开发模板</h3>

<p><img src="http://static.zybuluo.com/825973286/793j94yh3fxnpwel62vxbxvq/image_1ciu07dcffemccmornl661b3g37.png" alt="image_1ciu07dcffemccmornl661b3g37.png-64.4kB" title=""> <br>
<img src="http://static.zybuluo.com/825973286/vb5wuo0lgl5m962qoupcmi6m/image_1ciu080n8f4v2am1ngg1jbgqd544.png" alt="image_1ciu080n8f4v2am1ngg1jbgqd544.png-34.4kB" title=""></p>



<h3 id="wordcount-程序">WordCount 程序</h3>

<p><img src="http://static.zybuluo.com/825973286/ocl25rziw8sd78a495lqq20l/image_1ciu09m3i1q9pc991n1ls5p12414h.png" alt="image_1ciu09m3i1q9pc991n1ls5p12414h.png-69.1kB" title=""> <br>
<img src="http://static.zybuluo.com/825973286/r5a40tw7l4ixfpx3rmmlpb37/image_1ciu0a2rk1mu33qh1tq31mrd18aa4u.png" alt="image_1ciu0a2rk1mu33qh1tq31mrd18aa4u.png-64kB" title=""> <br>
<img src="http://static.zybuluo.com/825973286/j9rlhycjzc4e17bv1z1mldbr/image_1ciu0aji0l05a1he41m6kmbv5b.png" alt="image_1ciu0aji0l05a1he41m6kmbv5b.png-51.4kB" title=""> <br>
<img src="http://static.zybuluo.com/825973286/a7msmx9a5ce8eiy8iz435lq8/image_1ciu0atqa11k9uqkoc8106bejt5o.png" alt="image_1ciu0atqa11k9uqkoc8106bejt5o.png-25kB" title=""></p>



<h3 id="数据结果导入mysql">数据结果导入MySQL</h3>

<p><img src="http://static.zybuluo.com/825973286/4zz9yd5wxucetd67ii7k3nb9/image_1ciu0f151slh2qg1mvd15r011u065.png" alt="image_1ciu0f151slh2qg1mvd15r011u065.png-54.2kB" title=""> <br>
<img src="http://static.zybuluo.com/825973286/rhtxikcekgs61c5n8qdsccgz/image_1ciu0fjqv1btscg81nu8b11edv6i.png" alt="image_1ciu0fjqv1btscg81nu8b11edv6i.png-68.4kB" title=""> <br>
<img src="http://static.zybuluo.com/825973286/r3bn6fzcdm3a8q0o2kyi8ux9/image_1ciu0g0jd14sr7apoa8118250d6v.png" alt="image_1ciu0g0jd14sr7apoa8118250d6v.png-55.3kB" title=""> <br>
<img src="http://static.zybuluo.com/825973286/i3yqfaei6dsrrtehnsg7oygp/image_1ciu0g8671qr5dn1mrhft777a7c.png" alt="image_1ciu0g8671qr5dn1mrhft777a7c.png-22kB" title=""></p>



<h3 id="分组排序和topkey">分组、排序和TopKey</h3>

<p><img src="http://static.zybuluo.com/825973286/37msreyc7ejkkazzka8log38/image_1cj0mlqeq18er1ta91jii17941n584o.png" alt="image_1cj0mlqeq18er1ta91jii17941n584o.png-66.2kB" title=""> <br>
<img src="http://static.zybuluo.com/825973286/5ngg1b4fc61mwxiay2sjokue/image_1cj0mmik5h1icq41cfqafk1rue55.png" alt="image_1cj0mmik5h1icq41cfqafk1rue55.png-43.6kB" title=""> <br>
<img src="http://static.zybuluo.com/825973286/abf7snnj0s5p4wsc5vmzr2g9/image_1cj0mn10k6p81bph1unk1f89eqm5i.png" alt="image_1cj0mn10k6p81bph1unk1f89eqm5i.png-40.4kB" title=""> <br>
<img src="http://static.zybuluo.com/825973286/zbnwl0mffddgrv530r1siogk/image_1cj0mn6sn1vtq53ieqnjo213pe5v.png" alt="image_1cj0mn6sn1vtq53ieqnjo213pe5v.png-9.7kB" title=""></p>



<h3 id="分组排序topkey-并且改进map和局部聚合">分组排序、TopKey 并且改进map和局部聚合</h3>

<p><img src="http://static.zybuluo.com/825973286/vwwr4zpxmgjji1hxp0df4smj/image_1cj0mq9b01l3kpp41aio194t7m16c.png" alt="image_1cj0mq9b01l3kpp41aio194t7m16c.png-68.5kB" title=""> <br>
<img src="http://static.zybuluo.com/825973286/ibmhhd8ysneme584yxbg5aez/image_1cj0mqhc97ib1bqmadn9mv1eab6p.png" alt="image_1cj0mqhc97ib1bqmadn9mv1eab6p.png-45kB" title=""> <br>
<img src="http://static.zybuluo.com/825973286/k4o0ptyjw5wxkfrnktajzjli/image_1cj0mqv3u13c61cefriec6t12qf76.png" alt="image_1cj0mqv3u13c61cefriec6t12qf76.png-69.4kB" title=""></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>