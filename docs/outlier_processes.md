# Outlier Processes

相关论文：

[1] Michael J. Black, Anand Rangarajan. On the unification of line processes, outlier rejection, and robust statistics with applications in early vision. Int. J. Comput. Vis. 19(1): 57-91 (1996)

[2] Sungjoon Choi, Qian-Yi Zhou, Vladlen Koltun. Robust reconstruction of indoor scenes. CVPR 2015: 5556-5565

[3] Donald Geman, George Reynolds. Constrained Restoration and the Recovery of Discontinuities. IEEE Trans. Pattern Anal. Mach. Intell. 14(3): 367-383 (1992)

### Line Processes

Line processes一般用于处理图像恢复时的非连续性检测问题，下面给出一个图像恢复上的例子：

在计算机视觉中，大部分重建问题都被视作最小二乘估计问题。比如输入一个带噪音的数据d，需要重建出平滑的表面u，则，可以使用下面这个能量项：
$$
E(u,d) = E_D(u,d) + E_s(u) = \sum_{s\in S}[(u_s-d_s)^2+\lambda\sum_{t\in\mathcal{G}_s}(u_s-u_t)^2]
$$
这里，前一项约束恢复图像u和输入图像d之间的距离，后一项是约束我们要重建的这个光滑平面的先验信息。但是，自然情况下，图像中往往包含许多不连续性，比如边缘区域等等。

为此，为能量项中添加spatial line process，即，在空间域上的非连续性检测。得到分段的重建平面：
$$
E(u,d,l) = \sum_{s\in S}((u_s-d_s)^2 \lambda\sum_{t\in \mathcal{G}_s}[(u_s-u_t)^2l_{s,t}+\Psi(l_{s,t})])
$$
这里，$0\leq l_{s,t} \leq C$ ，一般C取值为1，而 $\Psi(z)$ 是一个惩罚项函数，当z取值为0时，该函数值为1，z取值为1时，该函数值为0，例如 $\Psi(z) = (\sqrt{z} - 1)^2$ 。 

#### Robust Optimization of Pose Graph

在Choi的*Robust Reconstruction of Indoor Scenes*这篇paper中，就使用了line processes在室内场景重建任务中对Pose Graph进行优化。一个Pose Graph由顶点 $\{P_i\}$（这里P是三维片段，比如RGBD数据或者点云数据） 和边（edge） $\{R_i\}\cup \{T_{ij}\} $（Ri是将Ti向Ti+1对齐的一个刚性变换，而T则是两两顶点之间的associate trasform）组成，优化目标是得到一系列Poses $\mathbb{T} = \{T_i\}$ 在世界坐标中定位三维重建中获得的片段（fragments）。

其基本方法是通过优化下面这个能量项：
$$
E(\mathbb{T})=\sum_i{f(T_i.T_{i+1},R_i) + \sum_{i,j}{f(T_i,T_j,T_{ij})}}
$$
其中，f函数为：
$$
f(T_i,T_j,X) = \sum_{(p,q)\in\mathcal{K}_{i,j}}||T_ip-T_jq||^2
\\ \approx \sum_{(p,q)\in\mathcal{K}_{i,j}}||T_ip-T_jXp||^2
\\ = \sum_{(p,q)\in\mathcal{K}_{i,j}}||X^{-1}T_j^{-1}T_ip-p||^2
$$
上式中，$\mathcal{K}_{ij}$ 是片段i和j中的匹配点对。

这里的主要问题是，在注册片段（fragment registration）以及相机轨迹（trajectory）估计的时候，大部分的 $T_{ij}$ 其实是不准确的，最终会严重影响优化结果。

因此，paper中使用了line processes，即引入一个参数 $l_{ij}$ 和对应的惩罚函数 $\Psi(l_{ij})$ ，这里作为pose graph中edge的validity。也就是在图中检测三维片段之间的非连续性，修改后的能量项如下：
$$
E(\mathbb{T},\mathbb{L}) = \sum_i f(T_i,T_{i+1},R_i) + \sum_{i,j}l_{ij}f(T_i,T_j,T_{ij}) + \mu\sum_{i,j}\Psi(l_{ij})
$$
其中，$f(T_i,T_j,X)$ 是alignment项，衡量两个Pose和relative Pose之间的非连续性；$\Psi(l_{ij})$ 是先验项，表示pose graph中某条边的可信度。$l_{ij}$ 的值会随着迭代的过程不断调整，作为初始值，一般会取值为1，意味着最大化pose graph中可信的边数。但是当某项的约束导致三维重建中出现误差，并导致alignment项增加，$l_{ij}$ 会逐渐（平滑地）减少，并且当值小于一定的阈值时，将会对对应的边进行剪枝。

### Robust Statistics

Robust Statistics的目的是1）描述一个最能符合一组数据的结构；2）能判别出离群值（outlier）或者离群的子结构（substructures）。更具体地说，就是寻找一组参数 $a = [a_0,...,a_n]$ 使得一个模型 $u(s; a)$ 最能符合一组数据的观测值 $d = \{d_0, ..., d_S\}$  ，也就是最小化其残差和：
$$
\min_{a}\sum_{s\in S}\rho(d_s - u(s;a),\sigma_s)
$$
这里，$\sigma_s$ 是一个归一化参数（scaler parameter），$\rho$ 是误差范式（error norm），该函数要使得误差在数据度量内均匀分布。最优的 $\rho$ 函数是平方函数，即：
$$
\rho(d_s-u(s;a),\sigma_s)=\frac{(d_s-u(s,a))^2}{2\sigma_s^2}
$$
而这就是标准的最小二乘估计问题，其形式上与最大似然（maximum-likelihood）估计相同。不同的 $\rho$ 函数对应不同的robust estimator以及其对离群值的敏捷性对应其robustness。众所周知，平方误差函数对离群值及其敏感，其 $\Psi$ 函数（用于衡量某个特定的观测值对其结果的影响，正比于其导数）线性增长并且无界。因此，为了提升鲁棒性，也就是降低其对离群值的敏感度（提升其对离群值的包容度）。因此，衍生出许多为此目的而设计的estimator，比如minimax estimator：
$$
\rho_{\epsilon}(x)=\begin{cases}
\frac{x^2}{2\epsilon}+\frac{\epsilon}{2}&|x|\leq\epsilon,\\
|x|&|x|>\epsilon,
\end{cases} \\
\Psi_{\epsilon}(x)=\begin{cases}
\frac{x}{\epsilon}&|x|\leq\epsilon,\\
sign(x)&|x|>\epsilon,
\end{cases} \\
$$
此外，还有Lorentzian和Skipped mean estimator等，都是为解决鲁棒性而设计的误差解算子。

### Robust Regularization

对于图像surface recovery任务，我们有：
$$
E(u,d) = \sum_{s\in S}[(u_s-d_s)^2 + \lambda\sum_{t\in\mathcal{G}_s}(u_s-t_s)^2]
$$
其中，第一项我们称为数据项（data term）而第二项则是空间项（spatial term），而将我们之前的 $\rho$ 函数应用在上式，我们有：
$$
E(u,d) = \sum_{s\in S}[\rho_D(u_s-d_s, \sigma_D) + \lambda\sum_{t\in\mathcal{G}_s}\rho_S(u_s-t_s, \sigma_S)]
$$
该方法也被应用于optical flow estimation上。