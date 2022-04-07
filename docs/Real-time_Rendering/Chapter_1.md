# Real Time Rendering Note # 1

### 渲染管线

#### 应用阶段（Application Stage）

一般这一阶段在CPU上进行相对复杂的计算。主要责任是为后面的流程输出图元，也就是点、线和三角面等等信息。

#### 几何处理（Geometry Processing）

几何处理由四个阶段构成：顶点着色、投影（Projection）、裁剪（Clipping）和屏幕映射（Screen mapping）。

##### 1 顶点着色（Vertex Shader）

> 对于shader和shading这两个相似，相关但又有所区别的概念，还是需要进行一下甄别：shader是一种可编程单元，往往运行在GPU中；shading更多则认为是对图像的绘制以及呈现。

顶点着色器有两大任务：

1. 计算顶点的位置，一般而言，在这一步里将顶点从model坐标系转换到view坐标系。这一步一般是通过应用两个transform，也就是model matrix和view matrix。
2. 计算需要在顶点中输出的信息。为了得到好的渲染效果，顶点中会包含许多将应用在后续流程上的数据，比如法线、颜色、材质等等。

此外，在裁剪（clipping）前，一般vertex shader中还会进行project处理，也就是应用一个投影矩阵（正交投影或者透视投影）。最终得到的是一个四维向量，也就是齐次坐标，便于后续的处理。其中，z坐标储存在z-buffer中，w坐标则将被用于透视除法。这部分将在下一章详细介绍。

##### 2 可选阶段

此外，在顶点着色阶段之后，Real Time Rendering还描述了一些可选的阶段，包括：曲面细分（tessellation）、几何着色器（geometry shader）和输出流（stream output）。其中，曲面细分类似LoD，是在Vertex Shader之后根据在View Coordinates中与相机的远近，来对曲面图元进行细分。曲面细分阶段分为hull shader，tessellator和and domain shader。接下来是几何着色器，这个阶段是对图元进行处理生成新的顶点数据。比如，最常用与粒子的生成，比如，对于一个只包含一个顶点的粒子，几何着色器将其展开（生成）为由两个三角面构成的，类似广告版（billboard）的结构，传递给下一个管线。这样，使得粒子在后续的渲染过程中能得到更好的处理。最后一个可选阶段是输出流，也就是将一部分GPU中的数据输出到CPU中进行处理（同样，在粒子模拟中应用）。

##### 3 裁剪（Clipping）

裁剪阶段就是将部分和完全不在view volume内的图元进行处理。完全不在view volume内的图元进行舍弃，而部分在view volume内的图元则进行处理，即生成新的落在view volume边缘的节点，并保留原始图元的形状。

##### 4 屏幕映射（Screen Mapping）

在屏幕映射阶段，将裁剪后的图元坐标进行重新映射。xy坐标被重新映射为 $x\in[x1, x2],y\in[y_1, y_2]$ 也就是屏幕的左上角到右下角的坐标。而z坐标，也称为（window coordinates）被映射为0到1之间的浮点数，或者-1到1，根据不同的API有着不同的具体实现（OpenGL和DirectX就有着不同的实现）。

而对于浮点坐标和整数坐标的转换，我们有：
$$
d = floor(f) \\
f = d + 0.5
$$

#### 删格化（Rasterization）

删格化将经过转换的三维坐标和每个顶点的渲染数据转化为屏幕上的每一个像素，大致分为两个过程（图元组装和三角形遍历）。一般而言，判断一个像素是否位于一个三角形内，可以采用最简单的算法，即，采样像素中的一个点，判断其是否位于三角形内。当然，如果使用超分辨率算法或者反走样（Anti-aliasing）算法，则会采样超过一个点进行判断。此外，判断该像素位于三角形内部的比例也是一种方法。

##### 1 图元组装（primitive assembly）

图元组装也可以叫做准备三角形（triangle setup），在这一步将计算一些前置数据，比如一些微分结果、三角形的边方程等等。这些数据一般是为下一步，或者一些顶点数据的插值做准备。

##### 2 三角形遍历（triangle traversal）

在这一阶段，我们遍历所有处于三角形内的像素点，生成一个片段（fragment）。并将用于生成该像素点插值的数据传递给片段着色器。然后，这一阶段的结果将被送往下一个阶段，即像素处理阶段。

#### 像素处理（Pixel processing）

这里，Real-Time Rendering这本书将像素处理分为两部分，第一个是像素着色（Pixel Shading），第二个是合并（Merging）

##### 1 像素着色

简而言之，就是OpenGL中所谓的fragment shader，用于输出某一像素的颜色（单个或多个）。这一流程一般在可编程的GPU核心上运行，并且目前在这一阶段中存在多种算法，也能渲染出许多不同的效果。

##### 2 合并

这一阶段也称为**ROP（raster operations pipeline）**或者render output unit。这时候，颜色信息被储存在color buffer中，而深度信息，储存在depth buffer中，也就是z-buffer。z-buffer和color buffer有相同的大小。z-buffer用于控制该像素是否需要被新的像素覆盖，以及用于执行一些相关的算法。但是z-buffer的弱项是对透明或半透明物体的处理，这往往需要按顺序从远到近进行绘制，或者使用其他的算法。

此外，在color buffer中也可能有一些其他的通道，比如alpha通道。但是，现在的渲染管线中，alpha测试可以在fragment shader中进行，而不需要额外的buffer储存信息。stencil（模板）buffer，类似蒙版，用于储存一些图元相关的信息，在render的时候配合fragment shader使用。

- Color Buffer 用于显示
- Z-Buffer / Depth Buffer 用于深度测试（Depth Test）
- Alpha Buffer 用于Alpha测试（混合）
- Stencil Buffer

一般而言，合并阶段是部分可编程的，在一些API中甚至是固定管线。然后，color buffer的内容被输出到屏幕上，被人的眼睛所观察到。这里，为了防止被用户观察到图元的绘制过程，一般使用双缓存方法（double buffering），即，使用两张缓存，一张用于展示，一张在后台进行绘制，绘制完成后，进行切换（swap）操作。