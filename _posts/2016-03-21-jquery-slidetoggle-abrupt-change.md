---
layout: post
title: 在table中使用jquery的slideToggle()产生的滑动效果失效
category: [经验积累]
date: 2016-03-21
---
在页面布局中经常会使用table，原因自然是碰到合适的页面用起来顺手，而且不用担心老旧版本浏览器产生各种奇怪的页面变形。缺点自然也很明显，div+css大行其道，而前端应该更多的应该是要求结构和样式分离，所以待css写的能看了后，应该也不会大量依赖table了。

在table中用第一行标题的Click来控制第二行内容的显示隐藏，用到的是jquery的slideToggle()方法，在切换的时候能看到明显的卡顿和内容闪现。
<!-- more -->

原因是这样子的，在jquery中，toggle()的实现方式是display:none到display:table-row的方式切换，而slideToggle是display:none到display:block的切换，在table中display:table，所以在不断改变高度的时候并没有任何变化，当display变为none的时候直接就隐藏掉了。

解决方式很简单，只要将tr和td同时slideToggle()就行了，td的slideToggle()来保持滑动效果，最后全部隐藏掉。
	
	{% highlight html %}
	<div id='button'></div>
	<tr class='hide'>
		<td class='hide'>我需要隐藏</td>
		<td class='hide'>我也需要隐藏</td>
	</tr>
	<script>
		$(document).ready(function(){
			$("#button").click(function(){
				$('.hide').slideToggle("slow");
			});
 	</script>
        	{% endhighlight %}