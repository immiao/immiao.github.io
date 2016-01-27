---
layout: post
comments: true
categories: Computer-Graphics
---
## Qt Framework ![qtlogo](./qtlogo.png)

> **Qt** (/ˈkjuːt/ "cute", or unofficially as Q-T cue-tee) is a cross-platform application framework that is widely used for developing application software that can be run on various software and hardware platforms with little or no change in the underlying codebase, while still being a native application with the capabilities and speed thereof. Qt is currently being developed both by the [Qt Company](https://en.wikipedia.org/wiki/The_Qt_Company), a subsidiary of [Digia](https://en.wikipedia.org/wiki/Digia), and the Qt Project under [open-source governance](https://en.wikipedia.org/wiki/Open-source_governance), involving individual developers and firms working to advance Qt. Digia owns the Qt trademark and copyright. Qt is available with both commercial and open source [GPL](https://en.wikipedia.org/wiki/GNU_General_Public_License) v3, [LGPL](https://en.wikipedia.org/wiki/GNU_Lesser_General_Public_License) v3 and LGPL v2 licenses.

I eventually chose Qt instead of [MFC](https://en.wikipedia.org/wiki/Microsoft_Foundation_Class_Library) for our user interface design considering its feature of simple operation, good across-platform, powerful maintainability and expansibility.

![qtdesigner](./qt_designer.png)

## DirectX11 Framework ![directx11logo](./directx11.jpg)

> **Microsoft DirectX** is a collection of application programming interfaces (APIs) for handling tasks related to multimedia, especially game programming and video, on Microsoft platforms.

Some samples provided by DirectX SDK and *Introduction to 3D Game Programming with Directx 11*

![sample1](./dx_sample1.png)![sample2](./dx_sample2.png)![sample3](./dx_sample3.png)

I strongly recommend the book-*Introduction to 3D Game Programming with Directx 11*, which introduces **DirectX11** as well as basic graphics knowledge to readers. The author also wrote *Introduction to 3D Game Programming with DirectX 9.0*, which is also a classical teaching material of **DirectX9.0.** However, the fix-function pipeline in **DirectX9.0** is outmoded and the programmable pipeline in **DirectX11** is prefered in the industry.

![book0](./book0.jpg)![book](./book.png)

## Line Tool based on Qt and DirectX11

> To enhance my proficiency in both Qt and DirectX11, I implemented a line tool.

OpenGL is supported well by Qt because they are both across-platform. However, DirectX is not supported officially by Qt. I encountered some difficulties in combining Qt and DirectX. Fortunately, the problem was solved. I disabled `QPaintEngine` provided by Qt and overwrote `paintEvent` as well as `resizeEvent` in order to enable DirectX11 to render onto the widget.

The core codes implementing the combination are showed as below.

{% highlight cpp %}
virtual QPaintEngine* paintEngine() const { return NULL; }
{% endhighlight %}

{% highlight cpp %}
DxWidget::DxWidget() // DxWidget inherits from QWidget
{
	setAttribute(Qt::WA_PaintOnScreen, true);
	setAttribute(Qt::WA_NativeWindow, true);

	...
}

void DxWidget::resizeEvent(QResizeEvent* pEvent)
{
	DxRender();
}

void DxWidget::paintEvent(QPaintEvent* pEvent)
{
	DxRender();
}

{% endhighlight %}

Then the program runs well.

![linetool](./linetool.gif)

## My Animation Generator

> This animation generator enables user to generate their own movies based on real-time rendering. The software is able to read `.obj` and `.m3d` files (`.m3d` files are the animation files provided by *Introduction to 3D Game Programming with Directx 11*). Files in the format of `.meproj` are our project files. Users are able to control the motions of roles by XML files (the module of XML parser is still under development). I am still conducting this project and hope to expand it to a full game engine.

Visit this link to see my project on Github : [https://github.com/immiao/ModelEditor](https://github.com/immiao/ModelEditor). I do not share it on Github at the early stage.

#### Results at Present Stage

![generator1](./generator1.gif)

![generator2](./generator2.gif)

![generator3](./generator3.gif)