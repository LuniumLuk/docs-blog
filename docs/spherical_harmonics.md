# Spherical harmonics

In rendering a realistic scene in computer graphics, skylight is implemented with a skybox which essentially a image mapped to a sphere whose radius is set to infinite. The common way of spped up this global illumination is to precompute a environment map that represent incident illumination at a point. But overall, it is still costly to compute even a 64 * 64 enviroment map.

We thus adopt spherical harmonics to represent enviroment map and focuses on the Lambertian component of the BRDF.

for a point in a surface, the irradiance E is a function of the surface normal only and given by a integral over the upper hemisphere $\Omega(n)$ :

$$
E(\mathbf{n}) = \int_{\Omega(\mathbf{n})}L(\omega)(\mathbf{n}\cdot\omega)d\omega
$$

Here, $\omega$ and $\mathbf{n}$ are unit direction vectors, therefore, we can describe E and L as a BRDF, i.e. a parameterization by a direction $(\theta,\phi)$

To approximate E, we derive a method adopting spherical harmonics. Harmonics are solutions to laplace's equation, and we take the spherical harmonic coefficients $Y_{lm}$ ，which are analogue on the sphere to the Fourier basis on line or circle. We utilize the first three layers of SH that is:

$$
Y_{lm} = \begin{cases}
\sqrt{2}K_{lm}cos(m\varphi)P_{lm}(cos(\theta)) & m>0 \\
\sqrt{2}K_{lm}cos(m\varphi)P_{l|m|}(cos(\theta)) & m<0 \\
K_{l0}P_{l0}cos(\theta) & m=0
\end{cases}
$$

where:

$$
K_{lm}=\sqrt{\frac{(2l+1)(l-|m|)!}{4\pi(l+m)!}}
$$

and P is calculated iteratively:

$$
P_{00}=1 \\
P_{mm} = (1-2m)P_{m-1,m-1} \\
p_{m+1,m} = (2m+1)zP_{mm} \\
p_{lm} = \frac{(2l-1)zP_{l-1,m}-(l+m-1)P_{l-2,m}}{l-m}
$$

In computer graphics, these coefficients are precomputed as fixed float values and we only take the first 9 coefficients, therefore, we have:

$$
\begin{align*}
(x,y,z) &= (sin\theta cos\phi,sin\theta sin\phi, cos\theta) \\
Y_{00}(\theta,\phi)&=0.282095 \\
(Y_{11};Y_{10};Y_{1,-1})(\theta,\phi) &= 0.488603(x;y;z) \\
(Y_{21};Y_{2,-1};Y_{2,-1})(\theta,\phi) &= 1.092548(xz;yz;xy) \\
Y_{20}(\theta,\phi) &= 0.315392(3z^2-1) \\
Y_{22}(\theta,\phi) &= 0.546274(x^2-y^2)
\end{align*}
$$

So, we can now write L and E in:

$$
L(\theta,\phi) = \sum_{l,m}L_{lm}Y_{lm}(\theta,\phi) \\
E(\theta,\phi) = \sum_{l,m}E_{lm}Y_{lm}(\theta,\phi)
$$

To simplify the calculation, we define $A = (\mathbf{n}\cdot\omega)$ , note that A is not dependent on $\phi$ , therefore, m = 0, and we only use l index:
$$
A(\theta) = max[cos(\theta),0]=\sum_l A_lY_{l0}(\theta,0)
$$
We have:
$$
E_{lm} = \sqrt{\frac{4\pi}{2l+1}}A_lL_{lm} \\
\hat{A_l}=\sqrt{\frac{4\pi}{2l+1}}A_l
$$
Therefore:
$$
E(\theta,\phi)=\sum_{l,m}\hat{A_l}L_{lm}Y_{lm}(\theta,\phi)
$$
Where $\hat{A_l}$ Can be calculated numerically:
$$
\hat{A_0}=3.141593 \quad \hat{A_1}=2.094395 \quad \hat{A_2}=0.785398 \\
\hat{A_3}=0 \quad \hat{A_4}=-0.130900 \quad \hat{A_5}=0 \quad \hat{A_6}=0.049087
$$
Because $\hat{A_l}$ decay quickly, for rendering, we only need to consider low-frequency lighting coefficients. More precisely: $l\leq2$ 

To calculate lunimance L, we do the following prefiltering stage to acquire all needed parameters $L_{lm}$:
$$
L_{lm} = \int_{\theta=0}^\pi\int_{\phi=0}^{2\pi}L(\theta,\phi)Y_{lm}(\theta,\phi)sin(\theta)d\theta d\phi
$$
which is in the form of integration over a unit sphere's surface area. In computer graphics, we implement this integral simply by sum of the input environment map L and weighted by function Y. Which takes $O(9S)$ where S is the size of environment map.


### Reference

[1] Ravi Ramamoorthi, Pat Hanrahan. An efficient representation for irradiance environment maps. SIGGRAPH 2001: 497-500

[2] Peter-Pike Sloan. Stupid Spherical Harmonics (SH) Tricks. GDC 2008 Lecture paper
