# Maths

# 基础公式

## 希腊字母表

|     音标     | 希腊字母大写 | 希腊字母小写  | LaTeX 大写 |  LaTeX 小写   |
| :----------: | :----------: | :-----------: | :--------: | :-----------: |
|   /ˈælfə/    |   $\Alpha$   |   $\alpha$    |  `\Alpha`  |   `\alpha`    |
|   /ˈbeɪtə/   |   $\Beta$    |    $\beta$    |  `\Beta`   |    `\beta`    |
|   /ˈɡæmə/    |   $\Gamma$   |   $\gamma$    |  `\Gamma`  |   `\gamma`    |
|   /ˈdeltə/   |   $\Delta$   |   $\delta$    |  `\Delta`  |   `\delta`    |
|  /ˈepsɪlɒn/  |  $\Epsilon$  |  $\epsilon$   | `\Epsilon` |  `\epsilon`   |
|  /ˈepsɪlɒn/  |  $\Epsilon$  | $\varepsilon$ | `\Epsilon` | `\varepsilon` |
|   /ˈzeɪtə/   |   $\Zeta$    |    $\zeta$    |  `\Zeta`   |    `\zeta`    |
|   /ˈiːtə/    |    $\Eta$    |    $\eta$     |   `\Eta`   |    `\eta`     |
|   /ˈθiːtə/   |   $\Theta$   |   $\theta$    |  `\Theta`  |   `\theta`    |
|   /ˈθiːtə/   |   $\Theta$   |  $\vartheta$  |  `\Theta`  |  `\vartheta`  |
|  /aɪˈəʊtə/   |   $\Iota$    |    $\iota$    |  `\Iota`   |    `\iota`    |
|   /ˈkæpə/    |   $\Kappa$   |   $\kappa$    |  `\Kappa`  |   `\kappa`    |
|   /ˈkæpə/    |   $\Kappa$   |  $\varkappa$  |  `\Kappa`  |  `\varkappa`  |
|   /ˈlæmdə/   |  $\Lambda$   |   $\lambda$   | `\Lambda`  |   `\lambda`   |
|    /mjuː/    |    $\Mu$     |     $\mu$     |   `\Mu`    |     `\mu`     |
|    /njuː/    |    $\Nu$     |     $\nu$     |   `\Nu`    |     `\nu`     |
|    /zaɪ/     |    $\Xi$     |     $\xi$     |   `\Xi`    |     `\xi`     |
| /əʊˈmaɪkrɒn/ |  $\Omicron$  |  $\omicron$   | `\Omicron` |  `\omicron`   |
|    /paɪ/     |    $\Pi$     |     $\pi$     |   `\Pi`    |     `\pi`     |
|    /paɪ/     |    $\Pi$     |   $\varpi$    |   `\Pi`    |   `\varpi`    |
|    /rəʊ/     |    $\Rho$    |    $\rho$     |   `\Rho`   |    `\rho`     |
|    /rəʊ/     |    $\Rho$    |   $\varrho$   |   `\Rho`   |   `\varrho`   |
|   /ˈsɪɡmə/   |   $\Sigma$   |   $\sigma$    |  `\Sigma`  |   `\sigma`    |
|   /ˈsɪɡmə/   |   $\Sigma$   |  $\varsigma$  |  `\Sigma`  |  `\varsigma`  |
|    /tɔː/     |    $\Tau$    |    $\tau$     |   `\Tau`   |    `\tau`     |
| /ˈjuːpsɪlɒn/ |  $\Upsilon$  |  $\upsilon$   | `\Upsilon` |  `\upsilon`   |
|    /faɪ/     |    $\Phi$    |    $\phi$     |   `\Phi`   |    `\phi`     |
|    /faɪ/     |    $\Phi$    |   $\varphi$   |   `\Phi`   |   `\varphi`   |
|    /kaɪ/     |    $\Chi$    |    $\chi$     |   `\Chi`   |    `\chi`     |
|    /psaɪ/    |    $\Psi$    |    $\psi$     |   `\Psi`   |    `\psi`     |
|  /ˈəʊmɪɡə/   |   $\Omega$   |   $\omega$    |  `\Omega`  |   `\omega`    |

## 扩展欧几里得算法

