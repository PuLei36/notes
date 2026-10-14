# 04 变换（模型、视图、投影变换）

## MVP变换

以现实拍照为例：

- 找到一个合适的地方并安排好要拍的人—**模型变换**(model transformation)
- 找到合适的角度并放置相机—**视图/相机变换**(view/camera transformation)
- 茄子—**投影变换**(projection transformation)

## 观测变换(Viewing transformation)

### 视图/相机变换(View/Camera Transformation)

#### 定义一个相机

- 相机**位置**(position)—$\overrightarrow{e}$
- 相机的**看向方向**(look-at/gaze direction)—$\widehat{g}$
- 相机的**向上方向**(up direction)—$\widehat{t}$

<img src="https://raw.githubusercontent.com/PuLei36/notes/note/images/games101/Games101-4-1.png" alt="Games101-4-1" style="zoom:50%;" />

#### key observation

- 如果相机和其他所有物体都一起移动，则拍出来的照片都一样
- 约定：总是把相机放在原点，并把向上方向设为y轴，看向方向为-z轴（标准位置）
- 因此，首先需要将相机转换到标准位置，使用矩阵$M_{view}$

<img src="https://raw.githubusercontent.com/PuLei36/notes/note/images/games101/Games101-4-2.png" alt="Games101-4-2" style="zoom: 33%;" />

#### $M_{view}$ in math

- $M_{view}$=$R_{view}$$T_{view}$（先位移再旋转）

- 位移矩阵：
	$$
	T_{view}=
	 \begin{Bmatrix}
	 1&0&0&-tx\\
	 0&1&0&-ty\\
	 0&0&1&-tz\\
	 0&0&0&1\\
	 \end{Bmatrix}
	$$

- 旋转矩阵：正着写不好写，但是反过来很好写，即X->(g x t), Y->t, z->-g
	$$
	R_{view}^{-1}=
	 \begin{Bmatrix}
	 x_{\widehat{g}×\widehat{t}}&x_{t}&x_{-g}&0\\
	 y_{\widehat{g}×\widehat{t}}&y_{t}&y_{-g}&0\\
	 z_{\widehat{g}×\widehat{t}}&z_{t}&z_{-g}&0\\
	 0&0&0&1\\
	 \end{Bmatrix}
	 ->
	 R_{view}=
	  \begin{Bmatrix}
	 x_{\widehat{g}×\widehat{t}}&y_{\widehat{g}×\widehat{t}}&z_{\widehat{g}×\widehat{t}}&0\\
	 x_{t}&y_{t}&z_{t}&0\\
	 x_{-g}&y_{-g}&z_{-g}&0\\
	 0&0&0&1\\
	 \end{Bmatrix}
	$$

###  模型变换(Model Transformation)

由于”如果相机和其他所有物体都一起移动，则拍出来的照片都一样“这条性质

模型变换即为**把对相机进行的变化对模型再应用一次**，即$M_{model}$=$R_{model}$$T_{model}$

因此两者经常被统称**模型视图变换(ModelView Transformation)**

### 投影变换(Projection Transformation)

#### 透视投影 vs 正交投影

<img src="https://raw.githubusercontent.com/PuLei36/notes/note/images/games101/Games101-4-3.png" alt="Games101-4-3" style="zoom: 67%;" />

#### 正交投影(Orthographic Projection)

##### 特殊操作

1. 将相机放在原点，看向-Z方向，向上方向为Y方向
2. 扔掉Z轴（此时所有图形都被压缩至XoY平面）
3. 将得到的图形平移和缩放至$[-1,1]^2$

<img src="https://raw.githubusercontent.com/PuLei36/notes/note/images/games101/Games101-4-4.png" alt="Games101-4-4" style="zoom:50%;" />

##### 一般操作

- 我们需要把一个任意长方体[l,r] x [b,t] x [f,n]规范化为
	一个"canonical(正则、规范、标准)"立方体$[-1,1]^3$

<img src="https://raw.githubusercontent.com/PuLei36/notes/note/images/games101/Games101-4-5.png" alt="Games101-4-5" style="zoom: 33%;" />

