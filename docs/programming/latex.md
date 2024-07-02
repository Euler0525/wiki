# $\LaTeX$

### Optimization

$$
\begin{equation}
\begin{aligned} \label{P}
&\max_{\mathbf{w}, q}\quad \log_2{(1+\dfrac{|(\mathbf{h}_{IU}\mathbf{Q}\mathbf{H}_{AI}+\mathbf{h}_{AU})\mathbf{w}|^2}{\sigma_{U}^2})} - \log_2{(1+\dfrac{|(\mathbf{h}_{IE}\mathbf{Q}\mathbf{H}_{AI}+\mathbf{h}_{AE})\mathbf{w}|^2}{\sigma_{E}^2})}\\
&\begin{array}{r@{\quad}r@{}l@{\quad}l}
\text{s.t.} &||\mathbf{w}||^2\leq P_{AP}\\
	&|q_n|=1, \forall n\\
\end{array}
\end{aligned}
\end{equation}
$$

```latex
\begin{equation}
\begin{aligned} \label{P}
&\max_{\mathbf{w}, q}\quad \log_2{(1+\dfrac{|(\mathbf{h}_{IU}\mathbf{Q}\mathbf{H}_{AI}+\mathbf{h}_{AU})\mathbf{w}|^2}{\sigma_{U}^2})} - \log_2{(1+\dfrac{|(\mathbf{h}_{IE}\mathbf{Q}\mathbf{H}_{AI}+\mathbf{h}_{AE})\mathbf{w}|^2}{\sigma_{E}^2})}\\
&\begin{array}{r@{\quad}r@{}l@{\quad}l}
\text{s.t.} &||\mathbf{w}||^2\leq P_{AP}\\
	&|q_n|=1, \forall n\\
\end{array}
\end{aligned}
\end{equation}
```

