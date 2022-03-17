# 第一课 游戏引擎综述

网易E星计划 游戏引擎课程 3月17日

## 游戏引擎及其简史

游戏引擎 = 引擎核心组件（Core） + 工具（Tool Chain）

Renderer ～= 引擎核心组件 或者 引擎Runtime

游戏 = 引擎核心组件 + 内容（资源 + 玩法）

- 1993 DOOM
- 1998 UE 1.0
- 2005 UNITY 1.0

Core
- 动画
- 渲染
- 物理
- 脚本
- 网络
- AI
- 声音

Tools

## 渲染

### 虚拟几何体（Nanite）
- GPU Driven
- Mesh Cluster Rendering (Mesh Shader)
- Cluster with LOD UE5 nanite

## 动画

程序化角色动画
- 融合 Blending
- 反向动力学 Inverse Kinmatic
- 布娃娃 Ragdoll

## 物理

刚体模拟
- 刚体碰撞检测与响应

柔体模拟
- Beam NG 弹簧质点模型
- NVIDIA FleX 基于位置的动力学
- AMD FEMFX 有限元方法
- CD-MPM 材料质点方法 高精度网格
- 深度学习方法（不成熟）

物理引擎
- PhysX
- Havok
- Bullet
- Box2D

自研
- Jolt Physics
- Beam.NG
- UE5 Chaos

## AI 网络 脚本 音乐

# 答疑内容

【首次课程弹幕答疑集结】

1，每周代码量大概多少？
应该不到300行，但是很需要智慧的300行哟~

2，对设备有什么要求？
windows设备，i5以上，4G内存以上即可

3，只会c++会不会跟不上？
作业主要以C++和GLSL为主，内容较为基础，且有范例可以参考。剩余的就要靠同学们自己的努力了

4，小白单周耗时大概多少 -- 3-4小时

5，求技术视频的油管链接（by兰洋老师）
渲染： https://www.youtube.com/watch?v=NEzJH-JrAdw
光栅： https://www.youtube.com/watch?v=t7Ztio8cwqM
https://www.youtube.com/watch?v=Qx_AmlZxzVk

光照着色：https://www.youtube.com/watch?v=bsSveswnQik
GTA单帧：https://www.youtube.com/watch?v=ygndZ5eIFO4

mesh:
Vege: https://www.youtube.com/watch?v=wavnKZNSYqU
Hair: https://www.youtube.com/watch?v=ool2E8SQPGU
https://www.youtube.com/watch?v=Gk_mTHvBx1w
http://advances.realtimerendering.com/s2019/hair_presentation_final.pdf

mtl:
https://www.youtube.com/watch?v=j-A0mwsJRmk&t=663s
https://www.gdcvault.com/play/1018270/Next-Generation-Character
原神：https://www.youtube.com/watch?v=-JFyAdI_rO8

光追与混合渲染：
https://www.youtube.com/watch?v=CsBHQGN0Q-0
https://www.youtube.com/watch?v=Qx_AmlZxzVk
https://www.youtube.com/watch?v=Bk5HOtRwgd8