| $q$  | $a$  | $b$  | $s_0$ | $s_1$ | $t_0$ | $t_1$ |
| :--: | :--: | :--: | :---: | :---: | :---: | :---: |
| $-$  | $25$ | $21$ |  $1$  |  $0$  |  $0$  |  $1$  |
| $1$  | $21$ | $4$  |  $0$  |  $1$  |  $1$  | $-1$  |
| $5$  | $4$  | $1$  |  $1$  | $-5$  | $-1$  |  $6$  |
| $4$  | $1$  | $0$  | $-5$  | $21$  |  $6$  | $-25$ |

$$
(-5) * 25 + 6 * 21 = 1
$$

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

# 微积分

## 积分

$$
\lim_{n\to\infty}\sum_{k = 1}^n{\dfrac{1}{n+k}} = \lim_{n\to\infty}\sum_{k = 1}^n{\dfrac{1}{1+\frac{k}{n}}}=\int_0^1{\dfrac{1}{1+x}}dx =\ln2.
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
f(x, y) &= f(x, ux)=\varphi(u)\\
\varphi(u) &= u+x\dfrac{du}{dx}\\
\int \dfrac{du}{\varphi(u)-u}&=\int \dfrac{dx}{x} + C =\ln|x| + C\\
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
&\dfrac{1}{1-n}\dfrac{dz}{dx}+p(x)z = q(x)
\end{aligned}
$$



## 多元函数微积分

- 方向导数

对于多元函数 $f(x_1, x_2, \cdots, x_n)$，在点 $\mathbf{x}_0=(x_{01}, x_{02}, \cdots, x_{0n})$ 处，沿单位向量 $\mathbf{v} = (v_1, v_2, \cdots, v_n)$ 的方向导数记为

$$
D_{\mathbf{v}} f(\mathbf{x}_0) = \lim_{t \to 0^+} =  \dfrac{f(\mathbf{x}_0 + t\mathbf{v}) - f(\mathbf{x}_0)}{t}
$$

如果函数在 $\mathbf{x}_0$ 处可微，则

$$
D_{\mathbf{v}} f(\mathbf{x}_0) = \nabla f(\mathbf{x}_0) \cdot \mathbf{v}
$$

---

# 概率论与数理统计

## 基本概念

### 常用统计量

- 样本均值 $\overline{X} = \dfrac{1}{n}\displaystyle\sum^{n}_{i=1}$，观测值 $\overline{x} = \dfrac{1}{n}\displaystyle\sum_{i=1}^{n}x_i$.

- 样本方差 $S^2 = \dfrac{1}{n-1}\displaystyle\sum_{i=1}^{n}(X_i - \overline{X})^2 = \dfrac{1}{n-1}(\displaystyle\sum_{i=1}^nX_i^2 - n \overline{X}^2)$

- 样本标准差 $S = \sqrt{S^2}$

- 样本 $k$ 阶原点矩 $A_k = \dfrac{1}{n}\displaystyle\sum_{i=1}^nX_i^k$

- 样本 $k$ 阶中心距 $B_k = \dfrac{1}{n}\displaystyle\sum_{i=1}^n(X_i - \overline{X})^k$

**定理**：对总体 $X$ 有 $E(X) = \mu, D(X) = \sigma^2$；从总体中取出样本，$E(\overline{X}) = \mu, D(\overline{X}) = \dfrac{\sigma^2}{n},E(S^2) = \sigma^2$

### 三种分布

#### $\chi^2$ 分布

若 $X_1,X_2\cdots X_n$ 独立同分布，且满足 $X\sim N(0,1)$，则随机变量 $X^2$ 满足 $X\sim \chi^2(n)$，其中 $n$ 为自由度。

- 若 $X\sim N(\mu, \sigma)$，则 $\dfrac{1}{\sigma^2}\displaystyle\sum_{i=1}^n(X_i-\mu)\sim \chi^2(n)$

- $E(x) = n, D(X) = 2n$

- 若 $X\sim \chi^2(m),Y\sim \chi^x(n)$，则 $X+Y\sim \chi^2(m+n)$

- 对于 $0 < \alpha < 1$，当 $P(X^2 \geq \chi^2_{\alpha}(n)) = \alpha$ 称为 $\chi_{\alpha}^2(n)$ 的 $\alpha$ 的上侧分位数

#### $t$ 分布

若 $X,Y$ 独立分布，$X\sim N(0,1), Y\sim \chi^(n)$，则 $T = \dfrac{X}{\sqrt{Y/n}}$ 称为 $t(n)$ 分布（密度函数关于 $y$ 轴对称）

