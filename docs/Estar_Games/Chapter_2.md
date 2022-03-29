# 第一课 游戏引擎综述

网易E星计划 游戏引擎课程 3月24日

## GPU

基本功能
1. 图形绘制
2. 物理模拟 PhysX
3. 海量计算 CUDA
4. AI运算 AI Core/Tensor Core

### GPU架构

以NVIDIA Fermi为例

- 有16个**SM**（Stream Multiprocessor）
- 每个SM
  - 2个**Warp** Scheduler
  - 2个Dispatch Unit（分发单元）
  - 2个Warp（线程束）
  - 16组LD/ST（加载存储单元）
  - 4个SFU（特殊函数单元）
  - 128KB寄存器
  - 64KB Shared Memory / L1 缓存
  - Uniform Cache 全局缓存
- 每个Warp
  - **32个Core**
- 每个Core
  - 1个FPU（浮点数单元）
  - 1个ALU（算数逻辑单元）

存储架构
- 分离式：CPU和GPU通过PCI-E总线通讯
- 耦合式：由MMU进行储存管理，比如APU、游戏主机和移动设备

每个SM由寄存器，共享内存，L1缓存

存取速度从寄存器到显存依次变慢，即离Core越近越频繁
- 寄存器 1 生命周期：单个线程
- 共享内存 1～32 生命周期：线程组
- L1缓存 1～32 生命周期：单个线程
- L2缓存 32～64 生命周期：单个线程
- 纹理、常量缓存 400～600 生命周期：用户
- 全局内存 400～600 生命周期：用户

## 渲染管线

略

### 着色器

交叉编译：写一份语言，比如HLSL，编译为其他语言

Unity Shaderlab是封装语言

### 绘制一个三角形

- CPU
  - Graphics API
  - PushBuffer
- GPU
  - Host Interface
  - Front End
  - Primitive Distributor
  - ...

1 首先准备Vertex Data（Vertex Array / Index Array）

2 程序通过Graphics API发出DrawCall指令，指令送到驱动程序，检测合法性后，放到GPU的PushBuffer中

3 经过一段时间或调用flush指令后，驱动程序吧PushBuffer发送给GPU，GPU通过主机接口（Host Interface）接受不了命令，通过前端（Front End）处理指令

4 图元分配器（Primitive Distributor）通过index buffer生成三角形，将三角形分批次（Batches）发给SM

5 每个SM的Poly Morph Engine使用triangle indices取出三角形数据，获取数据后SM中以32个线程为一组（Warp）处理顶点数据

6 一旦warp完成了所有vertex shader和geometry shader指令，运算结果被进行视图转换（View Transform），三角形被裁剪然后准备光栅化

- Work Distribution Crossbar
  - Raster Engine
  - Attribute Setup
  - PS（Pixel Shader） / FS（Fragment Shader）

7 通过分析三角形占用屏幕面积，由work distribution crossbar分发到一个到多个Raster Engine做光栅化

8 属性设置（Attribute Setup）：计算插值，并保证数据是pixel shader需要的格式

9 像素着色（Pixel Shader），一般是2x2像素块进行处理

- Crossbar 渲染输出单元（ROP）
  - Alpha混合，模板测试等等

## 拓展内容

### TBDR

Immediate Model Rendering（IMR）传统渲染流程：直接处理到像素shader然后写入显存。但是由于Pixel Shader会频繁读取DRAM，而DRAM功耗较高

Tile Based Rendering（TBR）因此在做Depth Buffer Frame Buffer时将屏幕分成多个小块，直接将结果写在L1/L2 cache上，避免直接读写取DRAM。相比IMR，多一个Tilling阶段，则Visibility Test和Alpha Blend可以做在On-Chip上

#### Tile Based Deffered Rendering（TBDR）

先计算Depth Test，然后使用Tag Buffer，只上色不被剔除的图元

### GPU Driven

把CPU上的一些内容转移到GPU上做，比如：
1. Comute Shader - UE4 Nanite
2. NV Mesh Shader
   - 延迟顶点处理
   - MeshLets

### 光追

- DirectX Ray Tracing（DXR）
- NV Turing，RT Core
- Battlefield 5
- UE4 Real-Time Hybrid Ray Tracing
  - 光追用于处理复杂的反射，折射
  - 其他普通内容使用光栅化比较好