- 规范化矩阵(先平移再缩放)
	$$
	M_{ortho}=
	 \begin{Bmatrix}
	 \frac{2}{r-l}&0&0&0\\
	 0&\frac{2}{t-b}&0&0\\
	 0&0&\frac{2}{n-f}&0\\
	 0&0&0&1\\
	 \end{Bmatrix}
	 \begin{Bmatrix}
	 1&0&0&-\frac{r+l}{2}\\
	 0&1&0&-\frac{t+b}{2}\\
	 0&0&1&-\frac{n+f}{2}\\
	 0&0&0&1\\
	 \end{Bmatrix}
	 =
	 \begin{Bmatrix}
	 \frac{2}{r-l}&0&0&-\frac{r+l}{r-l}\\
	 0&\frac{2}{t-b}&0&-\frac{t+b}{t-b}\\
	 0&0&\frac{2}{n-f}&-\frac{n+f}{n-f}\\
	 0&0&0&1\\
	 \end{Bmatrix}
	$$

- 在坐标系中近处的z值>远处的z值(n>f)，因为相机的看向方向是-Z

#### 透视投影(perspective projection)

##### 前置知识

(x, y, z, 1)=(kx, ky, kz, k!=0)=(xz, yz, $z^2$,z!=0)都是表示相同的三维坐标(x, y, z)

##### 透视投影步骤

1. 将透视投影挤压成方法体
2. 将长方体转换成正交投影

##### 将透视投影挤压成长方体

有相似三角形，得到$y'=\frac{n}{z}y$，$x'=\frac{n}{z}x$

<img src="https://raw.githubusercontent.com/PuLei36/notes/note/images/games101/Games101-4-6.png" alt="Games101-4-6" style="zoom: 33%;" />

此时有：
$$
M^{(4*4)}_{persp->ortho}
 \begin{Bmatrix}
 x\\
 y\\
 z\\
 1\\
 \end{Bmatrix}
 =
  \begin{Bmatrix}
 nx/z\\
 ny/z\\
 unknown\\
 1\\
 \end{Bmatrix}
 ==
 \begin{Bmatrix}
 nx\\
 ny\\
 unknown\\
 z\\
 \end{Bmatrix}
$$
通过已有的值可以得出转化矩阵的三行：
$$
M^{(4*4)}_{persp->ortho}=
 \begin{Bmatrix}
 n&0&0&0\\
 0&n&0&0\\
 ?&?&?&?\\
 0&0&1&0\\
 \end{Bmatrix}
$$
观察性质：

- 第三行只和z有关，和x、y无关，因此前两位是0
- **任何近处平面上的点在运算完后仍然不会变化**
- **任何远处平面上的点的z值在运算完后仍然不会变化**

近处平面上的点(x, y, n, 1)==(nx, ny, $n^2$, n)，此时转换矩阵的第三行乘该点：
$$
\begin{Bmatrix}
 0&0&A&B\\
 \end{Bmatrix}
 \begin{Bmatrix}
 x\\
 y\\
 n\\
 1\\
 \end{Bmatrix}
 =n^2
 ->
 An+B=n^2(1)
$$
远处平面的中心点(0, 0, f, 1)==(0, 0, $f^2$, f)，此时转换矩阵的第三行乘该点：
$$
\begin{Bmatrix}
 0&0&A&B\\
 \end{Bmatrix}
 \begin{Bmatrix}
 0\\
 0\\
 f\\
 1\\
 \end{Bmatrix}
 =f^2
 ->
 Af+B=f^2(2)
$$
由(1)(2)式得**A=n+f**，**B=-nf**，由此得到了完整的转换矩阵$M_{persp->ortho}$
$$
M^{(4*4)}_{persp->ortho}=
 \begin{Bmatrix}
 n&0&0&0\\
 0&n&0&0\\
 0&0&n+f&-nf\\
 0&0&1&0\\
 \end{Bmatrix}
$$

##### 将长方体转换成正交投影

转换公式：$M_{persp}=M_{ortho}M_{persp->ortho}$（先挤压再正交）
$$
M_{persp}= 
 \begin{Bmatrix}
 \frac{2}{r-l}&0&0&-\frac{r+l}{r-l}\\
 0&\frac{2}{t-b}&0&-\frac{t+b}{t-b}\\
 0&0&\frac{2}{n-f}&-\frac{n+f}{n-f}\\
 0&0&0&1\\
 \end{Bmatrix}
 \begin{Bmatrix}
 n&0&0&0\\
 0&n&0&0\\
 0&0&n+f&-nf\\
 0&0&1&0\\
 \end{Bmatrix}
 =
 \begin{Bmatrix}
 \frac{2n}{r-l}&0&0&0\\
 0&\frac{2n}{t-b}&0&0\\
 0&0&\frac{2n}{n-f}&\frac{2nf}{f-n}\\
 0&0&0&1\\
 \end{Bmatrix}
$$