- 对于 $0 < \alpha < 1$，当 $P(t \geq t_{\alpha}(n)) = \alpha$ 称为 $t_{\alpha}(n)$ 的 $\alpha$ 的上侧分位数

#### $F$ 分布

若 $X\sim \chi^2(m), Y\sim \chi^2(n)$ 相互独立，则 $F = \dfrac{X/m}{Y/n}$ 称为 $F(m,n)$ 分布

- 设 $X\sim F(m,n)$，则 $\dfrac{1}{X}\sim F(n,m)$

- 对于 $0< \alpha < 1$，当 $P(F \geq F_{\alpha}(m,n)) = \alpha$ 称为 $F_{\alpha}(m,n)$ 的上侧分位数

### 正态总体统计量的分布

#### 单变量

若 $X\sim N(\mu, \sigma^2)$，有

- $\overline{X} \sim N(\mu, \dfrac{\sigma^2}{n})$，$\dfrac{\overline{X} - \mu}{\sigma / \sqrt{n}}\sim N(0,1)$
- $\dfrac{1}{\sigma^2}\displaystyle\sum_{i=1}^n(X_i - \mu)^2\sim \chi^2(n)$
- $\overline{X}$ 与 $S^2$ 相互独立，且 $\dfrac{(n-1)S^2}{\sigma^2}\sim \chi^2(n-1)$

> $X^2 = \displaystyle\sum_{i=1}^n (\dfrac{X_i-\overline{X}}{\sigma})^2$，但是 $\displaystyle\sum _{i=1}^n(\dfrac{X_i-\overline{X}}{\sigma}) = 0$ 是一个约束，故自由度从 $n$ 减为 $n-1$.

- $\dfrac{\overline{X} - \mu}{S/\sqrt{n}} \sim t(n-1)$

> $z = \dfrac{\overline{X} - \mu}{\sigma / \sqrt{n}}\sim N(0,1)$，$\chi^2 = \dfrac{(n-1)S^2}{\sigma^2}\sim \chi^2(n-1)$ 满足相互独立，则 $t = \dfrac{z}{\sqrt{\frac{\chi^2}{n-1}}}$ 是自由度为 $n-1$ 的 $t$ 分布

#### 双变量

若 $X\sim N(\mu, \sigma^2),Y\sim N(0,1)$，有

- $\dfrac{\overline{X} - \overline{Y} - (\mu_1 - \mu_2)}{\sqrt{\frac{\sigma_1^2}{m}+\frac{\sigma_2^2}{n}}}\sim N(0,1)$

- $\dfrac{\overline{X} - \overline{Y} - (\mu_1 - \mu_2)}{S_w\sqrt{\frac{1}{m} + \frac{1}{n}}}\sim t(m+n-2),其中S_w^2 = \sqrt{\dfrac{(m-1)S_1^2+(n-1)S_2^2}{m+n-2}}$
- $\dfrac{S_1^2/S^2_2}{\sigma_1^2/\sigma_2^2}\sim F(m-1,n-1)$

> $\dfrac{(n-1)S_1^2}{\sigma_1^2}\sim \chi^2(m-1)$, $\dfrac{(n-1)S_2^2}{\sigma_2^2}\sim \chi^2(n-1)$ 两者相互独立

## 参数估计

### 点估计

#### 矩估计

用样本均值 $\overline{X}$ 代替总体均值 $\mu$，用 **样本的二阶中心距** 代替总体方差 $\sigma^2$.

#### 极大似然估计

选择能够使概率取到极值的参数

$$
\begin{aligned}
&P(X = x) = f(x;\theta)\\
极大似然函数&L_n(\theta) = \displaystyle\prod_{i = 1}^{n}f(x_i;\theta)\\
解方程&\dfrac{d}{d\theta}\ln {L_n(\theta)} = 0\\
\end{aligned}
$$

#### 衡量点估计的标准

- 无偏性：$E(\hat{\theta}) = \theta$
- 有效性：若 $D(\hat{\theta_1}) < D(\hat{\theta_2})$，则 $\theta_1$ 比 $\theta_2$ 更有效
- 一致性：$\displaystyle\lim_{n \to \infty}P(\lvert \hat{\theta_n} - \theta\rvert < \varepsilon) = 1$

> 样本均值是总体均值的无偏且一致的估计量；
>
> 样本方差是总体方差的无偏且一致估计量

### 区间估计

#### 单正态总体下的参数区间估计

