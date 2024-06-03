# Maths

## Integration

$$
\lim_{n\to\infty}\sum_{k=1}^n{\dfrac{1}{n+k}} = \lim_{n\to\infty}\sum_{k=1}^n{\dfrac{1}{1+\frac{k}{n}}}=\int_0^1{\dfrac{1}{1+x}}dx=\ln2.
$$

## Differential Equation

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



