# 着色(着色频率、shader、纹理映射)

## 着色频率

### 平面着色(Flat shading)

shade each triangle

求每个三角形面的法向量并着色

速度快，但效果不好，表面并不光滑

<img src="https://raw.githubusercontent.com/PuLei36/notes/note/images/games101/Games101-8-1.png" alt="Games101-8-1" style="zoom: 33%;" />

### 顶点着色(Gouraud shading)

shade each vertex

给三角形的三个顶点着色，其内部的点通过差值的方式算出来

<img src="https://raw.githubusercontent.com/PuLei36/notes/note/images/games101/Games101-8-2.png" alt="Games101-8-2" style="zoom: 33%;" />

定义逐顶点法线的方法：认为每个顶点的法线是周围面的法线**加权**求平均

<img src="https://raw.githubusercontent.com/PuLei36/notes/note/images/games101/Games101-8-3.png" alt="Games101-8-3" style="zoom: 33%;" />

### 像素着色(Phong shading)

也叫fragment/pixel shading - 片元着色

shade each pixel(并非Blinn-Phong反射模型)

求每个像素法线并着色

效果好，但开销大

<img src="https://raw.githubusercontent.com/PuLei36/notes/note/images/games101/Games101-8-4.png" alt="Games101-8-4" style="zoom: 33%;" />

定义逐像素法线的方法：重心坐标差值（注意最后要归一化成单位向量）

<img src="https://raw.githubusercontent.com/PuLei36/notes/note/images/games101/Games101-8-5.png" alt="Games101-8-5" style="zoom:33%;" />

### 效果图

<img src="https://raw.githubusercontent.com/PuLei36/notes/note/images/games101/Games101-8-6.png" alt="Games101-8-6" style="zoom: 33%;" />

## 图形管线(实时渲染管线)

### 总流程

<img src="https://raw.githubusercontent.com/PuLei36/notes/note/images/games101/Games101-8-7.png" alt="Games101-8-7" style="zoom: 40%;" />

### 处理流程

#### 顶点处理

对所有顶点进行MVP变换，最终得到投影到二维平面的坐标信息，超出观察空间的会被剪裁掉

<img src="https://raw.githubusercontent.com/PuLei36/notes/note/images/games101/Games101-8-8.png" alt="Games101-8-8" style="zoom:33%;" />

#### 三角形阶段

按原几何信息，变成三角面，每个面由三个顶点构成

#### 光栅化阶段

<img src="https://raw.githubusercontent.com/PuLei36/notes/note/images/games101/Games101-8-9.png" alt="Games101-8-9" style="zoom:33%;" />

#### 片元阶段

1. 深度测试
2. 着色操作（如果是顶点着色则在顶点处理阶段就进行，如果是像素着色则在片元阶段进行）
	实际的实时渲染业界就是在写如何进行顶点着色和像素着色（shader）
3. 纹理映射

<img src="https://raw.githubusercontent.com/PuLei36/notes/note/images/games101/Games101-8-10.png" alt="Games101-8-10" style="zoom:33%;" />

<img src="https://raw.githubusercontent.com/PuLei36/notes/note/images/games101/Games101-8-11.png" alt="Games101-8-11" style="zoom:33%;" />

#### framebuffer阶段

将所有像素信息整合在一起，输出给屏幕显示，就完成了整个渲染管线

## shader

### shader

特点：

- 是一个通用的程序，不需要写循环
- 只需要写一个顶点或一个像素的处理就能应用到所有顶点或像素上面

分类：

- 对顶点的操作：顶点着色器vertex shader
- 对像素的操作：像素着色器pixel(or fragment) shader

举例：一个简单的像素着色器

<img src="https://raw.githubusercontent.com/PuLei36/notes/note/images/games101/Games101-8-12.png" alt="Games101-8-12" style="zoom:33%;" />

### GPU

高度并行的处理器（多少核就是可以并行的线程的数量）

## 纹理映射

### 引入原因

一个物体上可能有不同颜色要显示，需要引入一个参数来定义物体上**每一个点的基本属性**

### 纹理(texture)

前提：认为物体的表面都是二维的，一个三维物体的任意一个点都映射在一个二维平面上

<img src="https://raw.githubusercontent.com/PuLei36/notes/note/images/games101/Games101-8-13.png" alt="Games101-8-13" style="zoom: 33%;" />

纹理：一个二维平面对三维物体的映射图，每个三角形的顶点都规定了它在纹理上的坐标

<img src="https://raw.githubusercontent.com/PuLei36/notes/note/images/games101/Games101-8-14.png" alt="Games101-8-14" style="zoom:33%;" />

### 纹理坐标

通常使用 (u, v) 表示一个三维的点在二维纹理上的坐标

u/v 的范围都是[0, 1]

<img src="https://raw.githubusercontent.com/PuLei36/notes/note/images/games101/Games101-8-15.png" alt="Games101-8-15" style="zoom:33%;" />

### 特殊纹理设计

tiled texture：能够重复利用，并且左右衔接流畅的纹理

tile纹理有上下左右重复拼接都是连续的特性，多用在贴在墙面或地板上。

<img src="https://raw.githubusercontent.com/PuLei36/notes/note/images/games101/Games101-8-16.png" alt="Games101-8-16" style="zoom: 33%;" />