**用枢轴量法求参数参数 $\mu$ 的置信水平为 $1-\alpha$ 的置信区间**

- $\sigma$ 已知

构造 $\overline{X}$ 和 $\mu$ 的函数

$$
\begin{aligned}
&Z = \dfrac{\overline{X}-\mu}{\sigma/\sqrt{n}}\sim N(0,1)\\
对给定\alpha，取 a < b，满足&P(a\leq  \dfrac{\overline{X}-\mu}{\sigma/\sqrt{n}} \leq b) = 1-\alpha\\
为使置信区间最短，取&a = -z_{\frac{\alpha}{2}}, b = z_{\frac{\alpha}{2}}\\
则\mu 的置信水平为 1-\alpha 的置信区间为&[\overline{X}-\dfrac{\sigma}{\sqrt{n}}z_{\frac{\alpha}{2}},\overline{X}+\dfrac{\sigma}{\sqrt{n}}z_{\frac{\alpha}{2}}]\\
\end{aligned}
$$

- $\sigma$ 未知

用样本标准差代替总体标准差，构造 $\overline{X}$ 和 $\mu$ 的函数

$$
\begin{aligned}
&T = \dfrac{\overline{X}-\mu}{S/\sqrt{n}} \sim t(n-1)\\
对给定\alpha，取 a < b，满足&P(a\leq  \dfrac{\overline{X}-\mu}{S/\sqrt{n}} \leq b) = 1-\alpha\\
t 分布也是对称分布，为使区间长度最短，取&a = -t_{\frac{\alpha}{2}}(n-1), b = t_{\frac{\alpha}{2}}(n-1)\\
则\mu 的置信水平为 1-\alpha 的置信区间为&[\overline{X} - \dfrac{S}{\sqrt{n}}t_{\frac{\alpha}{2}}(n-1),\overline{X} + \dfrac{S}{\sqrt{n}}t_{\frac{\alpha}{2}}(n-1)]\\
\end{aligned}
$$

**用枢轴量法求参数参数 $\sigma^2$ 的置信水平为 $1-\alpha$ 的置信区间**

- $\mu$ 已知

参数 $\sigma^2$ 的无偏估计为 $\hat{\sigma^2} = \dfrac{1}{n}\displaystyle\sum_{i = 1}^n(X_i - \mu)^2$ 构造 $\dfrac{1}{n}\displaystyle\sum_{i=1}^n(X_i - \mu)^2$ 和参数 $\sigma^2$ 的函数

$$
\begin{aligned}
&G = \dfrac{n\hat{\sigma^2}}{\sigma^2} = \dfrac{\displaystyle\sum_{i = 1}^n (X_i - \mu)^2}{\sigma^2}\sim \chi^2(n)\\
对给定\alpha，取 a < b，满足&P(a\leq  \dfrac{\displaystyle\sum_{i = 1}^n (X_i - \mu)^2}{\sigma^2} \leq b) = 1-\alpha\\
通常选取&P(G < a) = P(G > b) = \dfrac{\alpha}{2}, 即 a = \chi_{1-\alpha /2}^2(n), b = \chi_{\alpha / 2}^2(n)\\
由&\chi_{1-\alpha /2}^2(n) \leq \dfrac{\displaystyle\sum_{i = 1}^n (X_i - \mu)^2}{\sigma^2} \leq \chi_{\alpha / 2}^2(n)\\
求得置信区间为&\bigg [\dfrac{\displaystyle\sum_{i = 1}^n (X_i-\mu)^2}{\chi_{\alpha / 2}^2(n)}, \dfrac{\displaystyle\sum_{i = 1}^n (X_i-\mu)^2}{\chi^2_{1-\alpha/ 2}(n)}\bigg]\\
\end{aligned}
$$

- $\mu$ 未知

由于 $\mu$ 未知，参数 $\sigma^2$ 的无偏估计为 $S^2 = \dfrac{1}{n-1}\displaystyle\sum_{i = 1}^n(X_i - \overline{X})^2$，构造 $S^2$ 和 $\sigma^2$ 的函数

$$
\begin{aligned}
&\chi^2 = \dfrac{(n-1)S^2}{\sigma^2}\sim \chi^2(n-1)\\
类似地，可以得到置信区间为&\bigg [\dfrac{\displaystyle\sum_{i = 1}^n (X_i-\mu)^2}{\chi_{\alpha / 2}^2(n-1)}, \dfrac{\displaystyle\sum_{i = 1}^n (X_i-\mu)^2}{\chi^2_{1-\alpha/ 2}(n-1)}\bigg]
\end{aligned}
$$

