# 第三课 纹理综述

网易E星计划 游戏引擎课程 

## 纹理 Texture

表象上是使得模型具有更加丰富的颜色和质感，本质上是数据

总尺寸 = 单位尺寸 * 数量

纹素（Texel） = 纹理（Texture）的像素（pixel）

纹理编码成为图像，图像解码为纹理，即在渲染管线中成为纹理数据

## 纹理映射 Texture Mapping

即将纹理映射到三维模型上，在三角面中，一般使用UV坐标进行纹理映射，然后通过重心坐标进行插值

## 纹理加载 Texture loading

以下是两个在DX中使用的Device抽象

Device：资源处理

Device Context：渲染操作

- 创建纹理描述
- 初始化特征信息
- 读取图片数据，加载如内存
- 创建和初始化对象
- ShaderResourceView生成对对象的描述
- 绑定渲染管线
- 与Shader关联

## 纹理采样 Texture Sampling

处理纹理坐标超过区间时，需要明确Address的方法

- Address Wrap 平铺模式
- Address Mirror 镜像模式
- Address Clamp 裁剪模式
- Address Border 边缘模式 预先设置边缘颜色

## 采样过滤 Texture Sampling Filtering

- Nearest
- Bilinear

- minfilter 当纹理需要缩小时的采样
- magfilter 当纹理需要放大时的采样

## 透视采样 Perspective Sampling Filtering

### Mip Map

处理透视视角下不同距离的纹理的清晰程度，使用不同层级的mipmap处理不同远近程度

## 法线纹理的切线空间 Tangent Space of Normal Texture

下面先给出伪代码方案：

```c++
float3 tangentNormal = tNormalap.Sample(sNormalSampler, texCoord).xyz;

tangentNormal = (2.0 * tangentNormal) - 1.0;

float3 worldNormal.xyz = tangentNormal.x * worldTangent.xyz + tangentNormal.y * worldBinormal.xyz + tangentNormal.z * worldNormal.xyz;

worldNormal.xyz = normalize(worldNormal);
```

需要进行这一步处理的原因是，如果直接使用法线贴图，会忽略掉fragment本身的法线方向，而导致法线方向与实际的法线方向不同的问题。所以，法线贴图使用的其实是TBN空间，即切线空间。即，法线贴图的法线方向实际上是在片段的法线方向的基础上确定的。

![](https://github.com/LuniumLuk/docs-blog/raw/main/images/estar_02.png)
![](../../images/estar_02.png)

如上图所示，TBN空间分别为Tangent，Bitangent和Normal三个基底组成。

那么，给定三角形的三个顶点位置和三个顶点的纹理坐标，我们可以将切线空间的T和B基底和它们关联起来,其中$E_{ix}$表示第i个边（Edge）的x分量：

$$
E_0 = V_1 - V_0, E_1 = V_2 - V_1, E_2 = V_0 - V_1
$$

我们同样定义$\Delta{U}_i$和$\Delta{V}_i$为如下：

$$
\Delta{U}_0 = U_1 - U_0, \Delta{V}_0 = V_1 - V_0 , ...
$$

因此，我们有：

$$
E_0 = \Delta{U}_0 T + \Delta{V}_0 B \\
E_1 = \Delta{U}_1 T + \Delta{V}_1 B \\
$$

改写为矩阵形式：

$$
\begin{bmatrix}
    E_{0x} & E_{0y} & E_{0z} \\
    E_{1x} & E_{1y} & E_{1z} \\
\end{bmatrix}
=
\begin{bmatrix}
    \Delta{U}_0 & \Delta{V}_0 \\
    \Delta{U}_1 & \Delta{V}_1 \\
\end{bmatrix}
\begin{bmatrix}
    T_x & T_y & T_z \\
    B_x & B_y & B_z \\
\end{bmatrix}
$$

矩阵变换后得：

$$
\begin{bmatrix}
    T_x & T_y & T_z \\
    B_x & B_y & B_z \\
\end{bmatrix}
=
\frac{1}{
    \Delta{U}_0 \Delta{V}_1 - \Delta{U}_1 \Delta{U}_0
}
\begin{bmatrix}
    \Delta{V}_1 & -\Delta{V}_0 \\
    -\Delta{U}_1 & \Delta{U}_0 \\
\end{bmatrix}
\begin{bmatrix}
    E_{0x} & E_{0y} & E_{0z} \\
    E_{1x} & E_{1y} & E_{1z} \\
\end{bmatrix}

$$

## 其他

### MRT Multiple Render Targets

延迟光照渲染GBuffer，收集一些的渲染中间结果再进行渲染，比如GTA5使用了4张GBuffer（Diffuse，Normal，Specular，Irradiance）

### RWTexture

可读写纹理，再Compute Shader中使用

### 各向异性过滤 Anisotropic Filtering

考虑相机位置、平面方向、法向的过滤

### 颜色空间和伽马矫正

sRGB图片->采样结果->移除伽马->计算->伽马矫正->显示

线性空间图片->计算->伽马矫正->显示