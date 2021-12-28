# Freeform Deformation

Reference : Botsch, An Intuitive Framework for Real-Time Freeform Modeling

目的：简化用户交互，即使对复杂的网格。用户首先设置边界约束（Boundary constraints），然后使用一个9-dof的manipulator来控制物体。

论文认为，CAD中对几何的处理最重要的还是用户能方便地给出对物体形状的预想（metaphor），并且能实时观察到结果。在技术上的问题是，可能的几何形状（形变）空间十分巨大，而且维度极高。用户的操作不外乎对一个顶点或者一组顶点进行3-dof的平移操作，或者6-dof的刚性变换或者9-dof的更加复杂的变换（具有更高的要求，以及更多的handle）。