|   待估参数   |   待估参数   |                  $G(\hat{\theta}, \theta)$                   |                         双侧置信区间                         |
| :----------: | :----------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|  均值 $\mu$   | $\sigma$ 已知 | $G = \dfrac{(\overline{X}-\mu)}{\sigma/\sqrt{n}}\sim N(0,1)$ | $[\overline{X}-\dfrac{\sigma}{\sqrt{n}}z_{\frac{\alpha}{2}},\overline{X}+\dfrac{\sigma}{\sqrt{n}}z_{\frac{\alpha}{2}}]$ |
|  均值 $\mu$   | $\sigma$ 未知 |   $G = \dfrac{(\overline{X}-\mu)}{S/\sqrt{n}}\sim t(n-1)$    | $[\overline{X} - \dfrac{S}{\sqrt{n}}t_{\frac{\alpha}{2}}(n-1),\overline{X} + \dfrac{S}{\sqrt{n}}t_{\frac{\alpha}{2}}(n-1)]$ |
| 方差 $\sigma$ |  $\mu$ 已知   | $G = \dfrac{1}{\sigma^2}\displaystyle\sum_{i=1}^n(X_i-\mu)^2\sim \chi^2(n)$ | $\bigg[\dfrac{\displaystyle\sum_{i=1}^n (X_i-\mu)^2}{\chi_{\alpha / 2}^2(n)}, \dfrac{\displaystyle\sum_{i=1}^n (X_i-\mu)^2}{\chi^2_{1-\alpha/ 2}(n)}\bigg]$ |
| 方差 $\sigma$ |  $\mu$ 未知   |       $G = \dfrac{(n-1)S^2}{\sigma^2}\sim \chi^2(n-1)$       | $\bigg[\dfrac{\displaystyle\sum_{i=1}^n (X_i-\mu)^2}{\chi_{\alpha / 2}^2(n-1)}, \dfrac{\displaystyle\sum_{i=1}^n (X_i-\mu)^2}{\chi^2_{1-\alpha/ 2}(n-1)}\bigg]$ |

#### 两个正态总体下的参数区间估计

|                待估参数                |                    待估参数                    |                  $G(\hat{\theta}, \theta)$                   |    双侧置信区间     |
| :------------------------------------: | :--------------------------------------------: | :----------------------------------------------------------: | :-----------------: |
|          均值差 $\mu_1-\mu_2$           |            $\sigma_1,\sigma_2$ 已知             | $G = \dfrac{\overline{X} - \overline{Y}-(\mu_1 - \mu_2)}{\sqrt{\frac{\sigma_1^2}{m} + \frac{\sigma_2^2}{n}}}\sim N(0,1)$ | 不背， 现推:sleepy: |
|         均值差 $\mu_1 - \mu_2$          | $\sigma_1=\sigma_2=\sigma^2$，但 $\sigma^2$ 未知 | $G = \dfrac{\overline{X} - \overline{Y}-(\mu_1 - \mu_2)}{S_w\sqrt{\frac{1}{m} + \frac{1}{n}}}\sim t(m+n-2)$ |      $\cdots$       |
| 方差比 $\dfrac{\sigma_1^2}{\sigma_2^2}$ |               $\mu_1, \mu_2$ 已知               | $\dfrac{\hat{\sigma^2_1}/\hat{\sigma_2^2}}{\sigma_1^2/\sigma_2^2}\sim F(m.n)$ |      $\cdots$       |
| 方差比 $\dfrac{\sigma_1^2}{\sigma_2^2}$ |               $\mu_1, \mu_2$ 未知               | $G = \dfrac{S_1^2/S_2^2}{\sigma_1^2/\sigma_2^2}\sim F(m-1,n-1)$ |      $\cdots$       |

## 假设检验

1. 根据问题提出原假设 $H_0$ 和备择假设 $H_1$；
2. 选择统计量，在原假设成立的条件下确定统计量的分布；
3. 根据 $\alpha$ 确定统计量对应的临界值；
4. 根据样本观测值计算统计量的观测值与临界值比较，选择接受或拒绝原假设 $H_0$；

### 两类错误

- 弃真：当且仅当小概率事件 $A$ 发生时才拒绝 $H_0$，$P(A|H_0)\leq \alpha$
- 取伪：当 $\alpha$ 确定后，可以通过增加样本容量来减小取伪概率

