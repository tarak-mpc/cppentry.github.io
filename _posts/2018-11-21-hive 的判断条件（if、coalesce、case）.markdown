---
layout:     post
title:      hive 的判断条件（if、coalesce、case）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<br><br><span style="color:rgb(51,51,51);font-family:'Pingfang SC', STHeiti, 'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', 'WenQuanYi Micro Hei', SimSun, sans-serif;font-size:28px;background-color:rgb(248,248,248);">hive 的判断条件（if、coalesce、case）</span><br><div class="blog-abstract">摘要: hive 里面没有ifnull 那么怎么办呢</div>
<div class="blog-body" id="blogBody">
<div class="BlogContent"><span id="OSC_h2_1"></span><span style="font-size:16px;background-color:rgb(248,248,248);"></span>
<h2 id="h2_0">CONDITIONAL FUNCTIONS IN HIVE</h2>
<p>Hive supports three types of conditional functions. These functions are listed below:<br><br><span>IF( Test Condition, True Value, False Value ) </span><br>
The IF condition evaluates the “Test Condition” and if the “Test Condition” is true, then it returns the “True Value”. Otherwise, it returns the False Value.<br>
Example: IF(1=1, 'working', 'not working') returns 'working'<br><br><span>COALESCE( value1,value2,... )</span><br><br>
The COALESCE function returns the fist not NULL value from the list of values. If all the values in the list are NULL, then it returns NULL.<br>
Example: COALESCE(NULL,NULL,5,NULL,4) returns 5<br><br><span>CASE Statement</span><br><br>
The syntax for the case statement is:<br></p>
<blockquote>CASE  [ expression ] <br>
  WHEN condition1 THEN result1 <br>
  WHEN condition2 THEN result2 <br>
  ... <br>
  WHEN conditionn THEN resultn <br>
  ELSE result <br>
END</blockquote>
<p>Here expression is optional. It is the value that you are comparing to the list of conditions. (ie: condition1, condition2, ... conditionn).<br><br>
All the conditions must be of same datatype. Conditions are evaluated in the order listed. Once a condition is found to be true, the case statement will return the result and not evaluate the conditions any further.<br><br>
All the results must be of same datatype. This is the value returned once a condition is found to be true.<br><br>
IF no condition is found to be true, then the case statement will return the value in the ELSE clause. If the ELSE clause is omitted and no condition is found to be true, then the case statement will return NULL<br><br>
Example: <br><br></p>
<blockquote>CASE Fruit <br>
  WHEN 'APPLE' THEN 'The owner is APPLE' <br>
  WHEN 'ORANGE' THEN 'The owner is ORANGE' <br>
  ELSE 'It is another Fruit' <br>
END</blockquote>
<p>The other form of CASE is<br><br></p>
<blockquote>CASE  <br>
  WHEN Fruit = 'APPLE' THEN 'The owner is APPLE' <br>
  WHEN Fruit = 'ORANGE' THEN 'The owner is ORANGE' <br>
  ELSE 'It is another Fruit' <br>
END</blockquote>
<p><br></p>
<p><br></p>
</div>
</div>
<br><div><span style="color:rgb(51,51,51);font-family:'Pingfang SC', STHeiti, 'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', 'WenQuanYi Micro Hei', SimSun, sans-serif;font-size:28px;background-color:rgb(248,248,248);">hive
 的判断条件（if、coalesce、case）</span></div>
            </div>
                </div>