---
layout: post
title: 解决Ubuntu系统下sublime text3无法输入中文的问题
category: [经验积累]
date: 2016-03-26
---
装上sublime text3来格式化看一些jquery代码不要太方便，用来编辑文本的时候却意外发现不能输入中文，搜了一下搜出好多的答案，下面整理了一个可用的。
<!-- more -->

## 1.安装c++编译环境
程序是用c写的，需要自己编译，缺少编译环境自然是不行的。
	
	sudo apt-get install build-essential
	sudo apt-get install libgtk2.0-dev

## 2.编辑c文件
在～目录下新建一个名为sublime-imfix.c的文件，内容如下:
	
{% highlight c %}
/*
sublime-imfix.c
Use LD_PRELOAD to interpose some function to fix sublime input method support for linux.
By Cjacker Huang

gcc -shared -o libsublime-imfix.so sublime-imfix.c `pkg-config --libs --cflags gtk+-2.0` -fPIC
LD_PRELOAD=./libsublime-imfix.so subl
*/
#include <gtk/gtk.h>
#include <gdk/gdkx.h>
typedef GdkSegment GdkRegionBox;

struct _GdkRegion
{
  long size;
  long numRects;
  GdkRegionBox *rects;
  GdkRegionBox extents;
};

GtkIMContext *local_context;

void
gdk_region_get_clipbox (const GdkRegion *region,
	    GdkRectangle    *rectangle)
{
  g_return_if_fail (region != NULL);
  g_return_if_fail (rectangle != NULL);

  rectangle->x = region->extents.x1;
  rectangle->y = region->extents.y1;
  rectangle->width = region->extents.x2 - region->extents.x1;
  rectangle->height = region->extents.y2 - region->extents.y1;
  GdkRectangle rect;
  rect.x = rectangle->x;
  rect.y = rectangle->y;
  rect.width = 0;
  rect.height = rectangle->height;
  //The caret width is 2;
  //Maybe sometimes we will make a mistake, but for most of the time, it should be the caret.
  if(rectangle->width == 2 && GTK_IS_IM_CONTEXT(local_context)) {
	gtk_im_context_set_cursor_location(local_context, rectangle);
  }
}

//this is needed, for example, if you input something in file dialog and return back the edit area
//context will lost, so here we set it again.

static GdkFilterReturn event_filter (GdkXEvent *xevent, GdkEvent *event, gpointer im_context)
{
    XEvent *xev = (XEvent *)xevent;
    if(xev->type == KeyRelease && GTK_IS_IM_CONTEXT(im_context)) {
       GdkWindow * win = g_object_get_data(G_OBJECT(im_context),"window");
       if(GDK_IS_WINDOW(win))
	 gtk_im_context_set_client_window(im_context, win);
    }
    return GDK_FILTER_CONTINUE;
}

void gtk_im_context_set_client_window (GtkIMContext *context,
	  GdkWindow    *window)
{
  GtkIMContextClass *klass;
  g_return_if_fail (GTK_IS_IM_CONTEXT (context));
  klass = GTK_IM_CONTEXT_GET_CLASS (context);
  if (klass->set_client_window)
    klass->set_client_window (context, window);

  if(!GDK_IS_WINDOW (window))
    return;
  g_object_set_data(G_OBJECT(context),"window",window);
  int width = gdk_window_get_width(window);
  int height = gdk_window_get_height(window);
  if(width != 0 && height !=0) {
    gtk_im_context_focus_in(context);
    local_context = context;
  }
  gdk_window_add_filter (window, event_filter, context);
}
{% endhighlight %}

## 3.将文件编译成共享库libsublime-imfix.so
	
	gcc -shared -o libsublime-imfix.so sublime-imfix.c `pkg-config --libs --cflags gtk+-2.0` -fPIC

## 4.将编译号的文件拷贝到sublime text所在文件夹
	
	sudo mv libsublime-imfix.so /opt/sublime_text/

## 5.修改文件/usr/bin/subl的内容
	
	sudo vi /usr/bin/subl

将

	#!/bin/sh
	exec /opt/sublime_text/sublime_text "$@"

修改为

	#!/bin/sh
	LD_PRELOAD=/opt/sublime_text/libsublime-imfix.so exec /opt/sublime_text/sublime_text "$@"

这样在命令中执行subl打开的sublime text3就可以输入中文了

## 6.桌面运行及右键运行
为了使用鼠标右键打开文件时能够使用中文输入，还需要修改文件sublime_text.desktop的内容，
命令

	sudo vi /usr/share/applications/sublime_text.desktop

将[Desktop Entry]中的字符串

	Exec=/opt/sublime_text/sublime_text %F

修改为

	Exec=bash -c "LD_PRELOAD=/opt/sublime_text/libsublime-imfix.so exec /opt/sublime_text/sublime_text %F"

将[Desktop Action Window]中的字符串

	Exec=/opt/sublime_text/sublime_text -n

修改为

	Exec=bash -c "LD_PRELOAD=/opt/sublime_text/libsublime-imfix.so exec /opt/sublime_text/sublime_text -n"

将[Desktop Action Document]中的字符串


	Exec=/opt/sublime_text/sublime_text --command new_file

修改为

	Exec=bash -c "LD_PRELOAD=/opt/sublime_text/libsublime-imfix.so exec /opt/sublime_text/sublime_text --command new_file"

完美结束，不管是subl中还是桌面和鼠标右键打开的sublime text3都可以输入中文了
