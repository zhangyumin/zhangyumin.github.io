---
layout: post
title: js动态调整iframe的窗口高度
category: [经验积累]
date: 2016-03-25
---
iframe页面并不能撑开父容器div，使得页面看起来并不协调。解决方法是：
<!-- more -->

使用JavaScript监听iframe元素的load事件，然后根据iframe中document的高度来动态调整iframe窗口的高度。

	{% highlight html %}
	  <iframe id="testFrame" sandbox="allow-same-origin" src="sub.html"></iframe>

	  <script type="text/javascript">
	    var testFrame = document.getElementById('testFrame');
	    testFrame.addEventListener('load', function() {
	      testFrame.height = getHeight(testFrame.contentDocument);;
	    });

	    function getHeight(doc) {
	      var body = doc.body,
	          html = doc.documentElement;

	      var height = Math.max( body.scrollHeight, body.offsetHeight,
	        html.clientHeight, html.scrollHeight, html.offsetHeight );
	      return height;
	    }
	  </script>
	  {% endhighlight %}
