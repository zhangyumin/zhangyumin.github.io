---
layout: post
title: Firefox出现event is not defined解决方法
category: Javascript
date: 2015-08-02
---
自己写了一个div+checkbox的弹出式js.在之前一直在chrome中使用一切OK.

今天换Firefox后,弹出窗一直保持弹出状态不收回.consule中告诉我是event is not defined.

随便百度了一下,应该是Firefox中使用了不同的事件对象模型,区别于IE　Dom,而是W3C Dom.

那么DOM中就不包含Event对象.自然调用就无法使用了.

网友分享的是加入

	e = e || event;

或者

	e = e || window.event;

但是修改之后依然报错 e is not defined.

后来查阅资料后解决,Firefox需要通过参数传递事件对象.所以给function加入参数event就好.代码如下:

	$(document).bind('click', function(event){
		var targ;
		if (event.target) 
			targ = event.target
		else if (event.srcElement) 
			targ = event.srcElement
		if (targ.nodeType == 3) // defeat Safari bug
			targ = targ.parentNode;
		if (targ.id !='column8' && !$(targ).parents('div.container8').attr('class'))
			$('div.container8').hide(100);
	}
                                                                                                                        });
