---
layout: post
title: 使用HTML5之Canvas绘图
category: [HTML5,Javascript,经验积累]
date: 2015-10-15
---
最近上头来了新的需求,要根据Gene的坐标信息画出Gene Picture.搜了一大圈还是觉得HTML5的Cavans简单易用支持好,捣鼓了几天也算画的能看了.在此记录一下Canvas绘图积累到的知识,便于以后查阅.

Canvas是一块画布,可以画任何的路径,图形,字符,填充等等.画图的操作用的是Javascript,画布是一块矩形区域,可以控制其每一个元素.除了基本图形外,Canvas还支持图形的翻转和变换,因为未用到此类功能,所以暂时就不写了.Canvas功能强大,以下记录仅为最基础的绘图.
<!-- more -->

#绘图

1.绘图之前一定是要先添加一块画布Canvas,来确定需要绘制的区域,步骤和添加其他标签一样.

	<canvas id="gene" width="1000px;" height="150px;"></canvas>

创建好画布之后,根据画布的长和宽,就可以确定各个位置的坐标了.画布的左上角为坐标原点(0,0),每一个像素为一个刻度.

2.id属性是必须的,后边的绘图需要用id来拿到当前Canvas的Dom对象.通过获得的Dom对象便可以获取上下文,Canvas绘制图形就是考Canvas对象的上下文对象.
	
	<script type="text/javascript">
		//获得Canvas元素
		var canvas = document.getElementById("gene");
		//获得上下文,目前只能用2d
		var context = canvas.getContext("2d");
	</script>

3.接下来就可以绘制图形了,context默认的绘制方式有两种:stroke(线)和fill(填充).

(1)绘制一条直线.
	
	<body>
		<canvas id="gene" width="800px;" height="50px;" ></canvas>
		<script type="text/javascript">
			window.onload=function(){
				line("gene");
			}
			function line(id){
				var canvas = document.getElementById(id);
				var context = canvas.getContext("2d");
				context.strokeStyle="#000000";
				context.fillStyle="#000000";//line为黑色
				context.moveTo(0,0);//从(0,0)点开始绘图
				context.lineTo(800,0);//划线到(800,0)位置
				context.stroke();
		    	}
		</script>
	</body>

example:
<canvas id="gene1" width="800px;" height="50px;" ></canvas>
<script type="text/javascript">
	window.onload=function(){
				line("gene1");
				rectangle("gene2");
				rectangle2("gene3");
				round("gene4");
				info("gene5");
			}
			function line(id){
				var canvas = document.getElementById(id);
				var context = canvas.getContext("2d");
				context.strokeStyle="#000000";
				context.fillStyle="#000000";//line为黑色
				context.moveTo(0,0);//从(0,0)点开始绘图
				context.lineTo(800,0);//划线到(800,0)位置
				context.stroke();
		    	}
			function rectangle(id){
				var canvas = document.getElementById(id);
				var context = canvas.getContext("2d");
				context.strokeStyle="#000000";
				context.fillStyle="#000000";//rectangle为黑色
				context.fillRect(200,0,100,40);//fillRect(x,y,width,height);
				context.stroke();
		    	}
			function rectangle2(id){
				var canvas = document.getElementById(id);
				var context = canvas.getContext("2d");
				context.strokeStyle="#000000";
				context.fillStyle="#000000";//rectangle为黑色
				context.strokeRect(200,0,100,40);
				context.stroke();
		    	}
			function round(id){
				var canvas = document.getElementById(id);
				var context = canvas.getContext("2d");
				context.strokeStyle="#000000";
				context.fillStyle="#000000";//round为黑色
				context.beginPath();
				context.arc(250,25,20,0,Math.PI*2,true);//arc(x,y,radius,startAngle,endAngle,anticlockwise)
				context.closePath;
				context.fill();
		    	}
			function info(id){
				var canvas = document.getElementById(id);
				var context = canvas.getContext("2d");
				context.strokeStyle="#000000";
				context.fillStyle="#000000";//round为黑色
				context.font="20px Arial";
				context.fillText("Hello World",200,35);
		    	}
</script>

(2)绘制一个矩形
	
	<body>
		<canvas id="gene" width="800px;" height="50px;" ></canvas>
		<script type="text/javascript">
			window.onload=function(){
				rectangle("gene2");
			}
			function rectangle(id){
				var canvas = document.getElementById(id);
				var context = canvas.getContext("2d");
				context.strokeStyle="#000000";
				context.fillStyle="#000000";//rectangle为黑色
				context.fillRect(200,0,100,40);//fillRect(x,y,width,height);
				context.stroke();
		    	}
		</script>
	</body>

example:
<canvas id="gene2" width="800px;" height="50px;" ></canvas>

(3)绘制一个矩形边框
	
	<body>
		<canvas id="gene" width="800px;" height="50px;" ></canvas>
		<script type="text/javascript">
			window.onload=function(){
				rectangle("gene");
			}
			function rectangle2(id){
				var canvas = document.getElementById(id);
				var context = canvas.getContext("2d");
				context.strokeStyle="#000000";
				context.fillStyle="#000000";//rectangle为黑色
				context.strokeRect(200,0,100,40);
				context.stroke();
		    	}
		</script>
	</body>

example:
<canvas id="gene3" width="800px;" height="50px;" ></canvas>

(4)绘制一个圆形
	
	<body>
		<canvas id="gene" width="800px;" height="50px;" ></canvas>
		<script type="text/javascript">
			window.onload=function(){
				round("gene");
			}
			function round(id){
				var canvas = document.getElementById(id);
				var context = canvas.getContext("2d");
				context.strokeStyle="#000000";
				context.fillStyle="#000000";//round为黑色
				context.beginPath();
				context.arc(250,25,20,0,Math.PI*2,true);//arc(x,y,radius,startAngle,endAngle,anticlockwise)
				context.closePath;
				context.fill();
		    	}
		</script>
	</body>

example:
<canvas id="gene4" width="800px;" height="50px;" ></canvas>

(5)输入字符
	
	<body>
		<canvas id="gene" width="1000px;" height="150px;" >< /canvas>
		<script type="text/javascript">
			window.onload=function(){
				info("gene");
			}
			function info(id){
				var canvas = document.getElementById(id);
				var context = canvas.getContext("2d");
				context.strokeStyle="#000000";
				context.fillStyle="#000000";//round为黑色
				context.font="20px Arial";
				context.fillText("Hello World",95,35);
		    	}
		</script>
	</body>

example:
<canvas id="gene5" width="800px;" height="50px;" ></canvas>

#注意事项
1.图形最好用beginPath()和endPath()包含,否则可能发生颜色混乱.
