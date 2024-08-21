# Maths

## 和差化积与积化和差

$$
\begin{aligned}
\sin\alpha+\sin\beta &= 2\sin\frac{\alpha+\beta}{2}\cos\frac{\alpha-\beta}{2}\\
\cos\alpha+\cos\beta &= 2\cos\frac{\alpha+\beta}{2}\cos\frac{\alpha-\beta}{2}\\
\sin\alpha-\sin\beta &= 2\cos\frac{\alpha+\beta}{2}\sin\frac{\alpha-\beta}{2}\\
\cos\alpha-\cos\beta &= -2\sin\frac{\alpha+\beta}{2}\sin\frac{\alpha-\beta}{2}\\
\end{aligned}
$$

$$
\begin{aligned}
\sin\alpha\cos\beta &= \frac{1}{2}[\sin(\alpha+\beta)+\sin(\alpha-\beta)]\\
\cos\alpha\cos\beta &= \frac{1}{2}[\cos(\alpha+\beta)+\cos(\alpha-\beta)]\\
\cos\alpha\sin\beta &= \frac{1}{2}[\sin(\alpha+\beta)-\sin(\alpha-\beta)]\\
\sin\alpha\sin\beta &= -\frac{1}{2}[\cos(\alpha+\beta)-\cos(\alpha-\beta)]\\
\end{aligned}
$$

## 积分

$$
\lim_{n\to\infty}\sum_{k=1}^n{\dfrac{1}{n+k}} = \lim_{n\to\infty}\sum_{k=1}^n{\dfrac{1}{1+\frac{k}{n}}}=\int_0^1{\dfrac{1}{1+x}}dx=\ln2.
$$

## 常微分方程

- $\dfrac{dy}{dx}=f(x)g(y)$

$$
\int{\dfrac{dy}{g(y)}=\int{f(x)dx}+C}
$$

- $\dfrac{dy}{dx}=f(x,y)$

$$
\begin{aligned}
&\text{let}\ y = ux\\
f(x,y) &= f(x,ux)=\varphi(u)\\
\varphi(u) &= u+x\dfrac{du}{dx}\\
\int \dfrac{du}{\varphi(u)-u}&=\int \dfrac{dx}{x} + C=\ln|x| + C\\
\Phi(u)&=\Phi(\dfrac{y}{x}) = \ln|x| + C\\
\end{aligned}
$$

- $y'+p(x)y=q(x)$

$$
y = \dfrac{\displaystyle\int e^{\int p(x)dx} q(x)dx+C}{e^{\int p(x)dx}}
$$

- $y'+p(x)y=q(x)y^n$

$$
\begin{aligned}
&y'y^{-n}+p(x)y^{1-n} = q(x)\\
\text{let}\ &z = y^{1-n},\dfrac{dz}{dx}=(1-n)\dfrac{dy}{dx}y^{-n}\\
&\dfrac{1}{1-n}\dfrac{dz}{dx}+p(x)z=q(x)
\end{aligned}
$$

## Extended Euclidean Algorithm

| $q$  | $a$  | $b$  | $s_0$ | $s_1$ | $t_0$ | $t_1$ |
| :--: | :--: | :--: | :---: | :---: | :---: | :---: |
| $-$  | $25$ | $21$ |  $1$  |  $0$  |  $0$  |  $1$  |
| $1$  | $21$ | $4$  |  $0$  |  $1$  |  $1$  | $-1$  |
| $5$  | $4$  | $1$  |  $1$  | $-5$  | $-1$  |  $6$  |
| $4$  | $1$  | $0$  | $-5$  | $21$  |  $6$  | $-25$ |

$$
(-5) * 25 + 6 * 21 = 1
$$

## 渐进无偏估计

设$X_1, X_2, \cdots, X_n$是来自总体$X$的样本，且$E(X) = \mu$已知，$D(X)=\sigma^2$未知，则$\dfrac{1}{n}\displaystyle\sum_{i=1}^n(X_i-\mu)^2$是$\sigma^2$的无偏估计。

> $\forall i=1,2,,\cdots,n$，有$E[(X_i-\mu)^2]=\sigma^2$，则
> $$
> E\left(\dfrac{1}{n}\sum_{i=1}^n(X_i-\mu)^2\right) = \dfrac{1}{n}E\left(\sum_{i=1}^n(X_i-\mu)^2\right)=\dfrac{1}{n}n\sigma^2
> $$

设$X_1, X_2, \cdots, X_n$是来自总体$X$的样本，$E(X) = \mu$未知，$D(X)=\sigma^2$未知，则$\dfrac{1}{n}\displaystyle\sum_{i=1}^n(X_i-\overline{X})^2$不是$\sigma^2$的无偏估计。

> $$
> \begin{aligned}
> \dfrac{1}{n}\displaystyle\sum_{i=1}^n(X_i-\overline{X})^2 &= \dfrac{1}{n}\sum_{i=1}^n\left((X_i-\mu) + (\mu - \overline{X})\right)^2\\
> &= \dfrac{1}{n}\displaystyle\sum_{i=1}^n(X_i-\mu)^2 + 2(\overline{X}-\mu)(\mu-\overline{X}) + (\mu-\overline{X})^2\\
> &= \dfrac{1}{n}\displaystyle\sum_{i=1}^n(X_i-\mu)^2 -(\mu-\overline{X})^2\\
> &\leq \sigma^2
> \end{aligned}
> $$
>
> 当$\overline{X}\not=\mu$时，$\dfrac{1}{n}\displaystyle\sum_{i=1}^n(X_i-\overline{X})^2$会造成对方差的低估，因此需要缩小分母。

---

已知

$$
\begin{aligned}
E(\overline{X}) &= E(\dfrac{1}{n}\sum_{i=1}^nX_i) = \dfrac{1}{n}\sum_{i=1}^nE(X_i) = \dfrac{1}{n}n\mu = \mu\\
D(\overline{X}) &= D(\dfrac{1}{n}\sum_{i=1}^nX_i) = \dfrac{1}{n^2}D(\sum_{i=1}^nX_i) = \dfrac{1}{n^2}n\sigma^2 = \dfrac{1}{n}\sigma^2\\
E(\overline{X}^2) &= E(\overline{X})^2 + D(\overline{X}) = \mu^2 + \dfrac{1}{n}\sigma^2\\
\end{aligned}
$$

于是有

$$
\begin{aligned}
E\left(\sum_{i=1}^n(X_i-\overline{X})^2\right) 
&= E(\sum_{i=1}^nX_i^2-n\overline{X}^2)\\
&= \sum_{i=1}^nE(X_i^2) - nE(\overline{X}^2)\\
&= n(\mu^2 + \sigma^2) - n(\mu^2 + \dfrac{\sigma^2}{n})\\
&= (n - 1)\sigma^2\\
\end{aligned}
$$

最终得到$\dfrac{1}{n-1}\displaystyle\sum_{i=1}^n(X_i-\overline{X})^2$是$\sigma^2$的无偏估计。

> 由于$\dfrac{1}{n}\displaystyle\sum_{i=1}^n(X_i-\overline{X})^2=\dfrac{n}{n-1}\sigma^2$，且$\displaystyle\lim_{n\to\infty}\dfrac{n-1}{n}\sigma^2=\sigma^2$，则称$\dfrac{1}{n}\displaystyle\sum_{i=1}^n(X_i-\overline{X})^2$为$\sigma^2$的渐进无偏估计。