**显著性检验** 只对第一类错误的概率加以控制，而不考虑犯第二类错误的概率，称犯第一类错误的概率 $\alpha$ 为 **显著性水平**

> 后果严重的作为原假设

### 正态总体的假设检验

> 单边检验：拒绝域与备择假设的不等号方向一致


## 渐进无偏估计

> 贝塞尔校正

设 $X_1, X_2, \cdots, X_n$ 是来自总体 $X$ 的样本，且 $E(X) = \mu$ 已知，$D(X)=\sigma^2$ 未知，则 $\dfrac{1}{n}\displaystyle\sum_{i=1}^n(X_i-\mu)^2$ 是 $\sigma^2$ 的无偏估计。

> $\forall i=1,2,,\cdots,n$，有 $E[(X_i-\mu)^2]=\sigma^2$，则
>
> $$
> E\left(\dfrac{1}{n}\sum_{i = 1}^n(X_i-\mu)^2\right) = \dfrac{1}{n}E\left(\sum_{i = 1}^n(X_i-\mu)^2\right)=\dfrac{1}{n}n\sigma^2
> $$

设 $X_1, X_2, \cdots, X_n$ 是来自总体 $X$ 的样本，$E(X) = \mu$ 未知，$D(X)=\sigma^2$ 未知，则 $\dfrac{1}{n}\displaystyle\sum_{i=1}^n(X_i-\overline{X})^2$ 不是 $\sigma^2$ 的无偏估计。

> $$
> \begin{aligned}
> \dfrac{1}{n}\displaystyle\sum_{i = 1}^n(X_i-\overline{X})^2 &= \dfrac{1}{n}\sum_{i = 1}^n\left((X_i-\mu) + (\mu - \overline{X})\right)^2\\
> &= \dfrac{1}{n}\displaystyle\sum_{i = 1}^n(X_i-\mu)^2 + 2(\overline{X}-\mu)(\mu-\overline{X}) + (\mu-\overline{X})^2\\
> &= \dfrac{1}{n}\displaystyle\sum_{i = 1}^n(X_i-\mu)^2 -(\mu-\overline{X})^2\\
> &\leq \sigma^2
> \end{aligned}
> $$
>
> 当 $\overline{X}\not=\mu$ 时，$\dfrac{1}{n}\displaystyle\sum_{i=1}^n(X_i-\overline{X})^2$ 会造成对方差的低估，因此需要缩小分母。

---

已知

$$
\begin{aligned}
E(\overline{X}) &= E(\dfrac{1}{n}\sum_{i = 1}^nX_i) = \dfrac{1}{n}\sum_{i = 1}^nE(X_i) = \dfrac{1}{n}n\mu = \mu\\
D(\overline{X}) &= D(\dfrac{1}{n}\sum_{i = 1}^nX_i) = \dfrac{1}{n^2}D(\sum_{i = 1}^nX_i) = \dfrac{1}{n^2}n\sigma^2 = \dfrac{1}{n}\sigma^2\\
E(\overline{X}^2) &= E(\overline{X})^2 + D(\overline{X}) = \mu^2 + \dfrac{1}{n}\sigma^2\\
\end{aligned}
$$

于是有

$$
\begin{aligned}
E\left(\sum_{i = 1}^n(X_i-\overline{X})^2\right)
&= E(\sum_{i = 1}^nX_i^2-n\overline{X}^2)\\
&= \sum_{i = 1}^nE(X_i^2) - nE(\overline{X}^2)\\
&= n(\mu^2 + \sigma^2) - n(\mu^2 + \dfrac{\sigma^2}{n})\\
&= (n - 1)\sigma^2\\
\end{aligned}
$$

最终得到 $\dfrac{1}{n-1}\displaystyle\sum_{i=1}^n(X_i-\overline{X})^2$ 是 $\sigma^2$ 的无偏估计。

> 由于 $\dfrac{1}{n}\displaystyle\sum_{i=1}^n(X_i-\overline{X})^2=\dfrac{n}{n-1}\sigma^2$，且 $\displaystyle\lim_{n\to\infty}\dfrac{n-1}{n}\sigma^2=\sigma^2$，则称 $\dfrac{1}{n}\displaystyle\sum_{i=1}^n(X_i-\overline{X})^2$ 为 $\sigma^2$ 的渐进无偏估计。

---
